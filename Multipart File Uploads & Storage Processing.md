## 1. How the Data Flows (Client to Server)

The Frontend sends a **POST** request with the `Content-Type: multipart/form-data`
The image binary data is attached inside a multipart payload key (e.g., `"file"`).

**Spring Servlet Parsing:** Spring's `StandardMultipartResolver` parses the raw HTTP request streams and packages the uploaded file into a `MultipartFile` object.

**Storage:** The file binary is saved to a storage provider and only the media URL is saved in the database:
#### **Option A: Amazon S3 (Cloud Object Storage):**
Amazon S3 (Simple Storage Service) is the industry standard for storing media files, images, and documents. Instead of saving raw binary data in your database, you upload the file to an S3 bucket and save the resulting public/presigned S3 URL string in PostgreSQL.
#### **Option B: PostgreSQL Database (BLOB / `bytea` Storage)**
Storing raw image binary data directly inside PostgreSQL using the `bytea` (byte array) column type or PostgreSQL Large Objects.

`MultipartFile` is a Spring Framework interface that acts as a wrapper for an uploaded file coming in via an HTTP `multipart/form-data` request. Under the hood, it holds both the **raw binary data** of the file and its **associated metadata**.

