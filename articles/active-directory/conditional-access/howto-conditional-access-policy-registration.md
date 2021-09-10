---
title: 조건부 액세스 - 결합된 보안 정보 - Azure Active Directory
description: 보안 정보 등록을 위한 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/28/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa38195c836c77e9f0fd621776e997d4b7fff7bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536528"
---
# <a name="conditional-access-securing-security-info-registration"></a>조건부 액세스: 보안 정보 등록 보호

조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록하는 시기 및 방법에 대한 보안을 설정할 수 있습니다. 이 기능은 [결합된 등록](../authentication/concept-registration-mfa-sspr-combined.md)을 사용하도록 설정한 조직에서 사용할 수 있습니다. 이 기능을 사용하면 조직은 등록 프로세스를 조건부 액세스 정책의 모든 애플리케이션과 같이 처리하고 조건 액세스의 모든 기능을 사용하여 환경을 보호할 수 있습니다. Microsoft Authenticator 앱에 로그인하거나 암호 없는 휴대폰 로그인을 사용하도록 설정하는 사용자에게는 이 정책이 적용됩니다.

과거에 일부 조직에서는 등록 환경을 보호하기 위한 수단으로 신뢰된 네트워크 위치 또는 디바이스 준수를 사용했을 수 있습니다. Azure AD에 [임시 액세스 패스](../authentication/howto-authentication-temporary-access-pass.md)가 추가되면 관리자는 사용자에게 시간이 제한된 자격 증명을 프로비저닝하여 모든 디바이스 또는 위치에서 등록할 수 있습니다. 임시 액세스 패스 자격 증명은 다단계 인증에 대한 조건부 액세스 요구 사항을 충족합니다.

## <a name="create-a-policy-to-secure-registration"></a>보안 등록에 대한 정책 만들기

다음 정책은 통합된 등록 환경을 사용하여 등록을 시도하는 선택된 사용자에게 적용됩니다. 정책을 사용하려면 사용자가 다단계 인증을 수행하거나 임시 액세스 패스 자격 증명을 사용해야 합니다.

1. **Azure Portal** 에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 이름에 이 정책의 이름을 입력합니다. 예를 들어 **TAP으로 결합된 보안 정보 등록** 과 같이 입력하면 됩니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.

      > [!WARNING]
      > 사용자에 대해 [결합된 등록](../authentication/howto-registration-mfa-sspr-combined.md)을 사용하도록 설정해야 합니다.

   1. **제외** 아래의
      1. **모든 게스트 및 외부 사용자** 를 선택합니다.
      
         > [!NOTE]
         > 임시 액세스 패스는 게스트 사용자에게 작동하지 않습니다.

      1. **사용자 및 그룹** 을 선택하고 조직의 비상 액세스 또는 비상 계정을 선택합니다. 
1. **클라우드 앱 또는 작업** 에서 **사용자 작업** 을 선택하고 **보안 정보 등록** 을 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 다음을 수행합니다.
   1. **액세스 권한 부여** 를 선택합니다.
   1. **다단계 인증 필요** 를 선택합니다.
   1. **선택** 을 클릭합니다.
1. **정책 사용** 을 **켜기** 로 설정합니다.
1. 그런 다음 **만들기** 를 선택합니다.

이제 관리자는 등록을 위한 다단계 인증 요구 사항을 충족할 수 있도록 새 사용자에게 임시 액세스 패스 자격 증명을 발급해야 합니다. 이 작업을 수행하기 위한 단계는 [Azure AD Portal에서 임시 액세스 패스 만들기](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass) 섹션에서 확인할 수 있습니다.

조직은 6b 단계에서 **다단계 인증 요구** 외에 또는 해당 요구 대신 다른 권한 부여 컨트롤 요구를 선택할 수 있습니다. 여러 컨트롤을 선택하는 경우 변경 작업 진행 시 선택한 컨트롤 중 **하나** 또는 **모두** 필요하도록 알맞은 라디오 단추를 선택해야 합니다.

### <a name="guest-user-registration"></a>게스트 사용자 등록

디렉터리에서 다단계 인증을 등록해야 하는 [게스트 사용자](../external-identities/what-is-b2b.md)의 경우 다음 가이드를 이용하여 [신뢰할 수 있는 네트워크 위치](concept-conditional-access-conditions.md#locations) 외부에서 등록을 차단하도록 선택할 수 있습니다.

1. **Azure Portal** 에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 이름에 이 정책의 이름을 입력합니다. 예를 들어 **신뢰할 수 있는 네트워크의 결합된 보안 정보 등록** 이라고 지정할 수 있습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.
   1. **포함** 아래의 **모든 게스트 및 외부 사용자** 를 선택합니다.
1. **클라우드 앱 또는 작업** 에서 **사용자 작업** 을 선택하고 **보안 정보 등록** 을 선택합니다.
1. **조건** > **위치** 에서 다음을 수행합니다.
   1. **예** 를 구성합니다.
   1. **임의의 위치** 를 포함합니다.
   1. **신뢰할 수 있는 모든 위치** 를 제외합니다.
   1. 위치 블레이드에서 **완료** 를 선택합니다.
   1. 조건 블레이드에서 **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 다음을 수행합니다.
   1. **액세스 차단** 을 선택합니다.
   1. 그런 다음 **선택** 을 클릭합니다.
1. **정책 사용** 을 **켜기** 로 설정합니다.
1. 그런 다음 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[사용자가 인증 정보를 재확인하도록 요구](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
