# Create a lambda function to read and write Excel files in s3 when file pushed to S3
- We need to create a lambda package as a zip file for the external libraries like pandas as we can use only Python built-in and aws libraries like os, logging, boto3, io & so on
- also need to create a role for EC2 and Lambda to access the S3 bucket
  
## Create lambda package for layer (not use sudo if permission is denied)
- Create a Ubuntu ec2 instance
- update system
  - sudo apt-get update
- Install Python version matching with lambda
  - sudo apt-get install python3.11
- Check Python version exists
  - ls /usr/bin/python*
- Change the default Python version
  - sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1
  - sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 2
- Select from existing Python alternatives 
  - sudo update-alternatives --config python3
  - Press the selection number to select the required alternatives from the result of the above command
- Make dir structure use python version same as Lambda executable
  - mkdir -p build/python/lib/python3.11/site-packages
- Install pip3 if not installed already
  - sudo apt install python3-pip -y
- Install packages (you can use this command as and when you need to add or remove library
  - pip3 install pandas xlrd -t build/python/lib/python3.11/site-packages/
- install zip
  - sudo apt-get install zip
- cd to build dir and zip package
  - zip -r pandas_xlrd.zip .
- Install aws cli to push zip to s3
  - sudo apt-get install awscli
- copy zip to s3
  - aws s3 cp pandas_xlrd.zip s3://poc-output-report/

## Attched package to lambda
- Goto Lambda - layers - create layers -name it
- Select option upload from s3 and copy the S3 URL
- Select the compatible Python version
- Create

## Create the lambda function
- Create function - name it
- Select the Python version
- Select an existing execution role having permission to s3
- Create function
- attached created layer
- Test function by adding pandas import statement in code
- You can store multiple zip files with different versions in a layer and use it in the function.


## Add a trigger to the lambda function
- goto function - trigger - select s3 - bucket name - all object create events - add
- goto code - add below code to append data of the new file into the output file
```
import boto3
import pandas as pd
import os
import logging
import io 

# Configure logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

s3_client = boto3.client('s3')

def lambda_handler(event, context):
    # Get the bucket and object key from the S3 event
    bucket = event['Records'][0]['s3']['bucket']['name']
    filename = event['Records'][0]['s3']['object']['key']
    logger.info(f"Processing file: {filename}")

    # Read the content of the uploaded Excel source file
    response = s3_client.get_object(Bucket=bucket, Key=filename)
    file_content = response['Body'].read()

    # Read the target bucket and key for the output Excel file from the environment variable
    output_bucket =  os.environ['OUTPUT_BUCKET']  # Replace with your output bucket name
    output_file = os.environ['OUTPUT_SUFFIX']  # You can customize the output object key
    logger.info(f"Reading destination: {output_bucket}/{output_file}")
    try:
        # Check if the target Excel file already exists
        target_response = s3_client.get_object(Bucket=output_bucket, Key=output_file)
        target_content = target_response['Body'].read()
        logger.info(f"Reading destination file")
        
        # Read both source and target Excel files into Pandas DataFrames
        source_df = pd.read_excel(io.BytesIO(file_content), engine='openpyxl')
        logger.info(f"Reading source file")
        target_df = pd.read_excel(io.BytesIO(target_content), engine='openpyxl')
    
        # Concatenate the DataFrames vertically (assuming they have the same columns)
        concatenated_df = pd.concat([target_df, source_df], ignore_index=True)

        # Convert the concatenated DataFrame back to an Excel file
        with pd.ExcelWriter('/tmp/concatenated_data.xlsx', engine='openpyxl') as writer:
            concatenated_df.to_excel(writer, index=False)

        # Upload the concatenated Excel data to the output bucket
        with open('/tmp/concatenated_data.xlsx', 'rb') as file_data:
            s3_client.upload_fileobj(file_data, output_bucket, output_file)

    except s3_client.exceptions.NoSuchKey:
        # If the target Excel file does not exist, create a new one with the source file content
        logger.info(f"Creating file at  destination: {output_bucket}/{output_file}")
        s3_client.upload_fileobj(io.BytesIO(file_content), output_bucket, output_file)
        
        
    return {
        'statusCode': 200,
        'body': 'File processed and data appended successfully.'
    }

```

## Test lambda function
- Go to test dropdown - configure test event
- Create new events - name it
- Select the matching template with your trigger ex. for S3 create object select s3 put the template
- test JSON will be generated - change JSON with as per your actual S3 path and file names
- Run the test

  
## Glue code to read file
```
s3_path = 's3://s3-bucket/prefix/'
customer_table = glueContext.create_dynamic_frame_from_options(connection_type= 's3',
                                                               connection_options={"paths": [s3_path]},
                                                               format='csv', format_options = {"withHeader": True, "optimize
```
## lambda function to call glue job
```
import json
import boto3


def lambda_handler(event, context):
    # Call Glue Job
    client = boto3.client
    glue_jobname= "glue_transform_file"
    response = client.start_job_run(JobName=glue_jobname)
    print(response)
    
    return response

```


https://www.myworkday.com/wday/authgwy/nb/login.htmld?redirect=n

https://www.myworkday.com/nb/login.flex?redirect=n




