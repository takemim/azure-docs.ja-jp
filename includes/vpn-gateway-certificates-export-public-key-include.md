---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e19837c1d16ddeea185f340305a0c9c52ce23ff
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
自己署名ルート証明書を作成した後、ルート証明書の (秘密キーではなく) 公開キーの .cer ファイルをエクスポートします。 後でこのファイルを Azure にアップロードします。 次の手順で、自己署名ルート証明書の .cer ファイルをエクスポートしてください。

1. 証明書から .cer ファイルを取得するには、**[ユーザー証明書の管理]** を開きます。 自己署名ルート証明書を探して右クリックします (通常は 'Current User\Personal\Certificates' にあります)。 **[すべてのタスク]**、**[エクスポート]** の順にクリックします。 **証明書のエクスポート ウィザード**が開きます。

  ![エクスポート](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. ウィザードで **[次へ]** をクリックします。

  ![証明書をエクスポートします。](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. **[いいえ、秘密キーをエクスポートしません]** を選択して、**[次へ]** をクリックします。

  ![秘密キーをエクスポートしません](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. **[エクスポート ファイルの形式]** ページで **[Base-64 encoded X.509 (.CER)]** を選択し、**[次へ]** をクリックします。

  ![Base-64 エンコード](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. **[エクスポートするファイル]** で、**[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]**に証明書ファイルの名前を指定します。 次に、 **[次へ]**をクリックします。

  ![参照](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. **[完了]** をクリックして、証明書をエクスポートします。

  ![[完了]](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. 証明書が正常にエクスポートされました。

  ![成功](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. エクスポートされた証明書は次のようになります。

  ![エクスポート済み](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. メモ帳を使ってエクスポートした証明書を開くと、この例のようなものが表示されます。 青で示したセクションには、Azure にアップロードされる情報が含まれます。 証明書をメモ帳で開いてもこのように表示されない場合は、通常、Base-64 エンコードの X.509 (.CER) 形式を使ってエクスポートしなかったことを意味します。 また、別のテキスト エディターを使う場合は、一部のエディターでは意図しない書式設定がバックグラウンドで行われる場合があることに注意してください。 これにより、この証明書から Azure にテキストをアップロードすると問題が発生する可能性があります。

  ![メモ帳で開く](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)