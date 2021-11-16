---
title: Android 디바이스의 공유 디바이스 모드
titleSuffix: Microsoft identity platform | Azure
description: 일선 직원이 Android 디바이스를 공유할 수 있도록 공유 디바이스 모드를 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2021
ms.author: marsma
ms.reviewer: brandwe
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: fa55cf74ce8dc1de2782d748e770d7770057ab33
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227700"
---
# <a name="shared-device-mode-for-android-devices"></a>Android 디바이스의 공유 디바이스 모드

소매 연결, 비행 승무원 및 현장 서비스 작업자 같은 일선 직원은 공유 모바일 디바이스를 사용해 업무를 수행하는 경우가 많습니다. 공유 디바이스에서 고객/비즈니스 데이터에 액세스하기 위해 암호 또는 pin 번호를 공유하기 시작하면 문제가 됩니다.

공유 디바이스 모드를 사용하면 여러 직원이 쉽게 공유할 수 있도록 Android 디바이스를 구성할 수 있습니다. 직원은 로그인하여 고객 정보에 빠르게 액세스할 수 있습니다. 직원이 교대 근무 또는 작업을 마치면 공유 디바이스에서 로그아웃하여 다음 직원이 이 디바이스를 즉시 사용할 수 있도록 준비됩니다.

또한 공유 디바이스 모드는 디바이스의 Microsoft ID 지원 관리 기능을 제공 합니다.

공유 디바이스 모드 앱을 만들기 위해 개발자와 클라우드 디바이스 관리자는 다음과 같이 협업합니다.

- 개발자는 단일 계정 앱(여러 계정 앱은 공유 디바이스 모드에서 지원되지 않음)을 작성하고, 앱의 구성에 `"shared_device_mode_supported": true`를 추가하며, 공유 디바이스 로그아웃 등의 작업을 처리하는 코드를 작성합니다.
- 디바이스 관리자는 인증자 앱을 설치하고, 인증자 앱을 사용해 디바이스를 공유 모드로 설정하여 공유할 디바이스를 준비합니다. [클라우드 디바이스 관리자](../roles/permissions-reference.md#cloud-device-administrator) 역할에 속한 사용자만 [인증자 앱](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)을 사용하여 디바이스를 공유 모드로 전환할 수 있습니다. **Azure Active Directory** > **역할 및 관리자** > **클라우드 디바이스 관리자** 를 통해 Azure Portal에서 조직 역할의 멤버 자격을 구성할 수 있습니다.

이 문서에서는 개발자가 주로 고려해야 할 사항을 중점적으로 설명합니다.

## <a name="single-vs-multiple-account-applications"></a>단일 및 여러 계정 애플리케이션

MSAL(Microsoft Authentication Library) SDK를 사용하여 작성된 애플리케이션은 단일 계정 또는 여러 계정을 관리할 수 있습니다. 자세한 내용은 [단일 계정 모드 또는 여러 계정 모드](single-multi-account.md)를 참조하세요.

앱에서 사용할 수 있는 Microsoft ID 플랫폼 기능은 애플리케이션이 단일 계정 모드로 실행되는지, 여러 계정 모드로 실행되는지에 따라 달라집니다.

**공유 디바이스 모드 앱은 단일 계정 모드에서만 작동합니다**.

> [!IMPORTANT]
> 여러 계정 모드만 지원하는 애플리케이션은 공유 디바이스에서 실행할 수 없습니다. 직원이 단일 계정 모드를 지원하지 않는 앱을 로드하는 경우, 이 앱은 공유 디바이스에서 실행되지 않습니다.
>
> MSAL SDK가 출시되기 전에 작성된 앱은 여러 계정 모드로 실행되며, 단일 계정 모드를 지원하도록 업데이트해야 공유 모드 디바이스를 실행할 수 있습니다.

**단일 계정과 여러 계정 모두 지원**

개인 디바이스와 공유 디바이스에서 모두 실행을 지원하도록 앱을 빌드할 수 있습니다. 앱에서 현재 여러 계정을 지원하고 있고 공유 디바이스 모드를 지원하려는 경우, 단일 계정 모드 지원 기능을 추가합니다.

앱이 실행 중인 디바이스 유형에 따라 동작을 변경하도록 할 수도 있습니다. `ISingleAccountPublicClientApplication.isSharedDevice()`을(를) 사용하여 단일 계정 모드로 실행할 경우를 결정합니다.

애플리케이션이 있는 디바이스 유형을 나타내는 인터페이스는 두 가지가 있습니다. MSAL의 애플리케이션 팩터리에서 애플리케이션 인스턴스를 요청하면 올바른 애플리케이션 개체가 자동으로 제공됩니다.

다음 개체 모델은 사용자가 받을 수 있는 개체의 유형과 공유 디바이스의 컨텍스트에서 의미하는 바를 나타냅니다.

![퍼블릭 클라이언트 애플리케이션 상속 모델](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

`PublicClientApplication` 개체를 가져올 때 형식을 검사하고 적절한 인터페이스로 캐스팅해야 합니다. 다음 코드는 여러 계정 모드인지, 단일 계정 모드인지를 확인하고 애플리케이션 개체를 적절하게 캐스팅합니다.

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

앱이 공유 디바이스에서 실행되는지, 개인 디바이스에서 실행되는지에 따라 다음과 같은 차이점이 적용됩니다.

|                             | 공유 모드 디바이스 | 개인 디바이스                                                                                     |
| --------------------------- | ------------------ | --------------------------------------------------------------------------------------------------- |
| **계정**                | 단일 계정     | 여러 계정                                                                                   |
| **로그인**                 | 전역             | 전역                                                                                              |
| **로그아웃**                | Global             | 각 애플리케이션은 로그아웃이 앱 또는 애플리케이션 제품군에 대해 로컬인지 여부를 제어할 수 있습니다. |
| **지원되는 계정 유형** | 회사 계정 전용 | 개인/회사 계정 지원                                                                |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>단일 계정 모드만 지원해야 하는 이유

공유 디바이스를 통해 일선 직원에게만 사용되는 앱을 작성하는 경우 단일 계정 모드만 지원하도록 애플리케이션을 작성하는 것이 좋습니다. 여기에는 의료 기록 앱, 청구서 앱 및 대부분의 LOB(기간 업무) 앱과 같이 작업에 중점을 둔 대부분의 애플리케이션이 포함됩니다. 여러 계정 앱에 포함된 추가 기능을 구현할 필요가 없으므로, 단일 계정 모드만 지원하면 개발 작업이 간단해집니다.

## <a name="what-happens-when-the-device-mode-changes"></a>디바이스 모드가 변경되면 발생하는 일

애플리케이션이 여러 계정 모드로 실행되고 있고 관리자가 디바이스를 공유 디바이스 모드로 전환하는 경우, 디바이스의 모든 계정이 애플리케이션에서 지워지고 애플리케이션이 단일 계정 모드로 전환됩니다.

## <a name="microsoft-applications-that-support-shared-device-mode"></a>공유 디바이스 모드를 지원하는 Microsoft 애플리케이션

다음 Microsoft 애플리케이션은 Azure AD의 공유 디바이스 모드를 지원합니다.

* [Microsoft 팀](/microsoftteams/platform/)
* Android Enterprise용 [Microsoft 관리 홈 화면](/mem/intune/apps/app-configuration-managed-home-screen-app) 앱
## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>공유 디바이스 로그아웃 및 전체 앱 수명 주기

사용자가 로그아웃하면 사용자의 개인 정보와 데이터를 보호하기 위한 조치를 취해야 합니다. 예를 들어 의료 레코드 앱을 빌드하는 경우 사용자가 로그아웃할 때 이전에 표시된 환자 레코드가 지워지는지 확인하는 것이 좋습니다. 개인 정보 보호를 위해 애플리케이션을 준비하고 포그라운드에 들어갈 때마다 확인해야 합니다.

앱에서 MSAL을 사용하여 공유 모드에 있는 디바이스에서 실행 중인 앱의 사용자를 로그아웃하면, 로그인된 계정과 캐시된 토큰이 앱과 디바이스에서 모두 제거됩니다.

다음 다이어그램에는 앱이 실행되는 동안 발생할 수 있는 전체 앱 수명 주기와 일반적인 이벤트가 나와 있습니다. 다이어그램은 활동이 시작된 시간부터 계정 로그인/로그아웃 여부 그리고 활동 일시 중지, 다시 시작, 중지 같은 이벤트가 적용되는 방법을 보여 줍니다.

![공유 디바이스 앱 수명 주기](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>다음 단계

Android 디바이스의 공유 모드에서 일선 작업자 앱을 실행하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [Android 애플리케이션에서 공유 디바이스 모드 사용](tutorial-v2-shared-device-mode.md)
