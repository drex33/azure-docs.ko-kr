---
title: Azure AD 사용자 흐름의 사용자 언어 지정
description: Azure Active Directory의 사용자 흐름에서 언어 환경을 사용자 지정하는 방법을 알아봅니다.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf645226de725d19af859118c43c336e5f542804
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067104"
---
# <a name="language-customization-in-azure-active-directory"></a>Azure Active Directory의 언어 사용자 지정

Azure AD(Azure Active Directory)의 사용자 언어 지정을 사용하면 사용자 요구에 적합하도록 사용자 흐름을 다른 언어로 맞출 수 있습니다. Microsoft는 [36개 언어](#supported-languages)에 대한 번역을 제공합니다. 단일 언어로만 환경이 제공되더라도 특성 모음 페이지에서 특성 이름을 사용자 지정할 수 있습니다.

## <a name="how-language-customization-works"></a>언어 사용자 지정이 작동하는 방식

기본적으로 사용자 언어 지정은 일관된 등록 환경을 보장하기 위해 등록하는 사용자에 대해 사용하도록 설정됩니다. 등록하는 동안 언어를 사용하여 특성 모음 프로세스의 일부로 사용자에게 표시되는 문자열을 수정할 수 있습니다.

> [!NOTE]
> 사용자 지정 사용자 특성을 사용하는 경우 사용자 고유의 번역을 제공해야 합니다. 자세한 내용은 [문자열 사용자 지정](#customize-your-strings)을 참조하세요.

## <a name="customize-your-strings"></a>문자열 사용자 지정

언어 사용자 지정을 통해 사용자 흐름의 모든 문자열을 사용자 지정할 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **사용자 흐름** 을 선택합니다.
3. 번역에 사용할 사용자 흐름을 선택합니다.
4. **언어** 를 선택합니다.
5. 사용자 흐름에 대한 **언어** 페이지에서 사용자 지정하려는 언어를 선택합니다.
6. **특성 모음 페이지** 를 확장합니다.
7. **기본값 다운로드**(또는 이전에 이 언어를 편집한 경우 **재정의 다운로드**)를 선택합니다.

이러한 단계는 문자열 편집을 시작하는 데 사용할 수 있는 JSON 파일을 제공합니다.

### <a name="change-any-string-on-the-page"></a>페이지에서 문자열 변경

1. 이전 지침에서 다운로드한 JSON 파일을 JSON 편집기에서 엽니다.
1. 변경할 요소를 찾습니다. 찾으려는 문자열의 `StringId`를 찾거나 변경할 `Value` 특성을 찾을 수 있습니다.
1. `Value` 특성을 표시하기를 원하는 항목으로 업데이트합니다.
1. 변경할 모든 문자열의 `Override`를 `true`로 변경합니다.
1. 파일을 저장하고 변경 내용을 업로드합니다. (JSON 파일을 다운로드한 곳과 동일한 위치에서 업로드 컨트롤을 찾을 수 있습니다.)

> [!IMPORTANT]
> 문자열을 재정의해야 하는 경우 `Override` 값을 `true`로 설정해야 합니다. 값이 변경되지 않으면 항목이 무시됩니다.

### <a name="change-extension-attributes"></a>확장 특성 변경

사용자 지정 사용자 특성의 문자열을 변경하려는 경우 또는 JSON에 문자열을 추가하려는 경우 다음 형식을 사용합니다.

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>`를 사용자 지정 사용자 특성 이름으로 바꿉니다.

`<ExtensionAttributeValue>`를 표시할 새 문자열로 바꿉니다.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections를 사용하여 값 목록을 제공합니다.

응답에 대한 값 목록 집합을 제공하려면 `LocalizedCollections` 특성을 만들어야 합니다. `LocalizedCollections`는 `Name` 및 `Value` 쌍의 배열입니다. 항목의 순서대로 항목이 표시됩니다. `LocalizedCollections`를 추가하려면 다음 형식을 사용하세요.

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`는 이 `LocalizedCollections` 특성이 응답인 사용자 특성입니다.
* `Name`은 사용자에게 표시되는 값입니다.
* `Value`는 이 옵션이 선택될 때 클레임에 반환된 항목입니다.

### <a name="upload-your-changes"></a>변경 내용 업로드

1. JSON 파일 변경을 완료한 후 테넌트로 돌아갑니다.
1. **사용자 흐름** 을 선택하고 번역에 사용할 사용자 흐름을 클릭합니다.
1. **언어** 를 선택합니다.
1. 번역하려는 언어를 선택합니다.
1. **특성 모음 페이지** 를 선택합니다.
1. 폴더 아이콘을 선택하고 업로드할 JSON 파일을 선택합니다.

변경 내용이 자동으로 사용자 흐름에 저장됩니다.

## <a name="additional-information"></a>추가 정보

### <a name="page-ui-customization-labels-as-overrides"></a>재정의로 페이지 UI 사용자 지정 레이블

언어 사용자 지정을 사용하도록 설정하면 페이지 UI 사용자 지정을 사용한 레이블에 대한 이전 편집이 영어(en)용 JSON 파일에서 유지됩니다. 언어 사용자 지정에서 언어 리소스를 업로드하여 레이블 및 기타 문자열을 계속해서 변경할 수 있습니다.

### <a name="up-to-date-translations"></a>최신 번역

Microsoft는 사용자가 사용할 수 있는 가장 최신의 번역을 제공하기 위해 최선을 다하고 있습니다. Microsoft는 지속적으로 번역을 개선하고 사용자에 맞게 유지할 예정입니다. Microsoft는 버그 및 글로벌 용어의 변화를 파악하고 사용자 흐름에서 원활하게 작동하도록 업데이트할 예정입니다.

### <a name="support-for-right-to-left-languages"></a>오른쪽에서 왼쪽 언어 지원

현재 Microsoft는 오른쪽에서 왼쪽 언어에 대한 지원을 제공하지 않습니다. 사용자 지정 로캘을 사용하고 CSS를 사용하여 문자열이 표시되는 방식을 변경하여 지원할 수 있습니다. 이 기능이 필요한 경우 [Azure 피드백](https://feedback.azure.com/d365community/idea/10a7e89c-c325-ec11-b6e6-000d3a4f0789)에서 이 기능에 투표해 주세요.

### <a name="social-identity-provider-translations"></a>소셜 ID 공급자 변환

Microsoft는 소셜 로그인에 대한 `ui_locales` OIDC 매개 변수를 제공합니다. 하지만 Facebook과 Google을 포함한 일부 소셜 ID 공급자는 이러한 매개 변수를 인식하지 않습니다.

### <a name="browser-behavior"></a>브라우저 동작

Chrome과 Firefox는 설정된 언어를 요청합니다. 이 언어가 지원되는 언어이면 기본 언어보다 먼저 표시됩니다. Microsoft Edge에서는 현재 언어를 요청하지 않으며 기본 언어로 바로 이동합니다.

## <a name="supported-languages"></a>지원되는 언어

Azure AD에는 다음 언어에 대한 지원이 포함되어 있습니다. 사용자 흐름 언어는 Azure AD에서 제공합니다. MFA(다단계 인증) 알림 언어는 [Azure AD MFA](../authentication/concept-mfa-howitworks.md)에서 제공합니다.

| 언어              | 언어 코드 | 사용자 흐름         | MFA 알림  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 아랍어                | ar            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 불가리아어             | bg            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 벵골어                | bn            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 카탈로니아어               | ca            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 체코어                 | cs            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 덴마크어                | da            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 독일어                | de            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 그리스어                 | el            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 영어               | en            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 스페인어               | es            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 에스토니아어              | et            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 바스크어                | eu            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 핀란드어               | fi            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 프랑스어                | fr            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 갈리시아어              | gl            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 구자라트어              | gu            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 히브리어                | he            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 힌디어                 | hi            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 크로아티아어              | hr            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 헝가리어             | hu            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 인도네시아어            | id            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 이탈리아어               | it            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 일본어              | ja            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 카자흐어                | kk            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 칸나다어               | kn            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 한국어                | ko            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 리투아니아어            | lt            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 라트비아어               | lv            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 말라얄람어             | ml            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 마라티어               | mr            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 말레이어                 | ms            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 노르웨이어 복말      | nb            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 네덜란드어                 | nl            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 노르웨이어             | no            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 펀잡어               | pa            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 폴란드어                | pl            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 포르투갈어 - 브라질   | pt-br         | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 포르투갈어 - 포르투갈 | pt-pt         | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 루마니아어              | ro            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 러시아어               | ru            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 슬로바키아어                | sk            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 슬로베니아어             | sl            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 세르비아어 - 키릴 자모    | sr-cryl-cs    | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 세르비아어 - 라틴 문자       | sr-latn-cs    | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 스웨덴어               | sv            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 타밀어                 | ta            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 텔루구어                | te            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) |
| 태국어                  | th            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 터키어               | tr            | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 우크라이나어             | uk            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 베트남어            | vi            | ![아니요를 나타내는 X입니다.](./media/user-flow-customize-language/no.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 중국어 - 간체  | zh-hans       | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
| 중국어 - 번체 | zh-hant       | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) | ![녹색 확인 표시](./media/user-flow-customize-language/yes.png) |
