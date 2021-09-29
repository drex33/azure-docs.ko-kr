---
title: 프라이빗 엔드포인트를 사용하여 Data Factory Managed VNet에서 온-프레미스 SQL Server에 액세스
description: 이 자습서에서는 Azure Portal을 사용하여 Private Link Service를 설치하고 프라이빗 엔드포인트를 사용하여 Managed VNet에서 온-프레미스 SQL Server에 액세스하는 단계를 제공합니다.
author: lrtoyou1223
ms.author: lle
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 05/06/2021
ms.openlocfilehash: d924abc214d01cc4e981d7398a6a250753cb50a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605034"
---
# <a name="tutorial-how-to-access-on-premises-sql-server-from-data-factory-managed-vnet-using-private-endpoint"></a>자습서: 프라이빗 엔드포인트를 사용하여 Data Factory Managed VNet에서 온-프레미스 SQL Server에 액세스하는 방법

이 자습서에서는 Azure Portal을 사용하여 Private Link Service를 설치하고 프라이빗 엔드포인트를 사용하여 Managed VNet에서 온-프레미스 SQL Server에 액세스하는 단계를 제공합니다.

> [!NOTE]
> 이 문서에 나와 있는 솔루션은 SQL Server 연결에 대해 설명하지만 비슷한 방법을 사용하여 Azure Data Factory에서 지원되는 사용 가능한 다른 [온-프레미스 커넥터](connector-overview.md)를 연결하고 쿼리할 수 있습니다.

:::image type="content" source="./media/tutorial-managed-virtual-network/sql-server-access-model.png" alt-text="SQL Server의 액세스 모델을 보여 주는 스크린샷" lightbox="./media/tutorial-managed-virtual-network/sql-server-access-model-expanded.png":::

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Virtual Network**. 가상 네트워크가 없는 경우 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 따라서 가상 네트워크를 만듭니다.
* **가상 네트워크를 온-프레미스 네트워크로**. [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=/azure/virtual-network/toc.json) 또는 [VPN을](../vpn-gateway/tutorial-site-to-site-portal.md?toc=/azure/virtual-network/toc.json)사용하여 가상 네트워크와 온-프레미스 네트워크 간에 연결을 만듭니다.
* **Managed VNet이 사용하도록 설정된 Data Factory**. Data Factory가 없거나 Managed VNet을 사용하도록 설정하지 않은 경우 [Managed VNet을 사용하여 Data Factory 만들기](tutorial-copy-data-portal-private.md)에 따라 Data Factory를 만듭니다.

## <a name="create-subnets-for-resources"></a>리소스에 대한 서브넷 만들기

**포털을 사용하여 가상 네트워크에 서브넷을 만듭니다.**

| 서브넷 | Description |
|:--- |:--- |
|be-subnet |백 엔드 서버용 서브넷|
|fe-subnet |표준 내부 부하 분산 장치용 서브넷|
|pls-subnet|Private Link Service에 대한 서브넷|

:::image type="content" source="./media/tutorial-managed-virtual-network/subnets.png" alt-text="서브넷을 보여 주는 스크린샷" lightbox="./media/tutorial-managed-virtual-network/subnets-expanded.png":::

## <a name="create-a-standard-load-balancer"></a>표준 부하 분산 장치 만들기

포털을 사용하여 표준 내부 부하 분산 장치를 만듭니다.

1. 화면 왼쪽 상단에서 **리소스 만들기 > 네트워킹 > Load Balancer** 를 선택합니다.
2. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    |:--- |:--- |
    |Subscription|구독을 선택합니다.|
    |Resource group|리소스 그룹을 선택합니다.|
    |속성|**myLoadBalancer** 를 입력합니다.|
    |지역|**미국 동부** 를 선택합니다.|
    |Type|**내부** 를 선택합니다.|
    |SKU|**표준** 을 선택합니다.|
    |가상 네트워크|가상 네트워크를 선택합니다.|
    |서브넷|이전 단계에서 만든 **fe-subnet** 을 선택합니다.|
    |IP 주소 할당|**동적** 을 선택합니다.|
    |가용성 영역|**영역 중복** 을 선택합니다.|

3. 나머지 설정에는 기본값을 적용한 다음, **검토 + 만들기** 를 선택합니다.
4. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-managed-virtual-network/create-load-balancer.png" alt-text="표준 부하 분산 장치를 만드는 단계를 보여 주는 스크린샷.":::

## <a name="create-load-balancer-resources"></a>부하 분산 장치 리소스 만들기

### <a name="create-a-backend-pool"></a>백 엔드 풀 만들기

백 엔드 주소 풀에는 부하 분산 장치에 연결된 가상(NIC)의 IP 주소가 포함됩니다.

인터넷 트래픽의 부하를 분산하기 위한 가상 머신을 포함할 백 엔드 주소 풀 **myBackendPool** 을 만듭니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myLoadBalancer** 를 선택합니다.
2. **설정** 에서 **백 엔드 풀** 을 선택한 다음, **추가** 를 선택합니다.
3. **백 엔드 풀 추가** 페이지에서 이름에 백 엔드 풀의 이름인 **myBackEndPool** 을 입력한 후 **추가** 를 선택합니다.

### <a name="create-a-health-probe"></a>상태 프로브 만들기

부하 분산 장치는 상태 프로브를 사용하여 앱의 상태를 모니터링합니다.

상태 프로브는 상태 검사의 응답에 따라 부하 분산 장치에서 VM을 추가하거나 제거합니다.

VM 상태를 모니터링할 **myHealthProbe** 라는 상태 프로브를 만듭니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myLoadBalancer** 를 선택합니다.
2. **설정** 에서 **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.

    | 설정 | 값 |
    |:--- |:--- |
    |Name|**myHealthProbe** 를 입력합니다.|
    |프로토콜|**TCP** 를 선택합니다.|
    |포트|22를 입력합니다.|
    |간격|프로브 시도 **간격**(초)으로 **15** 를 입력합니다.|
    |비정상 임계값|**비정상 임계값** 또는 VM이 비정상 상태로 간주되는 데 필요한 연속 프로브 오류 횟수로 **2** 를 선택합니다.|

3. 나머지는 기본값으로 두고 **확인** 을 선택합니다.

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다. 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 받는 백 엔드 IP 풀을 정의합니다. 원본 및 대상 포트는 규칙에 정의됩니다.

이 섹션에서 만드는 부하 분산 장치 규칙은 다음과 같습니다.

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myLoadBalancer** 를 선택합니다.
2. **설정** 아래에서 **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
3. 다음 값을 사용하여 부하 분산 규칙을 구성합니다.

    | 설정 | 값 |
    |:--- |:--- |
    |Name|**myRule** 을 입력합니다.|
    |IP 버전|**IPv4** 를 선택합니다.|
    |프런트 엔드 IP 주소|**LoadBalancerFrontEnd** 를 선택합니다.|
    |프로토콜|**TCP** 를 선택합니다.|
    |포트|**1433** 을 입력합니다.|
    |백 엔드 포트|**1433** 을 입력합니다.|
    |백 엔드 풀|**myBackendPool** 을 선택합니다.|
    |상태 프로브|**myHealthProbe** 를 선택합니다.|
    |유휴 제한 시간(분)|슬라이더를 **15분** 으로 이동합니다.|
    |TCP 재설정|**사용 안 함** 을 선택합니다.|

4. 나머지는 기본값으로 둔 다음, **확인** 을 선택합니다.

## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기

이 섹션에서는 표준 부하 분산 장치 뒤에 Private Link 서비스를 만듭니다.

1. Azure Portal의 왼쪽 상단에서 **리소스 만들기** 를 선택합니다.
2. **Marketplace 검색** 상자에서 **Private Link** 를 검색합니다.
3. **만들기** 를 선택합니다.
4. **Private Link 센터** 의 **개요** 에서 파란색 **Private Link 서비스 만들기** 단추를 선택합니다.
5. **프라이빗 링크 서비스 만들기** 의 **기본** 탭에서 다음 정보를 선택합니다.

    |설정 |값 |
    |---------|--------|
    |**프로젝트 세부 정보**||
    |Subscription |구독을 선택합니다.|
    |리소스 그룹 |리소스 그룹을 선택합니다.|
    |**인스턴스 세부 정보**||
    |속성  |**myPrivateLinkService** 를 입력합니다.|
    |지역  |**미국 동부** 를 선택합니다.|

6. **아웃바운드 설정** 탭을 선택하거나 페이지 하단에서 **다음: 아웃바운드 설정** 을 선택합니다.
7. **아웃바운드 설정** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    |:--- |:--- |
    |부하 분산 장치|**myLoadBalancer** 를 선택합니다.|
    |부하 분산 장치 프런트 엔드 IP 주소|**LoadBalancerFrontEnd** 를 선택합니다.|
    |원본 NAT 서브넷|**pls-subnet** 을 선택합니다.|
    |TCP 프록시 V2 사용|**아니요**(기본값)를 그대로 둡니다.|
    |**개인 IP 주소 설정**||
    |기본 설정을 그대로 둡니다.||

8. **액세스 보안** 탭을 선택하거나 페이지 하단에서 **다음: 액세스 보안** 을 선택합니다.
9. **액세스 보안** 탭에서 기본값인 **역할 기반 액세스 제어만** 을 그대로 둡니다.
10. **태그** 탭을 선택하거나 페이지 하단에서 **다음: 태그** 를 선택합니다.
11. **검토 + 만들기** 탭을 선택하거나 페이지 하단에서 **다음: 검토 + 만들기** 를 선택합니다.
12. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.


## <a name="create-backend-servers"></a>백 엔드 서버 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기 > 컴퓨팅 > 가상 머신** 을 차례로 선택합니다.
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    |설정 |값|
    |---------|--------|
    |**프로젝트 세부 정보**||
    |Subscription |Azure 구독을 선택합니다.|
    |리소스 그룹 |리소스 그룹을 선택합니다.|
    |**인스턴스 세부 정보**||
    |가상 머신 이름  |**myVM1** 을 입력합니다.|
    |지역  |**미국 동부** 를 선택합니다.|
    |가용성 옵션  |**가용성 영역** 을 선택합니다.|
    |가용성 영역  |**1** 을 선택합니다.| 
    |이미지  |**Ubuntu Server 18.04LTS – Gen1** 을 선택합니다.| 
    |Azure Spot 인스턴스  |**아니오** 를 선택합니다.| 
    |크기   |VM 크기를 선택하거나 기본 설정을 사용합니다.| 
    |**관리자 계정**||
    |사용자 이름 |사용자 이름을 입력합니다.|
    |SSH 공개 키 원본  |새 키 쌍을 생성합니다.|
    |키 쌍 이름  |mySSHKey.|    
    |**인바운드 포트 규칙**||
    |공용 인바운드 포트 |없음|   

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 |값|
    |---------|--------|
    |**네트워크 인터페이스**||
    |가상 네트워크 |가상 네트워크를 선택합니다.|
    |서브넷 |**be-subnet**.|
    |공용 IP |**없음** 을 선택합니다.|
    |NIC 네트워크 보안 그룹 추가 |**없음** 을 선택합니다.|
    |**부하 분산**.||
    |기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치하시겠습니까?|**예** 를 선택합니다.|
    |**부하 분산 설정**||
    |부하 분산 옵션 |**Azure 부하 분산** 을 선택합니다.|
    |부하 분산 장치 선택 |**myLoadBalancer** 를 선택합니다.|
    |백 엔드 풀 선택 |**myBackendPool** 을 선택합니다.|    

5. **검토 + 만들기** 를 선택합니다.
6. 설정을 검토한 다음, **만들기** 를 선택합니다.
7. 1~6단계를 반복하여 HA용 백 엔드 서버 VM을 1개 이상 사용할 수 있습니다.

## <a name="creating-forwarding-rule-to-endpoint"></a>엔드포인트에 전달 규칙을 만드는 중

1. 로그인하고 [ip_fwd.sh](https://github.com/sajitsasi/az-ip-fwd/blob/main/ip_fwd.sh) 스크립트를 백 엔드 서버 VM에 복사합니다. 
2. 다음 옵션을 사용하여 스크립트를 실행합니다.<br/>
    **sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433**<br/>
    <FQDN/IP>는 대상 SQL Server IP입니다.<br/>
    
    > [!Note] 
    > Azure DNS 영역에 레코드를 추가하지 않으면 온-프레미스 SQL Server에 대해 FQDN이 작동하지 않습니다.
    
3. 아래 명령을 실행하고 백 엔드 서버 VM에서 iptable을 확인합니다. iptable에서 대상 IP를 통해 하나의 레코드를 볼 수 있습니다.<br/>
    **sudo iptables -t nat -v -L PREROUTING -n --line-number**

    :::image type="content" source="./media/tutorial-managed-virtual-network/command-record-1.png" alt-text="명령 레코드를 보여 주는 스크린샷.":::

    >[!Note]
    > SQL Server 또는 데이터 원본이 둘 이상 있는 경우 여러 개의 부하 분산 장치 규칙과 다른 포트를 사용하는 IP 테이블 레코드를 정의해야 합니다. 그렇지 않으면 충돌이 발생합니다. 예제:<br/>
    >
    >|                  |부하 분산 장치 규칙의 포트|부하 분산 규칙의 백 엔드 포트|백 엔드 서버 VM에서 실행되는 명령|
    >|------------------|---------|--------|---------|
    >|**SQL Server 1**|1433 |1433 |sudo ./ip_fwd.sh -i eth0 -f 1433 -a <FQDN/IP> -b 1433|
    >|**SQL Server 2**|1434 |1434 |sudo ./ip_fwd.sh -i eth0 -f 1434 -a <FQDN/IP> -b 1433|
    
## <a name="create-a-private-endpoint-to-private-link-service"></a>Private Link 서비스에 대한 프라이빗 엔드포인트 만들기

1. 왼쪽 메뉴에서 모든 서비스를 선택하고 모든 리소스를 선택한 다음, 리소스 목록에서 데이터 팩터리를 선택합니다.
2. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.
3. **관리** 탭으로 이동한 다음 **관리형 프라이빗 엔드포인트** 섹션으로 이동합니다.
4. **관리형 프라이빗 엔드포인트** 에서 **+ 새로 만들기** 를 선택합니다.
5. 목록에서 **Private Link 서비스** 타일, **계속** 을 차례로 선택합니다.
6. 프라이빗 엔드포인트의 이름을 입력하고 프라이빗 링크 서비스 목록에서 **myPrivateLinkService** 를 선택합니다.
7. 대상 온-프레미스 SQL Server의 FQDN을 추가합니다.

    :::image type="content" source="./media/tutorial-managed-virtual-network/private-endpoint-6.png" alt-text="프라이빗 엔드포인트 설정을 보여 주는 스크린샷.":::

8. 프라이빗 엔드포인트 만들기

## <a name="create-a-linked-service-and-test-the-connection"></a>연결된 서비스 만들기 및 연결 테스트

1. **관리** 탭으로 이동한 다음 **관리형 프라이빗 엔드포인트** 섹션으로 이동합니다.
2. **연결된 서비스** 에서 + **새로 만들기** 를 선택합니다.
3. 목록에서 **SQL Server** 타일, **계속** 을 차례로 선택합니다.    

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-1.png" alt-text="연결된 서비스 만들기 페이지를 보여 주는 스크린샷.":::   

4. **대화형 작성** 을 사용하도록 설정합니다.

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-2.png" alt-text="대화형 작성을 사용하도록 설정하는 방법을 보여 주는 스크린샷.":::

5. 온-프레미스 SQL Server의 **FQDN**, **사용자 이름** 및 **암호** 를 입력합니다.
6. 다음으로 **연결 테스트** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-managed-virtual-network/linked-service-3.png" alt-text="SQL Server가 연결된 서비스 만들기 페이지를 보여 주는 스크린샷.":::

## <a name="troubleshooting"></a>문제 해결

백 엔드 서버 VM으로 이동하여 SQL Server가 작동하는 텔넷을 확인합니다(**텔넷 **<** FQDN**>** 1433**).

## <a name="next-steps"></a>다음 단계

프라이빗 엔드포인트를 사용하여 Data Factory Managed VNet에서 Microsoft Azure SQL Managed Instance로 액세스하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Data Factory Managed VNet에서 SQL Managed Instance에 액세스](tutorial-managed-virtual-network-sql-managed-instance.md)
