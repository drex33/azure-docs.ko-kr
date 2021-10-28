---
title: 최전방 직원 관리 - Azure Active Directory
description: 내 직원 포털을 통해 제공되는 최전방 직원 관리 기능에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 26d7e967ceb0208d6e852db9946ac0c260751dd3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732573"
---
# <a name="frontline-worker-management"></a>최전방 직원 관리

최전방 직원 계정은 전 세계 인력의 80% 이상을 차지하고 있습니다. 그러나 최전방 직원들은 큰 규모, 빠른 회전율, 세분화된 프로세스로 인한 까다로운 작업을 좀 더 쉽게 처리할 수 있는 도구가 부족한 경우가 많습니다. 최전방 직원 관리는 디지털 트랜스포메이션을 통해 전체 최전방 인력의 다양한 요구 사항을 충족합니다. 인력에는 관리자, 최전방 직원, 작업, IT 등이 포함될 수 있습니다.

최전방 직원 관리는 다음과 같은 활동을 더 쉽게 수행하도록 지원함으로써 최전방 인력의 역량을 강화합니다.
- 내 직원을 통한 일반적인 IT 작업 간소화
- 간소화된 인증을 통해 최전방 직원의 간편한 온보딩
- 공유 디바이스의 원활한 프로비저닝 및 최전방 직원의 보안 로그아웃

## <a name="delegated-user-management-through-my-staff"></a>내 직원을 통해 위임된 사용자 관리

Azure AD(Azure Active Directory)는 [내 직원 포털](../roles/my-staff-configure.md)을 통해 최전방 관리자에게 사용자 관리를 위임하여 귀중한 시간을 절약하고 위험을 줄일 수 있는 기능을 제공합니다. 관리자는 상점이나 공장 바닥에서 직접 간단한 암호 재설정 및 휴대폰 관리를 사용할 수 있도록 설정하여 기술 지원팀, IT, 작업을 통해 요청을 라우팅하지 않고도 직원에게 액세스 권한을 부여할 수 있습니다.

![내 직원 포털에서 위임된 사용자 관리](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>간소화된 인증을 통해 가속화된 온보딩

또한 내 직원에서는 최전방 관리자가 [SMS 로그인](../authentication/howto-authentication-sms-signin.md)을 위해 팀 구성원의 전화 번호를 등록할 수 있습니다. 많은 계층에서 최전방 직원들은 대체로 번거롭고 비용이 많이 들고 오류가 발생하기 쉬운 솔루션인 로컬 사용자 이름과 암호 조합을 유지합니다. IT가 SMS 로그인을 사용하여 인증하도록 설정하면 최전방 직원은 Microsoft Teams용 [SSO(Single Sign-On)](../manage-apps/what-is-single-sign-on.md)와 전화 번호와 SMS를 통해 전송되는 OTP(일회성 암호)를 사용하는 기타 앱을 통해 로그인할 수 있습니다. 이를 통해 최전방 직원은 간단하고 안전하게 로그인하여 가장 필요한 앱에 신속하게 액세스할 수 있습니다.

![SMS 로그인](media/concept-fundamentals-frontline-worker/sms-signin.png)

또한 최전방 관리자는 MHS(Managed Home Screen) 애플리케이션을 사용하여 직원이 Intune에 등록된 Android 전용 디바이스에서 특정 애플리케이션 세트에 액세스할 수 있도록 합니다. 전용 디바이스는 [Azure AD 공유 디바이스 모드](../develop/msal-shared-devices.md)로 등록됩니다. MEM(Microsoft Endpoint Manager) 콘솔에서 다중 앱 키오스크 모드로 구성되면 MHS는 자동으로 디바이스의 기본 홈 화면으로 시작되고 최종 사용자에게 ‘전용’ 홈 화면으로 표시됩니다. 자세한 내용은 [Android Enterprise용 Microsoft Managed Home Screen 앱 구성](/mem/intune/apps/app-configuration-managed-home-screen-app) 방법을 참조하세요.

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>공유 디바이스에서 최전방 직원의 보안 로그아웃

많은 회사에서 공유 디바이스를 사용하므로 최전방 직원은 개별 디바이스를 프로비저닝하고 추적해야 하는 IT 부담 없이 재고 관리 및 판매 시점 트랜잭션을 수행할 수 있습니다. 공유 디바이스 로그아웃을 통해 최전방 직원이 공유 디바이스에 있는 모든 앱을 안전하게 로그아웃하여 허브로 다시 전달하거나 다음 교대 시 동료에게 쉽게 넘겨줄 수 있습니다. Microsoft Teams는 현재 공유 디바이스에서 지원되는 앱 중 하나이며, 이를 사용하면 최전방 직원이 자신에게 할당된 작업을 볼 수 있습니다. 직원이 공유 디바이스에서 로그아웃하면 Intune과 Azure AD가 회사 데이터를 모두 삭제하여 디바이스가 다음 동료에게 안전하게 전달될 수 있습니다. [Microsoft 인증 라이브러리](../develop/msal-overview.md)를 사용하여 이 기능을 [iOS](../develop/msal-ios-shared-devices.md) 및 [Android](../develop/msal-android-shared-devices.md)의 모든 LOB(기간 업무) 앱으로 통합하도록 선택할 수 있습니다.

![공유 디바이스 로그아웃](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>다음 단계

- 위임된 사용자 관리에 대한 자세한 내용은 [내 직원 사용자 설명서](https://support.microsoft.com/account-billing/manage-front-line-users-with-my-staff-c65b9673-7e1c-4ad6-812b-1a31ce4460bd)를 참조하세요.
- SAP SuccessFactors의 인바운드 사용자 프로비저닝에 대한 자세한 내용은 [SAP SuccessFactors에서 Active Directory 사용자 프로비저닝 구성](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) 자습서를 참조하세요.
- Workday의 인바운드 사용자 프로비저닝에 대한 자세한 내용은 [Workday에서 자동 사용자 프로비저닝 구성](../saas-apps/workday-inbound-tutorial.md) 자습서를 참조하세요.