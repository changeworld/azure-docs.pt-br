---
title: Série H-máquinas virtuais do Azure
description: Especificações para as VMs da série H.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b3e3beb51256bbf22d29d74b51c52ca3a6bee0c4
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774772"
---
# <a name="h-series"></a>Série H

As VMs da série H são otimizadas para aplicativos orientados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, até 14 GB de RAM por núcleo de CPU e sem hyperthreading. A série H apresenta 56 GB/s Mellanox FDR InfiniBand em uma configuração de árvore de Fat sem bloqueio para desempenho consistente de RDMA. As VMs da série H não são habilitadas para o SR-IOV atualmente e dão suporte ao Intel MPI 5. x e ao MS-MPI.

[ACU](acu.md): 290-300<br>
[Armazenamento Premium](premium-storage-performance.md): sem suporte<br>
[Armazenamento em cache Premium](premium-storage-performance.md): sem suporte<br>
[Migração ao vivo](maintenance-and-updates.md): sem suporte<br>
[Atualizações de preservação de memória](maintenance-and-updates.md): sem suporte<br>
[Suporte à geração de VM](generation-2.md): geração 1<br>
[Rede acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): sem suporte<br>
[Discos do sistema operacional efêmero](ephemeral-os-disks.md): sem suporte <br>
<br>

| Tamanho | vCPU | Processador | Memória (GiB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GiB) | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | VNICs Ethernet máx. |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32 x 500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64 x 500 | 4 |

<sup>1</sup> para aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR.

> [!NOTE]
> Entre as [VMs compatíveis com RDMA](sizes-hpc.md#rdma-capable-instances), a série H não é habilitada para Sr-iov. Portanto, as [imagens de VM](./workloads/hpc/configure.md#vm-images)com suporte, os requisitos de [Driver InfiniBand](./workloads/hpc/enable-infiniband.md) e as [bibliotecas MPI](./workloads/hpc/setup-mpi.md) com suporte são diferentes das VMs habilitadas para Sr-iov.

## <a name="software-specifications"></a>Especificações de software

| Especificações de software     |VM da série HC           |
|-----------------------------|-----------------------|
| Tamanho máximo do trabalho MPI            | 4800 núcleos (300 VMs em um único conjunto de dimensionamento de máquinas virtuais com singlePlacementGroup = true)  |
| Suporte a MPI                 | Intel MPI 5. x, MS-MPI  |
| Suporte do so para RDMA não SRIOV   | CentOS/RHEL 6,5-7,4, SLES 12 SP4 +, WinServer 2012-2016  |
| Suporte do Orchestrator        | CycleCloud, lote, AKS  |

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Propósito geral](sizes-general.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

- Leia sobre os comunicados mais recentes, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs da comunidade técnica de computação do Azure](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para obter uma visão de nível superior da arquitetura de execução de cargas de trabalho de HPC, confira [HPC (computação de alto desempenho) no Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.
