---
title: 부모 리소스 오류
description: Azure Resource Manager 템플릿에서 부모 리소스를 사용할 때 발생하는 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: bc1409a85eaaf8c7fb86880dc6db7d692a6101d7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053641"
---
# <a name="resolve-errors-for-parent-resources"></a>부모 리소스 오류 해결

이 문서에서는 부모 리소스에 종속된 리소스를 배포할 때 발생할 수 있는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

다른 리소스의 자식 리소스를 배포할 때 다음 오류가 표시될 수 있습니다.

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>원인

한 리소스가 다른 리소스의 자식인 경우 자식 리소스를 만들기 전에 부모 리소스가 있어야 합니다. 자식 리소스의 이름은 부모 리소스와의 연결을 정의합니다. 자식 리소스의 이름은 `<parent-resource-name>/<child-resource-name>` 형식으로 돼 있습니다. 예를 들어 SQL Database는 다음과 같이 정의될 수 있습니다.

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

동일한 템플릿으로 서버 및 데이터베이스를 배포하지만 서버에 대한 종속성을 지정하지 않는 경우 서버가 배포되기 전에 데이터베이스 배포가 먼저 시작될 수 있습니다.

부모 리소스가 이미 존재하고 동일한 템플릿으로 배포되지 않는 경우 Resource Manager가 부모와 자식 리소스를 연결할 수 없을 때 오류가 발생합니다. 자식 리소스가 올바른 형식이 아니거나 자식 리소스가 부모 리소스용 리소스 그룹과 다른 리소스 그룹에 배포되는 경우 오류가 발생할 수 있습니다.

## <a name="solution"></a>솔루션

부모 및 자식 리소스가 동일한 템플릿으로 배포될 경우 이 오류를 해결하려면 종속성을 포함합니다.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

이전에 부모 리소스를 다른 템플릿으로 배포한 경우 이 오류를 해결하려면 종속성을 설정하지 않습니다. 대신, 동일한 리소스 그룹에 자식 리소스를 배포하고 부모 리소스의 이름을 제공합니다.

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

자세한 내용은 [Azure Resource Manager 템플릿에서 리소스를 배포하는 순서 정의](../templates/resource-dependency.md)를 참조하세요.
