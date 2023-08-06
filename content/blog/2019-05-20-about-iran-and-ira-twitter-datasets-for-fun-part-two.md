---
title: About Iran and IRA Twitter datasets (for fun) – Part II
author: Luigi Gubello
type: post
date: 2019-05-20T00:32:37+00:00
url: /blog/about-iran-and-ira-twitter-datasets-for-fun-part-two/
categories:
  - English
  - Twitter

---
In this post I will move forward on the analysis of Twitter datasets, that I have started in December. You can read the previous post, [_"About Iran and IRA Twitter datasets (for fun) – Part I"_](https://gubello.me/blog/about-iran-and-ira-twitter-datasets-for-fun-part-one/). This time I have focused on the potential Russian propaganda in Europe, so I have decided to analyse the tweets written in **German**, **Italian**, **Spanish**, **French**, **Dutch** and **Danish**. I have left out English tweets because it would have been difficult to separate the propaganda in the United Kingdom from the propaganda in the United States. In my own Python code, in order to read data I have mainly used these packages: **pandas**, **langid**, **emoji**, **matplotlib** and **wordcloud**. The module [**langid**][1] has been useful to classify the languages used in the users' descriptions, it is **not** always correct, but it allowed me to make a comparison between the descriptions' languages and the tweets' languages. You can find a list of Python module to recognize the language of a text in [this topic][2] on _StackOverflow_. The module [**wordcloud**][3] allows to do awesome plots with words (thanks to [Python-Graph-Gallery](https://python-graph-gallery.com/) for the shared code).

### Data About Accounts

I have focused on the users' descriptions because they can give some interesting information, so I have tried to answer to some questions:

  * In which languages are the descriptions written?
  * What are the most common words**¹** and the most common emoji?
  * Is there a correlation between the languages of descriptions and the languages of tweets?

### Iran Accounts

The dataset of Iranian users contains 770 accounts. Most users (**83,9%**) have '_en_' (**English**) as account's language, but the most used language in the tweets is **French** (**35,9%**), followed by English (20,1%) and Arabic (12,8%).

**Important:** I have **not** count the retweets, and I have excluded 31.594 tweets, classified by Twitter as '_nan_' or '_und_'.

![](/images/2019/01/Iran_descriptions.png#center)

_On the left, the most used languages in the descriptions, analysed with langid.py. On the right, the most used languages in the tweets._

By comparing the languages of the tweets and the languages of the descriptions, obtained with **langid**, I have found results more congruent among them. The set of the five most used languages in the tweets and the set of the most used languages in the descriptions had four elements in common: **French** (_fr_), **English** (_en_), **Arabic** (_ar_) and **Persian** (_fa_). The results obtained with langid could be inaccurate, but they are consistent with the data about the tweets' languages. It is also interesting to see the most frequent words in the descriptions.<figure id="attachment_700" aria-describedby="caption-attachment-700" style="width: 1008px" class="wp-caption aligncenter">

![](/images/2019/01/Iran_description_wordcloud.png#center)

_Words¹ with at least four characters and used at least by ten different users._

I have chosen to consider only the words with at least four characters (so as to exclude pronouns, articles and conjunctions) and used at least by ten different users (this is a **subjective** **parameter** to define a word "**_common_**"**¹**). I have found only ten words under these conditions and eight of them are in English (I have excluded the word '_https_'). The most frequent word is '**journalist**', found in 18 descriptions, followed by '**news**', used in 16 descriptions. In my own opinion it is an important fact about the Iranian propaganda: the users tried to seem credible to spread news. In addition to the words' analysis, I have also analysed the emojis by using the python module [**emoji**][6]. In the descriptions of the Iranian accounts there are not many emojis, only 30 different ones, and the most used are 😜 and 🇯🇴, each of which appears in four descriptions.

### Internet Research Agency Accounts

In the dataset of **Internet Research Agency** (Russian) users there are 3.836 accounts, almost five times the number of Iranian accounts (well, more fun!). The main accounts' languages are '_en_' (**English**, 62,1%) and '_ru_' (**Russian**, 27,1%), following by '_es_' (Spanish, 6,1%) and '_de_' (German, 2,9%). It is interesting to compare these data with the ones of the tweets' languages and the descriptions' languages.

![IRA descriptions](/images/2019/01/IRA_descriptions.png#center)

**To analyse these data I have used the same rules as before**. The percentages are more homogeneous for IRA users than for Iranian users: the two most frequent languages (used in the account's settings, in the description and in the tweets) are **English** and **Russian**. There are 2.384 accounts (62,1%) with English language and 1.039 (27,1%) with Russian language, the descriptions written in English are **1.444** (**51,3%**) and ones written in Russian are **854** (**30,3%**), plus there are **2.178.513** English tweets (**41,1%**) and **2.839.839** Russian tweets (**53,5%**). There is only another language present in the five most used of every list, which is **German**. There are 111 accounts (2,9%) with German language and there are 86.259 tweets written in German (1,6%), after English and Russian tweets they are the **larger** group. In a set of 3.836 users, where 73% have a description, it is interesting to watch the most frequent words in their descriptions.

![IRA description wordcloud](/images/2019/01/IRA_description_wordcloud.png#center)

This cloud of words is more dense than Iranian one. The most common word is '**follow**', it is in **203** descriptions, and circa **180** times it is followed by the pronoun '**me**'. If I exclude the words '_that_' and '_your_', the next most common words are: '**love**' (128), '**conservative**' (121), '**life**' (119), '**trump**' (103), '**maga**' (96) and '**news**' (78). The first _not-English_ word is '**люблю**' (64), that means '_love_' in Russian. The most frequent words are in English and describe users with right-wing ideas who support Trump, but not all users are pro-Trump (e.g. the word '**blacklivesmatter**' is in 27 descriptions). These results are consistent with the emoji in the descriptions: the two most frequent ones are ❤, present in 23 descriptions, and 🇺🇸, present in 16 descriptions. By remembering [the **FiveThirtyEight's analysis**][9] about another dataset of (potential) IRA tweets, _my own opinion_ is that the goal of IRA users was to polarize the readers.

### Russian Propaganda in Europe

My own question is: **has there been Russian propaganda in Italy and in Europe?**

In Italy some journalists wrote about Russian influences in Italian politics via Twitter ([_Corriere della Sera_ [it]][10] and [_Repubblica_ [it]][11]), mainly by favoring the right, but with little evidence in the data. In the Twitter dataset of IRA tweets, there is the column **tweet_language** that identifies the language of each tweet, so I have compared the tweets written in six languages: **German**, **Italian**, **Spanish**, **French**, **Dutch** and **Danish**. My expectation was to find a common pattern, if there has been propaganda by using these languages.

![](/images/2019/01/Europe_comparison.png#center)

_On the left, the volume of the tweets and retweets for each language. On the right, the volume of the interactions (favorites + retweets + replies) for each language._

The first step is to compare the volume of tweets written in these six languages ​​and the interactions generated by these tweets. On the left the amount of tweets and retweets written in German, Italian, Spanish, French, Dutch and Danish. There are many tweets and retweets in German and Italian, few in Spanish and French. This information is interesting because Spanish is the second most spoken language in the United States, spoken by fourty million people, and the second in the world as well, so I expected many tweets written in Spanish - I was wrong. Another interesting number is the ratio between tweets and retweets (**#tweets / #retweets**), for all these languages the ratio is greater than 1 except for Italian. On the right there are the volumes of the interactions (favorites, retweets and replies), divided by language. The volume of the interactions to the propaganda tweets written in German is much larger than the others: to make a comparison, the second largest volume (interactions with French tweets) is **almost 13 times smaller**. The **Italian case** is interesting again, there are **20.376** tweets (tweets + retweets) in Italian, **+61%** than French tweets, but the total interactions with the propaganda tweets in Italian are only **6.197**, less than half of the total interactions to the tweets written in French. Looking at these two plots, _my own conclusion_ is that in Germany there could have been Internet Research Agency propaganda and that Italy seems to be a "_special_"**²** case.

### German

There are **99.332 tweets**, of which **13.073** are **retweets**, in German. These tweets have generated **121.493 hearts**, **74.693 retweets** and **15.460 replies**.

![](/images/2019/01/German_pie.png#center)

_On the left, the pie plot of tweets and retweets in German. On the right, the pie plot of the interactions with these tweets._

The tweets (not including the retweets) in German are **86.259**, they are the most frequent after the tweets in English and Russian. Among the six languages, it is the one with the highest **ratio between tweets and retweets** (**6,6**). Another interesting number is the **percentage of replies**: **7,3%** (**15.460** replies), it is the highest percentage among the six languages.

![](/images/2019/01/German_donut.png#center)

_On the left, the percentage of IRA accounts that are the authors of the tweets retweeted by other IRA accounts. On the right, the percentage of tweets written by IRA accounts among the retweeted tweets._

I was surprised to see that Russian users have retweeted a small group of Russian users (only **70** propaganda accounts out of **3.036** retweeted users), but the tweets written by this small group are **10%** of all retweets in German. I have created a word cloud with the most retweeted users (retweeted at least five times) to get a better view. For convenience I have replaced the Russian hashed users with the value "**hashed_user_n**", where **n** is an index to count these users.

![](/images/2019/01/German_user_wordcloud.png#center)

_The word cloud of the most retweeted users (retweeted at least five times)._

The **twenty-one** (**21**) most retweeted accounts are the authors of **25%** of all retweeted tweets and they are **0,7%** of all retweeted accounts. Among these there are many **mainstream media** and **journalists** (e.g. **@welf**, verified account of the newspaper "_Die Welt_", **@SPIEGELONLINE**, verified account of the newspaper "_Der Spiegel_", and **@tagesschau**, verified account of the television news provider "_Tagesschau_"), and **three** **propaganda accounts** (**@erdollum** and others two which are hashed because they have less than 5.000 followers). The user **@erdollum** was created in 2016 and had **7.691** followers, the others two were created in 2016 and 2015 and they had **4.654** and **938** followers each. If I increase the previous percentage **up to 50%**, I find that **135** users (**4,4%**) are the source of half of all retweeted tweets, of which **18** ones are IRA accounts. To get a full picture of the topics, I have also create a word cloud with the most common hashtags (by "_common_" I mean "_used in **at least ten** tweets or retweets_").

![](/images/2019/01/German_hashtag_wordcloud.png#center)

_Word cloud of the hashtags used in at least ten tweets or retweets._

Reading the hashtags can be useful to understand what kind of news has been spread by the propaganda accounts. The word cloud confirms that Germany is one of the targets of Russian propaganda. There are **8.619** _different_ hashtags used **61.795** times by the IRA users and the seventeen most frequent hashtags are **25%** (**15.516**) of all used ones. The main hashtags are about few topics: **internal politics**, **foreign politics**, **refugees**, **terrorism**, **Trump** and **Brexit**. So **#Merkel** is the most used hashtag, **2.455** times, but Merkel also appears in the hashtags **#MerkelMussBleiben** (**508**), which means "_Merkel has to stay_", **#JugendMitMerkel** (**265**), which means "_Youth with Merkel_", and **#MerkelMussWeg** (**144**), which means "_Merkel has to go_". It seems that it is not important to support an idea or not, the goal of Russian users is to polarize other users on the topic, but this is _my own opinion_ by reading these numbers (you might like this report by Von Karolin Schwarz and Patrick Gensing). Some other hashtags about the internal politics are **#AfD** (**876** times), acronym for the far-right party Alternative für Deutschland, **#CDU** (**793**), acronym for the liberal-conservative party Christlich Demokratische Union Deutschlands, and **#SPD** (**559**), acronym for the social-democratic party Sozialdemokratische Partei Deutschlands. The hashtag **#Flüchtlinge** (_refugees_) is repeated **1.308** times and the hashtag **#Syrien** **666** times. About foreign politics the hashtags **#Erdogan** and **#Türkei** are used **1.281** and **959** times each. The hashtag **#Trump** was written **472** times and there is an hashtag written in German to support Trump, **#WirLiebenTrump** ("_We love Trump_"), used **560** times. The hashtags about Brexit are interesting: **#BritainInOut** (**459**), **#Brexit** (**444**), **#GoodbyeUK** (**415**) and **#BrexitOrNot** (**315**). These hashtags are not for or against Brexit, but they are useful to create polarization. Anyway, the researchers _Johan Farkas_ and _Marco Bastos,_ in their paper "[_IRA Propaganda on Twitter: Stoking Antagonism and Tweeting Local News_][17]", classify the German tweets about Brexit in two sets: **Anti-EU Pro-Brexit** and **Pro-Eu Pro-Brexit**.

To conclude this statistic I have extracted the emoji from **86.259** tweets. Russian users have used **313** different emoji, the most five frequent are 😂 (in **702** tweets), 😊 (in **472** tweets), 😘 (in **230** tweets), 🔴 (in **215** tweets) and 😉 (in **159** tweets). It is interesting to notice the little number of occurences of 🇺🇸, compared to the volume of tweets (e.g. in **Italian** and **Spanish** tweets there are more United States flag emoji, despite the lower volume of tweets, but there is a easy reason: _the Twitter dataset isn't always correct_).

* * *

### Important

The graphs for the next five languages ​​are based on the same rules and conditions as the graphs built for the German language.

* * *

### Italian

Earlier I have written the Italian case is **special**, because the number of retweets are much more than the one of tweets. It is the only one with this feature among the six languages, and it is also **unique\*** among all other languages. There are **53** different languages ​​across the set of all retweets, and only **nine** of them ​​have more retweets than tweets, but among these the **only language\*** with over 10.000 retweets is Italian (except Japanese (_jp_) with 1.543 retweets, the other seven languages have very small numbers).

![](/images/2019/02/Italian_pie.png#center)

There are **2.494** **tweets** and **17.882** **retweets** in Italian, but they have generated few interactions, only **2.889** **hearts**, **3.152** **retweets** and **156** **replies**. It is useful to compare these numbers with others, to better understand: there are 20.376 tweets (tweets + retweets) written in Italian and they have generated a smaller volume of interactions (hearts + retweets + replies) compared to French tweets (12.636) and interactions (16.351). The situation is similar if the comparison is made with Spanish numbers. _The high number of retweets doesn't seem to have influenced the number of generated interactions, by observing these six languages ​​the number of interactions seems proportional only to the number of tweets_ (this is an **unconfirmed hypothesis**). It is interesting to know how many propaganda accounts have been retweeted.

![](/images/2019/02/Italian_donut.png#center)

**2.113** different users have been retweeted and only **41** (**1,9%**) are Russian accounts. Only **156** tweets out of **17.882** were created by these propaganda accounts and then retweeted by other propaganda users. These tweets are **0,9%** of all retweets, this percentage is very far from the German (10,2%) or Spanish (11,6%) ones and it is the lowest among the six languages. The Russian trolls prefered to retweet italian tweets by mainstream media and "trusted" sources.

![](/images/2019/02/Italian_user_wordcloud.png#center)

The most retweeted users are mainly **mainstream media** (e.g. _Repubblica_ (**@repubblica** + **@repubblicait**) has been retweeted **2.640** times, **14,8%** of all retweets). Half of the retweeted tweets were written by **only twenty-six** (**26**) users (**1,2%** of all retweeted users), and they are **all** Italian mainstream media accounts. There are only three propaganda accounts retweeted _at least_ 10 times: **@DanaGeezus** (13 times), **@ChrixMorgan** (12) and an hashed user (10). The descriptions of these accounts are in English and very short, and they geolocalize themselves in the United States, so _in my opinion_ they don't seem influential. Among the **top 100** most retweeted users, **only three** aren't accounts of media or journalists. They are **@carmen_piscopo**, retweeted 44 times, **@Elena07617349**, retweeted 43 times, and **@ausoloda**, retweeted 24 times. The first and the third still exist, the second has been suspended by Twitter, but _no one is an IRA account_ (according to Twitter). The users **@carmen_piscopo** and **@ausoloda** are **similar** [_on 24 February 2019_]: both have a high volume of tweets (**253.000** and **227.000**, they write **141** and **109** tweets per day circa), both retweet a lot, both have many favorites (**184.000** and **83.200**), both reply to other users. They _seem_ semi-automatic or automatic accounts (in particular _@carmen_piscopo_ is very _peculiar_ _in my opinion_) and their behaviour isn't so different from that of some Russian propaganda accounts. The user **@Elena07617349** has been suspended by Twitter, so I can't have precise information about it, by searching on Google I have found this username in some Italian sites ([[1]][21] - [[2]][22]), and in a paper about the "_networks of anti-Muslim hate on Twitter_" ([[3]][23], pg. 78). This account has become viral on the Italian mainstream media sites (_with no reason in my opinion_), after its suspension, because of the hashtag **#MattarellaDimettiti** ([[4]][10] - [[5]][24] - [[6]][25]). The wordcloud of the hashtags is useful to know the main news tweeted by the accounts of the IRA.

![](/images/2019/02/Italian_hashtag_wordcloud.png#center)

In the set of Italian tweets there are **5.580** _different_ hashtags, used **15.071** times (obviously the numbers are lower than those of the tweets in German). The most used hashtag is the generic **#news** (used in **198** tweets), among the twenty most frequent hashtags there are **many hashtags related to football** (**#SerieA**, used 73 times, **#Sports**, 70 times, **#Inter**, 69 times, **#Milan**, 67 times), **many about United States**, the hashtag **#Trump** occurs **150** times and it is the third most frequent hashtag, **#MAGA** 92 times and **#USA** 86 times. The most used hashtags about internal politics are: **#M5S** (88 times), **#PD** (78) and **#Renzi** (77). The hashtags **#Roma** (**163** times) and **#Napoli** (77) can refer to both internal politics and football. Some hashtags related to foreign policy are the same as the German ones, e.g. **#Siria** (**78**), **#UE** (**72**), **#Londra** (**65**), **#Brexit** (**62**) and **#Migranti** (**61**). _In general there are many hashtags about sports, many concerning current events, but there doesn't seem to be a scheme to "inflate" a particular topic_. The strange hashtags as **#AlternativeAcronymInterpretations** and **#ThingsMoreTrustedThanHillary** are an error, some tweets with these hashtags are classified like Italian but they aren't.

In the Italian tweets (tweets + retweets) I have found only **60** _different_ emoji, the five most used emoji are 🇺🇸 (in **59** tweets), 🌴 (in **51** tweets), ✨ (in 19 twees), ™ (in 10 tweets) and ❤ (in 10 tweets). These emoji are also common in other languages (Spanish, French and Dutch), especially 🇺🇸 and 🌴, but (like for the strange hashtags) _this depends on an error of Twitter in the classification of tweets' languages_.

### Spanish

The tweets in Spanish have surprised me, because they are not as many as I thought. Spanish is the second language of the United States, spoken by **37 million** people (circa 10-11%, _source: [WorldAtlas][27]_), and is the fourth language in the world (_source: [Wikipedia according to Ethnologue][28]_). I expected to find a large volume of Spanish tweets to polarize the Hispanic minority in the United States, since the propaganda campaign was mainly against the US. However, _the data tell another story_. Unlike the tweets in Italian and German, I didn't expect to find tweets or retweets related to Spain (in Europe), but rather to the countries of Latin America and the United States.

![](/images/2019/02/Spanish_pie.png#center)

There are **8.402 tweets** and **4.400 retweets** in Spanish (_es_) and they have generated **13.472** interactions: **6.991 hearts**, **5.928 retweets** and **553 replies**. If I exclude '_nan_' and '_und_', Spanish is the **seventh** most frequent language in the set of tweets and the **eighth** most frequent in the set of retweets, but the number of tweets is small (there is a tweet [_only tweets_] in Spanish every 10 tweets in German and every 338 tweets in English). Even the interactions generated are not many, e.g. the interactions generated by French tweets are slightly greater. Unlike retweets in Italian, the “presence” of IRA users in the set of Spanish retweets is "large": a retweeted account every twenty is a fake account and a retweet every ten is a tweet written by an IRA account.

![](/images/2019/02/Spanish_donut.png#center)

There are **1.872** users who have been retweeted, **108** of which are Russian users. The retweets are only **4.400**, corresponding to **24,7%** of retweets in Italian and **33,8%** of retweets in German. The retweets written by IRA users are **509** (**11,6%**), to make a comparison: in the set of Italian retweets there are only **156** written by the IRA users.

![](/images/2019/03/Spanish_user_wordcloud.png#center)

The **forty-eight** (**48**) most retweeted accounts are the authors of **25%** of the retweeted tweets, and they are **2,5%** of all retweeted users. This data is interesting: in previous cases (German and Italian) very few accounts are the authors of many retweeted tweets, _the situation for retweets written in Spanish is different, the retweets are more "distributed" among the various accounts_. Another interesting data is the number of IRA users present among these forty-eight users: there are **eight** IRA accounts (**16,6%**) and this percentage is three times the one calculated on all the retweeted accounts. _So it seems that the IRA accounts wanted to give greater prominence to their network and their contents, perhaps in an attempt to gain influence_ (this is an **unconfirmed hypothesis**). By manually checking the most retweeted accounts I have found that _the Russian users are actually more than eight, because some users changed their username_. The user **@ElPaso_Post**, the most retweeted one, changed the username to **@ElPasoTopNews**, which made it look like a local news site (it wasn't the only one, according to [this NPR article][32]). Also the user **@MariaMozhaiskay** is an IRA user, but she changed her username to **@MaryMozhaiskaya**. In the **fifteen** (**15**) most retweeted users there are **six** (**6**) **IRA users**, **two** (**2**) users related to Russia (**@ActualidadRT** and **@bookLoverRus**), a spam bot (**@AffiliateBuildr**), a pro-Trump account (**@UTHornsRawk**, probably a bot), **three** (**3**) **suspended accounts** (**@WorldLatinStar**, **@oldpicsarchive** and **@HipHopSpace**), so remain **only two** (**2**) "clean" users. I have to make a special mention for **@WorldLatinStar**, because it is mentioned in several Italian news sites (e.g. [TgCom24][33]), it has been suspended by Twitter, but there is still its private Instagram account with a million followers. In the list of retweeted accounts there is also the account **@Lettera43**, an Italian news site, retweeted nine (9) times; _this fact confirms the difficulty of Twitter to classify the language of the tweets_.

![](/images/2019/03/Spanish_hashtag_wordcloud.png#center)

I have counted **1.918** different hashtags in the Spanish tweets, used **8.772** times. There are many generic hashtags, like **#Noticias** (the most frequent hashtag, used **1.969** times, in English it means "_news_"), **#News** (**539** times), **#Politics** (**217**), **#Sports** (206), **#Local** (**154**), **#World** (121), **#Rap** (46) or **#NowPlaying** (44). _The hashtags about the American elections are interesting, they seem to be directed to a specific target "close to" these elections (in my own opinion)_. There are hashtags about **Obama** (**#ObamaNextJob**, used 58 times, **#TeamObama**, used 22 times, **#ObamasWishlist**, used 10 times), **Hillary Clinton** (**#ThingsMoreTrustedThanHillary**, used 32 times, and **#MakeAMovieHillary**, used 22 times) and, obviously, **Trump** (**#MAGA**, used **268** times, **#PresentsTrumpGot**, used 47 times, **#TCOT**, used 30 times, **#TrumpForPresident**, used 15 times, and **#Trump2016**, used 10 times). There are _at_ _least_ seven hashtags about the black people and the problem of racism (e.g. **#BlackLivesMatter** is repeated 33 times), _I suppose these trends were useful to polarise people_ (especially conservatives against a minority).

There are 121 different emojis, the most frequent one, used in 40 different tweets, is 🇺🇸, following by 🌴 (used in 21 tweets), ❤ (16), 😂 (11) and ✌ (10). _Three of the five most used emojis are in common with those found in tweets in Italian, probably due to some mistakes of Twitter in recognizing the Italian and Spanish language_.

### French

The volume of tweets written in French, the volume of interactions, and the ratio between tweets and retweets are similar to those of tweets written in Spanish, but the two sets of tweets are very different in terms of quality. Like Spanish, French is also widespread in the world, it is spoken in **France**, in many African countries and in some regions of **Canada**. However, it is less widespread than Spanish, especially in the United States.

![](/images/2019/03/French_pie.png#center)

There are **7.501** **tweets** and **5.135 retweets** in French, and they have generated **16.351 interactions** (**8.486 hearts**, **7.201 retweets** and **664 replies**), little more than the Spanish ones. By excluding the labels '_nan_' and '_und_', French is the seventh most used language in the retweets, behind Italian and German. _In this analysis I have not dealt with the temporal evolution of the various tweets, I hope to have time in the future to deepen it, in order to understand if the Russian propaganda increased during the French elections_.

![](/images/2019/03/French_donut.png#center)

The retweeted users are **2.189**, **61** (**2,8%**) of which are accounts of IRA, and there are **275** (**5,4%**) retweeted tweets created by Russian propaganda accounts. _Unlike previous cases, there are two important French politicians that are very retweeted_ (the two candidates in the last election). In _my opinion_ this is the most important fact about the retweets written in French and I have several hypotheses about this:

  * Russian propaganda accounts have tried to strongly influence the French elections, by retweeting politicians as well
  * Russian propaganda accounts have used different propaganda techniques for each target
  * France has a two-round electoral system, with only two candidates. This can create a polarization and therefore the propaganda accounts have only retweeted the two leaders in the run-off
  * Twitter was wrong to classify some accounts **⁴**, and there is another network
  * Other possibilities I have not thought about yet

![](/images/2019/03/French_user_wordcloud.png#center)

The situation regarding the propaganda in French is **chaotic**: there are tweets and users that mainly address France and the French elections and other users and trends that affect the American elections, probably because of Canada, where people speak also French. **25%** of the retweeted tweets were written by **forty-seven** (**47**) users, and **four of them** (**8,5%**) are in the IRA accounts list. By observing these users in detail, there are only four IRA accounts, but there are **twelve** (**12**) _suspended_ accounts: **@Voltuan**, **@ZombiaxX**, **@AllenWestRepub** (become @GreatAmRepublic), **@WashDCOnline** (become @WashingtOnline), **@C2017DebouCracy**, **@oldpicsarchive**, **@NOrleansDaily** (become @NewOrleansON), **@CarlosVFrVN**, **@DetroitPost** (become @DetroidDailyNews), **@amrightnow**, **@HaussmannParis** and **@AntiLeftistsFas**. In _my opinion,_ either did the Russian trolls retweet many accounts that didn't respect the terms and conditions of Twitter (_trolls retweet trolls_), or Twitter has suspended (is still suspending) some popular accounts among IRA accounts. There is another interesting fact about these forty-seven accounts, some retweeted users seem to be related to France, others to the United States, as if French was used to make two different types of propaganda on France and the USA. In my opinion the users retweeted to spread propaganda in France are: **@Voltuan** (the most retweeted user, **162** times), **@MLP_officiel** (retweeted **58** times), **@ZombiaxX** (**38**), **@Le_Figaro** (**29**), **@afpfr** (**25**), **@paoegilles** (**24**), **@gabriellecluzel** (**24**), **@EmmanuelMacron** (**24**), **@Elysee** (**22**), **@CarlosVFrVN** (**21**), **@AllAfricafrench** (**20**), **@HaussmannParis** (**16**), **@Marion_M_Le_Pen** (**14**), **@lemondefr** (**13**), **@franceinfo** (**13**) and **@ThomasWieder** (**13**). The accounts that I think were used to spread propaganda during the American elections are: **@AllenWestRepub** (**99**), **@UTHornsRawk** (**27**), **@WashDCOnline** (**26**), **@NOrleansDaily** (**24**), **@ESPEZY** (**22**), **@AnnoGalactic** (**21**), **@DetroitPost** (**17**), **@amrightnow** (**17**), **@CBCNews** (**16**) and **@darren_huxter** (**15**). There seems to be a difference between propaganda in Europe and propaganda in the United States: _in France the retweeted media are trusted and known, in the USA the retweeted accounts are local media, often false, and then suspended_.

![](/images/2019/03/French_hashtag_wordcloud.png#center)

The wordcloud of all (**2.287**) hashtags confirms the point of view written above. Many hashtags (e.g. #News, #Politics, #Sports, #Local, #NowPlaying, #MAGA, etc) are the _same_ as those present in many tweets written in Spanish, this fact could be an acknowledgement that some tweets in French were intended to influence the American elections. There are also some hashtags concerning France and French politics: **#PrayForBrussels** (used 115 times), **#Brussels** (58), **#Paris** (51), **#Macron** (**50**), **#Londres** (32), **#ParisAttacks** (24), **#Marine2017** (**19**), **#14mai2017** (**18**), **#MurdeLaPaix** (18), **#MarineLePen** (**17**), **#Fillon** (14) and **#JeVote** (13).

In the tweets written in French I have found 102 different emoji, the five most frequent are: ❤, used in 34 tweets, 😈, used in 22 tweets, 🇺🇸, used in 21 tweets, ™, used in 13 tweets, and ✨, used in 12 tweets. Also in the French tweets ❤ and 🇺🇸 are among the most used emoji.

### Holland and Denmark

The data concerning the tweets written in Dutch and Danish isn't reliable to make an analysis. Initially I decided to read it because I thought Twitter had correctly classified the language of the tweets, but there are many errors. _The errors are compensated on large numbers, but not on small numbers_. However the numbers of tweets written in Dutch and Danish are small: there are **2.292 tweets** and **1.249 retweets** classified as '_nl_' (Dutch) and they have generated **2.009** **interactions** (**1.029** **hearts**, **890 retweets** and only **90 replies**) and there are **2.700 tweets** and **873 retweets** classified as '_da_' (Danish) and they have generated **3.173 interactions** (**1.500 hears**, **1.543 retweets** and **130 replies**). The hashtags are mainly in English, confirming the fact that Twitter has difficulty classifying the language of the tweets.

### My Two Cents

In this post I have worked on some data extracted from the dataset published by Twitter, I have focused on tweets and retweets written in some languages present in Europe (German, Italian, Spanish and French), on the retweeted accounts and on the hashtags used by the IRA users to try to understand if there was propaganda in Germany, Italy, Spain and France. I have started this post with a question: _has there been Russian propaganda in Europe? And in Italy?_ I'll try to draw my own conclusions:

  * Twitter has not correctly classified the languages of some tweets (usually short tweets), this hasn't helped me to analyze the data because I can't completely trust the dataset. A good example are the tweets with the emoji 🌴, classified as Spanish or Italian. The tweets with the palm emoji are mainly quotes, with the addition of silly hashtags (e.g. #Top), of tweets very similar to [this tweet by @TerreBehlog][39] (now @Nov2020election). The tweets are mainly in English, but are mistakenly classified as Italian and Spanish.
  * [Twitter was wrong to classify some accounts**⁴**][40]. In January 2019 Twitter has removed 228 users from its dataset of IRA accounts, shared in October 2018, because these accounts seem to belong to a different network, coming from Venezuela. Twitter has never said how it classifies propaganda accounts, but it is clear that it is an uncertain procedure even for Twitter. **Unfortunately I analyzed the dataset before January 2019, so these statistics are based on the incorrect dataset. I hope the final results aren't so different.**
  * In **Germany** it seems that the Russian trolls have tried to alter and polarize the topics on Twitter, I don't know if the Russian propaganda has worked and I can't quantify its effects. Germany is one of the countries where the trolls have been most active (together with Russia, Ukraine and the United States). In my opinion the best analysis about Russian propaganda in Germany was written by **Luca Hammer**, you can read it **[on Twitter][41]** or [**on Threadapp.**][42]
  * In **Italy** the IRA accounts weren't very active (they wrote a few tweets), but they shared a lot of information in Italian via retweets. Shared retweets come from mainstream media, concerning various topics, the political hashtags mainly concern the PD (_Democratic Party_) and the M5S (_Five Star Movement_). I can't conclude that there has been Russian propaganda in Italy, but the large number of retweets is suspicious. In Italy, in August 2018, many newspapers discussed Russian propaganda in Italy and the FiveThirtyEight's dataset, the best analysis (in my opinion) was written by **Marianna Bruschi** [[link to Twitter]][43].
  * The tweets written in Spanish don't have hashtags or retweeted accounts that can be connected to Spain. The propaganda target is the Latin minority in the United States and this propaganda is very different from that seen in Germany: many retweeted accounts have been suspended, many of them were trolls, many hashtags refer to the American elections, and retweeted news is written from local news sites (fake) and not from trusted accounts.
  * The tweets classified as **French** are very important: they are the example of different propaganda for different targets. In the French tweets there two main sets: one similar to the tweets written in Spanish and one similar to the tweets written in German. In the first one there are propaganda tweets about American elections, so there are many fake accounts of local news, in the second one there are mainly retweets from trusted accounts (e.g. @Le_Figaro, @MLP_officiel, @EmmanuelMacron and @Elysee).

I hope to conclude these statistics by analyzing the retweets and tweets in Italian.

 [1]: https://github.com/saffsd/langid.py
 [2]: https://stackoverflow.com/questions/39142778/python-how-to-determine-the-language
 [3]: https://github.com/amueller/word_cloud
 [6]: https://pypi.org/project/emoji/
 [9]: https://fivethirtyeight.com/features/why-were-sharing-3-million-russian-troll-tweets/
 [10]: https://www.corriere.it/politica/18_agosto_01/tweet-populisti-russia-voto-italiano-come-usa-f33df26c-95cc-11e8-819d-89f988769835.shtml
 [11]: https://rep.repubblica.it/pwa/generale/2018/08/01/news/dalla_propaganda_di_putin_1500_tweet_per_lega_e_5s-203178628/
 [17]: http://openaccess.city.ac.uk/19401/1/FarkasBastos-SMS.pdf
 [21]: https://www.davidpuente.it/blog/2017/02/24/disinformazione-migrante-si-masturba-negozio-eccitato-da-manichino-arricchimento/amp/
 [22]: https://www.lastampa.it/2018/02/17/esteri/iovotono-consip-e-bastaimmigrati-le-operazioni-social-in-italia-dei-troll-di-san-pietroburgo-r5xkcnV6d3vhllZrswINvK/amphtml/pagina.amp.html
 [23]: https://rbwm.moderngov.co.uk/documents/g6386/Public%20reports%20pack%2008th-Nov-2016%2018.00%20Standing%20Advisory%20Council%20on%20Religious%20Education.pdf?T=10
 [24]: https://www.wired.it/attualita/politica/2018/08/02/tweet-russi-fake-news-lega-m5s/
 [25]: https://www.valigiablu.it/troll-russia-mattarella/
 [27]: https://www.worldatlas.com/articles/the-most-spoken-languages-in-america.html
 [28]: https://en.wikipedia.org/wiki/List_of_languages_by_total_number_of_speakers
 [32]: https://www.npr.org/2018/07/12/628085238/russian-influence-campaign-sought-to-exploit-americans-trust-in-local-news?t=1551487963254&t=1551547287632
 [33]: https://www.tgcom24.mediaset.it/mondo/speciale-elezioni-usa-2016/usa-2016-spunta-una-caricatura-di-hillary-clinton-nuda-proteste-a-new-york_3037112-201602a.shtml
 [39]: https://twitter.com/Nov2020election/status/891585241877499904
 [40]: https://www.bloomberg.com/news/articles/2019-02-20/twitter-revises-data-on-russian-trolls-and-their-2017-activity
 [41]: https://twitter.com/luca/status/1052562207140122624
 [42]: https://threadreaderapp.com/thread/1052562207140122624.html
 [43]: https://twitter.com/MariannaBruschi/status/1025060376830984193