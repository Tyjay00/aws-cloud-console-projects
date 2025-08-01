# Serverless Image Processing Pipeline Documentation

This document provides a step-by-step guide to building and deploying a serverless image processing pipeline on AWS. This pipeline automatically detects labels in an uploaded image using AWS Rekognition, adds a watermark, and saves the processed image to a separate S3 bucket.


## The Architecture

<img width="2964" height="2188" alt="image" src="https://github.com/user-attachments/assets/45d2a397-7fae-4c00-a3b0-ccff6e16f313" />


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
import io

s3_client = boto3.client('s3')
rekognition_client = boto3.client('rekognition')

def lambda_handler(event, context):
    print("Received event: " + json.dumps(event))

    # Get bucket name and object key from the S3 event
    bucket_name = event['Records'][0]['s3']['bucket']['name']
    object_key = event['Records'][0]['s3']['object']['key']

    # Log the image being processed
    print(f"Processing image {object_key} from bucket: {bucket_name}")

    # Call Rekognition to detect labels
    try:
        rekognition_response = rekognition_client.detect_labels(
            Image={'S3Object': {'Bucket': bucket_name, 'Name': object_key}},
            MaxLabels=10
        )
        print("Detected labels:")
        for label in rekognition_response['Labels']:
            print(f"- {label['Name']} (Confidence: {label['Confidence']:.2f}%)")
    except Exception as e:
        print(f"Error processing image with Rekognition: {e}")
        raise e

    # Create a simple text string from the detected labels
    labels = [label['Name'] for label in rekognition_response['Labels']]
    watermark_text = f"Labels: {', '.join(labels[:3])}"

    # Get the image from S3
    try:
        response = s3_client.get_object(Bucket=bucket_name, Key=object_key)
        image_data = response['Body'].read()
    except Exception as e:
        print(f"Error getting object from S3: {e}")
        raise e

    # Open the image with Pillow using BytesIO
    try:
        image = Image.open(io.BytesIO(image_data))
        draw = ImageDraw.Draw(image)
        
        # Load a font. If this fails, use the default font.
        try:
            font = ImageFont.truetype("arial.ttf", 40)
        except IOError:
            print("Arial.ttf not found, using default font.")
            font = ImageFont.load_default()
    except Exception as e:
        print(f"Error processing image with Pillow: {e}")
        raise e

    # Get text size using textbbox for a more accurate bounding box
    try:
        bbox = draw.textbbox((0, 0), watermark_text, font=font)
        text_width = bbox[2] - bbox[0]
        text_height = bbox[3] - bbox[1]
    except AttributeError:
        # Fallback for older Pillow versions or specific cases if textbbox is not available
        text_width, text_height = draw.textsize(watermark_text, font=font)

    # Position the watermark in the bottom-left corner
    x = 10
    y = image.height - text_height - 10

    # Draw a semi-transparent background for the watermark text
    bg_color = (0, 0, 0, 128)  # Semi-transparent black (R, G, B, Alpha)
    draw.rectangle([x - 5, y - 5, x + text_width + 5, y + text_height + 5], fill=bg_color)

    # Draw the watermark text
    draw.text((x, y), watermark_text, fill=(255, 255, 255), font=font)

    # Save the processed image back to S3
    processed_bucket = os.environ.get('PROCESSED_BUCKET')
    if not processed_bucket:
        raise ValueError("PROCESSED_BUCKET environment variable is not set.")

    processed_key = f"processed-{object_key}"

    try:
        image.save('/tmp/processed-image.jpg', 'JPEG')
        with open('/tmp/processed-image.jpg', 'rb') as f:
            s3_client.put_object(
                Bucket=processed_bucket,
                Key=processed_key,
                Body=f,
                ContentType='image/jpeg'
            )
        print(f"Successfully processed image and saved to {processed_bucket}/{processed_key}")
    except Exception as e:
        print(f"Error saving processed image to S3: {e}")
        raise e
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


<img width="1912" height="912" alt="image" src="https://github.com/user-attachments/assets/fc63bed4-3381-458d-92ad-cd3000fa3e3c" />

---

<img width="1897" height="911" alt="image" src="https://github.com/user-attachments/assets/27abdaec-1893-4ecf-9722-31f9edf5fe2f" />

---
<img width="1918" height="918" alt="cloudwatch logs" src="https://github.com/user-attachments/assets/8eacfe09-2f76-4db2-ba0a-aa773fb11ed4" />

