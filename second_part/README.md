# Part 2 - Infrastructure

During your work on Part 1, you discover that the mobile app is not sending all the necessary user behaviour events. You discuss it with the customer and they agree to collect and send the required, new user behaviour events.
Assume that:
- You are using a distributed pub-sub style technology cluster such as Apache Kafka/AWS Kinesis/Azure EventHubs 
- Other groups at the customer will need streaming access to the data

Requirements:

- User behaviour event data needs to be consumable from the pub-sub Cluster within 120 seconds of being created by the mobile application.
- Data Analysis team will need access to an environment to perform on-demand adhoc analysis
- Data Science team will need cleansed and backfilled data both on stream and batch capacity to build, train and re-train their models 

Your task is to: 
1. Design a workflow to move the user behaviour event data from the application to a backend and provide insights into the data pipelines that you foresee.
2. Explain how the workflow would provide the data to the batch process in Part 1 Algorithm. 
3. Explain any adaptations that your work from Part 1 - Algorithm would need to work as a streaming process.
4. Highlight any important design decisions you make, and describe briefly why you made those decisions. Important parts to address include:
    - Latency
    - Scalability & Data Volume
    - Robustness
    - Failure Modes
    - Delivery Guarantees
    - Technology selections

## Solution
**1.** Workflow to move the user behaviour event data from the application to a backend.

![diagram](diagram.png)

Workflow and Technology selections:
- (A) The application send the event to An Amazon API Gateway REST API that acts as a proxy to Amazon Kinesis Data Streams.
- (B) AWS Kinesis Data Streams to store the incoming streaming data.
- (C) AWS Lambda function that processes the records from the data stream. AWS Kinesis Data Streams features open-ended support for data consumers. Therefore, it can work with multiple consumers in case other teams need to consum the streaming data.
- (D) Amazon Redshift warehouse, because of its SQL interfaces and the ease with which it processes petabytes of data. Reports, analytics, and visualizations are powered using Periscope Data. In such a way, the data is easily spread across different teams (Data Analysis/Data Science), allowing them to make decisions based on data.
- (E) Errors and failed records that occur during AWS Lambda (B) processing are annotated, and the events are stored in Amazon Simple Queue Service (Amazon SQS). The queue stores metadata for failed batch records and Lambda errors, allowing customers to retrieve these records and determine the next steps to resolve them.
- (F) An Amazon CloudWatch dashboard monitors application health, progress, resource utilization, events, and errors. 

**2.** The workflow design will provide the data to the batch process in Part 1 through Redshift. We need to use a ODBC connection and query the needed data to generate metrics and store the result.

**3.** Explain any adaptations that your work from Part 1 - Algorithm would need to work as a streaming process

In Part 1 I am using pyspark to calculate the required metric. In order to adapt that work as a streaming process we can use Spark Streamig to consume the data directly from kinesis and store the result on Redshift.

**4.** Important decisions:
- This is a serverless design.
- Low latency:
    - Using AWS kinesis Data Streams we can have the data available to multiple real-time analytics applications within 70 milliseconds of being collected.
    - Amazon Redshift automatically adds or removw capacity in a few seconds, so your queries continue to be served with low latency. 
- Scalability
    - We can create a scaling solution without manual intervention to add and remove shards to AWS Kinesis Data Streams.
- Failure Modes
    As we are using custom lambdas to process the events we can control the next steps by any specific error, for example generate a special alert and store the failed event in S3 in parquet format. Further, With Amazon CloudWatch we are going to be able to validate the logs in the workflow.
