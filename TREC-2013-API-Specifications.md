The TREC 2013 microblog track is experimenting with the "track as a service" model. Instead of distributing the collection, the evaluation will be conducted by having everyone use a common API to access the collection. This page describes the specification of the API.

## Search API

### For the Impatient...

Here's a sample invocation of the command-line interface to the search API:

```
  etc/run.sh cc.twittertools.search.retrieval.TrecSearchThriftClientCli \
    -host [HOSTNAME] -port 9090 -qid MB01 -q 'BBC World Service staff cuts' \
    -max_id 34952194402811905 -num_results 1000 -runtag lucene \
    -group [GROUP] -token [TOKEN]
```

After you've cloned the `twitter-tools` repo and successfully built the project with `ant`, the above command should work. Note that you need three pieces of information to connect to the service:

+ `[HOSTNAME]`: the hostname serving the API
+ `[GROUP]`: your group id
+ `[TOKEN]`: your authentication token

**NOTE:** We're still figuring out the details of how exactly to distribute this information... for now, bug Jimmy Lin.

In this example, we are search for topic `MB01` from TREC 2011 (assuming the service provides the Tweets2011 corpus). The other command line parameters are:

+ `port`: use 9090
+ `qid`: the topic id
+ `q`: the query
+ `max_id`: return docids up to this value
+ `num_results`: number of hits to return
+ `runtag`: runtag to use (from `trec_eval` output format)

### Search Result Specification

The search API returns a list of results. Each result has the following Thrift specification:

```
struct TResult {
  1: i64 id,
  2: double rsv,
  3: string screen_name,
  4: i64 epoch,
  5: string text,
  6: i32 followers_count,
  7: i32 statuses_count,
  8: string lang,
  9: i64 in_reply_to_status_id,
 10: i64 in_reply_to_user_id,
 11: i64 retweeted_status_id,
 12: i64 retweeted_user_id,
 13: i32 retweeted_count
}
```

Here is a description of the fields:

<table>
 <tr>
  <td><b>Field Name in API</b></td>
  <td><b>Corresponding JSON element</b></td>
  <td><b>Always Present</b></td>
  <td><b>Type</b></td>
  <td><b>Description</b></td>
 </tr>
 <tr>
  <td>id</td><td>status.id</td><td>yes</td> <td>long</td> <td>The unique identifier assigned to this document by Twitter</td>
 </tr>
 <tr>
  <td>screen_name</td> <td>status.user.id</td><td>yes</td> <td>String</td> <td>The Twitter screen name of the Status author</td>
 </tr>
 <tr>
  <td>epoch</td> <td>NA</td><td>yes</td> <td>long</td> <td>The unix epoch (in seconds) corresponding to the created_at JSON element</td>
 </tr>
 <tr>
  <td>text</td> <td>status.text</td><td>yes</td> <td>String</td> <td>The text of the status</td>
 </tr>
 <tr>
  <td>followers_count</td><td>status.user.followers_count</td> <td>yes</td> <td>int</td> <td>The number of followers that the author of this status has</td>
 </tr>
 <tr>
  <td>statuses_count</td><td>status.user.friends_count</td> <td>yes</td> <td>int</td> <td>The number of statuses that the author of this status had at the time this status was created</td>
 </tr>
 <tr>
  <td>lang</td><td>status.lang</td> <td>no</td> <td>String</td> <td>The two-character language of the status (not the user) as described by the Twitter language id system</td>
 </tr>
 <tr>
  <td>in_reply_to_status_id</td><td>status.in_reply_to_status_id</td> <td>no</td> <td>long</td> <td>The unique identifier of the status that this document replies to</td>
 </tr>
 <tr>
  <td>in_reply_to_user_id</td><td>status.in_reply_to_user_id</td> <td>no</td> <td>long</td> <td>The unique identifier of the user who posted the status that this document replies to</td>
 </tr>
<tr>
 <td>retweeted_status_id</td><td>status.retweeted_status_id</td> <td>no</td> <td>long</td> <td>The unique identifier of the tweet that this is a retweet of.</td>
</tr>
<tr>
 <td>retweeted_user_id</td><td>status.retweeted_user_id</td> <td>no</td> <td>long</td> <td>The user ID of person who posted the tweet that this is a retweet of.</td>
</tr>
  <tr>
  <td>retweeted_count</td><td>status_retweet_count</td> <td>no</td> <td>int</td> <td>Number of times this status has been retweeted. Non-retweeted documents show 0</td>
 </tr>

</table>

Note that for the fields in which "Always present" is "no", the result value will be either 0 or `null` if the field is not present in the original JSON status object.

## Lucene Analyzer

This section describes the Lucene Analyzer used to process the collection for indexing.

#### Tokenization
The tokenizer creates a new token whenever it encounters whitespace or one of the following characters: 

     ] [ ! " # $ % & ( ) * + , . / : ; < = > ? @ \ ^ _ ` { | } ~ - … ¬ · 

There are a number of exceptions where characters listed above will not cause a new token to be created:
* A period (.) will not cause a new token if it is used as part of an acronym.
* An ampersand (&) will not cause a new token if the character on both sides is uppercase (such as M&S, AT&T or H&M).
* The characters @, # and _ will not cause a new token if used as part of a mention or hashtag.
* Valid URLs are not tokenized

#### Text Normalization
All text is converted to lowercase, with the exception of URLs which are left untouched due to prevalent use of URL shorteners in tweets, many of which use case-sensitive URLs.

#### Stemming
The implementation of porter stemming which is provided with Lucene 4.1 is applied to all tokens, except mentions, hashtags, and URLs.

#### Stop Word Removal
No stop word removal is performed.

### Examples
Please note that each token is surrounded by vertical bars: |

    AT&T getting secret immunity from wiretapping laws for government surveillance http://vrge.co/ZP3Fx5
    |att|get|secret|immun|from|wiretap|law|for|govern|surveil|http://vrge.co/ZP3Fx5|

    want to see the @verge aston martin GT4 racer tear up long beach? http://theracersgroup.kinja.com/watch-an-aston-martin-vantage-gt4-tear-around-long-beac-479726219 …
    |want|to|see|the|@verge|aston|martin|gt4|racer|tear|up|long|beach|http://theracersgroup.kinja.com/watch-an-aston-martin-vantage-gt4-tear-around-long-beac-479726219|

    Incredibly good news! #Drupal users rally http://bit.ly/Z8ZoFe  to ensure blind accessibility contributor gets to @DrupalCon #Opensource
    |incred|good|new|#drupal|user|ralli|http://bit.ly/Z8ZoFe|to|ensur|blind|access|contributor|get|to|@drupalcon|#opensource|

    We're entering the quiet hours at #amznhack. #Rindfleischetikettierungsüberwachungsaufgabenübertragungsgesetz
    |were|enter|the|quiet|hour|at|#amznhack|#rindfleischetikettierungsüberwachungsaufgabenübertragungsgesetz|

    The 2013 Social Event Detection Task (SED) at #mediaeval2013, http://bit.ly/16nITsf  supported by @linkedtv @project_mmixer @socialsensor_ip
    |the|2013|social|event|detect|task|sed|at|#mediaeval2013|http://bit.ly/16nITsf|support|by|@linkedtv|@project_mmixer|@socialsensor_ip|

    U.S.A. U.K. U.K USA UK #US #UK #U.S.A #U.K ...A.B.C...D..E..F..A.LONG WORD
    |usa|uk|uk|usa|uk|#us|#uk|#u|sa|#u|k|abc|d|e|f|a|long|word|

    this is @a_valid_mention and this_is_multiple_words
    |thi|is|@a_valid_mention|and|thi|is|multipl|word|

    PLEASE BE LOWER CASE WHEN YOU COME OUT THE OTHER SIDE - ALSO A @VALID_VALID-INVALID
    |pleas|be|lower|case|when|you|come|out|the|other|side|also|a|@valid_valid|invalid|

    ＠reply @with #crazy ~＃at
    |＠reply|@with|#crazy|＃at|

    :@valid testing(valid)#hashtags. RT:@meniton (the last @mention is #valid and so is this:@valid), however this is@invalid
    |@valid|test|valid|#hashtags|rt|@meniton|the|last|@mention|is|#valid|and|so|is|thi|@valid|howev|thi|is|invalid|

    this][is[lots[(of)words+with-lots=of-strange!characters?$in-fact=it&has&Every&Single:one;of<them>in_here_B&N_test_test?test\test^testing`testing{testing}testing…testing¬testing·testing what?
    |thi|is|lot|of|word|with|lot|of|strang|charact|in|fact|it|ha|everi|singl|on|of|them|in|here|bn|test|test|test|test|test|test|test|test|test|test|test|what|

