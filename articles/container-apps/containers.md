---
title: Azure 컨테이너 앱 미리 보기의 컨테이너
description: Azure Container Apps에서 컨테이너를 관리 및 구성 하는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9fc40b46ce2fbe93d64013ea4e3e7b7e36ef7bf3
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576304"
---
# <a name="containers-in-azure-container-apps-preview"></a>Azure 컨테이너 앱 미리 보기의 컨테이너

Azure 컨테이너 앱은 Kubernetes 및 컨테이너 오케스트레이션의 세부 정보를 관리 합니다. Azure Container Apps의 컨테이너는 원하는 런타임, 프로그래밍 언어 또는 개발 스택을 사용할 수 있습니다.

:::image type="content" source="media/containers/azure-container-apps-containers.png" alt-text="Azure 컨테이너 앱: 컨테이너":::

Azure 컨테이너 앱은 다음을 지원 합니다.

- 모든 Linux 기반 컨테이너 이미지
- 모든 공용 또는 개인 컨테이너 레지스트리의 컨테이너

추가 기능은 다음과 같습니다.

- 기본 컨테이너 이미지가 필요 하지 않습니다.
- `template`ARM 구성 섹션을 변경 하면 새 [컨테이너 앱 수정 버전이](application-lifecycle-management.md)트리거됩니다.
- 컨테이너가 충돌 하면 자동으로 다시 시작 됩니다.

## <a name="configuration"></a>구성

다음 예제 구성에서는 컨테이너를 설정할 때 사용할 수 있는 옵션을 보여 줍니다.

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
| `image` | 컨테이너 앱에 대 한 컨테이너 이미지 이름입니다. | 이 값은 형식 `repository/image-name:tag` 입니다. |
| `name` | 컨테이너의 이름입니다. | 보고 및 식별에 사용 됩니다. |
| `command` | 컨테이너의 시작 명령입니다. | Docker의 [entrypoint](https://docs.docker.com/engine/reference/builder/) 필드와 동일 합니다.  |
| `args` | 명령 인수를 시작 합니다. | 배열의 항목은 함께 조인 되어 시작 명령에 전달할 매개 변수 목록을 만듭니다. |
| `env` | 환경 변수를 정의 하는 키/값 쌍의 배열입니다. | `secretRef`필드 대신를 사용 `value` 하 여 암호를 참조 합니다. |
| `resources.cpu` | 컨테이너에 할당 된 Cpu 수입니다. | 값은 다음 규칙을 따라야 합니다. 값은 0 보다 크고 2 보다 작아야 하 고 최대 소수점이 하를 사용 하는 10 진수 여야 합니다. 예를 들어 `1.1` 는 유효 하지만 `1.55` 는 유효 하지 않습니다. 기본값은 컨테이너 당 0.5 CPU입니다. |
| `resources.memory` | 컨테이너에 할당 된 RAM의 양입니다. | 이 값은 최대 `4Gi` 입니다. 유일 하 게 허용 되는는는 [gid 바이트](https://simple.wikipedia.org/wiki/Gibibyte) ( `Gi` )입니다. 값은 다음 규칙을 따라야 합니다. 값은 0 보다 크고 보다 작아야 하 고 소수 `4Gi` 자릿수는 최대 두 자리인 10 진수 여야 합니다. 예를 들어 `1.25Gi` 는 유효 하지만 `1.555Gi` 는 유효 하지 않습니다. 기본값은 `1Gi` 컨테이너 당입니다.  |

컨테이너 앱의 모든 컨테이너에 대해 요청 된 Cpu 및 메모리의 총 크기는 다음 조합 중 하나를 추가 해야 합니다.

| vCPU | Gi의 메모리 |
|---|---|
| 0.5 | 1.0 |
| 1.0 | 2.0 |
| 1.5 | 3.0 |
| 2.0 | 4.0 |

- 모든 컨테이너의 모든 CPU 요청은 vCPUs 열에 있는 값 중 하 나와 일치 해야 합니다.
- 모든 컨테이너의 모든 메모리 요청은 CPU 열의 동일한 행에 있는 memory 열의 메모리 값과 일치 해야 합니다.

## <a name="multiple-containers"></a>여러 컨테이너

단일 컨테이너 앱에서 여러 컨테이너를 정의할 수 있습니다. 컨테이너 그룹을 [pod](https://kubernetes.io/docs/concepts/workloads/pods)이라고 합니다. Pod의 컨테이너는 하드 디스크 및 네트워크 리소스를 공유 하 고 동일한 [응용 프로그램 수명 주기](application-lifecycle-management.md)를 경험 합니다.

구성의 배열에 둘 이상의 컨테이너를 정의 하 여 여러 컨테이너를 함께 실행 `containers` 합니다.

Pod에서 컨테이너를 함께 실행 하는 이유는 다음과 같습니다.

- 컨테이너를 기본 앱에 대 한 사이드카 사용 합니다.
- 공유 디스크 공간 및 가상 네트워크 사용
- 컨테이너 간에 크기 조정 규칙을 공유 합니다.
- 항상 함께 실행 해야 하는 여러 컨테이너를 그룹화 합니다.
- 동일한 호스트의 컨테이너 간 직접 통신을 사용 하도록 설정 합니다.

## <a name="container-registries"></a>컨테이너 레지스트리

컨테이너 앱 구성을 통해 자격 증명이 제공 되는 개인 레지스트리에서 호스트 되는 이미지를 배포할 수 있습니다.

컨테이너 레지스트리를 사용 하려면 먼저 구성 섹션에 필요한 필드를 정의 합니다 [](azure-resource-manager-api-spec.md) `registries` .

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

이 설정을 사용 하면 배열에서의 컨테이너 이미지를 참조할 때 저장 된 자격 증명을 사용할 수 있습니다 `image` `containers` .

다음 예제에서는 Azure Container Registry에서 앱을 배포 하는 방법을 보여 줍니다.

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

Azure 컨테이너 앱에는 다음과 같은 제한 사항이 있습니다.

- **권한 있는 컨테이너**: Azure 컨테이너 앱은 권한 있는 컨테이너를 실행할 수 없습니다. 프로그램에서 루트 액세스가 필요한 프로세스를 실행 하려고 하면 컨테이너 내의 응용 프로그램에서 런타임 오류가 발생 합니다.

- **운영** 체제: Linux 기반 컨테이너 이미지가 필요 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [환경](environment.md)
