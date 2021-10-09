---
title: RHEL의 Azure VM에 있는 SAP HANA의 고가용성 | Microsoft Docs
description: Azure VM(Virtual Machines)에서 SAP HANA의 고가용성을 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/08/2021
ms.author: radeltch
ms.openlocfilehash: fb99396b141ad2d9b8c15e5f250396d32ee9f46e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708725"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux의 Azure VM에 있는 SAP HANA의 고가용성

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

온-프레미스 개발에서 HANA 시스템 복제를 사용하거나 공유 스토리지를 사용하여 SAP HANA의 고가용성을 설정할 수 있습니다.
Azure VM(Virtual Machines)에서 Azure의 HANA 시스템 복제는 현재 지원되는 유일한 고가용성 기능입니다.
SAP HANA 복제는 하나의 기본 노드와 하나 이상의 보조 노드로 구성됩니다. 기본 노드의 데이터를 변경하면 보조 노드에 동기적 또는 비동기적으로 복제됩니다.

이 문서에서는 가상 머신을 배포 및 구성하며, 클러스터 프레임워크를 설치하고, SAP HANA 시스템 복제를 설치 및 구성하는 방법을 설명합니다.
예제 구성에서 설치 명령, 인스턴스 번호 **03** 및 HANA 시스템 ID **HN1** 이 사용됩니다.

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록.
  * Azure VM 크기에 대한 중요한 용량 정보.
  * 지원되는 SAP 소프트웨어 및 OS(운영 체제)와 데이터베이스 조합.
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전.
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2002167]에는 Red Hat Enterprise Linux에 권장되는 OS 설정이 있습니다.
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포(이 문서)][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)(Pacemaker 클러스터의 SAP HANA 시스템 복제)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
* Azure 특정 RHEL 설명서:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)(Microsoft Azure에서 사용할 용도로 Red Hat Enterprise Linux에 SAP HANA 설치)

## <a name="overview"></a>개요

고가용성을 얻기 위해 두 개의 가상 머신에 SAP HANA가 설치됩니다. HANA 시스템 복제를 사용하여 데이터가 복제됩니다.

![SAP HANA 고가용성 개요](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA 시스템 복제 설정은 전용 가상 호스트 이름과 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 다음 목록에는 부하 분산 장치의 구성이 나와 있습니다.

* 프런트 엔드 구성: hn1-db의 IP 주소 10.0.0.13
* 백 엔드 구성: HANA 시스템 복제의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트: 포트 62503
* 부하 분산 규칙: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Linux에 배포

Azure Marketplace에는 새 가상 머신을 배포하는 데 사용할 수 있는 Red Hat Enterprise Linux 7.4 for SAP HANA용 이미지가 있습니다.

### <a name="deploy-with-a-template"></a>템플릿을 사용하여 배포

GitHub에서 빠른 시작 템플릿 중 하나를 사용하여 필요한 모든 리소스를 배포할 수 있습니다. 템플릿에서 가상 머신, 부하 분산 장치, 가용성 집합 등을 배포합니다.
템플릿을 배포하려면 다음 단계를 따릅니다.

1. Azure Portal에서 [데이터베이스 템플릿][template-multisid-db]을 엽니다.
1. 다음 매개 변수를 입력합니다.
    * **SAP 시스템 ID**: 설치하려는 SAP 시스템의 SAP 시스템 ID를 입력합니다. 이 ID는 배포되는 리소스의 접두사로 사용됩니다.
    * **OS 유형**: Linux 배포판 중 하나를 선택합니다. 이 예에서는 **RHEL 7** 을 선택합니다.
    * **Db 형식**: **HANA** 를 선택합니다.
    * **SAP 시스템 크기**: 새 시스템에서 제공할 SAP의 수를 입력합니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
    * **시스템 가용성**: **HA** 를 선택합니다.
    * **관리자 사용자 이름, 관리자 암호 또는 SSH 키**: 머신에 로그인하는 데 사용할 수 있게 만들어진 새 사용자입니다.
    * **서브넷 ID**: 서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 일반적으로 **/subscriptions/\<subscription ID>/resourceGroups/\<resource group name>/providers/Microsoft.Network/virtualNetworks/\<virtual network name>/subnets/\<subnet name>** 형태입니다. 새 가상 네트워크를 만들려면 공백으로 두세요.

### <a name="manual-deployment"></a>수동 배포

1. 리소스 그룹을 만듭니다.
1. 가상 네트워크를 만듭니다.
1. 가용성 집합을 만듭니다.  
   업데이트 도메인의 최대 수를 설정합니다.
1. 부하 분산 장치(내부)를 만듭니다. [표준 부하 분산 장치](../../../load-balancer/load-balancer-overview.md)를 만드는 것이 좋습니다.
   * 2단계에서 만든 가상 네트워크를 선택합니다.
1. 가상 머신 1을 만듭니다.  
   최소한 Red Hat Enterprise Linux 7.4 for SAP HANA를 사용합니다. 이 예제에서는 Red Hat Enterprise Linux 7.4 for SAP HANA 이미지(<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM>)를 사용합니다. 3단계에서 만든 가용성 집합을 선택합니다.
1. 가상 머신 2를 만듭니다.  
   최소한 Red Hat Enterprise Linux 7.4 for SAP HANA를 사용합니다. 이 예제에서는 Red Hat Enterprise Linux 7.4 for SAP HANA 이미지(<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM>)를 사용합니다. 3단계에서 만든 가용성 집합을 선택합니다.
1. 데이터 디스크를 추가합니다.

> [!IMPORTANT]
> 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.    

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
      1. **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
      1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
      1. **확인** 을 선택합니다.


1. 또는 시나리오에서 기본 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
   1. 부하 분산 장치를 구성합니다. 먼저 프런트 엔드 IP 풀을 만듭니다.

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

## <a name="install-sap-hana"></a>SAP HANA 설치

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

* **[A]** : 단계가 모든 노드에 적용됩니다.
* **[1]** : 단계가 노드 1에만 적용됩니다.
* **[2]** : 단계가 Pacemaker 클러스터의 노드 2에만 적용됩니다.

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

1. **[A]** HANA용 RHEL 구성

   <https://access.redhat.com/solutions/2447641> 및 다음 SAP Note에 설명된 대로 RHEL을 구성합니다.  
   - [2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: RHEL 8에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: GCC 6.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: GCC 7.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: GCC 9.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** SAP HANA 설치

   SAP HANA 시스템 복제를 설치하려면 <https://access.redhat.com/articles/3004101>의 설명을 따르세요.

   * HANA DVD에서 **hdblcm** 프로그램을 실행합니다. 프롬프트에서 다음 값을 입력합니다.
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

1. **[A]** 방화벽 구성

   Azure 부하 분산 장치 프로브 포트의 방화벽 규칙을 만듭니다.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 시스템 복제 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

* **[A]** : 단계가 모든 노드에 적용됩니다.
* **[1]** : 단계가 노드 1에만 적용됩니다.
* **[2]** : 단계가 Pacemaker 클러스터의 노드 2에만 적용됩니다.

1. **[A]** 방화벽 구성

   HANA 시스템 복제 및 클라이언트 트래픽을 허용하는 방화벽 규칙을 만듭니다. 필수 포트 목록은 [모든 SAP 제품의 TCP/IP 포트](https://help.sap.com/viewer/ports)에 나열되어 있습니다. 다음 명령은 HANA 2.0 시스템 복제 및 데이터베이스 SYSTEMDB, HN1, NW1에 대한 클라이언트 트래픽을 허용하는 예입니다.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

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

1. **[1]** 복제 상태 확인

   복제 상태를 확인하고 모든 데이터베이스가 동기화될 때까지 기다립니다. 상태가 여전히 UNKNOWN으로 남아 있는 경우 방화벽 설정을 확인합니다.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0 시스템 복제 구성

이 섹션의 다음 단계에서는 다음과 같은 접두사를 사용합니다.

* **[A]** : 단계가 모든 노드에 적용됩니다.
* **[1]** : 단계가 노드 1에만 적용됩니다.
* **[2]** : 단계가 Pacemaker 클러스터의 노드 2에만 적용됩니다.

1. **[A]** 방화벽 구성

   HANA 시스템 복제 및 클라이언트 트래픽을 허용하는 방화벽 규칙을 만듭니다. 필수 포트 목록은 [모든 SAP 제품의 TCP/IP 포트](https://help.sap.com/viewer/ports)에 나열되어 있습니다. 다음 명령은 HANA 2.0 시스템 복제를 허용하는 예제입니다. SAP HANA 1.0 설치에 적용하세요.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정](high-availability-guide-rhel-pacemaker.md) 단계에 따라 이 HANA 서버용 기본 Pacemaker 클러스터를 만듭니다.

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Python 시스템 복제 후크 SAPHanaSR 구현

이는 클러스터와의 통합을 최적화하고 클러스터 장애 조치(failover)가 필요할 때 검색 기능을 향상시키는 중요한 단계입니다. SAPHanaSR python 후크를 구성하는 것이 좋습니다.    

1. **[A]** HANA "시스템 복제 후크"를 설치합니다. 후크는 두 개의 HANA DB 노드에 모두 설치해야 합니다.           

   > [!TIP]
   > Python 후크는 HANA 2.0에서만 구현할 수 있습니다.        

   1. 후크를 `root`로 준비합니다.  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
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
    sudo visudo -f /etc/sudoers.d/20-saphana
    # Insert the following lines and then save
    Cmnd_Alias SITE1_SOK   = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE1 -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE1_SFAIL = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE1 -v SFAIL -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE2_SOK   = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE2 -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE2_SFAIL = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE2 -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SITE1_SOK, SITE1_SFAIL, SITE2_SOK, SITE2_SFAIL
    Defaults!SITE1_SOK, SITE1_SFAIL, SITE2_SOK, SITE2_SFAIL !requiretty
    ```

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
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

SAP HANA 시스템 복제 후크 구현에 대한 자세한 내용은 [SAP HA/DR 공급자 후크 사용](https://access.redhat.com/articles/3004101#enable-srhook)을 참조하세요.  
 
## <a name="create-sap-hana-cluster-resources"></a>SAP HANA 클러스터 리소스 만들기

**모든 노드** 에 SAP HANA 리소스 에이전트를 설치합니다. 패키지가 포함된 리포지토리를 사용하도록 설정해야 합니다. RHEL 8.x HA 사용 이미지를 사용하는 경우 추가 리포지토리를 사용하도록 설정할 필요가 없습니다.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

다음으로, HANA 토폴로지를 만듭니다. Pacemaker 클러스터 노드 중 하나에서 다음 명령을 실행합니다.

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

다음에는 HANA 리소스를 만듭니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *슬레이브* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

**RHEL 7.x** 에 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

**RHEL 8.x** 에 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

> [!NOTE]
> 위의 구성에서 시간 제한은 단지 예제이며 특정 HANA 설정에 맞게 조정해야 할 수 있습니다. 예를 들어 SAP HANA 데이터베이스를 시작하는 데 시간이 더 오래 걸리는 경우 시작 시간 제한을 늘려야 할 수 있습니다.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Pacemaker 클러스터에서 HANA 활성/읽기 사용 시스템 복제 구성

SAP HANA 2.0 SPS 01부터 SAP는 SAP HANA 시스템 복제를 위한 활성/읽기 사용 설정을 허용합니다. 여기서 SAP HANA 시스템 복제의 보조 시스템은 읽기 집약적인 워크로드에 적극적으로 사용할 수 있습니다. 클러스터에서 이러한 설정을 지원하려면 클라이언트가 보조 읽기 사용 SAP HANA 데이터베이스에 액세스할 수 있도록 두 번째 가상 IP 주소가 필요합니다. 인수가 발생한 후에도 보조 복제 사이트에 액세스할 수 있도록 클러스터에서 가상 IP 주소를 SAPHana 리소스의 보조로 이동해야 합니다.

이 섹션에서는 두 번째 가상 IP가 있는 Red Hat 고가용성 클러스터에서 HANA 활성/읽기 지원 시스템 복제를 관리하는 데 필요한 추가 단계를 설명합니다.    

계속 진행하기 전에 설명서의 상단 세그먼트에 설명된 대로 SAP HANA 데이터베이스를 관리하는 Red Hat 고가용성 클러스터를 완전히 구성했는지 확인합니다.  

![읽기 지원 보조를 통한 SAP HANA 고가용성](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>활성/읽기 사용 설정을 위한 Azure Load Balancer 추가 설정

두 번째 가상 IP를 프로비저닝하는 추가 단계를 진행하려면 [수동 배포](#manual-deployment) 섹션에 설명된 대로 Azure Load Balancer를 구성했는지 확인합니다.

1. **표준** 부하 분산 장치의 경우 이전 섹션에서 만든 것과 동일한 부하 분산 장치에서 아래 추가 단계를 수행합니다.

   a. 두 번째 프런트 엔드 IP 풀 만들기: 

   - 부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
   - 두 번째 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-secondaryIP**).
   - **할당** 을 **정적** 으로 설정하고 IP 주소(예: **10.0.0.14**)를 입력합니다.
   - **확인** 을 선택합니다.
   - 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

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
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 두 번째 가상 IP는 보조 사이트에서 SAPHana 보조 리소스와 함께 실행됩니다.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

다음 섹션에서는 실행할 일반적인 장애 조치(failover) 테스트 집합을 확인할 수 있습니다.

읽기 사용 보조로 구성된 HANA 클러스터를 테스트하는 동안 두 번째 가상 IP 동작에 유의해야 합니다.

1. **SAPHana_HN1_HDB03** 클러스터 리소스를 **hn1-db-1** 로 마이그레이션하면 두 번째 가상 IP가 다른 서버 **hn1-db-0** 으로 이동합니다. AUTOMATED_REGISTER=“false”를 구성하고 HANA 시스템 복제를 자동으로 등록하지 않은 경우 서버를 사용할 수 있고 클러스터 서비스가 온라인 상태가 될 때 두 번째 가상 IP가 **hn1-db-0** 에서 실행됩니다.  

2. 서버 충돌을 테스트할 때 두 번째 가상 IP 리소스(**rsc_secip_HN1_HDB03**)와 Azure Load Balancer 포트 리소스(**rsc_secnc_HN1_HDB03**)는 주 가상 IP 리소스와 함께 주 서버에서 실행됩니다.  보조 서버가 다운 상태인 동안 읽기 사용 HANA 데이터베이스에 연결된 애플리케이션은 주 HANA 데이터베이스에 연결됩니다. 보조 서버를 사용할 수 없는 동안 읽기 사용 HANA 데이터베이스에 연결된 애플리케이션에 액세스할 수 없도록 하려는 동작을 예상할 수 있습니다.

3. 보조 서버를 사용할 수 있고 클러스터 서비스가 온라인 상태이면 HANA 시스템 복제가 보조 서버로 등록되지 않은 경우에도 두 번째 가상 IP 및 포트 리소스가 보조 서버로 자동 이동합니다. 해당 서버에서 클러스터 서비스를 시작하기 전에 보조 HANA 데이터베이스를 읽기 사용으로 등록했는지 확인해야 합니다. AUTOMATED_REGISTER=true 매개 변수를 설정하여 보조 데이터베이스를 자동으로 등록하도록 HANA 인스턴스 클러스터 리소스를 구성할 수 있습니다.
   
4. 장애 조치(failover) 및 대체 중에 두 번째 가상 IP를 사용하여 HANA 데이터베이스에 연결하는 애플리케이션에 대한 기존 연결이 중단될 수 있습니다.  

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 설정을 테스트하는 방법을 설명합니다. 테스트를 시작하기 전에 Pacemaker에 실패한 작업이 없으며(pcs status를 통해), 예기치 않은 위치 제약 조건이 없고(예를 들어 마이그레이션 테스트의 결과), systemReplicationStatus의 경우처럼 해당 HANA가 동기화 상태에 있는지 확인합니다.

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>마이그레이션 테스트

테스트 시작 전 리소스 상태:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

다음 명령을 실행하여 SAP HANA 마스터 노드를 마이그레이션할 수 있습니다.

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

`AUTOMATED_REGISTER="false"`를 설정하는 경우 이 명령은 SAP HANA 마스터 노드와 가상 IP 주소를 포함하는 그룹을 hn1-db-1로 마이그레이션해야 합니다.

마이그레이션이 완료되면 ‘sudo pcs status’ 출력은 다음과 같습니다.

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

hn1-db-0의 SAP HANA 리소스가 중지되었습니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

마이그레이션을 통해 다시 삭제해야 하는 위치 제약조건을 만듭니다.

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

‘pcs status’를 사용하여 HANA 리소스의 상태를 모니터링합니다. HANA가 hn1-db-0에서 시작되면 출력은 다음과 같습니다.

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Azure 펜싱 에이전트 테스트

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *슬레이브* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.  

테스트 시작 전 리소스 상태:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

SAP HANA가 마스터로 실행 중인 노드에서 네트워크 인터페이스를 사용하지 않도록 설정하여 Azure 펜싱 에이전트의 설정을 테스트할 수 있습니다.
네트워크 장애를 시뮬레이션하는 방법에 대한 설명은 [Red Hat 기술 자료 문서 79523](https://access.redhat.com/solutions/79523)을 참조하세요. 이 예제에서는 net_breaker 스크립트를 사용하여 네트워크에 대한 모든 액세스를 차단합니다.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

클러스터 구성에 따라 이제 가상 머신을 다시 시작하거나 중지해야 합니다.
`stonith-action`을 off로 설정하면 가상 머신이 중지되고 리소스가 실행 중인 가상 머신으로 마이그레이션됩니다.

가상 머신을 다시 시작한 후, `AUTOMATED_REGISTER="false"`로 설정한 경우 SAP HANA 리소스가 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

테스트 후 리소스 상태:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>수동 장애 조치(failover) 테스트

테스트 시작 전 리소스 상태:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

hn1-db-0 노드에서 클러스터를 중지하여 수동 장애 조치(failover)를 테스트할 수 있습니다.

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

장애 조치(failover) 후에 클러스터를 다시 시작할 수 있습니다. `AUTOMATED_REGISTER="false"`로 설정하면 hn1-db-0 노드에서 SAP HANA 리소스를 보조로 시작하는 데 실패합니다. 이 경우 다음 명령을 실행하여 HANA 인스턴스를 보조로 구성합니다.

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

테스트 후 리소스 상태:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>수동 장애 조치(failover) 테스트

테스트 시작 전 리소스 상태:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

hn1-db-0 노드에서 클러스터를 중지하여 수동 장애 조치(failover)를 테스트할 수 있습니다.

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA VM 스토리지 구성](./hana-vm-operations-storage.md)