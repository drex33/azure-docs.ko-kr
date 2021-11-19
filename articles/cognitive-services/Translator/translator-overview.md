---
title: Microsoft Azure Cognitive Services Translator란?
titlesuffix: Azure Cognitive Services
description: Translator를 애플리케이션, 웹 사이트, 도구 및 기타 솔루션에 통합하여 다국어 사용자 환경을 제공합니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 11/16/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: 번역기, 텍스트 번역, 컴퓨터 번역, 번역 서비스, 사용자 지정 번역기
ms.openlocfilehash: 5c0d2553438f1765ff5f95d6264961728ee94675
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706570"
---
# <a name="what-is-translator"></a>Translator란?

:::image type="content" source="media/translator-icon.png" alt-text="Azure Portal의 Translator 아이콘":::

 Translator는 REST API의 [Azure Cognitive Services](../what-are-cognitive-services.md) 제품군에 속하는 클라우드 기반의 인공신경망 컴퓨터 번역 서비스입니다.  Translator는 모든 운영 체제에서 사용할 수 있고, 전세계의 무수한 기업에서 사용되는 많은 Microsoft 제품 및 서비스가 언어 번역 및 기타 언어 관련 작업을 수행할 수 있게 지원합니다. 이 개요에서는 모든 [지원되는 언어](./language-support.md)에서 Translator가 어떻게 인텔리전트 다국어 솔루션을 빌드할 수 있게 지원하는지 알아봅니다.

Translator 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [**빠른 시작**](quickstart-translator.md). 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](translator-how-to-signup.md). 더 구체적이거나 사용자 지정된 방식으로 서비스 액세스 및 사용 지침이 포함되어 있습니다.
* [**참조 문서**](reference/v3-0-reference.md). REST API 설명서 및 프로그래밍 언어 기반 콘텐츠입니다.

## <a name="translator-features-and-development-options"></a>Translator 기능 및 개발 옵션

Translator 서비스에서는 다음 기능이 지원됩니다. 이 표의 링크를 사용하여 각 기능에 대해 자세히 알아보고 API 참조를 탐색할 수 있습니다.

| 기능 | 설명 | 개발 옵션 |
|----------|-------------|--------------------------|
| [**텍스트 번역**](text-translation-overview.md) | 실시간으로 지원되는 소스와 대상 언어 간에 텍스트 번역을 실행합니다. | <ul><li>[**REST API**](reference/rest-api-guide.md) </li><li>[텍스트 번역 Docker 컨테이너 ](containers/translator-how-to-install-container.md) - 현재 제어된 미리 보기 상태입니다.</li></ul> |
| [**문서 번역**](document-translation/overview.md) | 원본 문서의 구조와 형식을 유지하면서 일괄 처리 및 복잡한 파일을 번역합니다. | <ul><li>[**REST API**](document-translation/reference/rest-api-guide.md)</li><li>[**클라이언트 라이브러리 SDK**](document-translation/client-sdks.md)</li></ul> |
| [**Custom Translator**](custom-translator/overview.md) | 도메인 및 산업별 언어, 용어 및 스타일을 번역하는 사용자 지정 모델을 빌드합니다. | <ul><li>[**Custom Translator 포털**](https://portal.customtranslator.azure.ai/)</li></ul> |

## <a name="try-the-translator-service-for-free"></a>무료로 Translator 서비스 사용해 보기

Microsoft 계정이 필요합니다. 없으면 [**Microsoft 계정 포털**](https://account.microsoft.com/account)에서 무료로 가입할 수 있습니다.  **Microsoft 계정 만들기** 를 선택하고 단계에 따라 새 계정을 만들고 확인합니다.

다음으로 Azure 계정이 필요합니다. [**Azure 가입 페이지**](https://azure.microsoft.com/free/ai/)로 이동하고 **무료로 시작** 단추를 선택한 다음, Microsoft 계정 자격 증명을 사용하여 새 Azure 계정을 만듭니다.

이제 시작할 준비가 되었습니다. [**Translator 서비스를 만들고**](translator-how-to-signup.md "Azure Portal로 이동합니다."), [**액세스 키와 API 엔드포인트를 구한 다음**](translator-how-to-signup.md#authentication-keys-and-endpoint-url "인증에는 엔드포인트 URL 및 읽기 전용 키가 필요합니다."), [**빠른 시작**](quickstart-translator.md "REST를 통해 Translator를 사용하는 방법을 알아봅니다.")을 사용해 보세요.

## <a name="next-steps"></a>다음 단계

* 다음 기능에 대한 자세한 정보:
  * [**텍스트 번역**](text-translation-overview.md)
  * [**문서 번역**](document-translation/overview.md)
  * [**Custom Translator**](custom-translator/overview.md)
* [**Translator 가격 책정**](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)을 검토합니다.
