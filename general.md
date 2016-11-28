# Invasive species (Proof of Value)
The purpose of the Proof of Value is to lay the foundations to centralise and standardise the data in the cloud, through means of a semi-automated workflow. To accomodate the original requests and workflow that was proposed in the working document that was provided by INBO, we agreed on the following general flow. 

<p align="center">
  <img src="INBO_AF_00.png">
</p>

In order to minimize the workflow and ensure that it's easy-to-use and intuitive, we chose to only use tools that the researchers are familiar with :
- A new **web portal** for the interaction between the researcher and the AWS components
- **GitHub** to save and adapt the R transformation scripts and YAML files with the business rules

With the web portal the researcher has an overview of the files that are available in the AWS S3 buckets and ready to be processed, without having to worry about the underlying technical setup. All R-scripts and YAML files are to be changed and saved on GitHub to ensure traceability and versioning.

In the next parts we'll further elaborate on the AWS components and the difference stages of the workflow.

## Web Application
The web application is the portal for the researcher. The tabs in the menu bar accord with the folder structure in the S3 bucket and represent the order in which the transformations in the workflow are executed. The fact that a file is in a certain folder, gives an indication of its data quality and state of transformation. A few extra tabs were added to provide additional and necessary functionalities.


<p align="center">
  <img src="/Web_Raw.png">
</p>

| Tab | Description |
| --- | --- |
| **Upload to Raw** | Opens the page to upload raw data files from the data providers to the S3 folder "Raw"|
| **Raw** | Shows the files that are in the S3 folder "Raw" and ready for technical check |
| **Refused** | Shows the files that have been refused by the technical or content check |
| **Transformed** | Shows the files that have passed the technical check and are awaiting actions for the INBO researcher based on the results of the content reports. The dataset is in the DynamoDB tables "Raw" and "Tidy".
| **Validated** | Shows the files that have passed the technical and content check. The dataset is in the DynamoDB tables "Raw" and "Tidy". The files in the S3 folder "Validated" are the source for constructing the DynamoDB table "Final"
| **Overview** | Shows the end user an overview of the files that are in the S3 bucket with their current location and state of transformation by color code |
| **Reports** | Shows all available technical and content reports and grouped by the filename of their data source |
| **Processing Information (console)** | Shows the end user the information related to the on-going transformation process with current state of affaires and potential errors |

**Final tab**

This tab contains all the functionalities that are related to the transformation to the DynamoDB table "Final".
The end user has the capability to start the to_Final transformation or to generate a export of the current content of the DynamoDB table "Final" to csv file.
Every transformation comes with a report, that are shown in a list, same as the export.

<p align="center">
  <img src="/Web_Final.png">
</p>

**Throughput**

This tab opens a dialogue window that allows the end user to change the read/write settings of the different DynamoDB tables. This way the user can optimise the resources to be cost-efficient by opening the floodgates when files need to be processed and lower the settings afterwards.

<p align="center">
  <img src="/Web_Throughput.png">
</p>

**Google Sign-in**

The user has to log in to use the application. At this moment anyone with a Google account can log in, but the internal INBO team can modify this to a list of approved users.

## AWS Components used

<center>

| Component | Extra information |
| --- | --- |
| **Cognito** | User Authentication & Mobile Data Service |
| **DynamoDB** | NoSQL database with tables : Raw - Tidy - Final |
| **EC2** | Elastic Cloud Computing |
| **S3** | Simple Storage Service with folders : <br>Raw - Refused - Transformed - Validated - Reports - Final reports - Raw Export - Final Export
| **SNS** | Simple Notification Service |
| **SQS** | Simple Queue Service |

</center>

## General Workflow

<p align="center">
  <img src="/INBO_AF_00.png">
</p>

The general workflow can be summarized into 4 steps :
- **Step 1** : User uploads file to S3 bucket through the web application
- **Step 2** : Checks and transformations to DynamoDB table "Raw" -> [`to_Raw`](https://github.com/smartie-tien/test/blob/master/to_Raw.md)
- **Step 3** : Checks and transformations to DynamoDB table "Tidy" -> [`to_Tidy`](https://github.com/smartie-tien/test/blob/master/to_Tidy.md)
- **Step 4** : Checks and transformations to DynamoDB table "Final" -> [`to_Final`](https://github.com/smartie-tien/test/blob/master/to_Final.md)

<p align="center">
  <img src="/INBO_AF_01_upload_file.png">
</p>

To start the process and upload your data file in Step 1 through the web application, there are a few key rules that need to fulfilled.
- The filename convention is **{ProviderID}_{Filename}**
- The name convention is **case sensitive** !
- No spaces in the name
- The underlying regular expression is `\w\.-]+_[\w\.-]+\.[\w\.-]+$`
- Filename is checked with other files that are in the S3 bucket. If the bucket already contains a file with that name, the user will get an error message.

For more in-depth understanding of the checks and transformations of steps 2-4, a separate readme is provided per step.
