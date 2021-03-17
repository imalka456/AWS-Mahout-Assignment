# AWS-Mahout-Assignment

https://aws.amazon.com/blogs/big-data/building-a-recommender-with-apache-mahout-on-amazon-elastic-mapreduce-emr/

## Introduction

This is to demonstrate how to build an analytic job with Mahout on EMR, we’ll build a movie recommender. 
We will start with ratings given to movie titles by users in the MovieLens data set, which was compiled by the GroupLens team, and will use the “recommenditembased” example to find most-recommended movies for each user.

## Building a Recommender

+ Start AWS EMR server 

https://console.aws.amazon.com/elasticmapreduce/home

+ Get MovieLens Data
```bash
wget http://files.grouplens.org/datasets/movielens/ml-1m.zip
unzip ml-1m.zip
```
+ Convert ratings.dat, trade “::” for “,”, and take only the first three columns:
```bash
cat ml-1m/ratings.dat | sed 's/::/,/g' | cut -f1-3 -d, > ratings.csv
```
+ Put ratings file into HDFS:
```bash
hadoop fs -put ratings.csv /ratings.csv
```
+ Run the recommender job: 
```bash
mahout recommenditembased --input /ratings.csv --output recommendations --numRecommendations 10 --outputPathForSimilarityMatrix similarity-matrix --similarityClassname SIMILARITY_COSINE
```
+ Look for the results in the part-files containing the recommendations:
```bash
hadoop fs -ls recommendations
hadoop fs -cat recommendations/part-r-00000 | head
```

## Building a Service

+ Get Twisted, and Klein and Redis modules for Python. In the tutorial, these are installed using easy_install. But since there were few errors, used pip3 for installation. (Python3 default installed)
```bash
sudo pip3 install twisted
sudo pip3 install klein
sudo pip3 install redis
```
+ Install Redis and start up the server. 
```bash
wget http://download.redis.io/releases/redis-2.8.7.tar.gz
tar xzf redis-2.8.7.tar.gz
cd redis-2.8.7
make
./src/redis-server &
```
+ Copy hello.py

https://github.com/DulanGit/AWS-Mahout-Assignment/blob/master/hello.py

+ Start the web service.
```bash
twistd -noy hello.py &
```
+ Test the web service with user id “37”:
```bash
curl localhost:8080/37
```
