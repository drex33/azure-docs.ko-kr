---
title: Event Grid 원본으로서의 Azure Kubernetes Service(미리 보기)
description: 이 문서에서는 Event Grid 이벤트 원본으로 Azure Kubernetes Service를 사용하는 방법을 설명합니다. 스키마와 자습서 및 방법 문서 링크를 제공합니다.
author: zr-msft
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: zarhoads
ms.openlocfilehash: 84eb67d9df14efefe753fe40da938e0a7f4f2c6c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423918"
---
# <a name="azure-kubernetes-service-aks-as-an-event-grid-source-preview"></a>Event Grid 원본으로서의 AKS(Azure Kubernetes Service)(미리 보기)

이 문서에서는 AKS 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 이벤트 원본으로 AKS를 사용하기 위한 빠른 시작 및 자습서의 목록을 제공합니다.

[!INCLUDE [preview features callout](../aks/includes/preview/preview-callout.md)]

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

AKS는 다음과 같은 이벤트 유형을 내보냅니다.

|    이벤트 유형                                             |    Description                                                       |
|-----------------------------------------------------------|----------------------------------------------------------------------|
| Microsoft.ContainerService.NewKubernetesVersionAvailable  | 사용 가능한 Kubernetes 버전 목록이 업데이트되면 트리거됩니다. |

## <a name="properties-common-to-all-events"></a>모든 이벤트에 공통된 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)
이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.
이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다. 각 이벤트에는 다음과 같은 최상위 데이터가 있습니다.

|     속성          |     형식     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    문자열    |    이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다.                                      |
|    `subject`            |    문자열    |    게시자가 정의한 이벤트 주체에 대한 경로입니다.                                                                                              |
|    `eventType`          |    문자열    |    이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다.                                                                                  |
|    `eventTime`          |    문자열    |    공급자의 UTC 시간을 기준으로 이벤트가 생성된 시간입니다.                                                                         |
|    `id`                 |    문자열    |    이벤트에 대한 고유 식별자입니다.                                                                                                            |
|    `data`               |    object    |    Blob Storage 이벤트 데이터입니다.                                                                                                                    |
|    `dataVersion`        |    문자열    |    데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다.                                                          |
|    `metadataVersion`    |    문자열    |    이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다.    |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트로 보냅니다.
이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시되는지 예가 포함되어 있습니다. 각 이벤트에는 다음과 같은 최상위 데이터가 있습니다.

|     속성          |     형식     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    문자열    |    이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다.                                      |
|    `subject`            |    문자열    |    게시자가 정의한 이벤트 주체에 대한 경로입니다.                                                                                              |
|    `type`          |    문자열    |    이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다.                                                                                  |
|    `time`          |    문자열    |    공급자의 UTC 시간을 기준으로 이벤트가 생성된 시간입니다.                                                                         |
|    `id`                 |    문자열    |    이벤트에 대한 고유 식별자입니다.                                                                                                            |
|    `data`               |    object    |    Blob Storage 이벤트 데이터입니다.                                                                                                                    |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

## <a name="example-events"></a>예제 이벤트

### <a name="newkubernetesversionavailable"></a>NewKubernetesVersionAvailable

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

```json
{
    "topic": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "eventType": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "dataVersion": "1",
    "metadataVersion": "1",
    "eventTime": "2021-07-01T04:52:57.0000000Z"
}
```
# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

```json

{
    "source": "/subscriptions/<id>/resourceGroups<rg>/providers/Microsoft.ContainerService/managedClusters/<cluster>",
    "subject": "<cluster>",
    "type": "Microsoft.ContainerService.NewKubernetesVersionAvailable",
    "id": "1234567890abcdef1234567890abcdef12345678",
    "data": {
      "latestSupportedKubernetesVersion": "1.20.7",
      "latestStableKubernetesVersion": "1.19.11",
      "lowestMinorKubernetesVersion": "1.18.19",
      "latestPreviewKubernetesVersion": "1.21.1"
    },
    "specversion": "1.0",
    "time": "2021-07-01T04:52:57.0000000Z"
}
```

---

데이터 개체에는 다음 속성이 포함되어 있습니다.

|    속성                        | 형식   | Description                                                  |
|------------------------------------|--------|--------------------------------------------------------------|
| `latestSupportedKubernetesVersion` | 문자열 | 최신 지원 버전의 Kubernetes를 사용할 수 있습니다.        |
| `latestStableKubernetesVersion`    | 문자열 | 최신 안정적인 지원 버전의 Kubernetes를 사용할 수 있습니다. |
| `lowestMinorKubernetesVersion`     | 문자열 | 사용 가능한 Kubernetes의 가장 낮은 지원 버전입니다.        |
| `latestPreviewKubernetesVersion`   | 문자열 | Kubernetes의 최신 미리 보기 버전을 사용할 수 있습니다.          |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.