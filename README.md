# Google Cloud Big Data and Machine Learning ---
## Course Introduction
        -----------------------------------------
                2. Big Data/ML Products
        -----------------------------------------
         1. Compute Power | Storage | Networking
        -----------------------------------------
                   0. Security
        -----------------------------------------
You can think of the google cloud infrastructure in terms of these 3 layers. At the base is security and networking which lay the foundations to support all of googles infrastructure and applications. Then we have compute and storage which are decoupled so they can scale independently based on needs and on top we have big data and ML products which enable us to perform tasks to ingest, store, process, and deliver business insights, data pipelines, and ML models.
## Big Data and Machine Learning on Google Cloud
### Compute
Google offers a range of computing services:
1. Compute Engine
a IaaS offering which provides raw compute, storage and network capabilities organized virtually into resources that are similar to physical data centers. Provides maximum flexibility for those who prefer to manage server instances themselves. 
2.  Google Kubernetes Engine (GKE)
runs containerized applications in a Cloud environment, as opposed to on an individual virtual machine like Compute Engine. A container represents code packaged up with all its dependencies.
3.  App Engine
A fully managed PaaS (platform as a service) that provides access to infrasstructure appliation needs. This allows more resources to be focused on app.iation logic.
4. Cloud Functions
Executes code and responds to events like when a new file is uploaded to cloud storage. It is a completely serverless execution environment, often referred to as Functions as a service
### Storage
For proper scaling capabilities, compute and storage are decouples. (major diference between Cloud and desktop computing)
With the Compute engine you can install and run a database on a virtual machine (like in a datacenter) Alternatively GCP offers fully managed database and storage services;
1. Cloud storage
2. Cloud Bigtable,
3. Cloud SQL
4. Cloud Spanner
5. Firestore


_structured vs unstructured data_

The right option depends on the data type and the business need unstructured data includes documents, images and audio files. This is best suited to Cloud storage which has 4 primary stroage classes
1. Standard Storage - best for requently accessed data (hot data) or data that is only stored for short periods of time
2. Nearline Storage - best for infrequent data (once per month) e.g. backups, archiving, longtail multimedia content
3. Coldline Storage - Once every 90 days
4. Archive Storage - Once per year

Structured data includes information stored in tables. It can be split into transactional workloads and analytical workloads. Transactional workload stem from online transactional processing systems, which are used when fast data inserts and updates are required to build row-based records. This is usually to maintain a system snapchat. They require relatively standardized queries that impact only a few records.  Analytical workloads which stem from online analytical processing systems, which are used when entire datasets need to be read. They often require complex queries.
![image](https://user-images.githubusercontent.com/80007111/182018919-060c4abe-3aeb-45c6-93b8-30f6a5c16682.png)

### Big Data and ML products
Google ML and Big Data products can be divided into 4 general categories along the data to AI workflow
1. Ingestion and process - Pub/Sub, Dataflow, Dataproc & Coud Data Fusion
3. Storage - listed above
3. Analytics - BigQuery, Google data studio and looker
4. Machine Learning

     4.1. ML development platform - Vertex AI, Auto ML, Vertex AI workbench, Tensor Flow

     4.2. AI Solutions - Document AI, Contact Center AI, Retail Product Discovery, Healthcare data engine

### Reading List
* GCP product description cheat sheet - https://googlecloudcheatsheet.withgoogle.com/

### Lab 1

__Public datasets__

Go to `BigQuery > Resources > Add Data > Explore public datasets` to add publically available datasets.

You can query the datasets using SQL syntax:

```
SELECT
  name, gender,
  SUM(number) AS total
FROM
  `bigquery-public-data.usa_names.usa_1910_2013`
GROUP BY
  name, gender
ORDER BY
  total DESC
LIMIT
  10
```

Before you run the query, the query validator in the bottom right will show much data is going to be run.

__Creating your own dataset__

Resources seem to have the following structure:

`Resources > Project > Dataset > Table`

To add your own dataset:

`Resources > click Project ID > Create dataset`
(For example babynames)

Then, we can add a table to this dataset.

`Click dataset name > Create table`

* Source > upload from file
* File format > CSV
* Table name > names_2014
* Schema > name:string,gender:string,count:integer

Creating the table will run a job. The table will be created after the job completes.

Click preview to see some of the data.

Then you can query the table like before.

SQL Syntax for BigQuery: https://cloud.google.com/bigquery/docs/reference/standard-sql/

## Data Engineering for Streaming Data
### Introduction
Streaming Data vs Batch processing:

* Batch processing is when the processing and analysis happens on a set of stored data (e.g payrole/billing system which is processed on a monthly basis)
* Streaming data is a flow of data records generated by various data sources. The processing happens as the data flows througha system (e.g. fraud detection).

Modern data processing has progressed from legacy batch processing towards working with real-time data streams (e.g. you no longer have to download a movie to watch it)

### Big Data Challenges
Data engineers and scientists are facing 4 major challenges, the 4 Vs
1. Variety

* Data can come in from a variety of different sources and in various formats

2. volume

* Volume can range from gigabytes to petabytes. Pipeline code and infrastructure must scale with those changes to avoid grinding to a halt

3. Velocity

* Data often needs to be processed in near real time as soon as it reaches the system. There also needs to be a way of handling data that is late, bad data or needs to be transformed mid flight

4. Veracity

* This refers to the data quality. Because of the multitude of data dimensions resulting from teh different data types and sources, data often comes with inconsistencies and uncertainties

### Message-Oriented Architecture
Data ingestion is where large amounts of streaming data is received. This data normally comes from 1000s different events that are all happening asynchronously (e.g. IoT)

Pub/Sub is a distibuted messaging service that can recieve messages from a variety of device streams. It ensures at least one delivery of received messages to subscribing applications with no provisioning required. Pub/Sub APIs are open, the services global by default, and it offers end to end encryption.

#### Pub/Sub Artitecture
1. Upstream source data comes in from devices all over the globe
2. This data is ingested into pub sub, which is teh first point of contact within the system
3. Pub/Sub reads, stores and broadcasts to any subscribers of this data topic that new messages are available.
4. As a subscriber of Pub/Sub data flow can ingest and transform those messages in an elastic streaming pipeline and output the results into an analytics data warehouse like big query
5. Finally you can connect a data visulization tool like looker or data studio to visulize and monitor the results of a pipeline or an AI or ML tools such as Vertex AI to explore the data, to uncover business insights or help with predictions.

A central element of Pub/Sub is the topic. (topic = radio antenna)
* This is like a radio antenna, whether your radio is playing music or not the antenna itself is always there
* If music is broadcast on a frequency nobody is listening to the stream of music still exists

Similarly a publisher can send data to a topic that has no subscriber to revieve it or a subscriber can be waiting for data from a topic that isnt getting data sent to it (like listening to static)

An operational pipeline has a publisher sending data to a topic that an application is subscribed to. That means that there can be zero, one or more publishers and zero, one or more subscribers related to a topic. These are completely decoupled so they're free to break without affecting their counterparts.

Pub/Sub supports many different inputs and outputs and you can even publish a Pub/Sub event from one topic to another. 



### Designing Streaming Pipelines with Apache Beam

After messages have been captured reliably into a data warehousewe need a pipeline that can match Pub/Sub scale and elasticity to do it. This is where Dataflow comes in, Dataflow creates a pipeline to process both streaming data and batch data.
* Here process refers to the steps to extract, transform and load data (ETL)

During pipeline design phase we need to consider the following questions:

1. Will the pipeline code be compatible with both batch and streaming data or will it need to be refactored?
2. Will the pipeline code software development kit or SDK being used have all the transformations, mid-flight aggregations and windowing and be able to handle late data?
3. Are there existing templates or solutions that should be referenced?

Apache Beam is a popular open source pipeline design solution. It is a unified programming model to define and execute data processing pipelines, including ETL, batch, and stream processing
* Unified means it uses a single programming model for both batch and streaming data
* Its portable meaning it can work on multiple execution environments like Dataflow and Apache Spark amoung others.
Its extensible meaning it allows you to write and share your own connectors and transformation libraries

The Apache Beam software development kit (SDK) is a collection of software development tools in one installable package. It provides a veriety of libraries for transformations and data connectors to sources and sinks

Apache Beam creates a model representation from your code thats protable across many runners
* runners pass off your model for execution on a variety of different possible engines with Dataflow being a popular choice

### Implementing Streaming Pipelines on Cloud Dataflow
An execution engine needs to be identified to implement pipelines such as those made in Apache Beam

When Choosing an implementation method consider the following:
1. how much maintenance overhead is involved? 
2. Is the infrastructure reliable? 
3. How is the pipeline scaling handled? 
4. How can the pipeline be monitored? 
5. Is the pipeline locked into a specific service provider?

Dataflow
* Dataflow is a fully managed service for executing Apache Beam pipelines within the Google Cloud ecosystem
* Since data flow is built on Google's infrastrucure it allows for reliable auto-scaling to meet data pipeline demands
* Dataflow is serverless and NoOps (no operations)
       
       - a NoOps environment is one that doesn't require management from an operations team as maintenance, monitoring and scaling are automated
       
       - Serverless computing is a Cloud computing execution model. so the cloud platform manages infrastructure tasks on behalf of the users. e.g. resource provisioning, performance tuning and ensuring pipeline reliablity
      
When dataflow receives a job it;
1. It starts by optimizing a pipeline model's execution graph to remove any inefficiencies. 
2. It then schedules out distributed work to new workers and scales as needed. 
3. After that, it auto-heals any worker faults. 
4. From there, it automatically re-balances efforts to most efficiently use its workers. 
5. Finally, it outputs data to produce a result. (e.g. output to bigquery)

Dataflow has many templates which cover common use cases across Google Cloud products. These can be broken into 3 sets
* Streaming templates

        - for processing bulk data or batch load data. e.g. Pub/Sub to BigQuery, Pub/Sub to cloud storage, Datastream to BigQuery and Pub/Sub to mongoDB.
        
* Batch templates

        - for processing bulk data or batch load data. e.g. BigQuery to Cloud Storage, Bigtable to Cloud Storage, Cloud Storage to BigQuery and Cloud Spanner to Cloud Storage

* Utility templates

        - Utility templates address activities related to bulk compression, deletion and conversion  


### Visulisation with Looker

Looker supports BigQuery as well as more than 60 SQL database products commonly referred to as dialects. It allows developers to define a semantic modeling layer on top of databases using looker modeling language LookML.
* LookML defines logic and permissions independent from a specific database or a swqual language which frees data engineer from interacting with individual databases to focus more on business logic across an organization
* Looker is 100% web based making it easy to integrate into workflows and share
* Looker also has an API which can be used to embed looker reports in other applications

Looker dashboards can be created to provide straightfowards presentations to help you and your colleagues quickly see a high level business status. To share a looker dashboard you schedule delivery through storage services like Google Drive, slack and dropbox

### Visulisation with Data Studio

DataStudio is integrated into BigQuery making data visulisation possible with just a few clicks. Therefore leveraging data studio doesn't require support from an administrator to establish a data connection.

Three steps needed to create a Data Studio dashboard
1. Choose a template, you can start with either a pre built template or a blank report.
2. link the dashboard to a data source. This may come from big query, a local file, or a google application like google sheets
3. explore your dashboard

### Lab 2: Creating a Streaming Data Pipeline for a Real-Time Dashboard with Dataflow

__Cloud Pub/Sub topics__

Cloud Pub/Sub lets decouples senders and receivers. Senders send messages to a Cloud Pub/Sub _topic_, and receivers subscribe to this topic.

__Create a BigQuery dataset_

Messages published into Pub/Sub will be stored in BigQuery.

1. In Cloud Shell, run `bq mk taxirides` to create a dataset called `taxirides` in BigQuery
2. Now create a table inside the dataset:

```
bq mk \
--time_partitioning_field timestamp \
--schema ride_id:string,point_idx:integer,latitude:float,longitude:float,\
timestamp:timestamp,meter_reading:float,meter_increment:float,ride_status:string,\
passenger_count:integer -t taxirides.realtime
```
OR: navigate to BigQuery on the GCP UI > View actions > create dataset. then fill out dataset details and click create dataset. then click into the newly created dataset and click create table then fill in table details and click create table.

__Create a Cloud Storage bucket__

1. Storage > Create Bucket
2. Name must be globally unique (e.g., project id)

__Setup Cloud Dataflow Pipeline__

1. Navigation > Dataflow
2. Create job from template
3. Type > Cloud Pub/Sub topic to BigQuery template
4. Input topic > projects/pubsub-public-data/topics/taxirides-realtime
5. Output table > qwiklabs-gcp-72fdadec78efe24c:taxirides.realtime
6. Temporary location > gs://qwiklabs-gcp-72fdadec78efe24c/tmp/
7. Click run job

Cloud Dataflow will now show a visualization of the Dataflow job running.

__Analyze streaming data__

You can check the data in BigQuery:

`SELECT * FROM taxirides.realtime LIMIT 10;`

See aggregated per/minute ride data:

```sql
WITH streaming_data AS (

SELECT
  timestamp,
  TIMESTAMP_TRUNC(timestamp, HOUR, 'UTC') AS hour,
  TIMESTAMP_TRUNC(timestamp, MINUTE, 'UTC') AS minute,
  TIMESTAMP_TRUNC(timestamp, SECOND, 'UTC') AS second,
  ride_id,
  latitude, 
  longitude,
  meter_reading,
  ride_status,
  passenger_count
FROM
  taxirides.realtime
WHERE ride_status = 'dropoff'
ORDER BY timestamp DESC
LIMIT 100000

)

# calculate aggregations on stream for reporting:
SELECT 
 ROW_NUMBER() OVER() AS dashboard_sort,
 minute,
 COUNT(DISTINCT ride_id) AS total_rides,
 SUM(meter_reading) AS total_revenue,
 SUM(passenger_count) AS total_passengers
FROM streaming_data
GROUP BY minute, timestamp
```

__Explore in Data Studio__

1. Click `Explore in Data Studio`
2. Set up dimensions and metrics as desired

Once finished, stop the Cloud Dataflow pipeline job.
