---
title: Erros de recurso pai
description: Descreve como resolver erros ao trabalhar com um recurso pai em um modelo de Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 474cb85d16382136e24e5502b87ba8a1a65488ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84673653"
---
# <a name="resolve-errors-for-parent-resources"></a>Resolva erros de recursos pai

Este artigo descreve os erros com que você pode se obter durante a implantação de um recurso que é dependente de um recurso pai.

## <a name="symptom"></a>Sintoma

Ao implantar um recurso que é filho de outro recurso, você receberá o seguinte erro:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Quando um recurso é filho de outro recurso, o recurso pai deve existir antes da criação do recurso filho. O nome do recurso filho define a conexão com o recurso pai. O formato do nome do recurso filho está no formato `<parent-resource-name>/<child-resource-name>`. Por exemplo, um Banco de Dados SQL pode ser definido como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Se você implantar o servidor e o banco de dados no mesmo modelo, mas não especifica uma dependência no servidor, a implantação de banco de dados talvez seja iniciado antes que o servidor tenha sido implantado.

Se o recurso pai já existe e não será implantado no mesmo modelo, você receber esse erro quando o Gerenciador de Recursos não é possível associar o recurso filho com o pai. Esse erro pode acontecer quando o recurso filho não está no formato correto ou o recurso filho é implantado em um grupo de recursos é diferente do grupo de recursos para o recurso pai.

## <a name="solution"></a>Solução

Para resolver esse erro quando recursos pai e filho são implantados no mesmo modelo, inclua uma dependência.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Para resolver esse erro quando o recurso pai foi anteriormente implantado em um modelo diferente, você não define uma dependência. Em vez disso, implante o filho no mesmo grupo de recursos e forneça o nome do recurso pai.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Para obter mais informações, consulte [definir a ordem de implantação de recursos em modelos de Azure Resource Manager](define-resource-dependency.md).