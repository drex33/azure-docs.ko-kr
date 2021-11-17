---
title: ID 공급자 추가 - Azure Active Directory B2C
description: Azure Active Directory B2C 테넌트에 ID 공급자를 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 04/29/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 92cbc796d4372c6127922ac6cac11d803a29b293
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039983"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트에 ID 공급자 추가

사용자가 외부 소셜 또는 엔터프라이즈 IdP(ID 공급자)의 자격 증명을 사용하여 애플리케이션에 로그인할 수 있도록 Azure AD B2C를 구성할 수 있습니다. Azure AD B2C는 Facebook, Microsoft 계정, Google, Twitter와 같은 외부 ID 공급자와 OAuth 1.0, OAuth 2.0, OpenID Connect 및 SAML 프로토콜을 지원하는 모든 ID 공급자를 지원합니다.

외부 ID 공급자 페더레이션을 사용하면 애플리케이션 전용의 새 계정을 만들 필요 없이 기존 소셜 또는 엔터프라이즈 계정으로 로그인할 수 있는 기능을 소비자에게 제공할 수 있습니다.

Azure AD B2C는 가입 또는 로그인 페이지에서 사용자가 로그인을 위해 선택할 수 있는 외부 ID 공급자의 목록을 제공합니다. 외부 ID 공급자 중 하나를 선택하면 로그인 프로세스를 수행할 수 있도록 선택한 공급자의 웹 사이트로 이동(리디렉션)됩니다. 사용자가 성공적으로 로그인하면 애플리케이션의 계정 인증을 위해 Azure AD B2C로 반환됩니다.

![소셜 계정이 있는 모바일 로그인 예제(Facebook)](media/add-identity-provider/external-idp.png)

Azure Portal을 사용하여 Azure AD B2C(Azure Active Directory B2C)에서 지원하는 ID 공급자를 [사용자 흐름](user-flow-overview.md)에 추가할 수 있습니다. [사용자 지정 정책](user-flow-overview.md)에 ID 공급자를 추가할 수도 있습니다.

## <a name="select-an-identity-provider"></a>ID 공급자 선택

일반적으로 애플리케이션에서 하나의 ID 공급자만 사용하지만, ID 공급자를 더 추가할 수 있습니다. 아래 방법 문서에서는 ID 공급자 애플리케이션을 만들고, ID 공급자를 테넌트에 추가하고, 사용자 흐름 또는 사용자 지정 정책에 ID 공급자를 추가하는 방법을 보여 줍니다.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD(단일 테넌트)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD(다중 테넌트)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [eBay](identity-provider-ebay.md)
* [Facebook](identity-provider-facebook.md)
* [일반 ID 공급자](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft 계정](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce(SAML 프로토콜)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
