---
title: SQL Server FCI 및 DNN의 기능 상호 운용성
description: 'Azure VM의 SQL Server 장애 조치(failover) 클러스터 인스턴스를 사용하여 특정 SQL Server 기능 및 DNN(분산 네트워크 이름) 리소스를 사용할 때의 추가 고려 사항에 대해 알아봅니다. '
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
ms.openlocfilehash: adacd4e8264b7fcd4c0389f8d575247a17a664aa
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158971"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>SQL Server FCI 및 DNN의 기능 상호 운용성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 동일한 Azure 가상 네트워크 내의 여러 서브넷에 SQL Server VM을 만들어 장애 조치(failover) 클러스터 인스턴스에 대한 DNN(분산 네트워크 이름)이 필요하지 않습니다.

하드 코딩된 VNN(가상 네트워크 이름)을 사용하는 특정 SQL Server 기능이 있습니다. 따라서 DNN(분산 네트워크 이름) 리소스를 Azure VM의 장애 조치(failover) 클러스터 인스턴스 및 SQL Server와 사용할 때 몇 가지 추가 고려 사항이 있습니다. 

이 문서에서는 DNN 리소스를 사용할 때 네트워크 별칭을 구성하는 방법과 추가 고려 사항이 필요한 SQL Server 기능에 대해 알아봅니다.

## <a name="create-network-alias-fci"></a>네트워크 별칭 만들기(FCI)

일부 서버 측 구성 요소는 하드 코딩된 VNN 값을 사용하며 제대로 작동하려면 DNN DNS 이름에 VNN을 매핑하는 네트워크 별칭이 필요합니다. [서버 별칭 만들기](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client)의 단계에 따라 DNN DNS 이름에 VNN을 매핑하는 별칭을 만듭니다. 

기본 인스턴스의 경우 VNN = DNN DNS 이름과 같이 DNN DNS 이름에 VNN을 직접 매핑할 수 있습니다.
예를 들어 VNN 이름이 `FCI1`이고, 인스턴스 이름이 `MSSQLSERVER`, 그리고 DNN이 `FCI1DNN`(클라이언트가 이전에는 `FCI`에 연결하고, 현재는 `FCI1DNN`에 연결)이면 VNN `FCI1`을 DNN `FCI1DNN`에 매핑합니다. 

명명된 인스턴스의 경우 `VNN\Instance` = `DNN\Instance`과 같은 전체 인스턴스에 대해 네트워크 별칭 매핑을 수행해야합니다. 예를 들어 VNN 이름이 `FCI1`이고 인스턴스 이름이 `instA`이고 DNN이 `FCI1DNN`(이전에 `FCI1\instA`에 연결된 클라이언트가 이제 `FCI1DNN\instaA`에 연결됨)인 경우 VNN `FCI1\instaA`을 DNN `FCI1DNN\instaA`에 매핑합니다. 



## <a name="client-drivers"></a>클라이언트 드라이버

ODBC, OLEDB, ADO.NET, JDBC, PHP 및 Node.js 드라이버의 경우 사용자는 연결 문자열의 서버 이름으로 DNN DNS 이름을 명시적으로 지정해야 합니다. 장애 조치(failover) 시 빠른 연결을 보장하려면 SQL 클라이언트에서 지원하는 경우 연결 문자열에 `MultiSubnetFailover=True`를 추가합니다. 

## <a name="tools"></a>도구

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is) 및 [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) 사용자는 연결 문자열의 서버 이름으로 DNN DNS 이름을 명시적으로 지정해야 합니다. 

## <a name="availability-groups-and-fci"></a>가용성 그룹 및 FCI

장애 조치(failover) 클러스터 인스턴스를 복제본 중 하나로 사용하여 Always On 가용성 그룹을 구성할 수 있습니다. 이 구성에서 FCI 복제본의 미러링 엔드포인트 URL은 FCI DNN를 사용해야 합니다. 마찬가지로 FCI가 읽기 전용 복제본으로 사용되는 경우 FCI 복제본에 대한 읽기 전용 라우팅은 FCI DNN을 사용해야 합니다. 

미러링 엔드포인트의 형식은 `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` 입니다. 

예를 들어 DNN DNS 이름이 `dnnlsnr`이고 `5022`가 FCI 미러링 엔드포인트의 포트인 경우 엔드포인트 URL을 생성하는 T-SQL(Transact-SQL) 코드 조각은 다음과 같습니다: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

마찬가지로 읽기 전용 라우팅 URL의 형식은 `TCP://<DNN DNS name>:<SQL Server instance port>` 입니다. 

예를 들어 DNN DNS 이름이 `dnnlsnr`이고 `1444`가 읽기 전용 대상 SQL Server FCI에서 사용하는 포트인 경우 읽기 전용 라우팅 URL을 만드는 T-SQL 코드 조각은 다음과 같습니다: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

기본 1433 포트인 경우 URL에서 포트를 생략할 수 있습니다. 명명된 인스턴스의 경우 명명된 인스턴스에 대한 고정 포트를 구성하고 읽기 전용 라우팅 URL에 지정합니다.  

## <a name="replication"></a>복제

복제는 게시자, 배포자와 구독자의 세 가지 구성 요소로 이루어져 있습니다. 이러한 구성 요소는 장애 조치(failover) 클러스터 인스턴스일 수 있습니다. FCI VNN은 명시적 및 암시적으로 복제 구성에 많이 사용되기 때문에 복제 작업을 수행하려면 VNN을 DNN에 매핑하는 네트워크 별칭이 필요할 수 있습니다. 

복제 내에서 FCI 이름으로 VNN 이름을 계속 사용하되 *복제를 구성하기 전에* 다음 원격 상황에서 네트워크 별칭을 만듭니다:

| **복제 구성 요소(DNN이 있는 FCI)** | **원격 구성 요소** | **네트워크 별칭 매핑** | **네트워크 맵이 있는 서버**| 
|---------|---------|---------|-------- | 
|게시자 | 배포자 | 게시자 VNN에서 게시자 DNN으로| 배포자| 
|배포자|가입자 |배포자 VNN에서 배포자 DNN으로| 가입자 | 
|배포자|게시자 | 배포자 VNN에서 배포자 DNN으로 | 게시자| 
|가입자| 배포자| 가입자 VNN에서 가입자 DNN으로 | 배포자| 

예를 들어 복제 토폴로지에서 DNN을 사용하여 FCI로 구성된 게시자가 있고 배포자가 원격이라고 가정합니다. 이 경우 배포자 서버에서 네트워크 별칭을 만들어 게시자 VNN을 게시자 DNN에 매핑합니다: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="SQL Server 구성 관리자를 사용하여 DNN DNS 이름을 네트워크 별칭으로 구성합니다." :::

다음 이미지 예제와 같이 명명된 인스턴스의 전체 인스턴스 이름을 사용합니다: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="명명된 인스턴스에 대한 네트워크 별칭을 구성할 때 전체 인스턴스 이름을 사용합니다." :::

## <a name="database-mirroring"></a>데이터베이스 미러링

FCI를 사용하여 데이터베이스 미러링 파트너로 데이터베이스 미러링을 구성할 수 있습니다. SQL Server Management Studio GUI가 아닌 [T-SQL(Transact-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql)을 사용하여 구성합니다. T-SQL을 사용하면 VNN 대신 DNN을 사용하여 데이터베이스 미러링 엔드포인트가 만들어집니다. 

예를 들어 DNN DNS 이름이 `dnnlsnr`이고 데이터베이스 미러링 엔드포인트가 7022인 경우 다음 T-SQL 코드 조각은 데이터베이스 미러링 파트너를 구성합니다: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

클라이언트 액세스의 경우 **장애 조치(failover) 파트너** 속성은 데이터베이스 미러링 장애 조치(failover)를 처리할 수 있지만 FCI 장애 조치(failover)는 처리할 수 없습니다. 

## <a name="msdtc"></a>MSDTC

FCI는 MSDTC(Microsoft Distributed Transaction Coordinator)에서 조정하는 분산 트랜잭션에 참여할 수 있습니다. 클러스터된 MSDTC와 로컬 MSDTC는 모두 FCI DNN에서 지원되지만 Azure에서는 클러스터형 MSDTC에 여전히 부하 분산 장치가 필요합니다. FCI에 정의된 DNN은 Azure의 클러스터형 MSDTC에 대한 Azure Load Balancer 요구 사항을 대체하지 않습니다. 

## <a name="filestream"></a>FileStream

FCI의 데이터베이스에 대해 FileStream이 지원되지만 DNN과 함께 파일 시스템 API를 사용하여 FileStream 또는 FileTable에 액세스하는 것은 지원되지 않습니다. 

## <a name="linked-servers"></a>연결된 서버

FCI DNN과 연결된 서버를 사용할 수 있습니다. DNN을 직접 사용하여 연결된 서버를 구성하거나 네트워크 별칭을 사용하여 VNN을 DNN에 매핑합니다. 


예를 들어 명명된 인스턴스 `insta1`에 대해 DNN DNS 이름이 `dnnlsnr`인 연결된 서버를 만들려면 다음 T-SQL(Transact-SQL) 명령을 사용합니다:

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

또는 VNN(가상 네트워크 이름)을 사용하여 연결된 서버를 만들 수 있지만 그런 다음 VNN을 DNN에 매핑하려면 네트워크 별칭을 정의해야 합니다. 

예를 들어 인스턴스 이름 `insta1`, VNN 이름 `vnnname` 및 DNN 이름 `dnnlsnr`의 경우 다음 T-SQL(Transact-SQL) 명령을 사용하여 VNN을 사용하는 연결된 서버를 만듭니다:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

그런 다음 `vnnname\insta1`을 `dnnlsnr\insta1`에 매핑할 네트워크 별칭을 만듭니다. 



## <a name="frequently-asked-questions"></a>질문과 대답


- 어떤 SQL Server 버전에서 DNN을 지원하나요? 

   SQL Server 2019 CU2 이상 버전에서 지원합니다.

- DNN을 사용할 때 예상되는 장애 조치(failover) 시간은 어느 정도입니까?

   DNN을 사용할 때 예상되는 장애 조치(failover) 시간의 경우 시간이 추가되지 않은 FCI 장애 조치(failover) 시간입니다(예: Azure Load Balancer를 사용하는 경우의 프로브 시간).

- SQL 클라이언트에서 OLEDB 및 ODBC와 함께 DNN을 지원하기위한 버전 요구 사항이 있나요?

   DNN에 대한 `MultiSubnetFailover=True` 연결 문자열 지원을 권장합니다. SQL Server 2012 (11.x)부터 사용할 수 있습니다.

- DNN을 사용하려면 SQL Server 구성을 변경해야 하나요? 

   DNN을 사용하기 위해 SQL Server 구성을 변경할 필요는 없지만 일부 SQL Server 기능은 더 많은 고려 사항이 필요할 수 있습니다. 

- DNN은 다중 서브넷 클러스터를 지원하나요?

   예. 클러스터는 서브넷에 관계없이 클러스터에 있는 모든 노드의 실제 IP 주소로 DNS의 DNN을 바인딩합니다. SQL 클라이언트는 서브넷에 관계없이 DNS 이름의 모든 IP 주소를 시도합니다. 



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)

