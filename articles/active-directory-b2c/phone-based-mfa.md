---
title: Azure AD B2C에서 전화 기반 MFA 보안
titleSuffix: Azure AD B2C
description: Azure Monitor Log Analytics 보고서 및 경고를 사용하여 Azure AD B2C 테넌트에서 전화 기반 MFA(다단계 인증)를 보호하기 위한 팁을 알아봅니다. 통합 문서를 사용하여 사기 전화 인증을 식별하고 사기 등록을 완화합니다. =
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: ef0c610b0b7177b4132832974a6f2ac6de37aa7b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044516"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>전화 기반 MFA(다단계 인증) 보안

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD(Azure Active Directory) MFA(Multi-Factor Authentication)를 사용하면 사용자가 확인을 위해 등록한 전화번호로 자동 음성 통화를 수신하도록 선택할 수 있습니다. 악의적인 사용자는 MFA 등록 프로세스를 완료하지 않고 여러 계정을 만들고 전화를 걸어 이 방법을 이용할 수 있습니다. 이러한 수많은 실패한 등록으로 인해 허용된 등록 시도가 모두 사용되어 다른 사용자가 Azure AD B2C 테넌트에서 새 계정에 등록하지 못할 수 있습니다. 이러한 공격으로부터 보호하기 위해 Azure Monitor를 사용하여 전화 인증 실패를 모니터링하고 사기성 등록을 완화할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [Log Analytics 작업 영역](azure-monitor.md)을 만듭니다.

## <a name="create-a-phone-based-mfa-events-workbook"></a>전화 기반 MFA 이벤트 통합 문서 만들기

GitHub의 [Azure AD B2C 보고서 및 경고](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) 리포지토리에는 Azure AD B2C 로그를 기반으로 보고서, 경고 및 대시보드를 만들고 게시하는 데 사용할 수 있는 아티팩트가 포함되어 있습니다. 아래 그림의 초안 통합 문서는 전화 관련 오류를 강조 표시합니다.

### <a name="overview-tab"></a>개요 탭

**개요** 탭에는 다음 정보가 표시됩니다.

- 실패 이유(각 원인에 대한 실패한 전화 인증의 총 수)
- 평판이 좋지 않아 차단됨
- 전화 인증에 실패한 IP 주소(주어진 각 IP 주소에 대해 실패한 전화 인증의 총 수)
- IP 주소가 있는 전화번호 - 전화 인증 실패
- 브라우저(클라이언트 브라우저별 전화 인증 실패)
- 운영 체제(클라이언트 운영 체제별 전화 인증 실패)

![개요 탭](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>자세히 탭

**세부 정보** 탭에 보고되는 정보는 다음과 같습니다.

- Azure AD B2C 정책 - 전화 인증 실패
- 전화번호별 전화 인증 실패 타임 차트(타임라인 조정 가능)
- Azure AD B2C 정책 시간 차트에 의한 전화 인증 실패(조정 가능한 타임라인)
- IP 주소별 전화 인증 실패 타임 차트(타임라인 조정 가능)
- 전화번호를 선택하여 오류 세부 정보 보기(자세한 오류 목록을 보려면 전화번호 선택)

![세부 정보 탭 1/3](media/phone-based-mfa/details-tab-1.png)

![세부 정보 탭 2/3](media/phone-based-mfa/details-tab-2.png)

![세부 정보 탭 3/3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>통합 문서를 사용하여 사기성 등록 식별

통합 문서를 사용하여 전화 기반 MFA 이벤트를 이해하고 전화 통신 서비스의 잠재적인 악의적인 사용을 식별할 수 있습니다.

1. 다음 질문에 답하여 테넌트에게 정상적인 것이 무엇인지 이해합니다.

   - 전화 기반 MFA가 필요한 지역은 어디인가요?
   - 실패한 전화 기반 MFA 시도에 대해 표시된 이유를 조사합니다. 정상 또는 예상되는 것으로 간주되나요?

2. 사기 등록의 특징을 파악합니다.

   - **위치 기반**: 사용자가 등록할 것으로 예상되지 않는 위치와 연결된 계정에 대해 **IP 주소별 전화 인증 실패** 를 조사합니다.

   > [!NOTE]
   > 제공된 IP 주소는 대략적인 지역입니다.

   - **속도 기반**: **실패한 전화 인증 초과 시간(일당)** 을 살펴봅니다. 이는 하루 전화 인증 실패 횟수가 비정상적으로 많은 전화번호를 가장 높은(왼쪽)에서 가장 낮은(오른쪽) 순서로 표시합니다.

3. 다음 섹션의 단계에 따라 사기성 등록을 완화합니다.
 

## <a name="mitigate-fraudulent-sign-ups"></a>사기성 등록 완화

사기성 등록을 완화하려면 다음 작업을 취합니다.

- 사용자 흐름의 **권장** 버전을 사용하여 다음을 수행합니다.
     
   - MFA에 대해 [이메일 OTP(일회용 암호) 기능을 사용하도록 설정](phone-authentication-user-flows.md)합니다(등록 및 로그인 흐름 모두에 적용됨).
   - 위치를 기반으로 로그인을 차단하도록 [조건부 액세스 정책을 구성](conditional-access-user-flow.md)합니다(등록 흐름이 아닌 로그인 흐름에만 적용됨).
   - API 커넥터를 사용하여 [reCAPTCHA와 같은 안티봇 솔루션과 통합](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha)합니다(등록 절차에 적용).

- 사용자가 전화번호를 확인하는 드롭다운 메뉴에서 조직과 관련이 없는 국가 코드를 제거합니다(이 변경 사항은 향후 등록에 적용됨).
    
   1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
   1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
   1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
   1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
   1. 사용자 흐름을 선택한 다음 **언어** 를 선택합니다. 조직의 지리적 위치에 대한 언어를 선택하여 언어 세부 정보 패널을 엽니다. (이 예에서는 미국의 경우 **English en** 을 선택합니다.) **다단계 인증 페이지** 를 선택한 다음 **기본값 다운로드(en)** 를 선택합니다.
 
      ![기본값을 다운로드하려면 새 재정의를 업로드합니다.](media/phone-based-mfa/download-defaults.png)

   1. 이전 단계에서 다운로드한 JSON 파일을 엽니다. 파일에서 `DEFAULT`을 검색하고 행을 `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"`로 바꿉니다. `Overrides`을 `true`로 설정해야 합니다.

   > [!NOTE]
   > `countryList` 요소에서 허용되는 국가 코드 목록을 사용자 지정할 수 있습니다([전화 요소 인증 페이지 예](localization-string-ids.md#phone-factor-authentication-page-example) 참조).

   1. JSON 파일을 저장합니다. 언어 세부 정보 패널의 **새 재정의 업로드** 에서 수정된 JSON 파일을 선택하여 업로드합니다.
   1. 패널을 닫고 **사용자 흐름 실행** 을 선택합니다. 이 예에서는 **미국** 이 드롭다운에서 사용할 수 있는 유일한 국가 코드인지 확인합니다.
 
      ![국가 코드 드롭다운](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C용 ID 보호 및 조건부 액세스](conditional-access-identity-protection-overview.md)에 대해 알아보기 

- [Azure Active Directory B2C의 사용자 흐름에 대한 조건부 액세스](conditional-access-user-flow.md) 적용
