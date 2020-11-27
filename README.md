# Toolbox Spark using Databricks Community

The principal idea of this repo is to involve you in a quick introduction to Spark, Spark MLLib, and Spark Streaming.

## Steps:

## Create a Databricks Community account:

1. Create an account in [Databricks Community](https://community.cloud.databricks.com/login.html) (student case).
2. Login in your account.
3. Enjoy the moment! 😉

## Create a Cluster in Databricks:

1. *Go to Cluster section*

    ![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Sin_ttulo.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Sin_ttulo.png)

2. *Click on create cluster button*

![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled.png)

*3. To do it easy write a name in the input and click create the cluster*

![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%201.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%201.png)

**Note:** It is normal that takes some minutes to create all.

## Upload data into the clusters

1. *Go to Data section*

    ![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Sin_ttulo%201.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Sin_ttulo%201.png)

2. *Click on Add Data*

![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%202.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%202.png)

*3. In this case we will use the option to use a .csv file so feel free to use other options. So, click or drop the .csv file into the files box.*

*And click on "Create Table with UI", but you can upload it from Notebook too.*

![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%203.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%203.png)

4. *Select your cluster and click on "Preview Table".*

4.1 *I recommend you to change the name(if you need) and click on Infer schema check to Spark infer your column types.*

4.2 *Finally, click on Create Table*

![Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%204.png](Toolbox%20Spark%20using%20Databricks%20Community%20e691cadfec2b496c842ca2ec60b03166/Untitled%204.png)

4.3 To use your dataset in a notebook write the next code:

```python
data = spark.sql("SELECT * FROM seed_dataset")
```

### Note:

- *To taste the streaming data in Spark follow the README in the file called* ***"Streaming"***

### 🤝🏻 Connect with Me