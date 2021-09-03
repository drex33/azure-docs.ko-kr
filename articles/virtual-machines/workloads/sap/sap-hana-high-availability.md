---
title: SLES의 Azure VM에 있는 SAP HANA의 고가용성 | Microsoft Docs
description: SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 079cf4fde03f2e9eeafc77a35f03c16e4b8c241b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112286006"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

온-프레미스 개발에서 HANA 시스템 복제를 사용하거나 공유 스토리지를 사용하여 SAP HANA의 고가용성을 설정할 수 있습니다.
Azure VM(Virtual Machines)에서 Azure의 HANA 시스템 복제는 현재 지원되는 유일한 고가용성 기능입니다. SAP HANA 복제는 하나의 기본 노드와 하나 이상의 보조 노드로 구성됩니다. 기본 노드의 데이터를 변경하면 보조 노드에 동기적 또는 비동기적으로 복제됩니다.

이 문서에서는 가상 머신을 배포 및 구성하며, 클러스터 프레임워크를 설치하고, SAP HANA 시스템 복제를 설치 및 구성하는 방법을 설명합니다.
예제 구성에서 설치 명령, 인스턴스 번호 **03** 및 HANA 시스템 ID **HN1** 이 사용됩니다.

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록.
  * Azure VM 크기에 대한 중요한 용량 정보.
  * 지원되는 SAP 소프트웨어 및 OS(운영 체제)와 데이터베이스 조합.
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전.
* SAP Note [2015553]은 Azure에서 SAP를 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소가 나열되어 있습니다.
* SAP Note [2205917]은 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799]는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* SAP Note [401162]는 HANA 시스템 복제를 설정할 때 “이미 사용 중인 주소”를 피하는 방법에 대한 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide] 가이드.
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide](이 문서).
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide] 가이드.
* [SAP 애플리케이션 12 SP3용 SUSE Linux Enterprise Server 모범 사례 가이드][sles-for-sap-bp]
  * SAP HANA SR 성능 최적화 인프라 설정(SAP 애플리케이션 12 SP1용 SLES). 이 가이드에는 온-프레미스 개발을 위해 SAP HANA 시스템 복제를 설정하는 데 필요한 모든 정보가 들어 있습니다. 이 가이드를 기준으로 사용합니다.
  * SAP HANA SR 비용 최적화 인프라 설정(SAP 애플리케이션 12 SP1용 SLES).

## <a name="overview"></a>개요

고가용성을 얻기 위해 두 개의 가상 머신에 SAP HANA가 설치됩니다. HANA 시스템 복제를 사용하여 데이터가 복제됩니다.

![SAP HANA 고가용성 개요](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA 시스템 복제 설정은 전용 가상 호스트 이름과 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 다음 목록에는 부하 분산 장치의 구성이 나와 있습니다.

* 프런트 엔드 구성: hn1-db의 IP 주소 10.0.0.13
* 백 엔드 구성: HANA 시스템 복제의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트: 포트 62503
* 부하 분산 규칙: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Linux에 배포

SAP HANA의 리소스 에이전트는 SAP 애플리케이션의 SUSE Linux Enterprise Server에 포함되어 있습니다.
Azure Marketplace에는 새 가상 머신을 배포하는 데 사용할 수 있는 SAP Applications 12용 SUSE Linux Enterprise Server의 이미지가 포함되어 있습니다.

### <a name="deploy-with-a-template"></a>템플릿을 사용하여 배포

GitHub에서 빠른 시작 템플릿 중 하나를 사용하여 필요한 모든 리소스를 배포할 수 있습니다. 템플릿에서 가상 머신, 부하 분산 장치, 가용성 집합 등을 배포합니다.
템플릿을 배포하려면 다음 단계를 따릅니다.

1. Azure Portal에서 [데이터베이스 템플릿][template-multisid-db] 또는 [수렴형 템플릿][template-converged]을 엽니다. 
    데이터베이스 템플릿은 데이터베이스에 대해서만 부하 분산 규칙을 만듭니다. 또한 수렴형 템플릿은 ASCS/SCS 및 ERS(Linux 전용) 인스턴스에 대해 부하 분산 규칙을 만듭니다. SAP NetWeaver 기반 시스템을 설치하고 동일한 머신에 ASCS/SCS 인스턴스를 설치하려는 경우 [수렴형 템플릿][template-converged]을 사용합니다.

1. 다음 매개 변수를 입력합니다.
    - **SAP 시스템 ID**: 설치하려는 SAP 시스템의 SAP 시스템 ID를 입력합니다. 이 ID는 배포되는 리소스의 접두사로 사용됩니다.
    - **스택 유형**: (이 매개 변수는 변환 템플릿을 사용할 때만 적용됩니다.) SAP NetWeaver 스택 유형을 선택합니다.
    - **OS 유형**: Linux 배포판 중 하나를 선택합니다. 이 예제에서는 **SLES 12** 를 선택합니다.
    - **Db 형식**: **HANA** 를 선택합니다.
    - **SAP 시스템 크기**: 새 시스템에서 제공할 SAP의 수를 입력합니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
    - **시스템 가용성**: **HA** 를 선택합니다.
    - **관리자 사용자 이름 및 관리자 암호**: 머신에 로그인하는 데 사용할 수 있는 새 사용자가 만들어집니다.
    - **새로운 또는 기존 서브넷**: 새 가상 네트워크 및 서브넷을 만들어야 하는지 또는 기존 서브넷을 사용해야 하는지 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 **기존** 을 선택합니다.
    - **서브넷 ID**: 서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 일반적으로 **/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>** 형태입니다.

### <a name="manual-deployment"></a>수동 배포

> [!IMPORTANT]
> 선택한 OS가 사용 중인 특정 VM 유형에서 SAP HANA용으로 인증된 SAP인지 반드시 확인하세요. 이 항목에 대한 SAP HANA 인증 VM 형식 및 OS 릴리스 목록은 [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 조회할 수 있습니다. 특정 VM 형식에 대한 SAP HANA 지원 OS 릴리스의 전체 목록을 보려면 목록에 있는 VM 유형의 세부 정보를 클릭하세요.
>  

1. 리소스 그룹을 만듭니다.
1. 가상 네트워크를 만듭니다.
1. 가용성 집합을 만듭니다.
   - 업데이트 도메인의 최대 수를 설정합니다.
1. 부하 분산 장치(내부)를 만듭니다. [표준 부하 분산 장치](../../../load-balancer/load-balancer-overview.md)를 만드는 것이 좋습니다.
   - 2단계에서 만든 가상 네트워크를 선택합니다.
1. 가상 머신 1을 만듭니다.
   - 선택한 VM 형식의 SAP HANA에 대해 지원되는 Azure 갤러리에서 SLES4SAP 이미지를 사용합니다.
   - 3단계에서 만든 가용성 집합을 선택합니다.
1. 가상 머신 2를 만듭니다.
   - 선택한 VM 형식의 SAP HANA에 대해 지원되는 Azure 갤러리에서 SLES4SAP 이미지를 사용합니다.
   - 3단계에서 만든 가용성 집합을 선택합니다. 
1. 데이터 디스크를 추가합니다.

> [!IMPORTANT]
> 부하 분산 시나리오의 NIC 보조 IP 구성에서는 부동 IP가 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.   

> [!Note]
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.  

1. 표준 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
   1. 먼저 프런트 엔드 IP 풀을 만듭니다.
   
      1. 부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
      1. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
      1. **할당** 을 **정적** 으로 설정하고 IP 주소를 입력합니다(예: **10.0.0.13**).
      1. **확인** 을 선택합니다.
      1. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.
   
   1. 다음으로, 백 엔드 풀을 만듭니다.
   
      1. 부하 분산 장치를 열고, **백 엔드 풀** 을 선택한 다음, **추가** 를 클릭합니다.
      1. 새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
      1. **Virtual Network** 를 선택합니다.
      1. **가상 머신 추가** 를 선택합니다.
      1. **가상 머신**을 선택합니다.
      1. SAP HANA 클러스터의 가상 머신 및 해당 IP 주소를 선택합니다.
      1. **추가** 를 선택합니다.
   
   1. 다음으로, 상태 프로브를 만듭니다.
   
      1. 부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
      1. 새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
      1. 프로토콜 및 포트 625 **03** 으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
      1. **확인** 을 선택합니다.
   
   1. 다음으로 부하 분산 규칙을 만듭니다.
   
      1. 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
      1. 새 부하 분산 장치 규칙의 이름을 입력합니다(예: **hana-lb**).
      1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**, **hana-backend** 및 **hana-hp**).
      1. **HA 포트** 를 선택합니다.
      1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
      1. **확인** 을 선택합니다.

1. 또는 시나리오에서 기본 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
   1. 먼저 프런트 엔드 IP 풀을 만듭니다.
   
      1. 부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
      1. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
      1. **할당** 을 **정적** 으로 설정하고 IP 주소를 입력합니다(예: **10.0.0.13**).
      1. **확인** 을 선택합니다.
      1. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.
   
   1. 다음으로, 백 엔드 풀을 만듭니다.
   
      1. 부하 분산 장치를 열고, **백 엔드 풀** 을 선택한 다음, **추가** 를 클릭합니다.
      1. 새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
      1. **가상 머신 추가** 를 선택합니다.
      1. 3단계에서 만든 가용성 집합을 선택합니다.
      1. SAP HANA 클러스터의 가상 머신을 선택합니다.
      1. **확인** 을 선택합니다.
   
   1. 다음으로, 상태 프로브를 만듭니다.
   
      1. 부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
      1. 새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
      1. 프로토콜 및 포트 625 **03** 으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
      1. **확인** 을 선택합니다.
   
   1. SAP HANA 1.0의 경우 부하 분산 규칙을 만듭니다.
   
      1. 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
      1. 새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3 **03** 15).
      1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
      1. **TCP** 로 설정된 **프로토콜** 을 유지하고, 포트 3 **03** 15를 입력합니다.
      1. **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
      1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
      1. **확인** 을 선택합니다.
      1. 포트 3 **03** 17에 대해 이러한 단계를 반복합니다.
   
   1. SAP HANA 2.0의 경우 시스템 데이터베이스에 대한 부하 분산 규칙을 만듭니다.
   
      1. 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
      1. 새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3 **03** 13).
      1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
      1. **TCP** 로 설정된 **프로토콜** 을 유지하고, 포트 3 **03** 13을 입력합니다.
      1. **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
      1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
      1. **확인** 을 선택합니다.
      1. 포트 3 **03** 14에 대해 이러한 단계를 반복합니다.
   
   1. SAP HANA 2.0의 경우 테넌트 데이터베이스에 대한 부하 분산 규칙을 만듭니다.
   
      1. 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
      1. 새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3 **03** 40).
      1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
      1. **TCP** 로 설정된 **프로토콜** 을 유지하고, 포트 3 **03** 40을 입력합니다.
      1. **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
      1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
      1. **확인** 을 선택합니다.
      1. 포트 3 **03** 41 및 3 **03** 42에 대해 이러한 단계를 반복합니다.

   SAP HANA에 필요한 포트에 대한 자세한 내용은 [SAP HANA 테넌트 데이터베이스](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) 가이드의 [테넌트 데이터베이스에 연결](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) 챕터 또는 [SAP Note 2388694][2388694]를 참조하세요.

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치되는 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하면 안 됩니다. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브에 오류가 발생합니다. 매개 변수 **net.ipv4.tcp_timestamps** 를 **0** 으로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브](../../../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.
> 또한 SAP 참고 [2382421](https://launchpad.support.sap.com/#/notes/2382421)을 참조하세요. 

## <a name="create-a-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

이 HANA 서버에 대한 기본 Pacemaker 클러스터를 만들려면 [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 단계를 따르세요. SAP HANA 및 SAP NetWeaver (A)SCS에 동일한 Pacemaker 클러스터를 사용할 수 있습니다.

## <a name="install-sap-hana"></a>SAP HANA 설치

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.
- **[A]** : 단계가 모든 노드에 적용됩니다.
- **[1]** : 단계가 노드 1에만 적용됩니다.
- **[2]** : 단계가 Pacemaker 클러스터의 노드 2에만 적용됩니다.

1. **[A]** 디스크 레이아웃 **LVM(논리 볼륨 관리자)** 을 설정합니다.

   데이터와 로그 파일을 저장하는 볼륨의 LVM을 사용하는 것이 좋습니다. 다음 예에서는 가상 머신에 두 개의 볼륨을 만드는 데 사용되는 4개의 데이터 디스크가 연결되어 있다고 가정합니다.

   사용 가능한 모든 디스크를 나열합니다.

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   예제 출력:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   사용하려는 모든 디스크의 물리적 볼륨을 만듭니다.

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   데이터 파일에 대한 볼륨 그룹을 만듭니다. 로그 파일에 대해 한 볼륨 그룹, SAP HANA의 공유 디렉터리에 대해 한 볼륨을 사용합니다.

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   논리 볼륨을 만듭니다. `-i` 스위치 없이 `lvcreate`를 사용하는 경우 선형 볼륨이 만들어집니다. 더 나은 I/O 성능을 위해 스트라이프 볼륨을 만들고 [SAP HANA VM 스토리지 구성](./hana-vm-operations-storage.md)에 설명된 값에 스트라이프 크기를 정렬하는 것이 좋습니다. `-i` 인수는 기본 실제 볼륨의 수여야 하며 `-I` 인수는 스트라이프 크기입니다. 이 문서에서는 2개의 물리적 볼륨이 데이터 볼륨에 사용되므로 `-i` 스위치 인수가 **2** 로 설정됩니다. 데이터 볼륨의 스트라이프 크기는 **256KiB** 입니다. 로그 볼륨에는 하나의 실제 볼륨이 사용되므로 `-i` 또는 `-I` 스위치는 로그 볼륨 명령에 명시적으로 사용되지 않습니다.  

   > [!IMPORTANT]
   > 각 데이터, 로그 또는 공유 볼륨에 대해 하나 이상의 물리적 볼륨을 사용하는 경우 `-i` 스위치를 사용하고 기본 물리적 볼륨 수로 설정합니다. 스트라이프 볼륨을 만들 때 `-I` 스위치를 사용하여 스트라이프 크기를 지정합니다.  
   > 스트라이프 크기 및 디스크 수를 비롯한 권장되는 스토리지 구성은 [SAP HANA VM 스토리지 구성](./hana-vm-operations-storage.md)을 참조하세요.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   탑재 디렉터리를 만들고 모든 논리 볼륨의 UUID를 복사합니다.

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   세 개의 논리 볼륨에 대한 `fstab` 항목을 만듭니다.       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   `/etc/fstab` 파일에서 다음 줄을 삽입합니다.      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   새 볼륨을 탑재합니다.

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** 디스크 레이아웃 **일반 디스크** 를 설정합니다.

   데모 시스템을 위해 HANA 데이터와 로그 파일을 같은 디스크에 배치할 수 있습니다. /dev/disk/azure/scsi1/lun0에 파티션을 만들고 xfs로 포맷합니다.

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   /etc/fstab 파일에서 이 줄을 삽입합니다.

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   대상 디렉터리를 만들고 디스크를 탑재합니다.

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** 모든 호스트의 호스트 이름 확인을 설정합니다.

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts 파일을 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일을 사용하는 방법을 보여 줍니다.
   다음 명령에서 IP 주소와 호스트 이름을 바꿉니다.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   /etc/hosts 파일에서 다음 줄을 삽입합니다. 사용자 환경에 맞게 IP 주소와 호스트 이름을 변경합니다.

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** SAP HANA 고가용성 패키지를 설치합니다.

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

SAP HANA 시스템 복제를 설치하려면 [SAP HANA SR 성능 최적화 시나리오 가이드](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)의 4장을 수행합니다.

1. **[A]** HANA DVD에서 **hdblcm** 프로그램을 실행합니다. 프롬프트에서 다음 값을 입력합니다.
   * 설치 선택: **1** 을 입력합니다.
   * 설치할 추가 구성 요소 선택: **1** 을 입력합니다.
   * 설치 경로 [/hana/shared] 입력: Enter 키를 선택합니다.
   * 로컬 호스트 이름 입력 [..]: Enter 키를 선택합니다.
   * 시스템에 호스트를 추가할까요? (y/n) [n]: Enter 키를 선택합니다.
   * SAP HANA 시스템 ID 입력: HANA의 SID를 입력합니다. 예: **HN1**.
   * 인스턴스 번호 [00] 입력: HANA 인스턴스 번호를 입력합니다. Azure 템플릿을 사용하거나 이 문서의 수동 배포 섹션을 수행한 경우 **03** 을 입력합니다.
   * 데이터베이스 모드 선택/인덱스 [1] 입력: Enter 키를 선택합니다.
   * 시스템 사용량 선택 / 인덱스 [4] 입력: 시스템 사용량 값을 선택합니다.
   * 데이터 볼륨의 위치 [/hana/data/HN1] 입력: Enter 키를 선택합니다.
   * 로그 볼륨의 위치 [/hana/log/HN1] 입력: Enter 키를 선택합니다.
   * 최대 메모리 할당 제한? [n]: Enter 키를 선택합니다.
   * '...' [...] 호스트의 인증서 호스트 이름 입력: Enter 키를 선택합니다.
   * SAP 호스트 에이전트 사용자(sapadm) 암호 입력: 호스트 에이전트 사용자 암호를 입력합니다.
   * SAP 호스트 에이전트 사용자(sapadm) 암호 확인: 호스트 에이전트 사용자 암호를 다시 입력하여 확인합니다.
   * 시스템 관리자(hdbadm) 암호 입력: 시스템 관리자 암호를 입력합니다.
   * 시스템 관리자(hdbadm) 암호 확인: 시스템 관리자 암호를 다시 입력하여 확인합니다.
   * 시스템 관리자 홈 디렉터리 [/usr/sap/HN1/home] 입력: Enter 키를 선택합니다.
   * 시스템 관리자 로그인 셸 [/bin/sh] 입력: Enter 키를 선택합니다.
   * 시스템 관리자 사용자 ID [1001] 입력: Enter 키를 선택합니다.
   * 사용자 그룹 ID(sapsys) [79] 입력: Enter 키를 선택합니다.
   * 데이터베이스 사용자(SYSTEM) 암호 입력: 데이터베이스 사용자 암호를 입력합니다.
   * 데이터베이스 사용자(SYSTEM) 암호 확인: 데이터베이스 사용자 암호를 다시 입력하여 확인합니다.
   * 컴퓨터를 다시 부팅한 다음 시스템 다시 시작? [n]: Enter 키를 선택합니다.
   * 계속할까요? (y/n): 요약의 유효성을 검사합니다. 계속하려면 **y** 를 입력합니다.

1. **[A]** SAP 호스트 에이전트를 업그레이드합니다.

   [SAP 소프트웨어 센터][sap-swcenter]에서 최신 SAP 호스트 에이전트 아카이브를 다운로드하고 다음 명령을 실행하여 에이전트를 업그레이드합니다. 다운로드한 파일을 가리키도록 아카이브의 경로를 바꿉니다.

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 시스템 복제 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

* **[A]** : 단계가 모든 노드에 적용됩니다.
* **[1]** : 단계가 노드 1에만 적용됩니다.
* **[2]** : 단계가 Pacemaker 클러스터의 노드 2에만 적용됩니다.

1. **[1]** 테넌트 데이터베이스를 만듭니다.

   SAP HANA 2.0 또는 MDC를 사용하는 경우 SAP NetWeaver 시스템에 대한 테넌트 데이터베이스를 만듭니다. **NW1** 을 SAP 시스템의 SID로 바꿉니다.

   다음 명령을 <hanasid\>adm으로 실행합니다.

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 첫 번째 노드에서 시스템 복제를 구성합니다.

   데이터베이스를 <hanasid\>adm으로 백업합니다.

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   시스템 PKI 파일을 보조 사이트에 복사합니다.

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   기본 사이트를 만듭니다.

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** 두 번째 노드에서 시스템 복제를 구성합니다.
    
   두 번째 노드를 등록하여 시스템 복제를 시작합니다. 다음 명령을 <hanasid\>adm으로 실행합니다.

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0 시스템 복제 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

* **[A]** : 단계가 모든 노드에 적용됩니다.
* **[1]** : 단계가 노드 1에만 적용됩니다.
* **[2]** : 단계가 Pacemaker 클러스터의 노드 2에만 적용됩니다.

1. **[1]** 필요한 사용자를 만듭니다.

   다음 명령을 루트로 실행합니다. 굵은 글꼴 문자열(HANA 시스템 ID **HN1** 및 인스턴스 번호 **03**)을 SAP HANA 설치의 값으로 바꿉니다.

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** 키 저장소 항목을 만듭니다.

   다음 명령을 루트로 실행하여 새로운 키 저장소 항목을 만듭니다.

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** 데이터베이스를 백업합니다.

   루트로 데이터베이스를 백업합니다.

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   다중 테넌트 설치를 사용하는 경우 테넌트 데이터베이스도 백업합니다.

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 첫 번째 노드에서 시스템 복제를 구성합니다.

   기본 사이트를 <hanasid\>adm으로 만듭니다.

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** 보조 노드에서 시스템 복제를 구성합니다.

   보조 사이트를 <hanasid\>adm으로 등록합니다.

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Python 시스템 복제 후크 SAPHanaSR 구현

이는 클러스터와의 통합을 최적화하고 클러스터 장애 조치(failover)가 필요할 때 검색 기능을 향상시키는 중요한 단계입니다. SAPHanaSR python 후크를 구성하는 것이 좋습니다.    

1. **[A]** HANA "시스템 복제 후크"를 설치합니다. 후크는 두 개의 HANA DB 노드에 모두 설치해야 합니다.           

   > [!TIP]
   > SAPHanaSR Python 후크 기능을 사용하려면 패키지 SAPHanaSR의 버전이 0.153 이상인지 확인합니다.       
   > Python 후크는 HANA 2.0에서만 구현할 수 있습니다.        

   1. 후크를 `root`로 준비합니다.  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 두 노드에서 HANA를 중지합니다. <sid\>adm으로 실행합니다.  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. 각 클러스터 노드에서 `global.ini`를 조정합니다.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** 클러스터에서 <sid\>adm용 각 클러스터 노드에 sudoers 구성을 요구합니다. 이 예제에서는 새 파일을 만들어 수행합니다. 명령을 `root`로 실행합니다.    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```
SAP HANA 시스템 복제 후크 구현에 대한 자세한 내용은 [HANA HA/DR 공급자 설정](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html#_set_up_sap_hana_hadr_providers)을 참조하세요.  

3. **[A]** 두 노드에서 SAP HANA를 시작합니다. <sid\>adm으로 실행합니다.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** 후크 설치를 확인합니다. 활성 HANA 시스템 복제 사이트에서 <sid\>adm으로 실행합니다.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-08 22:18:15.877583 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:18:46.531564 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:21:26.816573 ha_dr_SAPHanaSR SOK

    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA 클러스터 리소스 만들기

먼저 HANA 토폴로지를 만듭니다. Pacemaker 클러스터 노드 중 하나에서 다음 명령을 실행합니다.

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

다음으로, HANA 리소스를 만듭니다.

> [!IMPORTANT]
> 최근 테스트 결과, 백로그와 하나의 연결만 처리하는 제한으로 인해 netcat이 요청에 응답하지 않는 것으로 확인되었습니다. netcat 리소스가 Azure Load Balancer 요청 수신을 중지하고 부동 IP를 사용할 수 없게 됩니다.  
> 기존 Pacemaker 클러스터의 경우 netcat을 socat으로 교체할 것을 권장했습니다. 현재는 다음 패키지 버전 요구 사항과 함께 패키지 리소스 에이전트의 일부인 azure-lb 리소스 에이전트를 사용할 것을 권장합니다.
> - SLES 12 SP4/SP5의 경우 버전은 resource-agents-4.3.018.a7fb5035-3.30.1 이상이어야 합니다.  
> - SLES 15/15 SP1의 경우 버전은 resource-agents-4.3.0184.6ee15eb2-4.13.1 이상이어야 합니다.  
>
> 변경 내용 적용을 위해 잠시의 가동 중지 시간이 필요합니다.  
> 기존 Pacemaker 클러스터의 경우 [Azure Load-Balancer 감지 강화](https://www.suse.com/support/kb/doc/?id=7024128)의 설명에 따라 socat을 사용하도록 구성을 이미 변경한 경우 즉시 azure-lb 리소스 에이전트로 전환할 필요가 없습니다.


> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 ‘마스터’ 및 ‘종속’ 용어에 대한 참조가 포함되어 있습니다.  해당 용어가 소프트웨어에서 제거되면 이 문서에서도 제거할 것입니다.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Pacemaker 클러스터에서 HANA 활성/읽기 사용 시스템 복제 구성

SAP HANA 2.0 SPS 01부터 SAP에서 SAP HANA 시스템 복제에 활성/읽기 사용 설정을 사용할 수 있으며, SAP HANA 시스템 복제의 보조 시스템을 읽기 집약적인 워크로드에 적극적으로 사용할 수 있습니다. 클러스터에서 해당 설정을 지원하려면 클라이언트가 보조 읽기 사용 SAP HANA 데이터베이스에 액세스할 수 있도록 두 번째 가상 IP 주소가 필요합니다. 인수가 발생한 후에도 보조 복제 사이트에 액세스할 수 있게 하려면 클러스터에서 가상 IP 주소를 보조 SAPHana 리소스로 이동해야 합니다.

해당 섹션에서는 두 번째 가상 IP가 있는 SUSE 고가용성 클러스터에서 HANA 활성/읽기 사용 시스템 복제를 관리하는 데 필요한 추가 단계에 대해 설명합니다.    
계속 진행하기 전에 설명서의 상단 세그먼트에 설명된 대로 SAP HANA 데이터베이스를 관리하는 SUSE 고가용성 클러스터를 완전히 구성했는지 확인합니다.  

![읽기 사용 보조가 있는 고가용성 SAP HANA](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>활성/읽기 사용 설정을 위한 Azure Load Balancer 추가 설정

두 번째 가상 IP를 프로비저닝하는 추가 단계를 진행하려면 [수동 배포](#manual-deployment) 섹션에 설명된 대로 Azure Load Balancer를 구성했는지 확인합니다.

1. **표준** 부하 분산 장치의 경우 이전 섹션에서 만든 것과 동일한 부하 분산 장치에서 아래 추가 단계를 수행합니다.

   a. 두 번째 프런트 엔드 IP 풀 만들기: 

   - 부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
   - 두 번째 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-secondaryIP**).
   - **할당** 을 **정적** 으로 설정하고 IP 주소(예: **10.0.0.14**)를 입력합니다.
   - **확인** 을 선택합니다.
   - 새 프런트 엔드 IP 풀을 만든 후, 프런트 엔드 IP 주소를 적어 둡니다.

   b. 다음으로, 상태 프로브를 만듭니다.

   - 부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
   - 새 상태 프로브의 이름(예: **hana-secondaryhp**)을 입력합니다.
   - 프로토콜 및 포트 **62603** 으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
   - **확인** 을 선택합니다.

   다. 다음으로 부하 분산 규칙을 만듭니다.

   - 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
   - 새 부하 분산 장치 규칙의 이름(예: **hana-secondarylb**)을 입력합니다.
   - 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀, 상태 프로브를 선택합니다(예: **hana-secondaryIP**, **hana-backend** 및 **hana-secondaryhp**).
   - **HA 포트** 를 선택합니다.
   - **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
   - **부동 IP를 사용하도록 설정** 했는지 확인합니다.
   - **확인** 을 선택합니다.

### <a name="configure-hana-activeread-enabled-system-replication"></a>HANA 활성/읽기 사용 시스템 복제 구성

HANA 시스템 복제를 구성하는 단계는 [SAP HANA 2.0 시스템 복제 구성](#configure-sap-hana-20-system-replication) 섹션에 설명되어 있습니다. 읽기 사용 보조 시나리오를 배포하는 경우 두 번째 노드에서 시스템 복제를 구성하는 동안 **hanasid** adm으로 다음 명령을 실행합니다.

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>활성/읽기 사용 설정을 위한 보조 가상 IP 주소 리소스 추가

다음 명령을 사용하여 두 번째 가상 IP와 적절한 공동 배치 제약 조건을 구성할 수 있습니다.

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 두 번째 가상 IP는 보조 사이트에서 SAPHana 보조 리소스와 함께 실행됩니다.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

다음 섹션에서는 실행할 일반적인 장애 조치(failover) 테스트 집합을 확인할 수 있습니다.

읽기 사용 보조로 구성된 HANA 클러스터를 테스트하는 동안 두 번째 가상 IP 동작에 유의해야 합니다.

1. **SAPHana_HN1_HDB03** 클러스터 리소스를 **hn1-db-1** 로 마이그레이션하면 두 번째 가상 IP가 다른 서버 **hn1-db-0** 으로 이동합니다. AUTOMATED_REGISTER=“false”를 구성하고 HANA 시스템 복제를 자동으로 등록하지 않은 경우 서버를 사용할 수 있고 클러스터 서비스가 온라인 상태가 될 때 두 번째 가상 IP가 **hn1-db-0** 에서 실행됩니다.  

2. 서버 충돌을 테스트할 때 두 번째 가상 IP 리소스(**rsc_secip_HN1_HDB03**)와 Azure Load Balancer 포트 리소스(**rsc_secnc_HN1_HDB03**)는 주 가상 IP 리소스와 함께 주 서버에서 실행됩니다. 보조 서버가 다운 상태인 동안 읽기 사용 HANA 데이터베이스에 연결된 애플리케이션은 주 HANA 데이터베이스에 연결됩니다. 보조 서버를 사용할 수 없는 동안 읽기 사용 HANA 데이터베이스에 연결된 애플리케이션에 액세스할 수 없도록 하려는 동작을 예상할 수 있습니다.
  
3. 보조 서버를 사용할 수 있고 클러스터 서비스가 온라인 상태이면 HANA 시스템 복제를 보조 서버로 등록할 수 없는 경우에도 두 번째 가상 IP 및 포트 리소스가 보조 서버로 자동으로 이동합니다. 해당 서버에서 클러스터 서비스를 시작하기 전에 보조 HANA 데이터베이스를 읽기 사용으로 등록했는지 확인해야 합니다. AUTOMATED_REGISTER=true 매개 변수를 설정하여 보조 데이터베이스를 자동으로 등록하도록 HANA 인스턴스 클러스터 리소스를 구성할 수 있습니다.       

4. 장애 조치(failover) 및 대체 중에 두 번째 가상 IP를 사용하여 HANA 데이터베이스에 연결하는 애플리케이션에 대한 기존 연결이 중단될 수 있습니다.  

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 설정을 테스트하는 방법을 설명합니다. 모든 테스트에서는 사용자가 루트이고 SAP HANA 마스터가 **hn1-db-0** 가상 머신에서 실행 중이라고 가정합니다.

### <a name="test-the-migration"></a>마이그레이션 테스트

테스트를 시작하기 전에 Pacemaker에 실패한 작업이 없으며(crm_mon -r을 통해), 예기치 않은 위치 제약 조건이 없고(예: 마이그레이션 테스트의 결과), SAPHanaSR-showAttr을 사용하는 것처럼 해당 HANA가 동기화 상태에 있는지 확인합니다.

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr
Sites    srHook
----------------
SITE2    SOK

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

다음 명령을 실행하여 SAP HANA 마스터 노드를 마이그레이션할 수 있습니다.

<pre><code>crm resource move msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b> force
</code></pre>

`AUTOMATED_REGISTER="false"`를 설정하면 이 명령 시퀀스가 SAP HANA 마스터 노드와 가상 IP 주소를 포함하는 그룹을 hn1-db-1로 마이그레이션해야 합니다.

마이그레이션이 완료되면 crm_mon -r 출력은 다음과 같습니다.

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

hn1-db-0에서 SAP HANA 리소스를 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

마이그레이션을 통해 다시 삭제해야 하는 위치 제약조건을 만듭니다.

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource clear msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

보조 노드 리소스의 상태도 정리해야 합니다.

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

crm_mon -r을 사용하여 HANA 리소스의 상태를 모니터링합니다. HANA가 hn1-db-0에서 시작되면 출력은 다음과 같습니다.

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Azure 펜싱 에이전트 테스트(SBD 아님)

hn1-db-0 노드에서 네트워크 인터페이스를 사용하지 않도록 설정하여 Azure 펜싱 에이전트의 설정을 테스트할 수 있습니다.

<pre><code>sudo ifdown eth0
</code></pre>

클러스터 구성에 따라 이제 가상 머신을 다시 시작하거나 중지해야 합니다.
`stonith-action`을 off로 설정하면 가상 머신이 중지되고 리소스가 실행 중인 가상 머신으로 마이그레이션됩니다.

가상 머신을 다시 시작한 후, `AUTOMATED_REGISTER="false"`로 설정한 경우 SAP HANA 리소스가 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>SBD 펜싱 테스트

인퀴지터 프로세스를 종료하여 SBD의 설정을 테스트할 수 있습니다.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

클러스터 노드 hn1-db-0을 다시 부팅해야 합니다. 나중에 Pacemaker 서비스를 시작하지 못할 수도 있습니다. 다시 시작해야 합니다.

### <a name="test-a-manual-failover"></a>수동 장애 조치(failover) 테스트

hn1-db-0 노드에서 `pacemaker` 서비스를 중지하여 수동 장애 조치(failover)를 테스트할 수 있습니다.

<pre><code>service pacemaker stop
</code></pre>

장애 조치 후에 서비스를 다시 시작할 수 있습니다. `AUTOMATED_REGISTER="false"`로 설정하면 hn1-db-0 노드에서 SAP HANA 리소스를 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE 테스트

> [!IMPORTANT]
> 선택한 OS가 사용 중인 특정 VM 유형에서 SAP HANA용으로 인증된 SAP인지 반드시 확인하세요. 이 항목에 대한 SAP HANA 인증 VM 형식 및 OS 릴리스 목록은 [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 조회할 수 있습니다. 특정 VM 형식에 대한 SAP HANA 지원 OS 릴리스의 전체 목록을 보려면 목록에 있는 VM 유형의 세부 정보를 클릭하세요.

사용 사례에 따라 SAP HANA SR 성능 최적화 시나리오 또는 SAP HANA SR 비용 최적화 시나리오 가이드에 나열된 모든 테스트 사례를 실행합니다. 가이드는 [SAP용 SLES 모범 사례 페이지][sles-for-sap-bp]에서 찾을 수 있습니다.

다음 테스트는 SAP HANA SR 성능 최적화 시나리오 SAP 애플리케이션 12 SP1용 SUSE Linux Enterprise Server 가이드의 테스트 설명을 복사한 것입니다. 최신 버전의 경우 항상 자체 가이드를 읽습니다. 테스트를 시작하기 전에 항상 HANA가 동기화되어 있는지 확인하고, Pacemaker 구성이 올바른지 확인합니다.

다음 테스트 설명에서는 PREFER_SITE_TAKEOVER=“true” 및 AUTOMATED_REGISTER=“false”로 가정합니다.
참고: 다음 테스트는 이전 테스트가 종료된 상태에서 시퀀스에서 실행하도록 설계되었습니다.

1. 테스트 1: 노드 1에서 주 데이터베이스 중지

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   노드 hn1-db-0에서 <hanasid\>adm으로 다음 명령을 실행합니다.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker는 중지된 HANA 인스턴스 및 다른 노드로의 장애 조치(failover)를 감지해야 합니다. 장애 조치(failover)가 완료되면 Pacemaker는 노드를 HANA 보조로 자동으로 등록하지 않으므로 노드 hn1-db-0에서 HANA 인스턴스가 중지됩니다.

   다음 명령을 실행하여 노드 hn1-db-0을 보조로 등록하고 실패한 리소스를 정리합니다.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 테스트 2: 노드 2에서 주 데이터베이스 중지

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   노드 hn1-db-1에서 <hanasid\>adm으로 다음 명령을 실행합니다.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker는 중지된 HANA 인스턴스 및 다른 노드로의 장애 조치(failover)를 감지해야 합니다. 장애 조치(failover)가 완료되면 Pacemaker는 노드를 HANA 보조로 자동으로 등록하지 않으므로 노드 hn1-db-1에서 HANA 인스턴스가 중지됩니다.

   다음 명령을 실행하여 노드 hn1-db-1을 보조로 등록하고 실패한 리소스를 정리합니다.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 테스트 3: 노드에서 주 데이터베이스 크래시

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   노드 hn1-db-0에서 <hanasid\>adm으로 다음 명령을 실행합니다.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker는 중지된 HANA 인스턴스 및 다른 노드로의 장애 조치(failover)를 감지해야 합니다. 장애 조치(failover)가 완료되면 Pacemaker는 노드를 HANA 보조로 자동으로 등록하지 않으므로 노드 hn1-db-0에서 HANA 인스턴스가 중지됩니다.

   다음 명령을 실행하여 노드 hn1-db-0을 보조로 등록하고 실패한 리소스를 정리합니다.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 테스트 4: 노드 2에서 주 데이터베이스 크래시

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   노드 hn1-db-1에서 <hanasid\>adm으로 다음 명령을 실행합니다.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker는 중지된 HANA 인스턴스 및 다른 노드로의 장애 조치(failover)를 감지해야 합니다. 장애 조치(failover)가 완료되면 Pacemaker는 노드를 HANA 보조로 자동으로 등록하지 않으므로 노드 hn1-db-1에서 HANA 인스턴스가 중지됩니다.

   다음 명령을 실행하여 노드 hn1-db-1을 보조로 등록하고 실패한 리소스를 정리합니다.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 테스트 5: 주 사이트 노드 크래시(노드 1)

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   노드 hn1-db-0에서 루트로 다음 명령을 실행합니다.

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker는 중지된 클러스터 노드를 감지하고 노드를 펜스해야 합니다. 노드가 펜스되면 Pacemaker는 HANA 인스턴스의 인수를 트리거합니다. 펜스된 노드가 다시 부팅되는 경우 Pacemaker는 자동으로 시작되지 않습니다.

   다음 명령을 실행하여 Pacemaker를 시작하고, 노드 hn1-db-0에 대한 SBD 메시지를 정리하고, 노드 hn1-db-0을 보조로 등록하고, 실패한 리소스를 정리합니다.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 테스트 6: 보조 사이트 노드 크래시(노드 2)

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   노드 hn1-db-1에서 루트로 다음 명령을 실행합니다.

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker는 중지된 클러스터 노드를 감지하고 노드를 펜스해야 합니다. 노드가 펜스되면 Pacemaker는 HANA 인스턴스의 인수를 트리거합니다. 펜스된 노드가 다시 부팅되는 경우 Pacemaker는 자동으로 시작되지 않습니다.

   다음 명령을 실행하여 Pacemaker를 시작하고, 노드 hn1-db-1에 대한 SBD 메시지를 정리하고, 노드 hn1-db-1을 보조로 등록하고, 실패한 리소스를 정리합니다.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 테스트 7: 노드 2에서 보조 데이터베이스 중지

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   노드 hn1-db-1에서 <hanasid\>adm으로 다음 명령을 실행합니다.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker는 중지된 HANA 인스턴스를 감지하고 리소스를 노드 hn1-db-1에서 실패한 것으로 표시합니다. Pacemaker는 HANA 인스턴스를 자동으로 다시 시작해야 합니다. 다음 명령을 실행하여 실패한 상태를 정리합니다.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 테스트 8: 노드 2에서 보조 데이터베이스 크래시

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   노드 hn1-db-1에서 <hanasid\>adm으로 다음 명령을 실행합니다.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker는 중지된 HANA 인스턴스를 감지하고 리소스를 노드 hn1-db-1에서 실패한 것으로 표시합니다. 다음 명령을 실행하여 실패한 상태를 정리합니다. Pacemaker는 HANA 인스턴스를 자동으로 다시 시작해야 합니다.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 테스트 9: 보조 HANA 데이터베이스를 실행 중인 보조 사이트 노드 크래시(노드 2)

   테스트 시작 전 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   노드 hn1-db-1에서 루트로 다음 명령을 실행합니다.

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker는 중지된 클러스터 노드를 감지하고 노드를 펜스해야 합니다. 펜스된 노드가 다시 부팅되는 경우 Pacemaker는 자동으로 시작되지 않습니다.

   다음 명령을 실행하여 Pacemaker를 시작하고, 노드 hn1-db-1에 대한 SBD 메시지를 정리하고, 실패한 리소스를 정리합니다.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]