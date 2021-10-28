---
title: Teams에서 Azure Active Directory 애플리케이션 프록시 앱에 액세스
description: Azure Active Directory 애플리케이션 프록시를 사용하여 Microsoft Teams를 통해 온-프레미스 애플리케이션에 액세스합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: harshja
ms.openlocfilehash: d609e7e6fdebd6d4329989d325e76390ccc684f2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989155"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams-with-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용하여 Microsoft Teams를 통해 온-프레미스 애플리케이션에 액세스

Azure Active Directory 애플리케이션 프록시는 장소에 관계없이 온-프레미스 애플리케이션에 Single Sign-On을 제공합니다. Microsoft Teams를 사용하면 한 곳에서 효율적으로 공동 작업을 수행할 수 있습니다. 이 두 가지를 통합하면 사용자가 어떤 상황에서도 팀 동료와 협력하여 생산성을 높일 수 있습니다.

사용자가 [탭을 사용](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)하여 해당 Teams 채널에 클라우드 앱을 추가할 수 있으나 온-프레미스에 호스트되는 SharePoint 사이트 또는 계획 도구에서는 어떻게 될까요? 애플리케이션 프록시는 솔루션입니다. 원격으로 앱에 액세스하는 데 항상 사용하는 것과 동일한 외부 URL을 사용하여 애플리케이션 프록시를 통해 게시된 앱을 해당 채널에 추가할 수 있습니다. 애플리케이션 프록시는 Azure Active Directory를 통해 인증하므로 사용자에게 Single Sign-On 환경이 제공됩니다.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>애플리케이션 프록시 커넥터 설치 및 앱 게시

아직 수행하지 않은 경우 [테넌트에 대해 애플리케이션 프록시를 구성하고 커넥터를 설치](../app-proxy/application-proxy-add-on-premises-application.md)합니다. 그런 다음, 원격 액세스를 위해 온-프레미스 애플리케이션을 게시합니다. Teams에 앱을 추가하는 경우 사용되므로 앱을 게시하는 경우 외부 URL을 기록해 둡니다.

이미 게시된 앱이 있으나 외부 URL을 기억하지 못하는 경우 [Azure Portal](https://portal.azure.com)에서 찾아보세요. 로그인한 다음, **Azure Active Directory** > **Enterprise 애플리케이션** > **모든 애플리케이션** &gt; 앱 선택 &gt; **애플리케이션 프록시** 로 이동합니다.

## <a name="add-your-app-to-teams"></a>Teams에 앱 추가

애플리케이션 프록시를 통해 앱을 게시하면 사용자가 해당 Teams 채널에서 직접 탭으로 추가할 수 있음을 알 수 있으며 앱이 사용할 팀의 모든 사람에게 제공됩니다. 사용자가 다음 세 가지 단계를 수행하도록 합니다.

1. 이 앱을 추가하려는Teams 채널로 이동하여 탭을 추가할 **+** 를 선택합니다.

   ![Teams에서 탭을 추가하려면 +를 선택합니다.](./media/application-proxy-integrate-with-teams/add-tab.png)

1. 탭 옵션에서 **웹 사이트** 를 선택합니다.

   ![탭 추가 화면에서 웹 사이트 선택](./media/application-proxy-integrate-with-teams/website.png)

1. 탭 이름을 지정하고 URL을 애플리케이션 프록시 외부 URL로 설정합니다.

   ![탭 이름을 지정하고 외부 URL 추가](./media/application-proxy-integrate-with-teams/tab-name-url.png)

팀의 한 멤버가 탭을 추가하면 채널의 모든 사용자에게 표시됩니다. 앱에 대한 액세스 권한이 있는 모든 사용자에게는 Microsoft Teams에 사용하는 자격 증명으로 Single Sign-On 액세스가 제공됩니다. 앱에 대한 액세스 권한이 없는 사용자는 Teams에서 탭을 볼 수 있지만 온-프레미스 앱 및 Azure Portal 게시 버전 앱에 대한 권한이 부여될 때까지 액세스가 차단됩니다.

## <a name="next-steps"></a>다음 단계

- 애플리케이션 프록시를 통해 [온-프레미스 SharePoint 사이트를 게시](application-proxy-integrate-with-sharepoint-server.md)하는 방법을 알아보세요.
- 앱에서 외부 URL에 대해 [사용자 지정 도메인](application-proxy-configure-custom-domain.md)을 사용하도록 구성합니다.
