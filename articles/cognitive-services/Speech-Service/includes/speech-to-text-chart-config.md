---
title: Speech 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: speech-to-text helm 차트 구성 옵션을 자세히 설명합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: eur
ms.openlocfilehash: b5d2eed959f216d7ec3f1b504e086b1b83efdb58
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500777"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>음성 텍스트 변환(하위 차트: charts/speechToText)

“Umbrella” 차트를 재정의하려면 매개 변수에 `speechToText.` 접두사를 추가하여 더 구체화합니다. 예를 들어, `speechToText.numberOfConcurrentRequest`가 `numberOfConcurrentRequest`를 재정의하는 것처럼 해당 매개 변수를 재정의합니다.

|매개 변수|Description|기본값|
| -- | -- | -- |
| `enabled` | **speech-to-text** 서비스 사용 여부를 나타냅니다. | `false` |
| `numberOfConcurrentRequest` | **speech-to-text** 서비스의 동시 요청 수입니다. 이 차트는 해당 값에 따라 CPU 및 메모리 리소스를 자동으로 계산합니다. | `2` |
| `optimizeForAudioFile`| 서비스가 오디오 파일을 통해 오디오 입력에 대해 최적화해야 하는지 여부를 나타냅니다. `true`인 경우 이 차트는 더 많은 CPU 리소스를 서비스에 할당합니다. | `false` |
| `image.registry`| **speech-to-text** Docker 이미지 레지스트리입니다. | `containerpreview.azurecr.io` |
| `image.repository` | **speech-to-text** Docker 이미지 리포지토리입니다. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **speech-to-text** Docker 이미지 태그입니다. | `latest` |
| `image.pullSecrets` | **speech-to-text** Docker 이미지를 풀하기 위한 이미지 비밀입니다. | |
| `image.pullByHash`| Docker 이미지를 해시를 통해 풀할지 여부를 나타냅니다. `true`인 경우 `image.hash`가 필요합니다. | `false` |
| `image.hash`| **speech-to-text** Docker 이미지 해시입니다. `image.pullByHash: true`인 경우에만 사용됩니다.  | |
| `image.args.eula`(필수) | 라이선스에 동의했음을 나타냅니다. 유효한 값은 `accept`뿐입니다. | |
| `image.args.billing`(필수) | 청구 엔드포인트 URI 값은 Azure Portal의 Speech 개요 페이지에서 제공됩니다. | |
| `image.args.apikey`(필수) | 청구 정보를 추적하는 데 사용됩니다. ||
| `service.type` | **speech-to-text** 서비스의 Kubernetes 서비스 유형입니다. 자세한 내용은 [Kubernetes 서비스 유형 지침](https://kubernetes.io/docs/concepts/services-networking/service/)을 참조하고 클라우드 공급자 지원을 확인하세요. | `LoadBalancer` |
| `service.port`|  **speech-to-text** 서비스의 포트입니다. | `80` |
| `service.annotations` | 서비스 메타데이터의 **speech-to-text** 주석입니다. 주석은 키 값 쌍입니다. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)가 사용되는지 여부를 나타냅니다. `true`인 경우 `speech-to-text-autoscaler`가 Kubernetes 클러스터에 배포됩니다. | `true` |
| `service.podDisruption.enabled` | [Pod Disruption Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)이 사용되는지 여부를 나타냅니다. `true`인 경우 `speech-to-text-poddisruptionbudget`이 Kubernetes 클러스터에 배포됩니다. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>감정 분석(하위 차트: chars/speechToText)

음성 텍스트로 번역 컨테이너의 v2.2.0 및 Helm 차트의 v0.2.0부터 다음 매개 변수는 언어 서비스 API를 사용하여 감정 분석에 사용됩니다.

|매개 변수|Description|값|기본값|
| --- | --- | --- | --- |
|`textanalytics.enabled`| **text-analytics** 서비스 사용 여부를 나타냅니다.| true/false| `false`|
|`textanalytics.image.registry`| **text-analytics** Docker 이미지 레지스트리입니다.| 유효한 Docker 이미지 레지스트리| |
|`textanalytics.image.repository`| **text-analytics** Docker 이미지 리포지토리입니다.| 유효한 Docker 이미지 리포지토리| |
|`textanalytics.image.tag`| **text-analytics** Docker 이미지 태그입니다.| 유효한 Docker 이미지 태그| |
|`textanalytics.image.pullSecrets`| **text-analytics** Docker 이미지를 풀하기 위한 이미지 비밀입니다.| 유효한 비밀 이름| |
|`textanalytics.image.pullByHash`| 해시를 통해 Docker 이미지를 풀할지 여부를 지정합니다.  `yes`인 경우 `image.hash`도 포함해야 합니다. `no`인 경우 ‘false’로 설정합니다. 기본값은 `false`입니다.| true/false| `false`|
|`textanalytics.image.hash`| **text-analytics** Docker 이미지 해시입니다. `image.pullByHash:true`에서만 사용됩니다.| 유효한 Docker 이미지 해시 | |
|`textanalytics.image.args.eula`| 라이선스에 동의했음을 나타내는 **text-analytics** 컨테이너의 필수 인수 중 하나입니다. 이 옵션의 값은 `accept`로 설정해야 합니다.| 컨테이너를 사용하려면 `accept` | |
|`textanalytics.image.args.billing`| 청구 엔드포인트 URI를 지정하는 **text-analytics** 컨테이너의 필수 인수 중 하나입니다. 청구 엔드포인트 URI 값은 Azure Portal의 Speech 개요 페이지에서 제공됩니다.|유효한 청구 엔드포인트 URI||
|`textanalytics.image.args.apikey`| 청구 정보를 추적하는 데 사용되는 **text-analytics** 컨테이너의 필수 인수 중 하나입니다.| 유효한 apikey||
|`textanalytics.cpuRequest`| **text-analytics** 컨테이너의 요청된 CPU입니다.| int| `3000m`|
|`textanalytics.cpuLimit`| **text-analytics** 컨테이너의 제한된 CPU입니다.| | `8000m`|
|`textanalytics.memoryRequest`| **text-analytics** 컨테이너의 요청된 메모리입니다.| | `3Gi`|
|`textanalytics.memoryLimit`| **text-analytics** 컨테이너의 제한된 메모리입니다.| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| 감정 분석 URI 접미사, 전체 URI는 “http://:`<service>`:`<port>`/`<sentimentURISuffix>`” 형식입니다. | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Kubernetes의 **text-analytics** 서비스 유형입니다. [Kubernetes 서비스 유형](https://kubernetes.io/docs/concepts/services-networking/service/)을 참조하세요. | 유효한 Kubernetes 서비스 유형 | `LoadBalancer` |
|`textanalytics.service.port`| **text-analytics** 서비스의 포트입니다.| int| `50085`|
|`textanalytics.service.annotations`| 사용자가 **text-analytics** 서비스 메타데이터에 추가할 수 있는 주석입니다. 예를 들면 다음과 같습니다.<br/> **annotations:**<br/>`   ` **some/annotation1: value1**<br/>`  ` **some/annotation2: value2** | 주석, 줄당 하나| |
|`textanalytics.serivce.autoScaler.enabled`| [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)가 사용되는지 여부를 나타냅니다. 사용되는 경우 `text-analytics-autoscaler`가 Kubernetes 클러스터에 배포됩니다. | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| [Pod Disruption Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)이 사용되는지 여부를 나타냅니다. 사용되는 경우 `text-analytics-poddisruptionbudget`이 Kubernetes 클러스터에 배포됩니다.| true/false| `true`|
