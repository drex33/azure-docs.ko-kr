---
title: Azure Firewall을 사용하여 프라이빗 엔드포인트로 향하는 트래픽 검사
titleSuffix: Azure Private Link
description: Azure Firewall을 사용하여 프라이빗 엔드포인트로 향하는 트래픽의 검사 방법을 알아봅니다.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 0e252a1822d36bf1d8aecbac7f38a0d315d38351
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371842"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Azure Firewall을 사용하여 프라이빗 엔드포인트로 향하는 트래픽 검사

> [!NOTE]
> 보안 가상 허브를 사용하여 Azure Virtual WAN에서 프라이빗 엔드포인트에 대한 트래픽을 보호하려면 [Azure Virtual WAN의 프라이빗 엔드포인트로 향하는 트래픽 보호](../firewall-manager/private-link-inspection-secure-virtual-hub.md)를 참조하세요.

Azure 프라이빗 엔드포인트는 Azure Private Link를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트를 사용하면 가상 네트워크에 배포된 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있습니다.

프라이빗 엔드포인트를 사용하면 리소스가 가상 네트워크에 배포된 프라이빗 링크 서비스에 액세스할 수 있습니다. 가상 네트워크 피어링 및 온-프레미스 네트워크 연결을 통해 프라이빗 엔드포인트에 액세스하면 연결이 확장됩니다.

클라이언트에서 프라이빗 엔드포인트를 통해 노출된 서비스로 이동하는 트래픽을 검사하거나 차단해야 할 수 있습니다. [Azure Firewall](../firewall/overview.md) 또는 타사 네트워크 가상 어플라이언스를 사용하여 이 검사를 완료합니다.

다음과 같은 제한 사항이 적용됩니다.

* 프라이빗 엔드포인트에서 들어오는 트래픽은 NSG(네트워크 보안 그룹)를 우회함
* 프라이빗 엔드포인트에서 들어오는 트래픽은 UDR(사용자 정의 경로)을 바이패스합니다. 사용자 정의 경로를 사용하여 프라이빗 엔드포인트로 향하는 트래픽을 재정의할 수 있습니다.
* 단일 경로 테이블을 서브넷에 연결할 수 있습니다.
* 경로 테이블은 최대 400개의 경로를 지원합니다.

Azure Firewall은 다음 중 하나를 사용하여 트래픽을 필터링합니다.

* TCP 및 UDP 프로토콜에 관한 [네트워크 규칙의 FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP, HTTPS 및 MSSQL에 관한 [애플리케이션 규칙의 FQDN](../firewall/features.md#application-fqdn-filtering-rules). 

> [!IMPORTANT] 
> 흐름 대칭을 유지 관리하기 위해 프라이빗 엔드포인트로 향하는 트래픽을 검사할 때 네트워크 규칙에 앞서 애플리케이션 규칙을 사용하는 것이 좋습니다. 네트워크 규칙이 사용되거나 Azure Firewall 대신 NVA가 사용되는 경우 프라이빗 엔드포인트로 향하는 트래픽에 맞게 SNAT를 구성해야 합니다.

> [!NOTE]
> SQL FQDN 필터링은 [프록시 모드](../azure-sql/database/connectivity-architecture.md#connection-policy)에서만 지원됩니다(포트 1433). **프록시** 모드는 *리디렉션* 에 비해 더 많은 대기 시간이 발생할 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 네트워크 규칙의 FQDN을 사용하여 액세스를 필터링할 수 있습니다.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>시나리오 1: 허브 및 스포크 아키텍처 - 프라이빗 엔드포인트의 전용 가상 네트워크

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="프라이빗 엔드포인트의 전용 가상 네트워크" border="true":::

이 시나리오는 프라이빗 엔드포인트를 사용하여 여러 Azure 서비스에 비공개로 연결하는 확장 가능성이 가장 큰 아키텍처입니다. 프라이빗 엔드포인트가 배포되는 네트워크 주소 공간을 가리키는 경로가 생성됩니다. 이처럼 구성하면 관리 오버헤드가 감소하고 400개의 경로 제한을 초과하는 것을 방지합니다.

가상 네트워크가 피어링되는 경우 클라이언트 가상 네트워크에서 허브 가상 네트워크의 Azure Firewall에 연결하면 요금이 발생합니다. 허브 가상 네트워크의 Azure Firewall에서 피어링된 가상 네트워크의 프라이빗 엔드포인트로 연결하는 것은 요금이 발생하지 않습니다.

피어링된 가상 네트워크와의 연결과 관련된 요금에 관한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/private-link/) 페이지의 FAQ 섹션을 참조하세요.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>시나리오 2: 허브 및 스포크 아키텍처 - 프라이빗 엔드포인트와 가상 머신의 공유 가상 네트워크

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="같은 가상 네트워크의 프라이빗 엔드포인트 및 가상 머신" border="true":::

이 시나리오는 다음과 같은 경우 구현됩니다.

* 프라이빗 엔드포인트의 전용 가상 네트워크가 있을 수 없는 경우

* 프라이빗 엔드포인트를 사용하여 가상 네트워크에서 몇 개의 서비스만 노출된 경우

가상 머신에는 각각의 프라이빗 엔드포인트를 가리키는 /32 시스템 경로가 있습니다. 프라이빗 엔드포인트당 하나의 경로가 Azure Firewall을 통해 트래픽을 라우팅하도록 구성됩니다. 

서비스가 가상 네트워크에 노출되면 경로 테이블을 유지 관리하는 관리 오버헤드가 증가합니다. 경로 제한에 도달할 가능성도 커집니다.

전체 아키텍처에 따라 400개의 경로 제한까지 실행할 수 있습니다. 가능하면 항상 시나리오 1을 사용하는 것이 좋습니다.

가상 네트워크가 피어링되는 경우 클라이언트 가상 네트워크에서 허브 가상 네트워크의 Azure Firewall에 연결하면 요금이 발생합니다. 허브 가상 네트워크의 Azure Firewall에서 피어링된 가상 네트워크의 프라이빗 엔드포인트로 연결하는 것은 요금이 발생하지 않습니다.

피어링된 가상 네트워크와의 연결과 관련된 요금에 관한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/private-link/) 페이지의 FAQ 섹션을 참조하세요.

## <a name="scenario-3-single-virtual-network"></a>시나리오 3: 단일 가상 네트워크

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="단일 가상 네트워크" border="true":::

허브 및 스포크 아키텍처로 마이그레이션할 수 없는 경우 이 패턴을 사용합니다. 시나리오 2에서와 같은 고려 사항이 적용됩니다. 이 시나리오에서는 가상 네트워크 피어링 요금이 적용되지 않습니다.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>시나리오 4: 프라이빗 엔드포인트에 대한 온-프레미스 트래픽

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="프라이빗 엔드포인트에 대한 온-프레미스 트래픽" border="true":::

이 아키텍처는 다음 중 하나를 사용하여 온-프레미스 네트워크와의 연결을 구성한 경우 구현할 수 있습니다. 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [사이트 간 VPN](../vpn-gateway/tutorial-site-to-site-portal.md) 

프라이빗 엔드포인트를 통해 노출된 서비스로 향하는 클라이언트 트래픽은 보안 어플라이언스를 통해 라우팅해야 하는 것이 보안 요구 사항인 경우, 이 시나리오를 배포합니다.

위의 시나리오 2에서와 같은 고려 사항이 적용됩니다. 이 시나리오에서는 가상 네트워크 피어링 요금이 발생하지 않습니다. 온-프레미스 워크로드에서 프라이빗 엔드포인트에 액세스할 수 있도록 DNS 서버를 구성하는 방법에 관한 자세한 내용은 [DNS 전달자를 사용하는 온-프레미스 워크로드](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독
* Log Analytics 작업 영역.  

구독에 작업 영역이 없는 경우 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.


## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기

이 섹션에서는 프라이빗 링크 리소스에 액세스하는 데 사용되는 VM을 호스트하는 가상 네트워크와 서브넷을 만듭니다. 예제 서비스로 Azure SQL 데이터베이스를 사용합니다.

## <a name="virtual-networks-and-parameters"></a>가상 네트워크 및 매개 변수

세 개의 가상 네트워크와 대응하는 서브넷을 만들어 다음을 수행합니다.

* VM과 프라이빗 엔드포인트 사이의 통신을 제한하는 데 사용하는 Azure Firewall을 포함합니다.
* 프라이빗 링크 리소스에 액세스하는 데 사용되는 VM을 호스트합니다.
* 프라이빗 엔드포인트를 호스트합니다.

단계의 다음 매개 변수를 아래 정보로 바꿉니다.

### <a name="azure-firewall-network"></a>Azure Firewall 네트워크

| 매개 변수                   | 값                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | 미국 중남부      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>가상 머신 네트워크

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | 미국 중남부      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>프라이빗 엔드포인트 네트워크

| 매개 변수                   | 값                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | 미국 중남부      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. 1~9단계를 반복하여 가상 머신 및 프라이빗 엔드포인트 리소스를 호스트하는 가상 네트워크를 만듭니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 선택합니다.

2. **가상 머신 만들기 - 기본 사항** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. 이전 섹션에서 이 리소스 그룹을 만들었습니다.  |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **(미국) 미국 중남부** 를 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다** 를 그대로 둡니다. |
    | 이미지 | **Ubuntu Server 18.04 LTS - Gen1** 을 선택합니다. |
    | 크기 | **Standard_B2s** 를 선택합니다. |
    | **관리자 계정** |  |
    | 인증 유형 | **암호** 를 선택합니다. |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/linux/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
    |||

3. 완료되면 **다음: 디스크** 를 선택합니다.

4. **가상 머신 만들기 - 디스크** 에서 기본값을 그대로 두고 **다음: 네트워킹** 을 선택합니다.

5. **가상 머신 만들기 - 네트워킹** 에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | **myVMVNet** 를 선택합니다.  |
    | 서브넷 | **VMSubnet(10.1.0.0/24)** 를 선택합니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip** 를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용** 을 선택합니다. |
    | 인바운드 포트 선택 | **SSH** 를 선택합니다.|
    ||

6. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

7. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="deploy-the-firewall"></a>방화벽 배포

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에 **방화벽** 을 입력하고 **Enter** 키를 누릅니다.

3. **방화벽** 을 선택하고 **만들기** 를 선택합니다.

4. **방화벽 만들기** 페이지에서 다음 표를 사용하여 방화벽을 구성합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다.  |
    | **인스턴스 세부 정보** |  |
    | 이름 | **myAzureFirewall** 을 입력합니다. |
    | 지역 | **미국 중남부** 를 선택합니다. |
    | 가용성 영역 | 기본값인 **없음** 을 그대로 둡니다. |
    | 가상 네트워크 선택    |    **기존 항목 사용** 을 선택합니다.    |
    | 가상 네트워크    |    **myAzFwVNet** 를 선택합니다.    |
    | 공용 IP 주소    |    **새로 추가** 를 선택하고 이름에 **myFirewall-ip** 를 입력합니다.    |
    | 강제 터널링    | **사용하지 않음**(기본값)을 그대로 둡니다.    |
    |||
5. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

6. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

## <a name="enable-firewall-logs"></a>방화벽 로그 사용

이 섹션에서는 방화벽에서 로그를 사용하도록 설정합니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다.

2. 리소스 목록에서 **myAzureFirewall** 방화벽을 선택합니다.

3. 방화벽 설정의 **모니터링** 에서 **진단 설정** 을 선택

4. 진단 설정에서 **+ 진단 설정 추가** 를 선택합니다.

5. **진단 설정** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 진단 설정 이름 | **myDiagSetting** 을 입력합니다. |
    | 범주 세부 정보 | |
    | log | **AzureFirewallApplicationRule** 과 **AzureFirewallNetworkRule** 을 선택합니다. |
    | 대상 세부 정보 | **Log Analytics에 보내기** 를 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | Log Analytics 작업 영역 | Log Analytics 작업 영역을 선택합니다. |

6. **저장** 을 선택합니다.

## <a name="create-azure-sql-database"></a>Azure SQL 데이터베이스 만들기

이 섹션에서는 프라이빗 SQL Database를 만듭니다.

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **SQL Database** 를 선택합니다.

2. **SQL Database 만들기 - 기본 사항** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. 이전 섹션에서 이 리소스 그룹을 만들었습니다.|
    | **데이터베이스 세부 정보** |  |
    | 데이터베이스 이름  | **mydatabase** 를 입력합니다.  |
    | 서버 | **새로 만들기** 를 선택하고 아래 정보를 입력합니다.    |
    | 서버 이름 | **mydbserver** 를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 입력합니다.   |
    | 서버 관리자 로그인 | 선택한 이름을 입력합니다. |
    | 암호    |    선택한 암호를 입력합니다.    |
    | 암호 확인 | 암호를 다시 입력합니다.    |
    | Location    | **(미국) 미국 중남부** 를 선택합니다.    |
    | SQL 탄력적 풀 사용 여부    | 기본값인 **아니요** 를 그대로 둡니다. |
    | 컴퓨팅 + 스토리지 | **범용, Gen5, vCore 2개, 32GB 스토리지** 의 기본값을 그대로 둡니다. |
    |||

3. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

4. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

## <a name="create-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 이전 섹션의 Azure SQL 데이터베이스에 사용할 프라이빗 엔드포인트를 만듭니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다.

2. 서비스 목록에서 Azure SQL Server **mydbserver** 를 선택합니다.  다른 서버 이름을 사용한 경우 해당 이름을 선택합니다.

3. 서버 설정의 **보안** 아래에서 **프라이빗 엔드포인트 연결** 을 선택합니다.

4. **+ 프라이빗 엔드포인트** 를 선택합니다.

5. **프라이빗 엔드포인트 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** | |
    | 이름 | **SQLPrivateEndpoint** 를 입력합니다. |
    | 지역 | **(미국) 미국 중남부** 를 선택합니다. |

6. **리소스** 탭을 선택하거나 페이지 아래쪽에서 **다음: 리소스** 를 선택합니다.

7. **리소스** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 연결 방법 | **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.Sql/servers** 를 선택합니다. |
    | 리소스 | **mydbserver** 또는 이전 단계에서 만든 서버의 이름을 선택합니다.
    | 대상 하위 리소스 | **sqlServer** 를 선택합니다. |

8. **구성** 탭을 선택하거나 페이지 아래쪽에서 **다음: 구성** 을 선택합니다.

9. **구성** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워킹** | |
    | 가상 네트워크 | **myPEVnet** 를 선택합니다. |
    | 서브넷 | **PrivateEndpointSubnet** 를 선택합니다. |
    | **프라이빗 DNS 통합** | |
    | 프라이빗 DNS 영역과 통합 | **예** 를 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | 프라이빗 DNS 영역 | **privatelink.database.windows.net**(기본값)를 그대로 둡니다. |

10. **검토 + 만들기** 탭을 선택하거나 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.

11. **만들기** 를 선택합니다.

12. 엔드포인트를 만든 다음 **보안** 에서 **방화벽 및 가상 네트워크** 를 선택합니다.

13. **방화벽 및 가상 네트워크** 에서 **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 옆에 있는 **예** 를 선택합니다.

14. **저장** 을 선택합니다.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>가상 네트워크 피어링을 사용하여 가상 네트워크 연결

이 섹션에서는 피어링을 사용하여 **myVMVNet** 와 **myPEVNet** 를 **myAzFwVNet** 가상 네트워크에 연결합니다. **myVMVNet** 과 **myPEVNet** 간에 직접 연결되지 않습니다.

1. 포털의 검색 창에 **myAzFwVNet** 를 입력합니다.

2. **설정** 메뉴에서 **피어링** 을 선택하고 **+ 추가** 를 선택합니다.

3. **피어링 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | myAzFwVNet에서 원격 가상 네트워크로의 피어링 이름 | **myAzFwVNet-to-myVMVNet** 를 입력합니다. |
    | **피어 세부 정보** |  |
    | 가상 네트워크 배포 모델  | **Resource Manager**(기본값)를 그대로 둡니다.  |
    | 리소스 ID를 알고 있음 | 확인되지 않은 상태로 둡니다.    |
    | Subscription | 구독을 선택합니다.    |
    | 가상 네트워크 | **myVMVNet** 를 선택합니다. |
    | 원격 가상 네트워크에서 myAzFwVNet으로의 피어링 이름    |    **myVMVNet-to-myAzFwVNet** 를 입력합니다.    |
    | **Configuration** | |
    | **가상 네트워크 액세스 설정 구성** | |
    | myAzFwVNet에서 원격 가상 네트워크로의 가상 네트워크 액세스 허용 | 기본값인 **사용** 을 그대로 둡니다.    |
    | 원격 가상 네트워크에서 myAzFwVNet으로의 가상 네트워크 액세스 허용    | 기본값인 **사용** 을 그대로 둡니다.    |
    | **전달된 트래픽 설정 구성** | |
    | 원격 가상 네트워크에서 myAzFwVNet으로 전달된 트래픽 허용    | **사용** 을 선택합니다. |
    | myAzFwVNet에서 원격 가상 네트워크로 전달된 트래픽 허용 | **사용** 을 선택합니다. |
    | **게이트웨이 전송 설정 구성** | |
    | 게이트웨이 전송 허용 | 확인되지 않은 상태로 둡니다. |
    |||

4. **확인** 을 선택합니다.

5. **+추가** 를 선택합니다.

6. **피어링 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | myAzFwVNet에서 원격 가상 네트워크로의 피어링 이름 | **myAzFwVNet-to-myPEVNet** 를 입력합니다. |
    | **피어 세부 정보** |  |
    | 가상 네트워크 배포 모델  | **Resource Manager**(기본값)를 그대로 둡니다.  |
    | 리소스 ID를 알고 있음 | 확인되지 않은 상태로 둡니다.    |
    | Subscription | 구독을 선택합니다.    |
    | 가상 네트워크 | **myPEVNet** 를 선택합니다. |
    | 원격 가상 네트워크에서 myAzFwVNet으로의 피어링 이름    |    **myPEVNet-to-myAzFwVNet** 를 입력합니다.    |
    | **Configuration** | |
    | **가상 네트워크 액세스 설정 구성** | |
    | myAzFwVNet에서 원격 가상 네트워크로의 가상 네트워크 액세스 허용 | 기본값인 **사용** 을 그대로 둡니다.    |
    | 원격 가상 네트워크에서 myAzFwVNet으로의 가상 네트워크 액세스 허용    | 기본값인 **사용** 을 그대로 둡니다.    |
    | **전달된 트래픽 설정 구성** | |
    | 원격 가상 네트워크에서 myAzFwVNet으로 전달된 트래픽 허용    | **사용** 을 선택합니다. |
    | myAzFwVNet에서 원격 가상 네트워크로 전달된 트래픽 허용 | **사용** 을 선택합니다. |
    | **게이트웨이 전송 설정 구성** | |
    | 게이트웨이 전송 허용 | 확인되지 않은 상태로 둡니다. |

7. **확인** 을 선택합니다.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>프라이빗 DNS 영역에 가상 네트워크 연결

이 섹션에서는 가상 네트워크 **myVMVNet** 와 **myAzFwVNet** 를 **privatelink.database.windows.net** 프라이빗 DNS 영역에 연결합니다. 이 영역은 프라이빗 엔드포인트를 만들 때 생성되었습니다. 

링크는 VM과 방화벽이 데이터베이스의 FQDN을 프라이빗 엔드포인트 주소로 확인하는 데 필요합니다. 프라이빗 엔드포인트를 만들 때 **myPEVNet** 가상 네트워크가 자동으로 연결됩니다.

>[!NOTE]
>VM과 방화벽 가상 네트워크를 프라이빗 DNS 영역에 연결하지 않아도 VM과 방화벽 둘 다 여전히 SQL Server FQDN을 확인할 수 있습니다. 공용 IP 주소로 확인됩니다.

1. 포털의 검색 창에서 **privatelink.database** 를 입력합니다.

2. 검색 결과에서 **privatelink.database.windows.net** 를 선택합니다.

3. **설정** 에서 **가상 네트워크 링크** 를 선택합니다.

4. **+ 추가** 를 선택합니다.

5. **가상 네트워크 링크 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 링크 이름 | **Link-to-myVMVNet** 를 입력합니다. |
    | **가상 네트워크 세부 정보** |  |
    | 가상 네트워크의 리소스 ID를 알고 있음  | 확인되지 않은 상태로 둡니다.  |
    | Subscription | 구독을 선택합니다.    |
    | 가상 네트워크 | **myVMVNet** 를 선택합니다. |
    | **CONFIGURATION** | |
    | 자동 등록 사용 | 확인되지 않은 상태로 둡니다.    |


6. **확인** 을 선택합니다.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Azure Firewall에서 SQL FQDN을 사용하여 애플리케이션 규칙 구성

이 섹션에서는 **myVM** 과 SQL Server **mydbserver.database.windows.net** 의 프라이빗 엔드포인트 간 통신을 허용하는 애플리케이션 규칙을 구성합니다. 

이 규칙을 사용하면 이전 단계에서 만든 방화벽을 통해 통신할 수 있습니다.

1. 포털의 검색 창에 **myAzureFirewall** 을 입력합니다.

2. 검색 결과에서 **myAzureFirewall** 을 선택합니다.

3. **myAzureFirewall** 개요의 **설정** 에서 **규칙** 을 선택합니다.

4. **애플리케이션 규칙 컬렉션** 탭을 선택합니다.

5. **+ 애플리케이션 규칙 컬렉션 추가** 를 선택합니다.

6. **애플리케이션 규칙 컬렉션 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | **SQLPrivateEndpoint** 를 입력합니다. |
    | 우선 순위 | **100** 을 입력합니다. |
    | 작업 | **Allow** 를 입력합니다. |
    | **규칙.** |  |
    | **FQDN 태그** | |
    | 이름  | 비워 둡니다.  |
    | 소스 형식 | 기본 **IP 주소** 를 그대로 둡니다.    |
    | 원본 | 비워 둡니다. |
    | FQDN 태그 | 기본값 **0을 선택된 상태** 로 둡니다. |
    | **대상 FQDN** | |
    | 이름 | **SQLPrivateEndpoint** 를 입력합니다.    |
    | 소스 형식 | 기본 **IP 주소** 를 그대로 둡니다. |
    | 원본 | **10.1.0.0/16** 을 입력합니다. |
    | 프로토콜: 포트 | **mssql:1433** 을 입력합니다. |
    | 대상 FQDN | **mydbserver.database.windows.net** 를 입력합니다. |
    |||

7. **추가** 를 선택합니다.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Azure Firewall을 통해 가상 머신과 프라이빗 엔드포인트 간 트래픽 라우팅

**myVMVNet** 와 **myPEVNet** 가상 네트워크 간에 직접 가상 네트워크 피어링을 만들지 않았습니다. **myVM** 가상 머신에는 생성된 프라이빗 엔드포인트에 대한 경로가 없습니다. 

이 섹션에서는 사용자 지정 경로를 사용하여 경로 테이블을 만듭니다. 

경로는 Azure Firewall을 통해 **myVM** 서브넷에서 **myPEVNet** 가상 네트워크의 주소 공간으로 트래픽을 보냅니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

2. 검색 상자에 **경로 테이블** 을 입력하고 **Enter** 를 누릅니다.

3. **경로 테이블** 을 선택하고 **만들기** 를 선택합니다.

4. **경로 테이블 만들기** 페이지에서 다음 테이블을 사용하여 경로 테이블을 구성합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다.  |
    | **인스턴스 세부 정보** |  |
    | 지역 | **미국 중남부** 를 선택합니다. |
    | 이름 | **VMsubnet-to-AzureFirewall** 을 입력합니다. |
    | 게이트웨이 경로 전파 | **아니오** 를 선택합니다. |

5. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

6. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

7. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

8. **설정** 에서 **경로** 를 선택합니다.

9. **+추가** 를 선택합니다.

10. **경로 추가** 페이지에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 경로 이름 | **myVMsubnet-to-privateendpoint** 를 입력합니다. |
    | 주소 접두사 | **10.2.0.0/16** 을 입력합니다.  |
    | 다음 홉 유형 | **가상 어플라이언스** 를 선택합니다. |
    | 다음 홉 주소 | **10.0.0.4** 를 입력합니다. |

11. **확인** 을 선택합니다.

12. **설정** 에서 **서브넷** 을 선택합니다.

13. **+ 연결** 을 선택합니다.

14. **서브넷 연결** 페이지에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | **myVMVNet** 를 선택합니다. |
    | 서브넷 | **VMSubnet** 를 선택합니다.  |

15. **확인** 을 선택합니다.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>클라이언트 컴퓨터에서 가상 머신에 연결

다음과 같이 인터넷에서 **myVm** VM에 연결합니다.

1. 포털의 검색 창에 **myVm-ip** 를 입력합니다.

2. 검색 결과에서 **myVM-ip** 를 선택합니다.

3. **IP 주소** 에서 값을 복사하거나 기록해 둡니다.

4. Windows 10을 사용하는 경우 PowerShell을 사용하여 다음 명령을 실행합니다. 다른 Windows 클라이언트 버전의 경우 [Putty](https://www.putty.org/)와 같은 SSH 클라이언트를 사용합니다.

* VM을 만드는 동안 입력한 관리자 이름으로 **username** 을 바꿉니다.

* **IPaddress** 를 이전 단계의 IP 주소로 바꿉니다.

    ```bash
    ssh username@IPaddress
    ```

5. **myVm** 을 만들 때 정의한 암호 입력

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>가상 머신에서 비공개로 SQL Server에 액세스

이 섹션에서는 프라이빗 엔드포인트를 사용하여 SQL Database에 비공개로 연결합니다.

1. `nslookup mydbserver.database.windows.net`을 입력합니다.
    
    다음과 비슷한 메시지가 표시됩니다.

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. [SQL Server 명령줄 도구](/sql/linux/quickstart-install-connect-ubuntu#tools)를 설치합니다.

3. 다음 명령을 실행하여 SQL Server에 연결합니다. 이전 단계에서 SQL Server를 만들 때 정의한 서버 관리자와 암호를 사용합니다.

* SQL Server를 만드는 동안 입력한 관리자 이름으로 **\<ServerAdmin>** 을 바꿉니다.

* SQL Server를 만드는 동안 입력한 관리자 암호로 **\<YourPassword>** 를 바꿉니다.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. 로그인에 성공하면 SQL 명령 프롬프트가 표시됩니다. **exit** 를 입력하여 **sqlcmd** 도구를 종료합니다.

5. **exit** 를 입력하여 **myVM** 에 대한 연결을 닫습니다.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Azure Firewall 로그에서 트래픽의 유효성 검사

1. Azure Portal에서 **모든 리소스** 를 선택하고 Log Analytics 작업 영역을 선택합니다.

2. Log Analytics 작업 영역 페이지에서 **일반** 아래에 있는 **로그** 를 선택합니다.

3. 파란색 **시작** 단추를 선택합니다.

4. **예제 쿼리** 창의 **모든 쿼리** 에서 **방화벽** 을 선택합니다.

5. **애플리케이션 규칙 로그 데이터** 에서 **실행** 단추를 선택합니다.

6. 로그 쿼리 출력에서 **mydbserver.database.windows.net** 가 **FQDN** 에 나열되어 있으며 **SQLPrivateEndpoint** 가 **RuleCollection** 에 나열되어 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스를 다 사용했으면 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 **myResourceGroup** 을 입력하고 검색 결과에서 **myResourceGroup** 을 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. **리소스 그룹 이름 입력** 에 대해 **myResourceGroup** 을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Firewall을 사용하여 가상 머신과 프라이빗 엔드포인트 간의 트래픽을 제한하는 데 사용할 수 있는 다양한 시나리오를 살펴보았습니다. 

VM에 연결하고 프라이빗 링크를 사용하여 Azure Firewall을 통해 데이터베이스와 안전하게 통신했습니다.

프라이빗 엔드포인트에 대해 자세히 알아보려면 [Azure 프라이빗 엔드포인트란?](private-endpoint-overview.md)을 참조하세요.
