---
title: 양식 인식기 클라이언트 라이브러리 Sdk 또는 REST API 사용
titleSuffix: Azure Applied AI Services
description: 양식 인식기 클라이언트 라이브러리 SDK 또는 REST API를 사용 하 여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출 하는 폼 처리 앱을 만듭니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0402041121e34902d9801307dad8a759f8fe9516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053047"
---
# <a name="use-form-recognizer-sdks-or-rest-api"></a>양식 인식기 Sdk 또는 REST API 사용

 이 방법 가이드에서는 SDK를 사용 하 여 응용 프로그램 및 워크플로에 양식 인식기를 추가 하는 방법, 사용자가 선택한 프로그래밍 언어 또는 REST API 하는 방법에 대해 알아봅니다. Azure Form Recognizer는 기계 학습을 사용하여 문서에서 양식 필드, 텍스트 및 테이블을 추출하고 분석하는 클라우드 기반 Azure Applied AI Service입니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

다음 API를 사용하여 양식 및 문서에서 정형 데이터를 추출합니다.

* [클라이언트 인증](#authenticate-the-client)
* [레이아웃 분석](#analyze-layout)
* [영수증 분석](#analyze-receipts)
* [명함 분석](#analyze-business-cards)
* [송장 분석](#analyze-invoices)
* [ID 문서 분석](#analyze-id-documents)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/how-to-guides/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/how-to-guides/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/how-to-guides/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/how-to-guides/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/how-to-guides/rest-api.md)]

::: zone-end
