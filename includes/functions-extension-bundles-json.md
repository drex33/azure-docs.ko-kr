---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: feabf1cfde0facc49694554094064ee7b54e816b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852342"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[3.3.0, 4.0.0)"
    }
}
```

`extensionBundle`에서 사용할 수 있는 속성은 다음과 같습니다.

| 속성 | Description |
| -------- | ----------- |
| id | Microsoft Azure Functions 확장 번들에 대한 네임스페이스입니다. |
| 버전 | 설치할 번들의 버전입니다. Functions 런타임은 항상 버전 범위 또는 간격으로 정의된 허용 가능한 최대 버전을 선택합니다. 위의 version 값을 사용 하면 모든 번들 버전을 2.0.0에서 3.0.0까지 포함할 수 있습니다. 자세한 내용은 [버전 범위를 지정하기 위한 간격 표기법](/nuget/reference/package-versioning#version-ranges)을 참조하세요. |