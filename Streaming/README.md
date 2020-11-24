# Play with Twitter Data Using Spark Structured Streaming

# Introduction

Data is all around and Twitter is one of the golden sources of data for any kind of sentiment analysis. There are a lot of ways we can read Twitter live data and process them.
So the main idea of this section is to create a connection with the Twitter account to get the live feeds and then transform the data by using **Spark Structured Streaming**.

## What are we planning to do

From live tweet feeds get the count of different hashtag values based on specific topic we are interested.

In this case we use the word **"corona"** to get the count of different hashtag to include this word. 

## Steps to build streaming:

### 1) Create a Twitter Developer Account (get the authentication keys):

- a) Login to your developer account :

[](https://developer.twitter.com/en/apps)

- b) Click on ‘create app’, provide a name
- c) Now, regenerate API keys and auth token keys. We are going to use these keys in our code to connect with twitter and get the live feeds.
- d) Copy all 4 token keys as mentioned above: access_token, access_secret_token, consumer_key and consumer_secret_key

**Note:** If you are new in twitter developer, you need to fill some info about project and you in when you need to create a new project.

### 2) Create a databricks community account or use one if you have it

To help you I found that to create an account:

[https://docs.databricks.com/getting-started/try-databricks.html](https://docs.databricks.com/getting-started/try-databricks.html)

### 3) Program flow

There will be two different classes.

1. **Tweet_Listener** (python programming)
2. **StreamingTweetData** (Spark Structured Streaming)

**Tweet_Listener:**

**Purpose:**

I) Import all necessary libraries to create connection with Twitter, read the tweet and keep it available for streaming.

II) Read the incoming tweet JSON file (The inflow tweets are in JSON format).

III) Retrieve only the actual tweet message and sent it to the client socket.

IV) Define the host and port. Initialized the socket object and bind host and port together.

V) Establish the connection with Client.

VI) Use the authentication keys (access_token, access_secret_token, consumer_key and consumer_secret_key) to get the live stream data.

VII) Filter tweets which contains a specific subjects. In my example I searched tweets related to ‘corona’. We can pass multiple tracking criteria.

**Tweet_Listener code**

```python
import tweepy 
from tweepy import OAuthHandler # to authenticate Twitter API
from tweepy import Stream 
from tweepy.streaming import StreamListener
import socket 
import json 

# Twitter developer Credentials to connect to twitter account
access_token = "YOUR_ACCESS_TOKEN_FROM_TWITTER"
access_secret = "YOUR_ACCESS_SECRET_TOKEN_FROM_TWITTER"
consumer_key = "YOUR_CONSUMER_KEY_FROM_TWITTER"
consumer_secret = "YOUR_CONSUMER_SECRET_KEY_FROM_TWITTER"

class TweetsListener(StreamListener):
    # initialized the constructor
    def __init__(self, csocket):
        self.client_socket = csocket

    def on_data(self, data):
        try:
            # read the Twitter data which comes as a JSON format
            msg = json.loads(data)

            # the 'text' in the JSON file contains the actual tweet.
            print(msg['text'].encode('utf-8'))

            # the actual tweet data is sent to the client socket
            self.client_socket.send(msg['text'].encode('utf-8'))
            return True

        except BaseException as e:
            # Error handling
            print("Ahh! Look what is wrong : %s" % str(e))
            return True

    def on_error(self, status):
        print(status)
        return True

def sendData(c_socket):
    # authentication
    auth = OAuthHandler(consumer_key, consumer_secret)
    auth.set_access_token(access_token, access_secret)
    # twitter_stream will get the actual live tweet data
    twitter_stream = Stream(auth, TweetsListener(c_socket))
    # filter the tweet feeds related to "corona"
    twitter_stream.filter(track=['corona'])
    # in case you want to pass multiple criteria
    # twitter_stream.filter(track=['DataScience','python','Iot'])

# create a socket object
s = socket.socket()

# Get local machine name : host and port
host = "127.0.0.1"
port = 3333

# Bind to the port
s.bind((host, port))
print("Listening on port: %s" % str(port))

# Wait and Establish the connection with client.
s.listen(5)
c, addr = s.accept()

print("Received request from: " + str(addr))

# Keep the stream data available
sendData(c)
```

Once we run the above code our program will start listening to the port.

**StreamingTweetData** 

**Purpose:**

I) It’s the main Spark Structured streaming programming file.

II) We are reading the live streaming data from socket and type casting to String.

III) Then split words based on space, filter out only hashtag (#) values and group them up.

IV) After that write the above data into memory. Consider all data in each iterations (output mode = complete), and let the trigger runs in every 2 seconds.

V) Now, the ‘tweetquery’ will contain all the hashtag names and the number of times it appears. It’s basically a streaming dataframe and we are ready to run any dataframe operation or sql on top of this.

**StreamingTweetData  code** 

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import *
from pyspark.sql.types import *
from pyspark.sql.functions import col, split

if __name__ == "__main__":

    # create Spark session
    spark = SparkSession.builder.appName("TwitterSentimentAnalysis").getOrCreate()

    # read the tweet data from socket
    tweet_df = spark \
        .readStream \
        .format("socket") \
        .option("host", "127.0.0.1") \
        .option("port", 3333) \
        .load()

    # type cast the column value
    tweet_df_string = tweet_df.selectExpr("CAST(value AS STRING)")

    # split words based on space, filter out hashtag values and group them up
    tweets_tab = tweet_df_string.withColumn('word', explode(split(col('value'), ' '))) \
        .groupBy('word') \
        .count() \
        .sort('count', ascending=False). \
        filter(col('word').contains('#'))

    # write the above data into memory. consider the entire analysis in all iteration (output mode = complete). and let the trigger runs in every 2 secs.
    writeTweet = tweets_tab.writeStream. \
        outputMode("complete"). \
        format("memory"). \
        queryName("tweetquery"). \
        trigger(processingTime='2 seconds'). \
        start()

    print("----- streaming is running -------")
```

and then run that to see the graph:

```python
%sql 
select *  
from tweetquery
limit 100
```

![Play%20with%20Twitter%20Data%20Using%20Spark%20Structured%20Stre%2063f054eb920741b181b0914b2b1dcf1c/Untitled.png](Play%20with%20Twitter%20Data%20Using%20Spark%20Structured%20Stre%2063f054eb920741b181b0914b2b1dcf1c/Untitled.png)

## Reference:

[Easy to Play with Twitter Data Using Spark Structured Streaming](https://medium.com/@ch.nabarun/easy-to-play-with-twitter-data-using-spark-structured-streaming-76fe86f1f81c)