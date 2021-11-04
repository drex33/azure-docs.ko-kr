---
title: 상태 컨테이너에 대 한 Text Analytics 구성
titleSuffix: Azure Cognitive Services
description: 상태 컨테이너의 Text Analytics는 컨테이너에 대 한 저장소, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성 하 고 관리할 수 있도록 공통 구성 프레임 워크를 사용 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 801deee67503a7f7010e3c986b2ab1cf818c9f51
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102925"
---
# <a name="configure-text-analytics-for-health-docker-containers"></a>상태 docker 컨테이너에 대 한 Text Analytics 구성

상태에 대 한 Text Analytics는 컨테이너에 대 한 저장소, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성 하 고 관리할 수 있도록 각 컨테이너에 공통 구성 프레임 워크를 제공 합니다. 몇 가지 [docker 실행 명령 예](use-containers.md#run-the-container-with-docker-run)도 사용할 수 있습니다.

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](use-containers.md#billing)를 참조하세요.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey에 대 한 값을 지정 해야 하며 값은 구성 설정에 지정 된 _언어_ 리소스에 대해 유효한 키 여야 합니다 [`Billing`](#billing-configuration-setting) .

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **언어** 리소스 관리, **키 및 끝점**

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing`설정은 Azure에서 컨테이너에 대 한 청구 정보를 측정 하는 데 사용 되는 _언어_ 리소스의 끝점 URI를 지정 합니다. 이 구성 설정의 값을 지정 해야 하며,이 값은 Azure의 _언어_ 리소스에 대 한 올바른 끝점 URI 여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

이 설정은 다음 위치에서 찾을 수 있습니다.

* Azure Portal: **언어** 개요, 레이블 `Endpoint`

|필수| Name | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 엔드포인트 URI입니다. 청구 URI를 얻는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](use-containers.md#gathering-required-parameters)을 참조하세요. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../../../cognitive-services-custom-subdomains.md)을 참조하세요. |

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

상태 컨테이너에 대 한 Text Analytics는 입력 또는 출력 탑재를 사용 하 여 학습 또는 서비스 데이터를 저장 하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 [호스트 컴퓨터](use-containers.md#host-computer-requirements-and-recommendations)의 탑재 위치에는 Docker 서비스 계정에서 사용되는 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 액세스할 수 없습니다. 

|Optional| Name | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | String | 상태 컨테이너의 Text Analytics는이를 사용 하지 않습니다.|
|선택| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](use-containers.md)을 리뷰합니다.
* 추가적인 [Cognitive Services 컨테이너](../../../cognitive-services-container-support.md) 사용
