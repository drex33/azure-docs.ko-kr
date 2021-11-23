---
title: RHEL에서 HSR 및 Pacemaker를 사용하여 SAP HANA 스케일 아웃 | Microsoft Docs
description: SAP HANA RHEL(Red Hat Enterprise Linux)에서 HSR(HANA 시스템 복제) 및 Pacemaker를 통해 스케일 아웃
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/12/2021
ms.author: radeltch
ms.openlocfilehash: b83c1e91794261516ab60701f9ccd0dc4cdc1b97
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941323"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에 배포된 SAP HANA 스케일 아웃 시스템의 고가용성 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:../../../azure-netapp-files/index.yml
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 스케일 아웃 구성에서 고가용성 SAP HANA 시스템을 배포하는 방법을 설명합니다. 특히 구성은 Azure Red Hat Enterprise Linux VM(가상 머신)에서 HSR(HANA 시스템 복제) 및 Pacemaker를 사용합니다. [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 제공된 아키텍처에서 공유 파일 시스템을 제공하며 이러한 파일 시스템은 NFS(네트워크 파일 시스템)를 통해 탑재됩니다.  

예제 구성 및 설치 명령에서 HANA 인스턴스는 `03` 이고 HANA 시스템 ID는 `HN1` 입니다. 예제는 SAP 7.6용 HANA 2.0 SP4 및 RHEL(Red Hat Enterprise Linux)을 기반으로 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

일부 독자는 이 문서의 항목을 계속 진행하기 전에 다양한 SAP 참고 사항 및 리소스를 참조하는 것이 도움이 됩니다.

* SAP 참고 [1928533] 다음이 포함됩니다.  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록입니다.
  * Azure VM 크기에 대한 중요한 용량 정보.
  * 지원되는 SAP 소프트웨어, 운영 체제 및 데이터베이스 조합.
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전.
* SAP note [2015553:]Azure에서 SAP 지원 SAP 소프트웨어 배포를 위한 필수 구성요약을 나열합니다.
* SAP Note [2002167]: RHEL에 권장되는 운영 체제 설정이 있습니다.
* SAP note [2009879:]RHEL에 대한 SAP HANA 지침이 있습니다.
* SAP note [2178632:]Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함합니다.
* SAP note [2191498:]Azure에서 Linux에 필요한 SAP 호스트 에이전트 버전을 포함합니다.
* SAP note [2243692:]Azure의 Linux에서 SAP 라이선스에 대한 정보를 포함합니다.
* SAP note [1999351:]SAP용 Azure 고급 모니터링 확장에 대한 추가 문제 해결 정보를 포함합니다.
* SAP note [1900823:]SAP HANA 스토리지 요구 사항에 대한 정보를 포함합니다.
* [SAP 커뮤니티 wiki:](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)Linux에 필요한 모든 SAP 메모를 포함합니다.
* [Linux에서 SAP에 대한 Azure Virtual Machines 계획 및 구현.][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포.][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포.][dbms-guide]
* [SAP HANA 네트워크 요구 사항입니다.](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* 일반 RHEL 설명서:
  * [고가용성 추가 기능 개요.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [고가용성 추가 기능 관리.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [고가용성 추가 기능 참조 입니다.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux 네트워킹 가이드.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [어떻게 할까요? NFS 공유의 HANA 파일 시스템을 통해 Pacemaker 클러스터에서 SAP HANA 스케일 아웃 시스템 복제를 구성합니다.](https://access.redhat.com/solutions/5423971)
  * [활성/활성(읽기 사용): SAP HANA 스케일 아웃 및 시스템 복제를 위한 RHEL HA 솔루션입니다.](https://access.redhat.com/sites/default/files/attachments/v8_ha_solution_for_sap_hana_scale_out_system_replication_1.pdf)
* Azure 특정 RHEL 설명서:
  * [Microsoft Azure 사용할 Red Hat Enterprise Linux에 SAP HANA 설치합니다.](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Red Hat은 SAP HANA 스케일 아웃 및 시스템 복제를 위한 Linux 솔루션을 Enterprise.](https://access.redhat.com/solutions/4386601)
* [Azure NetApp Files 사용하여 Microsoft Azure NetApp SAP 애플리케이션][anf-sap-applications-azure]
* [Azure NetApp Files 설명서.][anf-azure-doc] 
* [SAP HANA Azure NetApp Files NFS v4.1 볼륨](./hana-vm-operations-netapp.md)

## <a name="overview"></a>개요

HANA 스케일 아웃 설치에 대한 HANA 고가용성을 달성하려면 HANA 시스템 복제를 구성하고 자동 장애 조치(failover)를 허용하도록 Pacemaker 클러스터를 사용하여 솔루션을 보호할 수 있습니다. 활성 노드에 오류가 발생하면 클러스터는 HANA 리소스를 다른 사이트로 장애 조치(failover)합니다.  

다음 다이어그램에는 각 사이트에 세 개의 HANA 노드와 "스플릿 브레인" 시나리오를 방지하기 위한 주 작성자 노드가 있습니다. HANA DB 노드로 더 많은 VM을 포함하도록 지침을 조정할 수 있습니다.  

[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) HANA 공유 파일 시스템 를 `/hana/shared` 제공합니다. 동일한 HANA 시스템 복제 사이트의 각 HANA 노드에서 NFS v4.1을 통해 탑재됩니다. 파일 시스템 `/hana/data` 및 는 로컬 파일 `/hana/log` 시스템이며 HANA DB 노드 간에 공유되지 않습니다. SAP HANA는 비공유 모드로 설치됩니다. 

> [!TIP]
> 권장 SAP HANA 스토리지 구성은 [SAP HANA Azure VM 스토리지 구성](./hana-vm-operations-storage.md)을 참조하세요.   

[![HSR 및 Pacemaker 클러스터를 SAP HANA 스케일 아웃 다이어그램](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

앞의 다이어그램은 SAP HANA 네트워크 권장 사항에 따라 하나의 Azure 가상 네트워크 내에 표시되는 세 개의 서브넷을 보여 드립니다. 

* 클라이언트 통신의 경우: `client` 10.23.0.0/24  
* 내부 HANA 노드 간 통신의 경우: `inter` 10.23.1.128/26  
* HANA 시스템 복제의 경우: `hsr` 10.23.1.192/26  

`/hana/data`및 `/hana/log` 는 로컬 디스크에 배포되므로 스토리지에 통신하기 위해 별도의 서브넷과 별도의 가상 네트워크 카드를 배포할 필요가 없습니다.  

Azure NetApp 볼륨은 별도의 서브넷, 즉 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md): `anf` 10.23.1.0/26으로 위임된 서브넷에 배포됩니다.   

## <a name="set-up-the-infrastructure"></a>인프라 설정

다음 지침에서는 리소스 그룹, 즉 3개의 Azure 네트워크 서브넷 `client`, `inter` 및 `hsr`가 있는 Azure 가상 네트워크를 만들었다고 가정합니다.

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure Portal을 통한 Linux 가상 머신 배포

1. Azure VM을 배포합니다. 이 구성의 경우 7개의 가상 머신을 배포합니다. 
   
   - HANA 복제 사이트 1에 대한 HANA DB 노드로 사용할 3개의 가상 머신: **hana-s1-db1,** **hana-s1-db2** 및 **hana-s1-db3.**  
   - HANA 복제 사이트 2에 대한 HANA DB 노드로 사용할 3개의 가상 머신: **hana-s2-db1,** **hana-s2-db2** 및 **hana-s2-db3.**  
   - 주 제조업체로 사용될 작은 가상 머신: **hana-s-mm**.

   SAP DB HANA 노드로 배포된 VM은 [SAP HANA 하드웨어 디렉터리에](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)게시된 HANA용 SAP에서 인증해야 합니다. HANA DB 노드를 배포하는 경우 [가속 네트워크](../../../virtual-network/create-vm-accelerated-networking-cli.md)를 선택해야 합니다.  
  
   주 작성자 노드의 경우 이 VM이 SAP HANA 리소스를 실행하지 않으므로 작은 VM을 배포할 수 있습니다. 주 작성자 VM은 클러스터 구성에서 스플릿 브레인 시나리오에서 클러스터 노드 수와 홀수를 달성하는 데 사용됩니다. 이 예제에서는 주 결정자 VM이 `client` 서브넷에서 하나의 가상 네트워크 인터페이스만 필요로 합니다.        

   `/hana/data` 및 `/hana/log`용 로컬 관리 디스크를 배포합니다. `/hana/data` 및 `/hana/log`의 최소 권장 스토리지 구성에 관한 설명이 [SAP HANA Azure VM 스토리지 구성](./hana-vm-operations-storage.md)에 나옵니다.

   `client` 가상 네트워크 서브넷의 각 VM별 기본 네트워크 인터페이스를 배포합니다. Azure Portal을 통해 VM을 배포하는 경우 네트워크 인터페이스 이름이 자동으로 생성됩니다. 이 문서에서는 자동으로 생성된 기본 네트워크 인터페이스를 **hana-s1-db1-client, hana-s1-db2-client,** **hana-s1-db3-client** 등으로 참조합니다.  이러한 네트워크 인터페이스는 `client` Azure 가상 네트워크 서브넷에 연결됩니다.  

   > [!IMPORTANT]
   > 선택한 운영 체제가 사용 중인 특정 VM 유형에서 SAP HANA SAP 인증되었는지 확인합니다. 이러한 유형의 SAP HANA 인증된 VM 유형 및 운영 체제 릴리스 목록은 [인증된 IaaS 플랫폼 SAP HANA 참조하세요.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 나열된 VM 유형의 세부 정보를 자세히 조사하여 해당 형식에 대해 지원되는 SAP HANA 운영 체제 릴리스의 전체 목록을 확인합니다.  
  
1. `inter` 가상 네트워크 서브넷에 HANA DB 가상 머신마다 하나씩 총 6개의 네트워크 인터페이스를 만듭니다(이 예에서는 **hana-s1-db1-inter**, **hana-s1-db2-inter**, **hana-s1-db3-inter**, **hana-s2-db1-inter**, **hana-s2-db2-inter**, **hana-s2-db3-inter**).  

1. `hsr` 가상 네트워크 서브넷에 HANA DB 가상 머신마다 하나씩 총 6개의 네트워크 인터페이스를 만듭니다(이 예에서는 **hana-s1-db1-hsr**, **hana-s1-db2-hsr**, **hana-s1-db3-hsr**, **hana-s2-db1-hsr**, **hana-s2-db2-hsr**, **hana-s2-db3-hsr**).  

1. 새로 만든 가상 네트워크 인터페이스를 해당 가상 머신에 연결합니다.  

    1. [Azure Portal](https://portal.azure.com/#home)에서 가상 머신으로 이동합니다.  

    1. 왼쪽 창에서 **Virtual Machines** 선택합니다. 가상 머신 이름(예: **hana-s1-db1**)을 필터링한 다음, 가상 머신을 선택합니다.  

    1. **개요** 창에서 **중지를** 선택하여 가상 머신의 할당을 중단합니다.  

    1. **네트워킹** 을 선택하고 네트워크 인터페이스를 연결합니다. 네트워크 **인터페이스 연결** 드롭다운 목록에서 및 서브넷에 대해 이미 만든 네트워크 `inter` `hsr` 인터페이스를 선택합니다.  
    
    1. **저장** 을 선택합니다. 
 
    1. 나머지 가상 머신도 b~e단계를 반복합니다(이 예에서는 **hana-s1-db2**, **hana-s1-db3**, **hana-s2-db1**, **hana-s2-db2**, **hana-s2-db3**).
 
    1. 지금은 가상 머신을 중지된 상태로 둡니다.   

1. [](../../../virtual-network/create-vm-accelerated-networking-cli.md) `inter` 다음을 수행하여 및 서브넷에 대한 추가 네트워크 인터페이스에 가속 네트워킹을 사용하도록 설정합니다. `hsr`  

    1. [Azure Portal](https://portal.azure.com/#home)에서 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)을 엽니다.  

    1. 다음 명령을 실행하여 및 서브넷에 연결된 추가 네트워크 인터페이스에 가속 네트워킹을 사용하도록 `inter` `hsr` 설정합니다.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

1. HANA DB 가상 머신을 시작합니다.

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer 배포

표준 부하 분산기를 사용하는 것이 가장 좋습니다. 방법은 다음과 같습니다.

1. 프런트 엔드 IP 풀 만들기:

    1. 부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
    1. 새 프런트 엔드 IP 풀의 이름을 입력합니다(예: *hana-frontend*).
    1. **할당을** **정적** 로 설정하고 IP 주소(예: *10.23.0.18)를* 입력합니다.
    1. **확인** 을 선택합니다.
    1. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

1. 백 엔드 풀을 만들고 모든 클러스터 VM을 백 엔드 풀에 추가합니다.

    1. 부하 분산 장치를 열고, **백 엔드 풀** 을 선택한 다음, **추가** 를 클릭합니다.
    1. 새 백 엔드 풀의 이름 입력합니다(예: *hana-backend*).
    1. **가상 머신 가상 머신 추가를**  >  선택합니다.
    1. SAP HANA 클러스터의 가상 머신과 `client` 서브넷의 해당 IP 주소를 선택합니다.
    1. **추가** 를 선택합니다.

1. 상태 프로브 만들기:

    1. 부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
    1. 새 상태 프로브의 이름을 입력합니다(예: *hana-hp*).
    1. 프로토콜 및 포트 625 **03** 으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
    1. **확인** 을 선택합니다.

1. 부하 분산 규칙 만들기:
   
    1. 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
    1. 새 부하 분산 장치 규칙의 이름을 입력합니다(예: *hana-lb*).
    1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**, **hana-backend** 및 **hana-hp**).
    1. **HA 포트** 를 선택합니다.
    1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
    1. **확인** 을 선택합니다.

   > [!IMPORTANT]
   > 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항을 참조하세요.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.    
   
표준 부하 분산기를 사용하는 경우 다음과 같은 제한 사항을 알고 있어야 합니다. 공용 IP 주소가 없는 VM을 내부 부하 분산기의 백 엔드 풀에 배치하면 아웃바운드 인터넷 연결이 없습니다. 퍼블릭 엔드포인트로 라우팅할 수 있도록 하려면 추가 구성을 수행해야 합니다. 자세한 내용은 [SAP 고가용성 시나리오에서 Azure Standard Load Balancer를 사용하는 가상 머신에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.  

   > [!IMPORTANT]
   > Azure Load Balancer 뒤에 배치된 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하지 마세요. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브가 실패합니다. 매개 변수를 `net.ipv4.tcp_timestamps` `0` 로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브 및](../../../load-balancer/load-balancer-custom-probe-overview.md) SAP note [2382421](https://launchpad.support.sap.com/#/notes/2382421)참조하세요.  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files 인프라 배포 

파일 시스템에 대한 Azure NetApp Files 볼륨을 `/hana/shared` 배포합니다. 각 HANA 시스템 복제 사이트에 대해 별도의 `/hana/shared` 볼륨이 필요합니다. 자세한 내용은 [Azure NetApp Files 인프라 설정](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure)을 참조하세요.

이 예제에서는 다음 Azure NetApp Files 볼륨을 사용합니다. 

* 볼륨 **HN1**-shared-s1(nfs://10.23.1.7/**HN1**-shared-s1)
* 볼륨 **HN1**-shared-s2(nfs://10.23.1.7/**HN1**-shared-s2)

## <a name="operating-system-configuration-and-preparation"></a>운영 체제 구성 및 준비

다음 섹션의 지침에는 다음 약어 중 하나가 접두사로 추가됩니다.

* **[A]** :      모든 노드에 적용 가능
* **[AH]** :      모든 HANA DB 노드에 적용 가능
* **[M]** :      주 결정자 노드에 적용 가능
* **[AH1]** :      SITE 1의 모든 HANA DB 노드에 적용 가능
* **[AH2]** :      SITE 2의 모든 HANA DB 노드에 적용 가능
* **[1]** :      SITE 1의 HANA DB 노드 1에만 적용 가능
* **[2]** :      SITE 2의 HANA DB 노드 1에만 적용 가능

다음을 수행하여 운영 체제를 구성하고 준비합니다.

1. **[A]** 가상 머신에서 호스트 파일을 유지 관리합니다. 모든 서브넷에 대한 항목을 포함합니다. 이 예제에서는 다음 항목이 `/etc/hosts` 에 추가됩니다.  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```

1. **[A]** SAP HANA 실행할 운영 체제를 준비합니다. 자세한 내용은 [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]을 참조하세요. Azure NetApp Files 구성 설정에 대한 구성 파일 */etc/sysctl.d/netapp-hana.conf를* 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1    
    net.ipv4.tcp_sack = 1
    </code></pre>

1. **[A]** 추가 최적화 설정을 사용하여 */etc/sysctl.d/ms-az.conf* 구성 파일을 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

    > [!TIP]
    > SAP `net.ipv4.ip_local_port_range` `net.ipv4.ip_local_reserved_ports` 호스트 에이전트가 포트 범위를 관리할 수 있도록 구성 파일에서 및 를 명시적으로 설정하지 `sysctl` 마십시오. 자세한 내용은 SAP note [2382421](https://launchpad.support.sap.com/#/notes/2382421)참조하세요.  

1. **[A]** Azure NetApp Files `sunrpc` 사용하여 Microsoft Azure [NetApp SAP 애플리케이션에서][anf-sap-applications-azure]권장하는 대로 설정을 조정합니다.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

1. **[A]** NFS 클라이언트 패키지를 설치합니다.  

   `yum install nfs-utils`


1. **[AH]** HANA 구성을 위한 Red Hat입니다.  

   [Red Hat 고객 포털](https://access.redhat.com/solutions/2447641) 및 다음 SAP 참고 사항에서 설명한 대로 RHEL을 구성합니다.

   - [2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: RHEL 8에 권장되는 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: GCC 6.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: GCC 7.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: GCC 9.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>파일 시스템 준비

다음 섹션에서는 파일 시스템을 준비하는 단계를 제공합니다.

### <a name="mount-the-shared-file-systems"></a>공유 파일 시스템 탑재

이 예제에서 공유 HANA 파일 시스템은 Azure NetApp Files 배포되고 NFS v4를 통해 탑재됩니다.  

1. **[AH]** HANA 데이터베이스 볼륨의 탑재 지점을 만듭니다.

    ```bash
    mkdir -p /hana/shared
    ```

1. **[AH]** NFS 도메인 설정을 확인합니다. 도메인이 기본 Azure NetApp Files 도메인()으로 구성되어 있는지 `defaultv4iddomain.com` 확인합니다. 매핑이 로 설정되어 있는지 `nobody` 확인합니다.  
   (이 단계는 Azure NetAppFiles NFS v4.1을 사용하는 경우에만 필요합니다.)  

    > [!IMPORTANT]
    > VM의 `/etc/idmapd.conf`에서 NFS 도메인을 Azure NetApp Files의 기본 도메인 구성( `defaultv4iddomain.com` )과 일치하도록 설정해야 합니다. NFS 클라이언트의 도메인 구성과 NFS 서버 간에 불일치가 있는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 권한은 로 `nobody` 표시됩니다.  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

1. **[AH]** `nfs4_disable_idmapping`을 확인합니다. `Y`로 설정되어야 합니다. `nfs4_disable_idmapping`이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 액세스는 커널 또는 드라이버용으로 예약되어 있으므로 */sys/modules* 아래에 디렉터리를 수동으로 만들 수 없습니다.  
   이 단계는 Azure NetAppFiles NFSv 4.1을 사용하는 경우에만 필요합니다.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   매개 변수를 변경하는 방법에 대한 자세한 내용은 `nfs4_disable_idmapping` [Red Hat 고객 포털을 참조하세요.](https://access.redhat.com/solutions/1749883)

1. **[AH1]** SITE1 HANA DB VM에 공유 Azure NetApp Files 볼륨을 탑재합니다.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

1. **[AH2]** SITE2 HANA DB VM에 공유 Azure NetApp Files 볼륨을 탑재합니다.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


1. **[AH]** `/hana/shared/` NFS 프로토콜 버전 **NFSv4를** 사용하여 해당 파일 시스템이 모든 HANA DB VM에 탑재되었는지 확인합니다.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>데이터 및 로그 로컬 파일 시스템 준비

제공된 구성에서는 파일 시스템 `/hana/data` 및 `/hana/log` 관리 디스크를 배포하고 이러한 파일 시스템을 각 HANA DB VM에 로컬로 연결합니다. 각 HANA DB 가상 컴퓨터에서 로컬 데이터 및 로그 볼륨을 만들려면 다음 단계를 실행 합니다. 

**LVM (논리 볼륨 관리자)** 을 사용 하 여 디스크 레이아웃을 설정 합니다. 다음 예에서는 각 HANA 가상 컴퓨터에 세 개의 데이터 디스크가 연결 되어 있고 이러한 디스크를 사용 하 여 두 개의 볼륨을 만드는 것으로 가정 합니다.

1. **[AH]** 사용 가능한 모든 디스크를 나열합니다.
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   예제 출력:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

1. **[AH]** 사용하려는 모든 디스크의 물리적 볼륨을 만듭니다.
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

1. **[AH]** 데이터 파일에 대한 볼륨 그룹을 만듭니다. 로그 파일에 대해 한 볼륨 그룹, SAP HANA의 공유 디렉터리에 대해 한 볼륨을 사용합니다.
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

1. **[AH]** 논리 볼륨을 만듭니다. *선형* 볼륨은 스위치 없이를 사용할 때 생성 됩니다 `lvcreate` `-i` . 더 나은 i/o 성능을 위해 *스트라이프* 볼륨을 만드는 것이 좋습니다. 스트라이프 크기를 [SAP HANA VM 저장소 구성](./hana-vm-operations-storage.md)에 설명 된 값에 맞춥니다. `-i` 인수는 기본 실제 볼륨의 수여야 하며 `-I` 인수는 스트라이프 크기입니다. 이 문서에서는 데이터 볼륨에 두 개의 실제 볼륨이 사용 되므로 `-i` 스위치 인수가로 설정 됩니다 `2` . 데이터 볼륨의 스트라이프 크기는 `256 KiB` 입니다. 로그 볼륨에는 하나의 실제 볼륨이 사용 되므로 `-i` `-I` 로그 볼륨 명령에 대해 explicit 또는 스위치를 사용할 필요가 없습니다.  

   > [!IMPORTANT]
   > `-i`각 데이터 또는 로그 볼륨에 둘 이상의 실제 볼륨을 사용 하는 경우 스위치를 사용 하 여 기본 실제 볼륨의 수로 설정 합니다. `-I`스트라이프 볼륨을 만들 때 스위치를 사용 하 여 스트라이프 크기를 지정 합니다. 스트라이프 크기 및 디스크 수를 비롯한 권장되는 스토리지 구성은 [SAP HANA VM 스토리지 구성](./hana-vm-operations-storage.md)을 참조하세요.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

1. **[AH]** 탑재 디렉터리를 만들고 모든 논리 볼륨의 UUID를 복사합니다.
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

1. **[AH]** 논리 볼륨에 대한 `fstab` 항목을 만들고 탑재합니다.
    ```bash
    sudo vi /etc/fstab
    ```

   `/etc/fstab` 파일에서 다음 줄을 삽입합니다.

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   새 볼륨을 탑재합니다.

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>설치  

이 예제에서는 Azure Vm에서 HSR를 사용 하 여 스케일 아웃 구성에 SAP HANA을 배포 하는 경우 HANA 2.0 SP4를 사용 하 고 있습니다.  

### <a name="prepare-for-hana-installation"></a>HANA 설치 준비

1. **[AH]** HANA 설치 전에 루트 암호를 설정합니다. 설치가 완료된 후 루트 암호를 사용하지 않도록 설정할 수 있습니다. 명령을 실행 `root` `passwd` 하 여 암호를 설정 합니다.  

1. **[1, 2]** 에 대 한 사용 권한을 변경 `/hana/shared` 합니다. 
    ```bash
    chmod 775 /hana/shared
    ```

1. **[1]** 암호를 입력 하 라는 메시지가 표시 되지 않고 **SSH (secure** shell)를 통해 **db3** 에서 로그인 할 수 있는지 확인 합니다. 이 경우 `ssh` [키 기반 인증 사용](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)에 설명 된 대로 교환 키가 아닌 것입니다.  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

1. **[2]** 암호를 입력 하 라는 메시지가 표시 되지 않고 SSH를 통해 **db3** **에서 로그인** 할 수 있는지 확인 합니다. 이 경우 `ssh` [키 기반 인증 사용](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)에 설명 된 대로 교환 키가 아닌 것입니다.  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

1. **[AH]** HANA 2.0 SP4에 필요한 추가 패키지를 설치합니다. 자세한 내용은 RHEL 7에 대한 SAP 노트 [2593824](https://launchpad.support.sap.com/#/notes/2593824)를 참조하세요. 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


1. **[A]** HANA 설치를 방해하지 않도록 방화벽을 일시적으로 사용하지 않도록 설정합니다. HANA 설치가 완료 된 후 다시 사용 하도록 설정할 수 있습니다. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>각 사이트의 첫 번째 노드에 HANA 설치

1. **[1]** [SAP HANA 2.0 설치 및 업데이트 가이드](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)의 지침에 따라 SAP HANA를 설치 합니다. 다음 지침에서는 사이트 1의 첫 번째 노드에 SAP HANA 설치를 보여 줍니다.   

   1. `hdblcm` `root` HANA 설치 소프트웨어 디렉터리에서 프로그램을 시작 합니다. `internal_network`매개 변수를 사용 하 고 내부 HANA 노드 간 통신에 사용 되는 서브넷에 대 한 주소 공간을 전달 합니다.  

      ```bash
      ./hdblcm --internal_network=10.23.1.128/26
      ```
   1. 프롬프트에서 다음 값을 입력합니다.

     * **작업 선택** 에 대해 **1** (설치의 경우)을 입력 합니다.
     * **설치용 추가 구성 요소** 에 대해 **2, 3** 을 입력 합니다.
     * 설치 경로에 대해 Enter 키를 누릅니다 (기본값은 */hana/shared*).
     * **로컬 호스트 이름** 에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **시스템에 호스트를 추가** 하 시겠습니까?에서 **n** 을 입력 합니다.
     * **SAP HANA 시스템 ID** 에 **h n 1** 를 입력 합니다.
     * **인스턴스 번호** [00]에 대해 **03** 을 입력 합니다.
     * **로컬 호스트 작업자 그룹** [기본값]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **시스템 사용 현황 선택/인덱스 [4] 입력** 에 대해 **4** (사용자 지정)를 입력 합니다.
     * **데이터 볼륨의 위치** [/hana/data/HN1]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **로그 볼륨의 위치** [/hana/log/HN1]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **최대 메모리 할당 제한?** [n] **n** 을 입력 합니다.
     * 호스트 hana-s1-db1 [hana-s1-db1] **에 대 한 인증서 호스트 이름** 에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **SAP 호스트 에이전트 사용자 (sapadm) 암호** 에 대해 암호를 입력 합니다.
     * **SAP 호스트 에이전트 사용자 (sapadm) 암호 확인** 에 암호를 입력 합니다.
     * **Hn1adm (시스템 관리자) 암호** 에 암호를 입력 합니다.
     * **시스템 관리자 홈 디렉터리** [/usr/sap/HN1/home]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **시스템 관리자 로그인 셸** [/bin/sh]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **시스템 관리자 사용자 ID** [1001]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **사용자 그룹의 ID (sapsys)** [79]에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * 시스템 **데이터베이스 사용자 (system) 암호** 에 시스템 암호를 입력 합니다.
     * 시스템 **데이터베이스 사용자 (시스템) 암호 확인** 에 시스템 암호를 입력 합니다.
     * **머신을 다시 부팅한 후 시스템 다시 시작?** [n] **n** 을 입력 합니다. 
     * **계속 하 시겠습니까 (y/n)**, 요약의 유효성을 검사 하 고 모든 항목이 양호 하면 **y** 를 입력 합니다.

1. **[2]** 이전 단계를 반복하여 SITE 2의 첫 번째 노드에 SAP HANA를 설치합니다.   

1. **[1, 2]** *global.ini* 확인 합니다.  

   *global.ini* 를 표시 하 고 내부 SAP HANA 노드 간 통신의 구성이 준비 되어 있는지 확인 합니다. 섹션을 확인 `communication` 합니다. `inter` 서브넷의 주소 공간이 있어야 하고 `listeninterface`가 `.internal`로 설정되어야 합니다. 섹션을 확인 `internal_hostname_resolution` 합니다. `inter` 서브넷에 속하는 HANA 가상 머신의 IP 주소가 있어야 합니다.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

1. **[1, 2]** SAP note [2080991](https://launchpad.support.sap.com/#/notes/0002080991)에 설명 된 대로 공유 되지 않는 환경에서 설치 *global.ini* 준비 합니다.  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

1. **[1,2]** SAP HANA를 다시 시작하여 변경 내용을 활성화합니다.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

1. **[1, 2]** 클라이언트 인터페이스가 통신을 위해 서브넷의 IP 주소를 사용 하는지 확인 `client` 합니다.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   구성을 확인 하는 방법에 대 한 자세한 내용은 SAP note [2183363-SAP HANA 내부 네트워크 구성](https://launchpad.support.sap.com/#/notes/2183363)을 참조 하세요.  

1. **[AH]** HANA 설치 오류를 방지 하려면 데이터 및 로그 디렉터리에 대 한 사용 권한을 변경 합니다.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

1. **[1]** 보조 HANA 노드를 설치합니다. 이 단계의 예제 지침은 SITE 1에 해당합니다.  
   1. `hdblcm`로 상주 프로그램을 시작 `root` 합니다.    
      ```bash
       cd /hana/shared/HN1/hdblcm
       ./hdblcm 
      ```

   1. 프롬프트에서 다음 값을 입력합니다.

     * **작업 선택** 에 대해 **2** 를 입력 합니다 (호스트 추가의 경우).
     * **추가할 쉼표로 구분 된 호스트 이름을 입력** 하십시오. hana-s1-db3을 입력 합니다.
     * **설치용 추가 구성 요소** 에 대해 **2, 3** 을 입력 합니다.
     * **Root 사용자 이름 입력 [root]** 에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **호스트 ' hana-s1-db2 ' [1]에 대 한 역할 선택** 에 대해 1 (작업자의 경우)을 선택 합니다.
     * **' Hana-s1-db2 ' 호스트에 대 한 호스트 장애 조치 (Failover) 그룹 입력 (기본값)** 에서 enter 키를 눌러 기본값을 적용 합니다.
     * **' hana-s1-db2 ' [ \<\<assign automatically\> \> ]에 대 한 Storage 파티션 번호 입력** 에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-s1-db2 ' 호스트에 대 한 작업자 그룹 입력 (기본값)** 에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **호스트 ' hana-s1-db3 ' [1]에 대 한 역할 선택** 에 대해 1 (작업자의 경우)을 선택 합니다.
     * **' Hana-s1-db3 ' 호스트에 대 한 호스트 장애 조치 (Failover) 그룹 입력 (기본값)** 에서 enter 키를 눌러 기본값을 적용 합니다.
     * **호스트 ' hana-db3 ' [ \<\<assign automatically\> \> ]에 대 한 enter Storage 파티션 번호** 에 대해 enter 키를 눌러 기본값을 적용 합니다.
     * **' Hana-db3 ' 호스트에 대 한 작업자 그룹 입력 (기본값)** 에서 enter 키를 눌러 기본값을 적용 합니다.
     * **Hn1adm (시스템 관리자) 암호** 에 암호를 입력 합니다.
     * **SAP 호스트 에이전트 사용자 (sapadm) 암호 입력** 에 대해 암호를 입력 합니다.
     * **SAP 호스트 에이전트 사용자(sapadm) 암호 확인에** 암호를 입력합니다.
     * **호스트 hana-s1-db2 [hana-s1-db2]의 인증서 호스트 이름에** 대해 Enter 키를 눌러 기본값을 적용합니다.
     * **호스트 hana-s1-db3 [hana-s1-db3]의 인증서 호스트 이름에** 대해 Enter 키를 눌러 기본값을 적용합니다.
     * **계속하시겠습니까(y/n)에** 요약의 유효성을 검사하고 모든 것이 양호하면 **y를** 입력합니다.

1. **[2]** SITE 2에 보조 SAP HANA 노드를 설치하는 이전 단계를 반복합니다.   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0 시스템 복제 구성

다음 단계를 수행하면 시스템 복제를 설정할 수 있습니다.

1. **[1] 사이트 1에서** 시스템 복제 구성:

   데이터베이스를 **hn1** adm으로 백업합니다.

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   시스템 PKI 파일을 보조 사이트에 복사합니다.

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   기본 사이트를 만듭니다.

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

1. **[2]** 사이트 2에서 시스템 복제 구성:
    
   두 번째 사이트를 등록하여 시스템 복제를 시작합니다. 다음 명령을 <hanasid\>adm으로 실행합니다.

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

1. **[1]** 복제 상태를 확인하고 모든 데이터베이스가 동기화될 때까지 기다립니다.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

1. **[1,2]** HANA 시스템 복제 가상 네트워크 인터페이스를 통해 HANA 시스템 복제에 대한 통신이 지시되도록 HANA 구성을 변경합니다.   
   1. 두 사이트에서 HANA를 중지합니다.
      ```bash
      sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
      ```

   1. *global.ini* 편집하여 HANA 시스템 복제에 대한 호스트 매핑을 추가합니다. 서브넷의 IP 주소를 `hsr` 사용합니다.  
      ```bash
      sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
      #Add the section
      [system_replication_hostname_resolution]
      10.23.1.202 = hana-s1-db1
      10.23.1.203 = hana-s1-db2
      10.23.1.204 = hana-s1-db3
      10.23.1.205 = hana-s2-db1
      10.23.1.206 = hana-s2-db2
      10.23.1.207 = hana-s2-db3
      ```

   1. 두 사이트에서 HANA를 시작합니다.
     ```bash
      sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
     ```

   자세한 내용은 [시스템 복제에 대한 호스트 이름 확인 을 참조하세요.](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)  

1. **[AH]** 방화벽을 다시 사용하도록 설정하고 필요한 포트를 엽니다.  
   1. 방화벽을 다시 사용하도록 설정합니다.
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   1. 필요한 방화벽 포트를 엽니다. HANA 인스턴스 번호에 대한 포트를 조정해야 합니다.  

       > [!IMPORTANT]
       > HANA 노드 간 통신 및 클라이언트 트래픽을 허용하는 방화벽 규칙을 만듭니다. 필요한 포트는 모든 [SAP 제품의 TCP/IP 포트에 나열됩니다.](https://help.sap.com/viewer/ports) 다음 명령은 예제입니다. 이 시나리오에서는 시스템 번호 03을 사용합니다.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

이 HANA 서버에 대한 기본 Pacemaker 클러스터를 만들려면 [Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정의](high-availability-guide-rhel-pacemaker.md)단계를 수행합니다. 클러스터의 주 결정자를 비롯한 모든 가상 머신을 포함합니다.  

> [!IMPORTANT]
> 를 2로 설정하지 `quorum expected-votes` 마세요. 2노드 클러스터가 아닙니다. `concurrent-fencing`노드 펜싱이 deserialized되도록 클러스터 속성이 사용하도록 설정되어 있는지 확인합니다.   

## <a name="create-file-system-resources"></a>파일 시스템 리소스 만들기

이 프로세스의 다음 부분에서는 파일 시스템 리소스를 만들어야 합니다. 방법은 다음과 같습니다.

1. **[1,2]** 두 복제 사이트에서 SAP HANA를 중지합니다. sid \> adm을 <실행합니다.  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

1. **[AH]** `/hana/shared`모든 HANA DB VM에 설치하기 위해 임시로 탑재된 파일 시스템 을 분리합니다. 탑재를 해제하려면 파일 시스템을 사용하는 프로세스와 세션을 모두 중지해야 합니다. 
 
    ```bash
    umount /hana/shared 
    ```

1. **[1]** 비활성화된 상태에서 에 대한 파일 시스템 클러스터 `/hana/shared` 리소스를 만듭니다. `--disabled`탑재를 사용하도록 설정하기 전에 위치 제약 조건을 정의해야 하므로 를 사용합니다.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20`모니터 작업에서 파일 시스템에서 읽기/쓰기 테스트를 수행하도록 특성이 모니터 작업에 추가됩니다. 이 특성이 없으면 모니터 작업은 파일 시스템이 탑재되어 있는지만 확인합니다. 연결이 끊어지면 액세스할 수 없더라도 파일 시스템이 탑재된 상태로 유지될 수 있기 때문에 문제가 될 수 있습니다.  

   `on-fail=fence`특성은 모니터 작업에도 추가됩니다. 이 옵션을 사용하면 노드에서 모니터 작업이 실패하는 경우 해당 노드가 즉시 펜싱됩니다. 이 옵션이 없으면 기본 동작은 실패한 리소스에 의존하는 모든 리소스를 중지한 다음 실패한 리소스를 다시 시작한 다음 실패한 리소스에 의존하는 모든 리소스를 시작하는 것입니다. 이 동작은 SAP HANA 리소스가 실패한 리소스에 의존하는 경우 시간이 오래 걸릴 수 있을 뿐만 아니라 모두 실패할 수도 있습니다. HANA 이진 파일을 보유하는 NFS 공유에 액세스할 수 없는 경우 SAP HANA 리소스를 성공적으로 중지할 수 없습니다.  

1. **[1]** 노드 특성을 구성하고 확인합니다. 복제 사이트 1의 모든 SAP HANA DB 노드에는 `S1` 특성이 할당되고, 복제 사이트 2의 모든 SAP HANA DB 노드에는 `S2` 특성이 할당됩니다.  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

1. **[1]** NFS 파일 시스템을 탑재할 위치를 결정하는 제약 조건을 구성하고 파일 시스템 리소스를 사용하도록 설정합니다.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   파일 시스템 리소스를 사용하도록 설정하면 클러스터에서 `/hana/shared` 파일 시스템을 탑재합니다.
 
1. **[AH]** 두 `/hana/shared` 사이트의 모든 HANA DB VM에서 Azure NetApp Files 볼륨이 에 탑재되어 있는지 확인합니다.

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

1. **[1]** 다음과 같이 특성 리소스를 구성 및 복제하고 제약 조건을 구성합니다.  

    ```bash
    # Configure the attribute resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribute resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > 구성에 / 이외의 파일 시스템이 포함되어 `hana/shared` 있고 이러한 파일 시스템이 NFS 탑재된 경우 옵션을 `sequential=false` 포함합니다. 이 옵션을 사용하면 파일 시스템 간에 순서가 정해지지 않습니다. 탑재된 모든 NFS 파일 시스템은 해당 특성 리소스 전에 시작해야 하지만 서로 상대적인 순서로 시작할 필요는 없습니다. 자세한 내용은 [HANA 파일 시스템이 NFS 공유인 경우 Pacemaker 클러스터에서 SAP HANA 스케일 아웃 HSR 구성](https://access.redhat.com/solutions/5423971)어떻게 할까요? 참조하세요.  

1. **[1]** HANA 클러스터 리소스 만들기를 준비하기 위해 Pacemaker를 유지 관리 모드로 배치합니다.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA 클러스터 리소스 만들기

이제 클러스터 리소스를 만들 준비가 되었습니다.

1. **[A]** 주 결정자 노드를 포함하여 모든 클러스터 노드에 HANA 스케일 아웃 리소스 에이전트를 설치합니다.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > 운영 체제 릴리스에 지원되는 최소 패키지 버전은 `resource-agents-sap-hana-scaleout` [RHEL HA 클러스터에 대한 지원 정책 - 클러스터의 SAP HANA 관리를](https://access.redhat.com/articles/3397471) 참조하세요.  

1. **[1,2]** 각 시스템 복제 사이트의 HANA DB 노드 하나에 HANA 시스템 복제 후크를 설치합니다. SAP HANA 여전히 중단되어야 합니다.        

   1. 후크를 `root`로 준비합니다. 
      ```bash
       mkdir -p /hana/shared/myHooks
       cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
       chown -R hn1adm:sapsys /hana/shared/myHooks
      ```

   1. 를 `global.ini` 조정합니다.
      ```bash
      # add to global.ini
      [ha_dr_provider_SAPHanaSR]
      provider = SAPHanaSR
      path = /hana/shared/myHooks
      execution_order = 1
    
      [trace]
      ha_dr_saphanasr = info
      ```

1. **[AH]** 클러스터에서 <sid\>adm용 클러스터 노드에 sudoers 구성을 요구합니다. 이 예제에서는 새 파일을 만들어 이를 달성합니다. 명령을 로 `root` 실행합니다.    
    ```bash
    sudo visudo -f /etc/sudoers.d/20-saphana
    # Insert the following lines and then save
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    Defaults!SOK, SFAIL !requiretty
    ```

1. **[1,2]** 두 복제 사이트에서 SAP HANA를 시작합니다. sid \> adm을 <실행합니다.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

1. **[1]** 후크 설치를 확인합니다. 활성 HANA 시스템 복제 사이트에서 <sid \> adm으로 실행합니다.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

1. **[1]** HANA 클러스터 리소스를 만듭니다. 다음 명령을 로 `root` 실행합니다.    
   1. 클러스터가 이미 유지 관리 모드에 있는지 확인합니다.  
    
   1. 다음으로 HANA 토폴로지 리소스를 만듭니다.  
      RHEL **7.x** 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      RHEL **8.x** 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   1. HANA 인스턴스 리소스를 만듭니다.  
      > [!NOTE]
      > 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *슬레이브* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.  
 
      RHEL **7.x** 클러스터를 빌드하는 경우 다음 명령을 사용합니다.    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      RHEL **8.x** 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > `AUTOMATED_REGISTER` `false` 장애 조치(failover) 테스트를 수행하는 동안 을 로 설정하여 실패한 주 인스턴스가 자동으로 보조 인스턴스로 등록되지 않도록 하는 것이 좋습니다. 테스트 후에는 인수 후 시스템 복제를 자동으로 다시 시작할 수 있도록 를 로 설정하는 `AUTOMATED_REGISTER` `true` 것이 좋습니다. 

   1. 가상 IP 및 연결된 리소스를 만듭니다.  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   1. 클러스터 제약 조건을 만듭니다.  
      RHEL **7.x** 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      RHEL **8.x** 클러스터를 빌드하는 경우 다음 명령을 사용합니다.  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

1. **[1]** 클러스터를 유지 관리 모드에서 제외시킵니다. 클러스터 상태가 `ok` 이고 모든 리소스가 시작되었는지 확인합니다.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > 이전 구성의 시간 제한은 예제일 뿐이며 특정 HANA 설정에 맞게 조정해야 할 수도 있습니다. 예를 들어 SAP HANA 데이터베이스를 시작하는 데 시간이 오래 걸리는 경우 시작 시간 초과를 늘려야 할 수 있습니다.
  
## <a name="configure-hana-activeread-enabled-system-replication"></a>HANA 활성/읽기 가능 시스템 복제 구성

SAP HANA 2.0 SPS 01부터 SAP는 SAP HANA 시스템 복제에 대 한 활성/읽기 지원 설정을 허용 합니다. 이 기능을 사용 하면 읽기 집약적 작업을 위해 SAP HANA 시스템 복제의 보조 시스템을 사용할 수 있습니다. 클러스터에서 이러한 설정을 지원 하려면 클라이언트가 보조 읽기 가능 SAP HANA 데이터베이스에 액세스할 수 있도록 하는 두 번째 가상 IP 주소가 필요 합니다. 인수 발생 한 후에도 보조 복제 사이트에 액세스할 수 있도록 하려면 클러스터에서 SAP HANA 리소스의 보조 데이터베이스에 대 한 가상 IP 주소를 이동 해야 합니다.

이 섹션에서는 두 번째 가상 IP 주소를 사용 하 여 Red Hat 고가용성 클러스터에서이 유형의 시스템 복제를 관리 하기 위해 수행 해야 하는 추가 단계에 대해 설명 합니다.  

계속 진행 하기 전에이 문서의 앞부분에서 설명한 대로 SAP HANA 데이터베이스를 관리 하는 Red Hat 고가용성 클러스터를 완전히 구성 했는지 확인 합니다.  

![읽기 가능 보조를 사용 하 여 스케일 아웃 고가용성 SAP HANA](./media/sap-hana-high-availability-rhel/sap-hana-high-avalability-scale-out-hsr-rhel-read-enabled.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>활성/읽기 가능 설치를 위한 Azure Load Balancer의 추가 설정

두 번째 가상 IP의 프로 비전을 계속 하려면 [배포 Azure Load Balancer](#deploy-azure-load-balancer)에 설명 된 대로 Azure Load Balancer를 구성 했는지 확인 합니다.

*표준* 부하 분산 장치에 대해 이전 섹션에서 만든 것과 동일한 부하 분산 장치에서 다음 추가 단계를 수행 합니다.

1. 두 번째 프런트 엔드 IP 풀 만들기: 

   1. 부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
   1. 두 번째 프런트 엔드 IP 풀의 이름을 입력합니다(예: *hana-secondaryIP*).
   1. **할당** 을 **정적** 으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.23.0.19**).
   1. **확인** 을 선택합니다.
   1. 새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.

1. 다음으로, 상태 프로브를 만듭니다.

   1. 부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
   1. 새 상태 프로브의 이름(예: *hana-secondaryhp*)을 입력합니다.
   1. 프로토콜 및 포트 **62603** 으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
   1. **확인** 을 선택합니다.

1. 다음으로 부하 분산 규칙을 만듭니다.

   1. 부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
   1. 새 부하 분산 장치 규칙의 이름(예: *hana-secondarylb*)을 입력합니다.
   1. 앞에서 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 (예: **Hana secondaryIP**, **hana 백 엔드** 및 **hana secondaryip**)를 선택 합니다.
   1. **HA 포트** 를 선택합니다.
   1. **부동 IP를 사용하도록 설정** 했는지 확인합니다.
   1. **확인** 을 선택합니다.

### <a name="configure-hana-activeread-enabled-system-replication"></a>HANA 활성/읽기 가능 시스템 복제 구성

HANA 시스템 복제를 구성 하는 단계는 [SAP HANA 2.0 시스템 복제 구성](#configure-sap-hana-20-system-replication) 섹션에 설명 되어 있습니다. 읽기 가능 보조 시나리오를 배포 하는 경우 두 번째 노드에서 시스템 복제를 구성 하는 동안 **hanasid** adm으로 다음 명령을 실행 합니다.

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2 --operationMode=logreplay_readaccess 
```

### <a name="add-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>활성/읽기 사용 설정의 보조 가상 IP 주소 리소스 추가

다음 명령을 사용 하 여 두 번째 가상 IP와 추가 제약 조건을 구성할 수 있습니다. 보조 인스턴스가 종료 되 면 보조 가상 IP가 주 복제본으로 전환 됩니다.   

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.23.0.19"
pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603
pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

# RHEL 8.x: 
pcs constraint location g_ip_HN1_03 rule score=500 role=master hana_hn1_roles eq "master1:master:worker:master" and hana_hn1_clone_state eq PROMOTED
pcs constraint location g_secip_HN1_03 rule score=50  hana_hn1_roles eq 'master1:master:worker:master'
pcs constraint order promote  SAPHana_HN1_HDB03-clone then start g_ip_HN1_03
pcs constraint order start g_ip_HN1_03 then start g_secip_HN1_03
pcs constraint colocation add g_secip_HN1_03 with Slave SAPHana_HN1_HDB03-clone 5

# RHEL 7.x:
pcs constraint location g_ip_HN1_03 rule score=500 role=master hana_hn1_roles eq "master1:master:worker:master" and hana_hn1_clone_state eq PROMOTED
pcs constraint location g_secip_HN1_03 rule score=50  hana_hn1_roles eq 'master1:master:worker:master'
pcs constraint order promote  msl_SAPHana_HN1_HDB03 then start g_ip_HN1_03
pcs constraint order start g_ip_HN1_03 then start g_secip_HN1_03
pcs constraint colocation add g_secip_HN1_03 with Slave msl_SAPHana_HN1_HDB03 5

pcs property set maintenance-mode=false
```
클러스터 상태가 이며 `ok` 모든 리소스가 시작 되었는지 확인 합니다. 두 번째 가상 IP는 보조 사이트에서 SAP HANA 보조 리소스와 함께 실행 됩니다.

```
# Example output from crm_mon
#Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#
#Active resources:
#
#rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
#Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
#    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
#Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
#    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
#    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
#Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
#    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
#    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
#    Masters: [ hana-s1-db1 ]
#    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
#Resource Group: g_ip_HN1_03
#    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
#    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
#Resource Group: g_secip_HN1_03
#    secnc_HN1_03       (ocf::heartbeat:azure-lb):      Started hana-s2-db1
#    secvip_HN1_03      (ocf::heartbeat:IPaddr2):       Started hana-s2-db1

```

다음 섹션에서는 실행할 일반적인 장애 조치 (failover) 테스트 집합을 찾을 수 있습니다.

읽기 가능 보조 데이터베이스를 사용 하 여 구성 된 HANA 클러스터를 테스트 하는 경우 두 번째 가상 IP의 다음 동작에 유의 해야 합니다.

- 클러스터 리소스 **SAPHana_HN1_HDB03** 가 보조 사이트 (**S2**)로 이동 하는 경우 두 번째 가상 IP는 다른 사이트인 **hana-s1-db1** 로 이동 합니다. 를 구성 하 `AUTOMATED_REGISTER="false"` 고 hana 시스템 복제를 자동으로 등록 하지 않은 경우 두 번째 가상 IP가 **hana-s2-db1** 에서 실행 됩니다.  

- 서버 충돌을 테스트 하는 경우 두 번째 가상 ip 리소스 (**secvip_HN1_03**) 및 Azure Load Balancer 포트 리소스 (**secnc_HN1_03**)는 주 가상 ip 리소스와 함께 주 서버에서 실행 됩니다. 보조 서버가 다운 상태인 동안 읽기 사용 HANA 데이터베이스에 연결된 애플리케이션은 주 HANA 데이터베이스에 연결됩니다. 이는 정상적인 동작입니다. 이를 통해 읽기 지원 HANA 데이터베이스에 연결 된 응용 프로그램은 보조 서버를 사용할 수 없는 동안 작동할 수 있습니다.   
  
- 장애 조치 (failover) 및 대체 중에 두 번째 가상 IP를 사용 하 여 HANA 데이터베이스에 연결 하는 응용 프로그램에 대 한 기존 연결이 중단 될 수 있습니다.  

## <a name="test-sap-hana-failover"></a>SAP HANA 장애 조치(failover) 테스트 

1. 테스트를 시작하기 전에 클러스터와 SAP HANA 시스템 복제 상태를 확인합니다.  

   1. 실패 한 클러스터 작업이 없는지 확인 합니다.  
       ```bash
       #Verify that there are no failed cluster actions
       pcs status
       # Example
       #Stack: corosync
       #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
       #Last updated: Thu Sep 24 06:00:20 2020
       #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
       #
       #7 nodes configured
       #45 resources configured
       #
       #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #
       #Active resources:
       #
       #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
       #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
       #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
       #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
       #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
       #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
       #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
       #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
       #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
       #    Masters: [ hana-s1-db1 ]
       #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
       #Resource Group: g_ip_HN1_03
       #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
       #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
       ```

   1. SAP HANA 시스템 복제가 동기화 되어 있는지 확인 하십시오.

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

1. 노드가 NFS 공유(`/hana/shared`)에 대한 액세스 권한을 상실하면 장애 시나리오에 대한 클러스터 구성을 확인합니다.  

   SAP HANA 리소스 에이전트는 `/hana/shared` 장애 조치 (failover) 중에 작업을 수행 하기 위해에 저장 된 이진 파일에 종속 됩니다. `/hana/shared` 파일 시스템은 제시된 구성의 NFS를 통해 탑재됩니다. 수행할 수 있는 한 가지 테스트는 `/hana/shared` 파일 시스템을 *읽기 전용* 으로 다시 탑재 하는 것입니다. 이 방식은 활성 시스템 복제 사이트에서 `/hana/shared`에 대한 액세스 권한을 상실할 때 클러스터 장애 조치(failover)가 이루어지는지 확인합니다.  

   **예상 결과**: 읽기 전용으로 다시 탑재 하 `/hana/shared` 는 경우 파일 시스템에서 읽기/쓰기 작업을 수행 하는 모니터링 작업이 실패 합니다.  이는 파일 시스템에 쓸 수 없고 HANA 리소스 장애 조치 (failover)를 트리거할 수 있기 때문입니다. HANA 노드가 NFS 공유에 액세스할 수 없는 경우에도 동일한 결과가 예상됩니다.  
     
   또는를 실행 하 여 클러스터 리소스의 상태를 확인할 수 있습니다 `crm_mon` `pcs status` . 테스트 시작 전 리소스 상태:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   `/hana/shared`주 복제 사이트 vm 중 하나에서 실패를 시뮬레이트하려면 다음 명령을 실행 합니다.
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the preceding command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   클러스터 구성에 따라에 대 한 액세스를 잃은 HANA VM을 `/hana/shared` 다시 시작 하거나 중지 해야 합니다. 클러스터 리소스가 다른 HANA 시스템 복제 사이트로 마이그레이션됩니다.  
         
   다시 시작 된 VM에서 클러스터가 시작 되지 않은 경우 다음을 실행 하 여 클러스터를 시작 합니다. 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   클러스터가 시작 되 면 파일 시스템이 `/hana/shared` 자동으로 탑재 됩니다. 를 설정 하는 경우 `AUTOMATED_REGISTER="false"` 보조 사이트에서 SAP HANA 시스템 복제를 구성 해야 합니다. 이 경우 다음 명령을 실행 하 여 SAP HANA를 보조로 다시 구성할 수 있습니다.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and clean up failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   테스트 후의 리소스 상태는 다음과 같습니다. 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


[RHEL의 Azure vm에서 SAP HANA에 대해 HA](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)에 설명 된 테스트를 수행 하 여 SAP HANA 클러스터 구성을 철저히 테스트 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md)
* 고가용성을 설정 하 고 Azure Vm의 SAP HANA에 대 한 재해 복구를 계획 하는 방법을 알아보려면 [Azure vm에서 고가용성 SAP HANA][sap-hana-ha]을 참조 하세요.
