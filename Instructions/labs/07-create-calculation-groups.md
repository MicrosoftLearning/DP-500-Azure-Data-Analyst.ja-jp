---
lab:
  title: 計算グループを作成する
  module: Design and build tabular models
---
# <a name="create-calculation-groups"></a>計算グループを作成する

## <a name="overview"></a>概要

このラボの推定所要時間: 45 分

このラボでは、Power BI Desktop と Tabular Editor 2 を使用して計算グループを作成します。

このラボでは、次の作業を行う方法について説明します。

-   計算グループを作成する。
-   計算項目の書式を設定する。
-   計算グループの優先順位を設定する。
-   計算グループを使用するようにビジュアルを構成する。

## <a name="get-started"></a>はじめに
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

## <a name="prepare-your-environment"></a>環境を準備する

Tabular Editor 2 のインストール、Power BI Desktop の設定、データ モデルの確認、メジャーの作成を行って、ラボ環境を準備します。

### <a name="download-and-install-tabular-editor-2"></a>Tabular Editor 2 をダウンロードしてインストールする

計算グループを作成できるようにするために、Tabular Editor 2 をダウンロードしてインストールします。

**重要:** *VM 環境に Tabular Editor 2 が既にインストールされている場合は、次のタスクに進みます。*

"Tabular Editor は、Analysis Services と Power BI 用の表形式モデルを作成するためのエディター代替ツールです。Tabular Editor 2 は、モデル内のデータにアクセスせずに BIM ファイルを編集できるオープンソース プロジェクトです。"**

1.  Power BI Desktop が閉じられていることを確認します。

1.  Microsoft Edge で、Tabular Editor のリリース ページに移動します。

    ```https://github.com/TabularEditor/TabularEditor/releases```
    
1. Scroll down to the <bpt id="p1">**</bpt>Assets<ept id="p1">**</ept> section and select the <bpt id="p2">**</bpt>TabularEditor.Installer.msi<ept id="p2">**</ept> file. This will initiate the file install.

1. 完了したら、 **[ファイルを開く]** を選択してインストーラーを実行します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/calculationgroups-downloadTE.png)

1.  Tabular Editor インストーラー ウィンドウで、 **[次へ]** を選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image2.png)

1.  **[使用許諾契約書]** ステップで、同意する場合は **[同意する]** をオンにし、 **[次へ]** を選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image3.png)

1.  **[インストール フォルダーの選択]** ステップで、 **[次へ]** を選択します。

    ![中程度の信頼度で自動的に生成された形状の説明](../images/image4.png)

1.  **[アプリケーション ショートカット]** ステップで、 **[次へ]** を選択します。

    ![中程度の信頼度で自動的に生成された形状の説明](../images/image4.png)

1. **[インストールの確認]** ステップで、 **[次へ]** を選択します。

    ![中程度の信頼度で自動的に生成された形状の説明](../images/image4.png)

1. **[ユーザー アカウント制御]** ポップアップ ウィンドウが表示されたら、 **[はい]** を選択します。

1. インストールが完了したら、**[閉じる]** を選択します。

    ![自動生成された形状の説明を含む図](../images/image5.png)

    "以上で、Tabular Editor がインストールされ、Power BI Desktop 外部ツールとして登録されました。"**

### <a name="set-up-power-bi-desktop"></a>Power BI Desktop を設定する

次に、事前に開発された Power BI Desktop ソリューションを開きます。

1.  エクスプローラーで、**D:\\DP500\\Allfiles\\07\\Starter** フォルダーに移動します。

2.  事前に作成された Power BI Desktop ファイルを開くには、**Sales Analysis - Create a dataflow.pbix** ファイルをダブルクリックします。

3.  ファイルを保存するには、 **[ファイル]** リボン タブで **[名前を付けて保存]** を選択します。

4.  **[名前を付けて保存]** ウィンドウで、**D:\\DP500\\Allfiles\\07\\MySolution** フォルダーに移動します。

5.  **[保存]** を選択します。

6.  **[外部ツール]** リボン タブを選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image7.png)

7.  このリボン タブから Tabular Editor を起動できることに注意してください。

    ![テキスト 低い信頼度で自動的に生成された説明](../images/image8.png)

    "次の演習では、Tabular Editor を使用して計算グループを作成します。"**

### <a name="review-the-data-model"></a>データ モデルを確認する

計算グループがこのモデルにどのように適用されるかを理解するために、データ モデルを確認します。

1.  Power BI Desktop で、左側にある **[モデル]** ビューに切り替えます。

    ![](../images/image9.png)

2.  モデル図を使って、モデルのデザインを確認します。

    ![グラフィカル ユーザー インターフェイス、図 自動的に生成された説明](../images/image10.png)

    "モデルは 7 つのディメンション テーブルと 2 つのファクト テーブルで構成されます。**Sales** ファクト テーブルには販売注文の詳細が格納されます。**Currency Rate** ファクト テーブルには、複数の通貨の為替レートが格納されます。これは、クラシック スター スキーマ設計です。"**

3.  **レポート** ビューに切り替えます。

    ![](../images/image11.png)

4.  **[フィールド]** ペイン (右側にあります) で、 **[Sales]** テーブルを展開してフィールドを確認します。

    ![テキスト 低い信頼度で自動的に生成された説明](../images/image12.png)

5.  **Sales** テーブルの 2 つのフィールドは、シグマ記号 (∑) で修飾されていることに注意してください。

    "シグマ記号は、sum、count、average などの集計関数を使用してフィールドが自動的に集計されることを示します。"**

    "ただし、計算グループをモデルに追加する場合、この自動的な動作を無効にする必要があります。つまり、集計は、データ分析式 (DAX) 式を使用して定義されるメジャーによってのみ実現できます。次のタスクでは、モデルにメジャーを追加します。"**

### <a name="create-measures"></a>メジャーを作成する

計算グループの作成の準備として、売上に関係する 3 つのメジャーを作成します。

1.  **[フィールド]** ペインで、 **[Sales]** テーブルを右クリックし、 **[新しいメジャー]** を選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image13.png)

2.  数式バー (リボンの下にあります) で、テキストを次のメジャー定義に置き換え、**Enter** キーを押します。

    ヒント: すべての数式は、**D:\\DP500\\Allfiles\\07\\Assets\\Snippets.txt** からコピーして貼り付けることができます。

    DAX

    ```Sales = SUM ( 'Sales'[Sales Amount] )```

3.  **[メジャー ツール]** コンテキスト リボンの **[書式設定]** グループ内で、小数点以下の桁数を **2** に設定します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image14.png)

4.  次の定義を使用して、**Cost** という名前の 2 番目のメジャーを作成し、同様に書式設定します。

    DAX

    ```Cost = SUM ( 'Sales'[Total Product Cost] )```

5.  次の定義を使用して、**Profit** という名前の 3 番目のメジャーを作成し、同様に書式設定します。

    DAX

    ```Profit = [Sales] - [Cost]```

6.  **[フィールド]** ペインで、 **[Sales Amount]** フィールドを右クリックし、 **[非表示]** を選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image15.png)

7.  **Total Product Cost** フィールドも非表示にします。

8.  以上で、**Sales** テーブルが **[フィールド]** ペインの一覧で最初に表示され、複数の電卓アイコンで修飾されます。

    ![アプリケーション 低い信頼度で自動的に生成された説明](../images/image16.png)

    "テーブルが表示可能なメジャーのみで構成されている場合、ペインの上部に表示されます。これにより、メジャー グループ (多次元モデルのオブジェクト) のように動作します。この表形式モデルの外観と DAX 計算グループを混同しないでください。"**

## <a name="create-a-calculation-group"></a>計算フィールドを作成する

Now you'll create two calculation groups. The first will support time intelligence. The second will support currency conversion.

### <a name="create-the-time-intelligence-calculation-group"></a>Time Intelligence 計算グループを作成する

Use Tabular Editor to create the <bpt id="p1">**</bpt>Time Intelligence<ept id="p1">**</ept> calculation group. It will simplify the creation of many time-related calculations, including PY (prior year), YoY (year-over-year), and YoY % (year-over-year percentage). The calculation group will allow analyzing any measure by using different Time Intelligence calculations.

"Power BI Desktop では、計算グループの作成または管理はサポートされていません。"**

   > **ヒント**: すべての構文は、D:\DP500\Allfiles\07\Assets\Snippets.txt からコピーして貼り付けることができます。

1.  **[外部ツール]** リボンで、**[Tabular Editor]** を選択します。

    ![テキスト 低い信頼度で自動的に生成された説明](../images/image17.png)

    "Tabular Editor が新しいウィンドウで開き、Power BI Desktop でホストされているデータ モデルにライブ接続されます。Tabular Editor でモデルに加えた変更は、保存するまで Power BI Desktop に反映されません。"**

2.  [Tabular Editor] ウィンドウの左側のペインで、 **[Tables]** フォルダーを右クリックし、 **[新規作成]** \> **[計算グループ]** の順に選択します。

    ![グラフィカル ユーザー インターフェイス、テキスト、アプリケーション、テーブル 自動的に生成された説明](../images/image18.png)

3.  左側のペインで、既定の名前を「**Time Intelligence**」に置き換え、**Enter** キーを押します。

4.  **Time Intelligence** テーブルを展開して開きます。

5.  **[Name]** 列を選択します。

    ![グラフィカル ユーザー インターフェイス 低い信頼度で自動的に生成された説明](../images/image19.png)

    "計算グループは、この単一の列で構成されますが、データ行は計算のグループを定義します。計算のテーマを反映するように列の名前を変更することをお勧めします。"**

6.  **[プロパティ]** ペイン (右下にあります) で、 **[名前]** プロパティを選択し、名前を「**Time Calculation**」に変更します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image20.png)

7.  計算項目を作成するには、 **[Time Intelligence]** テーブルを右クリックし、 **[新規作成]** \> **[計算項目]** の順に選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image21.png)

8.  左側のペインで、既定の名前を「**Current**」に置き換え、**Enter** キーを押します。

9.  **[式エディター]** ペイン (**[プロパティ]** ペインの上にあります) に次の式を入力します。

    DAX

    ```SELECTEDMEASURE ()```

    ![グラフィカル ユーザー インターフェイス、テキスト、アプリケーション、Word 自動的に生成された説明](../images/image22.png)

    "SELECTEDMEASURE 関数は、計算項目が評価されるときに、現在コンテキスト内にあるメジャーへの参照を返します。"**

10. **[式エディター]** ペインのツール バーで、最初のボタンを選択して変更を受け入れます。

    ![](../images/image23.png)

11. 次の数式を使用して、**PY** という名前の 2 番目の項目を作成します。

    DAX

    ```CALCULATE ( SELECTEDMEASURE (), SAMEPERIODLASTYEAR ( 'Date'[Date] ) )```

    "PY (前年度) 数式は、選択されたメジャーの前年度の値を計算します。"**

12. 次の数式を使用して、**YoY** という名前の 3 番目の項目を作成します。

    DAX
    ```
    SELECTEDMEASURE () 
        - CALCULATE ( SELECTEDMEASURE (), 'Time Intelligence'[Time Calculation] = "PY" )
    ```

    *前年度比 (YoY) 式は、選択したメジャーについて当年度と前年度の差を計算します。*

13. 次の数式を使用して、**YoY %** という名前の 4 番目の項目を作成します。

    DAX
    ```
    DIVIDE (
        CALCULATE ( SELECTEDMEASURE (), 'Time Intelligence'[Time Calculation] = "YoY" ),
        CALCULATE ( SELECTEDMEASURE (), 'Time Intelligence'[Time Calculation] = "PY" )
    )
    ```
    *対前年度変化率 (YoY %) 式は、選択したメジャーの前年度に対する変化率を計算します。*

14. **[プロパティ]** ペインで、 **[書式設定文字列式]** プロパティを次のように設定します。 
    ```
    "0.00%;-0.00%;0.00%"
    ```

    ヒント: 書式設定文字列式は、**D:\\DP500\\Allfiles\\07\\Assets\\Snippets.txt** からコピーして貼り付けることができます。

    ![グラフィカル ユーザー インターフェイス、テキスト、アプリケーション 自動的に生成された説明](../images/image24.png)

15. **[Time Intelligence]** 計算グループに 4 つの計算項目があることを確認します。

    ![テキスト 自動的に生成された説明](../images/image25.png)

16. Power BI Desktop モデルへの変更を保存するには、 **[ファイル]** メニューで、 **[保存]** を選択します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image26.png)

    **ヒント:** ツール バー ボタンを選択するか、**Ctrl + S** キーを押すこともできます。**

17. Power BI Desktop に切り替えます。

18. レポート デザイナーの上に表示された黄色のバナーに注意してください。

    ![](../images/image27.png)

19. バナーの右側にある **[今すぐ更新]** を選択します。

    ![](../images/image28.png)

    "更新では、計算グループをモデル テーブルとして作成することで、変更が適用されます。その後、計算項目がデータ行として読み込まれます。"**

20. **[フィールド]** ペインで、**Time Intelligence** テーブルを展開して開きます。

    ![テキスト 中程度の信頼度で自動的に生成された説明](../images/image29.png)

### <a name="update-the-matrix-visual"></a>マトリックス ビジュアルを更新する

次に、**Time Calculation** 列を使用するようにマトリックス ビジュアルを変更します。

1.  レポートで、マトリックス ビジュアルを選択します。

2.  次に、 **[視覚化]** ペインの **[値]** ウェルで、 **[X]** を選択し、 **[Sales Amount]** フィールドを削除します。

    ![グラフィカル ユーザーインターフェイス、テキスト、アプリケーション、電子メール 自動的に生成された説明](../images/image30.png)

3.  **[フィールド]** ペインの **[Sales]** テーブル内から **[値]** ウェルに **[Sales]** フィールドをドラッグします。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image31.png)

4.  **[フィールド]** ペインの **[Time Intelligence]** テーブル内から **[列]** ウェルに **[Time Calculation]** フィールドをドラッグします。

    ![グラフィカル ユーザー インターフェイス、アプリケーション、Word 自動的に生成された説明](../images/image32.png)

5.  マトリックス ビジュアルに、時間関係の **Sales** メジャー値が月別にグループ化されて表示されることを確認します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション、テーブル、Excel 自動的に生成された説明](../images/image33.png)

    "値の書式は、選択されたメジャーから派生します。ただし、パーセンテージ書式を生成するように **YoY %** の書式設定文字列式を設定したことを思い出してください。"**

### <a name="create-the-currency-conversion-calculation-group"></a>Currency Conversion 計算グループを作成する

Now you'll create the <bpt id="p1">**</bpt>Currency Conversion<ept id="p1">**</ept> calculation group. It will provide flexibility to convert the <bpt id="p1">**</bpt>Sales<ept id="p1">**</ept> table measures to a selected currency. It will also apply appropriate formatting for the selected currency.

1.  Power BI Desktop で、**データ** ビューに切り替えます。

    ![データ ビュー。](../images/image34.png)

2.  **[フィールド]** ペインで、**Currency** テーブルを選択します。

3.  書式設定文字列式を含む非表示列 **FormatString** が列値であることに注意してください。

    ![グラフィカル ユーザー インターフェイス 低い信頼度で自動的に生成された説明](../images/image35.png)

    "DAX 式を使用して、選択された通貨の書式設定文字列を適用します。"**

4.  Tabular Editor に切り替えます。

5.  **Currency Conversion**という名前の計算グループを作成します。

    "タスクの繰り返しにより、簡単な手順が提供されます。必要に応じて、この演習の最初のタスクの手順を参照できます。"**

    ![テキスト 自動的に生成された説明](../images/image36.png)

6.  **Name** 列の名前を **Converted Currency** に変更します。

    ![テキスト 低い信頼度で自動的に生成された説明](../images/image37.png)

7.  次の数式を使用して、**Currency Conversion** という名前の計算項目を作成します。

    DAX
    ```
    IF (
        HASONEVALUE ( 'Currency'[Currency] ),
        SUMX (
            VALUES ( 'Date'[Date] ),    CALCULATE (
                DIVIDE ( SELECTEDMEASURE (), MAX ( 'Currency Rate'[EndOfDayRate] ) )
            )
        )
    )
    ```
    "フィルター コンテキストに通貨が 1 つしかない場合、数式では、選択されたメジャーの日単位の値をその日の終わりのレートで除算してその合計を計算します。"**

8.  **[プロパティ]** ペインで、**[書式設定文字列式]** プロパティを次の数式に設定します。

    DAX
    ```
    SELECTEDVALUE ( 'Currency'[FormatString] )
    ```
    This formula returns the format string of the selected currency. This way, formatting is dynamically driven by the data in the <bpt id="p1">**</bpt>Currency<ept id="p1">**</ept> dimension table.

9.  Power BI Desktop モデルへの変更を保存します。

10. Power BI Desktop に切り替えて、変更を更新します。

    ![](../images/image28.png)

11. **レポート** ビューに切り替えます。

    ![](../images/image38.png)

12. マトリックス ビジュアルを選択します。

13. **[フィールド]** ペインで、 **[Currency Conversion]** テーブル内の **Converted Currency** フィールドを **[フィルター]** ペインの **[このビジュアルのフィルター]** グループにドラッグします。

    ![グラフィカル ユーザー インターフェイス、テキスト、アプリケーション 自動的に生成された説明](../images/image39.png)

14. フィルター カードで、**[通貨の換算]** をオンにします。

    ![グラフィカル ユーザー インターフェイス、テキスト、アプリケーション 自動的に生成された説明](../images/image40.png)

15. 値の書式が、米ドルの金額をわかりやすく説明するように更新されたことを確認します。

    ![テキスト 自動的に生成された説明](../images/image41.png)

16. **[通貨]** スライサーで別の通貨を選択し、マトリックス ビジュアルで、更新された値と書式を確認します。

17. **[通貨]** スライサーを **[米ドル]** に戻します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション、チーム 自動的に生成された説明](../images/image42.png)

18. ただし、**YoY %** 値はパーセンテージではなくなったことに注意してください。

    "問題があります。**Time Intelligence** 計算グループと **Currency Conversion** 計算グループの両方が適用されますが、計算の順序が正しくありません。現時点では、**YoY %** 計算が行われます。その後、為替換算によって月間の日次計算結果が追加されます。正しい結果を生成するには、計算の順序を逆にする必要があります。優先順位の値を設定することで、計算の順序を制御できます。"**

### <a name="modify-calculation-group-precedence"></a>計算グループの優先順位を変更する

次に、2 つの計算グループの優先順位を変更します。

1.  Tabular Editor に切り替えます。

2.  左側のペインで、**Time Intelligence** 計算グループを選択します。

    ![テキスト 自動的に生成された説明](../images/image43.png)

3.  **[プロパティ]** ペインで、**[計算グループの優先順位]** プロパティを **20** に設定します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション 自動的に生成された説明](../images/image44.png)

    "値が大きいほど、適用の優先順位は高くなります。したがって、優先順位が高い計算グループが先に適用されます。"**

4.  **[Currency Conversion]** 計算グループの[計算グループの優先順位] を **10** に設定します。

    ![グラフィカル ユーザー インターフェイス、アプリケーション、テーブル 自動的に生成された説明](../images/image45.png)

    "これらの構成により、**Time Intelligence** 計算が後で実行されることが保証されます。"**

5.  Power BI Desktop モデルへの変更を保存します。

6.  Power BI Desktop に切り替えます。

7.  **YoY %** 値がパーセンテージになったことに注意してください。

    ![グラフィカル ユーザーインターフェイス、テキスト 自動的に生成された説明](../images/image46.png)

### <a name="finish-up"></a>仕上げ

このタスクでは、完了作業を行います。

1.  Power BI Desktop ファイルを保存します。

    ![](../images/image47.png)

2.  Power BI Desktop を閉じます。

3.  Tabular Editor を閉じます。