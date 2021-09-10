---
title: Form Recognizer 컨테이너 구성
titleSuffix: Azure Applied AI Services
description: 양식 및 테이블 데이터를 구문 분석하도록 Form Recognizer 컨테이너를 구성하는 방법을 알아봅니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
ms.openlocfilehash: a7a42f9a2078a3384949704a6319c8acbedcb17d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567993"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer 컨테이너 구성

> [!IMPORTANT]
>
> Form Recognizer 컨테이너는 게이트 미리 보기 상태입니다. 이 컨테이너를 사용하려면 [온라인 요청](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)을 제출하고 승인을 받아야 합니다. 자세한 내용은 아래 [**컨테이너를 실행하도록 승인 요청**](form-recognizer-container-install-run.md#request-approval-to-run-the-container)을 참조하세요.

Azure Form Recognizer 컨테이너를 사용하여 강력한 클라우드 기능과 에지 지역성을 모두 활용하도록 최적화된 애플리케이션 아키텍처를 빌드할 수 있습니다. 컨테이너는 온프레미스 및 클라우드에 쉽게 배포할 수 있는 최소한의 격리에 가까운 환경을 제공합니다. 이 문서에서는 `docker compose` 명령 인수를 사용하여 Form Recognizer 컨테이너 런타임 환경을 구성하는 방법을 알아봅니다. Form Recognizer 기능은 6개 Form Recognizer 기능 컨테이너인 **레이아웃**, **명함**,**ID 문서**,  **영수증**, **청구서**, **사용자 지정** 에서 지원됩니다. 이러한 컨테이너에는 여러 필수 설정 및 몇 가지 선택적 설정이 있습니다. 몇 가지 예는 [docker-compose.yml 파일 예](#example-docker-composeyml-file) 섹션을 참조하세요.

## <a name="configuration-settings"></a>구성 설정

각 컨테이너에는 다음 구성 설정이 있습니다.

|필수|설정|용도|
|--|--|--|
|예|[ApiKey](#apikey-and-billing-configuration-setting)|청구 정보를 추적합니다.|
|예|[Billing](#apikey-and-billing-configuration-setting)|Azure에서 서비스 리소스의 엔드포인트 URI를 지정합니다. 획득에 대한 자세한 내용은 [청구]](form-recognizer-container-install-run.md#billing)을 _참조하세요_. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../../../cognitive-services/cognitive-services-custom-subdomains.md)을 _참조하세요_.|
|예|[Eula](#eula-setting)| 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.|
|예|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](/azure/application-insights) 원격 분석 지원을 컨테이너에 추가할 수 있습니다.|
|예|[Fluentd](#fluentd-settings)|로그 및 메트릭 데이터(선택 사항)를 Fluentd 서버에 씁니다.|
|예|HTTP 프록시|아웃바운드 요청을 만들기 위한 HTTP 프록시를 구성합니다.|
|예|[Logging](#logging-settings)|컨테이너에 대한 ASP.NET Core 로깅 지원을 제공합니다. |

> [!IMPORTANT]
> [`ApiKey`](#apikey-and-billing-configuration-setting), [`Billing`](#apikey-and-billing-configuration-setting), [`Eula`](#eula-setting) 설정은 함께 사용됩니다. 세 가지 설정 모두에 대해 유효한 값을 제공해야 합니다. 그러지 않으면 컨테이너가 시작되지 않습니다. 이러한 구성 설정을 사용하여 컨테이너를 인스턴스화하는 방법에 대한 자세한 내용은 [청구](form-recognizer-container-install-run.md#billing)를 참조하세요.

## <a name="apikey-and-billing-configuration-setting"></a>ApiKey 및 청구 구성 설정

`ApiKey` 설정은 컨테이너에 대한 청구 정보를 추적하는 데 사용되는 Azure 리소스 키를 지정합니다. ApiKey 값은 "청구 구성 설정" 섹션에 있는 `Billing`에 지정된 리소스에 대한 유효한 키여야 합니다.

`Billing` 설정은 컨테이너에 대한 청구 정보를 측정하는 데 사용되는 Azure의 리소스에 대한 엔드포인트 URI를 지정합니다. 이 구성 설정의 값은 Azure의 리소스에 대한 유효한 엔드포인트 URI여야 합니다. 컨테이너는 약 10 ~ 15분마다 사용량을 보고합니다.

 Azure Portal의 **키 및 엔드포인트** 페이지에서 이러한 설정을 찾을 수 있습니다.

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="스크린샷: Azure Portal 키 및 엔드포인트 페이지.":::

## <a name="eula-setting"></a>Eula 설정

[!INCLUDE [Container shared configuration eula settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="applicationinsights-setting"></a>ApplicationInsights 설정

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="fluentd-settings"></a>Fluentd 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 프록시 자격 증명 설정

[!INCLUDE [Container shared configuration fluentd settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>로깅 설정

[!INCLUDE [Container shared configuration logging settings](../../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="volume-settings"></a>볼륨 설정

[**볼륨**](https://docs.docker.com/storage/volumes/)을 사용하여 컨테이너에서 또는 컨테이너로 읽고 씁니다. 볼륨은 Docker 컨테이너에서 생성 및 사용되는 데이터를 유지하기 위한 기본 설정입니다. `volumes` 옵션을 포함하고 `type`(바인드), `source`(폴더 경로) 및 `target`(파일 경로 매개 변수)을 지정하여 입력 마운트 또는 출력 마운트를 지정할 수 있습니다.

Form Recognizer 컨테이너에는 입력 볼륨 및 출력 볼륨이 필요합니다. 입력 볼륨은 읽기 전용(`ro`)일 수 있으며 학습 및 채점에 사용할 데이터에 액세스하는 데 필요합니다. 출력 볼륨은 쓰기 가능해야 하며 모델 및 임시 데이터를 저장하는 데 사용됩니다.

호스트 볼륨 위치의 정확한 구문은 호스트 운영 체제에 따라 다릅니다. 또한 Docker 서비스 계정의 권한과 호스트 탑재 위치 권한 간의 충돌로 인해 [호스트 컴퓨터](form-recognizer-container-install-run.md#host-computer-requirements)의 볼륨 위치에 액세스하지 못할 수 있습니다.

## <a name="example-docker-composeyml-file"></a>docker-compose.yml 파일 예

**docker 구성** 방법은 세 단계로 구성됩니다.

 1. Dockerfile을 만듭니다.
 1. 격리된 환경에서 함께 실행할 수 있도록 **docker-compose.yml** 에 서비스를 정의합니다.
 1. `docker-compose up`을 실행하여 서비스를 시작하고 실행합니다.

### <a name="single-container-example"></a>단일 컨테이너 예

이 예에서는 레이아웃 컨테이너 인스턴스에 대한 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {FORM_RECOGNIZER_API_KEY} 값을 입력합니다.

#### <a name="layout-container"></a>**레이아웃 컨테이너**

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}

    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

### <a name="multiple-containers-example"></a>여러 컨테이너 예

#### <a name="receipt-and-ocr-read-containers"></a>**수신 및 OCR 읽기 컨테이너**

이 예에서는 수신 컨테이너에 대해 {FORM_RECOGNIZER_ENDPOINT_URI} 및 {FORM_RECOGNIZER_API_KEY} 값을 입력하고 Computer Vision 읽기 컨테이너에 대해 {COMPUTER_VISION_ENDPOINT_URI} 및 {COMPUTER_VISION_API_KEY} 값을 입력합니다.

```yml
version: "3"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: cognitiveservicespreview.azurecr.io/microsoft/cognitive-services-form-recognizer-receipt:2.1
    environment:
      - EULA=accept 
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept 
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [여러 컨테이너 및 docker 작성 명령 실행에 대한 자세한 정보](form-recognizer-container-install-run.md)
