---
title: Azure Active Directory 애플리케이션 프록시를 사용하여 온-프레미스 앱에 로그인하는 데 문제가 있음
description: Azure Active Directory 애플리케이션 프록시를 사용하여 통합된 온-프레미스 애플리케이션에 로그인할 수 없을 때 직면하는 일반적인 문제 해결
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: b51bd97f4ee79358230e85f94351abc937bf5225
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988452"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션에 로그인하는 데 문제가 있음

온-프레미스 애플리케이션에 로그인하는 데 문제가 있을 경우 아래 단계를 시도하여 문제를 해결할 수 있습니다.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>내 애플리케이션을 로드할 수 있지만 페이지의 일부가 손상됨

다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.

  * [내 애플리케이션으로 이동할 수 있지만 애플리케이션 페이지가 올바르게 표시되지 않음](application-proxy-page-appearance-broken-problem.md)
  * [내 애플리케이션으로 이동할 수 있지만 애플리케이션을 로드하는 데 시간이 너무 오래 걸림](application-proxy-page-load-speed-problem.md)
  * [내 애플리케이션으로 이동할 수 있지만 애플리케이션 페이지의 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>내 애플리케이션에 연결 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [내 애플리케이션에 대해 어떤 포트를 열지 모름](application-proxy-add-on-premises-application.md)
  * [내 애플리케이션에 대한 커넥터 그룹에 작동 중인 커넥터가 없으므로 문제가 발생함](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>관리 포털에서 Azure AD 애플리케이션 프록시를 구성하는 데 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [애플리케이션 프록시 애플리케이션을 구성하는 데 문제가 있음](application-proxy-config-how-to.md)
  * [내 애플리케이션 프록시 애플리케이션에 대해 Single Sign-On을 구성하는 방법을 모름](application-proxy-config-sso-how-to.md)
  * [관리 포털에 내 애플리케이션을 만들 때 문제가 발생함](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>내 애플리케이션에 백 엔드 인증을 설정하는 데 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [Kerberos 제한 위임을 구성하는 방법을 모름](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [PingAccess로 내 애플리케이션을 구성하는 방법을 모름](application-proxy-ping-access-publishing-guide.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>내 애플리케이션에 로그인할 때 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * ["Can't Access this Corporate Application"(회사 애플리케이션에 액세스할 수 없음) 오류가 표시됨](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>애플리케이션 프록시 에이전트 커넥터에 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [애플리케이션 프록시 에이전트 커넥터를 설치하는 데 문제가 있음](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>다음 단계
[온-프레미스 애플리케이션에 대한 보안 원격 액세스를 제공하는 방법](application-proxy.md)