---
title: 애플리케이션 프록시 애플리케이션 디버그 - Azure Active Directory
description: Azure Active Directory(Azure AD) 애플리케이션 프록시 애플리케이션의 문제를 디버그합니다.
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
ms.openlocfilehash: a1bd86432fbe4b24904515b3773b944cc88a4a7c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989649"
---
# <a name="debug-application-proxy-application-issues"></a>애플리케이션 프록시 디버그 애플리케이션 이슈 

이 문서는 Azure Active Directory(Azure AD) 애플리케이션 프록시 문제 해결에 도움이 됩니다. 애플리케이션 프록시 서비스를 사용하여 온-프레미스 웹 애플리케이션에 대한 원격 액세스를 하지만 애플리케이션에 연결하는 데 문제가 있는 경우 이 순서도를 사용하여 애플리케이션 문제를 디버그하세요. 

## <a name="before-you-begin"></a>시작하기 전에

애플리케이션 프록시 문제 해결 시 커넥터를 사용하여 시작하는 것이 좋습니다. 먼저 [애플리케이션 프록시 커넥터 문제 디버그](application-proxy-debug-connectors.md)에서 문제 해결 흐름을 따라 애플리케이션 프록시 커넥터가 올바르게 구성되어 있는지 확인합니다. 문제가 여전히 발생하는 경우 애플리케이션 문제 해결을 위해 이 문서를 참조하세요.  

애플리케이션 프록시에 대한 자세한 내용은 다음을 참조하세요.

- [애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격 액세스](application-proxy.md)
- [애플리케이션 프록시 커넥터](application-proxy-connectors.md)
- [커넥터 설치 및 등록](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>애플리케이션 문제에 대한 순서도

이 순서도는 애플리케이션에 연결하는 것과 관련된 몇 가지 일반적인 문제를 디버깅하는 단계를 안내합니다. 각 단계에 대한 세부 정보는 순서도 다음에 나오는 표를 참조하세요.

![애플리케이션 디버깅 단계를 보여 주는 순서도](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| 단계 | 작업 | 설명 |
|---------|---------|---------|
|1 | 브라우저를 열고 앱에 액세스한 다음 자격 증명을 입력합니다. | 자격 증명을 사용하여 앱에 로그인하고, [이 회사 앱에 액세스할 수 없는](application-proxy-sign-in-bad-gateway-timeout-error.md) 경우와 같은 사용자 관련 오류를 확인하세요. |
|2 | 앱에 사용자 할당 확인 | 사용자 계정에 회사 네트워크 내부에서 앱에 액세스할 수 있는 권한이 있는지 확인한 다음 [애플리케이션 테스트](application-proxy-add-on-premises-application.md#test-the-application) 단계를 수행하여 앱 로그인을 테스트합니다. 로그인 문제가 지속되는 경우 [로그인 오류 문제 해결 방법](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)을 참조하세요.  |
|3 | 브라우저를 열고 앱에 액세스를 시도합니다. | 오류가 즉시 표시되면 애플리케이션 프록시가 올바르게 구성되어 있는지 확인하세요. 특정 오류 메시지에 대한 세부 정보는 [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조하세요.  |
|4 | 사용자 지정 도메인 설정 확인 또는 오류 문제 해결 | 페이지가 전혀 표시되지 않으면 [사용자 지정 도메인에 대한 작업](application-proxy-configure-custom-domain.md)을 검토하여 사용자 지정 도메인이 올바르게 구성되어 있는지 확인합니다.<br></br>페이지가 로드되지 않고 오류 메시지가 표시되면 [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조하여 오류를 해결하세요. <br></br>오류 메시지를 표시하는 데 20초 이상이 소요되는 경우 연결 문제가 있을 수 있습니다. [디버그 애플리케이션 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서로 이동합니다.  |
|5 | 문제가 지속되면 커넥터 디버깅으로 이동합니다. | 프록시와 커넥터 사이 또는 커넥터와 백 엔드 사이에 연결 문제가 있을 수 있습니다. [디버그 애플리케이션 프록시 커넥터](application-proxy-debug-connectors.md) 문제 해결 문서로 이동합니다. |
|6 | 모든 리소스를 게시하고, 브라우저 개발자 도구를 확인하고, 링크를 수정합니다. | 애플리케이션에 필요한 모든 이미지, 스크립트 및 스타일 시트가 게시 경로에 포함되어 있는지 확인합니다. 세부 정보는 [Azure AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요. <br></br>브라우저의 개발자 도구(Internet Explorer 또는 Microsoft Edge의 F12 도구)를 사용하여 [애플리케이션 페이지가 제대로 표시되지 않음](application-proxy-page-appearance-broken-problem.md)에 설명된 게시 문제를 확인합니다. <br></br>[페이지의 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)에 따라 끊어진 링크를 확인하는 옵션을 검토합니다. |
|7 | 네트워크 대기 시간 확인 | 페이지가 느리게 로드되는 경우 [대기 시간을 줄이기 위한 고려사항](application-proxy-network-topology.md#considerations-for-reducing-latency)에서 네트워크 대기 시간을 최소화하는 방법에 대해 알아보세요. | 
|8 | 추가적인 문제 해결 도움말 | 문제가 지속되면 [애플리케이션 프록시 문제 해결 설명서](application-proxy-troubleshoot.md)에서 추가 문제 해결 문서를 찾습니다. |

## <a name="next-steps"></a>다음 단계


* [커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시](application-proxy-connector-groups.md)
* [기존 온-프레미스 프록시 서버 작업](application-proxy-configure-connectors-with-proxy-servers.md)
* [애플리케이션 프록시 및 커넥터 오류 문제 해결](application-proxy-troubleshoot.md)
* [Azure AD 애플리케이션 프록시 커넥터를 자동으로 설치하는 방법](application-proxy-register-connector-powershell.md)
