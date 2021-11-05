---
title: 'Azure AD Connect: Seamless Single Sign-On - 작동 방식 | Microsoft Docs'
description: 이 문서에서는 Azure Active Directory Seamless Single Sign-On 기능이 작동하는 방식을 설명합니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb536efebfa56e14ea2a14ecea088b26d9ec9461
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068096"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory Seamless Single Sign-On: 기술 심층 분석

이 문서에서는 Azure Active Directory Seamless SSO(Seamless Single Sign-On) 기능이 어떻게 작동하는지에 대한 기술적인 정보에 대해 자세히 설명합니다.

## <a name="how-does-seamless-sso-work"></a>Seamless SSO 작동 방식

이 섹션은 다음 세 부분으로 이루어져 있습니다.

1. Seamless SSO 기능 설정
2. 웹 브라우저의 단일 사용자 로그인 트랜잭션이 Seamless SSO와 작동하는 방식
3. 네이티브 클라이언트의 단일 사용자 로그인 트랜잭션이 Seamless SSO와 작동하는 방식

### <a name="how-does-set-up-work"></a>기능을 설정하는 방식

Seamless SSO는 [여기](how-to-connect-sso-quick-start.md)서 보여 주듯이 Azure AD Connect를 통해 사용하도록 설정할 수 있습니다. 이 기능을 사용하도록 설정하는 동안 발생하는 단계는 다음과 같습니다.

- 컴퓨터 계정(`AZUREADSSOACC`)은 Azure AD Connect를 사용하여 Azure AD에 동기화하는 각 AD 포리스트의 온-프레미스 AD(Active Directory)에 생성됩니다.
- 또한 Azure AD 로그인 프로세스 중에 사용하기 위해 다양한 Kerberos SPN(서비스 사용자 이름)이 생성됩니다.
- 컴퓨터 계정의 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다. AD 포리스트가 여러 개인 경우 각 컴퓨터 계정에는 고유한 Kerberos 암호 해독 키가 있습니다.

>[!IMPORTANT]
> 보안상의 이유로 `AZUREADSSOACC` 컴퓨터 계정은 강력하게 보호되어야 합니다. 도메인 관리자만 컴퓨터 계정을 관리할 수 있어야 합니다. 컴퓨터 계정에 대한 Kerberos 위임이 사용하지 않도록 설정되어 있고 Active Directory의 다른 계정에 컴퓨터 계정 `AZUREADSSOACC`에 대한 위임 권한이 없도록 확인하세요. 실수로 삭제될 위험이 없고 도메인 관리자만 액세스할 수 있는 OU(조직 구성 단위)에 컴퓨터 계정을 저장합니다. 컴퓨터 계정의 Kerberos 암호 해독 키도 중요한 것으로 간주됩니다. 적어도 30일마다 `AZUREADSSOACC` 컴퓨터 계정의 [Kerberos 암호 해독 키를 롤오버](how-to-connect-sso-faq.yml)하는 것이 좋습니다.

>[!IMPORTANT]
> Seamless SSO는 Kerberos에 대해 `AES256_HMAC_SHA1`, `AES128_HMAC_SHA1`, `RC4_HMAC_MD5` 암호화 유형을 지원합니다. `AzureADSSOAcc$` 계정의 암호화 유형은 `AES256_HMAC_SHA1`로 설정하거나 추가 보안을 위해 AES 및 RC4 유형 중 하나로 설정하는 것이 좋습니다. 암호화 유형은 Active Directory에 있는 계정의 `msDS-SupportedEncryptionTypes` 특성에 저장됩니다.  `AzureADSSOAcc$` 계정 암호화 유형을 `RC4_HMAC_MD5`로 설정하고 AES 암호화 유형 중 하나로 변경하려는 경우 관련 질문에 따라 [FAQ 문서`AzureADSSOAcc$`에 설명된 대로 먼저 ](how-to-connect-sso-faq.yml) 계정의 Kerberos 암호 해독 키를 롤오버하는지 확인하세요. 확인하지 않으면 Seamless SSO가 발생하지 않습니다.

설정이 완료되면 Seamless SSO는 IWA(Windows 통합 인증)를 사용하는 다른 로그인과 동일한 방식으로 작동합니다.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>웹 브라우저에서 Seamless SSO로 로그인하는 방식

웹 브라우저의 로그인 흐름은 다음과 같습니다.

1. 사용자가 회사 네트워크의 도메인 가입 회사 디바이스에서 웹 애플리케이션(예: Outlook 웹앱 https://outlook.office365.com/owa/))에 액세스하려고 합니다.
2. 사용자가 아직 로그인하지 않은 경우 해당 사용자는 Azure AD 로그인 페이지로 리디렉션됩니다.
3. 사용자는 자신의 사용자 이름을 Azure AD 로그인 페이지에 입력합니다.

   >[!NOTE]
   >[특정 애플리케이션](./how-to-connect-sso-faq.yml)의 경우 2-3단계를 건너뜁니다.

4. Azure AD는 백그라운드에서 JavaScript를 사용하여 401 권한 없음 응답을 통해 브라우저에 Kerberos 티켓을 제공합니다.
5. 그런 다음 브라우저는 Active Directory에서 Azure AD를 나타내는 `AZUREADSSOACC` 컴퓨터 계정에 대한 티켓을 요청합니다.
6. Active Directory는 컴퓨터 계정을 찾아서 컴퓨터 계정의 비밀로 암호화된 Kerberos 티켓을 브라우저에 반환합니다.
7. 브라우저는 Active Directory에서 받은 Kerberos 티켓을 Azure AD에 전달합니다.
8. Azure AD는 이전에 공유한 키를 사용하여 회사 디바이스에 로그인한 사용자의 ID가 포함된 Kerberos 티켓을 암호 해독합니다.
9. 평가 후에 Azure AD는 애플리케이션에 토큰을 반환하거나 사용자에게 Multi-Factor Authentication과 같은 추가 증명을 수행하도록 요청합니다.
10. 사용자 로그인에 성공하면 해당 사용자는 해당 애플리케이션에 액세스할 수 있습니다.

다음 다이어그램은 관련된 모든 구성 요소와 단계를 보여 줍니다.

![Seamless Single Sign-On - 웹앱 흐름](./media/how-to-connect-sso-how-it-works/sso2.png)

Seamless SSO는 편의적인 기능입니다. 이는 SSO가 실패하면 로그인 환경은 일반 동작으로 돌아감을 의미합니다. 즉 사용자가 자신의 암호를 입력하여 로그인해야 합니다.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>네이티브 클라이언트에서 Seamless SSO로 로그인하는 방식

네이티브 클라이언트의 로그인 흐름은 다음과 같습니다.

1. 사용자가 회사 네트워크의 도메인 가입 회사 디바이스에서 네이티브 애플리케이션(예: Outlook 클라이언트)에 액세스하려고 합니다.
2. 사용자가 아직 로그인되지 않은 경우 네이티브 애플리케이션은 디바이스의 Windows 세션에서 사용자의 사용자 이름을 검색합니다.
3. 앱은 Azure AD에 사용자 이름을 전송하고, 테넌트의 WS-Trust MEX 엔드포인트를 검색합니다. 이 WS-Trust 엔드포인트는 Seamless SSO 기능에 의해 단독으로 사용되며 Azure AD에서 WS-Trust 프로토콜의 일반적인 구현이 아닙니다.
4. 그런 후 WS-Trust MEX 엔드포인트를 쿼리하여 통합된 인증 엔드포인트를 사용할 수 있는지 확인합니다. 통합 인증 엔드포인트는 Seamless SSO 기능에 의해 단독으로 사용됩니다.
5. 4단계가 성공하면 Kerberos 챌린지가 발급됩니다.
6. 앱은 Kerberos 티켓을 검색할 수 있으면 Azure AD의 통합된 인증 엔드포인트까지 전달합니다.
7. Azure AD는 Kerberos 티켓의 암호를 해독하고 유효한지 검사합니다.
8. Azure AD에서 사용자가 로그인되고, 앱으로 SAML 토큰이 발급됩니다.
9. 그런 후 앱은 Azure AD의 OAuth2 토큰 엔드포인트로 SAML 토큰을 제출합니다.
10. Azure AD는 SAML 토큰이 유효한지 검사하고, 지정된 리소스에 대한 액세스 토큰 및 새로 고침 토큰과 ID 토큰을 발급합니다.
11. 사용자는 앱의 리소스에 액세스할 수 있게 됩니다.

다음 다이어그램은 관련된 모든 구성 요소와 단계를 보여 줍니다.

![Seamless Single Sign On - 네이티브 앱 흐름](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>다음 단계

- [**빠른 시작**](how-to-connect-sso-quick-start.md) - Azure AD Seamless SSO를 준비하고 실행합니다.
- [**FAQ(질문과 대답)**](how-to-connect-sso-faq.yml) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](tshoot-connect-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) - 새로운 기능 요청을 제출합니다.
