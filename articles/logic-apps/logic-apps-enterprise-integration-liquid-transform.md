---
title: Liquid 템플릿을 통한 JSON 및 XML 변환
description: Azure Logic Apps에서 Liquid 템플릿을 맵으로 사용하여 JSON과 XML을 변환합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/25/2021
ms.openlocfilehash: 01b5e9930353078406751001300a1baa3c23cf00
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690557"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Azure Logic Apps에서 Liquid 템플릿을 맵으로 사용하여 JSON 및 XML 변환

논리 앱에서 기본 JSON 변환을 수행하려는 경우, **작성** 또는 **JSON 구문 분석과** 같은 네이티브 [데이터 작업](../logic-apps/logic-apps-perform-data-operations.md)을 사용할 수 있습니다. 반복, 제어 흐름 및 변수와 같은 요소가 있는 고급의 복잡한 JSON에서 JSON으로의 변환의 경우, [Liquid](https://shopify.github.io/liquid/) 오픈 소스 템플릿 언어를 사용하여 이러한 변환을 설명하는 템플릿을 생성하고 사용합니다. 다른 변환(예: [JSON에서](#other-transformations)텍스트로, XML에서 JSON으로, XML에서 텍스트로 변환)을 수행할 수도 있습니다.

논리 앱에서 Liquid 변환을 수행하려면, 먼저 원하는 매핑을 정의하는 Liquid 템플릿을 생성해야 합니다. 그런 다음 템플릿을 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)에 [맵으로 업로드](../logic-apps/logic-apps-enterprise-integration-maps.md)합니다. **JSON에서 JSON으로 변환 - Liquid** 작업을 논리 앱에 추가하면 사용할 작업에 대한 맵으로 Liquid 템플릿을 선택할 수 있습니다.

이 문서에서는 다음 작업을 완료하는 방법을 보여줍니다.

* Liquid 템플릿을 생성합니다.
* 통합 계정에 템플릿을 추가합니다.
* Liquid 변환 작업을 논리 앱에 추가합니다.
* 사용하려는 맵으로 템플릿을 선택합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [Liquid 템플릿 언어](https://shopify.github.io/liquid/)에 대한 기본 지식

  > [!NOTE]
  > **JSON을 JSON으로 변환 - Liquid** 작업은 [Liquid에 대한 DotLiquid 구현](https://github.com/dotliquid/dotliquid)을 따르며, [Liquid에 대한 Shopify 구현](https://shopify.github.io/liquid)과는 특정 경우에 다릅니다. 자세한 내용은 [Liquid 템플릿 고려 사항](#liquid-template-considerations)을 참조하세요.

## <a name="create-the-template"></a>템플릿 만들기

1. JSON 변환을 위한 맵으로 사용할 Liquid 템플릿을 생성합니다. 원하는 편집 도구를 사용할 수 있습니다.

   이 예시에서는 이 섹션에 설명된 샘플 Liquid 템플릿을 생성합니다.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. `.liquid` 확장을 사용하여 템플릿을 저장합니다. 이 예에서는 `SimpleJsonToJsonTemplate.liquid`를 사용합니다.

## <a name="upload-the-template"></a>템플릿 업로드

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 검색 상자에 `integration accounts`을 입력하고, **통합 계정** 을 선택합니다.

   ![“통합 계정” 찾기](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 통합 계정 찾기 및 선택.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. **개요** 창의 **구성 요소** 아래에서 **맵** 을 선택합니다.

    !["맵" 타일 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. **맵** 창에서 **추가** 를 선택하고 맵에 대한 세부 정보를 제공합니다.

   | 속성 | 값 | 설명 |
   |----------|-------|-------------|
   | **이름** | `JsonToJsonTemplate` | 맵의 이름이며, 이 예제에서는 "JsonToJsonTemplate"입니다. |
   | **맵 유형** | **liquid** | 맵의 형식입니다. JSON부터 JSON 변환의 경우 **Liquid** 를 선택해야 합니다. |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | 변환에 사용할 기존 Liquid 템플릿이나 맵 파일이며 이 예제에서는 "SimpleJsonToJsonTemplate.liquid"입니다. 이 파일을 찾으려면 파일 선택기를 사용할 수 있습니다. 맵 크기 제한에 대해서는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)을 참조하세요. |
   |||

   ![Liquid 템플릿 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Liquid 변환 작업 추가

1. Azure Portal에서 다음 단계에 따라 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Logic Apps 디자이너에서 논리 앱에 [요청 트리거](../connectors/connectors-native-reqres.md#add-request)를 추가합니다.

1. 트리거 아래에서 **새 단계** 를 선택합니다. 검색 상자에서 `liquid`을 필터로 입력하고 **JSON을 JSON으로 변환-Liquid** 를 선택합니다

   ![Liquid 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. **맵** 목록을 열고 Liquid 템플릿을 선택합니다. 이 예시에서는 "JsonToJsonTemplate"입니다.

   ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   맵 목록이 비어 있는 경우, 논리 앱이 통합 계정에 연결되어 있지 않을 수 있습니다. 
   Liquid 템플릿 또는 지도를 포함한 통합 계정에 논리 앱을 연결하려면 다음 단계를 따릅니다.

   1. 논리 앱 메뉴에서 **워크플로 설정** 을 선택합니다.

   1. **통합 계정 선택** 목록에서 통합 계정을 선택하고 **저장** 을 선택합니다.

      ![통합 계정에 논리 앱 연결](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 이제 이 작업에 **Content** 속성을 추가합니다. **새 매개 변수 추가** 목록을 열고 **Content** 를 선택합니다.

   ![작업에 “Content” 속성 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. **Content** 속성 값을 설정하려면 **Content** 상자 내부를 클릭하여 동적 콘텐츠 목록이 표시되도록 합니다. 트리거의 본문 내용 출력을 나타내는 **Body** 토큰을 선택합니다.

   !["Content" 속성 값의 "Body" 토큰을 선택합니다](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   완료되면 작업은 다음 예제와 같습니다.

   ![완료된 "Json을 JSON으로 변환" 작업](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>논리 앱 테스트

1. [Postman](https://www.getpostman.com/postman)이나 유사한 도구와 `POST` 메서드를 사용하여 요청 트리거의 URL에 대한 호출을 보내고 변환할 JSON 입력을 포함합니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "devices": "Surface, Windows Phone, Desktop computer, Monitors",
      "firstName": "Dean",
      "lastName": "Ledet",
      "phone": "(111)5551111"
   }
   ```

1. 워크플로 실행이 완료되면 워크플로의 실행 기록으로 이동하여 **JSON을 JSON으로 변환** 작업의 입력과 출력을 검사합니다. 예를 들면 다음과 같습니다.

   ![예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Liquid 템플릿 고려 사항

* Liquid 템플릿은 Azure Logic Apps의 [맵 파일 크기 제한](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)을 따릅니다.

* **Json에서 json으로 변환-Liquid** 작업은 [Liquid에 대한 DotLiquid 구현](https://github.com/dotliquid/dotliquid)을 따릅니다. 이 구현은 [Liquid용 Shopify 구현](https://shopify.github.io/liquid/)에서의 .NET Framework 포트이며 [특정 사례](https://github.com/dotliquid/dotliquid/issues)에서 달라집니다.

  알려진 차이점은 다음과 같습니다.

  * **Json 을 json으로 변환-Liquid** 작업은 JSON, XML, HTML 등을 포함할 수 있는 문자열을 기본적으로 출력합니다. Liquid 작업은 Liquid 템플릿의 예상 텍스트 출력이 JSON 문자열임을 나타냅니다. 작업은 입력을 JSON 개체로 구문 분석하고, Liquid에서 JSON 구조를 해석할 수 있도록 래퍼를 적용하도록 논리 앱에 지시합니다. 변환 후, 작업은 텍스트 출력을 Liquid에서 JSON으로 구문 분석하도록 논리 앱에 지시합니다.

    DotLiquid은 기본적으로 JSON을 인식하지 않으므로 백슬래시 문자(`\`)와 기타 예약된 JSON 문자를 이스케이프해야 합니다.

  * 템플릿이 [Liquid 필터](https://shopify.github.io/liquid/basics/introduction/#filters)를 사용하는 경우, [DotLiquid 및 C# 명명 규칙](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)을 준수해야 합니다. 이 규칙은 *문장의 첫 글자를 대문자로 표기* 합니다. 모든 Liquid 변환에 대해 템플릿의 필터 이름이 문장의 첫 글자를 대문자로 표기하는지 확인합니다. 그렇지 않으면 필터가 일치하지 않습니다.

    예를 들어, `replace` 필터를 사용하는 경우 `replace`이 아닌 `Replace`를 사용합니다. [DotLiquid online](http://dotliquidmarkup.org/try-online)에서 예시를 사용해 보는 경우에도 동일한 규칙이 적용됩니다. 자세한 내용은 [Shopify Liquid 필터](https://shopify.dev/docs/themes/liquid/reference/filters) 및 [DotLiquid Liquid 필터](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)를 참조하세요. Shopify 사양에는 각 필터에 대한 예시가 포함되어 있으므로, 비교를 위해 [DotLiquid - 온라인 사용해 보기](http://dotliquidmarkup.org/try-online)에서 이러한 예시를 사용해 볼 수 있습니다.

  * Shopify 확장 필터의 `json` 필터는 현재 [DotLiquid에서 구현되지 않습니다](https://github.com/dotliquid/dotliquid/issues/384). 일반적으로 이 필터를 사용하여 JSON 문자열 구문 분석용 텍스트 출력을 준비할 수 있지만, 대신 `Replace` 필터를 사용해야 합니다.

  * [DotLiquid 구현의](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) 표준 `Replace` 필터는 [정규식(RegEx) 일치](/dotnet/standard/base-types/regular-expression-language-quick-reference)를 사용하는 반면, [Shopify 구현은](https://shopify.github.io/liquid/filters/replace/) [간단한 문자열 일치](https://github.com/Shopify/liquid/issues/202)를 사용합니다. 두 구현 모두 RegEx 예약 문자 또는 match 매개 변수에 이스케이프 문자를 사용할 때까지 동일한 방식으로 작동하는 것처럼 보입니다.

    예를 들어 RegEx 예약 백슬래시(`\`) 이스케이프 문자를 이스케이프하려면 `| Replace: '\', '\\'`이 아니라 `| Replace: '\\', '\\'`를 사용합니다. 다음 예시는 백슬래시 문자를 이스케이프하려고 할 때 `Replace` 필터가 어떻게 다르게 동작하는지 보여줍니다. 이 버전이 성공적으로 작동하는 동안:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    이 결과로:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    이 버전은 실패합니다:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    이 오류와 함께:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    자세한 내용은 [RegEx 패턴 일치를 사용하는 표준 필터 바꾸기](https://github.com/dotliquid/dotliquid/issues/385)를 참조하세요.

  * [DotLiquid 구현](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326)의 `Sort` 필터는 배열 또는 컬렉션의 항목을 속성별로 정렬하지만 다음과 같은 차이점이 있습니다.<p>

    * [Shopify의 sort 동작](https://shopify.github.io/liquid/filters/sort/)이 아닌 [Shopify의 sort_natural 동작](https://shopify.github.io/liquid/filters/sort_natural/)을 따릅니다.

    * 문자열-알파뉴메릭 순서로만 정렬합니다. 자세한 내용은 [숫자 정렬](https://github.com/Shopify/liquid/issues/980)을 참조하십시오.

    * *대/소문자를 구분하지 않는* 순서를 사용합니다. 자세한 내용은 [정렬 필터가 Shopify 사양의 대/소문자 동작을 따르지 않음]( https://github.com/dotliquid/dotliquid/issues/393)을 참조하세요.

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Liquid를 사용하는 기타 변환

Liquid는 JSON 변환으로만 제한되지 않습니다. Liquid를 사용하여 다른 변환을 수행할 수도 있습니다. 예를 들면 다음과 같습니다.

* [JSON에서 텍스트로](#json-text)
* [XML에서 JSON으로](#xml-json)
* [XML에서 텍스트로](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON을 텍스트로 변환

이 예시에 사용되는 Liquid 템플릿은 다음과 같습니다.

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

샘플 입력 및 출력은 다음과 같습니다.

![JSON에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML에서 JSON으로 변환

이 예시에 사용되는 Liquid 템플릿은 다음과 같습니다.

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor` 루프는 후행 쉼표를 사용하지 않는 JSON 페이로드를 생성할 수 있도록 해주는 XML 입력에 대한 사용자 지정 반복 메커니즘입니다. 또한 사용자 지정 반복 메커니즘의 `where` 조건은 다른 Liquid 필터와 같은 요소의 값이 아닌 비교를 위해 XML 요소 이름을 사용합니다. 자세한 내용은 [집합체 정책에 대한 심층 정보 - 모음](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)을 참조하세요.

샘플 입력 및 출력은 다음과 같습니다.

![XML에서 JSON으로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML에서 텍스트로 변환

이 예시에 사용되는 Liquid 템플릿은 다음과 같습니다.

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

샘플 입력 및 출력은 다음과 같습니다.

![XML에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>다음 단계

* [Shopify Liquid 언어 및 예시](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-온라인 사용해 보기](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub 문제](https://github.com/dotliquid/dotliquid/issues/)
* [맵](../logic-apps/logic-apps-enterprise-integration-maps.md)에 대해 자세히 알아보기
