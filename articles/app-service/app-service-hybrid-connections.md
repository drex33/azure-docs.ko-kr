---
title: 하이브리드 연결
description: Azure App Service에서 하이브리드 연결을 만들고 사용하여 서로 다른 네트워크의 리소스에 액세스하는 방법을 알아봅니다.
author: madsd
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 05/05/2021
ms.author: madsd
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 95d773b5ce4f9cdd84379bc9f4910f0eb8f0a5cd
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133439744"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 하이브리드 연결

하이브리드 연결은 Azure의 서비스인 동시에 Azure App Service의 기능입니다. 서비스로서, App Service에서 사용되는 것 이상의 기능과 용도가 있습니다. 하이브리드 연결 및 App Service 외부에서의 사용법을 자세히 알아보려면 [Azure Relay 하이브리드 연결][HCService]을 참조하세요.

App Service 내에서 하이브리드 연결을 사용하여 포트 443을 통해 Azure로 아웃바운드 호출을 수행할 수 있는 네트워크의 애플리케이션 리소스에 액세스할 수 있습니다. 하이브리드 연결은 앱에서 TCP 엔드포인트로의 액세스를 제공하며 앱에 액세스하는 새로운 방법을 사용하도록 설정하지 않습니다. App Service에서 사용되는 것처럼 각 하이브리드 연결은 단일 TCP 호스트 및 포트 조합에 상호 연결됩니다. 이렇게 하면 앱이 TCP 엔드포인트인 경우 모든 OS의 리소스에 액세스할 수 있습니다. 하이브리드 연결 기능은 애플리케이션 프로토콜이 무엇인지 또는 액세스하는 것을 알거나 신경 쓰지 않습니다. 네트워크 액세스만 제공합니다.  

## <a name="how-it-works"></a>작동 방법 ##
하이브리드 연결에는 원하는 엔드포인트와 Azure에 연결할 수 있는 릴레이 에이전트가 배포되어야 합니다. 릴레이 에이전트인 하이브리드 연결 관리자(HCM)는 포트 443을 통해 Azure Relay를 호출합니다. 웹앱 사이트에서 App Service 인프라는 애플리케이션을 대신하여 Azure Relay에도 연결합니다. 조인된 연결을 통해 앱은 원하는 엔드포인트에 액세스할 수 있습니다. 연결은 인증 및 권한 부여를 위한 보안 및 SAS(공유 액세스 서명) 키에 TLS 1.2를 사용합니다.

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-connectiondiagram.png" alt-text="하이브리드 연결 대략적인 흐름의 다이어그램":::

앱이 구성된 하이브리드 연결 엔드포인트와 일치하는 DNS 요청을 실행하면 아웃바운드 TCP 트래픽이 하이브리드 연결을 통해 리디렉션됩니다.  

> [!NOTE]
> 이는 항상 하이브리드 연결에 DNS 이름을 사용해야 함을 의미합니다. 일부 클라이언트 소프트웨어는 엔드포인트에서 IP 주소를 사용하는 경우 DNS 조회를 수행하지 않습니다. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service 하이브리드 연결의 장점 ###

하이브리드 연결 기능에는 다음과 같은 많은 장점이 있습니다.

- 앱이 온-프레미스 시스템 및 서비스에 안전하게 액세스할 수 있습니다.
- 이 기능에는 인터넷에 액세스할 수 있는 엔드포인트가 필요하지 않습니다.
- 빠르고 쉽게 설정할 수 있습니다. 게이트웨이가 필요하지 않습니다.
- 각 하이브리드 연결이 단일 호스트:포트 조합과 일치하므로 보안에 도움이 됩니다.
- 일반적으로 방화벽 구멍이 필요하지 않습니다. 연결은 모두 표준 웹 포트를 통한 아웃바운드입니다.
- 이 기능은 네트워크 수준이므로 앱에서 사용하는 언어와 엔드포인트에서 사용하는 기술에는 독립적입니다.
- 단일 앱에서 여러 네트워크에 액세스하는 데 사용될 수 있습니다. 
- Windows 앱 및 Linux 앱에 대한 GA에서 지원합니다. Windows 사용자 지정 컨테이너에 대해 지원되지 않습니다.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>하이브리드 연결로 할 수 없는 작업 ###

하이브리드 연결로 수행할 수 없는 작업에는 다음이 포함됩니다.

- 드라이브를 탑재합니다.
- UDP를 사용합니다.
- FTP 수동 모드 또는 확장 수동 모드 같은 동적 포트를 사용하는 TCP 기반 서비스에 액세스합니다.
- UDP가 필요할 수 있으므로 LDAP를 지원합니다.
- App Service 작업자를 도메인에 가입할 수 없으므로 Active Directory를 지원합니다. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>앱에 하이브리드 연결 추가 및 만들기 ##

하이브리드 연결을 만들려면 [Azure Portal][portal]로 이동하고 해당 앱을 선택합니다. **네트워킹** > **하이브리드 연결 엔드포인트 구성** 을 선택합니다. 여기에서 앱에 구성된 하이브리드 연결을 볼 수 있습니다.  

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-portal.png" alt-text="하이브리드 연결 목록 스크린샷":::

새 하이브리드 연결을 추가하려면 **[+]하이브리드 연결 추가** 를 선택합니다.  이미 만든 하이브리드 연결 목록이 표시됩니다. 앱에 하나 이상을 추가하려면 원하는 항목을 선택한 다음 **선택한 하이브리드 연결 추가** 를 선택합니다.

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-addhc.png" alt-text="하이브리드 연결 포털 스크린샷":::

새 하이브리드 연결을 만들려면 **새 하이브리드 연결 만들기** 를 선택합니다. 다음을 지정합니다. 

- 하이브리드 연결 이름
- 엔드포인트 호스트 이름
- 엔드포인트 포트
- 사용할 Service Bus 네임스페이스

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-createhc.png" alt-text="새 하이브리드 연결 만들기 대화 상자 스크린샷":::

모든 하이브리드 연결은 Service Bus 네임스페이스에 연결되고 각 Service Bus 네임스페이스는 Azure 지역에 있습니다. 네트워크로 인한 대기 시간을 피하려면 앱과 같은 지역에서 Service Bus 네임스페이스 사용을 시도하는 것이 중요합니다.

앱에서 하이브리드 연결을 제거하려면 해당 항목을 마우스 오른쪽 단추로 클릭하고 **연결 끊기** 를 선택합니다.  

하이브리드 연결이 앱에 추가되면 간단히 해당 항목을 선택하여 세부 정보를 볼 수 있습니다. 

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-properties.png" alt-text="하이브리드 연결 세부 정보 스크린샷":::

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Azure Relay 포털에서 하이브리드 연결 만들기 ###

앱 내의 포털 환경 외에도 Azure Relay 포털에서 하이브리드 연결을 만들 수 있습니다. 하이브리드 연결을 App Service에서 사용하려면:

* 클라이언트 권한 부여가 필요합니다.
* 호스트:포트 조합을 값으로 포함하는 endpoint라는 메타데이터 항목이 있어야 합니다.

## <a name="hybrid-connections-and-app-service-plans"></a>하이브리드 연결 및 App Service 계획 ##

App Service 하이브리드 연결은 기본, 표준, 프리미엄 및 격리 요금제 SKU에서만 사용할 수 있습니다. 요금제에는 연결된 제한이 있습니다.  

| 요금제 | 요금제에서 사용 가능한 하이브리드 연결 수 |
|----|----|
| Basic | 플랜당 5개 |
| Standard | 플랜당 25개 |
| Premium(v1-v3) | 앱당 220개 |
| 격리됨(v1-v2) | 앱당 220개 |

App Service 계획 UI는 어떤 앱에서 얼마나 많은 하이브리드 연결을 사용하고 있는지 보여줍니다.

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-aspproperties.png" alt-text="App Service 계획 속성 스크린샷":::

세부 정보를 확인할 하이브리드 연결을 선택합니다. 앱 보기에서 확인한 모든 정보를 볼 수 있습니다. 동일한 계획에서 해당 하이브리드 연결을 사용하고 있는 다른 앱의 수도 확인할 수 있습니다.

App Service 계획에 사용할 수 있는 하이브리드 연결 엔드포인트 수에는 제한이 있습니다. 하지만 사용된 각 하이브리드 연결은 해당 계획의 여러 앱에서 사용할 수 있습니다. 예를 들어 App Service 계획에서 5개의 개별 앱에 사용되는 단일 하이브리드 연결은 하나의 하이브리드 연결로 간주됩니다.

### <a name="pricing"></a>가격 책정 ###

App Service 계획 SKU 요구 사항 외에도 하이브리드 연결을 사용하는 데 추가 비용이 듭니다. 하이브리드 연결에서 사용하는 각 수신기에 대한 요금이 부과됩니다. 수신기는 하이브리드 연결 관리자입니다. 두 개의 하이브리드 연결 관리자가 5개의 하이브리드 연결을 지원하는 경우 수신기는 10개가 됩니다. 자세한 내용은 [Service Bus 가격][sbpricing]을 참조하세요.

## <a name="hybrid-connection-manager"></a>하이브리드 연결 관리자 ##

하이브리드 연결 기능에는 하이브리드 연결 엔드포인트를 호스트하는 네트워크의 릴레이 에이전트가 필요합니다. 이 릴레이 에이전트를 HCM(하이브리드 연결 관리자)이라고 합니다. HCM을 다운로드하려면 [Azure Portal][portal]의 앱에서 **네트워킹** > **하이브리드 연결 엔드포인트 구성** 을 선택합니다.  

이 도구는 Windows Server 2012 이상에서 실행됩니다. HCM은 서비스로 실행되며, 포트 443에서 Azure Relay에 아웃바운드로 연결됩니다.  

HCM을 설치한 후 HybridConnectionManagerUi.exe를 실행하여 도구의 UI를 사용할 수 있습니다. 이 파일은 하이브리드 연결 관리자 설치 디렉터리에 있습니다. Windows 10에서는 검색 상자에서 *하이브리드 연결 관리자 UI* 를 검색할 수도 있습니다.

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-hcm.png" alt-text="하이브리드 연결 관리자 스크린샷":::

HCM UI를 시작하면 가장 먼저 이 HCM 인스턴스로 구성된 하이브리드 연결을 모두 나열한 테이블이 표시됩니다. 변경하려는 경우 먼저 Azure로 인증합니다. 

HCM에 하나 이상의 하이브리드 연결을 추가하려면 다음을 수행합니다.

1. HCM UI를 시작합니다.
2. **새 하이브리드 연결 추가** 를 선택합니다.
:::image type="content" source="media/app-service-hybrid-connections/hybridconn-hcmadd.png" alt-text="새 하이브리드 연결 구성 스크린샷":::

1. Azure 계정으로 로그인하여 구독에 제공되는 하이브리드 연결을 가져옵니다. HCM은 계속 해 서 Azure 계정을 사용 하지 않습니다. 
1. 구독을 선택합니다.
1. HCM이 릴레이할 하이브리드 연결을 선택합니다.
:::image type="content" source="media/app-service-hybrid-connections/hybridconn-hcmadded.png" alt-text="하이브리드 연결 스크린샷":::

1. **저장** 을 선택합니다.

이제 추가한 하이브리드 연결을 볼 수 있습니다. 구성된 하이브리드 연결을 선택하여 세부 정보를 볼 수도 있습니다.

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-hcmdetails.png" alt-text="하이브리드 연결 세부 정보 스크린샷":::

를 사용 하 여 구성 된 하이브리드 연결 지원 하려면 HCM에 다음이 필요 합니다.

- 포트 443을 통한 Azure에 대한 TCP 액세스
- 하이브리드 연결 엔드포인트에 대한 TCP 액세스
- 엔드포인트 호스트 및 Service Bus 네임스페이스에서 DNS 조회를 수행하는 기능

> [!NOTE]
> Azure Relay는 연결을 위해 웹 소켓을 사용합니다. 이 기능은 Windows Server 2012 이상에서만 사용할 수 있습니다. 따라서 HCM은 Windows Server 2012 이전 버전에서 지원되지 않습니다.
>

### <a name="redundancy"></a>중복 ###

각 HCM은 여러 하이브리드 연결을 지원할 수 있습니다. 특정 하이브리드 연결이 여러 HCM에서 지원될 수도 있습니다. 기본 동작은 특정 엔드포인트에 대해 구성된 HCM을 통해 트래픽을 라우팅하는 것입니다. 네트워크에서 하이브리드 연결에 대한 고가용성이 필요한 경우에는 개별 컴퓨터에서 여러 HCM을 실행합니다. Relay 서비스가 HCM에 트래픽을 분산하는 데 사용하는 부하 분산 알고리즘은 임의 할당입니다. 

### <a name="manually-add-a-hybrid-connection"></a>수동으로 하이브리드 연결 추가 ###

구독 외부에서 사용자가 특정 하이브리드 연결에 대한 HCM 인스턴스를 호스트할 수 있도록 하려면 해당 하이브리드 연결에 대한 게이트웨이 연결 문자열을 공유합니다. [Azure Portal][portal]의 하이브리드 연결 속성에서 게이트웨이 연결 문자열을 볼 수 있습니다. 이 문자열을 사용하려면 HCM에서 **수동으로 입력** 을 선택하고 게이트웨이 연결 문자열에 붙여넣습니다.

:::image type="content" source="media/app-service-hybrid-connections/hybridconn-manual.png" alt-text="수동으로 하이브리드 연결 추가":::

### <a name="upgrade"></a>업그레이드 ###

문제를 해결하거나 개선 기능을 제공하기 위해 하이브리드 연결 관리자가 정기적으로 업데이트됩니다. 업그레이드가 릴리스되면 HCM UI에 팝업이 표시됩니다. 업그레이드를 적용하면 변경 내용이 적용되고 HCM이 다시 시작됩니다. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>프로그래밍 방식으로 앱에 하이브리드 연결 추가 ##

하이브리드 연결에 대 한 Azure CLI 지원이 있습니다. 제공된 명령은 앱과 App Service 요금제 수준 모두에서 작동합니다.  앱 수준 명령은 다음과 같습니다.

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

App Service 요금제 명령을 사용하면 지정된 하이브리드 연결에서 사용할 키를 설정할 수 있습니다. 각 하이브리드 연결에는 기본 및 보조라는 두 개의 키가 설정되어 있습니다. 아래 명령에서 기본 키 또는 보조 키를 사용하도록 선택할 수 있습니다. 이를 통해 정기적으로 키를 다시 생성하려는 경우 키를 교체할 수 있습니다. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>하이브리드 연결 보안 ##

기본 Azure Service Bus Relay에 대한 충분한 권한이 있는 사용자가 기존 하이브리드 연결을 다른 App Service Web Apps에 추가할 수 있습니다. 즉, 다른 사용자가 동일한 하이브리드 연결을 다시 사용 하지 못하도록 해야 하는 경우 (예: 대상 리소스가 무단 액세스를 차단 하는 추가 보안 조치를 미치지 않는 서비스인 경우) Azure Service Bus Relay에 대 한 액세스를 잠가야 합니다.

릴레이에 대 한 액세스 권한이 있는 사용자는 `Reader` Azure Portal의 웹 앱에 하이브리드 연결을 추가 하려고 할 때 하이브리드 연결을 _볼_ 수 있지만 릴레이 연결을 설정 하는 데 사용 되는 연결 문자열을 검색 하는 권한이 없는 사용자는이 연결을 _추가할_ 수 없습니다. 하이브리드 연결을 추가하려면 `listKeys` 권한(`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`)이 있어야 합니다. `Contributor`릴레이에 대해이 사용 권한을 포함 하는 역할 또는 다른 모든 역할은 사용자가 하이브리드 연결을 사용 하 여 자체 Web Apps에 추가할 수 있습니다.

## <a name="manage-your-hybrid-connections"></a>하이브리드 연결 관리 ##

하이브리드 연결에 대 한 끝점 호스트 또는 포트를 변경 해야 하는 경우 다음 단계를 수행 합니다.

1. 연결을 선택 하 고 하이브리드 연결 세부 정보 창의 왼쪽 위에서 **제거** 를 선택 하 여 로컬 컴퓨터의 하이브리드 연결 관리자에서 하이브리드 연결을 제거 합니다.
1. App Service **네트워킹** 페이지에서 **하이브리드 연결** 으로 이동 하 여 App Service 하이브리드 연결의 연결을 끊습니다.
1. 업데이트 해야 하는 끝점에 대 한 릴레이로 이동 하 여 왼쪽 탐색 메뉴의 **엔터티** 아래에서 **하이브리드 연결** 을 선택 합니다.
1. 업데이트 하려는 하이브리드 연결을 선택 하 고 왼쪽 탐색 메뉴의 **설정** 에서 **속성** 을 선택 합니다.
1. 변경을 수행 하 고 위쪽에서 **변경 내용 저장** 을 누릅니다.
1. App Service에 대 한 **하이브리드 연결** 설정으로 돌아가서 하이브리드 연결을 다시 추가 합니다. 끝점이 의도 한 대로 업데이트 되는지 확인 합니다. 하이브리드 연결이 목록에 표시 되지 않으면 5-10 분 후에 새로 고칩니다.
1. 로컬 컴퓨터의 하이브리드 연결 관리자로 돌아가서 연결을 다시 추가 합니다.

## <a name="troubleshooting"></a>문제 해결 ##

"연결됨" 상태는 하나 이상의 HCM이 해당 하이브리드 연결로 구성되어 Azure에 연결할 수 있음을 의미합니다. 하이브리드 연결의 상태가 **연결 됨** 으로 표시 되지 않으면 Azure에 대 한 액세스 권한이 있는 hcm에 하이브리드 연결이 구성 되지 않은 것입니다. HCM이 **연결되지 않음** 으로 표시될 때 몇 가지 확인할 사항이 있습니다.

* 호스트가 443 포트에서 Azure에 대한 아웃바운드 액세스를 보유합니까? PowerShell 명령 *Test-NetConnection Destination -P Port* 를 사용하여 HCM 호스트에서 테스트할 수 있습니다 
* HCM이 잘못된 상태에 있을 수 있습니까? ‘Azure 하이브리드 연결 관리자 서비스" 로컬 서비스를 다시 시작해 보세요.

* 충돌하는 소프트웨어가 설치되어 있나요? 하이브리드 연결 관리자는 Biztalk 하이브리드 연결 관리자 또는 Windows Server용 Service Bus와 함께 사용할 수 없습니다. 따라서 HCM을 설치할 때 이러한 패키지의 모든 버전을 먼저 제거해야 합니다.

상태가 **연결됨** 이지만 앱에서 엔드포인트에 도달할 수 없는 경우 다음을 수행합니다.

* 하이브리드 연결에서 DNS 이름을 사용 하 고 있는지 확인 합니다. IP 주소를 사용하는 경우 필요한 클라이언트 DNS 조회가 발생하지 않을 수 있습니다. 웹 앱에서 실행 되는 클라이언트가 DNS 조회를 수행 하지 않으면 하이브리드 연결이 작동 하지 않습니다.
* 하이브리드 연결에 사용된 DNS 이름이 HCM 호스트에서 확인될 수 있는지 확인합니다. *nslookup EndpointDNSname* 을 사용하여 확인을 점검합니다. 여기서 EndpointDNSname은 하이브리드 연결 정의에서 사용되는 것에 정확히 일치합니다.
* PowerShell 명령 *Test-NetConnection EndpointDNSname -P Port* 를 사용하여 HCM 호스트에서 엔드포인트로의 액세스를 테스트합니다. HCM 호스트에서 엔드포인트에 도달할 수 없는 경우 대상 호스트의 호스트 기반 방화벽을 포함하여 두 호스트 사이의 방화벽을 점검합니다.
* Linux에서 App Service 사용 하는 경우 "localhost"를 끝점 호스트로 사용 하 고 있지 않은지 확인 합니다. 대신 로컬 컴퓨터의 리소스에 대 한 연결을 만들려고 하는 경우 컴퓨터 이름을 사용 합니다.

App Service에서 **tcpping** 명령줄 도구는 고급 도구(Kudu) 콘솔에서 호출할 수 있습니다. 이 도구를 통해 TCP 끝점에 액세스할 수 있는지 알 수 있지만 하이브리드 연결 끝점에 액세스할 수 있는지는 알 수 없습니다. 콘솔에서 하이브리드 연결 끝점에 대 한 도구를 사용 하는 경우 호스트: 포트 조합을 사용 하는 것만 확인 합니다.  

엔드포인트에 대한 명령줄 클라이언트가 있는 경우 앱 콘솔에서 연결을 테스트할 수 있습니다. 예를 들어, curl을 사용하여 웹 서버 엔드포인트에 대한 액세스를 테스트할 수 있습니다.

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
