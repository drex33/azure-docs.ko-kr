---
title: Microsoft Azure Cognitive Services 텍스트 번역이란?
titlesuffix: Azure Cognitive Services
description: 텍스트 번역 API를 애플리케이션, 웹 사이트, 도구 및 기타 솔루션에 통합하여 다국어 사용자 환경을 제공합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/09/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 번역기, 텍스트 번역, 컴퓨터 번역, 번역 서비스, 사용자 지정 번역기
ms.openlocfilehash: 68354706935d0dcfb29055a1eb5b0e273022d306
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787969"
---
# <a name="what-is-text-translation"></a>텍스트 번역이란?

 텍스트 번역은 인공신경망 컴퓨터 번역 기술을 사용하여 모든 [지원되는 언어](language-support.md)에서 신속하고 정확한 소스-대상 텍스트 번역을 실시간으로 제공할 수 있도록 하는 Translator 서비스의 클라우드 기반 REST API 기능입니다. 이 개요에서는 텍스트 번역 REST API를 통해 애플리케이션 및 워크플로에 대한 인텔리전트 솔루션을 빌드하는 방법을 살펴봅니다.

텍스트 번역 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [**빠른 시작**](quickstart-translator.md). 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](translator-how-to-signup.md). 더 구체적이거나 사용자 지정된 방식으로 서비스 액세스 및 사용 지침이 포함되어 있습니다.
* [**참조 문서**](reference/v3-0-reference.md). REST API 설명서 및 프로그래밍 언어 기반 콘텐츠입니다.

## <a name="text-translation-features"></a>텍스트 번역 기능

 텍스트 번역 기능에서 지원되는 메서드는 다음과 같습니다.

* [**언어**](reference/v3-0-languages.md). **번역**, **음역** 및 **사전 조회** 작업에서 지원되는 언어의 목록을 반환합니다. 이 요청에는 인증이 필요하지 않으며 다음 GET 요청을 복사하여 Postman, 자주 사용하는 API 도구 또는 브라우저에 붙여 넣기만 하면 됩니다.

    ```http
    https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
    ```

* [**번역**](reference/v3-0-translate.md#translate-to-multiple-languages). 한 요청에서 단일 소스 언어 텍스트를 여러 대상 언어 텍스트로 렌더링합니다.

* [**음역**](reference/v3-0-transliterate.md). 소스 언어의 문자 또는 글을 대상 언어의 해당 문자 또는 글로 변환합니다.

* [**검색**](reference/v3-0-detect.md). 소스 코드 언어 코드와, 검색된 언어에 대해 텍스트 번역 및 음역이 지원되는지 여부를 표시하는 부울 변수를 반환합니다.

    > [!NOTE]
    > [단일 REST API 호출](reference/v3-0-translate.md#translate-a-single-input-with-language-autodetection)로 텍스트를 **번역, 음역, 검색** 할 수 있습니다.

* [**사전 조회**](reference/v3-0-dictionary-lookup.md). 소스 용어에 해당하는 단어를 대상 언어로 반환합니다.
* [**사전 예제**](reference/v3-0-dictionary-examples.md) 소스 용어와 대상 용어 쌍의 문법 구조와 컨텍스트 예제를 반환합니다.

## <a name="text-translation-deployment-options"></a>텍스트 번역 배포 옵션

다음 리소스를 사용하여 프로젝트 및 애플리케이션에 텍스트 번역을 추가합니다.

* Azure에서 사용할 수 있는 [**REST API**](reference/rest-api-guide.md)를 통해 클라우드 기반 번역기 서비스에 액세스합니다.

* [**텍스트 번역 Docker 컨테이너**](containers/translator-how-to-install-container.md)를 사용하여 REST API [번역 요청](containers/translator-container-supported-parameters.md)을 사용합니다.

    > [!IMPORTANT]
    >
    > * 번역기 컨테이너는 제어된 미리 보기 상태입니다. 사용하려면 [**제어된 서비스에 대한 Azure Cognitive Services 애플리케이션**](https://aka.ms/csgate-translator) 온라인 요청 양식을 입력 및 제출하여 컨테이너 액세스 권한을 받아야 합니다.
    >
    > * [**Translator 컨테이너 이미지**](https://hub.docker.com/_/microsoft-azure-cognitive-services-translator-text-translation)는 클라우드 제품에 비해 제한된 기능을 지원합니다.
    >

## <a name="get-started-with-text-translation"></a>텍스트 번역 시작

시작할 준비가 되셨나요?

* Azure Portal에서 [**Translator 리소스**](translator-how-to-signup.md "Azure Portal로 이동합니다.")를 만듭니다.

* [**액세스 키와 API 엔드포인트를 가져옵니다**](translator-how-to-signup.md#authentication-keys-and-endpoint-url). 인증에는 엔드포인트 URL 및 읽기 전용 키가 필요합니다.

* 다음 프로그래밍 언어에 대한 [**빠른 시작**](quickstart-translator.md "REST 및 선호하는 프로그래밍 언어를 통해 번역기를 사용하는 방법을 알아봅니다.")을 살펴보고 사용 사례 및 코드 샘플을 확인하세요. 
  * [**C#/.NET**](quickstart-translator.md?tabs=csharp)
  * [**Go**](quickstart-translator.md?tabs=go)
  * [**Java**](quickstart-translator.md?tabs=java)
  * [**JavaScript/Node.js**](quickstart-translator.md?tabs=nodejs)
  * [**Python**](quickstart-translator.md?tabs=python)

## <a name="next-steps"></a>다음 단계

텍스트 번역 REST API에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [REST API 참조 참고](./reference/v3-0-reference.md)
