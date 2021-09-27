---
title: Anomaly Detector API에 대해 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Cognitive Services
description: Docker 컨테이너를 사용하는 온-프레미스 환경에서 데이터의 비정상 상태를 찾으려면 Anomaly Detector API의 알고리즘을 사용합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbullwin
ms.custom: cog-serv-seo-aug-2020
keywords: 온-프레미스, Docker, 컨테이너, 스트리밍, 알고리즘
ms.openlocfilehash: 9a2481705ef1bed5a4b6d20bdfcbe68022c5f9e2
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057382"
---
# <a name="install-and-run-docker-containers-for-the-anomaly-detector-api"></a>Anomaly Detector API에 대해 Docker 컨테이너 설치 및 실행 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

컨테이너를 사용하면 사용자 고유의 환경에서 Anomaly Detector API를 사용할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 Anomaly Detector 컨테이너를 다운로드, 설치, 실행하는 방법을 알아봅니다.

Anomaly Detector는 온-프레미스 환경에서 API를 사용하기 위한 단일 Docker 컨테이너를 제공합니다. 컨테이너를 사용하여 다음을 수행합니다.
* 데이터에 Anomaly Detector의 알고리즘을 사용합니다.
* 스트리밍 데이터를 모니터링하고 이상이 발생하면 실시간으로 검색합니다.
* 일괄 처리로 데이터 세트 전체의 변칙을 검색합니다. 
* 일괄 처리로 데이터 세트에서 추세 변화 포인트를 검색합니다.
* 데이터에 더 적합하도록 변칙 검색 알고리즘의 민감도를 조정합니다.

API에 대한 자세한 정보는 다음을 참조하세요.
* [Anomaly Detector API 서비스에 대해 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Anomaly Detector 컨테이너를 사용하기 전에 다음 사전 요구 사항을 충족해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows** 에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.|
|Anomaly Detector 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 API 키 및 엔드포인트 URI를 가져오기 위한 Azure _Anomaly Detector_ 리소스가 있어야 합니다. 두 값은 모두 Azure Portal의 **Anomaly Detector** 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}** : **키** 페이지에서 사용 가능한 두 개의 리소스 키 중 하나<br><br>**{ENDPOINT_URI}** : **개요** 페이지에 제공된 엔드포인트|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](../../iot-edge/index.yml). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 Anomaly Detector 컨테이너에 할당해야 하는 최소/권장 CPU 코어 수와 메모리에 대해 설명합니다.

| QPS(초당 쿼리 수) | 최소 | 권장 |
|-----------|---------|-------------|
| 10 QPS | 4코어, 1GB 메모리 | 8코어, 2GB 메모리 |
| 20 QPS | 8코어, 2GB 메모리 | 16코어, 4GB 메모리 |

각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 이미지를 다운로드합니다.

| 컨테이너 | 리포지토리 |
|-----------|------------|
| cognitive-services-anomaly-detector | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Anomaly Detector 컨테이너에 대한 Docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](anomaly-detector-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](anomaly-detector-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Anomaly Detector 컨테이너 실행
* 1개 CPU 코어 및 4GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

### <a name="running-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

`<container-registry>`와 `<container-name>`을 사용하는 컨테이너의 값으로 대체합니다. 동일한 컨테이너일 필요는 없습니다. Anomaly Detector 컨테이너 및 LUIS 컨테이너를 HOST에서 함께 실행하거나 여러 Anomaly Detector 컨테이너를 실행할 수 있습니다.

호스트 포트 5000에서 첫 번째 컨테이너를 실행 합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

호스트 포트 5001에서 두 번째 컨테이너를 실행 합니다.


```bash
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이후의 각 컨테이너는 다른 포트에 있어야 합니다.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

컨테이너 API에 대한 호스트 http://localhost:5000을 사용합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](anomaly-detector-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Anomaly Detector 컨테이너는 Azure 계정의 _Anomaly Detector_ 리소스를 사용하여 청구 정보를 Azure로 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](anomaly-detector-container-configuration.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Anomaly Detector 컨테이너를 다운로드하고, 설치하고, 실행하는 개념 및 워크플로에 대해 알아보았습니다. 요약하면 다음과 같습니다.

* Anomaly Detector는 Docker용 Linux 컨테이너 한 개를 제공하여 일괄 대 스트리밍, 예상 범위 유추, 민감도 튜닝으로 변칙 검색을 캡슐화합니다.
* 컨테이너 이미지는 컨테이너 전용 프라이빗 Azure Container Registry에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Anomaly Detector 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 시계열 데이터)를 Microsoft로 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [Azure Container Instances에 Anomaly Detector 컨테이너 배포](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Anomaly Detector API 서비스에 대해 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
