---
title: Azure Data Factory で Hadoop Hive アクティビティを使用してデータを変換する | Microsoft Docs
description: Azure データ ファクトリで Hive アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Hive クエリを実行する方法について説明します。
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 637a9ce68cc1c8ac2ef0af8a606668fb4da64fb8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Azure Data Factory で Hadoop Hive アクティビティを使用してデータを変換する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-hive-activity.md)
> * [バージョン 2 - プレビュー](transform-data-using-hadoop-hive.md)

Data Factory [パイプライン](concepts-pipelines-activities.md)の HDInsight Hive アクティビティでは、[独自](compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンド](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の HDInsight クラスターで Hive クエリを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Hive アクティビティ](v1/data-factory-hive-activity.md)に関する記事をご覧ください。

Azure Data Factory の使用経験がない場合は、この記事を読む前に、「[Azure Data Factory の概要](introduction.md)」を参照し、[データの変換のチュートリアル](tutorial-transform-data-spark-powershell.md)を実行してください。 

## <a name="syntax"></a>構文

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>構文の詳細
| プロパティ            | [説明]                              | 必須 |
| ------------------- | ---------------------------------------- | -------- |
| name                | アクティビティの名前                     | [はい]      |
| 説明         | アクティビティの用途を説明するテキストです。 | いいえ        |
| 型                | Hive アクティビティの場合、アクティビティの種類は HDinsightHive です | [はい]      |
| 既定のコンテナー   | Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | [はい]      |
| scriptLinkedService | 実行する Hiveスクリプトの格納に使用される Azure Storage のリンクされたサービスへの参照。 このリンクされたサービスを指定していない場合は、HDInsight のリンクされたサービスで定義されている Azure Storage のリンクされたサービスが使用されます。 | いいえ        |
| scriptPath          | scriptLinkedService で参照される Azure Storage に格納されているスクリプト ファイルへのパスを指定します。 ファイル名は大文字と小文字が区別されます。 | [はい]      |
| getDebugInfo        | HDInsight クラスターで使用されている Azure Storage または scriptLinkedService で指定された Azure Storage にログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | いいえ        |
| arguments           | Hadoop ジョブの引数の配列を指定します。 引数はコマンド ライン引数として各タスクに渡されます。 | いいえ        |
| defines             | Hive スクリプト内で参照するキーと値のペアとしてパラメーターを指定します。 | いいえ        |

## <a name="next-steps"></a>次の手順
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)

