---
title: Azure 컨테이너 앱에서 크기 조정
description: 응용 프로그램이 Azure Container Apps에서 확장 및 축소 되는 방법에 대해 알아봅니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 339d4635dcb40d062b11e7c1e02cb62d2e314a8b
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028157"
---
# <a name="set-scaling-rules-in-azure-container-apps"></a>Azure Container Apps에서 크기 조정 규칙 설정

Azure Container Apps는 선언적 크기 조정 규칙 집합을 통해 자동 가로 크기 조정을 관리 합니다. 컨테이너 앱이 확장 되 면 컨테이너 앱의 새 인스턴스가 요청 시 만들어집니다. 이러한 인스턴스를 복제본 이라고 합니다.

크기 조정 규칙은 `resources.properties.template.scale` [구성](overview.md)의 섹션에서 정의 됩니다. 컨테이너 앱의 모든 규칙에 적용 되는 두 가지 크기 조정 속성이 있습니다.

| Scale 속성 | Description | 기본값 | 최솟값 | 최댓값 |
|---|---|---|---|---|
| `minReplicas` | 컨테이너 앱에 대해 실행 중인 최소 복제본 수입니다. | 0 | 1 | 25 |
| `maxReplicas` | 컨테이너 앱에 대해 실행 중인 최대 복제본 수입니다. | 해당 없음 | 1 | 25 |

- 컨테이너 앱이 0으로 확장 되 면 요금이 청구 되지 않습니다.
- 개별 크기 조정 규칙은 배열에 정의 됩니다 `rules` .
- 응용 프로그램의 인스턴스가 항상 실행 되 고 있는지 확인 하려면를 `minReplicas` 1 이상으로 설정 합니다.
- 복제본이 처리 되지 않지만 메모리에 남아 있는 복제본은 "유휴 요금" 범주에서 청구 됩니다.
- 크기 조정 규칙에 대 한 변경 내용은 [수정 범위](overview.md) 변경입니다.

> [!IMPORTANT]
> 복제본 수량은 보장 되는 것이 아니라 목표 금액입니다. 을로 설정한 경우 `maxReplicas` 에도 `1` 스레드 보안을 보장할 수 없습니다.

## <a name="scale-triggers"></a>트리거 크기 조정

컨테이너 앱은 많은 수의 배율 트리거를 지원 합니다. 지원 되는 크기 조정 트리거에 대 한 자세한 내용은 [Keda 조정기](https://keda.sh/docs/scalers/)를 참조 하세요.

KEDA 설명서는 YAML의 코드 예제를 보여 주지만 컨테이너 앱 ARM 템플릿은 JSON에 있습니다. 사용자 요구에 맞게 KEDA의 예제를 변환 하는 경우 [kebab](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Delimiter-separated_words) 사례에서 [카멜식](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Letter_case-separated_words) 대/소문자 구분으로 속성 이름을 전환 해야 합니다.

## <a name="http"></a>HTTP

HTTP 크기 조정 규칙을 사용 하 여 규모 확장 시기를 결정 하는 임계값을 제어할 수 있습니다.

| Scale 속성 | Description | 기본값 | 최솟값 | 최댓값 |
|---|---|---|---|---|
| `concurrentRequests`| 요청 수가이 값을 초과 하면 더 많은 복제본이 최대 크기까지 추가 됩니다 `maxReplicas` . | 50 | 1 | 해당 없음 |

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
                  "concurrentRequests": 100
              }
            }
          }]
        }
      }
    }
  }
}
```

이 예에서 컨테이너 앱은 최대 5 개의 복제본을 확장 하 고 0 개 인스턴스로 확장할 수 있습니다. 크기 조정 임계값은 초당 100 동시 요청 수로 설정 됩니다.

## <a name="event-driven"></a>이벤트 기반

컨테이너 앱은 다양 한 이벤트 유형을 기반으로 크기를 조정할 수 있습니다. [Keda](https://keda.sh/docs/scalers/)에서 지 원하는 모든 이벤트는 컨테이너 앱에서 지원 됩니다.

각 이벤트 유형은 `metadata` KEDA 정의의 섹션에 서로 다른 속성을 제공 합니다. 이러한 속성을 사용 하 여 컨테이너 앱에서 크기 조정 규칙을 정의 합니다.

다음 예에서는 [Azure Service Bus](https://keda.sh/docs/scalers/azure-service-bus/) 트리거를 기반으로 크기 조정 규칙을 만드는 방법을 보여 줍니다.

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

이 예제에서 컨테이너 앱은 다음 동작에 따라 크기를 조정 합니다.

- 큐의 메시지 수가 20 개를 초과 하면 새 복제본이 생성 됩니다.
- 큐에 대 한 연결 문자열은 구성 파일에 대 한 매개 변수로 제공 되며 속성을 통해 참조 됩니다 `secretRef` .

## <a name="cpu"></a>CPU

CPU 크기 조정을 사용 하면 사용 중인 CPU의 양에 따라 앱을 확장 하거나 축소할 수 있습니다. CPU 크기 조정에서는 컨테이너 앱을 0으로 확장할 수 없습니다. 이 트리거에 대 한 자세한 내용은 [Keda CPU 배율 트리거](https://keda.sh/docs/scalers/cpu/)를 참조 하세요.

다음 예에서는 CPU 크기 조정 규칙을 만드는 방법을 보여 줍니다.

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
          "minReplicas": "0",
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

- 이 예에서 컨테이너 앱은 CPU 사용량이 50%를 초과할 때 확장 됩니다.
- 최소한 단일 복제본은 CPU 사용률을 기준으로 크기가 조정 되는 앱에 대해 메모리에 남아 있습니다.

## <a name="memory"></a>메모리

메모리 크기 조정을 사용 하면 사용 중인 메모리 양에 따라 앱을 확장 하거나 축소할 수 있습니다. 메모리 크기 조정에서는 컨테이너 앱을 0으로 확장할 수 없습니다. 이 scaler에 대 한 자세한 내용은 [Keda Memory scaler](https://keda.sh/docs/scalers/memory/)를 참조 하세요.

다음 예에서는 메모리 크기 조정 규칙을 만드는 방법을 보여 줍니다.

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
          "minReplicas": "0",
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

- 이 예에서 컨테이너 앱은 메모리 사용량이 50%를 초과할 때 크기를 조정 합니다.
- 최소한 단일 복제본은 메모리 사용률을 기준으로 크기가 조정 되는 앱에 대해 메모리에 남아 있습니다.


## <a name="considerations"></a>고려 사항

- 수직 크기 조정은 지원 되지 않습니다.
- 복제본 수량은 보장 되는 것이 아니라 목표 금액입니다.
  - 을로 설정한 경우 `maxReplicas` 에도 `1` 스레드 보안을 보장할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [컨테이너 앱 보호](secure-app.md)
