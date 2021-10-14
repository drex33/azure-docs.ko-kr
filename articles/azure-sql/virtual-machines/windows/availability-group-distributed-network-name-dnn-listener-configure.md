---
title: 가용성 그룹에 대한 DNN 수신기 구성
description: DNN(분산 네트워크 이름) 수신기를 구성하여 VNN(가상 네트워크 이름) 수신기를 대체하고 Azure VM의 SQL Server에서 Always On 가용성 그룹으로 트래픽을 라우팅하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e3e349304281b98968810831d9cad0d8e64a8d75
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002627"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>가용성 그룹에 대한 DNN 수신기 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

DNN(분산 네트워크 이름)은 Azure VM의 SQL Server를 사용하여 트래픽을 클러스터된 적절한 리소스로 라우팅합니다. Azure Load Balancer 없이도 VNN(가상 네트워크 이름) 수신기보다 쉽게 Always On AG(가용성 그룹)에 연결하는 방법을 제공합니다.

이 문서는 DNN 수신기를 구성하여 VNN 수신기를 대체하고 Azure VM의 SQL Server를 통해 가용성 그룹으로 트래픽을 라우팅하여 HADR(고가용성 및 재해 복구)을 실현하는 방법을 설명합니다.


대체 연결 옵션의 경우 [VNN 수신기와 Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md)를 고려해보시기 바랍니다.

## <a name="overview"></a>개요

DNN(분산 네트워크 이름) 수신기는 [SQL Server VM의 Always On 가용성 그룹](availability-group-overview.md)과 함께 사용될 경우 기존 VNN(가상 네트워크 이름) 가용성 그룹 수신기를 대체합니다. 이렇게 하면 트래픽을 라우팅하기 위해 Azure Load Balancer를 사용할 필요가 없으며 배포 및 유지 관리를 간소화하고 장애 조치(failover)를 개선합니다.

DNN 수신기를 사용하여 기존 VNN 수신기를 대체하거나, 두 개의 고유한 연결 지점이 가용성 그룹에 있도록 기존 VNN 수신기와 함께 이 수신기를 사용합니다. 한 연결 지점은 VNN 수신기 이름(기본값이 아닌 경우 포트)을 사용하고 다른 하나는 DNN 수신기 및 포트를 사용합니다.

> [!CAUTION]
> DNN을 사용할 때의 라우팅 동작은 VNN을 사용하는 경우와 다릅니다. 포트 1433은 사용하지 마세요. 자세한 내용은 이 문서 뒷부분에 나오는 [포트 고려 사항](#port-considerations) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하기 전에 다음이 준비되어 있어야 합니다.

- Windows Server 2016 이상에서 [SQL Server 2019 CU8](https://support.microsoft.com/topic/cumulative-update-8-for-sql-server-2019-ed7f79d9-a3f0-a5c2-0bef-d0b7961d2d72) 이상, [SQL Server 2017 CU25](https://support.microsoft.com/topic/kb5003830-cumulative-update-25-for-sql-server-2017-357b80dc-43b5-447c-b544-7503eee189e9) 이상 또는 [SQL Server 2016 SP3](https://support.microsoft.com/topic/kb5003279-sql-server-2016-service-pack-3-release-information-46ab9543-5cf9-464d-bd63-796279591c31) 이상으로 시작 하는 SQL Server.
- 분산 네트워크 이름이 [HADR 솔루션에 적절한 연결 옵션](hadr-cluster-best-practices.md#connectivity)이라고 판단했습니다.
- [Always On 가용성 그룹](availability-group-overview.md)을 구성했습니다. 
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps)을 설치했습니다. 
- DNN 수신기에 사용할 고유 포트를 식별했습니다. DNN 수신기에 사용되는 포트는 가용성 그룹 또는 장애 조치(failover) 클러스터 인스턴스의 모든 복제본에서 고유해야 합니다.  다른 연결은 동일한 포트를 공유할 수 없습니다.



## <a name="create-script"></a>Create 스크립트

PowerShell을 사용하여 DNN(분산 네트워크 이름) 리소스를 만들고 가용성 그룹에 연결합니다.

이렇게 하려면 다음 단계를 따르십시오.

1. 메모장과 같은 텍스트 편집기를 엽니다.
1. 다음 스크립트를 복사하여 붙여넣습니다.

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. 스크립트를 `add_dnn_listener.ps1`과 같은 `.ps1` 파일로 저장합니다.

## <a name="execute-script"></a>스크립트 실행

DNN 수신기를 만들려면 가용성 그룹 이름, 수신기 이름 및 포트에 대한 매개 변수를 전달하는 스크립트를 실행합니다.

예를 들어 가용성 그룹 이름이 `ag1`, 수신기 이름이 `dnnlsnr`, 수신기 포트가 `6789`라고 가정하면 다음 단계를 따라야 합니다.

1. 명령 프롬프트 또는 PowerShell과 같은 명령줄 인터페이스 도구를 엽니다.
1. c:\Documents와 같이 `.ps1` 스크립트를 저장한 위치로 이동합니다.
1. 다음 스크립트를 실행합니다. ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` 예를 들면 다음과 같습니다.

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>수신기 확인

SQL Server Management Studio 또는 Transact-SQL을 사용하여 DNN 수신기가 성공적으로 만들어졌는지 확인합니다.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

[SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)에서 **가용성 그룹 수신기** 를 확장하여 DNN 수신기를 봅니다.

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="SQL Server Management Studio(SSMS)의 가용성 그룹 수신기에서 DNN 수신기 보기":::

### <a name="transact-sql"></a>Transact-SQL

Transact-SQL을 사용하여 DNN 수신기의 상태를 확인합니다.

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

`is_distributed_network_name`에 대한 `1` 값은 수신기가 DNN(분산 네트워크 이름) 수신기임을 나타냅니다.

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="sys.availability_group_listeners를 사용하여 is_distributed_network_name에서 값이 1인 DNN 수신기를 식별합니다.":::

## <a name="update-connection-string"></a>연결 문자열 업데이트

DNN 수신기에 연결 해야 하는 모든 응용 프로그램에 대 한 연결 문자열을 업데이트 합니다. DNN 수신기에 대 한 연결 문자열은 DNN 포트 번호를 제공 하 고 연결 문자열에를 지정 해야 합니다 `MultiSubnetFailover=True` . SQL 클라이언트가 매개 변수를 지원 하지 않는 경우 `MultiSubnetFailover=True` DNN 수신기와 호환 되지 않습니다.  

다음은 수신기 이름 **DNN_Listener** 및 포트 6789에 대 한 연결 문자열의 예입니다. 

`DataSource=DNN_Listener,6789,MultiSubnetFailover=True`

## <a name="test-failover"></a>테스트 장애 조치

가용성 그룹의 장애 조치(failover)를 테스트하여 기능을 보장합니다.

장애 조치(failover)를 테스트하려면 다음 단계를 수행합니다.

1. [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 DNN 수신기 또는 복제본 중 하나에 연결합니다.
1. **개체 탐색기** 에서 **Always On 가용성 그룹** 을 확장합니다.
1. 가용성 그룹을 마우스 오른쪽 단추로 클릭하고 **장애 조치(failover)** 를 선택하여 **장애 조치 마법사** 를 엽니다.
1. 메시지에 따라 장애 조치(failover) 대상을 선택하고 보조 복제본에 대한 가용성 그룹을 장애 조치(failover)합니다.
1. 데이터베이스가 새 주 복제본에서 동기화된 상태인지 확인합니다.
1. (선택 사항) 원래 주 복제본 또는 다른 보조 복제본으로 장애 복구합니다.

## <a name="test-connectivity"></a>연결 테스트

다음 단계를 통해 DNN 수신기에 대한 연결을 테스트합니다.

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 엽니다.
1. DNN 수신기에 연결합니다.
1. 새 쿼리 창을 열고 `SELECT @@SERVERNAME`을 실행하여 연결된 복제본을 확인합니다.
1. 가용성 그룹을 다른 복제본으로 장애 조치(failover)합니다.
1. 시간이 적당히 지나면 `SELECT @@SERVERNAME`을 실행하여 가용성 그룹이 다른 복제본에서 호스트되고 있는지 확인합니다.

## <a name="limitations"></a>제한 사항

- DNN 수신기는 **반드시** 고유 포트로 구성해야 합니다.  모든 복제본의 다른 연결과 포트를 공유할 수 없습니다.
- DNN 수신기에 연결하는 클라이언트는 연결 문자열에서 `MultiSubnetFailover=True` 매개 변수를 지원해야 합니다. 
- 다른 SQL Server 기능 및 DNN 지원 가용성 그룹으로 작업하는 경우 추가 고려 사항이 있을 수 있습니다. 자세한 내용은 [DNN과 상호 운용되는 AG](availability-group-dnn-interoperability.md)를 참조하세요. 

## <a name="port-considerations"></a>포트 고려 사항

DNN 수신기는 모든 IP 주소에서 수신 대기하고 특정한 고유 포트에서 수신하도록 설계되었습니다. 수신기 이름에 대한 DNS 항목은 가용성 그룹에 있는 모든 복제본의 주소로 확인되어야 합니다. 이는 [스크립트 만들기](#create-script) 섹션에 제공된 PowerShell 스크립트를 통해 자동으로 수행됩니다. DNN 수신기는 모든 IP 주소에 대한 연결을 허용하므로 수신기 포트가 고유하고 가용성 그룹의 다른 복제본에서는 사용되지 않는 것이 중요합니다. SQL Serve는 기본적으로 직접 또는 SQL Browser 서비스를 통해 포트 1433을 수신 대기하므로 DNN 수신기에 포트 1433을 사용하는 것은 권장되지 않습니다. 

## <a name="next-steps"></a>다음 단계

가용성 그룹이 배포되면 [Azure VM에서 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)을 최적화하는 것이 좋습니다. 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
