---
title: Endereços IP no Azure
description: Saiba como encontrar endereços IP de entrada e saída para aplicativos de função e o que faz com que eles sejam alterados.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 2c248756899459e17082bcab863a4e857b594909
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608224"
---
# <a name="ip-addresses-in-azure-functions"></a>Endereços IP no Azure

Este artigo explica os seguintes conceitos relacionados aos endereços IP dos aplicativos de funções:

* Localizando os endereços IP atualmente em uso por um aplicativo de funções.
* Condições que fazem com que os endereços IP do aplicativo de funções sejam alterados.
* Restrição dos endereços IP que podem acessar um aplicativo de funções.
* Definindo endereços IP dedicados para um aplicativo de funções.

Os endereços IP estão associados a aplicativos de função, não a funções individuais. Solicitações HTTP de entrada não podem usar o endereço IP de entrada para chamar funções individuais; eles devem usar o nome de domínio padrão (functionappname.azurewebsites.net) ou um nome de domínio personalizado.

## <a name="function-app-inbound-ip-address"></a>Endereço IP de entrada do aplicativo de função

Cada aplicativo de função possui um único endereço IP de entrada. Para encontrar esse endereço IP:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até o aplicativo de função.
3. Em **Configurações**, selecione **Propriedades**. O endereço IP de entrada aparece em **endereço IP virtual**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Função de endereços IP de saída do aplicativo

Cada aplicativo de função tem um conjunto de endereços IP de saída disponíveis. Qualquer conexão de saída de uma função, como um banco de dados back-end, usa um dos endereços IP de saída disponíveis como o endereço IP de origem. Você não pode saber de antemão qual endereço IP uma determinada conexão usará. Por esse motivo, seu serviço de back-end deve abrir seu firewall para todos os endereços IP de saída do aplicativo de função.

Para encontrar os endereços IP de saída disponíveis para um aplicativo de função:

1. Faça login no [Azure Resource Explorer](https://resources.azure.com).
2. Selecione **assinaturas> {sua assinatura}> provedores> Microsoft.Web> sites**.
3. No painel JSON, encontre o site com uma propriedade `id` que termine no nome do seu aplicativo de função.
4. Veja `outboundIpAddresses` e `possibleOutboundIpAddresses`. 

O conjunto de `outboundIpAddresses` está atualmente disponível para o aplicativo de função. O conjunto de `possibleOutboundIpAddresses` inclui endereços IP que estarão disponíveis somente se o aplicativo de função [for dimensionado para outras camadas de preços](#outbound-ip-address-changes).

Uma maneira alternativa de encontrar os endereços IP de saída disponíveis é usando o [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> Quando um aplicativo de funções que é executado no [plano de consumo](consumption-plan.md) ou no [plano Premium](functions-premium-plan.md) é dimensionado, um novo intervalo de endereços IP de saída pode ser atribuído. Ao executar em qualquer um desses planos, talvez seja necessário adicionar o data center inteiro a uma lista de permissões.

## <a name="data-center-outbound-ip-addresses"></a>Endereços IP de saída do data center

Se você precisar adicionar os endereços IP de saída usados por seus aplicativos de funções a uma storelist, outra opção é adicionar o data center de aplicativos de funções (região do Azure) a umalist de permissão. Você pode [fazer o download de um arquivo JSON que lista endereços IP para todos os datacenters do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Em seguida, localize o elemento JSON que se aplica à região em que seu aplicativo de função é executado.

Por exemplo, o fragmento JSON a seguir é o que alist de permissão para Europa ocidental poderia ter:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Para obter informações sobre quando este arquivo é atualizado e quando os endereços IP são alterados, expanda a seção **Detalhes** da [página do Centro de Download](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Mudanças no endereço IP de entrada

O endereço IP de entrada **pode** mudar quando você:

- Exclua um aplicativo de função e recrie-o em um grupo de recursos diferente.
- Exclua o último aplicativo de função em uma combinação de grupo de recursos e região e recrie-o.
- Exclua uma associação TLS, como durante a [renovação do certificado](../app-service/configure-ssl-certificate.md#renew-certificate).

Quando seu aplicativo de funções é executado em um [plano de consumo](consumption-plan.md) ou em um [plano Premium](functions-premium-plan.md), o endereço IP de entrada também pode ser alterado mesmo quando você não executou nenhuma ação, como as [listadas acima](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Mudanças no endereço IP de saída

O conjunto de endereços IP de saída disponíveis para um aplicativo de função pode mudar quando você:

* Execute qualquer ação que possa alterar o endereço IP de entrada.
* Altere a camada de preços do seu plano de serviço do aplicativo. A lista de todos os possíveis endereços IP de saída que seu aplicativo pode usar, para todas as camadas de preços, está na `possibleOutboundIPAddresses`propriedade. Consulte [Localizar IPs de saída](#find-outbound-ip-addresses).

Quando seu aplicativo de funções é executado em um [plano de consumo](consumption-plan.md) ou em um [plano Premium](functions-premium-plan.md), o endereço IP de saída também pode ser alterado mesmo quando você não tiver feito nenhuma ação, como as [listadas acima](#inbound-ip-address-changes).

Use o procedimento a seguir para forçar deliberadamente uma alteração de endereço IP de saída:

1. Expanda seu plano de serviço de aplicativos para cima ou para baixo entre os níveis de preços Padrão e Premium v2.

2. Esperar 10 minutos.

3. Volte para onde você começou.

## <a name="ip-address-restrictions"></a>Restrições de endereço IP

Você pode configurar uma lista de endereços IP que você deseja permitir ou negar acesso a um aplicativo de função. Para obter mais informações, consulte [Restrições de IP estático do Serviço de Aplicativo do Azure](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Endereços IP dedicados

Há várias estratégias para explorar quando seu aplicativo de funções requer endereços IP dedicados e estáticos. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Gateway NAT de rede virtual para IP estático de saída

Você pode controlar o endereço IP do tráfego de saída de suas funções usando um gateway NAT de rede virtual para direcionar o tráfego por meio de um endereço IP público estático. Você pode usar essa topologia quando estiver executando em um [plano Premium](functions-premium-plan.md). Para saber mais, consulte [tutorial: controle Azure Functions IP de saída com um gateway NAT da rede virtual do Azure](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>Ambientes de Serviço de Aplicativo

Para ter controle total sobre os endereços IP, tanto de entrada como de saída, recomendamos [ambientes de serviço de aplicativo](../app-service/environment/intro.md) (a [camada isolada](https://azure.microsoft.com/pricing/details/app-service/) de planos do serviço de aplicativo). Para obter mais informações, consulte [Endereços IP do Ambiente de Serviço de Aplicativo](../app-service/environment/network-info.md#ase-ip-addresses) e [Como controlar o tráfego de entrada para um Ambiente de Serviço de Aplicativo](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Para descobrir se seu aplicativo de função é executado em um Ambiente de Serviço de Aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até o aplicativo de função.
3. Selecione a guia **Visão geral**.
4. A camada do plano de Serviço de Aplicativo aparece em **Plano de serviço de aplicativo / camada de preço**. A camada de preços do Ambiente de Serviço de Aplicativo é **Isolado**.
 
Como alternativa, você pode usar o [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

O Ambiente do Serviço de Aplicativo `sku` é `Isolated`.

## <a name="next-steps"></a>Próximas etapas

Uma causa comum de alterações de IP é a função de escala de aplicativos. [Saiba mais sobre o dimensionamento do aplicativo de função](functions-scale.md).
