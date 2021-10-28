---
title: 조건부 액세스 - 모든 사용자용 MFA 필요 - Azure Active Directory
description: 모든 사용자가 다단계 인증을 수행하도록 요구하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3019a4f1f7b82ad202e8a42f5edb070083fd1db7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128578626"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>조건부 액세스: 모든 사용자용 MFA 필요

Microsoft의 ID 보안 디렉터리인 Alex Weinert의 블로그 게시물 [Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)에 언급된 바와 같이

> 암호는 중요하지 않지만 MFA는 중요합니다! Microsoft의 연구에 따르면, MFA를 사용하는 경우 계정이 손상될 가능성이 99.9% 이상 줄어듭니다.

이 문서의 지침을 참조하여 조직에서 사용자 환경에 맞게 조정된 MFA 정책을 만들 수 있습니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구이므로 정책에서 다음 계정을 제외하는 것이 좋습니다.

* 테넌트 전체 계정 잠금을 방지하기 위한 **응급 액세스** 또는 **비상** 계정. 드문 경우지만 모든 관리자가 테넌트에서 잠기면 응급 액세스 관리 계정을 사용하여 테넌트에 로그인하여 액세스 복구 단계를 수행할 수 있습니다.
   * 자세한 내용은 [Azure AD에서 응급 액세스 계정 관리](../roles/security-emergency-access.md) 문서를 참조하세요.
* **서비스 계정** 및 **서비스 주체**(예: Azure AD Connect 동기화 계정). 서비스 계정은 특정 사용자에게 연결되지 않은 비대화형 계정입니다. 일반적으로 애플리케이션에 대한 프로그래밍 방식 액세스를 허용하는 백 엔드 서비스에서 사용되지만 관리 목적으로 시스템에 로그인하는 데도 사용됩니다. MFA를 프로그래밍 방식으로 완료할 수 없기 때문에 이러한 서비스 계정은 제외되어야 합니다. 서비스 주체가 수행한 호출은 조건부 액세스에 의해 차단되지 않습니다.
   * 조직에서 스크립트나 코드에 이러한 계정을 사용하는 경우 [관리 ID](../managed-identities-azure-resources/overview.md)로 대체하는 것이 좋습니다. 임시 해결 방법으로 기준 정책에서 이러한 특정 계정을 제외할 수 있습니다.

## <a name="application-exclusions"></a>애플리케이션 제외

조직에서는 다양한 클라우드 애플리케이션을 사용할 것입니다. 이러한 애플리케이션이 모두 동일한 보안을 요구하는 것은 아닙니다. 예를 들어 급여 및 출석 애플리케이션은 MFA가 필요할 수 있지만 구내식당은 필요하지 않습니다. 관리자는 특정 애플리케이션을 해당 정책에서 제외하도록 선택할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 생성

다음 단계를 통해 모든 사용자에게 다단계 인증을 수행하도록 요구하는 조건부 액세스 정책을 만들 수 있습니다.

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.
   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **모든 클라우드 앱** 을 선택합니다.
   1. **제외** 에서 다단계 인증이 필요하지 않은 애플리케이션을 선택합니다.
1. **조건** > **클라이언트 앱(미리 보기)** 의 **이 정책이 적용될 클라이언트 앱 선택** 에서 모든 기본값을 선택한 상태로 두고 **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **다단계 인증 요구**, **선택** 을 차례로 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 만들어 사용하도록 설정합니다.

### <a name="named-locations"></a>명명된 위치

조직에서는 **명명된 위치** 라고 하는 알려진 네트워크 위치를 조건부 액세스 정책에 통합하도록 선택할 수 있습니다. 이러한 명명된 위치에는 본사 위치에 대한 것과 같은 신뢰할 수 있는 IPv4 네트워크가 포함될 수 있습니다. 명명된 위치 구성에 관한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건이란?](location-condition.md) 문서를 참조하세요.

위의 예제 정책에서 조직은 회사 네트워크에서 클라우드 앱에 액세스할 때 다단계 인증을 요구하지 않도록 선택할 수 있습니다. 이 경우 정책에 다음 구성을 추가할 수 있습니다.

1. **할당** 에서 **조건** > **위치** 를 선택합니다.
   1. **예** 를 구성합니다.
   1. **임의의 위치** 를 포함합니다.
   1. **신뢰할 수 있는 모든 위치** 를 제외합니다.
   1. **완료** 를 선택합니다.
1. **완료** 를 선택합니다.
1. 정책 변경 내용을 **저장** 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
