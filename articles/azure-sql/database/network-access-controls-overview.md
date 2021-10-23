---
title: 네트워크 액세스 컨트롤
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Azure SQL Database 및 Azure Synapse Analytics에 대한 네트워크 액세스를 관리하고 제어하는 방법에 대한 개요입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 970e20135fab85cbcc6c67a7add1ca7527cefba6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260439"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database 및 Azure Synapse Analytics 네트워크 액세스 제어

Azure SQL Database 및 Azure Synapse Analytics에 대한 [Azure Portal](single-database-create-quickstart.md)에서 논리 SQL Server를 만들 때 결과는 *yourservername.database.windows.net* 형식의 퍼블릭 엔드포인트입니다.

다음 네트워크 액세스 제어를 사용하여 퍼블릭 엔드포인트를 통해 데이터베이스에 대한 액세스를 선택적으로 허용할 수 있습니다.

- Azure 서비스 허용: ON으로 설정된 경우 Azure 가상 머신과 같은 Azure 경계 내의 다른 리소스가 SQL Database에 액세스할 수 있습니다.
- IP 방화벽 규칙: 이 기능을 사용하여 온-프레미스 컴퓨터와 같은 특정 IP 주소에서의 연결을 명시적으로 허용합니다.

다음을 통해 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)에서 데이터베이스에 대한 개인 액세스를 허용할 수도 있습니다.

- 가상 네트워크 방화벽 규칙: 이 기능을 사용하여 Azure 경계 내에서 특정 가상 네트워크의 트래픽을 허용합니다.
- Private Link: 이 기능을 사용하여 특정 가상 네트워크 내에서 [논리 SQL Server](logical-servers.md)에 대한 프라이빗 엔드포인트를 만들 수 있습니다.

> [!IMPORTANT]
> 이 문서는 **SQL Managed Instance** 에 적용되지 *않습니다*. 필요한 네트워킹 구성에 대한 자세한 내용은 [Azure SQL Managed Instance에 연결](../managed-instance/connect-application-instance.md)을 참조하세요.

이러한 액세스 제어 및 기능에 대한 개략적인 설명은 아래 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure 서비스 허용

기본적으로 [Azure Portal에서](single-database-create-quickstart.md) 새 논리 SQL Server를 만드는 동안 이 설정은 **OFF** 로 설정됩니다. 이 설정은 퍼블릭 서비스 엔드포인트를 사용하여 연결을 허용하는 경우에 나타납니다.

다음과 같이 논리 SQL Server를 만든 후 방화벽 창을 통해 이 설정을 변경할 수도 있습니다.
  
![서버 방화벽 관리 스크린샷][2]

**ON** 으로 설정하면 서버는 Azure 경계 내의 모든 리소스에서 통신을 허용하며, 이는 구독에 포함되거나 포함되지 않을 수 있습니다.

많은 경우, 대부분의 고객이 원하는 것 보다는 **ON** 설정이 더 관용적입니다. 이 설정을 **OFF** 로 설정하고 더 제한적인 IP 방화벽 규칙 또는 가상 네트워크 방화벽 규칙으로 바꿀 수 있습니다. 

하지만 이렇게 하면 가상 네트워크의 일부가 아닌 Azure의 가상 머신에서 실행되어 Azure IP 주소를 통해 데이터베이스에 연결되는 다음 기능에 영향을 줍니다.

### <a name="import-export-service"></a>가져오기/내보내기 서비스

가져오기/내보내기 서비스는 **Azure 서비스에 대한 액세스 허용** 을 **OFF** 로 설정하면 작동하지 않습니다. 하지만 [Azure VM에서 수동으로 sqlpackage.exe를 실행하거나 DACFx API를 사용하여 코드에서 직접 내보내기를 수행하여](./database-import-export-azure-services-off.md) 문제를 해결할 수 있습니다.

### <a name="data-sync"></a>데이터 동기화

**Azure 서비스에 대한 액세스 허용** 을 **OFF** 로 설정된 데이터 동기화 기능을 사용하려면 개별 방화벽 규칙 항목을 만들어 **허브** 데이터베이스를 호스팅하는 지역의 **SQL 서비스 태그** 에서 [IP 주소를 추가](firewall-create-server-level-portal-quickstart.md)해야 합니다.
**허브** 및 **구성원** 데이터베이스(다른 지역에 있을 수 있음)를 모두 호스트하는 서버에 이러한 서버 수준 방화벽 규칙을 추가합니다.

다음 PowerShell 스크립트를 사용하여 미국 서부 지역의 SQL 서비스 태그에 해당하는 IP 주소를 생성합니다.

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag는 위치 매개 변수를 지정했음에도 불구하고 SQL 서비스 태그의 전역 범위를 반환합니다. 동기화 그룹에서 사용하는 허브 데이터베이스를 호스트하는 지역으로 필터링해야 합니다.

PowerShell 스크립트의 출력은 CIDR(Classless Interdomain Routing) 표기법으로 작성됩니다. 다음과 같이 [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9)을 사용하여 시작 및 종료 IP 주소 형식으로 변환해야 합니다.

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

이러한 추가 PowerShell 스크립트를 사용하여 모든 IP 주소를 CIDR에서 시작 및 종료 IP 주소 형식으로 변환할 수 있습니다.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

이제 이러한 규칙을 별도의 방화벽 규칙으로 추가한 다음 **Azure 서비스에서 서버에 액세스하도록 허용** 을 OFF로 설정할 수 있습니다.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙

IP 기반 방화벽은 클라이언트 컴퓨터의 [IP 주소를 명시적으로 추가](firewall-create-server-level-portal-quickstart.md)할 때까지 서버에 대한 모든 액세스를 방지하는 Azure에서 논리적 SQL Server의 기능입니다.

## <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

IP 규칙 외에도 서버 방화벽을 사용하여 *가상 네트워크 규칙* 을 정의할 수 있습니다.  
자세한 내용은 [Azure SQL Database에 대한 가상 네트워크 서비스 엔드포인트 및 규칙](vnet-service-endpoint-rule-overview.md)을 참조하거나 이 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Azure 네트워킹 용어

가상 네트워크 방화벽 규칙을 살펴보기 위해 다음 Azure 네트워킹 용어를 알고 있어야 합니다.

**가상 네트워크:** Azure 구독과 연결된 가상 네트워크가 있을 수 있습니다.

**서브넷:** 가상 네트워크에 **서브넷** 이 포함됩니다. 소유한 Azure VM(가상 머신)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 컴퓨팅 노드가 포함될 수 있습니다. 액세스를 허용하도록 보안을 구성해야 가상 네트워크 외부의 컴퓨팅 노드가 가상 네트워크에 액세스할 수 있습니다.

**가상 네트워크 서비스 엔드포인트:** [가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. 이 문서에서는 SQL Database라는 Azure 서비스를 나타내는 **Microsoft.Sql** 의 형식 이름을 살펴봅니다.

**가상 네트워크 규칙:** 서버에 대한 가상 네트워크 규칙은 서버의 ACL(액세스 제어 목록)에 나열된 서브넷입니다. SQL Database에서 데이터베이스에 대한 ACL에 나열되려면 서브넷에 **Microsoft.Sql** 형식 이름이 있어야 합니다. 가상 네트워크 규칙은 서브넷에 있는 모든 노드에서 보낸 통신을 수락하도록 서버에 지시합니다.

## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 및 가상 네트워크 방화벽 규칙

Azure SQL Database 방화벽을 사용하여 SQL Database에 대한 통신이 수락되는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 프라이빗 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 하지만 Azure 프라이빗 네트워크 내의 가상 머신(VM)은 *동적* IP 주소로 구성됩니다. 동적 IP 주소는 VM이 다시 시작된 다음 IP 기반 방화벽 규칙을 무효화할 때 변경될 수 있습니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대한 *고정* IP 주소를 가져오면 이러한 제한을 해결할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 고정 공용 IP 주소로 가상 머신 만들기](../../virtual-network/ip-services/virtual-network-deploy-static-pip-arm-portal.md)를 참조하세요. 하지만 고정 IP 방법은 관리가 어려워질 수 있고 대규모로 이루어질 경우 비용이 많이 듭니다.

가상 네트워크 규칙은 VM이 포함된 특정 서브넷의 액세스를 설정하고 관리하는 보다 쉬운 방법입니다.

> [!NOTE]
> 아직 서브넷에 SQL Database가 있을 수 없습니다. 서버가 가상 네트워크에 있는 서브넷의 노드인 경우 가상 네트워크 내의 모든 노드가 SQL Database와 통신할 수 있습니다. 이 경우 VM은 가상 네트워크 규칙이나 IP 규칙이 없어도 SQL Database와 통신할 수 있습니다.

## <a name="private-link"></a>Private Link

Private Link를 사용하면 **프라이빗 엔드포인트** 를 통해 서버에 연결할 수 있습니다. 프라이빗 엔드포인트는 특정 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 및 서브넷 내의 개인 IP 주소입니다.

## <a name="next-steps"></a>다음 단계

- 서버 수준 IP 방화벽 규칙 만드는 방법에 대한 빠른 시작은 [SQL Database에서 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.

- 서버 수준 가상 네트워크 방화벽 규칙을 만드는 방법에 대한 빠른 시작은 [Azure SQL Database에 대한 가상 네트워크 서비스 엔드포인트 및 규칙](vnet-service-endpoint-rule-overview.md)을 참조하세요.

- 오픈 소스 또는 타사 애플리케이션에서 SQL Database의 데이터베이스에 연결하는 방법에 대한 도움말은 [SQL Database에 대한 클라이언트 빠른 시작 코드 샘플](/previous-versions/azure/ee336282(v=azure.100))을 참조하세요.

- 열어야 할 수 있는 추가 포트에 대한 자세한 내용은 [ADO.NET 4.5와 SQL Database에 대한 1433 이외 포트](adonet-v12-develop-direct-route-ports.md)의 **SQL Database: 내부 및 외부** 섹션을 참조하세요.

- Azure SQL Database 연결에 대한 개요는 [Azure SQL 연결 아키텍처](connectivity-architecture.md)를 참조하세요.

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](security-overview.md)을 참조하세요.

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png