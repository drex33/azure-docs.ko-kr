---
title: 2단계 인증 Azure AD MFA 및 ADFS - Azure Active Directory
description: Azure MFA 및 AD FS를 시작하는 방법을 설명하는 Azure AD Multi-Factor Authentication 페이지입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9abc9e2e2ca2b9305cccf5cec554994c3703ef1
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214497"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Azure AD Multi-Factor Authentication 및 Active Directory Federation Services 시작

<center>

![Azure AD MFA 및 ADFS 시작](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

조직에서 AD FS를 사용하여 온-프레미스 Active Directory를 Azure Active Directory에 페더레이션한 경우 Azure AD Multi-Factor Authentication을 사용하는 두 가지 옵션이 있습니다.

* Azure AD Multi-Factor Authentication 또는 Active Directory Federation Services를 사용하여 클라우드 리소스 보안 유지
* Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지

다음 표에서는 리소스 보안 유지를 위해 Azure AD Multi-Factor Authentication을 사용하는 경우와 AD FS를 사용하는 경우의 확인 환경을 요약해서 설명합니다.

| 확인 환경 - 브라우저 기반 앱 | 확인 환경 - 비 브라우저 기반 앱 |
|:--- |:--- |
| Azure AD Multi-Factor Authentication을 사용하여 Azure AD 리소스 보안 유지 |<li>첫 번째 확인 단계는 AD FS를 사용하여 온-프레미스에서 수행됩니다.</li> <li>두 번째 단계는 클라우드 인증을 사용하여 수행되는 휴대폰 기반 방법입니다.</li> |
| Active Directory Federation Services를 사용하여 Azure AD 리소스 보안 유지 |<li>첫 번째 확인 단계는 AD FS를 사용하여 온-프레미스에서 수행됩니다.</li><li>두 번째 단계는 클레임을 적용하여 온-프레미스에서 수행됩니다.</li> |

페더레이션된 사용자의 앱 암호 관련 주의 사항:

* 앱 암호는 클라우드 인증을 사용하여 확인되므로 페더레이션을 바이패스합니다. 앱 암호를 설정할 때 페더레이션이 능동적으로 사용됩니다.
* 앱 암호를 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 앱 암호에 대한 온-프레미스 인증 로깅 기능이 손실됩니다.
* 계정 사용 안 함/삭제 설정은 디렉터리 동기화 동안 최대 3시간이 걸리며 클라우드 ID에서 앱 암호의 사용 안 함/삭제가 지연됩니다.

Azure Multi-Factor Authentication 또는 AD FS를 통한 Azure Multi-factor Authentication Server를 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure AD Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안 유지](howto-mfa-adfs.md)
* [Windows Server와 Azure Multi-Factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지](howto-mfaserver-adfs-windows-server.md)
* [AD FS 2.0과 함께 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지](howto-mfaserver-adfs-2.md)
