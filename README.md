# GooglePlay with Nutch, Pig and Hive

# How to do
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
