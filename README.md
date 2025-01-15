# AWS-Glue-to-ingest-495-RDB-tables-in-AWS-Databricks-as-a-part-of-Renaissance-program
Renaissance Program aims to launch new products (new software defined products (SD Internet / SD Ethernet)) for Mid-market, OE and OW aiming faster time to market, simplified delivery process, less manual touch points, reduced operational costs, reusable APIs aligned to global TMF standards.

The Renaissance Foundation release will on-board new AMDOCs CPQ tool which will provide quoting and agreement management capabilities for Mid-Market.

It aims to reduce the manual steps involved in existing CPQ’s quoting process, introduces few automated validations and service qualification, centralized Catalog which enables onboarding new products a config driven change in CPQ, and provides bulk site product configuration.

The scope of Renaissance Foundation release is to enable buy journey of product - Enterprise Internet (no-NTU ) on NBN-EE (RFSale) for Mid-market customers. The business processes such as CRM activities, Address validations, Service Qualifications, Quoting and Contract Generation for Mid-Market customers would be implemented.

As a part of Renaissance program, I was tasked with ingesting Helix 495 RDB Tables in databricks bronze layer.

In order to efficiently ingest 495 RDB tables in AWS Databricks bronze layer, I used AWS Glue as an ETL tool. 
Here are the steps that I implemented to complete the ingestion. They are detailed below:
1) I set up AWS Glue crawlers
AWS Glue crawlers can automatically discover and catalog RDB tables, making them accessible for ETL operations. In order to set up crawlers, I navigated to crawlers in AWS Glue console and then clicked add crawlers. Subsequently, I treated the data store as my RDBMS and provided connection details. Then, I brought the output to new or existing database in AWS Glue Data catalog followed by scheduling the crawler multiple times at a suitable time as per the business.

2) Creation of ETL Glue job with PySpark
I developed an ETL job to extract data from RDB tables, transformed it with filters or join and then loaded the data into Databricks bronze layer. I defined the ETL job. In order to do that, I went to AWS Glue console, went to jobs and then added jobs. I chose spark as the ETL language and Python for Pyspark. I assigned the IAM role with both read and write permissions. I specified the script location in Amazon S3. I developed the Pyspark script in which I read data from AWS Glue Data catalog tables. Based on the business requirements, I did necessary transformations like filters and aggregates. Then, I wrote the transformed data to Amazon S3 in a parquet format in order to store in a scalable manner.

3) Integrate with Databricks

After finish loading data to S3, I was able to access data from databricks bronze layer. Before bringing data to bronze layer, I created DDLS and views in AWS Redshift. In order to access the data, I mounted S3 bucket in databricks. I used Databricks' DBFS to mount the S3 bucket:

dbutils.fs.mount(
    source="s3a://your-bucket/bronze-layer/",
    mount_point="/mnt/bronze-layer",
    extra_configs={"spark.hadoop.fs.s3a.access.key": "your-access-key",
                   "spark.hadoop.fs.s3a.secret.key": "your-secret-key"}
)

I read data into databricks using spark script in python

df = spark.read.parquet("/mnt/bronze-layer/")
df.show()

4) Automate the processs
As there were 495 tables, I modified the Pyspark script to loop through 495 tables in order to read and write each table's data. Later, I scheduled the ETL job into mutiple agreed interval as per the business from the AWS Glue console.

5 Monitor and optimize
I regularly monitored the ETL job's performance and logs in the AWS Glue console. I also optimized the script and AWS Glue resources to handle the large volume of tables effectively. 

By taking the aforementioned steps, I successfully ingested 495 RDB tables in databricks bronze layer.
