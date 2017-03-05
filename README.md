# GooglePlay with Nutch, Pig and Hive

# How we do?
## GooglePlayCrawler
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
```
org.apache.nutch.googleplay.GooglePlayCrawler s3://'your directory'/seed -numFetchers 100 -depth 3 -finalOutput s3://'your directory'/
```
