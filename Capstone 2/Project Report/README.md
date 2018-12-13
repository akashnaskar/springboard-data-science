## 2nd Capstone Project

# Sentiment Analysis on Movie Reviews with Tweets

#### Yueh-Tung (Nicole) Chao


![Home](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/Homepage.png)


## Problem
In this project we analyze sentiment on recent movie-related tweets on a scale of five values: negative, somewhat negative, neutral, somewhat positive, positive.

Twitter is an well-known American online news and social networking service on which users post and interact with messages known as "tweets". In 2012, more than 100 million users posted 340 million tweets a day and the service handled an average of 1.6 billion search queries per day. 

As tweets refelct the real-time popular opinion, by scraping tweets from twitter website and analyzing sentiment on movie reviews with tweets, it can be used for movie theater to predict how good the movie is and whether they should continue playing it or add more number of showings. It can also be used for movie producer to decide what kind of movie will be more popular and optimize their revenue.


## Client
As mentioned in previous session, by analyzing sentiment on movie reviews with tweets, it can be used for both movie theater and move producer to optimize their revenue.


## Data Set / Data 
We scraped data using Selenium and Twitter search URL on movie-related tweets. To be more specific, given the following link, we can give the search URL keyword, start-date & end-date for searching,

```
https://twitter.com/search?q=<keyword>%20since%3A<start_date>%20until%3A<end_date>&amp;amp;amp;amp;amp;amp;lang=tr%22
```

and it would return a page of tweets which fit these constraints.


Here's an example where we would like to search 'Mission Impossible' from 2018-06-01 to 2018-09-11.

```
https://twitter.com/search?q=missionimpossible%20since%3A2018-06-01%20until%3A2018-09-11&amp;amp;amp;amp;amp;amp;lang=tr%22
```

and then we can get tweets about 'Mission Impossible' posted between 2018-06-01 and 2018-09-11. Snapshot as followed:

![MI6](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/MissionImpossible.png)

By repeating the above process for recent movies, we collected about **15002** tweets.


## Data Wrangling
After scraping the data, we conduct the following steps to clean up data.

1. Drop duplicate data points.
2. Remove '\n' which represents newline in tweets.
3. Remove URLs in tweets using regular expression.

After applying the above steps, there are **14696** data points left.


## Initial Findings
During exploratory data analysis, we ask the following questions to understand more about our data:

1. How to identify useful tweets?
2. With all the useful tweets, which movie occurs most?
3. What are the most frequent tokens in tweets?
4. Usually how many tokens are there in a tweets?

and ask follow-up questions if needed.

Initial findings are:

1. We identify useful tweets using predefined keywords. After searching predfined keywords and remove data points without these keywords, there are 7628 tweets.
2. Then we check which movies occur most in tweets. The most common two are Jurassic World & Crazy Rich Asians considering our data was scraped during 2018-06-01 to 2018-09-11.
![MostCommon](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/MostCommon.png)
3. We check what are the top tokens/words in all tweets.
![Wordcloud](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/WordCloud.png)
4. We also analyze length of a tweets. After removing unwanted tokens, stop words & applying lemmatization, most of the tweets have about 5 ~ 11 useful words.
![WordCountHist](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/WordCountHistogram.png)
5. We tried to identify topic using both bad-of-words & TF-IDF. Looks like TD-IDF can at least generate more meaningful topic than bag-of-words.
6. We inspect tweets outliers which are really short or long. An observation is short tweets are pretty straight forward on sentiments while long ones seem not. Here are some examples:
<img src="https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/Outliers.png" width="300" />

## Modeling

With insights based on exploratory data analysis (EDA), we start to train predictive models.

We used [Valence Aware Dictionary and sEntiment Reasoner (VADER)](http://comp.social.gatech.edu/papers/icwsm14.vader.hutto.pdf), a popular sentiment analysis package to generate sentiments, i.e., our labels. Note that the sentiment score is a floating number ranging from -1.0 to 1.0. -1.0 means negative while 1.0 means positive. 0 means neutral.

A plot for average / minimum / maximum sentiment scores for each movie is like:

![MovieSentiments](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/MovieSentiments.png)

and the histogram of scores is like:

![SentimentHistogram](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/SentimentHistogram.png)

Note that we want to classify the sentiment into 5 groups, negative, somewhat negative, neutral, somewhat positive & positive. Intuitively, we classify them into 5 groups using three threshold -0.5, 0 & 0.5.

As for modeling, we identified four deep learning models, which are good for natural language processing to try.


 1. Long Short-Term Memory (LSTM)
    * TODO
 2. Multiplicative Long-Short Term Memory (mLSTM)
    * TODO
 3. Convolutional Neural Network (CNN)
    * TODO
 4. Temporal Convolutional Network (TCN)
    * TODO

And we train the above models with

1. Word-level Tweets
2. Character-level Tweets

Test size of Train-test-split is set to 33%.
We use accuracy and scoring metric since it's a classification problem.

A plot for each model and their coresponding accuracies is as followed:

![ModelAccuracies](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/ModelAccuracies.png)

Based on the above results,

1. mLSTM & TCM have best accuracies.
2. mLSTM performs better than LSTM.
3. TCN performs better than CNN.

In addition, in general accuracies of word-level modesl are better than accuracies of character-level models. The reason might be that the sentiments are actually formed by words instead of characters. So for character-level modeling, it would be better to predict next character, which acts like to predict the next words first, then to predict the sentiment.

There is a paper actually applying this idea, [Learning to Generate Reviews and Discovering Sentiment](https://arxiv.org/pdf/1704.01444.pdf), which trains model to predict next character and use the hidden state of these models to predict sentiment.

We applied this idea on the above four models. A plot for each model and their coresponding accuracies is as followed:

![ModelNextCharAccuracies](https://raw.githubusercontent.com/nicolechao/springboard-data-science/master/Capstone%202/Images/ModelNextCharAccuracies.png)

Indeed predicting character first and then use the hidden state to predict sentiment performs better than directly using characters to predict sentiments and mLSTM gives the best accuracy.

So, with all the models we trained, the best accuracy we acheived is about 0.65. We believe this can be improved by getting more data since the training accuracies are a lot better than testing accuracies.

### Summary
1. We tried 4 different deep learning models for sentiment analysis on both word-level and character-level tweets.
 - Long Short-Term Memory (LSTM)
 - Multiplicative Long-Short Term Memory (mLSTM)
 - Convolutional Neural Network (CNN)
 - Temporal Convolutional Network (TCN)
2. In generatl word-level mLSTM and TCN have the best accuracies for predicting sentiment. Accuracy of mLSTM is 0.651 and accuracy of TCM is 0.655. This can show that models with more memory performs better on tweets data.
3. mLSTM also performs well when predicting sentiment with hidden state of predicting next characeter with character-level texts.
4. Training accuracy is always higher than test accuracy, which can be improved with more data.


## Conclusion
Twitter is the most popular American online news and social networking service while tweets refelect the real-time opinion. By scraping tweets related to movies and analyze sentiment, it can be used for movie theater to predict how good the movie is or be used for movie producer to decide what kind of movie will be popular.

In this project, we have done data scraping to get tweets using Twitter Search URL. We identify "Jurassic World" and "Crazy Rich Asians" occur most in tweets during 2018-06-01 to 2018-09-11. We compared topics generated by bag-of-words versus TF-IDF and confirmed TD-IDF performs better than bag-of-words.

In addition, we applied 4 different deep learning models, LSTM, mLSTM, CNN and TCN to predict sentiment generated by VADER, a popular sentiment analysis package, on both word-level and character-level tweets. We also developed models to predict next character of tweet first and then use the hidden state of this model to prdict sentiment, which indeed outperforms models that predict sentiment directly use characters.

Among all the models, we achieved accuracy 0.651 with word-level mLSTM and accuracy 0.655 with word-level TCN. Training accuracy is actually higher in these models, about 0.89 for mLSTM, which suggests we can get more data to improve the test accuracy. The model can definitely be used for movie theater and producers for revenue optimization.


## Next Steps
There are some next steps we can do further to improve our model.

1. When acquiring labels with VADER, we can try to apply other strategies instead of using hard-coded threshold.
2. Apply dimmensionality reduction on texts to see if accuracies can be further improved.
3. Apply pre-trained model for embedding.
4. Get more data to improve test accuracies.


## Other Potential Data Sets
More data can be scraped give predefined keywords using Twitter Search URL.

## References
1. Valence Aware Dictionary and sEntiment Reasoner (VADER).
 * Paper: [VADER: A Parsimonious Rule-based Model for Sentiment Analysis of Social Media Text](http://comp.social.gatech.edu/papers/icwsm14.vader.hutto.pdf)
2. Multiplicative Long-Short Term Memory (mLSTM)
 * Paper: [Learning to Generate Reviews and Discovering Sentiment](https://arxiv.org/pdf/1704.01444.pdf)
 * Paper: [Multiplicative LSTM for Sequence Modeling](https://arxiv.org/pdf/1609.07959.pdf)
 * Code: https://github.com/titu1994/Keras-Multiplicative-LSTM
2. Temporal Convolutional Network (TCN)
 * Paper: [An Empirical Evaluation of Generic Convolutional and Recurrent Networks for Sequence Modeling](https://arxiv.org/pdf/1803.01271.pdf)
 * Code: https://github.com/philipperemy/keras-tcn