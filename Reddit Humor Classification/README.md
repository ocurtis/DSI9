# Leveraging NLP for Dad Humor Classification...That's the Joke


## Approach

Reddit has approached the DSI Boston team with a predicament. Two popular subreddits, r/Jokes and r/DadJokes, are in need of help. Cross-posting is a common tactic on Reddit across the board, but it is particularly bad between these two subreddits. They'd like the DSI team to work alongside internal UX experts and developers to build a prototype classification system that will prompt users for the subreddit they should be posting to when drafting their submission. They'd also like to understand if Natural Language Processing is the best way to build this prototype, or if there are other non-NLP options that are more reliable.

This project has been broken into the following notebooks. Please click the link below for a **brief summary of the steps taken for each notebook.** Otherwise feel free to follow along in each notebook.

**Workflow Summary**
 - [3.1 Data Collection](#Data-Collection)
 - [3.2 Homophone Homonym Scraper](#Homophone-Homonym-Scraper)
 - [3.3 Data Cleaning, Feature Engineering](#Data-Cleaning,-Feature_Engineering)
 - [3.4 Model Selection](#Model-Selection)
 - [3.5 Insights](#MInsights)
 
 
 ## Findings

 - **r/Joke, r/DadJoke classification is difficult**. Our model struggled to improve over baseline, and had an extremely low level of specificity. We chalk this up to cross-pollination between these properties, and the sometimes-ambiguous nature of humor and joke telling which requires a significant amount of context and knowledge. **To solely predict if text is a dad joke, a different classification not based on sub-reddit is recommended**.
 - **Polarity of Words**: *Non-PC words* are strong predictors for r/Jokes; *wholesome/familial* terms are strong predictors for r/DadJokes.
 - **Commenters behave similarly across subreddits** Our model saw accuracy scores improved with post text and comments combined; it underperformed when trained on just comments. Commenters in these posts are often responding with their own brand of humor and it can quickly veer off topic, so this may be one reason why our model struggled here.
 - **An ensemble NLP model was out top performer...But a simpler non-NLP model also showed promise**. Our final model had close to 1,200 text based features, with stable accuracy scores between training and test sets. Out ladder, non-NLP model had 8 total features and performed almost as well as our NLP winner on our test data (with higher variance).


## Data Dictionary

The following data dictionary corresponds to our final data set that fed into our 'Model Selection' notebook.


| Features             | DataType | Description                                     |
|----------------------|----------|-------------------------------------------------|
| subreddit            | int      | Binary. 1 if r/DadJokes, 0 if r/Jokes           |
| title                | object   | Title of post                                   |
| body                 | object   | Body of post                                    |
| comment_text         | object   | Text from top ten parent comments               |
| joke                 | object   | Title and Body combined                         |
| joke_length          | int      | Word count of 'joke'                            |
| full_text            | object   | Title, Body, Comment text combined              |
| Score                | int      | Cumulative upvotes                              |
| over_18              | bool     | NSFW flag                                       |
| created_utc          | object   | Timestamp for post                              |
| num_comments         | int      | Number of comments on post                      |
| permalink            | object   | Link to comment thread                          |
| url                  | object   | Link to post                                    |
| id                   | object   | Post ID                                         |
| author               | object   | Post author                                     |
| pun                  | int      | Pun flag (1 if yes, 0 if no)                    |
| profanity            | int      | Profanity flag (1 if yes, 0 if no)              |
| setup_did you        | int      | Post contains "did you" setup                   |
| setup_how do         | int      | Post contains "how do" setup                    |
| setup_what do        | int      | Post contains "what do" setup                   |
| setup_why do         | int      | Post contains "why do" setup                    |
| tokenized_joke       | object   | List of words in 'joke' for lemmatization       |
| tokenized_comment    | object   | List of words in 'comment'  for lemmatization   |
| tokenid_full_text    | object   | List of words in 'full_text'  for lemmatization |
| joke_lemmatized      | object   | Lemmatized 'joke' text                          |
| comment_lemmatized   | object   | Lemmatized 'comment' text                       |
| full_text_lemmatized | object   | Lemmatized 'full_text' text                     |


## Workflow Summary

### Data Collection
**Grabbing Reddit Posts**: 
  - The first step of our workflow is to grab Reddit posts. We execute this task by leveraging the PushShift Reddit API
  - The PushShift Reddit API has a number of customizable parameters. We leverage the 'before_date' parameter (in epoch time), alongside subreddit filters, to iteravely grab posts from our subreddits of of choice over the last ~2 months.

**Grabbing Reddit Comments**: 
  - Here, we employ a separate strategy. We take the comment URLs from our PushShift JSON results and iterate through each url to access the comment pages
  - For each comment page, we grab the *top 1o parent comments*. We do this because we believe comments lose relevance the lower they are on the page. In addition to this, we believe that parent comments are the only comments potentially relevant to the original post.
  - Note that PushShift also hase the functionality to query comments in what would likely be a faster process to do so.
  - Rather than compiling a list of comments here, we simply build a string of text for reference.
  
**Combining Data**: 
  - Once we've collected our data, we combine and save our dataframe for cleaning.
  
### Homophone Homonym Scraper
**Wikipedia Scraping**: 
  - Wikipedia contains a relatively exhaustive list of homophone and homonym words
  - Here we leverage BeautifulSoup to grab page contents and parse out with some manual intervention
  - We then save these words to a .csv for later feature engineering

### Data Cleaning, Feature Engineering
**Cleaning**: 
  - A majority of our cleaning takes place through post elimination. We scan for posts that meet score, text, and length thresholds. This reduces our collection of posts considerably
  - Once we've reduced our number of observations, we move into **regex** processing. A number of steps are taken for both our post and comment strings to remove special characters and oddities like double spacing.
  - Once cleaning is completed, we combine our text data so we have the following **Training Datasets: Joke Only, Comment Only, Full Text**
  
**Feature Engineering**
  - We then construct a **joke_length** feature based on the word counts of our *Title* and *Body* combined
  - **Profanity, Pun Flags** are added to our dataset. The former is constructed based off Homophone/Homonym data we collected
  - Finally, we hypothesize that there are "typical joke setups" for r/DadJoke posts. We flag these as well

### Model Selection
 - We approach the modeling process with the follow variables to test out.
 
| Variable       | Values                                                              | Anticipated Impact | Realized Impact |
|----------------|---------------------------------------------------------------------|--------------------|-----------------|
| Training Data  | Joke, Comment, Full Text                                            | High               | High            |
| Pre-Processing | None, Lemmatization                                                 | Med                | Low             |
| Vectorization  | CountVectorizer, TfidfVectorizer                                    | Med                | Low             |
| Model          | LogReg, MNB, AdaBoost, GradientBoost, KNeighbors, SVC, RandomForest | High               | High            |

**Lemmatization**: 
  - One of our test cells for modeling will be Lemmatization vs Non-Lemmatization. This involves the reduction of words to their root. In the case of lemma, these will remain interpretable words -- this is not always the case with the alternative reduction approach of stemming.
  
**Model Setup**
  - We will be iteratively building models based on different combinations of the variables above. In this section, we take time to build the function that will enable these combinations and corresponding/appropriate gridsearches
  - We also take time here to create the needed **training data** via train_test_split().
  
**Vetorization and Model Building**
  - With our training data in place, we apply CountVectorizer / TfidfVectorizer to our text features. The former is a simple count of word frequency, while the latter applys a weighting based on how frequently it appears in the corpus. Rarer words will recieve higher weighting.
  
**Model Evaluation**
  - *Training Data*: **Full_Text** training data has the strongest impact on accuracy for our collection of models. Impact: High.
  - *Pre-Processing*: Minimal difference in performance between lemmatization and non-lemmatization. Impact: Low
  - *Vectorization*: We see a mix of cvec, tvec in our top performing models. Impact: Low
  - *Model*: Strong performance flucations model to model were seen. **GradientBoost, RandomTree, LogisticRegression** among top performers. To determine is we could reduce variance further and push our accuracy, we also generate an **ensemble model** (Votingclassifier).
 
### Insights

**Differentiators**
 - By evaluating the coefficients of our models, we are a able to determine what words have the strongest bearing on r/DadJoke classification. We find that words associated with religion, race, and otherwise-Non PC topics are strongly associated to r/Jokes; words associated with family, wholesomeness, and simple joke contexts (walking, eating) are strongly associated to r/DadJokes
**Confusion Matrix**
 - A Deeper look at where our model struggles with classification shows that it's very good at correctly predicting when a post is from r/DadJokes, but exceptionally bad when predicting if a post is from r/Jokes. This results in a poor ROC curve and corresponding AOC.
