---
title: ITSMC에서 문제 해결
description: IT 서비스 관리 커넥터에서 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: d6e41cd80e27fe673d04592c61e590c09645619e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430363"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>IT 서비스 관리 커넥터의 문제 해결

이 문서에서는 ITSMC(IT 서비스 관리 커넥터)의 일반적인 문제 및 문제 해결 방법에 대해 설명합니다.

Azure Monitor는 모니터링 데이터에서 중요한 조건이 발견될 때 사전에 경고를 알려줍니다. 이러한 경고는 문제를 식별하고 해결한 후에 시스템 사용자에게 알리는 데 도움이 됩니다.

경고 수신 방법을 선택할 수 있습니다. 메일, SMS 또는 웹후크를 선택하거나 솔루션을 자동화할 수도 있습니다. 

또 다른 방법으로 ITSMC를 통해 알려줄 수 있습니다. ITSMC는 ServiceNow와 같은 외부 티켓 시스템에 경고를 보내는 옵션을 제공합니다.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>대시보드를 사용하여 인시던트 및 변경 요청 데이터 분석

연결을 설정할 때의 구성에 따라 ITSMC는 최대 120일의 인시던트 및 변경 요청 데이터를 동기화할 수 있습니다. 이 데이터에 대한 로그 레코드 스키마를 가져오려면 [ITSM 제품에서 동기화된 데이터](./itsmc-synced-data.md) 문서를 참조하세요.

ITSMC 대시보드를 사용하여 인시던트 및 변경 요청 데이터를 시각화할 수 있습니다.

![ITSMC 대시보드를 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

또한 대시보드는 커넥터 상태에 대한 정보를 제공합니다. 이 정보를 시작 지점으로 사용하여 연결 관련 문제를 분석할 수 있습니다. 자세한 내용은 [대시보드를 사용한 오류 조사](./itsmc-dashboard.md)를 참조하세요.

## <a name="use-service-map-to-visualize-incidents"></a>서비스 맵을 사용하여 인시던트 시각화

서비스 맵에서 영향을 받는 컴퓨터에 대해 동기화된 인시던트를 시각화할 수도 있습니다.

서비스 맵은 Windows 및 Linux 시스템에서 애플리케이션 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 이를 통해 서버를 생각한 것처럼(중요한 서비스를 제공하는 상호 연결된 시스템으로) 볼 수 있습니다. 

서비스 맵에서는 모든 TCP 연결 아키텍처에서 서버, 프로세스 및 포트 간의 연결을 보여 줍니다. 에이전트를 설치하는 것 외에는 구성이 필요하지 않습니다. 자세한 내용은 [서비스 맵 사용](../vm/service-map.md)을 참조하세요.

서비스 맵을 사용하는 경우 다음 예와 같이 ITSM(IT 서비스 관리) 솔루션에서 만든 서비스 데스크 항목을 볼 수 있습니다.

![Log Analytics 화면을 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>문제 해결

다음 섹션에서는 일반적인 증상, 가능한 원인 및 해결 방법을 식별합니다. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>ITSM 시스템에 대한 연결이 실패하고 "연결 저장 오류" 메시지가 표시됨

**원인**: 원인은 다음 옵션 중 하나일 수 있습니다.

* 자격 증명이 잘못되었습니다.
* 권한이 부족합니다.
* Service Manager 연결의 경우: 웹앱이 잘못 배포되었습니다.

**해결 방법**:

* ServiceNow, Cherwell 및 Provence 연결의 경우:
  * 각 연결에 대한 사용자 이름, 암호 클라이언트 ID 및 클라이언트 비밀을 올바르게 입력했는지 확인합니다.  
  * ServiceNow의 경우 해당 ITSM 제품에 대해 [충분한 권한](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)이 있는지 확인합니다.

* Service Manager 연결의 경우:  
  * 웹앱이 성공적으로 배포되고 하이브리드 연결이 만들어졌는지 확인합니다. 온-프레미스 Service Manager 컴퓨터와의 연결이 성공적으로 설정되었는지 확인하려면 [하이브리드 연결](./itsmc-connections-scsm.md#configure-the-hybrid-connection) 설정 설명서에 설명된 웹앱 URL로 이동합니다.  

### <a name="duplicate-work-items-are-created"></a>중복 작업 항목이 만들어짐

**원인**: 원인은 다음 두 가지 옵션 중 하나일 수 있습니다.

* 경고에 대해 두 개 이상의 ITSM 작업이 정의됩니다.
* 경고가 해결되었습니다.

**해결 방법**: 두 가지 솔루션이 있습니다.

* 경고당 하나의 ITSM 작업 그룹이 있는지 확인합니다.
* ITSMC는 경고가 해결될 때 일치하는 작업 항목의 상태 업데이트를 지원하지 않습니다. 해결된 새 작업 항목을 만듭니다.

### <a name="work-items-are-not-created"></a>작업 항목이 만들어지지 않음

**원인**: 이 증상에는 여러 가지 이유가 있을 수 있습니다.

* ServiceNow 쪽에서 코드가 수정되었습니다.
* 사용 권한이 잘못 구성되었습니다.
* ServiceNow 속도 제한이 너무 높거나 낮습니다.
* 새로 고침 토큰이 만료되었습니다.
* ITSMC가 삭제되었습니다.

**해결 방법**: [대시보드](itsmc-dashboard.md)를 확인하고 커넥터 상태 섹션에서 오류를 검토합니다. 그런 다음 [일반적인 오류 및 해결 방법](itsmc-dashboard-errors.md)을 검토합니다.

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>작업 그룹에 대한 ITSM 작업을 만들 수 없음

**원인**: 새로 만든 ITSMC 인스턴스가 아직 초기 동기화를 완료하지 않았습니다.

**해결 방법**: [일반적인 오류 및 해결 방법](itsmc-dashboard-errors.md)을 검토합니다.

### <a name="sync-connection"></a>동기화 연결 

**원인**: 이 증상에는 여러 가지 이유가 있을 수 있습니다.

* 템플릿은 작업 정의 드롭다운의 일부로 표시되지 않고 "템플릿 구성을 검색할 수 없습니다. 자세한 내용은 커넥터 로그를 참조하세요."라는 오류 메시지가 표시됩니다.
* 작업 정의의 일부로 기본 필드의 드롭다운에 값이 표시되지 않고 "다음 필드에 대한 값을 찾을 수 없음: \<field names\>" 오류 메시지가 표시됩니다.
* 인시던트/이벤트는 ServiceNow에서 만들어지지 않습니다.

**해결 방법**: 
* [커넥터를 동기화합니다](itsmc-resync-servicenow.md).
* [대시보드](itsmc-dashboard.md)를 확인하고 커넥터 상태 섹션에서 오류를 검토합니다. 그런 다음 [일반적인 오류 및 해결 방법](itsmc-dashboard-errors.md)을 검토합니다.
