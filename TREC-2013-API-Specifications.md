## Lucene Analyzer 
Please note that all details here are open to change. Discussion can be found in the mailing list, and in [issue #23](https://github.com/lintool/twitter-tools/issues/23).

### Tokenization
The tokenizer creates a new token whenever it encounters whitespace or one of the following characters: 

    _ - ? ! , ; : . ( ) [ ] @ # / \

It should be noted that although the @ and # characters are used as delimiters, they are preserved in cases where they proceed a valid mention or hashtag.

### Text Normalization
All text is converted to lowercase, with the exception of URLs which are left untouched due to prevalent use of URL shorteners in tweets, many of which use case-sensitive URLs.

### Stemming
The implementation of porter stemming which is provided with Lucene 4.1 is applied to all tokens, except mentions, hashtags, and URLs.

### Stop Word Removal
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



## Indexing Details

This section describes the fields from Twitter’s JSON-represented statuses that the API indexes, stores, and exposes.

### Indexed Fields
All fields are marked as Store.YES in the index, allowing users to access data from retrieved documents.  Some fields 
are present in all statuses, while others only contain a value if the source JSON object contained a non-null entry in
that slot.  See the table below for details.

<table>
 <tr>
  <td>Field Name in API</td><td>Corresponding JSON element</td><td>Always Present</td> <td>Data Type</td> <td>Description</td>
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
  <td>retweeted_count</td><td>status_retweet_count</td> <td>yes</td> <td>long</td> <td>Number of times this status has been retweeted. Non-retweeted documents show 0</td>
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
</table>

### Example
Consider the following JSON object:

     {
    created_at: "Fri Mar 29 11:42:34 +0000 2013",
    id: 317602681340432400,
    id_str: "317602681340432384",
    text: "@hanahani3310 alhamdulillah :)",
    source: "<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>",
    truncated: false,
    in_reply_to_status_id: 317602575803363300,
    in_reply_to_status_id_str: "317602575803363329",
    in_reply_to_user_id: 1054214132,
    in_reply_to_user_id_str: "1054214132",
    in_reply_to_screen_name: "hanahani3310",
    user: {
    id: 28106647,
    id_str: "28106647",
    name: "cheryna",
    screen_name: "cheryna27",
    location: "seremban-kl-seremban",
    url: "http://cheryna.blogspot.com",
    description: "I fart. A lot. Go away if you can't stand the smell.",
    protected: false,
    followers_count: 214,
    friends_count: 174,
    listed_count: 1,
    created_at: "Wed Apr 01 13:39:16 +0000 2009",
    favourites_count: 1825,
    utc_offset: -32400,
    time_zone: "Alaska",
    geo_enabled: true,
    verified: false,
    ... snip ...
    geo: {
    type: "Point",
    coordinates: [
    3.14489609,
    101.69596372
    ]
    },
    ... snip ...
    contributors: null,
    retweet_count: 0,
    favorite_count: 0,
    favorited: false,
    retweeted: false,
    lang: "id"
    }

If we retrieve this document from the index into a org.apache.lucene.document.Document object called hit and then invoke:

        List<IndexableField> fields = hit.getFields();
        Iterator<IndexableField> fieldIt = fields.iterator();
        while(fieldIt.hasNext()) {
          IndexableField field = fieldIt.next();
          System.out.println(field.toString());
        }

we see the following output:

    stored<id:317602681340432384>
    stored<epoch:1364557354>
    stored,indexed,tokenized<screen_name:cheryna27>
    stored,indexed,tokenized<text:@hanahani3310 alhamdulillah :)>
    stored<retweet_count:0>
    stored<in_reply_to_status_id:317602575803363329>
    stored<in_reply_to_user_id:1054214132>
    stored,indexed,tokenized<lang:id>
    stored<friends_count:180>
    stored<followers_count:160>
    stored<statuses_count:27700>
