**The current version of the search API is distributed in version 1.4.1 of `twitter-tools-core`.**

Using the TREC 2013 API, it is easy to perform baseline runs on the Tweets2011 collection with topics from TREC 2011 and TREC 2012. Simply connect to the host that serving the Tweets2011 collection. Here's the sample invocation:

```
sh target/appassembler/bin/RunQueriesThrift \
  -host [HOSTNAME] -port 9090 -group [GROUP] -token [TOKEN] \
  -queries data/topics.microblog2011.txt > out.lm.2011.txt
```

The `trec_eval` package is also included in `twitter-tools` under `etc/` (but you'll have to build the package). The `qrels` are in `twitter-tools-core/data/`. To evaluate:

```
$ ../etc/trec_eval data/qrels.microblog2011.txt out.lm.2011.txt 
num_q                 	all	49
map                   	all	0.3041
P_30                  	all	0.3476
```

And for the TREC 2012 topics:

```
sh target/appassembler/bin/RunQueriesThrift \
  -host [HOSTNAME] -port 9090 -group [GROUP] -token [TOKEN] \
  -queries data/topics.microblog2012.txt > out.lm.2012.txt
```

Evaluation:

```
$ ../etc/trec_eval data/qrels.microblog2012.txt out.lm.2012.txt
num_q                 	all	59
map                   	all	0.1815
P_30                  	all	0.2932
```