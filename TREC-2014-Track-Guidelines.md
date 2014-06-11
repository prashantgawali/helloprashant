**Track coordinators:** Jimmy Lin and Miles Efron

*Note*: As of June 10th, 2014, these guidelines are reasonably final. There will be no major deviations from these track guidelines, although this document may be tweaked for refinement and clarification purposes.

These are the guidelines for the TREC 2014 Microblog track, which is the fourth iteration of the track. In addition to temporally-anchored ad hoc retrieval (same as last year), this year's track will consist of an additional new task: tweet timeline generation (TTG).

The timeline for the evaluation is as follows:

+ May-June: Community discussion to finalize TTG logistics
+ July 18: Topics released
+ August 18: Runs due
+ Mid-September 2014: Relevance assessments released
+ November 2014: TREC conference at Gaithersburg MD, USA

The temporally-anchored ad hoc retrieval task is described in Section 3, and the tweet timeline generation (TTG) task is described in Section 4.

**Note**: You can choose to participate in the ad hoc retrieval task *only*. However, if you decide to participate in the TTG task, you *must* also submit to the ad hoc retrieval task.

### 1. How to Participate

In order to participate in the TREC 2014 Microblog track, you need to register to participate in TREC. See the [call for participation](http://trec.nist.gov/pubs/call2014.html). The call will close in late May.

The Microblog track in 2014 will use the "evaluation as a service" (EaaS) model (more below), where teams interact with the official corpus via a common API. Thus, you need to request access to the API via the following steps:

**Note**: If you participated in TREC 2013 and already have access to the API, you do not need to do anything.

1. Fill out the [API usage agreement](http://lintool.github.io/twitter-tools/API-agreement.pdf).
2. Email the usage agreement to `microblog-request@nist.gov`.
3. After NIST receives your request, you will receive an access token from NIST.
4. The code for accessing the API can be found in this repository. The endpoint of API itself (i.e., hostname, port) can be found at [this location](http://www.umiacs.umd.edu/~jimmylin/trec2014microblog/servers.txt).

Note that the file is password protected with the same username/password combination as the TREC 2014 Active Participants site: you should have received the username/password when you signed up for TREC 2014. Please do not publicize this information. 

### 2. The "Evaluation as a Service" Model

The TREC 2014 Microblog track will use the same collection of tweets as last year (informally, we refer to this as the Tweets2013 collection). It contains 243 million tweets gathered from the (sampled) public Twitter stream from February 1, 2013 to March 31, 2013 (inclusive, UTC time). Just like last year, we will be adopting the "evaluation as a service" (EaaS) model. A description of the model can be found in the [2013 track overview paper](http://www.umiacs.umd.edu/~jimmylin/publications/Lin_Efron_TREC2013_notebook.pdf). The basic idea is that participants will interact with the tweet collection via a search API. Use of the API will be limited to registered TREC participants. To use the track API, teams must obtain authentication credentials (via the instructions above).

**Note that the collection is not available for download. The search API is the only mechanism for accessing the collection and completing the track tasks** 

Documentation for the API is available [here](https://github.com/lintool/twitter-tools/wiki/TREC-2013-API-Specifications), and this document will not discuss the API details. But broadly speaking, the goal of the API is to allow all participants to obtain information necessary to support a wide variety of search strategies.  

The implementation of the API itself is open-source, and participants are encouraged to help in its ongoing development via the [twitter-tools](https://groups.google.com/forum/?fromgroups#!forum/trec-twitter-tools) mailing list and the associated [GitHub repository](https://github.com/lintool/twitter-tools). 

Community members are encouraged to suggest functionality for the API that would be of interest. The best way to submit such suggestions is by creating an "issue" (preferably a wishlist item) on the GitHub repository [https://github.com/lintool/twitter-tools/issues](https://github.com/lintool/twitter-tools/issues).  Organizers cannot promise that a given request will be integrated into the API, but it is hoped that the community can agree on crucial functionality.

### 3. Temporally-Anchored Ad Hoc Search Task

The ad hoc search task is the same as in 2013. Each topic consists of a query *Q* and a time *t*. The system's task is to return a list relevant tweets up until time *t*. Novelty and redundancy are not considered for this task. You can choose to participate in this task only and not participate in the TTG task.

Each run should consist of up to 1000 results, where each result must refer to a tweet that is published prior to and including the query time defined by the topic. Evaluation will then be conducted by standard IR effectiveness measures. The official metric we will use is mean average precision, but we will report other standard metrics such as R-precision and precision at rank 30.

As in previous years, judgment pools will be created from system output and manually assessed. Each tweet will be judged "not relevant", "relevant", or "highly-relevant".

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

NIST will create new topics for the purposes of this task. No narrative and description tags are provided. However, the topic developer/assessor will record a clearly defined information need when the topic is created. 

For each topic, systems should score each possibly relevant tweet with ids less than or equal to the query's `querytweettime` element. Note that `querytweettime` is considered to be definitive in preference to `querytime`.

*Note that the following is slightly different from last year:* Participating groups may submit up to four runs. External evidence can be used (in fact, encouraged), but future evidence cannot (see below for a more detailed discussion). Manual runs (i.e., runs with human involvement) are also welcome.

Submitted runs must follow standard TREC format:

``` 
MB01 Q0 3857291841983981 1 1.999 myRun  
MB01 Q0 3857291841983302 2 0.878 myRun  
MB01 Q0 3857291841983301 3 0.314 myRun  
...  
MB02 Q0 3857291214283390 1000 0.000001 myRun  
```

The fields are the topic number, an unused column, a tweet id, the rank of the tweet defined by the run, the score of the tweet by your system, and the identifier for the run (the "run tag"). 

System output will be evaluated using `trec_eval` to compute the various metrics: `trec_eval` uses the score as the definitive ranking key. That is, if there are tied scores the program will break those ties arbitrarily, ignoring the rank field. 

### 4. Tweet Timeline Generation Task

Tweet Timeline Generation (TTG) is a new task for this year's Microblog track with a putative user model as follows: "I have an information need expressed by a query *Q* at time *t* and I would like a summary that captures relevant information." In this year's task, the summary is operationalized by a list of non-redundant, chronologically ordered tweets that occur before time *t*.

Note that if you decide to participate in the TTG task, you must also participate in (i.e., submit runs to) the ad hoc retrieval task.

TTG supplements the standard challenges of ad hoc retrieval with issues from topic detection and tracking (TDT) and multi-document summarization. For this year (beyond ad hoc retrieval), systems will need to address two challenges:

+ Detect (and eliminate) redundant tweets. This is equivalent to saying that systems must detect novelty. Systems will be penalized for returning tweets that contain redundant information.
+ Determine how many results to return: that is, systems will be penalized for results that are too verbose.

Redundancy is operationalized as follows: for every pair of tweets, if the chronologically later tweet contains *substantive* information that is not present in the earlier tweet, the later tweet is said to be *novel*; otherwise the two tweets are *redundant*. It is assumed that redundancy is transitive, i.e., if *A* and *B* are redundant and *B* and *C* are redundant, then *A* and *C* are assumed to be redundant.

For example, consider the topic represented by the query "Haiti Aristide return". Given these results:

```
32204788955357184	Haiti opens door for return of ex-president Aristide http://tf.to/fJDt
32211683082502144	#int'l #news: Haiti opens door for return of ex-president Aristide: PORT-AU-PRINCE (Reuters) - Haiti'... http://bit.ly/gSIFwd #singapore
32469924240695297	Haiti allows ex-president Aristide's return http://t.co/pSBXmfq from @ajenglish (Can Haitian politics get any more interesting?)
```

Tweets `'144` and `'297` are redundant, given that tweet `'184` has been returned. As another example, in the following, tweet `'633`, tweets `'912`, etc. are redundant:

```
32250441588805633	Haiti to give Aristide passport: Officials in Haiti say they are ready to issue ex-president Jean-Bertrand Arist... http://bbc.in/eJlaNq
32252735009062912	The global drama keeps on coming! Past month is like drugs for IR majors RT @BBCWorld Haiti to give Aristide passport http://bbc.in/gS8USt
32273316047757312	Haiti to give Aristide passport - http://www.bbc.co.uk/news/world-latin-america-12330414
32279145685721088	Aristide eligible for Haitian passport http://bit.ly/fgZkSh
32443364628500480	#MIAMI Haiti to issue ex-president Aristide with passport, clearing way for him to return http://bit.ly/fAV5fB
32547700427718657	BBC News - Haiti to issue ex-president Aristide with passport http://www.bbc.co.uk/news/world-latin-america-12330414
```

We consider each group of tweets above a *semantic cluster*, representing an equivalence class of tweets that contain the same information (i.e., retrieving more than one cluster member is redundant). The ideal TTG run would retrieve one and only one tweet from each cluster of each topic.

Note that much like the notion of relevance, what constitutes *substantive* differences is in the eye of the assessor. For simplicity, this judgment is taken only on the level of tweets, *not* any smaller unit. Also note that the contents of linked URLs are ignored when creating the semantic clusters.

Human assessors will create semantic clusters from the list of tweets judged to be "relevant" or "highly relevant" from the ad hoc task. These clusters will then be used to score system results. A few examples of TREC topics from 2011 and 2012 that have been grouped into semantic clusters can be found [here](http://ylwang99.github.io/TweetTimelineGeneration/semantic-clusters.html) or [this prettier version](http://ylwang99.github.io/TweetTimelineGeneration/semantic-clusters-twitterstyle.html). These clusters are provided to help teams train their systems (they will be available in a machine-readable form soon); the methodology used to generate these clusters is identical to how this year's clusters will be created.

The *tentative* metric for scoring summaries will be the F1 metric that combines the following:

+ Cluster precision. Of tweets returned by the system, how many *distinct* semantic clusters are represented? To be precise, the denominator of the precision calculation is the total number of results returned by the system. Thus, the system does not get "credit" for retrieving multiple tweets from the same semantic cluster (in fact, redundant tweets lower precision).
+ Cluster recall. Of the semantic clusters discovered by the assessor, how many are represented in the system's output? As with the precision calculation, the system does not get "credit" for retrieving multiple tweets from the same semantic cluster.

The F1 metric combines precision and recall [in the usual way](http://en.wikipedia.org/wiki/F1_score).

The TTG output of a system should use the same format as the ad hoc task (i.e., standard TREC format), although note that the rank and score fields are essentially ignored.

Unresolved issues, open for discussion:

+ How do we account for the fact that some semantic clusters are more important than others? One solution might be to weight the clusters (i.e., compute weighted precision and weighted recall). We could weight the clusters by relevance grade, i.e., "relevant" tweets get a weight of one and "highly-relevant" tweets get a weight of two. Thus, clusters with many highly-relevant tweets will get a higher weights.
+ Which tweet is the best exemplar of a given cluster?  It might be desirable from the user perspective to see the earliest tweet in each cluster (i.e., the cluster representative). However, for simplicity, in this year's TTG task, any tweet from the same semantic cluster is considered equivalent and receives the same credit. That is, retrieving the last tweet will yield the same score as retrieving the first tweet. We could institute some type of temporal penalty, or as an alternative, we could simply punt on the problem for this year.
+ What do we do about unjudged tweets? We will treat as not relevant.

### 5. External and Future Evidence

The use of external or future evidence should be acknowledged for every submitted run. In particular, we define external and future evidence as follows:

**External Evidence:** Evidence obtained from sources other than the official API - for instance, this encompasses other tweets or information from Twitter, as well as other corpora, e.g., Wikipedia or the web.

**Future evidence:** Information that would not have been available to the system at the timestamp of the query.  

For example, if you make use of a Wikipedia snapshot from June 2013 (i.e., after the corpus was collected), then this is both an external and future evidence. But a Wikipedia snapshot from January 2013 is considered external but not future evidence.

Note that for operational simplicity, *general* statistics obtained from the search API (e.g., term frequency, collection probabilities) will not be considered future evidence, unless you are *specifically* computing statistics over tweets that are after the query time.