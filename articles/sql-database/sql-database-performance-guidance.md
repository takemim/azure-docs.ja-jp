---
title: Azure SQL Database のパフォーマンス チューニング ガイダンス | Microsoft Docs
description: 推奨事項を使用して、Microsoft Azure SQL Database のクエリ パフォーマンスを向上する方法について説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 63a8b9f8c81ad3dc122bf25d8a06cdf242a0f35b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="tuning-performance-in-azure-sql-database"></a>Azure SQL Database のパフォーマンスのチューニング

Azure SQL Database には、データベースのパフォーマンス向上に使用できる[推奨事項](sql-database-advisor.md)が用意されています。また、Azure SQL Database が[自動的にアプリケーションに適応](sql-database-automatic-tuning.md)し、ワークロードのパフォーマンスを向上する変更を適用できるようにすることが可能です。

適切な推奨事項がなく、パフォーマンスの問題が残っている場合は、次の方法を使用してパフォーマンスを向上させることができる可能性があります。
1. [サービス レベル](sql-database-service-tiers.md)を引き上げ、データベースにより多くのリソースを提供します。
2. アプリケーションを調整し、パフォーマンスを向上させるベスト プラクティスを適用します。 
3. データをより効率的に処理するようにインデックスとクエリを変更して、データベースをチューニングします。

どの[サービス レベル](sql-database-service-tiers.md)を選択するかを判断したり、アプリケーションまたはデータベースのコードを書き換えて変更をデプロイしたりする必要があるため、これらは手動による方法です。

## <a name="increasing-performance-tier-of-your-database"></a>データベースのパフォーマンス レベルを引き上げる

Azure SQL Database には、4 つの[サービス レベル](sql-database-service-tiers.md)が用意されており、Basic、Standard、Premium から選択できます (パフォーマンスはデータベース スループット単位 ([DTU](sql-database-what-is-a-dtu.md)) で測定されます)。 各サービス レベルでは、SQL データベースで使用できるリソースが厳密に分離されており、そのサービス レベルの予測可能なパフォーマンスが保証されています。 この記事では、アプリケーションに適したサービス レベルを選択する際に役立つガイダンスを提供します。 さらに、Azure SQL Database を最大限活用できるようにアプリケーションを調整する方法について説明します。

> [!NOTE]
> この記事では、Azure SQL Database のデータベースが 1 つの場合のパフォーマンス ガイダンスについて説明しています。 エラスティック プールに関連するパフォーマンス ガイダンスについては、[エラスティック プールの価格とパフォーマンスに関する考慮事項](sql-database-elastic-pool-guidance.md)に関するトピックを参照してください。 ただし、この記事に記載されている調整の推奨事項の多くは、エラスティック プールのデータベースにも当てはまり、パフォーマンスに関して同様のメリットが得られることに注意してください。
> 

* **Basic**: Basic サービス レベルでは、各データベースのパフォーマンスを時間単位で正確に予測できます。 Basic データベースでは、複数の同時要求がない小さなデータベースでの高いパフォーマンスが、十分なリソースによってサポートされています。 Basic サービス レベルを使用する一般的なユース ケースは次のとおりです。
  * **Azure SQL Database の使用を始めたばかりである**。 多くの場合、開発中のアプリケーションは高いパフォーマンス レベルを必要としません。 Basic データベースは、低価格という点でデータベース開発やテストに最適な環境です。
  * **データベースのユーザーが 1 人である**。 1 人のユーザーがデータベースに関連付けられるアプリケーションでは通常、同時性とパフォーマンスの要件が高くありません。 これらのアプリケーションの場合、Basic サービス レベルが候補となります。
* **Standard**: Standard サービス レベルではパフォーマンス予測機能が向上しており、ワークグループや Web アプリケーションなど、複数の同時要求があるデータベースで高いパフォーマンスが実現されます。 Standard サービス レベルのデータベースを選択すると、分単位の予測可能パフォーマンスに基づいてデータベース アプリケーションをサイズ調整できます。
  * **データベースに複数の同時要求がある**。 複数のユーザーに同時にサービスを提供するアプリケーションは通常、より高いパフォーマンス レベルを必要とします。 たとえば、複数の同時クエリをサポートしている低～中程度の I/O トラフィック要件があるワークグループまたは Web アプリケーションは、Standard サービス レベルが候補として適しています。
* **Premium**: Premium サービス レベルでは、Premium データベースごとに秒単位で予測できるパフォーマンスが提供されます。 Premium サービス レベルを選択すると、そのデータベースのピーク時の負荷に基づいてデータベース アプリケーションをサイズ調整できます。 このプランを利用すると、待機時間が重要な操作において、パフォーマンス差異が原因で小規模なクエリに予想以上の時間がかかることがなくなります。 このモデルは、ピーク時のリソース ニーズ、パフォーマンス差異、またはクエリ待機時間を高い精度で予測するために必要なアプリケーションの開発と製品検証の周期を大幅に単純化できます。 Premium サービス レベルのほとんどのユース ケースには、次の特性が 1 つ以上含まれています。
  * **高いピーク負荷**。 操作の完了に多くの CPU、メモリ、または入力/出力 (I/O) を必要とするアプリケーションでは、専用の高いパフォーマンス レベルが必要になります。 たとえば、長時間にわたって複数の CPU コアが使用されることがわかっているデータベース操作では、Premium サービス レベルが候補となります。
  * **多数の同時要求**。 トラフィック量が多い Web サイトにサービスを提供するなど、一部のデータベース アプリケーションは多くの同時要求にサービスを提供します。 Basic サービス レベルと Standard サービス レベルの場合、データベースごとに同時要求の数に制限があります。 より多くの接続を必要とするアプリケーションでは、必要な要求の最大数を処理できるように、適切な予約サイズを選択する必要があります。
  * **低待機時間**。 一部のアプリケーションでは、データベースからの応答時間を最小限にする必要があります。 広範なユーザー操作の一環として特定のストアド プロシージャが呼び出されるとき、99% の割合で 20 ミリ秒以内にその呼び出しから応答を得ることが要求される場合があります。 この種類のアプリケーションでは、Premium サービス レベルを活用することで必要なコンピューティング パワーを確実に得られます。

SQL データベースに必要なサービス レベルは、リソース ディメンションごとのピーク負荷要件に基づきます。 アプリケーションによっては、ある 1 つのリソースはほとんど使用せず、他のリソースは大量に使用するものがあります。

### <a name="service-tier-capabilities-and-limits"></a>サービス層の機能と制限

各サービス レベルでは、パフォーマンス レベルを設定します。これにより、必要な容量に対してのみ料金を支払うことができる柔軟性が得られます。 ワークロードの変化に応じて、レベルを上げたり下げたりして[容量を調整](sql-database-service-tiers.md)できます。 たとえば、新学期の買い物シーズンにデータベースのワークロードが高くなる場合、7 月から 9 月までの指定の期間、データベースのパフォーマンス レベルを上げることができます。 ピークのシーズンが過ぎたら、パフォーマンス レベルを下げることができます。 ビジネスの季節性に合わせてクラウド環境を最適化することで、支払いを最小限に抑えることができます。 このモデルはソフトウェア製品のリリース周期にも適しています。 テスト チームは、テストの実行中に容量を割り当て、テストが完了したらその容量を解放できます。 容量要求モデルでは、必要な分の容量に対して料金を支払い、使われることがほとんどない専用のリソースに対する支出を回避します。

### <a name="why-service-tiers"></a>サービス レベルを使用する理由
それぞれのデータベース ワークロードが変化する中でサービス レベルを使用する目的は、各種のパフォーマンス レベルでパフォーマンス予測可能性を提供することにあります。 データベース リソース要件の規模が大きなユーザーは、より専用度の高いコンピューティング環境で作業できます。

## <a name="tune-your-application"></a>アプリケーションの調整
従来のオンプレミス SQL Server では多くの場合、初回の容量計画のプロセスは、運用環境でアプリケーションを実行するプロセスから分離されます。 最初にハードウェアと製品ライセンスが購入され、パフォーマンス調整は後で行われます。 Azure SQL Database を使用する場合、アプリケーションの実行と調整のプロセスを組み合わせることをお勧めします。 オンデマンド容量の支払いモデルでは、現在必要とされる最小のリソースを使用するようにアプリケーションを調整できます。(不正確なことが多い) アプリケーションの将来的な成長計画の推測に基づいて、ハードウェアに過剰プロビジョニングを行うことはしません。 アプリケーションを調整しないでハードウェア リソースを過剰にプロビジョニングすることを選ぶユーザーもいます。 この方法は、利用が集中する期間に重要なアプリケーションの変更を望まない場合に適していることがあります。 しかし、アプリケーションを調整することで、リソース要件を最小限に抑え、Azure SQL Database のサービス レベルを使用する際に毎月の請求額を抑えることができます。

### <a name="application-characteristics"></a>アプリケーションの特性
Azure SQL Database のサービス レベルは、アプリケーションのパフォーマンスの安定性と予測可能性を高めるように設計されています。一方で、いくつかのベスト プラクティスを実践することで、パフォーマンス レベル内でリソースを最大限に活用するようアプリケーションを調整できます。 多くのアプリケーションは、上位のパフォーマンス レベルまたはサービス レベルに切り替えることでパフォーマンスが大幅に向上します。とはいえ、アプリケーションによっては、上位のサービス レベルの利点を活かすためにさらに調整が必要になります。 次のような特性を備えたアプリケーションでは、パフォーマンスを向上させるためにアプリケーションに調整を加えることを検討してください。

* **"煩雑な" 動作が原因でパフォーマンスの低いアプリケーション**。 煩雑なアプリケーションでは、ネットワークの待機時間が重要なデータ アクセス操作が過度に発生します。 この種のアプリケーションでは、SQL データベースに対するデータ アクセス操作の数を減らすよう変更を施す必要があります。 たとえば、アドホック クエリを一括処理したり、ストアド プロシージャにクエリを移動したりするなどの手法を使って、アプリケーションのパフォーマンスを向上させることができます。 詳細については、「 [バッチ クエリ](#batch-queries)」を参照してください。
* **単一のマシンではサポートし切れないほどワークロードが集中するデータベース**。 最高の Premium パフォーマンス レベルのリソースを超えるデータベースでは、ワークロードのスケールアウトを活用できる場合があります。 詳細については、「[データベース間のシャーディング](#cross-database-sharding)」と「[機能的パーティション分割](#functional-partitioning)」を参照してください。
* **最適でないクエリを含むアプリケーション**。 クエリが十分に調整されていない (特にデータ アクセス層の) アプリケーションの場合、上位のパフォーマンス レベルの利点を活かせないことがあります。 たとえば、WHERE 句がない、インデックスが足りない、統計が古いクエリです。 これらのアプリケーションの場合、クエリ パフォーマンスの標準的な調整方法で効果が得られます。 詳細については、「[インデックスの不足](#identifying-and-adding-missing-indexes)」と「[クエリの調整とヒント](#query-tuning-and-hinting)」を参照してください。
* **データ アクセス設計が最適ではないアプリケーション**。 デッドロックなど、データ アクセスの同時性問題が内在するアプリケーションの場合、上位のパフォーマンス レベルの利点を活かせないことがあります。 Azure キャッシュ サービスや他のキャッシング技術を利用し、クライアント側でデータをキャッシュすることで、Azure SQL Database に対するラウンド トリップを減らすことを検討してください。 詳しくは、「 [アプリケーション層のキャッシュ](#application-tier-caching)」を参照してください。

## <a name="tune-your-database"></a>データベースの調整
このセクションでは、Azure SQL Database を調整するいくつかの手法について説明します。これらの手法を使用すると、アプリケーションから最良のパフォーマンスを引き出し、可能な限り下位のパフォーマンス レベルでアプリケーションを実行することができます。 これらの手法の一部は従来の SQL Server 調整のベスト プラクティスと同じですが、その他のものは Azure SQL Database に固有です。 場合によっては、データベースで使用されるリソースを調べ、さらに調整すべき領域を見つけることができるほか、従来の SQL Server 手法を拡大し、Azure SQL Database に応用することができます。

### <a name="identify-performance-issues-using-azure-portal"></a>Azure Portal を使用したパフォーマンスの問題の特定
Azure Portal の次のツールを使用すると、SQL データベースのパフォーマンスの問題を分析して修正する際に役立ちます。

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

これらの両方のツールに関する詳細情報とその使用方法は、Azure Portal にあります。 問題を効率的に診断して解決するには、まず Azure Portal のツールを試してみることをお勧めします。 (インデックスの不足とクエリの調整について) 次に説明する手動の調整手法は、特殊なケースで使用することをお勧めします。

Azure SQL Database の問題の特定方法の詳細については、[パフォーマンスの監視](sql-database-single-database-monitor.md)に関する記事を参照してください。

### <a name="identifying-and-adding-missing-indexes"></a>不足しているインデックスの識別と追加
OLTP データベースのパフォーマンスの一般的問題は物理的なデータベース設計に関連します。 多くの場合、データベース スキーマは (負荷またはデータ量の) 規模の面で試験することなく設計され、出荷されます。 残念ながら、クエリ プランのパフォーマンスは、規模が小さい場合には許容されることがあるものの、実稼働レベルのデータ量では大幅に低下する可能性があります。 この問題の最も一般的な原因は、適切なインデックスがなく、クエリのフィルターまたはその他の制約を満たせないことにあります。 多くの場合、インデックスがないと、インデックス シークで足りるときにテーブル スキャンが行われます。

次の例では、シークで足りるときに、選択したクエリ プランでスキャンが使用されます。

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![A query plan with missing indexes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database は、インデックス不足の一般的な状態を発見して修正するのに役立ちます。 Azure SQL Database に組み込まれている DMV には、クエリ コンパイルが表示されます。クエリを実行するために見積もられたコストをインデックスで大幅に削減できる場合があります。 クエリの実行中、SQL Database によって、各クエリ プランが実行される頻度と、実行クエリ プランとそのインデックスが存在した想定クエリ プランの間で見積もられるギャップが追跡されます。 これらの DMV を使用し、データベースとその実際のワークロードに関してワークロード コストを全体的に改善できる物理データベース設計の変更をすばやく推測できます。

次のクエリは、潜在的なインデックス不足の評価に使用できます。

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

この例では、クエリの結果として次が推奨されました。

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

作成後、同じ SELECT ステートメントを実行すると、異なるプランが選択されます。スキャンではなくシークが使用され、プランがより効率的に実行されます。

![A query plan with corrected indexes](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

重要なことは、共有される汎用システムの I/O 容量は専用サーバー コンピューターの I/O 容量より限られているということです。 不必要な I/O を最小限に抑え、Azure SQL Database サービス レベルの各パフォーマンス レベルの DTU 内でシステムを最大限に活用することが重要です。 適切な物理データベース設計を選択すると、個々のクエリの待機時間のほか、スケール ユニットごとに処理される同時要求のスループットを大幅に改善し、クエリを満たすために必要なコストを最小限に抑えることができます。 インデックス不足の DMV に関する詳細については、「[sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)」を参照してください。

### <a name="query-tuning-and-hinting"></a>クエリの調整とヒント
Azure SQL Database のクエリ オプティマイザーは、従来の SQL Server クエリ オプティマイザーと似ています。 クエリを調整し、クエリ オプティマイザーの推論モデル制約を理解するためのベスト プラクティスのほとんどは、Azure SQL Database にも活かすことができます。 Azure SQL Database のクエリを調整すると、総リソース要求を減らせる場合があります。 下位のパフォーマンス レベルで実行できるため、クエリが調整されていない場合に比べて少ないコストでアプリケーションを実行できます。

SQL Server でよく見られ Azure SQL Database にも適用される例は、クエリ オプティマイザーによるパラメーターの "スニッフィング" です。 コンパイル中、クエリ オプティマイザーによってパラメーターの現在の値が評価され、より最適なクエリ プランを生成できるかどうかが判断されます。 この戦略を使用すると多くの場合、既知のパラメーター値を使用せずにコンパイルされたプランよりもはるかに速いクエリ プランが生成されます。ただし現時点では、SQL Server と Azure SQL Database の両方で動作が不完全です。 パラメーターがスニッフィングされなかったり、パラメーターがスニッフィングされたものの、生成されたプランがワークロードのすべてのパラメーター値に関して最適でなかったりする場合があります。 意図をより慎重に指定し、パラメーター スニッフィングの既定の動作を上書きできるように、Microsoft はクエリ ヒント (ディレクティブ) を追加しています。 多くの場合、ヒントを使用すると、SQL Server または Azure SQL Database の既定の動作で特定のユーザーのワークロードに完全に対応できない問題を修正できます。

次の例は、パフォーマンスとリソースの両方の要件について最適でないプランがクエリ プロセッサによって生成されるようすを示しています。 この例から、クエリ ヒントを使用すると、SQL データベースのクエリの実行時間とリソース要件を抑えることができることもわかります。

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

このセットアップ コードによって、傾斜データ分布が含まれたテーブルが作成されます。 最適なクエリ プランは、選択されたパラメーターによって異なります。 残念ながらプラン キャッシング動作では、常に最も一般的なパラメーター値に基づいてクエリが再コンパイルされるとは限りません。 そのため、平均すると別のプランの方がプランとしてより良い選択になる場合でも、最適でないプランがキャッシュされ、多くの値に使用される可能性があります。 次に、(一方に特殊なクエリ ヒントが含まれていることを除いて) 同一の 2 つのストアド プロシージャがクエリ プランによって作成されます。

**例 (パート 1)**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**例 (パート 2)**

(例のパート 2 を開始する前に少なくとも 10 分待つことをお勧めします。これにより、生成されるテレメトリ データの結果の差異がはっきりします)。

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

この例の各パートでは、(テスト データ セットとして使用するのに十分な負荷を生成するために) パラメーター化された挿入ステートメントが 1,000 回試行されます。 ストアド プロシージャを実行するとき、クエリ プロセッサは、その最初のコンパイル中にプロシージャに渡されるパラメーター値を調べます (パラメーターの "スニッフィング")。 結果として生成されたプランがプロセッサによってキャッシュされ、パラメーター値が異なる場合でも、後の呼び出しで使用されます。 最適なプランが使用されないことがあります。 クエリが最初にコンパイルされたときのケースではなく、平均的なケースに対して最適なプランを選択するように、オプティマイザーを調整する必要がある場合があります。 この例では、最初のプランは、パラメーターに一致する各値を見つけるためにすべての行を読み取る "スキャン" プランを生成します。

![Query tuning by using a scan plan](./media/sql-database-performance-guidance/query_tuning_1.png)

値 1 を使用してプロシージャを実行したため、結果として生成されたプランは値 1 に対して最適ですが、テーブルにある他のすべての値に対しては最適ではありません。 各プランを無作為に選択した場合、結果は望んだものと異なることが予想されます。これは、プランの実行が遅く、より多くのリソースが使用されるためです。

`SET STATISTICS IO` を `ON` に設定してテストを実行すると、この例の論理スキャン作業がバックグラウンドで行われます。 このプランによって 1,148 件の読み取りが行われたことがわかります (平均的なケースで返される行がたった 1 つの場合、非効率的です)。

![Query tuning by using a logical scan](./media/sql-database-performance-guidance/query_tuning_2.png)

例の 2 つ目の部分では、クエリ ヒントを利用し、コンパイル プロセス中に特定の値を使用するようにオプティマイザーに伝えます。 この場合、パラメーターとして渡される値を無視し、`UNKNOWN` を想定するようにクエリ プロセッサに強制します。 これはテーブル内での頻度が平均的な値を示します (傾斜を無視)。 結果として生成されるプランはシークベースのプランです。このプランはこの例のパート 1 のプランより全体的に高速であり、このプランで使用されるリソースもパート 1 のプランより全体的に少なくなっています。

![Query tuning by using a query hint](./media/sql-database-performance-guidance/query_tuning_3.png)

**sys.resource_stats** テーブルで影響を確認できます (テストを実行してからデータがテーブルに入力されるまでの間に遅延があります)。 この例では、パート 1 は 22:25:00 の時間枠で実行され、パート 2 は 22:35:00 の時間枠で実行されています。 遅い方の時間枠と比べ、早い方の時間枠でその時間枠のリソースがより多く使用されます (プランの効率性改善が理由)。

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Query tuning example results](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> この例では規模を意図的に小さくしてありますが、最適でないパラメーターの影響は特に大規模なデータベースで顕著になります。 極端なケースでは、速い場合は数秒単位、遅い場合は数時間単位の差異になります。
> 
> 

**sys.resource_stats** を調べると、あるテストで使用されるリソースが別のテストより多いか少ないか判断できます。 データを比較するとき、**sys.resource_stats** ビューで同じ 5 分の枠に入らないようにテストのタイミングを離します。 この演習の目標は、使用されるリソースの総量を最小限に抑えることであり、ピーク リソースを最小限に抑えることではありません。 一般的に、待ち時間のコードの一部を最適化すると、リソースの消費量も減ります。 アプリケーションに施す変更が必要なものであることと、アプリケーションでクエリ ヒントを使用している他のユーザーのカスタマー エクスペリエンスに対し変更による悪影響がないことを確認してください。

ワークロードに一連の反復的なクエリが含まれる場合は、データベースをホストするために必要な最小リソース サイズ単位を把握できるため、たいてい、プラン選択肢の最適性を理解して検証することは合理的です。 検証した後、プランのパフォーマンスが低くなっていないことを確認するために、ときどきプランを調べ直してください。 詳細については、「 [クエリ ヒント (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)」をご覧ください。

### <a name="cross-database-sharding"></a>データベース間のシャーディング
Azure SQL Database は汎用ハードウェアで実行されるため、従来のオンプレミス SQL Server インストールと比べ、単一データベースに対する容量制限が低くなります。 データベース操作が Azure SQL Database の単一データベースの制限内に収まらないときにシャーディング手法を使用して、複数のデータベースに操作を分散しているユーザーもいます。 Azure SQL Database でシャーディング手法を利用するほとんどのユーザーは、1 つのディメンションのデータを複数のデータベースで分割します。 この手法では、OLTP アプリケーションは多くの場合、スキーマ内の 1 行のみ、またはほんの数行から成るグループに適用されるトランザクションを実行することを理解しておく必要があります。

> [!NOTE]
> SQL Database にシャーディングを支援するライブラリが追加されました。 詳細については、「 [エラスティック データベース クライアント ライブラリの概要](sql-database-elastic-database-client-library.md)」をご覧ください。
> 
> 

たとえば、(SQL Server 付属の従来のサンプル Northwind データベースのように) あるデータベースに顧客名、注文、注文明細が含まれている場合、関連する注文と注文明細の情報を使って顧客をグループ化することで、このデータを複数のデータベースに分割できます。 顧客のデータは単一データベース内にとどめておくことができます。 アプリケーションはデータベース間で顧客を分割し、効果的に負荷を分散します。 シャーディングを使用すると、顧客がデータベース サイズの上限を回避できるだけでなく、個々のデータベースがその DTU に収まる限り、各パフォーマンス レベルの制限を大幅に超えるワークロードを Azure SQL Database で処理できます。

データベース シャーディングではソリューションの総リソース容量を減らすことはできませんが、複数のデータベースにまたがる非常に大規模なソリューションに対応する際に非常に効果的です。 各データベースを異なるパフォーマンス レベルで実行し、リソース要件の高い、非常に大規模で "効果的な" データベースに対応できます。

### <a name="functional-partitioning"></a>機能的パーティション分割
SQL Server ユーザーは多くの場合、1 つのデータベースのさまざまな機能を組み合わせます。 たとえば、店舗の在庫を管理するロジックがアプリケーションに含まれている場合、そのデータベースには、在庫に関連付けられているロジック、購買発注の追跡、ストアド プロシージャ、月末報告を管理するインデックス付きビュー/具体化されたビューが含まれていることがあります。 この手法では、バックアップなどの操作に関するデータベースの管理が容易になりますが、アプリケーションの機能全体でピーク負荷を処理できるようにハードウェアのサイズを調整する必要もあります。

Azure SQL Database 内でスケールアウト アーキテクチャを使用する場合、アプリケーションの異なる機能を異なるデータベースに分割することをお勧めします。 この手法を使用すると、各アプリケーションは独立してスケールされます。 管理者は、アプリケーションがビジー状態になった (データベースの負荷が増えた) ときに、アプリケーションの機能ごとにパフォーマンス レベルを個別に選択できます。 制限はありますが、このアーキテクチャを使用して、1 台の汎用コンピューターで処理できる範囲を超えてアプリケーションの規模を大きくできます。これは、複数のコンピューター間で負荷が分散されるためです。

### <a name="batch-queries"></a>バッチ クエリ
大量のアドホック クエリを頻繁に実行してデータにアクセスするアプリケーションの場合、アプリケーション層と Azure SQL Database 層の間で行われるネットワーク通信の応答に、相当な時間が費やされます。 アプリケーションと Azure SQL Database が両方同じデータ センターに存在する場合でも、データ アクセス操作の数が多ければ、この 2 つの間のネットワーク待機時間は長くなる可能性があります。 データ アクセス操作のネットワーク ラウンド トリップを減らすために、アドホック クエリを一括処理すること、またはストアド プロシージャとしてそれらをコンパイルすることを検討してください。 アドホック クエリを一括処理すると、複数のクエリを 1 つの大きなバッチとして 1 回のトリップで Azure SQL Database に送信できます。 アドホック クエリをストアド プロシージャにコンパイルすると、それらを一括処理した場合と同じ結果が得られます。 ストアド プロシージャを使用すると、クエリ プランが Azure SQL Database にキャッシュされる機会が増えるという利点もあるため、ストアド プロシージャを再度使用できます。

一部のアプリケーションでは、書き込みが集中します。 場合によっては、書き込みを一括処理する方法を検討することで、データベースの I/O 総負荷を減らすことができます。 これは多くの場合、ストアド プロシージャとアドホック バッチ内で自動コミット トランザクションではなく明示的なトランザクションを使用するのと同じくらい単純です。 使用できるさまざまな手法の評価については、 [Azure での SQL Database アプリケーションのバッチ処理手法](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx)に関する記事を参照してください。 独自のワークロードで実験を行って、一括処理に適したモデルを見つけてください。 モデルによってはトランザクションの整合性の保証がわずかに異なる場合があることを理解しておいてください。 リソース使用を最小限に抑える適切なワークロードを見つけるには、整合性とパフォーマンスの適度なバランスを見つける必要があります。

### <a name="application-tier-caching"></a>アプリケーション層のキャッシュ
一部のデータベース アプリケーションでは、ワークロードの大半が読み取りになります。 キャッシュ層を利用すれば、データベースの負荷を減らすことができます。また、Azure SQL Database を使用してデータベースをサポートするために必要なパフォーマンス レベルを下げられる可能性があります。 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) を利用すると、読み取りが多いワークロードがある場合に、データを 1 回 (または、構成方法に応じてアプリケーション層コンピューターごとに 1 回) 読み込んでから、SQL データベースの外部にそのデータを格納することができます。 この方法は、データベースの負荷 (CPU と読み取り I/O) を減らすことができるものの、トランザクションの整合性に影響があります。データがキャッシュから読み込まれると、データベースのデータとの同期が失われることがあるためです。 多くのアプリケーションではある程度の不整合が許容されますが、すべてのワークロードで許容されるとは限りません。 アプリケーション層のキャッシュ手法を実装する前に、あらゆるアプリケーション要件を完全に理解しておく必要があります。

## <a name="next-steps"></a>次の手順
* サービス レベルの詳細については、 [SQL Database のオプションとパフォーマンス](sql-database-service-tiers.md)
* エラスティック プールの詳細については、「[Azure エラスティック プールの概要](sql-database-elastic-pool.md)」を参照してください。
* パフォーマンスとエラスティック プールの詳細については、[エラスティック プールの使用を検討する場合](sql-database-elastic-pool-guidance.md)に関するページを参照してください。

