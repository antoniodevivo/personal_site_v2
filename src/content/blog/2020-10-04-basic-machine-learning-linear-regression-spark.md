---
title: 'A basic machine learning linear regression model with Spark (pyspark)'
description: 'Beginner level'
icon: "2"
pubDate: 'Oct 04 2020'
heroImage: "/src/assets/blog-1.jpg"
---

In this post will see how make a very basic linear regression algorythm.
<br><br>

## Part 1: Read Dataset

Let’s start to inizialize a **spark** session:


``` python
spark = SparkSession.builder.appName('linear_regression').getOrCreate()
```

and load our csv file:

``` python
df = spark.read.csv("iris.csv", inferSchema=True, header=True)
```


You must know that:

- **inferSchema**: for determine automatically columns data types;
- **header**: for indicate to spark that the first line contains the name of the columns.

Take a look to our dataframe:

``` python
df.show()
+---+------------+-----------+------------+-----------+-------+
|_c0|Sepal_Length|Sepal_Width|Petal_Length|Petal_Width|Species|
+---+------------+-----------+------------+-----------+-------+
|  1|         5.1|        3.5|         1.4|        0.2| setosa|
|  2|         4.9|        3.0|         1.4|        0.2| setosa|
|  3|         4.7|        3.2|         1.3|        0.2| setosa|
|  4|         4.6|        3.1|         1.5|        0.2| setosa|
|  5|         5.0|        3.6|         1.4|        0.2| setosa|
|  6|         5.4|        3.9|         1.7|        0.4| setosa|
|  7|         4.6|        3.4|         1.4|        0.3| setosa|
|  8|         5.0|        3.4|         1.5|        0.2| setosa|
|  9|         4.4|        2.9|         1.4|        0.2| setosa|
| 10|         4.9|        3.1|         1.5|        0.1| setosa|
| 11|         5.4|        3.7|         1.5|        0.2| setosa|
| 12|         4.8|        3.4|         1.6|        0.2| setosa|
| 13|         4.8|        3.0|         1.4|        0.1| setosa|
| 14|         4.3|        3.0|         1.1|        0.1| setosa|
| 15|         5.8|        4.0|         1.2|        0.2| setosa|
| 16|         5.7|        4.4|         1.5|        0.4| setosa|
| 17|         5.4|        3.9|         1.3|        0.4| setosa|
| 18|         5.1|        3.5|         1.4|        0.3| setosa|
| 19|         5.7|        3.8|         1.7|        0.3| setosa|
| 20|         5.1|        3.8|         1.5|        0.3| setosa|
+---+------------+-----------+------------+-----------+-------+
```

We’ll train our algorythm for a regression problem: predict the Petal_Width of new datas.

For do that, we must combine all the columns that we’ll use for prediction in one column. These columns are called features.
<br><br>

## Part 2: Create the cake pan

Follow these steps:

Import libraries for assemble our features column:

``` python
from pyspark.ml.linalg import Vectors
from pyspark.ml.feature import VectorAssembler
```

Let’s define our assembler (like a cake pan)

``` python
assembler = VectorAssembler(
    inputCols=["Sepal_Length", "Sepal_Width", "Petal_Length"],
    outputCol="features")
```

and create our new dataset with the new column “features”:

``` python
transform = assembler.transform(df)
```

Take a look to the new dataframe:

``` python
transform.show()
+---+------------+-----------+------------+-----------+-------+-------------+
|_c0|Sepal_Length|Sepal_Width|Petal_Length|Petal_Width|Species|     features|
+---+------------+-----------+------------+-----------+-------+-------------+
|  1|         5.1|        3.5|         1.4|        0.2| setosa|[5.1,3.5,1.4]|
|  2|         4.9|        3.0|         1.4|        0.2| setosa|[4.9,3.0,1.4]|
|  3|         4.7|        3.2|         1.3|        0.2| setosa|[4.7,3.2,1.3]|
|  4|         4.6|        3.1|         1.5|        0.2| setosa|[4.6,3.1,1.5]|
|  5|         5.0|        3.6|         1.4|        0.2| setosa|[5.0,3.6,1.4]|
|  6|         5.4|        3.9|         1.7|        0.4| setosa|[5.4,3.9,1.7]|
|  7|         4.6|        3.4|         1.4|        0.3| setosa|[4.6,3.4,1.4]|
|  8|         5.0|        3.4|         1.5|        0.2| setosa|[5.0,3.4,1.5]|
|  9|         4.4|        2.9|         1.4|        0.2| setosa|[4.4,2.9,1.4]|
| 10|         4.9|        3.1|         1.5|        0.1| setosa|[4.9,3.1,1.5]|
| 11|         5.4|        3.7|         1.5|        0.2| setosa|[5.4,3.7,1.5]|
| 12|         4.8|        3.4|         1.6|        0.2| setosa|[4.8,3.4,1.6]|
| 13|         4.8|        3.0|         1.4|        0.1| setosa|[4.8,3.0,1.4]|
| 14|         4.3|        3.0|         1.1|        0.1| setosa|[4.3,3.0,1.1]|
| 15|         5.8|        4.0|         1.2|        0.2| setosa|[5.8,4.0,1.2]|
| 16|         5.7|        4.4|         1.5|        0.4| setosa|[5.7,4.4,1.5]|
| 17|         5.4|        3.9|         1.3|        0.4| setosa|[5.4,3.9,1.3]|
| 18|         5.1|        3.5|         1.4|        0.3| setosa|[5.1,3.5,1.4]|
| 19|         5.7|        3.8|         1.7|        0.3| setosa|[5.7,3.8,1.7]|
| 20|         5.1|        3.8|         1.5|        0.3| setosa|[5.1,3.8,1.5]|
+---+------------+-----------+------------+-----------+-------+-------------+
only showing top 20 rows
```

We can notice that the new column is there.
<br><br>

## Part 3: Extract our train and test dataframes

How already said, we have to predict the Petal_Width variable for new datasets.

For do that, Spark admit only two variable for train the algorythm: the features and the variable to predict

So, let’s create our simplied dataframe with these two columns:

``` python
transformed_df = transform.select('features','Petal_Width')
transformed_df.show()
+-------------+-----------+
|     features|Petal_Width|
+-------------+-----------+
|[5.1,3.5,1.4]|        0.2|
|[4.9,3.0,1.4]|        0.2|
|[4.7,3.2,1.3]|        0.2|
|[4.6,3.1,1.5]|        0.2|
|[5.0,3.6,1.4]|        0.2|
|[5.4,3.9,1.7]|        0.4|
|[4.6,3.4,1.4]|        0.3|
|[5.0,3.4,1.5]|        0.2|
|[4.4,2.9,1.4]|        0.2|
|[4.9,3.1,1.5]|        0.1|
|[5.4,3.7,1.5]|        0.2|
|[4.8,3.4,1.6]|        0.2|
|[4.8,3.0,1.4]|        0.1|
|[4.3,3.0,1.1]|        0.1|
|[5.8,4.0,1.2]|        0.2|
|[5.7,4.4,1.5]|        0.4|
|[5.4,3.9,1.3]|        0.4|
|[5.1,3.5,1.4]|        0.3|
|[5.7,3.8,1.7]|        0.3|
|[5.1,3.8,1.5]|        0.3|
+-------------+-----------+
only showing top 20 rows
```

and define our train and test variables:

``` python
train, test = transformed_df.randomSplit([0.7,0.3])
```

**0.7** and **0.3** stand for **70%** and **30%** respectively

In fact, usually the test data are limited to 20-30% of the original dataset, this to leave the right amount of data for training.

Unbalancing the test and train data too much leads to several disadvantages:

- **with little train data**: you would have low model accuracy
- **with too much train data**: you would have a flawed model and not very suitable for predicting new values
- **with little test data**: you can’t be sure if the model works well
<br><br>

## Part 4: Let’s train our model
Import the class for linear regression

``` python
from pyspark.ml.regression import LinearRegression
```

Create an istance of this class

``` python
lr = LinearRegression(featuresCol='features', labelCol='Petal_Width', 
                      predictionCol='prediction')
```

In our setting, we can find:

- **featuresCol**: as the name tell us, we are indicating the column with all our features
- **labelCol**: the values to predict
- **predictionCol**: the column that will contain the predictions

And finally: let’s train our model!

``` python
lr_model = lr.fit(train)
```
<br>

## Part 5: Let’s do the first prediction

Okay, well done! We trained our model; we yelled a “YES” satisfied with the last line (or at least I did).

Now just do our first prediction on test data:

``` python
test_features = test.select('features')
predictions = lr_model.transform(test_features)
```

Now let’s see the differences between the predicted and original values:

``` python
predictions.show()
+-------------+-------------------+
|     features|         prediction|
+-------------+-------------------+
|[4.4,2.9,1.4]| 0.2165543276475982|
|[4.4,3.0,1.3]| 0.1879534561600471|
|[4.6,3.4,1.4]|0.29685042643249226|
|[4.7,3.2,1.3]|0.17199907241125534|
|[4.7,3.2,1.6]|  0.332200886106345|
|[4.9,2.4,3.3]| 0.9979107324895239|
|[4.9,3.0,1.4]| 0.1320976442188283|
|[4.9,3.6,1.4]| 0.2808960426837003|
|[5.0,2.3,3.3]| 0.9512597161107955|
|[5.0,3.0,1.6]|0.21704757004763853|
|[5.0,3.2,1.2]|0.05304461794247628|
|[5.0,3.4,1.6]|0.31624650235755314|
|[5.0,3.5,1.3]| 0.1808444217399422|
|[5.1,3.3,1.7]| 0.3229960905438546|
|[5.1,3.7,1.5]|0.31539381372370967|
|[5.2,2.7,3.9]| 1.3271597092083902|
|[5.2,3.5,1.5]|0.24394306426750245|
|[5.2,4.1,1.5]| 0.3927414627323743|
|[5.4,3.7,1.5]|0.24983996381996032|
|[5.5,2.3,4.0]|  1.215807531559756|
+-------------+-------------------+
only showing top 20 rows
```

``` python
test.show()
+-------------+-----------+
|     features|Petal_Width|
+-------------+-----------+
|[4.4,2.9,1.4]|        0.2|
|[4.4,3.0,1.3]|        0.2|
|[4.6,3.4,1.4]|        0.3|
|[4.7,3.2,1.3]|        0.2|
|[4.7,3.2,1.6]|        0.2|
|[4.9,2.4,3.3]|        1.0|
|[4.9,3.0,1.4]|        0.2|
|[4.9,3.6,1.4]|        0.1|
|[5.0,2.3,3.3]|        1.0|
|[5.0,3.0,1.6]|        0.2|
|[5.0,3.2,1.2]|        0.2|
|[5.0,3.4,1.6]|        0.4|
|[5.0,3.5,1.3]|        0.3|
|[5.1,3.3,1.7]|        0.5|
|[5.1,3.7,1.5]|        0.4|
|[5.2,2.7,3.9]|        1.4|
|[5.2,3.5,1.5]|        0.2|
|[5.2,4.1,1.5]|        0.1|
|[5.4,3.7,1.5]|        0.2|
|[5.5,2.3,4.0]|        1.3|
+-------------+-----------+
only showing top 20 rows
```

Well! As we can see, the predicted values are very similiar to the original ones. So can we conclude that the model is ok? Not so fast!

When we have little data it is easy to see the differences between the two datasets. But when the amount of data begins to increase, the situation change. In the next part we will see how to get information on the degree of reliability of the model. 
<br><br>

## Part 6: Check the quality of your model

There are several statistical methods for calculating the degree of reliability of a model.

Some of which are:

- **MSE**: the measure of how actually the predicted values are different from the actual values
- **RMSE**: the standard deviation of the residuals (prediction errors)
- **R2 Score**: the proportion of the variance in the dependent variable that is predictable from the independent variables.

The discussion here becomes more complicated, but for now it is enough to know that:

- if **r2** has a **value greater than 0.75**, then we have built a good model

So, let’s find our r2!

``` python
training_summary = lr_model.summary
print("r2: {}".format(training_summary.r2))
r2: 0.9284703394101349
```



As we can see, the value of **r2** is **greater than 0,75**, so the model is **great** for our problem 😀

You will find the complete example here: [https://drive.google.com/file/d/1zZE52jD4dqOdlBPvLDt-gMrbm9LH2K-O/view](https://drive.google.com/file/d/1zZE52jD4dqOdlBPvLDt-gMrbm9LH2K-O/view)

You can open the file with **Google Colaboratory**.

Text me for any issue.

Thank you for reading 🙂
<br><br>

## Insights

<table>
<colgroup>
<col width="20%" />
<col width="80%" />
</colgroup>
<tbody>
<tr>
<th markdown="span">MSE</th>
    <td markdown="span">
        <a href="https://en.wikipedia.org/wiki/Mean_squared_error" target="_blank">Link</a>
    </td>
</tr>
<tr>
<th markdown="span">RMSE</th>
    <td markdown="span">
        <a href="https://en.wikipedia.org/wiki/Root-mean-square_deviation" target="_blank">Link</a>
    </td>
</tr>
<tr>
<th markdown="span">R2 Score</th>
    <td markdown="span">
        <a href="https://en.wikipedia.org/wiki/Coefficient_of_determination" target="_blank">Link</a>
    </td>
</tr>
</tbody>
</table>

<br>