## Real-World Example

### Imagine a scenario where a media company needs to store high-resolution images and video content. The images/ folder is used for frequently accessed images, while videos/ are archived after a certain period. Content must be stored securely, with only authorized users able to access and manage files, while older files are automatically archived for cost efficiency.
## Task Overview
### Participants will create an S3-based solution to store, organize, manage, and archive multimedia content (e.g., images, videos, documents). They’ll learn to set up bucket configurations, use object versioning, implement lifecycle policies, and enforce security through bucket policies and access controls.
## Objectives
Gain practical experience with **S3 bucket creation, object storage, versioning**, and **lifecycle policies**.

Learn to secure data using **bucket policies, encryption**, and **access control lists (ACLs)**.

Understand **cost optimization** through storage class transitions and lifecycle management.

Use **S3 event notifications** to simulate file management automation.

## Step-by-Step Task Instructions
### Create an S3 Bucket with Appropriate Naming and Versioning
   * **Create a new S3 bucket named multimedia-storage-[yourname]-bucket**.
   ![preview](imgs3/create%20bucket.png)

  * **Enable bucket versioning to manage file versions and track changes over time**.
  ![preview](imgs3/bucket%20version%20enable.png)

* **Upload a few files with the same name multiple times to test versioning. Observe how S3 maintains versions of each object**.
![preview](imgs3/upload%20imgs%20to%20imgs%20folder.png)


## Organize Files Using Prefixes (Folders)
Create a folder structure within the bucket. For instance**:
* images/
![preview](imgs3/s3%20creating%20folder.png)

* videos/
![preview](imgs3/s3%20creating%20folder.png)

* documents/
![preview](imgs3/s3%20creating%20folder.png)

Upload several files into each "folder" and understand how prefixes work in S3 to simulate directory structures.

## Implement Lifecycle Policies for Cost Optimization
**Set up lifecycle policies for each folder to move objects to cost-effective storage classes over time**:
![preview](imgs3/s3%20imgs%20lfcycle2.png)
![preview](imgs3/s3%20lfcycle3.png)

* Move objects in the images/ folder to S3 Standard-IA (Infrequent Access) after 30 days.
![preview](imgs3/img30days.png)

* Move objects in the videos/ folder to S3 Glacier after 60 days and delete them after 180 days.
![preview](imgs3/s3%20videos%20lfcle%20fldr%203.png)


* Experiment with different configurations for archiving data based on folder and file type.

**Document each lifecycle policy created and explain why it’s optimized for cost and access patterns**.

## Enable Server-Side Encryption

* Apply **server-side encryption** to the bucket to secure data at rest.
* Use **S3 managed keys (SSE-S3)** or **AWS KMS keys (SSE-KMS)**.
![preview](imgs3/server%20side%20encription.png)

* Document the steps taken to enable encryption and the differences between SSE-S3 and SSE-KMS.
![preview](imgs3/S3%20encription.png)

## Configure Access Controls and Bucket Policies
* Create a bucket policy that grants read access to a specific IAM role or user.
![preview](imgs3/bucket%20policy%20code.png)

* Add an Access Control List (ACL) to give specific permissions to selected users or groups.
![preview](imgs3/create%20iam%20rule.png)

* Ensure that public access is blocked (unless specified) to prevent unintended exposure.

* Review the policy and understand how permissions are defined and restricted.
![preview](imgs3/bucket%20policy%20code.png)
## Set Up S3 Event Notifications for File Management Automation
* Configure the bucket to send event notifications on specific events, such as s3:ObjectCreated:* or s3:ObjectRemoved:*.
![preview](imgs3/create%20ntfn%20evnt.png)

* Route notifications to an Amazon SNS topic or email to simulate automation for file management.
![preview](imgs3/subscription%20confirmed.png)

* Upload and delete files in the bucket, observing notifications for each action.
![preview](imgs3/final%20s3%20delete%20ntfn.png)
## Implement Cross-Region Replication (Optional)
* Set up Cross-Region Replication (CRR) to duplicate data from your primary bucket to a secondary bucket in another AWS region.
* Make sure versioning is enabled on both buckets and create IAM policies that permit replication.
* Test the replication by uploading files to the primary bucket and verifying their presence in the secondary bucket.
