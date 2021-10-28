---
title: 조건부 액세스를 사용하여 승인된 클라이언트 앱 - Azure Active Directory
description: Azure Active Directory에서 조건부 액세스를 사용하여 클라우드 앱 액세스에 승인된 클라이언트 앱을 요구하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f6a2d54b8669ddfbb2a1427ffffad97a7d539f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587198"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>방법: 조건부 액세스를 사용하여 클라우드 앱 액세스에 승인된 클라이언트 앱 요구

사용자는 정기적으로 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원들이 생산성을 높일 수 있도록 하는 동시에 잠재적으로 안전하지 않은 애플리케이션의 데이터 손실을 방지하려고 합니다. 조건부 액세스를 사용하면 조직에서 승인된(최신 인증 지원) 클라이언트 앱으로 액세스를 제한할 수 있습니다.

이 문서에서는 Office 365, Exchange Online, SharePoint Online과 같은 리소스에 대한 조건부 액세스 정책을 구성하는 두 가지 시나리오를 제공합니다.

- [시나리오 1: Microsoft 365 앱에 승인된 클라이언트 앱 필요](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [시나리오 2: Exchange Online 및 SharePoint Online에 승인된 클라이언트 앱 필요](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

조건부 액세스에서 이 기능은 승인된 클라이언트 앱을 요구하는 것으로 알려져 있습니다. 승인된 클라이언트 앱의 목록은 [승인된 클라이언트 앱 요구 사항](concept-conditional-access-grant.md#require-approved-client-app)을 참조하세요.

> [!NOTE]
> iOS 및 Android 디바이스에 승인된 클라이언트 앱을 요구하려면 해당 디바이스는 먼저 Azure AD에 등록해야 합니다.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>시나리오 1: Microsoft 365 앱에 승인된 클라이언트 앱 필요

이 시나리오에서 Contoso는 Outlook mobile, OneDrive 및 Microsoft Teams와 같은 승인된 클라이언트 앱을 사용하는 경우 모바일 디바이스를 사용하는 사용자가 모든 Microsoft 365 서비스에 액세스할 수 있도록 결정했습니다. 모든 사용자는 이미 Azure AD 자격 증명을 사용하여 로그인하고 Azure AD Premium P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스가 할당되어 있습니다.

조직에서는 모바일 디바이스에서 승인된 클라이언트 앱을 사용하도록 요구하기 위해 다음 세 단계를 완료해야 합니다.

**1단계: Exchange Online에 액세스할 때 승인된 클라이언트 애플리케이션을 사용해야 하는 Android 및 iOS 기반 최신 인증 클라이언트에 대한 정책입니다.**

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹** 을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **Office 365** 를 선택합니다.
1. **조건** 에서 **디바이스 플랫폼** 을 선택합니다.
   1. **구성** 을 **예** 로 설정합니다.
   1. **Android** 및 **iOS** 를 포함합니다.
1. **조건** 에서 **클라이언트 앱(미리 보기)** 을 선택합니다.
   1. **구성** 을 **예** 로 설정합니다.
   1. **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **승인된 클라이언트 앱 필요** 를 선택하고 **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **만들기** 를 선택하여 정책을 만들고 사용하도록 설정합니다.

**2단계: EAS(Exchange Online with Active Sync)에 대한 Azure AD 조건부 액세스 정책 구성**

1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹** 을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **Office 365 Exchange Online** 을 선택합니다.
1. **조건** 에서:
   1. **클라이언트 앱(미리 보기)** :
      1. **구성** 을 **예** 로 설정합니다.
      1. **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **승인된 클라이언트 앱 필요** 를 선택하고 **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 만들고 사용하도록 설정합니다.

**3단계: iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies) 문서를 검토하세요. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>시나리오 2: Exchange Online 및 SharePoint Online에 승인된 클라이언트 앱 필요

이 시나리오에서 Contoso는 Outlook 모바일과 같은 승인된 클라이언트 앱을 사용하는 한, 사용자가 모바일 디바이스에서 메일 및 SharePoint 데이터에만 액세스할 수 있도록 결정했습니다. 모든 사용자는 이미 Azure AD 자격 증명을 사용하여 로그인하고 Azure AD Premium P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스가 할당되어 있습니다.

조직에서는 모바일 디바이스 및 Exchange ActiveSync 클라이언트에서 승인된 클라이언트 앱을 사용하도록 요구하기 위해 다음 세 단계를 완료해야 합니다.

**1단계: Exchange Online 및 SharePoint Online에 액세스할 때 승인된 클라이언트 애플리케이션을 사용해야 하는 Android 및 iOS 기반 최신 인증 클라이언트에 대한 정책입니다.**

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹** 을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online** 을 선택합니다.
1. **조건** 에서 **디바이스 플랫폼** 을 선택합니다.
   1. **구성** 을 **예** 로 설정합니다.
   1. **Android** 및 **iOS** 를 포함합니다.
1. **조건** 에서 **클라이언트 앱(미리 보기)** 을 선택합니다.
   1. **구성** 을 **예** 로 설정합니다.
   1. **모바일 앱 및 데스크톱 클라이언트** 및 **최신 인증 클라이언트** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **승인된 클라이언트 앱 필요** 를 선택하고 **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 만들고 사용하도록 설정합니다.

**2단계: 승인된 클라이언트 앱을 사용해야 하는 Exchange ActiveSync 클라이언트에 대한 정책입니다.**

1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹** 을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **Office 365 Exchange Online** 을 선택합니다.
1. **조건** 에서:
   1. **클라이언트 앱(미리 보기)** :
      1. **구성** 을 **예** 로 설정합니다.
      1. **모바일 앱 및 데스크톱 클라이언트** 및 **Exchange ActiveSync 클라이언트** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **승인된 클라이언트 앱 필요** 를 선택하고 **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 만들고 사용하도록 설정합니다.

**3단계: iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies) 문서를 검토하세요. 

## <a name="next-steps"></a>다음 단계

[조건부 액세스란?](overview.md)

[조건부 액세스 구성 요소](concept-conditional-access-policies.md)

[일반 조건부 액세스 정책](concept-conditional-access-policy-common.md)
