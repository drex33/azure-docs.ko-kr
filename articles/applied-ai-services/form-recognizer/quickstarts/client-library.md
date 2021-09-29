---
title: '빠른 시작: Form Recognizer 클라이언트 라이브러리 또는 REST API'
titleSuffix: Azure Applied AI Services
description: Form Recognizer 클라이언트 라이브러리 또는 REST API를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 양식 처리, 자동화된 데이터 처리
ms.openlocfilehash: a1b355bdd67d9bf84a2c9ae24fb557a9e50fa1d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652521"
---
# <a name="quickstart-get-started-with-the-client-library-sdks-or-rest-api"></a>빠른 시작: 클라이언트 라이브러리 SDK 또는 REST API 시작

선택한 프로그래밍 언어를 사용하여 Azure Form Recognizer를 시작합니다. Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 [Azure Applied AI Services](../../../applied-ai-services/index.yml)입니다. 양식 문서에서 텍스트, 키/값 쌍, 선택 표시 및 테이블 데이터 등을 식별하고 추출합니다. &mdash; 서비스는 원본 파일의 관계를 포함하는 구조화된 데이터를 출력합니다. REST API 또는 SDK를 통해 Form Recognizer를 사용할 수 있습니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

다음 API를 사용하여 양식 및 문서에서 정형 데이터를 추출합니다.

* [클라이언트 인증](#authenticate-the-client)
* [레이아웃 분석](#analyze-layout)
* [영수증 분석](#analyze-receipts)
* [명함 분석](#analyze-business-cards)
* [송장 분석](#analyze-invoices)
* [ID 문서 분석](#analyze-identity-documents)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end