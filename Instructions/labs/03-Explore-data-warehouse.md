---
lab:
  title: ラボ – リレーショナル データ ウェアハウスについて調べる
  module: 'Model, query, and explore data in Azure Synapse'
---

# <a name="explore-a-relational-data-warehouse"></a>ラボ – リレーショナル データ ウェアハウスについて調べる

Azure Synapse Analytics is built on a scalable set capabilities to support enterprise data warehousing; including file-based data analytics in a data lake as well as large-scale relational data warehouses and the data transfer and transformation pipelines used to load them. In this lab, you'll explore how to use a dedicated SQL pool in Azure Synapse Analytics to store and query data in a relational data warehouse.

このラボは完了するまで、約 **45** 分かかります。

## <a name="before-you-start"></a>開始する前に

管理レベルのアクセス権を持つ [Azure サブスクリプション](https://azure.microsoft.com/free)が必要です。

## <a name="provision-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースをプロビジョニングする

An Azure Synapse Analytics <bpt id="p1">*</bpt>workspace<ept id="p1">*</ept> provides a central point for managing data and data processing runtimes. You can provision a workspace using the interactive interface in the Azure portal, or you can deploy a workspace and resources within it by using a script or template. In most production scenarios, it's best to automate provisioning with scripts and templates so that you can incorporate resource deployment into a repeatable development and operations (<bpt id="p1">*</bpt>DevOps<ept id="p1">*</ept>) process.

この演習では、Azure Synapse Analytics ワークスペースをプロビジョニングするために、PowerShell スクリプトと ARM テンプレートを組み合わせて使用します。

1. [Azure portal](https://portal.azure.com) (`https://portal.azure.com`) にサインインします。
2. Use the <bpt id="p1">**</bpt>[<ph id="ph1">\&gt;</ph>_]<ept id="p1">**</ept> button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a <bpt id="p2">***</bpt>PowerShell<ept id="p2">***</ept> environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal と Cloud Shell のペイン](../images/cloud-shell.png)

    > **注**: 前に *Bash* 環境を使ってクラウド シェルを作成している場合は、そのクラウド シェル ペインの左上にあるドロップダウン メニューを使って、***PowerShell*** に変更します。

3. Azure Synapse Analytics は、エンタープライズ データ ウェアハウスをサポートするスケーラブルなセット機能に基づいて構築されています。これには、データ レイク内のファイル ベースのデータ分析、大規模なリレーショナル データ ウェアハウス、それらを読み込むのに使用されるデータ転送および変換パイプラインが含まれます。

4. PowerShell のペインで、次のコマンドを入力して、リポジトリを複製します。

    ```
    rm -r dp500 -f
    git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst dp500
    ```

5. リポジトリが複製されたら、次のコマンドを入力してこのラボ用のフォルダーに変更し、そこに含まれている **setup.ps1** スクリプトを実行します。

    ```
    cd dp500/Allfiles/03
    ./setup.ps1
    ```

6. メッセージが表示された場合は、使用するサブスクリプションを選択します (これは、複数の Azure サブスクリプションへのアクセス権を持っている場合にのみ行います)。
7. メッセージが表示されたら、Azure Synapse SQL プールに設定する適切なパスワードを入力します。

    > **注**: このパスワードは忘れないようにしてください。

8. このラボでは、Azure Synapse Analytics の専用 SQL プールを使用して、リレーショナル データ ウェアハウスにデータを格納およびクエリする方法について説明します。

## <a name="explore-the-data-warehouse-schema"></a>データ ウェアハウス スキーマを調べる

このラボでは、データ ウェアハウスは Azure Synapse Analytics の専用 SQL プールでホストされます。

### <a name="start-the-dedicated-sql-pool"></a>専用 SQL プールを開始する

1. スクリプトが完了したら、Azure portal で、作成された **dp500-*xxxxxxx*** リソース グループに移動して、Synapse ワークスペースを選択します。
2. Synapse ワークスペースの **[概要]** ページの **[Synapse Studio を開く]** カードで **[開く]** を選択し、新しいブラウザー タブで Synapse Studio を開きます。メッセージが表示された場合はサインインします。
3. Synapse Studio の左側にある **&rsaquo;&rsaquo;** アイコンを使用してメニューを展開します。これにより、リソースの管理とデータ分析タスクの実行に使用するさまざまなページが Synapse Studio 内に表示されます。
4. [ **管理** ] ページで、[ **SQL プール** ] タブが選択されていることを確認し、 **sql*xxxxxxx*** 専用 SQL プールを選択し、 ** その&#9655;** アイコンを使用して起動します。メッセージが表示されたら再開することを確認します。
5. Wait for the SQL pool to resume. This can take a few minutes. Use the <bpt id="p1">**</bpt>&amp;#8635; Refresh<ept id="p1">**</ept> button to check its status periodically. The status will show as <bpt id="p1">**</bpt>Online<ept id="p1">**</ept> when it is ready.

### <a name="view-the-tables-in-the-database"></a>データベース内のテーブルを表示する

1. Synapse Studioで、[**データ**] ページを選択し、[**ワークスペース**] タブが選択され、**SQL データベース** カテゴリが含まれていることを確認します。
2. **SQL データベース**、**sql*xxxxxxx*** プール、およびその **Tables** フォルダーを展開して、データベース内のテーブルを表示します。

    A relational data warehouse is typically based on a schema that consists of <bpt id="p1">*</bpt>fact<ept id="p1">*</ept> and <bpt id="p2">*</bpt>dimension<ept id="p2">*</ept> tables. The tables are optimized for analytical queries in which numeric metrics in the fact tables are aggregated by attributes of the entities represented by the dimension tables - for example, enabling you to aggregate Internet sales revenue by product, customer, date, and so on.
    
3. Expand the <bpt id="p1">**</bpt>dbo.FactInternetSales<ept id="p1">**</ept> table and its <bpt id="p2">**</bpt>Columns<ept id="p2">**</ept> folder to see the columns in this table. Note that many of the columns are <bpt id="p1">*</bpt>keys<ept id="p1">*</ept> that reference rows in the dimension tables. Others are numeric values (<bpt id="p1">*</bpt>measures<ept id="p1">*</ept>) for analysis.
    
    キーは、ファクト テーブルを 1 つ以上のディメンション テーブル (多くの場合、 *ina star* スキーマ) に関連付けるために使用されます。ファクト テーブルが各ディメンション テーブルに直接関連付けられている場合 (中央にファクト テーブルを含む複数の点の "星" を形成します)。

4. View the columns for the <bpt id="p1">**</bpt>dbo.DimPromotion<ept id="p1">**</ept> table, and note that it has a unique <bpt id="p2">**</bpt>PromotionKey<ept id="p2">**</ept> that uniquely identifies each row in the table. It also has an <bpt id="p1">**</bpt>AlternateKey<ept id="p1">**</ept>.

    Usually, data in a data warehouse has been imported from one or more transactional sources. The <bpt id="p1">*</bpt>alternate<ept id="p1">*</ept> key reflects the business identifier for the instance of this entity in the source, but a unique numeric <bpt id="p2">*</bpt>surrogate<ept id="p2">*</ept> key is usually generated to uniquely identify each row in the data warehouse dimension table. One of the benefits of this approach is that it enables the data warehouse to contain multiple instances of the same entity at different points in time (for example, records for the same customer reflecting their address at the time an order was placed).

5. dbo の列を表示します **。DimProduct** には、dbo を参照する **ProductSubcategoryKey** 列が含まれていることに注意してください **。DimProductSubcategory** テーブル。次に、dbo を参照する **ProductCategoryKey** 列が含まれます  **。DimProductCategory** テーブル。

    Azure Synapse Analytics *ワークスペース*は、データとデータ処理ランタイムを管理するための中心的なポイントを提供します。

6. dbo の列を表示します **。DimDate** テーブルには、曜日、曜日、月、年、日名、月名など、日付のさまざまなテンポラル属性を反映する複数の列が含まれていることに注意してください。

    Azure portalの対話型インターフェイスを使用してワークスペースをプロビジョニングすることも、スクリプトまたはテンプレートを使用してワークスペースとその中にリソースをデプロイすることもできます。

## <a name="query-the-data-warehouse-tables"></a>データ ウェアハウス テーブルのクエリを実行する

データ ウェアハウス スキーマのより重要な側面をいくつか調べてきたので、テーブルのクエリを実行してデータを取得する準備ができました。

### <a name="query-fact-and-dimension-tables"></a>ファクト テーブルとディメンション テーブルを比較する

ほとんどの運用シナリオでは、反復可能な開発および運用 (*DevOps*) プロセスにリソースのデプロイを組み込むことができるように、スクリプトとテンプレートを使用してプロビジョニングを自動化することをお勧めします。

1. [**データ**] ページで、**sql*xxxxxxx*** SQL プールを選択し、その **...** メニューで **[新しい SQL スクリプト****の空のスクリプト** > ] を選択します。
2. When a new <bpt id="p1">**</bpt>SQL Script 1<ept id="p1">**</ept> tab opens, in its <bpt id="p2">**</bpt>Properties<ept id="p2">**</ept> pane, change the name of the script to <bpt id="p3">**</bpt>Analyze Internet Sales<ept id="p3">**</ept> and change the <bpt id="p4">**</bpt>Result settings per query<ept id="p4">**</ept> to return all rows. Then use the <bpt id="p1">**</bpt>Publish<ept id="p1">**</ept> button on the toolbar to save the script, and use the <bpt id="p2">**</bpt>Properties<ept id="p2">**</ept> button (which looks similar to <bpt id="p3">**</bpt>&amp;#128463;.<ept id="p3">**</ept>) on the right end of the toolbar to close the <bpt id="p4">**</bpt>Properties<ept id="p4">**</ept> pane so you can see the script pane.
3. 新しい空のコード セルに、次のコードを追加します。

    ```sql
    SELECT  d.CalendarYear AS Year,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY Year;
    ```

4. Use the <bpt id="p1">**</bpt>&amp;#9655; Run<ept id="p1">**</ept> button to run the script, and review the results, which should show the Internet sales totals for each year. This query joins the fact table for Internet sales to a time dimension table based on the order date, and aggregates the sales amount measure in the fact table by the calendar month attribute of the dimension table.

5. 次のようにクエリを変更して、時間ディメンションから month 属性を追加し、変更したクエリを実行します。

    ```sql
    SELECT  d.CalendarYear AS Year,
            d.MonthNumberOfYear AS Month,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear, d.MonthNumberOfYear
    ORDER BY Year, Month;
    ```

    Note that the attributes in the time dimension enable you to aggregate the measures in the fact table at multiple hierarchical levels - in this case, year and month. This is a common pattern in data warehouses.

6. 次のようにクエリを変更して月を削除し、集計に 2 番目のディメンションを追加し、それを実行して結果を表示します (各リージョンの年間インターネット売上合計が表示されます)。

    ```sql
    SELECT  d.CalendarYear AS Year,
            g.EnglishCountryRegionName AS Region,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    GROUP BY d.CalendarYear, g.EnglishCountryRegionName
    ORDER BY Year, Region;
    ```

    ページ上部の検索バーの右側にある **[\>_]** ボタンを使用して、Azure portal に新しい Cloud Shell を作成します。メッセージが表示された場合は、***PowerShell*** 環境を選択して、ストレージを作成します。

7. クエリを変更して再実行して、別の snowflake ディメンションを追加し、製品カテゴリ別の年間地域売上を集計します。

    ```sql
    SELECT  d.CalendarYear AS Year,
            pc.EnglishProductCategoryName AS ProductCategory,
            g.EnglishCountryRegionName AS Region,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    JOIN DimProduct AS p ON i.ProductKey = p.ProductKey
    JOIN DimProductSubcategory AS ps ON p.ProductSubcategoryKey = ps.ProductSubcategoryKey
    JOIN DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey
    GROUP BY d.CalendarYear, pc.EnglishProductCategoryName, g.EnglishCountryRegionName
    ORDER BY Year, ProductCategory, Region;
    ```

    今回、製品カテゴリの snowflake ディメンションでは、製品、サブカテゴリ、およびカテゴリ間の階層関係を反映するために、3 つの結合が必要です。

8. スクリプトを発行して保存します。

### <a name="use-ranking-functions"></a>順位付け関数と行セット関数を使用する

大量のデータを分析する場合のもう 1 つの一般的な要件は、パーティションごとにデータをグループ化し、特定のメトリックに基づいてパーティション内の各エンティティの *ランク* を決定することです。

1. 既存のクエリで、次の SQL を追加して、国/地域名に基づいてパーティションに対する 2022 の売上値を取得します。

    ```sql
    SELECT  g.EnglishCountryRegionName AS Region,
            ROW_NUMBER() OVER(PARTITION BY g.EnglishCountryRegionName
                              ORDER BY i.SalesAmount ASC) AS RowNumber,
            i.SalesOrderNumber AS OrderNo,
            i.SalesOrderLineNumber AS LineItem,
            i.SalesAmount AS SalesAmount,
            SUM(i.SalesAmount) OVER(PARTITION BY g.EnglishCountryRegionName) AS RegionTotal,
            AVG(i.SalesAmount) OVER(PARTITION BY g.EnglishCountryRegionName) AS RegionAverage
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    WHERE d.CalendarYear = 2022
    ORDER BY Region;
    ```

2. 次に示すように、Azure portal の下部にあるペインに、Cloud Shell のコマンド ライン インターフェイスが表示されます。

    | リージョン | RowNumber | OrderNo | LineItem | SalesAmount | RegionTotal | RegionAverage |
    |--|--|--|--|--|--|--|
    |オーストラリア|1|SO73943|2|2.2900|2172278.7900|375.8918|
    |オーストラリア|2|SO74100|4|2.2900|2172278.7900|375.8918|
    |...|...|...|...|...|...|...|
    |オーストラリア|577.9|SO64284|1|2443.3500|2172278.7900|375.8918|
    |Canada|1|SO66332|2|2.2900|563177.1000|157.8411|
    |カナダ|2|SO68234|2|2.2900|563177.1000|157.8411|
    |...|...|...|...|...|...|...|
    |カナダ|3568|SO70911|1|2443.3500|563177.1000|157.8411|
    |フランス|1|SO68226|3|2.2900|816259.4300|315.4016|
    |フランス|2|SO63460|2|2.2900|816259.4300|315.4016|
    |...|...|...|...|...|...|...|
    |フランス|2588|SO69100|1|2443.3500|816259.4300|315.4016|
    |ドイツ|1|SO70829|3|2.2900|922368.2100|352.4525|
    |ドイツ|2|SO71651|2|2.2900|922368.2100|352.4525|
    |...|...|...|...|...|...|...|
    |ドイツ|2617|SO67908|1|2443.3500|922368.2100|352.4525|
    |イギリス|1|SO66124|3|2.2900|1051560.1000|341.7484|
    |イギリス|2|SO67823|3|2.2900|1051560.1000|341.7484|
    |...|...|...|...|...|...|...|
    |イギリス|3077|SO71568|1|2443.3500|1051560.1000|341.7484|
    |アメリカ合衆国|1|SO74796|2|2.2900|2905011.1600|289.0270|
    |アメリカ合衆国|2|SO65114|2|2.2900|2905011.1600|289.0270|
    |...|...|...|...|...|...|...|
    |アメリカ合衆国|10051|SO66863|1|2443.3500|2905011.1600|289.0270|

    これらの結果に関する次の事実を確認します。

    - 販売注文品目ごとに 1 行あります。
    - 行は、販売が行われた地域に基づいてパーティションに編成されます。
    - 各地理的パーティション内の行には、売上金額の順に番号が付けられます (最小から最大)。
    - 各行に対して、品目の売上金額と地域合計と平均売上金額が含まれます。

3. 既存のクエリの下に、次のコードを追加して GROUP BY クエリ内にウィンドウ関数を適用し、販売額の合計に基づいて各リージョンの都市をランク付けします。

    ```sql
    SELECT  g.EnglishCountryRegionName AS Region,
            g.City,
            SUM(i.SalesAmount) AS CityTotal,
            SUM(SUM(i.SalesAmount)) OVER(PARTITION BY g.EnglishCountryRegionName) AS RegionTotal,
            RANK() OVER(PARTITION BY g.EnglishCountryRegionName
                        ORDER BY SUM(i.SalesAmount) DESC) AS RegionalRank
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    JOIN DimCustomer AS c ON i.CustomerKey = c.CustomerKey
    JOIN DimGeography AS g ON c.GeographyKey = g.GeographyKey
    GROUP BY g.EnglishCountryRegionName, g.City
    ORDER BY Region;
    ```

4. Select only the new query code, and use the <bpt id="p1">**</bpt>&amp;#9655; Run<ept id="p1">**</ept> button to run it. Then review the results, and observe the following:
    - 結果には、地域別にグループ化された各都市の行が含まれます。
    - 合計売上 (個々の売上金額の合計) は、市区町村ごとに計算されます
    - 地域の売上合計 (地域内の各都市の売上金額の合計) は、地域パーティションに基づいて計算されます。
    - リージョン パーティション内の各都市のランクは、都市ごとの合計売上金額を降順で並べ替えることで計算されます。

5. 更新されたスクリプトを発行して変更を保存します。

> <bpt id="p1">**</bpt>Tip<ept id="p1">**</ept>: ROW_NUMBER and RANK are examples of ranking functions available in Transact-SQL. For more details, see the <bpt id="p1">[</bpt>Ranking Functions<ept id="p1">](https://docs.microsoft.com/sql/t-sql/functions/ranking-functions-transact-sql)</ept> reference in the Transact-SQL language documentation.

### <a name="retrieve-an-approximate-count"></a>概算カウントの取得

When exploring very large volumes of data, queries can take significant time and resources to run. Often, data analysis doesn't require absolutely precise values - a comparison of approximate values may be sufficient.

1. 既存のクエリの下に次のコードを追加して、各暦年の販売注文の数を取得します。

    ```sql
    SELECT d.CalendarYear AS CalendarYear,
        COUNT(DISTINCT i.SalesOrderNumber) AS Orders
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY CalendarYear;
    ```

2. ペインの上部にある区分線をドラッグして Cloud Shell のサイズを変更したり、ペインの右上にある **&#8212;** 、 **&#9723;** 、**X** アイコンを使用して、ペインを最小化または最大化したり、閉じたりすることができます。
    - クエリの [ **結果** ] タブで、各年の注文数を表示します。
    - [ **メッセージ** ] タブで、クエリの合計実行時間を表示します。
3. Azure Cloud Shell の使い方について詳しくは、[Azure Cloud Shell のドキュメント](https://docs.microsoft.com/azure/cloud-shell/overview)をご覧ください。

    ```sql
    SELECT d.CalendarYear AS CalendarYear,
        APPROX_COUNT_DISTINCT(i.SalesOrderNumber) AS Orders
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY CalendarYear;
    ```

4. 返される出力を確認します。
    - On the <bpt id="p1">**</bpt>Results<ept id="p1">**</ept> tab under the query, view the order counts for each year. These should be within 2% of the actual counts retrieved by the previous query.
    - On the <bpt id="p1">**</bpt>Messages<ept id="p1">**</ept> tab, view the total execution time for the query. This should be shorter than for the previous query.

5. スクリプトを発行して変更を保存します。

> 詳しくは、[APPROX_COUNT_DISTINCT](https://docs.microsoft.com/sql/t-sql/functions/approx-count-distinct-transact-sql) 関数の資料を参照してください。

## <a name="challenge---analyze-reseller-sales"></a>チャレンジ - リセラーの売上を分析する

1. **sql*xxxxxxx*** SQL プールの新しい空のスクリプトを作成し、**Analyze Reseller Sales** という名前で保存します。
2. スクリプトで SQL クエリを作成し、 **FactResellerSales ファクト** テーブルと関連するディメンション テーブルに基づいて次の情報を検索します。
    - 会計年度および四半期ごとの販売品目の合計数量。
    - 販売を行った従業員に関連付けられている会計年度、四半期、および販売地域ごとの販売品目の合計数量。
    - 製品カテゴリ別の会計年度、四半期、および販売地域ごとの販売品目の合計数量。
    - 年度の総売上金額に基づく、会計年度ごとの各販売区域のランク。
    - 各販売地域の 1 年あたりの販売注文の概算数。

    > **ヒント**: Synapse Studioの **[開発**] ページの**ソリューション** スクリプトのクエリとクエリを比較します。

3. クエリを試して、データ ウェアハウス スキーマ内の残りのテーブルをレジャーとして探索します。
4. 完了したら、[ **管理** ] ページで、 **sql*xxxxxxx*** 専用 SQL プールを一時停止します。

## <a name="delete-azure-resources"></a>Azure リソースを削除する

Azure Synapse Analytics を調べ終わったら、不要な Azure コストを避けるために、作成したリソースを削除する必要があります。

1. Synapse Studio ブラウザー タブを閉じ、Azure portal に戻ります。
2. Azure portal の **[ホーム]** ページで、**[リソース グループ]** を選択します。
3. Synapse Analytics ワークスペースに対して **dp500-*xxxxxxx*** リソース グループ (マネージド リソース グループ以外) を選択し、そこに Synapse ワークスペース、ストレージ アカウント、ワークスペースの Spark プールが含まれていることを確認します。
4. リソース グループの **[概要]** ページの上部で、**[リソース グループの削除]** を選択します。
5. リソース グループ名として「**dp500-*xxxxxxx***」と入力し、これが削除対象であることを確認したら、 **[削除]** を選択します。

    数分後に、Azure Synapse ワークスペース リソース グループと、それに関連付けられているマネージド ワークスペース リソース グループが削除されます。
