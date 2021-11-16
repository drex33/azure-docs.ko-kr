---
title: 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: 사용자 지정 정책에서 UI 사용자 지정을 위한 페이지 레이아웃 버전 기록입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/22/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: ef18f8391962520daada2e7abf5677863d503a91
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036097"
---
# <a name="page-layout-versions"></a>페이지 레이아웃 버전

페이지 레이아웃 패키지는 페이지 요소의 수정 및 개선 사항을 포함하도록 주기적으로 업데이트됩니다. 다음 변경 로그는 각 버전에 도입된 변경 내용을 지정합니다.

> [!IMPORTANT]
> Azure Active Directory B2C는 새 페이지 레이아웃 버전마다 향상된 기능 및 수정 사항을 릴리스합니다. 모든 페이지 요소에 향상된 최신 보안 기능, 접근성 표준, 피드백이 반영되도록 페이지 레이아웃 버전을 최신 상태로 유지하는 것이 좋습니다.
>

## <a name="jquery-and-handlebars-versions"></a>jQuery 및 Handlebars 버전

Azure AD B2C 페이지 레이아웃은 다음 버전의 [jQuery 라이브러리](https://jquery.com/) 및 [Handlebars 템플릿](https://handlebarsjs.com/)을 사용합니다.

|요소 |페이지 레이아웃 버전 범위 |jQuery 버전  |Handlebars 런타임 버전 |Handlebars 컴파일러 버전 |
|---------|---------|------|--------|----------|
|다단계 |>= 1.2.4 | 3.5.1 | 4.7.6 |4.7.7 |
|            |< 1.2.4 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |
|selfasserted |>= 2.1.4 | 3.5.1 |4.7.6 |4.7.7 |
|            |< 2.1.4 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |
|unifiedssp |>= 2.1.4 | 3.5.1 |4.7.6 |4.7.7 |
|            |< 2.1.4 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |
|globalexception |>= 1.2.1 | 3.5.1 |4.7.6 |4.7.7 |
|            |< 1.2.1 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |
|providerselection |>= 1.2.1 | 3.5.1 |4.7.6 |4.7.7 |
|            |< 1.2.1 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |
|claimsconsent |>= 1.2.1 | 3.5.1 |4.7.6 |4.7.7 |
|            |< 1.2.1 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |
|unifiedssd |>= 1.2.1 | 3.5.1 |4.7.6 |4.7.7 |
|            |< 1.2.1 | 3.4.1 |4.0.12 |2.0.1 |
|            |< 1.2.0 | 1.12.4 |

## <a name="self-asserted-page-selfasserted"></a>자체 어설션 페이지(selfasserted)

**2.1.8**

- 클레임 이름은 사용자의 특성 입력 요소를 둘러싼 `<li>` HTML 요소의 `class` 특성에 추가됩니다. 클래스 이름을 사용하면 CSS 선택기를 만들어 특정 사용자 특성 입력 요소의 부모 `<li>`를 선택할 수 있습니다. 다음 HTML 태그는 등록 페이지에 대한 클래스 특성을 보여줍니다.
  
  ```html
  <div id="attributeList" class="attr">
    <ul>
      <li class="EmailBox email_li">...</li>
      <li class="Password newPassword_li">...</li>
      <li class="Password reenterPassword_li">...</li>
      <li class="TextBox displayName_li">...</li>
      <li class="TextBox givenName_li">...</li>
      <li class="TextBox surname_li">...</li>
      <li class="TextBox extension_age_li">...</li>
    </ul>
  </div>
  ```
**2.1.7**
- 요청이 실패하도록 하는 언어 인코딩 문제를 해결했습니다.
- 양식 제출 시에만 인라인 오류 메시지를 표시하는 접근성 버그를 수정했습니다.

**2.1.6**
- 다른 필드에 너무 빨리 입력할 경우 지워지는 암호 오류를 해결했습니다.

**2.1.5**
- 텍스트 중간에서 편집할 때 발생하는 iOS의 커서 점프 문제를 해결했습니다.

**2.1.4**
- jQuery 버전을 3.5.1로 업데이트했습니다.
- HandlebarJS 버전을 4.7.6으로 업데이트했습니다.

**2.1.3**
- 보안 수정 사항입니다.

**2.1.2**
- 스페인어 및 프랑스어와 같은 언어의 지역화 인코딩 문제를 해결했습니다.

**2.1.1**

- `intro` 이외에 페이지에 제목으로 표시할 UXString `heading`을 추가했습니다. 이 항목은 기본적으로 숨겨집니다.
- iCloud 키체인에 암호를 저장하기 위한 지원을 추가했습니다.
- 레이아웃(classic, oceanBlue 또는 slateGray)을 선택하기 위해 정책 또는 QueryString 매개 변수 `pageFlavor`페이지에 제목으로 표시하는 지원을 추가했습니다.
- 자체 어설션 페이지에 고지 사항을 추가했습니다.
- 이제 페이지가 로드될 때 편집 가능한 첫 번째 필드에 포커스가 배치됩니다.
- 이제 여러 필드에 오류가 있는 경우 첫 번째 오류 필드에 포커스가 배치됩니다.
- 이제 메일 확인 코드가 확인된 후 ‘변경’ 단추에 포커스가 배치됩니다.

**2.1.0**

- 지역화 및 접근성 수정 사항입니다.

**2.0.0**

- 사용자 지정 정책의 [표시 컨트롤](display-controls.md)에 대한 지원을 추가했습니다.

**1.2.0**

- 이제 사용자 이름/메일과 암호 필드에 `form` HTML 요소를 사용하면 Edge 및 IE(Internet Explorer)가 이 정보를 제대로 저장할 수 있습니다.
- 사용자 환경을 개선하기 위해 구성 가능한 사용자 입력 유효성 검사 지연을 추가했습니다.
-  접근성 수정
- 이제 내레이터가 오류 메시지를 읽도록 접근성 문제를 수정했습니다. 
- 이제 메일이 확인된 후 암호 필드에 포커스가 배치됩니다.
- 확인란 컨트롤에서 `autofocus`를 제거했습니다. 
- 전화 번호 확인을 위해 표시 컨트롤에 대한 지원을 추가했습니다.
- 이제 [HTML 태그](customize-ui-with-html.md#guidelines-for-using-custom-page-content)에 `data-preload="true"` 특성을 추가할 수 있습니다.
  - 연결된 CSS 파일을 HTML 템플릿과 동시에 로드하므로 파일 로드 사이에 ‘깜박임’이 발생하지 않습니다.
  - 페이지가 로드되기 전에 `script` 태그를 가져오고 실행하는 순서를 제어합니다.
- 이제 메일 필드는 `type=email`이며 모바일 키보드는 올바른 제안을 제공합니다.
- Chrome 번역에 대한 지원입니다.
- 사용자 흐름 페이지에서 회사 브랜딩에 대한 지원을 추가했습니다.

**1.1.0**

- 경고 취소를 제거했습니다.
- 오류 요소에 대한 CSS 클래스
- 오류 논리 표시/숨기기가 향상되었습니다.
- 기본 CSS를 제거했습니다.

**1.0.0**

- 초기 릴리스

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>암호 재설정 링크를 사용하는 통합 로그인 등록 페이지(unifiedssp)

> [!TIP]
> 사용자 흐름에서 여러 로캘 또는 언어를 지원하기 위해 페이지를 지역화하는 경우입니다. [지역화 ID](localization-string-ids.md) 문서에서는 선택한 페이지 버전에 대해 사용할 수 있는 지역화 ID 목록을 제공합니다.

**2.1.5**
- idp 선택기 템플릿이 로그인 페이지에서 사용되는 경우 탭 순서의 문제를 해결했습니다.
- 로그인 링크 텍스트에서 인코딩 문제를 해결했습니다.

**2.1.4**
- jQuery 버전을 3.5.1로 업데이트했습니다.
- HandlebarJS 버전을 4.7.6으로 업데이트했습니다.

**2.1.3**
- 보안 수정 사항입니다.
- 사소한 버그가 수정되었습니다.

**2.1.2**
- 스페인어 및 프랑스어와 같은 언어의 지역화 인코딩 문제를 해결했습니다.
- “암호 찾기” 링크를 클레임 교환으로 사용할 수 있습니다. 자세한 내용은 [셀프 서비스 암호 재설정](add-password-reset-policy.md#self-service-password-reset-recommended)을 참조하세요.

**2.1.1**
- `intro` 이외에 페이지에 제목으로 표시할 UXString `heading`을 추가했습니다. 이 항목은 기본적으로 숨겨집니다.
- 레이아웃(classic, oceanBlue 또는 slateGray)을 선택하기 위해 정책 또는 QueryString 매개 변수 `pageFlavor`페이지에 제목으로 표시하는 지원을 추가했습니다.
- iCloud 키체인에 암호를 저장하기 위한 지원을 추가했습니다.
- 이제 여러 필드에 오류가 있는 경우 첫 번째 오류 필드에 포커스가 배치됩니다.
- 이제 페이지가 로드될 때 편집 가능한 첫 번째 필드에 포커스가 배치됩니다.
- 클레임 공급자 선택 링크 `bottomUnderFormClaimsProviderSelections`의 새 위치를 추가했습니다.
- 더 이상 사용되지 않는 UXString을 제거했습니다.

**2.1.0**

- 여러 등록 링크에 대한 지원을 추가했습니다.
- 정책에 정의된 조건자 규칙에 따라 사용자 입력 유효성 검사에 대한 지원을 추가했습니다.
- [로그인 옵션](sign-in-options.md)이 메일로 설정된 경우 로그인 헤더에는 “로그인 이름으로 로그인”이 표시됩니다. 사용자 이름 필드에는 “로그인 이름”이 표시됩니다. 자세한 내용은 [지역화](localization-string-ids.md#sign-up-or-sign-in-page-elements)를 참조하세요.

**1.2.0**

- 이제 사용자 이름/메일과 암호 필드에 `form` HTML 요소를 사용하면 Edge 및 IE(Internet Explorer)가 이 정보를 제대로 저장할 수 있습니다.
-  접근성 수정
- 이제 [HTML 태그](customize-ui-with-html.md#guidelines-for-using-custom-page-content)에 `data-preload="true"` 특성을 추가하여 CSS 및 JavaScript의 로드 순서를 제어할 수 있습니다.
  - 연결된 CSS 파일을 HTML 템플릿과 동시에 로드하므로 파일 로드 사이에 ‘깜박임’이 발생하지 않습니다.
  - 페이지가 로드되기 전에 `script` 태그를 가져오고 실행하는 순서를 제어합니다.
- 이제 메일 필드는 `type=email`이며 모바일 키보드는 올바른 제안을 제공합니다.
- Chrome 번역에 대한 지원입니다.
- 사용자 흐름 페이지에서 테넌트 브랜딩에 대한 지원을 추가했습니다.

**1.1.0**

- KMSI(로그인 유지) 컨트롤을 추가했습니다.

**1.0.0**

- 초기 릴리스

## <a name="mfa-page-multifactor"></a>MFA 페이지(다단계)

**1.2.5**
- 요청이 실패하도록 하는 언어 인코딩 문제를 해결했습니다.

**1.2.4**
- jQuery 버전을 3.5.1로 업데이트했습니다.
- HandlebarJS 버전을 4.7.6으로 업데이트했습니다.

**1.2.3**
- 언어 지역화를 통해 도구 설명 문자열을 재정의할 수 있습니다.
- 보안 수정 사항입니다.
- 사소한 버그가 수정되었습니다.

**1.2.2**
- iOS를 사용하는 경우 확인 코드 자동 채우기 관련 문제를 해결했습니다.
- Android 웹 보기에서 신뢰 당사자로 토큰을 리디렉션하는 문제를 해결했습니다. 
- `intro` 이외에 페이지에 제목으로 표시할 UXString `heading`을 추가했습니다. 이 항목은 기본적으로 숨겨집니다.  
- 레이아웃(classic, oceanBlue 또는 slateGray)을 선택하기 위해 정책 또는 QueryString 매개 변수 `pageFlavor`페이지에 제목으로 표시하는 지원을 추가했습니다.

**1.2.1**

- 기본 템플릿의 접근성 수정 사항

**1.2.0**

-  접근성 수정
- 이제 [HTML 태그](customize-ui-with-html.md#guidelines-for-using-custom-page-content)에 `data-preload="true"` 특성을 추가하여 CSS 및 JavaScript의 로드 순서를 제어할 수 있습니다.
  - 연결된 CSS 파일을 HTML 템플릿과 동시에 로드하므로 파일 로드 사이에 ‘깜박임’이 발생하지 않습니다.
  - 페이지가 로드되기 전에 `script` 태그를 가져오고 실행하는 순서를 제어합니다.
- 이제 메일 필드는 `type=email`이며 모바일 키보드는 올바른 제안을 제공합니다.
- Chrome 번역에 대한 지원입니다.
- 사용자 흐름 페이지에서 테넌트 브랜딩에 대한 지원을 추가했습니다.

**1.1.0**

- ‘코드 확인’ 단추가 제거되었습니다.
- 이제 코드의 입력 필드는 최대 6자의 입력만 사용합니다.
- 페이지는 단추를 클릭하지 않고도 6자리 숫자 코드가 입력될 때 입력된 코드를 자동으로 확인하려고 시도합니다.
- 코드가 잘못된 경우 입력 필드가 자동으로 지워집니다.
- 잘못된 코드로 3회 시도한 후 B2C는 신뢰 당사자에게 오류를 다시 보냅니다.
-  접근성 수정
- 기본 CSS를 제거했습니다.

**1.0.0**

- 초기 릴리스

## <a name="exception-page-globalexception"></a>예외 페이지(globalexception)

**1.2.1**
- jQuery 버전을 3.5.1로 업데이트했습니다.
- HandlebarJS 버전을 4.7.6으로 업데이트했습니다.

**1.2.0**

-  접근성 수정
- 이제 [HTML 태그](customize-ui-with-html.md#guidelines-for-using-custom-page-content)에 `data-preload="true"` 특성을 추가하여 CSS 및 JavaScript의 로드 순서를 제어할 수 있습니다.
  - 연결된 CSS 파일을 HTML 템플릿과 동시에 로드하므로 파일 로드 사이에 ‘깜박임’이 발생하지 않습니다.
  - 페이지가 로드되기 전에 `script` 태그를 가져오고 실행하는 순서를 제어합니다.
- 이제 메일 필드는 `type=email`이며 모바일 키보드는 올바른 제안을 제공합니다.
- Chrome 번역에 대한 지원

**1.1.0**

- 접근성 수정
- 정책에서 연락처가 없는 경우 기본 메시지를 제거했습니다.
- 기본 CSS를 제거했습니다.

**1.0.0**

- 초기 릴리스

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>기타 페이지(ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.1**
- jQuery 버전을 3.5.1로 업데이트했습니다.
- HandlebarJS 버전을 4.7.6으로 업데이트했습니다.

**1.2.0**

-  접근성 수정
- 이제 [HTML 태그](customize-ui-with-html.md#guidelines-for-using-custom-page-content)에 `data-preload="true"` 특성을 추가하여 CSS 및 JavaScript의 로드 순서를 제어할 수 있습니다.
  - 연결된 CSS 파일을 HTML 템플릿과 동시에 로드하므로 파일 로드 사이에 ‘깜박임’이 발생하지 않습니다.
  - 페이지가 로드되기 전에 `script` 태그를 가져오고 실행하는 순서를 제어합니다.
- 이제 메일 필드는 `type=email`이며 모바일 키보드는 올바른 제안을 제공합니다.
- Chrome 번역에 대한 지원

**1.0.0**

- 초기 릴리스

## <a name="next-steps"></a>다음 단계

사용자 지정 정책에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 관한 자세한 내용은 [사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)을 참조하세요.
