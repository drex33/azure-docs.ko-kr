---
title: 공유 디바이스 모드 개요
titleSuffix: Microsoft identity platform | Azure
description: 최전방 직원들이 디바이스를 공유할 수 있게 해주는 공유 디바이스 모드에 대해 알아봅니다.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6a6d66be619808ae7a6997966aef32e89b0b2226
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786886"
---
# <a name="overview-of-shared-device-mode"></a>공유 디바이스 모드 개요

공유 디바이스 모드는 최전방 직원을 지원하는 애플리케이션을 빌드하고 최전방 직원에게 배포된 디바이스에서 공유 디바이스 모드를 설정하는 데 사용할 수 있는 Azure Active Directory의 기능입니다.

>[!IMPORTANT]
> iOS용 공유 디바이스 모드 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>최전방 직원이란 무엇인가요?

최전방 직원은 소매 직원, 유지 관리 및 현장 직원, 의료 담당자, 이밖에 컴퓨터 앞에 있지 않거나 회사 공동 작업을 위해 이메일을 사용하는 사용자입니다. 다음 섹션에서는 최전방 직원 지원과 관련된 측면 및 문제를 살펴보고 조직의 최전방 직원이 사용할 애플리케이션을 구현할 수 있도록 Microsoft에서 제공하는 기능에 대해 소개합니다.

### <a name="challenges-of-supporting-frontline-workers"></a>최전방 직원 지원 문제

최전방 직원 워크플로를 구현하는 경우 일반적인 정보 근로자와는 다른 문제가 노출됩니다. 이러한 문제에는 높은 이직율, 조직의 핵심 생산성 도구에 대한 숙련도 부족 등이 있습니다. 최전방 직원의 역량을 강화하기 위해 조직에서는 다양한 전략을 채택하고 있습니다. 직원이 개인 휴대폰에서 비즈니스 앱을 사용하는 BYOD(Bring-Your-Own-Device) 전략을 채택하는 조직이 있는가 하면, iPad 또는 Android 태블릿과 같은 공유 디바이스를 직원에게 제공하는 조직도 있습니다.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>단일 사용자용으로 설계된 디바이스에서 여러 사용자 지원

iOS 또는 Android를 실행하는 모바일 디바이스는 단일 사용자용으로 설계되었기 때문에 대부분의 애플리케이션은 단일 사용자가 사용하는 환경을 최적화합니다. 이러한 최적화된 환경의 일부는 전체 애플리케이션에서 Single Sign-On을 사용하고 사용자가 디바이스에 로그인된 상태를 유지하는 것입니다. 사용자가 애플리케이션에서 계정을 제거하는 경우 일반적으로 앱은 보안 관련 이벤트를 고려하지 않습니다. 많은 앱이 빠른 로그인을 위해 사용자의 자격 증명을 유지합니다. 여러분도 모바일 디바이스에서 애플리케이션을 삭제했다가 다시 설치하여 로그인하니까 로그인 상태가 유지된 것을 경험한 적이 있을 것입니다.

### <a name="global-sign-in-and-sign-out-sso"></a>전역 로그인 및 로그 아웃(SSO)

조직의 직원이 직원들이 공유하는 디바이스 풀에서 앱을 사용할 수 있도록 하려면 개발자는 이와 반대되는 환경을 구현해야 합니다. 직원은 풀에서 디바이스를 선택하고, 단일 제스처를 수행하여 근무 시간 동안 '자신의 디바이스'로 만들 수 있어야 합니다. 근무 시간이 끝나면 다른 제스처를 수행하여 디바이스 전체에서 로그아웃하고 모든 개인 및 회사 정보가 제거된 상태로 디바이스를 풀로 돌려보낼 수 있습니다. 또한 직원이 로그아웃하는 것을 잊은 경우에는 해당 디바이스는 근무 시간 끝이나 일정 시간 비활성 후에 자동으로 로그아웃됩니다.

Azure Active Directory는 **공유 디바이스 모드** 라는 기능을 사용하여 이러한 시나리오를 가능하게 합니다.

## <a name="introducing-shared-device-mode"></a>공유 디바이스 모드 소개

앞서 언급했듯이 공유 디바이스 모드는 다음 작업을 지원하는 Azure Active Directory의 기능입니다.

* 최전방 직원을 지원하는 애플리케이션 빌드
* 최전방 직원에게 디바이스 배포 및 공유 디바이스 모드 설정

### <a name="build-applications-that-support-frontline-workers"></a>최전방 직원을 지원하는 애플리케이션 빌드

MSAL(Microsoft 인증 라이브러리) 및 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 사용하여 '공유 디바이스 모드'라는 디바이스 상태를 설정하여 애플리케이션에서 최전방 직원을 지원할 수 있습니다. 디바이스가 공유 디바이스 모드에 있는 경우 Microsoft는 디바이스에서 사용자의 상태에 따라 해당 동작을 수정하여 사용자 데이터를 보호할 수 있는 정보를 애플리케이션에 제공합니다.

지원되는 기능:

* 지원되는 애플리케이션을 통해 **사용자 디바이스 전체에 로그인** 합니다.
* 지원되는 애플리케이션을 통해 **사용자 디바이스 전체에 로그아웃** 합니다.
* **디바이스의 상태를 쿼리** 하여 애플리케이션이 공유 디바이스 모드인지 확인합니다.
* **디바이스에서 사용자의 상태를 쿼리** 하여 애플리케이션을 마지막으로 사용한 이후 변경 내용이 있는지 확인합니다.

공유 디바이스 모드 지원은 애플리케이션에 대한 보안 향상 및 기능 업그레이드를 모두 고려해야 하며, 의료 및 재무와 같은 규제가 엄격한 환경에서 도입을 촉진할 수 있습니다.

한 사용자의 데이터가 다른 사용자에게 유출되지 않도록 하는 것은 개발자인 여러분에게 달렸습니다. 공유 디바이스 모드는 애플리케이션에 관리해야 하는 변경 내용이 발생했음을 나타내는 데 유용한 신호를 제공합니다. 여러분의 애플리케이션은 앱이 사용될 때마다 디바이스에서 사용자의 상태를 확인하여 이전 사용자의 데이터를 지우는 역할을 담당합니다. 이는 멀티태스킹 중 백그라운드에서 다시 로드되는 경우를 포함합니다. 사용자가 바뀌는 경우 이전 사용자의 데이터를 모두 지우고 애플리케이션에 표시되는 캐시된 데이터가 모두 제거되었는지 확인해야 합니다. 앱에 공유 디바이스 모드 기능을 추가한 후에는 항상 철저한 보안 검토 프로세스를 수행하는 것이 좋습니다.

공유 디바이스 모드를 지원하도록 애플리케이션을 수정하는 방법에 대한 자세한 내용은 이 문서의 끝에 있는 [다음 단계](#next-steps) 섹션을 참조하세요.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>최전방 직원에게 디바이스 배포 및 공유 디바이스 모드 설정

애플리케이션에서 공유 디바이스 모드를 지원하고 필요한 데이터 및 보안 변경 내용을 포함한 후에는 최전방 직원이 사용할 수 있는 것으로 알릴 수 있습니다.

조직의 디바이스 관리자는 Microsoft Intune와 같은 MDM(모바일 디바이스 관리) 솔루션을 통해 디바이스와 애플리케이션을 저장소 및 작업 영역에 배포할 수 있습니다. 프로비전 프로세스의 일부로 디바이스를 '공유 디바이스'로 표시하고 있습니다. 관리자는 [Microsoft Authenticator 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 배포하고 구성 매개 변수를 통해 공유 디바이스 모드를 설정하여 공유 디바이스 모드를 구성합니다. 이러한 단계를 수행한 후에는 공유 디바이스 모드를 지원하는 모든 애플리케이션에서 Microsoft Authenticator 애플리케이션을 사용하여 사용자 상태를 관리하고 디바이스와 조직의 보안 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

공유 디바이스 모드에서 iOS 및 Android 플랫폼을 지원합니다. 애플리케이션에서 최전방 직원 지원을 시작하려면 아래 설명서를 검토하세요.

* [iOS의 공유 디바이스 모드 지원](msal-ios-shared-devices.md)
* [Android의 공유 디바이스 모드 지원](msal-android-shared-devices.md)