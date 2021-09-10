---
title: 컨테이너 구성 - Translator
titleSuffix: Azure Cognitive Services
description: Translator 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 필수 설정 및 선택적 설정이 모두 있습니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f298a4593ef5aa8af9b7f62f48b4e32075429dbf
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539595"
---
# <a name="configure-translator-docker-containers-preview"></a>Translator Docker 컨테이너 구성(미리 보기)

Cognitive Services는 각 컨테이너에 공통 구성 프레임워크를 제공합니다.  Translator 컨테이너를 쉽게 구성하고, 강력한 클라우드 기능 및 에지 위치에 맞게 최적화된 Translator 애플리케이션 아키텍처를 빌드할 수 있습니다.

**Translator** 컨테이너 런타임 환경은 `docker run` 명령 인수를 사용하여 구성됩니다. 이 컨테이너에는 여러 필수 설정과 몇 가지 선택적 설정이 있습니다. 청구 설정은 컨테이너별로 다릅니다.

## <a name="configuration-settings"></a>구성 설정

컨테이너에는 다음 구성 설정이 있습니다.

|필수|설정|용도|
|--|--|--|
|예|[ApiKey](#apikey-configuration-setting)|청구 정보를 추적합니다.|
|예|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다.|
|예|[Billing](#billing-configuration-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|예|[Fluentd](#fluentd-settings)|로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 씁니다.|
|예|HTTP 프록시|아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성합니다.|
|예|[Logging](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |
|예|[Mounts](#mount-settings)|호스트 컴퓨터에서 컨테이너로, 컨테이너에서 호스트 컴퓨터로 데이터를 읽고 씁니다.|

 > [!IMPORTANT]
> [**ApiKey**](#apikey-configuration-setting), [**Billing**](#billing-configuration-setting) 및 [**EULA**](#eula-setting) 설정은 함께 사용되며, 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그렇지 않으면 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 다음을 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대한 값을 지정해야 하며 이 값은 [`Billing`](#billing-configuration-setting) 구성 설정에 대해 지정된 _Translator_ 리소스에 대한 유효한 키여야 합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **키** 아래의 **Translator** 리소스 관리

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing` 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 _Translator_ 리소스에 대한 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 이 값은 Azure의 _Translator_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **Translator** 개요 페이지, 레이블이 지정된 `Endpoint`

| 필수 | Name | 데이터 형식 | Description |
| -------- | ---- | --------- | ----------- |
| 예 | `Billing` | String | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](translator-how-to-install-container.md#required-elements)을 참조하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared HTTP proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너에 대해 자세히 알아보기](../../cognitive-services-container-support.md)
