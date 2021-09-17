---
title: Azure의 SAP HANA(대규모 인스턴스) 설치 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37eff4049973c208ebe70ef452f121109f0c92ed
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435837"
---
# <a name="install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure(대규모 인스턴스) 설치 및 구성

이 문서에서는 BareMetal Infrastructure라고도 하는 Azure의 SAP HLI(HANA(대규모 인스턴스))의 유효성을 검사하고, 구성하고, 설치하는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 읽기 전에 다음 사항을 숙지해야 합니다.
- [HANA 대규모 인스턴스 일반적인 용어](hana-know-terms.md)
- [HANA 대규모 인스턴스 SKU](hana-available-skus.md)

또한 다음을 참조하세요.
- [Azure VM을 HANA 큰 인스턴스에 연결](hana-connect-azure-vm-large-instances.md)
- [가상 네트워크를 HANA 대규모 인스턴스에 연결](hana-connect-vnet-express-route.md)

## <a name="planning-your-installation"></a>설치 계획

SAP HANA 설치는 사용자의 책임입니다. Azure 가상 네트워크와 HANA 대규모 인스턴스 단위 간의 연결을 설정한 후 Azure의 SAP HANA(대규모 인스턴스) 설치를 새로 시작할 수 있습니다. 

> [!Note]
> SAP 정책에 따라, SAP HANA 설치 인증 시험인 Certified SAP Technology Associate 시험을 통과했거나 SAP 인증 SI(시스템 통합자)인 사람이 SAP HANA 설치를 수행해야 합니다.

HANA 2.0을 설치하려는 경우 [SAP 지원 노트 #2235581 - SAP HANA: 지원되는 운영 체제](https://launchpad.support.sap.com/#/notes/2235581/E)를 참조하세요. OS(운영 체제)가 설치하려는 SAP HANA 릴리스에서 지원되는지 확인합니다. HANA 2.0에서 지원되는 OS는 HANA 1.0에서 지원되는 OS보다 더 제한적입니다. 원하는 OS 릴리스가 특정 HANA 대규모 인스턴스를 지원하는지 확인합니다. 이 [목록](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 HLI를 선택하여 해당 단위를 지원하는 OS 목록의 세부 정보를 확인합니다. 

HANA 설치를 시작하기 전에 다음을 유효성 검사합니다.
- [HLI 단위](#validate-the-hana-large-instance-units)
- [운영 체제 구성](#operating-system)
- [네트워크 구성](#networking)
- [스토리지 구성](#storage)


## <a name="validate-the-hana-large-instance-units"></a>HANA 대규모 인스턴스 단위 유효성 검사

Microsoft에서 HANA 대규모 인스턴스를 받은 후에는 인스턴스에 대한 액세스 및 연결을 설정합니다. 그 후에는 다음 설정의 유효성을 검사하고 필요한 대로 조정합니다.

1. 인스턴스가 올바른 SKU 및 OS와 함께 표시되는지 Azure Portal에서 확인합니다. 자세한 내용은 [Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어](./hana-li-portal.md)를 참조하세요.

2. 인스턴스의 OS를 OS 공급자에 등록합니다. 이 단계에는 Azure의 VM에 배포된 SUSE SMT(구독 관리 도구) 인스턴스에 SUSE Linux OS를 등록하는 작업이 포함됩니다. 

    HANA 대규모 인스턴스는 SMT 인스턴스에 연결할 수 있습니다. 자세한 내용은 [SUSE Linux용 SMT 서버를 설정하는 방법](hana-setup-smt.md)을 참조하세요. Red Hat OS를 사용하는 경우 연결하려는 Red Hat Subscription Manager에 Red Hat OS를 등록해야 합니다. 자세한 내용은 [Azure의 SAP HANA(대규모 인스턴스)란?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json)의 설명을 참조하세요. 

    이 단계는 OS를 패치하는 데 필요하며 고객의 책임입니다. SUSE의 경우 [SMT 설치 및 구성](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)에 대한 설명서를 참조하세요.

3. 특정 OS 릴리스/버전의 새 패치 및 픽스를 확인합니다. HANA 대규모 인스턴스에 최신 패치가 있는지 확인합니다. 최신 패치가 포함되지 않은 경우가 가끔 있으므로 반드시 확인해야 합니다.

4. 특정 OS 릴리스/버전에서 SAP HANA를 설치하고 구성하는 작업과 관련된 SAP 노트를 확인합니다. Microsoft에서 항상 HLI를 완전히 구성하는 것은 아닙니다. 개별 시나리오에 따라 권장 사항이 변경되거나 SAP 노트 또는 구성 변경으로 인해 불가능할 수도 있습니다.  

    따라서 해당 Linux 릴리스의 SAP HANA와 관련된 SAP 노트를 반드시 읽어야 합니다. 또한 OS 릴리스/버전의 구성을 확인하고, 아직 수행하지 않은 경우 구성 설정을 적용합니다.

    특히, 다음 매개 변수를 확인하고 다음과 같이 조정합니다.

    - net.core.rmem_max = 16777216
    - net.core.wmem_max = 16777216
    - net.core.rmem_default = 16777216
    - net.core.wmem_default = 16777216
    - net.core.optmem_max = 16777216
    - net.ipv4.tcp_rmem = 65536 16777216 16777216
    - net.ipv4.tcp_wmem = 65536 16777216 16777216

    SLES12 SP1 및 RHEL(Red Hat Enterprise Linux) 7.2부터 이러한 매개 변수를 /etc/sysctl.d 디렉터리의 구성 파일에 설정해야 합니다. 예를 들어 이름이 91-NetApp-HANA.conf인 구성 파일을 만들어야 합니다. 이전 SLES 및 RHEL 릴리스의 경우 /etc/sysctl.conf에서 이러한 매개 변수를 설정해야 합니다.

    RHEL 6.3부터 모든 RHEL 릴리스에서는 다음 사항에 유의하세요. 
    - sunrpc.tcp_slot_table_entries = 128 매개 변수를 /etc/modprobe.d/sunrpc-local.conf에서 설정해야 합니다. 이 파일이 없는 경우 먼저 다음 항목을 추가하여 파일을 만듭니다. 
        - options sunrpc tcp_max_slot_table_entries=128

5. HANA 대규모 인스턴스의 시스템 시간을 확인합니다. 인스턴스는 시스템 표준 시간대로 배포됩니다. 이 표준 시간대는 HANA 대규모 인스턴스 스탬프가 있는 Azure 지역의 위치를 나타냅니다. 소유한 인스턴스의 시스템 시간이나 표준 시간대를 변경할 수 있습니다. 

    더 많은 인스턴스를 테넌트에 주문하는 경우 새로 전달된 인스턴스의 표준 시간대를 조정해야 합니다. Microsoft에서는 인계 후 인스턴스에서 사용자가 설정한 시스템 표준 시간대를 알 수 없습니다. 따라서 새로 배포된 인스턴스가 고객이 변경한 표준 시간대와 동일한 표준 시간대로 설정되지 않을 수 있습니다. 전달된 인스턴스의 표준 시간대를 필요한 대로 조정하는 것은 고객의 몫입니다. 

6. etc/hosts를 확인합니다. 인계 시 블레이드에는 각기 다른 용도로 할당된 여러 IP 주소가 있습니다. 단위가 기존 테넌트에 추가되면 etc/hosts 파일을 꼭 확인해야 합니다. 새로 배포된 시스템의 etc/hosts 파일은 이전에 제공된 시스템의 IP 주소에서 올바르게 유지되지 않을 수 있습니다. 새로 배포된 인스턴스가 이전에 테넌트에 배포한 단위의 이름을 확인할 수 있도록 하는 것은 고객의 책임입니다. 


## <a name="operating-system"></a>운영 체제

제공되는 OS 이미지의 스왑 공간은 [SAP Support Note #1999997 - FAQ: SAP HANA 메모리](https://launchpad.support.sap.com/#/notes/1999997/E)(영문)에 따라 2GB로 설정됩니다. 다른 설정을 원하는 경우 고객이 직접 설정해야 합니다.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/)는 Azure의 SAP HANA(대규모 인스턴스)용으로 설치되는 Linux 배포판입니다. 이 배포판은 SLES에서 SAP를 효과적으로 실행하기 위해 미리 설정된 매개 변수를 포함하여 SAP 관련 기능을 제공합니다.

SLES에 SAP HANA를 배포하는 작업과 관련된 여러 가지 유용한 리소스 정보는 다음을 참조하세요.
- SUSE 웹 사이트의 [리소스 라이브러리/백서](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)
- SCN(SAP 커뮤니티 네트워크)의 [SUSE의 SAP](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)

이러한 리소스에는 고가용성 설정, SAP 작업과 관련된 보안 강화 등에 대한 정보가 포함되어 있습니다.

다음은 SUSE의 SAP에 대한 추가 리소스입니다.

- [SUSE Linux의 SAP HANA 사이트](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 모범 사례: 복제 큐에 넣기 – SUSE Linux Enterprise 12의 SAP NetWeaver](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf)(영문)
- [ClamSAP - SAP용 SLES 바이러스 방지](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)(SLES 12 for SAP Applications 포함)(영문)

다음 문서는 SLES 12에서 SAP HANA를 구현할 때 적용되는 SAP 지원 노드입니다.

- [SAP Support Note #1944799 – SLES 운영 체제 설치에 대한 SAP HANA 지침](http://service.sap.com/sap/support/notes/1944799)(영문)
- [SAP Support Note #2205917 – SAP HANA DB: SLES 12 for SAP Applications에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2205917/E)(영문)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: 설치 노트](https://launchpad.support.sap.com/#/notes/1984787)(영문)
- [SAP Support Note #171356 – SAP Software on Linux: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)(영문)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)

[SAP HANA용 Red Hat Enterprise Linux](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana)(영문)는 HANA 큰 인스턴스에서 SAP HANA를 실행하기 위한 또 다른 제품입니다. RHEL 7.2 및 7.3 릴리스를 사용할 수 있으며 지원됩니다. Red Hat의 SAP에 대한 자세한 내용은 [SAP HANA on Red Hat Linux 사이트](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)를 참조하세요.

다음 문서는 Red Hat에서 SAP HANA를 구현할 때 적용되는 SAP 지원 노드입니다.

- [SAP Support Note #2009879 – RHEL(Red Hat Enterprise Linux) 운영 체제에 대한 SAP HANA 지침](https://launchpad.support.sap.com/#/notes/2009879/E)(영문)
- [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)(영문)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)
- [SAP Support Note #2228351 - Linux: RHEL 6 또는 SLES 11의 SAP HANA Database SPS 11 수정 번호 110 이상](https://launchpad.support.sap.com/#/notes/2228351)(영문)
- [SAP Support Note #2397039 - FAQ: RHEL의 SAP](https://launchpad.support.sap.com/#/notes/2397039)(영문)
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/2002167)(영문)

### <a name="time-synchronization"></a>시간 동기화

SAP NetWeaver 아키텍처를 기반으로 구축된 SAP 애플리케이션은 SAP 시스템 구성 요소의 시간 차이에 민감합니다. 오류 제목이 ZDATE\_LARGE\_TIME\_DIFF인 SAP ABAP 짧은 덤프를 본 적이 있을 것입니다. 이러한 짧은 덤프는 여러 서버 또는 VM(가상 머신)의 시스템 시간 차이가 너무 큰 경우에 표시되기 때문입니다.

Azure의 SAP HANA(대규모 인스턴스)의 경우 Azure에서 수행되는 시간 동기화가 대규모 인스턴스 스탬프의 컴퓨팅 단위에 적용되지 않습니다. 네이티브 Azure VM에서 SAP 애플리케이션을 실행하는 경우에도 적용되지 않습니다. Azure에서 시스템 시간이 적절하게 동기화되도록 관리하기 때문입니다. 

따라서 별도의 시간 서버를 설정해야 합니다. 이 서버는 Azure VM에서 실행되는 SAP 애플리케이션 서버에서 사용됩니다. HANA 대규모 인스턴스에서 실행되는 SAP HANA 데이터베이스 인스턴스에서도 사용됩니다. 대규모 인스턴스 스탬프의 스토리지 인프라는 NTP(네트워크 시간 프로토콜) 서버와 시간이 동기화됩니다.


## <a name="networking"></a>네트워킹
Azure 가상 네트워크를 디자인하고 이러한 가상 네트워크를 HANA 대규모 인스턴스에 연결할 때, 다음 문서에 설명된 권장 사항을 따라야 합니다.

- [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](./hana-overview-architecture.md)
- [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md)

단일 단위의 네트워킹에 대해 알아야 할 몇 가지 세부 정보가 있습니다. 모든 HANA 대규모 인스턴스 단위에는 2개 또는 3개의 NIC(네트워크 인터페이스 컨트롤러) 포트에 할당된 2개 또는 3개의 IP 주소가 포함되어 있습니다. HANA 확장 구성 및 HANA 시스템 복제 시나리오에는 3개의 IP 주소가 사용됩니다. 단위의 NIC에 할당된 IP 주소 중 하나가 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](./hana-overview-architecture.md)에 설명된 서버 IP 풀을 벗어납니다.

아키텍처의 이더넷 세부 정보에 대한 자세한 내용은 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.

## <a name="storage"></a>Storage

Azure의 SAP HANA(대규모 인스턴스)에 대한 스토리지 레이아웃은 SAP 권장 지침에 따라 Azure의 SAP HANA 서비스 관리를 통해 구성됩니다. 이 지침은 [SAP HANA 스토리지 요구 사항](https://blogs.saphana.com/wp-content/uploads/2015/02/Storage-Whitepaper-2-54.pdf)에 설명되어 있습니다. 

각기 다른 HANA 대규모 인스턴스 SKU가 포함된 다양한 볼륨의 대략적인 크기는 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md)에 문서화되어 있습니다.

스토리지 볼륨에 대한 명명 규칙은 아래 테이블에 나열되어 있습니다.

| 스토리지 사용 | 탑재 이름 | 볼륨 이름 | 
| --- | --- | ---|
| HANA data | /hana/data/SID/mnt0000\<m> | 스토리지 IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA log | /hana/log/SID/mnt0000\<m> | 스토리지 IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA log backup | /hana/log/backups | 스토리지 IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA shared | /hana/shared/SID | 스토리지 IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 스토리지 IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* 는 HANA 인스턴스 시스템 ID입니다. 

*Tenant* 는 테넌트 배포 시 작업의 내부 열거형입니다.

HANA usr/sap는 동일한 볼륨을 공유합니다. 탑재 지점의 명명법에는 HANA 인스턴스의 시스템 ID와 탑재 번호가 포함됩니다. 스케일 업 배포의 경우 탑재가 하나만 있습니다(예: mnt00001). 스케일 아웃 배포의 경우 보유한 작업자 및 기본 노드만큼 많은 탑재를 볼 수 있습니다. 

스케일 아웃 환경에서는 데이터, 로그 및 로그 백업 볼륨이 공유되고 스케일 아웃 구성의 각 노드에 연결됩니다. 여러 SAP 인스턴스로 이루어진 구성에서는 다른 볼륨 세트가 만들어져 HANA 대규모 인스턴스에 연결됩니다. 시나리오에 대한 스토리지 레이아웃 정보는 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.

HANA 대규모 인스턴스는 HANA/데이터를 위한 넉넉한 디스크 볼륨과 HANA/로그/백업 볼륨이 함께 제공됩니다. 스토리지 스냅샷이 동일한 디스크 볼륨을 사용하기 때문에 HANA/데이터를 아주 크게 만들었습니다. 스토리지 스냅샷을 많이 수행할수록 할당된 스토리지 볼륨에서 더 많은 공간이 스냅샷에 사용됩니다. 

HANA/로그/백업 볼륨은 데이터베이스 백업용 볼륨으로 지원되지 않습니다. HANA 트랜잭션 로그 백업용 백업 볼륨으로 사용하도록 크기가 설정되었습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조하세요. 

1TB 단위로 추가 용량을 구매하여 스토리지를 늘릴 수 있습니다. 이 추가 스토리지는 HANA 대규모 인스턴스에 새 볼륨으로 추가할 수 있습니다.

고객은 Azure의 SAP HANA 서비스 관리에 등록할 때 sidadm 사용자와 sapsys 그룹의 UID(사용자 ID) 및 GID(그룹 ID)를 지정하게 됩니다(예: 1000,500). SAP HANA 시스템을 설치하는 동안 동일한 값을 사용해야 합니다. 한 단위에 여러 개의 HANA 인스턴스를 배포하려고 하므로 다수의 볼륨 세트(인스턴스당 하나씩)를 갖게 됩니다. 따라서 배포 시 다음을 정의해야 합니다.

- 다양한 HANA 인스턴스의 SID(sidadm은 여기서 파생됨)
- 다양한 HANA 인스턴스의 메모리 크기. 인스턴스당 메모리 크기는 각각의 개별적인 볼륨 세트에서 볼륨 크기를 정의합니다.

스토리지 공급자 권장 사항에 따라 탑재된 모든 볼륨(부팅 LUN 제외)에 대해 다음 탑재 옵션이 구성됩니다.

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

이러한 탑재 지점은 다음 스크린샷처럼 /etc/fstab에 구성됩니다.

![HANA 대규모 인스턴스 단위에 탑재된 볼륨의 fstab를 보여주는 스크린샷](./media/hana-installation/image1_fstab.PNG)

S72m HANA 대규모 인스턴스에 대한 df -h 명령의 출력은 다음과 같습니다.

![HANA 대규모 인스턴스에 대한 명령의 출력을 보여주는 스크린샷](./media/hana-installation/image2_df_output.PNG)


큰 인스턴스 스탬프의 스토리지 컨트롤러와 노드는 NTP 서버와 동기화됩니다. Azure의 SAP HANA(대규모 인스턴스)와 Azure VM을 NTP 서버와 동기화하는 것이 중요합니다. 이렇게 하면 인프라와 Azure 또는 대규모 인스턴스 스탬프의 컴퓨팅 단위 간의 시간 차이가 상당히 제거됩니다.

아래 사용된 스토리지에 대해 SAP HANA를 최적화하려면 다음 SAP HANA 구성 매개 변수를 설정합니다.

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 버전 SPS12까지는 [SAP Note #2267798 - SAP HANA 데이터베이스의 구성](https://launchpad.support.sap.com/#/notes/2267798)에 설명된 대로 SAP HANA 데이터베이스 설치 중에 이러한 매개 변수를 설정할 수 있습니다.

hdbparam 프레임워크를 사용하여 SAP HANA 데이터베이스 설치 후 매개 변수를 구성할 수도 있습니다. 

HANA 대규모 인스턴스에서 사용된 스토리지에는 파일 크기 제한이 있습니다. 파일당 [크기 제한은 16TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)입니다. HANA는 EXT3 파일 시스템의 파일 크기 제한과 달리 HANA(대규모 인스턴스) 스토리지에 적용된 스토리지 제한을 암시적으로 인식하지 못합니다. 그 결과, HANA는 16TB 파일 크기 제한에 도달한 경우 새 데이터 파일을 자동으로 만들지 않습니다. HANA가 16TB를 초과하도록 파일을 확장하려고 하면, HANA가 오류를 보고하며 인덱스 서버 작동이 결국 중단됩니다.

> [!IMPORTANT]
> HANA가 HANA 대규모 인스턴스 스토리지의 16TB 파일 크기 제한을 초과하여 데이터 파일을 확장하지 않도록 SAP HANA의 global.ini 구성 파일에 다음 매개 변수를 설정합니다.
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)도 참조
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)에 유의


SAP HANA 2.0에서는 hdbparam 프레임워크가 사용되지 않습니다. 따라서 SQL 명령을 사용하여 매개 변수를 설정해야 합니다. 자세한 내용은 [SAP Note #2399079: HANA 2에서 hdbparam 제거](https://launchpad.support.sap.com/#/notes/2399079)를 참조하세요.

아키텍처의 스토리지 레이아웃에 대한 자세한 내용은 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Azure의 SAP HANA(대규모 인스턴스)를 설치하는 단계를 진행합니다.

> [!div class="nextstepaction"]
> [Azure의 SAP HANA(대규모 인스턴스)에 HANA 설치](hana-example-installation.md)
