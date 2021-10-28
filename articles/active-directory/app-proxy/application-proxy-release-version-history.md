---
title: 'Azure Active Directory 애플리케이션 프록시: 버전 릴리스 기록'
description: 이 문서에서는 Azure Active Directory 애플리케이션 프록시의 모든 릴리스를 나열하고 새로운 기능과 해결된 문제를 설명합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/27/2021
ms.subservice: app-proxy
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 7b00c6fb425f04d84c6f5d5c46b42865c05d5e02
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988907"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD 애플리케이션 프록시: 버전 릴리스 기록
이 문서에서는 릴리스된 Azure AD(Azure Active Directory) 애플리케이션 프록시의 버전과 기능을 나열합니다. Azure AD 팀은 새로운 기능으로 애플리케이션 프록시를 정기적으로 업데이트합니다. 애플리케이션 프록시 커넥터는 [새 주 버전이 릴리스되면 자동으로 업데이트](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-)됩니다. 

커넥터에 대해 자동 업데이트를 사용하도록 설정하여 최신 기능 및 버그 해결이 적용되도록 하는 것이 좋습니다. Microsoft 지원에서 문제를 해결하기 위해 최신 커넥터 버전을 설치하도록 요청할 수 있습니다.

다음은 관련 리소스 목록입니다.

| 리소스                                         | 세부 정보                                                      |
| ------------------------------------------------ | ------------------------------------------------------------ |
| 애플리케이션 프록시 사용 방법                  | 애플리케이션 프록시를 사용하고 커넥터를 설치 및 등록하는 데 있어서의 필수 조건은 이 [자습서](application-proxy-add-on-premises-application.md)에서 설명합니다. |
| Azure AD 애플리케이션 프록시 커넥터 이해 | [커넥터 관리](application-proxy-connectors.md)와 커넥터 [자동 업그레이드](application-proxy-connectors.md#automatic-updates) 방법을 자세히 알아보세요. |
| Azure AD 애플리케이션 프록시 커넥터 다운로드    | [최신 커넥터를 다운로드](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download)하세요. |

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>릴리스 상태

2020년 7월 22일: 다운로드용으로 릴리스된 이 버전은 다운로드 페이지를 통해서만 설치할 수 있습니다. 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
-   Azure Government 클라우드 환경 지원이 향상되었습니다. Azure Government 클라우드용 커넥터를 올바르게 설치하는 방법은 [필수 구성 요소](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls)와 [설치 단계](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)를 검토하세요.
- 애플리케이션 프록시를 사용하는 원격 데스크톱 서비스 웹 클라이언트 사용을 지원합니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 원격 데스크톱 게시](application-proxy-integrate-with-remote-desktop-services.md)를 참조하세요.
- Websocket 확장 협상이 향상되었습니다. 
- 지역을 기반으로 하는 커넥터 그룹과 애플리케이션 프록시 클라우드 서비스 간의 최적화된 라우팅을 지원합니다. 자세한 내용은 [Azure Active Directory 애플리케이션 프록시를 사용하여 트래픽 흐름 최적화](application-proxy-network-topology.md)를 참조하세요. 

### <a name="fixed-issues"></a>해결된 문제
- 소문자 문자열을 강제 적용하는 websocket 문제를 해결했습니다.
- 커넥터가 간헐적으로 응답하지 않는 문제를 해결했습니다.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>릴리스 상태

2020년 7월 17일: 다운로드용으로 릴리스되었습니다. 이 버전은 다운로드 페이지를 통해서만 설치할 수 있습니다. 

### <a name="fixed-issues"></a>해결된 문제
- 이전 버전의 메모리 누수 문제 해결
- websocket 지원에 대한 일반적인 개선 사항

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>릴리스 상태

2020년 4월 7일: 다운로드용으로 릴리스된 이 버전은 다운로드 페이지를 통해서만 설치할 수 있습니다. 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
-   커넥터는 모든 연결에 TLS 1.2만을 사용합니다. 자세한 내용은 [커넥터 필수 구성 요소](application-proxy-add-on-premises-application.md#prerequisites)를 참조하세요.
- 커넥터와 Azure 서비스 간의 신호가 향상되었습니다. 여기에는 커넥터와 Azure 서비스 간의 WCF 통신을 위한 신뢰할 수 있는 세션과 WebSocket 통신을 위한 DNS 캐싱 향상 지원이 포함됩니다.
- 커넥터와 백엔드 애플리케이션 간의 프록시 구성을 지원합니다. 자세한 내용은 [기존 온-프레미스 프록시 서버로 작업](application-proxy-configure-connectors-with-proxy-servers.md)을 참조하세요.

### <a name="fixed-issues"></a>해결된 문제
- 커넥터에서 Azure 서비스로의 통신을 위한 포트 8080에 대한 대체가 제거되었습니다.
- WebSocket 통신에 대한 디버그 추적을 추가했습니다. 
- 백엔드 애플리케이션 쿠키에 설정된 경우 SameSite 특성 보존을 해결했습니다.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>릴리스 상태

2018년 9월 20일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- QlikSense 애플리케이션에 대한 WebSocket 지원을 추가했습니다. QlikSense에 애플리케이션 프록시를 통합하는 방법을 자세히 알아보려면 이 [연습](application-proxy-qlik.md)을 참조하세요. 
- 아웃바운드 프록시를 쉽게 구성할 수 있도록 설치 마법사를 개선했습니다. 
- 커넥터의 기본 프로토콜로 TLS 1.2를 설정했습니다. 
- 새 EULA(최종 사용자 사용권 계약)를 추가했습니다.  

### <a name="fixed-issues"></a>해결된 문제

- 커넥터에서 약간의 메모리 누수를 유발하는 버그를 해결했습니다.
- 커넥터 시간 제한 문제에 대한 버그 해결을 포함하여 Azure Service Bus 버전을 업데이트했습니다.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>릴리스 상태

2018년 1월 19일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제

- 쿠키에서 도메인 변환이 필요한 사용자 지정 도메인에 대한 지원을 추가했습니다.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>릴리스 상태 

2017년 5월 25일: 다운로드용으로 릴리스됨 

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능 

커넥터의 아웃바운드 연결 제한에 대한 제어 기능이 향상되었습니다. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>릴리스 상태

2017년 4월 15일: 다운로드용으로 릴리스됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

- 필요한 포트 수를 줄이고 온보딩과 관리를 간소화했습니다. 이제 애플리케이션 프록시에서 2개의 표준 아웃바운드 포트(443 및 80)만 열어야 합니다. 애플리케이션 프록시는 계속해서 아웃바운드 연결만을 사용하므로 DMZ에 구성 요소가 필요하지 않습니다. 자세한 내용은  [구성 설명서](application-proxy-add-on-premises-application.md)를 참조하세요.  
- 외부 프록시 또는 방화벽에서 지원되는 경우 이제 IP 범위 대신 DNS로 네트워크를 열 수 있습니다. 애플리케이션 프록시 서비스에는 *.msappproxy.net 및 *.servicebus.windows.net에 대한 연결만 필요합니다.


## <a name="earlier-versions"></a>이전 버전

1\.5.36.0 이전 버전의 애플리케이션 프록시 커넥터를 사용하는 경우 완전히 지원되는 최신 기능을 이용할 수 있도록 최신 버전으로 업데이트하세요.

## <a name="next-steps"></a>다음 단계
- [Azure AD 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 원격으로 액세스](application-proxy.md)에 대해 자세히 알아보세요.
- 애플리케이션 프록시 사용을 시작하려면 [자습서: 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)를 참조하세요.
