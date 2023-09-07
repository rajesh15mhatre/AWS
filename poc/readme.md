# Create a lambda function to read and write Excel files in s3
- We need to create a lambda package with the pandas library as it is not available directly
## Create lambda package
- Create a Ubuntu ec2 instance
- update system
  - sudo apt-get update
- install Python version matching with lambda
  - sudo apt-get install python3.11
- Check python version exists
  -  ls /usr/bin/python*
-  Change the default Python version
  -  sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1
  -  sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 2
-  Select from existing Python alternatives 
  -  sudo update-alternatives --config python3
  -  Press the selection number to select the required alternatives from the result of the above command
-  Make dir structure
  -  mkdir -p build/python/python3.11/site-packages
-  Install pip3 if not installed already
  - sudo apt install python3-pip -y
- install packages
  - pip3 install pandas xlrd -t build/python/lib/python3.11/site-packages/
- install zip
  - sudo apt-get install zip
- cd to build dir and zip package
  - zip -r pandas_xlrd.zip .
- Install aws cli to push zip to s3
  - sudo apt-get install awscli
- copy zip to s3
  - aws s3 cp pandas_xlrd.zip s3://poc-output-report/
- 
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




