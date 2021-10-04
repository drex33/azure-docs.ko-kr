---
title: ServiceNow를 IT 서비스 관리 커넥터에 연결
description: ServiceNow를 Azure Monitor의 ITSMC(IT 서비스 관리 커넥터)에 연결하여 ITSM 작업 항목을 중앙에서 모니터링하고 관리하는 방법을 알아봅니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: e3d696580acd995a2396d56c404e2f904611ccac
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430139"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>ServiceNow를 IT 서비스 관리 커넥터에 연결

이 문서에서는 ITSM(IT 서비스 관리) 작업 항목을 중앙에서 관리할 수 있도록 Log Analytics에서 ServiceNow 인스턴스와 ITSMC(IT 서비스 관리 커넥터) 간에 연결을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소
다음 연결 필수 구성 요소를 충족하는지 확인합니다.

### <a name="itsmc-installation"></a>ITSMC 설치

ITSMC를 설치하는 방법에 대한 자세한 내용은 [IT 서비스 관리 커넥터 솔루션 추가](./itsmc-definition.md#add-it-service-management-connector)를 참조하세요.

> [!NOTE]
> ITSMC는 ServiceNow의 공식 SaaS(software as a service) 제품만 지원합니다. ServiceNow의 비공개 배포는 지원되지 않습니다.

### <a name="oauth-setup"></a>OAuth 설정

ServiceNow가 지원되는 버전에는 Quebec, Paris, Orlando, New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsinki 및 Geneva가 포함됩니다.

ServiceNow 관리자는 ServiceNow 인스턴스의 클라이언트 ID와 클라이언트 암호를 생성해야 합니다. 필요에 따라 다음 정보가 필요합니다.

- [Quebec에 대해 OAuth 설정](https://docs.servicenow.com/bundle/quebec-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Paris에 대해 OAuth 설정](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Orlando에 대해 OAuth 설정](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [London에 대해 OAuth 설정](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)

OAuth 설정의 일부로 다음을 수행하는 것이 좋습니다.

1. [클라이언트 엔드포인트를 만들어 인스턴스에 액세스합니다](https://docs.servicenow.com/bundle/rome-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. 새로 고침 토큰의 수명을 업데이트합니다.

   1. **ServiceNow** 창에서 **시스템 OAuth** 를 검색하고 **애플리케이션 레지스트리** 를 선택합니다. 
   1. 정의된 OAuth의 이름을 선택하고 **새로 고침 토큰 수명** 을 **7,776,000초**(90일)로 변경합니다. 
   1. **업데이트** 를 선택합니다. 

1. 연결이 활성 상태로 유지되도록 내부 프로시저를 설정합니다. 새로 고침 토큰 수명이 정상적으로 만료되기까지 몇 일이 남았으면 다음 작업을 수행합니다.

   1. [ITSM 커넥터 구성에 대한 수동 동기화 프로세스를 완료합니다](./itsmc-resync-servicenow.md).

   1. 이전 새로 고침 토큰을 철회합니다. 보안상의 이유로 이전 키를 보관하지 않는 것이 좋습니다. 
   
      1. **ServiceNow** 창에서 **시스템 OAuth** 를 검색하고 **토큰 관리** 를 선택합니다. 
      
      1. OAuth 이름과 만료 날짜에 따라 목록에서 이전 토큰을 선택합니다.

         ![OAuth에 대한 토큰 목록을 보여주는 스크린샷](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. **액세스 철회** > **철회** 를 선택합니다.

## <a name="install-the-user-app-and-create-the-user-role"></a>사용자 앱을 설치하고 사용자 역할을 만듭니다.

다음 절차를 수행하여 Service Now 사용자 앱을 설치하고 통합 사용자 역할을 만듭니다. 이러한 자격 증명을 사용하여 Azure에서 ServiceNow 연결을 만듭니다.

> [!NOTE]
> ITSMC는 Microsoft Log Analytics 통합에 사용되는 ServiceNow 저장소에서 다운로드한 공식 사용자 앱만 지원합니다. ITSMC는 ServiceNow 측이나 공식 ServiceNow 솔루션에 포함되지 않은 애플리케이션에서 코드를 수집할 수 없습니다. 

1. [ServiceNow 저장소](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1)로 이동하고 **ServiceNow 및 Microsoft OMS 통합용 사용자 앱** 을 ServiceNow 인스턴스에 설치합니다.
   
   >[!NOTE]
   >Microsoft OMS(Operations Management Suite)에서 Azure Monitor로 진행 중인 전환의 일부로, OMS를 이제 Log Analytics라고 합니다.     
2. 설치 후 ServiceNow 인스턴스의 왼쪽 탐색 모음으로 이동한 다음, **Microsoft OMS 통합기** 를 검색하고 선택합니다.  
3. **설치 검사 목록** 을 선택합니다.

   사용자 역할이 아직 생성되지 않았으므로 상태가 **완료되지 않음** 으로 표시됩니다.

4. **통합 사용자 만들기** 옆에 있는 텍스트 상자에 Azure의 ITSMC에 연결할 수 있는 사용자의 이름을 입력합니다.
5. 이 사용자의 암호를 입력한 다음, **확인** 을 선택합니다.  

새로 만든 사용자는 기본 역할이 할당된 상태로 표시됩니다.

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

사용자가 성공적으로 생성되면 **설치 검사 목록 확인** 상태가 **완료됨** 으로 바뀌고 생성된 사용자 역할의 세부 정보가 해당 앱에 나열됩니다.

> [!NOTE]
> ITSMC는 ServiceNow 인스턴스에 설치된 다른 모듈을 사용하지 않고도 ServiceNow에 인시던트를 보낼 수 있습니다. ServiceNow 인스턴스에서 EventManagement 모듈을 사용하고 커넥터를 사용하여 ServiceNow에서 이벤트나 경고를 만들려는 경우 통합 사용자에게 다음 역할을 추가합니다.
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>연결 만들기
다음 절차를 수행하여 ServiceNow 연결을 만듭니다.

> [!NOTE]
> Azure Monitor에서 전송되는 경고는 ServiceNow에서 이벤트, 인시던트 또는 경고 요소 중 하나를 만들 수 있습니다. 

1. Azure Portal에서 **모든 리소스** 로 이동하고 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2. **작업 영역 데이터 원본** 에서 **ITSM 연결** 을 선택합니다.

   ![데이터 원본 선택을 보여주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)

3. 오른쪽 창 맨 위에 있는 **추가** 를 클릭합니다.

4. 다음 표의 설명대로 정보를 제공한 다음, **확인** 을 선택합니다.

   | **필드** | **설명** |
   | --- | --- |
   | **연결 이름**   | ITSMC에 연결하려는 ServiceNow 인스턴스의 이름을 입력합니다. 나중에 ITSM 작업 항목을 구성하고 상세 분석을 볼 때 Log Analytics에서 이 이름을 사용합니다. |
   | **파트너 유형**   | **ServiceNow** 를 선택합니다. |
   | **서버 URL**   | ITSMC에 연결하려는 ServiceNow 인스턴스의 URL을 입력합니다. URL은 *.servicenow.com* 접미사를 사용하여 지원되는 SaaS 버전을 가리켜야 합니다(예: `https://XXXXX.service-now.com/`).|
   | **사용자 이름**   | ITSMC에 대한 연결을 지원하도록 ServiceNow 앱에서 만든 통합 사용자 이름을 입력합니다.|
   | **암호**   | 이 사용자 이름과 연결된 암호를 입력합니다. **참고**: 사용자 이름과 암호는 인증 토큰을 생성하는 데만 사용됩니다. ITSMC 서비스 내 어디에도 저장되지 않습니다.  |
   | **클라이언트 ID**   | 이전에 생성한 OAuth2 인증에 사용하려는 클라이언트 ID를 입력합니다. 클라이언트 ID와 비밀을 생성하는 방법에 대한 자세한 내용은 [OAuth 설정](https://old.wiki/index.php/OAuth_Setup)을 참조하세요. |
   | **클라이언트 암호**   | 이 ID에 생성된 클라이언트 암호를 입력합니다.   |
   | **데이터 동기화 범위(일)** | 데이터를 원하는 이전 일 수를 입력합니다. 제한은 120일입니다. |
   | **동기화할 작업 항목**   | ITSMC를 통해 Azure Log Analytics와 동기화할 ServiceNow 작업 항목을 선택합니다. 선택한 값을 Log Analytics로 가져옵니다. 옵션은 인시던트와 변경 요청입니다.|
   | **ITSM 제품에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 ITSMC는 지원되는 ITSM 시스템에 구성 항목을 만듭니다(없는 경우). 기본적으로 사용하지 않도록 설정되어 있습니다. |

![ServiceNow 연결을 추가할 수 있는 상자와 옵션의 스크린샷](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

성공적으로 연결되고 동기화된 경우:

- ServiceNow 인스턴스에서 선택한 작업 항목을 Log Analytics로 가져옵니다. **IT 서비스 관리 커넥터** 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고나 로그 레코드에서 또는 이 ServiceNow 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

> [!NOTE]
> ServiceNow에는 시간당 요청에 대한 요율 제한이 있습니다. 제한을 구성하려면 ServiceNow 인스턴스에서 **인바운드 REST API 전송률 제한** 을 정의합니다.

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터 개요](itsmc-overview.md)
* [Azure 경고에서 ITSM 작업 항목 만들기](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)
