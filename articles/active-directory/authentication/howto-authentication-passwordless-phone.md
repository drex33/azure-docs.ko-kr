---
title: Microsoft Authenticator 앱을 사용하여 암호 없는 로그인-Azure Active Directory
description: Microsoft Authenticator 앱을 사용하여 Azure AD에 대해 암호 없는 로그인 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/29/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e4a99967e8fdc64eb7d10c66649cac59db048a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456254"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱으로 암호 없는 로그인을 사용하도록 설정 

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. Microsoft Authenticator는 키 기반 인증을 사용하여 디바이스에 연결된 사용자 자격 증명을 사용하도록 설정합니다. 여기서 디바이스는 PIN 또는 생체 인식 기능을 사용합니다. [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)는 비슷한 기술을 사용합니다.

이 인증 기술은 모바일을 비롯한 모든 디바이스 플랫폼에서 사용할 수 있습니다. 이 기술은 Microsoft 인증 라이브러리와 통합된 모든 앱 또는 웹 사이트에서 사용할 수도 있습니다.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="사용자에게 로그인 동의를 요구하는 브라우저 로그인의 예.":::

Microsoft Authenticator 앱에서 휴대폰 로그인을 사용하도록 설정하는 사람은 앱에서 번호를 탭하도록 요청하는 메시지가 표시됩니다. 사용자 이름 또는 암호를 묻는 메시지가 표시되지 않습니다. 앱에서 로그인 프로세스를 완료하려면 사용자가 다음 작업을 수행해야 합니다.

1. 로그인 화면에 표시되는 숫자를 Microsoft Authenticator 앱 대화 상자에 입력합니다.
1. **승인** 을 선택합니다.
1. PIN 또는 생체 인식 기능을 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

Microsoft Authenticator 앱에서 암호 없는 휴대폰 로그인을 사용하려면 다음 사전 요구 사항을 충족해야 합니다.

- 권장: 푸시 알림이 확인 방법으로 허용되는 Azure AD Multi-Factor Authentication. 스마트폰 또는 태블릿에 알림을 푸시하여 Authenticator 앱에서 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지하도록 할 수 있습니다. Authenticator 앱은 푸시 알림을 수행하도록 설정된 경우 자동으로 코드를 생성하므로 디바이스가 연결되지 않은 경우에도 사용자에게 백업 로그인 방법이 있습니다. 
- iOS 8.0 이상 또는 Android 6.0 이상을 실행하는 디바이스에 설치된 최신 버전의 Microsoft Authenticator
- Microsoft Authenticator 앱이 설치된 디바이스를 Azure AD 테넌트 내에 개별 사용자에게 등록해야 한다는 것입니다. 

> [!NOTE]
> Azure AD PowerShell을 사용하여 암호 없는 로그인 Microsoft Authenticator 사용하도록 설정한 경우 전체 디렉터리에 대해 사용하도록 설정되었습니다. 이 새 메서드를 사용하도록 설정하면 PowerShell 정책에 우선합니다. 새 *인증 메서드* 메뉴를 통해 테넌트의 모든 사용자에 대해 사용하도록 설정하는 것이 좋습니다. 그렇지 않으면 새 정책에 없는 사용자는 더 이상 암호 없이 로그인 할 수 없습니다.

## <a name="enable-passwordless-authentication-methods"></a>암호 없는 인증 방법

Azure AD에서 암호 없는 인증을 사용하려면 먼저 결합된 등록 환경을 사용하도록 설정한 다음 사용자가 암호를 사용하지 않는 방법으로 사용하도록 설정합니다.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>암호 없는 휴대폰 로그인 인증 방법 사용

Azure AD를 사용하면 로그인 프로세스 중에 사용할 수 있는 인증 방법을 선택할 수 있습니다. 그런 다음 사용자가 사용하려는 메서드에 등록합니다. **Microsoft Authenticator** 인증 방법 정책은 기존의 푸시 MFA 방법과 암호 없는 인증 방법을 모두 관리합니다. 

암호 없는 휴대폰 로그인에 대한 인증 방법을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. *인증 정책 관리자* 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory* 를 검색하고 선택한 다음 **보안** > **인증 방법** > **정책** 으로 이동합니다.
1. **Microsoft Authenticator** 에서 다음 옵션을 선택합니다.
   1. **사용** - 예 또는 아니요
   1. **대상** - 모든 사용자 또는 사용자 선택
1. 추가된 각 그룹이나 사용자는 기본 값으로 암호 없는 및 푸시 알림 모드("Any" 모드) 모두에서 Microsoft Authenticator를 사용하도록 설정됩니다. 이를 변경하려면 각 행에 대해 다음을 수행합니다.
   1. **...**  > **구성** 으로 이동.
   1. **인증 모드** 의 경우 - **모두** 또는 **암호 없음** 을 선택합니다. **푸시** 를 선택하면 암호가 없는 전화 로그인 자격 증명을 사용할 수 없습니다. 
1. 새 정책을 적용하려면 **저장** 을 선택합니다.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>자세한 내용은 Microsoft Authenticator의 사용자 등록 및 관리를 참조하세요.

다음 단계를 사용하여 사용자는 Azure AD의 암호 없는 인증 방법으로 등록됩니다.

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 으로 이동합니다.
1. 로그인한 다음, **메서드 추가** > **Authenticator 앱** > **추가** 를 클릭하여 Authenticator 앱을 추가합니다.
1. 지침에 따라 디바이스에 Microsoft Authenticator 앱을 설치하고 구성합니다.
1. **완료** 를 선택하여 Authenticator 구성을 완료합니다.
1. **Microsoft Authenticator** 에서 등록된 계정에 대한 드롭다운 메뉴에서 **휴대폰 로그인 사용** 을 선택합니다.
1. 앱의 지침에 따라 암호 없는 휴대폰 로그인에 대한 계정 등록을 완료합니다.

조직에서는 암호를 사용하지 않고 사용자에게 전화를 통해 로그인하도록 지시할 수 있습니다. Microsoft Authenticator 앱을 구성하고 휴대폰 로그인을 사용하도록 설정하는 추가 지원이 필요한 경우 [Microsoft Authenticator 앱을 사용하여 계정에 로그인](https://support.microsoft.com/account-billing/sign-in-to-your-accounts-using-the-microsoft-authenticator-app-582bdc07-4566-4c97-a7aa-56058122714c)을 참조하세요.

> [!NOTE]
> 정책에서 휴대폰 로그인을 사용하도록 허용하지 않은 사용자는 더 이상 Microsoft Authenticator 앱 내에서 사용하도록 설정할 수 없습니다.

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명으로 로그인

사용자는 다음 작업을 모두 완료한 후 암호 없는 로그인을 사용하기 시작할 수 있습니다.

- 관리자가 사용자의 테넌트를 사용하도록 설정했습니다.
- 사용자가 휴대폰 로그인을 사용하도록 Microsoft Authenticator 앱을 업데이트 했습니다.

사용자가 휴대폰 로그인 프로세스를 처음 시작할 때 사용자는 다음 단계를 수행합니다.

1. 로그인 페이지에서 이름을 입력합니다.
2. **다음** 을 선택합니다.
3. 필요한 경우 **다른 로그인 방법** 을 선택합니다.
4. **내 Microsoft Authenticator 앱에서 요청 승인** 을 선택합니다.

그러면 사용자에게 숫자가 표시됩니다. 앱은 사용자에게 암호를 입력하는 대신 적절한 번호를 입력하여 인증하라는 메시지를 표시합니다.

사용자가 암호 없는 휴대폰 로그인을 사용한 후 앱은 이 방법을 통해 사용자를 계속 안내합니다. 그러나 사용자에게 다른 방법을 선택하는 옵션이 표시됩니다.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Microsoft Authenticator 앱을 사용한 브라우저 로그인의 예제입니다.":::

## <a name="known-issues"></a>알려진 문제

다음의 알려진 문제가 존재합니다.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>암호 없는 휴대폰 로그인에 대한 옵션을 표시하지 않음

한 가지 시나리오에서 사용자는 보류 중인 암호 없는 휴대폰 로그인을 확인할 수 있습니다. 그러나 사용자가 다시 로그인을 시도할 수 있습니다. 이 경우 사용자에게 암호를 입력하는 옵션만 표시될 수 있습니다.

이 시나리오를 해결하기 위해 다음 단계를 사용할 수 있습니다.

1. Microsoft Authenticator 앱을 엽니다.
2. 알림 메시지에 응답합니다.

그러면 사용자가 암호 없는 휴대폰 로그인을 계속 사용할 수 있습니다.

### <a name="federated-accounts"></a>페더레이션된 계정

사용자가 암호 없는 자격 증명을 사용하도록 설정하면 Azure AD 로그인 프로세스가 로그인\_ 힌트 사용을 중지합니다. 따라서 프로세스가 페더레이션 로그인 위치에 대해 더 이상 사용자를 가속화하지 않습니다.

이 논리는 일반적으로 하이브리드 테넌트의 사용자가 로그인 확인을 위해 AD FS(Active Directory 페더레이션된 서비스)로 전달되지 않도록 합니다. 그러나 사용자는 **대신 암호 사용** 을 클릭하는 옵션을 유지합니다.

### <a name="azure-mfa-server"></a>Azure MFA 서버

최종 사용자는 온-프레미스 Azure MFA 서버를 통해 MFA(다단계 인증)를 사용하도록 설정할 수 있습니다. 사용자는 여전히 단일 암호 없는 휴대폰 로그인 자격 증명을 만들고 활용할 수 있습니다.

사용자가 암호 없는 휴대폰 로그인 자격 증명으로 Microsoft Authenticator 앱의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.

### <a name="device-registration"></a>디바이스 등록

이 새로운 강력한 자격 증명을 만들기 전에 사전 요구 사항이 있습니다. 한 가지 사전 요구 사항은 Microsoft Authenticator 앱이 설치된 디바이스를 Azure AD 테넌트 내에 개별 사용자에게 등록해야 한다는 것입니다.

현재 디바이스는 단일 테넌트에만 등록될 수 있습니다. 이 제한은 Microsoft Authenticator 앱에서 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 설정할 수 있음을 의미합니다.

> [!NOTE]
> 디바이스 등록은 디바이스 관리 또는 MDM(모바일 디바이스 관리)과 다릅니다. 디바이스 등록은 Azure Active Directory에서 디바이스 ID와 사용자 ID만을 함께 연결합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 인증 및 암호 없는 메서드에 대해 알아보려면 다음 문서를 참조하세요.

- [암호 없는 인증의 작동 원리 알아보기](concept-authentication-passwordless.md)
- [디바이스 등록에 대한 자세한 정보](../devices/overview.md)
- [Azure AD 다단계 인증에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)
