---
title: 읽기 OCR 컨테이너 구성 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Computer Vision에서 읽기 OCR 컨테이너에 대한 필수 설정과 선택적 설정을 모두 구성하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: d5e405447a93f23f4845a018a74229aacbbad09c
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113302125"
---
# <a name="configure-read-ocr-docker-containers"></a>읽기 OCR Docker 컨테이너 구성

`docker run` 명령 인수를 사용하여 Computer Vision 읽기 OCR 컨테이너의 런타임 환경을 구성합니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다. 

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](computer-vision-how-to-install-containers.md)를 참조하세요.

컨테이너에는 다음과 같은 컨테이너 관련 구성 설정도 있습니다.

|필수|설정|용도|
|--|--|--|
|예|ReadEngineConfig:ResultExpirationPeriod| v2.0 컨테이너 전용입니다. 결과 만료 기간(시간)입니다. 기본값은 48시간입니다. 이 설정은 시스템이 인식 결과를 지워야 하는 시기를 지정합니다. 예를 들어 `resultExpirationPeriod=1`인 경우 시스템은 프로세스 후 1시간 뒤에 인식 결과를 지웁니다. `resultExpirationPeriod=0`이면 결과가 검색된 후 시스템이 인식 결과를 지웁니다.|
|예|Cache:Redis| v2.0 컨테이너 전용입니다. 결과를 저장하는 Redis 스토리지를 사용하도록 설정합니다. 여러 읽기 OCR 컨테이너가 부하 분산 장치 뒤에 배치되는 경우 캐시가 *필요* 합니다.|
|예|Queue:RabbitMQ|v2.0 컨테이너 전용입니다. 작업을 디스패칭하는 RabbitMQ를 사용하도록 설정합니다. 이 설정은 여러 읽기 OCR 컨테이너가 부하 분산 장치 뒤에 배치되는 경우에 유용합니다.|
|예|Queue:Azure:QueueVisibilityTimeoutInMilliseconds | v3.x 컨테이너 전용입니다. 다른 작업자가 처리할 때 메시지가 표시되지 않도록 하는 시간입니다. |
|예|Storage::DocumentStore::MongoDB|v2.0 컨테이너 전용입니다. 영구 결과 스토리지에 대해 MongoDB를 사용하도록 설정합니다. |
|예|Storage:ObjectStore:AzureBlob:ConnectionString| v3.x 컨테이너 전용입니다. Azure Blob 스토리지 연결 문자열. |
|예|Storage:TimeToLiveInDays| v3.x 컨테이너 전용입니다. 결과 만료 기간(일)입니다. 이 설정은 시스템이 인식 결과를 지워야 하는 시기를 지정합니다. 기본값은 2일(48시간)입니다. 즉, 해당 기간보다 더 길게 지속되는 결과는 성공적으로 검색되지 않을 수 있습니다. |
|예|Task:MaxRunningTimeSpanInMinutes| v3.x 컨테이너 전용입니다. 단일 요청에 대한 최대 실행 시간입니다. 기본값은 60분입니다. |
|No|EnableSyncNTPServer| v3.x 컨테이너 전용입니다. 시스템 시간과 예상 작업 런타임 간의 동기화를 보장하는 NTP 서버 동기화 메커니즘을 사용하도록 설정합니다. 이를 위해서는 외부 네트워크 트래픽이 필요합니다. 기본값은 `true`입니다. |
|예|NTPServerAddress| v3.x 컨테이너 전용입니다. 시간 동기화를 위한 NTP 서버입니다. 기본값은 `time.windows.com`입니다. |
|예|Mounts::Shared| v3.x 컨테이너 전용입니다. 인식 결과를 저장하기 위한 로컬 폴더입니다. 기본값은 `/share`입니다. Azure Blob Storage를 사용하지 않고 컨테이너를 실행하는 경우 인식 결과를 위한 충분한 공간이 있는지 확인하기 위해 이 폴더에 볼륨을 탑재하는 것이 좋습니다. |

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure `Cognitive Services` 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 이 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 대해 지정된 _Cognitive Services_ 리소스에 대해 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **키** 아래의 **Cognitive Services** 리소스 관리

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 _Cognitive Services_ 리소스 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure의 _Cognitive Services_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: `Endpoint` 레이블이 지정된 **Cognitive Services** 개요

다음 표에 표시된 대로 엔드포인트 URI에 `vision/<version>` 라우팅을 추가해야 합니다. 

|필수| Name | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 엔드포인트 URI입니다.<br><br>예:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v3.2` |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

Computer Vision 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정에서 사용하는 권한과 호스트 탑재 위치 권한이 충돌하여 [호스트 컴퓨터](computer-vision-how-to-install-containers.md#the-host-computer)의 탑재 위치에 액세스하지 못할 수도 있습니다. 

|선택 사항| 이름 | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | String | Computer Vision 컨테이너에는 사용되지 않습니다.|
|선택| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다.  한번 실행되면 컨테이너는 [중지](computer-vision-how-to-install-containers.md#stop-the-container)할 때까지 계속 실행됩니다.

* **줄 연속 문자**: 다음 섹션에서 Docker 명령은 줄 연속 문자로 백슬래시(`\`)를 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다. 
* **인수 순서**: Docker 컨테이너 사용법을 잘 아는 경우가 아니라면 인수 순서를 변경하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Computer Vision` 키 페이지에 있는 `Computer Vision` 리소스의 엔드포인트 키입니다. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 청구 엔드포인트 값은 Azure `Computer Vision` 개요 페이지에서 사용할 수 있습니다.| 명시적 예제에 대해서는 [필수 매개 변수 수집](computer-vision-how-to-install-containers.md#gathering-required-parameters)을 참조하세요. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](computer-vision-how-to-install-containers.md#billing)를 참조하세요.
> ApiKey 값은 Azure `Cognitive Services` 리소스 키 페이지의 **키** 입니다.

## <a name="container-docker-examples"></a>컨테이너 Docker 예제

다음 Docker 예제는 읽기 OCR 컨테이너에 대한 것입니다.


# <a name="version-32"></a>[버전 3.2](#tab/version-3-2)

### <a name="basic-example"></a>기본 예제

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>로깅 예제 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[버전 2.0-preview](#tab/version-2)

### <a name="basic-example"></a>기본 예제

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>로깅 예 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](computer-vision-how-to-install-containers.md)을 검토합니다.
