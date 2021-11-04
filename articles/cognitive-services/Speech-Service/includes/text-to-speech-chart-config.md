---
title: Speech 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: text-to-speech helm 차트 구성 옵션을 자세히 설명합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: eur
ms.openlocfilehash: b29fc97b9b354ce76728fdf3f2ed4f2cb4f3b6ad
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500776"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>텍스트 음성 변환(하위 차트: charts/textToSpeech)

“Umbrella” 차트를 재정의하려면 매개 변수에 `textToSpeech.` 접두사를 추가하여 더 구체화합니다. 예를 들어, `textToSpeech.numberOfConcurrentRequest`가 `numberOfConcurrentRequest`를 재정의하는 것처럼 해당 매개 변수를 재정의합니다.

|매개 변수|Description|기본값|
| -- | -- | -- |
| `enabled` | **text-to-speech** 서비스 사용 여부를 나타냅니다. | `false` |
| `numberOfConcurrentRequest` | **text-to-speech** 서비스의 동시 요청 수입니다. 이 차트는 해당 값에 따라 CPU 및 메모리 리소스를 자동으로 계산합니다. | `2` |
| `optimizeForTurboMode`| 서비스가 텍스트 파일을 통해 텍스트 입력에 대해 최적화해야 하는지 여부를 나타냅니다. `true`인 경우 이 차트는 더 많은 CPU 리소스를 서비스에 할당합니다. | `false` |
| `image.registry`| **text-to-speech** Docker 이미지 레지스트리입니다. | `containerpreview.azurecr.io` |
| `image.repository` | **text-to-speech** Docker 이미지 리포지토리입니다. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **text-to-speech** Docker 이미지 태그입니다. | `latest` |
| `image.pullSecrets` | **text-to-speech** Docker 이미지를 풀하기 위한 이미지 비밀입니다. | |
| `image.pullByHash`| Docker 이미지를 해시를 통해 풀할지 여부를 나타냅니다. `true`인 경우 `image.hash`가 필요합니다. | `false` |
| `image.hash`| **text-to-speech** Docker 이미지 해시입니다. `image.pullByHash: true`인 경우에만 사용됩니다.  | |
| `image.args.eula`(필수) | 라이선스에 동의했음을 나타냅니다. 유효한 값은 `accept`뿐입니다. | |
| `image.args.billing`(필수) | 청구 엔드포인트 URI 값은 Azure Portal의 Speech 개요 페이지에서 제공됩니다. | |
| `image.args.apikey`(필수) | 청구 정보를 추적하는 데 사용됩니다. ||
| `service.type` | **text-to-speech** 서비스의 Kubernetes 서비스 유형입니다. 자세한 내용은 [Kubernetes 서비스 유형 지침](https://kubernetes.io/docs/concepts/services-networking/service/)을 참조하고 클라우드 공급자 지원을 확인하세요. | `LoadBalancer` |
| `service.port`|  **text-to-speech** 서비스의 포트입니다. | `80` |
| `service.annotations` | 서비스 메타데이터의 **text-to-speech** 주석입니다. 주석은 키 값 쌍입니다. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)가 사용되는지 여부를 나타냅니다. `true`인 경우 `text-to-speech-autoscaler`가 Kubernetes 클러스터에 배포됩니다. | `true` |
| `service.podDisruption.enabled` | [Pod Disruption Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)이 사용되는지 여부를 나타냅니다. `true`인 경우 `text-to-speech-poddisruptionbudget`이 Kubernetes 클러스터에 배포됩니다. | `true` |
