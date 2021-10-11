---
title: Read v3.x 컨테이너로 마이그레이션
titleSuffix: Azure Cognitive Services
description: v3 Read OCR 컨테이너로 마이그레이션하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: aahi
ms.openlocfilehash: 5998ccdfa48a700ac8789954246f6b7bcec60543
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360501"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Read v3.x OCR 컨테이너로 마이그레이션

버전 2의 Computer Vision Read OCR 컨테이너를 사용하는 경우 이 문서를 사용하여 컨테이너의 버전 3.x를 사용하도록 애플리케이션을 업그레이드하는 방법을 알아봅니다. 


## <a name="configuration-changes"></a>구성 변경 내용

* `ReadEngineConfig:ResultExpirationPeriod`은 더 이상 지원되지 않습니다. Read OCR 컨테이너에는 48시간 후에 요청에 연결된 결과와 메타데이터를 제거하는 빌드된 Cron 작업이 있습니다.
* `Cache:Redis:Configuration`은 더 이상 지원되지 않습니다. Cache는 v3.x 컨테이너에서 사용되지 않으므로 설정하지 않아도 됩니다.

## <a name="api-changes"></a>API 변경 내용

Read v3.2 컨테이너는 Computer Vision API 버전 3을 사용하고 다음 엔드포인트를 사용합니다.

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

클라우드 기반 읽기 API 버전 3을 사용하도록 애플리케이션을 업데이트하는 방법에 대한 자세한 내용은 [Computer Vision v3 REST API 마이그레이션 가이드](./upgrade-api-versions.md)를 참조하세요. 이 정보는 컨테이너에도 적용됩니다. 동기화 작업은 컨테이너에서만 지원됩니다.

## <a name="memory-requirements"></a>메모리 요구 사항

요구 사항 및 권장 사항은 초당 단일 요청을 포함하는 벤치마크를 기준으로 하며, 8MB의 스캔된 비즈니스 문자 이미지를 사용하여 29개 줄과 총 803 문자를 포함합니다. 다음 표에서는 각 Read OCR 컨테이너에 대한 최소 및 권장 리소스 할당을 설명합니다.

|컨테이너  |최소 | 권장  |
|---------|---------|------|
|Read 3.2 미리 보기 | 8 코어, 16-GB 메모리         | 8 코어, 24-GB 메모리 |

각 코어는 속도가 2.6GHz 이상이어야 합니다.

docker run 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리

## <a name="storage-implementations"></a>스토리지 구현

>[!NOTE]
> MongoDB는 3.x 버전의 컨테이너에서 더 이상 지원되지 않습니다. 대신 컨테이너는 Azure Storage 및 오프라인 파일 시스템을 지원합니다.

| 구현 |    필요한 런타임 인수 |
|---------|---------|
|파일 수준(기본값)    | 런타임 인수가 필요하지 않습니다. `/share` 디렉터리가 사용됩니다. |
|Azure Blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>큐 구현

v3.x의 컨테이너에서 RabbitMQ는 현재 지원되지 않습니다. 지원되는 지원 구현은 다음과 같습니다.

| 구현 | 런타임 인수 | 올바른 사용법 |
|---------|---------|-------|
| 메모리 내(기본값) | 런타임 인수가 필요하지 않습니다. | 개발 및 테스트 |
| Azure 큐 | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | 생산 |
| RabbitMQ    | Unavailable | 생산 |

중복 추가의 경우 Read v3.x 컨테이너는 표시 타이머를 사용하여 다중 컨테이너 설정에서 실행될 때 요청이 충돌이 발생하는 경우 요청이 성공적으로 처리될 수 있도록 합니다. 

다른 작업자가 처리할 때 메시지가 표시되지 않도록 하는 시간을 설정하는 `Queue:Azure:QueueVisibilityTimeoutInMilliseconds`로 타이머를 설정합니다. 페이지가 중복 처리되지 않도록 하려면 제한 시간을 120초로 설정하는 것이 좋습니다. 기본값은 30초입니다.

| 기본값 | 권장되는 값 |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](computer-vision-resource-container-config.md)에서 구성 설정을 검토합니다.
* [OCR 개요](overview-ocr.md)를 검토하여 인쇄 및 필기 텍스트를 인식하는 방법을 자세히 알아봅니다.
* [Read API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)를 참조하여 컨테이너에서 지원하는 방법을 자세히 알아봅니다.
* [FAQ(질문과 대답)](FAQ.yml)를 참조하여 Computer Vision 기능과 관련된 문제를 해결합니다.
* 추가적인 [Cognitive Services 컨테이너](../cognitive-services-container-support.md) 사용