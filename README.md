# feature_store
### Set up S3 and CloudFormation
1. Create an S3 bucket 
2. Copy over the contents in the example S3 bucket 
```
aws s3 sync s3://aws-ml-blog/artifacts/Using-streaming-ingestion-with-Amazon-SageMaker-Feature-Store/ s3://<bucket-name>/artifacts/Using-streaming-ingestion-with-Amazon-SageMaker-Feature-Store
```
3. Now there should be two zip files and a yaml file in the bucket. The two zip files contains lambda functions. Change these files now if needed. 
If your CloudFormation has already created a pipeline with the same yaml file, you may consider changing the parameter names that was appended with `-yahoo`.
4. Upload boto3 zip to the S3 bucket. 
5. Visit this URL with your own parameters 
```
https://<REGION-NAME>.console.aws.amazon.com/cloudformation/home
?region=<REGION-NAME>#/stacks/create/template
?stackName=sm-fs-streaming-agg-stack
&templateURL=https://<BUCKET-NAME>.s3.<REGION-NAME>.amazonaws.com/
artifacts/Using-streaming-ingestion-with-Amazon-SageMaker-Feature-Store/
sagemaker-featurestore-template.yaml
```
In our case, we did 
```
https://us-west-1.console.aws.amazon.com/cloudformation/home
?region=us-west-1#/stacks/create/template
?stackName=sm-fs-streaming-agg-stack
&templateURL=https://yahoocsv.s3.us-west-1.amazonaws.com/
artifacts/Using-streaming-ingestion-with-Amazon-SageMaker-Feature-Store/
sagemaker-featurestore-template.yaml
```
6. Use default configurations except that you should change the first parameter (in the "Required Parameters" section) to be your bucket name (instead of "aws-ml-blog", the default).

### Notebook
1. Once you see your CloudFormation shows "CREATE_COMPLETE", navigate to Amazon SageMaker in the AWS console and click on the 'Notebook Instances' tab on the left.
2. Click "Open Jupyter" on the top right and navigate to the "notebooks" folder. 
3. Use ``conda_python3` as your kernel:
#### Notebook_0: 
- If you are not running the credit card example, you should skip the data generation steps in this notebook and replace `transactions.csv` with your own data.
- generate transaction records 
- insert fraud records
- Save to an s3 bucket (AmazonS3/sagemaker-us-west-1-302276091756/raw/)
#### Notebook_1:
- Replace the stack name with the stack name in your CloudFormation
- Create online-only feature groups
	- Input feature schema is defined at amazon-sagemaker-feature-store-streaming-aggregation/notebooks/schema
	- Create feature groups 
- Create an Amazon Kinesis data stream
- Create an Amazon Kinesis Data Applications (KDA) application
	- SQL code to query the last 10 min 
	- Specify input & output
#### Notebook_2:
- Run SageMaker Processing Job on the input S3 bucket to create aggregated features (now a dataframe) and writes to S3 
- Verify records exists in the feature group 
#### Notebook_3:
- Split data into train & test sets 
- Train with XGBoost 
- Test prediction 
#### Notebook_4:
- Run test data 
- Monitor on CloudWatch 
- Can call `kinesis_client.put_record()` to put in more record
#### Notebook_5:
- Tear down 

### Teardown CloudFormation
Select the CloudFormation instance and delete it. This will automatically delete the CloudFormation instance (and associated configurations so that the names will be available again), the Lambda instance and the Sagemaker jupyter instance.
Select the Kinesis instance and delete it. 

