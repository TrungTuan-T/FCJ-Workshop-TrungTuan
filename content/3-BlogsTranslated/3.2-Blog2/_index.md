---
title: "Blog 2"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# A Unified JSON Data Search System on AWS
**Introduction**
Modern applications such as streaming platforms, e-commerce systems, and large data products rarely store all data in a single database. Some data requires low-latency access, some requires strong transactional consistency, and other data is stored for analytics or long-term reporting.

This raises an important question: how can users search across many data sources quickly and accurately?

AWS solves this pattern by combining purpose-built data services with Amazon OpenSearch Service to create a unified search layer.
---

## Why not use only one database?

When I first started building web applications, I thought a single database such as MySQL could store everything: users, products, orders, activity history, and logs. After studying AWS and large-scale system architectures, I realized that this approach works only for small projects.

As data grows to millions or billions of records, one database becomes a limitation for performance, scalability, and operational cost. Each data type should use the tool that fits it best:

DynamoDB is suitable for fast key-value and real-time access.
Aurora PostgreSQL is suitable for transactional data.
DocumentDB is suitable for flexible JSON documents.
S3 is suitable for large-scale storage and data lakes.
Redshift is suitable for analytics and reporting.

---

## Overall architecture

The system has two main layers.

The data storage layer includes Amazon DynamoDB, Amazon Aurora PostgreSQL, Amazon DocumentDB, Amazon S3, and Amazon Redshift. Each service stores the type of data it handles best.

The search layer uses Amazon OpenSearch Service. Searchable data is synchronized into OpenSearch so users can query one unified search endpoint instead of searching each source database separately.

---

## AWS service roles

DynamoDB is a low-latency NoSQL database with high scalability. It fits user profiles, activity history, and real-time application state. For example, a streaming platform can store a user’s current movie and resume timestamp so playback can continue quickly on another device.
---

## Amazon Aurora PostgreSQL

Aurora PostgreSQL is used for payments, orders, and other transactional information. Because it supports ACID transactions, Aurora helps keep business data consistent and reliable.

---

## Amazon DocumentDB

DocumentDB stores flexible JSON data such as product information, metadata, and content with changing attributes. This is useful in e-commerce, where one product may have completely different attributes from another.

---

## Amazon S3

Amazon S3 acts as a data lake, log archive, and backup store. It provides low-cost storage with virtually unlimited scalability and is often the foundation for analytics pipelines.

---

## Amazon Redshift

Amazon Redshift handles large-scale analytical workloads. With the SUPER data type and PartiQL, Redshift can query complex JSON structures efficiently. In a streaming platform, Redshift can analyze billions of play, pause, and skip events from S3, calculate trend scores, and export aggregated results back to the search layer.

---

## Amazon OpenSearch Service

OpenSearch is the central search component. It supports full-text search, fuzzy search, auto suggestion, vector search, and AI search. For example, when a user searches for a product with incomplete or imperfect keywords, OpenSearch can still return relevant results.

---

## Synchronizing data into OpenSearch

OpenSearch Ingestion (OSI) collects, transforms, and synchronizes data into Amazon OpenSearch Service. The synchronization process usually has two stages:

Initial Load: load the baseline data from DynamoDB, Aurora, DocumentDB, or S3.
Change Data Capture: after the baseline is created, synchronize only new changes.
This design reduces load on source systems, lowers cost, and keeps search data close to real time.

---

## Conclusion

Combining DynamoDB, Aurora, DocumentDB, S3, Redshift, and OpenSearch creates a modern data architecture that is flexible and scalable. Amazon OpenSearch Service acts as the unified search layer that connects data from multiple systems and gives users a powerful search experience.

Reference: <https://awsstudygroup.com/2026/05/20/cach-xay-dung-giai-phap-tim-kiem-json-hop-nhat-trong-aws/>