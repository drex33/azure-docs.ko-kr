---
title: 조건부 액세스 - 규격 디바이스 필요 - Azure Active Directory
description: 규격 디바이스를 요구하는 사용자 지정 조건부 액세스 정책 만들기
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
ms.openlocfilehash: 327d04109be26e14f8fedc46e88266e51e733455
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615418"
---
# <a name="conditional-access-require-compliant-devices"></a>조건부 액세스: 규격 디바이스 필요

Microsoft Intune을 배포한 조직은 디바이스에서 반환된 정보를 사용하여 다음과 같은 규정 준수 요구 사항을 충족하는 디바이스를 식별할 수 있습니다.

* 잠금 해제 시 PIN 필요
* 디바이스 암호화 필요
* 최소 또는 최대 운영 체제 버전 필요
* 탈옥 또는 루팅되지 않은 디바이스 필요

이 정책 준수 정보는 Azure AD에 전달되며 조건부 액세스에서 리소스에 대한 액세스를 부여하거나 차단하는 결정을 내릴 수 있습니다. 디바이스 준수 정책에 대한 자세한 내용은 [Intune을 사용하여 디바이스에서 조직의 리소스에 대한 액세스를 허용하는 규칙 설정](/intune/protect/device-compliance-get-started) 문서를 참조하세요.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 생성

다음 단계는 조직의 Intune 준수 정책으로 리소스에 액세스하는 디바이스가 준수 상태로 표시되는 것을 요구하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.
   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **모든 클라우드 앱** 을 선택합니다.
   1. 특정 애플리케이션을 정책에서 제외해야 하는 경우 제외된 **클라우드 앱 선택** 의 **제외** 탭에서 해당 애플리케이션을 선택하고 **선택** 을 선택할 수 있습니다.
   1. **완료** 를 선택합니다.
1. 클라이언트 앱(미리 보기) **조건** > **아래에서** > **이 정책이 적용될 클라이언트 앱을 선택** 하고 모든 기본값을 선택된 채로 두고 **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **준수 상태로 표시된 디바이스 필요** 를 선택합니다.
   1. **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 만들어 사용하도록 설정합니다.

> [!NOTE]
> 위의 단계를 사용하여 **모든 사용자** 와 **모든 클라우드 앱** 에서 **규격으로 표시된 디바이스 필요** 를 선택한 경우에도 Intune에 새 디바이스를 등록할 수 있습니다. **규격으로 표시된 디바이스 필요** 제어는 Intune 등록을 차단하지 않습니다. 

### <a name="known-behavior"></a>알려진 동작

Windows 7, iOS, Android, macOS 및 일부 타사 웹 브라우저에서 Azure AD는 디바이스가 Azure AD에 등록될 때 프로비저닝되는 클라이언트 인증서를 사용하여 디바이스를 식별합니다. 사용자가 브라우저를 통해 처음으로 로그인하면 인증서를 선택하라는 메시지가 사용자에게 표시됩니다. 최종 사용자가 이 인증서를 선택해야 브라우저를 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[디바이스 준수 정책이 Azure AD에서 사용됨](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
