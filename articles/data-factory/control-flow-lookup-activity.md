---
title: Azure Data Factory でのルックアップ アクティビティ | Microsoft Docs
description: ルックアップ アクティビティを使用して外部ソースから値を検索する方法を説明します。 この出力は、後続のアクティビティによってさらに参照できます。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: f55e85bb424f4f5973fd6d633b6adf9fbca4d0ef
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory でのルックアップ アクティビティ
ルックアップ アクティビティを使用して、任意の外部ソースからレコード、テーブル名、または値を読み取ったり検索したりできます。 この出力は、後続のアクティビティによってさらに参照できます。 

ルックアップ アクティビティは、構成ファイルまたはデータ ソースからファイル、レコード、またはテーブルのリストを動的に取得する場合に役立ちます。 アクティビティからの出力は、他のアクティビティでそれらのアイテムに対してのみ特定の処理を実行するためにさらに使用できます。

> [!NOTE]
> この記事は、現在プレビュー段階にある Azure Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

現在、ルックアップでは次のデータ ソースがサポートされています。
- Azure Blob Storage の JSON ファイル
- ファイル システム内の JSON ファイル
- Azure SQL Database (クエリから変換された JSON データ)
- Azure SQL Data Warehouse (クエリから変換された JSON データ)
- SQL Server (クエリから変換された JSON データ)
- Azure Table Storage (クエリから変換された JSON データ)

ルックアップ アクティビティによって返される最大行数は **5000** であり、サイズは最大 **10MB** です。

## <a name="syntax"></a>構文

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>型のプロパティ
Name | [説明] | type | 必須
---- | ----------- | ---- | --------
dataset | ルックアップ用のデータセット参照を提供します。 現在サポートされているデータセットの種類は次のとおりです。<ul><li>[Azure Blob Storage ](connector-azure-blob-storage.md#dataset-properties)の場合 `AzureBlobDataset` (ソースとして)</li><li>[ファイル システム](connector-file-system.md#dataset-properties)の場合 `FileShareDataset` (ソースとして)</li><li>[Azure SQL Database](connector-azure-sql-database.md#dataset-properties) または [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#dataset-properties) の場合 `AzureSqlTableDataset` (ソースとして)</li><li>[SQL Server](connector-sql-server.md#dataset-properties) の場合 `SqlServerTable` (ソースとして)</li><li>[Azure Table Storage](connector-azure-table-storage.md#dataset-properties) の場合 `AzureTableDataset` (ソースとして)</li> | キーと値のペア | [はい]
source | データセット固有のソース プロパティを含みます (コピー アクティビティ ソースと同じ)。 対応する各コネクタの記事の「コピー アクティビティのプロパティ」セクションから詳細を取得します。 | キーと値のペア | [はい]
firstRowOnly | 最初の行のみまたはすべての行のどちらを返すかを示します。 | ブール | いいえ。 既定値は `true` です。

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>後続のアクティビティでルックアップ アクティビティの結果を使用する

ルックアップ結果は、アクティビティ実行結果の `output` セクションに返されます。

* **`firstRowOnly` が `true` (既定値) に設定されているときは**、出力形式は次のコードに示すとおりです。 ルックアップ結果は固定の `firstRow` キーの下にあります。 後続のアクティビティで結果を使用するには、パターン `@{activity('MyLookupActivity').output.firstRow.TableName}` を使用します。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **`firstRowOnly` が `false` に設定されているときは**、出力形式は次のコードに示すとおりです。 `count` フィールドは返されたレコードの数を示し、詳細な値は固定の `value` 配列の下に表示されます。 このような場合は通常、ルックアップ アクティビティの後に [Foreach アクティビティ](control-flow-for-each-activity.md)が続きます。 `value` 配列は、パターン `@activity('MyLookupActivity').output.value` 使用して ForEach アクティビティの `items` フィールドに渡すことができます。 `value` 配列の要素にアクセスするには、構文 `@{activity('lookupActivity').output.value[zero based index].propertyname}` を使用します。 たとえば次のようになります。`@{activity('lookupActivity').output.value[0].tablename}`

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>例
この例では、コピー アクティビティで、Azure SQL Database インスタンス内の SQL テーブルから Azure Blob Storage にデータをコピーします。 SQL テーブルの名前は、Blob Storage 内の JSON ファイルに格納されます。 ルックアップ アクティビティは、実行時にテーブル名を検索します。 このアプローチでは、パイプラインやデータセットを再デプロイすることなく JSON を動的に変更できます。 

この例では、最初の行のみのルックアップを示します。 すべての行のルックアップについて、および ForEach アクティビティで結果をチェーンするには、「[Azure Data Factory を使って複数のテーブルを一括コピーする](tutorial-bulk-copy.md)」のサンプルを参照してください。

### <a name="pipeline"></a>パイプライン
このパイプラインには、*ルックアップ*と*コピー*の 2 つのアクティビティが含まれます。 

- ルックアップ アクティビティは、Azure Blob Storage 内の場所を表す LookupDataset を使用するように設定されています。 ルックアップ アクティビティは、この場所にある JSON ファイルから SQL テーブルの名前を読み取ります。 
- コピー アクティビティは、ルックアップ アクティビティの出力 (SQL テーブルの名前) を使用します。 ソース データセット (SourceDataset) 内の tableName プロパティは、ルックアップ アクティビティからの出力を使用するように設定されています。 コピー アクティビティは、SQL テーブルから SinkDataset プロパティで指定された Azure Blob Storage 内の場所にデータをコピーします。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>ルックアップ データセット
ルックアップ データセットは、AzureStorageLinkedService タイプで指定された Azure Storage のルックアップ フォルダーにある *sourcetable.json* ファイルを参照します。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>コピー アクティビティのソース データセット
ソース データセットは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 コピー アクティビティは、この SQL テーブルから、シンク データセットで指定された Azure Blob Storage 内の場所にデータをコピーします。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>コピー アクティビティのシンク データセット
コピー アクティビティは、SQL テーブルから、AzureStorageLinkedService プロパティで指定された Azure Storage 内の *csv* フォルダーにある *filebylookup.csv* ファイルにデータをコピーします。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
このストレージ アカウントには、SQL テーブルの名前を含む JSON ファイルが格納されています。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
この Azure SQL Database インスタンスには、Blob Storage にコピーされるデータが格納されています。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>オブジェクトのセット

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>オブジェクトの配列

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
