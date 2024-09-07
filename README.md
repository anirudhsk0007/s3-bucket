import boto3
from botocore.exceptions import NoCredentialsError, PartialCredentialsError

# Initialize the S3 client
s3 = boto3.client('s3')

def create_bucket(bucket_name):
    try:
        s3.create_bucket(Bucket=bucket_name)
        print(f"Bucket '{bucket_name}' created successfully.")
    except s3.exceptions.BucketAlreadyOwnedByYou:
        print(f"Bucket '{bucket_name}' already exists and is owned by you.")
    except s3.exceptions.BucketAlreadyExists:
        print(f"Bucket '{bucket_name}' already exists.")
    except (NoCredentialsError, PartialCredentialsError):
        print("Error: AWS credentials not found or incomplete.")

def upload_file(bucket_name, file_name, object_name=None):
    try:
        if object_name is None:
            object_name = file_name
        s3.upload_file(file_name, bucket_name, object_name)
        print(f"File '{file_name}' uploaded to bucket '{bucket_name}' as '{object_name}'.")
    except FileNotFoundError:
        print(f"Error: File '{file_name}' not found.")
    except NoCredentialsError:
        print("Error: AWS credentials not found.")

def list_files(bucket_name):
    try:
        response = s3.list_objects_v2(Bucket=bucket_name)
        if 'Contents' in response:
            print(f"Files in bucket '{bucket_name}':")
            for obj in response['Contents']:
                print(obj['Key'])
        else:
            print(f"No files found in bucket '{bucket_name}'.")
    except s3.exceptions.NoSuchBucket:
        print(f"Bucket '{bucket_name}' does not exist.")
    except NoCredentialsError:
        print("Error: AWS credentials not found.")

def download_file(bucket_name, object_name, file_name=None):
    try:
        if file_name is None:
            file_name = object_name
        s3.download_file(bucket_name, object_name, file_name)
        print(f"File '{object_name}' downloaded from bucket '{bucket_name}' as '{file_name}'.")
    except s3.exceptions.NoSuchKey:
        print(f"Error: File '{object_name}' not found in bucket '{bucket_name}'.")
    except NoCredentialsError:
        print("Error: AWS credentials not found.")

# Example usage
if __name__ == "__main__":
    bucket_name = "your-bucket-name"
    
    # Create a bucket
    create_bucket(bucket_name)

    # Upload a file
    upload_file(bucket_name, "example.txt")

    # List files in the bucket
    list_files(bucket_name)

    # Download a file
    download_file(bucket_name, "example.txt", "downloaded_example.txt")
