---
title: "Azure CLI を使用したロールベースの Access Control (RBAC) の管理 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイスを使用して、ロールとロールのアクションの表示、サブスクリプションとアプリケーションのスコープへのロールの割り当てなどを行って、ロールベースの Access Control (RBAC) を管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 6c9df11e528601d94cb72a8e3ef0868dc7781e12
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Azure コマンド ライン インターフェイスを使用したロールベースの Access Control の管理

> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)


ロールベースのアクセス制御 (RBAC) を使用して特定の範囲にロールを割り当てることにより、ユーザー、グループ、およびサービス プリンシパルのアクセスを定義します。 この記事では、Azure コマンド ライン インターフェイス (CLI) 2.0 を使用してアクセスを管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure CLI を使用して RBAC を管理するには、以下の前提条件が整っている必要があります。

* [Azure CLI 2.0](/cli/azure)。 ブラウザーの [Azure Cloud Shell](../cloud-shell/overview.md) で使用できるほか、macOS、Linux、Windows に[インストール](/cli/azure/install-azure-cli)してコマンド ラインで実行することもできます。

## <a name="list-roles"></a>ロールの一覧表示

### <a name="list-role-definitions"></a>ロール定義の一覧表示

使用可能なすべてのロール定義を一覧表示するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用します。

```azurecli
az role definition list
```

次の例では、使用可能なすべてのロール定義の名前と説明を一覧表示します。

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

次の例では、すべての組み込みのロール定義を一覧表示します。

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description, "type":.properties.type}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "type": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>ロール定義の動作の一覧表示

ロール定義の動作を一覧表示するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用します。

```azurecli
az role definition list --name <role_name>
```

次の例では、"*共同作成者*" ロール定義を一覧表示します。

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "properties": {
      "additionalProperties": {
        "createdBy": null,
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedBy": null,
        "updatedOn": "2016-12-14T02:04:45.1393855Z"
      },
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything except access to resources.",
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": [
            "Microsoft.Authorization/*/Delete",
            "Microsoft.Authorization/*/Write",
            "Microsoft.Authorization/elevateAccess/Action"
          ]
        }
      ],
      "roleName": "Contributor",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

次の例では、"*共同作成者*" ロールの *actions* および *notActions* を一覧表示します。

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.properties.permissions[0].actions, "notActions":.properties.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

次の例では、"*仮想マシンの共同作成者*" ロールの動作を一覧表示します。

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .properties.permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>アクセス権の表示

### <a name="list-role-assignments-for-a-user"></a>ユーザーのロールの割り当ての表示

特定のユーザーのロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) を使用します。

```azurecli
az role assignment list --assignee <assignee>
```

既定では、サブスクリプションをスコープとする割り当てのみが表示されます。 リソースまたはグループでスコープとされている割り当てを表示するには、`--all` を使用します。

次の例では、*patlong@contoso.com* ユーザーに直接割り当てられているロールの割り当てを一覧表示します。

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.properties.principalName, "roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>リソース グループに対するロールの割り当ての一覧表示

リソース グループ用に存在しているロールの割り当てを一覧表示するには、[az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) を使用します。

```azurecli
az role assignment list --resource-group <resource_group>
```

次の例では、*pharma-sales-projectforecast* リソース グループに対するロールの割り当てを一覧表示します。

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="assign-access"></a>アクセス権を割り当てる

### <a name="assign-a-role-to-a-user"></a>ユーザーにロールを割り当てる

リソース グループのスコープでユーザーにロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用します。

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

次の例では、*pharma-sales-projectforecast* リソース グループのスコープで、*patlong@contoso.com* ユーザーに "*仮想マシンの共同作成者*" ロールを付与します。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="assign-a-role-to-a-group"></a>グループにロールを割り当てる

グループにロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用します。

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

次の例では、サブスクリプション スコープで ID 22222222-2222-2222-2222-222222222222 を使って、"*閲覧者*" ロールを *Ann Mack Team* グループに割り当てます。 グループの ID を取得するには、[az ad group list](/cli/azure/ad/group#az_ad_group_list) または [az ad group show](/cli/azure/ad/group#az_ad_group_show) を使用できます。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

次の例では、*pharma-sales-project-network* という名前の仮想ネットワークのリソース スコープで、ID 22222222-2222-2222-2222-222222222222 を使って "*仮想マシンの共同作成者*" ロールを *Ann Mack Team* グループに割り当てます。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="assign-a-role-to-an-application"></a>アプリケーションにロールを割り当てる

アプリケーションにロールを割り当てるには、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用します。

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

次の例では、*pharma-sales-projectforecast* リソース グループのスコープで、オブジェクト ID 44444444-4444-4444-4444-444444444444 を使ってアプリケーションに "*仮想マシンの共同作成者*" ロールを割り当てています。 アプリケーションのオブジェクト ID を取得するには、[az ad app list](/cli/azure/ad/app#az_ad_app_list) または [az ad app show](/cli/azure/ad/app#az_ad_app_show) を使用できます。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>アクセス権の削除

### <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

ロールの割り当てを削除するには、[az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) を使用します。

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

次の例では、*pharma-sales-projectforecast* リソース グループの *patlong@contoso.com* ユーザーから、"*仮想マシンの共同作成者*" ロールの割り当てを削除します。

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

次の例では、サブスクリプション スコープで ID 22222222-2222-2222-2222-222222222222 を使って、"*閲覧者*" ロールを *Ann Mack Team* グループから削除します。 グループの ID を取得するには、[az ad group list](/cli/azure/ad/group#az_ad_group_list) または [az ad group show](/cli/azure/ad/group#az_ad_group_show) を使用できます。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>カスタム ロール

### <a name="list-custom-roles"></a>カスタム ロールの一覧表示

スコープで割り当てに使用できるロールを一覧表示するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用します。

次の 2 つの例では、現在のサブスクリプションのすべてのカスタム ロールを一覧表示します。

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.properties.roleName, "type":.properties.type}'
```

```azurecli
az role definition list --output json | jq '.[] | if .properties.type == "CustomRole" then {"roleName":.properties.roleName, "type":.properties.type} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[az role definition create](/cli/azure/role/definition#az_role_definition_create) を使用します。 ロール定義には、JSON 記述を含むファイルへの JSON 記述またはパスを指定できます。

```azurecli
az role definition create --role-definition <role_definition>
```

次の例では、"*仮想マシン オペレーター*" というカスタム ロールが作成されます。 このカスタム ロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作に対するアクセス許可を割り当てて、仮想マシンの起動、再起動、監視を行うためのアクセス許可を割り当ています。 このカスタム ロールは、2 つのサブスクリプションで使うことができます。 この例では、入力として JSON ファイルを使用します。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールを更新するには、最初に [az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用して、ロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、[az role definition update](/cli/azure/role/definition#az_role_definition_update) を使用して、更新されたロール定義を保存します。

```azurecli
az role definition update --role-definition <role_definition>
```

次の例では、*Microsoft.Insights/diagnosticSettings/* 操作が "*仮想マシン オペレーター*" の *Actions* に追加されます。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[az role definition delete](/cli/azure/role/definition#az_role_definition_delete) を使用します。 削除するロールを指定するには、ロール名またはロール ID を使用します。 ロール ID を決定するには、[az role definition list](/cli/azure/role/definition#az_role_definition_list) を使用します。

```azurecli
az role definition delete --name <role_name or role_id>
```

次の例では、"*仮想マシン オペレーター*" カスタム ロールが削除されます。

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>次の手順

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

