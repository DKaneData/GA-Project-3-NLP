# Predicting Marvel and DC Subreddits using APIs and NLP
by David Kanevsky
General Assembly, Data Science Immersive
May 2024

## Problem Statement
Marvel and DC comics are two of the best known comic companies, that have published thousands of comic books and graphic novels, and created tens of thousands of comic book characters, several of which have transended the comic book medium and been turned into high profile movies, TV shows and video games.

However, one common critique is that while Marvel and DC comics may be different brand names, they actually have a lot of parallels in common with them. There are several [articles](https://collider.com/marvel-dc-characters-copycats/#:~:text=Comics%2C%20specifically%20those%20of%20the,%2C%20designs%2C%20and%20so%20on.) and even a [reddit thread](https://www.reddit.com/r/comicbooks/comments/15m0sln/similar_characters_between_marvel_and_dc/) about how many of the characters are very similar to each other. 

To see how similar or different the comic book companies are, I am going to use the [reddit api](https://www.reddit.com/dev/api/) to scrape the [DC comics](https://www.reddit.com/r/DCcomics/) and [Marvel](https://www.reddit.com/r/Marvel/) reddits. Using natural language processing, I will build several models to see if we can use the text to identify.

Additionally, both companies have had [crossovers](https://dc.fandom.com/wiki/DC_Marvel_Crossovers) where the characters interact with each other.

## Process
On April 26th, 2024, I pulled 2,891 reddit posts from the DC and Marvel comics subreddits. After cleaning the data to remove URLs and punctuation, and then removing duplicates (including what appear to be duplicate posts that have different number of up votes/up votes ratio, but have identical text in both the post and the title), I was left with 1,675 unique posts.

While all the reddit posts had titles, nearly half (834) had no text in the body of the post, indicating the post was an image, video or a link that NLP would not be suitable on.

The workbooks should be read in the following order:

1. 1-pulling-reddit-data.ipynb

This was where I accessed the Reddit API, got the posts and put them into CSVs of around 100 posts per CSV, and then combined the CSVs into 1 CSV called comics-reddit.csv.

Additionally, I also used the reddit API to pull data from the Babylon Bee and The Onion subreddits, and saved that data into another subdirectory called comedy-data. While I didn't end up using that data for this project, I scraped it to have as a back up in case there were issues with the DC/Marvel subreddit data and I needed to pivot topics.

2. 2-cleaning.ipynb

This is where I cleaned the data by making everything lower case (so that when it ran counts on Marvel and marvel, it treated them as a single word), removed punctuation and URLs, and also removed duplicates. In addition to removing duplicates where all the columns were duplicated, I also removed the duplicates where there were multiple identical combined titles and posts. This was because while these posts seemed different because they had different numbers of up votes and up votes ratio, the identical text in both the post and title indicate that this was likely the same post, just pulled at a different. I also created several variables based on the length of the title and post in both characters and words, and whether the post has text or not. I also explored some data on the number of words in the subreddit titles and posts to understand what the impact of cleaning might do when it comes to analyzing and modeling the data.

3. 3-additional-EDA

This is where I also did some analysis on the up votes and up vote ratio for each subreddit on the cleaned data. While Marvel posts have a higher average number of up votes, that is because there are more outliers that get a very large number of upvotes (2,000+) than DC. The median number of upvotes and the mean/median number of the upvote ratio is similar between the two subreddits.

4. Model-1-GS-CVEC

This is where I extracted the text through a CountVectorizer function and ran a Logistic Regression through a Grid Search pipeline over 90 hyperparameters. This determined the best parameters were with maximum of 3,000 features, that features needed to appear at least twice, the ngram range is 1, and using English stop words. The model predicted whether a reddit title/post was from the Marvel or DC subreddit with 94.7% accuracy.

6. Model-2-GS_TFIDF

This is where I extracted the text with Term Frequency - Inverse Document Frequency (TF-IDF) function to look at not only how often a feature appears, but how important the feature is based on the frequency it appears relative to other features. I ran a Logistic Regression through a Grid Search pipeline with 90 hyperparameters. This determined the best hyper parameters were with a maximum of 3,000 features, the features needed to appear at least twice, the Ngram range was 1, and using English stop words. The model predicted whether a reddit title/post was from the Marvel or DC subreddit with 95.2% accuracy. Since this model had a higher accuracy, I used this for the analysis. The model performed well because it was able to identify both the company names, as well as prominent characters that are more likely to appear in each relative subreddit. The vast majority of the incorrect predictions were largely ones where the model was not very confident in its prediction, thinking the probability of a post being in one subreddit or another was between 40% and 60%.

## Data Sets and Data Dictionary

|Feature|Type|Description|
|---|---|---|
|title|object|The title of the reddit post|
|post|object|The text of the body of a reddit post|
|subreddit|object|Identifying whether the post came from the Marvel or DC subreddit|
|up_votes|integer|The number of up votes the post received|
|up_votes_ratio|float|The ratio between the up votes a post has received and the number of total votes it has received |
|clean_title|object|The title of the reddit post, after punctuation and URLs are removed|
|clean_post|object|The text of the reddit post, after punctuation and URLs are removed|
|clean_text|object|The combined clean title and clean post|
|title_length|int|The number of characters in the original title (before cleaning)|
|post_length|int|The number of characters in the original post (before cleaning)|
|title_word_count|int|The number of words in the original title (before cleaning) as defined by the spaces between words|
|post_word_count|float|The number of words in the original post (before cleaning) as defined by the spaces between words|
|post_has_text|int|Identifying whether after cleaning, a post has text (1) or not (0) |

The first 5 columns (title, post, subreddit, up_votes, up_vote_ratio) are in both the original comics_reddit.csv and the cleaned_comics_data.csv. The remaining columns were added into the cleaned_comics_data.csv.

Additionally, there are CSVs (Marvel_04_26_x.csv and DCComics_04_26_x.csv) that are the original CSVs outputted when scraping the reddit API subreddit, with the X representing the order the post was scraped. These CSVs were then combined into the comics_reddit.csv. These CSVs have just the 5 first columns in the data dictionary.

## Analysis
Since the TF-IDF function with a Logistic Regression model had a higher accuracy, I used this for the analysis. The model performed well because it was able to identify both the company names, as well as prominent characters that are more likely to appear in each relative subreddit. Of the less than 5% predictions the model classified incorrectly, the majority (14 of 22) of those predictions, the model was not very confident whether a title/post was in the Marvel or DC subreddit (less than 60% probability of being in one subreddit or other). 
