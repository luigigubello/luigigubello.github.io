---
title: About Iran and IRA Twitter datasets (for fun) – Part I
author: Luigi Gubello
type: post
date: 2018-12-07T18:29:05+00:00
url: /blog/about-iran-and-ira-twitter-datasets-for-fun-part-one/
categories:
  - English
  - Twitter

---
On 17 October 2018 Twitter [released two datasets](https://about.twitter.com/en_us/values/elections-integrity.html#data) about the propaganda accounts of the Internet Research Agency (IRA) and Iran. Each dataset has three parts: a CSV file with the user list, a CSV file with _all*_ the tweets of said users and a dataset of the shared images and memes. For fun I tried to use **pandas** and **matplotlib** to read the data. To read the file **ira_tweets_csv_hashed.csv (5,4 GB)** I split it into 91 parts, with 100.000 rows each, by using the awesome [`split`](https://kb.iu.edu/d/afar) command. I used a lot of code posted on the site [Python-Graph-Gallery](https://python-graph-gallery.com/) (❤️) to draw the plots.

### Iran Dataset
  
This is the smallest dataset, Twitter has published **770** users potentially connected to Iranian propaganda (many information _[userid, user_display_name, user_screen_name]_ is hashed because of a choice of Twitter).
  
![](/images/2018/12/users_iran.png)

Plots about Iranian users CSVBy watching the plots, it is possible to see that the Iranian propaganda seems to have been very intense in the years 2017 and 2018, because over **50%** of the users have been created in these two years. Most accounts have a description (and the word _journalist_ is common in their descriptions). Over half of the users have less than 100 followers and follow less than 100 accounts.

These 770 users tweeted **1.122.929** times from 2012 to 2018 (total tweets = classic tweets + retweets + quotes + replies).

![](/images/2018/12/tweets_iran.png)

The number of retweets is small, until 2017 the retweets do not seem to be influential. The retweets seem to increase with the increase in propaganda accounts. These tweets generated **1.517.593 favorites**, **828.536 retweets** and **164.128 replies**. It is interesting to note that the number of favorites and the number of retweets is similar until 2016, but from 2017 the number of favorites is about twice the number of retweets. Another interesting fact is that the volume of interactions (favorites + retweets + replies) in 2017 and 2018 seems to be directly proportional to the number of new users, but not proportional to the volume of tweets: in 2015 there were 248.780 tweets and 159.497 interactions, in 2017 there were 290.421 tweets **(+16,7%)** and 1.133.106 interactions **(+610%)**. Almost all of the tweets (88%) are produced by **only five** different user-agents: _Twitter Web Client_, _TweetDeck_, _dlvr.it_, _Twitter for Android_ and _Facebook_. Curiously the most used language in the tweets is French (28%), followed by English (24%).


  * [**Iranian Dataset - summary**](/docs/2018/12/iran_summary.txt)

### Internet Research Agency Dataset
    
Twitter says IRA users are 3.841, almost five times Iranian users. In the file **ira_users_csv_hashed.csv** I count only 3.836 users (3.837 rows).

![](/images/2018/12/users_russia.png)

A difference with Iranian users is the creation date, IRA users were created mainly in 2013 and 2014, two years before the US elections.</span></span> <span class="tlid-translation translation"><span class="" title="">A curious fact is that only two new accounts were created in 2018.</span></span> Also the IRA accounts have a description (73%), a number similar to the Iranian one.

These 3.836 users tweeted **9.007.377 (nine million)** times from 2012 to 2018, in 2015 there were **3.132.628 (three million)** tweets (45% are retweets), almost three times the number of Iranian tweets from 2012 to 2018.

![](/images/2018/12/tweets_russia.png)


There is another important difference between the two datasets: the Iranian propaganda machine is "classic", the accounts increase in 2017 and 2018, the effects (_interactions_) grow in 2017 and 2018. The IRA accounts created **from 2013 to 2014** are (68%), the year **2015** is the one with the highest activity, more than three million tweets, but the years with more interactions are **2016 and 2017**, in those years there were **30.059.381 (thirty million) hearts**, **25.615.627 (twenty-five million) retweets** and **1.962.259 (almost two million) replies** to their tweets. A hypothesis about this is that the IRA propaganda machine has improved over the years: they can reach more people with fewer tweets. This is only an **unconfirmed** hypothesis, there may also be other causes. The most common language in the tweets is Russian, followed by English. The most used European languages are: German, Bulgarian, Italian and Spanish. Another difference with Iranian tweets is the plot of user-agents: the IRA accounts used many different applications to post their tweets. The most common user-agent is _Twitter Web Client_ (28%), followed by _twitterfeed_ (16%) and _TweetDeck_ (6%).

  * [**IRA Dataset - summary**](/docs/2018/12/ira_summary.txt)

### My Two Cents

In my opinion the two Twitter datasets are not so good, for two main reasons:
    
1. Why are these users potential propaganda accounts? How did Twitter choose them? There are various reasons why Twitter may not give this information publicly, but this is a limitation for the analysis of the researchers.
2. Most screen names are hashed and Twitter have not shared information about the followers, so it is not possible to create a network of these propaganda accounts.

Looking at the plots, from 2016 the favorites to the propaganda tweets increase a lot and they exceed the number of retweets. This fact happens both in the IRA propaganda and in the Iranian propaganda. There may be many reasons why this happens, but I find it curious how Twitter changed at the end of 2015: [Twitter changed the "star" symbol to the "heart" symbol](https://blog.twitter.com/official/en_us/a/2015/hearts-on-twitter.html), [saying that this had quickly increased the number of the users' interactions](https://techcrunch.com/2015/11/10/twitter-sees-6-increase-in-like-activity-after-first-week-of-hearts/).
    
About this topic, I suggest an article by Wired, [_Twitter' Dated Data Dump Doesn’t Tell Us About Future Meddling_](https://www.wired.com/story/twitters-dated-data-dump-doesnt-tell-us-about-future-meddling/), and an analysis by [Ilja](https://twitter.com/fubits),[_Election Hacking: Exploring 10 Million Tweets from the Russian Internet Research Agency Dataset, Pt. 1 - Apporaching 5.3GB in R_](https://dadascience.design/post/election-hacking-exploring-2-million-tweets-from-the-russian-internet-research-agency-dataset-pt-1/).

I hope to have time to write the **part II**: I would like to analyze the frequency of words in the descriptions and tweets, the most frequent hashtags and some other statistics.

hese days in France there have been many manifestations of people in the yellow vest. Of course on Twitter it was born a hashtag to talk about this: [# GiletsJaunes](https://twitter.com/hashtag/GiletsJaunes?src=hash). To better understand how propaganda can affect Twitter I suggest three tweets:

{{< tweet user="fs0c131y" id="1070978229224267776" >}}

{{< tweet user="x0rz" id="1070716650322829312" >}}