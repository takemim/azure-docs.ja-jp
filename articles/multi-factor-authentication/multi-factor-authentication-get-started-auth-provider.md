---
title: "Azure 多要素認証プロバイダーの概要 | Microsoft Docs"
description: "Azure Multi-Factor Auth プロバイダーの作成方法について説明します。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: b04fd6f969461cf39016df951007c59047c8857a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Azure Multi-Factor Authentication プロバイダーの概要
Azure Active Directory を持つグローバル管理者と Office 365 ユーザーは、既定で 2 段階認証を使用できます。 ただし、[高度な機能](multi-factor-authentication-whats-next.md)が必要な場合は、通常版の Multi-Factor Authentication (MFA) をご購入ください。

Azure Multi-Factor Auth プロバイダーは、通常版の Azure MFA に備わっている機能を活用するために使用されます。 その対象となるのは、**Azure MFA、Azure AD Premium、Enterprise Mobility + Security (EMS) のいずれのライセンスも持たないユーザー**です。  Azure MFA、Azure AD Premium、EMS には、通常版の Azure MFA が既定で含まれています。 ライセンスを所有している場合、Azure 多要素認証プロバイダーは必要ありません。

SDK をダウンロードする場合は、Azure 多要素認証プロバイダーが必要です。

> [!IMPORTANT]
> Azure Multi-Factor Authentication ソフトウェア開発キット (SDK) の廃止が発表されました。 新規のお客様には、この機能はサポートされなくなりました。 現在のお客様は、2018 年 11 月 14 日まで、SDK の使用を継続できます。 その後は、SDK への呼び出しは失敗します。

> [!IMPORTANT]
>SDK をダウンロードするには、Azure MFA、AAD Premium、または EMS ライセンスを所有している場合でも、Azure多要素認証プロバイダーを作成する必要があります。  既にライセンスがある状態で、SDK のダウンロードのために Azure 多要素認証プロバイダーを作成する場合には、プロバイダーの作成に **[有効化されたユーザーごと]** モデルを使用してください。 プロバイダーを作成したら、Azure MFA、Azure AD Premium、または EMS のライセンスが保存されているディレクトリにリンクします。 この構成により、所有しているライセンス数よりも 2 段階認証を実行する一意のユーザーの数が多い場合でも、適切な課金が行われます。 

## <a name="what-is-an-mfa-provider"></a>MFA プロバイダーとは

Azure Multi-factor Authentication のライセンスを持っていない場合は、ユーザーに 2 段階認証を要求する認証プロバイダーを作成できます。

2 種類の認証プロバイダーがあり、違いは Azure サブスクリプションの課金方法です。 認証ごとのオプションは、1 か月間にテナントに対して実行された認証の数を計算します。 このオプションは、ときどき認証を行うユーザーが多数いる場合に最適です。 ユーザーごとのオプションは、1 か月間に 2 段階認証を実行するテナント内の個人の数を計算します。 このオプションは、ライセンスを持つユーザーはいますが、ライセンス制限を超えてより多くのユーザーに MFA を拡張する必要がある場合に最適です。

## <a name="create-an-mfa-provider"></a>MFA プロバイダーの作成

Azure Portal で Azure Multi-Factor Authentication プロバイダーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。 
2. **[Azure Active Directory]** > **[MFA Server]** > **[プロバイダー]** を選択します。

   ![プロバイダー][Providers]

3. **[追加]**を選択します。
4. 次の各フィールドの内容を入力し、**[追加]** を選択します。
   - **名前** - プロバイダーの名前。
   - **使用モデル** – 次の 2 つのオプションのいずれかを選ぶことができます。
      * 認証ごと – 認証ごとに課金される購入モデル。 通常、コンシューマー向けのアプリケーションで Azure Multi-factor Authentication を使用するシナリオで使用されます。
      * 有効ユーザーごと – 有効ユーザーごとに課金される購入モデル。 通常、Office 365 などのアプリケーションにアクセスに従業員向けに使用されます。 Azure MFA のライセンスを既に許諾されているユーザーがいる場合には、このオプションを選択します。
   - **サブスクリプション** - プロバイダーを使用した 2 段階認証アクティビティの課金対象となる Azure サブスクリプション。 
   - **ディレクトリ** - プロバイダーに関連付けられている Azure Active Directory テナント。
      * プロバイダーの作成に、Azure AD ディレクトリは必要ありません。 Azure Multi-Factor Authentication Server をダウンロードするだけであれば、空欄にしておいてください。
      * 高度な機能を利用するためには、Azure AD ディレクトリにプロバイダーを関連付ける必要があります。
      * 1 つの Azure AD ディレクトリに関連付けることができるプロバイダーは 1 つだけです。

## <a name="manage-your-mfa-provider"></a>MFA プロバイダーの管理

MFA プロバイダーの作成後に使用モデル (有効化されたユーザーごと、または認証ごと) を変更することはできません。 ただし、MFA プロバイダーを削除すれば、別の使用モデルで新しい MFA プロバイダーを作成できます。

現在の Multi-Factor Auth プロバイダーが Azure AD ディレクトリ (Azure AD テナントとも呼ばれます) に関連付けられている場合は、その MFA プロバイダーを安全に削除し、同じ Azure AD テナントにリンクされた新しい MFA プロバイダーを作成することができます。 また、MFA が有効化されているすべてのユーザーに対応できる、十分な数の MFA、Azure AD Premium、または Enterprise Mobility + Security (EMS) ライセンスを購入している場合は、MFA プロバイダーをすべて削除することもできます。

MFA プロバイダーが Azure AD テナントにリンクされていない場合、または新しい MFA プロバイダーを別の Azure AD テナントにリンクする場合、ユーザー設定と構成オプションは転送されません。 また、既存の Azure MFA サーバーは、新しい MFA プロバイダーによって生成されるアクティブ化資格情報を使用して再アクティブ化する必要があります。 MFA Server を新しい MFA プロバイダーにリンクするために再アクティブ化しても、電話呼び出しやテキスト メッセージによる認証には影響ありませんが、モバイル アプリ通知は、各ユーザーがモバイル アプリを再アクティブ化するまで機能しなくなります。

## <a name="next-steps"></a>次のステップ

[Multi-Factor Authentication の設定を構成する](multi-factor-authentication-whats-next.md)

[Providers]: ./media/multi-factor-authentication-get-started-auth-provider/add-providers.png "MFA プロバイダーの追加"
