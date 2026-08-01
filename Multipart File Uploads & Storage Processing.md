# Multipart File Uploads & Storage Processing

## 1. How the Data Flows (Client to Server)

The Frontend sends a **POST** request with the `Content-Type: multipart/form-data`
The image binary data is attached inside a multipart payload key (e.g., `"file"`).

**Spring Servlet Parsing:** Spring's `StandardMultipartResolver` parses the raw HTTP request streams and packages the uploaded file into a `MultipartFile` object.

**Storage:** The file binary is saved to a storage provider and only the media URL is saved in the database:
#### **Option A: Amazon S3 (Cloud Object Storage):**
Amazon S3 (Simple Storage Service) is the industry standard for storing media files, images, and documents. Instead of saving raw binary data in your database, you upload the file to an S3 bucket and save the resulting public/presigned S3 URL string in PostgreSQL.

``` Java
@Transactional
public User setProfileImage(User user, MultipartFile file) throws IOException {
    // 1. Sanitize filename and add a timestamp to prevent duplicate collisions & caching issues
    String originalName = file.getOriginalFilename();
    String extension = (originalName != null && originalName.contains(".")) 
            ? originalName.substring(originalName.lastIndexOf(".")) 
            : "";

    String fileName = "user_" + user.getId() + "_" + System.currentTimeMillis() + extension;

    // 2. Upload to S3/Supabase bucket
    s3Template.upload(
            BUCKET_NAME, // 1. Where to put it
            fileName, // 2. What to name it
            file.getInputStream() // 3. The actual binary data
    );

    // 3. Format public URL cleanly
    String publicImageUrl = s3PublicBase.endsWith("/") 
            ? s3PublicBase + fileName 
            : s3PublicBase + "/" + fileName;

    // 4. Update user entity and save via injected repository instance
    user.setProfileImageUrl(publicImageUrl);
    return userRepository.save(user);
}
```

#### **Option B: PostgreSQL Database (BLOB / `bytea` Storage)**
Storing raw image binary data directly inside PostgreSQL using the `bytea` (byte array) column type or PostgreSQL Large Objects.

``` Java
@Transactional
public User setProfileImage(User user, MultipartFile file) throws IOException {
    // Convert the MultipartFile binary input stream straight into a byte array
    user.setProfileImage(file.getBytes());
    // Save directly to PostgreSQL bytea column
    return userRepository.save(user);
}
```

`MultipartFile` is a Spring Framework interface that acts as a wrapper for an uploaded file coming in via an HTTP `multipart/form-data` request. Under the hood, it holds both the **raw binary data** of the file and its **associated metadata**.