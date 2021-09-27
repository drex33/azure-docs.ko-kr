---
title: RHEL의 Azure VM(가상 머신)에 IBM Db2 HADR 설정 | Microsoft Docs
description: Azure VM(가상 머신) RHEL에서 IBM Db2 LUW의 고가용성을 설정합니다.
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/27/2021
ms.author: juergent
ms.openlocfilehash: 99ea40f7fcfe0a5d3f34c9c9a9a7bbee58fd1186
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562405"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server의 Azure VM에서 IBM DB2 LUW의 고가용성

[HADR(고가용성 및 재해 복구) 구성](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)의 LUW(Linux, UNIX 및 Windows)용 IBM Db2는 주 데이터베이스 인스턴스를 실행하는 노드 하나와 보조 데이터베이스 인스턴스를 실행하는 하나 이상의 노드로 구성됩니다. 주 데이터베이스 인스턴스에 대한 변경 사항은 구성에 따라 동기식 또는 비동기식으로 보조 데이터베이스 인스턴스에 복제됩니다. 

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *마스터* 및 *종속* 용어에 대한 참조가 포함되어 있습니다. 이러한 용어가 소프트웨어에서 제거되면 이 문서에서도 제거할 것입니다.

이 문서에서는 Azure VM(가상 머신)을 배포 및 구성하고, 클러스터 프레임워크를 설치하고, HADR 구성이 포함된 IBM Db2 LUW를 설치하는 방법을 설명합니다. 

SAP 소프트웨어 설치 또는 HADR이 포함된 IBM Db2 LUW를 설치하고 구성하는 방법은 이 문서에서 다루지 않습니다. 이러한 작업을 수행하는 데 도움이 되도록 SAP 및 IBM 설치 설명서에 대한 참조가 제공됩니다. 이 문서에서는 Azure 환경과 관련된 부분에 대해 중점을 두고 설명합니다. 

지원되는 IBM Db2 버전은 10.5 이상이며, SAP 노트 [1928533]에 설명되어 있습니다.

설치를 시작하기 전에 다음 SAP 노트 및 설명서를 참조하세요.

| SAP 노트 | Description |
| --- | --- |
| [1928533] | Azure의 SAP 애플리케이션: 지원 제품 및 Azure VM 유형 |
| [2015553] | Azure의 SAP: 필수 구성 요소 지원 |
| [2178632] | Azure의 SAP에 대한 주요 모니터링 메트릭 |
| [2191498] | Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2243692] | Linux on Azure(IaaS) VM: SAP 라이선스 문제 |
| [2002167] | Red Hat Enterprise Linux 7.x: 설치 및 업그레이드 |
| [2694118] | Azure의 Red Hat Enterprise Linux HA 추가 항목 |
| [1999351] | SAP용 고급 Azure 모니터링 문제 해결 |
| [2233094] | DB6: Linux, UNIX 및 Windows용 IBM Db2를 사용하는 Azure의 SAP 애플리케이션 - 추가 정보 |
| [1612105] | DB6: HADR이 포함된 Db2에 대한 FAQ |


| 설명서 | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux에 필요한 모든 SAP 노트를 포함하고 있습니다. |
| [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide] 가이드 |
| [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide](이 문서) |
| [Linux에서 SAP용 Azure Virtual Machines DBMS(데이터베이스 관리 시스템) 배포][dbms-guide] 가이드 |
| [Azure의 SAP 워크로드 계획 및 배포 검사 목록][azr-sap-plancheck] |
| [Red Hat Enterprise Linux 7용 고가용성 추가 항목 개요][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin](고가용성 추가 기능 관리) |
| [High Availability Add-On Reference][rhel-ha-ref](고가용성 추가 기능 참조) |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp](RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst](Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
| [SAP 워크로드용 IBM DB2 Azure Virtual Machines DBMS 배포][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [RHEL 고가용성 클러스터에 대한 지원 정책 - 클러스터에서 Linux, Unix 및 Windows용 IBM Db2 관리][rhel-db2-supp]



## <a name="overview"></a>개요
고가용성을 달성하기 위해 HADR이 포함된 IBM Db2 LUW는 [Azure 가용성 집합](../../windows/tutorial-availability-sets.md) 또는 [Azure 가용성 영역](./sap-ha-availability-zones.md)에 배포되는 둘 이상의 Azure 가상 머신에 설치됩니다. 

다음 그래픽에는 두 데이터베이스 서버 Azure VM의 설정이 나와 있습니다. 두 데이터베이스 서버 Azure VM은 각자 자체 스토리지와 연결되어 있고 실행 중입니다. HADR에서는 Azure VM 중 하나의 데이터베이스 인스턴스 하나가 주 인스턴스 역할을 갖습니다. 모든 클라이언트는 주 인스턴스에 연결됩니다. 데이터베이스 트랜잭션의 모든 변경 내용은 로컬에서 Db2 트랜잭션 로그에 유지됩니다. 트랜잭션 로그 레코드가 로컬에 유지되면서, TCP/IP를 통해 두 번째 데이터베이스 서버, 대기 서버 또는 대기 인스턴스의 데이터베이스 인스턴스로 레코드가 전송됩니다. 대기 인스턴스는 전송된 트랜잭션 로그 레코드를 롤포워드하여 로컬 데이터베이스를 업데이트합니다. 이러한 방식으로 대기 서버는 주 서버와 동기화된 상태를 유지합니다.

HADR은 복제 기능일 뿐입니다. 오류 검색 및 자동 인수 또는 장애 조치(failover) 기능은 없습니다. 대기 서버로의 전송이나 인수는 데이터베이스 관리자가 수동으로 시작해야 합니다. 자동 인수 및 오류 검색을 수행하기 위해 Linux Pacemaker 클러스터링 기능을 사용할 수 있습니다. Pacemaker는 두 개의 데이터베이스 서버 인스턴스를 모니터링합니다. 주 데이터베이스 서버 인스턴스가 충돌하면 Pacemaker는 대기 서버에서 *자동* HADR 인수를 시작합니다. 또한 Pacemaker는 가상 IP 주소가 새 주 서버에 할당되도록 합니다.

![IBM Db2 고가용성 개요](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

SAP 애플리케이션 서버를 주 데이터베이스에 연결하려면 가상 호스트 이름 및 가상 IP 주소가 필요합니다. 장애 조치(failover)가 발생하면 SAP 애플리케이션 서버는 새로운 주 데이터베이스 인스턴스에 연결됩니다. Azure 환경에서 IBM Db2의 HADR에 필요한 방식으로 가상 IP 주소를 사용하려면 [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)가 필요합니다. 

HADR 및 Pacemaker가 포함된 IBM Db2 LUW가 고가용성 SAP 시스템 설정에 얼마나 적합한지 완전히 이해하는 데 참고할 수 있는 IBM Db2 데이터베이스에 기반한 SAP 시스템의 고가용성 설정에 대한 개요가 아래 이미지에 제공됩니다. 이 문서에서는 IBM Db2에 대해서만 설명하고 SAP 시스템의 다른 구성 요소를 설정하는 방법에 대해서는 다른 문서에 대한 참조를 제공합니다.

![IBM DB2 고가용성 전체 환경 개요](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>필요한 단계에 대한 대략적인 개요
IBM Db2 구성을 배포하려면 다음 단계를 수행해야 합니다.

  + 환경을 계획합니다.
  + VM을 배포합니다.
  + RHEL Linux를 업데이트하고 파일 시스템을 구성합니다.
  + Pacemaker를 설치하고 구성합니다.
  + [glusterfs 클러스터][glusterfs] 또는 [Azure NetApp Files][anf-rhel]를 설정합니다.
  + [별도의 클러스터에 ASCS/ERS][ascs-ha-rhel]를 설치합니다.
  + 분산형/고가용성 옵션(SWPM)을 사용하여 IBM Db2 데이터베이스를 설치합니다.
  + 보조 데이터베이스 노드 및 인스턴스를 설치 및 생성하고 HADR을 구성합니다.
  + HADR이 작동하는지 확인합니다.
  + Pacemaker 구성을 적용하여 IBM Db2를 제어합니다.
  + Azure Load Balancer를 구성합니다.
  + 주 애플리케이션 서버 및 대화 애플리케이션 서버를 설치합니다.
  + SAP 애플리케이션 서버의 구성을 확인하고 조정합니다.
  + 장애 조치(failover) 및 인수 테스트를 수행합니다.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>HADR이 포함된 IBM Db2 LUW를 호스트하기 위한 Azure 인프라 계획

배포를 실행하기 전에 계획 프로세스를 완료합니다. 계획을 통해 Azure에서 HADR이 포함된 Db2 구성을 배포하기 위한 토대를 구축합니다. IMB Db2 LUW(SAP 환경의 데이터베이스 부분) 계획에 포함해야 하는 주요 요소는 다음 표에 나와있습니다.

| 토픽 | 간단한 설명 |
| --- | --- |
| Azure 리소스 그룹 정의 | VM, VNet, Azure Load Balancer 및 기타 리소스를 배포하는 리소스 그룹입니다. 기존 그룹을 사용해도 되고 새로 만들어도 됩니다. |
| 가상 네트워크/서브넷 정의 | IBM Db2 및 Azure Load Balancer용 VM이 배포되는 위치입니다. 기존 위치를 사용해도 되고 새로 만들어도 됩니다. |
| IBM Db2 LUW를 호스트하는 가상 머신 | VM 크기, 스토리지, 네트워킹, IP 주소입니다. |
| IBM Db2 데이터베이스의 가상 호스트 이름 및 가상 IP| SAP 애플리케이션 서버 연결에 사용되는 가상 IP 또는 호스트 이름입니다. **db-virt-hostname**, **db-virt-ip**. |
| Azure 펜싱 | 스플릿 브레인 상황을 피하는 메서드는 금지됩니다. |
| Azure Load Balancer | 기본 또는 표준(권장), Db2 데이터베이스용 프로브 포트(권장 62500) **probe-port** 를 사용합니다. |
| 이름 확인| 환경에서 이름 확인이 작동하는 방식입니다. DNS 서비스가 적극 권장됩니다. 로컬 호스트 파일을 사용할 수 있습니다. |
    
Azure의 Linux Pacemaker에 대한 자세한 내용은 [Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정][rhel-pcs-azr]을 참조하세요.

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에 배포

IBM Db2 LUW의 리소스 에이전트는 Red Hat Enterprise Linux Server HA 추가 항목에 포함되어 있습니다. 이 문서에서 설명하는 설치의 경우 Red Hat Enterprise Linux for SAP를 사용해야 합니다. Azure Marketplace에는 새 Azure 가상 머신을 배포하는 데 사용할 수 있는 Red Hat Enterprise Linux 7.4 for SAP 이상의 이미지가 포함되어 있습니다. Azure VM Marketplace에서 VM 이미지를 선택하는 경우, Azure Marketplace를 통해 Red Hat에서 제공하는 다양한 지원 또는 서비스 모델을 알고 있어야 합니다.

### <a name="hosts-dns-updates"></a>호스트: DNS 업데이트
호스트 이름 확인에 대한 적절한 IP 주소를 사용할 수 있도록 가상 호스트 이름을 포함한 모든 호스트 이름의 목록을 만들고 DNS 서버를 업데이트합니다. DNS 서버가 없거나 DNS 항목을 업데이트 및 생성할 수 없으면 이 시나리오에 참여하는 개별 VM의 로컬 호스트 파일을 사용해야 합니다. 호스트 파일 항목을 사용하는 경우에는 해당 항목이 SAP 시스템 환경의 모든 VM에 적용되는지 확인합니다. 그러나 이상적으로는 Azure로 확장되는 DNS를 사용하는 것이 좋습니다.


### <a name="manual-deployment"></a>수동 배포

선택한 OS가 IBM Db2 LUW용 IBM/SAP에서 지원되는지 확인합니다. Azure VM 및 Db2 릴리스에 대해 지원되는 OS 버전 목록은 SAP노트 [1928533]에 제공됩니다. 개별 Db2 릴리스의 OS 릴리스 목록은 SAP 제품 가용성 매트릭스에서 확인할 수 있습니다. Red Hat Enterprise Linux 7.4 for SAP 이상을 적극 권장합니다. 이 이상의 Red Hat Enterprise Linux 버전에서 Azure 관련 성능이 향상되었기 때문입니다.

1. 리소스 그룹을 만들거나 선택합니다.
1. 가상 네트워크 및 서브넷을 만들거나 선택합니다.
1. Azure 가용성 집합을 만들거나 가용성 영역을 배포합니다.
    + 가용성 집합의 경우 최대 업데이트 도메인을 2로 설정합니다.
1. 가상 머신 1을 만듭니다.
    + Azure Marketplace에서 Red Hat Enterprise Linux for SAP 이미지를 사용합니다.
    + 3단계에서 만든 Azure 가용성 집합을 선택하거나 가용성 영역을 선택합니다.
1.  가상 머신 2를 만듭니다.
    + Azure Marketplace에서 Red Hat Enterprise Linux for SAP 이미지를 사용합니다.
    + 3단계에서 만든 Azure 가용성 집합을 선택하거나 가용성 영역(3단계와 다른 영역)을 선택합니다.
1. VM에 데이터 디스크를 추가한 다음, [SAP 워크로드용 IBM DB2 Azure Virtual Machines DBMS 배포][dbms-db2] 문서에서 파일 시스템 설정 권장 사항을 확인합니다.

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>IBM Db2 LUW 및 SAP 환경 설치

IBM Db2 LUW를 기반으로 SAP 환경 설치를 시작하기 전에 다음 문서를 검토하세요.

+ Azure 설명서
+ SAP 설명서
+ IBM 설명서

이 설명서에 대한 링크는 이 문서의 서두에 제공됩니다.

IBM Db2 LUW에 NetWeaver 기반 애플리케이션을 설치하는 방법에 대한 SAP 설치 설명서를 확인하세요.
[SAP Installation Guide Finder][sap-instfind]를 사용하여 SAP 도움말 포털에서 가이드를 찾을 수 있습니다.

다음 필터를 설정하여 포털에 표시되는 가이드 수를 줄일 수 있습니다.
- I want to 필터에서 "Install a new system"을 선택합니다.
- My Database 필터에서 "IBM Db2 for Linux, Unix 및 Windows"를 선택합니다.
- SAP NetWeaver 버전, 스택 구성 또는 운영 체제에 대한 추가 필터를 설정합니다.

#### <a name="red-hat-firewall-rules"></a>Red Hat 방화벽 규칙
Red Hat Enterprise Linux에는 기본적으로 방화벽을 사용하도록 설정되어 있습니다. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>HADR이 포함된 IBM Db2 LUW 설정에 대한 설치 힌트

주 IBM Db2 LUW 데이터베이스 인스턴스를 설정하려면 다음을 수행합니다.

- 고가용성 또는 분산 옵션을 사용합니다.
- SAP ASCS/ERS 및 데이터베이스 인스턴스를 설치합니다.
- 새로 설치된 데이터베이스를 백업합니다.

> [!IMPORTANT] 
> 설치 중에 설정된 "데이터베이스 통신 포트"를 적어둡니다. 두 데이터베이스 인스턴스에 대해 동일한 포트 번호여야 합니다.
>![SAP SWPM 포트 정의](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Azure에 대한 IBM Db2 HADR 설정

   Azure Pacemaker 펜싱 에이전트를 사용하는 경우 다음 매개 변수를 설정합니다.

   - HADR 피어 지속 시간(초)(HADR_PEER_WINDOW) = 240  
   - HADR 제한 시간 값(HADR_TIMEOUT) = 45

초기 장애 조치(failover)/인수 테스트에 따라 앞의 매개 변수를 사용하는 것이 좋습니다. 이러한 매개 변수 설정을 사용하여 장애 조치(failover) 및 인수의 적절한 기능을 테스트해야 합니다. 개별 구성은 다를 수 있으므로 매개 변수를 조정해야 할 수도 있습니다. 

> [!NOTE]
> 정상적인 시작 시 HADR 구성이 포함된 IBM Db2에만 해당: 주 데이터베이스 인스턴스를 시작하려면 먼저 보조 또는 대기 데이터베이스 인스턴스를 시작 및 실행 중이어야 합니다.

   
> [!NOTE]
> Azure 및 Pacemaker와 관련된 설치 및 구성의 경우: SAP Software Provisioning Manager를 통한 설치 과정 중에 IBM Db2 LUW의 고가용성에 대한 명시적인 질문이 있습니다.
>+ **IBM Db2 pureScale** 을 선택하지 마세요.
>+ **Install IBM Tivoli System Automation for Multiplatforms** 를 선택하지 마세요.
>+ **Generate cluster configuration files**(클러스터 구성 파일 생성)를 선택하지 마십시오.
>![SAP SWPM - DB2 HA 옵션](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


SAP 동종 시스템 복사 프로시저를 사용하여 대기 데이터베이스 서버를 설정하려면 다음 단계를 실행합니다.

1. **시스템 복사** 옵션 > **대상 시스템** > **Distributed**(분산형) > **데이터베이스 인스턴스** 를 선택합니다.
1. 복사 방법으로 **Homogeneous System**(동종 시스템)을 선택합니다. 그래야 백업을 사용하여 대기 서버 인스턴스에서 백업을 복원할 수 있습니다.
1. 동종 시스템 복사를 위해 데이터베이스를 복원하는 종료 단계에 도달하면 설치 프로그램을 종료합니다. 주 호스트의 백업에서 데이터베이스를 복원합니다. 주 데이터베이스 서버에서 모든 후속 설치 단계가 이미 실행되었습니다.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR에 대한 Red Hat 방화벽 규칙
HADR이 작동하도록 DB2에 대한 트래픽 및 DB2 간 트래픽을 허용하는 방화벽 규칙을 추가합니다.
+ 데이터베이스 통신 포트. 파티션을 사용하는 경우 해당 포트도 추가합니다.
+ HADR 포트(DB2 매개 변수 HADR_LOCAL_SVC의 값)
+ Azure 프로브 포트
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 검사
이 문서에 설명된 절차 및 데모용 데이터베이스 SID는 **ID2** 입니다.

HADR을 구성하고 주 노드 및 대기 노드의 상태가 PEER 및 CONNECTED이면 다음 검사를 수행합니다.

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N


#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N
</code></pre>

### <a name="configure-azure-load-balancer"></a>Azure Load Balancer 구성

Azure Load Balancer를 구성하려면 [Azure 표준 Load Balancer SKU](../../../load-balancer/load-balancer-overview.md)를 사용한 후 다음을 수행하는 것이 좋습니다.

> [!NOTE]
> 표준 Load Balancer SKU는 Load Balancer 아래의 노드에서 공용 IP 주소에 액세스하는 데 제한이 있습니다. [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md) 문서에는 해당 노드가 공용 IP 주소에 액세스할 수 있도록 설정하는 방법이 설명되어 있습니다.

> [!IMPORTANT]
> 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.  

1. 프런트 엔드 IP 풀 만들기:

   a. Azure Portal에서 Azure Load Balancer를 열고 **프런트 엔드 IP 풀** 을 선택한 다음, **추가** 를 선택합니다.

   b. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **Db2-connection**).

   c. **할당** 을 **정적** 으로 설정하고, 시작 부분에 정의된 IP 주소 **Virtual-IP** 를 입력합니다.

   d. **확인** 을 선택합니다.

   e. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

1. 백 엔드 풀 만들기:

   a. Azure Portal에서 Azure Load Balancer를 열고 **백 엔드 풀** 을 선택한 다음, **추가** 를 선택합니다.

   b. 새 백 엔드 풀의 이름 입력합니다(예: **Db2-backend**).

   다. **가상 머신 추가** 를 선택합니다.

   d. 이전 단계에서 만든 IBM Db2 데이터베이스를 호스트하는 가상 머신 또는 가용성 집합을 선택합니다.

   e. IBM Db2 클러스터의 가상 머신을 선택합니다.

   f. **확인** 을 선택합니다.

1. 상태 프로브 만들기:

   a. Azure Portal에서 Azure Load Balancer를 열고 **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.

   b. 새 상태 프로브의 이름을 입력합니다(예: **Db2-hp**).

   c. 프로토콜로 **TCP** 를 선택하고 포트 **62500** 을 선택합니다. **5** 로 설정된 **간격** 값 및 **2** 로 설정된 **비정상 임계값** 의 값을 유지합니다.

   d. **확인** 을 선택합니다.

1. 부하 분산 규칙 만들기:

   a. Azure Portal에서 Azure Load Balancer를 열고 **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.

   b. 새 Load Balancer 규칙의 이름을 입력합니다(예: **Db2-SID**).

   다. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **Db2-frontend**).

   d. **프로토콜** 을 **TCP** 로 설정된 상태로 유지하고 포트 *데이터베이스 통신 포트* 를 입력합니다.

   e. **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.

   f. **부동 IP를 사용하도록 설정** 했는지 확인합니다.

   g. **확인** 을 선택합니다.

**[A]** 프로브 포트에 대한 방화벽 규칙 추가:

<pre><code>sudo firewall-cmd --add-port=<b>&lt;probe-port&gt;</b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

## <a name="create-the-pacemaker-cluster"></a>Pacemaker 클러스터 만들기
    
이 IBM Db2 서버에 대한 기본 Pacemaker 클러스터를 만들려면 [Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정][rhel-pcs-azr]을 참조하세요. 

## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 구성

노드 오류가 발생할 경우 자동 장애 조치(failover)에 Pacemaker를 사용하는 경우 Db2 인스턴스와 Pacemaker를 적절히 구성해야 합니다. 이 섹션에서는 이러한 유형의 구성에 대해 설명합니다.

항목에는 다음 중 한 가지 접두사가 추가됩니다.

- **[A]** : 모든 노드에 적용 가능
- **[1]** : 노드 1에만 적용 가능 
- **[2]** : 노드 2에만 적용 가능

**[A]** Pacemaker 구성의 선행 조건:
1. 사용자 db2\<sid>로 db2stop을 사용하여 두 데이터베이스 서버를 종료합니다.
1. db2\<sid> 사용자에 대한 셸 환경을 */bin/ksh* 로 변경합니다.
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>  

### <a name="pacemaker-configuration"></a>Pacemaker 구성

**[1]** IBM Db2 HADR 관련 Pacemaker 구성:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM Db2 리소스 만들기:

**RHEL 7.x** 에 클러스터를 빌드하는 경우 다음 명령을 사용합니다.

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**RHEL 8.x** 에 클러스터를 빌드하는 경우 다음 명령을 사용합니다.

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' promotable meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-clone meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-clone

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-clone then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** IBM Db2 리소스 시작:
* Pacemaker를 유지 관리 모드에서 해제합니다.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** 클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 어떤 노드에서 실행되는지는 중요하지 않습니다.
<pre><code>sudo pcs status
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

> [!IMPORTANT]
> Pacemaker 클러스터형 Db2 인스턴스는 Pacemaker 도구를 사용하여 관리해야 합니다. db2stop과 같은 db2 명령을 사용하면 Pacemaker는 해당 동작을 리소스 실패로 감지합니다. 유지 관리를 수행하는 경우 노드 또는 리소스를 유지 관리 모드로 전환할 수 있습니다. Pacemaker는 모니터링 리소스를 일시 중단하고 일반 db2 관리 명령을 사용할 수 있습니다.

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>연결에 가상 IP를 사용하도록 SAP 프로필 변경
HADR 구성의 주 인스턴스에 연결하려면 Azure Load Balancer에 대해 정의하고 구성한 가상 IP 주소가 SAP 애플리케이션 계층에 사용되어야 합니다. 다음과 같은 변경이 필요합니다.

/sapmnt/\<SID>/profile/DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>

## <a name="install-primary-and-dialog-application-servers"></a>주 애플리케이션 서버 및 대화 애플리케이션 서버 설치

Db2 HADR 구성에 대해 주 애플리케이션 서버 및 대화 애플리케이션 서버를 설치할 때는 구성에 대해 선택한 가상 호스트 이름을 사용합니다.

Db2 HADR 구성을 만들기 전에 설치를 수행한 경우 이전 섹션에서 설명한 대로 변경하고 SAP Java 스택의 경우 다음과 같이 변경합니다.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java 또는 Java 스택 시스템 JDBC URL 확인

J2EE Config 도구를 사용하여 JDBC URL을 확인하거나 업데이트합니다. J2EE Config 도구는 그래픽 도구이므로 X 서버가 설치되어 있어야 합니다.
 
1. J2EE 인스턴스의 주 애플리케이션 서버에 로그인하고 다음을 실행합니다.
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. 왼쪽 프레임에서 **보안 저장소** 를 선택합니다.
1. 오른쪽 프레임에서 키 `jdbc/pool/\<SAPSID>/url`을 선택합니다.
1. JDBC URL의 호스트 이름을 가상 호스트 이름으로 변경합니다.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. **추가** 를 선택합니다.
1. 변경 내용을 저장하려면 왼쪽 위에서 디스크 아이콘을 선택합니다.
1. 구성 도구를 닫습니다.
1. Java 인스턴스를 다시 시작합니다.

## <a name="configure-log-archiving-for-hadr-setup"></a>HADR 설정에 대한 로그 보관 구성

HADR 설정에 대한 Db2 로그 보관을 구성하려면 주 데이터베이스와 대기 데이터베이스가 모든 로그 보관 위치에서 자동 로그 검색 기능을 갖추도록 구성하는 것이 좋습니다. 주 데이터베이스와 대기 데이터베이스는 각각의 데이터베이스 인스턴스가 로그 파일을 보관할 수 있는 모든 로그 보관 위치에서 로그 보관 파일을 검색할 수 있어야 합니다. 

로그 보관은 주 데이터베이스에서만 수행됩니다. 데이터베이스 서버의 HADR 역할을 변경하거나 오류가 발생하면 새로운 주 데이터베이스가 로그 보관을 담당합니다. 여러 로그 보관 위치를 설정하면 로그가 두 번 보관될 수 있습니다. 로컬 또는 원격 보완의 경우 보관된 로그를 이전 주 서버에서 새로운 주 서버의 활성 로그 위치로 수동으로 복사해야 할 수도 있습니다.

두 노드에서 로그가 기록되는 공통 NFS 공유 또는 GlusterFS를 구성하는 것이 좋습니다. NFS 공유 또는 GlusterFS는 고가용성이어야 합니다. 

기존의 고가용성 NFS 공유 또는 GlusterFS를 전송 또는 프로필 디렉토리에 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [SAP NetWeaver에 대한 Red Hat Enterprise Linux에 있는 Azure VM의 GlusterFS][glusterfs] 
- [SAP 애플리케이션용 Azure NetApp Files를 사용하여 Red Hat Enterprise Linux에서 Azure VM의 SAP NetWeaver 고가용성 실현][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)(NFS 공유 생성)

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 Db2 HADR 설정을 테스트하는 방법을 설명합니다. 모든 테스트는 *az-idb01* 가상 머신에서 IBM Db2 주 데이터베이스가 실행되고 있다고 가정합니다. 루트(권장하지 않음) 또는 sudo 권한이 있는 사용자를 사용해야 합니다.

모든 테스트 사례의 초기 상태는 (crm_mon -r  또는 pcs status)에 설명되어 있습니다.

- **pcs status** 는 실행 시 Pacemaker 상태의 스냅샷입니다. 
- **crm_mon -r** 은 Pacemaker 상태의 지속적인 출력입니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

SAP 시스템의 원래 상태는 트랜잭션 DBACOCKPIT> Configuration(구성) > Overview(개요)에 다음 이미지와 같이 문서화되어 있습니다.

![DBACockpit - 마이그레이션 전](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)

### <a name="test-takeover-of-ibm-db2"></a>IBM Db2의 인수 테스트


> [!IMPORTANT] 
> 테스트를 시작하기 전에 다음을 확인해야 합니다.
> * Pacemaker에는 실패한 작업(pcs status)이 없습니다.
> * 위치 제약 조건이 없습니다(마이그레이션 테스트의 나머지).
> * IBM Db2 HADR 동기화가 작동합니다. 사용자 db2\<sid>로 확인할 수 있습니다. <pre><code>db2pd -hadr -db \<DBSID></code></pre>

다음 명령을 실행하여 기본 Db2 데이터베이스를 실행하는 노드를 마이그레이션합니다.
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
</code></pre>

마이그레이션이 완료되면 crm 상태 출력은 다음과 같습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

SAP 시스템의 원래 상태는 트랜잭션 DBACOCKPIT> Configuration(구성) > Overview(개요)에 다음 이미지와 같이 문서화되어 있습니다.

![DBACockpit - 마이그레이션 후](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

"pcs resource move"를 사용한 리소스 마이그레이션은 위치 제약 조건을 만듭니다. 이 경우 위치 제약 조건으로 인해 az-idb01에서 IBM Db2 인스턴스를 실행하지 못합니다. 위치 제약 조건이 삭제되지 않으면 리소스를 장애 복구(failback)할 수 없습니다.

위치 제약 조건을 제거하면 az-idb01에서 대기 노드가 시작됩니다.
<pre><code># On RHEL 7.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

그리고 클러스터 상태는 다음과 같이 변경됩니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - 위치 제약 조건 제거](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)

리소스를 *az-idb01* 로 다시 마이그레이션하고 위치 제약 조건을 지웁니다.
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

- **On RHEL 7.x - pcs resource move \<res_name\> \<host\>:** 위치 제약 조건을 생성하고 인수 문제를 일으킬 수 있음
- **On RHEL 8.x - pcs resource move \<res_name\> --master:** 위치 제약 조건을 생성하고 인수 문제를 일으킬 수 있음
- **pcs resource clear \<res_name\>** : 위치 제약 조건 지우기
- **pcs resource cleanup \<res_name\>** : 리소스의 모든 오류 지우기

### <a name="test-a-manual-takeover"></a>수동 인수 테스트

*az-idb01* 노드에서 Pacemaker 서비스를 중지하여 수동 인수를 테스트할 수 있습니다.
<pre><code>systemctl stop pacemaker</code></pre>

*az-ibdb02* 의 상태
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

장애 조치(failover) 후 *az-idb01* 에서 서비스를 다시 시작할 수 있습니다.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>HADR 주 데이터베이스를 실행하는 노드에서 Db2 프로세스 종료

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 인스턴스가 실패하고 Pacemaker가 마스터 노드를 이동하고 다음 상태를 보고합니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker는 동일한 노드에서 Db2 주 데이터베이스 인스턴스를 다시 시작하거나 보조 데이터베이스 인스턴스를 실행하는 노드로 장애 조치(failover)되고 오류가 보고됩니다.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>보조 데이터베이스 인스턴스를 실행하는 노드에서 Db2 프로세스 종료

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

노드가 실패한 상태가 되고 오류가 보고됩니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2 인스턴스는 이전에 할당한 보조 역할에서 다시 시작됩니다.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>HADR 주 데이터베이스 인스턴스를 실행하는 노드에서 db2stop force를 통해 DB 중지

사용자 db2\<sid>로 db2stop force 명령을 실행합니다.
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

검색된 오류:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR 보조 데이터베이스 인스턴스가 주 역할로 승격되었습니다.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>"halt"(중지)로 인해 HADR 주 데이터베이스 인스턴스를 실행하는 VM 작동 중단

<pre><code>#Linux kernel panic.
sudo echo b > /proc/sysrq-trigger</code></pre>

이 경우 Pacemaker는 주 데이터베이스 인스턴스를 실행하는 노드가 응답하지 않는다는 것을 감지합니다.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

다음 단계는 스플릿 브레인(*Split brain*) 상황을 확인하는 것입니다. 주 데이터베이스 인스턴스를 마지막으로 실행한 노드가 중단된 것이 생존 노드에서 확인되면, 리소스에 대한 장애 조치(failover)가 실행됩니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


커널 패닉이 발생하면 실패한 노드는 펜싱 에이전트에 의해 다시 시작됩니다. 실패한 노드가 다시 온라인 상태가 되면 다음을 실행하여 Pacemaker 클러스터를 시작해야 합니다.
<pre><code>sudo pcs cluster start</code></pre> Db2 인스턴스를 보조 역할로 시작합니다.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>다음 단계
- [SAP NetWeaver에 대한 고가용성 아키텍처 및 시나리오](./sap-high-availability-architecture-scenarios.md)
- [Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:dbms_guide_ibm.md

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:high-availability-guide-rhel.md
[glusterfs]:high-availability-guide-rhel-glusterfs.md
[rhel-pcs-azr]:high-availability-guide-rhel-pacemaker.md
[anf-rhel]:high-availability-guide-rhel-netapp-files.md

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:sap-deployment-checklist.md
