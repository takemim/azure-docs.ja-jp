1. 新しいブラウザー ウィンドウで、[Azure Portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]** > **[データベース]** > **[Azure Cosmos DB]** の順にクリックします。
   
   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. **[新しいアカウント]** ページで、新しい Azure Cosmos DB アカウントの設定を入力します。 
 
    Setting|推奨値|[説明]
    ---|---|---
    ID|<*一意の名前を入力*>|この Azure Cosmos DB アカウントを識別するための一意の名前を入力します。 指定した ID に *documents.azure.com* が付加されて URI が作成されるので、ID は一意であっても識別可能なものを使用してください。<br><br>ID に含めることができるのは英小文字、数字、ハイフン (-) のみ、3 文字以上で 50 文字以内にする必要があります。
    API|SQL|API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、アプリケーションのニーズに応じて、SQL (ドキュメント データベース)、Gremlin (グラフ データベース)、MongoDB (ドキュメント データベース)、Azure Table、および Cassandra の 5 つの API が用意されています。現時点では、それぞれ別個のアカウントが必要です。 <br><br>このクイックスタートでは、SQL 構文を使ってクエリを実行し、SQL API でアクセスできるドキュメント データベースを作成しているので、**[SQL]** を選びます。<br><br>[SQL API について詳しくは、こちらをご覧ください](../articles/cosmos-db/documentdb-introduction.md)|
    [サブスクリプション]|*該当するサブスクリプション*|この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。 
    リソース グループ|新規作成<br><br>*上記の ID で指定したものと同じ一意の名前を入力*|**新規作成**を選択してから、自分のアカウントの新しいリソースグループの名前を入力します。 簡略化のため、ID と同じ名前を使用することができます。 
    場所|<*ユーザーに最も近いリージョンを選択*>|Azure Cosmos DB アカウントをホストする地理的な場所を入力します。 データに最も高速にアクセスできる、ユーザーに最も近い場所を使用します。
    Geo 冗長の有効化| 空白 | データベースのレプリケート バージョンが 2 番目 (ペア) のリージョンに作成されます。 空白のままにします。  
    [ダッシュボードにピン留めする] | elect | このボックスを選択すると、新しいデータベース アカウントが、アクセスしやすいようにポータルのダッシュボードに追加されます。

    **[Create]**をクリックします。

    ![Azure Cosmos DB の新しいアカウント ページ](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

4. アカウントの作成には数分かかります。 アカウントの作成中に、ポータルの右側に **[Deploying Azure Cosmos DB]\(Azure Cosmos DB のデプロイ\)** タイルが表示されます。タイルを表示するのに、ダッシュボードを右にスクロールしなければならない場合もあります。 スクリーンの上部に進行状況バーも表示されます。 いずれかの領域で進行状況を確認できます。 

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount/deploying-cosmos-db.png)

    アカウントが作成されると、**[Azure Cosmos DB アカウントが作成されました]** ページが表示されます。 

