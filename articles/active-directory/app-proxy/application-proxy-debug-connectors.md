---
title: 애플리케이션 프록시 커넥터 디버그 - Azure Active Directory
description: Azure AD(Azure Active Directory) 애플리케이션 프록시 커넥터의 문제를 디버그합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: beb232054eb4f0ead8aaaaf3f878bca8ef360cbd
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989497"
---
# <a name="debug-application-proxy-connector-issues"></a>애플리케이션 프록시 커넥터 디버그 이슈 

이 문서는 Azure AD(Azure Active Directory) 애플리케이션 프록시 커넥터 문제 해결에 도움이 됩니다. 애플리케이션 프록시 서비스를 사용하여 온-프레미스 웹 애플리케이션에 대한 원격 액세스를 하지만 애플리케이션에 연결하는 데 문제가 있는 경우 이 순서도를 사용하여 커넥터 문제를 디버그하세요. 

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 애플리케이션 프록시 커넥터를 설치했으며 문제가 있는 것으로 가정합니다. 애플리케이션 프록시 문제를 해결할 때 애플리케이션 프록시 커넥터가 올바르게 구성되어 있는지 확인하기 위해 이 문제 해결을 시작하는 것이 좋습니다. 애플리케이션에 연결하는 문제가 계속 지속되는 경우 [애플리케이션 프록시 애플리케이션 디버그 문제](application-proxy-debug-apps.md)의 문제 해결 흐름을 따르세요.  


애플리케이션 및 커넥터 사용에 대한 자세한 내용은 다음을 참조하세요.

- [애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격 액세스](application-proxy.md)
- [애플리케이션 프록시 커넥터](application-proxy-connectors.md)
- [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>커넥터 문제에 대한 순서도

이 순서도는 일반적인 몇 가지 커넥터 문제를 디버깅하는 단계를 안내합니다. 각 단계에 대한 세부 정보는 순서도 다음에 나오는 표를 참조하세요.

![커넥터 디버깅 단계를 보여 주는 순서도](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| 단계 | 작업 | 설명 |
|---------|---------|---------|
|1 | 앱에 할당된 커넥터 그룹 찾기 | 여러 서버에 커넥터를 설치했을 수 있습니다. 이 경우 커넥터는 [커넥터 그룹에 할당](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)되어야 합니다. 커넥터 그룹에 대한 자세한 내용은 [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)를 참조하세요. |
|2 | 커넥터 설치 및 그룹 할당 | 커넥터가 설치되지 않은 경우 [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md#install-and-register-a-connector)을 참고하세요.<br></br> 커넥터 설치에 문제가 있는 경우 [커넥터 설치 문제](application-proxy-connector-installation-problem.md)를 참조하세요.<br></br> 커넥터가 그룹에 할당되지 않은 경우 [그룹에 커넥터 할당](application-proxy-connector-groups.md#create-connector-groups)을 참조하세요.<br></br>애플리케이션이 커넥터 그룹에 할당되지 않은 경우 [커넥터 그룹에 애플리케이션 할당](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)을 참조하세요.|
|3 | 커넥터 서버에서 포트 테스트 실행 | 커넥터 서버에서 [텔넷](/windows-server/administration/windows-commands/telnet) 또는 기타 포트 테스트 도구를 사용하여 포트 [443 및 80이 열려 있는지](application-proxy-add-on-premises-application.md#open-ports) 확인하는 방식으로 포트 테스트를 실행합니다.|
|4 | 도메인 및 포트 구성 | [도메인 및 포트가 올바르게 구성되어 있는지 확인](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 커넥터가 제대로 작동하려면 특정 포트가 열려 있어야 하고 서버가 액세스할 수 있는 URL이 있어야 합니다. |
|5 | 백 엔드 프록시가 사용 중인지 확인 | 커넥터에서 백 엔드 프록시 서버를 사용하고 있는지 또는 우회하는지 확인합니다. 자세한 내용은 [커넥터 프록시 및 서비스 연결 문제 해결](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)을 참조하세요. |
|6 | 백 엔드 프록시를 사용하도록 커넥터 및 업데이트 프로그램 업데이트 | 백 엔드 프록시를 사용 중인 경우 커넥터가 동일한 프록시를 사용하고 있는지 확인하는 것이 좋습니다. 프록시 서버를 사용하도록 커넥터를 구성하고 문제를 해결하는 방법에 대한 자세한 내용은 [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)을 참조하세요. |
|7 | 커넥터 서버에서 앱의 내부 URL 로드 | 커넥터 서버에서 앱의 내부 URL을 로드합니다. |
|8 | 내부 네트워크 연결 확인 | 내부 네트워크에 이 디버깅 흐름이 진단할 수 없는 연결 문제가 있습니다. 커넥터가 작동하려면 애플리케이션이 내부적으로 액세스할 수 있어야 합니다. [애플리케이션 프록시 커넥터](application-proxy-connectors.md#under-the-hood)에서 설명하는 것과 같이 커넥터 이벤트 로그를 사용 설정하고 볼 수 있습니다. |
|9 | 백 엔드에서 시간 제한 값 증가 | 애플리케이션에 대한 **추가 설정** 에서 **백 엔드 애플리케이션 시간 제한** 설정을 **장기** 로 변경합니다. [Azure AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요. |
|10 | 문제가 지속되는 경우 특정 흐름 문제를 해결하고 앱 및 SSO 디버깅 흐름을 검토합니다. | [애플리케이션 프록시 애플리케이션 문제 디버그](application-proxy-debug-apps.md) 문제 해결 흐름을 사용합니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)