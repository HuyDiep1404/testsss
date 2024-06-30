# Introduction
In this project, we try to help one music streaming startup, Sparkify.
Project Datasets
You'll be working with two datasets stored in S3. Here are the S3 links for each:

+ Song data : s3://udacity-dend/song_data
+ Log data: s3://udacity-dend/log_data
To properly read log data s3://udacity-dend/log_data, you'll need the following metadata file:

+ Log metadata: s3://udacity-dend/log_json_path.json
Song Dataset
The first dataset is a subset of real data from the Million Song Dataset(opens in a new tab). Each file is in JSON format and contains metadata about a song and the artist of that song. The files are partitioned by the first three letters of each song's track ID. For example, here are file paths to two files in this dataset.

+ song_data/A/B/C/TRABCEI128F424C983.json
+ song_data/A/A/B/TRAABJL12903CDCF1A.json
Log Dataset
The second dataset consists of log files in JSON format generated by this event simulator(opens in a new tab) based on the songs in the dataset above. These simulate app activity logs from an imaginary music streaming app based on configuration settings.

The log files in the dataset you'll be working with are partitioned by year and month. For example, here are file paths to two files in this dataset.

+ log_data/2018/11/2018-11-12-events.json
+ log_data/2018/11/2018-11-13-events.json

Log JSON Metadata
The log_json_path.json file is used when loading JSON data into Redshift. It specifies the structure of the JSON data so that Redshift can properly parse and load it into the staging tables.

In the context of this project, you will need the log_json_path.json file in the COPY command, which is responsible for loading the log data from S3 into the staging tables in Redshift. The log_json_path.json file tells Redshift how to interpret the JSON data and extract the relevant fields. This is essential for further processing and transforming the data into the desired analytics tables.

Schema for Song Play Analysis
Using the song and event datasets, you'll need to create a star schema optimized for queries on song play analysis. This includes the following tables.

# Fact Table
+ **songplays**  - records in event data associated with song plays i.e. records with page NextSong
songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent
Dimension Tables
+ **users** - users in the app
user_id, first_name, last_name, gender, level
+ **songs** - songs in music database
song_id, title, artist_id, year, duration
+ **artists** - artists in music database
artist_id, name, location, latitude, longitude
+ **time** - timestamps of records in songplays broken down into specific units
start_time, hour, day, week, month, year, weekday

# Steps to follow:

## 1/Create an IAM Role:
+ In the AWS Service group as the trusted entity, and choose Redshift service.
+ Under Select your use case, choose Redshift - Customizable, and then Next: Permissions.
+ On the Attach permissions policies page, search for and select the AmazonS3ReadOnlyAccess policy, and then click on the Next: Tags button.
+ enter RedshiftRole, and then choose Create Role.

## 2/Create Security Group:

- Security group name: redshift_security_group
- VPC: Choose the default VPC It is a VPC in a default region, and has a public subnet in each Availability Zone.We choose default VPC If a default VPC doesn't show up. 

In the Inbound rules section, click on Add Rule and enter the following values:

    Field	                Value
    Type	                Custom TCP Rule
    Protocol	        TCP
    Port range	        5439 The default port for Amazon Redshift is 5439, but your port might be different.
    Source type	        Custom
    Source	                0.0.0.0/0 (Anywhere in the world)

Outbound rules allow traffic to anywhere by default.

Click on the Create security group button at the bottom.

## 3/Create Cluster:

Provide a unique identifier, such as redshift-cluster-1, and choose the Free trial option. It will automatically, choose the following configuration:
1 node of dc2.large hardware type. It is a high performance with fixed local SSD storage.
2 vCPUs.
160 GB storage capacity.

### Database configurations.
- A few fields will be already filled up by default. Ensure to have to the following values:

        Field	                    Value
        Database name	            dev
        Database port	            5439
        Master user name	    awsuser
        Master user password	    Enter a password of your choice

- Cluster permissions (optional)
We will Choose the IAM role created earlier, RedshiftRole, from the drop-down and click on the Associate IAM role button.

Additional configurations
Toggle the button to turn off the "use defaults" feature, and expand the Network and security section. Choose the following values:

    Field	                            Value
    Virtual private cloud (VPC)	    Default VPC
                                        If you are not able to view/select the default VPC,
                                        refer to the resolution given here.
                                        You will have to create a cluster subnet group.
    VPC security groups	                Choose the redshift_security_group created earlier.

    Cluster subnet group	            Choose the default
                                        It is the one you have just created.
    Availability Zone	            No preference
    Enhanced VPC routing	            Disabled
    Publicly accessible	            Enable

Leave the rest of the values as default.
click on the Create cluster button at the bottom.

## Create S3 Buckets:
#### - General configuration
Provide the bucket-name and the region where you want to locate the bucket. The Required bucket name must be unique worldwide, and must not contain spaces or uppercase letters.

#### - Public Access settings:
You can choose public visibility. Let's uncheck the Block all public access option.

#### - Bucket Versioning and Encryption:
+ Bucket Versioning - Keep it disabled.
+ Encryption - If enabled, it will encrypt the files being stored in the bucket.
+ Object Lock - If enables, it will prevent the files in the bucket from being deleted or modified.

Click on the Upload button to upload files and folders into the current bucket.
Add 2 folders song_data,log_data and add files json into folders.
Add files log_json_path.json.

Add redshift database and IAM role info to dwh.cfg.

After drop table and create table.

+ Test by running with create_tables.py.

Run ETL pipeline.

+ Test by running with python etl.py.
