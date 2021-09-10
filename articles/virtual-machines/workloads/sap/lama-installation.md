---
title: Azure용 SAP LaMa 커넥터 | Microsoft Docs
description: SAP LaMa를 사용하여 Azure에서 SAP 시스템 관리
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.custom: subject-rbac-steps
ms.openlocfilehash: bebae71179153479d66891924ba2f7679838d8bc
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112297048"
---
# <a name="sap-lama-connector-for-azure"></a>Azure용 SAP LaMa 커넥터

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> SAP LaMa 또는 Azure 커넥터에 대한 지원이 필요한 경우 항상 구성 요소 BC-VCM-LVM-HYPERV에서 SAP에 대한 인시던트를 열어주세요.

SAP LaMa는 많은 고객이 SAP 환경을 운영 및 모니터링하는 데 사용됩니다. SAP LaMa 3.0 SP05부터 Azure에 대한 커넥터가 기본적으로 제공됩니다. 이 커넥터를 사용하여 가상 머신을 할당 해제하여 시작하고, 관리 디스크를 복사하여 재배치하고, 관리 디스크를 삭제할 수 있습니다. 이러한 기본 작업을 통해 SAP LaMa를 사용하여 SAP 시스템을 재배치, 복사, 복제 및 새로 고칠 수 있습니다.

이 가이드에서는 SAP LaMa용 Azure 커넥터를 설정하고 적응형 SAP 시스템을 설치하는 데 사용할 수 있는 가상 머신을 만들고 구성 방법을 설명합니다.

> [!NOTE]
> 커넥터는 SAP LaMa Enterprise Edition에서만 사용할 수 있습니다.

## <a name="resources"></a>리소스

다음 SAP 정보는 Azure의 SAP LaMa 항목과 관련이 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [2343511] |SAP LaMa(Landscape Management)용 Microsoft Azure 커넥터 |
| [2350235] |SAP Landscape Management 3.0 - Enterprise Edition |

[SAP LaMa에 대한 SAP Help Portal](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)도 참조하세요.

## <a name="general-remarks"></a>일반적인 설명

* 설치 -> 설정 -> 엔진에서 *Automatic Mountpoint Creation*(자동 탑재 지점 만들기)을 사용하도록 설정해야 합니다.  
  SAP LaMa가 가상 머신에서 SAP Adaptive Extensions를 사용하여 볼륨을 마운트하는 경우, 이 설정을 사용하지 않으면 마운트 지점이 있어야 합니다.

* 새 VM을 배포할 때 및 SAP 인스턴스가 준비가 되지 않은 경우에는 별도의 서브넷을 사용하며 IP 주소가 "도용"되지 않도록 동적 IP 주소를 사용하지 않습니다.  
  SAP LaMa에서도 사용되는 서브넷에 동적 IP 주소 할당을 사용하면 SAP LaMa를 통한 SAP 시스템 준비에 실패할 수 있습니다. SAP 시스템이 준비되지 않으면 IP 주소가 예약되지 않으며 다른 가상 머신에 할당될 수 있습니다.

* 관리 호스트에 로그인하는 경우 파일 시스템의 마운트 해제를 차단하지 마세요.  
  Linux 가상 머신에 로그인하여 작업 디렉터리를 마운트 포인트의 디렉터리(예: /usr/sap/AH1/ASCS00/exe)로 변경하면 볼륨을 마운트 해제할 수 없으며 재배치 또는 준비 취소가 실패합니다.

* SUSE SLES Linux 가상 머신에서 CLOUD_NETCONFIG_MANAGE를 사용하지 않도록 설정해야 합니다. 자세한 내용은 [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)을 참조하세요.

## <a name="set-up-azure-connector-for-sap-lama"></a>SAP LaMa용 Azure 커넥터 설정

Azure 커넥터는 SAP LaMa 3.0 SP05부터 제공됩니다. SAP LaMa 3.0용 최신 지원 패키지 및 패치를 항상 설치하는 것이 좋습니다.

Azure 커넥터는 Azure Resource Manager API를 사용하여 Azure 리소스를 관리합니다. SAP LaMa는 서비스 주체 또는 관리 ID를 사용하여 이 API에 인증할 수 있습니다. SAP LaMa가 Azure VM에서 실행되는 경우 [관리 ID를 사용하여 Azure API에 대한 액세스 권한 얻기](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) 챕터에 설명된 대로 관리 ID를 사용하는 것이 좋습니다. 서비스 주체를 사용하려면 [서비스 주체를 사용하여 Azure API에 대한 액세스 권한 얻기](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e) 챕터의 단계를 따르세요.

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>서비스 주체를 사용하여 Azure API에 대한 액세스 권한 얻기

Azure 커넥터는 서비스 주체를 사용하여 Microsoft Azure에 대해 권한을 부여합니다. 다음 단계에 따라 SAP LaMa(Landscape Management)에 대한 서비스 주체를 생성합니다.

1. [https://editor.swagger.io](https://portal.azure.com) 으로 이동합니다.
1. Azure Active Directory 블레이드 열기
1. 앱 등록을 클릭합니다.
1. 새 등록을 클릭합니다.
1. 이름을 입력하고 등록을 클릭합니다.
1. 새 앱을 선택하고 설정 탭에서 인증서 및 비밀을 클릭합니다.
1. 새 클라이언트 암호를 만들고, 새 키에 대한 설명을 입력하고, 비밀이 만료되는 시점을 선택하고, 저장을 클릭합니다.
1. 값을 기록해 둡니다. 서비스 주체의 암호로 사용됩니다.
1. 애플리케이션 ID를 적어둡니다. 서비스 주체의 사용자 이름으로 사용됩니다.

서비스 주체에는 기본적으로 Azure 리소스에 액세스할 권한이 없습니다.
SAP LaMa에서 관리해야 하는 SAP 시스템이 포함된 모든 리소스 그룹의 리소스 그룹 범위에서 서비스 주체에게 기여자 역할을 할당합니다.

세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>관리 ID를 사용하여 Azure API에 대한 액세스 권한 얻기

관리 ID를 사용하려면 SAP LaMa 인스턴스가 시스템 또는 사용자가 할당한 ID가 있는 Azure VM에서 실행되어야 합니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../../active-directory/managed-identities-azure-resources/overview.md) 및 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)을 참조하세요.

관리 ID에는 기본적으로 Azure 리소스에 액세스할 권한이 없습니다.
SAP LaMa에서 관리해야 하는 SAP 시스템이 포함된 모든 리소스 그룹의 리소스 그룹 범위에서 가상 머신 ID에 기여자 역할을 할당합니다.

세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

SAP LaMa Azure 커넥터 구성에서 '관리 ID 사용'을 선택하여 관리 ID를 사용하도록 설정합니다. 시스템 할당 ID를 사용하려면 사용자 이름 필드를 비워 두어야 합니다. 사용자 할당 ID를 사용하려면 사용자 이름 필드에 사용자 할당 ID를 입력합니다.

### <a name="create-a-new-connector-in-sap-lama"></a>SAP LaMa에서 새 커넥터 만들기

SAP LaMa 웹 사이트를 열고 Infrastructure(인프라)로 이동합니다. Cloud Managers(클라우드 관리자) 탭으로 이동하여 Add(추가)를 클릭합니다. Microsoft Azure 클라우드 어댑터를 선택하고 다음을 클릭합니다. 다음 정보를 입력합니다.

* Label(레이블): 커넥터 인스턴스의 이름 선택
* 사용자 이름: 서비스 주체 애플리케이션 ID 또는 가상 머신의 사용자 할당 ID입니다. 자세한 내용은 [시스템 또는 사용자 할당 ID 사용]을 참조하세요.
* 암호: 서비스 주체 키/암호입니다. 시스템 또는 사용자 할당 ID를 사용하는 경우 이 필드를 비워 둘 수 있습니다.
* URL: 기본값 `https://management.azure.com/` 유지
* Monitoring Interval (Seconds)(모니터링 간격(초)): 300 이상이어야 합니다.
* 관리 ID 사용: SAP LaMa는 시스템 또는 사용자 할당 ID를 사용하여 Azure API에 인증할 수 있습니다. 이 가이드에서 [관리 ID를 사용하여 Azure API에 대한 액세스 권한 가져오기](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d)를 참조하세요.
* Subscription ID(구독 ID): Azure 구독 ID
* Azure Active Directory Tenant ID(테넌트 ID): Active Directory의 테넌트 ID
* Proxy host(프록시 호스트): SAP LaMa가 인터넷에 연결하는 데 프록시가 필요한 경우 프록시의 호스트 이름
* Proxy port(프록시 포트): 프록시의 TCP 포트
* 비용 절감을 위해 스토리지 유형 변경: 디스크가 사용되고 있지 않을 때 Azure 어댑터가 Managed Disks의 스토리지 유형을 변경하여 비용을 절감해야 하는 경우 이 설정을 사용하도록 설정합니다. SAP 인스턴스 구성에서 참조되는 데이터 디스크의 경우, 어댑터가 인스턴스 준비 취소 중에 디스크 유형을 Standard Storage로 변경하고, 인스턴스 준비 중에 다시 원래 스토리지 유형으로 변경합니다. SAP LaMa에서 가상 머신을 중지하면 어댑터가 OS 디스크를 포함한 모든 연결된 디스크의 스토리지 유형을 Standard Storage로 변경합니다. SAP LaMa에서 가상 머신을 시작하면 어댑터가 스토리지 유형을 원래 스토리지 유형으로 다시 변경합니다.

[테스트 구성]을 클릭하여 사용자 입력의 유효성을 검사합니다. 다음과 같은 결과가 표시됩니다.

연결 성공: Microsoft 클라우드 연결에 성공했습니다. 리소스 그룹을 7개 찾았습니다(그룹 10개만 요청함)

웹 사이트 아래쪽에 표시됩니다.

## <a name="provision-a-new-adaptive-sap-system"></a>새 적응형 SAP 시스템 프로비전

새 가상 머신을 수동으로 배포하거나 [빠른 시작 리포지토리](https://github.com/Azure/azure-quickstart-templates)에 있는 Azure 템플릿 중 하나를 사용할 수 있습니다. [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/sap/sap-lama-ascs), [SAP NetWeaver Application Server](https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/sap/sap-lama-apps) 및 [데이터베이스](https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/sap/sap-lama-database)에 대한 템플릿을 포함합니다. 이 템플릿을 사용하여 시스템 복사/복제 등의 일환으로 새 호스트를 프로비전할 수 있습니다.

SAP LaMa로 관리하려는 모든 가상 머신에 대해 별도의 서브넷을 사용하는 것이 좋으며, 새 가상 머신을 배포할 때 및 SAP 인스턴스가 준비가 되지 않은 경우에는 IP 주소가 "도용"되지 않도록 동적 IP 주소를 사용하지 마십시오.

> [!NOTE]
> 가능하면 모든 가상 머신 확장을 제거합니다. 가상 머신에서 디스크를 분리하는 데 긴 런타임이 발생할 수 있기 때문입니다.

사용자 \<hanasid>adm, \<sapsid>adm 및 그룹 sapsys가 동일한 ID 및 GID를 사용하는 대상 머신에 존재하거나 LDAP를 사용해야 합니다. SAP NetWeaver (A)SCS를 실행하는 데 사용해야 하는 가상 머신에서 NFS 서버를 사용하도록 설정하고 시작합니다.

### <a name="manual-deployment"></a>수동 배포

SAP LaMa는 SAP Host Agent를 사용하여 가상 머신과 통신합니다. 가상 머신을 수동으로 배포하거나 빠른 시작 리포지토리의 Azure Resource Manager 템플릿을 사용하지 않는 경우에는 최신 SAP Host Agent와 SAP Adaptive Extensions를 설치해야 합니다. Azure에 필요한 패치 수준에 대한 자세한 정보는, SAP Note [2343511]을 참조하세요.

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Linux Virtual Machine의 수동 배포

SAP Note [2343511]에 나열된 지원되는 운영 체제 중 하나를 사용하여 새 가상 머신을 만듭니다. SAP 인스턴스에 대한 추가 IP 구성을 추가합니다. 각 인스턴스에는 IP 주소가 하나 이상 필요하며 가상 호스트 이름을 사용하여 설치해야 합니다.

SAP NetWeaver ASCS 인스턴스에는 /sapmnt/\<SAPSID>, /usr/sap/\<SAPSID>, /usr/sap/trans 및 /usr/sap/\<sapsid>adm에 대한 디스크가 필요합니다. SAP NetWeaver Application Server에는 추가 디스크가 필요하지 않습니다. SAP 인스턴스와 관련된 모든 사항은 ASCS에 저장하고 NFS를 통해 내보내야 합니다. 그렇지 않으면, 현재는 SAP LaMa를 사용하여 추가 애플리케이션 서버를 추가할 수 없습니다.

![Linux의 SAP NetWeaver ASCS](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>SAP HANA 수동 배포

SAP Note [2343511]에 나열된 SAP HANA에 지원되는 운영 체제 중 하나를 사용하여 새 가상 머신을 만듭니다. SAP HANA 및 HANA 테넌트당 IP 구성을 하나 더 추가합니다.

SAP HANA에는 /hana/shared, /hana/backup, /hana/data 및 /hana/log에 대한 디스크가 필요합니다.

![Linux의 SAP HANA](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Linux에 Oracle Database 수동 배포

SAP Note [2343511]에 나열된 Oracle Database를 지원하는 운영 체제 중 하나를 사용하여 새 가상 머신을 만듭니다. Oracle Database에 대한 IP 구성을 하나 더 추가합니다.

Oracle Database에는 /oracle, /home/oraod1 및 /home/oracle에 대한 디스크가 필요합니다.

![Linux의 Oracle 데이터베이스와 필요한 디스크를 보여 주는 다이어그램.](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Microsoft SQL Server 수동 배포

SAP Note [2343511]에 나열된 Microsoft SQL Server를 지원하는 운영 체제 중 하나를 사용하여 새 가상 머신을 만듭니다. SQL Server 인스턴스에 대한 IP 구성을 하나 더 추가합니다.

SQL Server 데이터베이스 서버에는 데이터베이스 데이터 및 로그 파일에 대한 디스크와 c:\usr\sap에 대한 디스크가 필요합니다.

![Linux의 Oracle Database](media/lama/sap-lama-db-sql.png)

SAP NetWeaver Application Server를 시스템 복사/복제 대상으로 재배치하는 데 사용할 가상 머신에 SQL Server용으로 지원되는 Microsoft ODBC 드라이버를 설치해야 합니다.

SAP LaMa는 SQL Server 자체를 재배치할 수 없습니다. 따라서 데이터베이스 인스턴스를 시스템 복사/복제 대상으로 재배치하는 데 사용하려는 가상 머신에 SQL Server가 미리 설치되어 있어야 합니다.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Azure 템플릿을 사용하여 Virtual Machine 배포

가상 머신의 운영 체제에 대해 [SAP Software Marketplace](https://support.sap.com/swdc)에서 다음과 같은 최신 아카이브를 다운로드합니다.

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

[Microsoft 다운로드 센터](https://www.microsoft.com/download)에서 다음 구성 요소도 다운로드합니다.

1. Microsoft Visual C++ 2010 재배포 가능 패키지(x64)(Windows만 해당)
1. Microsoft ODBC Driver for SQL Server(SQL Server만 해당)

이 구성 요소는 템플릿을 배포하는 데 필요합니다. 구성 요소를 템플릿에서 사용할 수 있도록 만드는 가장 쉬운 방법은 Azure 스토리지 계정에 업로드하고 SAS(Shared Access Signature)를 생성하는 것입니다.

템플릿에는 다음과 같은 매개 변수가 있습니다.

* sapSystemId: SAP 시스템 ID입니다. 디스크 레이아웃(예: /usr/sap/\<sapsid>)을 만드는 데 사용됩니다.

* computerName: 새 가상 머신의 컴퓨터 이름입니다. 이 매개 변수는 SAP LaMa에도 사용됩니다. 이 템플릿을 사용하여 시스템 복사본의 일부로 새 가상 머신을 프로비전하면, SAP LaMa는 이 컴퓨터 이름을 가진 호스트에 연결할 수 있을 때까지 대기합니다.

* osType: 배포하려는 운영 체제의 형식입니다.

* dbtype: 데이터베이스의 형식입니다. 이 매개 변수는 더 추가해야 하는 IP 구성의 수와 디스크 레이아웃의 모양을 결정하는 데 사용됩니다.

* sapSystemSize: 배포하려는 SAP 시스템의 크기입니다. 가상 머신 인스턴스 형식과 크기를 결정하는 데 사용됩니다.

* adminUsername: 가상 머신에 대한 사용자 이름입니다.

* adminPassword: 가상 머신에 대한 암호입니다. SSH에 대한 공개 키를 제공할 수도 있습니다.

* sshKeyData: 가상 머신에 대한 공개 SSH 키입니다. Linux 운영 체제에 대해서만 지원됩니다.

* subnetId: 사용하려는 서브넷의 ID입니다.

* deployEmptyTarget: 가상 머신을 인스턴스 재배치 또는 유사한 대상으로 사용하려는 경우 빈 대상을 배포할 수 있습니다. 이 경우 추가 디스크 또는 IP 구성이 연결되지 않습니다.

* sapcarLocation: 배포하는 운영 체제와 일치하는 sapcar 애플리케이션의 위치입니다. sapcar는 다른 매개 변수에 제공하는 아카이브를 추출하는 데 사용됩니다.

* sapHostAgentArchiveLocation: SAP Host Agent 아카이브의 위치입니다. SAP Host Agent는 템플릿 배포의 일부로 배포됩니다.

* sapacExtLocation: SAP Adaptive Extensions의 위치입니다. SAP Note [2343511]에 Azure에 필요한 최소 패치 수준이 나열되어 있습니다.

* vcRedistLocation: SAP Adaptive Extensions를 설치하는 데 필요한 VC 런타임의 위치입니다. 이 매개 변수는 Windows에 대해서만 필요합니다.

* odbcDriverLocation: 설치하려는 ODBC 드라이버의 위치입니다. SQL Server용 Microsoft ODBC 드라이버만 지원됩니다.

* sapadmPassword: sapadm 사용자의 암호입니다.

* sapadmId: sapadm 사용자의 Linux 사용자 ID입니다. Windows에 대해서는 필요하지 않습니다.

* sapsysGid: sapsys 그룹의 Linux 그룹 ID입니다. Windows에 대해서는 필요하지 않습니다.

* _artifactsLocation: 이 템플릿에 필요한 아티팩트가 있는 기본 URI입니다. 함께 제공되는 스크립트를 사용하여 템플릿이 배포되면, 구독의 프라이빗 위치가 사용되고 이 값이 자동으로 생성됩니다. GitHub에서 템플릿을 배포하지 않은 경우에만 필요합니다.

* _artifactsLocationSasToken: _artifactsLocation에 액세스하는 데 필요한 sasToken입니다. 함께 제공되는 스크립트를 사용하여 템플릿이 배포되면, sasToken이 자동으로 생성됩니다. GitHub에서 템플릿을 배포하지 않은 경우에만 필요합니다.

### <a name="sap-hana"></a>SAP HANA

아래 예제에서는 시스템 ID HN1로 SAP HANA를 설치하고 시스템 ID AH1로 SAP NetWeaver 시스템을 설치한다고 가정합니다. 가상 호스트 이름은 HANA 인스턴스의 경우 hn1-db이고 SAP NetWeaver 시스템에 사용되는 HANA 테넌트는 ah1-db이며 SAP NetWeaver ASCS는 ah1-ascs이고 첫 번째 SAP NetWeaver Application Server는 ah1-di-0입니다.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Azure Managed Disks를 사용하여 SAP HANA용 SAP NetWeaver ASCS 설치

SAP SWPM(Software Provisioning Manager)을 시작하기 전에 ASCS의 가상 호스트 이름의 IP 주소를 마운트해야 합니다. 권장되는 방식은 sapacext를 사용하는 것입니다. sapacext를 사용하여 IP 주소를 마운트하는 경우에는 다시 부팅한 후 IP 주소를 다시 마운트해야 합니다.

![Linux 로고.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows 로고.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

SWPM을 실행하고 *ASCS 인스턴스 호스트 이름* 에 *ah1-ascs* 를 사용합니다.

![Linux 로고.][Logo_Linux] Linux  
다음 프로필 매개 변수를 /usr/sap/hostctrl/exe/host_profile에 있는 SAP Host Agent 프로필에 추가합니다. 자세한 내용은 SAP Note [2628497]를 참조하세요.
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Azure NetAppFiles(ANF) 베타에서 SAP HANA용 SAP NetWeaver ASCS 설치

> [!NOTE]
> 이 기능은 아직 일반 공급되지 않습니다. 자세한 내용은 SAP 참고 [2815988](미리 보기 고객에게만 표시됨)을 참조하세요.
구성 요소 BC-VCM-LVM-HYPERV에서 SAP 인시던트를 열고 Azure NetApp Files 미리 보기에 LaMa 스토리지 어댑터 가입 요청

ANF는 Azure용 NFS를 제공합니다. SAP LaMa의 컨텍스트에서는 이를 통해 ASCS(ABAP Central Services) 인스턴스의 생성 및 후속 애플리케이션 서버 설치를 간소화합니다. 이전에 ASCS 인스턴스는 NFS 서버 역할을 수행하고 acosprep/nfs_paths 매개 변수를 SAP Hostagent의 host_profile에 추가해야 했습니다.

#### <a name="anf-is-currently-available-in-these-regions"></a>현재 ANF를 사용할 수 있는 지역은 다음과 같습니다.

오스트레일리아 동부, 미국 중부, 미국 동부, 미국 동부 2, 북유럽, 미국 중남부, 서유럽 및 미국 서부 2.

#### <a name="network-requirements"></a>네트워크 요구 사항

ANF에는 SAP 서버와 동일한 VNET의 일부인 위임된 서브넷이 필요합니다. 이러한 구성에 대한 예제는 다음과 같습니다.
이 화면에서는 VNET 및 첫 번째 서브넷을 만드는 방법을 보여 줍니다.

![SAP LaMa Azure ANF용 가상 네트워크 만들기 ](media/lama/sap-lama-createvn-50.png)

다음 단계에서는 Microsoft.NetApp/volumes를 위한 위임된 서브넷을 만듭니다.

![SAP LaMa 위임된 서브넷 추가 ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa 서브넷 목록 ](media/lama/sap-lama-subnets.png)

이제 Azure Portal 내에서 NetApp 계정을 만들어야 합니다.

![SAP LaMa NetApp 계정 만들기 ](media/lama/sap-lama-create-netappaccount-50.png)

![SAP LaMa NetApp 계정이 생성됨 ](media/lama/sap-lama-netappaccount.png)

NetApp 계정 내에서 용량 풀은 각 풀의 디스크 크기와 유형을 지정합니다.

![SAP LaMa NetApp 용량 풀 만들기 ](media/lama/sap-lama-capacitypool-50.png)

![SAP LaMa NetApp 용량 풀이 생성됨 ](media/lama/sap-lama-capacitypool-list.png)

이제 NFS 볼륨을 정의할 수 있습니다. 하나의 풀에 여러 시스템에 대한 볼륨이 있으므로 자체 설명 명명 스키마를 선택해야 합니다. SID를 추가하면 관련 볼륨을 함께 그룹화하는 데 도움이 됩니다. ASCS 및 AS 인스턴스의 경우 */sapmnt/\<SID\>* , */usr/sap/\<SID\>* 및 */home/\<sid\>adm* 마운트가 필요합니다. 필요에 따라 중앙 전송 디렉터리에 */usr/sap/trans* 가 필요합니다. 이 디렉터리는 최소한 하나의 환경의 모든 시스템에서 사용됩니다.

> [!NOTE]
> 베타 단계에서는 볼륨 이름이 구독 내에서 고유해야 합니다.

![SAP LaMa 볼륨 만들기 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa 볼륨 만들기 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa 볼륨 만들기 3 ](media/lama/sap-lama-createvolume3-80.png)

다른 볼륨에 대해서도 이러한 단계를 반복해야 합니다.

![SAP LaMa 생성된 볼륨 목록 ](media/lama/sap-lama-volumes.png)

이제 SAP SWPM을 사용하여 초기 설치를 수행할 시스템에 이러한 볼륨을 탑재해야 합니다.

먼저 탑재 지점을 만들어야 합니다. 이 경우 SID가 AN1이므로 다음 명령을 실행해야 합니다.

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
다음으로, 아래 명령을 사용하여 ANF 볼륨을 탑재합니다.

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
탑재 명령은 포털에서 파생될 수도 있습니다. 로컬 탑재 지점을 조정해야 합니다.

df -h 명령을 사용하여 확인합니다.

![SAP LaMa 탑재 지점의 OS 수준 ](media/lama/sap-lama-mounts.png)

이제 SWPM을 사용하여 설치를 수행해야 합니다.

AS 인스턴스 하나 이상에 대해 동일한 단계를 수행해야 합니다.

설치가 완료되면 SAP LaMa 내에서 시스템이 검색되어야 합니다.

ASCS 및 AS 인스턴스의 탑재 지점은 다음과 같이 표시됩니다.

![LaMa의 SAP LaMa 탑재 지점](media/lama/sap-lama-ascs.png)(이는 한 가지 예시입니다. IP 주소와 내보내기 경로는 이전에 사용한 것과 다릅니다.)


#### <a name="install-sap-hana"></a>SAP HANA 설치

명령줄 도구 hdblcm을 사용하여 SAP HANA를 설치하는 경우에는 --hostname 매개 변수를 사용하여 가상 호스트 이름을 제공합니다. 데이터베이스의 가상 호스트 이름에 대한 IP 주소를 네트워크 인터페이스에 추가해야 합니다. 권장되는 방식은 sapacext를 사용하는 것입니다. sapacext를 사용하여 IP 주소를 마운트하는 경우에는 다시 부팅한 후 IP 주소를 다시 마운트해야 합니다.

애플리케이션 서버가 HANA 테넌트와 연결하는 데 사용하는 이름의 또 다른 가상 호스트 이름 및 IP 주소를 추가합니다.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

HANA 가상 머신이 아닌 애플리케이션 서버 가상 머신에서 SWPM의 데이터베이스 인스턴스 설치를 실행합니다. *Database for SAP System* 대화 상자의 *데이터베이스 호스트* 에 대해 *ah1-db* 를 사용합니다.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>SAP HANA용 SAP NetWeaver Application Server 설치

SAP SWPM(Software Provisioning Manager)을 시작하기 전에 애플리케이션 서버의 가상 호스트 이름의 IP 주소를 마운트해야 합니다. 권장되는 방식은 sapacext를 사용하는 것입니다. sapacext를 사용하여 IP 주소를 마운트하는 경우에는 다시 부팅한 후 IP 주소를 다시 마운트해야 합니다.

![Linux 로고.][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows 로고.][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

SAP NetWeaver 프로필 매개 변수 dbs/hdb/hdb_use_ident를 사용하여 HDB userstore에서 키를 찾는 데 사용되는 ID를 설정하는 것이 좋습니다. SWPM을 사용하여 데이터베이스 인스턴스를 설치한 후 이 매개 변수를 수동으로 추가하거나 다음을 사용하여 SWPM을 실행할 수 있습니다.

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

수동으로 설정하는 경우에는 새 HDB userstore 항목을 만들어야 합니다.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

*Primary Application Server Instance*(기본 애플리케이션 서버 인스턴스) 대화 상자의 *PAS 인스턴스 호스트 이름* 에 대해 *ah1-di-0* 을 사용합니다.

#### <a name="post-installation-steps-for-sap-hana"></a>SAP HANA의 설치 후 단계

테넌트 복사를 시도하기 전에 SYSTEMDB 및 모든 테넌트 데이터베이스를 백업해야 합니다. 테넌트가 시스템 복제본을 이동하거나 생성합니다.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

아래 예제에서는 시스템 ID AS1로 SAP NetWeaver 시스템을 설치한다고 가정합니다. 가상 호스트 이름은 SAP NetWeaver 시스템에 사용되는 SQL Server 인스턴스는 as1-db이고 SAP NetWeaver ASCS는 as1-ascs이고 첫 번째 SAP NetWeaver Application Server는 as1-di-0입니다.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>SQL Server용 SAP NetWeaver ASCS 설치

SAP SWPM(Software Provisioning Manager)을 시작하기 전에 ASCS의 가상 호스트 이름의 IP 주소를 마운트해야 합니다. 권장되는 방식은 sapacext를 사용하는 것입니다. sapacext를 사용하여 IP 주소를 마운트하는 경우에는 다시 부팅한 후 IP 주소를 다시 마운트해야 합니다.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

SWPM을 실행하고 *ASCS 인스턴스 호스트 이름* 에 *as1-ascs* 를 사용합니다.

#### <a name="install-sql-server"></a>SQL Server 설치

데이터베이스의 가상 호스트 이름에 대한 IP 주소를 네트워크 인터페이스에 추가해야 합니다. 권장되는 방식은 sapacext를 사용하는 것입니다. sapacext를 사용하여 IP 주소를 마운트하는 경우에는 다시 부팅한 후 IP 주소를 다시 마운트해야 합니다.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

SQL Server 가상 머신에서 SWPM의 데이터베이스 인스턴스 설치를 실행합니다. Use SAPINST_USE_HOSTNAME=*as1-db* 를 사용하여 SQL Server에 연결하는 데 사용되는 호스트 이름을 재정의합니다. Azure Resource Manager 템플릿을 사용하여 가상 머신을 배포한 경우에는 데이터베이스 데이터 파일에 사용되는 디렉터리를 *C:\sql\data* 로 설정하고 데이터베이스 로그 파일에 설정되는 디렉터리를 *C:\sql\log* 로 설정해야 합니다.

사용자 *NT AUTHORITY\SYSTEM* 에게는 SQL Server에 대한 액세스 권한과 *sysadmin* 이라는 서버 역할이 있어야 합니다. 자세한 내용은 SAP Note [1877727] 및 [2562184]를 참조하세요.

#### <a name="install-sap-netweaver-application-server"></a>SAP NetWeaver Application Server 서버 설치

SAP SWPM(Software Provisioning Manager)을 시작하기 전에 애플리케이션 서버의 가상 호스트 이름의 IP 주소를 마운트해야 합니다. 권장되는 방식은 sapacext를 사용하는 것입니다. sapacext를 사용하여 IP 주소를 마운트하는 경우에는 다시 부팅한 후 IP 주소를 다시 마운트해야 합니다.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

*Primary Application Server Instance*(기본 애플리케이션 서버 인스턴스) 대화 상자의 *PAS 인스턴스 호스트 이름* 에 대해 *as1-di-0* 을 사용합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="errors-and-warnings-during-discover"></a>검색 중 오류 및 경고

* SELECT 권한이 거부됨
  * [Microsoft][ODBC SQL Server Driver][SQL Server]'log_shipping_primary_databases', database 'msdb', schema 'dbo' 개체에 대한 SELECT 권한이 거부되었습니다. [SOAPFaultException]  
  'log_shipping_primary_databases', database 'msdb', schema 'dbo' 개체에 대한 SELECT 권한이 거부되었습니다.
  * 해결 방법  
    *NT AUTHORITY\SYSTEM* 이 SQL Server에 액세스할 수 있는지 확인합니다. SAP Note [2562184]를 참조하세요.


### <a name="errors-and-warnings-for-instance-validation"></a>인스턴스 유효성 검사에 대한 오류 및 경고

* HDB userstore의 유효성 검사 중 예외가 발생했습니다.  
  * 로그 뷰어를 확인하세요.  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: ID가 'RuntimeHDBConnectionValidator'인 유효성 검사기의 예외(Validation: 'VALIDATION_HDB_USERSTORE'): hdbuserstore를 검색할 수 없습니다.  
    HANA userstore가 올바른 위치에 있지 않습니다.
  * 해결 방법  
    /usr/sap/AH1/hdbclient/install/installation.ini가 올바른지 확인합니다.

### <a name="errors-and-warnings-during-a-system-copy"></a>시스템 복사 중 오류 및 경고

* 시스템 프로비전 단계의 유효성을 검사하는 중 오류가 발생했습니다.
  * 원인: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * 해결 방법  
    원본 HANA 시스템의 모든 데이터베이스를 백업합니다.

* 시스템 복사 단계 데이터베이스 인스턴스의 시작(*Start*)
  * 호스트 에이전트 작업 '000D3A282BC91EE8A1D76CF1F92E2944' 실패(OperationException. FaultCode: '127', 메시지: '명령을 실행하지 못했습니다. : [Microsoft][ODBC SQL Server Driver][SQL Server]사용자에게 'AS2' 데이터베이스를 변경할 권한이 없거나, 데이터베이스가 없거나, 데이터베이스가 액세스 검사를 허용하지 않는 상태입니다.')
  * 해결 방법  
    *NT AUTHORITY\SYSTEM* 이 SQL Server에 액세스할 수 있는지 확인합니다. SAP Note [2562184]를 참조하세요.

### <a name="errors-and-warnings-during-a-system-clone"></a>시스템 복제 중 오류 및 경고

* ASCS 또는 애플리케이션 서버의 *Forced Register and Start Instance Agent*(강제 등록 및 인스턴스 에이전트 시작) 단계에서 인스턴스 에이전트 등록을 시도하는 중 오류가 발생했습니다.
  * 인스턴스 에이전트 등록을 시도하는 중 오류가 발생했습니다. (RemoteException: '프로필 '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0'에서 인스턴스 데이터를 로드하지 못했습니다. 프로필 '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0'에 액세스할 수 없습니다. 해당하는 파일이나 디렉터리가 없습니다.')
  * 해결 방법  
   ASCS/SCS의 sapmnt 공유에 SAP_AS1_GlobalAdmin에 대한 전체 액세스 권한이 있는지 확인합니다.

* *Enable Startup Protection for Clone*(복제에 대한 시작 보호 사용) 단계의 오류
  * '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' 파일을 열지 못했습니다. 원인: 해당하는 파일이나 디렉터리가 없습니다.
  * 해결 방법  
    애플리케이션 서버의 컴퓨터 계정에 프로필에 대한 쓰기 권한이 필요합니다.

### <a name="errors-and-warnings-during-create-system-replication"></a>시스템 복제 생성 중 오류 및 경고

* 시스템 복제 생성을 클릭하면 예외 발생
  * 원인: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException   Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r
  * 해결 방법  
    sapacext를 `<hanasid`>adm으로 실행할 수 있는지 테스트합니다.

* 스토리지 단계에서 전체 복사본을 사용할 수 없는 경우 오류 발생
  * 경로 IStorageCopyData.storageVolumeCopyList:1 및 필드 targetStorageSystemId에 대한 컨텍스트 특성 메시지를 보고할 때 오류가 발생했습니다.
  * 해결 방법  
    단계의 경고를 무시하고 다시 시도하세요. 이 문제는 SAP LaMa의 새로운 지원 패키지/패치에서 수정될 예정입니다.

### <a name="errors-and-warnings-during-relocate"></a>재배치 중 오류 및 경고

* nfs 다시 내보내기에 경로 '/usr/sap/AH1'이 허용되지 않습니다.
  * 자세한 내용은 SAP Note [2628497]을 참조하세요.
  * 해결 방법  
    ASCS 내보내기를 ASCS HostAgent 프로필에 추가합니다. SAP Note [2628497]을 참조하세요.

* ASCS 재배치 시 기능이 구현되지 않음
  * 명령 출력: exportfs: host:/usr/sap/AX1: 구현되지 않는 기능입니다.
  * 해결 방법  
    재배치 대상 가상 머신에 NFS 서버 서비스가 활성화되어 있는지 확인합니다.

### <a name="errors-and-warnings-during-application-server-installation"></a>Application Server 설치 중 오류 및 경고

* SAPinst 단계: getProfileDir 실행 중 오류 발생
  * 오류: (단계에서 보고한 마지막 오류: Caught ESAPinstException in module call: Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' reported an error: Node \\\as1-ascs\sapmnt\AS1\SYS\profile does not exist. SAPinst를 대화형 모드로 시작하여 문제를 해결하십시오.)
  * 해결 방법  
    프로필에 대한 액세스 권한이 있는 사용자로 SWPM이 실행 중인지 확인합니다. 이 사용자는 Application Server 설치 마법사에서 구성할 수 있습니다.

* SAPinst 단계: askUnicode 실행 중 오류 발생
  * 오류: (단계에서 보고한 마지막 오류: Caught ESAPinstException in module call: Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' reported an error: SAPinst를 대화형 모드로 시작하여 문제를 해결하십시오.)
  * 해결 방법  
    최근 SAP 커널을 사용하는 경우, SWPM은 ASCS의 메시지 서버를 사용하여 시스템이 더 이상 유니코드 시스템인지 여부를 확인할 수 없습니다. 자세한 내용은 SAP Note [2445033]을 참조하세요.  
    이 문제는 SAP LaMa의 새로운 지원 패키지/패치에서 수정될 예정입니다.  
    이 문제를 해결하려면 SAP 시스템의 기본 프로필에서 프로필 매개 변수 OS_UNICODE=uc를 설정합니다.

* SAPinst 단계: dCheckGivenServer 실행 중 오류 발생
  * SAPinst 단계: dCheckGivenServer 실행 중 오류 발생" version="1.0" ERROR: (단계에서 보고한 마지막 오류: \<p> 사용자가 설치를 취소했습니다. \</p>
  * 해결 방법  
    프로필에 대한 액세스 권한이 있는 사용자로 SWPM이 실행 중인지 확인합니다. 이 사용자는 Application Server 설치 마법사에서 구성할 수 있습니다.

* SAPinst 단계: checkClient 실행 중 오류 발생
  * SAPinst 단계: checkClient 실행 중 오류 발생" version="1.0" ERROR: (단계에서 보고한 마지막 오류: \<p> 사용자가 설치를 취소했습니다. \</p>)
  * 해결 방법  
    애플리케이션 서버를 설치하려는 가상 머신에 SQL Server용 Microsoft ODBC 드라이버가 설치되어 있는지 확인합니다.

* SAPinst 단계: copyScripts 실행 중 오류 발생
  * 단계에서 보고한 마지막 오류: 시스템 호출 실패입니다. 세부 정보 Error 13 (0x0000000d) (사용 권한 거부됨) in execution of system call 'fopenU' with parameter (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), line (494) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), 스택 추적:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * 해결 방법  
    프로필에 대한 액세스 권한이 있는 사용자로 SWPM이 실행 중인지 확인합니다. 이 사용자는 Application Server 설치 마법사에서 구성할 수 있습니다.

* SAPinst 단계: askPasswords 실행 중 오류 발생
  * 단계에서 보고한 마지막 오류: 시스템 호출 실패입니다. 세부 정보: Error 5 (0x00000005) (액세스가 거부되었습니다.) in execution of system call 'NetValidatePasswordPolicy' with parameter (...), line (359) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), 스택 추적:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * 해결 방법  
    *Isolation*(격리) 단계에서 호스트를 추가하여 VM에서 도메인 컨트롤러로의 통신을 허용해야 합니다.

## <a name="next-steps"></a>다음 단계
* [Azure 운영 가이드의 SAP HANA][hana-ops-guide]
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
