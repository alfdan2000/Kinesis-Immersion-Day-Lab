# Kinesis-Immersion-Day-Lab

## Section 0 – Logging in

We have prepared accounts for you to use during today’s Immersion day. To login to your account, goto https://dashboard.eventengine.run/ and enter the hash provided to you by the facilitators.

 ![who are you](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/Who+are+you.png)

The AWS console can be access by clicking the "AWS Console" button in the top right.

![user dashboard](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/User+Dashboard.png)
 
# Section I - Data Ingestion & Storage Layers
## Lab 1.1 - Create simulated data in real-time with KDG

In lab 1, you will deploy an open-source serverless real-time data generator tool to simulate data in real-time and ingest into AWS Kinesis Firehose. We will simulate product catalogue data with random values. The data will be used in upcoming labs to demonstrate storage, processing, reporting and visualization. 

The tool is called Kinesis Data Generator (KDG), and is available on GitHub under:  https://github.com/awslabs/amazon-kinesis-data-generator 

KDG is a UI that simplifies how you send test data to Amazon Kinesis Streams or Amazon Kinesis Firehose. Using the Amazon Kinesis Data Generator, you can create templates for your data, create random values to use for your data, and save the templates for future use.

<ol type="1">
<li>Click <a href =https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=Kinesis-Data-Generator-Cognito-User&templateURL=https://lab-cloudformation-templates.s3.amazonaws.com/cognito-setup.json>here</a> to get started with installing and configuring the KDG on your AWS account. Because the project is a collection of static HTML and JavaScript, No servers are required.</li>
<br>
Deploy Cloudformation Template from the above link
<ol type="A">
<br>
<li>Hit <b><i>Next</i></b> on the <b><i>Create Stack</b></i> page.</li>
<li>Provide a username and password and hit <b><i>Next</b></i>.</li>
<ol type="i">
<li>Username: <i>kdgpoc</i> </li>
<li>Password <i>(e.g. kdgpoc1234)</i></li>


![create stack](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/create+stack.png)

<li>Hit <i><b>Next</i></b> on the <i><b>Configuration Stack Options</i></b> page without making any changes.</li>



<li>At the <i><b>Review Kinesis-Data-Generator-Cognito-User</i></b> page, make sure to check the checkbox for “I acknowledge that AWS CloudFormation might create IAM resources.” At the bottom of the page, then hit <i><b>Create Stack</i></b>.</li>

![stack creation](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/stack+creation+options.png)
 
<li>The stack creates a user password in a User DataBase (called User Pool) using the Amazon Cognito service.</li>
<li>Select <i><b>Outputs tab</i></b> of the CloudFormation Stack, which contains a URL generated for your KDG. Copy this URL to your browser and navigate to the page. </li>
</ol type="i">

![Kinesis data generator](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/kinesis+data+generator.png) 

</ol type="A">


<li>Login to KDG with the user/password you created during CloudFormation stack creation, and select your region (us-east-1). </li>
<li>At this stage, there’s no stream we can push data towards. Let’s define the structure of our simulated data using the parametrized template. Copy and paste it to the template tabs. Template to use:</li>

```
{
    "productName" : "{{commerce.productName}}",
    "color" : "{{commerce.color}}",
    "department" : "{{commerce.department}}",
    "product" : "{{commerce.product}}",
    "imageUrl": "{{image.imageUrl}}",
    "dateSoldSince": "{{date.past}}",
    "dateSoldUntil": "{{date.future}}",
    "price": {{random.number(
        {
            "min":10,
            "max":150
        }
    )}},
    "campaign": "{{random.arrayElement(
        ["BlackFriday","10Percent","NONE"]
    )}}"
}
```

Below is a sample of generated values (each record will be random). 

```
{    
  "productName" : "Generic Granite Ball",    
  "color" : "teal",    
  "department" : "Beauty",    
  "product" : "Cheese",    
  "imageUrl": "http://lorempixel.com/640/480",    
  "dateSoldSince": "Mon Dec 04 2017 19:19:50 GMT+0300 (GMT+03:00)",    
  "dateSoldUntil": "Fri May 17 2019 08:56:56 GMT+0300 (GMT+03:00)",    
  "price": 64,    
  "campaign": "10Percent"
}
```
![kinesis data generator 2](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/kinesis+data+generator+2.png)

Hint: KDG makes use of an open-source toolkit called Faker.js (available in GitHub: https://github.com/marak/Faker.js/ ). You can customize the template as you wish. Check Faker.js documentation for details.

<li>Press <b><i>Test Template</i></b> to generate a few records.</li>
 
![sample records](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/sample+records.png) 

You have successfully created KDG, and are ready to ingest data to the AWS Kinesis Firehose. Please proceed to the next lab.
</ol type="1">

 
## Lab 1.2 - Create Kinesis Firehose Delivery Stream to load Stream data on S3

In this lab, you will create a Firehose delivery stream from the AWS Management Console, configure it with a few clicks to store incoming stream data into S3, and start sending data to the stream Kinesis Data Generator (created in Lab 1) as data source.

<i><b>Amazon Kinesis Firehose</i></b> is a serverless service used to reliably load streaming data into data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and 3rd Party services (e.g. Splunk), enabling near real-time analytics. 


Amazon Kinesis Firehose benefits are:
  *	No explicit provisioning or scaling, 
  *	Extremely simple APIs and console, 
  *	Guaranteed data capture and reliable delivery.
  *	It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. 
  *	It can also batch, compress, transform, and encrypt the data before loading it, minimizing the amount of storage used at the destination and increasing security.  

<ol type="1">
<li>Click <a href=https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=IAM-Roles&templateURL=https://lab-cloudformation-templates.s3.amazonaws.com/sf-immersion-day.json>here</a> to get started with setup IAM roles necessary for todays labs in your AWS account. Deploy Cloudformation Template from the above link</li>
<ol type="A">
<li>Hit <i><b>Next</i></b> on the <i><b>Create Stack</i></b> page.
<li>At the <i><b>Specify Stack Details</i></b> page, enter <i><b>&#60;your initials + two digit number&#62;</b></i> for the parameter. The full length of the parameter <b><i><u>must be under 5 characters</u></b></i>.
<li>Hit <b><i>Next</b></i> on the <b><i>Configuration Stack Options</b></i> page without making any changes
<li>At the <b><i>Review IAM-Roles</b></i> page, make sure to check the checkbox for <i>“I acknowledge that AWS CloudFormation might create IAM resources.”</i> At the bottom of the page, then hit <b><i>Create Stack</b></i>.
</ol type="A">

<li>Create a bucket with exactly the naming convention and press Create: bucket name: (name: <i><b>&#60;your initials + two digit number&#62;-tame-bda-immersion</b></i>. For instance <b><i>fs32-tame-bda-immersion</b></i> for Frank Sinatra) (Important, the CloudFormation template creates IAM policies according to a naming convention for simplicity. Therefore, please use the exact naming convention). 

![create bucket](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/create+bucket.png)
 
<li>Create a Firehose delivery stream and configure as follows:
<ol type="A">
<li>Stream name: <b><i>tamebda-rta-kinesisfh-prodcat</b></i>
<li>Source: <b><i>direct put</b></i>
<li>Transformation: <b><i>disabled</b></i>
<li>Conversion: <b><i>disabled</b></i>
<li>Destination: <b><i>S3 </b></i>
<ol type="i">
<li>(name: <i><b>&#60;your initials + two digit number&#62;-tame-bda-immersion</b></i>. For instance <b><i>fs32-tame-bda-immersion</b></i>)
<li>prefix: <b><i>raw/</b></i> (Important: Don’t forget the trailing “/” character. It shall be raw/ and not raw)
</ol type="i">
<li>buffer: <b><i>1 MB, 60 seconds</b></i>
<li>compression: <b><i>gzip</b></i>

![create delivery stream](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/create+delivery+stream.png)
 
 
<li>IAM:
<ol type="i">	
<li>IAM role: Select the role created by the CloudFormation template (AWS Console -> CloudFormation -> Stacks -> sldimmersion-> Outputs. The IAM role has a name like “ <b><i>IAM-Roles-tameFHoseRoleSless-&#60;UNIQUE ID&#62;</b></i>
 
![Firehose policy](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/firehose+policy.png)

<li>Policy: Select the role created by the CloudFormation template. The IAM policy has a name like: “FirehosePolicy”

![data generator tool](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/data+generator+tool.png)

</ol type="i">
</ol type="A">

<li>Return to Kinesis Data Generator tool, select the region you created Firehose Delivery stream (us-east-1), and select your stream. Start sending simulated data at 1000 messages per second rate (If the delivery stream doesn’t appear in KDG, simply logout and login to the KDG to refresh). 

![data to kinesis](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/data+to+kinesis.png)
 
<li>Wait a few minutes. From AWS Console -> Firehose -> Monitoring tab, check activity on your delivery stream.

![bda immersion](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/bda+immersion.png)

<li>From AWS console -> S3, go to the S3 folder you created. You should see data in <b><i>s3://&#60;bucket-name&#62;/&#60;prefix&#62;/&#60;year&#62;/&#60;month&#62;/&#60;day&#62;/&#60;hour&#62;</b></i>format. For instance: <b><i>s3:// &#60; your initials + two digit number &#62;-tame-bda-immersion/raw/2018/11/14/12</b></i>.
<ol type="A">
<li>Hour is in UTC format.
<li>Depending on your buffering configuration, Firehose writes data to S3 either when size limit (1 ML in our case) or time limit (60 seconds in our case) is reached, whichever condition is satisfied first triggers data delivery to Amazon S3.
<li>Download one file and view its content.
</ol type="A">
<li><b><i>Hint</b></i>: Don’t stop sending events from KDG. Let it run while you are working on the other labs. Since the KDG is serverless, there are no servers to pay when there is no traffic. Kinesis Firehose is charged per GB, and during the whole day, the scenario above will not ingest data more than a few hundred MBs. 

 ![S3 image](https://csaimmersiondaymaterial.s3-us-west-2.amazonaws.com/Kinesis/s3+picture.png)


## Summary & Next Steps

Congratulations. You have successfully created an ingestion pipeline without servers, which is ready to process huge amounts of data. 

So, what’s next? In the next lab, you will create the processing pipeline to convert, transform the data from the ingestion layer.

