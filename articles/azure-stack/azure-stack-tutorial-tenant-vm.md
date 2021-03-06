---
title: Azure Stack ユーザーが仮想マシンを使用できるようにする | Microsoft Docs
description: Azure Stack ユーザーが仮想マシンを使用できるようにするためのチュートリアル
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: af97f32736959f8ebf8f3c4fbca400d6b0c41f3e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>Azure Stack ユーザーが仮想マシンを使用できるようにする
Azure Stack のクラウド管理者は、ユーザー (テナントとも呼ばれます) がサブスクライブできるオファーを作成できます。 ユーザーはサブスクリプションを使用して Azure Stack サービスを利用できます。

この記事では、オファーを作成し、それをテストする方法について説明します。 テストの場合は、ユーザーとしてポータルにログインし、オファーにサブスクライブしてから、そのサブスクリプションを使用して仮想マシンを作成します。

学習内容

> [!div class="checklist"]
> * オファーの作成
> * イメージの追加
> * オファーのテスト


Azure Stack では、サブスクリプション、オファー、プランを使用してサービスがユーザーに配信されます。 ユーザーは複数のオファーにサブスクライブできます。 オファーは 1 つまたは複数のプランを含むことができ、プランは 1 つまたは複数のサービスを含むことができます。

![サブスクリプション、オファー、およびプラン](media/azure-stack-key-features/image4.png)

詳細については、「[Azure Stack の主要な機能と概念](azure-stack-key-features.md)」を参照してください。

## <a name="create-an-offer"></a>オファーの作成

これでユーザーのための準備を始められます。 プロセスを開始すると、最初にオファーを、次にプランを、最後にクォータを作成するように指示されます。

3. **オファーの作成**

   オファーは、プロバイダーがユーザーに提示する 1 つまたは複数のプランのグループで、ユーザーはそれを購入またはサブスクライブします。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ポータルにクラウドの管理者として[サインイン](azure-stack-connect-azure-stack.md)してから、**[新規]** > **[プラン]** > **[オファー]** の順にクリックします。
   ![新しいプラン](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. **[新しいプラン]** セクションの **[表示名]** と **[リソース名]** に入力し、新規または既存の **[リソース グループ]** を選択します。 表示名は、オファーのフレンドリ名です。 リソース名は、クラウド オペレーターのみが表示できます。 この名前を使用して、管理者はオファーを Azure リソース マネージャーのリソースとして操作します。

   ![表示名](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. **[基本プラン]** をクリックし、**[プラン]** セクションで **[追加]** をクリックして新しいプランをオファーに追加します。

   ![プランの追加](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. **[新しいプラン]** セクションで **[表示名]** と **[リソース名]** を入力します。 表示名は、ユーザーに表示されるプランのフレンドリ名です。 リソース名は、クラウド オペレーターのみが表示できます。 クラウド オペレーターはこの名前を使用して、プランを Azure Resource Manager のリソースとして操作します。

   ![プランの表示名](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. **[サービス]** をクリックし、**[Microsoft.Compute]**、**[Microsoft.Network]**、および **[Microsoft.Storage]** を選択し、**[選択]** をクリックします。

   ![プランのサービス](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. **[クォータ]** をクリックしてから、クォータを作成する最初のサービスを選択します。 IaaS クォータの場合は、以下に示す、コンピューティング サービス、ネットワーク サービス、およびストレージ サービスの手順に従います。

   この例では、最初に、コンピューティング サービスのクォータを作成します。 名前空間の一覧で、**[Microsoft.Compute]** 名前空間を選択し、**[新しいクォータの作成]** をクリックします。
   
   ![新しいクォータの作成](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. **[Create quota]** (クォータの作成) セクションで、クォータの名前を入力し、そのクォータに必要なパラメーターを設定して **[OK]** をクリックします。

   ![クォータ名](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. ここで、**Microsoft.Compute** のために、作成したクォータを選択します。

   ![クォータの選択](media/azure-stack-tutorial-tenant-vm/image08.png)

   これらの手順をネットワーク サービスとストレージ サービスについて繰り返し、**[クォータ]** セクションの **[OK]** をクリックします。

   i. **[New plan]** (新しいプラン) セクションの **[OK]** をクリックします。

   j. **[プラン]** セクションで新しいプランを選択し、**[選択]** をクリックします。

   k. **[New offer]** (新しいオファー) セクションで **[作成]** をクリックします。 オファーが作成されると通知が表示されます。

   l. ダッシュ ボード メニューの **[Offer]** (オファー) をクリックしてから、作成したオファーをクリックします。

   m. **[状態の変更]** をクリックし、**[パブリック]** をクリックします。

   ![[パブリック] の状態](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>イメージの追加

仮想マシンをプロビジョニングする前に、Azure Stack Marketplace にイメージを追加する必要があります。 Azure Marketplace の Linux イメージを含め、任意のイメージを追加できます。

接続されるシナリオで運用していて、Azure に Azure Stack インスタンスを登録済みの場合は、「[Azure から Azure Stack に Marketplace の項目をダウンロードする](azure-stack-download-azure-marketplace-item.md)」というトピックに記載されている手順を使用して、Azure Marketplace から Windows Server 2016 の VM イメージをダウンロードできます。

Marketplace にさまざまな項目を追加する方法については、「[Azure Stack Marketplace](azure-stack-marketplace.md)」を参照してください。

## <a name="test-the-offer"></a>オファーのテスト

オファーを作成したので、そのテストを行うことができます。 ユーザーとしてログインしてオファーにサブスクライブし、仮想マシンを追加します。

1. **プランへのサブスクライブ**

   これで、オファーにサブスクライブするユーザーとしてポータルにログインできます。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ユーザーとしてユーザー ポータルにログインし、**[サブスクリプションの取得]** をクリックします。
   - 統合システムの場合、URL はオペレーターのリージョンと外部ドメイン名によって異なり、https://portal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; の形式になります。
   - Azure Stack Development Kit を使用している場合、ポータルのアドレスは https://portal.local.azurestack.external になります。

   ![サブスクリプションの取得](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. **[表示名]** フィールドにサブスクリプションの名前を入力し、**[Offer]** (オファー) をクリックして、**[Choose an offer]** (オファーの選択) セクションでいずれかのオファーをクリックしたら、**[作成]** をクリックします。

   ![オファーの作成](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. 作成したサブスクリプションを表示するには、**[その他のサービス]**、**[サブスクリプション]** の順にクリックし、作成した新しいサブスクリプションをクリックします。  

   オファーをサブスクライブしたら、ポータルを更新して、どのサービスが新しいサブスクリプションの一部であるかを確認します。

2. **仮想マシンのプロビジョニング**

   これで、ユーザーとしてポータルにログインし、サブスクリプションを使用して仮想マシンをプロビジョニングすることができます。 

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ユーザーとしてユーザー ポータルにログインします。
      - 統合システムの場合、URL はオペレーターのリージョンと外部ドメイン名によって異なり、https://portal.&lt;*リージョン*&gt;.&lt;*FQDN*&gt; の形式になります。
   - Azure Stack Development Kit を使用している場合、ポータルのアドレスは https://portal.local.azurestack.external になります。

   b.  ダッシュボードで、**[新規]** > **[コンピューティング]** > **[Windows Server 2016 Datacenter Eval]** の順にクリックし、**[作成]** をクリックします。

   c. **[基本]** セクションで、**[名前]**、**[ユーザー名]**、**[パスワード]** を入力し、**[サブスクリプション]** を選択し、**[リソース グループ]** を作成し (または既存のものを選択し)、**[OK]** をクリックします。

   d. **[サイズの選択]** セクションで、**[A1 標準]** をクリックしてから **[選択]** をクリックします。  

   e. **[設定]** セクションで、**[仮想ネットワーク]** をクリックします。 **[仮想ネットワークの選択]** セクションで **[新規作成]** をクリックします。 **[Create virtual network]** (仮想ネットワークの作成) セクションで、すべての既定値をそのまま使用し、**[OK]** をクリックします。 **[設定]** セクションで **[OK]** をクリックします。

   ![Create virtual network](media/azure-stack-provision-vm/image04.png)

   f. **[概要]** セクションで、**[OK]** をクリックして仮想マシンを作成します。  

   g. 新しい仮想マシンを表示するには、**[すべてのリソース]** をクリックして仮想マシンを検索し、仮想マシン名をクリックします。

    ![すべてのリソース](media/azure-stack-provision-vm/image06.png)

このチュートリアルで学習した内容:

> [!div class="checklist"]
> * オファーの作成
> * イメージの追加
> * オファーのテスト

> [!div class="nextstepaction"]
> [Make web, mobile, and API apps available to your Azure Stack users (Azure Stack ユーザーが Web Apps、Mobile Apps、および API Apps を使用できるようにする)](azure-stack-tutorial-app-service.md)