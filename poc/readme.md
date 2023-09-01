## Glue code to read file
```
s3_path = 's3://s3-bucket/prefix/'
customer_table = glueContext.create_dynamic_frame_from_options(connection_type= 's3',
                                                               connection_options={"paths": [s3_path]},
                                                               format='csv', format_options = {"withHeader": True, "optimizeP
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




