---
title: Contornar as configurações de firewall de armazenamento
description: Uma configuração de firewall de rede da conta de armazenamento pode causar falha ao criar um destino de armazenamento de BLOBs do Azure no cache do HPC do Azure. Este artigo fornece uma solução alternativa para a limitação até que uma correção de software esteja em vigor.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 10d68ce679fe42f5deeaae364bc46adb23436a27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587144"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Ajustar as configurações de firewall da conta de armazenamento de blobs

Uma configuração específica usada em firewalls de conta de armazenamento pode fazer com que a criação de destino do armazenamento de BLOBs falhe. A equipe de cache do Azure HPC está trabalhando em uma correção de software para esse problema, mas você pode contorná-la seguindo as instruções neste artigo.

A configuração de firewall que permite o acesso somente de "redes selecionadas" pode impedir que o cache crie ou modifique um destino de armazenamento de BLOBs. Essa configuração está na página de configurações **firewalls e redes virtuais** da conta de armazenamento.

O problema é que o serviço de cache usa uma rede virtual de serviço oculto que é separada dos ambientes de clientes. Não é possível autorizar explicitamente esta rede a acessar sua conta de armazenamento.

Quando você cria um destino de armazenamento de BLOBs, o serviço de cache usa essa rede para verificar se o contêiner está vazio ou não. Se o firewall não permitir acesso da rede oculta, a verificação falhará e a criação do destino de armazenamento falhará.

O firewall também pode bloquear alterações nos caminhos do namespace de destino do armazenamento de BLOBs.

Para contornar o problema, altere temporariamente as configurações de firewall ao criar o destino de armazenamento:

1. Vá para a página firewalls da conta de armazenamento **e redes virtuais** e altere a configuração "permitir acesso de" para **todas as redes**.
1. Crie o destino de armazenamento de BLOBs no cache do Azure HPC.
1. Crie o caminho do namespace do destino de armazenamento.
1. Depois que o destino e o caminho de armazenamento tiverem sido criados com êxito, altere a configuração de firewall da conta de volta para **redes selecionadas**.

O cache HPC do Azure não usa a rede virtual de serviço para acessar o destino de armazenamento concluído.

Para obter ajuda com essa solução alternativa, [entre em contato com o serviço e suporte da Microsoft](hpc-cache-support-ticket.md).
