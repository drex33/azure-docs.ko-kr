---
title: 아키텍처 개요
description: Azure Backup 서비스에서 사용하는 아키텍처, 구성 요소 및 프로세스에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 2523d0fc9a8e4843fc0d3d41b5ed35c89bace325
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430381"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup 아키텍처 및 구성 요소

[Azure Backup 서비스](backup-overview.md)를 사용하여 Microsoft Azure 클라우드 플랫폼에 데이터를 백업할 수 있습니다. 이 문서에는 Azure Backup 아키텍처, 구성 요소 및 프로세스가 요약되어 있습니다.

## <a name="what-does-azure-backup-do"></a>Azure Backup의 기능은 무엇인가요?

Azure Backup은 온-프레미스 컴퓨터와 Azure VM(가상 머신) 인스턴스에서 실행되는 데이터, 컴퓨터 상태 및 워크로드를 백업합니다. 여러 Azure Backup 시나리오가 있습니다.

## <a name="how-does-azure-backup-work"></a>Azure Backup은 어떻게 작동하나요?

다양한 방법을 사용하여 컴퓨터와 데이터를 백업할 수 있습니다.

- **온-프레미스 머신 백업**:
  - Azure Backup MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 온-프레미스 Windows 컴퓨터를 Azure에 직접 백업할 수 있습니다. Linux 컴퓨터는 지원되지 않습니다.
  - 온-프레미스 컴퓨터를 백업 서버인 System Center DPM(Data Protection Manager) 또는 MABS(Microsoft Azure Backup Server)에 백업할 수 있습니다. 그런 다음, 백업 서버를 Azure의 Recovery Services 자격 증명 모음에 백업할 수 있습니다.

- **Azure VM 백업**:
  - Azure VM을 직접 백업할 수 있습니다. Azure Backup은 VM에서 실행 중인 Azure VM 에이전트에 대해 백업 확장을 설치합니다. 이 확장은 전체 VM을 백업합니다.
  - MARS 에이전트를 실행하여 Azure VM에서 특정 파일 및 폴더를 백업할 수 있습니다.
  - Azure에서 실행중인 MABS에 Azure VM을 백업한 다음, MABS를 Recovery Services 자격 증명 모음에 백업할 수 있습니다.

[백업할 수 있는 항목](backup-overview.md)과 [지원되는 백업 시나리오](backup-support-matrix.md)에 대해 자세히 알아보세요.

## <a name="where-is-data-backed-up"></a>데이터는 어디에 백업되나요?

Azure Backup은 백업된 데이터를 자격 증명 모음(Recovery Services 자격 증명 모음 및 Backup 자격 증명 모음)에 저장합니다. 자격 증명 모음은 백업 복사본, 복구 지점 및 백업 정책과 같은 데이터를 보관하는 데 사용되는 Azure의 온라인 스토리지 엔터티입니다.

자격 증명 모음에는 다음과 같은 기능이 있습니다.

- 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다.
- Azure VM 및 온-프레미스 컴퓨터를 포함하여 자격 증명 모음에 백업된 항목을 모니터링할 수 있습니다.
- [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 사용하여 자격 증명 모음 액세스를 관리할 수 있습니다.
- 자격 증명 모음의 데이터가 중복성을 위해 복제되는 방법을 지정합니다.
  - **LRS(로컬 중복 스토리지)** : 데이터 센터의 장애로부터 보호하기 위해 LRS를 사용할 수 있습니다. LRS는 스토리지 배율 단위에 데이터를 복제합니다. [자세히 알아보기](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **GRS(지역 중복 스토리지)** : 지역 전체의 중단으로부터 보호하기 위해 GRS를 사용할 수 있습니다. GRS는 데이터를 보조 지역에 복제합니다. [자세히 알아보기](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **ZRS(영역 중복 스토리지)** : [가용성 영역](../availability-zones/az-overview.md#availability-zones)에서 데이터를 복제하여 동일한 지역에 데이터 상주 및 복원력을 보장합니다. [자세히 알아보기](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - 기본적으로 Recovery Services 자격 증명 모음은 GRS를 사용합니다.

Recovery Services 자격 증명 모음에는 다음과 같은 추가 기능이 있습니다.

- 각 Azure 구독에서 최대 500개의 자격 증명 모음을 만들 수 있습니다.

## <a name="backup-agents"></a>백업 에이전트

Azure Backup은 백업 중인 컴퓨터의 유형에 따라 서로 다른 백업 에이전트를 제공합니다.

**에이전트** | **세부 정보**
--- | ---
**MARS 에이전트** | <ul><li>파일, 폴더 및 시스템 상태를 백업하기 위해 개별 온-프레미스 Windows Server 컴퓨터에서 실행됩니다.</li> <li>파일, 폴더 및 시스템 상태를 백업하기 위해 Azure VM에서 실행됩니다.</li> <li>DPM/MABS 로컬 스토리지 디스크를 Azure에 백업하기 위해 DPM/MABS 서버에서 실행됩니다.</li></ul>
**Azure VM 확장** | 자격 증명 모음에 백업하기 위해 Azure VM에서 실행됩니다.

## <a name="backup-types"></a>Backup 유형

다음 표에서는 다양한 백업 유형과 사용 시기를 설명합니다.

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체** | 전체 백업에는 전체 데이터 원본이 포함됩니다. 차등 또는 증분 백업보다 더 많은 네트워크 대역폭을 사용합니다. | 초기 백업에 사용됩니다.
**차등** |  차등 백업은 초기 전체 백업 이후 변경된 블록을 저장합니다. 네트워크와 스토리지를 더 적게 사용하고 변경되지 않은 데이터의 중복 복사본을 보관하지 않습니다.<br/><br/> 이후 백업 간에 변경되지 않은 데이터 블록이 전송되어 저장되므로 비효율적입니다. | Azure Backup에서 사용되지 않습니다.
**증분** | 증분 백업은 이전 백업 이후 변경된 데이터 블록만 저장합니다. 스토리지 및 네트워크 효율성이 높습니다. <br/><br/> 증분 백업의 경우 전체 백업으로 보완할 필요가 없습니다. | DPM/MABS에서 디스크 백업에 사용되며 Azure에 대한 모든 백업에서 사용됩니다. SQL Server 백업에는 사용되지 않습니다.

## <a name="sql-server-backup-types"></a>SQL Server 백업 유형

다음 표에서는 SQL Server 데이터베이스에 사용되는 다양한 유형의 백업과 사용 빈도에 대해 설명합니다.

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체 백업** | 전체 데이터베이스 백업은 전체 데이터베이스를 백업합니다. 여기에는 특정 데이터베이스나 파일 그룹 또는 파일 세트의 모든 데이터가 포함됩니다. 전체 백업에는 해당 데이터를 복구하기에 충분한 로그도 포함됩니다. | 많으면, 하루에 하나의 전체 백업을 트리거할 수 있습니다.<br/><br/> 매일 또는 매주 간격으로 전체 백업을 수행하도록 선택할 수 있습니다.
**차등 백업** | 차등 백업은 가장 최근에 수행한 이전 전체 데이터 백업을 기반으로 합니다.<br/><br/> 이 백업은 전체 백업 이후 변경된 데이터만 캡처합니다. |  많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.<br/><br/> 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다.
**트랜잭션 로그 백업** | 로그 백업을 사용하면 특정 시간(초 단위)까지의 특정 시점 복원이 가능합니다. | 많으면, 15분마다 트랜잭션 로그 백업을 구성할 수 있습니다.

## <a name="sap-hana-backup-types"></a>SAP HANA 백업 유형

다음 표에서는 SAP HANA 데이터베이스에 사용되는 다양한 유형의 백업과 사용 빈도에 대해 설명합니다.

| 백업 유형 | 세부 정보 | 사용량 |
| --- | --- | --- |
| **전체 백업** | 전체 데이터베이스 백업은 전체 데이터베이스를 백업합니다. 이 유형의 백업은 특정 지점으로 복원하는 데 독립적으로 사용할 수 있습니다. | 많으면, 하루에 하나의 전체 백업을 예약할 수 있습니다. <br><br> 매일 또는 매주 간격으로 전체 백업을 예약하도록 선택할 수 있습니다. |
| **차등 백업** | 차등 백업은 가장 최근에 수행한 이전 전체 데이터 백업을 기반으로 합니다. <br><br> 이 백업은 이전의 전체 백업 이후 변경된 데이터만 캡처합니다. | 많으면, 하루에 하나의 차등 백업을 예약할 수 있습니다.  <br><br> 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다. |
| **증분 백업** | 증분 백업은 가장 최근의 이전 전체/차등/증분 데이터 백업을 기반으로 합니다. <br><br> 이 백업은 이 이전 데이터 백업 이후 변경된 데이터만 캡처합니다. | 많으면, 하루에 하나의 증분 백업을 예약할 수 있습니다. <br><br> 데이터베이스에서 차등 백업과 증분 백업을 모두 예약할 수는 없으며 하나의 델타 백업 유형만 예약할 수 있습니다. <br><br> 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다. |k
| **트랜잭션 로그 백업** | 로그 백업을 사용하면 특정 시간(초 단위)까지의 특정 시점 복원이 가능합니다. | 많으면, 15분마다 트랜잭션 로그 백업을 구성할 수 있습니다. |

## <a name="comparison-of-backup-types"></a>백업 유형 비교

스토리지 사용량, RTO(복구 시간 목표) 및 네트워크 사용량은 각 백업 유형마다 다릅니다. 다음 이미지는 백업 유형을 비교하여 보여줍니다.

- 데이터 원본 A는 매달 백업되는 10개의 스토리지 블록 A1-A10으로 구성됩니다.
- 블록 A2, A3, A4 및 A9는 첫 번째 달에 변경되고 블록 A5는 다음 달에 변경됩니다.
- 차등 백업의 경우 두 번째 달에 변경된 블록 A2, A3, A4 및 A9가 백업됩니다. 세 번째 달에 이와 동일한 블록이 변경된 블록 A5와 함께 다시 백업됩니다. 변경된 블록은 다음 전체 백업이 발생할 때가지 계속 백업됩니다.
- 증분 백업의 경우 두 번째 달에 A2, A3, A4 및 A9 블록이 변경되고 전송된 것으로 표시됩니다. 세 번째 달에는 변경된 블록 A5만 표시되고 전송됩니다.

![스토리지 사용량, RTO(복구 시간 목표) 및 네트워크 사용량이 각 백업 유형에 따라 달라지는 것을 보여 주는 다이어그램](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>백업 기능

다음 표에는 다양한 백업 유형에 대해 지원되는 기능이 요약되어 있습니다.

**기능** | **파일 및 폴더 직접 백업(MARS 에이전트 사용)** | **Azure VM 백업** | **DPM/MABS가 있는 컴퓨터 또는 앱**
--- | --- | --- | ---
자격 증명 모음에 백업 | ![예][green] | ![예][green] | ![예][green]
DPM/MABS 디스크에 백업 한 다음, Azure에 백업 | | | ![예][green]
백업을 위해 전송된 데이터 압축 | ![예][green] | 데이터를 전송할 때 압축이 사용되지 않습니다. 스토리지가 약간 증가하지만 복원 속도가 더 빠릅니다.  | ![예][green]
증분 백업 실행 |![예][green] |![예][green] |![예][green]
중복 제거된 디스크 백업 | | | ![부분적으로][yellow]<br/><br/> 온-프레미스에 배포된 DPM/MABS 서버에만 해당합니다.

![테이블 키](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>백업 정책 기본 정보

- 자격 증명 모음당 백업 정책을 만듭니다.
- Azure VM, Azure VM의 SQL, Azure VM의 SAP HANA 및 Azure 파일 공유와 같은 워크로드의 백업을 위한 백업 정책을 만들 수 있습니다. MARS 에이전트를 사용하는 파일 및 폴더 백업에 대한 정책은 MARS 콘솔에서 지정됩니다.
  - Azure 파일 공유
- 다양한 리소스에 정책을 할당할 수 있습니다. 여러 Azure VM을 보호하는 데 Azure VM 백업 정책을 사용할 수 있습니다.
- 두 구성 요소로 이루어진 정책
  - 일정: 백업을 수행하는 경우
  - 보존: 각 백업을 보존해야 할 기간
- 특정 시점을 사용하여 "매일" 또는 "매주"로 일정을 정의할 수 있습니다.
- "매일", "매주", "매월", "매년" 단위로 백업 지점에 대한 보존을 정의할 수 있습니다.
  - "매주"는 매주 특정 요일에 백업하는 것을 의미합니다.
  - "매월"은 매월 특정 일에 백업하는 것을 의미합니다.
  - "매년"은 매년 특정 일에 백업하는 것을 의미합니다.
- "매월", "매년" 백업 지점의 보존을 LTR(장기 보존)이라고 합니다.
- 자격 증명 모음이 만들어지면 리소스 백업에 사용할 수 있는 "DefaultPolicy"도 만들어집니다.
- 백업 정책의 보존 기간에 대한 변경 내용은 새 복구 지점을 제외한 모든 이전 복구 지점에 소급 적용됩니다.

### <a name="impact-of-policy-change-on-recovery-points"></a>복구 지점에 대한 정책 변경의 영향

- **보존 기간 증가/감소:** 보존 기간이 변경되면 기존 복구 지점에도 새로운 보존 기간이 적용됩니다. 결과적으로 일부 복구 지점이 정리됩니다. 보존 기간이 늘어나면 기존 복구 지점도 보존 기간이 늘어납니다.
- **매일에서 매주로 변경됨:** 예약된 백업이 매일에서 매주로 변경되면 기존 매일 복구 지점이 정리됩니다.
- **매주에서 매일로 변경됨:** 기존의 매주 백업은 현재 보존 정책에 따라 남은 일 수를 기준으로 보존됩니다.

### <a name="additional-reference"></a>추가 참조

- Azure VM 컴퓨터: 정책을 [만들고](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) [수정](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm)하는 방법입니다.
- Azure VM 컴퓨터의 SQL Server 데이터베이스: 정책을 [만들고](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) [수정](./manage-monitor-sql-database-backup.md#modify-policy)하는 방법입니다.
- Azure 파일 공유: 정책을 [만들고](./backup-afs.md) [수정](./manage-afs-backup.md#modify-policy)하는 방법입니다.
- SAP HANA: 정책을 [만들고](./backup-azure-sap-hana-database.md#create-a-backup-policy) [수정](./sap-hana-db-manage.md#change-policy)하는 방법입니다.
- MARS: 정책을 [만들고](./backup-windows-with-mars-agent.md#create-a-backup-policy) [수정](./backup-azure-manage-mars.md#modify-a-backup-policy)하는 방법입니다.
- [워크로드 유형에 따라 백업 예약에 제한이 있나요?](./backup-azure-backup-faq.yml#are-there-limits-on-backup-scheduling-)
- [보존 정책을 변경하는 경우 기존 복구 지점은 어떻게 되나요?](./backup-azure-backup-faq.yml#what-happens-when-i-change-my-backup-policy-)

## <a name="architecture-built-in-azure-vm-backup"></a>아키텍처: 기본 제공 Azure VM 백업

[!INCLUDE [azure-vm-backup-process.md](../../includes/azure-vm-backup-process.md)]

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>아키텍처: 온-프레미스 Windows Server 컴퓨터나 Azure VM 파일 또는 폴더의 직접 백업

1. 시나리오를 설정하려면 컴퓨터에 MARS 에이전트를 다운로드하여 설치합니다. 그런 다음, 백업할 항목, 백업 실행 시기 및 Azure에 보관되는 기간을 선택합니다.
1. 초기 백업은 백업 설정에 따라 실행됩니다.
1. MARS 에이전트는 VSS를 사용하여 백업을 위해 선택한 볼륨의 특정 시점 스냅샷을 만듭니다.
    - MARS 에이전트는 Windows 시스템 쓰기 작업만 사용하여 스냅샷을 캡처합니다.
    - 에이전트는 애플리케이션 VSS 기록기를 사용하지 않기 때문에 앱 일관성이 있는 스냅샷을 캡처하지 않습니다.
1. VSS를 사용하여 스냅샷을 만든 후 MARS 에이전트는 백업을 구성할 때 지정한 캐시 폴더에 가상 하드 디스크(VHD)를 만듭니다. 에이전트는 또한 각 데이터 블록에 대한 체크섬을 저장합니다. 이는 나중에 후속 증분 백업을 위해 변경된 블록을 검색하는 데 사용됩니다.
1. 주문형 백업을 실행하지 않는 한, 지정하는 일정에 따라 증분 백업이 실행됩니다.
1. 증분 백업에서는 변경된 파일이 식별되고 새 VHD가 만들어집니다. VHD는 압축되고 암호화된 후 자격 증명 모음으로 전송됩니다.
1. 증분 백업이 완료되면 새 VHD가 초기 복제 이후에 만든 VHD와 병합됩니다. 이 병합된 VHD는 지속적인 백업을 위한 비교에 사용할 최신 상태를 제공합니다.

![MARS 에이전트를 사용하여 온-프레미스 Windows Server 컴퓨터 백업](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>아키텍처: DPM/MABS에 백업

1. 보호하려는 컴퓨터에 DPM 또는 MABS 보호 에이전트를 설치합니다. 그런 다음, DPM 보호 그룹에 컴퓨터를 추가합니다.
    - 온-프레미스 머신을 보호하려면 DPM 또는 MABS 서버가 온-프레미스에 있어야 합니다.
    - Azure VM을 보호하려면 Azure VM으로 실행되는 MABS 서버가 Azure에 있어야 합니다.
    - DPM/MABS를 사용하여 백업 볼륨, 공유, 파일 및 폴더를 보호할 수 있습니다. 또한 컴퓨터의 시스템 상태/완전 복구를 보호하고 특정 앱을 앱 인식 백업 설정으로 보호할 수 있습니다.
1. DPM/MABS에서 컴퓨터 또는 앱에 대한 보호를 설정할 때 단기 스토리지용 MABS/DPM 로컬 디스크에 백업한 다음, 온라인 보호를 위해 Azure에 백업하도록 선택합니다. 로컬 DPM/MABS 스토리지에 백업을 실행할 시기와 Azure에 온라인 백업을 실행할 시기도 지정합니다.
1. 보호된 워크로드의 디스크는 지정한 일정에 따라 로컬 MABS/DPM 디스크에 백업됩니다.
1. DPM/MABS 디스크는 DPM/MABS 서버에서 실행되는 MARS 에이전트에 의해 자격 증명 모음으로 백업됩니다.

![DPM 또는 MABS로 보호된 컴퓨터 및 워크로드의 백업](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 스토리지

Azure VM은 디스크를 사용하여 운영 체제, 앱 및 데이터를 저장합니다. 각 Azure VM에는 운영 체제용 디스크와 임시 디스크라는 2개 이상의 디스크가 있습니다. 앱 데이터에 사용되는 데이터 디스크도 있을 수 있습니다. 디스크는 VHD로 저장됩니다.

- VHD는 Azure의 Standard 또는 Premium Storage 계정에 페이지 Blob으로 저장됩니다.
  - **Standard Storage:** 대기 시간이 중요하지 않은 워크로드를 실행하는 VM에 대한 신뢰할 수 있는 저비용 디스크 지원입니다. Standard Storage는 SSD(표준 반도체 드라이브) 디스크 또는 HDD(표준 하드 디스크 드라이브) 디스크를 사용할 수 있습니다.
  - **Premium Storage:** 고성능 디스크 지원입니다. 프리미엄 SSD 디스크를 사용합니다.
- 디스크에는 다음과 같은 여러 성능 계층이 있습니다.
  - **표준 HDD 디스크:** HDD를 통해 지원되며 경제적인 스토리지에 사용됩니다.
  - **표준 SSD 디스크:** 프리미엄 SSD 디스크와 표준 HDD 디스크의 요소를 결합합니다. HDD보다 일관된 성능과 안정성을 제공하지만 여전히 비용 효율적입니다.
  - **프리미엄 SSD 디스크:** SSD로 지원되며 I/O 집약적 워크로드를 실행하는 VM에 대해 고성능과 낮은 대기 시간을 제공합니다.
- 다음과 같은 관리 또는 비관리 디스크가 있습니다.
  - **비관리 디스크:** VM에서 사용하는 기존 유형의 디스크입니다. 이러한 디스크의 경우 사용자 고유의 스토리지 계정을 만든 다음, 디스크를 만들 때 지정합니다. 그런 다음, VM의 스토리지 리소스를 최대화하는 방법을 파악해야 합니다.
  - **관리 디스크:** Azure는 사용자를 위해 스토리지 계정을 만들고 관리합니다. 디스크 크기와 성능 계층을 지정하면 Azure에서 관리 디스크를 만듭니다. 디스크를 추가하고 VM을 확장하면 Azure에서 스토리지 계정을 처리합니다.

디스크 스토리지와 VM에 사용 가능한 디스크 유형에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Linux VM용 Azure 관리 디스크](../virtual-machines/managed-disks-overview.md)
- [VM에 사용 가능한 디스크 유형](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Premium Storage로 Azure VM 백업 및 복원

Azure Backup과 함께 Premium Storage를 사용하여 Azure VM을 백업할 수 있습니다.

- Premium Storage를 사용하여 VM을 백업하는 과정에서 Backup 서비스는 Premium Storage 계정에 *AzureBackup-* 이라는 임시 준비 위치를 만듭니다. 준비 위치의 크기는 복구 지점 스냅샷의 크기와 같습니다.
- Premium Storage 계정에 임시 준비 위치를 수용할 충분한 여유 공간이 있어야 합니다. 자세한 내용은 [프리미엄 페이지 Blob Storage 계정에 대한 확장성 목표](../storage/blobs/scalability-targets-premium-page-blobs.md)를 참조하세요. 준비 위치를 수정하지 마세요.
- 백업 작업이 완료되면 준비 위치가 삭제됩니다.
- 준비 위치에 사용되는 스토리지의 가격은 모든 [Premium Storage 가격 책정](../virtual-machines/disks-types.md#billing)과 일관성이 있습니다.

Premium Storage를 사용하여 Azure VM을 복원하는 경우 Premium 또는 Standard Storage로 복원할 수 있습니다. 일반적으로 Premium Storage로 복원합니다. 그러나 VM에서 파일의 하위 집합만 필요한 경우 Standard Storage로 복원하는 것이 경제적일 수 있습니다.

### <a name="back-up-and-restore-managed-disks"></a>관리 디스크 백업 및 복원

관리 디스크를 사용하여 Azure VM을 백업할 수 있습니다.

- 다른 Azure VM과 동일한 방식으로 관리 디스크를 사용하여 VM을 백업합니다. 가상 머신 설정에서 직접 VM을 백업하거나, Recovery Services 자격 증명 모음에서 VM에 대한 백업을 사용하도록 설정할 수 있습니다.
- Managed Disks에 VM 백업은 Managed Disks를 기반으로 구축된 RestorePoint 컬렉션을 통해 지원됩니다.
- 또한 Azure Backup은 Azure Disk Encryption을 사용하여 암호화된 관리 디스크로 VM 백업을 지원합니다.

관리 디스크를 포함하는 VM을 복원하는 경우 관리 디스크를 포함하는 전체 VM으로 복원하거나 스토리지 계정으로 복원할 수 있습니다.

- 복원 프로세스 중 Azure는 관리 디스크를 관리합니다. 스토리지 계정 옵션을 사용하는 경우 복원 프로세스 중에 생성되는 스토리지 계정을 관리합니다.
- 암호화된 관리형 VM을 복원하는 경우 복원 프로세스를 시작하기 전에 VM 키와 비밀이 키 자격 증명 모음에 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- 지원 매트릭스를 검토하여 [백업 시나리오에 대해 지원되는 기능 및 제한 사항을 알아봅니다](backup-support-matrix.md).
- 다음 시나리오 중 하나에 대해 백업을 설정합니다.
  - [Azure VM 백업](backup-azure-arm-vms-prepare.md):
  - 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
  - Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
  - Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
