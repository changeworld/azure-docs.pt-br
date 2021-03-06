---
title: Melhores práticas para a Sincronização de Dados SQL do Azure
description: Conheça as práticas recomendadas para configurar e executar a Sincronização de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ee15bfaa1d69e2e5047e7d24986f8e4e7d5b8b31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180234"
---
# <a name="best-practices-for-azure-sql-data-sync"></a>Melhores práticas para a Sincronização de Dados SQL do Azure 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve as práticas recomendadas para a Sincronização de Dados SQL do Azure.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL **não dá suporte ao** azure SQL instância gerenciada no momento.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a> Segurança e confiabilidade

### <a name="client-agent"></a>Agente cliente

-   Instale o agente cliente usando a conta de usuário que tem o mínimo de privilégios com acesso ao serviço de rede.  
-   Instale o agente cliente em um computador que não seja o SQL Server computador.  
-   Não registre um banco de dados local com mais de um agente.    
    -   Evite isso mesmo se você estiver sincronizando diferentes tabelas para diferentes grupos de sincronização.  
    -   Registrar um banco de dados local com vários agentes cliente apresenta desafios ao se excluir um dos grupos de sincronização.

### <a name="database-accounts-with-least-required-privileges"></a>Contas de banco de dados com o mínimo de privilégios necessários

-   **Para configuração da sincronização**. Criar/alterar tabela; alterar banco de dados; criar procedimento; selecionar/alterar esquema; criar tipo definido pelo usuário.

-   **Para sincronização em andamento**. Selecionar/inserir/atualizar/excluir em tabelas selecionadas para sincronização e em metadados de sincronização e tabelas de acompanhamento; Permissão EXECUTE em procedimentos armazenados criados pelo serviço; Permissão EXECUTE em tipos de tabela definidos pelo usuário.

-   **Para desprovisionamento**. Alterar em tabelas que fazem parte da sincronização; Selecionar/Excluir em tabelas de metadados de sincronização; Controlar em tabelas de sincronização, procedimentos armazenados e tipos definidos pelo usuário.

O banco de dados SQL do Azure oferece suporte a apenas um único conjunto de credenciais. Para realizar essas tarefas dentro desta restrição, considere as seguintes opções:

-   Altere as credenciais para diferentes fases (por exemplo, *credenciais1* para instalação e *credenciais2* para em andamento).  
-   Altere a permissão das credenciais (ou seja, altere a permissão após a sincronização estar configurada).

### <a name="auditing"></a>Auditoria

É recomendável habilitar a auditoria no nível dos bancos de dados nos grupos de sincronização. 

## <a name="setup"></a>Instalação

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a> Restrições e considerações de banco de dados

#### <a name="database-size"></a>Tamanho do banco de dados

Ao criar um novo banco de dados, defina o tamanho máximo para que ele seja sempre maior do que o banco de dados implantado. Se você não definir o tamanho máximo maior do que o banco de dados implantado, a sincronização falhará. Embora a Sincronização de Dados SQL não ofereça o crescimento automático, você pode executar o comando `ALTER DATABASE` para aumentar o tamanho do banco de dados após ele ter sido criado. Certifique-se de permanecer dentro dos limites de tamanho do banco de dados.

> [!IMPORTANT]
> A Sincronização de Dados SQL armazena metadados adicionais com cada banco de dados. Não deixe de considerar esses metadados ao calcular o espaço necessário. A quantidade de sobrecarga adicionada é relacionada à largura das tabelas (por exemplo, tabelas estreitas exigem mais sobrecarga) e a quantidade de tráfego.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a> Restrições e considerações de tabela

#### <a name="selecting-tables"></a>Selecionando tabelas

Você não precisa incluir todas as tabelas que estão em um banco de dados em um grupo de sincronização. As tabelas que você incluir em um grupo de sincronização afetam a eficiência e os custos. Inclua tabelas e as tabelas de que dependem, em um grupo de sincronização somente se for exigido pela empresa.

#### <a name="primary-keys"></a>Chaves primárias

Cada tabela em um grupo de sincronização deve ter uma chave primária. Sincronização de Dados SQL não pode sincronizar uma tabela que não tem uma chave primária.

Antes de usar a Sincronização de Dados SQL em produção, teste o desempenho de sincronização inicial e em andamento.

#### <a name="empty-tables-provide-the-best-performance"></a>Tabelas vazias fornecem o melhor desempenho

Tabelas vazias fornecem o melhor desempenho em tempo de inicialização. Se a tabela de destino estiver vazia, a Sincronização de Dados usará inserção em massa para carregar os dados. Caso contrário, a Sincronização de Dados fará uma comparação e inserção linha por linha para verificar conflitos. No entanto, se o desempenho não for uma preocupação, você poderá configurar a sincronização entre tabelas que já contenham dados.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a> Provisionamento de bancos de dados de destino

A Sincronização de Dados SQL fornece provisionamento automático de banco de dados básico.

Esta seção discute as limitações do provisionamento da Sincronização de Dados SQL.

#### <a name="autoprovisioning-limitations"></a>Limitações de provisionamento automático

A Sincronização de Dados SQL tem as seguintes limitações para provisionamento automático:

-   Selecione somente as colunas que são criadas na tabela de destino. As colunas que não fazem parte do grupo de sincronização não são provisionadas nas tabelas de destino.
-   Índices são criados somente para as colunas selecionadas. Se o índice da tabela de origem tem colunas que não fazem parte do grupo de sincronização, esses índices não são provisionados nas tabelas de destino.  
-   Índices em colunas de tipo XML não são provisionados.  
-   Restrições CHECK não são provisionadas.  
-   Os gatilhos existentes nas tabelas de origem não são provisionados.  
-   Exibições e procedimentos armazenados não são criados no banco de dados de destino.
-   EM UPDATE CASCADE e ON DELETE CASCADE ações em restrições de chave estrangeira não são recriadas nas tabelas de destino.
-   Se você tiver colunas decimais ou numéricas com uma precisão maior que 28, Sincronização de Dados SQL poderá encontrar um problema de estouro de conversão durante a sincronização. Recomendamos que você limite a precisão de colunas decimais ou numéricas para 28 ou menos.

#### <a name="recommendations"></a>Recomendações

-   Use o recurso de provisionamento automático da Sincronização de Dados SQL somente quando você estiver experimentando o serviço.  
-   Para a produção, provisione o esquema de banco de dados.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a> Onde localizar o banco de dados hub

#### <a name="enterprise-to-cloud-scenario"></a>Cenário de empresa para nuvem

Para minimizar a latência, mantenha o banco de dados hub próximo da maior concentração de tráfego de banco de dados do grupo de sincronização.

#### <a name="cloud-to-cloud-scenario"></a>Cenário de nuvem para nuvem

-   Quando todos os bancos de dados em um grupo de sincronização estiverem em um datacenter, o hub deverá estar localizado no mesmo datacenter. Essa configuração reduz a latência e o custo da transferência de dados entre datacenters.
-   Quando os bancos de dados em um grupo de sincronização estiverem em vários datacenters, o hub deverá estar localizado no mesmo datacenter que a maioria dos bancos de dados e do tráfego de banco de dados.

#### <a name="mixed-scenarios"></a>Cenários mistos

Aplique as diretrizes anteriores para as configurações complexas de grupo de sincronização, como as que são uma combinação de cenários de empresa para nuvem e nuvem para nuvem.

## <a name="sync"></a>Sincronizar

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a> Evitar a sincronização inicial lenta e dispendiosa

Nesta seção, discutiremos a sincronização inicial de um grupo de sincronização. Saiba como evitar que uma sincronização inicial demore mais e seja mais cara do que o necessário.

#### <a name="how-initial-sync-works"></a>Como funciona a sincronização inicial

Quando você criar um grupo de sincronização, comece com os dados em apenas um banco de dados. Se você tiver dados em vários bancos de dados, a Sincronização de Dados SQL tratará cada linha como um conflito a ser resolvido. Esta resolução de conflitos causa lentidão na sincronização inicial. Se você tiver dados em vários bancos de dados, a sincronização inicial poderá levar entre vários dias e meses, dependendo do tamanho do banco de dados.

Se os bancos de dados estiverem em datacenters diferentes, cada linha deverá percorrer os diferentes datacenters. Isso aumenta o custo de uma sincronização inicial.

#### <a name="recommendation"></a>Recomendação

Se for possível, comece com os dados em apenas um dos bancos de dados do grupo de sincronização.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a> Design para evitar loops de sincronização

Um loop de sincronização ocorre quando há referências circulares dentro de um grupo de sincronização. Nesse cenário, cada alteração em um banco de dados é infinitamente e circularmente replicada por meio de bancos de dados no grupo de sincronização.   

Evite loops de sincronização, pois eles causam degradação de desempenho e podem aumentar significativamente os custos.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a> Alterações com falha de propagação

#### <a name="reasons-that-changes-fail-to-propagate"></a>Razões pelas quais as alterações falham ao se propagar

As alterações podem apresentar falha na propagação por um dos seguintes motivos:

-   Incompatibilidade de esquema e tipos de dados.
-   Inserindo nulo em colunas não anuláveis.
-   Violação de restrições de chave estrangeira.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>O que acontece quando as alterações falham ao ser propagadas?

-   O grupo de sincronização mostra que ele está em um estado de **Aviso**.
-   Os detalhes estão listados no visualizador de log de interface do usuário do portal.
-   Se o problema não for resolvido por 45 dias, o banco de dados se tornará desatualizado.

> [!NOTE]
> Essas alterações nunca se propagarão. A única maneira de recuperar-se neste cenário é recriar o grupo de sincronização.

#### <a name="recommendation"></a>Recomendação

Monitore a integridade do banco de dados e do grupo de sincronização regularmente através da interface de log e do Portal.


## <a name="maintenance"></a>Manutenção

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a> Evitar bancos de dados e grupos de sincronização desatualizados

Um grupo de sincronização ou um banco de dados em um grupo de sincronização pode ficar desatualizado. Quando o status de um grupo de sincronização for **Desatualizado**, ele deixará de funcionar. Quando o status de um banco de dados é **Desatualizado**, os dados podem ser perdidos. É melhor evitar este cenário em vez de tentar recuperá-lo.

#### <a name="avoid-out-of-date-databases"></a>Evitar bancos de dados desatualizados

O status de um banco de dados é definido como **Desatualizado** quando ele ficou offline por 45 dias ou mais. Para evitar o status **Desatualizado** em um banco de dados, assegure que nenhum dos bancos de dados fique offline por 45 dias ou mais.

#### <a name="avoid-out-of-date-sync-groups"></a>Evitar grupos de sincronização desatualizados

O status de um grupo de sincronização é definido como **Desatualizado** quando alguma alteração no grupo de sincronização não consegue se propagar para o restante do grupo de sincronização por 45 dias ou mais. Para evitar um status **Desatualizado** em um grupo de sincronização, verifique regularmente o log do histórico do grupo de sincronização. Verifique se todos os conflitos são resolvidos e as alterações propagadas com êxito pelos bancos de dados do grupo de sincronização.

Um grupo de sincronização pode não conseguir aplicar uma alteração por um dos seguintes motivos:

-   Incompatibilidade de esquema entre tabelas.
-   Incompatibilidade de dados entre tabelas.
-   Inserir uma linha com um valor nulo em uma coluna que não permite valores nulos.
-   Atualizar uma linha com um valor que viola uma restrição de chave estrangeira.

Para impedir que grupos de sincronização fiquem desatualizados:

-   Atualize o esquema para permitir os valores que estão contidos nas linhas com falha.
-   Atualize os valores das chaves estrangeiras para incluir os valores que estão contidos nas linhas com falha.
-   Atualize os valores de dados na linha com falha para que sejam compatíveis com o esquema ou chaves estrangeiras no banco de dados de destino.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a> Evitar problemas de desprovisionamento

Em algumas circunstâncias, cancelar o registro de um banco de dados com um agente cliente pode causar falhas de sincronização.

#### <a name="scenario"></a>Cenário

1. O grupo de sincronização A foi criado usando uma instância do banco de dados SQL e um banco de dados SQL Server, que está associado ao agente local 1.
2. O mesmo banco de dados local está registrado com o agente local 2 (esse agente não está associado a nenhum grupo de sincronização).
3. Cancelar o registro do banco de dados local do agente local 2 remove as tabelas meta e de acompanhamento referentes ao grupo de sincronização A do banco de dados local.
4. As operações do grupo de sincronização A falham com este erro: "A operação atual não pôde ser concluída porque o banco de dados não está provisionado para sincronização ou você não tem permissões para as tabelas de configuração de sincronização".

#### <a name="solution"></a>Solução

Para evitar esse cenário, não registre um banco de dados com mais de um agente.

Para se recuperar desse cenário:

1. Remova o banco de dados de cada grupo de sincronização a que ele pertence.  
2. Adicione o banco de dados de volta a cada grupo de sincronização de que você acabou de removê-lo.  
3. Implante cada grupo de sincronização afetado (esta ação provisiona o banco de dados).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a> Modificando um grupo de sincronização

Não tente remover um banco de dados de um grupo de sincronização e, em seguida, editar o grupo de sincronização sem primeiro implantar uma das alterações.

Em vez disso, primeiro remova um banco de dados de um grupo de sincronização. Em seguida, implante a alteração e aguarde a conclusão do desprovisionamento. Quando terminar de desprovisionar, você poderá editar o grupo de sincronização e implantar as alterações.

Se você tentar remover um banco de dados e, em seguida, editar um grupo de sincronização sem primeiro implantar as alterações, uma ou outra operação falhará. A interface do portal pode se tornar inconsistente. Se isto ocorrer, atualize a página para restaurar o estado correto.

### <a name="avoid-schema-refresh-timeout"></a>Evitar tempo limite de atualização do esquema

Se você tiver um esquema complexo para sincronizar, poderá encontrar um "tempo limite de operação" durante uma atualização de esquema se o banco de dados de metadados de sincronização tiver um SKU inferior (exemplo: básico). 

#### <a name="solution"></a>Solução

Para atenuar esse problema, escale verticalmente seu banco de dados de metadados de sincronização para ter uma SKU superior, como S3. 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a Sincronização de Dados SQL, consulte:

-   Visão geral - [Sincronize dados em vários bancos de dados locais e na nuvem com o Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar Sincronização de Dados SQL
    - No portal- [tutorial: configurar o sincronização de dados SQL para sincronizar dados entre o Azure SQL Database e SQL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um banco de dados no banco de dados SQL e um banco de dados em uma instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-data-sync-agent-overview.md)
-   Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](./monitor-tune-overview.md)
-   Solucionar problemas - [Solucionar problemas com o SQL Data Sync do Azure](sql-data-sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com Transact-SQL - [Automatize a replicação de alterações de esquema no Azure SQL Data Sync](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

Para saber mais sobre Bancos de Dados SQL, confira:

-   [Visão geral do Banco de Dados SQL](sql-database-paas-overview.md)
-   [Gerenciamento do ciclo de vida do banco](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
