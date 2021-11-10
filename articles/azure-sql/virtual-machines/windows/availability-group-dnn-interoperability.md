---
title: 가용성 그룹 및 DNN 수신기와의 기능 상호 운용성
description: 'Azure VM의 SQL Server에서 Always On 가용성 그룹을 사용하여 특정 SQL Server 기능 및 DNN(분산 네트워크 이름) 수신기를 사용할 때의 추가 고려 사항에 대해 알아봅니다. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 79aefd5c4b41e86aeeb2f7f02f2df57397c4b36b
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157483"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>AG 및 DNN 수신기와의 기능 상호 운용성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내에서 [여러 서브넷](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) 에 SQL Server vm을 만들어 AG (Always On 가용성) 그룹에 대 한 분산 네트워크 이름이 필요 하지 않도록 합니다.

하드 코딩된 VNN(가상 네트워크 이름)을 사용하는 특정 SQL Server 기능이 있습니다. 따라서 Always On 가용성 그룹과 함께 DNN (분산 네트워크 이름) 수신기를 사용 하 고 단일 서브넷의 Azure vm에서 SQL Server 하는 경우 몇 가지 추가 고려 사항이 있을 수 있습니다. 

이 문서에서는 가용성 그룹 DNN 수신기와 SQL Server 기능 및 상호 운용성에 대해 자세히 설명합니다. 

## <a name="behavior-differences"></a>동작 차이점

VNN 수신기 및 DNN 수신기의 기능 간에는 주의해야 할 몇 가지 동작 차이점이 있습니다. 

- **장애 조치 시간**: 네트워크 부하 분산 장치가 오류 이벤트를 감지하고 라우팅을 변경할 때까지 기다릴 필요가 없으므로 DNN 수신기를 사용할 때 장애 조치 시간이 더 단축됩니다. 
- **기존 연결**: 장애 조치 가용성 그룹 내의 *특정 데이터베이스* 에 대한 연결이 끊어지지만 장애 조치 프로세스 중에 DNN이 온라인 상태를 유지하므로 주 복제본에 대한 다른 연결은 열린 상태로 유지됩니다. 이것은 가용성 그룹이 장애 조치되고 수신기가 오프라인으로 전환되고 주 복제본이 보조 역할로 전환되어 주 복제본에 대한 모든 연결이 닫히게 되는 기존 VNN 환경과는 다릅니다. DNN 수신기를 사용하는 경우 장애 조치 시 연결이 새 주 복제본으로 리디렉션되도록 애플리케이션 연결 문자열을 조정해야 할 수 있습니다.
- **트랜잭션 열기**: 장애 조치 가용성 그룹의 데이터베이스에 대해 열린 트랜잭션이 닫히고 롤백되며 *수동으로* 다시 연결해야 합니다. 예를 들어, SQL Server Management Studio에서 쿼리 창을 닫고 새 창을 엽니다. 

## <a name="client-drivers"></a>클라이언트 드라이버

ODBC, OLEDB, ADO.NET, JDBC, PHP 및 Node.js 드라이버의 경우 사용자는 연결 문자열의 서버 이름으로 DNN 수신기 이름 및 포트를 명시적으로 지정해야 합니다. 장애 조치(failover) 시 빠른 연결을 보장하려면 SQL 클라이언트에서 지원하는 경우 연결 문자열에 `MultiSubnetFailover=True`를 추가합니다. 

## <a name="tools"></a>도구

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is) 및 [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) 사용자는 수신기에 연결하려는 연결 문자열의 서버 이름으로 DNN 수신기 이름 및 포트를 명시적으로 지정해야 합니다. 

SSMS(SQL Server Management Studio) GUI를 통해 DNN 수신기를 만드는 것은 현재 지원되지 않습니다. 


## <a name="availability-groups-and-fci"></a>가용성 그룹 및 FCI

FCI(장애 조치(failover) 클러스터 인스턴스)를 복제본 중 하나로 사용하여 Always On 가용성 그룹을 구성할 수 있습니다. 이 구성이 DNN 수신기에서 작동하려면 [FCI가 DNN를 사용](failover-cluster-instance-distributed-network-name-dnn-configure.md)해야 합니다. FCI 가상 IP 주소를 AG DNN IP 목록에 배치할 수 있는 방법이 없기 때문입니다. 

이 구성에서 FCI 복제본의 미러링 엔드포인트 URL은 FCI DNN를 사용해야 합니다. 마찬가지로 FCI가 읽기 전용 복제본으로 사용되는 경우 FCI 복제본에 대한 읽기 전용 라우팅은 FCI DNN을 사용해야 합니다. 

미러링 엔드포인트의 형식은 `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` 입니다. 

예를 들어 FCI DNN DNS 이름이 `dnnlsnr`이고 `5022`가 FCI의 미러링 엔드포인트의 포트인 경우 엔드포인트 URL을 만드는 T-SQL(Transact-SQL) 코드 조각은 다음과 같습니다. 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

마찬가지로 읽기 전용 라우팅 URL의 형식은 `TCP://<FCI DNN DNS name>:<SQL Server instance port>` 입니다. 

예를 들어 DNN DNS 이름이 `dnnlsnr`이고 `1444`가 읽기 전용 대상 SQL Server FCI에서 사용하는 포트인 경우 읽기 전용 라우팅 URL을 만드는 T-SQL 코드 조각은 다음과 같습니다: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

기본 1433 포트인 경우 URL에서 포트를 생략할 수 있습니다. 명명된 인스턴스의 경우 명명된 인스턴스에 대한 고정 포트를 구성하고 읽기 전용 라우팅 URL에 지정합니다.  

## <a name="distributed-availability-group"></a>분산 가용성 그룹

분산 가용성 그룹은 현재 DNN 수신기에서 지원되지 않습니다. 

## <a name="replication"></a>복제

트랜잭션, 병합 및 스냅샷 복제는 모두 VNN 수신기를 수신기에 연결하는 복제 개체의 DNN 수신기 및 포트로 바꾸기를 지원합니다. 

가용성 그룹에서 복제를 사용하는 방법에 대한 자세한 내용은 [게시자 및 AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [구독자 및 AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server), [배포자 및 AG](/sql/relational-databases/replication/configure-distribution-availability-group)를 참조하세요.

## <a name="msdtc"></a>MSDTC

로컬 및 클러스터형 MSDTC는 모두 지원되지만 MSDTC는 HA 포트를 구성하는 데 표준 Azure Load Balancer가 필요한 동적 포트를 사용합니다. 따라서 VM은 표준 IP 예약을 사용해야 하거나 인터넷에 노출될 수 없습니다. 

RPC 엔드포인트 매퍼 포트 135용 규칙과 및 실제 MSDTC 포트용 규칙, 총 두 개의 규칙을 정의합니다. 장애 조치(failover) 후 새 노드에서 변경된 후 LB 규칙을 새 MSDTC 포트로 수정합니다. 

MSDTC가 로컬인 경우 아웃바운드 통신을 허용해야 합니다. 

## <a name="distributed-query"></a>분산 쿼리 

분산 쿼리는 AG DNN 수신기 및 포트를 사용하여 구성할 수 있는 연결된 서버를 사용합니다. 포트가 1433이 아닌 경우 연결된 서버를 구성할 때 SSMS(SQL Server Management Studio)의 **다른 데이터 원본 사용** 옵션을 선택합니다. 

## <a name="filestream"></a>FileStream

Filestream은 지원되지만 사용자가 Windows 파일 API를 사용하여 범위가 있는 파일 공유에 액세스하는 시나리오에는 지원되지 않습니다. 

## <a name="filetable"></a>Filetable

Filetable은 지원되지만 사용자가 Windows 파일 API를 사용하여 범위가 있는 파일 공유에 액세스하는 시나리오에는 지원되지 않습니다. 

## <a name="linked-servers"></a>연결된 서버

AG DNN 수신기 이름 및 포트를 사용하여 연결된 서버를 구성합니다. 포트가 1433이 아닌 경우 연결된 서버를 구성할 때 SSMS(SQL Server Management Studio)의 **다른 데이터 원본 사용** 옵션을 선택합니다. 


## <a name="frequently-asked-questions"></a>질문과 대답


- 어떤 SQL Server 버전이 AG DNN 수신기를 지원하나요? 

   SQL Server 2019 CU8 이상 버전에서 지원됩니다.

- DNN 수신기가 사용되는 경우 예상되는 장애 조치(failover) 시간은 어느 정도인가요?

   DNN 수신기의 경우 장애 조치(failover) 시간은 추가 시간 없이 AG 장애 조치(failover) 시간입니다(예: Azure Load Balancer를 사용할 경우의 프로브 시간).

- SQL 클라이언트에 OLEDB 및 ODBC와 함께 DNN을 지원하기 위한 버전 요구 사항이 있나요?

   DNN 수신기에 대한 `MultiSubnetFailover=True` 연결 문자열 지원을 권장합니다. SQL Server 2012(11.x)부터 사용할 수 있습니다.

- DNN 수신기를 사용하려면 SQL Server 구성을 변경해야 하나요? 

   DNN을 사용하기 위해 SQL Server 구성을 변경할 필요는 없지만 일부 SQL Server 기능은 더 많은 고려 사항이 필요할 수 있습니다. 

- DNN은 다중 서브넷 클러스터를 지원하나요?

   예. 클러스터는 서브넷에 관계없이 가용할 수 있는 모든 복제본의 실제 IP 주소로 DNS의 DNN을 바인딩합니다. SQL 클라이언트는 서브넷에 관계없이 DNS 이름의 모든 IP 주소를 시도합니다. 

- 가용성 그룹 DNN 수신기가 읽기 전용 라우팅을 지원 하나요? 

   예. 읽기 전용 라우팅은 DNN 수신기에서 지원 됩니다. 


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)

