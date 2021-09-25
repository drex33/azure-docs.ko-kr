---
title: Event Grid 원본으로서의 Azure Container Registry
description: Azure Event Grid에서 Container Reigstry 이벤트에 대해 제공되는 속성을 설명합니다.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 9be7a7b8b9674aa755bc40d56394257783753edd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651088"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Event Grid 원본으로서의 Azure Container Registry

이 문서에서는 Container Reigstry 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Container Registry는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | 이미지를 푸시할 때 발생합니다. |
| Microsoft.ContainerRegistry.ImageDeleted | 이미지를 삭제할 때 발생합니다. |
| Microsoft.ContainerRegistry.ChartPushed | Helm 차트가 푸시될 때 발생합니다. |
| Microsoft.ContainerRegistry.ChartDeleted | Helm 차트가 삭제될 때 발생합니다. |

## <a name="example-event"></a>예제 이벤트

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

다음 예제는 이미지가 푸시된 이벤트의 스키마를 보여줍니다. 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

이미지가 삭제된 이벤트의 스키마는 다음과 유사합니다.

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

차트 푸시 이벤트의 스키마는 이미징된 푸시 이벤트에 대한 스키마와 유사하지만 요청 개체를 포함하지 않습니다.

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

차트 삭제 이벤트의 스키마는 이미징된 삭제 이벤트에 대한 스키마와 유사하지만 요청 개체 포함하지 않습니다.

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

다음 예제는 이미지가 푸시된 이벤트의 스키마를 보여줍니다. 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "type": "ImagePushed",
  "time": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "specversion": "1.0"
}]
```

이미지가 삭제된 이벤트의 스키마는 다음과 유사합니다.

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "type": "ImageDeleted",
  "time": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "specversion": "1.0"
}]
```

차트 푸시 이벤트의 스키마는 이미징된 푸시 이벤트에 대한 스키마와 유사하지만 요청 개체를 포함하지 않습니다.

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "type": "Microsoft.ContainerRegistry.ChartPushed",
  "time": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "specversion": "1.0"
}]
```

차트 삭제 이벤트의 스키마는 이미징된 삭제 이벤트에 대한 스키마와 유사하지만 요청 개체 포함하지 않습니다.

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "source": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "type": "Microsoft.ContainerRegistry.ChartDeleted",
  "time": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>이벤트 속성

# <a name="event-grid-event-schema"></a>[Event Grid 이벤트 스키마](#tab/event-grid-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `topic` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `eventType` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `eventTime` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Blob Storage 이벤트 데이터입니다. |
| `dataVersion` | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| `metadataVersion` | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

# <a name="cloud-event-schema"></a>[클라우드 이벤트 스키마](#tab/cloud-event-schema)

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `source` | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| `subject` | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| `type` | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| `time` | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| `id` | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| `data` | object | Blob Storage 이벤트 데이터입니다. |
| `specversion` | 문자열 | CloudEvents 스키마 사양 버전입니다. |

---

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `id` | 문자열 | 이벤트 ID입니다. |
| `timestamp` | 문자열 | 이벤트가 발생한 시간입니다. |
| `action` | 문자열 | 제공된 이벤트를 포함하는 동작입니다. |
| `target` | object | 이벤트의 대상입니다. |
| `request` | object | 이벤트를 생성한 요청입니다. |


대상 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `mediaType` | 문자열 | 참조된 개체의 MIME 형식입니다. |
| `size` | 정수 | 콘텐츠의 바이트 수입니다. 길이 필드와 동일합니다. |
| `digest` | 문자열 | 콘텐츠의 다이제스트로, 레지스트리 V2 HTTP API 사양에 따라 정의됩니다. |
| `length` | 정수 | 콘텐츠의 바이트 수입니다. 크기 필드와 동일합니다. |
| `repository` | 문자열 | 리포지토리 이름입니다. |
| `tag` | 문자열 | 태그 이름입니다. |
| `name` | 문자열 | 차트 이름입니다. |
| `version` | 문자열 | 차트 버전입니다. |

요청 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| `id` | 문자열 | 이벤트를 시작한 요청의 ID입니다. |
| `addr` | 문자열 | IP 또는 호스트 이름 및 가능한 경우 이벤트를 시작한 클라이언트 연결의 포트입니다. 이 값은 표준 http 요청에서 온 RemoteAddr입니다. |
| `host` | 문자열 | 외부에서 액세스할 수 있는 레지스트리 인스턴스의 호스트 이름으로, 들어오는 요청의 http 호스트 헤더를 통해 지정됩니다. |
| `method` | 문자열 | 이벤트를 생성한 요청 메서드입니다. |
| `useragent` | 문자열 | 요청의 사용자 에이전트 헤더입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목 |설명  |
|---------|---------|
| [빠른 시작: 컨테이너 레지스트리 이벤트 전송](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Container Registry 이벤트를 전송하는 방법을 보여줍니다. |


## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
