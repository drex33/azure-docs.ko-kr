---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 23e75e2cf2e097e08c0b6bf3c4b0b8c9842a9184
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609760"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

`extensionBundle`에서 사용할 수 있는 속성은 다음과 같습니다.

| 속성 | Description |
| -------- | ----------- |
| id | Microsoft Azure Functions 확장 번들에 대한 네임스페이스입니다. |
| 버전 | 설치할 번들의 버전입니다. Functions 런타임은 항상 버전 범위 또는 간격으로 정의된 허용 가능한 최대 버전을 선택합니다. 위의 버전 값은 2.0.0부터 3.0.0까지의 모든 번들 버전을 허용하지만 3.0.0은 포함하지 않습니다. 자세한 내용은 [버전 범위를 지정하기 위한 간격 표기법](/nuget/reference/package-versioning#version-ranges)을 참조하세요. |