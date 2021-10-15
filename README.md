# Arthur Conan Doyle: Text Analysis

Jordan Hiatt – jdh2e

Arthur Conan Doyle is most well known as the author of the novels and short stories of Sherlock Holmes. However, Conan Doyle was a prolific author of many other books, like the adventure stories of Professor Challenger and other historical works. For this project I have pulled in the full literary works of Arthur Conan Doyle housed in Project Gutenberg. Some of Conan Doyle&#39;s works were not listed there, and others were not formatted correctly for my purposes. After eliminating non-English translations, I was left with 61 titles including some song and poetry. I have not read any of Conan Doyle&#39;s other works, but I have read most of the Sherlock Holmes canon. For this project I will explore various exploratory text analytic techniques to try and distinguish between Sherlock Holmes works and others in the corpus. As I go, I will evaluate these techniques, acknowledging limits in my background with the non-Sherlock Holmes portion of the corpus.

## Data Preparation

Using the corpus from Project Gutenberg, I split the data into a TOKEN table using the OHCO model (Ordered Hierarchy of Content Objects) splitting the data into Books, Paragraphs, Sentences and Tokens (words). Chapters would be an obvious choice as well, but as chapter headers have a wide variety of formatting, this would take too much work for all 61 titles.

I then saved each unique token to a VOCAB table, supplementing the table with data on stop word status, most common part of speech in the corpus, stem words, and Zipf rank. Several of these were not used in this project, but with more time they may yet prove useful. From the TOKEN table, I created a TFIDF table and later I added sentiment data to the TOKEN table to get a plot summary via sentiment.

## Principle Component Analysis

My first strategy was to us PCA to isolate distinct themes within the entire corpus and to see if Sherlock Holmes components are distinct from the other components. Here I ended up with mixed success.

L ![](RackMultipart20211015-4-kdy8r6_html_211b262390adcf28.png) ooking at the positive and negative loadings of the first four components gives us a list of words most closely and least closely associated with each component. The first PC is full of Sherlock Holmes related words, including the characters Homes, Watson, Steiner, Martha, and Von Bork. However, these are all characters from &quot;His Last Bow.&quot; Looking at the weighting of each component by book, we see that this first component aligns almost exclusively with &quot;The Last Bow.&quot; The two Professor Challenger novels (&quot;The Lost World,&quot; and &quot;The Poison Belt&quot;) have two PC&#39;s in common, PC1 and PC3. On further examination, none of the other PC&#39;s show a strong differentiation between Sherlock Holmes and Conan Doyle&#39;s other works—though several non-Sherlock Holmes novels do tend to g ![](RackMultipart20211015-4-kdy8r6_html_ce28fc06f2ef0238.png) ravitate toward PC&#39;s. My guess is that this is due to the prevalence of &quot;The Last Bow,&quot; which accounts for most of the Sherlock Holmes related variation, not leaving room for a wider grouping. Either way, it doesn&#39;t appear there is enough stark difference between Conan Doyle&#39;s work for a more dramatic separation in PC&#39;s.

## Word2Vec - Word Similarity

Next, I made a word plot showing similarity of different words, trying to find some kind of grouping that would signify Sherlock Holmes specific words. For this, I tried using the Book level as the bag (as in Bag of Words), the paragraph level as the bag, and the paragraph level as the bag without removing proper nouns.

F ![](RackMultipart20211015-4-kdy8r6_html_fe84e317d180ada1.png) rom this I was able to plot several very interesting charts grouping words by closeness of their vectors. I was able to find groups for time of day, body parts, even weapons (see below), but nothing specific to a series of books as far as I could tell. The plots were working, but it appears that Conan Doyle&#39;s work is too similar to find a difference based on words used. This makes sense if you consider that regardless of character, Conan Doyle&#39;s books are generally about Victorian gentlemen doing dangerous things, much in line with the masculine culture of the time (from what I understand).

## Cluster Analysis – Tree Based Model

Next, I attempted to group books based on similarity in a tree based model. After attempting 8 different measures of similarity, I narrowed it down to trees built using cityblock, yule and js (jensenshannon) methods. ![](RackMultipart20211015-4-kdy8r6_html_25cbccb9f0fc0a50.png)

Yule grouped all the Sherlock Holmes books together on one end (though some other books were mixed in between), and grouped the two Professor Challenger books together, near the Sherlock Holmes books. It also groped some books of songs and poetry together near the opposite side of the tree.

JS groups the short stories of Sherlock Holmes and the longer novels in two separate groups. Again, the Challenger books are grouped together.

Cityblock groups some of the Sherlock Holmes novels together but misses grouping the short stories.

Overall, tree-based clustering was very effective, with the yule method showing the most promise.

## Topic Modeling

Next I grouped sentences into paragraphs, keeping only regular nouns, and used Latent Dirichlet Allocation to trace topics throughout paragraphs. Finally sorting words by importance for each topic, we get some topics described by their most relevant words.

![](RackMultipart20211015-4-kdy8r6_html_2ea93b1cca62e2ad.png)

These topics prove less than helpful. None of these themes are recognizably Sherlock Holmes over Conan Doyle&#39;s other books. In fact, most of these topics are probably recognizable in any of Conan Doyle&#39;s books. Conan Doyle&#39;s work is probably too similar for topic modeling to differentiate between his different works; something less subtle is required.

## Plot Comparison - Syuzhet Model

Finally, I used the R Syuzhet model, rebuilt using python (by Professor Alvarado) to compare plots of a few select books. For this I selected the four Sherlock Holmes novels and the two Challenger novels in our corpus. Viewing the plots it is clear that &quot;A Study in Scarlet,&quot; &quot;The Sign of Four,&quot; and &quot;The Valley of Fear&quot; all have the same general plot shape, while the two Challenger novels have unique shapes. Of course, plot mapping has issues with reliability and while interesting, these results are far from conclusive.

![](RackMultipart20211015-4-kdy8r6_html_3b409b7329429bdd.png)

## Conclusion

Arthur Conan Doyle was a prolific author with a wide range of works spanning many genres. However, most of these works share a similar style making most word frequency based techniques difficult.

However, tree-based models, relying on TFIDF, are surprisingly effective using certain methods. If our goal is to predict whether a book by Conan Doyle is a story about Sherlock Holmes, then a tree-based method (like the yule model) is probably the way to go. As far as getting further insight into the difference between his Sherlock Holmes novels and his other works, perhaps it is safe to conclude that his work is pretty homogeneous regardless of character, at least as far as computer-based algorithms are concerned. There are areas to investigate further: perhaps PCA is a good candidate (it definitely pulled &quot;The Last Bow&quot; away from the rest), and plot modeling with Syuzhet may be worth looking into.
