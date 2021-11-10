---
title: AG VNN 수신기에 대해 Load Balancer 구성
description: HADR(고가용성 및 재해 복구)을 위해 Azure VM에서 SQL Server를 사용하여 트래픽을 가용성 그룹의 VNN(가상 네트워크 이름) 수신기로 라우팅하도록 Azure Load Balancer를 구성하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 7c749744ef6dcad4ca8f233b1a85e07843ba0406
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158481"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>AG VNN 수신기에 대해 Load Balancer 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내에서 [여러 서브넷](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) 에 SQL Server vm을 만들어 AG (Always On availability) 그룹에 대 한 Azure Load Balancer 필요성을 제거 합니다.

Azure Virtual Machines에서 클러스터는 한 번에 하나의 클러스터 노드에 있어야 하는 IP 주소를 저장하는 부하 분산 장치를 사용합니다. 이 솔루션에서 부하 분산 장치는 SQL Server vm이 단일 서브넷에 있는 경우 AG (Always On 가용성 그룹)의 vnn (가상 네트워크 이름) 수신기에 대 한 IP 주소를 보유 합니다. 

이 문서에서는 Azure Load Balancer 서비스를 사용하여 부하 분산 장치를 구성하는 방법을 안내합니다. 부하 분산 장치는 HADR(고가용성 및 재해 복구)을 위해 Azure VM의 SQL Server를 사용하여 트래픽을 [AG(가용성 그룹) 수신기](availability-group-overview.md)로 라우팅합니다. 

SQL Server 2019 CU8 이상을 사용하는 고객을 위한 대체 연결 옵션의 경우 구성 간소화 및 장애 조치 개선을 위해 [DNN 수신기](availability-group-vnn-azure-load-balancer-configure.md)를 고려합니다.  



## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하기 전에 다음이 준비되어 있어야 합니다.

- Azure Load Balancer가 [가용성 그룹에 대한 연결 옵션](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)으로 적합하다고 결정했습니다.
- [가용성 그룹 수신기](availability-group-overview.md)를 구성했습니다.
- 최신 버전의 [PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)을 설치했습니다. 


## <a name="create-load-balancer"></a>부하 분산 장치 만들기

내부 부하 분산 장치 또는 외부 부하 분산 장치를 만들 수 있습니다. 내부 부하 분산 장치는 네트워크 내부에 있는 프라이빗 리소스에서만 액세스할 수 있습니다.  외부 부하 분산 장치는 공용에서 내부 리소스로 트래픽을 라우팅할 수 있습니다. 내부 부하 분산 장치를 구성하는 경우 부하 분산 규칙을 구성할 때 프런트 엔드 IP의 가용성 그룹 수신기 리소스와 동일한 IP 주소를 사용합니다. 외부 부하 분산 장치를 구성할 때는 수신기 IP 주소가 공용 IP 주소일 수 없으므로, 가용성 그룹 수신기와 동일한 IP 주소를 사용할 수 없습니다. 따라서 외부 부하 분산 장치를 사용하려면 다른 IP 주소와 충돌하지 않는 가용성 그룹과 동일한 서브넷의 IP 주소를 논리적으로 할당하고, 이 주소를 부하 분산 규칙의 프런트 엔드 IP 주소로 사용합니다. 

[Azure Portal](https://portal.azure.com)에서 부하 분산 장치를 만듭니다.

1. Azure Portal에서 가상 머신이 포함된 리소스 그룹으로 이동합니다.

1. **추가** 를 선택합니다. Azure Marketplace에서 **부하 분산 장치** 를 검색합니다. **부하 분산 장치** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 다음 값을 사용하여 부하 분산 장치를 설정합니다.

   - **구독**: Azure 구독.
   - **리소스 그룹**: 가상 머신이 포함된 리소스 그룹을 선택합니다.
   - **Name**: 부하 분산 장치를 식별하는 이름입니다.
   - **지역**: 가상 머신이 포함된 Azure 위치입니다.
   - **형식**: 공용 또는 비공개 중 하나입니다. 동일한 가상 네트워크 내에서 프라이빗 부하 분산 장치에 액세스할 수 있습니다. 대부분의 Azure 애플리케이션은 프라이빗 부하 분산 장치를 사용할 수 있습니다. 애플리케이션에 인터넷을 통해 직접 SQL Server에 대한 액세스가 필요한 경우 공개 부하 분산 장치를 사용합니다.
   - **SKU**: 표준입니다.
   - **가상 네트워크**: 가상 머신과 동일한 네트워크입니다.
   - **IP 주소 할당**: 정적입니다. 
   - **개인 IP 주소**: 클러스터형 네트워크 리소스에 할당한 IP 주소입니다.

   다음 이미지에서는 **부하 분산 장치 만들기** UI를 보여 줍니다.

   ![부하 분산 장치 설정](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>백 엔드 풀 구성

1. 가상 머신이 포함된 Azure 리소스 그룹으로 돌아가고 새 부하 분산 장치를 찾습니다. 리소스 그룹 보기를 새로 고쳐야 할 수도 있습니다. 부하 분산 장치를 선택합니다.

1. **백 엔드 풀** 을 선택한 다음 **추가** 를 선택합니다.

1. VM이 포함된 가용성 집합과 백 엔드 풀을 연결합니다.

1. **대상 네트워크 IP 구성** 에서 **가상 머신** 을 선택하고 클러스터 노드로 참여하는 가상 머신을 선택합니다. 가용성 그룹을 호스트하는 모든 가상 머신을 포함해야 합니다.

1. **확인** 을 선택하여 백엔드 풀을 만듭니다.

## <a name="configure-health-probe"></a>상태 프로브 구성

1. 부하 분산 장치 창에서 **상태 프로브** 를 선택합니다.

1. **추가** 를 선택합니다.

1. **상태 프로브 추가** 창에서 <span id="probe"></span> 다음의 상태 프로브 매개 변수를 설정합니다.

   - **Name**: 상태 프로브의 이름입니다.
   - **프로토콜**: TCP입니다.
   - **포트**: [VM을 준비할 때](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1) 상태 프로브용으로 방화벽에서 만든 포트입니다. 이 문서에서는 TCP 포트 `59999`를 사용합니다.
   - **간격**: 5초입니다.
   - **비정상 임계값**: 2번 연속 실패입니다.

1. **확인** 을 선택합니다.

## <a name="set-load-balancing-rules"></a>부하 분산 규칙 설정

부하 분산 장치에 대한 부하 분산 규칙을 설정합니다. 

# <a name="private-load-balancer"></a>[프라이빗 부하 분산 장치](#tab/ilb)

1. 부하 분산 장치 창에서 **부하 분산 규칙** 을 선택합니다.

1. **추가** 를 선택합니다.

1. 부하 분산 규칙 매개 변수를 설정합니다.

   - **이름**: 부하 분산 규칙의 이름입니다.
   - **프런트 엔드 IP 주소**: AG 수신기의 클러스터형 네트워크 리소스에 대한 IP 주소입니다.
   - **포트**: SQL Server TCP 포트입니다. 기본 인스턴스 포트는 1433입니다.
   - **백 엔드 포트**: **부동 IP(Direct Server Return)** 를 활성화할 때 **포트** 값과 동일한 포트를 사용합니다.
   - **백 엔드 풀**: 이전에 구성한 백 엔드 풀 이름입니다.
   - **상태 프로브**: 이전에 구성한 상태 프로브입니다.
   - **세션 지속성**: 없음
   - **유휴 제한 시간(분)** : 4.
   - **부동 IP(Direct Server Return)** : 사용

1. **확인** 을 선택합니다.

# <a name="public-load-balancer"></a>[공용 부하 분산 장치](#tab/elb)

1. 부하 분산 장치 창에서 **부하 분산 규칙** 을 선택합니다.

1. **추가** 를 선택합니다.

1. 부하 분산 규칙 매개 변수를 설정합니다.

   - **이름**: 부하 분산 규칙의 이름입니다.
   - **프런트 엔드 IP 주소**: 클라이언트가 퍼블릭 엔드포인트에 연결하는 데 사용하는 공용 IP 주소입니다. 
   - **포트**: SQL Server TCP 포트입니다. 기본 인스턴스 포트는 1433입니다.
   - **백 엔드 포트**: AG 수신기가 사용하는 것과 같은 포트입니다. 기본적으로 이 포트는 1433입니다. 
   - **백 엔드 풀**: 이전에 구성한 백 엔드 풀 이름입니다.
   - **상태 프로브**: 이전에 구성한 상태 프로브입니다.
   - **세션 지속성**: 없음
   - **유휴 제한 시간(분)** : 4.
   - **부동 IP(Direct Server Return)** : 사용하지 않도록 설정됩니다.

1. **확인** 을 선택합니다.

---

## <a name="configure-cluster-probe"></a>클러스터 프로브 구성

PowerShell에서 클러스터 프로브 포트 매개 변수를 설정합니다.

# <a name="private-load-balancer"></a>[프라이빗 부하 분산 장치](#tab/ilb)

클러스터 프로브 포트 매개 변수를 설정하려면 다음 스크립트의 변수를 사용자 환경의 값으로 업데이트합니다. 스크립트에서 꺾쇠 괄호(`<` 및 `>`)를 제거합니다.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<Availability group Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

다음 표는 업데이트해야 하는 값을 보여 줍니다.


|**값**|**설명**|
|---------|---------|
|`Cluster Network Name`| 네트워크의 Windows Server 장애 조치(failover) 클러스터 이름입니다. **장애 조치(failover) 클러스터 관리자** > **네트워크** 에서 네트워크를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.|
|`AG listener IP Address Resource Name`|AG 수신기의 IP 주소에 대한 리소스 이름입니다. **장애 조치(failover) 클러스터 관리자** > **역할** 의 **서버 이름** 에서 IP 주소 리소스를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.|
|`ILBIP`|ILB(내부 부하 분산 장치)의 IP 주소입니다. 이 주소는 Azure Portal에서 ILB의 프런트 엔드 주소로 구성됩니다.  가용성 그룹 수신기와 동일한 프런트 엔드 IP 주소입니다. 이 주소는 `<AG listener IP Address Resource Name>`이 있는 동일한 속성 페이지의 **장애 조치(Failover) 클러스터 관리자** 에서 확인할 수 있습니다.|
|`nnnnn`|부하 분산 장치의 상태 프로브에서 구성한 프로브 포트입니다. 사용하지 않는 모든 TCP 포트는 유효합니다.|
|"SubnetMask"| 클러스터 매개 변수의 서브넷 마스크입니다. TCP IP 브로드캐스트 주소 `255.255.255.255`여야 합니다.| 


클러스터 프로브를 설정한 후에는 PowerShell에서 모든 클러스터 매개 변수를 볼 수 있습니다. 다음 스크립트를 실행합니다.

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

# <a name="public-load-balancer"></a>[공용 부하 분산 장치](#tab/elb)

클러스터 프로브 포트 매개 변수를 설정하려면 다음 스크립트의 변수를 사용자 환경의 값으로 업데이트합니다. 스크립트에서 꺾쇠 괄호(`<` 및 `>`)를 제거합니다.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<Availability group Listener IP Address Resource Name>" 
$ELBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ELBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

다음 표는 업데이트해야 하는 값을 보여 줍니다.


|**값**|**설명**|
|---------|---------|
|`Cluster Network Name`| 네트워크의 Windows Server 장애 조치(failover) 클러스터 이름입니다. **장애 조치(failover) 클러스터 관리자** > **네트워크** 에서 네트워크를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.|
|`AG listener IP Address Resource Name`|AG 수신기의 IP 주소에 대한 리소스 이름입니다. **장애 조치(Failover) 클러스터 관리자** > **역할** 에서, 가용성 그룹 역할의 **서버 이름** 에서 IP 주소 리소스를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다.|
|`ELBIP`|ELB(외부 부하 분산 장치)의 IP 주소입니다. 이 주소는 Azure Portal에서 ELB의 프런트 엔드 주소로 구성되며, 외부 리소스에서 퍼블릭 부하 분산 장치에 연결하는 데 사용됩니다.|
|`nnnnn`|부하 분산 장치의 상태 프로브에서 구성한 프로브 포트입니다. 사용하지 않는 모든 TCP 포트는 유효합니다.|
|"SubnetMask"| 클러스터 매개 변수의 서브넷 마스크입니다. TCP IP 브로드캐스트 주소 `255.255.255.255`여야 합니다.| 


클러스터 프로브를 설정한 후에는 PowerShell에서 모든 클러스터 매개 변수를 볼 수 있습니다. 다음 스크립트를 실행합니다.

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```

> [!NOTE]
> 외부 부하 분산 장치에 대한 개인 IP 주소가 없으므로 사용자는 서브넷 안에서 IP 주소를 확인할 때 VNN DNS 이름을 직접 사용할 수 없습니다. 퍼블릭 LB의 공용 IP 주소를 사용하거나 DNS 서버에서 다른 DNS 매핑을 구성합니다. 


---

## <a name="modify-connection-string"></a>연결 문자열 수정 

이를 지원하는 클라이언트의 경우 `MultiSubnetFailover=True`를 연결 문자열에 추가합니다. MultiSubnetFailover 연결 옵션은 필수가 아니지만 서브넷 장애 조치(failover) 시간을 단축하는 이점이 있습니다. 클라이언트 드라이버가 각 IP 주소에 대해 TCP 소켓을 병렬로 열려고 하기 때문입니다. 클라이언트 드라이버는 첫 번째 IP가 응답할 때까지 기다린 다음 성공적으로 응답하면 해당 IP를 사용하여 연결합니다.

클라이언트가 MultiSubnetFailover 매개 변수를 지원하지 않는 경우 RegisterAllProvidersIP 및 HostRecordTTL 설정을 수정하여 장애 조치(failover) 후 연결 지연을 방지할 수 있습니다. 

PowerShell을 사용하여 RegisterAllProvidersIp 및 HostRecordTTL 설정을 수정합니다. 

```powershell
Get-ClusterResource yourListenerName | Set-ClusterParameter RegisterAllProvidersIP 0  
Get-ClusterResource yourListenerName|Set-ClusterParameter HostRecordTTL 300 
```

자세한 내용은 SQL Server [수신기 시간 제한](/troubleshoot/sql/availability-groups/listener-connection-times-out) 설명서를 참조하세요. 


> [!TIP]
> - 단일 서브넷에 걸친 HADR 솔루션에 대해서도 연결 문자열에서 MultiSubnetFailover parameter = true를 설정하여 연결 문자열 업데이트에 대한 필요성 없이 서브넷의 향후 확장을 지원합니다.  
> - 기본적으로 클라이언트는 클러스터 DNS 레코드를 20분 동안 캐시합니다. 캐시된 레코드에 대한 TTL(Time to Live)인 HostRecordTTL을 줄이면 레거시 클라이언트가 더 빨리 다시 연결할 수 있습니다. 이 경우 HostRecordTTL 설정을 줄이면 DNS 서버의 트래픽이 증가할 수 있습니다.

## <a name="test-failover"></a>테스트 장애 조치

클러스터 기능의 유효성을 검사하기 위해 클러스터형 리소스의 장애 조치(failover)를 테스트하세요. 

다음과 같은 단계를 수행합니다.

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 열고 가용성 그룹 수신기에 연결합니다. 
1. **개체 탐색기** 에서 **Always On 가용성 그룹** 을 확장합니다. 
1. 마우스 오른쪽 단추로 가용성 그룹을 클릭하고, **장애 조치** 를 선택합니다. 
1. 마법사의 안내에 따라 가용성 그룹을 보조 복제본으로 장애 조치합니다. 

복제본이 역할을 전환하고 둘 다 동기화되면 장애 조치가 성공합니다. 


## <a name="test-connectivity"></a>연결 테스트

연결을 테스트하려면 동일한 가상 네트워크의 다른 가상 머신에 로그인합니다. **SQL Server Management Studio** 를 열고 가용성 그룹 수신기에 연결합니다.

>[!NOTE]
>필요한 경우 [SQL Server Management Studio를 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

VNN을 만든 후에는 [SQL Server VM에 대한 클러스터 설정](hadr-cluster-best-practices.md) 최적화를 고려합니다. 

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)



