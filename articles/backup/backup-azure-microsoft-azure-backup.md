---
title: Azure Backup Server를 사용하여 워크로드 백업
description: 이 문서에서는 MABS(Microsoft Azure Backup Server)를 사용하여 워크로드를 보호 및 백업하기 위한 환경을 준비하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: adc87847900167299c9a2473079237e069782b48
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108769510"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Azure Backup Server 설치 및 업그레이드

> [!div class="op_single_selector"]
>
> * [Azure Backup 서버](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> 적용 대상: MABS v3. (MABS v2는 더 이상 지원되지 않습니다. MABS v3 이전 버전을 사용하는 경우 최신 버전으로 업그레이드하세요.)

이 문서는 MABS(Microsoft Azure Backup Server)를 사용하여 워크로드를 백업하기 위한 환경을 준비하는 방법을 설명합니다. Azure Backup Server로 Hyper-V VM, Microsoft SQL Server, SharePoint Server, 단일 콘솔의 Microsoft Exchange 및 Windows 클라이언트와 같은 애플리케이션 워크로드를 보호할 수 있습니다.

> [!NOTE]
> Azure Backup Server는 이제 VMware VM을 보호하고 개선된 보안 기능을 제공할 수 있습니다. 아래 섹션에 설명된 대로 제품 및 최신 Azure Backup 에이전트를 설치합니다. Azure Backup Server를 사용하여 VMware 서버를 백업하는 방법에 대한 자세한 내용은 [Azure Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md) 문서를 참조하세요. 보안 기능에 대한 자세한 내용은 [Azure Backup 보안 기능 설명서](backup-azure-security-feature.md)를 참조하세요.
>
>

Azure VM에 배포된 MABS는 Azure의 VM을 백업할 수 있지만 백업 작업을 사용하려면 동일한 도메인에 있어야 합니다. Azure VM을 지원하는 프로세스는 VM의 온-프레미스 백업과 동일하지만 Azure에 MABS를 배포하는 경우 몇 가지 제한 사항이 있습니다. 제한 사항에 관한 자세한 내용은 [Azure 가상 머신으로 DPM](/system-center/dpm/install-dpm#setup-prerequisites)을 참조하세요.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [Resource Manager 및 클래식](../azure-resource-manager/management/deployment-models.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 리소스 관리자 모델을 사용하여 배포된 VM을 복원하기 위한 정보 및 절차를 제공합니다.
>
>

Azure Backup 서버는 DPM(Data Protection Manager)에서 대부분의 워크로드 백업 기능을 상속합니다. 이 문서는 공유 기능 중 일부를 설명하기 위해 DPM 설명서를 링크합니다. Azure Backup Server는 DPM으로 대부분의 동일한 기능을 공유하지만 Azure Backup Server는 테이프에 백업 및 System Center와 통합하지 않습니다.

## <a name="choose-an-installation-platform"></a>설치 플랫폼 선택

Azure Backup 서버를 작동하고 실행하는 첫 번째 단계는 Windows Server를 설정하는 것입니다. 서버는 Azure 또는 온-프레미스에 있을 수 있습니다.

* 온-프레미스 워크로드를 보호하려면 MABS 서버가 온-프레미스에 있고 도메인에 연결되어 있어야 합니다.
* Azure VM에서 실행되는 워크로드를 보호하려면 Azure VM으로 실행되는 MABS 서버가 Azure에 있고 도메인에 연결되어 있어야 합니다.

### <a name="using-a-server-in-azure"></a>Azure에서 서버 사용

Azure Backup Server를 실행하기 위한 서버를 선택할 때 Windows Server 2016 Datacenter 또는 Windows Server 2019 Datacenter의 갤러리 이미지로 시작하는 것이 좋습니다. [Azure 포털에서 첫 번째 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)문서는 Azure를 사용한 경험이 없는 경우 Azure에서 권장된 가상 머신 시작에 대한 자습서를 제공합니다. 서버 VM(가상 머신)에 대한 권장 최소 요구 사항은 4코어 및 8GB RAM이 있는 Standard_A4_v2입니다.

Azure Backup 서버를 사용하여 워크로드를 보호하는 데는 미묘한 많은 차이가 있습니다. [MABS용 보호 매트릭스](./backup-mabs-protection-matrix.md)는 이와 같은 미묘한 차이를 설명하는 데 도움이 됩니다. 컴퓨터를 배포하기 전에 이 문서를 완전히 읽어보세요.

### <a name="using-an-on-premises-server"></a>온-프레미스 서버 사용

Azure에서 기본 서버를 실행하지 않을 경우 Hyper-V VM, VMware VM 또는 실제 호스트에서 서버를 실행할 수 있습니다. 서버 하드웨어에 대한 권장 최소 요구 사항은 2코어 및 8GB RAM입니다. 지원되는 운영 체제는 다음 표에 나열되어 있습니다.

| 운영 체제 | 플랫폼 | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64비트 |Standard, Datacenter, Essentials |
| Windows Server 2016 및 최신 SP |64비트 |Standard, Datacenter, Essentials  |

Windows Server 중복 제거를 사용하여 DPM 스토리지를 중복 제거할 수 있습니다. [DPM 및 중복 제거](/system-center/dpm/deduplicate-dpm-storage) 가 Hyper-V VM에 배포될 때 함께 작동하는 방법에 대해 자세히 알아보세요.

> [!NOTE]
> Azure Backup Server는 단일 용도의 전용 서버에서 실행하도록 설계되었습니다. Azure Backup Server를 다음 항목에 설치할 수 없습니다.
>
> * 도메인 컨트롤러로 실행하는 컴퓨터
> * 애플리케이션 서버 역할이 설치된 컴퓨터
> * System Center Operations Manager 관리 서버인 컴퓨터
> * Exchange Server를 실행하는 컴퓨터
> * 클러스터의 한 노드인 컴퓨터
>
> Windows Server Core 또는 Microsoft Hyper-V Server에서는 Azure Backup Server 설치가 지원되지 않습니다.

항상 Azure Backup Server를 도메인에 가입시킵니다. 배포 후 기존 Azure Backup 서버 컴퓨터를 새 도메인으로 이동하는 것은 *지원되지 않습니다*.

백업 데이터를 Azure에 전송하거나 로컬로 유지하는 경우 Azure Backup Server를 Recovery Services 자격 증명 모음에 등록해야 합니다.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>스토리지 복제 설정

스토리지 복제 옵션을 사용하면 지역 중복 스토리지와 로컬 중복 스토리지 중에서 선택할 수 있습니다. 기본적으로 Recovery Services 자격 증명 모음은 지역 중복 스토리지를 사용합니다. 이 자격 증명 모음이 기본 자격 증명 모음인 경우 스토리지 옵션을 지역 중복 스토리지 상태로 둡니다. 오래 지속되지 않는 저렴한 옵션을 원하는 경우에는 로컬 중복 스토리지를 선택합니다. [지역 중복](../storage/common/storage-redundancy.md#geo-redundant-storage), [로컬 중복](../storage/common/storage-redundancy.md#locally-redundant-storage) 및 [영역 중복](../storage/common/storage-redundancy.md#zone-redundant-storage) 스토리지 옵션에 대한 자세한 내용은 [Azure Storage 복제 개요](../storage/common/storage-redundancy.md)를 참조하세요.

스토리지 복제 설정을 편집하려면

1. **Recovery Services 자격 증명 모음** 창에서 새 자격 증명 모음을 선택합니다. **설정** 섹션에서 **속성** 을 선택합니다.
2. **속성** 의 **백업 구성** 에서 **업데이트** 를 선택합니다.

3. 스토리지 복제 형식을 선택하고 **저장** 을 선택합니다.

     ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>소프트웨어 패키지

### <a name="downloading-the-software-package"></a>소프트웨어 패키지 다운로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Recovery Services 자격 증명 모음이 이미 열려 있으면 3단계를 진행합니다. Recovery Services 자격 증명 모음이 열려 있지 않지만 Azure Portal에 있는 경우 주 메뉴에서 **찾아보기** 를 선택합니다.

   * 리소스 목록에서 **Recovery Services** 를 입력합니다.
   * 입력을 시작하면 목록이 입력에 따라 필터링됩니다. **Recovery Services 자격 증명 모음** 이 표시되면 이를 선택합니다.

     ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Recovery Services 자격 증명 모음의 목록이 표시됩니다.
   * Recovery Services 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

     선택한 자격 증명 모음 대시보드가 열립니다.

     ![자격 증명 모음 대시보드](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. **설정** 창이 기본적으로 열립니다. 닫혀 있는 경우 **설정** 을 선택하여 설정 창을 엽니다.

    ![설정 창](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. **Backup** 을 선택하여 시작 마법사를 엽니다.

    ![백업 시작](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    **백업 시작** 창이 열리면, **Backup 목표** 가 자동으로 선택됩니다.

    ![Backup-목표-기본-열기](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. **Backup 목표** 창의 **작업이 실행되는 위치** 메뉴에서 **온-프레미스** 를 선택합니다.

    ![온-프레미스 및 목표 워크로드](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    **무엇을 백업하시겠습니까?** 드롭다운 메뉴에서 Azure Backup Server를 사용하여 보호하려는 작업을 선택하고 **확인** 을 선택합니다.

    **백업 시작** 마법사는 **인프라 준비** 옵션으로 전환되어 작업을 Azure로 백업합니다.

   > [!NOTE]
   > 파일 및 폴더만 백업하려는 경우 Azure Backup 에이전트를 사용하고 [소개: 파일 및 폴더 백업](./backup-windows-with-mars-agent.md) 문서의 지침을 따르는 것이 좋습니다. 파일과 폴더 이상을 보호하려고 하거나 향후 보호 요구를 확장하려는 경우 해당 워크로드를 선택합니다.
   >
   >

    ![시작 마법사 변경](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. 열린 **인프라 준비** 창에서 Azure Backup Server 설치 및 자격 증명 모음 자격 증명 다운로드의 **다운로드** 링크를 선택합니다. Recovery Services 자격 증명 모음에 Azure Backup Server를 등록하는 동안 자격 증명 모음 자격 증명을 사용합니다. 소프트웨어 패키지를 다운로드할 수 있는 다운로드 센터로 연결됩니다.

    ![Azure Backup 서버에 대한 인프라 준비](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. 모든 파일을 선택하고 **다음** 을 선택합니다. Microsoft Azure Backup 다운로드 페이지에서 들어오는 모든 파일을 다운로드하고 모든 파일을 동일한 폴더에 배치합니다.

    ![다운로드 센터 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    모든 파일의 다운로드 크기가 >3GB이기 때문에 10Mbps 다운로드 링크에서 다운로드를 완료하는 데 최대 60분이 걸릴 수 있습니다.

### <a name="extracting-the-software-package"></a>소프트웨어 패키지 추출

모든 파일을 다운로드한 후에 **MicrosoftAzureBackupInstaller.exe** 를 선택합니다. 사용자가 지정한 위치에 설치 파일을 추출하는 **Microsoft Azure Backup 설정 마법사** 가 시작됩니다. 마법사를 계속 진행하고 **추출** 단추를 선택하여 추출 프로세스를 시작합니다.

> [!WARNING]
> 설치 파일을 추출하려면 4GB 이상의 사용 가능한 공간이 필요합니다.
>
>

![설치할 파일 압축 풀기 설치](./media/backup-azure-microsoft-azure-backup/extract/03.png)

추출 프로세스가 완료되면 새로 추출한 *setup.exe* 를 시작할 상자를 선택하여 Microsoft Azure Backup Server 설치를 시작하고 **마침** 단추를 선택합니다.

### <a name="installing-the-software-package"></a>소프트웨어 패키지 설치

1. **Microsoft Azure Backup** 을 선택하여 설치 마법사를 시작합니다.

    ![Microsoft Azure Backup 설정 마법사](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. 시작 화면에서 **다음** 단추를 선택합니다. 그러면 *필수 구성 요소 확인* 섹션으로 이동합니다. 이 화면에서 **확인** 을 선택하여 Azure Back Server에 대한 하드웨어 및 소프트웨어 필수 구성 요소가 충족되었는지 확인합니다. 모든 필수 조건이 성공적으로 충족되면 머신이 요구 사항을 만족한다는 것을 나타내는 메시지가 표시됩니다. **다음** 단추를 선택합니다.

    ![Azure Backup 서버 - 시작 및 필수 조건 확인](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Azure Backup Server 설치 패키지는 필요한 적절한 SQL Server 이진 파일이 번들로 함께 제공됩니다. 새 Azure Backup Server 설치를 시작할 때 **이 설치 프로그램으로 새 SQL Server 인스턴스 설치** 옵션을 선택하고 **확인 후 설치** 단추를 선택합니다. 필수 구성 요소가 성공적으로 설치되면 **다음** 을 선택합니다.

    >[!NOTE]
    >고유한 SQL Server를 사용하려는 경우 지원되는 SQL Server 버전은 SQL Server 2014 SP1 이상, 2016 및 2017입니다.  모든 SQL Server 버전은 Standard 또는 Enterprise 64비트여야 합니다.
    >Azure Backup Server는 원격 SQL Server 인스턴스에서 작동하지 않습니다. Azure Backup 서버에서 사용되는 인스턴스는 로컬이어야 합니다. MABS에 기존 SQL 서버를 사용하는 경우 MABS 설정은 SQL 서버의 *명명된 인스턴스* 사용만을 지원합니다.

    ![Azure Backup 서버 - SQL 확인](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    머신을 다시 시작하라는 권장 사항과 함께 실패가 발생한 경우 작업을 수행하고 **다시 확인** 을 선택합니다. SQL 구성 문제가 있는 경우 SQL 지침에 따라 SQL을 다시 구성하고 SQL의 기존 인스턴스를 사용하여 MABS 설치/업그레이드를 다시 시도합니다.

   **수동 구성**

   SQL의 사용자 고유 인스턴스를 사용하는 경우 builtin\Administrators를 마스터 DB에 대한 sysadmin 역할에 추가했는지 확인합니다.

    **SQL 2017을 사용하여 SSRS 구성**

    SQL 2017의 고유한 인스턴스를 사용하는 경우 수동으로 SSRS를 구성해야 합니다. SSRS 구성 후 SSRS의 *IsInitialized* 속성이 *True* 로 설정되었는지 확인합니다. True로 설정하면 MABS는 SSRS가 이미 구성되어 있다고 가정하고 SSRS 구성을 건너뜁니다.

    SSRS 구성에 대해 다음 값을 사용합니다.
    * 서비스 계정: ‘기본 제공 계정 사용’은 네트워크 서비스여야 합니다.
    * 웹 서비스 URL: ‘가상 디렉터리’는 ReportServer_\<SQLInstanceName>이어야 합니다.
    * 데이터베이스: DatabaseName은 ReportServer$\<SQLInstanceName>이어야 합니다.
    * 웹 포털 URL: ‘가상 디렉터리’는 Reports_\<SQLInstanceName>이어야 합니다.

    SSRS 구성에 대해 [자세히 알아봅니다](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode).

    > [!NOTE]
    > MABS의 데이터베이스로 사용되는 SQL Server용 라이선스는 [Microsoft OST(온라인 서비스 사용 약관)](https://www.microsoft.com/licensing/product-licensing/products)를 통해 관리됩니다. OST에 따라 MABS와 함께 제공되는 SQL Server는 MABS의 데이터베이스로만 사용할 수 있습니다.

4. Microsoft Azure Backup 서버 파일을 설치할 위치를 입력하고 **다음** 을 선택합니다.

    ![파일 설치 위치 제공](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    스크래치 위치는 Azure에 백업에 대한 요구 사항입니다. 스크래치 위치가 클라우드로 백업할 계획된 데이터의 5%인지 확인하세요. 디스크 보호를 위해 별도 디스크가 설치를 완료하면 구성되어야 합니다. 스토리지 풀에 대한 자세한 내용은 [데이터 스토리지 준비](/system-center/dpm/plan-long-and-short-term-data-storage)를 참조하세요.

    디스크 스토리지의 용량 요구 사항은 주로 보호된 데이터의 크기, 일별 복구 지점 크기, 예상 볼륨 데이터 증가율 및 보존 범위 목표에 따라 달라집니다. 디스크 스토리지를 보호된 데이터의 두 배 크기로 만드는 것이 좋습니다. 이렇게 하면 일별 복구 지점 크기를 보호된 데이터 크기의 10%와 10일의 보존 범위로 가정합니다. 크기를 정확하게 예측하려면 [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)를 검토합니다. 

5. 제한된 로컬 사용자 계정에 강력한 암호를 제공하고 **다음** 을 선택합니다.

    ![강력한 암호 제공](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. *Microsoft 업데이트* 를 사용할지 선택하여 업데이트를 확인하고 **다음** 을 선택합니다.

   > [!NOTE]
   > Windows 업데이트에 Microsoft 업데이트를 리디렉션하는 것이 좋으며 이는 Windows 및 Microsoft Azure Backup 서버와 같은 다른 제품에 대한 보안 및 중요 업데이트를 제공합니다.
   >
   >

    ![Microsoft Update 옵트인](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. *설정 요약* 을 검토하고 **설치** 를 선택합니다.

    ![설정 요약](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. 설치는 단계적으로 발생합니다. 첫 번째 단계에서 Microsoft Azure Recovery Services 에이전트가 서버에 설치됩니다. 또한 마법사가 인터넷 연결을 확인합니다. 인터넷이 연결되어 있는 경우 설치를 계속할 수 있습니다. 그렇지 않은 경우 인터넷에 연결하는 데 필요한 프록시 정보를 제공해야 합니다.

    다음 단계는 Microsoft Azure Recovery Services 에이전트를 구성하는 것입니다. 구성의 일부로 Recovery Services 자격 증명 모음에 머신을 등록하려면 자격 증명 모음 자격 증명을 제공해야 합니다. 또한 Azure 및 프레미스 간에 전송된 데이터를 암호화/암호 해독할 암호를 제공합니다. 자동으로 암호를 생성하거나 최소 16자인 고유의 암호를 제공할 수 있습니다. 에이전트가 구성될 때까지 마법사를 계속 진행합니다.

    ![서버 등록 마법사](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure Backup 서버 등록이 완료되면 전체 설치 마법사는 SQL Server 및 Azure Backup 서버 구성 요소의 설치 및 구성으로 진행됩니다. SQL Server 구성 요소 설치가 완료되면 Azure Backup 서버 구성 요소가 설치됩니다.

    ![Azure Backup Server 설치 프로세스](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

설치 단계가 완료되면 제품의 바탕 화면 아이콘도 만들어집니다. 제품을 시작하려면 아이콘을 두 번 클릭합니다.

### <a name="add-backup-storage"></a>백업 스토리지 추가

첫 번째 백업 복사본은 Azure Backup 서버 컴퓨터에 연결된 스토리지에 보관됩니다. 디스크 추가에 대한 자세한 내용은 [스토리지 풀 및 디스크 스토리지 구성](./backup-mabs-add-storage.md)을 참조하세요.

> [!NOTE]
> 데이터를 Azure에 전송하려는 경우에도 백업 스토리지를 추가해야 합니다. Azure Backup 서버의 현재 아키텍처에서 Azure Backup 자격 증명 모음은 데이터의 *두 번째* 복사본을 보유하고 로컬 스토리지에서는 첫 번째(및 필수) 백업 복사본을 보유합니다.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Data Protection Manager 보호 에이전트 설치 및 업데이트

MABS에서는 System Center Data Protection Manager 보호 에이전트를 사용합니다. 보호 서버에서 보호 에이전트를 설치하는 [단계는 다음과 같습니다](/system-center/dpm/deploy-dpm-protection-agent).

다음 섹션에서는 클라이언트 컴퓨터에 대한 보호 에이전트를 업데이트하는 방법을 설명합니다.

1. Backup Server 관리자 콘솔에서 **관리** > **에이전트** 를 선택합니다.

2. 표시 창에서 보호 에이전트를 업데이트할 클라이언트 컴퓨터를 선택합니다.

   > [!NOTE]
   > **에이전트 업데이트** 열에는 각 보호된 컴퓨터에 보호 에이전트 업데이트를 사용할 수 있는 시기가 표시됩니다. **작업** 창에서 **업데이트** 작업은 보호된 컴퓨터가 선택되고 업데이트가 사용 가능한 경우에만 사용할 수 있습니다.
   >
   >

3. 선택된 컴퓨터에 업데이트된 보호 에이전트를 설치하려면 **작업** 창에서 **업데이트** 를 선택합니다.

4. 네트워크에 연결되지 않은 클라이언트 컴퓨터의 경우 컴퓨터가 네트워크에 연결될 때까지 **에이전트 상태** 열에는 **업데이트 보류 중** 상태가 표시됩니다.

   클라이언트 컴퓨터가 네트워크에 연결된 후 클라이언트 컴퓨터에 대한 **에이전트 업데이트** 열에는 **업데이트 중** 상태가 표시됩니다.

## <a name="move-mabs-to-a-new-server"></a>MABS를 새 서버로 이동

스토리지를 유지하면서 MABS를 새 서버로 이동해야 하는 경우 단계는 다음과 같습니다. 모든 데이터가 Modern Backup Storage에 있는 경우에만 수행할 수 있습니다.

  > [!IMPORTANT]
  >
  > * 새 서버 이름은 원래 Azure Backup Server 인스턴스와 같은 이름이어야 합니다. 이전 스토리지 풀 및 MABS 데이터베이스(DPMDB)를 사용하여 복구 지점을 유지하려는 경우 새 Azure Backup Server 인스턴스의 이름을 변경할 수 없습니다.
  > * MABS 데이터베이스(DPMDB)의 백업이 있어야 합니다. 데이터베이스를 복원해야 합니다.

1. 표시 창에서 보호 에이전트를 업데이트할 클라이언트 컴퓨터를 선택합니다.
2. 원래 Azure Backup 서버를 종료하거나 오프라인으로 전환합니다.
3. Active Directory에서 머신 계정을 다시 설정합니다.
4. 새 머신에 Server 2016을 설치하고 원래 Azure Backup Server와 동일한 머신 이름으로 지정합니다.
5. 도메인에 조인합니다.
6. Azure Backup Server V3 이상 설치(MABS 스토리지 풀 디스크를 이전 서버에서 이동 및 가져오기)
7. 1단계에서 가져온 DPMDB를 복원합니다.
8. 스토리지를 원래 백업 서버에서 새 서버로 연결합니다.
9. SQL에서 DPMDB를 복원합니다.
10. 새 서버에서 관리자 권한으로 CMD를 실행합니다. Microsoft Azure Backup 설치 위치 및 bin 폴더로 이동합니다.

    경로 예제: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Azure Backup에 연결하려면 `DPMSYNC -SYNC`를 실행합니다.

    이전 항목을 이동하는 대신 DPM 스토리지 풀에 **새** 디스크를 추가했다면 `DPMSYNC -Reallocatereplica`를 실행합니다.

## <a name="network-connectivity"></a>네트워크 연결

Azure Backup 서버가 Azure Backup 서비스에 연결되어야 제품이 제대로 작동합니다. 컴퓨터가 Azure에 연결되어 있는지 여부를 확인하려면 Azure Backup 서버 PowerShell 콘솔에서 ```Get-DPMCloudConnection``` cmdlet을 사용합니다. cmdlet의 출력이 TRUE인 경우 연결되어 있고 그렇지 않으면 연결되지 않은 것입니다.

이와 동시에 Azure 구독은 정상 상태여야 합니다. 구독 상태를 확인하고 관리하려면 [구독 포털](https://account.windowsazure.com/Subscriptions)에 로그인합니다.

Azure 연결 및 Azure 구독 상태를 알고 있다면 아래 표를 사용하여 제공된 백업/복원 기능에 미치는 영향을 알아볼 수 있습니다.

| 연결 상태 | Azure 구독 | Azure에 백업 | 디스크에 백업 | Azure에서 복구 | 디스크에서 복구 |
| --- | --- | --- | --- | --- | --- |
| 연결됨 |Active |허용됨 |허용됨 |허용됨 |허용됨 |
| 연결됨 |만료됨 |중지됨 |중지됨 |허용됨 |허용됨 |
| 연결됨 |프로비전 해제됨 |중지됨 |중지됨 |중지되고 Azure 복구 지점 삭제됨 |중지됨 |
| 손실된 연결 > 15일 |Active |중지됨 |중지됨 |허용됨 |허용됨 |
| 손실된 연결 > 15일 |만료됨 |중지됨 |중지됨 |허용됨 |허용됨 |
| 손실된 연결 > 15일 |프로비전 해제됨 |중지됨 |중지됨 |중지되고 Azure 복구 지점 삭제됨 |중지됨 |

### <a name="recovering-from-loss-of-connectivity"></a>연결 끊김 복구

머신의 인터넷 액세스가 제한된 경우 머신 또는 프록시의 방화벽 설정이 다음 URL 및 IP 주소를 허용하는지 확인합니다.

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18

ExpressRoute Microsoft 피어링을 사용하는 경우 다음 서비스/지역을 선택합니다.

* Azure Active Directory(12076:5060)
* Microsoft Azure 지역(Recovery Services 자격 증명 모음의 위치에 따름)
* Azure Storage(Recovery Services 자격 증명 모음의 위치에 따름)

자세한 내용은 [ExpressRoute 라우팅 요구 사항](../expressroute/expressroute-routing.md)을 참조하세요.

Azure Backup 서버 컴퓨터에 Azure에 대한 연결이 복원되면 수행할 수 있는 작업은 Azure 구독 상태에 따라 결정됩니다. 컴퓨터가 "연결된" 경우 허용되는 작업에 대한 세부 정보가 위의 표에 나와 있습니다.

### <a name="handling-subscription-states"></a>구독 상태 처리

Azure 구독을 *만료됨* 또는 *프로비전 해제됨* 상태에서 *활성* 상태로 전환할 수 있습니다. 그러나 이 작업은 *활성* 상태가 아닌 동안 제품 동작에 일부 영향을 미칩니다.

* *프로비전 해제됨* 구독은 프로비전 해제 기간 동안 기능이 손실됩니다. *활성* 으로 전환될 때 제품의 백업/복원 기능을 다시 사용할 수 있습니다. 또한 로컬 디스크의 백업 데이터가 충분한 보존 기간으로 유지되었다면 검색이 가능합니다. 그러나 Azure의 백업 데이터는 구독이 *프로비전 해제됨* 상태로 전환되면 회복할 수 없는 상태로 손실됩니다.
* *만료됨* 구독은 다시 *활성* 상태로 되기 전까지 기능을 상실합니다. 구독이 *만료됨* 상태인 기간에 예약된 모든 백업은 실행되지 않습니다.

## <a name="upgrade-mabs"></a>MABS 업그레이드

다음 절차를 사용하여 MABS를 업그레이드합니다.

### <a name="upgrade-from-mabs-v2-to-v3"></a>MABS V2에서 V3으로 업그레이드

> [!NOTE]
>
> MABS V2는 MABS V3 설치를 위한 필수 구성 요소는 아닙니다. 그러나 MABS V2에서만 MABS V3으로 업그레이드할 수 있습니다.

다음 단계를 사용하여 MABS를 업그레이드합니다.

1. MABS V2에서 MABS V3으로 업그레이드하려면 필요한 경우 OS를 Windows Server 2016 또는 Windows Server 2019로 업그레이드합니다.

2. 서버를 업그레이드합니다. 단계는 [설치](#install-and-upgrade-azure-backup-server)와 비슷합니다. 그러나 SQL 설정의 경우 SQL 인스턴스를 SQL 2017로 업그레이드하거나 SQL 서버 2017의 사용자 고유 인스턴스를 사용하는 옵션이 있습니다.

   > [!NOTE]
   >
   > SQL 인스턴스를 업그레이드하는 동안 종료하지 마세요. 종료하면 SQL reporting 인스턴스가 제거되므로 MABS를 다시 업그레이드하려고 시도하지 않습니다.

   > [!IMPORTANT]
   >
   >  SQL 2017 업그레이드의 일환으로 SQL 암호화 키를 백업하고 보고 서비스를 제거합니다. SQL 서버 업그레이드 후 보고 서비스(14.0.6827.4788)가 설치되고 암호화 키가 복원됩니다.
   >
   > SQL 2017을 수동으로 구성하는 경우 설치 지침 아래의 *SQL 2017을 사용하여 SSRS 구성* 을 참조하세요.

3. 보호된 서버에서 보호 에이전트를 업데이트합니다.
4. 프로덕션 서버를 다시 시작하지 않아도 백업을 계속해야 합니다.
5. 이제 데이터 보호를 시작할 수 있습니다. 보호하는 동안 Modern Backup Storage로 업그레이드하는 경우 백업을 저장하려는 볼륨을 선택하고, 프로비전된 공간이 부족한지 확인할 수도 있습니다. [자세히 알아보기](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>문제 해결

설치(또는 백업 또는 복원) 단계에서 Microsoft Azure Backup Server가 오류로 실패하는 경우에 대한 자세한 내용은 [오류 코드 문서](https://support.microsoft.com/kb/3041338)를 참조합니다.

[Azure Backup 관련 FAQ](backup-azure-backup-faq.yml)를 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[DPM을 위한 환경 준비](/system-center/dpm/prepare-environment-for-dpm)에 관한 자세한 내용은 여기에서 확인할 수 있습니다. 또한 여기에는 Azure Backup 서버를 배포 및 사용하는 데 지원되는 구성에 대한 정보도 포함되어 있습니다. 다양한 작업을 수행하는 데 일련의 [PowerShell cmdlet](/powershell/module/dataprotectionmanager/)을 사용할 수 있습니다.

Microsoft Azure Backup 서버를 사용하여 워크로드 보호를 더 깊이 이해하려면 다음 문서를 사용할 수 있습니다.

* [SQL Server 백업](backup-azure-backup-sql.md)
* [SharePoint Server 백업](backup-azure-backup-sharepoint.md)
* [대체 서버 백업](backup-azure-alternate-dpm-server.md)
