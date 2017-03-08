# GooglePlay with Nutch, Pig and Hive
•	Parsed GooglePlay website and designed a distributed web crawler with Apache Nutch Java API

•	Acquired 2000 app data with AWS EMR and EC2 in 1 minute and stored the data in S3

•	Loaded data with PIG LoadFunc and then filtered data using PIG

•	Construct data analysis using Hive
# How we do?
## GooglePlay Crawler (Nutch, Hadoop MapReduce, AWS EMR)
Our crawler is based on Nutch 1.12, so you need to git clone nutch repository first.
```
git clone https://github.com/apache/nutch
git checkout release-1.12
```
Then, we need to customize the crawler to fit the need of crawling data from GooglePlay
```
patch -p1 < googleplaycrawler.patch
```
Fixed the skew problem in fetch job
```
patch -p1 < fixskew.patch
```
Apply the change
```
ant job
```
Create a seed file which contains the web page we plan to crawl first.
```
echo "https://play.google.com/store/apps/details?id=com.facebook.orca" > seed
```
Run googleplaycrawler on single node cluster
```
hadoop fs -put seed .
hadoop jar build/apache-nutch-1.12.job org.apache.nutch.googleplay.GooglePlayCrawler seed -numFetchers 10
```
Check output
```
hadoop fs -text nutchdb/segments/xxxxx/parse_data/part-00000/data
```
You can also send the jar and seed file to AWS S3, and run the job in EMR
![wechatimg2](https://cloud.githubusercontent.com/assets/25273483/23620380/ad783280-0264-11e7-977c-c25ca9f353a4.jpeg)

## Nutch Parsed Data Loader (Pig, AWS S3)
Build package
```
mvn package
```
Access Amazon S3 and change your hadoop core-site.xml settings
```
<property>
    <name>fs.s3n.impl</name> 
    <value>org.apache.hadoop.fs.s3native.NativeS3FileSystem</value>
</property>
<property>
    <name>fs.s3n.awsAccessKeyId</name>
    <value>your aws access key</value>
</property>
<property>
    <name>fs.s3n.awsSecretAccessKey</name>
    <value>your aws secret key</value>
</property>
```
Run the loadgoogle.pig--remember to change the directory to your s3 directory
```
pig loadgoogle.pig
```
