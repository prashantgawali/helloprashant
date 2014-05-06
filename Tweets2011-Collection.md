The Tweets2011 corpus, used in the TREC 2011 and 2012 microblog tracks, is distributed as directories, each of which contains approximately 100 `.dat` files, each of which contains a list of (tweet id, username, MD5 checksum). Each of these files is referred to as a status block (i.e., block of tweets).  The status block downloader works by fetching the tweets indicated in the `.dat` files from twitter.com. The combination of tweet id and username straightforwardly maps to a URL, which can be retrieved. Think `curl`. On steroids.

If you want to use this tool to download Tweets2011, or another static tweet collection distributed similarly, see the section below titled 'Fetching a status block'.

Fetching a status block
-----------------------

The HTML crawler is `cc.twittertools.download.AsyncHTMLStatusBlockCrawler`. Here's a sample invocation:

    sh target/appassembler/bin/AsyncHTMLStatusBlockCrawler \
       -data 20110123/20110123-000.dat -output json/20110123-000.json.gz

Use the `-data` option to specify the status block (`.dat` file) to read. Use the `-output` option to specify where to write the output (gzip-compressed JSON file).

To download the entire corpus, you'll need to fetch all blocks using this crawler. It makes sense to do some lightweight scripting (e.g., shell scripts, Python, Perl, etc.) to accomplish this. We have decided not to include these scripts because 1.) they are easy enough to write, and 2.) you may wish to download the blocks in parallel from multiple machines, in which no general purpose script would be particularly useful.

Despite best efforts handling timeouts and retrying, the crawler may not successfully download all statuses in one go. To address this issue, there is a "repair" command-line option that will output a new data file containing only those statuses that went missing the first time around. Here's a sample invocation:

    sh target/appassembler/bin/AsyncHTMLStatusBlockCrawler \
       -data 20110123/20110123-000.dat -output json/20110123-000.json.gz \
       -repair repair/20110123-000.dat

And the corresponding repair:

    sh target/appassembler/bin/AsyncHTMLStatusBlockCrawler \
       -data repair/20110123-000.dat -output json/20110123-000.repair.json.gz

**Notes:** 

* Please be considerate when downloading the corpus. Using a couple of machines is fine. Writing a Hadoop MapReduce job to download the corpus from 500 EC2 instances _is not_. Use common sense.

* A status will not be marked for repair if the account is protected, if the account has been suspended, or if the tweet has been deleted (404).

* There are known issues with retweets, which cause only the original tweet to appear in the output. In order to preserve the requested tweet id, the crawler therefore injects a 'requested\_id' field into each JSON status with the value of the originally requested (input) tweet id. Statuses where 'requested\_id' differs from 'id' can be considered to be retweets.
