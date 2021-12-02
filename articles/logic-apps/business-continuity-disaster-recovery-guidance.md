---
title: 비즈니스 연속성 및 재해 복구
description: 데이터를 보호하고, 중단 이벤트로부터 신속하게 복구하고, 중요한 비즈니스 기능에 필요한 리소스를 복원하고, Azure Logic Apps의 비즈니스 연속성을 유지 관리하는 전략 디자인
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 84cea1c58c448fcd7a54bbb5d28a1ccbdf366456
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133437375"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Azure Logic Apps의 비즈니스 연속성 및 재해 복구

예기치 않은 이벤트가 비즈니스 및 고객에게 미칠 수 있는 영향을 줄이려면 데이터를 보호하고, 중요한 비즈니스 기능을 지원하는 리소스를 신속하게 복원하고, 작업을 계속 실행하여 BC(‘[비즈니스 연속성](https://en.wikipedia.org/wiki/Business_continuity_planning)’)를 유지할 수 있도록 DR(‘[재해 복구](https://en.wikipedia.org/wiki/Disaster_recovery)’) 솔루션이 준비되어 있어야 합니다.  예를 들어 중단에는 중단, 기본 인프라 또는 구성 요소(예: 스토리지, 네트워크, 컴퓨팅 리소스)의 손실, 복구할 수 없는 애플리케이션 오류, 전체 데이터 센터 손실 등이 있습니다. BCDR(비즈니스 연속성 및 재해 복구) 솔루션을 준비하면 기업이나 조직은 계획되거나 계획되지 않은 중단에 보다 신속하게 응답하고 고객을 위해 가동 중지 시간을 줄일 수 있습니다.

이 문서에서는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 자동화된 워크플로를 빌드할 때 적용할 수 있는 BCDR 지침 및 전략을 제공합니다. 논리 앱 워크플로를 사용하면 작성해야 하는 코드 범위를 줄여 앱, 클라우드 서비스, 온-프레미스 시스템 간에 데이터를 보다 쉽게 통합하고 오케스트레이션할 수 있습니다. BCDR을 계획하는 경우 논리 앱뿐만 아니라 논리 앱에서 사용하는 다음과 같은 Azure 리소스도 고려해야 합니다.

* [연결](../connectors/apis-list.md) - 논리 앱에서 다른 앱, 서비스, 시스템으로의 연결을 만듭니다. 자세한 내용은 이 항목의 뒷부분에 나오는 [리소스에 대한 연결](#resource-connections)을 참조하세요.

* [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md) - 온-프레미스 시스템의 데이터에 액세스하기 위해 논리 앱에서 만들고 사용하는 Azure 리소스입니다. 각 게이트웨이 리소스는 로컬 컴퓨터에 있는 별도의 [데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md)를 나타냅니다. 자세한 내용은 이 항목의 뒷부분에 나오는 [온-프레미스 데이터 게이트웨이](#on-premises-data-gateways)를 참조하세요.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) - 여기에서 논리 앱이 [B2B 엔터프라이즈 통합](../logic-apps/logic-apps-enterprise-integration-overview.md) 시나리오에 사용하는 아티팩트를 정의하고 저장합니다. 예를 들어 [통합 계정에 대해 지역 간 재해 복구를 설정](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)할 수 있습니다.

* [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) - 여기에서 Azure 가상 네트워크 내의 격리된 Logic Apps 런타임 인스턴스에서 실행되는 논리 앱을 만듭니다. 이러한 논리 앱은 해당 가상 네트워크의 방화벽 뒤에서 보호되는 리소스에 액세스할 수 있습니다.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>주 위치 및 보조 위치

각 논리 앱은 배포에 사용할 위치를 지정해야 합니다. 이 위치는 글로벌 다중 테넌트 Azure의 퍼블릭 지역(예: “미국 서부”)이거나 이전에 만들고 Azure 가상 네트워크에 배포한 ISE(통합 서비스 환경)입니다. ISE에서 논리 앱을 실행하는 것은 글로벌 Azure 지역에서 논리 앱을 실행하는 것과 유사합니다. 즉, 재해 복구 전략이 두 시나리오에 모두 적용될 수 있습니다. 그러나 ISE에서는 ISE만 사용할 수 있는 리소스에 대한 액세스를 구성하는 등 다른 사항도 고려해야 합니다.

> [!NOTE]
> 논리 앱이 거래 업체, 규약, 스키마, 맵, 인증서 등 통합 계정에 저장되는 B2B 아티팩트에서도 작동하는 경우 통합 계정과 논리 앱 모두 동일한 위치를 지정해야 합니다.

이 재해 복구 전략은 Azure Logic Apps도 사용할 수 있는 대체 위치에서 대기 또는 백업 논리 앱으로 ‘[장애 조치(failover)](https://en.wikipedia.org/wiki/Failover)’하도록 주 논리 앱을 설정하는 데 중점을 둔 것입니다. 이렇게 하면 주 논리 앱이 손실, 중단 또는 실패하는 경우 보조 논리 앱에서 작업을 수행할 수 있습니다. 이 전략을 사용하려면 보조 논리 앱 및 종속 리소스가 대체 위치에 이미 배포되고 준비되어 있어야 합니다.

DevOps 모범 사례를 따르는 경우 이미 [Azure Resource Manager 템플릿](../azure-resource-manager/management/overview.md)을 사용하여 논리 앱 및 해당 종속 리소스를 정의하고 배포한 것입니다. Resource Manager 템플릿은 단일 배포 정의를 사용하고 매개 변수 파일을 사용하여 각 배포 대상에 사용할 구성 값을 제공하는 기능을 제공합니다. 이 기능은 동일한 논리 앱을 개발, 테스트, 프로덕션 등의 다양한 환경에 배포할 수 있음을 의미합니다. 또한 동일한 논리 앱을 다양한 Azure 지역 또는 ISE에 배포하여 [쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md)을 사용하는 재해 복구 전략을 지원할 수 있습니다.

장애 조치(failover) 전략의 경우 논리 앱 및 위치는 다음 요구 사항을 충족해야 합니다.

* 보조 논리 앱 인스턴스는 주 논리 앱 인스턴스와 동일한 앱, 서비스, 시스템에 액세스할 수 있습니다.

* 두 논리 앱 인스턴스가 동일한 호스트 유형을 갖습니다. 따라서 두 인스턴스 모두 글로벌 다중 테넌트 Azure의 지역에 배포되거나 두 인스턴스 모두 ISE에 배포되어 논리 앱이 Azure 가상 네트워크의 리소스에 직접 액세스할 수 있습니다. BCDR에 대 한 쌍을 이루는 지역에 대 한 모범 사례 및 자세한 내용은 [Azure에서 지역 간 복제: 비즈니스 연속성 및 재해 복구](../availability-zones/cross-region-replication-azure.md)를 참조 하세요.

  예를 들어 주 논리 앱이 ISE에서 실행되고 [ISE 버전의 커넥터](../connectors/managed.md#ise-connectors), Azure 가상 네트워크에서 리소스를 호출하는 HTTP 작업 또는 둘 다를 사용하는 경우 주 위치와 보조 위치 모두 ISE여야 합니다. 이 시나리오에서 보조 논리 앱에는 주 논리 앱과 유사한 설정이 보조 위치에 있어야 합니다.

  > [!NOTE]
  > 고급 시나리오에서는 다중 테넌트 Azure와 ISE를 위치로 함께 사용할 수 있습니다. 그러나 [ISE와 다중 테넌트 Azure에서 논리 앱이 실행되는 방식의 차이점](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)을 고려하고 이해해야 합니다.

* ISE를 사용하는 경우 로드를 처리할 수 있도록 [스케일 아웃되거나 충분한 용량이 있는지 확인](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)합니다.

#### <a name="example-multi-tenant-azure"></a>예: 다중 테넌트 Azure

이 예에서는 이 시나리오에 대한 글로벌 다중 테넌트 Azure의 개별 지역에 배포되는 주 논리 앱 인스턴스 및 보조 논리 앱 인스턴스를 보여 줍니다. 단일 [Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)은 두 논리 앱 인스턴스와 해당 논리 앱에 필요한 종속 리소스를 정의합니다. 별도의 매개 변수 파일은 각 배포 위치에 사용할 구성 값을 지정합니다.

![별도의 위치에 있는 주 논리 앱 인스턴스 및 보조 논리 앱 인스턴스](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>예: 통합 서비스 환경

이 예에서는 위의 주 논리 앱 인스턴스 및 보조 논리 앱 인스턴스가 별도의 ISE에 배포되는 경우를 보여 줍니다. 단일 Resource Manager 템플릿은 두 논리 앱 인스턴스, 해당 논리 앱에 필요한 종속 리소스 및 ISE를 배포 위치로 정의합니다. 별도의 매개 변수 파일은 각 위치의 배포에 사용할 구성 값을 정의합니다.

![다른 위치에 있는 주 논리 앱 및 보조 논리 앱](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>리소스에 대한 연결

Azure Logic Apps는 논리 앱이 다른 앱, 서비스, 시스템 및 기타 리소스(예: Azure Storage 계정, SQL Server 데이터베이스, 회사 또는 학교 메일 계정 등)로 작업하는 데 사용할 수 있는 [기본 제공 트리거와 작업 및 수백 가지의 관리형 커넥터](../connectors/apis-list.md)를 제공합니다. 논리 앱에서 이러한 리소스에 액세스해야 하는 경우 이러한 리소스에 대한 액세스를 인증하는 연결을 만듭니다. 각 연결은 특정 위치에 있는 별도의 Azure 리소스이며 다른 위치의 리소스에서 사용할 수 없습니다.

재해 복구 전략에서는 논리 앱 인스턴스를 기준으로 종속 리소스가 있는 위치를 고려합니다.

* 주 인스턴스와 종속 리소스가 다른 위치에 있습니다. 이 경우 보조 인스턴스는 동일한 종속 리소스 또는 엔드포인트에 연결할 수 있습니다. 그러나 보조 인스턴스에 대해서만 연결을 만들어야 합니다. 이렇게 하면 주 위치를 사용할 수 없게 되는 경우 보조 위치의 연결이 영향을 받지 않습니다.

  예를 들어 주 논리 앱이 Salesforce 같은 외부 서비스에 연결된다고 가정합니다. 일반적으로 외부 서비스의 가용성 및 위치는 논리 앱의 가용성과 별개입니다. 이 경우 보조 인스턴스는 동일한 서비스에 연결할 수 있지만 자체 연결이 있어야 합니다.

* 주 인스턴스와 종속 리소스가 모두 동일한 위치에 있습니다. 이 경우 종속 리소스의 백업이나 복제된 버전이 다른 위치에 있어야 보조 인스턴스에서 해당 리소스에 계속 액세스할 수 있습니다.

  예를 들어 주 논리 앱이 동일한 위치 또는 지역에 있는 서비스(예: Azure SQL Database)에 연결된다고 가정합니다. 이 전체 지역을 사용할 수 없게 되면 해당 지역의 Azure SQL Database 서비스도 사용하지 못할 수 있습니다. 이 경우 보조 인스턴스는 해당 데이터베이스에 대한 별도의 연결과 함께 복제된 데이터베이스나 백업 데이터베이스를 사용하려고 합니다.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>온-프레미스 데이터 게이트웨이

논리 앱이 다중 테넌트 Azure에서 실행되고 SQL Server 데이터베이스와 같은 온-프레미스 리소스에 액세스해야 하는 경우 로컬 컴퓨터에 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md)를 설치해야 합니다. 그런 다음, 리소스에 대한 연결을 만들 때 논리 앱이 게이트웨이를 사용할 수 있도록 Azure Portal에서 데이터 게이트웨이 리소스를 만들 수 있습니다.

데이터 게이트웨이 리소스는 논리 앱 리소스와 마찬가지로 위치나 Azure 지역에 연결됩니다. 재해 복구 전략에서는 논리 앱이 사용할 수 있도록 데이터 게이트웨이가 사용 가능한 상태로 유지되는지 확인합니다. 여러 게이트웨이가 설치되어 있는 경우 [게이트웨이에 고가용성을 사용하도록 설정](../logic-apps/logic-apps-gateway-install.md#high-availability)할 수 있습니다.

> [!NOTE]
> 논리 앱이 ISE(통합 서비스 환경)에서 실행되고 온-프레미스 데이터 원본에 ISE 버전의 커넥터만 사용하는 경우 ISE 커넥터는 온-프레미스 리소스에 대한 직접 액세스를 제공하기 때문에 데이터 게이트웨이가 필요하지 않습니다.
>
> 원하는 온-프레미스 리소스에 대해 ISE 버전의 커넥터를 사용할 수 없는 경우에도 논리 앱은 ISE가 아닌 글로벌 다중 테넌트 Azure에서 실행되는 비 ISE 커넥터를 사용하여 연결을 만들 수 있습니다. 그러나 이 연결에는 온-프레미스 데이터 게이트웨이가 필요합니다.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>활성-활성 및 활성-수동 역할

주 위치 및 보조 위치를 설정하여 이러한 위치의 논리 앱 인스턴스가 다음 역할을 수행하도록 할 수 있습니다.

| 주-보조 역할 | Description |
|------------------------|-------------|
| ‘활성-활성’ | 두 위치의 주 논리 앱 인스턴스 및 보조 논리 앱 인스턴스는 다음 패턴 중 하나에 따라 요청을 적극적으로 처리합니다. <p><p>- ‘부하 분산’: 두 인스턴스가 엔드포인트에서 수신 대기하고 필요에 따라 각 인스턴스에 대한 트래픽의 부하를 분산하도록 할 수 있습니다. <p>- ‘경쟁 소비자’: 인스턴스가 큐의 메시지에 대해 경쟁하도록 두 인스턴스가 경쟁 소비자 역할을 하도록 할 수 있습니다. 한 인스턴스가 실패하면 다른 인스턴스가 워크로드를 처리합니다. |
| *활성-수동* | 주 논리 앱 인스턴스는 전체 워크로드를 적극적으로 처리하는 반면, 보조 인스턴스는 수동(사용 안 함 또는 비활성)입니다. 보조 인스턴스는 중단 또는 오류로 인해 주 인스턴스가 사용 불가능하거나 작동하지 않는다는 신호를 대기하고 활성 인스턴스로서 워크로드를 처리합니다. |
| 결합 | 일부 논리 앱은 활성-활성 역할을 수행하고, 다른 논리 앱은 활성-수동 역할을 수행합니다. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>활성-활성 예

이러한 예에서는 두 논리 앱 인스턴스가 요청 또는 메시지를 적극적으로 처리하는 활성-활성 설정을 보여 줍니다. 일부 다른 시스템 또는 서비스는 인스턴스 간에 요청 또는 메시지를 배포합니다. 예를 들어 다음 옵션 중 하나입니다.

* “물리적” 부하 분산 장치(예: 트래픽을 라우팅하는 하드웨어)

* “소프트” 부하 분산 장치(예: [Azure Load Balancer](../load-balancer/load-balancer-overview.md) 또는 [Azure API Management](../api-management/api-management-key-concepts.md)). API Management를 사용하여 들어오는 트래픽의 부하를 분산하는 방법을 결정하는 정책을 지정할 수 있습니다. 또는 상태 추적을 지원하는 서비스(예: [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md))를 사용할 수 있습니다.

  이 예에서는 주로 Azure Load Balancer를 보여 주지만 시나리오 요구 사항에 가장 적합한 옵션을 사용할 수 있습니다.

  ![부하 분산 장치 또는 상태 저장 서비스를 사용하는 “활성-활성” 설정](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* 각 논리 앱 인스턴스는 소비자 역할을 하고 두 인스턴스는 큐의 메시지에 대해 경쟁합니다.

  ![“경쟁 소비자”를 사용하는 “활성-활성” 설정](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>활성-수동 예

이 예에서는 주 논리 앱 인스턴스는 한 위치에서 활성 상태이고 보조 인스턴스는 다른 위치에서 비활성 상태로 유지되는 활성-수동 설정을 보여 줍니다. 주 인스턴스에서 중단 또는 오류가 발생하는 경우 해당 워크로드를 수행하도록 보조 인스턴스를 활성화하는 스크립트를 운영자가 실행하도록 할 수 있습니다.

![“경쟁 소비자”를 사용하는 “활성-수동” 설정](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>활성-활성과 활성-수동의 조합

이 예에서는 주 위치에는 두 활성 논리 앱 인스턴스가 있고 보조 위치에는 활성-수동 논리 앱 인스턴스가 있는 결합된 설정을 보여 줍니다. 주 위치에서 중단 또는 오류가 발생하는 경우 부분 워크로드를 이미 처리하고 있는 보조 위치의 활성 논리 앱이 전체 워크로드를 처리할 수 있습니다.

* 주 위치에서, 활성 논리 앱은 Azure Service Bus 큐에서 메시지를 수신 대기하고 다른 활성 논리 앱은 Office 365 Outlook 폴링 트리거를 사용하여 메일을 확인합니다.

* 보조 위치에서, 활성 논리 앱은 동일한 Service Bus 큐에서 메시지를 수신 대기하고 경쟁하여 주 위치의 논리 앱과 함께 작동합니다. 한편, 수동 비활성 논리 앱은 대기 모드로 기다리다가 주 위치를 사용할 수 없게 되면 메일을 확인하지만 메일을 다시 읽을 수 없도록 ‘사용 안 함’ 상태입니다.

![되풀이 트리거를 사용하는 “활성-수동” 및 “활성-수동” 조합](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>논리 앱 상태 및 기록

논리 앱이 트리거되고 실행되기 시작하면 앱의 상태가 앱이 시작된 위치와 동일한 위치에 저장되며 다른 위치로 이동할 수 없습니다. 오류 또는 중단이 발생하면 진행 중인 모든 워크플로 인스턴스가 중단됩니다. 주 위치 및 보조 위치를 설정한 경우 새로운 워크플로 인스턴스가 보조 위치에서 실행되기 시작합니다.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>중단되는 진행 중인 인스턴스 줄이기

중단되는 진행 중인 워크플로 인스턴스의 수를 최소화하기 위해 구현할 수 있는 다양한 메시지 패턴 중에서 선택할 수 있습니다. 예를 들면 다음과 같습니다.

* [고정 라우팅 슬립 패턴](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  비즈니스 프로세스를 더 작은 단계로 분할하는 엔터프라이즈 메시지 패턴입니다. 각 단계에서 해당 단계에 대한 워크로드를 처리하는 논리 앱을 설정합니다. 논리 앱은 서로 통신하기 위해 Azure Service Bus 큐나 토픽 같은 비동기 메시징 프로토콜을 사용합니다. 프로세스를 더 작은 단계로 분할하면 실패한 논리 앱 인스턴스에서 중단될 수 있는 비즈니스 프로세스의 수가 줄어듭니다. 이 패턴에 대한 일반적인 정보는 [Enterprise integration patterns - Routing slip](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)(엔터프라이즈 통합 패턴 - 라우팅 슬립)을 참조하세요.

  이 예에서는 각 논리 앱이 단계를 나타내고 Service Bus 큐를 사용하여 프로세스의 다음 논리 앱과 통신하는 라우팅 슬립 패턴을 보여 줍니다.

  ![Azure Service Bus 큐를 사용하여 서로 통신하는 논리 앱이 나타내는 단계로 비즈니스 프로세스 분할](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  주 논리 앱 인스턴스와 보조 논리 앱 인스턴스가 모두 해당 위치에서 동일한 라우팅 슬립 패턴을 따르는 경우 해당 인스턴스에 대해 [활성-활성 역할](#roles)을 설정하여 [경쟁 소비자 패턴](/azure/architecture/patterns/competing-consumers)을 구현할 수 있습니다.

* [프로세스 관리자(broker) 패턴](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [시간 제한 없는 미리 보기-잠금 패턴](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>트리거 및 실행 기록 액세스

논리 앱의 이전 워크플로 실행에 대해 자세히 알아보려면 앱의 트리거 및 실행 기록을 검토할 수 있습니다. 논리 앱의 실행 기록은 논리 앱이 실행된 것과 동일한 위치 또는 지역에 저장됩니다. 즉, 이 기록을 다른 위치로 마이그레이션할 수 없습니다. 주 인스턴스가 보조 인스턴스로 장애 조치(failover)되는 경우 해당 인스턴스가 실행된 각 위치에서만 각 인스턴스의 트리거 및 실행 기록에 액세스할 수 있습니다. 그러나 Azure Log Analytics 작업 영역으로 진단 이벤트를 보내도록 논리 앱을 설정하여 논리 앱의 기록에 대한 위치 중립적 정보를 가져올 수 있습니다. 그런 다음, 여러 위치에서 실행되는 논리 앱에서 상태 및 기록을 검토할 수 있습니다.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>트리거 형식 지침

논리 앱에서 사용하는 트리거 형식에 따라 재해 복구 전략에서 여러 위치의 논리 앱을 설정하는 방법에 대한 옵션이 결정됩니다. 다음은 논리 앱에서 사용할 수 있는 트리거 형식입니다.

* [되풀이 트리거](#recurrence-trigger)
* [폴링 트리거](#polling-trigger)
* [요청 트리거](#request-trigger)
* [웹후크 트리거](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>되풀이 트리거

**되풀이** 트리거는 특정 서비스 또는 엔드포인트와 별개이며, 다른 조건 없이 지정된 일정에 따라서만 발생합니다. 예를 들면 다음과 같습니다.

* 고정 빈도 및 간격(예: 10분마다)
* 고급 일정(예: 매월 마지막 월요일 오후 5시)

논리 앱이 되풀이 트리거로 시작되는 경우 [활성-수동 역할](#roles)로 주 논리 앱 인스턴스 및 보조 논리 앱 인스턴스를 설정해야 합니다. 중단 또는 재해 발생 후 비즈니스 프로세스를 복원하는 대상 기간을 나타내는 RTO(‘복구 시간 목표’)를 줄이려면 [활성-수동 역할](#roles) 및 [수동-활성 역할](#roles)의 조합으로 논리 앱 인스턴스를 설정할 수 있습니다. 이 설정에서는 여러 위치 간에 일정을 분할합니다.

예를 들어 10분마다 실행되어야 하는 논리 앱이 있다고 가정합니다. 주 위치를 사용할 수 없게 되면 보조 위치에서 작업을 처리하도록 논리 앱 및 위치를 설정할 수 있습니다.

![되풀이 트리거를 사용하는 “활성-수동” 및 “수동-활성” 조합](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* 주 위치에서 이러한 논리 앱에 대해 [활성-수동 역할](#roles)을 설정합니다.

  * ‘활성’ 사용 논리 앱의 경우 매 정시에 시작하고 20분마다 반복(예: 오전 9시, 오전 9시 20분 등)하도록 되풀이 트리거를 설정합니다.

  * ‘수동’ 사용 안 함 논리 앱의 경우 동일한 일정이지만 매시 10분에 시작하고 20분마다 반복(예: 오전 9시 10분, 오전 9시 30분 등)하도록 되풀이 트리거를 설정합니다.

* 보조 위치에서 이러한 논리 앱에 대해 [수동-활성](#roles)을 설정합니다.

  * ‘수동’ 사용 안 함 논리 앱의 경우 매 정시에 시작하고 20분마다 반복(예: 오전 9시, 오전 9시 20분 등)되는 주 위치의 활성 논리 앱과 동일한 일정으로 되풀이 트리거를 설정합니다.

  * ‘활성’ 사용 논리 앱의 경우 매시 10분에 시작하고 20분마다 반복(예: 오전 9시 10분, 오전 9시 30분 등)되는 주 위치의 수동 논리 앱과 동일한 일정으로 되풀이 트리거를 설정합니다.

이제 주 위치에서 중단 이벤트가 발생하는 경우 대체 위치에서 수동 논리 앱을 활성화합니다. 이렇게 하면 오류를 찾는 데 시간이 걸리는 경우 이 설정에서 해당 지연 시간 동안 누락된 되풀이 수를 제한합니다.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>폴링 트리거

특정 서비스 또는 엔드포인트에서 처리할 새 데이터가 있는지 정기적으로 확인하기 위해 논리 앱은 고정된 되풀이 일정에 따라 서비스 또는 엔드포인트를 반복적으로 호출하는 ‘폴링’ 트리거를 사용할 수 있습니다. 서비스 또는 엔드포인트에서 제공하는 데이터는 다음 형식 중 하나일 수 있습니다.

* 정적 데이터 - 항상 읽을 수 있는 데이터를 설명합니다.
* 휘발성 데이터 - 읽은 다음에는 더 이상 사용할 수 없는 데이터를 설명합니다.

동일한 데이터를 반복적으로 읽지 않도록 하려면 논리 앱은 클라이언트 쪽 또는 서버, 서비스, 시스템 쪽에서 상태를 유지 관리하여 이전에 읽은 데이터를 기억해야 합니다.

* 클라이언트 쪽 상태를 사용하는 논리 앱은 상태를 유지 관리할 수 있는 트리거를 사용합니다.

  예를 들어 메일 받은 편지함에서 새 메시지를 읽는 트리거는 트리거가 가장 최근에 읽은 메시지를 기억할 수 있도록 해야 합니다. 이렇게 하면 트리거는 읽지 않은 다음 메시지가 도착할 때만 논리 앱을 시작합니다.

* 서버, 서비스, 시스템 쪽 상태를 사용하는 논리 앱은 서버, 서비스, 시스템 쪽에 있는 속성 값 또는 설정을 사용합니다.

  예를 들어 데이터베이스에서 행을 읽는 쿼리 기반 트리거를 사용하려면 행에 `FALSE`로 설정된 `isRead` 열이 있어야 합니다. 트리거가 행을 읽을 때마다 논리 앱은 `isRead` 열을 `FALSE`에서 `TRUE`로 변경하여 해당 행을 업데이트합니다.

  이 서버 쪽 접근 방식은 논리 앱이 메시지를 처리하는 동안 트리거에서 메시지를 읽고 잠글 수 있는 큐 의미 체계를 사용하는 Service Bus 큐 또는 토픽과 유사하게 작동합니다. 논리 앱의 처리가 완료되면 트리거는 큐 또는 토픽에서 메시지를 삭제합니다.

재해 복구 관점에서, 논리 앱의 주 인스턴스 및 보조 인스턴스를 설정할 때 논리 앱이 상태를 클라이언트 쪽에서 추적하는지 서버 쪽에서 추적하는지에 따라 이러한 동작을 고려해야 합니다.

* 클라이언트 쪽 상태를 사용하는 논리 앱의 경우 논리 앱이 동일한 메시지를 두 번 이상 읽지 않도록 해야 합니다. 특정 시간에 한 위치에만 활성 논리 앱 인스턴스가 있을 수 있습니다. 주 인스턴스가 대체 위치로 장애 조치(failover)될 때까지 대체 위치의 논리 앱 인스턴스가 비활성 또는 사용 안 함 상태인지 확인합니다.

  예를 들어 Office 365 Outlook 트리거는 클라이언트 쪽 상태를 유지 관리하고 가장 최근에 읽은 메일에 대한 타임스탬프를 추적하여 중복된 항목을 읽지 않도록 합니다.

* 서버 쪽 상태를 사용하는 논리 앱의 경우 경쟁 소비자 역할을 하는 [활성-활성 역할](#roles) 또는 주 인스턴스가 대체 위치로 장애 조치(failover)될 때까지 대체 인스턴스가 대기하는 [활성-수동 역할](#roles)을 수행하도록 논리 앱 인스턴스를 설정할 수 있습니다.

  예를 들어 Azure Service Bus 큐와 같은 메시지 큐에서 읽기에서는 큐 서비스가 메시지에 대한 잠금을 유지 관리하여 다른 클라이언트가 동일한 메시지를 읽지 못하도록 하기 때문에 서버 쪽 상태를 사용합니다.

  > [!NOTE]
  > 예를 들어 논리 앱이 Service Bus 큐에서 특정 순서로 메시지를 읽어야 하는 경우 경쟁 소비자 패턴을 사용할 수 있지만 [‘순차 호위(convoy)’ 패턴](/azure/architecture/patterns/sequential-convoy)이라고도 하는 Service Bus 세션과 함께만 사용할 수 있습니다. 그러지 않으면 활성-수동 역할로 논리 앱 인스턴스를 설정해야 합니다.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>요청 트리거

**요청** 트리거는 다른 앱, 서비스, 시스템에서 논리 앱을 호출할 수 있도록 하며 일반적으로 다음 기능을 제공하는 데 사용됩니다.

* 다른 논리 앱에서 호출할 수 있는 논리 앱에 대한 직접 REST API

  예를 들어 요청 트리거를 사용하여 논리 앱을 시작하면 다른 논리 앱에서 **호출 워크플로 - Logic Apps** 작업을 사용하여 트리거를 호출할 수 있습니다.

* 논리 앱에 대한 [웹후크](#webhook-trigger) 또는 콜백 메커니즘

* 특정 작업(task)을 수행하는 PowerShell 스크립트를 사용하는 경우처럼 논리 앱을 호출하는 사용자 작업(operation) 또는 루틴을 수동으로 실행할 수 있는 방법

재해 복구 관점에서, 논리 앱은 작업을 수행하지 않고 다른 서비스 또는 시스템이 명시적으로 트리거를 호출할 때까지 대기하므로 요청 트리거는 수동 수신기입니다. 수동 엔드포인트로서 다음과 같은 방법으로 주 인스턴스 및 보조 인스턴스를 설정할 수 있습니다.

* [활성-활성](#roles): 두 인스턴스가 요청 또는 호출을 적극적으로 처리합니다. 호출자 또는 라우터는 이러한 인스턴스 간에 트래픽을 분산하거나 트래픽의 균형을 맞춥니다.

* [활성-수동](#roles): 주 인스턴스만 활성 상태로 모든 작업을 처리하고, 보조 인스턴스는 주 인스턴스에서 중단 또는 오류가 발생할 때까지 대기합니다. 호출자 또는 라우터는 보조 인스턴스 호출 시기를 결정합니다.

권장되는 아키텍처로, 요청 트리거를 사용하는 논리 앱에 대한 프록시로 Azure API Management를 사용할 수 있습니다. API Management는 [기본 제공 지역 간 복원력 및 여러 엔드포인트 간에 트래픽을 라우팅하는 기능](../api-management/api-management-howto-deploy-multi-region.md)을 제공합니다.

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>웹후크 트리거

‘웹후크’ 트리거는 논리 앱이 해당 서비스에 ‘콜백 URL’을 전달하여 서비스를 구독하는 기능을 제공합니다.  그러면 논리 앱은 해당 서비스 엔드포인트에서 특정 이벤트가 발생할 때까지 수신 대기할 수 있습니다. 이벤트가 발생하면 서비스는 콜백 URL을 사용하여 웹후크 트리거를 호출한 다음, 논리 앱을 실행합니다. 사용하도록 설정하면 웹후크 트리거가 서비스를 구독합니다. 사용하지 않도록 설정하면 트리거가 서비스에서 구독을 취소합니다.

재해 복구 관점에서, 하나의 인스턴스만 구독된 엔드포인트에서 이벤트 또는 메시지를 받아야 하므로 웹후크 트리거를 사용하여 활성-수동 역할을 수행하는 주 인스턴스와 보조 인스턴스를 설정합니다.

## <a name="assess-primary-instance-health"></a>주 인스턴스 상태 평가

재해 복구 전략이 작동하려면 솔루션에 다음 작업을 수행하는 방법이 필요합니다.

* [주 인스턴스의 가용성 확인](#check-primary-availability)
* [주 인스턴스의 상태 모니터링](#monitor-primary-health)
* [보조 인스턴스 활성화](#activate-secondary)

이 섹션에서는 직접 디자인하는 데 기반으로 사용하거나 바로 사용할 수 있는 한 가지 솔루션에 대해 설명합니다. 다음은 이 솔루션에 대한 간단한 시각적 개요입니다.

![주 위치에서 상태 검사 논리 앱을 모니터링하는 watchdog 논리 앱 만들기](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>주 인스턴스 가용성 확인

주 인스턴스가 사용 가능한지, 실행 중인지, 작동할 수 있는지를 확인하기 위해 주 인스턴스와 동일한 위치에 있는 “상태 검사” 논리 앱을 만들 수 있습니다. 그런 다음, 대체 위치에서 이 상태 검사 앱을 호출할 수 있습니다. 상태 검사 앱이 성공적으로 응답하면 해당 지역의 Azure Logic Apps 서비스에 대한 기본 인프라가 사용 가능하고 작동하는 것입니다. 상태 검사 앱이 응답하지 않으면 위치가 더 이상 정상이 아닌 것으로 가정할 수 있습니다.

이 작업을 위해 다음 작업을 수행하는 기본 상태 검사 논리 앱을 만듭니다.

1. 요청 트리거를 사용하여 watchdog 앱에서 호출을 받습니다.

1. 응답 작업을 사용하여 검사한 논리 앱이 계속 작동하는지 여부를 나타내는 상태로 응답합니다.

   > [!IMPORTANT]
   > 상태 검사 논리 앱은 앱이 비동기적으로가 아닌 동기적으로 응답하도록 응답 작업을 사용해야 합니다.

1. 필요에 따라 주 위치가 정상인지 확인하려면 이 위치의 대상 논리 앱과 상호 작용하는 다른 모든 서비스의 상태를 고려할 수 있습니다. 이러한 다른 서비스의 상태도 평가하려면 상태 검사 논리 앱을 확장하면 됩니다.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>watchdog 논리 앱 만들기

주 인스턴스의 상태를 모니터링하고 상태 검사 논리 앱을 호출하려면 ‘대체 위치’에 “watchdog” 논리 앱을 만듭니다. 예를 들어 상태 검사 논리 호출이 실패할 경우 watchdog에서 운영 팀으로 경고를 보내 운영 팀에서 오류 및 주 인스턴스가 응답하지 않는 이유를 조사할 수 있도록 watchdog 논리 앱을 설정할 수 있습니다.

> [!IMPORTANT]
> watchdog 논리 앱이 ‘주 위치와 다른 위치’에 있는지 확인합니다. 주 위치의 Logic Apps 서비스에서 문제가 발생하는 경우 watchdog 논리 앱이 실행되지 않을 수 있습니다.

이 작업을 위해 보조 위치에서 다음 작업을 수행하는 watchdog 논리 앱을 만듭니다.

1. 되풀이 트리거를 사용하여 고정 되풀이 또는 예약된 되풀이에 따라 실행합니다.

   RTO(복구 시간 목표)에 대한 허용 오차 수준보다 낮은 값으로 되풀이를 설정할 수 있습니다.

1. HTTP 작업을 사용하여 주 위치에서 상태 검사 논리 앱을 호출합니다. 예를 들면 다음과 같습니다.

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>보조 인스턴스 활성화

보조 인스턴스를 자동으로 활성화하려면 [Azure Resource Manager 커넥터](/connectors/arm/)와 같은 관리 API를 호출하는 논리 앱을 만들어 보조 위치에서 적절한 논리 앱을 활성화할 수 있습니다. 특정 횟수의 오류가 발생한 후 이 활성화 논리 앱을 호출하도록 watchdog 앱을 확장할 수 있습니다.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>진단 데이터 수집

논리 앱 실행에 대한 로깅을 설정하고 결과 진단 데이터를 Azure Storage, Azure Event Hubs, Azure Log Analytics 등의 서비스로 전송하여 추가로 처리할 수 있습니다.

* Azure Log Analytics에서 이 데이터를 사용하려는 경우 논리 앱의 **진단 설정** 을 설정하고 여러 Log Analytics 작업 영역으로 데이터를 전송하여 주 위치 및 보조 위치 모두에서 데이터를 사용 가능하도록 설정할 수 있습니다. 자세한 내용은 [Azure Monitor 로그 설정 및 Azure Logic Apps에 대한 진단 데이터 수집](../logic-apps/monitor-logic-apps-log-analytics.md)을 참조하세요.

* Azure Storage 또는 Azure Event Hubs로 데이터를 전송하려는 경우 지역 중복을 설정하여 주 위치 및 보조 위치 모두에서 데이터를 사용 가능하도록 설정할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.<p>

  * [Azure Blob Storage 재해 복구 및 계정 장애 조치(failover)](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>다음 단계

* [신뢰할 수 있는 Azure 애플리케이션 디자인](/azure/architecture/framework/resiliency/app-design)
* [특정 Azure 서비스에 대한 복원력 검사 목록](/azure/architecture/checklist/resiliency-per-service)
* [Azure의 복원력을 위한 데이터 관리](/azure/architecture/framework/resiliency/data-management)
* [Azure 애플리케이션의 백업 및 재해 복구](/azure/architecture/framework/resiliency/backup-and-recovery)
* [지역 전체의 서비스 중단으로부터 복구](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Azure 서비스에 대한 Microsoft SLA(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/)
