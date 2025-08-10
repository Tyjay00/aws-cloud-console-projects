# Serverless Image Processing Pipeline Documentation

This document provides a step-by-step guide to building and deploying a serverless image processing pipeline on AWS. This pipeline automatically detects labels in an uploaded image using AWS Rekognition, adds a watermark and saves the processed image to a separate S3 bucket.


## The Architecture

<img width="2992" height="2188" alt="image" src="https://github.com/user-attachments/assets/bff0a9b4-5dd1-4d6c-ba98-d53ddec2016c" />

-----

## Initial Setup - AWS Services

First, you need to set up the core AWS services:

  * **Source S3 Bucket:** Create an S3 bucket named `my-source-images-app`. This bucket will be configured to trigger your Lambda function whenever a new image is uploaded.
  * **Destination S3 Bucket:** Create a second S3 bucket named `my-processed-images-app`. This is where the final, watermarked images will be stored.
  * **Lambda Function:** Create a new Lambda function named `ImageProcessorFunction` using a Python 3.9 runtime. You will configure this function in the following steps.

<img width="1903" height="912" alt="image" src="https://github.com/user-attachments/assets/52d9a91f-bb8c-4c1c-a167-a4ff7854233a" />

-----

## Creating a Lambda Layer for the Pillow Library

The Pillow library is not included in the default Lambda runtime. To make it available to your function, you must create a Lambda Layer. This is best done using a Docker container to ensure the library is correctly compiled for the Amazon Linux environment.

Use the following commands to create the layer:

* Create a directory for the layer and install the Pillow library into it.
    
    ```
    mkdir python
    docker run --platform linux/x86_64 -v "$PWD":/var/task "public.ecr.aws/lambda/python:3.9" /bin/sh -c "pip install Pillow -t python; exit"
    ```
    
    
<img width="1475" height="761" alt="powershell" src="https://github.com/user-attachments/assets/e5019c73-44e3-4509-bc57-c01745eada7c" />

* Package the directory into a zip file.

    
    zip -r PillowLayer.zip python

* Upload the `PillowLayer.zip` file to a new Lambda Layer in the AWS console and attach it to your `ImageProcessorFunction`.

<img width="1892" height="932" alt="layer pillow" src="https://github.com/user-attachments/assets/2819a7f9-d1bc-4c5d-982a-1c2e9f96ffe4" />

-----

## Configuring Lambda Environment Variables

To ensure your code is portable and secure, the name of the destination S3 bucket should be an environment variable.

*  In the Lambda console, select your `ImageProcessorFunction`.
*  Go to the "**Configuration**" tab and click on "**Environment variables**".
*  Click "**Edit**" and add a new variable with:
      * **Key:** `PROCESSED_BUCKET`
      * **Value:** `my-processed-images-app`
*  Click "**Save**".

<img width="1608" height="521" alt="image" src="https://github.com/user-attachments/assets/0ef35b3d-fcf3-47d7-9a6f-6fd48ca27ac9" />

-----

## Writing the Python Code for the Lambda Function

Here is the complete Python code for your `lambda_function.py` file. This code uses the `PROCESSED_BUCKET` environment variable, calls Rekognition, and uses the Pillow library to add a watermark.



```python
import json
import boto3
import os
from PIL import Image, ImageDraw, ImageFont
from urllib.parse import unquote_plus
import io

rekognition_client = boto3.client('rekognition', region_name='us-east-1')
s3_client = boto3.client('s3', region_name='us-east-1')

def lambda_handler(event, context):
    print("Received event: " + json.dumps(event))

    bucket_name = event['Records'][0]['s3']['bucket']['name']
    object_key = unquote_plus(event['Records'][0]['s3']['object']['key'])
    print(f"Processing image {object_key} from bucket: {bucket_name}")

    # Detect labels
    rekognition_response = rekognition_client.detect_labels(
        Image={'S3Object': {'Bucket': bucket_name, 'Name': object_key}},
        MaxLabels=10
    )
    print("Detected labels:")
    for label in rekognition_response['Labels']:
        print(f"- {label['Name']} ({label['Confidence']:.2f}%)")

    labels = [label['Name'] for label in rekognition_response['Labels']]
    watermark_text = f"Labels: {', '.join(labels[:3])}"

    # Download image from S3
    response = s3_client.get_object(Bucket=bucket_name, Key=object_key)
    with response['Body'] as stream:
        image = Image.open(stream).convert("RGBA")

        draw = ImageDraw.Draw(image, 'RGBA')
        try:
            font = ImageFont.truetype("arial.ttf", 40)
        except IOError:
            font = ImageFont.load_default()

        try:
            bbox = draw.textbbox((0, 0), watermark_text, font=font)
            text_width = bbox[2] - bbox[0]
            text_height = bbox[3] - bbox[1]
        except AttributeError:
            try:
                text_width, text_height = font.getsize(watermark_text)
            except AttributeError:
                mask = font.getmask(watermark_text)
                text_width, text_height = mask.size

        x = 10
        y = image.height - text_height - 10

        draw.rectangle([x - 5, y - 5, x + text_width + 5, y + text_height + 5], fill=(0, 0, 0, 128))
        draw.text((x, y), watermark_text, fill=(255, 255, 255, 255), font=font)

        rgb_image = image.convert('RGB')
        buffer = io.BytesIO()
        rgb_image.save(buffer, 'JPEG', quality=85, optimize=True)
        buffer.seek(0)

    processed_bucket = os.environ.get('PROCESSED_BUCKET')
    if not processed_bucket:
        raise ValueError("PROCESSED_BUCKET environment variable is not set.")
    processed_key = f"watermarked-{object_key}"

    print(f"Uploading watermarked image to bucket: {processed_bucket}, key: {processed_key}")

    try:
        s3_client.upload_fileobj(buffer, processed_bucket, processed_key,
                                 ExtraArgs={'ContentType': 'image/jpeg'})
        print(f"Watermarked image successfully uploaded to {processed_bucket}/{processed_key}")
    except Exception as e:
        print(f"Failed to upload watermarked image: {e}")
        raise

    return {
        'statusCode': 200,
        'body': json.dumps({
            'labels': labels,
            'watermarked_image': f"s3://{processed_bucket}/{processed_key}"
        })
    }

```
-----

## Configuring IAM Permissions

The IAM role for your Lambda function needs a policy that allows it to access S3 and Rekognition.

  * Go to the IAM console, find the role attached to your `ImageProcessorFunction`.
  * Attach a policy with the following permissions. This is the only policy required for this part of the setup. No separate S3 bucket policy is needed.

<!-- end list -->

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-source-images-app/*",
                "arn:aws:s3:::my-processed-images-app/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "rekognition:DetectLabels",
            "Resource": "*"
        }
    ]
}
```

-----

## Configuring the S3 Trigger

The Lambda function needs to be triggered whenever an image is uploaded to your source bucket.

*  In the Lambda console, select your `ImageProcessorFunction`.
*  Click "**Add trigger**" and choose "**S3**" from the list of services.
*  Configure the trigger to use the `my-source-images-app` bucket.
*  Set the event type to `All object create events` and confirm the configuration.

<img width="1915" height="571" alt="image" src="https://github.com/user-attachments/assets/8b20c1f0-11d1-4e8f-98e1-3763ffbd11b2" />

-----

## Adjusting the Lambda Function Timeout

Image processing can be a resource-intensive task. To prevent the function from timing out, it's a good practice to increase the default timeout.

*  In the Lambda console, select your `ImageProcessorFunction`.
*  Go to the "**Configuration**" tab and click on "**General configuration**".
*  Click "**Edit**" and change the `Timeout` to `5 minutes`.
*  Click "**Save**".

<img width="1911" height="410" alt="image" src="https://github.com/user-attachments/assets/73a1e11b-3bdf-4108-ace0-c067ce27d8b3" />

-----

## Final Testing and Verification

With all the above steps complete, the pipeline was ready for a final test. I uploaded an image to the source S3 bucket, and the Lambda function successfully ran from start to finish, saving the final image to the destination S3 bucket.


<img width="1911" height="907" alt="image" src="https://github.com/user-attachments/assets/a689e43b-6790-4644-a31f-acc9e0e8d31d" />

---

<img width="1918" height="918" alt="image" src="https://github.com/user-attachments/assets/8fbeeeaf-7925-4187-b3a9-fc6162d00617" />

---
<img width="1918" height="908" alt="image" src="https://github.com/user-attachments/assets/5de45658-fd89-44bd-80cc-1957b76c1158" />

<img width="1887" height="977" alt="image" src="https://github.com/user-attachments/assets/fe4338d0-a928-48f1-b3fa-71ff4c612abf" />

---

## Error Logs on intial testing

* During initial testing, errors occurred when uploading images. The output was not processed to the destination bucket.

<img width="1917" height="917" alt="Cloud Watch Logs Error" src="https://github.com/user-attachments/assets/b363e2cf-44d8-4702-8d7d-a438f4451f8f" />
<img width="1902" height="912" alt="CloudWatch Logs Successful" src="https://github.com/user-attachments/assets/9e62a989-add5-49f9-b089-d3b46db7eccd" />

