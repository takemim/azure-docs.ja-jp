---
title: Stream Analytics Visual Studio Tools を使用した継続的インテグレーションおよびデプロイ プロセスの設定 | Microsoft Docs
description: Visual Studio の Stream Analytics ツールを使用して Stream Analytics Edge ジョブをオーサリング、デバッグ、および作成するためのチュートリアル。
keywords: Visual Studio, NuGet, DevOps, Edge jobs, Stream analytics
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
editor: ''
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/13/2018
ms.author: sujie
ms.openlocfilehash: 9362b201fbabc9f8f43647dfd8ac62986b5b6790
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="develop-stream-analytics-edge-jobs-by-using-visual-studio-tools"></a>Visual Studio ツールを使用して Stream Analytics Edge ジョブを作成する

このチュートリアルでは、Visual Studio の Stream Analytics ツールを使用して、Stream Analytics ジョブのオーサリング、デバッグ、および作成を行う方法について説明します。 ジョブを作成してテストした後、 Azure Portal に移動して、それをデバイスに配置できます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)、[Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)、または [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326) をインストールします。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。  

* [インストール手順](stream-analytics-tools-for-visual-studio-edge-jobs.md)に従って、Visual Studio の Stream Analytics ツールをインストールします。
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge プロジェクトを作成する 

Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]** を選択します。 左側の **テンプレート**の一覧に移動し、**[Azure Stream Analytics]** > **[Stream Analytics Edge]** > **[Azure Stream Analytics Edge Application]** を選択します。 プロジェクトの名前、場所、およびソリューション名を入力し、**[OK]** を選択します。

![新しい Edge プロジェクト](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

プロジェクトが作成されたら、**ソリューション エクスプローラー**に移動して、フォルダー階層を表示します。

![ソリューション エクスプローラーの表示](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>適切なサブスクリプションを選択する

1. Visual Studio の **[表示]** メニューで **[サーバー エクスプローラー]** を選択します。  

2. **[Azure]** 右クリックし、**[Microsoft Azure サブスクリプションへの接続]** を選択します。その後、Azure アカウントでログインします。

## <a name="define-inputs"></a>入力を定義する

1. **ソリューション エクスプローラー**で、**[入力]** ノードを展開します。**EdgeInput.json** という名前の入力が表示されます。 ダブルクリックしてその設定を表示します。  

2. [ソースの種類] が **[Data Stream]** に、[ソース] が **[Edge Hub]** に、[イベントシリアル化形式] が **[Json]** に、[エンコード] が **[UTF8]** に設定されていることを確認します。 必要に応じて **[入力のエイリアス]**の名前を変更できますが、この例ではそのままにします。 入力の別名の名前を変更する場合は、クエリを定義するときに指定した名前を使用します。 **[保存]** を選択して設定を保存します。  
   ![入力の構成](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>出力を定義する

1. **ソリューション エクスプローラー**で、**[出力]** ノードを展開します。**EdgeOutput.json** という名前の出力が表示されます。 ダブルクリックしてその設定を表示します。  

2. [シンク] が **[Edge Hub]** に、[イベントシリアル化形式] が **[Json]** に、[エンコード] が **[UTF8]** に、[フォーマット] が **[配列]** に設定されていることを確認します。 必要に応じて **[出力のエイリアス]** の名前を変更できますが、この例ではそのままにします。 出力の別名の名前を変更する場合は、クエリを定義するときに指定した名前を使用します。 **[保存]** を選択して設定を保存します。 
   ![出力の構成](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>変換クエリを定義する

Edge 環境に配置する Stream Analytics ジョブは、[Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)の大半をサポートしますが、Edge ジョブでは次の操作はまだサポートされていません。 


|**カテゴリ**  | **コマンド**  |
|---------|---------|
|地理空間演算子 |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|その他の演算子 | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>COUNT 演算子の Expression パラメーター</li><li>日付と時刻関数のマイクロ秒</li><li>JavaScript UDA (この機能は、クラウドに配置されるジョブに対しては、まだプレビュー段階です)</li></ul>   |

ポータルで Edge ジョブを作成するときに、サポートされている演算子を使用していない場合は、コンパイラによって自動的に警告が表示されます。

Visual Studio のクエリ エディターで、次の変換クエリを定義します (**script.asaql ファイル**)。

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>ローカル ジョブをテストする

クエリをローカルでテストするには、サンプル データをアップロードする必要があります。 サンプル データは、[GitHub リポジトリ](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json)から登録データをダウンロードしてローカル コンピューターに保存することで取得できます。 

1. サンプル データをアップロードするには、**EdgeInput.json** ファイル右クリックし、**[ローカル入力の追加]** を選択します。  

2. ポップアップ ウィンドウで、ローカル パスからサンプル データを**参照**し、**[保存]** を選択します。
   ![ローカル入力の構成](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. **local_EdgeInput.json** という名前のファイルが、入力フォルダーに自動的に追加されます。  
4. ローカルで実行するか、Azure に送信できます。 クエリをテストするには、**[ローカルで実行]** を選択します。  
   ![実行オプション](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. コマンド プロンプト ウィンドウに、ジョブの状態が表示されます。 ジョブが正常に実行されると、プロジェクト フォルダーのパス "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" に “2018-02-23-11-31-42” のようなフォルダーが作成されます。 フォルダー パスに移動して、ローカル フォルダー内の結果を表示します。

   Azure Portal にサインインし、ジョブが作成されたことを確認することもできます。 

   ![結果フォルダー](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>ジョブを Azure に送信する

1. Azure にジョブを送信する前に、Azure サブスクリプションに接続する必要があります。 **サーバー エクスプローラー**を開きます。**[Azure]** を右クリックし、**[Microsoft Azure サブスクリプションへの接続]** を選択し、Azure サブスクリプションにサインインします。  

2. Azure にジョブを送信するには、クエリ エディターに移動し、**[Azure に送信]** を選択します。  

3. ポップアップ ウィンドウが開き、既存の Edge ジョブを更新するか、新しく作成することを選択できます。 既存のジョブを更新すると、すべてのジョブの構成が置き換えられます。このシナリオでは、新しいジョブを発行します。 **[新しい Azure Stream Analytics ジョブの作成]** を選択し、**MyASAEdgeJob** のようなジョブの名前を入力し、必要な **[サブスクリプション]**、**[リソース グループ]**、および **[場所]** を選択し、**[送信]** を選択します。

   ![Azure に送信](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   これで、Stream Analytics Edge job ジョブが作成されました。[IoT Edge でのジョブの実行に関するチュートリアル](stream-analytics-edge.md)を参照して、デバイスへの配置方法を確認できます。 

## <a name="manage-the-job"></a>ジョブを管理する 

サーバー エクスプローラーで、ジョブの状態とジョブ ダイアグラムを表示できます。 **サーバー エクスプローラー**で、**[Stream Analytics]** を展開し、Edge ジョブを配置したサブスクリプションとリソース グループを展開します。MyASAEdgejob の状態が **[作成済み]** であることを確認できます。 ジョブ ノードを展開し、ノードをダブルクリックしてジョブ ビューを開きます。

![サーバー エクスプローラーのオプション](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
ジョブの表示ウィンドウでは、ジョブの更新、ジョブの削除、Azure Portal からのジョブのオープンなどの操作を実行できます。

![ジョブ ダイアグラムとその他のオプション](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>次の手順

* [Azure IoT Edge の詳細](../iot-edge/how-iot-edge-works.md)
* [ASA on IoT Edge チュートリアル](../iot-edge/tutorial-deploy-stream-analytics.md)
* [このアンケートを使用してフィードバックをチームに送信する](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
