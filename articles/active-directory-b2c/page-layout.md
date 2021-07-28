---
title: 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: 사용자 지정 정책에서 UI 사용자 지정을 위한 페이지 레이아웃 버전 기록입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8469e05b82a651760829761ca57af3bdb1b256a9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443409"
---
# <a name="page-layout-versions"></a>페이지 레이아웃 버전

페이지 레이아웃 패키지는 페이지 요소의 수정 및 개선 사항을 포함하도록 주기적으로 업데이트됩니다. 다음 변경 로그는 각 버전에 도입된 변경 내용을 지정합니다.

## <a name="jquery-version"></a>jQuery 버전

Azure AD B2C 페이지 레이아웃은 다음 버전의 [jQuery 라이브러리](https://jquery.com/)를 사용합니다.

|페이지 레이아웃 버전에서  |jQuery 버전  |
|---------|---------|
|2.1.4 | 3.5.1 |
|1.2.0 | 3.4.1 |
|1.1.0 | 1.10.2 |

## <a name="self-asserted-page-selfasserted"></a>자체 어설션 페이지(selfasserted)

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
