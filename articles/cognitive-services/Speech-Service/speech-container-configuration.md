---
title: 음성 컨테이너 구성
titleSuffix: Azure Cognitive Services
description: Speech Service는 각 컨테이너에 공통된 구성 프레임워크를 제공하므로, 사용자는 컨테이너에 대한 스토리지, 로깅 및 원격 분석은 물론, 보안 설정을 쉽게 구성하고 관리할 수 있습니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: eur
ms.openlocfilehash: 8824e6cdf70fae19becb54d5a404b4cb8a9a344d
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503568"
---
# <a name="configure-speech-service-containers"></a>Speech Service 컨테이너 구성

음성 컨테이너를 통해 고객은 강력한 클라우드 기능 및 에지 위치를 활용하기 위해 최적화된 단일 음성 애플리케이션 아키텍처를 구축할 수 있습니다. 현재 지원되는 5가지 음성 컨테이너는 **음성 텍스트 변환**, **사용자 지정 음성 텍스트 변환**, **텍스트 음성 변환**, **인공신경망 텍스트 음성 변환** 및 **사용자 지정 텍스트 음성 변환** 입니다.

**Speech** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 몇 가지 명령의 [예제](#example-docker-run-commands)를 사용할 수 있습니다. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](speech-container-howto.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 이 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 대해 지정된 _Speech_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

- Azure Portal: **Speech** 의 리소스 관리, **키** 아래

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 _Speech_ 리소스에 대한 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure의 _Speech_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

- Azure Portal: **Speech** 개요, `Endpoint` 레이블

| 필수 | Name | 데이터 형식 | Description |
| -------- | ---- | --------- | ----------- |
| 예 | `Billing` | String | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](speech-container-howto.md#gathering-required-parameters)을 참조하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

표준 Speech 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 그러나 Custom Speech 컨테이너는 볼륨 탑재를 사용합니다.

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](speech-container-howto.md#host-computer-requirements-and-recommendations)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다.

| Optional | Name | 데이터 형식 | Description |
| -------- | ---- | --------- | ----------- |
| 허용되지 않음 | `Input` | String | 표준 Speech 컨테이너는 사용하지 않습니다. Custom Speech 컨테이너는 [볼륨 탑재](#volume-mount-settings)를 사용합니다.                                                                                    |
| 선택 | `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>볼륨 탑재 설정

Custom Speech 컨테이너는 [볼륨 탑재](https://docs.docker.com/storage/volumes/)를 사용하여 사용자 지정 모델을 유지합니다. [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령에 `-v`(또는 `--volume`) 옵션을 추가하여 볼륨 탑재를 지정할 수 있습니다.

사용자 지정 모델은 Custom Speech 컨테이너 docker run 명령의 일부로 새 모델을 수집할 때 처음 다운로드됩니다. Custom Speech 컨테이너에 대해 동일한 `ModelId`를 순차적으로 실행하면 이전에 다운로드한 모델이 사용됩니다. 볼륨 탑재를 제공하지 않으면 사용자 지정 모델을 유지할 수 없습니다.

볼륨 탑재 설정은 세 가지 색상의 `:`로 구분된 필드로 구성됩니다.

1. 첫 번째 필드는 호스트 컴퓨터의 볼륨 이름입니다(예: _C:\input_).
2. 두 번째 필드는 컨테이너의 디렉터리입니다(예: _/usr/local/models_).
3. 세 번째 필드(선택 사항)는 쉼표로 구분된 옵션 목록입니다. 자세한 내용은 [볼륨 사용](https://docs.docker.com/storage/volumes/)을 참조하세요.

### <a name="volume-mount-example"></a>볼륨 탑재 예

```bash
-v C:\input:/usr/local/models
```

이 명령은 호스트 컴퓨터 _C:\input_ 디렉터리를 컨테이너 _/usr/local/models_ 디렉터리에 탑재합니다.

> [!IMPORTANT]
> 볼륨 탑재 설정은 **사용자 지정 음성 텍스트 변환** 및 **사용자 지정 텍스트 음성 변환** 컨테이너에만 적용됩니다. **음성 텍스트 변환**, **인공신경망 텍스트 음성 변환** 및 **텍스트 음성 변환** 컨테이너는 볼륨 탑재를 사용하지 않습니다.

## <a name="example-docker-run-commands"></a>Docker 실행 명령 예제

다음 예제에서는 구성 설정을 사용하여 `docker run` 명령을 쓰고 사용하는 방법을 설명합니다. 한번 실행되면 컨테이너는 [중지](speech-container-howto.md#stop-the-container)할 때까지 계속 실행됩니다.

- **줄 연속 문자**: 다음 섹션에서 Docker 명령은 줄 연속 문자로 백슬래시(`\`)를 사용합니다. 호스트 운영 체제의 요구 사항에서 이 기준을 바꾸거나 제거합니다.
- **인수 순서**: Docker 컨테이너 사용법을 잘 아는 경우가 아니라면 인수 순서를 변경하지 마세요.

{_argument_name_}을(를) 사용자 고유 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Azure `Speech` 키 페이지에 있는 `Speech` 리소스의 엔드포인트 키입니다.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | 청구 엔드포인트 값은 Azure `Speech` 개요 페이지에서 사용할 수 있습니다. | 명시적 예제에 대해서는 [필수 매개 변수 수집](speech-container-howto.md#gathering-required-parameters)을 참조하세요. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing-configuration-setting)를 참조하세요.
> ApiKey 값은 Azure Speech 리소스 키 페이지의 **키** 입니다.

## <a name="speech-container-docker-examples"></a>Speech 컨테이너 Docker 예

아래에는 Speech 컨테이너용 Docker 예가 나와 있습니다.

## <a name="speech-to-text"></a>[음성 텍스트 변환](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>음성 텍스트 변환의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>음성 텍스트 변환의 로깅 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[사용자 지정 음성 텍스트 변환](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>사용자 지정 음성 텍스트 변환의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>사용자 지정 음성 텍스트 변환의 로깅 예

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>텍스트 음성 변환의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>텍스트 음성 변환의 로깅 예

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[텍스트 음성 변환 사용자 지정](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환의 로깅 예

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[인공신경망 텍스트 음성 변환](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>인공신경망 텍스트 음성 변환의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>인공신경망 텍스트 음성 변환의 로깅 예

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-identification"></a>[음성 언어 식별](#tab/lid)

### <a name="basic-example-for-speech-language-identification"></a>음성 언어 식별의 기본 예

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-identification"></a>음성 언어 식별의 로깅 예

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>다음 단계

- [컨테이너 설치 및 실행 방법](speech-container-howto.md)을 리뷰합니다.
