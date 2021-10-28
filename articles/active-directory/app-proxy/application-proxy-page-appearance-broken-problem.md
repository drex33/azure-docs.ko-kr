---
title: 애플리케이션 프록시 앱의 앱 페이지가 올바르게 표시되지 않음
description: 페이지에서 Azure Active Directory와 통합한 애플리케이션 프록시 애플리케이션을 제대로 표시하지 않는 경우 가이드입니다.
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
ms.openlocfilehash: 0467d156b7bfe252317f866991a8c8763c61963f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989041"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>애플리케이션 페이지가 애플리케이션 프록시 애플리케이션에 올바르게 표시되지 않음

이 아티클을 통해 페이지로 이동하지만 페이지가 올바르게 표시하지 않는 경우 Azure Active Directory 애플리케이션 프록시 애플리케이션의 문제를 해결할 수 있습니다.

## <a name="overview"></a>개요
애플리케이션 프록시 앱을 게시하면 애플리케이션에 액세스할 때 루트의 페이지에 액세스할 수 있습니다. 페이지에 올바르게 표시되지 않으면 애플리케이션에 사용되는 루트 내부 URL에는 일부 페이지 리소스가 누락될 수 있습니다. 이 문제를 해결하려면 페이지의 *모든* 리소스를 애플리케이션의 일부분으로 게시했는지 확인합니다.

네트워크 추적기를 열고, 페이지를 로드하고, 404 오류를 찾아서 누락된 리소스가 문제인지를 확인할 수 있습니다(예: Internet Explorer/Microsoft Edge의 Fiddler 또는 F12 도구). 현재 찾을 수 없고 게시해야 하는 페이지를 나타냅니다.

이 경우에 예를 들어 내부 URL(`http://myapps/expenses`)을 사용하여 비용 애플리케이션을 게시했지만 앱에서 스타일 시트(`http://myapps/style.css`)를 사용한다고 가정합니다. 이 경우에 비용 앱을 로드하면 style.css를 로드하는 동안 404 오류를 throw하므로 스타일 시트는 애플리케이션에서 게시되지 않습니다. 이 예제에서는 내부 URL(`http://myapp/`)을 사용하여 애플리케이션을 게시하여 문제를 해결합니다.

## <a name="problems-with-publishing-as-one-application"></a>하나의 애플리케이션으로 게시하는 문제

동일한 애플리케이션 내에서 모든 리소스를 게시할 수 없는 경우 여러 애플리케이션을 게시하고 이들 간의 링크를 사용해야 합니다.

이렇게 하려면 [사용자 지정 도메인](application-proxy-configure-custom-domain.md) 솔루션을 사용하는 것이 좋습니다. 그러나 이 솔루션에서 사용자는 도메인의 인증서를 소유하고 애플리케이션은 FQDN(정규화된 도메인 이름)을 사용해야 합니다. 다른 옵션은 [끊어진 링크 문제 해결 설명서](application-proxy-page-links-broken-problem.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md)
