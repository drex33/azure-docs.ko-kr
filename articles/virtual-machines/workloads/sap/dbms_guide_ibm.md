---
title: SAP 워크로드용 IBM DB2 Azure Virtual Machines DBMS 배포 | Microsoft Docs
description: SAP 워크로드용 IBM DB2 Azure Virtual Machines DBMS 배포
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, Db2, SAP, IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, ignite-fall-2021
ms.openlocfilehash: 8740e2969030c331ffd5b45fcd88b73975ef3e63
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013083"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드용 IBM DB2 Azure Virtual Machines DBMS 배포

Microsoft Azure를 사용하면 Linux, UNIX, Windows(LUW)용 IBM DB2에서 실행 중인 기존 SAP 애플리케이션을 Azure 가상 머신으로 마이그레이션할 수 있습니다. LUW용 IBM DB2에서 SAP를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.
LUW용 IBM DB2에서 SAP Business Suite를 실행하는 방법에 대한 일반적인 내용은 SCN(SAP Community Network)의 <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>에서 찾을 수 있습니다.

Azure의 LUW용 DB2 SAP에 대한 추가 정보 및 업데이트는 SAP Note [2233094]를 참조하세요. 

릴리스된 Azure의 SAP 워크로드에 대해 다양한 문서가 있습니다.  [Azure의 SAP 워크로드 - 시작](./get-started.md)에서 시작한 다음, 원하는 영역을 선택하는 것이 좋습니다.

이 문서에서 다루는 영역에 대해 Azure의 SAP와 관련된 SAP Note는 다음과 같습니다.

| Note 번호 |제목 |
| --- |--- |
| [1928533] |Azure의 SAP 애플리케이션: 지원되는 제품 및 Azure VM 유형 |
| [2015553] |Microsoft Azure의 SAP: 필수 구성 요소 지원 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] |Windows에서의 가상화: 고급 모니터링 |
| [2191498] |Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2233094] |DB6: Linux, UNIX 및 Windows용 IBM DB2를 사용하는 Azure의 SAP 애플리케이션 - 추가 정보 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 사항 |
| [2002167] |Red Hat Enterprise Linux 7.x: 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |

이 문서를 시작하기 전에 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](./get-started.md)에 나오는 다른 가이드를 참조해야 합니다. 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Linux, UNIX 및 Windows용 IBM DB2 버전 지원
Microsoft Azure Virtual Machine 서비스에서 LUW용 IBM DB2의 SAP는 DB2 버전 10.5부터 지원됩니다.

지원되는 SAP 제품 및 Azure VM 형식에 대한 내용은 SAP Note [1928533]을 참조하세요.

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM의 SAP 설치에 대한 Linux, UNIX 및 Windows용 IBM DB2 구성 지침
### <a name="storage-configuration"></a>스토리지 구성
SAP 워크로드에 대한 Azure Storage 유형의 개요는 [SAP 워크로드에 대한 Azure Storage 유형](./planning-guide-storage.md) 문서를 참조하세요. 모든 데이터베이스 파일은 Azure 블록 스토리지의 탑재된 디스크(Windows: NTFS, Linux: xfs 또는 ext3)에 저장되어야 합니다. 나열된 시나리오에서 Azure 서비스와 같은 원격 공유 볼륨은 Db2 데이터베이스 파일에 대해 지원되지 **않습니다**. 

* 모든 게스트 OS용 [Microsoft Azure File 서비스](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* Windows 게스트 OS에서 실행되는 Db2용 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). 

나열된 시나리오에서 Azure 서비스와 같은 원격 공유 볼륨은 Db2 데이터베이스 파일에 대해 지원됩니다. 
 
* Azure NetApp Files에서 호스트되는 NFS 공유에서 Linux 게스트 OS 기반 Db2 데이터 및 로그 파일 호스트가 지원됩니다.

Azure 페이지 Blob Storage 또는 Managed Disks를 기반으로 하는 디스크를 사용하는 경우, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 제공된 설명이 IBM DB2 DBMS와 함께 배포하는 경우에도 적용됩니다.

이 문서 앞부분의 일반 정보에서 설명했듯이 Azure 디스크에 대한 IOPS 처리량에 할당량이 존재합니다. 정확한 할당량은 사용되는 VM 유형에 따라 달라집니다. 할당량을 포함하는 VM 유형 목록은 [여기(Linux)][virtual-machines-sizes-linux] 및 [여기(Windows)][virtual-machines-sizes-windows]에서 찾을 수 있습니다.

디스크당 현재 IOPS 할당량이 충분한 경우 탑재된 단일 디스크에 모든 데이터베이스 파일을 저장할 수 있습니다. 그러나 항상 데이터 파일 및 트랜잭션 로그 파일을 서로 다른 디스크/VHD로 분리해야 합니다.

SAP 설치 가이드의 ‘데이터베이스 디렉터리의 데이터 보안 및 성능 고려 사항’ 장에서도 성능 고려 사항을 참조할 수 있습니다.

또는 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 설명한 대로 Window 스토리지 풀(Windows Server 2012 이상에서만 사용 가능)이나 Linux의 LVM 또는 mdadm을 사용하여 여러 디스크에 하나의 큰 논리적 디바이스를 만들 수 있습니다.

<!-- log_dir, sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

Azure M-Series VM의 경우, Azure Write Accelerator를 사용하면 Azure Premium Storage 성능과 비교하여 요소에 의해 트랜잭션 로그에 대한 기록 대기 시간을 줄일 수 있습니다. 따라서 DB2 트랜잭션 로그의 볼륨을 구성하는 VHD용 Azure Write Accelerator를 배포해야 합니다. 자세한 내용은 [Write Accelerator](../../how-to-enable-write-accelerator.md) 문서에서 참조할 수 있습니다.

IBM Db2 LUW 11.5는 4KB 섹터 크기에 대한 지원을 릴리스했습니다. 이전 Db2 버전의 경우 512바이트 섹터 크기를 사용해야 합니다. 프리미엄 SSD 디스크는 기본 4KB이며 512바이트 에뮬레이션을 포함합니다. 울트라 디스크는 기본적으로 4KB 섹터 크기를 사용합니다. 울트라 디스크를 만드는 동안 512바이트 섹터 크기를 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure 울트라 디스크 사용](../../disks-enable-ultra-ssd.md#deploy-an-ultra-disk---512-byte-sector-size)에서 확인할 수 있습니다. 이 512바이트 섹터 크기는 IBM Db2 LUW 11.5 이전 버전의 필수 구성 요소입니다.

`log_dir`, `sapdata` 및 `saptmp` 디렉터리의 Db2 스토리지 경로에 스토리지 풀을 사용하는 Windows에서는 512바이트의 물리적 디스크 섹터 크기를 지정해야 합니다. Windows 스토리지 풀을 사용하는 경우 `-LogicalSectorSizeDefault` 매개 변수를 사용하여 명령줄 인터페이스를 통해 수동으로 스토리지 풀을 만들어야 합니다. 자세한 내용은 <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>을 참조하세요.


## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>IBM Db2 배포를 위한 VM 및 디스크 구조에 대한 권장 사항

SAP NetWeaver 애플리케이션용 IBM Db2는 SAP 지원 정보 [1928533]에 나열된 모든 VM 유형에서 지원됩니다.  대규모의 다중 테라바이트 데이터베이스에서 IBM Db2 데이터베이스를 실행하는 데 권장되는 VM 패밀리는 Esd_v4/Eas_v4/Es_v3 및 M/M_v2 시리즈입니다. M 시리즈 쓰기 가속기를 사용하여 IBM Db2 트랜잭션 로그 디스크 쓰기 성능을 향상시킬 수 있습니다. 

다음은 소규모에서 대규모에 이르는 Db2 배포에서 SAP의 다양한 크기 및 용도에 대한 기본 구성입니다. 목록은 Azure Premium Storage를 기반으로 합니다. 그러나 Azure 울트라 디스크는 Db2에서도 완전히 지원되며 사용할 수도 있습니다. 용량, 버스트 처리량 및 버스트 IOPS에 대한 값을 사용하여 울트라 디스크 구성을 정의합니다. 5000 IOPS 정도에서 /db2/```<SID>```/log_dir에 대한 IOPS를 제한할 수 있습니다. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>초소규모 SAP 시스템: 데이터베이스 크기 50~200GB: 예제 솔루션 관리자
| VM 이름/크기 |Db2 탑재 지점 |Azure Premium Disk |디스크의 NR |IOPS |처리량[MB/s] |크기[GB] |버스트 IOPS |버스트 처리량[GB] | 스트라이프 크기 | 캐싱 |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  ||  |
|vCPU: 4 |/db2/```<SID>```/sapdata |P10 |2 |1,000  |200  |256  |7,000  |340  |256KB |ReadOnly |
|RAM: 32GiB |/db2/```<SID>```/saptmp |P6 |1 |240  |50  |128  |3,500  |170  | ||
| |/db2/```<SID>```/log_dir |P6 |2 |480  |100  |128  |7,000  |340  |64KB ||
| |/db2/```<SID>```/offline_log_dir |P10 |1 |500  |100  |128  |3,500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>소규모 SAP 시스템: 데이터베이스 크기 200~750GB: 소규모 Business Suite
| VM 이름/크기 |Db2 탑재 지점 |Azure Premium Disk |디스크의 NR |IOPS |처리량[MB/s] |크기[GB] |버스트 IOPS |버스트 처리량[GB] | 스트라이프 크기 | 캐싱 |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU: 16 |/db2/```<SID>```/sapdata |P15 |4 |4,400  |500  |1.024  |14,000  |680  |256KB |ReadOnly |
|RAM: 128GiB |/db2/```<SID>```/saptmp |P6 |2 |480  |100  |128  |7,000  |340  |128KB ||
| |/db2/```<SID>```/log_dir |P15 |2 |2,200  |250  |512  |7,000  |340  |64KB ||
| |/db2/```<SID>```/offline_log_dir |P10 |1 |500  |100  |128  |3,500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>중간 규모 SAP 시스템: 데이터베이스 크기 500~1,000GB: 소규모 Business Suite
| VM 이름/크기 |Db2 탑재 지점 |Azure Premium Disk |디스크의 NR |IOPS |처리량[MB/s] |크기[GB] |버스트 IOPS |버스트 처리량[GB] | 스트라이프 크기 | 캐싱 |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU: 32 |/db2/```<SID>```/sapdata |P30 |2 |10000  |400  |2.048  |10000  |400  |256KB |ReadOnly |
|RAM: 256GiB |/db2/```<SID>```/saptmp |P10 |2 |1,000  |200  |256  |7,000  |340  |128KB ||
| |/db2/```<SID>```/log_dir |P20 |2 |4,600  |300  |1.024  |7,000  |340  |64KB ||
| |/db2/```<SID>```/offline_log_dir |P15 |1 |1,100  |125  |256  |3,500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>대규모 SAP 시스템: 데이터베이스 크기 750~2,000GB: Business Suite
| VM 이름/크기 |Db2 탑재 지점 |Azure Premium Disk |디스크의 NR |IOPS |처리량[MB/s] |크기[GB] |버스트 IOPS |버스트 처리량[GB] | 스트라이프 크기 | 캐싱 |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU: 64 |/db2/```<SID>```/sapdata |P30 |4 |20,000  |800  |4.096  |20,000  |800  |256KB |ReadOnly |
|RAM: 504GiB |/db2/```<SID>```/saptmp |P15 |2 |2,200  |250  |512  |7,000  |340  |128KB ||
| |/db2/```<SID>```/log_dir |P20 |4 |9,200  |600  |2.048  |14,000  |680  |64KB ||
| |/db2/```<SID>```/offline_log_dir |P20 |1 |2,300  |150  |512  |3,500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>대규모 다중 테라바이트 SAP 시스템: 데이터베이스 크기 2TB+: 글로벌 Business Suite 시스템
| VM 이름/크기 |Db2 탑재 지점 |Azure Premium Disk |디스크의 NR |IOPS |처리량[MB/s] |크기[GB] |버스트 IOPS |버스트 처리량[GB] | 스트라이프 크기 | 캐싱 |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3,500  |170  || |
|vCPU: 128 |/db2/```<SID>```/sapdata |P40 |4 |30,000  |1.000  |8.192  |30,000  |1.000  |256KB |ReadOnly |
|RAM: 2048GiB |/db2/```<SID>```/saptmp |P20 |2 |4,600  |300  |1.024  |7,000  |340  |128KB ||
| |/db2/```<SID>```/log_dir |P30 |4 |20,000  |800  |4.096  |20,000  |800  |64KB |WriteAccelerator |
| |/db2/```<SID>```/offline_log_dir |P30 |1 |5,000  |200  |1.024  |5,000  |200  || |


### <a name="using-azure-netapp-files"></a>Azure NetApp Files 사용
Azure NetApp Files(AND)를 기반으로 하는 NFS v4.1 볼륨의 사용은 Suse 또는 Red Hat Linux 게스트 OS에서 호스팅되는 IBM Db2에서 지원됩니다. 다음과 같이 나열된 최소 4개의 서로 다른 볼륨을 만들어야 합니다.

- saptmp1, sapmnt, usr_sap, ```<sid>```_home, db2```<sid>```_home, db2_software의 공유 볼륨
- sapdata1에서 sapdatan까지 하나의 데이터 볼륨
- 다시 실행 로그 디렉터리를 위한 하나의 로그 볼륨
- 로그 보관 및 백업을 위한 하나의 볼륨

다섯 번째 잠재적 볼륨은 Azure Blob 저장소에서 스냅샷을 만들고 저장하는 데 사용하는 보다 장기적인 백업에 사용하는 ANF 볼륨일 수 있습니다.

구성은 여기에 표시된 것처럼 보일 수 있습니다.

![ANF를 사용한 Db2 구성의 예](./media/dbms_guide_ibm/anf-configuration-example.png)


ANF 호스팅 볼륨의 성능 계층과 크기는 성능 요구 사항에 따라 선택해야 합니다. 그러나 데이터 및 로그 볼륨에 대해 Ultra 성능 수준을 사용하는 것이 좋습니다. 데이터 및 로그 볼륨에 대해 블록 스토리지와 공유 스토리지 유형을 혼합하는 것은 지원되지 않습니다.

탑재 옵션에서 해당 볼륨을 탑재하면 다음과 같이 보일 수 있습니다(```<SID>``` 및 ```<sid>```를 SAP 시스템의 SID로 바꿔야 함).

```
vi /etc/idmapd.conf   
 # Example
 [General]
 Domain = defaultv4iddomain.com
 [Mapping]
 Nobody-User = nobody
 Nobody-Group = nobody

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2shared /mnt 
mkdir -p /db2/Software /db2/AN1/saptmp /usr/sap/<SID> /sapmnt/<SID> /home/<sid>adm /db2/db2<sid> /db2/<SID>/db2_software
mkdir -p /mnt/Software /mnt/saptmp  /mnt/usr_sap /mnt/sapmnt /mnt/<sid>_home /mnt/db2_software /mnt/db2<sid>
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2data /mnt
mkdir -p /db2/AN1/sapdata/sapdata1 /db2/AN1/sapdata/sapdata2 /db2/AN1/sapdata/sapdata3 /db2/AN1/sapdata/sapdata4
mkdir -p /mnt/sapdata1 /mnt/sapdata2 /mnt/sapdata3 /mnt/sapdata4
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2log /mnt 
mkdir /db2/AN1/log_dir
mkdir /mnt/log_dir
umount /mnt

mount -t nfs -o rw,hard,sync,rsize=1048576,wsize=1048576,sec=sys,vers=4.1,tcp 172.17.10.4:/db2backup /mnt
mkdir /db2/AN1/backup
mkdir /mnt/backup
mkdir /db2/AN1/offline_log_dir /db2/AN1/db2dump
mkdir /mnt/offline_log_dir /mnt/db2dump
umount /mnt
```

>[!NOTE]
> 탑재 옵션 하드 및 동기화가 필요합니다.


### <a name="backuprestore"></a>Backup/복원
LUW용 IBM DB2의 백업/복원 기능은 표준 Windows Server 운영 체제 및 Hyper-V와 동일한 방법으로 지원됩니다.

유효한 데이터베이스 백업 전략이 있는지 확인합니다. 

완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 또한 백업 압축에서 사용하는 CPU 사용량은 최대 8개의 CPU 스레드가 있는 VM에서 중요한 역할을 수행할 수 있습니다. 따라서 다음을 가정할 수 있습니다.

* 데이터베이스 디바이스를 저장하는 데 사용하는 디스크 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(스트라이프 디렉터리, 디스크) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 필요에 따라 사용/혼합할 수 있습니다.

* 해당 스트라이프 볼륨에 대한 IOPS 처리량을 개선하려면 여러 디스크에 백업 대상 볼륨을 스트라이프합니다.
* 둘 이상의 대상 디렉터리를 사용하여 백업을 작성합니다.

>[!NOTE]
>Windows의 DB2는 Windows VSS 기술을 지원하지 않습니다. 따라서 DB2 DBMS가 배포된 VM에는 Azure Backup 서비스의 애플리케이션 일치 VM 백업을 사용할 수 없습니다.

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

#### <a name="linux-pacemaker"></a>Linux Pacemaker

Pacemaker를 사용하는 DB2 HADR(고가용성 재해 복구)을 지원합니다. SLES 및 RHEL 운영 체제 모두 지원됩니다. 이 구성은 SAP용 IBM Db2의 고가용성을 보장합니다. 배포 가이드:
* SLES: [Pacemaker를 사용하는 SUSE Linux Enterprise Server의 Azure VMs에 배포된 IBM Db2 LUW의 고가용성](dbms-guide-ha-ibm.md) 
* RHEL: [Red Hat Enterprise Linux Server의 Azure VMs에서 IBM DB2 LUW의 고가용성](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Windows 클러스터 서버

MSCS(Microsoft Cluster Server)는 지원되지 않습니다.

DB2 HADR(고가용성 재해 복구)은 지원됩니다. HA 구성의 가상 머신에 이름 확인 작업이 있는 경우 Azure 설정이 온-프레미스의 설정과 다르지 않습니다. IP 확인만 사용하는 것은 권장되지 않습니다.

데이터베이스 디스크를 저장하는 스토리지 계정에는 지역에서 복제를 사용하지 마세요. 자세한 내용은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서를 참조하세요. 

### <a name="accelerated-networking"></a>가속 네트워킹
Windows의 DB2 배포에서는 [Azure 가속화된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 문서에 설명된 대로 가속화된 네트워킹의 Azure 기능을 사용하는 것이 좋습니다. 또한 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)도 고려하세요. 


### <a name="specifics-for-linux-deployments"></a>Linux 배포에 대한 세부 정보
디스크당 현재 IOPS 할당량이 충분한 경우 하나의 단일 디스크에 모든 데이터베이스 파일을 저장할 수 있습니다. 그러나 항상 데이터 파일 및 트랜잭션 로그 파일을 서로 다른 디스크로 분리해야 합니다.

단일 Azure VHD의 IOPS 또는 I/O 처리량이 충분하지 않은 경우, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서에서 설명한 대로 LVM(논리적 볼륨 관리자) 또는 MDADM을 사용하여 여러 디스크에 하나의 큰 논리적 디바이스를 만들 수 있습니다.
sapdata 및 saptmp 디렉터리에 대한 DB2 스토리지 경로를 포함하는 디스크의 경우 물리적 디스크 섹터 크기를 512KB로 지정해야 합니다.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>기타
Azure 가용성 집합 또는 SAP 모니터링과 같은 다른 일반적인 영역은 모두 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서에 설명한 대로 IBM Database가 있는 VM 배포에 적용됩니다.

## <a name="next-steps"></a>다음 단계
기사 읽기 

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)


[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]: /powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
