---
title: Azure AD에서 레거시 인증 프로토콜 차단
description: 조직에서 레거시 인증 프로토콜을 차단하는 방법 및 이유 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f87c7f008a7ef004ec109129f440eede0f1cc25a
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080039"
---
# <a name="blocking-legacy-authentication"></a>레거시 인증 차단
 
사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 레거시 인증은 다음을 통해 수행된 인증 요청을 나타내는 용어입니다.

- 최신 인증을 사용하지 않는 이전 Office 클라이언트(Office 2010 클라이언트 등)
- IMAP, SMTP, POP3 등의 레거시 메일 프로토콜을 사용하는 모든 클라이언트

현재 대부분의 손상된 로그인 시도는 레거시 인증에서 발생합니다. 레거시 인증은 MFA(다단계 인증)를 지원하지 않습니다. 디렉터리에서 MFA 정책을 사용하는 경우에도 잘못된 작업자가 레거시 프로토콜을 사용하여 인증하고 MFA를 바이패스할 수 있습니다. 레거시 프로토콜에서 만든 악의적인 인증 요청으로부터 계정을 보호하는 가장 좋은 방법은 이러한 시도를 모두 차단하는 것입니다.

## <a name="identify-legacy-authentication-use"></a>레거시 인증 사용 확인

디렉터리에서 레거시 인증을 차단하려면 먼저 사용자에게 레거시 인증을 사용하는 앱이 있는지, 이로 인해 전체 디렉터리에 어떤 영향을 미치는지 이해해야 합니다. Azure AD 로그인 로그를 사용하여 레거시 인증을 사용하고 있는지 파악할 수 있습니다.

1. **Azure Portal** > **Azure Active Directory** > **로그인** 으로 이동합니다.
1.  **열** > **클라이언트 앱** 을 클릭하여 표시되지 않는 경우 **클라이언트 앱** 열을 추가합니다.
1. **클라이언트 앱** 별로 필터링 > 제공된 모든 **레거시 인증 클라이언트** 옵션을 확인합니다.
1. **상태** > **성공** 으로 필터링합니다. 
1. 필요한 경우 **날짜** 필터를 사용하여 날짜 범위를 확장합니다.
1. [새 로그인 작업 보고서 미리 보기](../reports-monitoring/concept-all-sign-ins.md)를 활성화한 경우 **사용자 로그인(비 대화형)** 탭에서도 위의 단계를 반복합니다.

필터링은 선택한 레거시 인증 프로토콜에서 수행한 성공적인 로그인 시도만 표시합니다. 각 개별 로그인 시도를 클릭하면 추가 세부 정보가 표시됩니다. 개별 데이터 행을 선택한 후 기본 정보 탭 아래의 클라이언트 앱 열 또는 클라이언트 앱 필드에는 사용된 레거시 인증 프로토콜이 표시됩니다. 이러한 로그에는 레거시 인증에 여전히 의존하는 사용자와 레거시 프로토콜을 사용하여 인증 요청을 수행하는 애플리케이션이 표시됩니다. 이러한 로그에 표시되지 않고 레거시 인증을 사용하지 않도록 확인된 사용자의 경우에만 이러한 사용자 전용 조건부 액세스 정책을 구현하거나 기준 정책을 사용합니다. 이러한 사용자에 대해서만 레거시 인증을 차단합니다.

## <a name="moving-away-from-legacy-authentication"></a>레거시 인증에서 벗어나기 

디렉터리에서 레거시 인증을 사용하는 사용자와 해당 인증에 의존하는 애플리케이션을 더 잘 알게 되면 다음 단계는 최신 인증을 사용하도록 사용자를 업그레이드하는 것입니다. 최신 인증은 보다 안전한 사용자 인증 및 권한 부여를 제공하는 ID 관리 방법입니다. 디렉터리에 MFA 정책이 있는 경우 최신 인증을 사용하면 필요한 경우 사용자에게 MFA를 묻는 메시지가 표시됩니다. 레거시 인증 프로토콜에 대한 보다 안전한 대안입니다.

이 섹션에서는 환경을 최신 인증으로 업데이트하는 방법에 대한 단계별 개요를 제공합니다. 조직에서 레거시 인증 차단 정책을 사용하기 전에 아래 단계를 읽어보세요.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1단계: 디렉터리에서 최신 인증 사용

최신 인증을 사용하도록 설정하는 첫 단계는 디렉터리에서 최신 인증을 지원하는지 확인하는 것입니다. 최신 인증은 2017년 8월 1일 또는 그 이후에 생성된 디렉터리에 대해 기본값으로 사용하도록 설정됩니다. 이 날짜 이전에 디렉터리가 만들어진 경우, 디렉터리에 대한 최신 인증을 사용하도록 다음 단계에 따라 수동으로 설정해야 합니다.

1. 디렉터리가  [비즈니스용 Skype 온라인 PowerShell 모듈](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)에서  `Get-CsOAuthConfiguration` 를 실행하여 디렉터리에서 최신 인증을 이미 지원하는지 확인합니다.
1. 명령에서 빈 `OAuthServers` 속성을 반환하는 경우 최신 인증을 사용할 수 없습니다.  `Set-CsOAuthConfiguration`을 사용하여 최신 인증을 사용 하도록 설정을 업데이트합니다.  `OAuthServers` 속성이 항목을 포함하는 경우 진행해도 좋습니다.

계속 진행하기 전에 이 단계를 완료해야 합니다. 디렉터리 구성은 모든 Office 클라이언트에서 사용할 프로토콜을 결정하기 때문에 디렉터리 구성을 먼저 변경하는 것이 중요합니다. 최신 인증을 지원하는 Office 클라이언트를 사용하는 경우에도 디렉터리에서 최신 인증을 사용 하지 않도록 설정하면 레거시 프로토콜이 기본적으로 사용 됩니다.

### <a name="step-2-office-applications"></a>2 단계: Office 애플리케이션

디렉터리에서 최신 인증을 사용 하도록 설정한 후에는 Office 클라이언트에 대한 최신 인증을 사용하도록 설정하여 애플리케이션 업데이트를 시작할 수 있습니다. Office 2016 이상 클라이언트는 기본값으로 최신 인증을 지원합니다. 추가 단계가 필요하지 않습니다.

Office 2013 Windows 클라이언트 또는 이전 버전을 사용하는 경우 Office 2016 이상으로 업그레이드 하는 것을 권장합니다. 디렉터리에서 최신 인증을 사용하도록 설정하는 이전 단계를 완료한 후에도 이전 Office 애플리케이션은 계속 레거시 인증 프로토콜을 사용합니다. Office 2013 클라이언트를 사용하고 있으며 Office 2016 이상으로 즉시 업그레이드할 수 없는 경우  [Windows 장치에서 Office 2013에 대한 최신 인증을 사용하도록 설정](/office365/admin/security-and-compliance/enable-modern-authentication)하려면 다음 문서의 단계를 수행합니다. 레거시 인증을 사용하는 동안 계정을 보호하려면 디렉터리에서 강력한 암호를 사용하는 것이 좋습니다. 디렉터리에서 약한 암호를 금지 하려면 [Azure AD 암호 보호](../authentication/concept-password-ban-bad.md) 를 확인하세요.

Office 2010은 최신 인증을 지원하지 않습니다. Office 2010를 사용하는 모든 사용자를 최신 버전의 Office로 업그레이드 해야 합니다. Office 2016 이상 버전은 기본값으로 레거시 인증을 차단하기 때문에 업그레이드 하는 것이 좋습니다.

macOS를 사용하는 경우 Mac용 Office 2016 이상으로 업그레이드 하는 것이 좋습니다. 네이티브 메일 클라이언트를 사용하는 경우 모든 디바이스에 macOS 버전 10.14 이상이 필요합니다.

### <a name="step-3-exchange-and-sharepoint"></a>3단계: Exchange 및 SharePoint

Windows 기반 Outlook 클라이언트에서 최신 인증을 사용하려면 Exchange Online에서도 최신 인증을 사용하도록 설정해야 합니다. Exchange Online에 최신 인증을 사용하지 않도록 설정하는 경우, 최신 인증을 지원하는 Windows 기반 Outlook 클라이언트(Outlook 2013 이상)에서 기본 인증을 사용하여 Exchange Online 사서함에 연결합니다.

SharePoint Online은 최신 인증 기본값으로 사용하도록 설정되어 있습니다. 2017년 8월 1일 이후에 만든 디렉터리에 대한 최신 인증은 Exchange Online에서 기본값으로 사용하도록 설정됩니다. 그러나 이전에 최신 인증을 사용하지 않도록 설정했거나 이 날짜 이전에 만든 디렉터리를 사용하는 경우 다음 문서의 단계에 따라  [Exchange Online에서 최신 인증 사용](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)을 수행합니다.

### <a name="step-4-skype-for-business"></a>4단계: 비즈니스용 Skype

비즈니스용 Skype에서 만든 레거시 인증 요청을 방지하려면 비즈니스용 Skype Online에 최신 인증을 사용하도록 설정해야 합니다. 2017년 8월 1일 이후에 만든 디렉터리에 대해 비즈니스용 Skype에 대한 최신 인증이 기본값으로 사용하도록 설정됩니다.

기본값으로 최신 인증을 지원하는 Microsoft Teams로 전환하는 것이 좋습니다. 그러나 지금 마이그레이션할 수 없는 경우 비즈니스용 Skype 클라이언트가 최신 인증을 사용하기 시작하도록 비즈니스용 Skype Online에 최신 인증을 사용해야 합니다. 비즈니스용 Skype에 최신 인증을 사용하도록 설정하려면  [최신 인증에서 지원되는 비즈니스용 Skype 토폴로지](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported) 문서의 단계를 따릅니다.

비즈니스용 Skype Online에 최신 인증을 사용하도록 설정하는 것 외에도, 비즈니스용 Skype에 최신 인증을 사용하도록 설정할 때 Exchange Online에 최신 인증을 사용하도록 설정하는 것이 좋습니다. 이 프로세스는 Exchange Online 및 비즈니스용 Skype 온라인에서 최신 인증 상태를 동기화하는 데 도움이 되며 비즈니스용 Skype 클라이언트에 대한 다중 로그인 프롬프트를 방지합니다.

### <a name="step-5-using-mobile-devices"></a>5단계: 모바일 디바이스 사용

모바일 디바이스의 애플리케이션 에서도 레거시 인증을 차단해야 합니다. 모바일에서 Outlook을 사용하는 것을 권장합니다. 모바일용 Outlook은 기본값으로 최신 인증을 지원하고 기타 MFA 기준 보호 정책을 충족합니다.

네이티브 iOS 메일 클라이언트를 사용하려면 iOS 11.0 이상 버전을 실행하여 메일 클라이언트가 레거시 인증을 차단하도록 업데이트 되었는지 확인해야 합니다.

### <a name="step-6-on-premises-clients"></a>6단계: 온-프레미스 클라이언트

Exchange Server 온-프레미스와 비즈니스용 Skype 온-프레미스를 사용하는 하이브리드 고객인 경우 최신 인증을 사용하도록 두 서비스를 업데이트해야 합니다. 하이브리드 환경에서 최신 인증을 사용하는 경우에도 온-프레미스에서 사용자를 인증하는 것입니다. 리소스(파일 또는 이메일)에 대한 액세스 권한을 부여하는 스토리가 변경됩니다.

온-프레미스에서 최신 인증을 사용을 시작하기 전에 먼저 선제 요건을 충족하는지 확인하세요. 이제 온-프레미스에서 최신 인증을 사용할 준비가 되었습니다.

최신 인증을 사용하는 단계는 다음 문서에서 찾을 수 있습니다.

* [하이브리드 최신 인증을 사용 하도록 Exchange Server 온-프레미스를 구성하는 방법](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [비즈니스용 Skype에서 최신 인증(ADAL)을 사용하는 방법](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>다음 단계

- [하이브리드 최신 인증을 사용 하도록 Exchange Server 온-프레미스를 구성하는 방법](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [비즈니스용 Skype에서 최신 인증(ADAL)을 사용하는 방법](/skypeforbusiness/manage/authentication/use-adal)
- [레거시 인증 차단](../conditional-access/block-legacy-authentication.md)