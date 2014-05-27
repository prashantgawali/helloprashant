**Track coordinators:** Jimmy Lin and Miles Efron

### DRAFT GUIDELINES -- THIS IS A WORK IN PROGRESS!

These are the guidelines for the TREC 2014 Microblog track, which is the fourth running of the track. In addition to temporally-anchored ad hoc retrieval (same as last year), this year's track will consist of an additional new task: tweet timeline generation (TTG).

The timeline for the evaluation is as follows:

+ May-June: Community discussion to finalize TTG logistics
+ July 18: Topics released
+ August 18: Runs due
+ Mid-September 2014: Relevance assessments released
+ November 2014: TREC conference at Gaithersburg MD, USA

### 1. How to Participate

In order to participate in the TREC 2014 Microblog track, you need to register to participate in TREC. See the [call for participation](http://trec.nist.gov/pubs/call2014.html). The call will close in late May.

The Microblog track in 2014 will use the "evaluation as a service" (EaaS) model (more below), where teams interact with the official corpus via a common API. Thus, you need to request access to the API via the following steps:

**NOTE**: If you participated in TREC 2013 and already have access to the API, you do not need to do anything.

1. Fill out the [API usage agreement](http://lintool.github.io/twitter-tools/API-agreement.pdf).
2. Email the usage agreement to `microblog-request@nist.gov`.
3. After NIST receives your request, you will receive an access token from NIST.
4. The code for accessing the API can be found in this repository. The endpoint of API itself (i.e., hostname, port) can be found at [this location](http://www.umiacs.umd.edu/~jimmylin/trec2014microblog/servers.txt).

Note that the file is password protected with the same username/password combination as the TREC 2014 Active Participants site: you should have received the username/password when you signed up for TREC 2014. Please do not publicize this information. 

### 2. The "Evaluation as a Service" Model

The TREC 2014 Microblog track will use the same collection of tweets as last year (informally, we refer to this as the Tweets2013 collection). It contains 243 million tweets gathered from the (sampled) public Twitter stream from February 1, 2013 to March 31, 2013 (inclusive, UTC time). Just like last year, we will be adopting the "evaluation as a service" model. A description of the model can be found in the [2013 track overview paper](http://www.umiacs.umd.edu/~jimmylin/publications/Lin_Efron_TREC2013_notebook.pdf). The basic idea is that participants will interact with the tweet collection via a search API. Use of the API will be limited to registered TREC participants. To use the track API, teams must obtain authentication credentials (via the instructions above).

**Note that the collection is not available for download. The search API is the only mechanism for accessing the collection and completing the track tasks** 

Documentation for the API is available [here](https://github.com/lintool/twitter-tools/wiki/TREC-2013-API-Specifications), and this document will not discuss the API details. But broadly speaking, the goal of the API is to allow all participants to obtain information necessary to support a wide variety of search strategies.  

The implementation of the API itself is open-source, and participants are encouraged to help in its ongoing development via the [twitter-tools](https://groups.google.com/forum/?fromgroups#!forum/trec-twitter-tools) mailing list and the associated [GitHub repository](https://github.com/lintool/twitter-tools). 

Community members are encouraged to suggest functionality for the API that would be of interest. The best way to submit such suggestions is by creating an "issue" (preferably a wishlist item) on the GitHub repository [https://github.com/lintool/twitter-tools/issues](https://github.com/lintool/twitter-tools/issues).  Organizers cannot promise that a given request will be integrated into the API, but it is hoped that the community can agree on crucial functionality.

### 3. Temporally-Anchored Ad Hoc Search Task

The ad hoc search task is the same as in 2013. Each topic consists of a query *Q* and a time *t*. The system's task is to return a list relevant tweets up until time *t*. Novelty and redundancy are not considered for this task.

Each run should consist of up to 1000 results, where each result must refer to a tweet that is published prior to and including the query time defined by the topic. Evaluation will then be conducted by standard IR effectiveness measures. The official metric we will use is mean average precision, but we will report other standard metrics such as R-precision and precision at rank 30.

The topics will be in the same format as last year:

```
<top>
<num> Number: MB111 </num>
<query> water shortages </query>
<querytime> Fri Mar 29 18:56:02 +0000 2013 </querytime>
<querytweettime> 317711766815653888 </querytweettime>
</top>
```
where:
+ the `num` tag contains the topic number.
+ the `query` tag contains the user's query representation.
+ the `querytime` contains the timestamp of the query in a human and machine readable ISO standard form.
+ the `querytweettime` tag contains the timestamp of the query in terms of the chronologically nearest tweet id within the corpus.

NIST will create new topics for the purposes of this task. No narrative and description tags are provided.  But the topic developer/assessor will record a clearly defined information need  when the topic is created. 

For each topic, systems should score each possibly relevant tweet with ID's less than or equal to the query's querytweettime element. Note that while tweet ids are not strictly chronologically ordered, we consider querytweettime to be definitive in preference to querytime.

Participating groups may submit up to four runs. At least one run should not use any external or future source of evidence (see below for a description of external and future sources of evidence). The use of future evidence is discouraged but the use of timely external resources is encouraged. 

Submitted runs must follow standard TREC format:

``` 
MB01 Q0 3857291841983981 1 1.999 myRun  
MB01 Q0 3857291841983302 2 0.878 myRun  
MB01 Q0 3857291841983301 3 0.314 myRun  
...  
MB02 Q0 3857291214283390 1000 0.000001 myRun  
```

The fields are the topic number, an unused column, a tweet id, the rank of the tweet defined by the run, the score of the tweet by your system, and the identifier for the run (the "run tag"). 

### 4. Tweet Timeline Generation Task

The Tweet Timeline Generation (TTG) task is a new task with a putative user model as follows: "I have an information need expressed by a query *Q* at time *t* and I would like a summary that captures relevant information that addresses my need." In this year's task, the summary is operationalized by a list of non-redundant, chronologically ordered tweets that occur before time *t*.

TTG supplements the standard challenges of ad hoc retrieval with issues from topic detection and tracking (TDT) and multi-document summarization. In this years task (beyond ad hoc retrieval), systems will need to address two challenges:

+ Detect (and eliminate) redundant tweets. Systems will be penalized for returning redundant tweets. This is equivalent to saying that systems must detect novelty.
+ Determine how many results to return:\ that is, systems will be penalized for returning too many results.

Redundancy is operationalized as follows: for each pair of tweets, if the chronologically later tweet contains *substantive* information that is not present in the earlier tweet, the latter tweet is said to be *novel*; otherwise the two tweets are assumed to be *redundant*.  It is assumed that redundancy is transitive, i.e., if *A* and *B* are redundant and *B* and *C* are redundant, then *A* and *C* are assumed to be redundant.

For example, consider the topic "Haiti Aristide return", the following tweets might be considered redundant:

```
32204788955357184	Haiti opens door for return of ex-president Aristide http://tf.to/fJDt
32211683082502144	#int'l #news: Haiti opens door for return of ex-president Aristide: PORT-AU-PRINCE (Reuters) - Haiti'... http://bit.ly/gSIFwd #singapore
32469924240695297	Haiti allows ex-president Aristide's return http://t.co/pSBXmfq from @ajenglish (Can Haitian politics get any more interesting?)
```



*substantively* 
**What follows below is just rough fodder --- DO NOT PAY ATTENTION (yet)**

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

### 5. External and Future Evidence

The use of external or future evidence should be acknowledged for every submitted run. In particular, we define external and future evidence as follows:

**External Evidence:** Evidence obtained from sources other than the official API - for instance, this encompasses other tweets or information from Twitter, as well as other corpora, e.g., Wikipedia or the web.

**Future evidence:** Information that would not have been available to the system at the timestamp of the query.  

For example, if you make use of a Wikipedia snapshot from June 2013 (i.e., after the corpus was collected), then this is both an external and future evidence. But a Wikipedia snapshot from January 2013 is considered external but not future evidence.

Note that for operational simplicity, *general* statistics obtained from the search API (e.g., term frequency, collection probabilities) will not be considered future evidence, unless you are *specifically* computing statistics over tweets that are after the query time.

### 6. Assessment & Evaluation

NIST assessors' work will have two phases.  First, for a topic *t*, an assessor will evaluate pooled tweets with respect to relevance, using criteria from previous years.  As all topics are expressed in English, non-English tweets will be judged non-relevant, even if the topic's assessor understands the language of the tweet and the tweet would be relevant in that language. Additionally, retweets are considered non-relevant.

In the second phase, the assessor will go through the relevant tweets chronologically from oldest to newest and create clusters of tweets that contain substantially the same information (where substantially is defined by whatever the assessor deems relevant).   

Given these clusters of tweets, the participants’ runs are then scored. There are many options for an evaluation metric, and here we propose a starting point: If there are r tweet clusters for a particular topic, at rank r in the participants’ results list, how many clusters are covered? A run gets credit for retrieving only one tweet for each cluster.  We can plot a tradeoff curve by then sweeping the rank cutoff position, e.g., 2r, 3r, etc. Of course, this is only one of many possible evaluation metrics, and we will solicit both community feedback and explore alternatives.

Examples of clustered results will be made available to participants as training data during June 2014.
