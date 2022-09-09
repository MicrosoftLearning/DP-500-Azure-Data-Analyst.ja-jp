---
lab:
  title: Spark を使用してデータ レイク内のデータを分析する
  module: 'Model, query, and explore data in Azure Synapse'
---

# <a name="analyze-data-in-a-data-lake-with-spark"></a>Spark を使用してデータ レイク内のデータを分析する

Apache Spark is an open source engine for distributed data processing, and is widely used to explore, process, and analyze huge volumes of data in data lake storage. Spark is available as a processing option in many data platform products, including Azure HDInsight, Azure Databricks, and Azure Synapse Analytics on the Microsoft Azure cloud platform. One of the benefits of Spark is support for a wide range of programming languages, including Java, Scala, Python, and SQL; making Spark a very flexible solution for data processing workloads including data cleansing and manipulation, statistical analysis and machine learning, and data analytics and visualization.

このラボは完了するまで、約 **45** 分かかります。

## <a name="before-you-start"></a>開始する前に

管理レベルのアクセス権を持つ [Azure サブスクリプション](https://azure.microsoft.com/free)が必要です。

## <a name="provision-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースをプロビジョニングする

データ レイク ストレージへのアクセス権を持つ Azure Synapse Analytics ワークスペースと、データ レイク内のファイルのクエリ実行と処理に使用できる Apache Spark プールが必要です。

この演習では、Azure Synapse Analytics ワークスペースをプロビジョニングするために、PowerShell スクリプトと ARM テンプレートを組み合わせて使用します。

1. [Azure portal](https://portal.azure.com) (`https://portal.azure.com`) にサインインします。
2. Use the <bpt id="p1">**</bpt>[<ph id="ph1">\&gt;</ph>_]<ept id="p1">**</ept> button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a <bpt id="p2">***</bpt>PowerShell<ept id="p2">***</ept> environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal と Cloud Shell のペイン](../images/cloud-shell.png)

    > **注**: *Bash* 環境が使用されているクラウド シェルを前に作成していた場合は、クラウド シェルのペインの左上にあるドロップダウン メニューを使用して、***PowerShell*** に変更します。

3. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the <bpt id="p1">**</bpt>&amp;#8212;<ept id="p1">**</ept>, <bpt id="p2">**</bpt>&amp;#9723;<ept id="p2">**</ept>, and <bpt id="p3">**</bpt>X<ept id="p3">**</ept> icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the <bpt id="p1">[</bpt>Azure Cloud Shell documentation<ept id="p1">](https://docs.microsoft.com/azure/cloud-shell/overview)</ept>.

4. PowerShell のペインで、次のコマンドを入力して、リポジトリを複製します。

    ```
    rm -r dp500 -f
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst dp500
    ```

5. リポジトリが複製されたら、次のコマンドを入力してこのラボ用のフォルダーに変更し、そこに含まれている **setup.ps1** スクリプトを実行します。

    ```
    cd dp500/Allfiles/02
    ./setup.ps1
    ```

6. メッセージが表示された場合は、使用するサブスクリプションを選択します (これは、複数の Azure サブスクリプションへのアクセス権を持っている場合にのみ行います)。
7. メッセージが表示されたら、Azure Synapse SQL プールに設定する適切なパスワードを入力します。

    > **注**: このパスワードは忘れないようにしてください。

8. Apache Spark は、分散データ処理を行うためのオープン ソース エンジンであり、データ レイク ストレージ内の膨大な量のデータを探索、処理、分析するために広く使用されています。

## <a name="query-data-in-files"></a>ファイル内のデータのクエリを実行する

このスクリプトを使用すると、Azure Synapse Analytics ワークスペースと、データ レイクをホストする Azure Storage アカウントがプロビジョニングされ、いくつかのデータ ファイルがデータ レイクにアップロードされます。

### <a name="view-files-in-the-data-lake"></a>データ レイク内のファイルを表示する

1. スクリプトが完了したら、Azure portal で、作成された **dp500-*xxxxxxx*** リソース グループに移動して、Synapse ワークスペースを選択します。
2. Synapse ワークスペースの **[概要]** ページの **[Synapse Studio を開く]** カードで **[開く]** を選択し、新しいブラウザー タブで Synapse Studio を開きます。メッセージが表示された場合はサインインします。
3. Synapse Studio の左側にある **&rsaquo;&rsaquo;** アイコンを使用してメニューを展開します。これにより、リソースの管理とデータ分析タスクの実行に使用するさまざまなページが Synapse Studio 内に表示されます。
4. Spark は、Azure HDInsight、Azure Databricks、Microsoft Azure クラウド プラットフォームの Azure Synapse Analytics など、多くのデータ プラットフォーム製品で処理オプションとして使用することができます。
5. **[データ]** ページで **[リンク]** タブを表示して、**synapse*xxxxxxx* (Primary - datalake*xxxxxxx*)** のような名前の Azure Data Lake Storage Gen2 ストレージ アカウントへのリンクがワークスペースに含まれていることを確認します。
6. ストレージ アカウントを展開して、そこに **files** という名前のファイル システム コンテナーが含まれていることを確認します。
7. Java、Scala、Python、SQL など、幅広いプログラミング言語に対応していることが Spark の利点の 1 つであり、これにより Spark は、データ クレンジングと操作、統計分析と機械学習、データ分析と視覚化など、データ処理ワークロードのソリューションとして高い柔軟性を実現しています。
8. **sales** フォルダーと **orders** フォルダーを開き、3 年間の売上データの .csv ファイルが **orders** フォルダーに含まれていることを確認します。
9. Right-click any of the files and select <bpt id="p1">**</bpt>Preview<ept id="p1">**</ept> to see the data it contains. Note that the files do not contain a header row, so you can unselect the option to display column headers.

### <a name="use-spark-to-explore-data"></a>Spark を使用してデータを探索する

1. Select any of the files in the <bpt id="p1">**</bpt>orders<ept id="p1">**</ept> folder, and then in the <bpt id="p2">**</bpt>New notebook<ept id="p2">**</ept> list on the toolbar, select <bpt id="p3">**</bpt>Load to DataFrame<ept id="p3">**</ept>. A dataframe is a structure in Spark that represents a tabular dataset.
2. In the new <bpt id="p1">**</bpt>Notebook 1<ept id="p1">**</ept> tab that opens, in the <bpt id="p2">**</bpt>Attach to<ept id="p2">**</ept> list, select your Spark pool (*<bpt id="p3">*</bpt>spark<ept id="p3">*</ept>xxxxxxx***). Then use the <bpt id="p1">**</bpt>&amp;#9655; Run all<ept id="p1">**</ept> button to run all of the cells in the notebook (there's currently only one!).

    Since this is the first time you've run any Spark code in this session, the Spark pool must be started. This means that the first run in the session can take a few minutes. Subsequent runs will be quicker.

3. Spark セッションによる初期化を待っている間、生成されたコードを確認します。このコードは次のようになります。

    ```Python
    %%pyspark
    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/sales/orders/2019.csv', format='csv'
    ## If header exists uncomment line below
    ##, header=True
    )
    display(df.limit(10))
    ```

4. When the code has finished running, review the output beneath the cell in the notebook. It shows the first ten rows in the file you selected, with automatic column names in the form <bpt id="p1">**</bpt>_c0<ept id="p1">**</ept>, <bpt id="p2">**</bpt>_c1<ept id="p2">**</ept>, <bpt id="p3">**</bpt>_c2<ept id="p3">**</ept>, and so on.
5. Modify the code so that the <bpt id="p1">**</bpt>spark.read.load<ept id="p1">**</ept> function reads data from <bpt id="p2">&lt;u&gt;</bpt>all<ept id="p2">&lt;/u&gt;</ept> of the CSV files in the folder, and the <bpt id="p3">**</bpt>display<ept id="p3">**</ept> function shows the first 100 rows. Your code should look like this (with <bpt id="p1">*</bpt>datalakexxxxxxx<ept id="p1">*</ept> matching the name of your data lake store):

    ```Python
    %%pyspark
    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/sales/orders/*.csv', format='csv'
    )
    display(df.limit(100))
    ```

6. コード セルの左側にある **&#9655;** ボタンを使用して、そのセルのみを実行し、結果を確認します。

    The dataframe now includes data from all of the files, but the column names are not useful. Spark uses a "schema-on-read" approach to try to determine appropriate data types for the columns based on the data they contain, and if a header row is present in a text file it can be used to identify the column names (by specifying a <bpt id="p1">**</bpt>header=True<ept id="p1">**</ept> parameter in the <bpt id="p2">**</bpt>load<ept id="p2">**</ept> function). Alternatively, you can define an explicit schema for the dataframe.

7. Modify the code as follows (replacing <bpt id="p1">*</bpt>datalakexxxxxxx<ept id="p1">*</ept>), to define an explicit schema for the dataframe that includes the column names and data types. Rerun the code in the cell.

    ```Python
    %%pyspark
    from pyspark.sql.types import *
    from pyspark.sql.functions import *

    orderSchema = StructType([
        StructField("SalesOrderNumber", StringType()),
        StructField("SalesOrderLineNumber", IntegerType()),
        StructField("OrderDate", DateType()),
        StructField("CustomerName", StringType()),
        StructField("Email", StringType()),
        StructField("Item", StringType()),
        StructField("Quantity", IntegerType()),
        StructField("UnitPrice", FloatType()),
        StructField("Tax", FloatType())
        ])

    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/sales/orders/*.csv', format='csv', schema=orderSchema)
    display(df.limit(100))
    ```

8. ページ上部の検索バーの右側にある **[\>_]** ボタンを使用して、Azure portal に新しい Cloud Shell を作成します。メッセージが表示された場合は、***PowerShell*** 環境を選択して、ストレージを作成します。

    ```Python
    df.printSchema()
    ```

9. 次に示すように、Azure portal の下部にあるペインに、Cloud Shell のコマンド ライン インターフェイスが表示されます。

## <a name="analyze-data-in-a-dataframe"></a>データフレーム内のデータを分析する

Spark の **dataframe** オブジェクトは、Python の Pandas データフレームに似ています。このオブジェクトには、そこに存在するデータの操作、フィルター処理、グループ化、または分析に使用できるさまざまな関数が含まれています。

### <a name="filter-a-dataframe"></a>データフレームをフィルター処理する

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    customers = df['CustomerName', 'Email']
    print(customers.count())
    print(customers.distinct().count())
    display(customers.distinct())
    ```

2. Run the new code cell, and review the results. Observe the following details:
    - データフレームに対して操作を実行すると、その結果として新しいデータフレームが作成されます (この場合、**df** データフレームから列の特定のサブセットを選択することで、新しい **customers** データフレームが作成されます)
    - データフレームには、そこに含まれているデータの集計やフィルター処理に使用できる **count** や **distinct** などの関数が用意されています。
    - The <ph id="ph1">`dataframe['Field1', 'Field2', ...]`</ph> syntax is a shorthand way of defining a subset of column. You can also use <bpt id="p1">**</bpt>select<ept id="p1">**</ept> method, so the first line of the code above could be written as <ph id="ph1">`customers = df.select("CustomerName", "Email")`</ph>

3. コードを次のように変更します。

    ```Python
    customers = df.select("CustomerName", "Email").where(df['Item']=='Road-250 Red, 52')
    print(customers.count())
    print(customers.distinct().count())
    display(customers.distinct())
    ```

4. Run the modified code to view the customers who have purchased the <bpt id="p1">*</bpt>Road-250 Red, 52<ept id="p1">*</ept> product. Note that you can "chain" multiple functions together so that the output of one function becomes the input for the next - in this case, the dataframe created by the <bpt id="p1">**</bpt>select<ept id="p1">**</ept> method is the source dataframe for the <bpt id="p2">**</bpt>where<ept id="p2">**</ept> method that is used to apply filtering criteria.

### <a name="aggregate-and-group-data-in-a-dataframe"></a>データフレーム内のデータを集計してグループ化する

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    productSales = df.select("Item", "Quantity").groupBy("Item").sum()
    display(productSales)
    ```

2. ペインの上部にある区分線をドラッグして Cloud Shell のサイズを変更したり、ペインの右上にある **&#8212;** 、 **&#9723;** 、**X** アイコンを使用して、ペインを最小化または最大化したり、閉じたりすることができます。

3. ノートブックに新しいコード セルをもう 1 つ追加し、そこに次のコードを入力します。

    ```Python
    yearlySales = df.select(year("OrderDate").alias("Year")).groupBy("Year").count().orderBy("Year")
    display(yearlySales)
    ```

4. Azure Cloud Shell の使い方について詳しくは、[Azure Cloud Shell のドキュメント](https://docs.microsoft.com/azure/cloud-shell/overview)をご覧ください。

## <a name="query-data-using-spark-sql"></a>Spark SQL を使用してデータのクエリを実行する

As you've seen, the native methods of the dataframe object enable you to query and analyze data quite effectively. However, many data analysts are more comfortable working with SQL syntax. Spark SQL is a SQL language API in Spark that you can use to run SQL statements, or even persist data in relational tables.

### <a name="use-spark-sql-in-pyspark-code"></a>PySpark コードで Spark SQL を使用する

The default language in Azure Synapse Studio notebooks is PySpark, which is a Spark-based Python runtime. Within this runtime, you can use the <bpt id="p1">**</bpt>spark.sql<ept id="p1">**</ept> library to embed Spark SQL syntax within your Python code, and work with SQL constructs such as tables and views.

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    df.createOrReplaceTempView("salesorders")

    spark_df = spark.sql("SELECT * FROM salesorders")
    display(spark_df)
    ```

2. Run the cell and review the results. Observe that:
    - The code persists the data in the <bpt id="p1">**</bpt>df<ept id="p1">**</ept> dataframe as a temporary view named <bpt id="p2">**</bpt>salesorders<ept id="p2">**</ept>. Spark SQL supports the use of temporary views or persisted tables as sources for SQL queries.
    - 次に、**spark.sql** メソッドを使用して、**salesorders** ビューに対して SQL クエリが実行されます。
    - クエリの結果は、データフレームに格納されます。

### <a name="run-sql-code-in-a-cell"></a>SQL コードをセル内で実行する

PySpark コードが含まれているセルに SQL ステートメントを埋め込むことができるのは便利ですが、データ アナリストにとっては、SQL で直接作業できればよいという場合も多くあります。

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```sql
    %%sql
    SELECT YEAR(OrderDate) AS OrderYear,
           SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue
    FROM salesorders
    GROUP BY YEAR(OrderDate)
    ORDER BY OrderYear;
    ```

2. Run the cell and review the results. Observe that:
    - セルの先頭にある `%%sql` 行 (*magic* と呼ばれます) は、このセル内でこのコードを実行するには、PySpark ではなく、Spark SQL 言語ランタイムを使用する必要があることを示しています。
    - SQL コードを使用すると、前に PySpark を使用して作成した **salesorder** ビューが参照されます。
    - SQL クエリからの出力は、セルの下に自動的に結果として表示されます。

> **注**: Spark SQL とデータフレームの詳細については、[Spark SQL のドキュメント](https://spark.apache.org/docs/2.2.0/sql-programming-guide.html)を参照してください。

## <a name="visualize-data-with-spark"></a>Spark を使用してデータを視覚化する

A picture is proverbially worth a thousand words, and a chart is often better than a thousand rows of data. While notebooks in Azure Synapse Analytics include a built in chart view for data that is displayed from a dataframe or Spark SQL query, it is not designed for comprehensive charting. However, you can use Python graphics libraries like <bpt id="p1">**</bpt>matplotlib<ept id="p1">**</ept> and <bpt id="p2">**</bpt>seaborn<ept id="p2">**</ept> to create charts from data in dataframes.

### <a name="view-results-as-a-chart"></a>結果をグラフで表示する

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```sql
    %%sql
    SELECT * FROM salesorders
    ```

2. コードを実行し、前に作成した **salesorders** ビューからのデータが返されることを確認します。
3. セルの下の結果セクションで、 **[ビュー]** オプションを **[表]** から **[グラフ]** に変更します。
4. スクリプトの完了まで待ちます。通常、約 10 分かかりますが、さらに時間がかかる場合もあります。
    - **グラフの種類**: 横棒グラフ
    - **キー**: Item
    - **値**: Quantity
    - **系列グループ**: "空白のままにする"**
    - **集計**: SUM
    - **積み上げ**: "未選択"**

5. グラフが次のように表示されていることを確認します。

    ![製品の合計注文数別の横棒グラフ](../images/notebook-chart.png)

### <a name="get-started-with-matplotlib"></a>**matplotlib** の使用を開始する

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    sqlQuery = "SELECT CAST(YEAR(OrderDate) AS CHAR(4)) AS OrderYear, \
                    SUM((UnitPrice * Quantity) + Tax) AS GrossRevenue \
                FROM salesorders \
                GROUP BY CAST(YEAR(OrderDate) AS CHAR(4)) \
                ORDER BY OrderYear"
    df_spark = spark.sql(sqlQuery)
    df_spark.show()
    ```

2. コードを実行し、年間収益を含む Spark データフレームが返されることを確認します。

    待っている間、Azure Synapse Analytics ドキュメントの「[Azure Synapse Analytics での Apache Spark](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-overview)」の記事を確認してください。

3. ノートブックに新しいコード セルを追加し、そこに次のコードを追加します。

    ```Python
    from matplotlib import pyplot as plt

    # matplotlib requires a Pandas dataframe, not a Spark one
    df_sales = df_spark.toPandas()

    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'])

    # Display the plot
    plt.show()
    ```

4. Run the cell and review the results, which consist of a column chart with the total gross revenue for each year. Note the following features of the code used to produce this chart:
    - **matplotlib** ライブラリには *Pandas* データフレームが必要であるため、Spark SQL クエリによって返される *Spark* データフレームをこの形式に変換する必要があります。
    - At the core of the <bpt id="p1">**</bpt>matplotlib<ept id="p1">**</ept> library is the <bpt id="p2">**</bpt>pyplot<ept id="p2">**</ept> object. This is the foundation for most plotting functionality.
    - 既定の設定では、使用可能なグラフが生成されますが、カスタマイズすべき範囲が大幅に増えます。

5. コードを次のように変更して、グラフをプロットします。

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

    # Customize the chart
    plt.title('Revenue by Year')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
    plt.xticks(rotation=45)

    # Show the figure
    plt.show()
    ```

6. Re-run the code cell and view the results. The chart now includes a little more information.

    A plot is technically contained with a <bpt id="p1">**</bpt>Figure<ept id="p1">**</ept>. In the previous examples, the figure was created implicitly for you; but you can create it explicitly.

7. コードを次のように変更して、グラフをプロットします。

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a Figure
    fig = plt.figure(figsize=(8,3))

    # Create a bar plot of revenue by year
    plt.bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')

    # Customize the chart
    plt.title('Revenue by Year')
    plt.xlabel('Year')
    plt.ylabel('Revenue')
    plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
    plt.xticks(rotation=45)

    # Show the figure
    plt.show()
    ```

8. Re-run the code cell and view the results. The figure determines the shape and size of the plot.

    図には複数のサブプロットが含まれており、それぞれに独自の "軸" があります**。

9. コードを次のように変更して、グラフをプロットします。

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a figure for 2 subplots (1 row, 2 columns)
    fig, ax = plt.subplots(1, 2, figsize = (10,4))

    # Create a bar plot of revenue by year on the first axis
    ax[0].bar(x=df_sales['OrderYear'], height=df_sales['GrossRevenue'], color='orange')
    ax[0].set_title('Revenue by Year')

    # Create a pie chart of yearly order counts on the second axis
    yearly_counts = df_sales['OrderYear'].value_counts()
    ax[1].pie(yearly_counts)
    ax[1].set_title('Orders per Year')
    ax[1].legend(yearly_counts.keys().tolist())

    # Add a title to the Figure
    fig.suptitle('Sales Data')

    # Show the figure
    plt.show()
    ```

10. Re-run the code cell and view the results. The figure contains the subplots specified in the code.

> **注**: matplotlib を使用したプロットの詳細については、[matplotlib のドキュメント](https://matplotlib.org/)を参照してください。

### <a name="use-the-seaborn-library"></a>**seaborn** ライブラリを使用する

While <bpt id="p1">**</bpt>matplotlib<ept id="p1">**</ept> enables you to create complex charts of multiple types, it can require some complex code to achieve the best results. For this reason, over the years, many new libraries have been built on the base of matplotlib to abstract its complexity and enhance its capabilities. One such library is <bpt id="p1">**</bpt>seaborn<ept id="p1">**</ept>.

1. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    import seaborn as sns

    # Clear the plot area
    plt.clf()

    # Create a bar chart
    ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

2. コードを実行し、seaborn ライブラリを使用した横棒グラフが表示されていることを確認します。
3. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    # Clear the plot area
    plt.clf()

    # Set the visual theme for seaborn
    sns.set_theme(style="whitegrid")

    # Create a bar chart
    ax = sns.barplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

4. コードを実行し、seaborn によってプロットに一貫性のある配色テーマが設定されていることに注意します。

5. ノートブックに新しいコード セルを追加し、そこに次のコードを入力します。

    ```Python
    # Clear the plot area
    plt.clf()

    # Create a bar chart
    ax = sns.lineplot(x="OrderYear", y="GrossRevenue", data=df_sales)
    plt.show()
    ```

6. コードを実行し、年間収益を折れ線グラフで表示します。

> **注**: seaborn を使用したプロットの詳細については、[seaborn のドキュメント](https://seaborn.pydata.org/index.html)を参照してください。

## <a name="delete-azure-resources"></a>Azure リソースを削除する

Azure Synapse Analytics を調べ終わったら、不要な Azure コストを避けるために、作成したリソースを削除する必要があります。

1. Synapse Studio ブラウザー タブを閉じ、Azure portal に戻ります。
2. Azure portal の **[ホーム]** ページで、**[リソース グループ]** を選択します。
3. Synapse Analytics ワークスペースに対して **dp500-*xxxxxxx*** リソース グループ (マネージド リソース グループ以外) を選択し、そこに Synapse ワークスペース、ストレージ アカウント、ワークスペースの Spark プールが含まれていることを確認します。
4. リソース グループの **[概要]** ページの上部で、**[リソース グループの削除]** を選択します。
5. リソース グループ名として「**dp500-*xxxxxxx***」と入力し、これが削除対象であることを確認したら、 **[削除]** を選択します。

    数分後に、Azure Synapse ワークスペース リソース グループと、それに関連付けられているマネージド ワークスペース リソース グループが削除されます。
