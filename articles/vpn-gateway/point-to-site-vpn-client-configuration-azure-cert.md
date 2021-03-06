---
title: "Azure 証明書認証用の P2S VPN クライアント構成ファイルを作成およびインストールする: PowerShell: Azure | Microsoft Docs"
description: "P2S 証明書認証のために、Windows および Mac OS X VPN クライアント構成ファイルを作成してインストールします。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 0ca7b7ca9435d1ba05a2cc0951f5bc88b51bf81b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>ネイティブ Azure 証明書認証のポイント対サイト構成のための VPN クライアント構成ファイルを作成およびインストールする

VPN クライアント構成ファイルは、ZIP ファイルに含まれています。 構成ファイルでは、Windows または Mac のネイティブ IKEv2 VPN クライアントが、ネイティブ Azure 証明書認証を使用するポイント対サイト接続を介して VNet に接続するために必要な設定を提供します。

### <a name="workflow"></a>P2S ワークフロー

  1. P2S 接続用に Azure VPN ゲートウェイを設定する。
  2. ルート証明書とクライアント証明書を生成する。 ルート証明書の公開キーを Azure にアップロードし、クライアント証明書をクライアント デバイスにインストールします。 証明書は、接続するユーザーの認証に使用されます。
  3. 選択した認証オプションの VPN クライアント構成を取得し、それを使用して Windows および Mac デバイスで VPN クライアントを設定する。 これにより、ポイント対サイト接続経由で Azure VNet に接続できます。

>[!NOTE]
>クライアント構成ファイルは、VNet の VPN 構成に固有です。 VPN プロトコルの種類や認証の種類など、VPN クライアント構成ファイルの生成後にポイント対サイト VPN 構成に対する変更があった場合は、ユーザー デバイス用に新しい VPN クライアント構成ファイルを生成してください。
>
>

## <a name="generate"></a>VPN クライアント構成ファイルの生成

開始する前に、接続するすべてのユーザーでは、有効な証明書がユーザーのデバイスにインストールされていることを確認してください。 クライアント証明書のインストールの詳細については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

PowerShell または Azure Portal を使用してクライアント構成ファイルを生成することができます。 どちらの方法でも、同じ zip ファイルが返されます。 そのファイルを解凍して、次のフォルダーを表示します。

  * **WindowsAmd64** および **WindowsX86**。Windows の 32 ビットと 64 ビットのインストーラー パッケージがそれぞれに含まれています。 **WindowsAmd64** インストーラー パッケージは、Amd だけでなく、サポートされている 64 ビットの Windows クライアントを対象としています。
  * **Generic**。これには、独自の VPN クライアント構成の作成に使用される全般的な情報が含まれています。 このフォルダーは無視してください。 Generic フォルダーが提供されるのは、IKEv2 または SSTP+IKEv2 がゲートウェイ上で構成された場合です。 構成されているのが SSTP のみの場合、Generic フォルダーは存在しません。

### <a name="zipportal"></a>Azure Portal を使用してルールを生成する

1. Azure Portal で、接続する仮想ネットワークの仮想ネットワーク ゲートウェイに移動します。
2. 仮想ネットワーク ゲートウェイ ページで、**[ポイント対サイトの構成]** をクリックします。
3. [ポイント対サイトの構成] ページの上部で **[VPN クライアントのダウンロード]** をクリックします。 クライアント構成パッケージが生成されるまでに数分かかります。
4. お使いのブラウザーは、クライアント構成の zip ファイルが使用可能なことを示します。 ゲートウェイと同じ名前が付いています。 そのファイルを解凍して、フォルダーを表示します。

### <a name="zipps"></a>PowerShell を使用してファイルを生成する

1. VPN クライアント構成ファイルを生成する際、"-AuthenticationMethod" の値は "EapTls" です。 次のコマンドを使用して、VPN クライアント構成ファイルを生成します。

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. ブラウザーに URL をコピーして、zip ファイルをダウンロードし、ファイルを解凍してフォルダーを表示します。

## <a name="installwin"></a>Windows VPN クライアント構成パッケージのインストール

バージョンがクライアントのアーキテクチャと一致する限り、各 Windows クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md#P2S)」のポイント対サイトに関するセクションを参照してください。

>[!NOTE]
>接続元の Windows クライアント コンピューターの管理者権限が必要です。
>
>

証明書認証用にネイティブ Windows VPN クライアントを構成するには、次の手順を実行してください。

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。 
2. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、**[詳細]**、**[実行]** の順にクリックしてください。
3. クライアント コンピューターで **[ネットワークの設定]** に移動し、**[VPN]** をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。 
4. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 証明書の生成の詳細については、「[証明書の生成](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。

## <a name="installmac"></a>Mac (OS X) 上の VPN クライアント構成

Azure では、ネイティブの Azure 証明書の認証用の mobileconfig ファイルは提供されません。 Azure に接続するすべての Mac で、ネイティブの IKEv2 VPN クライアントを手動で構成する必要があります。 **Generic** フォルダーには、構成に必要な情報がすべて揃っています。 ダウンロードに、Generic フォルダーが表示されない場合は、IKEv2 がトンネルの種類として選択されていない可能性があります。 IKEv2 を選択したら、もう一度 zip ファイルを生成して、Generic フォルダーを取得します。 Generic フォルダーには、次のファイルが含まれています。

* **VpnSettings.xml**。サーバー アドレスやトンネルの種類など、重要な設定が含まれています。 
* **VpnServerRoot.cer**。P2S 接続の設定中に Azure VPN ゲートウェイを検証するために必要なルート証明書が含まれています。

証明書認証用に Mac 上でネイティブ VPN クライアントを構成するには、次の手順を実行してください。 Azure に接続するすべての Mac でこれらの手順を完了する必要があります。

1. **VpnServerRoot** ルート証明書を Mac にインポートします。 これを行うには、ファイルを Mac にコピーしてダブルクリックします。  
**[追加]** をクリックしてインポートします。

  ![証明書の追加](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >証明書をダブルクリックしても、**[追加]** ダイアログが表示されない場合がありますが、証明書は正しいストアにインストールされています。 証明書のカテゴリの下にあるログイン キーチェーンで証明書を確認できます。
  
2. P2S 設定を構成したときに、Azure にアップロードしたルート証明書によって発行されたクライアント証明書が、インストール済みであることを確認します。 これは、前の手順でインストールした VPNServerRoot とは異なります。 クライアント証明書は認証に使用され、必須です。 証明書の生成の詳細については、「[証明書の生成](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)」をご覧ください。 クライアント証明書のインストール方法については、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページをご覧ください。
3. **[ネットワーク]** ダイアログを開き、**[ネットワーク環境設定]** で **[+]** をクリックして、Azure VNet への P2S 接続用に新しい VPN クライアント接続プロファイルを作成します。

  **[インターフェイス]** の値は "VPN"、**[VPN タイプ]** の値は "IKEv2" です。 **[サービス名]** フィールドにプロファイルの名前を指定し、**[作成]** をクリックして VPN クライアント接続プロファイルを作成します。

  ![ネットワーク](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. **Generic** フォルダーの **VpnSettings.xml** ファイルから、**VpnServer** タグの値をコピーします。 この値をプロファイルの **[サーバー アドレス]** フィールドと **[リモート ID]** フィールドに貼り付けます。

  ![サーバー情報](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. **[認証設定]** をクリックし、**[証明書]** を選択します。 

  ![認証設定](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. **[選択]** をクリックして、 認証に使用するクライアント証明書を選択します。 これは、手順 2 でインストールした証明書です。

  ![証明書](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **[Choose An Identity]\(ID の選択\)** では、選択できる証明書の一覧が表示されます。 適切な証明書を選択し、**[続ける]** をクリックします。

  ![ID](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. **[ローカル ID]** フィールドに、(手順 6 の) 証明書の名前を指定します。 この例では、"ikev2Client.com" です。 次に、**[適用]** ボタンをクリックして変更を保存します。

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. **[ネットワーク]** ダイアログで、**[適用]** をクリックしてすべての変更を保存します。 次に、**[接続]** をクリックして、Azure VNet への P2S 接続を開始します。

## <a name="next-steps"></a>次の手順

[P2S 構成を完了する](vpn-gateway-howto-point-to-site-rm-ps.md)ための記事に戻ります。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。