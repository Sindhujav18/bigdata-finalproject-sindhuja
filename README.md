# bigdata-finalproject-sindhuja

## Author:
- Sindhuja Valeti.

## Text Data
- Text Data Source: [The Conchologist's First Book](https://www.gutenberg.org/files/65171/65171-0.txt)

## Tools and Languages:
- The language used in this project is Python
- The tools used in this project is 
  1. Regex
  2. Pyspark
  3. Urllib
  4. Databricks Notebook
  5. Pandas
  6. MatPlotLib
   

## Procedure involved:
## Data Gathering:
**urllib** - It is used to open URL
1.The urllib.request library is used to request data from the text data's url or to pull data from it. The data is saved in a temporary file called 'sindhuja.txt' and will get the text data from the Gutenberg.org site 'The Conchologist's First Book'
```
# Read the data from the URL and print it
import urllib.request
# Open a connection URL using urllib
urllib.request.urlretrieve("https://www.gutenberg.org/files/65171/65171-0.txt" , "/tmp/sindhuja.txt")
```
2.The information has been saved. Dbutils is a tool for working with filesystems. dbutils.fs.mv is a program that transfers temporary data to a new site called data.
— dbutils.fsmv(from: String, to: String, recurse: boolean = false): boolean - boolean - boolean - boolean - boolean - boolean - boolean - boolean - boole This command transfers a file or directory between FileSystems.
```
dbutils.fs.mv("file:/tmp/sindhuja.txt","dbfs:/data/sindhuja.txt")
```
3.The elements can run and work on multiple nodes to do parallel processing on a cluster, as shown below, to transfer the data file into Spark using sc.textfile into sindhujaRDD(Resilient distributed Systems).
```
sindhujaRDD = sc.textFile("dbfs:/data/sindhuja.txt")
```
## Data Cleaning

4. We must split each line by its spaces, convert capitalized to lower case, filter out empty lines, and break sentences into terms.
```
# flatmap each line to words
my_words_RDD=sindhujaRDD.flatMap(lambda line : line.lower().strip().split(" "))
```
5.All punctuation has been removed. Regular expressions are employed. It's done with the aid of the library re. It's used to look at things that aren't letters.
```
import re
clean_Tokens_RDD = my_words_RDD.map(lambda w: re.sub(r'[^a-zA-Z]','',w))
```
6.The stopwords must be removed. PySpark is also aware of the stopwords. StopWordsRemover is a pyspark library that we need to import. Filter the words from the library after they've been imported.
```
from pyspark.ml.feature import StopWordsRemover
remover =StopWordsRemover()
stopwords = remover.getStopWords()
clean_word_RDD=clean_Tokens_RDD.filter(lambda w: w not in stopwords)
```
## Data Processing

7. After cleaning the data, the next step is to process it. Our terms will be converted into intermediate key-value pairs. For each word element in the RDD, we'll make a pair consisting of ('word>', 1) We can make a pair RDD by combining the map() transformation with the lambda() function.
Once we map it, it will look like this: (word,1).

```
IKV_Pairs_RDD= clean_word_RDD.map(lambda word: (word,1))
```
8. We'll perform the Reduce by key operation in this step. The word is the answer. We'll keep track of the first word count each time. If the word appears again, the most recent one will be removed and the first word count will be kept.
```
word_Count_RDD = IKV_Pairs_RDD.reduceByKey(lambda acc, value: acc+value)
```
9.We will retrieve the elements in this stage. The collect() action function is used to return to the driver program all elements from the dataset(RDD/DataFrame/Dataset) as an Array(row).
```
sindhujaresults = word_Count_RDD.collect()
```

- Sorting a list of tuples by the second value, which will be used to reorder the list's tuples. Tuples' second values are mentioned in ascending order. The top 10 words are shown in print below.

```
sindhujaresults = word_Count_RDD.map(lambda x: (x[1], x[0])).sortByKey(False).take(10)
print(sindhujaresults)
```
- The library mathplotlib will be used to graph the results. By plotting the x and y axes, we can show any form of graph.
```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from collections import Counter

source = 'The Conchologist s First Book'
title = 'Top 10 Words in ' + source
xlabel = 'Count'
ylabel = 'Words'

# create Pandas dataframe from list of tuples
df = pd.DataFrame.from_records(sindhujaresults, columns =[xlabel, ylabel]) 
print(df)

# create plot (using matplotlib)
plt.figure(figsize=(15,5))
sns.barplot(xlabel, ylabel, data=df, palette="rocket").set_title(title)
```
# Charting Results
![]()
![]()

# References
- [guru99](https://www.guru99.com/pyspark-tutorial.html)
- [Databricks Wikipedia](https://en.wikipedia.org/wiki/Databricks)
