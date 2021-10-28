---
title: 조건부 액세스 정책의 권한 부여 컨트롤 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 권한 부여 컨트롤이란 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8c0bf5ac063fbf300cf43cd46a22e8904cb6bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548790"
---
# <a name="conditional-access-grant"></a>조건부 액세스: 권한 부여

조건부 액세스 정책 내에서 관리자는 액세스 제어를 사용하여 리소스에 대한 액세스를 허용하거나 차단할 수 있습니다.

![다단계 인증이 필요한 권한 부여 컨트롤을 사용하는 조건부 액세스 정책](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>액세스 차단

차단은 모든 할당을 고려하여 조건부 액세스 정책 구성에 따라 액세스를 방지합니다.

차단은 적절한 정보를 바탕으로 행사해야 하는 강력한 컨트롤입니다. 차단 문이 있는 정책에는 의도하지 않은 부작용이 발생할 수 있습니다. 대규모로 차단을 사용 설정하기 전에 적절한 테스트와 유효성 검사를 거치는 것이 중요합니다. 관리자는 [조건부 액세스 보고 전용 모드](concept-conditional-access-report-only.md) 및 [조건부 액세스의 What If 도구](what-if-tool.md)와 같은 도구를 활용해야 합니다.

## <a name="grant-access"></a>액세스 권한 부여

관리자는 액세스 권한을 부여할 때 하나 이상의 컨트롤을 적용하도록 선택할 수 있습니다. 해당 컨트롤에는 다음 옵션이 포함됩니다. 

- [다단계 인증 필요(Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [준수 상태로 표시된 디바이스 필요(Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [하이브리드 Azure AD 조인 디바이스 필요](../devices/concept-azure-ad-join-hybrid.md)
- [승인된 클라이언트 앱 필요](app-based-conditional-access.md)
- [앱 보호 정책 필요](app-protection-based-conditional-access.md)
- [암호 변경 필요](#require-password-change)

관리자가 이러한 옵션 중 일부를 결합하도록 선택하는 경우 다음 방법 중에서 선택할 수 있습니다.

- 선택한 모든 컨트롤 필요(컨트롤 **및** 컨트롤)
- 선택한 컨트롤 중 하나가 필요(컨트롤 **또는** 컨트롤)

기본적으로 조건부 액세스에는 선택한 모든 컨트롤이 필요합니다.

### <a name="require-multi-factor-authentication"></a>다단계 인증 필요

이 확인란을 선택하면 사용자가 Azure AD Multi-Factor Authentication을 수행해야 합니다. Azure AD Multi-Factor Authentication 배포에 대한 자세한 내용은 [클라우드 기반 Azure Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md) 문서에서 확인할 수 있습니다.

[비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview)는 조건부 액세스 정책의 다단계 인증에 대한 요구 사항을 충족합니다. 

### <a name="require-device-to-be-marked-as-compliant"></a>디바이스를 준수 상태로 표시해야 함

Microsoft Intune을 배포한 조직은 디바이스에서 반환된 정보를 사용하여 특정 규정 준수 요구 사항을 충족하는 디바이스를 식별할 수 있습니다. 이 정책 준수 정보는 Intune에서 Azure AD에 전달되며 조건부 액세스에서 리소스에 대한 액세스를 부여하거나 차단하는 결정을 내릴 수 있습니다. 준수 정책에 대한 자세한 내용은 [Intune을 사용하여 디바이스에서 조직의 리소스에 대한 액세스를 허용하는 규칙 설정](/intune/protect/device-compliance-get-started) 문서를 참조하세요.

디바이스는 Intune(모든 디바이스 OS용) 또는 Windows 10 디바이스용 타사 MDM 시스템에서 규정 준수 상태로 표시할 수 있습니다. 지원되는 타사 MDM 시스템 목록은 [Intune에서 타사 디바이스 준수 파트너 지원](/mem/intune/protect/device-compliance-partners)문서에서 확인할 수 있습니다.

디바이스는 준수 상태로 표시되기 전에 Azure AD에 등록되어 있어야 합니다. 디바이스 등록에 대한 자세한 내용은 [디바이스 ID란?](../devices/overview.md)문서에서 확인할 수 있습니다.

### <a name="require-hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 디바이스 필요

조직에서는 조건부 액세스 정책의 일부로 디바이스 ID를 사용하도록 선택할 수 있습니다. 조직에서는 이 확인란을 사용하여 디바이스가 하이브리드 Azure AD에 조인되도록 요구할 수 있습니다. 디바이스 ID에 대한 자세한 내용은 [디바이스 ID란?](../devices/overview.md)문서를 참조하세요.

[디바이스 코드 OAuth 흐름](../develop/v2-oauth2-device-code.md)을 사용하는 경우 관리 디바이스 권한 부여 컨트롤 요구 또는 디바이스 상태 조건이 지원되지 않습니다. 이는 인증을 수행하는 디바이스가 코드를 제공하는 디바이스에 디바이스 상태를 제공할 수 없으며, 토큰의 디바이스 상태가 인증을 수행하는 디바이스에 대해 잠겨 있기 때문입니다. 대신 다단계 인증 권한 부여 컨트롤 필요를 사용하세요.

### <a name="require-approved-client-app"></a>승인된 클라이언트 앱 필요

조직에서는 클라우드 앱에 대한 액세스 시도가 승인된 클라이언트 앱에서 수행되도록 요구할 수 있습니다. 이러한 승인된 클라이언트 앱은 모든 MDM(모바일 디바이스 관리) 솔루션과는 별도로 [Intune 앱 보호 정책](/intune/app-protection-policy)을 지원합니다.

이 권한 부여 컨트롤을 적용하려면 조건부 액세스는 디바이스가 Azure Active Directory에 등록되어 있을 것을 요구합니다. 이렇게 하려면 Broker 앱을 사용해야 합니다. iOS의 Microsoft Authenticator, 또는 Android 디바이스용 Microsoft Authenticator 또는 Microsoft 회사 포털 중 하나를 Broker 앱으로 사용할 수 있습니다. 사용자가 인증을 시도할 때 Broker 앱이 디바이스에 설치되어 있지 않은 경우, 사용자는 필요한 Broker 앱 설치를 위한 적절한 앱 스토어로 리디렉션됩니다.

다음은 해당 설정을 지원하는 것으로 확인된 클라이언트 앱입니다.

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft 목록
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power Apps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- 비즈니스용 Microsoft Skype
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Microsoft 365 관리

**주의**

- 승인된 클라이언트 앱은 Intune 모바일 애플리케이션 관리 기능을 지원합니다.
- **승인된 클라이언트 앱 필요** 요구 사항:
   - 디바이스 플랫폼 조건에서는 iOS 및 Android만 지원됩니다.
   - 디바이스를 등록하려면 Broker 앱이 필요합니다. iOS의 Microsoft Authenticator, 또는 Android 디바이스용 Microsoft Authenticator 또는 Microsoft 회사 포털 중 하나를 Broker 앱으로 사용할 수 있습니다.
- 조건부 액세스는 승인된 클라이언트 앱으로 InPrivate 모드의 Microsoft Edge를 고려할 수 없습니다.
- Azure AD 애플리케이션 프록시를 사용하여 Power BI 모바일 앱을 온-프레미스 Power BI Report Server에 연결하도록 사용 설정하는 것은 Microsoft Power PI 앱을 승인된 클라이언트 앱으로 요구하는 조건부 액세스 정책에서 지원되지 않습니다.

구성 예제는 [방법: 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 승인된 클라이언트 앱 필요](app-based-conditional-access.md) 문서를 참조하세요.

### <a name="require-app-protection-policy"></a>앱 보호 정책 필요

조건부 정책에서 선택한 클라우드 앱에 액세스하려면 클라이언트 앱에 [Intune 앱 보호 정책](/intune/app-protection-policy)이 있어야 하도록 요구할 수 있습니다. 

이 권한 부여 컨트롤을 적용하려면 조건부 액세스는 디바이스가 Azure Active Directory에 등록되어 있을 것을 요구합니다. 이렇게 하려면 Broker 앱을 사용해야 합니다. 브로커 앱은 iOS의 경우 Microsoft Authenticator, Android 디바이스의 경우 Microsoft 회사 포털일 수 있습니다. 사용자가 인증을 시도할 때 Broker 앱이 디바이스에 설치되어 있지 않은 경우, 사용자는 Broker 앱 설치를 위한 앱 스토어로 리디렉션됩니다.

애플리케이션은 **정책 보증** 이 구현된 **Intune SDK** 가 있어야 하며 해당 설정을 지원하기 위한 다른 특정 요구 사항을 충족해야 합니다. Intune SDK를 사용하여 애플리케이션을 구현하는 개발자는 이러한 요구 사항에 대한 SDK 문서에서 자세한 내용을 확인할 수 있습니다.

다음은 해당 설정을 지원하는 것으로 확인된 클라이언트 앱입니다.

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft 목록(iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Teams
- Microsoft Word
- Intune용 MultiLine
- Nine Mail - 메일 및 달력

> [!NOTE]
> Microsoft Kaizala, 비즈니스용 Microsoft Skype, Microsoft Visio는 **앱 보호 정책 부여 필요** 를 지원하지 않습니다. 이러한 앱이 작동하도록 해야 하는 경우 **승인된 앱 필요** 권한 부여만을 사용하세요. 이러한 3가지 애플리케이션에서는 두 개의 권한 부여 사이에 the 또는 절을 사용할 수 없습니다.

**주의**

- 앱 보호 정책용 앱은 정책 보호를 사용하여 Intune 모바일 애플리케이션 관리 기능을 지원합니다.
- **앱 보호 정책 필요** 요구 사항은 다음과 같습니다.
    - 디바이스 플랫폼 조건에서는 iOS 및 Android만 지원됩니다.
    - 디바이스를 등록하려면 Broker 앱이 필요합니다. iOS에서 Broker 앱은 Microsoft Authenticator이며 Android에서는 Intune 회사 포털 앱입니다.

구성에 대한 예제는 [방법: 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 앱 보호 정책 및 승인된 클라이언트 앱 필요](app-protection-based-conditional-access.md) 문서를 참조하세요.

### <a name="require-password-change"></a>암호 변경 필요 

사용자 위험이 감지된 경우 관리자는 사용자 위험 정책 조건을 사용하여 사용자가 Azure AD 셀프 서비스 암호 재설정을 통해 암호를 안전하게 변경하도록 선택할 수 있습니다. 사용자 위험이 검색되면 사용자는 셀프 서비스 암호 재설정을 수행하여 사용자 위험 이벤트를 자동으로 수정하고 닫을 수 있으므로 관리자에게 불필요한 업무 부담이 발생하지 않게 됩니다. 

사용자에게 암호를 변경하라는 메시지가 표시되면 먼저 다단계 인증을 완료해야 합니다. 사용자 계정에 위험이 감지되는 경우를 대비할 수 있도록 모든 사용자가 다단계 인증에 등록했는지 확인해야 합니다.  

> [!WARNING]
> 사용자는 사용자 위험 정책을 트리거하기 전에 셀프 서비스 암호 재설정에 이미 등록되어 있어야 합니다. 

암호 변경 컨트롤을 사용하여 정책을 구성하는 경우의 제한 사항은 다음과 같습니다.  

1. 정책은 ‘모든 클라우드 앱’에 할당해야 합니다. 이 요구 사항은 다른 앱을 통해 로그인하는 방식을 통해 공격자가 다른 앱을 사용하여 사용자의 암호를 변경하고 계정을 재설정하는 위험을 방지할 수 있습니다. 
1. 암호 변경 필요는 규정 준수 디바이스 필요와 같은 다른 컨트롤과 함께 사용할 수 없습니다.  
1. 암호 변경 컨트롤은 사용자 및 그룹 할당 조건, 클라우드 앱 할당 조건(all로 설정되어야 함), 사용자 위험 조건에만 사용할 수 있습니다. 

### <a name="terms-of-use"></a>사용 약관

조직에서 사용 약관을 만든 경우 권한 부여 컨트롤에 다른 옵션이 표시될 수 있습니다. 이러한 옵션을 사용하면 관리자는 정책에 의해 보호되는 리소스에 액세스하는 조건으로 사용 약관을 승인하도록 요구할 수 있습니다. 사용 약관에 대한 자세한 내용은 [Azure Active Directory 사용 약관](terms-of-use.md)문서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 세션 컨트롤](concept-conditional-access-session.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)
