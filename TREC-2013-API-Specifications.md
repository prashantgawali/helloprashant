## Lucene Analyzer 
Please note that all details here are open to change. Discussion can be found in the mailing list, and in [issue #23](https://github.com/lintool/twitter-tools/issues/23).

### Tokenization
The tokenizer creates a new token whenever it encounters whitespace, or one of the following characters: 
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