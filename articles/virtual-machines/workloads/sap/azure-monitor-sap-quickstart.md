---
title: Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포
description: SAP 솔루션을 위한 Azure Monitor를 배포하기 위해 브라우저를 사용하는 방법을 알아봅니다.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 32ded30543e018b86f4329d7d08be100f84831d0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027511"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포

이 문서에서는 [Azure Portal](https://azure.microsoft.com/features/azure-portal)에서 SAP 솔루션을 위한 Azure Monitor를 배포하는 방법을 살펴보겠습니다. 포털의 브라우저 기반 인터페이스를 사용하여 SAP 솔루션을 위한 Azure Monitor를 배포하고 공급자를 구성합니다.

## <a name="sign-in-to-the-portal"></a>포털에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-monitoring-resource"></a>모니터링 리소스 만들기

1. **마켓플레이스** 에서 **SAP 솔루션을 위한 Azure Monitor** 를 선택합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Azure Marketplace에서 SAP 솔루션을 위한 Azure Monitor 제품 선택을 보여 주는 스크린샷." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. **기본 사항** 탭에서 필요한 값을 제공합니다. 해당하는 경우 기존 Log Analytics 작업 영역을 사용할 수 있습니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="기본 탭의 구성 옵션을 보여 주는 스크린샷." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   가상 네트워크를 선택하는 경우 해당 가상 네트워크 내에서 모니터링할 시스템에 연결할 수 있는지 확인합니다. 

   > [!IMPORTANT]
   > **Microsoft 지원팀과 데이터 공유** 에 대해 **공유** 를 선택하면 지원팀에서 문제 해결을 도울 수 있습니다.

## <a name="configure-providers"></a>공급자 구성

### <a name="sap-netweaver-provider"></a>SAP NetWeaver 공급자

SAP 시작 서비스는 SAP 시스템 모니터링을 비롯한 서비스의 호스트를 제공합니다. 이 기능을 공개하는 SOAP 웹 서비스 인터페이스인 SAPControl을 사용하고 있습니다. SAPControl 웹 서비스 인터페이스는 [보호된 및 보호되지 않는](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) 웹 서비스 메서드를 구별합니다. 

특정 메트릭을 가져오려면 현재 릴리스의 일부 메서드를 보호 해제해야 합니다. 각 SAP 시스템에 대해 다음 단계를 따르세요.

1. SAP 서버에 대한 SAP GUI 연결을 엽니다.
2. 관리 계정을 사용하여 로그인합니다.
3. 트랜잭션 RZ10을 실행합니다.
4. 적절한 프로필(*DEFAULT.PFL*)을 선택합니다.
5. **연장 유지 관리** > **변경** 을 선택합니다. 
6. 프로필 매개 변수 "service/protectedwebmethods"를 선택 하 고 다음 값을 갖도록 수정한 다음 복사를 클릭 합니다.  

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate ( replace the hostname , instance number and method name ) leverage the below powershell script 

```Powershell command to test unprotect method 
$SAPHostName = "<hostname>"
$InstanceNumber = "<instancenumber>"
$Function = "ABAPGetWPTable"
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
$sapcntrluri = "https://&quot; + $SAPHostName + &quot;:5&quot; + $InstanceNumber + &quot;14/?wsdl"
$sapcntrl = New-WebServiceProxy -uri $sapcntrluri -namespace WebServiceProxy -class sapcntrl
$FunctionObject = New-Object ($sapcntrl.GetType().NameSpace + ".$Function")
$sapcntrl.$Function($FunctionObject)
```
11. * * 각 인스턴스 프로필에 대해 3-10 단계를 반복 합니다.

>[!Important] 
>SAPControl 웹 메서드가 보호되지 않도록 하려면 SAP 시스템의 각 인스턴스에서 sapstartsrv 서비스를 다시 시작하는 것이 중요합니다.  이러한 읽기 전용 SOAP API는 NetWeaver 공급자가 SAP 시스템에서 메트릭 데이터를 가져오는 데 필요하며 이러한 메서드를 보호 해제하지 않으면 NetWeaver 메트릭 통합 문서에서 시각화가 비어 있거나 누락됩니다.
   
>[!Tip]
> ACL(액세스 제어 목록)을 사용하여 서버 포트에 대한 액세스를 필터링합니다. 자세한 내용은 [이 SAP Note](https://launchpad.support.sap.com/#/notes/1495075)를 참조하세요.

Azure Portal에 NetWeaver 공급자를 설치하려면:

1. 이전 필수 구성 요소 단계를 완료하고 서버가 다시 시작되었는지 확인합니다.
1. Azure Portal의 **SAP 솔루션을 위한 Azure Monitor** 에서 **공급자 추가** 를 선택한 후 다음을 수행합니다.

   1. **유형** 으로 **SAP NetWeaver** 를 선택합니다.

   1. **호스트 이름** 에 SAP 시스템의 호스트 이름을 입력합니다.

   1. **하위 도메인** 에 해당하는 경우 하위 도메인을 입력합니다.

   1. **인스턴스 번호** 에 입력한 호스트 이름에 해당하는 인스턴스 번호를 입력합니다. 

   1. **SID** 에 시스템 ID를 입력합니다.
   
   ![SAP NetWeaver 공급자를 추가하기 위한 구성 옵션을 보여 주는 스크린샷.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

>[!Important]
>SAP 애플리케이션 서버(예: 가상 머신)가 Azure Active Directory에서 관리하는 것과 같은 네트워크 도메인의 일부인 경우 해당 하위 도메인을 하위 도메인 텍스트 상자에 입력하는 것이 중요합니다.  가상 네트워크 내부에 있는 SAP 수집기 VM에 대한 Azure Monitor는 도메인에 가입되어 있지 않으므로 호스트 이름이 정규화된 도메인 이름이 아니면 SAP 시스템 내부의 인스턴스 호스트 이름을 확인할 수 없습니다.  이를 제공하지 않으면 NetWeaver 통합 문서에서 시각화가 누락되거나 불완전하게 됩니다.
 
>예를 들어 SAP 시스템의 호스트 이름에 정규화 된 도메인 이름 "myhost. mycompany"가 있는 경우 "myhost"의 호스트 이름을 입력 하 고 "mycompany. corp"의 하위 도메인을 제공 하세요.  NetWeaver 공급자가 SAP 시스템에서 GetSystemInstanceList API를 호출하면 SAP는 시스템에 있는 모든 인스턴스의 호스트 이름을 반환합니다.  수집기 VM은이 목록을 사용 하 여 추가 API 호출을 수행 하 여 각 인스턴스의 기능 (예:  ABAP, J2EE, MESSAGESERVER, 넣지, enqrep 등). 지정 하는 경우 수집기 VM은 하위 도메인 "mycompany. corp"를 사용 하 여 SAP 시스템에서 각 인스턴스의 정규화 된 도메인 이름을 작성 합니다.  
 
>SAP 시스템이 네트워크 도메인의 일부인 경우 호스트 이름 필드에 IP 주소를 지정하지 마세요.

   
### <a name="sap-hana-provider"></a>SAP HANA 공급자 

1. **공급자** 탭을 선택하여 구성하려는 공급자를 추가합니다. 여러 공급자를 차례로 추가하거나 모니터링 리소스를 배포한 후에 추가할 수 있습니다. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="공급자를 추가하는 탭을 보여 주는 스크린샷." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. **공급자 추가** 를 선택한 후 다음을 수행합니다.

   1. **유형** 으로 **SAP HANA** 를 선택합니다. 

      > [!IMPORTANT]
      > SAP HANA `master` 노드에 대해 SAP HANA 공급자가 구성되어 있는지 확인합니다.

   1. **IP 주소** 에 HANA 서버의 개인 IP 주소를 입력합니다.

   1. **데이터베이스 테넌트** 에 사용할 테넌트의 이름을 입력합니다. 모든 테넌트를 선택할 수 있지만, 더 광범위한 모니터링 영역을 사용할 수 있도록 **SYSTEMDB** 를 사용하는 것이 좋습니다. 

   1. **SQL 포트** 에 HANA 데이터베이스와 연결된 포트 번호를 입력합니다. *[3]*  +  *[인스턴스#]*  +  *[13]* 형식이어야 합니다. 예를 들어 **30013** 입니다. 

   1. **데이터베이스 사용자 이름** 에 사용할 사용자 이름을 입력합니다. 데이터베이스 사용자에게 *모니터링* 및 *카탈로그 읽기* 역할이 할당되어 있는지 확인합니다.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="SAP HANA 공급자를 추가하기 위한 구성 옵션을 보여 주는 스크린샷." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. 완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server 공급자

1. Microsoft SQL Server 공급자를 추가하기 전에 SQL Server Management Studio에서 다음 스크립트를 실행하여 공급자를 구성하기 위한 적절한 권한을 가진 사용자를 만듭니다.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

1. **공급자 추가** 를 선택한 후 다음을 수행합니다.

   1. **유형** 에서 **Microsoft SQL Server** 를 선택합니다. 

   1. SQL Server 인스턴스와 관련된 정보를 사용하여 나머지 필드를 채우세요. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Microsoft SQL Server 공급자를 추가하기 위한 구성 옵션을 보여 주는 스크린샷." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

1. 완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

### <a name="high-availability-cluster-pacemaker-provider"></a>고가용성 클러스터(Pacemaker) 공급자

고가용성(pacemaker) 클러스터에 대한 공급자를 추가하기 전에 환경에 적합한 에이전트를 설치합니다.

**SUSE** 기반 클러스터의 경우 각 노드에 ha_cluster_provider가 설치되어 있는지 확인합니다. [HA 클러스터 내보내기](https://github.com/ClusterLabs/ha_cluster_exporter#installation) 설치 방법을 참조하세요. 지원되는 SUSE 버전: SLES for SAP 12 SP3 이상.  
   
**RHEL** 기반 클러스터의 경우 PCP(Performance Co-pilot) 및 pcp-pmda-hacluster 하위 패키지가 각 노드에 설치되어 있는지 확인합니다. [PCP HACLUSTER 에이전트](https://access.redhat.com/articles/6139852) 설치 방법 보기. 지원되는 RHEL 버전: 8.2, 8.4 이상.
 
위의 필수 설치를 완료한 후 각 클러스터 노드에 대한 공급자를 만듭니다.

1. **공급자 추가** 를 선택한 후 다음을 수행합니다.

1. **유형** 으로 **고가용성 클러스터(Pacemaker)** 를 선택합니다. 
   
1. **HA 클러스터 내보내기 엔드포인트** 에 엔드포인트 URL을 입력하여 클러스터의 각 노드에 대한 공급자를 구성합니다. **SUSE** 기반 클러스터의 경우 **http://\<IP  address\>:9664/metrics** 를 입력합니다. **RHEL** 기반 클러스터의 경우 **http://\<IP address\>:44322/metrics?names=ha_cluster** 를 입력합니다.
 
1. 각 상자에 시스템 ID, 호스트 이름 및 클러스터 이름을 입력합니다.
   
   > [!IMPORTANT]
   > 호스트 이름은 VM의 실제 호스트 이름을 나타냅니다. SUSE 및 RHEL 기반 클러스터 모두에 "hostname -s" 명령을 사용하세요.  

1. 완료되면 **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나 **검토 + 만들기** 를 선택하여 배포를 완료합니다.

### <a name="os-linux-provider"></a>OS(Linux) 공급자 

1. **공급자 추가** 를 선택한 후 다음을 수행합니다.

   1. **유형** 으로 **OS(Linux)** 를 선택합니다. 

      >[!IMPORTANT]
      > OS(Linux) 공급자를 구성하려면 모니터링하려는 각 호스트(BareMetal 또는 가상 머신)에 [최신 버전의 node_exporter](https://prometheus.io/download/#node_exporter)가 설치되어 있는지 확인합니다. [자세히 알아보기](https://github.com/prometheus/node_exporter).

   1. **이름** 에 BareMetal 인스턴스의 식별자가 될 이름을 입력합니다.

   1. **노드 내보내기 엔드포인트** 에 **http://IP:9100/metrics** 를 입력합니다.

      >[!IMPORTANT]
      >Linux 호스트의 개인 IP 주소를 사용합니다. SAP 리소스를 위한 호스트와 Azure Monitor가 동일한 가상 네트워크에 있는지 확인합니다. 
      >
      >방화벽 포트 9100이 Linux 호스트에서 열려야 합니다. `firewall-cmd`를 사용하는 경우 다음 명령을 사용합니다. 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >`ufw`를 사용하는 경우 다음 명령을 사용합니다.
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > Linux 호스트가 Azure VM(가상 머신)인 경우 적용 가능한 모든 네트워크 보안 그룹이 `VirtualNetwork`의 포트 9100에서 인바운드 트래픽을 원본으로 허용하는지 확인합니다.
 
1. 완료되면  **공급자 추가** 를 선택합니다. 필요에 따라 다른 공급자를 계속 추가하거나  **검토 + 만들기** 를 선택하여 배포를 완료합니다. 


## <a name="next-steps"></a>다음 단계

SAP 솔루션을 위한 Azure Monitor에 관해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure의 SAP 모니터링](monitor-sap-on-azure.md)
