**Track coordinators:** Miles Efron, Jimmy Lin, and Ian Soboroff

These are the guidelines for the TREC 2013 microblog track, which is the third running of the track. This year's track will consist of a single task: real-time ad hoc search, where a user's information need will be represented by a query at a specific point in time. This task is identical in conception to the ad hoc tasks of previous years. The primary difference this year from the 2011-2012 microblog tracks lies in the tweet collection and the way that participants will interact with it. In the following sections, we describe the tweet collection used by the microblog track, the method by which teams will interact with the collection, and the details of the search task.

Note that to participate in the track you need to be a registered participant in TREC 2013.  See [http://trec.nist.gov/pubs/call2013.html](http://trec.nist.gov/pubs/call2013.html) for details.  TREC becomes closed to new participants in late May or early June.

### Track-as-a-Service

For TREC 2013 participants will interact with a tweet collection stored remotely via a search API. Use of the API will be limited to registered TREC participants. To use the track API, teams must obtain authentication credentials. Instructions for obtaining credentials will be posted when the community API testing begins (in June).  

The motivation for the track-as-a-service design is to increase the size of the collection while adhering to [Twitter's terms of service](https://twitter.com/tos). Past arrangements allowed teams to acquire local copies of a canonical corpus. But the logistics of this approach prohibited scaling the corpus size up dramatically.  The corpus for 2013 is more than an order of magnitude larger than the previously used [tweets2011](http://trec.nist.gov/data/tweets/) collection. 

Documentation for the API is available [here](https://github.com/lintool/twitter-tools/wiki/TREC-2013-API-Specifications), and this document will not discuss the API's details. But broadly speaking, the goal of the API is to allow all participants to obtain statistics and other information to support a wide variety of search strategies.  Track organizers surveyed approaches used by teams in 2011 and 2012 and found that most runs relied on a relatively small number of operations including:

+	Query modification
+	Re-ranking of results obtained from a baseline retrieval.

To support such approaches the API will allow, at a minimum, these services:

+	Retrieval based on a state-of-the-art IR model (e.g., language modeling)
+	Text and API-supplied metadata from retrieved tweets
+	Access to corpus-level statistics

The implementation of the API itself is open-source, and participants are encouraged to help in its ongoing development via the [twitter-tools](https://groups.google.com/forum/?fromgroups#!forum/trec-twitter-tools) mailing list and the associated [GitHub repository](https://github.com/lintool/twitter-tools). 

Community members are encouraged to suggest functionality for the API that would be of interest. The best way to submit such suggestions is by creating an "issue" (prefereably a wishlist item) on the GitHub repository [https://github.com/lintool/twitter-tools/issues](https://github.com/lintool/twitter-tools/issues).  Organizers cannot promise that a given request will be integrated into the API, but it is hoped that the community can agree on crucial functionality.


### Tweet Collection

For TREC 2013, the collection consists of approximately 240 million tweets (statuses), collected via the Twitter streaming API over a two-month period: 1 February, 2013 - 31 March, 2013 (inclusive).  

Note that there is no way to access the official collection except through the search API.
The documentation cited above describes the representation of these tweets as exposed by the API.

### Real-Time Ad Hoc Search Task

In the real-time search task, the user issues a query at a time *T*.  Systems must rank tweets posted up to this time that are relevant to the user's information need.  Due to the nature of microblogs, it is likely that relevance will have a temporal dimension, but this is by no means guaranteed nor is it operationalized.  Thus, the system should answer a query by providing a list of relevant tweets ranked in decreasing order of predicted relevance. Participating groups should return their top 1000 tweets published prior to and including the query time defined by the topic. Evaluation will then be conducted by standard IR effectiveness measures.  At a minimum we will assess mean average precision and precision@30.

When scoring tweets, systems should favor relevant and highly informative tweets about the query topic. For this year, the "novelty" between tweets will again not be considered.  

Topics will be developed to represent an information need at a specific point in time. An example topic could be:  

```
  <top>  
  <num> Number: MB01 </num>  
  <query> Wael Ghonim </query>  
  <querytime> 25th February 2011 04:00:00 +0000 </querytime>  
  <querytweettime> 3857291841983981 </querytweettime>  
  </top> 
```
where:

+ the num tag contains the topic number.
+ the query tag contains the user's query representation.
+ the querytime contains the timestamp of the query in a human and machine readable ISO standard form.
+ the querytweettime tag contains the timestamp of the query in terms of the chronologically nearest tweet id within the corpus.
 
NIST will create new topics for the purposes of this task. No narrative and description tags are provided.  But the topic developer/assessor will record a clearly defined information need  when the topic is created. 

For each topic, systems should score each possibly relevant tweet with ID's less than or equal to the query's querytweettime element. Note that while tweet ids are not strictly chronologically ordered, we consider querytweettime to be definitive in preference to querytime.


### Submission Guidelines

Participating groups may submit up to four runs. At least one run should not use any external or future source of evidence (see below for a description of external and future sources of evidence). While because of the nature of real-time search, the use of future evidence is discouraged, the use of timely external resources is encouraged. 

Differently from last year, submitted runs must follow standard TREC format:  
``` 
MB01 Q0 3857291841983981 1 1.999 myRun  
MB01 Q0 3857291841983302 2 0.878 myRun  
MB01 Q0 3857291841983301 3 0.314 myRun  
...  
MB02 Q0 3857291214283390 1000 0.000001 myRun  
```

The fields are the topic number, an unused column, a tweet id, the rank of the tweet defined by the run, the score of the tweet by your system, and the identifier for the run (the "run tag"). 

For each query, tweets not scored/returned will be assumed to have a minimal score (e.g. negative infinity). 


### External and Future Evidence

The use of external or future evidence should be acknowledged for every submitted run. In particular, we define external and future evidence as follows:

**External Evidence:** Evidence obtained from sources other than the official API - for instance, this encompasses other tweets or information from Twitter, as well as other corpora, e.g., Wikipedia or the web.

**Future evidence:** Information that would not have been available to the system at the timestamp of the query.  

For example, if you make use of a Wikipedia snapshot from June 2013 (i.e., after the corpus was collected), then this is both an external and future evidence. But a Wikipedia snapshot from January 2013 is considered external but not future evidence.

Note that for operational simplicity, *general* statistics obtained from the search API (e.g., term frequency, collection probabilities) will not be considered future evidence, unless you are *specifically* computing statistics over tweets that are after the query time.

### Assessment & Evaluation

NIST assessors will judge the relevance of tweets to the specified information need on a graded scale of "informativeness". As all topics are expressed in English, non-English tweets will be judged non-relevant, even if the topic's assessor understands the language of the tweet and the tweet would be relevant in that language. 

Regarding retweets: partial retweets will be judged relevant if the non-RT part of the text conveys some relevant added value. 

As mentioned above, the effectiveness measures that the organizers will report are to be determined.


## Timeline

+ April-May 2013: Alpha testing of API 
+ June 2013: Community testing of API
+ July 16: Topics released
+ August 15, 2013: Runs due
+ Mid-September 2013: Relevance assessments released
+ November 2013: TREC conference at Gaithersburg MD, USA