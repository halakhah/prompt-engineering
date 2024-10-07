# prompt-engineering

This project analyzes the effectiveness of large language models in performing common tasks in natural language processing, with a focus on sentiment analysis. Utilizing forum data from Reddit, the analysis explores the capacity of these models to correctly format data according to specific guidelines and structures, as well as their ability to adequately perform sentiment analysis on text-based data. By examining trends in model accuracy, consistency in data formatting, and the handling of diverse linguistic expressions, this study sheds light on the strengths and limitations of large language models in real-world applications.

## Objective

To analyze the effectiveness of large language models (LLMs), specifically ChatGPT, in performing sentiment analysis on forum posts and comments, focusing on data formatting accuracy and sentiment classification consistency.

Key Areas of Focus:

1. Sentiment Analysis with LLMs:

Evaluate the ability of large language models to classify sentiment (Positive, Neutral, Negative) in both forum posts and associated comments. The task involves determining how accurately the models perform when handling multiple inputs and context-specific nuances.

2. Data Formatting and Consistency:

Assess the performance of LLMs in generating sentiment classifications that adhere to required output formats, such as producing structured lists of sentiment tuples for each post-comment pair in CSV format.

3. Model Accuracy and Handling of Edge Cases:

Investigate how well the models handle ambiguous or complex linguistic structures where sentiment might not be immediately clear. Identify common errors and challenges in correctly labeling sentiment for diverse, real-world data such as Reddit posts.

4. Scaling the Approach:

Test the model’s ability to scale sentiment analysis for larger data sets, scraping Reddit posts and top comments to evaluate its performance on datasets with various sentiments, structures, and conversational tones.

Data Sources

	•	Reddit Forum Data: Scraped using PRAW (Python Reddit API Wrapper) for top Reddit posts and associated comments.
	•	LLMs: OpenAI’s ChatGPT, utilizing its capabilities for sentiment classification and data formatting.

Tools and Technologies Used

	•	Programming Language: Python
	•	Data Retrieval: PRAW (Reddit API)
	•	Large Language Models: Hugging Face’s Transformers, OpenAI ChatGPT

Skills Demonstrated

	•	Data Collection: Scraping Reddit forum posts and comments using Python’s PRAW library.
	•	Prompt Engineering: Refining prompts for LLMs to generate accurate and correctly formatted sentiment labels.
	•	Data Wrangling: Cleaning and preparing forum data for analysis.
	•	Error Analysis: Identifying areas where sentiment classification failed and proposing improvements.

Project Structure

1. Initial Prompting for Sentiment Analysis

We’d like for ChatGPT to perform sentiment analysis on a collection of forum posts and associated comments. To that end, we’re going to develop a prompt which can get ChatGPT to ingest formatted data (a forum post and an associated comment) and return a corresponding sentiment analysis, where each piece of data maps to a 2-tuple of the form [(x,y)], where x is the sentiment analysis of the forum post, and y is the sentiment analysis of the associated comment post. Each sentiment analysis can take on one of three values: Positive, Negative, or Neutral.

Attempt 1.1

Prompt: 

I'm going to give you a list of forum posts. Each forum post has an associated comment. Your task is to perform sentiment analysis on the posts and the comments by classifying each as either positive, negative, or neutral sentiment. Do not explain your reasoning. Return the data for each forum post as a 2-tuple. 

Post: "Watching the coronavirus spread reminds me of waiting for a hurricane to hit: it's coming, and it will probably be bad, but it's so slow... Although there has been no 'community spread' in the US yet, I think it will happen. Human nature always trumps even the best-laid plans." 

Comment: "Confirmed cases in 28 countries."

Explanation: 
Self-explanatory. Concise instructions, followed by a command to not explain reasoning. ChatGPT and other LLMs often take on an educational persona for these sorts of requests, and they’ll waste time and compute by tediously explaining their reasoning. Finally, there’s an instruction on how to format the data. 

Response:

Here is the sentiment analysis of the provided forum post and comment:
Post: Negative
Comment: Neutral
Result:
("Negative", "Neutral")

 Observations: We received extraneous information, and the result wasn’t formatted quite correctly. 

Attempt 1.2 

Prompt: 

I'm going to give you a list of forum posts. Each forum post has an associated comment. Your task is to perform sentiment analysis on the posts and the comments by classifying each as either positive, negative, or neutral sentiment. Do not summarize anything. Do not explain your reasoning. Return the data as a list of 2-tuples for each forum post. 

Post: "Watching the coronavirus spread reminds me of waiting for a hurricane to hit: it's coming, and it will probably be bad, but it's so slow... Although there has been no 'community spread' in the US yet, I think it will happen. Human nature always trumps even the best-laid plans." 

Comment: "Confirmed cases in 28 countries."

Explanation: 

Added an additional command to not summarize anything, and also gave more specific instructions on how to format the resulting data.

Response: 

[("Negative", "Neutral")]

Observations: 

This prompt worked very well. We didn’t receive any unnecessary pre or post-amble, and the data was correctly formatted. At this point, the prompt addresses the goal as-is. 



## Scaling Up

The input data supplied in the assignment instructions only contained a single forum post and comment. In order to see how well this prompt holds up, we should feed chatgpt a larger set of data. So, let’s scrape some Reddit posts, along with the top voted comment for each post. Here’s the code we used to get the data.

```python
import pandas as pd
import praw

data = pd.DataFrame({'Post': [], 'Comment': []})


reddit = praw.Reddit(
   client_id="8QIJn04bRxOWInsXPaFMlQ",
   client_secret="5Htc2X5V3rftNZj0-Atyhw4h4C-BPQ",
   user_agent="my user agent",
)

subreddit = reddit.subreddit("pics")

posts = subreddit.top('year')

for post in posts:
   post.comment_sort = "best"
   if post.num_comments < 10:
       continue
   data.loc[len(data)] = [post.title, post.comments[1].body]


data.to_csv('posts.csv', index=False)
```

We chose reddit.com/r/pics as the source of our data simply because it’s a popular subreddit which generates hundreds of new posts per day, with a wide array of sentiments. We restricted our scraping to only posts that had ten or more comments, just to make the code work nicely with how comments work on Reddit. We’re going to feed this CSV file to Chatgpt, along with an updated prompt, in order to see how well it does at handling larger sets of data.


