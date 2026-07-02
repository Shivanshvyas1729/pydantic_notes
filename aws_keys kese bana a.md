# How to Get AWS Credentials and Create an S3 Bucket

## 1. AWS Access Key ID(AWS_ACCESS_KEY)

1. Sign in to the AWS Management Console.
2. Open the **IAM (Identity and Access Management)** service.
3. <img width="381" height="108" alt="image" src="https://github.com/user-attachments/assets/3b87d175-f860-4d05-ad6e-0e2e48b2a9aa" />

4. From the left sidebar, select **Users**.
5. <img width="526" height="342" alt="image" src="https://github.com/user-attachments/assets/7665fad7-f0f9-4378-9553-7ddd914261ec" />

6. Click **Create user**.
7. Enter a username and click **Next**.
8. Under **Permissions**, choose **Attach policies directly**.
9. Select **AdministratorAccess** (or the appropriate policy for your use case).
10. <img width="555" height="256" alt="image" src="https://github.com/user-attachments/assets/702bd99a-b4a4-46a2-8a2d-2aec343c3e56" />

11. Click **Next**, then **Create user**.
12. Open the newly created user.
13. Go to the **Security credentials** tab.
14. Scroll to **Access keys** and click **Create access key**.
15. <img width="642" height="325" alt="image" src="https://github.com/user-attachments/assets/b74d9e55-0bf5-44ec-af81-d8592c531d4f" />

16. Select **Command Line Interface (CLI)** as the use case.
17. Create the access key.

You will receive:

* **AWS Access Key ID**
* **AWS Secret Access Key**

> **Important:** Download or copy the credentials immediately, as the Secret Access Key cannot be viewed again after creation.

---

## 2. AWS Secret Access Key(AWS_SECRECT_KEY)

The **Secret Access Key** is generated along with the **AWS Access Key ID** during the access key creation process.
<img width="1327" height="568" alt="image" src="https://github.com/user-attachments/assets/1960bcda-3ee8-4c71-940d-e0faefef6f0e" />


Store it securely, as AWS does not allow you to retrieve it later. If lost, you must create a new access key pair.


---

## 3. AWS S3 Bucket Name(AWS_BUCKET_NAME)

1. Open the **Amazon S3** service in the AWS Console.
2. Click **Create bucket**.
3. Enter a unique bucket name.
4. For testing purposes, you may uncheck **Block all public access** (not recommended for production).
5. Acknowledge the warning if prompted.
6. Click **Create bucket**.
7. After the bucket is created, copy the **Bucket Name**. This is your **AWS Bucket Name** and will be used in your application.
