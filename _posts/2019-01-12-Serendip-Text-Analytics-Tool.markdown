---
layout: single
title:  Serendip - Text Exploration Tool
date:   2019-01-12 19:49:00 -0700
<!-- tags: PCA feature-selection -->
tags: text javascript python flask data-visualization
---

I implemented a revised version of [Serendip](https://graphics.cs.wisc.edu/Papers/2014/AKVWG14/Preprint.pdf) as a part of the 'Data Visualization' course.

You can find the entire codebase on [this git repository.](https://github.com/RishabhPatil/Serendip)

Here's a description of the views we implemented:

## Corpus View:

Corpus View represents the trends between documents and topics encoding the adjacency in circles.
Vertical Axis contains all the names of documents in the dataset while the horizontal axis represents all the topics extracted from Topic Modeling.

<img src="/assets/img/Corpus.PNG">

The proportion of each topic in the document can be determined by the size of the circle on the intersection. When the topics or documents are selected the respective label colors are changed to red.
For large text corpora, there could be a large number of dimensions of the matrix.
To make it less confusing, we provide a selection method so that the user would be able to access the relevant documents and topics easily.
By clicking on the topics at the top, the intersection circles or the vertical lines, we can see the top rank words of the respective topic or topic distribution on the right side of the corpus view. 
<br>
<br>
Topic Distribution shows top 20 words from the selected topic in the form of horizontal bar chart.
The horizontal length of bar signifies the intensity of word in the topic. Higher the intensity, higher is the rank of that word.
Likewise, by clicking on the document name in the corpus view, we can see the topic overview for the same document below the topic distribution.
<br>
<br>
Topic Overview of the document is shown in the form of a vertical line chart which shows us the behavior of document line by line.
Different lines represent the different topics in the document.
Horizontal Axis signifies the density of the topic at that position in the document.
Different topics are encoded with different colors so that it would be easy to distinguish between topics and relate the words from the same topic in the Text View.

---

## Text View:

The text view allows the user to view the distribution of topics throughout a document, as well as the individual words which contribute to each of these topics. We use the same color coding for topics throughout the system, this consistency makes it easier to observe patterns and make deductions from the text data. The text view is comprised of three visualizations Topic Buttons, Document Text, and Topic Distribution. These visualizations are interconnected and the interactions in text view affect all of them.

<img src="/assets/img/Text.PNG">

### Topic Buttons:
The left panel on the text view displays the total topics of the system and a progress bar within each of these buttons shows the topic score for the currently selected document. Clicking any of these topic buttons will highlight all words in the document that belong to this topic. This allows the user to investigate the topic’s contribution to a document. It may be possible that a topic has a high score for a given topic, but in reality, the words mentioned have little to do with the actual context of the text.

### Document Text:
The middle section shows the text contained within the document. Each word is displayed inside a span tag, this allows us to highlight individual words. The user can hover over the important words picked up by the system as a result of topic modeling. When a topic button is clicked each of the topic’s words in the document is highlighted with the topic’s unique color and the intensity depends on the score of each word.

### Topic Distribution:
Compared to the original paper we have taken a different approach in representing the distribution of topics throughout a document. We split the document into a fixed number of windows. The 30 topic scores are estimated for each of these windows. We generate line graphs for each topic based on these scores. This new approach allows us to compare topic distributions for multiple documents on the same scale and since the size of the y-axis remains the same it makes it easier to study the development of topics as we go deeper in a document.

---

## Rank View 

Rank View provides us with the list of words in the topic and compares the ranks of those words on different topics. Input words can be assigned with colors and these encoded colors are shown on a horizontal line which is placed on the topic bars. We can add multiple words through text input and bars of topics will be rearranged in the ascending order by the rank of those words in the topics. Ascending nature of ranking helps us to analyze and compare the entered words. Words can be removed from the list by clicking on the cross button in front of the word. High salient words are shown by small horizontal lines at the top of the bars. The length of the bar represents the total number of words on the topic. 

<img src="/assets/img/Rank.PNG">
                                                                                                                                   
Our rank view shows equal length for all the bars as the gensim lda model returns equal number of words for all the topics during topic modeling. By clicking on the bar of the topic, we could see the topic distribution of words on the right side of the Rank View

---

### Document Search

This is a new module that we have added to the Serendip system. The original Serendip allows users to explore the topics in a document, the words in a document and even the saliency of a word in a topic. We felt like the next best tool would be to allow a user to find relevant documents by searching for keywords.

<img src="/assets/img/Extension.PNG">

This was a challenge because we need to return all relevant documents which may or may not contain the searched 
terms. We took a vector based similarity scoring approach to solve this problem.  Based on the topic scores for each word we generate a aggregate score vector of size 30, we call this the ‘search vector’. The search vector is visualized with the help of a bubble chart on the right-hand side of the view. It displays the prominence of each topic in the search vector. The larger the topic bubble radius the higher is the likelihood that the author is looking for documents related to that topic.


Similarly, we have a vector generated for each document (i.e. ‘document vectors’) in the corpus as a result of LDA. The document vectors are visualized as horizontal bar graphs for each of the documents returned for the search query. 


We calculate the cosine similarity between the search vector and each of the document vector and documents cards are returned in order of decreasing similarity with the search vector.

---
