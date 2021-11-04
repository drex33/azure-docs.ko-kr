---
title: Azure Backup 용어집
description: 이 문서에서는 Azure Backup에서 사용하는 데 유용한 용어를 정의합니다.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 774fc63002677aa9fbb986d40a5d8fe2b3e99f7d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465196"
---
# <a name="azure-backup-glossary"></a>Azure Backup 용어집

이 용어집은 Azure Backup을 사용할 때 유용할 수 있습니다.

>[!NOTE]
>
> - "(워크로드 관련 용어)"라는 접두사로 표시된 용어는 Azure Backup에서 지원하는 워크로드의 특정 하위 세트에 대한 컨텍스트에서만 관련된 용어를 나타냅니다.
> - Azure Backup 설명서에서 일반적으로 사용되지만 다른 Azure 서비스를 참조하는 용어의 경우 관련 Azure 서비스 설명서에 직접 연결되는 링크가 제공됩니다.

## <a name="afs-azure-file-shares"></a>AFS(Azure 파일 공유)

[Azure Files 설명서](../storage/files/storage-files-introduction.md) 참조

## <a name="alternate-location-recovery"></a>대체 위치 복구

복구 지점에서 백업이 수행된 원래 위치가 아닌 다른 위치로 복구합니다. Azure VM 백업을 사용하는 경우 이는 백업이 수행된 원래 서버가 아닌 다른 서버로 VM을 복원하는 것을 의미합니다. Azure 파일 공유 백업을 사용하는 경우 이는 백업된 파일 공유와 다른 파일 공유로 데이터를 복원하는 것을 의미합니다.

## <a name="application-consistent-backup"></a>애플리케이션 일치 백업

(워크로드 관련 용어)

애플리케이션 일치 백업은 메모리 콘텐츠 및 보류 중인 I/O 작업을 캡처합니다. 앱 일치 스냅샷은 백업이 발생하기 전에 [VSS](#vss-windows-volume-shadow-copy-service) VSS 기록기(또는 Linux용 사전/사후 스크립트)를 사용하여 앱 데이터 일관성을 보장합니다. [자세히 알아보기](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>ARM(Azure Resource Manager) 템플릿

[ARM 템플릿 설명서](../azure-resource-manager/templates/overview.md) 참조

## <a name="autoprotection-for-databases"></a>자동 보호(데이터베이스용)

(워크로드 관련 용어)

자동 보호는 독립 실행형 SQL Server 인스턴스 또는 SQL Server Always On 가용성 그룹의 모든 데이터베이스를 자동으로 보호할 수 있는 기능입니다. 기존 데이터베이스에 대한 백업을 사용하도록 설정할 뿐만 아니라 나중에 추가할 수 있는 모든 데이터베이스도 보호합니다.

## <a name="availability-storage-replication-types"></a>가용성(스토리지 복제 유형)

Azure Backup은 스토리지 및 데이터의 고가용성을 유지할 수 있는 다음 세 가지 유형의 복제를 제공합니다.

### <a name="lrs"></a>LRS

[LRS(로컬 중복 스토리지](../storage/common/storage-redundancy.md#locally-redundant-storage))는 백업 데이터를 데이터 센터의 스토리지 배율 단위로 세 번 복제합니다(세 개의 백업 데이터 복사본을 만듦). 모든 백업 데이터 복사본은 동일한 지역 내에 있습니다. LRS는 로컬 하드웨어 오류로부터 백업 데이터를 보호할 수 있는 저렴한 옵션입니다.

### <a name="grs"></a>GRS

[GRS(지역 중복 스토리지)](../storage/common/storage-redundancy.md#geo-redundant-storage)는 기본값이며 권장하는 복제 옵션입니다. GRS는 백업 데이터를 원본 데이터의 기본 위치에서 수백 마일 떨어진 보조 지역으로 복제합니다. GRS는 LRS보다 더 많은 비용이 들지만, 지역 가동 중단이 발생하는 경우에도 높은 수준의 백업 데이터 내구성을 제공합니다.

>[!NOTE]
>지역 간 복원 기능이 사용하도록 설정된 GRS 자격 증명 모음의 경우 백업 스토리지가 GRS에서 RA-GRS(읽기 액세스 지역 중복 스토리지)로 업그레이드됩니다.

### <a name="zrs"></a>ZRS

[ZRS(영역 중복 스토리지)](../storage/common/storage-redundancy.md#zone-redundant-storage)는 [가용성 영역](../availability-zones/az-overview.md#availability-zones)의 백업 데이터를 복제하여 동일한 지역의 백업 데이터 상주 및 복원력을 보장합니다. 따라서 [데이터 상주](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)가 필요한 중요한 워크로드는 ZRS에서 백업할 수 있습니다.

## <a name="azure-command-line-interface-cli"></a>Azure CLI(명령줄 인터페이스)

[Azure CLI 설명서](/cli/azure/what-is-azure-cli) 참조

## <a name="azure-policy"></a>Azure Policy

[Azure Policy 설명서](../governance/policy/overview.md) 참조

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell 설명서](/powershell/azure/) 참조

## <a name="azure-resource-manager-arm"></a>ARM(Azure Resource Manager)

[Azure Resource Manager 설명서](../azure-resource-manager/management/overview.md) 참조

## <a name="azure-disk-encryption-ade"></a>ADE(Azure Disk Encryption)

[Azure Disk Encryption 설명서](../security/fundamentals/azure-disk-encryption-vms-vmss.md) 참조

## <a name="backend-storage--cloud-storage--backup-storage"></a>백 엔드 스토리지/클라우드 스토리지/백업 스토리지

백업 인스턴스에서 사용하는 실제 스토리지입니다. 백업 및 보존 정책에서 정의한 대로 백업 인스턴스에 대해 존재하는 모든 보존 지점의 크기를 포함합니다.

## <a name="bare-metal-backup"></a>완전 백업

사용자 데이터를 제외한 중요한 볼륨의 운영 체제 파일 및 모든 데이터를 백업합니다. 정의에 따라 완전 백업에는 시스템 상태 백업이 포함됩니다. BMR 백업은 컴퓨터가 시작되지 않고 모든 것을 복구해야 할 경우 보호 기능을 제공합니다. [자세히 알아보기](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>백업 확장/VM 확장

(Azure VM 워크로드 유형에만 해당)

Azure VM(가상 머신) 확장은 Azure VM에서 배포 후 구성 및 자동화 태스크를 제공하는 작은 애플리케이션입니다. Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. Azure Backup은 이러한 확장을 자동으로 관리하므로 사용자는 이러한 확장을 수동으로 업데이트할 필요가 없습니다.

## <a name="backup-instance--backup-item"></a>백업 인스턴스/백업 항목

특정 백업 및 보존 정책을 사용하여 자격 증명 모음에 백업된 데이터 원본은 백업 인스턴스 또는 백업 항목을 구성합니다.

## <a name="backup-rule--backup-policy"></a>백업 규칙/백업 정책

백업 규칙은 데이터 원본에서 백업을 수행해야 하는 시기와 빈도를 지정하는 사용자 정의 규칙입니다. 일부 워크로드 유형의 경우 백업 정책은 데이터 원본에 적용할 스냅샷 방법(전체, 증분, 차등)을 지정하는 방법도 제공합니다. 백업 정책은 백업 규칙과 보존 규칙의 조합으로 만들어지는 경우가 많습니다.

## <a name="backup-vault"></a>Backup 자격 증명 모음

*Microsoft.DataProtection/BackupVaults* 형식의 Azure Resource Manager 리소스입니다. 현재 백업 자격 증명 모음은 Azure Database for PostgreSQL 서버를 백업하는 데 사용됩니다. [Backup 자격 증명 모음에 대해 자세히 알아보기](backup-azure-recovery-services-vault-overview.md)

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR(비즈니스 연속성 및 재해 복구)

BCDR에는 비즈니스 중단을 최소화하면서 계획되거나 계획되지 않은 서비스 또는 Azure 가동 중단 중에 앱 및 워크로드가 가동되고 실행되도록 하기 위해 조직에서 채택해야 하는 일단의 프로세스가 포함됩니다. 적절한 BCDR 전략을 수립하는 데 도움이 되도록 Azure에서 제공하는 다양한 서비스에 대해 [자세히 알아보세요](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview).

## <a name="churn"></a>변동

두 개의 연속된 백업 간에 백업되는 데이터의 변경 비율입니다. 이는 새 데이터가 추가되거나 기존 데이터가 수정 또는 삭제되었기 때문일 수 있습니다.

## <a name="crash-consistent-backup"></a>크래시 일치 백업

(워크로드 관련 용어)

크래시 일치 스냅샷은 일반적으로 백업 시 Azure VM이 종료되는 경우에 발생합니다. 백업 시 디스크에 이미 존재하는 데이터만 캡처 및 백업됩니다. [자세히 알아보기](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>CRR(지역 간 복원)

[복원 옵션](backup-azure-arm-restore-vms.md#restore-options) 중 하나인 CRR(지역 간 복원)을 사용하면 [Azure 쌍으로 연결된 지역](../best-practices-availability-paired-regions.md#what-are-paired-regions)인 보조 지역에서 백업 항목을 복원할 수 있습니다.

## <a name="data-box"></a>Data Box

[Data Box 설명서](../databox/data-box-overview.md) 참조

## <a name="datasource"></a>데이터 원본

백업 후보인 리소스(Azure 리소스, 프록시 리소스 또는 온-프레미스 리소스)입니다. 예를 들어 Azure VM 또는 Azure 파일 공유가 있습니다.

## <a name="dpm-data-protection-manager"></a>DPM(Data Protection Manager)

(워크로드 관련 용어)

[DPM 설명서](/system-center/dpm/dpm-overview) 참조

## <a name="expressroute"></a>ExpressRoute

[ExpressRoute 설명서](../expressroute/expressroute-introduction.md) 참조

## <a name="file-system-consistent-backup"></a>파일 시스템 일치 백업

(워크로드 관련 용어)

파일 시스템 일치 백업은 모든 파일의 스냅샷을 동시에 만들어 일관성을 제공합니다. [자세히 알아보기](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>프런트 엔드 스토리지/원본 크기

데이터 원본에 대해 백업할 데이터의 크기입니다. 데이터 원본의 프런트 엔드 크기는 [보호된 인스턴스](#protected-instance) 수를 결정합니다.

## <a name="full-backup"></a>전체 백업

전체 백업에서는 모든 백업에 대한 전체 데이터 원본의 복사본이 저장됩니다.

## <a name="gfs-backup-policy"></a>GFS 백업 정책

GFS(할아버지-아버지-자식) 백업 정책은 매일 백업 일정 외에도 매주, 매월 및 매년 백업 일정을 정의할 수 있는 정책입니다. 매주 백업을 "아들"이라고 하고, 매월 백업을 "아버지"라고 하며, 매년 백업을 "할아버지"라고 합니다. 이러한 백업 복사본 세트는 각각 서로 다른 기간 동안 보존되도록 구성할 수 있으므로 백업 복사본에 대한 보존 선택 항목을 더 자세히 사용자 지정할 수 있습니다. GFS 정책은 백업을 더 효율적인 스토리지 방식으로 오랫동안 유지하는 데 유용합니다.

## <a name="iaas-vms--azure-vms"></a>IaaS VM/Azure VM

[Azure VM 설명서](../virtual-machines/index.yml) 참조

## <a name="incremental-backup"></a>증분 백업

증분 백업은 이전 백업 이후 변경된 블록만 저장합니다.

## <a name="instant-restore"></a>즉시 복원

(워크로드 관련 용어) 즉시 복원에는 자격 증명 모음의 스냅샷 복사본이 아니라 백업 스냅샷에서 컴퓨터를 직접 복원하는 작업이 포함됩니다. 즉시 복원은 자격 증명 모음에서 복원하는 것보다 더 빠릅니다. 사용 가능한 즉시 복원 지점의 수는 스냅샷에 대해 구성된 보존 기간에 따라 달라집니다. 현재 Azure VM 백업에만 적용됩니다.

## <a name="iops"></a>IOPS

초당 입출력 작업 수입니다.

## <a name="item-level-restore"></a>항목 수준 복원

(워크로드 관련 용어)

복구 지점에서 컴퓨터 내의 개별 파일 또는 폴더를 복원합니다.

## <a name="job"></a>작업

사용자 또는 Azure Backup 서비스에서 만든 백업 관련 작업입니다. 작업은 예약되거나 주문형(임시)일 수 있습니다. 백업, 복원, 보호 구성 등 다양한 유형의 작업이 있습니다. [작업에 대해 자세히 알아보기](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-backup-center)

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(워크로드 관련 용어)

Azure Backup Server로 Hyper-V VM, Microsoft SQL Server, SharePoint Server, 단일 콘솔의 Microsoft Exchange 및 Windows 클라이언트와 같은 애플리케이션 워크로드를 보호할 수 있습니다. DPM에서 많은 워크로드 백업 기능을 상속하지만 몇 가지 차이점이 있습니다. [자세한 정보](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>관리 디스크

[관리 디스크 설명서](../virtual-machines/managed-disks-overview.md) 참조

## <a name="mars-agent"></a>MARS 에이전트

(워크로드 관련 용어)

**Azure Backup 에이전트** 또는 **Recovery Services 에이전트** 라고도 하는 MARS 에이전트는 Azure Backup에서 온-프레미스 머신 및 Azure VM의 데이터를 Azure의 백업 Recovery Services 자격 증명 모음에 백업하는 데 사용됩니다. [자세히 알아보기](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG(네트워크 보안 그룹)

[NSG 설명서](../virtual-network/network-security-groups-overview.md) 참조

## <a name="offline-seeding"></a>오프라인 시드

오프라인 시드는 네트워크 대역폭을 사용하지 않고 초기(전체) 백업을 오프라인으로 전송하는 프로세스를 말합니다. 백업 데이터를 실제 스토리지 디바이스에 복사한 다음, 가까운 Azure 데이터 센터로 전송하여 Recovery Services 자격 증명 모음에 업로드하는 메커니즘을 제공합니다. [자세히 알아보기](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>주문형 백업/임시 백업

리소스에 대해 구성된 백업 일정(정책)을 기반으로 하지 않고 사용자가 일회성으로 요구하여 트리거되는 백업 작업입니다.

## <a name="original-location-recovery-olr"></a>OLR(원래 위치 복구)

복원 지점에서 백업이 수행된 원본 위치로 수행되는 복구이며, 복구 지점에 저장된 상태로 바뀝니다. Azure VM 백업을 사용하는 경우 이는 백업이 수행된 원래 서버로 VM을 복원하는 것을 의미합니다. Azure 파일 공유 백업을 사용하는 경우 이는 백업된 파일 공유로 데이터를 복원하는 것을 의미합니다.

## <a name="passphrase"></a>암호

(워크로드 관련 용어)

암호는 MARS 에이전트를 사용하여 Azure에서 온-프레미스 또는 로컬 컴퓨터를 백업 또는 복원하는 동안 데이터를 암호화 및 암호 해독하는 데 사용됩니다.

## <a name="private-endpoint"></a>프라이빗 엔드포인트

[프라이빗 엔드포인트 설명서](../private-link/private-endpoint-overview.md) 참조

## <a name="protected-instance"></a>보호된 인스턴스

보호된 인스턴스는 백업을 Azure에 구성하는 데 사용하는 컴퓨터, 실제 또는 가상 서버를 나타냅니다.  **청구 관점** 에서 컴퓨터의 보호된 인스턴스 수는 프런트 엔드 크기의 함수입니다. 따라서 Azure에 백업된 VM과 같은 단일 백업 인스턴스는 프런트 엔드 크기에 따라 여러 개의 보호된 인스턴스에 해당할 수 있습니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC(역할 기반 액세스 제어)

[RBAC 설명서](../role-based-access-control/overview.md) 참조

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>복구 지점/복원 지점/보존 지점/PIT(특정 시점)

백업하는 원본 데이터의 복사본입니다. 보존 지점은 타임스탬프와 연결되어 있으므로 이를 사용하여 항목을 특정 시점으로 복원할 수 있습니다.

## <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음

*Microsoft.RecoveryServices/vaults* 형식의 Azure Resource Manager 리소스입니다. 현재 Recovery Services 자격 증명 모음은 Azure VM, Azure VM의 SQL, Azure VM의 SAP HANA 및 Azure 파일 공유를 백업하는 데 사용됩니다. 또한 MARS, Azure Backup Server(MABS) 및 System Center DPM을 사용하여 온-프레미스 워크로드를 백업하는 데도 사용됩니다. [Recovery Services 자격 증명 모음에 대해 자세히 알아보기](backup-azure-recovery-services-vault-overview.md)

## <a name="resource-group"></a>Resource group

[Azure Resource Manager 설명서](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group) 참조

## <a name="rest-api"></a>REST API

[Azure REST API 설명서](/rest/api/azure/) 참조

## <a name="retention-rule"></a>보존 규칙

백업을 보존해야 하는 기간을 지정하는 사용자 정의 규칙입니다.

## <a name="rpo-recovery-point-objective"></a>RPO(복구 지점 목표)

RPO는 데이터 손실 시나리오에서 가능한 최대 데이터 손실을 나타냅니다. 백업 빈도에 따라 결정됩니다.

## <a name="rto-recovery-time-objective"></a>RTO(복구 시간 목표)

RTO는 데이터 손실 시나리오 후에 사용 가능한 마지막 시점으로 데이터를 복원할 수 있는 최대 가능한 시간을 나타냅니다.

## <a name="scheduled-backup"></a>예약된 백업

지정된 항목에 대해 구성된 백업 정책에 따라 자동으로 트리거되는 백업 작업입니다.

## <a name="secondary-region--paired-region"></a>보조 지역/쌍으로 연결된 지역

지역 쌍은 동일한 지리 내의 두 지역으로 구성됩니다. 하나는 주 지역이고, 다른 하나는 보조 지역입니다. 쌍으로 연결된 지역은 비즈니스 연속성을 보장하고 데이터 손실을 방지하기 위해 일부 Azure 서비스(GRS 설정이 있는 Azure Backup 포함)에서 사용됩니다. [자세히 알아보기](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>일시 삭제

일시 삭제는 백업 데이터가 실수로 삭제되지 않도록 방지하는 기능입니다. 일시 삭제를 사용하면 악의적인 행위자가 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 백업 데이터가 추가 기간 동안 유지되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. [자세히 알아보기](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>스냅샷

스냅샷은 VHD(가상 하드 드라이브) 또는 Azure 파일 공유의 전체 읽기 전용 복사본입니다. [디스크 스냅샷](../virtual-machines/windows/snapshot-copy-managed-disk.md) 및 [파일 스냅샷](../storage/files/storage-snapshots-files.md)에 대해 자세히 알아보세요.

## <a name="storage-account"></a>스토리지 계정

[스토리지 계정 설명서](../storage/common/storage-account-overview.md) 참조

## <a name="subscription"></a>구독

Azure 구독은 Azure에서 리소스를 프로비저닝하는 데 사용되는 논리적 컨테이너입니다. VM(가상 머신), 데이터베이스 등과 같은 모든 리소스에 대한 세부 정보를 보관합니다.

## <a name="system-state-backup"></a>시스템 상태 백업

(워크로드 관련 용어)

운영 체제 파일을 백업합니다. 이 백업을 사용하면 컴퓨터가 시작될 때 복구할 수 있지만, 시스템 파일과 레지스트리가 손실됩니다. [자세히 알아보기](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>테넌트

테넌트는 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다.

## <a name="tier"></a>계층

현재 Azure Backup에서 지원하는 백업 스토리지 계층은 다음과 같습니다.

### <a name="snapshot-tier"></a>스냅샷 계층

VM 백업의 첫 번째 단계에서는 만든 스냅샷이 디스크와 함께 저장됩니다. 이러한 형태의 스토리지를 스냅샷 계층이라고 합니다. 스냅샷 계층 복원은 복원 작업을 트리거하기 전에 자격 증명 모음에서 스냅샷이 복사될 때까지 기다리는 시간을 제거하므로 자격 증명 모음에서 복원하는 것보다 더 빠릅니다.

### <a name="vault-standard-tier"></a>자격 증명 모음-표준 계층

Azure Backup에서 지원하는 모든 워크로드에 대한 백업 데이터는 Azure Backup에서 관리하는 자동 크기 조정 스토리지 계정 세트인 백업 스토리지를 보관하는 자격 증명 모음에 저장됩니다. 자격 증명 모음-표준 계층은 Microsoft 관리 테넌트에 격리된 백업 데이터 복사본을 저장하여 추가 보호 계층을 만들 수 있는 온라인 스토리지 계층입니다. 스냅샷 계층이 지원되는 워크로드의 경우 스냅샷 계층과 자격 증명 모음-표준 계층 모두에 백업 데이터 복사본이 있습니다. 자격 증명 모음-표준 계층을 사용하면 백업하는 데이터 원본이 삭제되거나 손상된 경우에도 백업 데이터를 사용할 수 있도록 합니다.

## <a name="unmanaged-disk"></a>관리되지 않는 디스크

[비관리 디스크 설명서](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) 참조

## <a name="vault"></a>Vault

백업 데이터를 보관하는 Azure의 스토리지 엔터티입니다. 또한 RBAC 및 청구의 단위이기도 합니다. 현재 두 가지 유형의 자격 증명 모음(Recovery Services 자격 증명 모음 및 Backup 자격 증명 모음)이 있습니다.

## <a name="vault-credentials"></a>자격 증명 모음 자격 증명

자격 증명 모음 자격 증명 파일은 포털에서 각 자격 증명 모음에 대해 생성하는 인증서입니다. 이는 온-프레미스 서버를 자격 증명 모음에 등록하는 동안 사용됩니다. [자세히 알아보기](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>VNET(Virtual Network)

[VNET 설명서](../virtual-network/virtual-networks-overview.md) 참조

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS(Windows 볼륨 섀도 복사본 서비스)

[VSS 설명서](/windows-server/storage/file-server/volume-shadow-copy-service) 참조

## <a name="next-steps"></a>다음 단계

- [Azure Backup 개요](backup-overview.md)
- [Azure Backup 아키텍처 및 구성 요소](backup-architecture.md)