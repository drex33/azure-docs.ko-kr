---
title: Microsoft Azure Backup Server의 새로운 기능
description: Microsoft Azure Backup Server에서는 VM, 파일 및 폴더, 워크로드 등을 보호하기 위한 향상된 백업 기능을 제공합니다.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 8f7c0e8709cd899b9b6a285dcd8c556763869c65
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528119"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>MABS(Microsoft Azure Backup Server)의 새로운 기능

## <a name="whats-new-in-mabs-v3-ur2"></a>MABS v3 UR2의 새로운 기능

MABS(Microsoft Azure Backup Server) 버전 3 UR2는 다음과 같은 새로운 기능/기능 업데이트를 지원합니다.

UR2 문제 수정 및 설치 지침에 대한 자세한 내용은 [KB 문서](https://support.microsoft.com/topic/update-rollup-2-for-microsoft-azure-backup-server-v3-350de164-0ae4-459a-8acf-7777dbb7fd73)를 참조하세요.

### <a name="support-for-azure-stack-hci"></a>Azure Stack HCI 지원

MABS v3 UR2를 사용하면 Azure Stack HCI에서 가상 머신을 백업할 수 있습니다. [자세히 알아보기](./back-up-azure-stack-hyperconverged-infrastructure-virtual-machines.md).

### <a name="support-for-vmware-70"></a>VMware 7.0 지원

MABS v3 UR2를 사용하면 VMware 7.0 VM을 백업할 수 있습니다. [자세히 알아보기](./backup-azure-backup-server-vmware.md).

### <a name="support-for-sql-server-failover-cluster-instance-fci-using-cluster-shared-volume-csv"></a>CSV(클러스터 공유 볼륨)를 사용하여 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스) 지원

MABS v3 UR2는 CSV(클러스터 공유 볼륨)를 사용하여 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)를 지원합니다. CSV를 사용하면 SQL Server 인스턴스의 관리가 간단해집니다. 이를 통해 디스크를 소유하는 노드가 추상화되어 있으므로 모든 노드에서 기본 스토리지를 관리할 수 있습니다. [자세히 알아보기](./backup-azure-sql-mabs.md).

### <a name="optimized-volume-migration"></a>최적화된 볼륨 마이그레이션

MABS v3 UR2는 최적화된 볼륨 마이그레이션을 지원합니다. 볼륨 마이그레이션이 최적화되면 데이터 원본을 새 볼륨으로 훨씬 더 빠르게 이동할 수 있습니다. 고급 마이그레이션 프로세스는 활성 백업 복사본(활성 복제본)만 새 볼륨으로 마이그레이션합니다. 새 볼륨에 모든 새 복구 지점이 생성되며, 기존 복구 지점은 기존 볼륨에서 유지 관리되고 보존 정책에 따라 제거됩니다. [자세히 알아보기](/system-center/dpm/volume-to-volume-migration?view=sc-dpm-2019&preserve-view=true).

### <a name="offline-backup-using-azure-data-box"></a>Azure Data Box를 사용하여 오프라인 백업

MABS v3 UR2는 Azure Data Box를 사용하여 오프라인 백업을 지원합니다. Microsoft Azure Data Box 통합을 사용하면 테라바이트 크기의 백업 데이터를 온-프레미스에서 Azure Storage로 쉽게 이동할 수 있습니다. Azure Data Box는 더욱 편리하게 사용자 고유의 Azure 호환 디스크 및 커넥터를 확보하거나 임시 스토리지를 스테이징 위치로 프로비저닝할 수 있도록 지원합니다. 또한 Microsoft가 엔드투엔드 전송 물류를 처리하므로 Azure Portal을 통해 추적할 수 있습니다. [자세히 알아보기](./offline-backup-azure-data-box-dpm-mabs.md).

## <a name="whats-new-in-mabs-v3-ur1"></a>MABS V3 UR1의 새로운 기능

MABS(Microsoft Azure Backup Server) 버전 3 UR1은 최신 업데이트이며 중요한 버그 수정과 기타 기능 및 향상 기능을 포함합니다. 수정된 버그 목록 및 MABS V3 UR1의 설치 지침을 보려면 KB 문서 [4534062](https://support.microsoft.com/help/4534062)를 참조하세요.

>[!NOTE]
>32비트 보호 에이전트 지원은 MABS v3 UR1에서 중단되었습니다. [32비트 보호 에이전트 사용 중단](#32-bit-protection-agent-deprecation)을 참조하세요.

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>SSD를 사용하는 계층형 스토리지로 빠른 백업

MABS V2에서는 MBS([Modern Backup Storage](backup-mabs-add-storage.md))가 도입되어 스토리지 사용률 및 성능을 개선했습니다. MBS는 ReFS를 기본 파일 시스템으로 사용하며 계층형 스토리지와 같은 하이브리드 스토리지를 사용하도록 설계되었습니다.

MBS를 통한 규모와 성능을 달성하려면 MABS V3 UR1이 있는 플래시 스토리지(SSD)의 작은 비율(전체 스토리지의 4%)을 DPM HDD 스토리지와 결합하여 계층화된 볼륨으로 사용하는 것이 좋습니다. 계층화된 스토리지와 함께 MABS V3 UR1을 사용하면 백업 속도가 50~70% 더 빨라집니다. 계층화된 스토리지를 구성하는 단계는 DPM 문서 [계층화된 스토리지를 사용하여 MBS 설정](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)을 참조하세요.

### <a name="support-for-refs-volumes"></a>ReFS 볼륨에 대한 지원

MABS V3 UR1을 사용하면 ReFS 볼륨 및 ReFS 볼륨에 배포된 워크로드를 백업할 수 있습니다. ReFS 볼륨에 배포된 다음 워크로드를 백업할 수 있습니다.

* 운영 체제(64비트) : Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: 최신 SP가 있는 SharePoint 2019, 2016.

>[!NOTE]
> ReFS 볼륨에 저장된 Hyper-V VM의 백업은 MABS V3에서 지원됩니다.

>[중요] 중복 제거된 ReFS 볼륨의 백업과 관련해서 몇 가지 이슈를 발견했습니다. 해당 이슈를 해결하기 위해 노력하고 있으며 해결되는 즉시 이 섹션을 업데이트할 예정입니다. 그때까지 중복 제거된 ReFS 볼륨의 백업 지원이 MABSv3 UR1에서 제거됩니다.

### <a name="azure-vmware-solution-protection-support"></a>Azure VMware Solution 보호 지원

이제 MABS v3 UR1을 사용하여 [Azure VMware Solution](../azure-vmware/index.yml)에 배포된 가상 머신을 보호할 수 있습니다.

### <a name="vmware-parallel-backups"></a>VMware 병렬 백업

MABS V3 UR1을 사용하면 단일 보호 그룹 내의 모든 VMware VM 백업이 병렬 처리되므로 VM 백업 속도가 25% 더 빨라집니다.
이전 버전의 MABS에서는 보호 그룹 간에만 병렬 백업이 수행되었습니다. MABS V3 UR1에서는 VMware 델타 복제 작업이 병렬로 실행됩니다. 병렬로 실행할 작업 수는 기본적으로 8로 설정되어 있습니다. [VMware 병렬 백업](backup-azure-backup-server-vmware.md#vmware-parallel-backups)에 대해 자세히 알아보세요.

### <a name="disk-exclusion-for-vmware-vm-backup"></a>VMware VM 백업에 대한 디스크 제외

MABS V3 UR1을 사용하면 VMware VM 백업에서 특정 디스크를 제외할 수 있습니다. [VMware VM 백업에서 디스크를 제외](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup)하는 방법을 자세히 알아보세요.  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>온라인 백업을 삭제하기 위한 추가 인증 계층 지원

MABS V3 UR1에서는 중요한 작업에 대해 추가 인증 계층이 추가됩니다. **데이터 삭제를 통해 보호 중지** 작업을 수행하는 경우 보안 PIN을 입력하라는 메시지가 표시됩니다.

### <a name="offline-backup-improvements"></a>오프라인 백업 개선 사항

MABS v3 UR1에서는 Azure Import/Export 서비스를 사용하여 오프라인 백업 환경을 개선합니다. 자세한 내용은 [여기](./backup-azure-backup-server-import-export.md)에서 업데이트된 단계를 참조하세요.

>[!NOTE]
>MABS v3 UR2부터 MABS는 Azure Data Box를 사용하여 오프라인 백업을 수행할 수 있습니다. [자세히 알아보기](./offline-backup-azure-data-box-dpm-mabs.md).

### <a name="new-cmdlet-parameter"></a>새 cmdlet 매개 변수

MABS V3 UR1에는 새 매개 변수 **[-CheckReplicaFragmentation]** 이 있습니다. 새 매개 변수는 복제본의 조각화 비율을 계산하며 **Copy-DPMDatasourceReplica** cmdlet에 포함됩니다.

### <a name="32-bit-protection-agent-deprecation"></a>32비트 보호 에이전트 사용 중단

MABS v3 UR1에서는 32비트 보호 에이전트가 더 이상 지원되지 않습니다. MABS v3 서버를 UR1로 업그레이드한 후에는 32비트 워크로드를 보호할 수 없습니다. 기존의 모든 32비트 보호 에이전트는 사용 안 함 상태가 되며 예약된 백업은 실패하고 **에이전트를 사용할 수 없습니다.** 오류가 표시됩니다. 해당 에이전트의 백업 데이터를 보존하려는 경우 데이터 보존 옵션을 사용하여 보호를 중지할 수 있습니다. 그렇지 않으면 보호 에이전트를 제거할 수 있습니다.

>[!NOTE]
>MABS UR1에서 보호할 수 있는 워크로드를 알아보려면 [업데이트된 보호 매트릭스](./backup-mabs-protection-matrix.md)를 참조하세요.

## <a name="whats-new-in-mabs-v3-rtm"></a>MABS V3 RTM의 새로운 기능

MABS V3(Microsoft Azure Backup Server 버전 3)에는 중요한 버그 수정, Windows Server 2019 지원, SQL 2017 지원, 기타 기능 및 향상 기능이 포함되어 있습니다. 수정된 버그 목록 및 MABS V3에 대한 설치 지침을 보려면 KB 문서 [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)를 참조하세요.

다음 기능이 MABS V3에 포함됩니다.

### <a name="volume-to-volume-migration"></a>볼륨 간 마이그레이션

MABS V2에서 MBS(Modern Backup Storage)를 사용하여 워크로드 인식 스토리지를 발표했습니다. 여기에서 스토리지 속성에 따라 특정 스토리지로 백업할 특정 워크로드를 구성합니다. 그러나 구성 후 최적화된 리소스 사용률을 위해 특정 데이터 원본의 백업을 다른 스토리지로 이동해야 함을 알 수 있습니다. MABS V3은 [3단계](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)로 백업을 마이그레이션하고 다른 볼륨에 저장되도록 구성하는 기능을 제공합니다.

### <a name="prevent-unexpected-data-loss"></a>예기치 않은 데이터 손실 방지

기업에서 MABS는 관리자 팀에서 관리됩니다. 백업에 사용해야 하는 스토리지에 대한 지침이 있지만 백업 스토리지로 MABS에 지정된 올바르지 않은 볼륨은 중요한 데이터 손실을 발생시킬 수 있습니다. MABS V3에서는 [이 PowerShell cmdlet](./backup-mabs-add-storage.md)을 통해 해당 볼륨을 스토리지에 사용할 수 없는 볼륨으로 구성하여 관련 시나리오를 방지할 수 있습니다.

### <a name="custom-size-allocation"></a>사용자 지정 크기 할당

MBS(Modern Backup Storage)는 필요한 경우 스토리지를 얇게 소비합니다. 이렇게 하기 위해 MABS는 보호를 위해 구성된 경우 백업되는 데이터의 크기를 계산합니다. 그러나 파일 서버의 경우와 같이 많은 파일 및 폴더가 함께 백업되는 경우 크기 계산에 시간이 오래 걸릴 수 있습니다. MABS V3을 사용하면 각 파일의 크기를 계산하는 대신 기본값으로 볼륨 크기를 적용하도록 MABS를 구성하여 시간을 절약할 수 있습니다.

### <a name="optimized-cc-for-rct-vms"></a>RCT VM에 최적화된 참조

MABS는 VM이 충돌할 때 시간이 오래 걸리는 일관성 검사에 대한 필요성을 줄이는 RCT(Hyper-V의 기본 변경 내용 추적)를 사용합니다. RCT는 VSS 스냅샷 기반 백업에서 제공된 변경 내용 추적보다 더 나은 복원력을 제공합니다. MABS V3은 모든 일관성 검사 중 변경된 데이터만을 전송하여 네트워크 및 스토리지 사용을 더욱 최적화합니다.

### <a name="support-to-tls-12"></a>TLS 1.2에 대한 지원

TLS 1.2는 최상의 암호화를 사용하여 Microsoft에서 제안하는 통신의 보안 방법입니다. MABS는 이제 인증서 기반 인증 및 클라우드 백업을 위해 MABS와 보호된 서버 사이의 TLS 1.2 통신을 지원합니다.

### <a name="vmware-vm-protection-support"></a>VMware VM 보호 지원

VMware VM 백업은 이제 프로덕션 배포에 지원됩니다. MABS V3은 VMware VM 보호를 위해 다음을 제공합니다.

* 5.5 및 6.0에 대한 지원과 함께 vCenter 및 ESXi 6.5에 대한 지원
* 클라우드로 VMware VM의 자동 보호 새 VMware VM이 보호된 폴더에 추가되는 경우 디스크 및 클라우드로 자동으로 보호됩니다.
* VMware 대체 위치 복구를 위한 복구 효율성 개선

### <a name="sql-2017-support"></a>SQL 2017 지원

MABS 데이터베이스로 SQL 2017과 함께 MABS V3을 설치할 수 있습니다. SQL 2016에서 SQL 2017로 SQL 서버를 업그레이드하거나 새로 설치할 수 있습니다. MABS V3을 사용하여 클러스터형 및 비클러스터형 환경에서 모두 SQL 2017 워크로드를 백업할 수도 있습니다.

### <a name="windows-server-2019-support"></a>Windows Server 2019 지원

Windows Server 2019에 MABS V3을 설치할 수 있습니다. WS2019와 함께 MABS V3을 사용하기 위해 MABS V3으로 설치/업그레이드하기 전에 OS를 WS2019로 업그레이드하거나 WS2016에서 V3 설치/업그레이드 후에 OS를 업그레이드할 수 있습니다.

MABS V3은 정식 릴리스이며, Windows Server 2016, Windows Server 2019에 직접 설치하거나 MABS V2에서 업그레이드할 수 있습니다. Backup Server V3으로 업그레이드하거나 이 버전을 설치하기 전에 설치 필수 구성 요소를 읽어 보세요.
[여기](./backup-azure-microsoft-azure-backup.md#software-package)에서 MABS에 대한 설치/업그레이드 단계에 대한 자세한 내용을 참조하세요.

> [!NOTE]
>
> MABS에는 System Center Data Protection Manager와 동일한 코드베이스가 있습니다. MABS v3은 Data Protection Manager 1807에 해당합니다. MABS v3 UR1은 Data Protection Manager 2019 UR1에 해당합니다.

## <a name="next-steps"></a>다음 단계

서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.

* [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
* [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
* [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)
* [Backup Server에서 Modern Backup Storage 사용](backup-mabs-add-storage.md)