**Track coordinators:** Jimmy Lin and Miles Efron

# DRAFT GUIDELINES -- THESE ARE SUBJECT TO CHANGE

These are the guidelines for the TREC 2014 microblog track, which is the fourth running of the track. This year's track will consist of a single task: tweet timeline generation (TTG).

Note that to participate in the track you need to be a registered participant in TREC 2014.  See [http://trec.nist.gov/pubs/call2013.html](http://trec.nist.gov/pubs/call2014.html) for details.  TREC becomes closed to new participants in late May or early June.

## Track-as-a-Service

For TREC 2014 participants will interact with a tweet collection stored remotely via a search API. Use of the API will be limited to registered TREC participants. To use the track API, teams must obtain authentication credentials.  

The motivation for the track-as-a-service design is to increase the size of the collection while adhering to [Twitter's terms of service](https://twitter.com/tos). Past arrangements allowed teams to acquire local copies of a canonical corpus. But the logistics of this approach prohibited scaling the corpus size up dramatically.  The corpus for 2013 is more than an order of magnitude larger than the previously used [tweets2011](http://trec.nist.gov/data/tweets/) collection. 

Documentation for the API is available [here](https://github.com/lintool/twitter-tools/wiki/TREC-2013-API-Specifications), and this document will not discuss the API's details. But broadly speaking, the goal of the API is to allow all participants to obtain statistics and other information to support a wide variety of search strategies.  

The implementation of the API itself is open-source, and participants are encouraged to help in its ongoing development via the [twitter-tools](https://groups.google.com/forum/?fromgroups#!forum/trec-twitter-tools) mailing list and the associated [GitHub repository](https://github.com/lintool/twitter-tools). 

Community members are encouraged to suggest functionality for the API that would be of interest. The best way to submit such suggestions is by creating an "issue" (prefereably a wishlist item) on the GitHub repository [https://github.com/lintool/twitter-tools/issues](https://github.com/lintool/twitter-tools/issues).  Organizers cannot promise that a given request will be integrated into the API, but it is hoped that the community can agree on crucial functionality.


## Tweet Collection

For TREC 2014, the track will use the same corpus collected for TREC 2013, with the possibility of supplementing this with additional (later) documents.  The collection consists of approximately 240 million tweets (statuses), collected via the Twitter streaming API over a two-month period: 1 February, 2013 - 31 March, 2013 (inclusive).  

Note that there is no way to access the official collection except through the search API.
The documentation cited above describes the representation of these tweets as exposed by the API.

## Tweet Timeline Generation Task

Given an information need about an event *E*, TTG involves finding on-topic tweets and organizing them into a coherent, non-redundant, chronologically ordered list.  TTG supplements the standard challenges of ad hoc retrieval with issues from topic detection and tracking (TDT) and automatic Q&A.  That is, effective systems will address novelty and redundancy in their results, while also identifying which tweets hold key information about an unfolding topic. 

The goal of TTG is to provide the user with a concise list of tweets (i.e., a timeline summary) that addresses his or her information need. If the topic concerned an event (for example, the 2013 Butler County, PA train crash), these tweets might highlight breaking developments: the initial report of the accident, updates on fatalities, progress on determination of cause, etc.


For 2014, NIST will develop new topics for the microblog track.  As in the past, each topic will represent an information need issued at a specific point in time. An example topic could be:  

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
 
No narrative and description tags are provided.  But the topic developer/assessor will record a clearly defined information need  when the topic is created. 

For each topic, systems should create a set *R* of tweets with querytweettime elements less than or equal to the topics' querytweettime.  It is up to participants to decide how to populate *R*.  But in general, *R* should consist of tweets that

+ are topically relevant to the query
+ convey key (as opposed to tangential) information about the event referenced by the query
+ are non-redundant with respect to other tweets in the set.

Collectively, the tweets in the result set should summarize the target event *E* as it has unfolded up to the querytweettime, while including as little redundancy as possible.



### Submission Guidelines

Participating groups may submit up to four runs. Submitted runs must follow standard TREC format:  
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

NIST assessors' work will have two phases.  First, for a topic *t*, an assessor will evaluate pooled tweets with respect to relevance, using criteria from previous years.  As all topics are expressed in English, non-English tweets will be judged non-relevant, even if the topic's assessor understands the language of the tweet and the tweet would be relevant in that language. Additionally, retweets are considered non-relevant.

In the second phase, the assessor will go through the relevant tweets chronologically from oldest to newest and create clusters of tweets that contain substantially the same information (where substantially is defined by whatever the assessor deems relevant).   

Given these clusters of tweets, the participants’ runs are then scored. There are many options for an evaluation metric, and here we propose a starting point: If there are r tweet clusters for a particular topic, at rank r in the participants’ results list, how many clusters are covered? A run gets credit for retrieving only one tweet for each cluster.  We can plot a tradeoff curve by then sweeping the rank cutoff position, e.g., 2r, 3r, etc. Of course, this is only one of many possible evaluation metrics, and we will solicit both community feedback and explore alternatives.

Examples of clustered results will be made available to participants as training data during June 2014.


## Timeline

+ May-June: Community discussion to finalize TTG logistics
+ (subject to change) July 14: Topics released
+ (subject to change) August 18: Runs due
+ Mid-September 2014: Relevance assessments released
+ November 2014: TREC conference at Gaithersburg MD, USA