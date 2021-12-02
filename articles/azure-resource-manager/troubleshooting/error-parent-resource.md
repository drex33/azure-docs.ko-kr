---
title: 부모 리소스 오류
description: Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일에서 부모 리소스를 사용할 때 발생 하는 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 12/01/2021
ms.openlocfilehash: cc1363484e5de4f9fadc05e7ce47c63948e3d8d0
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486165"
---
# <a name="resolve-errors-for-parent-resources"></a>부모 리소스 오류 해결

이 문서에서는 `ParentResourceNotFound` 부모 리소스에 종속 된 리소스를 배포할 때 나타날 수 있는 오류에 대해 설명 합니다.

## <a name="symptom"></a>증상

다른 리소스에 자식 리소스를 배포 하는 경우 다음과 같은 오류가 표시 될 수 있습니다.

```Output
Code=ParentResourceNotFound,
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>원인

한 리소스가 다른 리소스에 대 한 자식인 경우 자식 리소스를 만들기 전에 부모 리소스가 있어야 합니다. 자식 리소스의 이름은 부모 리소스와의 연결을 정의합니다. 자식 리소스의 이름은 `<parent-resource-name>/<child-resource-name>` 형식으로 돼 있습니다. 예를 들어 SQL Database는 다음과 같이 정의될 수 있습니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sqlDatabase 'Microsoft.Sql/servers/databases@2021-05-01-preview' = {
  name: '${sqlServerName}/${databaseName}'
  ...
}
```

부모 및 자식 리소스를 Bicep 하는 방법에 대 한 자세한 내용은 [Bicep에서 자식 리소스의 이름 및 형식 설정](../bicep/child-resource-name-type.md)을 참조 하세요.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('sqlServerName'), '/', parameters('databaseName'))]",
  ...
```

ARM 템플릿 부모 및 자식 리소스에 대 한 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../templates/child-resource-name-type.md)을 참조 하세요.

---

서버와 데이터베이스를 동일한 템플릿에 배포 하지만 서버에 대 한 종속성을 지정 하지 않는 경우 서버를 배포 하기 전에 데이터베이스 배포가 시작 될 수 있습니다. 그러면 오류가 발생 하 여 데이터베이스 배포가 실패 `ParentResourceNotFound` 합니다.

부모 리소스가 이미 있고 동일한 템플릿에 배포 되지 않은 경우 `ParentResourceNotFound` 리소스 관리자 자식 리소스를 부모에 연결할 수 없는 경우 오류가 발생 합니다. 이 오류는 자식 리소스의 형식이 올바르지 않은 경우에 발생할 수 있습니다. 부모 리소스에 대 한 리소스 그룹과 다른 리소스 그룹에 자식 리소스를 배포 하는 경우에는입니다.

## <a name="solution-1---deployed-in-same-template"></a>해결 방법 1-동일한 템플릿에 배포 됨

부모 및 자식 리소스가 동일한 템플릿에 배포 되는 경우이 오류를 해결 하려면 종속성을 사용 합니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

이 예제에서는 부모 리소스 내에서 중첩 된 자식 리소스를 사용 하 고 종속성을 만듭니다. 자식은 부모 리소스에서 리소스 형식 및 API 버전을 가져옵니다.

```bicep
resource sqlServer 'Microsoft.Sql/servers@2021-05-01-preview' = {
  name: sqlServerName
  properties: {
    ...
  }
  resource sqlDatabase 'databases' = {
    name: databaseName
    ...
  }
}
```

종속성에 대 한 자세한 내용은 [Bicep의 리소스 선언](../bicep/resource-declaration.md#dependencies)을 참조 하세요.

# <a name="json"></a>[JSON](#tab/json)

ARM 템플릿에서를 사용 하 여 `dependsOn` 다른 리소스에 대 한 종속성을 만듭니다.

```json
"dependsOn": [
  "[variables('sqlServerName')]"
]
```

에 대 한 자세한 내용은 `dependsOn` [ARM 템플릿에서 리소스를 배포 하는 순서 정의](../templates/resource-dependency.md#dependson)를 참조 하세요.

---

## <a name="solution-2---deployed-in-different-templates"></a>솔루션 2-여러 템플릿에 배포 됨

부모 리소스가 다른 템플릿에 배포 되었을 때이 오류를 해결 하려면 종속성을 설정 하지 마십시오. 대신, 동일한 리소스 그룹에 자식 리소스를 배포하고 부모 리소스의 이름을 제공합니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

이 예제에서는 [기존](../bicep/resource-declaration.md#existing-resources) 키워드를 사용 하 여 별도의 파일에 배포 된 부모를 참조 합니다. 자식 리소스는 `parent` 요소 및 부모 리소스의 기호화 된 이름을 사용 합니다.

```bicep
param location string = resourceGroup().location
param sqlServerName string
param databaseName string

resource sqlServer 'Microsoft.Sql/servers@2021-05-01-preview' existing = {
  name: sqlServerName
}

resource sqlDatabase 'Microsoft.Sql/servers/databases@2021-05-01-preview' = {
  parent: sqlServer
  name: databaseName
  location: location
}
```

# <a name="json"></a>[JSON](#tab/json)

`name`요소는 부모 리소스의 이름과 자식 리소스를 사용 합니다.

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
      "apiVersion": "2021-05-01-preview",
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

---
