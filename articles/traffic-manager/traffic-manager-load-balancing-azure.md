---
title: Azure에서 부하 분산 서비스 사용 | Microsoft Docs
description: 이 자습서에서는 Azure 부하 분산 포트폴리오인 Traffic Manager, Application Gateway 및 Load Balancer를 사용하여 시나리오를 만드는 방법을 보여줍니다.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184376"
---
# <a name="using-load-balancing-services-in-azure"></a>Azure에서 부하 분산 서비스 사용

## <a name="introduction"></a>소개

Microsoft Azure는 네트워크 트래픽을 분산하고 부하를 분산하는 방법을 관리하는 여러 서비스를 제공합니다. 사용자의 요구 사항에 따라 이러한 서비스를 개별적으로 사용하거나 해당 메서드를 결합하여 최적의 솔루션을 빌드할 수 있습니다.

이 자습서에서는 먼저 고객 사용 사례를 정의하고 Azure 부하 분산 포트폴리오인 Traffic Manager, Application Gateway 및 Load Balancer를 사용하여 보다 강력하게 성능을 향상시키는 방법을 알아봅니다. 그런 다음 지리적으로 중복되고 VM에 트래픽을 분산하며 다양한 유형의 요청을 관리하도록 하는 배포를 만들기 위한 단계별 지침을 제공합니다.

개념 수준에서 이러한 서비스는 각각 부하 분산 계층 구조에서 고유한 역할을 담당합니다.

* **Traffic Manager** 는 전역 DNS 부하 분산을 제공 합니다. 고객이 선택한 라우팅 정책에 따라 들어오는 DNS 요청을 보고 정상적인 엔드포인트를 통해 응답합니다. 라우팅 메서드의 옵션은 다음과 같습니다.
  * 대기 시간 측면에서 가장 가까운 엔드포인트에 요청자를 보내는 성능 라우팅
  * 백업인 다른 엔드포인트를 통해 엔드포인트에 모든 트래픽을 다이렉트하는 우선 순위 라우팅
  * 각 엔드포인트에 할당된 가중치에 따라 트래픽을 분산하는 가중치가 적용된 라운드 로빈 라우팅
  * 사용자의 지리적 위치를 기반으로 애플리케이션 엔드포인트에 트래픽을 분산시키는 지리 기반 라우팅입니다.
  * 사용자의 서브넷(IP 주소 범위)을 기반으로 애플리케이션 엔드포인트에 트래픽을 분산시키는 서브넷 기반 라우팅입니다.
  * 단일 DNS 응답에서 둘 이상의 IP 주소를 보낼 수 있는 다중값 라우팅입니다.

  클라이언트는 Traffic Manager가 반환하는 엔드포인트에 직접 연결합니다. Azure Traffic Manager는 엔드포인트가 비정상임을 감지한 다음 클라이언트를 다른 정상적인 인스턴스로 리디렉션합니다. 서비스에 대한 자세한 내용은 [Azure Traffic Manager 설명서](traffic-manager-overview.md)를 참조하세요.
* **Application Gateway** 는 ADC(애플리케이션 전달 컨트롤러)를 서비스로 제공하여 애플리케이션에 대한 다양한 Layer 7 부하 분산 기능을 제공합니다. 따라서 고객은 애플리케이션 게이트웨이에 CPU 집약적인 TLS 종료를 오프로드하여 웹 팜 생산성을 최적화할 수 있습니다. Layer 7의 기타 라우팅 기능으로 들어오는 트래픽의 라운드 로빈 배포, 쿠키 기반 세션 선호도, URL 패스 기반 라우팅 및 단일 Application Gateway 뒤에 여러 웹 사이트를 호스트할 수 있는 기능 등을 포함합니다. Application Gateway는 인터넷 연결 게이트웨이, 내부 전용 게이트웨이 또는 둘의 조합으로 구성될 수 있습니다. Application Gateway는 전적으로 Azure에 의해 관리되고, 확장성 및 고가용성을 제공합니다. 관리 효율성을 향상시키기 위한 풍부한 진단 및 로깅 기능을 제공합니다.
* **Load Balancer** 는 Azure SDN 스택의 필수적인 부분으로 모든 UDP 및 TCP 프로토콜에 대한 고성능, 지연율이 낮은 Layer 4 부하 분산 서비스를 제공 합니다. 인바운드 및 아웃 바운드 연결을 관리합니다. 서비스 가용성을 관리하는 TCP 및 HTTP 상태 확인 옵션을 사용하여 공용 및 내부 부하가 분산된 엔드포인트를 구성하고 백 엔드 풀 대상에 인바운드 연결을 매핑하는 규칙을 정의할 수 있습니다.

## <a name="scenario"></a>시나리오

이 예제 시나리오에서는 두 가지 유형의 콘텐츠와 동적으로 렌더링 되는 웹 페이지를 제공하는 간단한 웹 사이트를 사용합니다. 웹 사이트는 지리적으로 중복되고 사용자들에게 가장 가까운 (가장 낮은 대기 시간) 위치에서 서비스를 제공해야 합니다. 애플리케이션 개발자는 /images/* 패턴과 일치하는 모든 URL이 웹 팜의 나머지 부분과는 다른 전용 VM 풀에서 서비스를 제공하도록 결정했습니다.

또한 동적 콘텐츠를 제공하는 기본 VM 풀은 고가용성 클러스터에 호스트되는 백 엔드 데이터베이스와 통신해야 합니다. 전체 배포는 Azure Resource Manager를 통해 설정됩니다.

Traffic Manager, Application Gateway 및 Load Balancer를 사용하여 이 웹 사이트에서 다음과 같은 디자인 목표를 달성할 수 있습니다.

* **다중 지역 중복**: 한 지역의 작동이 중단되더라도 Traffic Manager에서는 애플리케이션 소유자의 개입 없이 가장 가까운 지역으로 트래픽을 원활하게 라우팅합니다.
* **대기 시간 감소**: Azure Traffic Manager에서 고객을 자동으로 가장 가까운 지역으로 다이렉트하기 때문에 고객이 웹 페이지 콘텐츠를 요청할 때 대기 시간이 줄어들게 됩니다.
* **독립 확장성**: 웹 애플리케이션 워크로드를 콘텐츠 형식에 따라 구분했으므로 애플리케이션 소유자는 서로 독립적인 요청 워크로드를 확장할 수 있습니다. Application Gateway는 지정된 규칙 및 애플리케이션의 상태에 따라 트래픽을 적당한 풀로 라우팅되게 합니다.
* **내부 부하 분산**: Load Balancer가 고가용성 클러스터 앞에 배치되기 때문에 데이터베이스에 대한 활성 및 정상 엔드포인트만이 애플리케이션에 노출됩니다. 또한 데이터베이스 관리자는 프런트 엔드 애플리케이션과 독립적인 클러스터 전반에 활성 및 수동 복제본을 배포하여 워크로드를 최적화할 수 있습니다. Load Balancer는 고가용성 클러스터에 연결을 제공하고 정상적인 데이터베이스만 연결 요청을 받는지 확인합니다.

다음 다이어그램은 이 시나리오의 아키텍처를 보여 줍니다.

![부하 분산 아키텍처의 다이어그램](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> 이 예제는 Azure에서 제공하는 부하 분산 서비스의 다양한 구성 중 하나에 불과합니다. Traffic Manager, Application Gateway 및 Load Balancer를 부하 분산 요구에 가장 잘 맞게 혼합 및 일치시킬 수 있습니다. 예를 들어, TLS 오프로드 또는 Layer 7 처리가 필요 없는 경우 부하 분산 장치 Application Gateway 대신에 Load Balancer를 사용할 수 있습니다.

## <a name="setting-up-the-load-balancing-stack"></a>부하 분산 스택 설정

### <a name="step-1-create-a-traffic-manager-profile"></a>1단계: Traffic Manager 프로필 만들기

1. Azure Portal에서 **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기** 를 클릭합니다.
2. 다음 기본 정보를 입력하세요.

   * **이름**: Traffic Manager 프로필에 DNS 접두사 이름을 지정합니다.
   * **라우팅 정책** 에서 트래픽 라우팅 메서드 정책을 선택합니다. 메서드에 대한 자세한 내용은 [Traffic Manager 트래픽 라우팅 메서드 정보](traffic-manager-routing-methods.md)를 참조하세요.
   * **구독**: 프로필을 포함하는 구독을 선택합니다.
   * **리소스 그룹**: 프로필을 포함하는 리소스 그룹을 선택합니다. 새 리소스 그룹이나 기존 리소스 그룹을 선택할 수 있습니다.
   * **리소스 그룹 위치**: Traffic Manager 서비스는 전역적이며 위치에 묶여 있지 않습니다. 그러나 Traffic Manager 프로필과 연결된 메타데이터가 있는 그룹의 지역을 지정해야 합니다. 이 위치는 프로필의 런타임 가용성에 영향을 주지 않습니다.

3. Traffic Manager 프로필을 생성하려면 **만들기** 를 클릭합니다.

   !["Traffic Manager 만들기" 블레이드](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2단계: Application Gateway 만들기

1. Azure Portal의 왼쪽 창에서 **리소스 만들기** > **네트워킹** > **Application Gateway** 를 클릭합니다.
2. Application Gateway에 대한 기본 정보를 입력합니다.

   * **이름**: Application Gateway의 이름입니다.
   * **SKU 크기**:Application Gateway의 크기이며 소형, 중형 및 대형을 사용 가능합니다.
   * **인스턴스 개수**: 인스턴스의 수이며 2에서 10사이의 값입니다.
   * **리소스 그룹**: Application Gateway를 보유하는 리소스 그룹입니다. 기존 리소스 그룹이나 새 리소스 그룹을 선택할 수 있습니다.
   * **위치**: Application Gateway의 지역이며 리소스 그룹과 동일한 위치입니다. 위치는 가상 네트워크와 공용 IP가 게이트웨이와 동일한 위치에 있어야 하므로 중요합니다.
3. **확인** 을 클릭합니다.
4. Application Gateway에 대한 가상 네트워크, 서브넷, 프런트 엔드 IP 및 수신기 구성을 정의합니다. 이 시나리오에서 프런트 엔드 IP 주소는 **공용** 이며 따라서 나중에 Traffic Manager 프로필에 엔드포인트로 추가할 수 있습니다.
5. 다음 옵션 중 하나를 사용하여 수신기를 구성합니다.
    * HTTP를 사용하면 구성할 항목이 없습니다. **확인** 을 클릭합니다.
    * HTTPS를 사용하는 경우 추가 구성이 필요합니다. 9단계에서 시작하는 [Application Gateway 만들기](../application-gateway/quick-create-portal.md)를 참조하세요. 구성 작업을 완료한 경우 **확인** 을 클릭합니다.

#### <a name="configure-url-routing-for-application-gateways"></a>Application Gateway에 대한 URL 라우팅 구성

백 엔드 풀을 선택하는 경우 경로 기반 규칙을 사용하여 구성된 Application Gateway는 라운드 로빈 배포 외에도 요청 URL의 경로 패턴을 사용합니다. 이 시나리오에서는 "/images/\*"를 포함하는 모든 URL을 이미지 서버 풀로 보내도록 패스 기반 규칙을 추가합니다. Application Gateway에 대해 URL 경로 기반 라우팅을 구성하는 방법에 대한 자세한 내용은 [Application Gateway에 대한 경로 기반 규칙 만들기](../application-gateway/create-url-route-portal.md)를 참조하세요.

![Application Gateway 웹 계층 다이어그램](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. 리소스 그룹에서 앞의 섹션에서 만든 Application Gateway의 인스턴스로 이동합니다.
2. **설정** 에서 **백 엔드 풀** 을 선택한 다음 **추가** 를 선택하여 웹 계층 백 엔드 풀과 연결하려는 VM을 추가합니다.
3. 백 엔드 풀의 이름과 풀에 있는 컴퓨터의 모든 IP 주소를 입력합니다. 이 시나리오에서는 가상 머신에 있는 두 개의 백 엔드 서버 풀을 연결합니다.

   ![Application Gateway “백 엔드 풀 추가”](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Application Gateway의 **설정** 에서 **규칙** 을 선택한 다음, **경로 기반** 단추를 클릭하여 새 규칙을 추가합니다.

   ![Application Gateway 규칙 "경로 기반" 단추](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. 다음 정보를 제공하여 규칙을 구성하세요.

   기본 설정:

   + **이름**: 포털에서 액세스할 수 있는 규칙의 이름입니다.
   + **수신기**: 규칙에 사용되는 수신기입니다.
   + **기본 백 엔드 풀**: 기본 규칙에 사용할 백 엔드 풀입니다.
   + **기본 HTTP 설정**: 기본 규칙에 사용할 HTTP 설정입니다.

   패스 기반 규칙:

   + **이름**: 경로 기반 규칙의 이름입니다.
   + **경로**: 트래픽 전달에 사용되는 경로 규칙입니다.
   + **백 엔드 풀**: 이 규칙에 사용할 백 엔드 풀입니다.
   + **HTTP 설정**: 이 규칙에 사용할 HTTP 설정입니다.

   > [!IMPORTANT]
   > 패스: 유효한 패스는 "/"로 시작해야 합니다. 와일드 카드 "\*"는 끝에만 허용됩니다. 사용 가능한 예는 /xyz, /xyz\* 또는 /xyz/\*입니다.

   ![Application Gateway "경로 기반 규칙 추가" 블레이드](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3단계: Traffic Manager 엔드포인트에 Application Gateway 추가

이 시나리오에서 Traffic Manager는 다른 지역에 있는 Application Gateway(위의 단계에서 구성됨)에 연결됩니다. Application Gateway를 구성했으므로 다음 단계에서는 Traffic Manager 프로필에 연결합니다.

1. Traffic Manager 프로필을 엽니다. 이렇게 하려면 리소스 그룹을 확인하거나 **모든 리소스** 에서 Traffic Manager 프로필의 이름을 검색합니다.
2. 왼쪽 창에서 **엔드포인트** 를 선택한 다음 **추가** 를 클릭하여 엔드포인트를 추가합니다.

   ![Traffic Manager 엔드포인트 "추가" 단추](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. 다음 정보를 입력하여 엔드포인트을 만드세요.

   * **유형**: 부하 분산 엔드포인트의 유형을 선택합니다. 이 시나리오에서는 이전에 구성된 Application Gateway 인스턴스에 연결 중이기 때문에 **Azure 엔드포인트** 를 선택합니다.
   * **이름**: 엔드포인트의 이름을 입력합니다.
   * **대상 리소스 유형**: **공용 IP 주소** 를 선택한 다음, **대상 리소스** 에서 앞서 구성한 Application Gateway의 공용 IP를 선택합니다.

   ![Traffic Manager “엔드포인트 추가”](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. 이제 Traffic Manager 프로필의 DNS로 액세스하여 사용자의 설정을 테스트할 수 있습니다(이 경우 TrafficManagerScenario.trafficmanager.net). 요청을 다시 전송하고 다른 지역에서 만든 VM 및 웹 서버를 내보내거나 가져오고 Traffic Manager 프로필 설정을 변경하여 사용자 설정을 테스트할 수 있습니다.

### <a name="step-4-create-a-load-balancer"></a>4단계: 부하 분산 장치 만들기

이 시나리오에서 Load Balancer는 고가용성 클러스터 내에서 웹 계층으로부터 데이터베이스로 연결을 배포합니다.

고가용성 데이터베이스 클러스터에서 SQL Server Always On을 사용하는 경우 단계별 지침은 [하나 이상의 Always On 가용성 그룹 수신기 구성](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md)을 참조하세요.

내부 부하 분산 장치 구성에 대한 자세한 내용은 [Azure Portal에서 내부 부하 분산 장치 만들기](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)를 참조하세요.

1. Azure Portal의 왼쪽 창에서 **리소스 만들기** > **네트워킹** > **Load Balancer** 를 클릭합니다.
2. 부하 분산 장치의 이름을 선택합니다.
3. **유형** 을 **내부** 로 설정하고 부하 분산 장치가 배치될 적절한 가상 네트워크 및 서브넷을 선택합니다.
4. **IP 주소 할당** 에서 **동적** 또는 **정적** 중 하나를 선택합니다.
5. **리소스 그룹** 에서 부하 분산 장치에 대한 리소스 그룹을 선택합니다.
6. **위치** 에서 부하 분산 장치에 대한 적절한 리소스 그룹을 선택합니다.
7. **만들기** 를 클릭하여 부하 분산 장치를 생성합니다.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>백 엔드 데이터베이스 계층을 부하 분산 장치에 연결

1. 리소스 그룹에서 이전 단계에서 만든 부하 분산 장치를 찾습니다.
2. **설정** 에서 **백 엔드 풀** 과 **추가** 를 차례로 클릭하여 새 백 엔드 풀을 추가합니다.

   ![부하 분산 장치 “백 엔드 풀 추가”](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. 백 엔드 풀의 이름을 입력합니다.
4. 개별 컴퓨터 또는 가용성 집합을 백 엔드 풀에 추가합니다.

#### <a name="configure-a-probe"></a>프로브를 구성합니다.

1. 부하 분산 장치의 **설정** 에서 **프로브** 와 **추가** 를 차례로 선택합니다.

   ![부하 분산 장치 “프로브 추가”](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. 프로브의 이름을 입력합니다.
3. 프로브에 대한 **프로토콜** 을 선택합니다. 데이터베이스의 경우 HTTP 프로브가 아닌 TCP 프로브를 사용하려고 할 수 있습니다. Load Balancer 프로브에 대한 자세한 내용은 [Load Balancer프로브 이해](../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.
4. 프로브에 액세스하는 데 사용될 데이터베이스의 **포트** 를 입력합니다.
5. **간격** 에서 얼마나 자주 애플리케이션을 프로브할지 지정합니다.
6. **비정상 임계값** 에서 백 엔드 VM을 비정상 상태로 간주하는 경우 발생해야 하는 연속 프로브 오류 개수를 지정합니다.
7. **확인** 을 클릭하여 프로브를 만듭니다.

#### <a name="configure-the-load-balancing-rules"></a>부하 분산 규칙 구성

1. 부하 분산 장치의 **설정** 에서 규칙을 만들려면 **부하 분산 규칙** 과 **추가** 를 차례로 선택합니다.
2. 부하 분산 규칙의 **이름** 을 입력합니다.
3. 부하 분산 장치의 **프런트 엔드 IP 주소**, **프로토콜** 및 **포트** 를 선택합니다.
4. **백 엔드 포트** 에서 백 엔드 풀에 사용할 포트를 지정합니다.
5. 규칙을 적용할 이전 단계에서 만든 **백 엔드 풀** 과 **프로브** 를 선택합니다.
6. **세션 지속성** 에서 세션을 얼마나 지속할지 선택합니다.
7. **유휴 시간 제한** 에서, 유휴 시간 제한까지의 시간(분)을 지정합니다.
8. **부동 IP** 에서 **사용 안 함** 이나 **사용** 을 선택합니다.
9. **확인** 을 클릭하여 규칙을 만듭니다.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>5단계: 부하 분산 장치에 웹 계층 VM 연결

이제 모든 데이터베이스 연결에 대해 웹 계층 VM에서 실행 중인 애플리케이션의 IP 주소 및 부하 분산 장치 프런트 엔드 포트를 구성합니다. 이 구성은 이러한 VM에서 실행되는 애플리케이션에 지정됩니다. 대상 IP 주소와 포트를 구성하려면 애플리케이션 설명서를 참조하세요. 프런트 엔드의 IP 주소를 찾으려면 Azure Portal의 **부하 분산 장치 설정** 에서 프런트 엔드 IP 풀로 이동합니다.

![부하 분산 장치 "프런트 엔드 IP 풀" 탐색 창](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 개요](traffic-manager-overview.md)
* [Application Gateway 개요](../application-gateway/overview.md)
* [Azure Load Balancer개요](../load-balancer/load-balancer-overview.md)