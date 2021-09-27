---
title: Linter 규칙 - 하드 코딩된 환경 URL 없음
description: Linter 규칙 - 하드 코딩된 환경 URL 없음
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: d35736860dd672ffc00ea1d4cde52d8f4d4ef8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634133"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Linter 규칙 - 하드 코딩된 환경 URL 없음

Linter를 사용하면 개발 중에 지침을 제공하여 코딩 표준을 보다 쉽게 적용할 수 있습니다. 현재 linter 규칙 집합은 최소이며 [arm-ttk 테스트 사례에서 가져온 것입니다.](../templates/template-test-cases.md)

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)

## <a name="code"></a>코드

`no-hardcoded-env-urls`

## <a name="description"></a>Description

템플릿에서 환경 URL을 하드 코딩하지 않습니다. 대신 [환경 함수](../templates/template-functions-deployment.md#environment)를 사용하여 배포 중에 이러한 URL을 동적으로 가져옵니다. 차단된 URL 호스트 목록은 `DisallowedHosts` [bicepconfig.json](https://github.com/Azure/bicep/blob/main/src/Bicep.Core/Configuration/bicepconfig.json)의 기본 목록을 참조하세요.

## <a name="examples"></a>예제

URL이 하드 코드되었으므로 다음 예제는 이 테스트에 실패합니다.

```bicep
var AzureURL = 'https://management.azure.com'
```

concat 또는 uri와 함께 사용하는 경우에도 테스트에 실패합니다.

```bicep
var AzureSchemaURL1 = concat('https://','gallery.azure.com')
var AzureSchemaURL2 = uri('gallery.azure.com','test')
```

다음 예에서는 이 테스트를 통과합니다.

```bicep
var AzureSchemaURL = environment().gallery
```

## <a name="configuration"></a>Configuration

허용하지 않을 URL 호스트 집합은 다음과 같이 bicepconfig.json 파일의 disallowedHosts 속성을 사용하여 사용자 지정할 수 있습니다.

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedHosts": [
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
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
