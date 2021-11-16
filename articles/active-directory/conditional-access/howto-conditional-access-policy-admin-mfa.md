---
title: 조건부 액세스 - 관리자용 MFA 필요 - Azure Active Directory
description: 관리자가 다단계 인증을 수행하도록 요구하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7abf8738fd93cc84923abb3d9e5308599c4220da
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272494"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>조건부 액세스: 관리자용 MFA 필요

관리 권한이 할당된 계정은 공격자의 대상이 됩니다. 해당 계정에 대한 MFA(다단계 인증)를 요구하는 것은 해당 계정이 침해되는 위험을 줄일 수 있는 간단한 방법입니다.

최소한 다음 역할에 대한 MFA를 요구하는 것이 좋습니다.

- 글로벌 관리자
- 애플리케이션 관리자
- 인증 관리자
- 대금 청구 관리자
- 클라우드 애플리케이션 관리자
- 조건부 액세스 관리자
- Exchange 관리자
- 기술 지원팀 관리자
- 암호 관리자
- 권한 있는 인증 관리자
- 권한 있는 역할 관리자
- 보안 관리자
- SharePoint 관리자
- 사용자 관리자

조직에서는 적합한 역할을 포함하거나 제외하도록 선택할 수 있습니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구이므로 정책에서 다음 계정을 제외하는 것이 좋습니다.

- 테넌트 전체 계정 잠금을 방지하기 위한 **응급 액세스** 또는 **비상** 계정. 드문 경우지만 모든 관리자가 테넌트에서 잠기면 응급 액세스 관리 계정을 사용하여 테넌트에 로그인하여 액세스 복구 단계를 수행할 수 있습니다.
   - 자세한 내용은 [Azure AD에서 응급 액세스 계정 관리](../roles/security-emergency-access.md) 문서를 참조하세요.
- **서비스 계정** 및 **서비스 주체**(예: Azure AD Connect 동기화 계정). 서비스 계정은 특정 사용자에게 연결되지 않은 비대화형 계정입니다. 일반적으로 애플리케이션에 대한 프로그래밍 방식 액세스를 허용하는 백 엔드 서비스에서 사용되지만 관리 목적으로 시스템에 로그인하는 데도 사용됩니다. MFA를 프로그래밍 방식으로 완료할 수 없기 때문에 이러한 서비스 계정은 제외되어야 합니다. 서비스 주체가 수행한 호출은 조건부 액세스에 의해 차단되지 않습니다.
   - 조직에서 스크립트나 코드에 이러한 계정을 사용하는 경우 [관리 ID](../managed-identities-azure-resources/overview.md)로 대체하는 것이 좋습니다. 임시 해결 방법으로 기준 정책에서 이러한 특정 계정을 제외할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 생성

다음 단계를 통해 관리자 역할이 할당된 사용자에게 다단계 인증을 수행하도록 요구하는 조건부 액세스 정책을 만들 수 있습니다.

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 아래에서 **디렉터리 역할** 을 선택하고 다음과 같은 기본 제공 역할을 선택합니다.
      - 글로벌 관리자
      - 애플리케이션 관리자
      - 인증 관리자
      - 대금 청구 관리자
      - 클라우드 애플리케이션 관리자
      - 조건부 액세스 관리자
      - Exchange 관리자
      - 기술 지원팀 관리자
      - 암호 관리자
      - 권한 있는 인증 관리자
      - 권한 있는 역할 관리자
      - 보안 관리자
      - SharePoint 관리자
      - 사용자 관리자
   
      > [!WARNING]
      > 조건부 액세스 정책은 기본 제공 역할을 지원합니다. 조건부 액세스 정책은 [관리 단위 범위](../roles/admin-units-assign-roles.md) 또는 [사용자 지정 역할](../roles/custom-create.md)을 비롯한 다른 역할 형식에는 적용되지 않습니다.

   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다.
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **모든 클라우드 앱** 을 선택하고 **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **다단계 인증 요구**, **선택** 을 차례로 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **만들기** 를 선택하여 정책을 만들어 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
