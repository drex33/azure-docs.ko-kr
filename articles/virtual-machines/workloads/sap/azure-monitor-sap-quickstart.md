---
title: Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포
description: SAP 솔루션을 위한 Azure Monitor를 배포하기 위해 브라우저를 사용하는 방법을 알아봅니다.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 0f5b2c2d94a2b0e106bf0541e080cfa9d05b45ac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441137"
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

11. **Repeat Steps 3-10 for each instance profile **.

>[!Important] 
>It is critical that the sapstartsrv service is restarted on each instance of the SAP system for the SAPControl web methods to be unprotected.  These read-only SOAP API are required for the NetWeaver provider to fetch metric data from the SAP System and failure to unprotect these methods will lead to empty or missing visualizations on the NetWeaver metric workbook.
   
>[!Tip]
> Use an access control list (ACL) to filter the access to a server port. For more information, see [this SAP note](https://launchpad.support.sap.com/#/notes/1495075).

To install the NetWeaver provider on the Azure portal:

1. Make sure you've completed the earlier prerequisite steps and that the server has been restarted.
1. On the Azure portal, under **Azure Monitor for SAP Solutions**, select **Add provider**, and then:

   1. For **Type**, select **SAP NetWeaver**.

   1. For **Hostname**, enter the host name of the SAP system.

   1. For **Subdomain**, enter a subdomain if one applies.

   1. For **Instance No**, enter the instance number that corresponds to the host name you entered. 

   1. For **SID**, enter the system ID.
   
   ![Screenshot showing the configuration options for adding a SAP NetWeaver provider.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

>[!Important]
>If the SAP application servers (ie. virtual machines) are part of a network domain, such as one managed by Azure Active Directory, then it is critical that the corresponding subdomain is provided in the Subdomain text box.  The Azure Monitor for SAP collector VM that exists inside the Virtual Network is not joined to the domain and as such will not be able to resolve the hostname of instances inside the SAP system unless the hostname is a fully qualified domain name.  Failure to provide this will result in missing / incomplete visualizations in the NetWeaver workbook.
 
>For example, if the hostname of the SAP system has a fully qualified domain name of "myhost.mycompany.global.corp" then please enter a Hostname of "myhost" and provide a Subdomain of "mycompany.global.corp".  When the NetWeaver provider invokes the GetSystemInstanceList API on the SAP system, SAP returns the hostnames of all instances in the system.  The collector VM will use this list to make additional API calls to fetch metrics specific to each instance's features (e.g.  ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc…). If specified, the collector VM will then use the subdomain  "mycompany.global.corp" to build the fully qualified domain name of each instance in the SAP system.  
 
>Please DO NOT specify an IP Address for the hostname field if the SAP system is a part of network domain.

   
### SAP HANA provider 

1. Select the **Providers** tab to add the providers you want to configure. You can add multiple providers one after another, or add them after you deploy the monitoring resource. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot showing the tab where you add providers." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Select **Add provider**, and then:

   1. For **Type**, select **SAP HANA**. 

      > [!IMPORTANT]
      > Ensure that a SAP HANA provider is configured for the SAP HANA `master` node.

   1. For **IP address**, enter the private IP address for the HANA server.

   1. For **Database tenant**, enter the name of the tenant you want to use. You can choose any tenant, but we recommend using **SYSTEMDB** because it enables a wider array of monitoring areas. 

   1. For **SQL port**, enter the port number associated with your HANA database. It should be in the format of *[3]* + *[instance#]* + *[13]*. An example is **30013**. 

   1. For **Database username**, enter the username you want to use. Ensure the database user has the *monitoring* and *catalog read* roles assigned.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot showing configuration options for adding an SAP HANA provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

   
### Microsoft SQL Server provider

1. Before you add the Microsoft SQL Server provider, run the following script in SQL Server Management Studio to create a user with the appropriate permissions for configuring the provider.

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
