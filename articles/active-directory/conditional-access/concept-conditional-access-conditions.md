---
title: 조건부 액세스 정책의 조건 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 조건은 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, sandeo-MSFT
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89dab74b476345e08a5b995ad04d7d512d0e3a28
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012646"
---
# <a name="conditional-access-conditions"></a>조건부 액세스: 조건

관리자는 조건부 액세스 정책 내에서 위험, 디바이스 플랫폼 또는 위치와 같은 조건 신호를 활용하여 정책 결정을 강화할 수 있습니다. 

[![조건부 액세스 정책 정의 및 조건 지정](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

여러 조건을 결합하여 세분화된 특정 조건부 액세스 정책을 만들 수 있습니다.

예를 들어 중요한 애플리케이션에 액세스하는 경우 관리자는 ID 보호와 위치의 로그인 위험 정보를 다단계 인증 같은 다른 제어 수단과 함께 액세스 결정에 반영할 수 있습니다.

## <a name="sign-in-risk"></a>로그인 위험

[ID 보호](../identity-protection/overview-identity-protection.md)에 대한 액세스 권한이 있는 고객의 경우 조건부 액세스 정책의 일부로 로그인 위험을 평가할 수 있습니다. 로그인 위험은 ID 소유자가 지정된 인증 요청에 권한이 부여되지 않은 확률을 나타냅니다. 로그인 위험에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다. [위험이란 무엇인가](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) 및 [위험 정책 구성 및 사용 방법](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>사용자 위험 

[ID 보호](../identity-protection/overview-identity-protection.md)에 대한 액세스 권한이 있는 고객의 경우 조건부 액세스 정책의 일부로 사용자 위험을 평가할 수 있습니다. 사용자 위험은 지정된 ID 또는 계정이 손상되었을 가능성을 나타냅니다. 사용자 위험에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다. [위험이란 무엇인가](../identity-protection/concept-identity-protection-risks.md#user-linked-detections) 및 [위험 정책 구성 및 사용 방법](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>디바이스 플랫폼

디바이스 플랫폼의 특징은 디바이스에서 실행되는 운영 체제입니다. Azure AD는 디바이스에서 제공하는 정보(예: 사용자 에이전트 문자열)를 사용하여 플랫폼을 식별합니다. 사용자 에이전트 문자열은 수정할 수 있으므로 이 정보는 확인되지 않습니다. 디바이스 플랫폼은 Microsoft Intune 디바이스 준수 정책과 함께 사용하거나 차단 명령문의 일부로 사용해야 합니다. 기본값은 모든 디바이스 플랫폼에 적용하는 것입니다.

Azure AD 조건부 액세스에서는 다음과 같은 디바이스 플랫폼을 지원합니다.

- Android
- iOS
- Windows Phone
- Windows
- macOS

레거시 인증을 **다른 클라이언트** 상태를 사용하여 차단하는 경우 디바이스 플랫폼 조건을 설정할 수도 있습니다.

> [!IMPORTANT]
> Microsoft는 지원되지 않는 디바이스 플랫폼에 조건부 액세스 정책 사용을 권합니다. 예를 들어 Linux 또는 기타 지원되지 않는 클라이언트에서 회사 리소스에 대한 액세스를 차단하려면, 모든 디바이스를 포함하고 지원되는 디바이스 플랫폼을 제외한 디바이스 플랫폼 조건으로 정책을 구성한 뒤에 블록 액세스에 컨트롤 집합 권한을 부여해야 합니다.

## <a name="locations"></a>위치

위치를 조건으로 구성하는 경우 조직에서 위치를 포함하거나 제외하도록 선택할 수 있습니다. 이러한 명명된 위치에는 공용 IPv4 네트워크 정보, 국가나 지역, 또는 특정 국가나 지역에 매핑되지 않는 알 수 없는 영역도 포함될 수 있습니다. IP 범위만을 신뢰할 수 있는 위치로 표시할 수 있습니다.

**모든 위치** 를 포함하는 경우 이 옵션에는 구성된 명명된 위치뿐만이 아니라 인터넷의 모든 IP 주소가 포함됩니다. **모든 위치** 를 선택하는 경우 관리자는 **신뢰할 수 있는 모든 위치** 또는 **선택한 위치** 를 제외하도록 선택할 수 있습니다.

예를 들어, 일부 조직에서는 사용자가 실제 본사와 같은 신뢰할 수 있는 위치에서 네트워크에 연결된 경우 다단계 인증을 요구하지 않도록 선택할 수 있습니다. 관리자는 모든 위치를 포함하지만 본사 네트워크에서 선택한 위치를 제외하는 정책을 만들 수 있습니다.

위치에 대한 자세한 내용은 다음 문서 [Azure Active Directory 조건부 액세스의 위치 조건이란](location-condition.md)에서 찾을 수 있습니다.

## <a name="client-apps"></a>클라이언트 앱

기본값으로 새로 만든 모든 조건부 액세스 정책은 클라이언트 앱 조건이 구성되지 않은 경우에도 모든 클라이언트 앱 형식에 적용됩니다. 

> [!NOTE]
> 클라이언트 앱 조건의 동작은 2020년 8월에 업데이트되었습니다. 기존 조건부 액세스 정책이 있는 경우 변경되지 않은 상태로 유지됩니다. 그러나 기존 정책을 클릭하면 구성 토글이 제거되고 정책이 적용되는 클라이언트 앱이 선택됩니다.

> [!IMPORTANT]
> 레거시 인증 클라이언트의 로그인은 MFA를 지원하지 않으며, 디바이스 상태 정보를 Azure AD에 전달하지 않으므로 MFA 또는 호환 디바이스 요구와 같은 조건부 액세스 권한 부여 컨트롤에 의해 차단됩니다. 레거시 인증을 사용해야 하는 계정이 있는 경우 정책에서 해당 계정을 제외하거나 최신 인증 클라이언트에만 적용되도록 정책을 구성해야 합니다.

**구성** 토글이 **예** 로 설정되면 확인된 항목에 적용되며, **아니오** 로 설정되면 최신 및 레거시 인증 클라이언트 같은 모든 클라이언트 앱에 적용됩니다. 이 토글은 2020년 8월 이전에 만든 정책에는 표시되지 않습니다.

- 최신 인증 클라이언트
   - 브라우저
      - 여기에는 SAML, WS-Federation, OpenID Connect 또는 OAuth 기밀 클라이언트로 등록된 서비스와 같은 프로토콜을 사용하는 웹 기반 애플리케이션이 포함됩니다.
   - 모바일 앱 및 데스크톱 클라이언트
      -  이 옵션에는 Office 데스크톱 및 전화 애플리케이션과 같은 애플리케이션이 포함됩니다.
- 레거시 인증 클라이언트
   - Exchange ActiveSync 클라이언트
      - 이렇게 선택하면 EAS(Exchange ActiveSync) 프로토콜의 모든 사용이 포함됩니다.
      - 정책에서 Exchange ActiveSync 사용을 차단하는 경우 영향을 받는 사용자는 단일 격리 메일을 받게 됩니다. 이 메일은 차단된 이유에 대한 정보를 제공하고 가능하다면 수정 지침도 포함합니다.
      - 관리자는 조건부 액세스 Microsoft Graph API를 통해 지원되는 플랫폼(예: iOS, Android 및 Windows)에만 정책을 적용할 수 있습니다.
   - 기타 클라이언트
      - 이 옵션은 최신 인증을 지원하지 않는 기본/레거시 인증 프로토콜을 사용하는 클라이언트를 포함합니다.
         - 인증된 SMTP - POP 및 IMAP 클라이언트에서 이메일 메시지를 보낼 때 사용합니다.
         - 자동 검색 - Outlook 및 EAS 클라이언트에서 Exchange Online의 사서함을 찾아 연결할 때 사용합니다.
         - Exchange Online PowerShell - 원격 PowerShell을 사용하여 Exchange Online에 연결하는 데 사용됩니다. Exchange Online PowerShell에 대한 기본 인증을 차단하는 경우 Exchange Online PowerShell 모듈을 사용하여 연결해야 합니다. 자세한 내용은 [다단계 인증을 사용하여 Exchange Online PowerShell에 연결](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)을 참조하세요.
         - EWS(Exchange 웹 서비스) - Outlook, Outlook for Mac 및 타사 앱에서 사용하는 프로그래밍 인터페이스입니다.
         - IMAP4 - IMAP 이메일 클라이언트에서 사용합니다.
         - MAPI over HTTP(MAPI/HTTP) - Outlook 2010 이상에서 사용합니다.
         - OAB(오프라인 주소록) - Outlook에서 다운로드하여 사용하는 주소 목록 컬렉션의 복사본입니다.
         - 외부에서 Outlook 사용(RPC over HTTP) - Outlook 2016 이하 버전에서 사용합니다.
         - Outlook 서비스 - Windows 10용 메일 및 일정 앱에서 사용합니다.
         - POP3 - POP 이메일 클라이언트에서 사용합니다.
         - 보고 웹 서비스 - Exchange Online에서 보고서 데이터를 검색할 때 사용합니다.

이러한 조건은 관리 디바이스를 요구하고, 레거시 인증을 차단하고, 웹 애플리케이션을 차단하지만 모바일 또는 데스크톱 앱을 허용할 때 주로 사용됩니다.

### <a name="supported-browsers"></a>지원되는 브라우저

이 설정은 모든 브라우저에서 작동합니다. 하지만 호환 디바이스 요구 사항과 같은 디바이스 정책을 충족하기 위해 다음 운영 체제와 브라우저가 지원됩니다.

| OS | 브라우저 |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome, [Firefox 91 이상](https://support.mozilla.org/kb/windows-sso) |
| Windows 8 / 8.1 | Internet Explorer, 크롬 |
| Windows 7 | Internet Explorer, 크롬 |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Microsoft Edge, Chrome, Safari |

이러한 브라우저는 디바이스 인증을 지원하므로 정책에 대해 디바이스 유효성을 검사하고 식별하는 것이 가능합니다. 브라우저가 개인 모드로 실행 중이거나 쿠키가 비활성화된 경우 장치 확인이 실패합니다.

> [!NOTE]
> Edge 85 이상에서는 디바이스 ID를 올바르게 전달하기 위해 사용자의 브라우저 로그인을 요구합니다. 그렇지 않으면 계정 확장이 없는 Chrome처럼 동작합니다. 이 로그인은 하이브리드 Azure AD 조인 시나리오에서는 자동으로 발생하지 않을 수 있습니다. Safari는 디바이스 기반 조건부 액세스에 지원되지만 **승인된 클라이언트 앱 필요** 또는 **앱 보호 정책 필요** 조건을 충족할 수 없습니다. Microsoft Edge와 같은 관리되는 브라우저는 승인된 클라이언트 앱 및 앱 보호 정책 요구 사항을 충족합니다.

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>브라우저에 인증서 메시지가 표시되는 이유는 무엇인가요

Windows 7, iOS, Android 및 macOS Azure AD는 디바이스가 Azure AD에 등록될 때 프로비저닝되는 클라이언트 인증서를 사용하여 디바이스를 식별합니다.  사용자가 브라우저를 통해 처음으로 로그인하면 사용자에게 인증서를 선택하라는 메시지가 표시됩니다. 사용자는 이 인증서를 선택해야 브라우저를 사용할 수 있습니다.

#### <a name="chrome-support"></a>Chrome 지원

**Windows 10 크리에이터스 업데이트(버전 1703)** 이상에서 Chrome을 지원하려면 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치합니다. 이 확장은 조건부 액세스 정책에서 디바이스 관련 세부 정보를 요구하는 경우에 필요합니다.

이 확장을 Chrome 브라우저에 자동으로 배포하려면 다음 레지스트리 키를 만듭니다.

- Path HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Name 1
- Type REG_SZ (String)
- Data ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx

**Windows 8.1 및 7** 에서 Chrome을 지원하려면 다음 레지스트리 키를 만듭니다.

- Path HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- Name 1
- Type REG_SZ (String)
- Data {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}

### <a name="supported-mobile-applications-and-desktop-clients"></a>지원되는 모바일 애플리케이션 및 데스크톱 클라이언트

조직에서는 **모바일 앱 및 데스크톱 클라이언트** 를 클라이언트 앱으로 선택할 수 있습니다.

이 설정은 다음 모바일 앱 및 데스크톱 클라이언트에서 수행하는 액세스 시도에 영향을 미칩니다.

| 클라이언트 앱 | 대상 서비스 | 플랫폼 |
| --- | --- | --- |
| Dynamics CRM 앱 | Dynamics CRM | Windows 10, Windows 8.1, iOS 및 Android |
| 메일/달력/사람 앱, Outlook 2016, Outlook 2013(최신 인증 포함)| Exchange Online | Windows 10 |
| 앱에 대한 MFA 및 위치 정책입니다. 디바이스 기반 정책은 지원되지 않습니다.| 모든 My Apps 앱 서비스 | Android 및 iOS |
| Microsoft Teams Services - 이 클라이언트 앱은 Microsoft Teams 및 모든 클라이언트 앱(Windows 데스크톱, iOS, Android, WP 및 웹 클라이언트)을 지원하는 서비스를 모두 제어합니다. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android 및 macOS |
| Office 2016 앱, Office 2013(최신 인증 사용), [OneDrive 동기화 클라이언트](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1, Windows 7 |
| Office 2016 앱, Universal Office 앱, Office 2013(최신 인증 사용), [OneDrive 동기화 클라이언트](/onedrive/enable-conditional-access) | SharePoint Online | Windows 10 |
| Office 2016(Word, Excel, PowerPoint, OneNote만 해당) | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10, macOS |
| Office 모바일 앱 | SharePoint | Android, iOS |
| Office Yammer 앱 | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016(macOS용 Office) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013(최신 인증 사용), 비즈니스용 Skype(최신 인증 사용) | Exchange Online | Windows 8.1, Windows 7 |
| Outlook 모바일 앱 | Exchange Online | Android, iOS |
| Power BI 앱 | Power BI 서비스 | Windows 10, Windows 8.1, Windows 7, Android 및 iOS |
| 비즈니스용 Skype | Exchange Online| Android, iOS |
| Visual Studio Team Services 앱 | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS 및 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트

- 조직에서는 사용자 또는 그룹에 정책을 할당하는 경우에만 Exchange ActiveSync 클라이언트를 선택할 수 있습니다. **모든 사용자**, **모든 게스트 및 외부 사용자**, 또는 **디렉터리 역할** 을 선택하면 모든 사용자에게 정책이 적용됩니다.
- Exchange ActiveSync 클라이언트에 할당된 정책을 만들 때 **Exchange Online** 만이 정책에 할당된 유일한 클라우드 애플리케이션이어야 합니다. 
- 조직에서는 **디바이스 플랫폼** 상태를 사용하여 특정 플랫폼에 대해 이 정책의 범위를 좁힐 수 있습니다.

정책에 할당된 액세스 제어에서 **승인된 클라이언트 앱 필요** 를 사용하는 경우 사용자는 Outlook 모바일 클라이언트를 설치하고 사용하도록 안내 받습니다. **다단계 인증**, **사용 약관**, 또는 **사용자 지정 컨트롤** 이 필요한 경우 기본 인증에서 이러한 컨트롤을 지원하지 않기 때문에 영향을 받는 사용자는 차단됩니다.

자세한 내용은 다음 문서를 참조하세요.

- [조건부 액세스를 사용하여 레거시 인증 차단](block-legacy-authentication.md)
- [조건부 액세스를 사용하여 승인된 클라이언트 앱 요구](app-based-conditional-access.md)

### <a name="other-clients"></a>기타 클라이언트

**다른 클라이언트** 를 선택하여 IMAP, MAPI, POP, SMTP 및 최신 인증을 사용하지 않는 이전 Office 앱과 같은 메일 프로토콜과 함께 기본 인증을 사용하는 앱에 영향을 미치는 조건을 지정할 수 있습니다.

## <a name="device-state-preview"></a>디바이스 상태(미리 보기)
> [!CAUTION]
> **이 미리 보기 기능은 더 이상 사용되지 않습니다.** 고객은 이전에 디바이스 상태(미리 보기) 조건을 사용하여 달성된 시나리오를 조건부 액세스에서 **디바이스에 대한 필터** 조건을 사용하여 충족해야 합니다.

디바이스 상태 조건은 하이브리드 Azure AD 조인된 디바이스 및/또는 조직의 조건부 액세스 정책에서 Microsoft Intune 준수 정책을 준수한다고 표시된 디바이스를 제외하는 데 사용할 수 있습니다.

예를 들어 *Microsoft Azure Management* 클라우드 앱에 액세스하는 *모든 사용자* 는 **모든 디바이스 상태** 를 포함하고 **하이브리드 Azure AD 조인된 디바이스** 및 **준수 상태로 표시된 디바이스** 를 *액세스 제어*, **차단** 을 위해 제외합니다. 
   - 이 예제에서는 하이브리드 Azure AD 조인된 디바이스 또는 준수 상태로 표시된 디바이스에서만 Microsoft Azure 관리에 대한 액세스를 허용하는 정책을 만듭니다.

위 시나리오는 **device.trustType -ne "ServerAD" -or device.isCompliant -ne True** 규칙과 *액세스 제어* 의 경우 **차단** 을 사용하는 **디바이스에 대한 필터** 조건을 제외하고 *Microsoft Azure Management* 클라우드 앱에 액세스하는 *모든 사용자* 를 사용하여 구성할 수 있습니다.
- 이 예제에서는 하이브리드 Azure AD 조인된 디바이스 또는 준수 상태로 표시된 디바이스에서만 Microsoft Azure 관리에 대한 액세스를 허용하는 정책을 만듭니다.

> [!IMPORTANT]
> 디바이스에 대한 디바이스 상태 및 필터는 조건부 액세스 정책에서 함께 사용할 수 없습니다. 디바이스에 대한 필터는 `trustType` 및 `isCompliant` 속성을 통해 디바이스 상태 정보를 대상으로 지정하는 지원을 포함하여 보다 세부적인 대상 지정을 제공합니다.

## <a name="filter-for-devices"></a>디바이스에 대한 필터

조건부 액세스에는 디바이스에 대한 필터라는 새로운 선택적 조건이 있습니다. 디바이스에 대한 필터를 조건으로 구성할 때 조직은 디바이스 속성에 대한 규칙 표현식을 사용하여 필터를 기반으로 디바이스를 포함하거나 제외하도록 선택할 수 있습니다. 디바이스 필터에 대한 규칙 식은 규칙 작성기 또는 규칙 구문을 사용하여 작성할 수 있습니다. 이 환경은 그룹의 동적 멤버 자격 규칙에 사용되는 환경과 비슷합니다. 자세한 내용은 [조건부 액세스: 디바이스 필터(미리 보기)](concept-condition-filters-for-devices.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 권한 부여](concept-conditional-access-grant.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
