# [@Atlantic_first_said](https://twitter.com/Atlantic_first_said)

This code/idea borrows heavily from [@NYT_first_said](https://twitter.com/NYT_first_said)

A twitter bot to track when the Atlantic publishes a word for the first time in history.
running at: [@Atlantic_first_said](https://twitter.com/Atlantic_first_said)

The code takes some steps to throw away un-interesting words like proper nouns and urls, but still picks up a lot of typos and nonsense, so the sanitization is an ongoing process.

Some points of inspiration are Allison Parrish's @everyword bot, and the [NewsDiffs](http://newsdiffs.org/about/) editorial change archiving software.

## Basic architecture

Atlantic-first-said is essentially a single script. It's running once an hour as a cron job on a small VPS.

`atlantic.py` is a beautifulsoup parser adapted from the [newsdiffs sourcecode](https://github.com/ecprice/newsdiffs). 

`redis` holds a list of scraped URLs and seen words (to reduce load on Atlantic API). It also holds a count of words tweeted recently to avoid blasting out too many tweets in a short period of time.

`api_check.py` uses the [NYT article_search API](https://developer.nytimes.com/) to check through all digitized NYT history to be confident this is really the first occurrence of a word. It returns weird 500s for some words. If you know why let me know.

`simple_scrape.py` Checks for new article urls, retrieves the article text using `nyt.py`, splits them into words, and then determines whether each word is fit to tweet using (in this order) some heuristics to discard unwanted types of words, uniqueness in our local redis instance, and finally uniqueness against the article_search api. If all of these checks pass, it tweets the word, and replies with the context and link.

