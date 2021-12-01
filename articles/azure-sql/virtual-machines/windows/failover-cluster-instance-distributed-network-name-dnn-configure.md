---
title: 장애 조치(failover) 클러스터 인스턴스에 대한 DNN 구성
description: Azure VM 장애 조치(failover) 클러스터 인스턴스(FCI)에서 트래픽을 SQL 서버로 라우팅하도록 분산 네트워크 이름(DNN)을 구성하는 방법에 대해 알아보세요.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
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
ms.openlocfilehash: 4470fea8caa2ec9132a10b9a0ab7c1e4ee6786cf
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367810"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>장애 조치(failover) 클러스터 인스턴스에 대한 DNN 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내의 여러 서브넷에 SQL Server VM을 만들어 장애 조치(failover) 클러스터 인스턴스에 대한 DNN(분산 네트워크 이름)이 필요하지 않습니다.

Azure 가상 머신에서 분산 네트워크 이름(DNN)은 적절한 클러스터 리소스로 트래픽을 라우팅합니다. Azure Load Balancer 없이 가상 네트워크 이름(VNN)보다 SQL Server 페일오버 클러스터 인스턴스(FCI)에 더 쉽게 연결하는 방법을 제공합니다. 

이 문서에서는 고가용성 및 재해 복구(HADR)을 위해 Azure VM의 SQL Server를 사용하여 장애 조치(failover) 클러스터 인스턴스로 트래픽을 라우팅하도록 DNN 리소스 구성 방법에 대해 설명합니다. 


대체 연결 옵션의 경우 대신 [가상 네트워크 이름 및 Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md)를 고려하세요. 

## <a name="overview"></a>개요

[SQL Server VM에서 AlwaysOn 장애 조치(failover) 클러스터 인스턴스와](failover-cluster-instance-overview.md)와 함께 사용할 경우 분산 네트워크 이름(DNN)이 가상 네트워크 이름(VNN)을 연결점으로 대체합니다. 이로 인해 Azure Load Balancer가 트래픽을 VNN으로 라우팅하여 배포, 유지 보수 및 장애 조치(failover)를 간소화할 필요가 없습니다. 

FCI 배포의 경우 VNN은 여전히 존재하지만 클라이언트는 VNN 이름 대신 DNN DNS 이름에 연결됩니다. 

## <a name="prerequisites"></a>필수 구성 요소 

이 문서의 단계를 완료하기 전에 다음이 준비되어 있어야 합니다.

- SQL Server [SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) 이상, [SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9) 이상 또는 Windows Server 2016 이상에서 [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31) 이상부터 시작합니다.
- 분산 네트워크 이름이 [HADR 솔루션에 대한 연결 옵션](hadr-cluster-best-practices.md#connectivity)으로 적절하다고 결정했습니다.
- [장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)를 구성했습니다. 
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps)을 설치했습니다. 

## <a name="create-dnn-resource"></a>DNN 리소스 생성 

DNN 리소스는 SQL Server FCI와 동일한 클러스터 그룹에 생성됩니다. PowerShell을 사용하여 FCI 클러스터 그룹 내에 DNN 리소스를 생성합니다. 

다음 PowerShell 명령은 리소스 이름이 `<dnnResourceName>`인 SQL Server FCI 클러스터 그룹에 DNN 리소스를 추가합니다. 리소스 이름은 리소스를 고유하게 식별하는 데 사용됩니다. 사용자에게 적합한 클러스터 특유의 이름을 사용하세요. 리소스 종류는 `Distributed Network Name`이어야 합니다. 

`-Group` 값은 분산 네트워크 이름을 추가할 SQL Server FCI에 해당하는 클러스터 그룹 이름이어야 합니다. 기본 인스턴스의 경우, 일반적인 양식은 `SQL Server (MSSQLSERVER)`입니다. 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

예를 들어 기본 SQL Server FCI에 대한 DNN 리소스`dnn-demo`를 생성하려면 다음 PowerShell 명령을 사용하세요.

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>클러스터 DNN DNS 이름 설정

클러스터의 DNN 리소스에 대한 DNS 이름을 설정하세요. 그런 다음 클러스터는 이 값을 사용하여 현재 SQL Server FCI를 호스팅하는 노드로 트래픽을 라우팅합니다. 

클라이언트는 DNS 이름을 사용하여 SQL Server FCI에 연결합니다. 고유한 값을 선택할 수 있습니다. 또는 기존 FCI가 이미 있고 클라이언트 연결 문자열을 업데이트하지 않으려는 경우 클라이언트가 이미 사용하고 있는 현재 VNN을 사용하도록 DNN을 구성할 수 있습니다. 이렇게 하려면 DNS에서 DNN을 설정하기 전에 [VNN의 이름을 변경](#rename-the-vnn)해야 합니다.

DNN의 DNS 이름을 설정하려면 다음 명령을 사용하세요. 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName` 값은 클라이언트가 SQL Server FCI에 연결하는 데 사용하는 값입니다. 예를 들어 `FCIDNN`에 연결하는 클라이언트의 경우 다음 PowerShell 명령을 사용하세요.

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

이제 클라이언트는 SQL Server FCI에 연결할 때 연결 문자열에 `FCIDNN`을 입력합니다. 

   > [!WARNING]
   > 현재 가상 네트워크 이름(VNN)은 FCI 인프라의 필수 구성 요소이므로 삭제하지 마세요. 


### <a name="rename-the-vnn"></a>VNN 이름 변경 

기존 가상 네트워크 이름이 있고 클라이언트가 이 값을 사용하여 SQL Server FCI에 연결하도록 하려면 현재 VNN의 이름을 자리 표시자 값으로 변경해야 합니다. 현재 VNN의 이름을 변경한 후에는 DNN의 DNS 이름 값을 VNN으로 설정할 수 있습니다. 

VNN 이름을 바꾸려면 몇 가지 제한 사항이 적용됩니다. 자세한 내용은 [FCI 이름 변경](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)을 참조하세요.

비즈니스에 현재 VNN을 사용할 필요가 없는 경우 이 섹션을 건너뛰세요. VNN 이름을 변경한 후 [클러스터 DNN DNS 이름을 설정하세요](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>DNN 리소스 온라인 설정

DNN 리소스의 이름이 적절하게 지정되고 클러스터에서 DNS 이름 값을 설정한 후 PowerShell을 사용하여 클러스터에서 DNN 리소스를 온라인으로 설정합니다. 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

예를 들어 DNN 리소스`dnn-demo`을 시작하려면 다음 PowerShell 명령을 사용하세요. 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>가능한 소유자 구성

기본적으로 클러스터는 DNN DNS 이름을 클러스터의 모든 노드에 바인딩합니다. 그러나 SQL Server FCI에 속하지 않는 클러스터 노드는 DNN 가능한 소유자 목록에서 제외해야 합니다. 

가능한 소유자를 업데이트하려면 다음 단계를 수행하세요.

1. 장애 조치(failover) 클러스터 관리자의 DNN 리소스로 이동하세요. 
1. DNN 리소스를 마우스 오른쪽 버튼으로 클릭하고 **속성** 을 선택하세요. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="속성 명령이 강조 표시된 DNN 리소스의 바로 가기 메뉴.":::

1. 장애 조치(failover) 클러스터 인스턴스에 참여하지 않는 노드 확인란의 선택을 취소하세요. DNN 리소스의 가능한 소유자 목록은 SQL Server 인스턴스 리소스의 가능한 소유자 목록과 일치해야 합니다. 예를 들어, Data3가 FCI에 참여하지 않는다고 가정하면 다음 이미지는 DNN 리소스의 가능한 소유자 목록에서 Data3를 제거하는 예입니다. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="DNN 리소스의 가능한 소유자에 대해 FCI에 참여하지 않는 노드 옆의 확인란 선택 취소":::

1. **확인** 을 선택하여 설정을 저장합니다. 


## <a name="restart-sql-server-instance"></a>SQL Server 인스턴스 재시작 

장애조치(failover) 클러스터 관리자를 사용하여 SQL 서버 인스턴스를 다시 시작하세요. 다음 단계를 수행합니다.

1. 장애 조치(failover) 클러스터 관리자의 SQL 서버 리소스로 이동하세요.
1. SQL Server 리소스를 마우스 오른쪽 버튼으로 클릭하고 오프라인으로 전환하세요. 
1. 연결된 모든 리소스가 오프라인 상태가 되면 SQL Server 리소스를 마우스 오른쪽 버튼으로 클릭하고 다시 온라인으로 전환하세요. 

## <a name="update-connection-string"></a>연결 문자열 업데이트

SQL Server FCI DNN에 연결하는 모든 애플리케이션의 연결 문자열을 업데이트하고 `MultiSubnetFailover=True` 연결 문자열에 를 포함합니다. 클라이언트가 MultiSubnetFailover 매개 변수를 지원하지 않는 경우 DNN과 호환되지 않습니다. 

다음은 DNS 이름이 FCIDNN인 SQL FCI **DNN에** 대한 연결 문자열의 예입니다. 

`Data Source=FCIDNN, MultiSubnetFailover=True`

또한 DNN이 원래 VNN을 사용하지 않는 경우 SQL Server FCI에 연결하는 SQL 클라이언트는 연결 문자열을 DNN DNS 이름으로 업데이트해야 합니다. 이러한 요구 사항을 방지하기 위해 DNS 이름 값을 VNN 이름으로 업데이트할 수 있습니다. 그러나 [기존 VNN을 자리 표시자로 먼저 변경](#rename-the-vnn)해야 합니다. 

## <a name="test-failover"></a>테스트 장애 조치


클러스터 기능의 유효성을 검사하기 위해 클러스터된 리소스의 장애 조치(failover)를 테스트하세요. 


장애 조치(failover)를 테스트하려면 다음 단계를 수행하세요. 

1. RDP를 사용하여 SQL Server 클러스터 노드 중 하나에 연결하세요.
1. **장애 조치(Failover) 클러스터 관리자** 를 엽니다. **역할** 을 선택합니다. SQL Server FCI 역할을 소유하는 노드를 살펴봅니다.
1. SQL Server FCI 역할을 마우스 오른쪽 단추로 클릭합니다. 
1. **이동** 을 선택한 다음 **가장 적합한 노드** 를 선택합니다.

**장애 조치(failover) 클러스터 관리자** 는 역할을 보여 주며 해당 리소스는 오프라인으로 전환됩니다. 그런 다음 리소스가 이동하고 다른 노드에서 온라인 상태로 다시 전환됩니다.

## <a name="test-connectivity"></a>연결 테스트

연결을 테스트하려면 동일한 가상 네트워크의 다른 가상 머신에 로그인합니다. **SQL Server Management Studio** 를 열고 DNN DNS 이름을 사용하여 SQL Server FCI에 연결하세요.

필요한 경우 [SQL Server Management Studio를 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)할 수 있습니다.


## <a name="avoid-ip-conflict"></a>IP 충돌 방지

이는 FCI 리소스에서 사용하는 가상 IP(VIP) 주소가 Azure의 다른 리소스에 중복으로 할당되는 것을 방지하는 선택적 단계입니다. 

이제 고객이 DNN을 사용하여 SQL Server FCI에 연결하지만 가상 네트워크 이름(VNN) 및 가상 IP는 FCI 인프라의 필수 구성 요소이므로 삭제할 수 없습니다. 그러나 더 이상 Azure에 가상 IP 주소를 예약하는 부하 분산 장치가 없으므로 가상 네트워크의 다른 리소스에 FCI에서 사용하는 가상 IP 주소와 동일한 IP 주소가 할당될 위험이 있습니다. 이로 인해 중복된 IP의 충돌 문제가 발생할 수 있습니다. 

IP 주소를 예약하기 위해 APIPA 주소 또는 전용 네트워크 어댑터를 구성하세요. 

### <a name="apipa-address"></a>APIPA 주소

중복 IP 주소를 사용하지 않으려면 APIPA 주소(또는 링크-로컬 주소라고도 함)를 구성하세요. 이렇게 하려면 다음 명령을 실행합니다.

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

이 명령에서 "가상 IP 주소"는 클러스터된 VIP 주소 리소스의 이름이고 "169.254.1.1"은 VIP 주소에 대한 선택된 APIPA 주소입니다. 해당 비즈니스에 가장 적합한 주소를 선택하세요. APIPA 주소 공간을 비롯한 모든 네트워크에 IP 주소를 허용하도록 `OverrideAddressMatch=1`을 설정하세요. 

### <a name="dedicated-network-adapter"></a>전용 네트워크 어댑터

또는 Azure에서 가상 IP 주소 리소스에 사용되는 IP 주소를 예약하도록 네트워크 어댑터를 구성하세요. 그러나 이렇게 하면 서브넷 주소 공간의 주소가 사용되고 네트워크 어댑터가 다른 용도로 사용되지 않도록 하는 추가 오버헤드가 발생합니다.

## <a name="limitations"></a>제한 사항


- DNN 수신기에 연결하는 클라이언트는 연결 문자열에서 `MultiSubnetFailover=True` 매개 변수를 지원해야 합니다. 
- 다른 SQL Server 기능 및 DNN을 포함하는 FCI를 사용하여 작업하는 경우 더 많은 고려 사항이 있을 수 있습니다. 자세한 내용은 [DNN 상호 운용성을 포함한 FCI](failover-cluster-instance-dnn-interoperability.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(Failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)

