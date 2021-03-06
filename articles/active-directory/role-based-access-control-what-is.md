---
title: "RBAC を使用したアクセスとアクセス許可の管理 - Azure RBAC | Microsoft Docs"
description: "Azure Portal で Azure のロールベースのアクセス制御を使用したアクセス管理を開始します。 ロールの割り当てを使用して、ディレクトリ内でアクセス許可を割り当てます。"
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 6eb51cb8dc8c158f5412334a09493172f2973c74
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Azure Portal でのロールベースの Access Control の基礎を確認する
セキュリティを重視する企業は、実際に必要となるアクセス許可を従業員に付与することに注力する必要があります。 アクセス許可が多すぎると、アカウントが攻撃者による悪用の対象になりかねません。 アクセス許可が少なすぎると、従業員は業務を効率的に遂行できなくなる可能性があります。 Azure のロールベースのアクセス制御 (RBAC) は、Azure のアクセス許可を詳細に管理を実現することでこの問題に対処できます。

RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定の操作のみを許可することができます。 たとえば、RBAC を使用して、ある従業員にはサブスクリプションで仮想マシンを管理できるようにし、他の従業員にも同じサブスクリプション内で SQL データベースを管理できるようにします。

## <a name="basics-of-access-management-in-azure"></a>Azure でのアクセス管理の基礎
各 Azure サブスクリプションは、1 つの Azure Active Directory (AD) ディレクトリと関連付けられます。 そのディレクトリに登録されたユーザー、グループ、およびアプリケーションのみが、Azure サブスクリプションでリソースを管理できます。 このためのアクセス権は、Azure ポータル、Azure コマンドライン ツール、および Azure 管理 API を使用して割り当てます。

アクセス権を付与するには、ユーザー、グループ、およびアプリケーションに適切な RBAC ロールを特定のスコープで割り当てます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。 親スコープでロールが割り当てられると、その親に含まれる子へのアクセス権も付与されます。 たとえば、リソース グループへのアクセス権を持つユーザーは、Web サイト、仮想マシン、サブネットなど、リソース グループに含まれるすべてのリソースを管理できます。

![Relationship between Azure Active Directory elements - diagram](./media/role-based-access-control-what-is/rbac_aad.png)

割り当てる RBAC ロールにより、そのスコープ内でユーザー、グループ、またはアプリケーションが管理できるリソースが決まります。

## <a name="built-in-roles"></a>組み込みのロール
Azure RBAC には、すべてのリソースの種類に適用される 3 つの基本的なロールがあります。

* **所有者** は、他のユーザーへアクセス権を委任する権限を含め、すべてのリソースへのフル アクセス権を持ちます。
* **共同作成者** は、Azure リソースのすべてのタイプを作成および管理できますが、他のユーザーへアクセス権を付与することはできません。
* **閲覧者** は、既存の Azure リソースを表示できます。

残りの Azure RBAC ロールでは、特定の Azure リソースの管理が許可されます。 たとえば、仮想マシンの共同作成者ロールが割り当てられたユーザーには、仮想マシンの作成と管理が許可されます。 その一方で、仮想マシンが接続する仮想ネットワークまたはサブネットへのアクセス権は付与されません。 

[RBAC: 組み込みのロール](role-based-access-built-in-roles.md) 」に、Azure で使用できる RBAC ロールが記載されています。 各組み込みロールによってユーザーに付与される操作とスコープが説明されています。 制御を強化するために独自のロールを定義する場合は、 [Azure RBAC でカスタム ロール](role-based-access-control-custom-roles.md)を作成する方法を参照してください。

## <a name="resource-hierarchy-and-access-inheritance"></a>リソース階層とアクセス権の継承
* Azure 内の各 **サブスクリプション** は、1 つのディレクトリのみに属しています。 (ただし、1 つのディレクトリに複数のサブスクリプションが含まれることはあります。)
* 各 **リソース グループ** は、1 つのサブスクリプションのみに属しています。
* 各 **リソース** は、1 つのリソース グループのみに属しています。

親スコープで付与されたアクセス権は、子スコープに継承されます。 たとえば:

* 閲覧者ロールをサブスクリプション スコープで Azure AD グループに割り当てると、 そのグループのメンバーは、サブスクリプション内のすべてのリソース グループとすべてのリソースを表示できるようになります。
* 共同作成者ロールをリソース グループ スコープでアプリケーションに割り当てると、 そのアプリケーションでは、そのリソース グループ内のすべてのタイプのリソースを管理できるようになりますが、サブスクリプション内の他のリソース グループは管理できません。

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC と従来のサブスクリプションの管理者の比較
[従来のサブスクリプションの管理者と共同管理者](../billing/billing-add-change-azure-subscription-administrator.md)には、Azure サブスクリプションへのフル アクセス権があります。 リソースの管理には、[Azure Portal](https://portal.azure.com)、Azure Resource Manager API、およびクラシック デプロイメント モデル API を使用できます。 RBAC モデルで、従来の管理者は、サブスクリプション スコープで所有者ロールを割り当てられます。

Azure RBAC は Azure ポータルと新しい Azure Resource Manager API の組み合わせのみでサポートされています。 RBAC ロールが割り当てられているユーザーやアプリケーションは、Azure クラシック デプロイメント モデル API を使用できません。

## <a name="authorization-for-management-vs-data-operations"></a>管理操作とデータ操作の許可
Azure RBAC は、Azure ポータルと Azure Resource Manager API での Azure リソースの管理操作のみに対応しています。 Azure リソースのデータ レベルの操作の中には、許可されていないものもあります。 たとえば、同じユーザーにストレージ アカウントを管理することを承認できますが、ストレージ アカウントでの BLOB またはテーブルの操作を許可することはできません。 同様に、SQL データベースは管理できますが、その中のテーブルは管理できません。

## <a name="next-steps"></a>次の手順
* [Azure ポータルでのロールベースの Access Control](role-based-access-control-configure.md)の基礎を確認する
* [RBAC: 組み込みのロール](role-based-access-built-in-roles.md)
* 独自の [Azure RBAC でカスタム ロール](role-based-access-control-custom-roles.md)
