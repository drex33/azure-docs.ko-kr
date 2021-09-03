---
title: PRT(주 새로 고침 토큰) 및 Azure AD - Azure Active Directory
description: Azure Active Directory에서 PRT(주 새로 고침 토큰)는 어떤 역할을 하며 어떻게 관리하나요?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87fd2189222828eef2ff03a82125e0b6dcf7111e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529926"
---
# <a name="what-is-a-primary-refresh-token"></a>주 새로 고침 토큰이란?

PRT(주 새로 고침 토큰)는 Windows 10, Windows Server 2016 이상 버전, iOS 및 Android 디바이스에서 Azure AD 인증의 핵심 아티팩트입니다. 이러한 디바이스에 사용되는 애플리케이션 전체에서 SSO(Single Sign-On)를 사용하도록 설정하기 위해 Microsoft의 자사 토큰 브로커에 특별히 발급된 JWT(JSON Web Token)입니다. 이 문서에서는 Windows 10 디바이스에서 PRT를 발급, 사용 및 보호하는 방법에 대한 세부 정보를 제공합니다.

이 문서에서는 사용자가 Azure AD에서 사용할 수 있는 여러 디바이스 상태와 Windows 10에서 Single Sign-On의 작동 방식을 이미 잘 알고 있다고 가정합니다. Azure AD의 디바이스에 대한 자세한 내용은 [Azure Active Directory의 디바이스 관리란?](overview.md) 문서를 참조하세요.

## <a name="key-terminology-and-components"></a>주요 용어 및 구성 요소

다음 Windows 구성 요소는 PRT를 요청 및 사용하는 데 중요한 역할을 합니다.

* **클라우드 인증 공급자**(CloudAP): CloudAP는 Windows 로그인을 위한 최신 인증 공급자로, Windows 10 디바이스에 로그인하는 사용자를 확인합니다. CloudAP는 ID 공급자가 해당 ID 공급자의 자격 증명을 사용하여 Windows에 인증할 수 있도록 하기 위해 구축할 수 있는 플러그 인 프레임워크를 제공합니다.
* **웹 계정 관리자**(WAM): WAM은 Windows 10 디바이스의 기본 토큰 브로커입니다. 또한 WAM은 ID 공급자가 해당 ID 공급자를 사용하는 애플리케이션에 대해 SSO를 지원하기 위해 구축할 수 있는 플러그 인 프레임워크를 제공합니다. (Windows Server 2016 LTSC 빌드에 포함되지 않음)
* **Azure AD CloudAP 플러그 인**: CloudAP 프레임워크를 기준으로 구축된 Azure AD 특정 플러그 인으로, Windows 로그인 중에 Azure AD를 사용하여 사용자 자격 증명을 확인합니다.
* **Azure AD WAM 플러그 인**: 인증을 위해 Azure AD를 사용하는 애플리케이션에서 SSO를 사용할 수 있도록 하는 WAM 프레임워크를 기준으로 구축된 Azure AD 특정 플러그 인입니다.
* **Dsreg**: 모든 디바이스 상태에 대한 디바이스 등록 프로세스를 처리하는 Windows 10의 Azure AD 특정 구성 요소입니다.
* **신뢰할 수 있는 플랫폼 모듈**(TPM): TPM은 디바이스에 기본 제공되는 하드웨어 구성 요소로, 사용자 및 디바이스 암호에 대한 하드웨어 기반 보안 기능을 제공합니다. 자세한 내용은 [신뢰할 수 있는 플랫폼 모듈 기술 개요](/windows/security/information-protection/tpm/trusted-platform-module-overview) 문서에서 찾을 수 있습니다.

## <a name="what-does-the-prt-contain"></a>PRT에는 무엇이 포함되나요?

PRT는 일반적으로 모든 Azure AD 새로 고침 토큰에 포함된 클레임을 포함합니다. 또한 일부 디바이스 관련 클레임이 PRT에도 포함되어 있습니다. 다음과 같습니다.

* **디바이스 ID**: PRT는 특정 디바이스의 사용자에게 발급됩니다. 디바이스 ID 클레임 `deviceID`는 사용자가 PRT가 발급된 디바이스를 결정합니다. 이 클레임은 나중에 PRT를 통해 획득한 토큰에 발급됩니다. 디바이스 ID 클레임은 디바이스 상태 또는 규정 준수에 따라 조건부 액세스에 대한 권한 부여를 결정하는 데 사용됩니다.
* **세션 키**: 세션 키는 Azure AD 인증 서비스에 의해 생성되고 PRT의 일부로 발급되는 암호화된 대칭 키입니다. 세션 키는 PRT를 사용하여 다른 애플리케이션에 대한 토큰을 획득할 때 소유 증명 역할을 합니다.

### <a name="can-i-see-whats-in-a-prt"></a>PRT에 포함된 항목을 볼 수 있나요?

PRT는 어떤 클라이언트 구성 요소도 해당 콘텐츠를 알 수 없는 Azure AD에서 보낸 불투명 Blob입니다. PRT 안에 있는 항목은 볼 수 없습니다.

## <a name="how-is-a-prt-issued"></a>PRT는 어떻게 발급되나요?

Azure AD의 디바이스 기반 인증을 위해서는 반드시 디바이스를 등록해야 합니다. PRT는 등록된 디바이스의 사용자에게만 발급됩니다. 디바이스 등록에 대한 자세한 내용은 [비즈니스용 Windows Hello 및 디바이스 등록](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration) 문서를 참조하세요. 디바이스를 등록하는 동안 dsreg 구성 요소는 두 가지 암호화 키 쌍 세트를 생성합니다.

* 디바이스 키(dkpub/dkpub)
* 전송 키(tkpub/tkpub)

프라이빗 키는 디바이스에 유효하고 작동하는 TPM이 있는 경우에 디바이스의 TPM에 바인딩되지만, 퍼블릭 키는 디바이스 등록 프로세스 동안 Azure AD로 전송됩니다. 이러한 키는 PRT 요청 중에 디바이스 상태가 유효한지 확인하는 데 사용됩니다.

PRT는 Windows 10 디바이스에서 사용자 인증을 수행하는 동안 다음과 같은 두 가지 시나리오에서 발급됩니다.

* **Azure AD 조인** 또는 **하이브리드 Azure AD 조인**: 사용자가 조직 자격 증명을 사용하여 로그인하면 Windows 로그온 중에 PRT가 발급됩니다. PRT는 암호 및 비즈니스용 Windows Hello와 같은 모든 Windows 10 지원 자격 증명과 함께 발급됩니다. 이 시나리오에서 Azure AD CloudAP 플러그 인은 PRT의 기본 인증 기관입니다.
* **Azure AD 등록 디바이스**: 사용자가 Windows 10 디바이스에 보조 회사 계정을 추가하면 PRT가 발급됩니다. 사용자는 다음 두 가지 방법으로 Windows 10에 계정을 추가할 수 있습니다.  
   * 앱(예: Outlook)에 로그인한 후 **내 조직에서 내 디바이스를 관리하도록 허용** 프롬프트를 통해 계정 추가
   * **설정** > **계정** > **회사 또는 학교 액세스** > **연결** 에서 계정 추가

Azure AD 등록 디바이스 시나리오에서 Azure AD WAM 플러그 인은 이 Azure AD 계정으로 Windows 로그온이 발생하지 않기 때문에 PRT의 기본 인증 기관입니다.

> [!NOTE]
> 타사 ID 공급자는 Windows 10 디바이스에서 PRT 발급이 이루어지도록 WS-Trust 프로토콜을 지원해야 합니다. WS-Trust를 사용하지 않으면 하이브리드 Azure AD 조인 또는 Azure AD 조인 디바이스에서 사용자에게 PRT를 발급할 수 없습니다. ADFS에서는 usernamemixed 엔드포인트만 필요합니다. adfs/services/trust/2005/windowstransport 및 adfs/services/trust/13/windowstransport는 모두 인트라넷 연결 엔드포인트로만 사용하도록 설정해야 하며, 웹 애플리케이션 프록시를 통해 엑스트라넷 연결 엔드포인트로 **노출되어서는 안됩니다**.

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT의 수명은 얼마나 되나요?

PRT는 일단 발급되면 14일 동안 유효하며 사용자가 디바이스를 적극적으로 사용하는 동안에는 계속 갱신됩니다.  

## <a name="how-is-a-prt-used"></a>PRT는 어떻게 사용되나요?

PRT는 다음과 같은 Windows의 두 가지 주요 구성 요소에서 사용됩니다.

* **Azure AD CloudAP 플러그 인**: Windows 로그인 중에 Azure AD CloudAP 플러그 인은 사용자가 제공한 자격 증명을 사용하여 Azure AD에서 PRT를 요청합니다. 또한 사용자가 인터넷 연결에 액세스할 수 없는 경우 캐시된 로그인을 사용하도록 PRT를 캐시합니다.
* **Azure AD WAM 플러그 인**: 사용자가 애플리케이션에 액세스하려고 하면 Azure AD WAM 플러그 인에서 PRT를 사용하여 Windows 10에서 SSO를 사용하도록 설정합니다. Azure AD WAM 플러그 인은 PRT를 사용하여 토큰 요청에 대해 WAM를 사용하는 애플리케이션에 대한 새로 고침 및 액세스 토큰을 요청합니다. 또한 브라우저 요청에 PRT를 삽입하여 브라우저에서 SSO를 사용하도록 설정합니다. Windows 10의 브라우저 SSO는 Microsoft Edge(기본적으로) 및 Chrome([Windows 10 계정](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) 또는 [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) 확장을 통해)에서 지원됩니다.

## <a name="how-is-a-prt-renewed"></a>PRT는 어떻게 갱신되나요?

PRT는 다음 두 가지 방법으로 갱신됩니다.

* **4시간마다 Azure AD CloudAP 플러그 인**: CloudAP 플러그 인은 Windows 로그인 동안 4시간마다 PRT를 갱신합니다. 사용자가 해당 시간 동안 인터넷에 연결되지 않은 경우 CloudAP 플러그 인은 디바이스가 인터넷에 연결된 후에 PRT를 갱신합니다.
* **앱 토큰 요청 동안 Azure AD WAM 플러그 인**: WAM 플러그 인은 애플리케이션에 대한 자동 토큰 요청을 사용하도록 설정하여 Windows 10 디바이스에서 SSO를 사용하도록 설정합니다. WAM 플러그 인은 다음 두 가지 방법으로 이러한 토큰 요청 동안 PRT를 갱신할 수 있습니다.
   * 앱은 WAM에 자동으로 액세스 토큰을 요청하지만 해당 앱에 사용할 수 있는 새로 고침 토큰이 없습니다. 이 경우 WAM은 PRT를 사용하여 앱에 대한 토큰을 요청하고 응답에서 새 PRT를 다시 가져옵니다.
   * 앱은 WAM에 액세스 토큰을 요청하지만 PRT가 유효하지 않거나 Azure AD가 추가 인증을 요구합니다(예: Azure AD Multi-Factor Authentication). 이 시나리오에서 WAM은 사용자에게 다시 인증하거나 추가 확인을 제공하도록 요구하는 대화형 로그온을 시작하고, 인증이 성공하면 새 PRT를 발급합니다.

ADFS 환경에서는 도메인 컨트롤러를 직접 보지 않고도 PRT를 갱신할 수 있습니다. PRT를 갱신하려면 WS-Trust를 사용하여 프록시에서 /adfs/services/trust/2005/usernamemixed 및 /adfs/services/trust/13/usernamemixed 엔드포인트만 사용하도록 설정하면 됩니다.

Windows 전송 엔드포인트는 암호를 변경하는 경우에만 암호 인증을 위해 필요하며, PRT 갱신에는 필요하지 않습니다.

### <a name="key-considerations"></a>주요 고려 사항

* PRT는 네이티브 앱 인증 중에만 발급되고 갱신됩니다. PRT는 브라우저 세션 동안 갱신되거나 발급되지 않습니다.
* Azure AD 조인 및 하이브리드 Azure AD 조인 디바이스에서 CloudAP 플러그 인은 PRT의 기본 인증 기관입니다. WAM 기반 토큰 요청 중에 PRT가 갱신되면 PRT가 CloudAP 플러그 인으로 다시 전송됩니다. 그러면 해당 PRT를 수락하기 전에 Azure AD를 사용하여 PRT의 유효성을 확인합니다.

## <a name="how-is-the-prt-protected"></a>PRT는 어떻게 보호되나요?

PRT는 사용자가 로그인한 디바이스에 바인딩하여 보호됩니다. Azure AD 및 Windows 10은 다음 방법을 통해 PRT 보호를 사용하도록 설정합니다.

* **처음 로그인하는 동안**: 처음 로그인하는 동안 디바이스를 등록하는 동안 암호화 생성 암호 키를 사용하여 요청에 서명하여 PRT가 발급됩니다. 유효하고 작동하는 TPM이 있는 디바이스에서 디바이스 키는 악의적인 액세스를 방지하는 TPM에 의해 보호됩니다. 해당하는 디바이스 키 서명의 유효성을 검사할 수 없는 경우에는 PRT가 발급되지 않습니다.
* **토큰 요청 및 갱신 동안**: PRT가 발급되면 Azure AD는 디바이스에 암호화된 세션 키도 발급합니다. 생성된 퍼블릭 전송 키(tkpub)로 암호화된 후 디바이스 등록의 일부로 Azure AD로 전송됩니다. 이 세션 키는 TPM으로 보호되는 프라이빗 전송 키(tkpriv)로만 암호를 해독할 수 있습니다. 세션 키는 Azure AD로 전송된 모든 요청에 대한 POP(소유 증명) 키입니다.  세션 키는 TPM에 의해 보호되며 다른 OS 구성 요소는 이 키에 액세스할 수 없습니다. 토큰 요청 또는 PRT 갱신 요청은 TPM을 통해 이 세션 키로 안전하게 서명되므로 변조할 수 없습니다. Azure AD는 해당 세션 키로 서명되지 않은 디바이스의 모든 요청을 무효화합니다.

이러한 키를 TPM으로 보호하여 키를 도용하거나 PRT를 재생하려고 하는 악의적인 행위자로부터 PRT 보안이 강화됩니다.  따라서 TPM을 사용하면 자격 증명 도난에 대비해서 Azure AD 조인, 하이브리드 Azure AD 조인 및 Azure AD 등록 디바이스의 보안이 크게 향상됩니다. 성능 및 안정성을 위해 TPM 2.0은 Windows 10의 모든 Azure AD 디바이스 등록 시나리오에 권장되는 버전입니다. Windows 10, 1903 업데이트부터 Azure AD는 안정성 문제로 인해 위의 키에 TPM 1.2을 사용하지 않습니다. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>앱 토큰 및 브라우저 쿠키는 어떻게 보호되나요?

**앱 토큰**: 앱이 WAM를 통해 토큰을 요청하면 Azure AD는 새로 고침 토큰과 액세스 토큰을 발급합니다. 그러나 WAM은 앱에 액세스 토큰만 반환하고 사용자의 DPAPI(데이터 보호 애플리케이션 프로그래밍 인터페이스) 키로 암호화하여 캐시의 고침 토큰을 보호합니다. WAM은 세션 키로 요청에 서명하여 새로 고침 토큰을 안전하게 사용하며 추가 액세스 토큰을 발급합니다. DPAPI 키는 Azure AD 자체의 Azure AD 기반 대칭 키로 보호됩니다. 디바이스에서 DPAPI 키를 사용하여 사용자 프로필의 암호를 해독해야 하는 경우 Azure AD는 세션 키로 암호화된 DPAPI 키를 제공하며 CloudAP 플러그 인에서 TPM에 이러한 암호화의 해독을 요청합니다. 이 기능은 새로 고침 토큰 보호 시 일관성을 보장하고 애플리케이션이 자체 보호 메커니즘을 구현하지 못하게 합니다.  

**브라우저 쿠키**: Windows 10에서 Azure AD는 Internet Explorer 및 Microsoft Edge에서 기본적으로 또는 Windows 10 계정 확장을 통해 Google Chrome에서 브라우저 SSO를 지원합니다. 보안은 쿠키를 보호하는 것 뿐만 아니라 쿠키가 전송되는 엔드포인트도 보호하도록 구축됩니다. 브라우저 쿠키는 세션 키를 활용하여 쿠키를 서명 및 보호함으로써 PRT와 동일한 방식으로 보호됩니다.

사용자가 브라우저 상호 작용을 시작하면 브라우저(또는 확장)가 COM 네이티브 클라이언트 호스트를 호출합니다. 네이티브 클라이언트 호스트는 페이지가 허용 도메인 중 하나에 있는지 확인합니다. 브라우저는 nonce를 비롯한 다른 매개 변수를 네이티브 클라이언트 호스트로 보낼 수 있지만 네이티브 클라이언트 호스트는 호스트 이름의 유효성을 보장합니다. CloudAP 플러그 인에서 PRT 쿠키를 만들고 TPM 보호 세션 키로 서명하면 네이티브 클라이언트 호스트에서 PRT 쿠키를 요청합니다. PRT 쿠키는 세션 키로 서명되므로 변조하기가 매우 어렵습니다. 이 PRT 쿠키는 Azure AD에 대한 요청 헤더에 포함되어 원본 디바이스가 유효한지 검사합니다. Chrome 브라우저를 사용하는 경우 네이티브 클라이언트 호스트의 매니페스트에 명시적으로 정의된 확장에서만 이 쿠키를 호출할 수 있으므로 임의 확장이 이러한 요청을 수행할 수 없게 됩니다. Azure AD는 PRT 쿠키가 유효한지 검사한 후에는 브라우저에 세션 쿠키를 발급합니다. 이 세션 쿠키에는 PRT와 함께 발급된 것과 동일한 세션 키도 포함되어 있습니다. 후속 요청 중에 세션 키의 유효성이 검사되고 쿠키를 디바이스에 효과적으로 바인딩하며 다른 곳에서 다시 재생되지 못하게 합니다.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT는 언제 MFA 클레임을 얻나요?

PRT는 특정 시나리오에서 MFA(다단계 인증) 클레임을 가져올 수 있습니다. MFA 기반 PRT를 사용하여 애플리케이션에 대한 토큰을 요청하는 경우 MFA 클레임은 이러한 앱 토큰으로 전송됩니다. 이 기능은 필요로 하는 모든 앱에 대해 MFA 챌린지를 방지하여 사용자에게 원활한 환경을 제공합니다. PRT는 다음과 같은 방법으로 MFA 클레임을 가져올 수 있습니다.

* **비즈니스용 Windows Hello로 로그인**: 비즈니스용 Windows Hello는 암호를 대신하며 암호화 키를 사용하여 강력한 2단계 인증을 제공합니다. 비즈니스용 Windows Hello는 디바이스이 사용자에게 국한되며, 자체적으로 MFA에 프로비저닝하도록 요청합니다. 사용자가 비즈니스용 Windows Hello를 사용하여 로그인하면 사용자의 PRT는 MFA 클레임을 가져옵니다. 이 시나리오는 스마트 카드 인증이 ADFS에서 MFA 클레임을 생성하는 경우 스마트 카드를 사용하여 로그인하는 사용자에게도 적용됩니다.
   * 비즈니스용 Windows Hello는 다단계 인증으로 간주되기 때문에 MFA 클레임은 PRT가 자체적으로 새로 고쳐질 때 업데이트됩니다. 따라서 사용자가 비즈니스용 Windows Hello를 사용하여 로그인하면 MFA 기간이 지속적으로 확장됩니다.
* **WAM 대화형 로그인 중에 MFA**: WAM을 통해 토큰을 요청하는 동안 사용자가 MFA를 수행하여 앱에 액세스해야 하는 경우 이 상호 작용 중에 갱신된 PRT는 MFA 클레임으로 임프린트됩니다.
   * 이 경우 MFA 클레임은 지속적으로 업데이트되지 않으므로 MFA 기간은 디렉터리에 설정된 수명에 따라 결정됩니다.
   * 이전의 기존 PRT 및 RT를 앱에 액세스하는 데 사용하는 경우 PRT 및 RT는 첫 번째 인증 증명으로 간주됩니다. 두 번째 증명 및 임프린트된 MFA 클레임에는 새 AT가 필요합니다. 이 AT는 새 PRT 및 RT도 발급합니다.

Windows 10은 각 자격 증명에 대해 분할된 PRT 목록을 유지 관리합니다. 따라서 비즈니스용 Windows Hello, 암호 또는 스마트 카드 각각에 대한 PRT가 있습니다. 이 분할은 사용된 자격 증명에 따라 MFA 클레임이 격리되도록 하고 토큰 요청 중에 혼합되지 않도록 합니다.

## <a name="how-is-a-prt-invalidated"></a>PRT는 어떻게 무효화되나요?

다음 시나리오에서는 PRT가 무효화됩니다.

* **잘못된 사용자**: Azure AD에서 사용자를 삭제하거나 사용하지 않도록 설정하면 해당 PRT가 무효화되며 애플리케이션에 대한 토큰을 가져오는 데 사용할 수 없습니다. 삭제된 사용자 또는 사용하지 않도록 설정된 사용자가 이전에 디바이스에 로그인한 경우에는 CloudAP가 유효하지 않은 상태를 인식할 때까지 캐시된 로그인으로 로그인합니다. CloudAP에서 사용자가 유효하지 않다고 확인하면 후속 로그온을 차단합니다. 유효하지 않은 사용자는 자격 증명이 캐시되지 않은 새 디바이스에 자동으로 로그인하지 못하게 됩니다.
* **잘못된 디바이스**: Azure AD에서 디바이스를 삭제하거나 사용하지 않도록 설정하면 해당 디바이스에서 획득한 PRT가 무효화되며 다른 애플리케이션에 대한 토큰을 가져오는 데 사용할 수 없습니다. 사용자가 이미 잘못된 디바이스에 로그인한 경우에는 이 작업을 계속할 수 있습니다. 그러나 디바이스의 모든 토큰이 무효화되고 사용자는 해당 디바이스의 어떤 리소스에 대해서도 SSO가 없습니다.
* **암호 변경:** 사용자가 암호를 변경하면 이전 암호를 사용하여 획득한 PRT가 Azure AD에서 무효화됩니다. 암호 변경으로 인해 사용자는 새 PRT를 얻게 됩니다. 이러한 무효화는 다음과 같은 두 가지 방법으로 발생할 수 있습니다.
   * 사용자가 새 암호를 사용하여 Windows에 로그인하는 경우 CloudAP는 이전 PRT를 삭제하고 새 암호를 사용하여 새 PRT를 발급하도록 Azure AD에 요청합니다. 사용자가 인터넷에 연결되어 있지 않은 경우 새 암호의 유효성을 검사할 수 없으며 Windows에서 사용자에게 이전 암호를 입력하도록 요구할 수 있습니다.
   * 사용자가 이전 암호를 사용하여 로그인하거나 Windows에 로그인한 후 암호를 변경한 경우 WAM 기반 토큰 요청에 대해 이전 PRT가 사용됩니다. 이 시나리오에서 WAM 토큰 요청 중에 다시 인증하라는 메시지가 표시되고 고 새 PRT가 발급됩니다.
* **TPM 문제**: 경우에 따라 디바이스의 TPM이 불안정해지거나 오류가 발생하여 TPM을 통해 보호되는 키에 액세스하지 못할 수 있습니다. 이 경우 디바이스는 암호화 키의 소유를 증명할 수 없으므로 PRT를 가져오거나 기존 PRT를 사용하여 토큰을 요청할 수 없습니다. 따라서 모든 기존 PRT는 Azure AD에 의해 무효화됩니다. Windows 10은 오류를 감지하면 복구 흐름을 시작하여 새 암호화 키로 디바이스를 다시 등록합니다. 하이브리드 Azure Ad 조인을 사용하는 경우 초기 등록과 마찬가지로 사용자 입력 없이 복구가 자동으로 수행됩니다. Azure AD 조인 또는 Azure AD 등록 디바이스의 경우 디바이스에 대한 관리자 권한이 있는 사용자가 복구를 수행해야 합니다. 이 시나리오에서 복구 흐름은 사용자가 디바이스를 성공적으로 복구하도록 안내하는 Windows 프롬프트에 의해 시작됩니다.

## <a name="detailed-flows"></a>자세한 흐름

다음 다이어그램에서는 PRT를 발급, 갱신 및 사용하여 애플리케이션에 대한 액세스 토큰을 요청하는 기본 세부 정보를 보여 줍니다. 또한 이러한 단계에서는 이러한 상호 작용 중에 앞서 언급한 보안 메커니즘이 적용되는 방법도 설명합니다.

### <a name="prt-issuance-during-first-sign-in"></a>처음 로그인하는 동안 PRT 발급

![처음 로그인하는 동안 PRT 발급 상세 흐름](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Azure AD 조인 디바이스에서 이러한 교환은 비동기식으로 발생하여 사용자가 Windows에 로그온할 수 있도록 먼저 PRT가 발급됩니다. 하이브리드 Azure AD 조인 디바이스에서 온-프레미스 Active Directory는 기본 인증 기관입니다. 따라서 사용자는 TGT를 획득하여 로그인할 수 있을 때까지만 대기하지만, PRT 발급은 비동기식으로 발생합니다. 로그온 시 Azure AD 자격 증명을 사용하지 않으므로 이 시나리오가 Azure AD 등록 디바이스에는 적용되지 않습니다.

| 단계 | Description |
| :---: | --- |
| A | 사용자가 로그인 UI에 암호를 입력합니다. LogonUI는 인증 버퍼의 자격 증명을 LSA에 전달하며 LSA에서는 이 자격 증명을 내부적으로 CloudAP에 전달합니다. CloudAP는 이 요청을 CloudAP 플러그 인으로 전달합니다. |
| b | CloudAP 플러그 인은 사용자에 대한 ID 공급자를 식별하기 위한 영역 검색 요청을 시작합니다. 사용자의 테넌트에 페더레이션 공급자가 설정된 경우 Azure AD는 페더레이션 공급자의 MEX(Metadata Exchange Endpoint) 엔드포인트를 반환합니다. 그렇지 않은 경우 Azure AD는 사용자가 관리되고 있으므로 사용자가 Azure AD에서 인증을 받을 수 있음을 나타냅니다. |
| C | 사용자가 관리되는 경우 CloudAP는 Azure AD에서 nonce를 가져옵니다. 사용자가 페더레이션된 경우 CloudAP 플러그 인은 페더레이션 공급자에서 사용자 자격 증명을 사용하여 SAML 토큰을 요청합니다. 플러그 인은 SAML 토큰을 받으면 Azure AD에서 nonce를 요청합니다. |
| D | CloudAP 플러그 인은 사용자의 자격 증명, nonce 및 broker 범위를 사용하여 인증 요청을 생성하고, 디바이스 키(dkpriv)를 사용하여 요청에 서명하고 Azure AD에 보냅니다. 페더레이션된 환경에서 CloudAP 플러그 인은 사용자 자격 증명 대신, 페더레이션 공급자가 반환하는 SAML 토큰을 사용합니다. |
| E | Azure AD는 사용자 자격 증명, nonce 및 디바이스 서명이 유효한지 검사하고 디바이스가 테넌트에서 유효한지 확인하고 암호화된 PRT를 발급합니다. 또한 Azure AD는 PRT와 함께 전송 키(tkpub)를 사용하여 Azure AD에서 암호화된 세션 키 라는 대칭 키를 발급합니다. 또한 세션 키는 PRT에도 포함되어 있습니다. 이 세션 키는 PRT를 사용하는 후속 요청에 대한 PoP(소유 증명) 키 역할을 합니다. |
| F | CloudAP 플러그 인은 암호화된 PRT 및 세션 키를 CloudAP에 전달합니다. CloudAP는 전송 키(tkpriv)를 사용하여 세션 키의 암호를 해독하고 TPM의 자체 키를 사용해서 다시 암호화하도록 TPM에 요청합니다. CloudAP는 암호화된 세션 키를 PRT와 함께 캐시에 저장합니다. |

### <a name="prt-renewal-in-subsequent-logons"></a>후속 로그온 시 PRT 갱신

![후속 로그온 시 PRT 갱신](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 단계 | Description |
| :---: | --- |
| A | 사용자가 로그인 UI에 암호를 입력합니다. LogonUI는 인증 버퍼의 자격 증명을 LSA에 전달하며 LSA에서는 이 자격 증명을 내부적으로 CloudAP에 전달합니다. CloudAP는 이 요청을 CloudAP 플러그 인으로 전달합니다. |
| b | 사용자가 이전에 로그온한 적이 있는 경우 Windows는 캐시된 로그인을 시작하고 자격 증명이 유효한지 검사하여 사용자를 로그인합니다. 4시간마다 CloudAP 플러그 인은 비동기식으로 PRT 갱신을 시작합니다. |
| C | CloudAP 플러그 인은 사용자에 대한 ID 공급자를 식별하기 위한 영역 검색 요청을 시작합니다. 사용자의 테넌트에 페더레이션 공급자가 설정된 경우 Azure AD는 페더레이션 공급자의 MEX(Metadata Exchange Endpoint) 엔드포인트를 반환합니다. 그렇지 않은 경우 Azure AD는 사용자가 관리되고 있으므로 사용자가 Azure AD에서 인증을 받을 수 있음을 나타냅니다. |
| D | 사용자가 페더레이션된 경우 CloudAP 플러그 인은 페더레이션 공급자에서 사용자 자격 증명을 사용하여 SAML 토큰을 요청합니다. 플러그 인은 SAML 토큰을 받으면 Azure AD에서 nonce를 요청합니다. 사용자가 관리되는 경우 CloudAP는 Azure AD에서 직접 nonce를 가져옵니다. |
| E | CloudAP 플러그 인은 사용자의 자격 증명, nonce 및 기존 PRT를 사용하여 인증 요청을 생성하고, 세션 키를 사용하여 요청에 서명하고 Azure AD에 보냅니다. 페더레이션된 환경에서 CloudAP 플러그 인은 사용자 자격 증명 대신, 페더레이션 공급자가 반환하는 SAML 토큰을 사용합니다. |
| F | Azure AD는 PRT에 포함된 세션 키와 비교하여 세션 키 서명이 유효한지 검사하고, nonce가 유효한지 검사하고, 디바이스가 테넌트에서 유효한지 확인한 후 새 PRT를 발급합니다. 앞에서 확인한 것처럼 PRT는 전송 키(tkpub)로 암호화된 세션 키와 함께 다시 제공됩니다. |
| G | CloudAP 플러그 인은 암호화된 PRT 및 세션 키를 CloudAP에 전달합니다. CloudAP는 전송 키(tkpriv)를 사용하여 세션 키의 암호를 해독하고 TPM의 자체 키를 사용해서 다시 암호화하도록 TPM에 요청합니다. CloudAP는 암호화된 세션 키를 PRT와 함께 캐시에 저장합니다. |

> [!NOTE]
> usernamemixed 엔드포인트가 외부에서 사용하도록 설정되면 VPN 연결 없이 PRT를 외부에서 갱신할 수 있습니다.

### <a name="prt-usage-during-app-token-requests"></a>앱 토큰 요청 중에 PRT 사용

![앱 토큰 요청 중에 PRT 사용](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 단계 | Description |
| :---: | --- |
| A | 애플리케이션(예: Outlook, OneNote 등)은 WAM에 대한 토큰 요청을 시작합니다. 그런 다음, WAM은 Azure AD WAM 플러그 인에 토큰 요청을 제공하도록 요청합니다. |
| b | 애플리케이션에 대한 새로 고침 토큰을 이미 사용할 수 있는 경우 Azure AD WAM 플러그 인은 이 토큰을 사용하여 액세스 토큰을 요청합니다. 디바이스 바인딩 증명을 제공하기 위해 WAM 플러그 인은 세션 키를 사용하여 요청에 서명합니다. Azure AD는 세션 키가 유효한지 검사하고, 세션 키로 암호화된 앱에 대해 액세스 토큰 및 새 새로 고침 토큰을 발급합니다. WAM 플러그 인은 토큰의 암호를 해독하도록 CloudAP 플러그 인에 요청합니다. 그러면 CloudAP 플러그 인은 세션 키를 사용하여 암호를 해독하도록 TPM에 요청하고 WAM 플러그 인은 두 토큰을 모두 가져옵니다. 그런 다음, WAM 플러그 인은 DPAPI를 사용하여 새로 고침 토큰을 다시 암호화한 후 자체 캐시에 저장하지만 애플리케이션에 액세스 토큰만 제공합니다.  |
| C |  애플리케이션에 대한 새로 고침 토큰을 사용할 수 없는 경우 Azure AD WAM 플러그 인은 PRT를 사용하여 액세스 토큰을 요청합니다. 소유 증명을 제공하기 위해 WAM 플러그 인은 PRT를 포함하는 요청에 세션 키로 서명합니다. Azure AD는 PRT에 포함된 세션 키와 비교하여 세션 키 서명이 유효한지 검사하고, 디바이스가 유효한지 확인한 후 애플리케이션에 대한 액세스 토큰 및 새로 고침 토큰을 발급합니다. 또한 Azure AD는 새로 고침 주기를 기준으로 새 PRT를 발급할 수 있으며 이러한 PRT는 모두 세션 키로 암호화됩니다. |
| D | WAM 플러그 인은 토큰의 암호를 해독하도록 CloudAP 플러그 인에 요청합니다. 그러면 CloudAP 플러그 인은 세션 키를 사용하여 암호를 해독하도록 TPM에 요청하고 WAM 플러그 인은 두 토큰을 모두 가져옵니다. 그런 다음, WAM 플러그 인은 DPAPI를 사용하여 새로 고침 토큰을 다시 암호화한 후 자체 캐시에 저장하지만 애플리케이션에 액세스 토큰만 제공합니다. WAM 플러그 인은 이 애플리케이션에 대해 계속해서 새로 고침 토큰을 사용합니다. 또한 WAM 플러그 인은 새 PRT를 CloudAP 플러그 인에 다시 제공합니다. 그러면 CloudAP 플러그 인은 Azure AD를 사용하여 PRT가 유효한지 검사한 후 자체 캐시로 업데이트합니다. CloudAP 플러그 인 계속해서 새 PRT를 사용하게 됩니다. |
| E | WAM은 WAM에 대해 새로 발급된 액세스 토큰을 제공하고, 호출 애플리케이션에 다시 제공합니다.|

### <a name="browser-sso-using-prt"></a>PRT를 사용하는 브라우저 SSO

![PRT를 사용하는 브라우저 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 단계 | Description |
| :---: | --- |
| A | 사용자는 자격 증명을 사용하여 Windows에 로그인한 후 PRT를 가져옵니다. 사용자가 브라우저를 열면 브라우저(또는 확장)가 레지스트리에서 URL을 로드합니다. |
| b | 사용자가 Azure AD 로그인 URL을 열면 브라우저나 확장에서 레지스트리에서 가져온 URL로 해당 URL이 유효한지 검사합니다. 두 URL이 일치하는 경우 브라우저는 토큰을 가져오기 위해 네이티브 클라이언트 호스트를 호출합니다. |
| C | 네이티브 클라이언트 호스트는 URL이 Microsoft ID 공급자(Microsoft 계정 또는 Azure AD)에 속하는지 확인하고, URL에서 보낸 nonce를 추출하고, CloudAP 플러그 인을 호출하여 PRT 쿠키를 가져옵니다. |
| D | CloudAP 플러그 인은 PRT 쿠키를 만들고 TPM 바인딩 세션 키로 로그인한 다음, 다시 네이티브 클라이언트 호스트로 보냅니다. |
| E | 네이티브 클라이언트 호스트는 이 PRT 쿠키를 브라우저에 반환합니다. 브라우저는 x-ms-RefreshTokenCredential이라는 요청 헤더의 일부로 이 쿠키를 포함하고 Azure AD에서 토큰을 요청합니다. |
| F | Azure AD는 PRT 쿠키에서 세션 키 서명이 유효한지 검사하고, nonce가 유효한지 검사하고, 디바이스가 테넌트에서 유효한지 확인하고, 웹 페이지를 위한 ID 토큰과 브라우저를 위한 암호화된 세션 쿠키를 발급합니다. |

> [!NOTE]
> 위의 단계에서 설명한 브라우저 SSO 흐름은 Microsoft Edge의 InPrivate 또는 Google Chrome의 시크릿 모드(Microsoft 계정 확장을 사용하는 경우)와 같은 비공개 모드의 세션에는 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

PRT 관련 문제를 해결하는 방법에 대한 자세한 내용은 [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md) 문서를 참조하세요.
