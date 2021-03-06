---
title: Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる | Microsoft Docs
description: Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 4f1100b7e4fa2250baf282b53ef83c5f1aaa1c0e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
以下のシナリオでは、Azure-SSIS 統合ランタイム (IR) を Azure 仮想ネットワークに参加させます。 

- 仮想ネットワークの Azure SQL Database マネージ インスタンス (プライベート プレビューで) 上で SQL Server Integration Services (SSIS) のカタログ データベースをホストしています。
- Azure-SSIS 統合ランタイム上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。

 Azure Data Factory バージョン 2 (プレビュー) では、クラシック デプロイメント モデルまたは Azure Resource Manager デプロイメント モデルで作成された仮想ネットワークに Azure-SSIS 統合ランタイムを参加させることができます。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使っている場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)をご覧ください。

## <a name="access-to-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする
SSIS パッケージがパブリック クラウドのデータ ストアだけにアクセスする場合は、仮想ネットワークに Azure-SSIS IR を参加させる必要はありません。 SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、オンプレミスのネットワークに接続されている仮想ネットワークに Azure-SSIS IR を参加させる必要があります。 

SSIS カタログが仮想ネットワーク内にない Azure SQL Database インスタンスでホストされている場合は、適切なポートを開く必要があります。 

SSIS カタログが仮想ネットワークの SQL Database マネージ インスタンスでホストされている場合は、次のものに Azure-SSIS IR を参加させることができます。

- 同じ仮想ネットワーク。
- SQL Database マネージ インスタンスが存在する仮想ネットワークとの間にネットワーク間接続がある別の仮想ネットワーク。 

仮想ネットワークは、クラシック デプロイメント モデルまたは Azure Resource Manager デプロイメント モデルで展開できます。 SQL Database マネージ インスタンスと "*同じ仮想ネットワーク*" に Azure-SSIS IR を参加させる場合は、Azure-SSIS IR を SQL Database マネージ インスタンスとは "*異なるサブネット*" にする必要があります。   

以降のセクションではさらに詳しく説明します。

注意すべき重要な点がいくつかあります。 

- オンプレミスのネットワークに接続された既存の仮想ネットワークがない場合は、最初に、Azure-SSIS 統合ランタイムを参加させるための [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)または[クラシック仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を作成します。 次に、その仮想ネットワークからオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)または[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。
- Azure-SSIS IR と同じ場所にオンプレミス ネットワークに接続された既存の Azure Resource Manager またはクラシック仮想ネットワークがある場合は、その仮想ネットワークに IR を参加させることができます。
- Azure-SSIS IR とは異なる場所にオンプレミス ネットワークに接続された既存のクラシック仮想ネットワークがある場合は、最初に、Azure-SSIS IR を参加させるための[クラシック仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を作成します。 次に、[クラシック間の仮想ネットワーク](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)接続を構成します。 または、Azure-SSIS 統合ランタイムを参加させるための [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。
- Azure-SSIS IR とは異なる場所にオンプレミス ネットワークに接続された既存の Azure Resource Manager 仮想ネットワークがある場合は、最初に、Azure-SSIS IR を参加させるための [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md##create-a-virtual-network)を作成します。 次に、Azure Resource Manager 間の仮想ネットワーク接続を構成します。 または、Azure-SSIS IR を参加させるための[クラシック仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を作成します。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。

## <a name="domain-name-services-server"></a>ドメイン ネーム サービス サーバー 
Azure-SSIS 統合ランタイムが参加する仮想ネットワークで独自のドメイン ネーム サービス (DNS) サーバーを使う必要がある場合は、ガイダンスに従い、[仮想ネットワーク内の Azure-SSIS 統合ランタイムのノードが Azure エンドポイントを解決できるようにします](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>ネットワーク セキュリティ グループ
Azure-SSIS 統合ランタイムが参加する仮想ネットワークにネットワーク セキュリティ グループ (NSG) を実装する必要がある場合は、次のポートを受信/送信トラフィックが通過できるようにします。

| ポート | 方向 | トランスポート プロトコル | 目的 | 受信元/送信先 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100、20100、30100 (IR をクラシック仮想ネットワークに参加させる場合)<br/><br/>29876、29877 (IR を Azure Resource Manager 仮想ネットワークに参加させる場合) | 受信 | TCP | Azure サービスはこれらのポートを使って、仮想ネットワークの Azure-SSIS 統合ランタイムのノードと通信します。 | インターネット | 
| 443 | 送信 | TCP | 仮想ネットワークの Azure-SSIS 統合ランタイムのノードはこのポートを使って、Azure Storage や Azure Event Hubs などの Azure サービスにアクセスします。 | インターネット | 
| 1433<br/>11000 ～ 11999<br/>14000 ～ 14999  | 送信 | TCP | 仮想ネットワークの Azure-SSIS 統合ランタイムのノードはこれらのポートを使って、Azure SQL Database サーバーによってホストされている SSISDB にアクセスします (SQL Database マネージ インスタンスによってホストされている SSISDB には該当しません)。 | インターネット | 

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (データ ファクトリ UI)
このセクションでは、Azure Portal とデータ ファクトリ UI を使って、既存の Azure-SSIS ランタイムを仮想ネットワーク (クラシックまたは Azure Resource Manager) に参加させる方法について説明します。 Azure-SSIS IR を仮想ネットワークに参加させる前に、まず仮想ネットワークを適切に構成する必要があります。 仮想ネットワークの種類 (クラシックまたは Azure Resource Manager) に基づいて次の 2 つのセクションのいずれかを実行します。 次に 3 番目のセクションに進み、Azure-SSIS IR を仮想ネットワークに参加させます。 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>ポータルを使ってクラシック仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。

1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこれらの Web ブラウザーでのみサポートされています。
2. [Azure Portal](https://portal.azure.com) にサインインします。
3. **[そのほかのサービス]** を選択します。 **[仮想ネットワーク (クラシック)]** を選びます。
4. 一覧で目的の仮想ネットワークを選びます。 
5. **[仮想ネットワーク (クラシック)]** ページで、**[プロパティ]** を選びます。 

    ![クラシック仮想ネットワークのリソース ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. **[リソース ID]** のコピー ボタンを選び、クラシック ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。
6. 左側のメニューの **[サブネット]** を選びます。 **[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。

    ![仮想ネットワークで使用可能なアドレスの数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. **MicrosoftAzureBatch** を仮想ネットワークの**従来の仮想マシン共同作成者**ロールに参加させます。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 左側メニューの **[アクセス制御 (IAM)]** を選び、ツール バーの **[追加]** を選びます。 
       ![[アクセス制御] ボタンと [追加] ボタン](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. **[アクセス許可の追加]** ページで、**[ロール]** の **[従来の仮想マシン共同作成者]** を選びます。 **[選択]** ボックスに **ddbf3205-c6bd-46ae-8127-60eb93363864** を貼り付け、検索結果の一覧から **[Microsoft Azure Batch]** を選びます。   
       ![[アクセス許可の追加] ページでの検索結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. **[保存]** を選んで設定を保存し、ページを閉じます。  
       ![アクセス設定の保存](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. 共同作成者の一覧に **Microsoft Azure Batch** があることを確認します。  
       ![Azure Batch アクセスの確認](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 Azure Portal で、左メニューの **[サブスクリプション]** を選びます。

   b. サブスクリプションを選択します。

   c. 左側の **[リソース プロバイダー]** を選び、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。     
      !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>ポータルを使ってAzure Resource Manager 仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。

1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこれらの Web ブラウザーでのみサポートされています。
2. [Azure Portal](https://portal.azure.com) にサインインします。
3. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。
4. 一覧で目的の仮想ネットワークを選びます。 
5. **[仮想ネットワーク]** ページで、**[プロパティ]** を選びます。 
6. **[リソース ID]** のコピー ボタンを選び、仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。
7. 左側のメニューの **[サブネット]** を選びます。 **[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。
8. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 Azure Portal で、左メニューの **[サブスクリプション]** を選びます。

   b. サブスクリプションを選択します。 
   
   c. 左側の **[リソース プロバイダー]** を選び、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。     
      !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR を仮想ネットワークに参加させる


1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこれらの Web ブラウザーでのみサポートされています。
2. [Azure Portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、**[その他のサービス]** を選び、**[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選びます。 
    
   ![データ ファクトリの一覧](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. リストで Azure-SSIS 統合ランタイムを使うデータ ファクトリを選びます。 データ ファクトリのホーム ページが表示されます。 **[作成およびデプロイ]** タイルを選びます。 Data Factory の UI が別のタブに表示されます。 

   ![データ ファクトリのホーム ページ](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. データ ファクトリの UI で、**[編集]** タブに切り替え、**[接続]** を選択し、**[統合ランタイム]** タブに切り替えます。 

   ![[統合ランタイム] タブ](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Azure-SSIS IR が実行されている場合、統合ランタイム リストで、Azure-SSIS IR の **[アクション]** 列の **[停止]** ボタンを選びます。 IR は、停止するまで編集できません。 

   ![IR を停止する](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. 統合ランタイム リストで、Azure-SSIS IR の **[アクション]** 列の **[編集]** ボタンを選びます。

   ![統合ランタイムを編集する](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. **[統合ランタイムのセットアップ]** ウィンドウの **[全般設定]** ページで、**[次へ]** を選択します。 

   ![IR セットアップの全般設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. **[SQL 設定]** ページで管理者のパスワードを入力し、**[次へ]** を選びます。

   ![IR セットアップの SQL Server 設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. **[詳細設定]** ページで、次の操作を実行します。 

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings]\(参加させる Azure-SSIS 統合ランタイム用の VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** チェック ボックスをオンにします。

   b. **[種類]** で、仮想ネットワークがクラシック仮想ネットワークか、Azure Resource Manager 仮想ネットワークかを指定します。 

   c. **[VNET 名]** で仮想ネットワークを選びます。

   d. **[サブネット名]** で、仮想ネットワークのサブネットを選びます。

   e. **[Update]\(更新\)** を選択します。 

   ![IR セットアップの詳細設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. これで、Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンを使って IR を開始できます。 Azure-SSIS IR の開始には約 20 分かかります。 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 Azure-SSIS 統合ランタイムが仮想ネットワークを参加させるための仮想ネットワークのアクセス許可/設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Register to the Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR を作成して仮想ネットワークに参加させる
Azure-SSIS IR を作成し、作成と同時に仮想ネットワークに参加させることができます。 完全なスクリプトと手順については、[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md#azure-powershell)に関するページをご覧ください。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>既存の Azure-SSIS IR を仮想ネットワークに参加させる
[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事に示されているスクリプトは、Azure-SSIS IR の作成と仮想ネットワークへの参加を同じスクリプトで実行する方法を示しています。 既存の Azure-SSIS IR がある場合に、それを仮想ネットワークに参加させるには、次の手順を実行します。 

1. Azure-SSIS IR を停止します。
2. 仮想ネットワークに参加するように Azure-SSIS IR を構成します。 
3. Azure-SSIS IR を開始します。 

### <a name="define-the-variables"></a>変数を定義する

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

#### <a name="guidelines-for-selecting-a-subnet"></a>サブネットを選択するためのガイドライン
-   仮想ネットワーク ゲートウェイ専用であるため、Azure SSIS Integration Runtime をデプロイするために GatewaySubnet を選択しないでください。
-   選択したサブネットに、Azure SSIS IR が使用するための利用可能なアドレス領域が十分にあることを確認してください。 利用可能な IP アドレスで少なくとも 2 * の IR ノード番号をそのまま残しておきます。 Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「[これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)」をご覧ください。


### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR を停止する
仮想ネットワークに参加させる前に、Azure-SSIS 統合ランタイムを停止します。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR が参加するように仮想ネットワーク設定を構成する
Azure Backup リソース プロバイダーに登録します。

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR を構成する
仮想ネットワークに参加するように Azure-SSIS 統合ランタイムを構成するのには、`Set-AzureRmDataFactoryV2IntegrationRuntime` コマンドを実行します。 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR を開始する
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
このコマンドは、完了するまでに 20 ～ 30 分かかります。

## <a name="use-azure-expressroute-with-the-azure-ssis-ir"></a>Azure SSIS IR と共に Azure ExpressRoute を使用する

[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回路を自分の仮想ネットワーク インフラストラクチャに接続することで、オンプレミス ネットワークを Azure に拡張できます。 

一般的な構成では、VNet フローからオンプレミス ネットワーク アプライアンスへの送信インターネット トラフィックを強制して調査とログ記録を行う、強制トネリング (BGP ルート、0.0.0.0/0 を VNet にアドバタイズする) を使用します。 このトラフィック フローは、依存する Azure Data Factory サービスを備えた VNet の Azure SSIS IR 間の接続を中断させます。 解決策は、Azure SSIS IR を含むサブネット上で 1 つ (以上) の[ユーザー定義ルート (UDR)](../virtual-network/virtual-networks-udr-overview.md) を定義することです。 UDR は、BGP ルートに優先するサブネット固有のルートを定義します。

可能な場合、次の構成を使用します。
-   ExpressRoute 構成は 0.0.0.0/0 をアドバタイズし、既定でオンプレミスのすべての送信トラフィックを強制的にトンネリングします。
-   Azure SSIS IR を含むサブネットに適用される UDR では、次ホップの種類がインターネットである 0.0.0.0/0 ルートを定義します。
- 
これらの手順を組み合わせた結果として、サブネット レベル UDR は ExpressRoute 強制トンネリングよりも優先されるので、Azure SSIS IR からの送信インターネット アクセスを確保できます。

該当のサブネットからの送信インターネット トラフィックを調査する機能を失うことが心配な場合は、そのサブネットで NSG ルールを追加して、送信先を [Azure データ センターの IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653)に制限することもできます。

例については、[こちらの PowerShell スクリプト](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c)をご覧ください。 スクリプトを週単位で実行して、Azure データ センター IP アドレスの一覧を最新に保つ必要があります。

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md):  この記事では、Azure-SSIS IR の作成手順が示されています。 Azure SQL Database を使って SSIS カタログをホストします。 
- [Azure-SSIS 統合ランタイムを作成](create-azure-ssis-integration-runtime.md)します。 この記事では、チュートリアルを基に、Azure SQL Database マネージ インスタンス (プライベート プレビュー) の使い方と、IR を仮想ネットワークに参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
