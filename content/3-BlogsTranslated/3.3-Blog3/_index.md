---
title: "Blog 3"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Voting System and Reputation Management for Crowdsourcing Platforms

The voting and reputation system is the heart of TSL-SignMap, ensuring data quality from the community. This article presents how to build a voting system with DynamoDB and Lambda.

---

## Voting System Requirements

| Requirement | Solution |
|-------------|----------|
| Prevent duplicate votes | DynamoDB composite key (SignID + UserID) |
| Weight votes by reputation | Calculate weight in Lambda |
| Real-time vote counting | DynamoDB atomic counters |
| Vote history tracking | DynamoDB Streams → audit log |
| Fraud detection | Rate limiting + reputation threshold |

---

## Database Schema

**Votes Table**

```python
{
  "VoteID": "vote_12345",  # PK
  "SignID": "sign_67890",
  "UserID": "user_abc123",
  "VoteType": "upvote",  # upvote or downvote
  "Weight": 1.5,  # Based on user reputation
  "Timestamp": "2026-01-15T14:30:00Z",
  "IPAddress": "103.12.34.56",
  "DeviceFingerprint": "hash123"
}

# GSI for querying votes by sign
GSI: SignVotesIndex
- PartitionKey: SignID
- SortKey: Timestamp

# GSI for querying votes by user
GSI: UserVotesIndex
- PartitionKey: UserID
- SortKey: Timestamp
```

**Users Table (with Reputation)**

```python
{
  "UserID": "user_abc123",  # PK
  "Email": "user@example.com",
  "ReputationScore": 85,  # 0-100
  "TotalVotes": 150,
  "AccurateVotes": 135,  # Votes matching final decision
  "TotalSubmissions": 25,
  "ApprovedSubmissions": 20,
  "CoinBalance": 120,
  "JoinedAt": "2025-12-01T00:00:00Z",
  "LastActiveAt": "2026-01-15T14:30:00Z"
}
```

**TrafficSigns Table (with Vote Counters)**

```python
{
  "SignID": "sign_67890",  # PK
  "VoteStats": {
    "totalVotes": 12,
    "weightedUpvotes": 18.5,
    "weightedDownvotes": 3.2,
    "approvalRate": 0.85  # 85%
  },
  "Status": "pending",  # pending, approved, rejected
  "AutoDecisionAt": "2026-01-22T14:30:00Z"  # 7 days after submission
}
```

---

## Vote Processing Lambda

**Cast Vote Function**

```python
import boto3
import json
from decimal import Decimal
from datetime import datetime, timedelta

dynamodb = boto3.resource('dynamodb')
votes_table = dynamodb.Table('Votes')
signs_table = dynamodb.Table('TrafficSigns')
users_table = dynamodb.Table('Users')

def lambda_handler(event, context):
    user_id = event['userId']
    sign_id = event['signId']
    vote_type = event['voteType']  # 'upvote' or 'downvote'
    
    # Check if user already voted
    existing_vote = check_existing_vote(user_id, sign_id)
    if existing_vote:
        return {
            'statusCode': 400,
            'body': json.dumps({'error': 'Already voted on this sign'})
        }
    
    # Get user reputation
    user = users_table.get_item(Key={'UserID': user_id})['Item']
    reputation = user['ReputationScore']
    
    # Calculate vote weight
    vote_weight = calculate_vote_weight(reputation, user)
    
    # Check daily vote limit
    daily_votes = get_daily_votes_count(user_id)
    if daily_votes >= 5:
        return {
            'statusCode': 429,
            'body': json.dumps({'error': 'Daily vote limit reached'})
        }
    
    # Save vote
    vote_id = f"vote_{sign_id}_{user_id}"
    votes_table.put_item(Item={
        'VoteID': vote_id,
        'SignID': sign_id,
        'UserID': user_id,
        'VoteType': vote_type,
        'Weight': Decimal(str(vote_weight)),
        'Timestamp': datetime.utcnow().isoformat(),
        'TTL': int((datetime.utcnow() + timedelta(days=90)).timestamp())
    })
    
    # Update sign vote counters (atomic)
    update_sign_votes(sign_id, vote_type, vote_weight)
    
    # Reward user with coin
    reward_user_coin(user_id, 1)
    
    # Check if auto-decision threshold reached
    check_auto_decision(sign_id)
    
    return {
        'statusCode': 200,
        'body': json.dumps({
            'message': 'Vote recorded',
            'weight': vote_weight,
            'coinsEarned': 1
        })
    }

def calculate_vote_weight(reputation, user):
    """
    Calculate vote weight based on reputation and activity
    """
    # Base weight from reputation (0.5 to 1.5)
    base_weight = 0.5 + (reputation / 100)
    
    # Bonus for accurate voting history
    accuracy = user['AccurateVotes'] / max(user['TotalVotes'], 1)
    accuracy_bonus = accuracy * 0.3
    
    # Bonus for active contributors
    approval_rate = user['ApprovedSubmissions'] / max(user['TotalSubmissions'], 1)
    contributor_bonus = approval_rate * 0.2
    
    total_weight = base_weight + accuracy_bonus + contributor_bonus
    return min(total_weight, 2.0)  # Cap at 2.0

def update_sign_votes(sign_id, vote_type, weight):
    """
    Atomically update vote counters
    """
    if vote_type == 'upvote':
        signs_table.update_item(
            Key={'SignID': sign_id},
            UpdateExpression='ADD VoteStats.totalVotes :one, VoteStats.weightedUpvotes :weight',
            ExpressionAttributeValues={
                ':one': 1,
                ':weight': Decimal(str(weight))
            }
        )
    else:
        signs_table.update_item(
            Key={'SignID': sign_id},
            UpdateExpression='ADD VoteStats.totalVotes :one, VoteStats.weightedDownvotes :weight',
            ExpressionAttributeValues={
                ':one': 1,
                ':weight': Decimal(str(weight))
            }
        )
```

---

## Auto-Decision Logic

**Automatic Approval/Rejection**

```python
def check_auto_decision(sign_id):
    """
    Auto-approve or reject based on voting results
    Rules:
    - >70% approval + 5+ votes → approve
    - <30% approval + 5+ votes → reject
    - 30-70% → escalate to admin
    - After 7 days → auto-decide
    """
    sign = signs_table.get_item(Key={'SignID': sign_id})['Item']
    stats = sign['VoteStats']
    
    total_weighted = stats['weightedUpvotes'] + stats['weightedDownvotes']
    
    if total_weighted == 0:
        return
    
    approval_rate = stats['weightedUpvotes'] / total_weighted
    total_votes = stats['totalVotes']
    
    # Update approval rate
    signs_table.update_item(
        Key={'SignID': sign_id},
        UpdateExpression='SET VoteStats.approvalRate = :rate',
        ExpressionAttributeValues={':rate': Decimal(str(approval_rate))}
    )
    
    # Check auto-decision criteria
    if total_votes >= 5:
        if approval_rate > 0.7:
            approve_sign(sign_id)
        elif approval_rate < 0.3:
            reject_sign(sign_id)
        else:
            # 30-70% → escalate to admin
            escalate_to_admin(sign_id, approval_rate)
    
    # Check 7-day timeout
    submitted_at = datetime.fromisoformat(sign['SubmittedAt'])
    if datetime.utcnow() - submitted_at > timedelta(days=7):
        if approval_rate >= 0.5:
            approve_sign(sign_id)
        else:
            reject_sign(sign_id)

def approve_sign(sign_id):
    """Approve sign and reward submitter"""
    sign = signs_table.get_item(Key={'SignID': sign_id})['Item']
    
    # Update sign status
    signs_table.update_item(
        Key={'SignID': sign_id},
        UpdateExpression='SET #status = :approved',
        ExpressionAttributeNames={'#status': 'Status'},
        ExpressionAttributeValues={':approved': 'approved'}
    )
    
    # Reward submitter
    reward_user_coin(sign['SubmittedBy'], 10)
    update_user_reputation(sign['SubmittedBy'], +5)
    
    # Update voters who voted correctly
    update_voter_accuracy(sign_id, 'upvote')
    
    # Send notification
    send_notification(sign['SubmittedBy'], {
        'title': 'Sign Approved!',
        'body': f'Your sign earned you 10 coins',
        'signId': sign_id
    })

def reject_sign(sign_id):
    """Reject sign and penalize if spam"""
    sign = signs_table.get_item(Key={'SignID': sign_id})['Item']
    
    # Update sign status
    signs_table.update_item(
        Key={'SignID': sign_id},
        UpdateExpression='SET #status = :rejected',
        ExpressionAttributeNames={'#status': 'Status'},
        ExpressionAttributeValues={':rejected': 'rejected'}
    )
    
    # Check if likely spam (< 20% approval)
    if sign['VoteStats']['approvalRate'] < 0.2:
        update_user_reputation(sign['SubmittedBy'], -3)
    
    # Update voters who voted correctly
    update_voter_accuracy(sign_id, 'downvote')
```

---

## Reputation Management

**Update Reputation Score**

```python
def update_user_reputation(user_id, change):
    """
    Update user reputation (0-100 scale)
    """
    user = users_table.get_item(Key={'UserID': user_id})['Item']
    current = user['ReputationScore']
    
    new_score = max(0, min(100, current + change))
    
    users_table.update_item(
        Key={'UserID': user_id},
        UpdateExpression='SET ReputationScore = :score',
        ExpressionAttributeValues={':score': new_score}
    )
    
    # Check reputation milestones
    if new_score >= 80 and current < 80:
        award_badge(user_id, 'trusted_contributor')
    elif new_score < 30:
        send_warning(user_id, 'low_reputation')

def update_voter_accuracy(sign_id, correct_vote_type):
    """
    Update reputation for voters who voted correctly
    """
    # Get all votes for this sign
    response = votes_table.query(
        IndexName='SignVotesIndex',
        KeyConditionExpression='SignID = :sid',
        ExpressionAttributeValues={':sid': sign_id}
    )
    
    for vote in response['Items']:
        user_id = vote['UserID']
        was_correct = vote['VoteType'] == correct_vote_type
        
        if was_correct:
            # Increment accurate votes
            users_table.update_item(
                Key={'UserID': user_id},
                UpdateExpression='ADD AccurateVotes :one',
                ExpressionAttributeValues={':one': 1}
            )
            # Small reputation boost
            update_user_reputation(user_id, +1)
```

---

## Fraud Detection

**Detect Suspicious Voting Patterns**

```python
def detect_fraud(user_id, sign_id):
    """
    Detect suspicious voting patterns
    """
    checks = {
        'rapid_voting': check_rapid_voting(user_id),
        'vote_ring': check_vote_ring(user_id, sign_id),
        'new_account': check_new_account(user_id),
        'ip_abuse': check_ip_abuse(user_id)
    }
    
    fraud_score = sum(checks.values())
    
    if fraud_score >= 3:
        # Flag for review
        flag_user_for_review(user_id, checks)
        return True
    
    return False

def check_rapid_voting(user_id):
    """Check if user is voting too quickly"""
    recent_votes = votes_table.query(
        IndexName='UserVotesIndex',
        KeyConditionExpression='UserID = :uid AND #ts > :time',
        ExpressionAttributeNames={'#ts': 'Timestamp'},
        ExpressionAttributeValues={
            ':uid': user_id,
            ':time': (datetime.utcnow() - timedelta(minutes=5)).isoformat()
        }
    )
    
    return 1 if len(recent_votes['Items']) > 10 else 0

def check_vote_ring(user_id, sign_id):
    """Check if user is part of coordinated voting"""
    # Get user's recent votes
    user_votes = get_user_recent_votes(user_id, days=7)
    user_voted_signs = [v['SignID'] for v in user_votes]
    
    # Check if other users with similar patterns exist
    suspicious_overlap = 0
    for other_user_id in get_recent_voters():
        if other_user_id == user_id:
            continue
        
        other_votes = get_user_recent_votes(other_user_id, days=7)
        other_voted_signs = [v['SignID'] for v in other_votes]
        
        # Calculate overlap
        overlap = len(set(user_voted_signs) & set(other_voted_signs))
        if overlap > 10:  # >10 same signs voted
            suspicious_overlap += 1
    
    return 1 if suspicious_overlap >= 3 else 0
```

---

## API Endpoints

**Vote API**

```python
# POST /api/votes
{
  "signId": "sign_67890",
  "voteType": "upvote"
}

# Response
{
  "success": true,
  "weight": 1.5,
  "coinsEarned": 1,
  "currentVoteStats": {
    "totalVotes": 13,
    "approvalRate": 0.87
  }
}
```

**Get Vote Stats**

```python
# GET /api/signs/{signId}/votes
{
  "signId": "sign_67890",
  "voteStats": {
    "totalVotes": 13,
    "weightedUpvotes": 20.0,
    "weightedDownvotes": 3.2,
    "approvalRate": 0.86,
    "status": "approved"
  },
  "topVoters": [
    {
      "userId": "user_123",
      "voteType": "upvote",
      "weight": 1.8,
      "reputation": 92
    }
  ]
}
```

---

## Performance Optimization

| Technique | Implementation |
|-----------|----------------|
| **DynamoDB Transactions** | Atomic vote + counter update |
| **Conditional Writes** | Prevent duplicate votes |
| **GSI for queries** | Fast lookup by SignID/UserID |
| **TTL** | Auto-delete old votes after 90 days |
| **Batch processing** | Process reputation updates in batches |
| **Caching** | Cache vote stats in ElastiCache |

---

## Cost Estimation

| Operation | Volume | Cost/month |
|-----------|--------|------------|
| Vote writes | 100K/month | $1.25 |
| Vote reads | 500K/month | $0.25 |
| User updates | 100K/month | $1.25 |
| Sign updates | 50K/month | $0.60 |
| DynamoDB Streams | 100K changes | $0.20 |
| **Total** | | **~$3.55** |

---

## Conclusion

DynamoDB-based voting system provides:
- **Scalability**: Handle millions of votes per day
- **Real-time**: Atomic counters for instant feedback
- **Fraud prevention**: Multi-layer detection
- **Reputation-based**: Weight votes by user quality
- **Cost-effective**: Pay-per-request pricing

**References:**
- <https://docs.aws.amazon.com/dynamodb/latest/developerguide/transactions.html>
- <https://aws.amazon.com/blogs/database/amazon-dynamodb-transactions/>

---
