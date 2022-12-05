# ETL3: INGESTING STREAMING DATA WITH AMAZON KINESIS (similar to Apache Kafka)

Amazon Kinesis makes it easy to collect, process, and analyze real-time, streaming data so you can get timely insights and react quickly to new information. Amazon Kinesis offers key capabilities to cost-effectively process streaming data at any scale, along with the flexibility to choose the tools that best suit the requirements of your application. With Amazon Kinesis, you can ingest real-time data such as video, audio, application logs, website clickstreams, and IoT telemetry data for machine learning, analytics, and other applications. Amazon Kinesis enables you to process and analyze data as it arrives and respond instantly instead of having to wait until all your data is collected before the processing can begin.

Amazon Kinesis service has four (4) major components:
- Kinesis Video Streams: for streaming video from connected devices for analytics, ML, etc
- Kinesis Data Streams: A scalable real-time data streaming service that can continuously capture lots of data from different sources in gigabytes per second
- Kinesis Data Firehose: for loading and transforming data streams into AWS data stores for near real-time analytics
- Kinesis Data Analytics: for processing data streams in real time using the popular SQL language or Apache Flink

We will be using Kinesis Data Firehose in this session

Benefits of Kinesis Data Firehose
Load real-time data. Load streaming data into data lakes, data stores, and analytics tools for:
- Log and event analytics
- IoT data analytics
- Clickstream analytics
- Security monitoring

PROCEDURE:
- Amazon Kinesis Data Firehose to ingest streaming data, and write the data out to Amazon S3
- Amazon Kinesis Data Generator (KDG) to generate source of streaming data

**DELIVERY STREAM**

- s3 bucket prefix: streaming/!{timestamp:yyyy/MM/}
- s3 bucket error prefix: !{firehose:error-output-type}/!{timestamp:yyyy/MM/}
- For this Use case we set buffer size to 1 MB and buffer interval to 60 seconds

**STREAMING**

The Sakila Database from ETL2, was for a company that produced classic movies.
Now movies are available for rental through various streaming platforms.
The company receives information about their classic movies being streamed from their distribution partners in real time,
in a standard format. Using KDG, the streaming data information includes the following;

- Streaming timestamp
- Whether the customer rented, purchased, or watched the trailer
- film_id that matches the sakila film database
- the distribution partner name
- Straming platform
- the state the movie was streamed in

Amazon Kinesis Data Generator (KDG) is an open source tool that makes it easy to send data to Kinesis Data Streams and Kinesis Firehose

KDG Cloudformation template

 The CloudFormation template will create the following resources in your AWS account:

    An IAM role that gives the Lambda function permission to create Cognito resources.
    An IAM role that is assigned to authenticated Cognito users. This role has only enough permission to use the KDG.
    An IAM role that is assigned to unauthenticated Cognito users. This role has only enough permission to create Cognito analytics events.
    A Lambda function.

The Lambda function will create the following resources in your AWS account:

    A Cognito User Pool.
    A Cognito Federated Identity Pool.
    A Cognito User, with the username and password specified by you when you created the CloudFormation stack.
    The necessary relationships between the roles, users and pools.

KDG URL: <my-url>

Region: us-east-1
Delivery Stream: etl3-delivery
Records per second: 10

Record Template:
```
{
	"timestamp":"{{date.now}}",
	"eventType":"{{random.weightedArrayElement(
	{
	"weights": [0.3, 0.1, 0.6],
	"data":["rent","buy","trailer"]
	}
	)}}",
	"film_id":{{random.number(
	{
	"min":1,
	"max":1000
	}	
	)}},
	"distributor":"{{random.arrayElement(
	["amazon prime", "google play", "apple itunes", "twitter",
	"microsoft", "meta", "youtube"]
	)}}",
	"platform": "{{random.arrayElement(
	["ios", "android", "xbox", "social-media", "tech", "other"]
	)}}",
	"state":"{{address.state}}"
}
```
}
