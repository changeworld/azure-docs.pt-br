---
title: Gerenciar contêineres de volume para dispositivos StorSimple da série 8000
description: Explica como você pode usar a página de contêineres de volume do serviço do Gerenciador de Dispositivos do StorSimple para adicionar, modificar ou excluir um contêiner de volume.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545192"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Usar o serviço do Gerenciador de Dispositivos do StorSimple para gerenciar contêineres de volume do StorSimple

## <a name="overview"></a>Visão geral
Este tutorial explica como usar o serviço de Gerenciador de Dispositivos do StorSimple para criar e gerenciar contêineres de volume do StorSimple.

Um contêiner de volume em um dispositivo Microsoft Azure StorSimple contém um ou mais volumes que compartilham as configurações da conta de armazenamento, da criptografia e do consumo de largura de banda. Um dispositivo pode ter vários contêineres de volume para todos os seus volumes. 

Um contêiner de volume apresenta os seguintes atributos:

* **Volumes** – os volumes do StorSimple em camadas ou fixados localmente que estão contidos no contêiner do volume. 
* **Criptografia** – Uma chave de criptografia que pode ser definida para cada contêiner de volume. Essa chave é usada para criptografar os dados enviados do seu dispositivo StorSimple para a nuvem. Uma chave de bits AES-256 de nível militar é usada com a chave inserida pelo usuário. Para proteger seus dados, é recomendável sempre habilitar a criptografia de armazenamento na nuvem.
* **Conta de armazenamento** – a conta de armazenamento do Azure usada para armazenar os dados. Todos os volumes que residem em um contêiner de volume compartilham essa conta de armazenamento. Você pode escolher uma conta de armazenamento em uma lista existente ou criar uma nova conta quando criar o contêiner de volume e especificar as credenciais de acesso para essa conta.
* **Largura da banda em nuvem** – A largura da banda consumida pelo dispositivo quando os dados do dispositivo estão sendo enviados para a nuvem. Imponha um controle de largura de banda especificando um valor entre 1 e 1.000 Mbps ao criar esse contêiner. Se deseja que o dispositivo consuma toda a largura de banda disponível, defina este campo para **Ilimitado**. Também é possível criar e aplicar um modelo de largura de banda para alocar a largura de banda com base no agendamento.

Os seguintes procedimentos explicam como usar a folha **Contêineres de volume** do StorSimple para concluir as seguintes operações comuns:

* Adicionar um contêiner de volume
* Modificar um contêiner de volume
* Excluir um contêiner de volume

## <a name="add-a-volume-container"></a>Adicionar um contêiner de volume
Execute as etapas a seguir para adicionar um contêiner de volume.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificar um contêiner de volume
Execute as etapas a seguir para modificar um contêiner de volume.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Excluir um contêiner de volume
Um contêiner de volume possui volumes dentro dele. Ele poderá ser excluído somente se todos os volumes contidos nele forem excluídos primeiro. Execute as etapas a seguir para excluir um contêiner de volume.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [como gerenciar volumes do StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Saiba mais sobre como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

