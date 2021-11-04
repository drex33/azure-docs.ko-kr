---
title: 컨테이너 구성 - 언어 서비스
titleSuffix: Azure Cognitive Services
description: 언어 서비스는 각 컨테이너에 공통 구성 프레임워크를 제공하므로 컨테이너에 대한 스토리지, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성하고 관리할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: ignite-fall-2021
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.openlocfilehash: 43beab44e3e56a43f9efd57bd02af2ae8d487c0f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052869"
---
# <a name="configure-language-service-docker-containers"></a>언어 서비스 Docker 컨테이너 구성

언어 서비스는 각 컨테이너에 공통 구성 프레임워크를 제공하므로 컨테이너에 대한 스토리지, 로깅 및 원격 분석 및 보안 설정을 쉽게 구성하고 관리할 수 있습니다. 이 문서는 다음 컨테이너에 적용됩니다.

* 감정 분석
* 언어 감지
* 핵심 구 추출
* Text Analytics for health 

## <a name="configuration-settings"></a>구성 설정

[!INCLUDE [Container shared configuration settings table](../../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) 및 [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 이 세 가지 설정 모두에 대해 유효한 값을 제공해야 하며, 제공하지 않을 경우 컨테이너는 시작되지 않습니다.

## <a name="apikey-configuration-setting"></a>ApiKey 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. 키 값을 지정해야 하며 구성 설정에 지정된 _언어_ 리소스에 유효한 키여야 [`Billing`](#billing-configuration-setting) 합니다.

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>청구 구성 설정

`Billing`설정은 컨테이너에 대한 청구 정보를 계량하는 데 사용되는 Azure의 _언어_ 리소스에 대한 엔드포인트 URI를 지정합니다. 이 구성 설정에 대한 값을 지정해야 하며, 값은 Azure의 _언어_ 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

|필수| Name | 데이터 형식 | Description |
|--|------|-----------|-------------|
|예| `Billing` | String | 청구 엔드포인트 URI입니다. |


## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration proxy settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정
 
[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>탑재 설정

바인딩 탑재를 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. [Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령의 `--mount`옵션을 지정하여 입력 탑재 또는 출력 탑재를 지정할 수 있습니다.

언어 서비스 컨테이너는 입력 또는 출력 탑재를 사용하여 학습 또는 서비스 데이터를 저장하지 않습니다. 

호스트 탑재 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정에서 사용하는 사용 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 호스트 컴퓨터의 탑재 위치에 액세스할 수 없습니다. 

|선택 사항| Name | 데이터 형식 | Description |
|-------|------|-----------|-------------|
|허용되지 않음| `Input` | String | 언어 서비스 컨테이너는 이를 사용하지 않습니다.|
|선택| `Output` | 문자열 | 출력 탑재의 대상입니다. 기본값은 `/output`입니다. 로그의 위치입니다. 컨테이너 로그가 포함됩니다. <br><br>예제:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>다음 단계

* 추가적인 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
