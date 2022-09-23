---
lab:
  title: データをリアルタイムで監視する
  module: Implement advanced data visualization techniques by using Power BI
---

# <a name="monitor-data-in-real-time"></a>データをリアルタイムで監視する

## <a name="overview"></a>概要

**ラボの推定所要時間: 30 分**

In this lab, you will configure a report to use automatic page refresh. That way, it will be possible for report consumers to monitor real-time internet sales results.

このラボでは、次の作業を行う方法について説明します。

- パフォーマンス アナライザーを使用して更新アクティビティを確認する。

- ページの自動更新を設定する。

- 変更の検出機能を作成して使用する。

## <a name="get-started"></a>はじめに

この演習では、環境を準備します。

### <a name="clone-the-repository-for-this-course"></a>このコースのリポジトリを複製する

1. スタート メニューで、コマンド プロンプトを開きます

    ![](../images/command-prompt.png)

1. コマンド プロンプト ウィンドウで、次のように入力して D ドライブに移動します。

    `d:` 

   Enter キーを押します。

    ![](../images/command-prompt-2.png)


1. コマンド プロンプト ウィンドウで、次のコマンドを入力して、コース ファイルをダウンロードし、DP500 という名前のフォルダーに保存します。
    
    `git clone https://github.com/MicrosoftLearning/DP-500-Azure-Data-Analyst DP500`
   
1. リポジトリが複製されたら、コマンド プロンプト ウィンドウを閉じます。 
   
1. エクスプローラーで D ドライブを開き、ファイルがダウンロードされていることを確認します。

### <a name="set-up-the-database"></a>データベースを設定する

このタスクでは、SQL Server Management Studio (SSMS) を使用し、2 つのスクリプトを実行してデータベースをセットアップします。

1. SSMS を開くには、タスク バーで、**SSMS** のショートカットを選択します。

    ![](../images/dp500-monitor-data-in-real-time-image1.png)

2. In the <bpt id="p1">**</bpt>Connect to Server<ept id="p1">**</ept> window, ensure that the <bpt id="p2">**</bpt>Server name<ept id="p2">**</ept> dropdown list is set to <bpt id="p3">**</bpt>localhost<ept id="p3">**</ept>, and that the Authentication dropdown list is set to <bpt id="p4">**</bpt>Windows Authentication<ept id="p4">**</ept>.
    ![](../images/dp500-monitor-data-in-real-time-image2.png)

3. **[接続]** を選択します。
    
    ![](../images/dp500-monitor-data-in-real-time-image3.png)

4. スクリプト ファイルを開くには、 **[ファイル]** メニューで、 **[開く]**  >  **[ファイル]** を選択します。

5. **[ファイルを開く]** ウィンドウで、**D:\DP500\Allfiles\14\Assets** フォルダーに移動します。

6. **[1-Setup.sql]** ファイルを選択します。

    ![](../images/dp500-monitor-data-in-real-time-image4.png)

7. **[Open (開く)]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image5.png)

8. スクリプトを確認します。

    "このスクリプトでは、**FactInternetSalesRealTime** という名前のテーブルを作成します。インターネット販売注文のリアルタイム ワークロードをシミュレートするためのデータをこのテーブルに読み込むのは、別のスクリプトで行います。"**

9. スクリプトを実行するには、ツール バーで **[実行]** を選択します (または **F5** キーを押します)。

    ![](../images/dp500-monitor-data-in-real-time-image6.png)

10. ファイルを閉じるには、 **[ファイル]** メニューで **[閉じる]** を選択します。

11. **[2-InsertOrders.sql]** ファイルを開きます。

    ![](../images/dp500-monitor-data-in-real-time-image7.png)

12. このスクリプトも確認します。

    "このスクリプトでは、無限ループを実行します。各ループでは、販売注文を挿入し、1 から 15 秒のランダムな期間遅延します。"**

13. スクリプトを実行し、ラボの最後まで実行したままにします。

### <a name="set-up-power-bi-desktop"></a>Power BI Desktop を設定する

このタスクでは、事前に開発された Power BI Desktop ソリューションを開きます。

1. エクスプローラーを開くには、タスク バーで**エクスプローラー**のショートカットを選択します。

    ![](../images/dp500-monitor-data-in-real-time-image8.png)

2. **D:\DP500\Allfiles\14\Starter** フォルダーに移動します。

3. 事前に作成された Power BI Desktop ファイルを開くには、 **[Internet Sales - Monitor data in real time.pbix]** ファイルをダブルクリックします。

4. ファイルを保存するには、 **[ファイル]** リボン タブで **[名前を付けて保存]** を選択します。

5. **[名前を付けて保存]** ウィンドウで、**D:\DP500\Allfiles\14\MySolution** フォルダーに移動します。

6. **[保存]** を選択します。

### <a name="review-the-report"></a>レポートを確認する

このタスクでは、事前に作成されたレポートを確認します。

1. Power BI Desktop で、レポート ページを確認します。

    ![](../images/dp500-monitor-data-in-real-time-image9.png)

    "このレポート ページには、タイトルと 2 つのビジュアルがあります。カード ビジュアルには、販売注文件数が表示され、横棒グラフ ビジュアルには、自転車サブカテゴリごとの売上高が表示されます。"**

2. レポートを更新するには、 **[表示]** リボン タブの **[表示]** ペイン グループで、 **[パフォーマンス アナライザー]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image10.png)

3. **[パフォーマンス アナライザー]** ペイン ( **[視覚化]** ペインの右側にあります) で、 **[記録の開始]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image11.png)

    "パフォーマンス アナライザーでは、ビジュアルの更新に必要な期間を検査して表示します。各ビジュアルでは、少なくとも 1 つのクエリがソース データベースに発行されます。詳細については、「[パフォーマンス アナライザーを使用してレポート要素のパフォーマンスを確認する](https://docs.microsoft.com/power-bi/create-reports/desktop-performance-analyzer)」を参照してください。"**

4. **[ビジュアルを更新する]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image12.png)

5. レポート ビジュアルが更新され、最新のインターネット販売結果が表示されることに注意してください。

    "ローカル DirectQuery モデルに接続されるレポートを作成する場合、 **[更新]** コマンド ( **[ホーム] リボン タブにあります**) を使用してレポートを更新することはできません。これは、Power BI Desktop では、代わりに DirectQuery テーブル接続が更新されるためです。レポート ビジュアルを更新するには、先ほど行った手順に従います。Power BI サービスに発行すると、レポート コンシューマーは、アクション バーで **[更新]** を選択してレポート ビジュアルを更新できます。"**

    "リアルタイム分析用にレポートを設計する場合、ユーザーにレポート ページを常に更新するように求めるよりも効率的な方法が必要です。次の演習では、そのより効率的な方法を実現するためにページの自動更新を設定します。"**

## <a name="set-up-automatic-page-refresh"></a>ページの自動更新を設定する

この演習では、ページの自動更新を設定し、変更の検出機能を試用します。

"ページの自動更新には、DirectQuery ストレージ モードを使用するように設定された少なくとも 1 つのモデル テーブルが必要です。"**

### <a name="set-up-automatic-page-refresh"></a>ページの自動更新を設定する

このタスクでは、ページの自動更新を設定します。

1. レポート ページを選択するには、最初にレポート ページの空の領域を選択します。

2. **[視覚化]** ペインで、[書式] アイコン (ペイント ブラシ) を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image13.png)

3. **[ページの更新]** 設定 (一覧の最後) を **[オン]** に切り替えます。

    ![](../images/dp500-monitor-data-in-real-time-image14.png)

    "ページの自動更新はページレベルの設定です。これは、レポートの特定のページに対して有効にすることができます。"**

4. **[パフォーマンス アナライザー]** ペインで、レポート ビジュアルが更新されたばかりであることに注意してください。

5. **[視覚化]** ペインで、 **[ページの更新]** 設定を展開して開きます。

    ![](../images/dp500-monitor-data-in-real-time-image15.png)

6. 既定では、ページは 30 分ごとに更新されることに注意してください。

7. この設定を、5 秒ごとにページが更新されるように変更します。

    ![](../images/dp500-monitor-data-in-real-time-image16.png)

    "重要: このように更新する間隔を頻繁にすると、このラボの作業を効率的に行うことができます。ただし、このような頻繁な更新間隔を設定すると、ソース データベースのパフォーマンスや、レポートを表示する他のユーザーに重大な影響を与える可能性があるため、注意が必要です。"**

    "インターネット販売注文は 1 から 15 秒ごとに読み込まれるため、場合によっては、ページを更新しても同じ結果が取得されることがあります (過去 5 秒間にデータベースに注文が記録されなかったため)。可能であれば、必要なときにのみレポート ビジュアルが更新されるようにします。次のタスクでは、これを行うために変更の検出機能を設定します。"**

    "Power BI サービスに発行した後、更新間隔を 30 分未満にするには、Premium 容量に割り当てられたワークスペースにレポートを保存する必要があります。また、容量管理者は、このような頻繁な間隔に対応できる容量を有効にして設定する必要があります。詳細については、「[Power BI でのページの自動更新](https://docs.microsoft.com/power-bi/create-reports/desktop-automatic-page-refresh)」を参照してください。"**

### <a name="set-up-change-detection"></a>変更の検出を設定する

このタスクでは、変更の検出を設定します。

1. **[ページの更新]** 設定で、 **[更新の種類]** ドロップダウン リストを **[変更の検出]** に設定します。

    ![](../images/dp500-monitor-data-in-real-time-image17.png)

2. 変更の検出のメジャーを作成するには、 **[変更の検出の追加]** リンクを選択します。

    ![](../images/dp500-monitor-data-in-real-time-image18.png)

3. **[変更の検出]** ウィンドウで、既定の設定が、新しいメジャーの作成になっていることに注意してください。

    ![](../images/dp500-monitor-data-in-real-time-image19.png)

4. **[計算の選択]** ドロップダウン リストで、 **[カウント (個別)]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image20.png)

5. **[フィールド]** ペイン (ウィンドウ内の右側にあります) で、下にスクロールして **[Internet Sales]** テーブルを見つけます。

6. **[Internet Sales]** フィールドを選択します。ウィンドウで、このフィールドが **[Choose a field to apply it to](適用対象のフィールを選択する)** に追加されることに注意してください。

    ![](../images/dp500-monitor-data-in-real-time-image21.png)

7. **[変更を確認する間隔]** 設定で、5 秒に設定します。

    ![](../images/dp500-monitor-data-in-real-time-image22.png)

8. **[適用]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image23.png)

9. **[フィールド]** ペインで、 **[Internet Sales]** テーブル内に、変更の検出のメジャーが追加されていることに注意してください。

    ![](../images/dp500-monitor-data-in-real-time-image24.png)

    "Power BI で、変更の検出のメジャーを使用して 5 秒ごとにソース データベースのクエリが実行されるようになりました。毎回、Power BI によって結果が格納されるため、次回使用するときに比較できます。結果が異なる場合、データが変更されたことを意味します (この場合、データベースには、新しい販売注文が挿入されています)。この場合、Power BI により、すべてのレポート ページ ビジュアルが更新されます。"**

    "Power BI サービスに発行すると、Power BI では、Premium 容量に関する変更の検出のメジャーのみがサポートされます。"**

10. **[パフォーマンス アナライザー]** ペインで、 **[クリア]** を選択します。

    ![](../images/dp500-monitor-data-in-real-time-image25.png)

11. パフォーマンス アナライザーに変更の検出クエリが表示されることに注意してください。

12. Power BI Desktop でレポート ビジュアルが更新される前に、複数の変更の検出クエリが発生する場合があることに注意してください。

    "これは、その時点で、データベースに挿入された新しいインターネット販売注文がないためです。レポート ビジュアルは必要なときにのみ更新されるため、この設定によって効率が向上しました。"**

### <a name="finish-up"></a>仕上げ

このタスクでは、完了作業を行います。

1. Power BI Desktop ファイルを保存します。

    ![](../images/dp500-monitor-data-in-real-time-image26.png)

2. Power BI Desktop を閉じます。

3. SSMS で、スクリプトの実行を停止するには、ツール バーで **[停止]** を選択します (または **Alt + Break** キーを押します)。

    ![](../images/dp500-monitor-data-in-real-time-image27.png)

4. スクリプト ファイルを閉じます。

5. **[3-Cleanup.sql]** ファイルを開きます。

    ![](../images/dp500-monitor-data-in-real-time-image28.png)

    "このスクリプトでは、**FactInternetSalesRealTime** テーブルを削除します。"**

6. スクリプトを実行します。

7. SSMS を閉じます。
