---
title: Lecrule-하드 코드 되지 않은 환경 URL 없음
description: Lecrule-하드 코드 되지 않은 환경 URL 없음
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: efbd0e1cdd7a0639fd9dc592ea5bae21254f0326
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006283"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Lecrule-하드 코드 되지 않은 환경 URL 없음

이 규칙은 클라우드 환경에 따라 달라 지는 하드 코드 된 Url을 찾습니다.

## <a name="returned-code"></a>반환 된 코드

`no-hardcoded-env-urls`

## <a name="solution"></a>해결 방법

Bicep 파일에서 Url을 하드 코딩 하는 대신 [환경 함수](../templates/template-functions-deployment.md#environment) 를 사용 하 여 배포 중에 이러한 url을 동적으로 가져옵니다. 환경 함수는 배포 하는 클라우드 환경에 따라 다른 Url을 반환 합니다.

URL이 하드 코드되었으므로 다음 예제는 이 테스트에 실패합니다.

```bicep
var managementURL = 'https://management.azure.com'
```

concat 또는 uri와 함께 사용하는 경우에도 테스트에 실패합니다.

```bicep
var galleryURL1 = concat('https://','gallery.azure.com')
var galleryURL2 = uri('gallery.azure.com','test')
```

하드 코드 된 URL을 함수로 바꿔 수정할 수 있습니다 `environment()` .

```bicep
var galleryURL = environment().gallery
```

배포 된 리소스에서 속성을 가져와서 해결할 수 있는 경우도 있습니다. 예를 들어 저장소 계정에 대 한 끝점을 생성 하는 대신를 사용 하 여 검색 `.properties.primaryEndpoints` 합니다.

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

기본적으로이 규칙은 다음 설정을 사용 하 여 허용 되지 않는 Url을 결정 합니다. 

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

Bicepconfig 파일을 추가 하 고 새 설정을 적용 하 여 사용자 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
* 현재 lecrules는 다음과 같습니다.

  * [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
  * [no-unused-params](./linter-rule-no-unused-parameters.md)
  * [no-unused-vars](./linter-rule-no-unused-variables.md)
  * [prefer-interpolation](./linter-rule-prefer-interpolation.md)
  * [secure-parameter-default](./linter-rule-secure-parameter-default.md)
  * [simplify-interpolation](./linter-rule-simplify-interpolation.md)
