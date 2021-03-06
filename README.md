# 7 Linear Regression
1. [Import libraries](#schema1)
2. [Handling categorical features ](#schema2)
3. [train test split](#schema3)
4. [Final comparison](#schema4)

<hr>

<a name="schema1"></a>

# 1. Import libraries
~~~python
from pyspark.sql import SparkSession
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.feature import StringIndexer
from pyspark.ml.regression import LinearRegression
~~~

<hr>

<a name="schema2"></a>

# 2. Handling categorical features
~~~python

indexer = StringIndexer(inputCols=["smoker","day","time"], outputCols =["smoker_indexed","day_indexed","time_indexed"] )
df_r = indexer.fit(df_r).transform(df_r)
+----------+----+------+------+---+------+----+-----------+--------------+-----------+------------+
|total_bill| tip|   sex|smoker|day|  time|size|sex_indexed|smoker_indexed|day_indexed|time_indexed|
+----------+----+------+------+---+------+----+-----------+--------------+-----------+------------+
|     16.99|1.01|Female|    No|Sun|Dinner|   2|        1.0|           0.0|        1.0|         0.0|
|     10.34|1.66|  Male|    No|Sun|Dinner|   3|        0.0|           0.0|        1.0|         0.0|
|     21.01| 3.5|  Male|    No|Sun|Dinner|   3|        0.0|           0.0|        1.0|         0.0|
|     23.68|3.31|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     24.59|3.61|Female|    No|Sun|Dinner|   4|        1.0|           0.0|        1.0|         0.0|
|     25.29|4.71|  Male|    No|Sun|Dinner|   4|        0.0|           0.0|        1.0|         0.0|
|      8.77| 2.0|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     26.88|3.12|  Male|    No|Sun|Dinner|   4|        0.0|           0.0|        1.0|         0.0|
|     15.04|1.96|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     14.78|3.23|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     10.27|1.71|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     35.26| 5.0|Female|    No|Sun|Dinner|   4|        1.0|           0.0|        1.0|         0.0|
|     15.42|1.57|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     18.43| 3.0|  Male|    No|Sun|Dinner|   4|        0.0|           0.0|        1.0|         0.0|
|     14.83|3.02|Female|    No|Sun|Dinner|   2|        1.0|           0.0|        1.0|         0.0|
|     21.58|3.92|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|
|     10.33|1.67|Female|    No|Sun|Dinner|   3|        1.0|           0.0|        1.0|         0.0|
|     16.29|3.71|  Male|    No|Sun|Dinner|   3|        0.0|           0.0|        1.0|         0.0|
|     16.97| 3.5|Female|    No|Sun|Dinner|   3|        1.0|           0.0|        1.0|         0.0|
|     20.65|3.35|  Male|    No|Sat|Dinner|   3|        0.0|           0.0|        0.0|         0.0|
+----------+----+------+------+---+------+----+-----------+--------------+-----------+------------+
~~~
# 2. VectorAssembler
~~~python
featureassembler=VectorAssembler(inputCols=['tip','size','sex_indexed','smoker_indexed','day_indexed',
                          'time_index'],outputCol="Independent Features")
output=featureassembler.transform(df_r)
+----------+----+------+------+---+------+----+-----------+--------------+-----------+------------+--------------------+
|total_bill| tip|   sex|smoker|day|  time|size|sex_indexed|smoker_indexed|day_indexed|time_indexed|Independent Features|
+----------+----+------+------+---+------+----+-----------+--------------+-----------+------------+--------------------+
|     16.99|1.01|Female|    No|Sun|Dinner|   2|        1.0|           0.0|        1.0|         0.0|[1.01,2.0,1.0,0.0...|
|     10.34|1.66|  Male|    No|Sun|Dinner|   3|        0.0|           0.0|        1.0|         0.0|[1.66,3.0,0.0,0.0...|
|     21.01| 3.5|  Male|    No|Sun|Dinner|   3|        0.0|           0.0|        1.0|         0.0|[3.5,3.0,0.0,0.0,...|
|     23.68|3.31|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|[3.31,2.0,0.0,0.0...|
|     24.59|3.61|Female|    No|Sun|Dinner|   4|        1.0|           0.0|        1.0|         0.0|[3.61,4.0,1.0,0.0...|
|     25.29|4.71|  Male|    No|Sun|Dinner|   4|        0.0|           0.0|        1.0|         0.0|[4.71,4.0,0.0,0.0...|
|      8.77| 2.0|  Male|    No|Sun|Dinner|   2|        0.0|           0.0|        1.0|         0.0|[2.0,2.0,0.0,0.0,...|

output.select("Independent Features").show()
+--------------------+
|Independent Features|
+--------------------+
|[1.01,2.0,1.0,0.0...|
|[1.66,3.0,0.0,0.0...|
|[3.5,3.0,0.0,0.0,...|
|[3.31,2.0,0.0,0.0...|
|[3.61,4.0,1.0,0.0...|
|[4.71,4.0,0.0,0.0...|
|[2.0,2.0,0.0,0.0,...|
|[3.12,4.0,0.0,0.0...|
|[1.96,2.0,0.0,0.0...|
|[3.23,2.0,0.0,0.0...|
|[1.71,2.0,0.0,0.0...|
|[5.0,4.0,1.0,0.0,...|
|[1.57,2.0,0.0,0.0...|
|[3.0,4.0,0.0,0.0,...|
|[3.02,2.0,1.0,0.0...|
|[3.92,2.0,0.0,0.0...|
|[1.67,3.0,1.0,0.0...|
|[3.71,3.0,0.0,0.0...|
|[3.5,3.0,1.0,0.0,...|
|(6,[0,1],[3.35,3.0])|
+--------------------+
~~~
~~~python
finalized_data = output.select("Independent Features", "total_bill")
+--------------------+----------+
|Independent Features|total_bill|
+--------------------+----------+
|[1.01,2.0,1.0,0.0...|     16.99|
|[1.66,3.0,0.0,0.0...|     10.34|
|[3.5,3.0,0.0,0.0,...|     21.01|
|[3.31,2.0,0.0,0.0...|     23.68|
|[3.61,4.0,1.0,0.0...|     24.59|
|[4.71,4.0,0.0,0.0...|     25.29|
|[2.0,2.0,0.0,0.0,...|      8.77|
|[3.12,4.0,0.0,0.0...|     26.88|
|[1.96,2.0,0.0,0.0...|     15.04|
|[3.23,2.0,0.0,0.0...|     14.78|
|[1.71,2.0,0.0,0.0...|     10.27|
|[5.0,4.0,1.0,0.0,...|     35.26|
|[1.57,2.0,0.0,0.0...|     15.42|
|[3.0,4.0,0.0,0.0,...|     18.43|
|[3.02,2.0,1.0,0.0...|     14.83|
|[3.92,2.0,0.0,0.0...|     21.58|
|[1.67,3.0,1.0,0.0...|     10.33|
|[3.71,3.0,0.0,0.0...|     16.29|
|[3.5,3.0,1.0,0.0,...|     16.97|
|(6,[0,1],[3.35,3.0])|     20.65|
+--------------------+----------+
~~~

<hr>

<a name="schema3"></a>

# 3. train test split
~~~python
train_data,test_data=finalized_data.randomSplit([0.75,0.25])
regressor=LinearRegression(featuresCol='Independent Features', labelCol='total_bill')
regressor=regressor.fit(train_data)
regressor.coefficients

DenseVector([2.9552, 3.6237, -1.1196, 2.8078, -0.476, -0.5397])
~~~
<hr>

<a name="schema4"></a>

# 4. Final comparison
~~~python
pred_resuls = regressor.evaluate(test_data)

pred_resuls.predictions.show()
+--------------------+----------+------------------+
|Independent Features|total_bill|        prediction|
+--------------------+----------+------------------+
|(6,[0,1],[1.45,2.0])|      9.55|13.587720329611756|
|(6,[0,1],[1.97,2.0])|     12.02|15.124447603068203|
| (6,[0,1],[2.0,3.0])|     16.31| 18.83679834696462|
|(6,[0,1],[3.15,3.0])|     20.08|22.235329817108685|
|(6,[0,1],[3.39,2.0])|     11.61|19.320895157506964|
|(6,[0,1],[6.73,4.0])|     48.27|   36.438799447102|
|(6,[0,1],[7.58,4.0])|     39.42| 38.95075749025196|
| (6,[0,1],[9.0,4.0])|     48.33|43.147205044690715|
|[1.0,2.0,0.0,1.0,...|      12.6|15.065687183807816|
|[1.1,2.0,1.0,1.0,...|      12.9| 14.24161595455826|
|[1.44,2.0,0.0,1.0...|      7.74|  16.3659948767325|
|[1.5,2.0,0.0,0.0,...|     12.46|12.307487415715084|
|[1.57,2.0,0.0,0.0...|     15.42|13.466351316499724|
|[1.61,2.0,1.0,1.0...|     10.59|15.748790780448237|
|[1.68,2.0,1.0,0.0...|     13.42|11.656098828033159|
|[1.71,2.0,0.0,0.0...|     10.27|13.880085582430306|
|[2.0,2.0,0.0,0.0,...|      7.51| 13.72137285507446|
|[2.0,2.0,0.0,0.0,...|     13.03| 13.72137285507446|
|[2.0,2.0,0.0,0.0,...|     15.95| 13.72137285507446|
|[2.0,2.0,0.0,1.0,...|     10.34|16.529199849761675|
+--------------------+----------+------------------+

pred_resuls.r2, pred_resuls.meanAbsoluteError, pred_resuls.meanSquaredError
(0.6686972431399111, 3.863898607197531, 26.535167687179538)
~~~