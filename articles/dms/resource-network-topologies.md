---
title: Azure Database Migration Service を使用して Azure SQL DB マネージ インスタンスを移行するためのネットワーク トポロジ | Microsoft Docs
description: Database Migration Service のソースとターゲットの構成について説明します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Azure Database Migration Service を使用して Azure SQL DB マネージ インスタンスを移行するためのネットワーク トポロジ
この記事では、オンプレミスの SQL サーバーから Azure SQL Database マネージ インスタンスへのシームレスな移行エクスペリエンスを提供するために Azure Database Migration Service で使用できる、さまざまなネットワーク トポロジについて説明します。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>ハイブリッド ワークロード用に構成された Azure SQL Database マネージ インスタンス 
Azure SQL Database マネージ インスタンスがオンプレミス ネットワークに接続されている場合は、このトポロジを使用します。 このアプローチでは、ネットワーク ルーティングが最も簡素化され、移行時に最大限のデータ スループットが得られます。

![ハイブリッド ワークロード用のネットワーク トポロジ](media\resource-network-topologies\hybrid-workloads.png)

**要件**
- このシナリオでは、Azure SQL Database マネージ インスタンスと Azure Database Migration Service インスタンスが同じ Azure VNet 内に作成されますが、サブネットはそれぞれ別のものが使用されます。  
- このシナリオで使用される VNet は、ExpressRoute または VPN を使用して、オンプレミス ネットワークに接続されます。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>オンプレミス ネットワークから分離された Azure SQL Database マネージ インスタンス
使用する環境において、次の 1 つ以上のシナリオが当てはまる場合は、このネットワーク トポロジを使用します。
- Azure SQL Database マネージ インスタンスはオンプレミス接続から分離されているが、Azure Database Migration Service はオンプレミス ネットワークに接続されている。
- ロール ベースのアクセス制御 (RBAC) ポリシーが使用されていて、ユーザー アクセスが、Azure SQL Database マネージ インスタンスをホストしている同じサブスクリプションに制限される。
- Azure SQL Database マネージ インスタンス用に使用される VNet と Azure Database Migration Service 用に使用される VNet が、それぞれ異なるサブスクリプションに属している。

![オンプレミス ネットワークから分離されたマネージ インスタンス用のネットワーク トポロジ](media\resource-network-topologies\mi-isolated-workload.png)

**要件**
- このシナリオで Azure Database Migration Service に使用される VNet は、ExpressRoute または VPN を使用して、オンプレミス ネットワークにも接続される必要があります。
- Azure SQL Database マネージ インスタンスと Azure Database Migration Service 用に使用される VNet 間に、VNet ネットワーク ピアリングを作成します。


## <a name="cloud-to-cloud-migrations"></a>クラウド間の移行
ソース SQL Server が Azure 仮想マシンでホストされている場合は、このトポロジを使用します。

![クラウド間移行のためのネットワーク トポロジ](media\resource-network-topologies\cloud-to-cloud.png)

**要件**
- Azure SQL Database マネージ インスタンスと Azure Database Migration Service 用に使用される VNet 間に、VNet ネットワーク ピアリングを作成します。

## <a name="see-also"></a>関連項目
- [SQL Server を Azure SQL Database マネージ インスタンスに移行する](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Azure Database Migration Service を使用するための前提条件の概要](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Azure Portal を使用した仮想ネットワークの作成](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>次の手順
Azure Database Migration Service の概要と、パブリック プレビュー期間中のリージョンごとの利用可能性については、「[Azure Database Migration Service プレビューとは何ですか](dms-overview.md)」という記事を参照してください。 