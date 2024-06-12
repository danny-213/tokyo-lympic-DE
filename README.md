# AZURE CLOUD DATA ENGINEER PROJECT - TOKYO OLYMPIC DATASE

## Abstract

This project goes through step by step to create a data warehouse for Kaggle Olympic dataset and providing analytics using a combination of technologies including Azure Data Factory, Data Lake Gen 2, Synapse Analytics, and Azure Databricks. The data is strutured in csv format but we can include semi/un-structured data to go the extra mile.
In a real world scenario, we can use just 1 app like Synapse

1. [The dataset used](https://github.com/darshilparmar/tokyo-olympic-azure-data-engineering-project/tree/main/data)
2. [Original Dataset link](https://www.kaggle.com/datasets/arjunprasadsarkhel/2021-olympics-in-tokyo/data)
3. [Live PowerBI Dashboard]()

## Azure storage account

After creating the proper subscription and resource group (parent folder for all azure tools in a project), a container is needed to store dataset
![Creating Container for the data](https://github.com/danny-213/tokyo-lympic-DE/blob/main/azure-storage.png)
![Creating a Folder Directory inside a Container](https://github.com/danny-213/tokyo-lympic-DE/blob/main/storage-container.png)
\*Upload a random file as place holder cus it seems it doesnt accept an empty folder

## Azure Data Factory

Create a new pipeline, then a copy action inside that pipeline
![Setup copy action with source](https://github.com/danny-213/tokyo-lympic-DE/blob/main/pipeline-task.png)
![Connect Source on Github HTTP GET Request](https://github.com/danny-213/tokyo-lympic-DE/blob/main/pipeline-task2.png) Set the Authentication type to anonymous (cus no credential needed to make the request)
![Connect Blob storage service with the Sink tab](https://github.com/danny-213/tokyo-lympic-DE/blob/main/pipeline-task1.png) => create a link service under your subscription
==> None Import schema for both Source & Sink

## Transform the data with Azure Databricks

Create a new compute cluster (Virtual machine) to run your code on
Mount the data to databricks

### Access to the blob storage account from Databricks: 2 methods

Mount point is like a usb plugin socket

1. Mount the blob storage container to Databricks with a SAS token

```
configs = {
  "fs.azure.sas.<container-name>.<storage-account-name>.blob.core.windows.net": "<SAS Token>"
}

dbutils.fs.mount(
  source="wasbs://<container-name>@<storage-account-name>.blob.core.windows.net",
  mount_point="/mnt/blobstorage",
  extra_configs=configs
)
```

2. Make an App Registration & Giving Access to the app by going to Access Control in the blob container

   a. Create an App Registration with client, tenant ID and secret key
   ![Setup App Registration](https://github.com/danny-213/tokyo-lympic-DE/blob/main/app-reg.png)

   b. Create Role for the App to access Blob
   ![Access control to the blob by app](https://github.com/danny-213/tokyo-lympic-DE/blob/main/blob-access.png)
   ![In the Blob Container, set member for Blob Contributor Assigned Role](https://github.com/danny-213/tokyo-lympic-DE/blob/main/db-accessblob.png)
   ![Role as Blob contributor](https://github.com/danny-213/tokyo-lympic-DE/blob/main/blob-access.png)

   c. Mount with the App authentication info

   ```
   configs = {"fs.azure.account.auth.type": "OAuth",
   "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
   "fs.azure.account.oauth2.client.id": "",
   "fs.azure.account.oauth2.client.secret": '',
   "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/tanent_id/oauth2/token"}
   ```

   ```
   dbutils.fs.mount(
   source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net", # contrainer@storageacc
   mount_point = "/mnt/<any-mount-point-name>",
   extra_configs = configs)

   ```

## Azure Synapse Analytics: 1 stop shop for all the steps

### Ease of schema setup

![Connect to the blob storage and set schema](https://github.com/danny-213/tokyo-lympic-DE/blob/main/synapse-schema.png)

## Connect Power BI with Blob Storage Container / Datalake

### Anonymous Read

![Make the data public for any Power BI account to access](https://github.com/danny-213/tokyo-lympic-DE/blob/main/powerbi-access.png)

### Manage user access

![Get Endpoints in Setting](https://github.com/danny-213/tokyo-lympic-DE/blob/main/powerbi-connect.png)
![Get Account Key in Security & Networking](https://github.com/danny-213/tokyo-lympic-DE/blob/main/access-key.png)
