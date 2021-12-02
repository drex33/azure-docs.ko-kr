---
title: Azure Container Apps에서 크기 조정
description: Azure Container Apps에서 애플리케이션을 스케일 인 및 스케일 아웃하는 방법을 알아봅니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9e9f0c4b686b527984811e29069d892ef2dcbcb3
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133435074"
---
# <a name="set-scaling-rules-in-azure-container-apps"></a>Azure Container Apps에서 크기 조정 규칙 설정

Azure Container Apps는 선언적 크기 조정 규칙 집합을 통해 자동 수평 크기 조정을 관리합니다. 컨테이너 앱이 확장되면 컨테이너 앱의 새 인스턴스가 주문형으로 만들어집니다. 이러한 인스턴스를 복제본이라고 합니다.

크기 조정 규칙은 `resources.properties.template.scale` [구성의](overview.md)섹션에서 정의됩니다. 컨테이너 앱의 모든 규칙에 적용되는 두 가지 크기 조정 속성이 있습니다.

| 크기 조정 속성 | 설명 | 기본값 | 최솟값 | 최댓값 |
|---|---|---|---|---|
| `minReplicas` | 컨테이너 앱에 대해 실행 중인 최소 복제본 수입니다. | 0 | 1 | 25 |
| `maxReplicas` | 컨테이너 앱에 대해 실행 중인 최대 복제본 수입니다. | 해당 없음 | 1 | 25 |

- 컨테이너 앱이 0으로 확장되면 요금이 청구되지 않습니다.
- 개별 크기 조정 규칙은 `rules` 배열에 정의됩니다.
- 애플리케이션의 인스턴스가 항상 실행되도록 하려면 를 `minReplicas` 1 이상으로 설정합니다.
- 복제본이 처리되지 않지만 메모리에 남아 있는 복제본은 "유휴 요금" 범주로 청구됩니다.
- 크기 조정 규칙의 변경 내용은 [수정 범위](overview.md) 변경입니다.
- HTTP가 아닌 이벤트 크기 조정 규칙을 사용하는 경우 를 로 설정하는 `activeRevisionMode` `single` 것이 좋습니다.

> [!IMPORTANT]
> 복제본 수량은 보장이 아닌 대상 크기입니다. 를 로 설정하더라도 `maxReplicas` `1` 스레드 보안을 보증하지 않습니다.

## <a name="scale-triggers"></a>트리거 크기 조정

Container Apps는 많은 수의 크기 조정 트리거를 지원합니다. 지원되는 크기 조정 트리거에 대한 자세한 내용은 [KEDA Scalers를 참조하세요.](https://keda.sh/docs/scalers/)

KEDA 설명서에서는 YAML의 코드 예제를 보여 주지만 Container Apps ARM 템플릿은 JSON에 있습니다. 필요에 따라 KEDA에서 예제를 변환할 때는 속성 이름을 [kebab](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Delimiter-separated_words) 대/소문자에서 [카멜식](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Letter_case-separated_words) 대/소문자로 전환해야 합니다.

## <a name="http"></a>HTTP

HTTP 크기 조정 규칙을 사용하면 규모 확장 시기를 결정하는 임계값을 제어할 수 있습니다.

| 크기 조정 속성 | 설명 | 기본값 | 최솟값 | 최댓값 |
|---|---|---|---|---|
| `concurrentRequests`| 요청 수가 이 값을 초과하면 최대 크기까지 더 많은 복제본이 `maxReplicas` 추가됩니다. | 50 | 1 | 해당 없음 |

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": 0,
          "maxReplicas": 5,
          "rules": [{
            "name": "http-rule",
            "http": {
              "metadata": {
                  "concurrentRequests": "100"
              }
            }
          }]
        }
      }
    }
  }
}
```

이 예제에서 컨테이너 앱은 최대 5개의 복제본으로 확장되며 0개의 인스턴스로 축소할 수 있습니다. 크기 조정 임계값은 초당 100개의 동시 요청으로 설정됩니다.

## <a name="event-driven"></a>이벤트 기반

Container Apps는 다양한 이벤트 유형에 따라 확장할 수 있습니다. [KEDA에서](https://keda.sh/docs/scalers/)지원하는 모든 이벤트는 Container Apps에서 지원됩니다.

각 이벤트 유형은 KEDA 정의의 섹션에서 서로 다른 속성을 `metadata` 제공합니다. 이러한 속성을 사용하여 Container Apps에서 크기 조정 규칙을 정의합니다.

다음 예제에서는 [Azure Service Bus](https://keda.sh/docs/scalers/azure-service-bus/) 트리거를 기반으로 크기 조정 규칙을 만드는 방법을 보여줍니다.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      "configuration": {
        "secrets": [{
          "name": "servicebusconnectionstring",
          "value": "<MY-CONNECTION-STRING-VALUE>"
        }],
      },
      "template": {
        ...
        "scale": {
          "minReplicas": "0",
          "maxReplicas": "10",
          "rules": [
          {
            "name": "queue-based-autoscaling",
            "custom": {
              "type": "azure-servicebus",
              "metadata": {
                "queueName": "myServiceBusQueue",
                "messageCount": "20"
              },
              "auth": [{
                "secretRef": "servicebusconnectionstring",
                "triggerParameter": "connection"
              }]
        }
    }]
}
```

이 예제에서 컨테이너 앱은 다음 동작에 따라 크기가 조정됩니다.

- 큐의 메시지 수가 20을 초과하면 새 복제본이 만들어집니다.
- 큐에 대한 연결 문자열은 구성 파일에 대한 매개 변수로 제공되며 속성을 통해 `secretRef` 참조됩니다.

## <a name="cpu"></a>CPU

CPU 크기 조정을 사용하면 사용 중인 CPU 양에 따라 앱 규모를 확장하거나 축소할 수 있습니다. CPU 크기 조정은 컨테이너 앱의 크기를 0으로 조정하는 것을 허용하지 않습니다. 이 트리거에 대한 자세한 내용은 [KEDA CPU 크기 조정 트리거를 참조하세요.](https://keda.sh/docs/scalers/cpu/)

다음 예제에서는 CPU 크기 조정 규칙을 만드는 방법을 보여줍니다.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "1",
          "maxReplicas": "10",
          "rules": [{
            "name": "cpuScalingRule",
            "custom": {
              "type": "cpu",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- 이 예제에서는 CPU 사용량이 50%를 초과하면 컨테이너 앱의 크기를 조정합니다.
- 최소한 CPU 사용률에 따라 크기가 조정되는 앱의 경우 단일 복제본이 메모리에 남아 있습니다.

## <a name="memory"></a>메모리

메모리 크기 조정을 사용하면 사용 중인 메모리 양에 따라 앱의 규모를 확장하거나 축소할 수 있습니다. 메모리 크기 조정은 컨테이너 앱의 크기를 0으로 조정하는 것을 허용하지 않습니다. 이 배율 조정기 에 대한 자세한 내용은 [KEDA 메모리 배율 조정기 를 참조하세요.](https://keda.sh/docs/scalers/memory/)

다음 예제에서는 메모리 크기 조정 규칙을 만드는 방법을 보여줍니다.

```json
{
  ...
  "resources": {
    ...
    "properties": {
      ...
      "template": {
        ...
        "scale": {
          "minReplicas": "1",
          "maxReplicas": "10",
          "rules": [{
            "name": "memoryScalingRule",
            "custom": {
              "type": "memory",
              "metadata": {
                "type": "Utilization",
                "value": "50"
              }
            }
          }]
        }
      }
    }
  }
}
```

- 이 예제에서는 메모리 사용량이 50%를 초과하면 컨테이너 앱의 크기가 조정됩니다.
- 최소한 메모리 사용률에 따라 크기가 조정되는 앱의 경우 단일 복제본이 메모리에 남아 있습니다.


## <a name="considerations"></a>고려 사항

- 수직적 크기 조정은 지원되지 않습니다.
- 복제본 수량은 보장이 아닌 대상 크기입니다.
  - 를 로 설정하더라도 `maxReplicas` `1` 스레드 보안을 보증하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [컨테이너 앱 보안](secure-app.md)
