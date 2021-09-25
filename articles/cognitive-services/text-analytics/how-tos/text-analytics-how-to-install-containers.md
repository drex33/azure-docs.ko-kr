---
title: Text Analytics API에 대한 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Cognitive Services
description: Text Analytics API에 대한 Docker 컨테이너를 사용하여 감정 분석, 온-프레미스와 같은 자연어 처리를 수행합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020, devx-track-azurecli
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: aahi
keywords: 온-프레미스, Docker, 컨테이너, 감정 분석, 자연어 처리
ms.openlocfilehash: 4cf8ddce1045209f6d5e45a2eaccbbf4d2f03afc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677570"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics 컨테이너 설치 및 실행

컨테이너를 사용하면 사용자 환경에서 Text Analytic API를 실행할 수 있으며 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 사용할 수 있는 Text Analytics 컨테이너는 다음과 같습니다.

* 감정 분석
* 언어 감지
* 핵심 구 추출(미리 보기)
* 의료 분야 Text Analytics 

> [!NOTE]
> * 엔터티 연결 및 NER은 현재 컨테이너로 사용할 수 없습니다.
> * 컨테이너 이미지 위치가 최근에 변경되었을 수 있습니다. 이 컨테이너의 업데이트된 위치를 확인하려면 이 문서를 참조하세요.
> * 체험 계정은 매월 5,000개의 텍스트 레코드로 제한되며 **무료** 및 **표준** [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics)만 컨테이너에 유효합니다. 트랜잭션 요청 속도에 대한 자세한 내용은 [데이터 제한](../concepts/data-limits.md)을 참조하세요.

컨테이너를 사용하면 사용자 환경에서 Text Analytic API를 실행할 수 있으며 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. Text Analytics 컨테이너는 원시 텍스트에 대한 고급 자연어 처리를 제공하며 감정 분석, 핵심 구 추출, 언어 감지 등 세 가지 주요 기능을 포함하고 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Text Analytics 컨테이너를 사용하려면 다음 필수 조건을 충족해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

* 호스트 컴퓨터에 [Docker](https://docs.docker.com/)가 설치되어 있습니다. Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. 
    * Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.
    * [Docker 개념](https://docs.docker.com/get-started/overview/)에 대한 기본 지식이 있어야 합니다. 
* 무료(F0) 또는 표준(S) [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)을 사용하여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스 "  target="_blank">Text Analytics 리소스</a>를 만듭니다.

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>호스트 컴퓨터 요구 사항 및 권장 사항

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

다음 표에서는 사용 가능한 Text Analytics 컨테이너의 최소 및 권장 사양에 대해 설명합니다. 각 CPU 코어는 속도가 2.6GHz 이상이어야 합니다. 허용되는 TPS(초당 트랜잭션 수)도 나열됩니다.

|  | 최소 호스트 사양 | 권장 호스트 사양 | 최소 TPS | 최대 TPS|
|---|---------|-------------|--|--|
| **언어 감지**   | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |15 | 30| 
| **핵심 구 추출(미리 보기)**   | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |15 | 30| 
| **감정 분석**   | 1개 코어, 2GB 메모리 | 4개 코어, 8GB 메모리 |15 | 30|
| **Text Analytics for Health - 요청별 1개 문서**   |  4개 코어, 10GB 메모리 | 6개 코어, 12GB 메모리 |15 | 30|
| **Text Analytics for Health - 요청별 10개 문서**   |  6개 코어, 16GB 메모리 | 8개 코어, 20GB 메모리 |15 | 30|

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 CPU 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

# <a name="sentiment-analysis"></a>[감정 분석 ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[핵심 구 추출(미리 보기)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[언어 감지](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[의료 분야 Text Analytics](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

컨테이너가 호스트 컴퓨터에 있으면 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. 컨테이너는 중지할 때까지 계속 실행됩니다.

> [!IMPORTANT]
> * 다음 섹션에서 Docker 명령은 줄 연속 문자 같은 백 슬래시, `\`을 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
> * 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.
>   * 상태 컨테이너에 Text Analytics를 사용하는 경우에는 RAI([책임 AI](/legal/cognitive-services/text-analytics/transparency-note-health)) 승인을 `accept` 값과 함께 제공해야 합니다.
> * 감정 분석 및 언어 감지 컨테이너는 API v3을 사용하며 출시되었습니다. 핵심 구 추출 컨테이너는 API의 v2를 사용하며 미리 보기 상태입니다.

# <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[핵심 구 추출(미리 보기)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[언어 감지](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[의료 분야 Text Analytics](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](../text-analytics-resource-container-config.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Text Analytics 컨테이너는 Azure 계정의 _Text Analytics_ 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](../text-analytics-resource-container-config.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Text Analytics 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Text Analytics는 다양한 기능을 캡슐화하는 세 가지 Linux 컨테이너를 Docker에 제공합니다.
   * *감정 분석*
   * *핵심 구 추출(미리 보기)* 
   * *언어 감지*
   * *의료 분야 Text Analytics*
* 컨테이너 이미지는 Microsoft 컨테이너 레지스트리(MCR)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Text Analytics 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정은 [컨테이너 구성](../text-analytics-resource-container-config.md)을 참조하세요.
