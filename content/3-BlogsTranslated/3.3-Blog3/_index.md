---
title: "Blog 3"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# Amazon DynamoDB - Choosing the Right Throughput Mode for Your Application

## Introduction

When building applications on AWS, choosing the right database affects performance, scalability, and operating cost. For applications with large or unpredictable traffic, such as e-commerce, online games, and serverless APIs, Amazon DynamoDB is a common choice.

DynamoDB is a fully managed NoSQL database. Developers do not need to manage servers, maintenance, or infrastructure scaling. However, they still need to understand throughput modes in order to choose the right operating model

---

## Capacity Mode in DynamoDB

When creating a DynamoDB table, users choose a capacity mode. This setting controls how the table handles read and write requests.

DynamoDB provides two main modes:

On-Demand Capacity
Provisioned Capacity
Both modes process data quickly, but they differ in scaling behavior and pricing.

---

## On-Demand Capacity

On-Demand Capacity lets AWS automatically manage the table’s read and write capacity. Users do not need to define Read Capacity Units (RCU) or Write Capacity Units (WCU). DynamoDB scales based on real traffic.

This mode is suitable for new applications or workloads with unpredictable traffic. For example, a newly launched food delivery startup may not know how many users will access the app in the first days. On-Demand helps the table absorb growth without manual capacity planning.

---

## Advantages

Easy to deploy.
No need to calculate RCU or WCU.
Automatically scales with real demand.
Charges are based on actual read and write requests.
Works well with serverless architectures such as Lambda and API Gateway.

---

## Limitations

For workloads with consistently high and stable traffic, On-Demand can cost more than Provisioned Capacity. It can also be harder to forecast monthly budget because cost depends directly on request volume.

---

## Provisioned Capacity

Provisioned Capacity requires users to define how much read and write capacity the table should maintain.

Read Capacity Unit (RCU): measures read throughput.
Write Capacity Unit (WCU): measures write throughput.
For example, if an e-commerce site processes 500 orders per second and each order is around 1 KB, the table needs at least 500 WCU for stable writes.

---

## Auto Scaling

Provisioned Capacity can be combined with Auto Scaling. When usage exceeds a configured threshold, DynamoDB can increase RCU or WCU. When traffic drops, it can reduce capacity to save cost.  

---

## Advantages

Lower cost for stable workloads.
More predictable capacity planning.
Suitable for ERP, CRM, warehouse management systems, internal APIs, and other predictable workloads.

---

## Limitations

If capacity is too low, DynamoDB may throttle requests. If capacity is too high, the business pays for unused resources. Operating Provisioned Capacity also requires monitoring CloudWatch metrics and adjusting settings when traffic changes.

---

## Warm Throughput

Warm Throughput prepares a table for sudden traffic spikes. It is useful for flash sales, ticketing systems, game launches, and marketing campaigns that create a large burst of traffic at a known time.

---

## Throttling and Hot Partitions

Throttling happens when requests exceed the table’s available capacity or the capacity of a specific partition. Hot partitions occur when most requests target the same partition key, causing one partition to overload while others remain underused.

To avoid hot partitions, partition keys should distribute data evenly across many partitions.

---

## Which mode should you choose?

Choose On-Demand Capacity for new applications, unpredictable traffic, flash sales, event-driven workloads, and serverless systems.

Choose Provisioned Capacity when the workload is stable, historical traffic data is available, long-term cost optimization is important, and the team can monitor and tune capacity.

Many teams start with On-Demand to reduce operational effort, then move to Provisioned Capacity when the application becomes stable.

---

## Conclusion

Throughput is one of the most important concepts in DynamoDB because it directly affects performance, scalability, and cost. On-Demand is simple and flexible, while Provisioned Capacity provides better cost control for predictable workloads.

Warm Throughput, Throttling, and Hot Partition design also show that good partition-key design, CloudWatch monitoring, and Auto Scaling are essential for operating DynamoDB effectively.