---
lab:
  title: ラボ – リレーショナル データ ウェアハウスについて調べる
  module: 'Model, query, and explore data in Azure Synapse'
---

# <a name="explore-a-relational-data-warehouse"></a>ラボ – リレーショナル データ ウェアハウスについて調べる

Azure Synapse Analytics は、エンタープライズ データ ウェアハウスをサポートするスケーラブルなセット機能に基づいて構築されています。これには、データ レイク内のファイル ベースのデータ分析、大規模なリレーショナル データ ウェアハウス、データ転送と変換パイプラインの読み込みに使用されます。 このラボでは、Azure Synapse Analytics の専用 SQL プールを使用して、リレーショナル データ ウェアハウスにデータを格納およびクエリする方法について説明します。

このラボは完了するまで、約 **45** 分かかります。

## <a name="before-you-start"></a>開始する前に

管理レベルのアクセス権を持つ [Azure サブスクリプション](https://azure.microsoft.com/free)が必要です。

## <a name="provision-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics ワークスペースをプロビジョニングする

Azure Synapse Analytics *ワークスペース*は、データとデータ処理ランタイムを管理するための中心的なポイントを提供します。 Azure portalの対話型インターフェイスを使用してワークスペースをプロビジョニングすることも、スクリプトまたはテンプレートを使用してワークスペースとその中にリソースをデプロイすることもできます。 ほとんどの運用シナリオでは、反復可能な開発と運用 (*DevOps*) プロセスにリソースのデプロイを組み込むことができるように、スクリプトとテンプレートを使用してプロビジョニングを自動化することをお勧めします。

この演習では、Azure Synapse Analytics ワークスペースをプロビジョニングするために、PowerShell スクリプトと ARM テンプレートを組み合わせて使用します。

1. [Azure portal](https://portal.azure.com) (`https://portal.azure.com`) にサインインします。
2. ページ上部の検索バーの右側にある **[\>_]** ボタンを使用して、Azure portal に新しい Cloud Shell を作成します。メッセージが表示された場合は、***PowerShell*** 環境を選択して、ストレージを作成します。 次に示すように、Azure portal の下部にあるペインに、Cloud Shell のコマンド ライン インターフェイスが表示されます。

    ![Azure portal と Cloud Shell のペイン](../images/cloud-shell.png)

    > **注**: 前に *Bash* 環境を使ってクラウド シェルを作成している場合は、そのクラウド シェル ペインの左上にあるドロップダウン メニューを使って、***PowerShell*** に変更します。

3. ペインの上部にある区分線をドラッグして Cloud Shell のサイズを変更したり、ペインの右上にある **&#8212;** 、 **&#9723;** 、**X** アイコンを使用して、ペインを最小化または最大化したり、閉じたりすることができます。 Azure Cloud Shell の使い方について詳しくは、[Azure Cloud Shell のドキュメント](https://docs.microsoft.com/azure/cloud-shell/overview)をご覧ください。

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

8. スクリプトの完了まで待ちます。通常、約 10 分かかりますが、さらに時間がかかる場合もあります。 待っている間、Azure Synapse Analytics ドキュメントの記事「[Azure Synapse Analytics のサーバーレス SQL プール](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is)」を確認してください。

## <a name="explore-the-data-warehouse-schema"></a>データ ウェアハウススキーマを調べる

このラボでは、データ ウェアハウスは Azure Synapse Analytics の専用 SQL プールでホストされます。

### <a name="start-the-dedicated-sql-pool"></a>専用 SQL プールを起動する

1. スクリプトが完了したら、Azure portal で、作成された **dp500-*xxxxxxx*** リソース グループに移動して、Synapse ワークスペースを選択します。
2. Synapse ワークスペースの **[概要]** ページの **[Synapse Studio を開く]** カードで **[開く]** を選択し、新しいブラウザー タブで Synapse Studio を開きます。メッセージが表示された場合はサインインします。
3. Synapse Studio の左側にある **&rsaquo;&rsaquo;** アイコンを使用してメニューを展開します。これにより、リソースの管理とデータ分析タスクの実行に使用するさまざまなページが Synapse Studio 内に表示されます。
4. [ **管理** ] ページで、[ **SQL プール** ] タブが選択されていることを確認し、 **sql*xxxxxxx*** 専用 SQL プールを選択し、 ** その&#9655;** アイコンを使用して起動します。メッセージが表示されたら再開することを確認します。
5. SQL プールが再開されるまで待ちます。 これには数分かかることがあります。 **&#8635;更新**ボタンを使用して、その状態を定期的に確認します。 状態は、準備ができたら **オンライン** と表示されます。

### <a name="view-the-tables-in-the-database"></a>データベース内のテーブルを表示する

1. Synapse Studioで、[**データ**] ページを選択し、[**ワークスペース**] タブが選択され、**SQL データベース** カテゴリが含まれていることを確認します。
2. **SQL データベース**、**sql*xxxxxxx*** プール、およびその**テーブル** フォルダーを展開して、データベース内のテーブルを表示します。

    リレーショナル データ ウェアハウスは、通常、 *ファクト* テーブルと *ディメンション* テーブルで構成されるスキーマに基づいています。 テーブルは、ファクト テーブル内の数値メトリックがディメンション テーブルによって表されるエンティティの属性によって集計される分析クエリ用に最適化されています。たとえば、製品、顧客、日付などのインターネット売上収益を集計できます。
    
3. dbo を展開します **。このテーブルの列を表示する FactInternetSales** テーブルとその **Columns** フォルダー。 列の多くは、ディメンション テーブル内の行を参照する *キー* であることに注意してください。 その他は、分析用の数値 (*メジャー*) です。
    
    キーは、ファクト テーブルを 1 つ以上のディメンション テーブル (多くの場合 *スター* スキーマ内) に関連付けるために使用されます。ファクト テーブルが各ディメンション テーブルに直接関連付けられている (ファクト テーブルを中心に複数のポイントを持つ "星" を形成する)。

4. dbo の列を表示します **。DimPromotion** テーブルに、テーブル内の各行を一意に識別する一意の **PromotionKey** があることに注意してください。 また、**** プロパティもあります。

    通常、データ ウェアハウス内のデータは、1 つ以上のトランザクション ソースからインポートされています。 *代替*キーはソース内のこのエンティティのインスタンスのビジネス識別子を反映しますが、通常、データ ウェアハウス ディメンション テーブルの各行を一意に識別するために一意の数値*サロゲート* キーが生成されます。 この方法の利点の 1 つは、データ ウェアハウスに、異なる時点で同じエンティティの複数のインスタンス (たとえば、注文時に住所を反映する同じ顧客のレコード) を含めるということです。

5. dbo の列を表示します **。DimProduct**、および dbo を参照する **ProductSubcategoryKey** 列が含まれていることに注意してください **。DimProductSubcategory** テーブル。このテーブルには、dbo を参照する **ProductCategoryKey** 列が含まれます  **。DimProductCategory** テーブル。

    場合によっては、ディメンションが複数の関連テーブルに部分的に正規化され、サブカテゴリやカテゴリにグループ化できる製品など、さまざまなレベルの粒度が可能になります。 これにより、単純な星が *snowflake* スキーマに拡張され、中央のファクト テーブルがディメンション テーブルに関連し、さらにディメンション テーブルに関連します。

6. dbo の列を表示します **。DimDate** テーブルには、曜日、曜日、月、年、日名、月名など、日付のさまざまな一時的な属性を反映する複数の列が含まれていることに注意してください。

    データ ウェアハウスの時間ディメンションは、通常、ファクト テーブル内のメジャーを集計する最小の時間単位 (ディメンションの *グレイン* と呼ばれる) ごとに行を含むディメンション テーブルとして実装されます。 この場合、メジャーを集計できる最も低いグレインは個々の日付であり、テーブルにはデータで参照される最初から最後の日付までの各日付の行が含まれます。 **DimDate** テーブルの属性を使用すると、アナリストは、一貫した一連のテンポラル属性を使用して、ファクト テーブル内の日付キーに基づいてメジャーを集計できます (たとえば、注文日に基づいて月単位で注文を表示するなど)。 **FactInternetSales** テーブルには、**DimDate** テーブルに関連する 3 つのキー (**OrderDateKey**、**DueDateKey**、**ShipDateKey) が**含まれています。

## <a name="query-the-data-warehouse-tables"></a>データ ウェアハウス テーブルのクエリを実行する

データ ウェアハウス スキーマのより重要な側面をいくつか調べてきたので、テーブルのクエリを実行してデータを取得する準備ができました。

### <a name="query-fact-and-dimension-tables"></a>ファクト テーブルとディメンション テーブルを比較する

リレーショナル データ ウェアハウスの数値は、複数の属性にわたってデータを集計するために使用できる関連ディメンション テーブルを持つファクト テーブルに格納されます。 この設計は、リレーショナル データ ウェアハウスのほとんどのクエリに、関連テーブル (JOIN 句を使用) にわたってデータを集計およびグループ化する (集計関数と GROUP BY 句を使用) ことを意味します。

1. [**データ**] ページで、**sql*xxxxxxx*** SQL プールを選択し、その **...** メニューで [**新しい SQL スクリプト****空のスクリプト** > ] を選択します。
2. 新しい **SQL スクリプト 1** タブが開いたら、[ **プロパティ** ] ウィンドウでスクリプトの名前を [ **Analyze Internet Sales]** に変更し、 **クエリごとの結果設定** を変更してすべての行を返します。 ツール バーで **[発行]** を選択してスクリプトを保存し、ツール バーの右端にある **[プロパティ]** ボタン ( **&#128463;** のような外観) を使用して、 **[プロパティ]** ペインを非表示にします。
3. 新しい空のコード セルに、次のコードを追加します。

    ```sql
    SELECT  d.CalendarYear AS Year,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY Year;
    ```

4. **&#9655;実行**ボタンを使用してスクリプトを実行し、結果を確認します。この結果には、各年のインターネット売上の合計が表示されます。 このクエリでは、インターネット販売のファクト テーブルを注文日に基づいて時間ディメンション テーブルに結合し、ディメンション テーブルのカレンダー月属性によってファクト テーブルの売上金額メジャーを集計します。

5. 時間ディメンションから月属性を追加し、変更したクエリを実行するには、次のようにクエリを変更します。

    ```sql
    SELECT  d.CalendarYear AS Year,
            d.MonthNumberOfYear AS Month,
            SUM(i.SalesAmount) AS InternetSalesAmount
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear, d.MonthNumberOfYear
    ORDER BY Year, Month;
    ```

    時間ディメンションの属性を使用すると、ファクト テーブルのメジャーを複数の階層レベル (この場合は年と月) で集計できます。 これは、データ ウェアハウスの一般的なパターンです。

6. 次のようにクエリを変更して月を削除し、集計に 2 番目のディメンションを追加し、それを実行して結果を表示します (各リージョンの年間インターネット売上の合計が表示されます)。

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

    geography は、顧客ディメンションを通じてインターネット販売ファクト テーブルに関連する *スノーフレーク* ディメンションであることに注意してください。 そのため、インターネットの売上を地域別に集計するには、クエリに 2 つの結合が必要です。

7. クエリを変更して再実行して、別の snowflake ディメンションを追加し、製品カテゴリ別に年間地域売上を集計します。

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

    今回は、製品カテゴリのスノーフレーク ディメンションでは、製品、サブカテゴリ、カテゴリ間の階層関係を反映するために 3 つの結合が必要です。

8. スクリプトを発行して保存します。

### <a name="use-ranking-functions"></a>順位付け関数と行セット関数を使用する

大量のデータを分析する際のもう 1 つの一般的な要件は、パーティションごとにデータをグループ化し、特定のメトリックに基づいてパーティション内の各エンティティの *ランク* を決定することです。

1. 既存のクエリの下に、次の SQL を追加して、国/地域名に基づいてパーティションに対する 2022 の売上値を取得します。

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

2. 新しいクエリ コードのみを選択し、 **&#9655;実行** ボタンを使用して実行します。 次に、結果を確認します。次の表のようになります。

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
    |United States|1|SO74796|2|2.2900|2905011.1600|289.0270|
    |United States|2|SO65114|2|2.2900|2905011.1600|289.0270|
    |...|...|...|...|...|...|...|
    |United States|10051|SO66863|1|2443.3500|2905011.1600|289.0270|

    これらの結果に関する次の事実を確認します。

    - 販売注文品目ごとに 1 行あります。
    - 行は、販売が行われた地域に基づいてパーティションに編成されます。
    - 各地理的パーティション内の行には、売上高の順に番号が付けられます (最小から最高)。
    - 各行に、品目の売上金額と地域の合計と平均売上金額が含まれます。

3. 既存のクエリの下に次のコードを追加して、GROUP BY クエリ内にウィンドウ関数を適用し、売上の合計に基づいて各リージョンの都市をランク付けします。

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

4. 新しいクエリ コードのみを選択し、[ **&#9655;実行** ] ボタンを使用して実行します。 次に、結果を確認し、次の内容を確認します。
    - 結果には、地域別にグループ化された各都市の行が含まれます。
    - 市区町村ごとに売上合計 (個々の売上高の合計) が計算されます。
    - 地域の売上合計 (地域内の各都市の売上金額の合計) は、地域のパーティションに基づいて計算されます。
    - 地域パーティション内の各都市の順位は、1 都市あたりの合計売上金額を降順に並べ替えることによって計算されます。

5. 更新されたスクリプトを発行して変更を保存します。

> **ヒント**: ROW_NUMBERと RANK は、Transact-SQL で使用できるランク付け関数の例です。 詳細については、Transact-SQL 言語ドキュメントの [ランキング関数](https://docs.microsoft.com/sql/t-sql/functions/ranking-functions-transact-sql) リファレンスを参照してください。

### <a name="retrieve-an-approximate-count"></a>概算カウントの取得

非常に大量のデータを探索する場合、クエリの実行にかなりの時間とリソースがかかることがあります。 多くの場合、データ分析では絶対に正確な値は必要ありません。近似値の比較で十分な場合があります。

1. 既存のクエリの下に、次のコードを追加して、各暦年の販売注文の数を取得します。

    ```sql
    SELECT d.CalendarYear AS CalendarYear,
        COUNT(DISTINCT i.SalesOrderNumber) AS Orders
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY CalendarYear;
    ```

2. 新しいクエリ コードのみを選択し、[ **&#9655;実行** ] ボタンを使用して実行します。 次に、返される出力を確認します。
    - クエリの [ **結果** ] タブで、各年の注文数を表示します。
    - [ **メッセージ** ] タブで、クエリの合計実行時間を表示します。
3. 各年のおおよその数を返すように、次のようにクエリを変更します。 次に、クエリを再実行します。

    ```sql
    SELECT d.CalendarYear AS CalendarYear,
        APPROX_COUNT_DISTINCT(i.SalesOrderNumber) AS Orders
    FROM FactInternetSales AS i
    JOIN DimDate AS d ON i.OrderDateKey = d.DateKey
    GROUP BY d.CalendarYear
    ORDER BY CalendarYear;
    ```

4. 返される出力を確認します。
    - クエリの [ **結果** ] タブで、各年の注文数を表示します。 これらは、前のクエリによって取得された実際のカウントの 2% 以内である必要があります。
    - [ **メッセージ** ] タブで、クエリの合計実行時間を表示します。 これは、前のクエリよりも短くする必要があります。

5. スクリプトを発行して変更を保存します。

> 詳しくは、[APPROX_COUNT_DISTINCT](https://docs.microsoft.com/sql/t-sql/functions/approx-count-distinct-transact-sql) 関数の資料を参照してください。

## <a name="challenge---analyze-reseller-sales"></a>課題 - リセラーの売上を分析する

1. **sql*xxxxxxx* SQL** プールの新しい空のスクリプトを作成し、**Analyze Reseller Sales** という名前で保存します。
2. **FactResellerSales ファクト** テーブルと関連するディメンション テーブルに基づいて次の情報を検索する SQL クエリをスクリプトに作成します。
    - 会計年度および四半期ごとに販売された品目の合計数量。
    - 販売を行った従業員に関連付けられている会計年度、四半期、および販売区域地域ごとに販売された品目の合計数量。
    - 製品カテゴリ別の会計年度、四半期、販売地域ごとの販売品目の合計数量。
    - 年度の総売上金額に基づく、会計年度ごとの各販売区域のランク。
    - 各販売区域の 1 年あたりの販売注文のおおよその数。

    > **ヒント**: Synapse Studioの **[開発**] ページにある**ソリューション** スクリプトのクエリとクエリを比較します。

3. クエリを試して、データ ウェアハウス スキーマ内の残りのテーブルを自由に探索します。
4. 完了したら、[ **管理** ] ページで **、sql*xxxxxxx*** 専用 SQL プールを一時停止します。

## <a name="delete-azure-resources"></a>Azure リソースを削除する

Azure Synapse Analytics を調べ終わったら、不要な Azure コストを避けるために、作成したリソースを削除する必要があります。

1. Synapse Studio ブラウザー タブを閉じ、Azure portal に戻ります。
2. Azure portal の **[ホーム]** ページで、**[リソース グループ]** を選択します。
3. Synapse Analytics ワークスペースに対して **dp500-*xxxxxxx*** リソース グループ (マネージド リソース グループ以外) を選択し、そこに Synapse ワークスペース、ストレージ アカウント、ワークスペースの Spark プールが含まれていることを確認します。
4. リソース グループの **[概要]** ページの上部で、**[リソース グループの削除]** を選択します。
5. リソース グループ名として「**dp500-*xxxxxxx***」と入力し、これが削除対象であることを確認したら、 **[削除]** を選択します。

    数分後に、Azure Synapse ワークスペース リソース グループと、それに関連付けられているマネージド ワークスペース リソース グループが削除されます。
