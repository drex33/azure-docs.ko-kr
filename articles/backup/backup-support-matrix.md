---
title: Azure Backup 지원 매트릭스
description: Azure Backup 서비스에 대한 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 09/21/2021
ms.custom: references_regions
ms.openlocfilehash: 11ed401ef3a1591bd3dc38ad7b4964033adfa3ac
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059377"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup Server의 지원 매트릭스

[Azure Backup](backup-overview.md)을 사용하여 Microsoft Azure 클라우드 플랫폼에 데이터를 백업할 수 있습니다. 이 문서에는 Azure Backup 시나리오 및 배포에 대한 일반 지원 설정과 제한 사항이 요약되어 있습니다.

기타 지원 매트릭스를 사용할 수 있습니다.

- [Azure Virtual Machine(VM) 백업](backup-support-matrix-iaas.md)의 지원 매트릭스
- [System Center DPM(Data Protection Manager)/MABS(Microsoft Azure Backup Server)](backup-support-matrix-mabs-dpm.md)를 사용하는 백업 지원 매트릭스
- [MARS(Microsoft Azure Recovery Services) 에이전트](backup-support-matrix-mars-agent.md)를 사용하는 백업 지원 매트릭스

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>자격 증명 모음 지원

Azure Backup에서는 Recovery Services 자격 증명 모음을 사용하여 Azure VM, Azure VM의 SQL, Azure VM의 SAP HANA, Azure 파일 공유 및 온-프레미스 워크로드(Azure Backup 에이전트, Azure Backup Server, System Center DPM 사용) 등의 워크로드 유형에 대한 백업을 오케스트레이션하고 관리합니다. 또한 Recovery Services 자격 증명 모음을 사용하여 이러한 워크로드에 대한 백업 데이터를 저장합니다.

다음 표에서는 Recovery Services 자격 증명 모음의 기능을 설명합니다.

**기능** | **세부 정보**
--- | ---
**구독의 자격 증명 모음 수** | 단일 구독에서 최대 500개의 Recovery Services 자격 증명 모음입니다.
**자격 증명 모음의 머신 수** | 모든 워크로드에서 최대 2000개의 데이터 원본(예: Azure VM, SQL Server VM, MABS 서버 등)이 단일 자격 증명 모음에서 보호될 수 있습니다.<br><br>단일 자격 증명 모음에서 최대 1,000개의 Azure VM입니다.<br/><br/> 최대 50대의 MABS 서버를 단일 자격 증명 모음에 등록할 수 있습니다.
**데이터 원본** | 개별 [데이터 원본](./backup-azure-backup-faq.yml#how-is-the-data-source-size-determined-)의 최대 크기는 54,400GB입니다. Azure VM 백업에는 이 제한이 적용되지 않습니다. 자격 증명 모음에 백업할 수 있는 총 데이터양에는 제한이 적용되지 않습니다.
**자격 증명 모음에 대한 백업 횟수** | **Azure VM:** 하루 한 번.<br/><br/>**DPM/MABS로 보호된 머신:** 하루에 두 번.<br/><br/> **MARS 에이전트를 사용하여 직접 백업하는 머신:** 하루에 세 번.
**자격 증명 모음 간 백업** | 백업은 한 지역 내에서 수행됩니다.<br/><br/> 백업할 VM이 포함된 각 Azure 지역에 자격 증명 모음이 있어야 합니다. 다른 지역으로 백업할 수 없습니다.
**자격 증명 모음 이동** | 구독 간에 또는 동일한 구독의 리소스 그룹 간에 [자격 증명 모음을 이동](./backup-azure-move-recovery-services-vault.md)할 수 있습니다. 그러나 자격 증명 모음을 지역 간에 이동하는 것은 지원되지 않습니다.
**자격 증명 모음 간 데이터 이동** | 자격 증명 모음 간의 백업된 데이터 이동은 지원되지 않습니다.
**자격 증명 모음 스토리지 유형 수정** | 백업을 저장하기 전에 자격 증명 모음에 대한 스토리지 복제 유형(지역 중복 스토리지 또는 로컬 중복 스토리지)을 수정할 수 있습니다. 자격 증명 모음에서 백업이 시작되면 복제 유형을 수정할 수 없습니다.
**ZRS(영역 중복 스토리지)** | 영국 남부, 남부 동아시아, 오스트레일리아 동부, 북유럽, 미국 중부, 미국 동부 2, 브라질 남부, 미국 중남부, 한국 중부, 노르웨이 동부, 프랑스 중부, 서유럽, 동아시아, 스웨덴 중부 및 일본 동부에서 미리 보기로 지원합니다.
**프라이빗 엔드포인트** | Recovery Services 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들기 위한 요구 사항은 [이 섹션](./private-endpoints.md#before-you-start)을 참조하세요.  

## <a name="on-premises-backup-support"></a>온-프레미스 백업 지원

온-프레미스 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**머신** | **백업 대상** | **위치** | **기능**
--- | --- | --- | ---
**MARS 에이전트를 사용한 Windows 머신 직접 백업** | 파일, 폴더, 시스템 상태 | Recovery Services 자격 증명 모음에 백업합니다. | 하루에 세 번 백업<br/><br/> 앱 인식 백업이 없음<br/><br/> 파일, 폴더, 볼륨 복원
**MARS 에이전트를 사용한 Linux 머신 직접 백업** | 백업이 지원되지 않음
**DPM에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | 로컬 DPM 스토리지에 백업합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음
**MABS에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS 로컬 스토리지에 백업합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음

## <a name="azure-vm-backup-support"></a>Azure VM 백업 지원

### <a name="azure-vm-limits"></a>Azure VM 제한

**제한** | **세부 정보**
--- | ---
**Azure VM 데이터 디스크 수** | [Azure VM 백업에 대한 지원 매트릭스](./backup-support-matrix-iaas.md#vm-storage-support)를 확인합니다.
**Azure VM 데이터 디스크 크기** | 개별 디스크 크기는 최대 32TB이며 VM의 모든 디스크에 대해 최대 256TB를 결합할 수 있습니다.

### <a name="azure-vm-backup-options"></a>Azure VM 백업 옵션

Azure VM을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**머신** | **백업 대상** | **위치** | **기능**
--- | --- | --- | ---
**VM 확장을 사용한 Azure VM 백업** | 전체 VM | 자격 증명 모음에 백업합니다. | VM에 백업을 사용하도록 설정할 때 설치된 확장<br/><br/> 하루에 한 번 백업합니다.<br/><br/> Windows VM의 경우 앱 인식 백업이고, Linux VM의 경우 파일 일치 백업입니다. 사용자 지정 스크립트를 사용하여 Linux 머신에 대한 앱 일관성을 구성할 수 있습니다.<br/><br/> VM 또는 디스크를 복원합니다.<br/><br/>[Active Directory 도메인 컨트롤러의 백업 및 복원](active-directory-backup-restore.md)이 지원됩니다.<br><br> 온-프레미스 위치에 Azure VM을 백업할 수 없습니다.
**MARS 에이전트를 사용한 Azure VM 백업** | 파일, 폴더, 시스템 상태 | 자격 증명 모음에 백업합니다. | 하루에 세 번 백업합니다.<br/><br/> 전체 VM이 아닌 특정 파일 또는 폴더를 백업하려는 경우 MARS 에이전트는 VM 확장을 실행할 수 있습니다.
**Azure VM(DPM 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | DPM을 실행하는 Azure VM의 로컬 스토리지에 백업합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음
**Azure VM(MABS 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS를 실행하는 Azure VM의 로컬 스토리지에 백업합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음

## <a name="linux-backup-support"></a>Linux 백업 지원

Linux 머신을 백업하려는 경우 지원되는 사항은 다음과 같습니다.

**백업 유형** | **Linux(Azure 인증)**
--- | ---
**Linux를 실행하는 온-프레미스 머신 직접 백업** | 지원되지 않습니다. MARS 에이전트는 Windows 머신에만 설치할 수 있습니다.
**에이전트 확장을 사용하여 Linux를 실행하는 Azure VM 백업** | [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)를 사용하는 앱 일치 백업입니다.<br/><br/> 파일 수준 복구입니다.<br/><br/> 복구 지점 또는 디스크에서 VM을 만들어 복원합니다.
**DPM을 사용하여 Linux를 실행하는 온-프레미스 머신 백업** | Hyper-V 및 VMWare에서 일관성 있는 Linux 게스트 VM 파일 백업입니다.<br/><br/> Hyper-V 및 VMWare Linux 게스트 VM의 VM 복원입니다.
**MABS를 사용하여 Linux를 실행하는 온-프레미스 머신 백업** | Hyper-V 및 VMWare에서 일관성 있는 Linux 게스트 VM 파일 백업입니다.<br/><br/> Hyper-V 및 VMWare Linux 게스트 VM의 VM 복원입니다.
**MABS 또는 DPM을 사용하여 Linux Azure Vm 백업** | 지원되지 않습니다.

## <a name="daylight-saving-time-support"></a>일광 절약 시간 지원

Azure Backup은 Azure VM 백업을 위한 일광 절약 시간에 대한 자동 시계 조정을 지원하지 않습니다. 또한 백업 시간을 앞뒤로 이동하지 않습니다. 원하는 시간에 백업이 실행되도록 하려면 필요에 따라 백업 정책을 수동으로 수정합니다.

## <a name="disk-deduplication-support"></a>디스크 중복 제거 지원

디스크 중복 제거 지원은 다음과 같습니다.

- DPM 또는 MABS를 사용하여 Windows를 실행하는 Hyper-V VM을 백업하는 경우 온-프레미스에서 디스크 중복 제거를 지원합니다. Windows Server는 백업 스토리지로 VM에 연결된 VHD(가상 하드 디스크)에서 호스트 수준 데이터 중복 제거를 수행합니다.
- Azure에서는 백업 구성 요소에 대한 중복 제거가 지원되지 않습니다. DPM 및 MABS가 Azure에 배포되는 경우 VM에 연결된 스토리지 디스크의 중복은 제거할 수 없습니다.

## <a name="security-and-encryption-support"></a>보안 및 암호화 지원

Azure Backup은 전송 중 및 정지 상태의 데이터에 대한 암호화를 지원합니다.

### <a name="network-traffic-to-azure"></a>Azure로의 네트워크 트래픽

- 서버에서 Recovery Services 자격 증명 모음으로의 백업 트래픽은 Advanced Encryption Standard 256을 사용하여 암호화됩니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.

### <a name="data-security"></a>데이터 보안

- 백업 데이터는 암호화된 형식으로 Recovery Services 자격 증명 모음에 저장됩니다.
- MARS 에이전트를 사용하여 온-프레미스 서버에서 데이터를 백업하는 경우, Azure Backup 업로드 하기 전에 암호를 사용하여 데이터를 암호화하고 Azure Backup에서 다운로드한 후에만 암호를 해독합니다.
- Azure VM을 백업하고 있다면 가상 머신 *내에서* 암호화를 설정해야 합니다.
- Azure Backup은 Azure Disk Encryption을 지원하며, Windows 가상 머신의 BitLocker와 Linux 가상 머신의 **dm-crypt** 를 사용합니다.
- 백 엔드에서 Azure Backup는 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 사용하여 미사용 데이터를 보호합니다.

**머신** | **전송 중** | **저장**
--- | --- | ---
**온-프레미스 Windows 머신(DPM/MABS 사용 안 함)** | ![예][green] | ![예][green]
**Azure VM** | ![예][green] | ![예][green]
**DPM을 사용하는 온-프레미스 Windows 머신 또는 Azure VM** | ![예][green] | ![예][green]
**MABS를 사용하는 온-프레미스 Windows 머신 또는 Azure VM** | ![예][green] | ![예][green]

## <a name="compression-support"></a>압축 지원

백업은 다음 표에 요약된 대로 백업 트래픽의 압축을 지원합니다.

- Azure VM의 경우 VM 확장은 Azure 스토리지 계정에서 스토리지 네트워크를 통해 직접 데이터를 읽으므로 이 트래픽을 압축할 필요가 없습니다.
- DPM 또는 MABS를 사용하는 경우 백업하기 전에 데이터를 압축하여 대역폭을 절약할 수 있습니다.

**머신** | **MABS/DPM에 압축(TCP)** | **자격 증명 모음으로 압축(HTTPS)**
--- | --- | ---
**온-프레미스 Windows 머신 직접 백업** | 해당 없음 | ![예][green]
**VM 확장을 사용하여 Azure VM 백업** | 해당 없음 | 해당 없음
**온-프레미스/Azure 머신에서 MABS/DPM을 사용한 백업** | ![예][green] | ![예][green]

## <a name="retention-limits"></a>보존 제한

**설정** | **제한**
--- | ---
**보호된 인스턴스(머신 또는 워크로드)당 최대 복구 지점 수** | 9,999
**복구 지점에 대한 최대 만료 시간** | 제한 없음
**DPM/MABS에 대한 최대 백업 빈도** | SQL Server에 대해 15분마다<br/><br/> 다른 워크로드의 경우 한 시간에 한 번
**자격 증명 모음에 대한 최대 백업 빈도** | **MARS를 실행하는 온-프레미스 Windows 머신 또는 Azure VM:** 하루 세 번<br/><br/> **DPM/MABS:** 하루에 두 번<br/><br/> **Azure VM 백업:** 하루에 한 번
**복구 지점 보존** | 매일, 매주, 매월, 매년
**최대 보존 기간** | 백업 빈도에 따라 다름
**DPM/MABS 디스크의 복구 지점 수** | 파일 서버의 경우 64개, 앱 서버의 경우 448개 <br/><br/>온-프레미스 DPM에 대한 테이프 복구 지점 수는 제한되지 않음

## <a name="cross-region-restore"></a>지역 간 복원

Azure Backup은 데이터 가용성 및 복원력 기능을 강화하도록 지역 간 복원 기능을 추가해 사용자에게 보조 지역으로 데이터를 복원할 수 있는 모든 권한을 제공합니다. 이 기능을 구성하려면 [지역 간 복원 설정 문서](backup-create-rs-vault.md#set-cross-region-restore)를 확인하세요. 이 기능은 다음과 같은 관리 형식에 대해 지원됩니다.

| 백업 관리 유형 | 지원됨                                                    | 지원되는 지역 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 관리 디스크 및 관리되지 않는 디스크를 모두 사용하는 Azure VM(암호화된 Azure VM 포함)에 대해 지원됩니다. 클래식 VM에는 지원되지 않습니다. | UG 아이오와 및 UG 버지니아를 제외한 모든 Azure 공용 지역 및 소버린 지역에서 사용할 수 있습니다. |
| SQL/SAP HANA | 사용 가능      | 프랑스 중부, UG 아이오와 및 UG 버지니아를 제외한 모든 Azure 공용 지역 및 소버린 지역에서 사용할 수 있습니다. |
| MARS 에이전트/온-프레미스  | 예                                                           | 해당 없음               |
| AFS(Azure 파일 공유)                 | 예                                                           | 해당 없음               |

## <a name="resource-health"></a>리소스 상태

리소스 상태 확인은 다음 조건에서 작동합니다.

| 리소스 상태 검사    | 세부 정보    |
| --- | --- |
| **지원되는 리소스** | Recovery Services 자격 증명 모음 |
| **지원되는 지역** | 미국 동부, 미국 동부 2, 미국 중부, 미국 동부, 미국 중 북부, 미국 서 부, 미국 서 부, 미국 서 부 2, 미국 서 부 3, 캐나다 동부, 캐나다 중부, 북부, 유럽 서부, 영국 서부, 영국 남부, 프랑스 중부, 프랑스 남부, 스웨덴 중부, 스웨덴 남부, 동아시아, 남부 동아시아, 일본 동부, 일본 서 부, 대한민국 중부, 한국 남부,  오스트레일리아 동부, 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 남부, 남아프리카 북부, 남아프리카 공화국 서 부, 아랍에미리트 북부, 아랍에미리트 중부, 브라질 남부 동부, 브라질 남부, 스위스 북부, 스위스 서부, 노르웨이 동부, 노르웨이 서 부, 독일 북부, 독일 중서부, 인도 서 부, 인도 중부, 인도 남부, Jio 인도 서 부, Jio 인도 중부. |
| **지원되지 않는 지역의 경우** | 리소스 상태는 "알 수 없음"으로 표시됩니다. |


## <a name="next-steps"></a>다음 단계

- Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
