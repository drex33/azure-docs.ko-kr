---
title: 쿠키 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용되는 쿠키에 대한 정의를 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: e970b7981531418748c91fed2d62b3b3e13d9590
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037573"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Azure AD B2C 쿠키 정의

다음 섹션에서는 Azure AD B2C(Azure Active Directory B2C)에서 사용되는 쿠키에 대한 정보를 제공합니다.

## <a name="samesite"></a>SameSite

Microsoft Azure AD B2C 서비스는 `Secure` 특성을 사용하는 `SameSite=None` 지원을 포함하여 SameSite 브라우저 구성과 호환됩니다.

사이트에 대한 액세스를 보호하기 위해 웹 브라우저에서는 기본적으로 보호되는 새 모델을 도입하여 별도로 지정하지 않는 한 모든 쿠키를 외부 액세스로부터 보호해야 한다고 가정합니다. 이러한 변경 내용이 구현된 첫 번째 브라우저는 Chrome 브라우저이며, [2020년 2월에 출시된 Chrome 80](https://www.chromium.org/updates/same-site)부터 시작됩니다. Chrome의 변경 준비에 대한 자세한 내용은 Chromium Blog의 [Developers: Get Ready for New SameSite=None; Secure Cookie Settings](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)(개발자: 새 SameSite=None; Secure 쿠키 설정 준비)를 참조하세요.

개발자는 새 쿠키 설정인 `SameSite=None`을 사용하여 사이트 간 액세스를 위한 쿠키를 지정해야 합니다. `SameSite=None` 특성이 있는 경우 HTTPS 연결을 통해서만 사이트 간 쿠키에 액세스할 수 있도록 추가 `Secure` 특성을 사용해야 합니다. Azure AD B2C를 사용하는 애플리케이션을 포함하여 모든 애플리케이션의 유효성을 검사하고 테스트하세요.

자세한 내용은 다음을 참조하세요.

* [Chrome 브라우저에서 SameSite 쿠키 변경 내용 처리](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Chrome 버전 80 이상에서 고객 웹 사이트와 Microsoft 서비스 및 제품에 미치는 영향](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>쿠키

다음 표에는 Azure AD B2C에서 사용되는 쿠키가 표시되어 있습니다.

| 이름 | 도메인 | 만료 | 목적 |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | [브라우저 세션](session-behavior.md) 종료 | 테넌트의 사용자 멤버 자격 데이터를 보존합니다. 사용자가 구성원인 테넌트 및 멤버 자격 수준(관리자 또는 사용자)입니다. |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | 요청을 적절한 프로덕션 인스턴스로 라우팅하는 데 사용됩니다. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | 트랜잭션(Azure AD B2C에 대한 인증 요청 수) 및 현재 트랜잭션을 추적하는 데 사용됩니다. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | SSO 세션을 유지 관리하는 데 사용됩니다. 이 쿠키는 [로그인 유지](session-behavior.md#enable-keep-me-signed-in-kmsi)가 사용하도록 설정된 경우 `persistent`로 설정됩니다.|
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료, 인증 성공 | 요청 상태를 유지 관리하는 데 사용됩니다. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | CRSF 보호에 사용되는 교차 사이트 요청 위조 토큰입니다. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | Azure AD B2C 네트워크 라우팅에 사용됩니다. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | 리소스 공급자 테넌트의 멤버 자격 데이터를 저장하는 데 사용됩니다. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, 브랜드 도메인 | [브라우저 세션](session-behavior.md) 종료 | 릴레이 쿠키를 저장하는 데 사용됩니다. |
