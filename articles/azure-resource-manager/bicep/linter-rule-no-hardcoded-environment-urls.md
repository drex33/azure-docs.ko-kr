---
title: Linter 규칙 - 하드 코딩된 환경 URL 없음
description: Linter 규칙 - 하드 코딩된 환경 URL 없음
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: c27af2581354044394edbdfdcc1c67a8e51ce722
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814392"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Linter 규칙 - 하드 코딩된 환경 URL 없음

이 규칙은 클라우드 환경에 따라 달라지는 하드 코딩된 URL을 찾습니다.

## <a name="linter-rule-code"></a>Linter 규칙 코드

[Bicep 구성 파일에서](bicep-config-linter.md) 다음 값을 사용하여 규칙 설정을 사용자 지정합니다.

`no-hardcoded-env-urls`

## <a name="solution"></a>해결 방법

Bicep 파일에서 URL을 하드 코딩하는 대신 [환경 함수를](../templates/template-functions-deployment.md#environment) 사용하여 배포 중에 이러한 URL을 동적으로 얻습니다. 환경 함수는 배포하는 클라우드 환경에 따라 다른 URL을 반환합니다.

URL이 하드 코드되었으므로 다음 예제는 이 테스트에 실패합니다.

```bicep
var managementURL = 'https://management.azure.com'
```

concat 또는 uri와 함께 사용하는 경우에도 테스트에 실패합니다.

```bicep
var galleryURL1 = concat('https://','gallery.azure.com')
var galleryURL2 = uri('gallery.azure.com','test')
```

하드 코드된 URL을 함수로 대체하여 수정할 수 `environment()` 있습니다.

```bicep
var galleryURL = environment().gallery
```

경우에 따라 배포한 리소스에서 속성을 얻어 수정할 수 있습니다. 예를 들어 스토리지 계정에 대한 엔드포인트를 구성하는 대신 를 사용하여 `.properties.primaryEndpoints` 검색합니다.

```bicep
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: storageAccountName
  location: 'westus'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = sa.properties.primaryEndpoints.web
```

## <a name="configuration"></a>구성

기본적으로 이 규칙은 허용되지 않는 URL을 결정하기 위해 다음 설정을 사용합니다. 

```json
"analyzers": {
  "core": {
    "verbose": false,
    "enabled": true,
    "rules": {
      "no-hardcoded-env-urls": {
        "level": "warning",
        "disallowedhosts": [
          "gallery.azure.com",
          "management.core.windows.net",
          "management.azure.com",
          "database.windows.net",
          "core.windows.net",
          "login.microsoftonline.com",
          "graph.windows.net",
          "trafficmanager.net",
          "datalake.azure.net",
          "azuredatalakestore.net",
          "azuredatalakeanalytics.net",
          "vault.azure.net",
          "api.loganalytics.io",
          "asazure.windows.net",
          "region.asazure.windows.net",
          "batch.core.windows.net"
        ],
        "excludedhosts": [
          "schema.management.azure.com"
        ]
      }
    }
  }
}
```

bicepconfig.json 파일을 추가하고 새 설정을 적용하여 사용자 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

linter에 대한 자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)
