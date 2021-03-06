---
title: incluir arquivo
description: incluir arquivo
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 68ce927c05f7179cca0aa2833460b9550f0a82d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027504"
---
> [!div class="op_single_selector"]
> * [Azure portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI do Azure](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

Uma VM (máquina virtual) do Azure tem uma ou mais NICs (adaptadores de rede) conectadas a ele. Qualquer NIC pode ter um ou mais endereços IP públicos e privados estáticos ou dinâmicos atribuídos a ele. A atribuição de vários endereços IP a uma VM permite as seguintes capacidades:

* Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
* Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
* A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Todas as NICs anexadas a uma VM têm uma ou mais configurações IP associadas a elas. Cada configuração recebe um endereço IP privado estático ou dinâmico. Cada configuração também pode ter um recurso de endereço IP público associado a ele. Um recurso de endereço IP público tem um endereço IP público dinâmico ou estático atribuído a ele. Para saber mais sobre endereços IP no Azure, leia o artigo [Endereços IP no Azure](../articles/virtual-network/public-ip-addresses.md). 

Há um limite de quantos endereços IP privados podem ser atribuídos a uma NIC. Há também um limite de quantos endereços IP públicos podem ser usados em uma assinatura do Azure. Leia o artigo [Limites de Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.