---
title: Criar assinaturas do Azure de modo programático para um Contrato de Cliente da Microsoft com as APIs mais recentes
description: Saiba como criar assinaturas do Azure para um Contrato de Cliente da Microsoft de modo programático usando as versões mais recentes da API REST, da CLI do Azure e do Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 61a658cc9654a93b4c92fda6cc1f38cd2e77dafa
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216081"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>Criar assinaturas do Azure de modo programático para um Contrato de Cliente da Microsoft com as APIs mais recentes

Este artigo ajudará você a criar assinaturas do Azure de modo programático para um Contrato de Cliente da Microsoft usando as versões mais recentes da API. Caso ainda esteja usando a versão prévia mais antiga, confira como [Criar assinaturas do Azure de modo programático com APIs de versão prévia](programmatically-create-subscription-preview.md). 

Neste artigo, você aprenderá a criar assinaturas programaticamente usando o Azure Resource Manager.

Quando você cria uma assinatura do Azure programaticamente, essa assinatura é regida pelo contrato sob o qual você obteve serviços Azure da Microsoft ou de um revendedor autorizado. Para obter mais informações, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para criar assinaturas, você precisa ter uma função de proprietário, de colaborador ou de criador de assinatura do Azure em uma seção de fatura; ou uma função de proprietário ou de colaborador em um perfil de cobrança ou uma conta de cobrança. Para obter mais informações, confira [Funções e tarefas da cobrança de assinatura](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Se você não souber se tem acesso a uma conta do Contrato de Cliente da Microsoft, confira [Verificar o acesso a um Contrato de Cliente da Microsoft](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

Os exemplos a seguir usam APIs REST. Atualmente, não há suporte para o PowerShell nem para a CLI do Azure.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Localizar as contas de cobrança às quais você tem acesso

Faça a solicitação a seguir para listar todas as contas de cobrança.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
A resposta da API lista as contas de cobrança às quais você tem acesso.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreementType da conta é *MicrosoftCustomerAgreement*. Copie o `name` da conta.  Por exemplo, para criar uma assinatura para a conta de cobrança `Contoso`, copie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para usá-lo na próxima etapa.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-getBillingAccounts)

```azurepowershell-interactive
PS C:\WINDOWS\system32> Get-AzBillingAccount
```
Você receberá uma lista de todas as contas de cobrança às quais você tem acesso 

```json
Name          : 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
DisplayName   : Contoso
AccountStatus : Active
AccountType   : Enterprise
AgreementType : MicrosoftCustomerAgreement
HasReadAccess : True
```
Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreementType da conta é *MicrosoftCustomerAgreement*. Copie o `name` da conta.  Por exemplo, para criar uma assinatura para a conta de cobrança `Contoso`, copie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para usá-lo na próxima etapa.


### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-getBillingAccounts)
```azurecli
> az billing account list
```
Você receberá uma lista de todas as contas de cobrança às quais você tem acesso 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Enterprise",
    "agreementType": "MicrosoftCustomerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreementType da conta é *MicrosoftCustomerAgreement*. Copie o `name` da conta.  Por exemplo, para criar uma assinatura para a conta de cobrança `Contoso`, copie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para usá-lo na próxima etapa.

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Localizar perfis de cobrança e seções de fatura para criar assinaturas

Os preços da sua assinatura serão exibidos em uma seção da fatura do perfil de cobrança. Use a API a seguir para obter uma lista de perfis de cobrança e seções de fatura nos quais você tem permissão para criar assinaturas do Azure.

Primeiro, você obtém a lista de perfis de cobrança na conta de cobrança à qual você tem acesso (use o `name` que você obteve na etapa anterior)

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
A resposta da API listará todos os perfis de cobrança aos quais você tem acesso para criar assinaturas:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Copie a `id` para identificar a seguir as seções de fatura abaixo do perfil de cobrança. Por exemplo, copie `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` e chame a API a seguir.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Resposta

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Use a propriedade `id` para identificar a seção da fatura para a qual você deseja criar assinaturas. Copie toda a cadeia de caracteres. Por exemplo, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-getBillingProfiles)

```powershell-interactive
PS C:\WINDOWS\system32> Get-AzBillingProfile -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

Você receberá a lista de perfis de cobrança nesta conta como parte da resposta.

```json
Name              : AW4F-xxxx-xxx-xxx
DisplayName       : Contoso Billing Profile
Currency          : USD
InvoiceDay        : 5
InvoiceEmailOptIn : True
SpendingLimit     : Off
Status            : Active
EnabledAzurePlans : {0002, 0001}
HasReadAccess     : True
BillTo            :
CompanyName       : Contoso
AddressLine1      : One Microsoft Way
AddressLine2      : 
City              : Redmond
Region            : WA
Country           : US
PostalCode        : 98052
```

Observe o `name` do perfil de cobrança da resposta acima. As próximas etapas incluem obter a seção de fatura a que você tem acesso abaixo desse perfil de cobrança. Você precisará do `name` da conta de cobrança e do perfil de cobrança

```powershell-interactive
PS C:\WINDOWS\system32> Get-AzInvoiceSection -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx -BillingProfileName AW4F-xxxx-xxx-xxx
```

A seção de fatura será retornada

```json
Name        : SH3V-xxxx-xxx-xxx
DisplayName : Development
```

O `name` acima é o nome da seção de fatura em que você precisa criar uma assinatura. Construa seu escopo do orçamento usando o formato "/providers/Microsoft.Billing/billingAccounts/<BillingAccountName>/billingProfiles/<BillingProfileName>/invoiceSections/<InvoiceSectionName>". Nesse exemplo, isso será equivalente a `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-getBillingProfiles)

```azurecli-interactive
> az billing profile list --account-name "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --expand "InvoiceSections"
```
Esta API retornará a lista de perfis de cobrança e seções de fatura na conta de cobrança fornecida.

```json
[
  {
    "billTo": {
      "addressLine1": "One Microsoft Way",
      "addressLine2": "",
      "addressLine3": null,
      "city": "Redmond",
      "companyName": "Contoso",
      "country": "US",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": "98052",
      "region": "WA"
    },
    "billingRelationshipType": "Direct",
    "currency": "USD",
    "displayName": "Contoso Billing Profile",
    "enabledAzurePlans": [
      {
        "skuDescription": "Microsoft Azure Plan for DevTest",
        "skuId": "0002"
      },
      {
        "skuDescription": "Microsoft Azure Plan",
        "skuId": "0001"
      }
    ],
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
    "indirectRelationshipInfo": null,
    "invoiceDay": 5,
    "invoiceEmailOptIn": true,
    "invoiceSections": {
      "hasMoreResults": false,
      "value": [
        {
          "displayName": "Field_Led_Test_Ace",
          "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
          "labels": null,
          "name": "SH3V-xxxx-xxx-xxx",
          "state": "Active",
          "systemId": "SH3V-xxxx-xxx-xxx",
          "targetCloud": null,
          "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
        }
      ]
    },
    "name": "AW4F-xxxx-xxx-xxx",
    "poNumber": null,
    "spendingLimit": "Off",
    "status": "Warned",
    "statusReasonCode": "PastDue",
    "systemId": "AW4F-xxxx-xxx-xxx",
    "targetClouds": [],
    "type": "Microsoft.Billing/billingAccounts/billingProfiles"
  }
]
```
Use a propriedade id no objeto da seção de fatura para identificar a seção de fatura para a qual você deseja criar assinaturas. Copie toda a cadeia de caracteres. Por exemplo, /providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx.

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Criar uma assinatura de uma seção da fatura

O exemplo a seguir criará uma assinatura chamada *Assinatura da Equipe de Desenvolvimento* para a seção de fatura *Desenvolvimento*. A assinatura será cobrada no perfil de cobrança chamado *Perfil de Cobrança da Contoso* e aparecerá na seção *Desenvolvimento* da fatura da empresa. Use o escopo do orçamento copiado da etapa anterior: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Corpo da solicitação

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```

### <a name="response"></a>Resposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

É possível executar uma função GET na mesma URL para obter o status da solicitação.

### <a name="request"></a>Solicitação

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Resposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Um status em andamento será retornado como um estado `Accepted` em `provisioningState`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MCA)

Para instalar a versão mais recente do módulo que contém o cmdlet `New-AzSubscriptionAlias`, execute `Install-Module Az.Subscription`. Para instalar uma versão recente do PowerShellGet, confira [Obter o Módulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) e o escopo do orçamento `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
```

Você obterá subscriptionId como parte da resposta do comando.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-MCA)

Primeiro, instale a extensão executando `az extension add --name account` e `az extension add --name alias`.

Execute o comando [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create).

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
```

Você obterá subscriptionId como parte da resposta do comando.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="next-steps"></a>Próximas etapas

* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerenciar um grande número de assinaturas usando grupos de gerenciamento, confira como [Organizar seus recursos com os grupos de gerenciamento do Azure](../../governance/management-groups/overview.md).
