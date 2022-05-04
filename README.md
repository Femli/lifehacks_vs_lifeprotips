# Evaluating the Differences between r/lifehacks and r/LifeProTips
#### By: Franco Sanchez

### Problem Statements and Introduction
> 1. Develop a classification model that can predict whether a reddit post belongs to the subreddits r/LifeProTips or r/Lifehacks, based on the content of the post.
> 2. Identify the top 10 keywords that distinguish r/LifeProTips and r/Lifehacks

Hi there, thank you for taking the time to read my work. This repository includes several notebooks that capture my entire analysis. The notebooks are prefixed from 0-4, and you can find them referenced below. If you wish to replicate this analysis, clone this repository and run each notebook in sequential order, 0 to 4.


|Index|Title|Description|
|---|---|---|
|0|[Workflow Functions]()| This notebook contains various custom functions that aid our analysis.|
|1|[Data Wrangling]()| This notebook contains the subreddit data wrangled via the pushshift API.|
|2|[Preprocessing & Feature Engineering]()| This notebook captures all the preprocessing and feature engineering performed on our training and test data splits.|
|3|[Model Performances]()| This notebook contains all the model performances on the engineered features.|
|4|[Production]()| This notebook captures the final model and inferences made from the coefficients.|

### Background
This project is focused on developing a classification model that can best predict whether a reddit post belongs in the [r/lifehacks](https://www.reddit.com/r/lifehacks/) or in [r/LifeProTips](https://www.reddit.com/r/lifehacks/) based on the body text of the post. Additionally, our model provides insights on tokenized words that distinguished `r/lifehacks` and `r/LifeProTips`. The data was wrangled from the web using the [Pushshift API](https://github.com/pushshift/api).

We include a presentation deck that summarizes our results [here]().

---
### Data and Resources

For this analysis, we generated our own datasets, which you can find in the [dataset]() directory. The `submissions_data.csv` is the raw data wrangled via the API. The `submissions_data_cleaned.csv` is the dataset we used to train and test our models. Here is the data dictionary for both datasets:

|Column|Data Type|Description|
|---|---|---|
|**author**|string|The author of the subreddit post|
|**author_fullname**|string|An alias for the author's registered name|
|**created_utc**|float|Epoch timestamp (UTC) of when the post was submitted|
|**selftext**|string|Body text of the post|
|**title**|string|Title of the post|
|**subreddit**|string|If the post belongs to *r/lifehacks* or *r/lifeprotips*|
|**is_video**|string|If the post is a video post|
|**num_comments**|float|Number of comments on the post|
|**score**|float|Number of upvotes a post has|
|**upvote_ratio**|float|Proportion of upvotes and downvotes on the post|

For our modeling features, we only used the `selftext` columns, and our response was the `subreddit` column.

---
### Methodology and Analysis
The main components of our analysis are the engineering of features and the various classification models we iterated our features on. Here is a summary of our feature engineering.
1. Preprocessed by dropping all null values, since we are only interested in posts with text data.
    - Note: After data cleaning, our data became unbalanced, where `r/LifeProTips` accounted for 76% percent of the data.
2. Encoded our response to boolean values based on the subreddit:
    - `r/lifehacks` : 1
    - `r/LifeProTips` : 0
3. Vectorized the `selftext` feature into various tokens, which yielded thousands of column data. In total, we generated four variations of our tokens.
4. Vectorized the `selftext` feature using the "term frequency-inverse document frequency" (TF-IDF) method; generated five variations of our tokens.
5. Vectorized the `selftext` feature using the lemmatization method; generated three variations of our tokens.
6. Iterated all engineered features on the following classification models:
    - Logistic regression
    - K-Nearest-Neighbors
    - Bagged trees
    - Extra trees
    - Adaptive boosting
    - Ensembles
    
Additionally, we grid-searched over several hyperparameters to yield the strongest results. Our best performing model was logistic regression, with the following performance metrics on unseen data:
- Accuracy: 0.746
- Balanced Accuracy: 0.693
- **Recall**: 0.591
- Specificity: 0.796
- **Precision**: 0.478
- **F1 Score**: 0.528
- AUC Score: 0.78

Since our data was imbalanced, and our positive class `r/lifehacks` became the minority class, the recall, precision, and F1 scores were the most important.

### Recommendations and Future Work

Overall, our strongest model performed fairly well in predicting if a reddit post belongs to `r/lifehacks` or `r/LifeProTips`. However, when evaluating the model coefficients, we see that some of the most impactful words are somewhat erroneous. Our recommendation for improving the model is to further preprocess the data to remove spam or bot posts that lead to noise in our model.

---
