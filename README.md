# Cloud-academy-03
Week 3 - System Design Applications - 🚀 Project - Video Sharing Platform

## Architecture Diagram

### High-Level Architecture Diagram

```
                +-------------------------+
                |      User Interface     |
                |  (Web/Mobile Client)    |
                +-----------+-------------+
                            |
                            |
                +-----------+-------------+
                |       API Gateway       |
                +-----------+-------------+
                            |
        +-------------------+-------------------+
        |                   |                   |
        |                   |                   |
+-------+-------+   +-------+-------+   +-------+-------+
|    Upload     |   |   Metadata    |   |   Video       |
|   Service     |   |   Service     |   |   Processing  |
| (Lambda/EC2)  |   | (Lambda/EC2)  |   |  Service      |
+-------+-------+   +-------+-------+   | (Lambda/EC2)  |
        |                   |           +-------+-------+
        |                   |                   |
        |                   |                   |
        |                   |                   |
+-------+-------+   +-------+-------+   +-------+-------+
|   Object      |   |    NoSQL      |   |  Content      |
|   Storage     |   |   Database    |   |  Analysis     |
|   (S3)        |   |  (DynamoDB)   |   |  Service      |
+-------+-------+   +-------+-------+   | (Lambda/EC2)  |
        |                   |           +-------+-------+
        |                   |                   |
        +-------------------+-------------------+
                            |
                            |
                +-----------+-------------+
                |       CDN (CloudFront)  |
                +-----------+-------------+
                            |
                            |
                +-----------+-------------+
                |       End Users         |
                +-------------------------+

```

---

## System Design Document

### 1. Video Upload and Storage

**Feature:** Allow users to upload videos in a high-resolution format.

**Storage Solution:** Amazon S3 (Simple Storage Service)

**Justification:**

- **Scalability:** S3 automatically scales to handle the storage requirements of growing user uploads.
- **Durability:** S3 provides 99.999999999% durability, ensuring the videos are safe.
- **Cost:** Pay-as-you-go pricing model makes it cost-effective.

### 2. Metadata Handling

**Feature:** Store video metadata (e.g., title, description, tags).

**Database Choice:** Amazon DynamoDB (NoSQL Database)

**Justification:**

- **Scalability:** DynamoDB scales horizontally, making it capable of handling large volumes of metadata.
- **Flexibility:** Supports unstructured data and can handle dynamic schemas, which is beneficial for storing metadata with varying attributes.
- **Performance:** Provides low-latency responses, which is crucial for real-time data access.

### 3. Video Processing

**Feature:** Transcode uploaded videos into multiple resolutions.

**Workflow:**

- **Upload Service:** Triggers video processing once the upload is complete.
- **Video Processing Service:** Uses AWS Lambda or EC2 instances to transcode videos into multiple resolutions (e.g., 360p, 720p, 1080p).

**Justification for Serverless:**

- **Scalability:** Serverless functions like AWS Lambda can handle bursts of video processing requests without manual intervention.
- **Cost-Efficiency:** Pay only for the compute time used during the video processing.

### 4. Content Analysis

**Feature:** Automated content analysis to flag inappropriate videos.

**Method:**

- **Integration:** Use AWS Rekognition or a similar service to analyze video content.
- **Workflow:** The content analysis service is triggered after the video upload and processing. It scans the video for inappropriate content and updates the metadata with the results.

### 5. Video Delivery

**Feature:** Serve videos with minimal latency to a global audience.

**Strategy:**

- **Content Delivery Network (CDN):** Use Amazon CloudFront to cache videos at edge locations globally.

**Caching Mechanism:**

- **Initial Access:** When a video is first accessed, it is fetched from S3 and cached at the nearest CDN edge location.
- **Subsequent Access:** Further requests for the same video are served from the CDN cache, reducing latency and load on the origin server.

### Considerations

### Scalability

- **S3:** Automatically scales to store an unlimited number of videos.
- **DynamoDB:** Scales to handle increasing metadata entries without performance degradation.
- **Lambda:** Scales automatically to handle video processing tasks in parallel.

### Resilience

- **S3:** High durability and availability, ensuring video storage is resilient to failures.
- **DynamoDB:** Multi-AZ (Availability Zone) replication provides high availability and data resilience.
- **CloudFront:** Distributes traffic across multiple edge locations, reducing the risk of localized failures.

### Latency

- **CDN:** Caches videos at edge locations, ensuring low-latency video delivery.
- **DynamoDB:** Provides single-digit millisecond latency for read and write operations, enabling quick metadata access.

---

## Conclusion

This system design provides a robust, scalable, and efficient architecture for a video-sharing platform. It leverages AWS services like S3, DynamoDB, Lambda, and CloudFront to meet the requirements of video upload, storage, metadata handling, processing, content analysis, and delivery. The use of serverless technologies simplifies scaling and reduces operational overhead, making it an ideal choice for handling varying traffic patterns and workloads.
