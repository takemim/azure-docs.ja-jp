---
title: Azure SQL Database 顧客実装技術 | Microsoft Docs
description: ビジネス上の問題を解決するために、Azure SQL Database の顧客実装に関する技術的な詳細を説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
ms.openlocfilehash: 22f462010f636731cdceed5105e6057d2d45f005
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Azure SQL Database 顧客実装技術スタディ

- [Daxko](sql-database-implementation-daxko.md): Daxko/CSI Software 社 (以下 CSI Software) は問題に直面していました。自社の包括的な企業向けソフトウェア ソリューションの成功により、フィットネス レクリエーション センターの顧客基盤が急速に拡大する一方で、顧客数の増加により、IT インフラストラクチャのニーズに応えることが難しくなっていたのです。 CSI Software は、拡大するデータベースの管理に伴う経費の増加に頭を悩ませるようになりました。 また、この問題により、ソフトウェアのモビリティ機能などの新たな取り組みに向けた開発リソースがひっ迫していました。

- [GEP](sql-database-implementation-gep.md): GEP 社 (以下 GEP) は、世界中の調達責任者が、事業のオペレーション、戦略、および財務パフォーマンスへの効果を最大化するためのソフトウェアとサービスを提供しています。 コンサルティングおよびマネージド サービスに加えて、クラウド ベースの包括的な調達ソフトウェア プラットフォーム SMART by GEP® も提供しています。 しかし GEP は、SMART by GET などのソリューションを自社のオンプレミス型データセンターでサポートすることを試みて壁に突き当たりました。ばく大な投資が必要なこと、また世界各国の規制要件への対応を考慮すると、必要な投資はさらに困難なものに思えました。 現在 GEP は、クラウドへの移行によって IT リソースを確保し、IT 運用の省力化を実現するとともに、世界中の顧客に新しい価値をもたらすサービスの開発に注力できるようになりました。

- [SnelStart](sql-database-implementation-snelstart.md): SnelStart 社 (以下 SnelStart) は、オランダの中堅・中小規模企業 (SMB) 向けに、定評ある財務/業務管理ソフトウェアを製造しています。 SnelStart では、IT スタッフ 35 人を含む 110 人の社員が 5 万 5,000 人の顧客に対応しています。 デスクトップ ソフトウェアから Azure 上に構築されたサービスとしてのソフトウェア (SaaS) の提供に移行して、管理を自動化するビルトイン サービスの大半を、使い慣れたC# 言語の環境を使用して作成しています。またエラスティック プールを使用することで、顧客企業へのプロビジョニングの過不足なく、パフォーマンスとスケーラビリティを最適化しています。 Azure の導入により、SnelStart は、顧客のオンプレミスとクラウド間の円滑な移動を実現しています。

- [Umbraco](sql-database-implementation-umbraco.md): 顧客の環境を簡素化するため、Umbraco は、サービスとしてのソフトウェア (SaaS) の一種である、「サービスとしての Umbraco (UaaS)」 を追加しました。UaaS はオンプレミス環境の必要性をなくし、組み込み型拡張機能を提供して、ソリューション管理の手間を省き、開発者が製品のイノベーションに注力することで、管理費用を削減します。 Microsoft Azure の柔軟性に優れたサービスとしてのプラットフォーム (PaaS) モデルに依存することで、Umbraco はこれらのメリットを提供することが可能になりました。

- [Quorum](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database): Quorum 社は、SQL Database を使用して DTU を 70% 下げながら、主要なデータベースのワークロードを 2 倍にしました。

- [Quest](https://customers.microsoft.com/story/quest): Quest 社では 1 つの目標を念頭に置いて、Spotlight on SQL Server Enterprise サービスを提供しています。その目標とは、データの保護、そのデータの移動、およびデータベース操作の監視に使用できる最適なツールをデータベースの専門家に提供することです。 Spotlight と Microsoft Azure および Azure SQL Database を使用することで、SQL Server データベース管理者は職場や自宅などの場所を問わず、SQL Server におけるパフォーマンスの問題を監視、検出、診断でき、問題を解決するための方法を提供できます。

- [Microsoft Dynamics](https://customers.microsoft.com/story/dynamics365operationsproductteam): この簡単なケース スタディは、完全に管理されたサービスとしてのソフトウェア (SaaS) オファリングをお客様に提供するために、Dynamics 365 for Operations 製品チームが Azure SQL Database に移行した経験から得たベスト プラクティスと教訓に重点を置いています。 Azure SQL Database を使用することで、Dynamics 365 for Operations チームは非常に少ない人員でサービスを管理および運営し、データベースの自動バックアップ、データベースのバックアップ保持、高可用性およびディザスター リカバリー機能など、すぐに使用できる管理容易性機能を利用して簡単にスケーリングすることができました。 単純な自動化によるデータベースのプロビジョニング機能と共に使用することで、Azure SQL Database は大規模なサービスに対応できる優れたプラットフォームとなりました。

- [Microsoft OneDrive および SharePoint Online](https://customers.microsoft.com/story/microsoft-azure-sql-database-dicrete-manufacturing-united-states): このケース スタディの要約は、Microsoft OneDrive と SharePoint Online の Azure SQL Database への移行に関する背景事情と、この移行によってほぼ無制限のエラスティックな容量管理が可能になり、運用コストとインフラストラクチャのオーバーヘッドも大幅に削減できるようになった方法を説明しています。
