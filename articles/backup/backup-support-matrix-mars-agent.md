---
title: MARS 에이전트 지원 매트릭스
description: 이 문서에는 MARS(Microsoft Azure Recovery Services) 에이전트를 실행하는 컴퓨터를 백업할 때의 Azure Backup 지원이 요약되어 있습니다.
ms.date: 06/04/2021
ms.topic: conceptual
ms.openlocfilehash: 7b6ec2f1170857a994187c8debc5474f928f45ba
ms.sourcegitcommit: 5c1cd21464e8165b16eb8d63ab31ab7b1a8f3675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133206956"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 통한 백업 매트릭스 지원

[Azure Backup 서비스](backup-overview.md)를 사용하여 온-프레미스 컴퓨터 및 워크로드, Azure VM(가상 머신)을 백업할 수 있습니다. 이 문서에는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 컴퓨터를 백업할 때의 지원 설정 및 제한 사항이 요약되어 있습니다.

## <a name="the-mars-agent"></a>MARS 에이전트

Azure Backup은 MARS 에이전트를 사용하여 온-프레미스 컴퓨터와 Azure VM의 데이터를 Azure의 백업 Recovery Services 자격 증명 모음에 백업합니다. MARS 에이전트는 다음을 수행할 수 있습니다.

- 온-프레미스 Windows 컴퓨터에서 실행되어 Azure의 백업 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
- Windows VM에서 실행되어 자격 증명 모음에 직접 백업할 수 있습니다.
- MABS(Microsoft Azure Backup Server) 또는 System Center DPM(Data Protection Manager) 서버에서 실행됩니다. 이 시나리오에서 컴퓨터와 워크로드는 MABS 또는 DPM 서버에 백업됩니다. 그러면 MARS 에이전트가 이 서버를 Azure의 자격 증명 모음에 백업합니다.

> [!NOTE]
>Azure Backup은 DST(일광 절약 시간제)를 위한 시간 자동 조정을 지원하지 않습니다. 실제 시간과 예약된 백업 시간의 불일치를 방지하려면 일광 절약 시간제를 고려하도록 정책을 수정합니다.

백업 옵션은 에이전트가 설치된 위치에 따라 달라집니다. 자세한 내용은 [MARS 에이전트를 사용하는 Azure Backup 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)를 참조하세요. MABS 및 DPM 백업 아키텍처에 대한 자세한 내용은 [DPM 또는 MABS에 백업](backup-architecture.md#architecture-back-up-to-dpmmabs)을 참조하세요. 백업 아키텍처의 [요구 사항](backup-support-matrix-mabs-dpm.md)도 참조하세요.

**설치** | **세부 정보**
--- | ---
최신 MARS 에이전트 다운로드 | 자격 증명 모음에서 최신 버전의 에이전트를 다운로드하거나 [직접 다운로드](https://aka.ms/azurebackup_agent)할 수 있습니다.
머신에 직접 설치 | MARS 에이전트를 온-프레미스 Windows 서버 또는 [지원되는 운영 체제](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) 중 하나를 실행하는 Windows VM에 직접 설치할 수 있습니다.
백업 서버에 설치 | Azure에 백업하도록 DPM 또는 MABS를 설정할 때 MARS 에이전트를 서버에 다운로드하여 설치합니다. 백업 서버 지원 매트릭스의 [지원되는 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)에 에이전트를 설치할 수 있습니다.

> [!NOTE]
> 백업에 사용하도록 설정된 Azure VM에는 기본적으로 Azure Backup 확장 설치가 있습니다. 이 확장은 전체 VM을 백업합니다. 전체 VM이 아닌 특정 폴더와 파일을 백업하려면 MARS 에이전트를 확장과 함께 Azure VM에 설치하고 실행할 수 있습니다.
> Azure VM에서 MARS 에이전트를 실행하면 VM의 임시 스토리지에 있는 파일 또는 폴더가 백업됩니다. 파일 또는 폴더가 임시 스토리지에서 제거되거나 임시 스토리지가 제거되면 백업이 실패합니다.

## <a name="cache-folder-support"></a>캐시 폴더 지원

MARS 에이전트를 사용하여 데이터를 백업하면 에이전트에서 데이터의 스냅샷을 만들어 로컬 캐시 폴더에 저장한 후에 데이터를 Azure로 보냅니다. 캐시(스크래치) 폴더에는 몇 가지 요구 사항이 있습니다.

**Cache** | **세부 정보**
--- | ---
크기 |  캐시 폴더의 사용 가능한 공간은 전체 백업 데이터 크기의 5~10% 이상이어야 합니다.
Location | 캐시 폴더는 백업되는 컴퓨터에 로컬로 저장되어야 하며, 온라인 상태여야 합니다. 캐시 폴더는 네트워크 공유, 이동식 미디어 또는 오프라인 볼륨에 있으면 안 됩니다.
폴더 | 중복 제거된 볼륨 또는 압축되거나 스파스이거나 재분석 지점이 있는 폴더에서 캐시 폴더를 암호화해서는 안 됩니다.
위치 변경 | 백업 엔진(`net stop obengine`)을 중지하고 캐시 폴더를 새 드라이브에 복사하여 캐시 위치를 변경할 수 있습니다. (공간이 충분한지 확인). 그런 다음 **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** 아래의 두 레지스트리 항목(**Config/ScratchLocation** 및 **Config/CloudBackupProvider/ScratchLocation**)을 새 위치로 업데이트하고 엔진을 다시 시작합니다.

## <a name="networking-and-access-support"></a>네트워킹 및 액세스 지원

[!INCLUDE [Configuring network connectivity](../../includes/backup-network-connectivity.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

>[!NOTE]
> MARS 에이전트는 Windows Server Core SKU를 지원하지 않습니다.

MARS 에이전트를 사용하면 다음에서 실행되는 아래 나열된 운영 체제에서 Azure로 직접 백업할 수 있습니다.

1. 온-프레미스 Windows Server
2. Windows가 실행되는 Azure VM

운영 체제는 64비트여야 하며, 최신 서비스 팩 및 업데이트를 실행하고 있어야 합니다. 다음 표에는 이러한 운영 체제가 요약되어 있습니다.

**운영 체제** | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**
--- | --- | --- | ---
Windows 10(Enterprise, Pro, Home) | 예 | 예 |  소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인
Windows 8.1(Enterprise, Pro)| 예 |예 | 소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인
Windows 8(Enterprise, Pro) | 예 | 예 | 소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인
Windows Server 2016(Standard, Datacenter, Essentials) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0
Windows Server 2012 R2(Standard, Datacenter, Foundation, Essentials) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0
Windows Server 2012(Standard, Datacenter, Foundation) | 예 | 예 |- .NET 4.5 <br> -Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0 <br> - 배포 이미지 서비스 및 관리(DISM.exe)
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0
Windows Server 2019(Standard, Datacenter, Essentials) | 예 | 예 | - .NET 4.5 <br> - Windows PowerShell <br> - 호환되는 최신 Microsoft VC++ 재배포 가능 패키지 <br> - MMC(Microsoft Management Console) 3.0

자세한 내용은 [지원되는 MABS 및 DPM 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)를 참조하세요.

### <a name="operating-systems-at-end-of-support"></a>지원 종료 운영 체제

다음 운영 체제는 지원이 종료되며, 보호된 상태로 계속 유지하려면 운영 체제를 업그레이드하는 것이 좋습니다.

기존 약정으로 인해 운영 체제를 업그레이드할 수 없는 경우 Windows 서버를 Azure VM으로 마이그레이션하고 Azure VM 백업을 활용하여 보호된 상태를 유지하는 것이 좋습니다. Windows 서버 마이그레이션에 대한 자세한 내용은 [여기의 마이그레이션 페이지](https://azure.microsoft.com/migration/windows-server/)를 참조하세요.

온-프레미스 또는 호스트된 환경에서 운영 체제를 업그레이드하거나 Azure로 마이그레이션할 수 없는 경우 계속 보호 및 지원을 받으려면 컴퓨터의 확장된 보안 업데이트를 활성화합니다. 확장된 보안 업데이트는 특정 버전만 받을 수 있습니다. 자세한 내용은 [FAQ 페이지](https://www.microsoft.com/windows-server/extended-security-updates)를 참조하세요.

| **운영 체제**                                       | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7(Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | 예               | 예                 | 소프트웨어/모듈 요구 사항에 해당하는 서버 버전 확인 |
| Windows Server 2008 R2(Standard, Enterprise, Datacenter, Foundation) | 예               | 예                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - 호환되는 Microsoft VC++ 재배포 가능 패키지 <br>  - MMC(Microsoft Management Console) 3.0 <br>  - 배포 이미지 서비스 및 관리(DISM.exe) |
| Windows Server 2008 SP2(Standard, Datacenter, Foundation)  | 예               | 예                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - 호환되는 Microsoft VC++ 재배포 가능 패키지 <br>  - MMC(Microsoft Management Console) 3.0 <br>  - 배포 이미지 서비스 및 관리(DISM.exe) <br>  - Virtual Server 2005 기본 + KB KB948515 |

## <a name="backup-limits"></a>Backup 제한

### <a name="size-limits"></a>크기 한도

Azure Backup은 백업할 수 있는 파일 또는 폴더 데이터 원본의 크기를 제한합니다. 단일 볼륨에서 백업하는 항목은 이 표에 요약된 크기를 초과할 수 없습니다.

**운영 체제** | **크기 제한**
--- | ---
Windows Server 2012 이상 |54,400GB
Windows Server 2008 R2 SP1 |1,700GB
Windows Server 2008 SP2| 1,700GB
Windows 8 이상| 54,400GB
Windows 7| 1,700GB

### <a name="minimum-retention-limits"></a>최소 보존 한도

다음은 다양한 복구 시점에 대해 설정할 수 있는 최소 보존 기간입니다.

|복구 지점 |Duration  |
|---------|---------|
|일간 복구 지점    |   7 일      |
|주간 복구 지점     |    4주     |
|월간 복구 지점    |   3개월      |
|연간 복구 지점  |      1년   |

### <a name="other-limitations"></a>기타 제한 사항

- MARS는 단일 자격 증명 모음에 대한 이름이 동일한 여러 컴퓨터 보호를 지원하지 않습니다.

## <a name="supported-file-types-for-backup"></a>백업에 지원되는 파일 형식

**유형** | **지원**
--- | ---
암호화됨<sup>*</sup>| 지원됨.
Compressed | 지원됨.
스파스 | 지원됨.
압축 및 스파스 |지원됨.
하드 링크| 지원되지 않습니다. 건너뜀.
재분석 지점| 지원되지 않습니다. 건너뜀.
암호화 및 스파스 |지원되지 않습니다. 건너뜀.
압축된 스트림| 지원되지 않습니다. 건너뜀.
스파스 스트림| 지원되지 않습니다. 건너뜀.
OneDrive(동기화된 파일은 스파스 스트림)| 지원되지 않습니다.
DFS 복제를 사용하도록 설정된 폴더 | 지원되지 않습니다.

\* MARS 에이전트가 암호화된 파일에 액세스하는 데 필요한 인증서에 액세스할 수 있는지 확인합니다. 액세스할 수 없는 파일은 건너뜁니다.

## <a name="supported-drives-or-volumes-for-backup"></a>백업에 지원되는 드라이브 또는 볼륨

**드라이브/볼륨** | **지원** | **세부 정보**
--- | --- | ---
읽기 전용 볼륨| 지원되지 않음 | VSS(볼륨 섀도 복사본 서비스)는 쓰기 가능한 볼륨에서만 사용할 수 있습니다.
오프라인 볼륨| 지원되지 않음 |VSS는 온라인 상태인 볼륨에서만 사용할 수 있습니다.
네트워크 공유| 지원되지 않음 |볼륨이 서버에서 로컬이어야 합니다.
BitLocker 잠김 볼륨| 지원되지 않음 |볼륨의 잠금을 해제해야 백업을 시작할 수 있습니다.
파일 시스템 식별| 지원되지 않음 |NTFS만 지원됩니다.
이동식 미디어| 지원되지 않음 |모든 백업 항목 원본의 상태가 *고정* 이어야 합니다.
중복 제거된 드라이브 | 지원됨 | Azure Backup에서 중복 제거된 데이터를 일반 데이터로 변환합니다. 데이터를 최적화, 암호화, 저장하고, 자격 증명 모음으로 보냅니다.

## <a name="support-for-initial-offline-backup"></a>오프라인 초기 백업 지원

Azure Backup은 디스크를 사용하여 초기 백업 데이터를 Azure로 보내는 *오프라인 시딩* 을 지원합니다. 이 지원은 초기 백업이 TB(테라바이트) 크기 범위에 있을 가능성이 높은 경우에 유용합니다. 오프라인 백업이 지원되는 대상은 다음과 같습니다.

- MARS 에이전트를 실행하는 온-프레미스 컴퓨터의 파일 및 폴더 직접 백업
- DPM 서버 또는 MABS에서 파일 및 워크로드 백업

시스템 상태 파일에는 오프라인 백업을 사용할 수 없습니다.

## <a name="support-for-data-restoration"></a>데이터 복원 지원

Azure Backup의 [즉시 복원](backup-instant-restore-capability.md) 기능을 사용하면 자격 증명 모음에 복사하기 전에 데이터를 복원할 수 있습니다. 백업하는 컴퓨터가 .NET Framework 4.5.2 이상을 실행해야 합니다.

이전 버전의 운영 체제를 실행 중인 대상 컴퓨터에는 백업을 복원할 수 없습니다. 예를 들어 Windows 7를 실행하는 컴퓨터에서 만든 백업은 Windows 8 이상에서 복원할 수 있습니다. 그러나 Windows 8을 실행하는 컴퓨터에서 만든 백업은 Windows 7을 실행하는 컴퓨터에서 복원할 수 없습니다.

## <a name="previous-mars-agent-versions"></a>이전 MARS 에이전트 버전

다음 표에는 이전 버전의 에이전트가 다운로드 링크와 함께 나열됩니다. 최신 기능과 최적의 성능을 활용할 수 있도록 에이전트 버전을 최신 버전으로 업그레이드하는 것이 좋습니다.

**버전** | **기술 자료 문서**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | 사용할 수 없음
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>안정성과 성능이 약간 개선된 MARS 에이전트 버전에는 KB 문서가 없습니다.

## <a name="next-steps"></a>다음 단계

- [MARS 에이전트를 사용하는 백업 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)에 대해 자세히 알아봅니다.
- [MABS 또는 DPM 서버에서 MARS 에이전트를 실행](backup-support-matrix-mabs-dpm.md)할 때 지원되는 항목을 알아봅니다.
