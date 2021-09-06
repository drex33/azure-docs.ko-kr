---
title: Azure VMware Solution에 대한 Azure Backup Server 설정
description: Azure Backup Server를 사용하여 가상 머신을 백업하도록 Azure VMware Solution 환경을 설정합니다.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: fc82e10093402ffd4a026b0c9bef0d8e5a221647
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432143"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Azure VMware Solution에 대한 Azure Backup Server 설정

Azure Backup Server는 BCDR(비즈니스 연속성 및 재해 복구 개선) 전략에 기여하는 서비스입니다. Azure VMware Solution에서는 Azure Backup Server를 사용하여 VM(가상 머신) 수준 백업만 구성할 수 있습니다. 

Azure Backup Server는 백업 데이터를 다음 위치에 저장할 수 있습니다.

- **디스크**: 단기 스토리지의 경우 Azure Backup Server는 데이터를 디스크 풀에 백업합니다.
- **Azure**: 단기 및 장기 오프-프레미스 스토리지의 경우 디스크 풀에 저장된 Azure Backup Server 데이터는 Azure Backup을 사용하여 Microsoft Azure 클라우드로 백업될 수 있습니다.

Azure Backup Server를 사용하여 데이터를 원본 또는 대체 위치로 복원합니다. 이러한 방식으로 계획되었거나 예기치 않은 문제 때문에 원본 데이터를 사용할 수 없는 경우 데이터를 대체 위치로 복원할 수 있습니다.

이 문서에서는 Azure Backup Server를 사용하여 VM을 백업하도록 Azure VMware Solution 환경을 준비하는 방법을 설명합니다. 다음과 같은 단계를 안내합니다. 

> [!div class="checklist"]
> * 사용할 권장 VM 디스크 유형 및 크기를 결정합니다.
> * 복구 지점이 저장되는 Recovery Services 자격 증명 모음을 만듭니다.
> * Recovery Services 자격 증명 모음에 대한 스토리지 복제를 설정합니다.
> * Azure Backup Server에 스토리지를 추가합니다.

## <a name="supported-vmware-features"></a>지원되는 VMware 기능

- **에이전트 없는 백업:** Azure Backup Server의 경우 VM을 백업하기 위해 vCenter 또는 ESXi 서버에 에이전트를 설치할 필요가 없습니다. 대신에 IP 주소 또는 FQDN(정규화된 도메인 이름)과 VMware 서버를 인증하는 데 사용되는 로그인 자격 증명을 Azure Backup Server에 제공합니다.
- **클라우드 통합 백업:** Azure Backup Server는 디스크 및 클라우드에 대한 워크로드를 보호합니다. Azure Backup Server의 백업 및 복구 워크플로를 통해 장기 보존 및 오프사이트 백업을 관리할 수 있습니다.
- **vCenter에서 관리하는 VM을 검색 및 보호:** Azure Backup Server는 vCenter 또는 ESXi 서버에 배포된 VM을 검색하고 보호합니다. 또한 Azure Backup Server는 대규모 배포를 보호할 수 있도록 vCenter에서 관리하는 VM을 검색합니다.
- **폴더 수준 자동 보호:** vCenter를 통해 VM을 VM 폴더로 구성할 수 있습니다. Azure Backup Server는 이러한 폴더를 검색합니다. 모든 하위 폴더를 포함하여 폴더 수준에서 VM을 보호하는 데 사용할 수 있습니다. 폴더를 보호하는 경우 Azure Backup Server는 해당 폴더 내의 VM을 보호하고 나중에 추가되는 VM도 보호합니다. Azure Backup Server는 매일 새 VM을 검색하여 자동으로 보호합니다. 재귀 폴더에서 VM을 구성하는 경우 Azure Backup Server는 재귀 폴더에 배포된 새 VM을 자동으로 검색하고 보호합니다.
- **Azure Backup Server는 클러스터 내에서 vMotion되는 VM을 지속적으로 보호:** VM은 클러스터 내에서 부하 분산을 위해 vMotion되므로 Azure Backup Server는 자동으로 VM 보호를 검색하고 지속합니다.
- **필요한 파일을 더 빠르게 복구:** Azure Backup Server는 전체 VM을 복구하지 않고 Windows VM에서 파일 또는 폴더를 복구할 수 있습니다.

## <a name="limitations"></a>제한 사항

- Azure Backup Server v3의 업데이트 롤업 1을 설치해야 합니다.
- 첫 번째 Azure Backup Server 백업 전에 사용자 스냅샷을 백업할 수 없습니다. Azure Backup Server가 첫 번째 백업을 완료하면 사용자 스냅샷을 백업할 수 있습니다.
- Azure Backup Server는 통과 디스크 및 pRDM(물리적 원시 디바이스 매핑)이 포함된 VMware VM을 보호할 수 없습니다.
- Azure Backup Server는 VMware vApps를 검색하거나 보호할 수 없습니다.

Azure VMware Solution에 대한 Azure Backup Server를 설정하려면 다음 단계를 완료해야 합니다.

- 필수 구성 요소 및 환경을 설정합니다.
- Recovery Services 자격 증명 모음을 만듭니다.
- Azure Backup Server를 다운로드하고 설치합니다.
- Azure Backup Server에 스토리지를 추가합니다.

### <a name="deployment-architecture"></a>배포 아키텍처

Azure Backup Server는 Azure VMware Solution VM을 보호하기 위한 Azure IaaS(서비스 제공 인프라) VM으로 배포됩니다.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure VMware Solution VM을 보호하기 위한 Azure IaaS(서비스 제공 인프라) VM으로 배포되는 Azure Backup Server를 보여 주는 다이어그램" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure Backup Server 환경의 필수 구성 요소

Azure 환경에서 Azure Backup Server를 설치하는 경우 이 섹션의 권장 사항을 고려해야 합니다.

### <a name="azure-virtual-network"></a>Azure Virtual Network

[Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹을 구성](tutorial-configure-networking.md)해야 합니다.

### <a name="determine-the-size-of-the-vm"></a>VM 크기 결정

[Azure Portal에서 첫 번째 Windows VM 만들기](../virtual-machines/windows/quick-create-portal.md) 자습서의 지침을 따릅니다.  이전 단계에서 만든 가상 네트워크에서 VM을 만듭니다. Azure Backup Server를 실행하려면 Windows Server 2019 Datacenter의 갤러리 이미지로 시작합니다. 

표에는 각 Azure Backup Server VM 크기에서 보호되는 워크로드의 최대 수가 요약되어 있습니다. 이 정보는 작업 크기 및 변동의 정식 값을 사용한 내부 성능 및 확장 테스트를 기준으로 한 결과입니다. 실제 워크로드 크기는 더 클 수 있지만, Azure Backup Server VM에 연결된 디스크에 수용할 수 있을 것입니다.

| 최대 보호되는 워크로드 수 | 평균 작업 크기 | 평균 작업 변동(매일) | 최소 스토리지 IOPS | 권장 디스크 유형/크기      | 권장 VM 크기 |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100GB                | 순 변동 5%                   | 2,000             | 표준 HDD(디스크당 8TB 이상 크기)  | A4V2       |
| 40                      | 150GB                | 순 변동 10%                  | 4,500             | 프리미엄 SSD*(디스크당 1TB 이상 크기) | DS3_V2     |
| 60                      | 200GB                | 순 변동 10%                  | 10,500            | 프리미엄 SSD*(디스크당 8TB 이상 크기) | DS3_V2     |

\* 필요한 IOPs를 얻으려면 최소 권장 크기 이상의 디스크를 사용합니다. 디스크 크기가 작을수록 IOPs가 낮아집니다.

> [!NOTE]
> Azure Backup Server는 단일 용도의 전용 서버에서 실행하도록 설계되었습니다. 다음과 같은 컴퓨터에는 Azure Backup Server을 설치할 수 없습니다.
> * 도메인 컨트롤러로 실행되는 경우.
> * 애플리케이션 서버 역할이 설치된 경우.
> * System Center Operations Manager 관리 서버인 경우.
> * Exchange Server를 실행하는 경우.
> * 클러스터의 한 노드인 경우.

### <a name="disks-and-storage"></a>디스크 및 스토리지

Azure Backup Server는 설치용 디스크가 필요합니다. 

| 요구 사항                      | 권장 크기  |
|----------------------------------|-------------------------|
| Azure Backup Server 설치                | 설치 위치: 3GB<br />데이터베이스 파일 드라이브: 900MB<br />시스템 드라이브: SQL Server 설치용 1GB<br /><br />또한 보관할 때 Azure Backup Server가 파일 카탈로그를 임시 설치 위치에 복사하기 위한 공간이 필요합니다.      |
| 스토리지 풀용 디스크<br />(기본 볼륨 사용, 동적 디스크에 위치할 수 없음) | 보호되는 데이터 크기의 2~3배.<br />자세한 스토리지 계산은 [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)를 참조하세요.   |

새 관리되는 데이터 디스크를 기존 Azure VM에 연결하는 방법을 알아보려면 [Azure Portal을 사용하여 Windows VM에 관리되는 데이터 디스크 연결](../virtual-machines/windows/attach-managed-disk-portal.md)을 참조하세요.

> [!NOTE]
> 단일 Azure Backup Server는 스토리지 풀에 대한 120TB의 소프트 제한이 있습니다.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>로컬 디스크 및 Azure에 백업 데이터 저장

Azure에 백업 데이터를 저장하면 Azure Backup Server VM에서 백업 인프라가 감소합니다. 운영 복구(백업)의 경우, Azure Backup Server는 VM에 연결된 Azure 디스크에 백업 데이터를 저장합니다. 디스크 및 스토리지 공간이 VM에 연결되면 Azure Backup Server에서 자동으로 스토리지를 관리합니다. 스토리지의 크기는 각 Azure VM에 연결된 디스크의 수와 크기에 따라 달라집니다. Azure VM의 각 크기마다 연결할 수 있는 최대 디스크 수가 있습니다. 예를 들어 A2는 디스크 4개, A3은 디스크 8개, A4는 디스크 16개입니다. 또한 디스크의 크기와 수는 총 백업 스토리지 풀 용량을 결정합니다.

> [!IMPORTANT]
> Azure Backup Server에 연결된 디스크에서 운영 복구(백업) 데이터를 5일 넘게 유지하지 않아야 합니다. 데이터가 5일보다 오래된 경우 Recovery Services 자격 증명 모음에 저장합니다.

Azure에서 백업 데이터를 저장하려면 Recovery Services 자격 증명 모음을 만들거나 사용합니다. Azure Backup Server 워크로드를 백업하도록 준비할 때 [Recovery Services 자격 증명 모음을 구성](#create-a-recovery-services-vault)합니다. 구성되면 온라인 백업 작업이 실행될 때마다 자격 증명 모음에서 복구 지점이 생성됩니다. 각 Recovery Services 자격 증명 모음은 최대 9,999개의 복구 지점을 유지합니다. 만든 복구 지점의 수 및 유지 기간에 따라 여러 해 동안 백업 데이터를 유지할 수 있습니다. 예를 들어 매월 복구 지점을 만들고 5년 동안 유지할 수 있습니다.

> [!IMPORTANT]
> 백업 데이터를 Azure에 전송하거나 로컬로 유지하는 경우 Azure Backup Server를 Recovery Services 자격 증명 모음에 등록해야 합니다.

### <a name="scale-deployment"></a>배포 크기 조정

배포의 크기를 조정하려는 경우 다음 옵션을 사용할 수 있습니다.

- **스케일 업** - A 시리즈에서 DS3 시리즈로 Azure Backup Server VM의 크기를 높이고, 로컬 스토리지를 늘립니다.
- **데이터 오프로드** - 이전 데이터를 Azure로 보내고, Azure Backup Server 가상 머신에 연결된 스토리지에는 최신 데이터만 유지합니다.
- **스케일 아웃** - 워크로드를 보호하기 위한 Azure Backup Server 가상 머신을 더 추가합니다.

### <a name="net-framework"></a>.NET Framework

VM에 .NET Framework 3.5 SP1 이상이 설치되어 있어야 합니다.

### <a name="join-a-domain"></a>도메인에 가입

Azure Backup Server VM은 도메인에 조인되어 있어야 합니다. VM에 대한 관리자 권한이 있는 도메인 사용자가 Azure Backup Server를 설치해야 합니다.

Azure VM에 배포된 Azure Backup Server는 Azure VMware Solution의 VM에서 워크로드를 백업할 수 있습니다. 백업 작업을 사용하도록 설정하려면 워크로드가 동일한 도메인에 있어야 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 스토리지 엔터티입니다. 또한 보호되는 항목과 연결된 백업 정책도 포함합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 왼쪽 메뉴에서 **모든 서비스** 를 선택합니다.

1. **모든 서비스** 대화 상자에서 **Recovery Services** 를 입력하고 목록에서 **Recovery Services 자격 증명 모음** 을 선택합니다.

   구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.

1. **Recovery Services 자격 증명 모음** 대시보드에서 **추가** 를 선택합니다.

   **Recovery Services 자격 증명 모음** 대화 상자가 열립니다.

1. 값을 입력한 다음, **만들기** 를 선택합니다.

   - **Name**: 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다.
   - **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용** 을 선택한 다음, 드롭다운 목록에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기** 를 선택하고 이름을 입력합니다.
   - **위치**: 자격 증명 모음에 대한 지리적 지역을 선택합니다. Azure VMware Solution 가상 머신을 보호하는 자격 증명 모음을 만들려면 자격 증명 모음이 Azure VMware Solution 프라이빗 클라우드와 동일한 지역에 있어야 합니다.

   Recovery Services 자격 증명 모음을 만드는 데 어느 정도 시간이 걸릴 수 있습니다. 포털의 오른쪽 위에 있는 **알림** 영역에서 상태 알림을 모니터링합니다. 자격 증명 모음이 만들어지면 Recovery Services 자격 증명 모음 목록에서 볼 수 있습니다. 자격 증명 모음이 표시되지 않으면 **새로 고침** 을 선택합니다.


## <a name="set-storage-replication"></a>스토리지 복제 설정

스토리지 복제 옵션을 사용하면 지역 중복 스토리지(기본값)와 로컬 중복 스토리지 중에서 선택할 수 있습니다. 지역 중복 스토리지는 스토리지 계정의 데이터를 보조 지역으로 복사하여 데이터 내구성을 확보합니다. 이보다 내구성이 낮은 저렴한 옵션을 원하는 경우에는 로컬 중복 스토리지를 선택합니다. 지역 중복 스토리지 및 로컬 중복 스토리지 옵션에 대한 자세한 내용은 [Azure Storage 중복](../storage/common/storage-redundancy.md)을 참조하세요.

> [!IMPORTANT]
> Recovery Services 자격 증명 모음에 대한 **스토리지 복제 유형 로컬 중복/지역 중복** 설정 변경은 자격 증명 모음에서 백업을 구성하기 전에 수행해야 합니다. 백업을 구성한 후에는 이를 수정하는 옵션이 비활성화되어 스토리지 복제 유형을 변경할 수 없습니다.

1. **Recovery Services 자격 증명 모음** 에서 새 자격 증명 모음을 선택합니다. 

1. **설정** 아래에서 **속성** 을 선택합니다. **백업 구성** 아래에서 **업데이트** 를 선택합니다.

1. 스토리지 복제 형식을 선택하고 **저장** 을 선택합니다.

## <a name="download-and-install-the-software-package"></a>소프트웨어 패키지 다운로드 및 설치

이 섹션의 단계에 따라 소프트웨어 패키지를 다운로드, 추출 및 설치합니다.

### <a name="download-the-software-package"></a>소프트웨어 패키지 다운로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Recovery Services 자격 증명 모음이 이미 열려 있으면 다음 단계로 진행합니다. 

   >[!TIP]
   >열린 Recovery Services 자격 증명 모음이 없고 Azure Portal에 있는 경우 리소스 목록에서 **Recovery Services** > **Recovery Services 자격 증명 모음** 을 입력합니다.

1. Recovery Services 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

   선택한 자격 증명 모음 대시보드가 열립니다.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png" alt-text="자격 증명 모음 대시보드를 보여 주는 스크린샷":::

   기본적으로 **설정** 옵션이 열립니다. 이 옵션이 닫혀 있으면 **설정** 을 선택하여 엽니다.

1. **백업** 을 선택하여 **시작** 마법사를 엽니다.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png" alt-text="시작 마법사에서 선택한 Backup 옵션을 보여 주는 스크린샷":::

1. 열린 창에서

   1. **워크로드가 실행되는 위치** 메뉴에서 **온-프레미스** 를 선택합니다.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="워크로드가 실행되는 위치 및 백업할 작업에 대한 옵션을 보여 주는 스크린샷":::

   1. **백업할 항목** 메뉴에서 Azure Backup Server를 사용하여 보호할 워크로드를 선택합니다.

   1. **인프라 준비** 를 선택하여 Azure Backup Server 및 자격 증명 모음 자격 증명을 다운로드하고 설치합니다.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="인프라를 준비하는 단계를 보여 주는 스크린샷":::

1. 열리는 **인프라 준비** 창에서 다음을 수행합니다.

   1. **다운로드** 링크를 선택하여 Azure Backup Server를 설치합니다.

   1. **최신 Azure Backup Server 설치를 이미 다운로드했거나 사용 중** 을 선택한 다음 **다운로드** 를 선택하여 자격 증명 모음 자격 증명을 다운로드합니다. 이 자격 증명은 Azure Backup Server를 Recovery Services 자격 증명 모음에 등록할 때 사용합니다. 링크를 선택하면 소프트웨어 패키지를 다운로드하는 다운로드 센터로 연결됩니다.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure-2.png" alt-text="Azure Backup Server를 위해 인프라를 준비하는 단계를 보여 주는 스크린샷":::

1. 다운로드 페이지에서 모든 파일을 선택하고 **다음** 을 선택합니다.

   > [!NOTE]
   > 모든 파일을 동일한 폴더에 다운로드해야 합니다. 파일 다운로드 크기가 3GB를 넘기 때문에 다운로드를 완료하는 데 최대 60분이 걸릴 수 있습니다. 

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png" alt-text="다운로드할 Microsoft Azure Backup 파일을 보여 주는 스크린샷":::

### <a name="extract-the-software-package"></a>소프트웨어 패키지 추출

소프트웨어 패키지를 다른 서버에 다운로드한 경우 파일을 Azure Backup Server를 배포하기 위해 만든 VM에 복사합니다.

> [!WARNING]
> 설치 파일을 추출하려면 4GB 이상의 사용 가능한 공간이 필요합니다.

1. 모든 파일을 다운로드한 후 **MicrosoftAzureBackupInstaller.exe** 를 두 번 클릭하여 **Microsoft Azure Backup** 설치 마법사를 열고 **다음** 을 선택합니다.

1. 파일 압축을 풀 위치를 선택하고 **다음** 을 선택합니다.

1. **추출** 을 선택하여 추출 프로세스를 시작합니다.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/extract/03.png" alt-text="추출할 준비가 된 Microsoft Azure Backup 파일을 보여 주는 스크린샷":::

1. 압축이 풀리면 **setup.exe 실행** 옵션을 선택한 다음 **마침** 을 선택합니다.

> [!TIP]
> 소프트웨어 패키지를 추출한 폴더에서 setup.exe 파일을 찾을 수도 있습니다.

### <a name="install-the-software-package"></a>소프트웨어 패키지 설치

1. **설치** 아래의 설치 창에서 **Microsoft Azure Backup** 을 선택하여 설치 마법사를 엽니다.

1. **시작** 화면에서 **다음** 을 선택하여 **필수 구성 요소 확인** 페이지로 이동합니다.

1. 하드웨어 및 소프트웨어가 Azure Backup Server의 필수 구성 요소를 충족하는지 확인하려면 **다시 확인** 을 선택합니다. 성공적으로 충족되었으면 **다음** 을 선택합니다.

1. Azure Backup Server 설치 패키지에는 필요한 SQL Server 이진 파일이 번들로 함께 제공됩니다. 새 Azure Backup Server 설치를 시작할 때 **이 설치 프로그램으로 새 SQL Server 인스턴스 설치** 옵션을 선택합니다. 그런 다음 **확인 후 설치** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/sql/01.png" alt-text="SQL 설정 대화 상자와 이 설치 옵션이 선택된 SQL Server의 새 인스턴스 설치를 보여 주는 스크린샷":::

   > [!NOTE]
   > 자체 SQL Server 인스턴스를 사용하려는 경우 지원되는 SQL Server 버전은 SQL Server 2014 SP1 이상, 2016 및 2017입니다. 모든 SQL Server 버전은 Standard 또는 Enterprise 64비트여야 합니다. Azure Backup Server에서 사용하는 인스턴스는 로컬 전용이어야 합니다. 원격일 수 없습니다. Azure Backup Server에 기존 SQL Server 인스턴스를 사용하는 경우 설치 프로그램은 SQL Server의 명명된 인스턴스만 사용하도록 지원합니다.

   가상 머신을 다시 시작하라는 권장 사항과 함께 오류가 발생하는 경우 다시 시작하고 **다시 확인** 을 선택합니다. SQL Server 구성 문제가 발생하는 경우 SQL Server 지침에 따라 SQL Server를 다시 구성하세요. 그런 다음 기존 SQL Server 인스턴스를 사용하여 Azure Backup Server 설치 또는 업그레이드를 다시 시도합니다.

   **수동 구성**

   자체 SQL Server 인스턴스를 사용하는 경우 마스터 데이터베이스의 sysadmin 역할에 builtin\Administrators 역할을 추가해야 합니다.

   **SQL Server 2017을 사용하여 보고 서비스 구성**

   SQL Server 2017 인스턴스를 사용하는 경우 SQL SSRS(Server 2017 Reporting Services)를 수동으로 구성해야 합니다. SSRS를 구성한 후 SSRS의 **IsInitialized** 속성을 **True** 로 설정해야 합니다. **True** 로 설정하면 Azure Backup Server는 SSRS가 이미 구성되어 있다고 가정하고 SSRS 구성을 건너뜁니다.

   SSRS 구성 상태를 확인하려면 다음을 실행합니다.

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   SSRS 구성에 대해 다음 값을 사용합니다.

   * **서비스 계정**: **기본 제공 계정 사용** 은 **네트워크 서비스** 여야 합니다.
   * **웹 서비스 URL**: **가상 디렉터리** 는 **ReportServer_\<SQLInstanceName>** 이어야 합니다.
   * **데이터베이스**: **DatabaseName** 은 **ReportServer$\<SQLInstanceName>** 이어야 합니다.
   * **웹 포털 URL**: **가상 디렉터리** 는 **Reports_\<SQLInstanceName>** 이어야 합니다.

   SSRS 구성에 대해 [자세히 알아봅니다](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode).

   > [!NOTE]
   > Azure Backup Server용 데이터베이스로 사용되는 SQL Server에 대한 라이선스는 [Microsoft OST(온라인 서비스 사용 약관)](https://www.microsoft.com/licensing/product-licensing/products)를 통해 관리됩니다. OST에 따라 Azure Backup Server와 함께 제공되는 SQL Server만 Azure Backup Server용 데이터베이스로 사용합니다.

1. 설치가 완료되면 **다음** 을 선택합니다.

1. Microsoft Azure Backup Server 파일을 설치할 위치를 입력하고 **다음** 을 선택합니다.

   > [!NOTE]
   > Azure에 백업하는 데 스크래치 위치가 필요합니다. 스크래치 위치는 클라우드로 백업하려는 데이터의 5% 이상이어야 합니다. 디스크 보호의 경우 설치가 완료된 후 별도의 디스크를 구성해야 합니다. 스토리지 풀에 관한 자세한 내용은 [스토리지 풀 및 디스크 스토리지 구성](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))을 참조하세요.

   :::image type="content" source="../backup/media/backup-azure-microsoft-azure-backup/space-screen.png" alt-text="SQL Server 설정을 보여 주는 스크린샷":::

1. 제한된 로컬 사용자 계정에 강력한 암호를 제공하고 **다음** 을 선택합니다.

1. 업데이트를 확인하기 위해 Microsoft 업데이트를 사용할지 여부를 선택하고 **다음** 을 선택합니다.

   > [!NOTE]
   > Windows 업데이트를 Windows 및 Azure Backup Server와 같은 다른 제품에 대한 보안 및 중요 업데이트를 제공하는 Microsoft 업데이트로 리디렉션하는 것이 좋습니다.

1. **설정 요약** 을 검토하고 **설치** 를 선택합니다.

   설치는 단계적으로 발생합니다. 
   - 첫 번째 단계에서는 Microsoft Azure Recovery Services 에이전트를 설치합니다.
   - 두 번째 단계에서는 인터넷 연결을 확인합니다. 사용할 수 있는 경우 설치를 계속할 수 있습니다. 사용할 수 없는 경우 프록시 정보를 제공하여 인터넷에 연결해야 합니다. 
   - 마지막 단계에서는 필수 구성 요소 소프트웨어를 확인합니다. 설치되지 않은 경우 모든 누락된 소프트웨어가 Microsoft Azure Recovery Services 에이전트와 함께 설치됩니다.

1. **찾아보기** 를 선택하여 Recovery Services 자격 증명 모음에 가상 머신을 등록하기 위한 자격 증명 모음 자격 증명을 찾은 후 **다음** 을 선택합니다.

1. Azure와 프레미스 간에 전송되는 데이터를 암호화 또는 암호 해독할 암호를 선택합니다.

   > [!TIP]
   > 자동으로 암호를 생성하거나 최소 16자인 암호를 제공할 수 있습니다.

1. 암호를 저장할 위치를 입력하고 **다음** 을 선택하여 서버를 등록합니다.

   > [!IMPORTANT]
   > 암호는 로컬 서버가 아닌 안전한 위치에 암호를 저장합니다. Azure Key Vault를 사용하여 암호를 저장하는 것이 가장 좋습니다.

   Microsoft Azure Recovery Services 에이전트 설치가 완료되면 설치 단계가 SQL Server 및 Azure Backup Server 구성 요소의 설치 및 구성으로 이동합니다.

1. 설치 단계가 완료되면 **닫기** 를 선택합니다.

### <a name="install-update-rollup-1"></a>업데이트 롤업 1 설치

워크로드를 보호하려면 반드시 Azure Backup Server v3용 업데이트 롤업 1을 설치해야 합니다.  [기술 자료 문서](https://support.microsoft.com/en-us/help/4534062/)에서 버그 수정 사항 및 설치 지침을 찾을 수 있습니다.

## <a name="add-storage-to-azure-backup-server"></a>Azure Backup Server에 스토리지 추가

Azure Backup Server v3은 다음과 같은 이점을 제공하는 Modern Backup Storage를 지원합니다.

-  스토리지 비용 50% 절감
-  3배 더 빠른 백업
-  보다 효율적인 스토리지
-  워크로드 인식 스토리지

### <a name="volumes-in-azure-backup-server"></a>Azure Backup Server의 볼륨

아직 추가하지 않은 경우 Azure Backup Server VM의 필요한 스토리지 용량을 가진 데이터 디스크를 추가합니다.

Azure Backup Server v3은 스토리지 볼륨만 허용합니다. 볼륨을 추가하면 Azure Backup Server에서는 볼륨 형식을 Modern Backup Storage에 필요한 ReFS(Resilient File System)로 지정합니다.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Azure Backup Server 디스크 스토리지에 볼륨 추가

1. **관리** 창에서 스토리지를 다시 검사한 다음 **추가** 를 선택합니다. 

1. 스토리지 풀에 추가하는 데 사용 가능한 볼륨 중에서 선택합니다. 

1. 사용 가능한 볼륨을 추가한 후 해당 볼륨에 식별 이름을 지정하면 관리하는 데 도움이 될 수 있습니다. 

1. Azure Backup Server에서 Modern Backup Storage의 이점을 활용할 수 있도록 **확인** 을 선택하여 이러한 볼륨을 ReFS로 포맷합니다.


## <a name="next-steps"></a>다음 단계

Azure VMware Solution에 대해 Azure Backup Server를 설정하는 방법을 알아봤으므로 이제 다음에 대해 알아보세요.

- [Azure VMware Solution VM에 대한 백업 구성](backup-azure-vmware-solution-virtual-machines.md).
- [Azure Security Center 통합을 사용하여 Azure VMware Solution VM 보호](azure-security-integration.md)