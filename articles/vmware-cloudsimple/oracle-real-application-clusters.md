---
title: Azure VMware Solution by CloudSimple - Oracle RAC용 CloudSimple 프라이빗 클라우드 최적화
description: 새 클러스터를 배포하고 Oracle RAC(Real Application Clusters) 설치 및 구성을 위해 VM을 최적화하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 098f9a65af4537dace4f6ac0edb2ff71df34c3d2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232012"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Oracle RAC 설치를 위해 CloudSimple 프라이빗 클라우드 최적화

CloudSimple 프라이빗 클라우드 환경에 Oracle RAC(Real Application Clusters)를 배포할 수 있습니다. 이 가이드에서는 새 클러스터를 배포하고 Oracle RAC 솔루션을 위해 VM을 최적화하는 방법을 설명합니다. 이 항목의 단계를 완료한 후 Oracle RAC를 설치하고 구성할 수 있습니다.

## <a name="storage-policy"></a>스토리지 정책

Oracle RAC를 성공적으로 구현하려면 클러스터에 적절한 수의 노드가 필요합니다.  vSAN 스토리지 정책에서는 데이터베이스, 로그 및 다시 실행 디스크를 저장하는 데 사용되는 데이터 디스크에 FTT(허용 실패)가 적용됩니다.  실패를 효과적으로 허용하기 위해 필요한 노드 수는 2N+1개입니다. 여기서 N은 FTT의 값입니다.

예를 들어 원하는 FTT가 2인 경우 클러스터의 총 노드 수는 2*2+1 = 5개여야 합니다.

## <a name="overview-of-deployment"></a>배포 개요

다음 섹션에서는 Oracle RAC용 CloudSimple 프라이빗 클라우드 환경을 설정하는 방법을 설명합니다.

1. 디스크 구성에 대한 모범 사례
2. CloudSimple 프라이빗 클라우드 vSphere 클러스터 배포
3. Oracle RAC에 대한 네트워킹 설정
4. vSAN 스토리지 정책 설정
5. Oracle VM 만들기 및 공유 VM 디스크 만들기
6. VM-호스트 선호도 규칙 설정

## <a name="best-practices-for-disk-configuration"></a>디스크 구성에 대한 모범 사례

Oracle RAC 가상 머신에는 특정 기능에 사용되는 여러 디스크가 있습니다.  공유 디스크는 Oracle RAC 클러스터에서 사용되는 모든 가상 머신에 탑재됩니다.  운영 체제 및 소프트웨어 설치 디스크는 개별 가상 머신에만 탑재됩니다.  

![Oracle RAC 가상 머신 디스크 개요](media/oracle-vm-disks-overview.png)

다음 예에서는 아래 표에 정의된 디스크를 사용합니다.

| 디스크                                      | 용도                                       | 공유 디스크 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 운영 체제 디스크                         | 아니요          |
| GRID                                      | Oracle Grid 소프트웨어의 설치 위치     | 아니요          |
| DATABASE                                  | Oracle 데이터베이스 소프트웨어의 설치 위치 | 아니요          |
| ORAHOME                                   | Oracle 데이터베이스 이진 파일의 기본 위치    | 아니요          |
| DATA1, DATA2, DATA3, DATA4                | Oracle 데이터베이스 파일이 저장되는 디스크   | Yes         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | 다시 실행 로그 디스크                                | Yes         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | 투표 디스크                                  | Yes         |
| FRA1, FRA2                                | 빠른 복구 영역 디스크                      | Yes         |

![Oracle 가상 머신 디스크 구성](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>가상 머신 구성

* 각 가상 머신은 4개의 SCSI 컨트롤러로 구성됩니다.
* SCSI 컨트롤러 유형이 VMware Paravirtual로 설정됩니다.
* 여러 가상 디스크(.vmdk)가 생성됩니다.
* 디스크는 서로 다른 SCSI 컨트롤러에 탑재됩니다.
* 공유 클러스터 디스크에 대해 다중 기록기 공유 유형이 설정됩니다.
* vSAN 스토리지 정책은 디스크의 고가용성을 보장하기 위해 정의됩니다.

### <a name="operating-system-and-software-disk-configuration"></a>운영 체제 및 소프트웨어 디스크 구성

각 Oracle 가상 머신은 호스트 운영 체제, 스왑, 소프트웨어 설치 및 기타 OS 기능을 위해 여러 개의 디스크로 구성됩니다.  이러한 디스크는 가상 머신 간에 공유되지 않습니다.  

* 각 가상 머신에 대해 3개의 디스크가 가상 디스크로 구성되어 Oracle RAC 가상 머신에 탑재됩니다.
    * OS 디스크
    * Oracle Grid 설치 파일을 저장하기 위한 디스크
    * Oracle 데이터베이스 설치 파일을 저장하기 위한 디스크
* 디스크는 **씬 프로비전됨** 으로 구성할 수 있습니다.
* 각 디스크는 첫 번째 SCSI 컨트롤러(SCSI0)에 탑재됩니다.  
* 공유는 **공유 안 함** 으로 설정됩니다.
* 중복도는 vSAN 정책을 사용하여 스토리지에 정의됩니다.  

![Oracle RAC OS 디스크 실제 구성을 보여 주는 다이어그램](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>데이터 디스크 구성

데이터 디스크는 주로 데이터베이스 파일을 저장하는 데 사용됩니다.  

* 4개의 디스크가 가상 디스크로 구성되며 모든 Oracle RAC 가상 머신에 탑재됩니다.
* 각 디스크는 서로 다른 SCSI 컨트롤러에 탑재됩니다.
* 각 가상 디스크는 **Thick Provision Eager Zeroed** 로 구성됩니다.  
* 공유는 **다중 기록기** 로 설정됩니다.  
* 디스크는 ASM(자동 스토리지 관리) 디스크 그룹으로 구성되어야 합니다.  
* 중복도는 vSAN 정책을 사용하여 스토리지에 정의됩니다.  
* ASM 중복도는 **외부** 중복도로 설정됩니다.

![Oracle RAC 데이터 디스크 그룹 구성](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>다시 실행 로그 디스크 구성

다시 실행 로그 파일은 데이터베이스의 변경 내용에 대한 복사본을 저장하는 데 사용됩니다.  로그 파일은 오류 발생 후 데이터를 복구해야 할 때 사용됩니다.

* 다시 실행 로그 디스크는 여러 디스크 그룹으로 구성되어야 합니다.  
* 6개의 디스크가 생성되어 모든 Oracle RAC 가상 머신에 탑재됩니다.
* 디스크는 서로 다른 SCSI 컨트롤러에 탑재됩니다.
* 각 가상 디스크는 **Thick Provision Eager Zeroed** 로 구성됩니다.
* 공유는 **다중 기록기** 로 설정됩니다.  
* 디스크는 두 개의 ASM 디스크 그룹으로 구성되어야 합니다.
* 각 ASM 디스크 그룹에는 서로 다른 SCSI 컨트롤러에 있는 세 개의 디스크가 포함되어 있습니다.  
* ASM 중복도는 **정상** 중복도로 설정됩니다.
* 두 ASM 다시 실행 로그 그룹 모두에서 5개의 다시 실행 로그 파일이 생성됩니다.

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC 다시 실행 로그 디스크 그룹 구성](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 투표 디스크 구성

투표 디스크는 스플릿 브레인(Split-Brain) 상황을 방지하기 위해 추가 통신 채널로 쿼럼 디스크 기능을 제공합니다.

* 5개의 디스크가 생성되어 모든 Oracle RAC 가상 머신에 탑재됩니다.
* 디스크는 하나의 SCSI 컨트롤러에 탑재됩니다.
* 각 가상 디스크는 **Thick Provision Eager Zeroed** 로 구성됩니다.
* 공유는 **다중 기록기** 로 설정됩니다.  
* 디스크는 하나의 ASM 디스크 그룹으로 구성되어야 합니다.  
* ASM 중복도는 **높음** 중복도로 설정됩니다.

![Oracle RAC 투표 디스크 그룹 구성](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 빠른 복구 영역 디스크 구성(선택 사항)

FRA(빠른 복구 영역)는 Oracle ASM 디스크 그룹에 의해 관리되는 파일 시스템입니다.  FRA는 백업 및 복구 파일을 위한 공유 스토리지 위치를 제공합니다. Oracle은 보관된 로그와 플래시백 로그를 빠른 복구 영역에 만듭니다. RMAN(Oracle Recovery Manager)은 필요에 따라 빠른 복구 영역에 백업 세트 및 이미지 복사본을 저장하고 미디어 복구 중에 파일을 복원할 때 이를 사용할 수 있습니다.

* 2개의 디스크가 생성되어 모든 Oracle RAC 가상 머신에 탑재됩니다.
* 디스크는 서로 다른 SCSI 컨트롤러에 탑재됩니다.
* 각 가상 디스크는 **Thick Provision Eager Zeroed** 로 구성됩니다.
* 공유는 **다중 기록기** 로 설정됩니다.  
* 디스크는 하나의 ASM 디스크 그룹으로 구성되어야 합니다.  
* ASM 중복도는 **외부** 중복도로 설정됩니다.

![Oracle RAC 투표 디스크 그룹 구성을 보여 주는 다이어그램](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple 프라이빗 클라우드 vSphere 클러스터 배포

프라이빗 클라우드에 vSphere 클러스터를 배포하려면 다음 프로세스를 수행합니다.

1. CloudSimple 포털에서 [프라이빗 클라우드를 만듭니다](create-private-cloud.md). CloudSimple은 새로 만든 프라이빗 클라우드에서 'cloudowner'라는 기본 vCenter 사용자를 만듭니다. 기본 프라이빗 클라우드 사용자 및 권한 모델에 대한 자세한 내용은 [프라이빗 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)를 참조하세요.  이 단계에서는 프라이빗 클라우드에 대한 주 관리 클러스터를 만듭니다.

2. CloudSimple 포털에서 새 클러스터를 사용하여 [프라이빗 클라우드를 확장](expand-private-cloud.md)합니다.  이 클러스터는 Oracle RAC를 배포하는 데 사용됩니다.  원하는 내결함성에 따라 노드 수를 선택합니다(3개 노드 이상).

## <a name="set-up-networking-for-oracle-rac"></a>Oracle RAC에 대한 네트워킹 설정

1. 프라이빗 클라우드에서 [두 개의 VLAN을 만듭니다](create-vlan-subnet.md)(Oracle 공용 네트워크용으로 하나, Oracle 프라이빗 네트워크용으로 하나). 그 다음, 적절한 서브넷 CIDR을 할당합니다.
2. VLAN을 만든 후 [프라이빗 클라우드 vCenter에서 분산 포트 그룹](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)을 만듭니다.
3. Oracle 환경을 위한 관리 클러스터에서 [DHCP 및 DNS 서버 가상 머신](dns-dhcp-setup.md)을 설정합니다.
4. 프라이빗 클라우드에 설치된 [DNS 서버에서 DNS 전달을 구성](on-premises-dns-setup.md#create-a-conditional-forwarder)합니다.

## <a name="set-up-vsan-storage-policies"></a>vSAN 스토리지 정책 설정

vSAN 정책은 VM 디스크에 저장된 데이터에 대해 허용되는 실패 및 디스크 스트라이프를 정의합니다.  생성된 스토리지 정책은 VM을 만드는 동안 VM 디스크에 적용해야 합니다.

1. 프라이빗 클라우드의 [vSphere 클라이언트에 로그인](./vcenter-access.md)합니다.
2. 상단 메뉴에서 **정책 및 프로필** 을 선택합니다.
3. 왼쪽 메뉴에서 **VM 스토리지 정책** 을 선택한 다음, **VM 스토리지 정책 만들기** 를 선택합니다.
4. 정책에 대한 의미 있는 이름을 입력하고 **다음** 을 클릭합니다.
5. **정책 구조** 섹션에서 **vSAN 스토리지에 대한 규칙 사용** 을 선택하고 **다음** 을 클릭합니다.
6. **vSAN** > **가용성** 섹션에서 사이트 재해 허용 범위에 대해 **없음** 을 선택합니다. 허용되는 실패에 대해 원하는 FTT의 **RAID - 미러링** 옵션을 선택합니다.
    ![vSAN 설정](media/oracle-rac-storage-wizard-vsan.png).
7. **고급** 섹션에서 개체당 디스크 스트라이프 수를 선택합니다. 개체 공간 예약에 대해 **Thick Provisioned** 를 선택합니다. **개체 체크섬 사용 안 함** 을 선택합니다. **다음** 을 클릭합니다.
8. 화면의 지시에 따라 호환되는 vSAN 데이터 저장소 목록을 확인하고 설정을 검토한 후 설정을 완료합니다.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle VM 만들기 및 Oracle용 공유 VM 디스크 만들기

Oracle용 VM을 만들려면 기존 VM을 복제하거나 새 VM을 만듭니다.  이 섹션에서는 새 VM을 만든 다음, 기본 운영 체제를 설치한 후 해당 VM을 복제하여 두 번째 VM을 만드는 방법을 설명합니다.  VM을 만든 후에는 해당 VM에 추가 디스크를 만들 수 있습니다.  Oracle 클러스터는 공유 디스크를 사용하여 데이터, 로그 및 다시 실행 로그를 저장합니다.

### <a name="create-vms"></a>VM 만들기

1. vCenter에서 **호스트 및 클러스터** 아이콘을 클릭합니다. Oracle용으로 만든 클러스터를 선택합니다.
2. 해당 클러스터를 마우스 오른쪽 단추로 클릭하고 **새 Virtual Machine** 을 선택합니다.
3. **새 가상 머신 만들기** 를 선택하고 **다음** 을 클릭합니다.
4. 컴퓨터에 이름을 지정하고, Oracle VM의 위치를 선택하고, **다음** 을 클릭합니다.
5. 클러스터 리소스를 선택하고 **다음** 을 클릭합니다.
6. 클러스터에 대한 vSAN 데이터 저장소를 선택하고 **다음** 을 클릭합니다.
7. 기본 ESXi 6.5 호환성 선택 항목을 유지하고 **다음** 을 클릭합니다.
8. 만들려는 VM에 대한 ISO의 게스트 OS를 선택하고 **다음** 을 클릭합니다.
9. OS를 설치하는 데 필요한 하드 디스크 크기를 선택합니다.
10. 다른 디바이스에 애플리케이션을 설치하려면 **새 디바이스 추가** 를 클릭합니다.
11. 네트워크 옵션을 선택하고 공용 네트워크용으로 만든 분산 포트 그룹을 할당합니다.
12. 다른 네트워크 인터페이스를 추가하려면 **새 디바이스 추가** 를 클릭하고 프라이빗 네트워크용으로 만든 분산 포트 그룹을 선택합니다.
13. 새 DC/DVD 드라이브의 경우 기본 운영 체제 설치에 대한 ISO가 포함된 데이터 저장소 ISO 파일을 선택합니다. 이전에 ISO 및 템플릿 폴더에 업로드한 파일을 선택하고 **확인** 을 클릭합니다.
14. 설정을 검토하고 **확인** 을 클릭하여 새 VM을 만듭니다.
15. VM의 전원을 켭니다. 운영 체제 및 필요한 업데이트를 설치합니다.

운영 체제가 설치된 후에는 두 번째 VM을 복제할 수 있습니다. VM 항목을 마우스 오른쪽 단추로 클릭하고 복제 옵션을 선택합니다.

### <a name="create-shared-disks-for-vms"></a>VM에 대한 공유 디스크 만들기

Oracle은 공유 디스크를 사용하여 데이터, 로그 및 다시 실행 로그 파일을 저장합니다.  vCenter에서 공유 디스크를 만들어 두 VM 모두에 탑재할 수 있습니다.  성능을 높이려면 다른 SCSI 컨트롤러에 데이터 디스크를 저장합니다. 아래 단계에서는 vCenter에서 공유 디스크를 만든 다음, 가상 머신에 연결하는 방법을 보여 줍니다. vCenter Flash 클라이언트는 VM 속성을 수정하는 데 사용됩니다.

#### <a name="create-disks-on-the-first-vm"></a>첫 번째 VM에 디스크 만들기

1. vCenter에서 Oracle VM 중 하나를 마우스 오른쪽 단추로 클릭하고 **설정 편집** 을 선택합니다.
2. 새 디바이스 섹션에서 **SCSI 컨트롤러** 를 선택하고 **추가** 를 클릭합니다.
3. 새 디바이스 섹션에서 **새 하드 디스크** 를 선택하고 **추가** 를 클릭합니다.
4. 새 하드 디스크의 속성을 확장합니다.
5. 하드 디스크의 크기를 지정합니다.
6. VM 스토리지 정책을 이전에 정의한 vSAN 스토리지 정책으로 지정합니다.
7. vSAN 데이터 저장소의 폴더로 위치를 선택합니다. 이 위치는 디스크를 탐색하고 두 번째 VM에 연결하는 데 도움이 됩니다.
8. 디스크 프로비전의 경우 **Thick Provision Eager Zeroed** 를 선택합니다.
9. 공유의 경우 **다중 기록기** 를 지정합니다.
10. 가상 디바이스 노드에 대해 2단계에서 만든 새 SCSI 컨트롤러를 선택합니다.

    ![첫 번째 VM에서 디스크를 만드는 데 필요한 필드가 강조 표시된 스크린샷](media/oracle-rac-new-hard-disk.png)

Oracle 데이터, 로그 및 다시 실행 로그 파일에 필요한 모든 새 디스크에 대해 2 ~ 10단계를 반복합니다.

#### <a name="attach-disks-to-second-vm"></a>두 번째 VM에 디스크 연결

1. vCenter에서 Oracle VM 중 하나를 마우스 오른쪽 단추로 클릭하고 **설정 편집** 을 선택합니다.
2. 새 디바이스 섹션에서 **SCSI 컨트롤러** 를 선택하고 **추가** 를 클릭합니다.
3. 새 디바이스 섹션에서 **기존 하드 디스크** 를 선택하고 **추가** 를 클릭합니다.
4. 첫 번째 VM에 대해 디스크가 생성된 위치로 이동하여 VMDK 파일을 선택합니다.
5. VM 스토리지 정책을 이전에 정의한 vSAN 스토리지 정책으로 지정합니다.
6. 디스크 프로비전의 경우 **Thick Provision Eager Zeroed** 를 선택합니다.
7. 공유의 경우 **다중 기록기** 를 지정합니다.
8. 가상 디바이스 노드에 대해 2단계에서 만든 새 SCSI 컨트롤러를 선택합니다.

    ![첫 번째 VM에 디스크 만들기](media/oracle-rac-existing-hard-disk.png)

Oracle 데이터, 로그 및 다시 실행 로그 파일에 필요한 모든 새 디스크에 대해 2 ~ 7단계를 반복합니다.

## <a name="set-up-vm-host-affinity-rules"></a>VM 호스트 선호도 규칙 설정

VM-호스트 선호도 규칙은 VM이 원하는 호스트에서 실행되도록 합니다.  Oracle VM이 적절한 리소스로 호스트에서 실행되고 특정 라이선스 요구 사항을 충족하도록 vCenter에 대한 규칙을 정의할 수 있습니다.

1. CloudSimple 포털에서 cloudowner 사용자의 [권한을 에스컬레이션](escalate-private-cloud-privileges.md)합니다.
2. 프라이빗 클라우드의 vSphere 클라이언트에 로그인합니다.
3. vSphere 클라이언트에서 Oracle VM이 배포되는 클러스터를 선택하고 **구성** 을 클릭합니다.
4. 구성 아래에서 **VM/호스트 그룹** 을 선택합니다.
5. 클릭 **+** 합니다.
6. VM 그룹을 추가합니다. 유형으로 **VM 그룹** 을 선택합니다. 그룹의 이름을 입력합니다. VM을 선택하고 **확인** 을 클릭하여 그룹을 만듭니다.
6. 호스트 그룹을 만듭니다. 유형으로 **호스트 그룹** 을 선택합니다. 그룹의 이름을 입력합니다. VM이 실행될 호스트를 선택하고 **확인** 을 클릭하여 그룹을 만듭니다.
7. 규칙을 만들려면 **VM/호스트 규칙** 을 클릭합니다.
8. 클릭 **+** 합니다.
9. 규칙의 이름을 입력하고 **사용** 을 선택합니다.
10. 규칙 유형에 대해 **호스트할 Virtual Machines** 를 선택합니다.
11. Oracle VM을 포함하는 VM 그룹을 선택합니다.
12. **이 그룹의 호스트에서 실행해야 함** 을 선택합니다.
13. 앞에서 만든 호스트 그룹을 선택합니다.
14. **확인** 을 클릭하여 규칙을 만듭니다.

## <a name="references"></a>참조

* [vSAN 정책 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
