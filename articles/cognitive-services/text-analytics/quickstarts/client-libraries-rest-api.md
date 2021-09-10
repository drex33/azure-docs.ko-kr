---
title: '빠른 시작: Text Analytics 클라이언트 라이브러리를 사용한 텍스트 마이닝'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 통해 Azure Cognitive Services에서 Text Analytics API를 사용하여 감정 분석 등을 수행합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/25/2021
ms.author: aahi
keywords: 텍스트 마이닝, 감정 분석, 텍스트 분석
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 8eeac06c4db34a7aa2452ae9fda51890ac1b91a1
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864863"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>빠른 시작: Text Analytics 클라이언트 라이브러리 및 REST API 사용

이 문서를 사용하여 Text Analytics 클라이언트 라이브러리 및 REST API를 시작합니다. 다음 단계에 따라 텍스트 마이닝을 위한 코드 예를 사용해 봅니다.


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * 이 빠른 시작에서는 v3.1 및 v3.2-preview API 버전만 다룹니다.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.
> * 클라이언트 라이브러리의 최신 미리 보기 버전을 사용하여 추출 요약을 사용할 수도 있습니다. [GitHub에서](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeExtractiveSummarization.java) 다음 샘플을 참조하세요.


[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.
> * [브라우저에서](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md) 이 버전의 Text Analytics 클라이언트 라이브러리를 실행할 수도 있습니다.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * 이 빠른 시작에서는 v3.1 및 v3.2-preview API 버전만 다룹니다.

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [솔루션 살펴보기](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 감지](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
