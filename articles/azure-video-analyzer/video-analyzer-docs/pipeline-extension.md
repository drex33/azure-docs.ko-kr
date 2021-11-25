---
title: 파이프라인 확장
description: Azure Video Analyzer를 사용하면 파이프라인 확장 노드를 통해 파이프라인 처리 능력을 확장할 수 있습니다. 이 문서에서는 파이프라인 확장 노드에 대해 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2ddfa55fb82118b4b01e65a749b98eb23b97811b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133062454"
---
# <a name="pipeline-extension"></a>파이프라인 확장

Azure Video Analyzer를 사용하면 파이프라인 확장 노드를 통해 파이프라인 처리 능력을 확장할 수 있습니다. 분석 확장 플러그 인은 기존의 이미지 처리 기술 또는 컴퓨터 비전 AI 모델을 사용할 수 있습니다. 파이프라인 확장은 확장 프로세서 노드를 파이프라인 흐름에 포함하여 사용할 수 있습니다. 확장 프로세서 노드는 비디오 프레임을 구성된 엔드포인트에 릴레이하고, 확장에 대한 인터페이스로 작동합니다. 로컬 또는 원격 엔드포인트에 연결할 수 있으며, 필요한 경우 인증 및 TLS 암호화를 통해 보안을 유지할 수 있습니다. 또한 파이프라인 확장 프로세서 노드를 사용하면 사용자 지정 확장에 전송하기 전에 비디오 프레임을 선택적으로 크기 조정하고 인코딩할 수 있습니다.

Video Analyzer는 다음 파이프라인 확장 프로세서를 지원합니다.

* [HTTP 확장 프로세서](pipeline.md#http-extension-processor) 
* [gRPC 확장 프로세서](pipeline.md#grpc-extension-processor)
* [Cognitive Services 확장 프로세서](pipeline.md#cognitive-services-extension-processor)

파이프라인 확장 노드에서는 분석 확장 플러그 인이 JSON 형식으로 결과를 반환해야 합니다. 이상적으로 결과는 [유추 메타데이터 스키마 개체 모델](inference-metadata-schema.md)을 따라야 합니다.

[!INCLUDE [available-features](./includes/available-features.md)]

## <a name="http-extension-processor"></a>HTTP 확장 프로세서

HTTP 확장 프로세서는 [HTTP 프로토콜](http-extension-protocol.md)을 사용하는 확장성 시나리오를 가능하게 합니다. 여기서 성능 및/또는 최적 리소스 사용률은 주요 문제가 아닙니다. HTTP REST 엔드포인트를 통해 자신의 AI를 파이프라인에 공개할 수 있습니다.

HTTP 확장 프로세서 노드를 사용하는 경우는 다음과 같습니다.

* 기존 HTTP 유추 시스템과의 상호 운용성을 더 효율적으로 향상시키려고 합니다.
* 저성능 데이터 전송이 허용됩니다.
* Video Analyzer에 대한 간단한 요청-응답 인터페이스를 사용하려고 합니다.

## <a name="grpc-extension-processor"></a>gRPC 확장 프로세서

gRPC 확장 프로세서는 gRPC 기반의 고성능 [구조적 프로토콜](grpc-extension-protocol.md)을 사용하는 확장성 시나리오를 가능하게 합니다. 성능 및/또는 최적 리소스 사용률이 중요하게 고려되는 시나리오에 적합합니다. gRPC 확장 프로세서를 사용하면 정형 데이터 정의의 모든 이점을 활용할 수 있습니다. gRPC는 다음을 사용하여 높은 콘텐츠 전송 성능을 제공합니다.

* [기본 공유 메모리](https://en.wikipedia.org/wiki/Shared_memory) 또는
* 콘텐츠를 gRPC 메시지의 본문에 직접 포함

gRPC 확장 프로세서는 유추 메시지 교환과 함께 속성을 보내는 데 사용할 수 있습니다. 따라서 gRPC 확장 프로세서 노드를 사용하는 경우는 다음과 같습니다.

* 구조화된 계약(예: 요청 및 응답에 대한 구조화된 메시지)을 사용하려고 합니다.
* [프로토콜 버퍼(protobuf)](https://developers.google.com/protocol-buffers)를 통신에 대한 기본 메시지 교환 형식으로 사용하려고 합니다.
* 들어오는 요청을 구문 분석하고 올바른 구현 함수를 호출하는 사용자 지정 요청 처리기가 필요한 기존 요청-응답 모델 대신, 단일 스트림 세션에서 gRPC 서버와 통신하려고 합니다.
* Video Analyzer와 모듈 간에 짧은 대기 시간 및 높은 처리량의 통신이 필요합니다.

## <a name="cognitive-services-extension-processor"></a>Cognitive Services 확장 프로세서

Cognitive Services 확장 프로세서 (Microsoft에서 & 제공 하는 Microsoft에서 지원 되는 AI)는 사용자가 작성 한 확장 프로세서로, 비디오 분석기가 gRPC 기반의 고성능 [구조화 된 프로토콜](grpc-extension-protocol.md)을 사용 하 여 [Computer Vision 공간 분석](../../cognitive-services/computer-vision/overview.md) 기능과 잘 작동할 수 있도록 해 줍니다. 

다음과 같은 경우 Cognitive Services 확장 프로세서 노드를 사용합니다.

* 기존 [공간 분석 작업](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)과의 상호 운용성을 향상시키려고 합니다.
* **Microsoft에서 구현 하 고 지원 되는 AI** 의 grpc 프로토콜, 정확도 및 성능의 모든 이점을 사용 하고자 합니다.
* 짧은 대기 시간과 높은 처리량으로 여러 카메라 피드를 분석합니다.

## <a name="use-your-inferencing-model"></a>유추 모델 사용

파이프라인 확장을 사용하면 사용자 고유의 Docker 컨테이너에 있는 ONNX, TensorFlow, PyTorch 등과 같은 사용 가능한 유추 런타임에서 원하는 유추 모델을 실행할 수 있습니다. 최상의 성능을 위해 유추 사용자 지정 확장을 Video Analyzer 에지 모듈과 함께 배포해야 합니다. 그런 다음, 파이프라인 토폴로지에 포함된 HTTP 확장 프로세서, gRPC 확장 프로세서 또는 Cognitive Services 확장 프로세서를 통해 이 확장을 호출합니다. 또한 필요에 따라 [동작 탐지기 프로세서](pipeline.md#motion-detection-processor) 업스트림을 파이프라인 확장 프로세서에 추가하여 사용자 지정 확장에 대한 호출 빈도를 제한할 수 있습니다.

아래 다이어그램에서는 개략적인 데이터 흐름을 보여 줍니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline-extension/ai-inference-results.svg" alt-text="유추 모델":::
 
## <a name="samples"></a>샘플

[HTTP 확장 프로세서](pipeline.md#http-extension-processor)를 사용하는 낮은 프레임 속도 또는 [gRPC 확장 프로세서](pipeline.md#grpc-extension-processor)를 사용하는 높은 프레임 속도에서 미리 빌드된 확장 서비스를 통해 Video Analyzer를 설명하는 빠른 시작 중 하나로 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계 

개념: [비디오 녹화](video-recording.md)
