---
layout: post
title: Sentiment analysis
description: A review of textblob's sentiment analysis, and a better way to do it with neural networks.
modified: 2019-04-06
tags:
  - machine learning
  - sentiment analysis
  - data science
image:
  feature: "sentiment.jpg"
  credit: BarnRaisers
  creditlink: https://barnraisersllc.com/2017/01/best-tools-sentiment-analysis-free-fee/
published: true
---

### Summary
Maybe you want to see how people are responding to a story or blog of yours -- do they love it or hate it; does it make them excited?  Maybe you want to detect the tone of people mentioning your company or product.  Or perhaps you want to keep tabs on the mood of stocks in order to make money.  We can use sentiment analysis for all of these things.

In this multi-part series, we will look at different methods of sentiment and emotion analysis in both Python and R.  We will compare performance on a standard dataset, and also scrape our own live tweets for analysis.  Finally, we will check performance on stock-related text snippets from news headlines and stocktwits.

<!--more-->

Currently if you Google 'Python sentiment analysis package', the top results include textblob and NLTK.  However, both of these use Naive Bayes models, which are pretty weak.  Another option is the VADER lookup dictionary, which has a pre-set score for a number of words.

We can also train our own neural network on data, and add our own custom data -- adding value to our creation.  Sure, you can also train your own Naive Bayes model -- which I'll show you how to do -- but for reasons we'll get in to, it doesn't work super well.

### Classic models

Let's begin with the simple model.  The *simplest* model is a lookup dictionary.  You take your words, then check if they are in your dictionary.  If you find the word in the dictionary, return the sentiment.  Here is an example of using the `textblob` lookup dictionary:

```python
import textblob

tb = textblob.TextBlob("this as not a good sentence and I don't like it :)")
tb.sentiment_assessments
```

This will output:

```python
Sentiment(polarity=0.07500000000000001, subjectivity=0.8, assessments=[(['not', 'goo
d'], -0.35, 0.6000000000000001, None), ([':)'], 0.5, 1.0, 'mood')])
```

We can see it has polarity (sentiment -- +1 is most positive, -1 most negative), subjectivity (0 is completely objective, i.e. factual; 1 is completely subjective, i.e. an opinion), and assessments.  The 'assessments' are each chunk textblob is using to assess the sentence.  The first phrase has a modifier -- 'not' -- which changes the score of the word 'good'.  'not' will flip the sign of the polarity (sentiment), and also multiplies it by 0.5 (because the original score for 'good' is 0.7 according to the [dictionary](https://github.com/sloria/TextBlob/blob/90cc87ab0f9e25f37379079840ec43aba59af440/textblob/en/en-sentiment.xml#L1097)).  The last thing which is `None` for 'not good' and 'mood' for the smiley is the ['semantic label'](https://github.com/sloria/TextBlob/blob/dev/textblob/_text.py#L680).  For [emoticons](https://github.com/sloria/TextBlob/blob/dev/textblob/_text.py#L223) in textblob, this is labeled a 'mood', for an exclamation mark in parenthesis, like (!), this is considered irony (making that assessment completely subjective).

As you can tell, the default sentiment analysis in textblob is very rule-based.

### Next step up: Naive Bayes

The next step up is to use the Naive Bayes model.  [This ends up following the equation](http://scikit-learn.org/stable/modules/naive_bayes.html):

{% raw %}
$$ \hat{y}_c = P(c) \prod_{i=1}^n P(x_i|c) $$
{% endraw %}


where $$\hat{y}_c$$ is the predicted probability of the class $$c$$, $$P(y_c)$$ is the probability of class $$c$$ in the training dataset, and $$P(x_i \vert y_c)$$ is the probability of seeing a word $$x_i$$ given the class $$c$$.  The big Pi symbol ($$\prod$$) means we multiply all these $$P(x_i \vert y_c)$$ values together for all the words in our dataset.  

Another way of writing this (identical to the sklearn explanation) is:

{% raw %}
$$ \hat{y} = argmax_y P(y) \prod_{i=1}^n P(x_i|y) $$
{% endraw %}

where we take the largest probability out of our predictions, and use that as our class prediction.  A detail which can make this incorrect is if we have two classes, we can set the threshold anywhere between 0 and 1 to choose our prediction, meaning our predicted class won't always be the max value.  This is related to [ROC/AUC](http://gim.unmc.edu/dxtests/roc3.htm).  Want to learn more about setting the best threshold for text classification/binary sentiment analysis?  Sign up for the email list to get notified when I publish more materials, including those on ROC/AUC and training your own sentiment analysis classifiers:

<form name="submit-to-google-sheet">
  <input name="email" type="email" placeholder="Email" required>
  <button type="submit">Send</button>
</form>

#### Small detail:
[Here is the sentiment dictionary used the the textblob library](https://github.com/sloria/TextBlob/blob/90cc87ab0f9e25f37379079840ec43aba59af440/textblob/en/en-sentiment.xml).  Textblob adds a bit of complexity with ['assessments'](https://github.com/sloria/TextBlob/blob/dev/textblob/_text.py#L854), which are words with modifiers like 'not'.  I'm not sure where this is in the [docs](http://textblob.readthedocs.io/en/dev/index.html) exactly, but in the source code, it talks about it [here](https://github.com/sloria/TextBlob/blob/dev/textblob/_text.py#L661):

```python
### SENTIMENT POLARITY LEXICON #####################################################################
# A sentiment lexicon can be used to discern objective facts from subjective opinions in text.
# Each word in the lexicon has scores for:
# 1)     polarity: negative vs. positive    (-1.0 => +1.0)
# 2) subjectivity: objective vs. subjective (+0.0 => +1.0)
# 3)    intensity: modifies next word?      (x0.5 => x2.0)

# For English, adverbs are used as modifiers (e.g., "very good").
# For Dutch, adverbial adjectives are used as modifiers
# ("hopeloos voorspelbaar", "ontzettend spannend", "verschrikkelijk goed").
# Negation words (e.g., "not") reverse the polarity of the following word.

# Sentiment()(txt) returns an averaged (polarity, subjectivity)-tuple.
# Sentiment().assessments(txt) returns a list of (chunk, polarity, subjectivity, label)-tuples.

# Semantic labels are useful for fine-grained analysis, e.g.,
# negative words + positive emoticons could indicate cynicism.
```

#### Small detail: multiple entries in lookup dictionary

Scores for words with multiple entries in the dictionary are [averaged](https://github.com/sloria/TextBlob/blob/dev/textblob/_text.py#L773).  This can be verified by checking out the subjectivity of the word [accurate](https://github.com/sloria/TextBlob/blob/90cc87ab0f9e25f37379079840ec43aba59af440/textblob/en/en-sentiment.xml#L51), which has 3 versions in the lookup dictionary.  The subjectivity score is 0.63333 (the average of 0.5, 0.6, and 0.8 -- the 3 values for 'accurate' in the lookup dictionary).  Here is example code you can use to verify this (I ran it in an IPython shell):

```python
import textblob

# simplest example
tb = textblob.TextBlob('accurate')
tb.subjectivity
tb.sentiment_assessments

# negated
tb = textblob.TextBlob('not accurate')
tb.subjectivity
tb.sentiment_assessments

# a longer sentence -- noticed it misinterprets 'pretty' here
tb = textblob.TextBlob('this sentence is pretty accurate')
tb.subjectivity
tb.sentiment_assessments
```


<script>
  // form submission for emails to google sheets
  const scriptURL = 'https://script.google.com/macros/s/AKfycbxY49ErwyHKtnZm1TGyfT2WdS40i9z8JUtJmJUTsg3biK7lQDoS/exec'
  const form = document.forms['submit-to-google-sheet']

  form.addEventListener('submit', e => {
    e.preventDefault()
    fetch(scriptURL, { method: 'POST',
                      body: new FormData(form)})
      .then(response => console.log('Success!', response))
      .catch(error => console.error('Error!', error.message))
  })
</script>
