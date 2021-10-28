---
title: Azure AD Multi-Factor Authentication에 대한 앱 암호 구성 - Azure Active Directory
description: Azure AD Multi-Factor Authentication에서 레거시 애플리케이션에 대한 앱 암호를 구성하고 사용하는 방법 알아보기
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f79bca8626eca56c40f99f75daa2b8cb4da3a995
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773781"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>앱 암호를 사용하여 레거시 애플리케이션에서 Azure AD Multi-Factor Authentication 사용 설정 및 사용

Office 2010 또는 이전 버전과 iOS 11 이전의 Apple Mail과 같이 일부 오래된 비 브라우저 앱은 인증 프로세스의 일시 중지 또는 중단을 이해하지 못합니다. 사용자가 Azure AD Multi-Factor Authentication을 사용하도록 설정되어 있고 이러한 오래된 비 브라우저 앱 중 하나를 사용하려고 시도하면 성공적으로 인증할 수 없습니다. 사용자 계정에 대해 사용하도록 설정된 Azure AD Multi-Factor Authentication을 사용하여 해당 애플리케이션을 안전한 방식으로 사용하려면 앱 암호를 사용할 수 있습니다. 해당 앱 암호는 기존 암호를 대체하여 앱이 다단계 인증을 바이패스하고 올바르게 작동할 수 있도록 합니다.

Microsoft Office 2013 클라이언트 이상에 대해 최신 인증이 지원됩니다. Outlook을 포함한 Office 2013 클라이언트는 최신 인증 프로토콜을 지원하고 2단계 인증과 함께 사용할 수 있습니다. 클라이언트가 사용하도록 설정된 후에는 클라이언트에 대해 앱 암호가 필요하지 않습니다.

이 문서에서는 다단계 인증 프롬프트를 지원하지 않는 레거시 애플리케이션에 대해 앱 암호를 사용하도록 설정하고 사용하는 방법을 보여줍니다.

>[!NOTE]
> 앱 암호는 조건부 액세스 기반 Multi-Factor Authentication 정책 및 최신 인증과 작동하지 않습니다.

## <a name="overview-and-considerations"></a>개요 및 고려 사항

Azure AD Multi-Factor Authentication에 대해 사용자 계정을 사용하도록 설정하면 추가 확인 요청에 의해 일반 로그인 프롬프트가 중단됩니다. 일부 이전 애플리케이션은 로그인 프로세스에서 이러한 중단을 이해하지 못하므로 인증에 실패하게 됩니다. 사용자 계정의 보안을 유지하고 Azure AD Multi-Factor Authentication을 사용하도록 설정된 상태로 유지하려면 사용자의 일반 사용자 이름 및 암호 대신 앱 암호를 사용할 수 있습니다. 로그인 시 앱 암호를 사용하면 추가 확인 메시지가 표시되지 않으므로 인증에 성공합니다.

앱 암호는 자동으로 생성되며 사용자가 지정하지 않습니다. 자동으로 생성된 암호는 공격자가 추측하기 어렵기 때문에 더 안전합니다. 앱 암호를 애플리케이션당 한 번만 입력하면 암호를 추적하거나 매번 입력할 필요가 없습니다.

앱 암호를 사용할 때 다음 고려 사항이 적용됩니다:

* 사용자당 40개의 앱 암호로 제한되어 있습니다.
* 앱 암호는 회사 또는 학교 계정 외부에 알려지지 않기 때문에 암호를 캐시하고 온-프레미스 시나리오에서 사용하는 애플리케이션은 실패할 수 있습니다. 이러한 시나리오의 예로 온-프레미스에 있지만 보관된 메일은 클라우드에 있는 Exchange 전자 메일이 있습니다. 이 시나리오에서 동일한 암호는 작동하지 않습니다.
* 사용자 계정에서 Azure AD Multi-Factor Authentication을 사용하도록 설정하면 Outlook 및 비즈니스용 Microsoft Skype와 같은 대부분의 비 브라우저 클라이언트에서 앱 암호를 사용할 수 있습니다. 그러나 Windows PowerShell과 같은 비 브라우저 애플리케이션을 통해 앱 암호를 사용하여 관리 작업을 수행할 수 없습니다. 이 작업은 사용자가 관리자 계정을 가지고 있는 경우에도 수행할 수 없습니다.
    * PowerShell 스크립트를 실행하려면 강력한 암호로 서비스 계정을 만들고 해당 계정에 2단계 인증을 사용하도록 설정하지 않습니다.
* 사용자 계정이 손상된 것으로 의심되고 계정 암호를 취소/초기화하는 경우 앱 암호도 업데이트해야 합니다. 사용자 계정 암호가 취소/초기화될 때 앱 암호는 자동으로 취소되지 않습니다. 사용자는 기존 앱 암호를 삭제하고 새 암호를 만들어야 합니다.
   * 자세한 내용은 [추가 보안 확인 페이지에서 앱 암호 생성 및 삭제](https://support.microsoft.com/account-billing/manage-app-passwords-for-two-step-verification-d6dc8c6d-4bf7-4851-ad95-6d07799387e9#create-and-delete-app-passwords-from-the-additional-security-verification-page)를 참조하세요.

>[!WARNING]
> 앱 암호는 클라이언트가 온-프레미스 및 클라우드 자동 검색 엔드포인트와 통신하는 하이브리드 환경에서는 작동하지 않습니다. 온-프레미스를 인증하려면 도메인 암호가 필요합니다. 클라우드를 통해 인증하려면 앱 암호가 필요합니다.

### <a name="app-password-names"></a>앱 암호 이름

앱 암호 이름은 해당 이름이 사용될 디바이스를 반영해야 합니다. Outlook, Word 및 Excel 등의 비 브라우저 애플리케이션이 있는 노트북을 가지고 있는 경우 이러한 앱에 대해 **Laptop** 이라는 하나의 앱 암호를 만듭니다. 그런 다음, 데스크톱 컴퓨터에서 실행하는 동일한 애플리케이션에 대해 **Desktop** 이라는 다른 앱 암호를 만듭니다.

애플리케이션별로 하나의 앱 암호보다는 디바이스별로 하나의 앱 암호를 만드는 것을 권장합니다.

## <a name="federated-or-single-sign-on-app-passwords"></a>페더레이션된 또는 Single Sign-On 앱 암호

Azure AD는 온-프레미스 AD DS(Active Directory Domain Services)를 통해 페더레이션 또는 SSO(Single Sign-On)를 지원합니다. 조직이 Azure AD와 페더레이션되고 Azure AD Multi-Factor Authentication을 사용하는 경우 앱 암호에 대한 다음 고려 사항이 적용됩니다:

>[!NOTE]
> 다음 사항은 페더레이션된(SSO) 고객에게만 적용됩니다.

* Azure AD에서 앱 암호를 확인하기 때문에 페더레이션을 바이패스합니다. 앱 암호를 설정할 때만 페더레이션이 능동적으로 사용됩니다.
* 페더레이션된(SSO) 사용자의 경우 수동 흐름과 달리 ID 공급자(IdP)에 연결되지 않습니다. 앱 암호는 회사 또는 학교 계정에 저장됩니다. 사용자가 회사에서 떠나는 경우 사용자의 정보는 **DirSync** 를 사용하여 실시간으로 회사 또는 학교 계정으로 흐릅니다. 계정 비활성화/삭제는 동기화하는 데 최대 3시간이 걸릴 수 있으며, 이에 따라 Azure AD에서 앱 암호 비활성화/삭제가 지연될 수 있습니다.
* 앱 암호 기능을 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 앱 암호 기능에는 온-프레미스 인증 로깅 또는 감사 기능을 사용할 수 없습니다.

일부 고급 아키텍처에서는 클라이언트를 다단계 인증하려면 자격 증명 조합이 필요합니다. 이러한 자격 증명은 회사 또는 학교 계정 사용자 이름과 암호 및 앱 암호를 포함할 수 있습니다. 요구 사항은 인증이 수행되는 방법에 따라 달라집니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 회사 또는 학교 계정의 사용자 이름과 암호가 필요합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호가 필요합니다.

예를 들어 다음과 같은 아키텍처가 있다고 가정하겠습니다.

* Azure AD를 사용하여 Active Directory의 온-프레미스 인스턴스를 페더레이션합니다.
* 온라인으로 Exchange를 사용합니다.
* 온-프레미스로 Skype for Business를 사용합니다.
* Azure AD Multi-Factor Authentication을 사용합니다.

이 시나리오에서는 다음과 같은 자격 증명을 사용합니다.

* Skype for Business에 로그인하려면 회사 또는 학교 계정 사용자 이름 및 암호를 사용합니다.
* 온라인으로 Exchange에 연결하는 Outlook 클라이언트에서 주소록에 액세스하려면 앱 암호를 사용합니다.

## <a name="allow-users-to-create-app-passwords"></a>사용자가 앱 암호를 만들 수 있음

기본적으로 사용자가 앱 암호를 만들 수 없습니다. 앱 암호 기능은 사용자가 사용하기 전에 활성화되어야 합니다. 사용자에게 앱 암호를 생성할 수 있는 기능을 제공하려면 **관리자** 가 다음 단계를 완료해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** 를 검색하여 선택한 후 **사용자** 를 선택합니다.
3. *사용자* 창 위의 탐색 표시줄에서 **다단계 인증** 을 선택합니다.
4. Multi-Factor Authentication 섹션 아래에서 **서비스 설정** 을 선택합니다.
5. **서비스 설정** 페이지에서 **사용자가 비 브라우저 앱에 로그인하기 위해 앱 암호를 만들 수 있음** 옵션을 선택합니다.

    ![다단계 인증에 앱 암호의 사용자를 허용하는 서비스 설정을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> 사용자가 앱 암호를 생성하는 기능을 사용하지 않도록 설정하면 기존 앱 암호가 계속 작동합니다. 그러나 이 기능을 사용하지 않도록 설정하면 사용자가 기존 앱 암호를 관리하거나 삭제할 수 없습니다.
>
> 앱 암호 생성 기능을 사용하지 않도록 설정하는 경우 [레거시 인증 사용을 비활성화하는 조건부 액세스 정책을 생성](../conditional-access/block-legacy-authentication.md)하는 것을 권장합니다. 이 방법은 기존 앱 암호가 작동하지 않도록 하고 최신 인증 방법을 강제로 사용하게 합니다.

## <a name="create-an-app-password"></a>앱 암호 만들기

사용자가 Azure AD Multi-Factor Authentication의 초기 등록을 완료하면 등록 프로세스의 마지막에 앱 암호를 만드는 옵션이 있습니다.

사용자는 등록 후 앱 암호를 만들 수도 있습니다. 사용자에 대한 자세한 내용 및 자세한 단계는 다음 리소스를 참조하세요.
* [Azure AD Multi-Factor Authentication의 앱 암호는 무엇인가요?](https://support.microsoft.com/account-billing/manage-app-passwords-for-two-step-verification-d6dc8c6d-4bf7-4851-ad95-6d07799387e9)
* [보안 정보 페이지에서 앱 암호 만들기](https://support.microsoft.com/account-billing/create-app-passwords-from-the-security-info-preview-page-d8bc744a-ce3f-4d4d-89c9-eb38ab9d4137)

## <a name="next-steps"></a>다음 단계

사용자가 Azure AD Multi-Factor Authentication에 빠르게 등록할 수 있도록 하는 방법에 대한 자세한 내용은 [결합된 보안 정보 등록 개요](concept-registration-mfa-sspr-combined.md)를 참조하세요.