---
title: Azure Container Apps 미리 보기의 컨테이너
description: Azure Container Apps에서 컨테이너를 관리하고 구성하는 방법 알아보기
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: a471f0026140f5d2b3a559788422e92447e14cb8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102885"
---
# <a name="containers-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기의 컨테이너

Azure Container Apps는 Kubernetes 및 컨테이너 오케스트레이션의 세부 정보를 관리합니다. Azure Container Apps의 컨테이너는 선택한 런타임, 프로그래밍 언어 또는 개발 스택을 사용할 수 있습니다.

:::image type="content" source="media/containers/azure-container-apps-containers.png" alt-text="Azure Container Apps: 컨테이너":::

Azure Container Apps는 다음을 지원합니다.

- 모든 Linux 기반 컨테이너 이미지
- 공용 또는 프라이빗 컨테이너 레지스트리의 컨테이너

추가 기능은 다음과 같습니다.

- 필수 기본 컨테이너 이미지는 없습니다.
- `template`ARM 구성 섹션을 변경하여 새 [컨테이너 앱 수정 버전](application-lifecycle-management.md)을 트리거합니다.
- 컨테이너가 충돌하면 자동으로 다시 시작됩니다.

## <a name="configuration"></a>구성

다음 예제 구성에서는 컨테이너를 설정하는 경우 사용할 수 있는 옵션을 보여 줍니다.

```json
{
  ...
  "template": {
    "containers": [
      {
        "image": "myacr.azurecr.io/myrepo/api-service:v1",
        "name": "my-container-image",
        "command": ["/bin/queue"],
        "args": [],
        "env": [
          {
            "name": "HTTP_PORT",
            "value": "8080"
          }
        ],
        "resources": {
            "cpu": 1,
            "memory": "250Mb"
        }
    }]
  }
}
```

| 설정 | Description | 설명 |
|---|---|---|
| `image` | 컨테이너 앱의 컨테이너 이미지 이름입니다. | 이 값은 `repository/image-name:tag` 형식입니다. |
| `name` | 컨테이너의 이름입니다. | 보고 및 식별에 사용됩니다. |
| `command` | 컨테이너의 시작 명령입니다. | Docker의 [진입점](https://docs.docker.com/engine/reference/builder/) 필드에 해당합니다.  |
| `args` | 명령 인수를 시작합니다. | 배열의 항목은 함께 조인되어 시작 명령에 전달할 매개 변수 목록을 만듭니다. |
| `env` | 환경 변수를 정의하는 키/값 쌍의 배열입니다. | 필드 대신 를 사용하여 `secretRef` `value` 비밀을 참조합니다. |
| `resources.cpu` | 컨테이너에 할당된 CPU 수입니다. | 값은 다음 규칙을 따라야 합니다. 값은 0보다 크고 2보다 작은 값이어야 하며 10진수의 최대 10진수일 수 있습니다. 예를 들어 `1.1` 는 유효하지만 `1.55` 는 유효하지 않습니다. 기본값은 컨테이너당 0.5 CPU입니다. |
| `resources.memory` | 컨테이너에 할당된 RAM의 양입니다. | 이 값은 최대 `4Gi` 입니다. 유일하게 허용되는 통합은 [gibibytes(](https://simple.wikipedia.org/wiki/Gibibyte) `Gi` )입니다. 값은 다음 규칙을 따라야 합니다. 값은 0보다 크고 보다 작은 값이어야 하며 소수 자릿수가 최대 두 `4Gi` 자리인 임의의 10진수일 수 있습니다. 예를 들어 `1.25Gi` 는 유효하지만 `1.555Gi` 는 유효하지 않습니다. 기본값은 `1Gi` 컨테이너당입니다.  |

컨테이너 앱의 모든 컨테이너에 대해 요청된 CPU 및 메모리의 총 양은 다음 조합 중 하나까지 더해야 합니다.

| vCPU | Gi의 메모리 |
|---|---|
| 0.5 | 1.0 |
| 1.0 | 2.0 |
| 1.5 | 3.0 |
| 2.0 | 4.0 |

- 모든 컨테이너의 모든 CPU 요청은 vC CPU 열의 값 중 하나와 일치해야 합니다.
- 모든 컨테이너의 모든 메모리 요청은 CPU 열의 동일한 행에 있는 메모리 열의 메모리 값과 일치해야 합니다.

## <a name="multiple-containers"></a>여러 컨테이너

단일 컨테이너 앱에서 여러 컨테이너를 정의할 수 있습니다. 컨테이너 그룹을 [Pod라고 합니다.](https://kubernetes.io/docs/concepts/workloads/pods) Pod의 컨테이너는 하드 디스크 및 네트워크 리소스를 공유하고 동일한 애플리케이션 수명 주기 를 [경험합니다.](application-lifecycle-management.md)

구성의 배열에서 두 개 이상의 컨테이너를 정의하여 여러 컨테이너를 함께 `containers` 실행합니다.

Pod에서 컨테이너를 함께 실행하는 이유는 다음과 같습니다.

- 컨테이너를 기본 앱에 대한 사이드카로 사용합니다.
- 공유 디스크 공간 및 가상 네트워크 사용.
- 컨테이너 간에 크기 조정 규칙을 공유합니다.
- 항상 함께 실행해야 하는 여러 컨테이너를 그룹화합니다.
- 동일한 호스트의 컨테이너 간 직접 통신을 사용하도록 설정합니다.

## <a name="container-registries"></a>컨테이너 레지스트리

Container Apps 구성을 통해 자격 증명이 제공되는 프라이빗 레지스트리에 호스트된 이미지를 배포할 수 있습니다.

컨테이너 레지스트리를 사용하려면 먼저 [구성의](azure-resource-manager-api-spec.md) 섹션에 필요한 필드를 `registries` 정의합니다.

```json
{
  ...
  "registries": {
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secretref-name"
  }
}
```

이 설정을 사용하면 배열의 에서 컨테이너 이미지를 참조할 때 저장된 자격 증명을 사용할 수 `image` `containers` 있습니다.

다음 예제에서는 Azure Container Registry 앱을 배포하는 방법을 보여줍니다.

```json
{
  ...
  "configuration": {
      "secrets": [
          {
              "name": "acr-password",
              "value": "my-acr-password"
          }
      ],
      "registries": [
          {
              "server": "myacr.azurecr.io",
              "username": "someuser",
              "passwordSecretRef": "acr-password"
          }
      ]
  }
}
```

## <a name="limitations"></a>제한 사항

Azure Container Apps에는 다음과 같은 제한이 있습니다.

- **권한 있는 컨테이너:** Azure Container Apps는 권한 있는 컨테이너를 실행할 수 없습니다. 프로그램이 루트 액세스가 필요한 프로세스를 실행하려고 하면 컨테이너 내의 애플리케이션에 런타임 오류가 발생합니다.

- **운영 체제:** Linux 기반 컨테이너 이미지가 필요합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [환경](environment.md)
