# NLP, mp3s for Dope Emcee R-E-Cs: Building a Rap Recommendation Engine From Scratch


## Project Summary

As an avid music enthusiast, I have always been interested in understanding the mechanisms that drive music recommendations across the most popular streaming platforms. Spotify is the most popular of said platforms and leverages a three-pronged approach.

 - **User Data**: This is by and far the most important data when it comes to delivering a recommendation. Our taste and genre profiles, recent browsing and listening history, and social relationships are the most influential data source
 - **Audio Data**: With Spotify's acquisition of Echo Nest, the company absorbed a MIR team (Music Information Retrieval) dedicated to building proprietary audio features like "Danciness" and "Speechiness." These funny sounding engineered features are the result of extensive domain knowledge and exploration by their DS team, and are also utilized in the process of delivering recommendations
 - **"Cultural" Data**: The last source of information is what is described as the "cultural vector"-- this is a representation of what the Blogosphere/Social media is saying about a given track or artist.
 
For my capstone, my goal was to, on a much smaller scale, generate these datasets and take it a step further by doing some heavy lifting with respect to **Lyrical Data**, leveraging a variety of NLP outputs that could also be used for recommendations.

For a deeper understanding of all elements of this capstone, please reference the below table of contents:

**Table of Contents**
 - [Recommendation Engines Overview](#Recommendation-Engines-Overview)
 - [Summary of Findings](#Summary-of-Findings)
 - [Learning Agenda](#The-Learning-Agenda)
 - [The Approach](#The-Approach)
 - [Data Dictionary](#Model-Selection)

## Recommendation Engines Overview

Please see the below table for a description of several approaches to recommendation engines. This is the tip of the iceberg in a field that has a number of different implementation strategies. Our focus will be on *collaborative filtering* and *content based filtering* to produce a **hybrid** approach.


![table1](https://i.imgur.com/XYwqxlM.jpg)

A summary of general watchouts and pitfalls that can be encountered is also outlined below.

![Imgur](https://i.imgur.com/Cwz1tk1.jpg)


## Summary of Findings
 - **Track features (lyrics and audio) are insufficient for generating consistent recommendations.** Recommender systems should not rely solely on lyric/review/audio data exclusively.
  - **Collaborative Filtering / User Data should be prioritized before anything.** The lack of granularity seen with our collaborative filter system was solely the result of data availability. This approach produced the most consistent results without the need for over-engineering features. 
 - **Track features, cultural data can produce novel - and sometimes serendipitous -- results at the expense of relevance.** Despite struggles with maintaining relevance, in subjective testing of recommender output, it was determined that album review data and track features produced more novel and serendipitous recs compared to our collaborative filter
 - **High dimensionality can impact your results**. When constructing a recommendation engine, it's important to remember that introduction of related features will increase the importance of that feature group. For example, if you recommender has 20 lyrical features and 10 audio features, your distance metrics will be more impacted by lyrical data on the whole. This is something to keep in mind when it comes to interpretability.


## The Learning Agenda
 
While the primary goal of this project is to generate a small scale version of Spotify's recommendation system leveraging self-found datasets, I also had a specific set of questions I wanted to answer concerning concepts related to data pipeline management, NLP and unsupervised learning. The goal of a **Learning Agenda** was to build a question-based framework to drive best in class execution for our recommender. In particular, we had the following questions:

**Web Scraping:**
  - *Is Selenium an effective tool for bespoke scraping needs? What are the pros and cons?*
   - Selenium is a highly customizable tool for automated testing and more difficult scraping tasks. However, it can be slow and is susceptible to interruption (ads, site scripts).
   
**Merging:**
  - *What are best practies for fuzzy matching?*
   - A number of different distance-based metrics can be leveraged for fuzzy text matching. Selecting the right distance metric depends on context. To improve match rates, leverage these features to train a classification model.
   
**Hip Hop:**
  - *How has the genre changed over time? Is hop hop as a lyrica artform "dead?"*
   - It may be "dead" as a lyrical art form. Far less rappers are talking about their skills, focusing instead on "money" and "the hustle" in their raps. The genre has seen a sizeable decline in rhyme density and vocab complexity since the rize of **Mumble Rapping** in 2016.
   
**NLP:**
  - *What is the most effective method for topic extraction? What are the challenges*
   - Latent Dirichlect Allocation is the most popular approach here. This approach requires some subjectivity when it comes to setting the number of topics and interpreting said topics. However, there are several scores (Coherence, Perplexity) that can assist.

**Recommender: (PLEASE SEE THE RECOMMENDER FINDINGS SECTION FOR MORE DETAIL)**
  - *How do recommendations differ between collaborative filter vs content based filter?*
   - In our implementation, content based recommendations produced more novel tracks. This is not surprising, as features like rhymedensity and positive sentiment can unearth less positive tracks.
  - *What are the major pitfalls for recommendation engines?*
    -Subjectivity: The idea that content cannot capture subjective info like humor, points of view (collaborative is better here)
    -Scalability: as features and the user base grows, the computational cost of recommendation algorithms grows significantly.
    -Sparsity: In particular with user-based features this is a problem. If you are leveraging user interactions with a specific page for site recommendations, for example, it may be that only 1% of users ever get to that page at all.


## The Approach

Please see the below visual for a visual explanation of the project from data sourcing to recommendation implementation. The workflow breaks into the following stages:


 
![flow](https://i.imgur.com/B9WMGgI.jpg)

 - **Source**: Identify the data sources needed to implement our recommendation engine. In this case the sources were Genius.com, Spotify, RapReviews.com, Pitchfork.com, and Youtube (specifically, theNeedleDrop).
 
 - **Collect**: To pull down our data a variety of libraries were leveraged (Stopipy, lyricsgenius, YoutubeDL were among some of the third party python wrappers for our APIs.
 
 - **Engineer**: 
   - *Audio*: A variety of core audio features were engineered using the Librosa library. While these weren't strictly necessary and, to some degree, duplicative with pre-existing features via Spotify, I wanted to understand the process.
   - *Lyrics*: A number of custom features were engineered based on lyrical data. Rhyme Density is one example, which was an attempt to capture the "flow of a rapper." This was done by assessing the number of potential rhyming words for every line in a song. Here, we also took time to assess the vocabulary size/complexity of our rappers, and the overall sentiment of tracks. Last, a significant amount of time was spent **extracting topics** from our lyrics. This was done using LDA, via Gensim. More information on this topic can be found in **Notebook 2.B.**
   - *Reviews*: In order to implement a recommender at the album level based on review data, we implemented a *TfidfVectorizer* after some pre-processing steps (removal of stopwords, profanity, etc.).

 - **Explore**: 
   -*Genre EDA*: With our lyrical features in place, we took some time to deep dive into how the genre has changed over time, and who floats to the top in terms of lyrical complexity and skill. We answered the following questions:
     - What rappers have the largest vocabularies?
     - What rappers have the highest rhyme density? Is this an effective measure of "flow?"
     - What are the most complex and least complex tracks?
     - What are the happiest and saddest tracks? does VADER get it right or are there challenges?
     - Is hip hop as a lyrical art form dead?

 - **Recommend**: With our features in place, we can now move into the creation of our recommendation engine. A few things to note:
     - *Metric for Recommendation*: Cosine Similarity
     - *Recommenders*:
       - Recommender 1: *Content Based Recommender* (Track Level)
       - Recommender 2: *Content Based Recommender* (Album Level)
       - Recommender 3: *Collaborative Filter* (Artist Level)

 - ***Combine**: Recommendation engines have been constructed and we then take the time to combine our approach. We do the following:
     - (1) Given a track, leverage *Recommender 1* to produce a list of candidate tracks
     - (2) Extract the album information from the track and feed this information into *Recommender 2*.
     - (3) Extract the album information from the track and feed this information into *Recommender 3*.
     - (4) Filter our candidate tracks based on *Recommenders 2 and 3*
     - (5) Deliver recommendations.
     
For a visual explanation of the **Combine** step please see below:
     
 
![hybrid](https://i.imgur.com/GIwT6Qj.jpg)

## Data Dictionary

The following data dictionary corresponds to our final data set that fed into our 'Model Selection' notebook.

#### Track Level Recommender

| Data Source | Bucket  | Field                  | Type   | Description                                                                                                                                                                               |
|-------------|---------|------------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Genius.com  | General | album                  | object | album name                                                                                                                                                                                |
| Genius.com  | General | artist                 | object | artist name                                                                                                                                                                               |
| Genius.com  | General | date                   | int    | date of album release                                                                                                                                                                     |
| Genius.com  | General | features               | object | featured artists on track                                                                                                                                                                 |
| Genius.com  | General | cleaned_lyrics         | object | refined lyrics text                                                                                                                                                                       |
| Genius.com  | General | producers              | object | producers on the track                                                                                                                                                                    |
| Genius.com  | General | song                   | object | song name                                                                                                                                                                                 |
| Genius.com  | General | genres                 | object | genres associated with track                                                                                                                                                              |
| Spotify     | General | pop                    | int    | popularity of artist                                                                                                                                                                      |
| Spotify     | General | followers              | int    | follower count for artist                                                                                                                                                                 |
| Spotify     | Audio   | acousticness           | float  | A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high confidence the track is acoustic.                                                              |
| Spotify     | Audio   | energy                 | float  | Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity.                                                                                        |
| Spotify     | Audio   | instrumentallness      | float  | Predicts whether a track contains no vocals.                                                                                                                                              |
| Spotify     | Audio   | key                    | float  | The key the track is in. Integers map to pitches using standard Pitch Class notation                                                                                                      |
| Spotify     | Audio   | liveness               | float  | Detects the presence of an audience in the recording.                                                                                                                                     |
| Spotify     | Audio   | loudness               | float  | The overall loudness of a track in decibels (dB).                                                                                                                                         |
| Spotify     | Audio   | danceability           | float  | Danceability describes how suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity. Scale 0-1 |
| Spotify     | Audio   | duration_ms            | float  | Duration in milliseconds                                                                                                                                                                  |
| Spotify     | Audio   | mode                   | float  | Modality of a track (major or minor; 1 or 0)                                                                                                                                              |
| Spotify     | Audio   | preview_url            | object | URL for 30 second snippet of mp3                                                                                                                                                          |
| Spotify     | Audio   | speechiness            | float  | Speechiness detects the presence of spoken words in a track. The more exclusively speech-like the recording (e.g. talk show, audio book, poetry), the closer to 1.0 the attribute value   |
| Spotify     | Audio   | time_signature         | float  | Notation of how many beats per bar                                                                                                                                                        |
| Spotify     | Audio   | track_href             | object | API endpoint providing track details                                                                                                                                                      |
| Custom      | Audio   | tempo                  | float  | beats per minute                                                                                                                                                                          |
| Custom      | Audio   | chroma_stft            | float  | average semitones                                                                                                                                                                         |
| Custom      | Audio   | spec_cent              | float  | center of mass for a music signal                                                                                                                                                         |
| Custom      | Audio   | spec_bw                | float  | spectral bandwidth of the song                                                                                                                                                            |
| Custom      | Audio   | rolloff                | float  | frequently below which a majority of spectral enegry resides                                                                                                                              |
| Custom      | Audio   | zcr                    | float  | rate at which music signal changes from positive to negative                                                                                                                              |
| Custom      | Audio   | mfcc                   | float  | small set of features describing spectral envelope                                                                                                                                        |
| Custom      | Lyrics  | track_unique_words_pct | float  | % of words on track unique                                                                                                                                                                |
| Custom      | Lyrics  | track_complexity       | float  | % of words on track 3+ syllables                                                                                                                                                          |
| Custom      | Lyrics  | track_rhyme_density    | float  | Number of internal rhyme candidates / lines in a song. Measures "flow"                                                                                                                    |
| Custom      | Lyrics  | sentiment_track_neg    | float  | Negative Sentiment Index (VADER)                                                                                                                                                          |
| Custom      | Lyrics  | sentiment_track_pos    | float  | Positive Sentiment Index (VADER)                                                                                                                                                          |
| Custom      | Lyrics  | sentiment_track_neu    | float  | Positive Sentiment Index (VADER)                                                                                                                                                          |
| Custom      | Lyrics  | sentiment_track_comp   | float  | Compositive Sentiment Score (VADER)                                                                                                                                                       |
| Custom      | Lyrics  | track_sad_words        | int    | # Sad words [NRC Emotion Lexicon]                                                                                                                                                         |
| Custom      | Lyrics  | track_angry_words      | int    | # Angry words [NRC Emotion Lexicon]                                                                                                                                                       |
| Custom      | Lyrics  | track_joy_words        | int    | # Joy words [NRC Emotion Lexicon]                                                                                                                                                         |
| Custom      | Lyrics  | track_ant_words        | int    | # Anticipation words [NRC Emotion Lexicon]                                                                                                                                                |
| Custom      | Lyrics  | track_trust_words      | int    | # Trust words [NRC Emotion Lexicon]                                                                                                                                                       |
| Custom      | Lyrics  | track_fear_words       | int    | # Fear words [NRC Emotion Lexicon]                                                                                                                                                        |
| Custom      | Lyrics  | track_digust_words     | int    | # Disgust words [NRC Emotion Lexicon]                                                                                                                                                     |
| Custom      | Lyrics  | track_surprise_words   | int    | # Surprise words [NRC Emotion Lexicon]                                                                                                                                                    |
