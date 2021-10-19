---
title: 도메인 독립 작업 그룹 가용성 그룹 만들기
description: Azure의 SQL Server 가상 머신에서 Active Directory Domain Services 도메인 독립 작업 그룹 AlwaysOn 가용성 그룹을 구성하는 방법에 대해 알아보세요.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 6eb8af995b733fb796827d508ee003d83e46c67f
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167295"
---
# <a name="configure-a-workgroup-availability-group"></a>작업 그룹 가용성 그룹 구성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 작업 그룹 클러스터라고도 하는 Always On 가용성 그룹을 사용하여 Active Directory 도메인 독립 클러스터를 만드는 데 필요한 단계를 설명합니다. 이 문서에서는 작업 그룹과 가용성 그룹 준비 및 구성과 관련된 단계를 중점적으로 설명하고, 클러스터를 만들거나 가용성 그룹을 배포하는 방법과 같이 다른 문서에서 다루는 단계를 간단히 살펴봅니다. 


## <a name="prerequisites"></a>필수 구성 요소

작업 그룹 가용성 그룹을 구성하려면 다음이 필요합니다.
- 고정 IP 주소를 사용하여 동일한 가용성 집합 또는 다른 가용성 영역에 배포된 SQL Server 2016 이상을 실행하는 Windows Server 2016 이상의 가상 머신 최소 2개. 
- 서브넷에 최소 4개의 사용 가능한 IP 주소가 있는 로컬 네트워크. 
- SQL Server 내에서 sysadmin 권한도 있는 관리자 그룹의 각 컴퓨터에 대한 계정. 
- 열린 포트: TCP 1433, TCP 5022, TCP 59999. 

참고로 이 문서에서는 다음 매개 변수를 사용하지만 필요에 따라 수정할 수 있습니다. 

| **이름** | **매개 변수** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1(10.0.0.4) |
| **Node2**   | AGNode2(10.0.0.5) |
| **클러스터 이름** | AGWGAG(10.0.0.6) |
| **수신기** | AGListener(10.0.0.7) | 
| **DNS 접미사** | ag.wgcluster.example.com | 
| **작업 그룹 이름** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>DNS 접미사 설정 

이 단계에서는 두 서버 모두에 대한 DNS 접미사를 구성합니다. 예들 들어 `ag.wgcluster.example.com`입니다. 그러면 연결하려는 개체의 이름을 네트워크 내의 정규화된 주소(예: `AGNode1.ag.wgcluster.example.com`)로 사용할 수 있습니다. 

DNS 접미사를 구성하려면 다음 단계를 수행합니다.

1. 첫 번째 노드에 RDP를 활성화하고서버 관리자를 여세요. 
1. **로컬 서버** 를 선택하고 **컴퓨터 이름** 에서 가상 머신의 이름을 선택합니다. 
1. **이 컴퓨터 이름을 바꾸려면...** 에서 **변경...** 을 선택합니다. 
1. 작업 그룹 이름을 `AGWORKGROUP`과 같은 의미 있는 이름으로 변경합니다. 

   ![작업 그룹 이름 변경](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. **자세히...** 를 선택하여 **DNS 접미사 및 NetBIOS 컴퓨터 이름** 대화 상자를 엽니다. 
1. **이 컴퓨터의 주 DNS 접미사** 아래에 DNS 접미사의 이름(예: `ag.wgcluster.example.com`)을 입력하고 **확인** 을 선택합니다. 

   ![스크린샷에 값을 입력할 수 있는 DNS 접미사와 NetBIOS 컴퓨터 이름 대화 상자가 표시됩니다.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. **전체 컴퓨터 이름** 에 DNS 접미사가 표시되는지 확인하고 **확인** 을 선택하여 변경 내용을 저장합니다. 

   ![스크린샷에 전체 컴퓨터 이름을 볼 수 있는 위치가 표시됩니다.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. 지시에 따라 서버를 다시 부팅합니다. 
1. 가용성 그룹에 사용할 다른 모든 노드에서 이 단계를 반복합니다. 

## <a name="edit-a-host-file"></a>호스트 파일 편집

Active Directory가 없으므로 Windows 연결을 인증할 방법이 없습니다. 따라서 텍스트 편집기로 호스트 파일을 편집하여 신뢰를 할당하십시오. 

호스트 파일을 편집하려면 다음 단계를 수행합니다.

1. 가상 머신에 RDP를 활성화하세요. 
1. **파일 탐색기** 를 사용하여 `c:\windows\system32\drivers\etc`로 이동합니다. 
1. **호스트** 파일을 마우스 오른쪽 단추로 클릭하고 **메모장** 이나 다른 텍스트 편집기로 파일을 엽니다.
1. 파일 끝에 다음과 같이 `IP Address, DNS Suffix #comment` 형식으로 각 노드, 가용성 그룹 및 수신기에 대한 항목을 추가합니다. 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![호스트 파일에 IP 주소, 클러스터 및 수신기에 대한 항목 추가](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>권한 설정

사용 권한 관리를 위한 Active Directory가 없으므로 기본 제공되지 않은 로컬 관리자 계정이 클러스터를 만들도록 수동으로 허용해야 합니다. 

이렇게 하려면 모든 노드의 관리 PowerShell 세션에서 다음 PowerShell cmdlet을 실행합니다. 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기

이 단계에서는 장애 조치(failover) 클러스터를 만듭니다. 이 단계에 익숙하지 않은 경우 [장애 조치(failover) 클러스터 자습서](failover-cluster-instance-storage-spaces-direct-manually-configure.md)에서 해당 단계를 수행할 수 있습니다.

자습서와 작업 그룹 클러스터에 대해 수행할 작업의 중요한 차이점은 다음과 같습니다.
- 클러스터 유효성 검사를 실행할 때 **스토리지** 및 **스토리지 공간 다이렉트** 선택을 취소합니다. 
- 클러스터에 노드를 추가할 때 다음과 같이 정규화된 이름을 추가합니다.
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- **클러스터에 사용할 수 있는 모든 스토리지를 추가하세요.** 선택을 취소합니다. 

클러스터가 만들어지면 고정 클러스터 IP 주소를 할당합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 노드 중 하나에서 **장애 조치(failover) 클러스터 관리자** 를 열고, 클러스터를 선택하고, **클러스터 코어 리소스** 에서 **이름:\<ClusterNam>** 을 선택한 다음, **속성** 을 선택합니다. 

   ![클러스터 이름의 시작 속성](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. **IP 주소** 에서 IP 주소를 선택하고 **편집** 을 선택합니다. 
1. **고정 주소 사용** 을 선택하고 클러스터의 IP 주소를 제공한 다음, **확인** 을 선택합니다. 

   ![클러스터의 고정 IP 주소 제공](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. 설정이 올바른지 확인한 다음, **확인** 을 선택하여 설정을 저장합니다.

   ![클러스터 속성 확인](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>클라우드 감시 만들기 

이 단계에서는 클라우드 공유 감시를 구성합니다. 단계가 익숙하지 않은 경우 [장애 조치(failover) 클러스터용 클라우드 감시 배포](/windows-server/failover-clustering/deploy-cloud-witness)를 참조하세요. 

## <a name="enable-the-availability-group-feature"></a>가용성 그룹 기능 사용 

이 단계에서는 가용성 그룹 기능을 사용하도록 설정합니다. 이 단계에 익숙하지 않은 경우 [가용성 그룹 자습서](availability-group-manually-configure-tutorial.md#enable-availability-groups)를 참조하세요. 

## <a name="create-keys-and-certificates"></a>키 및 인증서 생성

이 단계에서는 SQL 로그인이 암호화된 엔드포인트에서 사용하는 인증서를 만듭니다. 각 노드에 인증서 백업을 저장할 폴더(예: `c:\certs`)를 만듭니다. 

첫 번째 노드를 구성하려면 다음 단계를 수행합니다. 

1. **SQL Server Management Studio** 를 열고 `AGNode1` 등의 첫 번째 노드에 연결합니다. 
1. 복잡하고 안전한 암호로 업데이트한 후 **새 쿼리** 창을 열고 다음 T-SQL(Transact-SQL) 문을 실행합니다.

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. 그런 다음, HADR 엔드포인트를 만들고 이 T-SQL(Transact-SQL) 문을 실행하여 인증에 인증서를 사용합니다.

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **파일 탐색기** 를 사용하여 인증서가 있는 파일 위치(예: `c:\certs`)로 이동합니다. 
1. 첫 번째 노드에서 `AGNode1Cert.crt`와 같은 인증서의 사본을 수동으로 만들고 두 번째 노드의 동일한 위치로 전송합니다. 

두 번째 노드를 구성하려면 다음 단계를 수행합니다. 

1. **SQL Server Management Studio** 를 사용하여 두 번째 노드(예: `AGNode2`)에 연결합니다. 
1. 복잡하고 안전한 암호로 업데이트한 후 **새 쿼리** 창에서 다음 T-SQL(Transact-SQL) 문을 실행합니다. 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. 그런 다음, HADR 엔드포인트를 만들고 이 T-SQL(Transact-SQL) 문을 실행하여 인증에 인증서를 사용합니다.

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **파일 탐색기** 를 사용하여 인증서가 있는 파일 위치(예: `c:\certs`)로 이동합니다. 
1. 두 번째 노드에서 `AGNode2Cert.crt`와 같은 인증서의 사본을 수동으로 만들고 첫 번째 노드의 동일한 위치로 전송합니다. 

클러스터에 다른 노드가 있는 경우 해당 인증서 이름을 수정하여 이 단계를 반복합니다. 

## <a name="create-logins"></a>로그인 만들기

인증서 인증은 여러 노드 간에 데이터를 동기화하는 데 사용됩니다. 이를 허용하려면 다른 노드에 대한 로그인을 만들고, 로그인의 사용자를 만들고, 백업된 인증서 사용을 위한 로그인용 인증서를 만든 다음, 미러링 엔드포인트에 연결을 부여합니다. 

이렇게 하려면 먼저 첫 번째 노드(예: `AGNode1`)에서 다음 T-SQL(Transact-SQL) 쿼리를 실행합니다. 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

그런 다음, 두 번째 노드(예: `AGNode2`)에서 다음 T-SQL(Transact-SQL) 쿼리를 실행합니다. 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

클러스터에 다른 노드가 있는 경우 해당 인증서와 사용자 이름을 수정하여 이 단계를 반복합니다. 

## <a name="configure-an-availability-group"></a>가용성 그룹 구성

이 단계에서는 가용성 그룹을 구성하고 데이터베이스를 추가합니다. 지금은 수신기를 만들지 않습니다. 이 단계에 익숙하지 않은 경우 [가용성 그룹 자습서](availability-group-manually-configure-tutorial.md#create-the-availability-group)를 참조하세요. 장애 조치(failover)와 장애 복구(failback)를 시작하여 모두 제대로 작동하는지 확인해야 합니다. 

   > [!NOTE]
   > 동기화 프로세스 중에 오류가 발생하는 경우 임시로 `NT AUTHORITY\SYSTEM`에 sysadmin 권한을 부여하여 `AGNode1`과 같은 첫 번째 노드에 클러스터 리소스를 만들어야 할 수 있습니다. 

## <a name="configure-a-load-balancer"></a>부하 분산 장치 구성

마지막 단계에서는 [Azure Portal](availability-group-load-balancer-portal-configure.md) 또는 [PowerShell](availability-group-listener-powershell-configure.md)을 사용하여 부하 분산 장치를 구성하세요.


## <a name="next-steps"></a>다음 단계

가용성 그룹이 배포되면 [Azure VM에서 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)을 최적화하는 것이 좋습니다. 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Always On 가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
