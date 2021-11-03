---
title: Azure Data Box를 사용하여 오프라인 백업
description: Azure Data Box를 사용하여 MARS 에이전트에서 Recovery Services 자격 증명 모음에 대량의 초기 백업 데이터를 오프라인으로 시드하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 40fb046506238c5f02c7e36d6ce12b31ed44fcaa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020543"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Data Box를 사용한 Azure Backup 오프라인 백업

[Azure Data Box](../databox/data-box-overview.md)를 사용하여 Recovery Services 자격 증명 모음에 대량의 초기 MARS(Microsoft Azure Recovery Services) 백업을 오프라인으로 시드할 수 있습니다(네트워크 사용 없이). 이 프로세스는 대기 시간이 긴 네트워크를 통해 대량 백업 데이터를 온라인으로 이동하는 데 사용되었을 시간과 네트워크 대역폭을 절약합니다.  Azure Data Box 기반의 오프라인 백업은 [Azure Import/Export 서비스 기반의 오프라인 백업](./backup-azure-backup-import-export.md)에 비해 다음 두 가지 고유한 이점이 있습니다.

- 사용자 고유의 Azure 호환 디스크와 커넥터를 전달하지 않아도 됩니다. Azure Data Box에서 선택한 [Data Box SKU](https://azure.microsoft.com/services/databox/data/)와 관련된 디스크를 제공합니다.
- Azure Backup(MARS 에이전트)에서 Azure Data Box의 지원되는 SKU에 백업 데이터를 직접 쓸 수 있습니다. 이 기능을 사용하면 초기 백업 데이터에 사용할 스테이징 위치를 프로비저닝하지 않아도 됩니다. 디스크를 포맷하고 해당 데이터를 디스크에 복사하는 유틸리티도 필요하지 않습니다.

## <a name="azure-data-box-with-the-mars-agent"></a>MARS 에이전트를 통한 Azure Data Box

이 문서에서는 Azure Data Box를 사용하여 MARS 에이전트에서 Recovery Services 자격 증명 모음에 대량의 초기 백업 데이터를 오프라인으로 시드하는 방법을 설명합니다.

## <a name="supported-platforms"></a>지원 플랫폼

Azure Data Box를 사용하여 MARS 에이전트에서 데이터를 시드하는 프로세스는 다음 Windows SKU에서 지원됩니다.

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **워크스테이션**                        |                                                              |
| Windows 10 64비트                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64비트                    | Enterprise, Pro                                             |
| Windows 8 64비트                      | Enterprise, Pro                                             |
| Windows 7 64비트                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **서버**                             |                                                              |
| Windows Server 2019 64비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64비트         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64비트            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64비트    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64비트 | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64비트    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64비트     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64비트        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>백업 데이터 크기 및 지원되는 Data Box SKU

| 서버당 백업 데이터 크기(MARS에서 압축 후) | 지원되는 Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 7\.2TB 이하                                                    | [Azure Data Box 디스크](../databox/data-box-disk-overview.md) |
| 7\.2TB 초과 및 80TB 이하**                                      | [Azure Data Box(100TB)](../databox/data-box-overview.md) |

*일반적인 압축률은 10%에서 20% 사이입니다. <br>
**단일 MARS 서버에 대한 초기 백업 데이터가 80TB를 초과할 것으로 예상되는 경우 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)에 문의하세요.

>[!IMPORTANT]
>단일 서버의 초기 백업 데이터는 단일 Azure Data Box 인스턴스 또는 Azure Data Box 디스크에 포함되어야 하며, 동일한 SKU 또는 다른 SKU의 여러 디바이스 간에 공유할 수 없습니다. 하지만 Azure Data Box 디바이스는 여러 서버의 초기 백업을 포함할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-subscription-and-required-permissions"></a>Azure 구독 및 필요한 권한

- 이 작업을 실행하려면 Azure를 구독해야 합니다.
- 이 작업을 실행하려면 오프라인 백업 정책을 수행하도록 지정된 사용자가 Azure 구독의 소유자여야 합니다.
- 데이터를 시드해야 하는 Recovery Services 자격 증명 모음 및 Data Box 작업을 동일한 구독에서 사용할 수 있어야 합니다.
- Azure Data Box 작업 및 Recovery Services 자격 증명 모음과 연결된 대상 저장소 계정이 동일한 지역에 있는 것이 좋습니다. 하지만 필수 사항은 아닙니다.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 가져오기

*이는 프로세스의 가장 중요한 필수 구성 요소입니다*. Azure PowerShell 버전 3.7.0을 설치하기 전에 다음 검사를 수행합니다.

#### <a name="step-1-check-the-powershell-version"></a>1단계: PowerShell 버전 확인

1. Windows PowerShell을 열고 다음 명령을 실행합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. 출력에 3.7.0보다 높은 버전이 표시되면 "2단계"를 수행합니다. 그렇지 않으면 “3단계”로 건너뜁니다.

#### <a name="step-2-uninstall-the-powershell-version"></a>2단계: PowerShell 버전 제거

현재 버전의 PowerShell을 제거합니다.

1. PowerShell에서 다음 명령을 실행하여 종속 모듈을 제거합니다.

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 모든 종속 모듈이 성공적으로 삭제되었는지 확인하려면 다음 명령을 실행합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3단계: PowerShell 버전 3.7.0 설치

AzureRM 모듈이 없음을 확인한 후 다음 방법 중 하나를 사용하여 버전 3.7.0을 설치합니다.

- GitHub에서 [이 링크](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)를 사용합니다.

또는

- PowerShell 창에서 다음 명령을 실행합니다.

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell MSI 파일을 사용하여 설치되었을 수도 있습니다. 제거하려면 제어판의 **프로그램 제거** 옵션을 사용하여 제거합니다.

### <a name="order-and-receive-the-data-box-device"></a>Data Box 디바이스 주문 및 수령

MARS 및 Azure Data Box를 사용하는 오프라인 백업 프로세스를 수행하려면 MARS 에이전트를 사용하여 오프라인 백업을 트리거하기 전에 Data Box 디바이스가 배달됨 상태여야 합니다. 요구 사항에 가장 적합한 SKU를 주문하려면 [백업 데이터 크기 및 지원되는 Data Box SKU](#backup-data-size-and-supported-data-box-skus)를 참조하세요. Data Box 디바이스를 주문하여 받으려면 [자습서: Azure Data Box 디스크 주문](../databox/data-box-disk-deploy-ordered.md)에서 단계를 수행합니다.

> [!IMPORTANT]
> **계정 종류** 에서 *BlobStorage* 를 선택하지 마세요. MARS 에이전트에는 *BlobStorage* 를 선택한 경우 지원되지 않는 페이지 Blob을 지원하는 계정이 필요합니다. Azure Data Box 작업의 대상 스토리지 계정을 만들 때 **계정 종류** 로 **Storage V2(범용 v2)** 를 선택합니다.

![인스턴스 세부 정보에서 계정 종류 선택](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>MARS 에이전트 설치 및 설정

1. MARS 에이전트의 이전 설치를 모두 제거해야 합니다.
1. [이 웹사이트](https://aka.ms/azurebackup_agent)에서 최신 MARS 에이전트를 다운로드합니다.
1. *MARSAgentInstaller.exe* 를 실행하고 백업을 저장하려는 Recovery Services 자격 증명 모음에 에이전트에 [에이전트를 설치 및 등록](./install-mars-agent.md#install-and-register-the-agent)하는 단계 *만* 실행합니다.

   > [!NOTE]
   > Recovery Services 자격 증명 모음은 Azure Data Box 작업과 동일한 구독에 있어야 합니다.

   에이전트가 Recovery Services 자격 증명 모음에 등록되면 다음 섹션의 단계를 수행합니다.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box 디바이스 설정

주문한 Azure Data Box SKU에 따라 다음에 나오는 해당 섹션에서 설명하는 단계를 수행합니다. 이 단계에서는 MARS 에이전트가 초기 백업 데이터를 식별하고 전송하기 위해 Data Box 디바이스를 설정하고 준비하는 방법을 보여줍니다.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box 디스크 설정

Azure Data Box 디스크(각각 최대 8TB)를 하나 이상 주문한 경우 여기에 설명된 단계를 수행하여 [Data Box 디스크의 포장을 풀고, 디스크를 연결 및 잠금 해제](../databox/data-box-disk-deploy-set-up.md)합니다.

>[!NOTE]
>MARS 에이전트를 포함하는 서버에는 USB 포트가 없을 수 있습니다. 이러한 상황에는 Azure Data Box 디스크를 다른 서버/클라이언트에 연결하고 디바이스의 루트를 네트워크 공유로 공개할 수 있습니다.

### <a name="set-up-azure-data-box"></a>Azure Data Box 설정

Azure Data Box 인스턴스(최대 100TB)를 주문한 경우 여기에 설명된 단계를 수행하여 [Data Box 인스턴스를 설정](../databox/data-box-deploy-set-up.md)합니다.

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Azure Data Box 인스턴스를 로컬 시스템으로 탑재

MARS 에이전트는 로컬 시스템 컨텍스트에서 작동하므로 Azure Data Box 인스턴스가 연결된 탑재 경로에도 동일한 수준의 권한을 제공해야 합니다.

NFS 프로토콜을 사용하여 Data Box 디바이스를 로컬 시스템으로 탑재할 수 있도록 하려면 다음을 수행합니다.

1. MARS 에이전트가 설치된 Windows Server에서 NFS 기능에 클라이언트를 사용하도록 설정합니다. 대체 원본 *WIM:D:\Sources\Install.wim:4* 를 지정합니다.
1. [Sysinternals](/sysinternals/downloads/psexec) 페이지의 PsExec를 MARS 에이전트가 설치된 서버로 다운로드합니다.
1. 관리자 권한 명령 프롬프트를 열고 *PSExec.exe* 를 포함하는 디렉터리를 현재 디렉터리로 하여 다음 명령을 실행합니다.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   이전 명령이 로컬 시스템 컨텍스트에 있기 때문에 열리는 명령 창입니다. 이 명령 창을 사용하여 Azure 페이지 Blob 공유를 Windows Server에 네트워크 드라이브로 탑재하는 단계를 실행합니다.
1. [Data Box에 연결](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box)의 단계를 수행하여 NFS를 통해 MARS 에이전트가 있는 서버를 Data Box 디바이스에 연결합니다. 로컬 시스템 명령 프롬프트에서 다음 명령을 실행하여 Azure 페이지 blob 공유를 탑재합니다.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   공유가 탑재된 후 서버에서 X:에 액세스할 수 있는지 확인합니다. 액세스할 수 있는 경우 이 문서의 다음 섹션을 계속 진행합니다.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>초기 백업 데이터를 Azure Data Box 디바이스로 전송

1. 서버에서 **Microsoft Azure Backup** 애플리케이션을 엽니다.
1. **작업** 창에서 **백업 예약** 을 선택합니다.

    ![백업 예약 선택](./media/offline-backup-azure-data-box/schedule-backup.png)

1. **백업 예약 마법사** 의 단계를 수행합니다.

1. **항목 추가** 단추를 선택하여 항목을 추가합니다. 주문하고 받은 [Azure Data Box SKU가 지원하는 크기 제한](#backup-data-size-and-supported-data-box-skus) 내에서 항목의 총 크기를 유지합니다.

    ![백업할 항목 추가](./media/offline-backup-azure-data-box/add-items.png)

1. **파일 및 폴더** 와 **시스템 상태** 에 적절한 백업 예약 및 보존 정책을 선택합니다. 시스템 상태는 Windows Server에만 적용되고 Windows 클라이언트에는 적용되지 않습니다.
1. 마법사의 **초기 백업 유형 선택(파일 및 폴더)** 페이지에서 **Microsoft Azure Data Box 디스크를 사용하여 이전** 옵션을 선택하고 **다음** 을 선택합니다.

    ![초기 백업 유형 선택](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 메시지가 표시되면 Azure 구독에 대한 소유자 권한이 있는 사용자 자격 증명을 사용하여 Azure에 로그인합니다. 이 작업을 완료한 후에는 다음과 유사한 페이지가 표시됩니다.

    ![리소스 만들기 및 필요한 권한 적용](./media/offline-backup-azure-data-box/creating-resources.png)

   그런 다음 MARS 에이전트는 배달됨 상태인 구독의 Data Box 작업을 가져옵니다.

    ![구독 ID에 대한 Data Box 작업 가져오기](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Data Box 디스크의 포장을 풀고, 디스크를 연결 및 잠금 해제한 올바른 Data Box 주문을 선택합니다. **다음** 을 선택합니다.

    ![Data Box 주문 선택](./media/offline-backup-azure-data-box/select-databox-order.png)

1. **Data Box 디바이스 검색** 페이지에서 **디바이스 검색** 을 선택합니다. 이 작업을 수행하면 MARS 에이전트가 로컬로 연결된 Azure Data Box 디스크를 스캔하여 디바이스를 검색합니다.

    ![Data Box 디바이스 검색](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box 인스턴스를 네트워크 공유로 연결한 경우(USB 포트를 사용할 수 없거나 100TB Data Box 디바이스를 주문하여 탑재했기 때문에) 처음에는 검색에 실패합니다. Data Box 디바이스에 네트워크 경로를 입력하는 옵션이 제공됩니다.

    ![네트워크 경로 입력](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure Data Box 디스크의 루트 디렉터리에 대한 네트워크 경로를 제공합니다. 이 디렉터리에는 이름이 *PageBlob* 인 디렉터리가 포함되어 있어야 합니다.
    >
    >![Azure Data Box 디스크의 루트 디렉터리](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >예를 들어, 디스크 경로가 `\\mydomain\myserver\disk1\`이고 *disk1* 이 *PageBlob* 이라는 디렉터리를 포함하는 경우, MARS 에이전트 마법사 창에 입력하는 경로는 `\\mydomain\myserver\disk1\`입니다.
    >
    >[Azure Data Box 100TB 디바이스를 설정](#set-up-azure-data-box-devices)하는 경우 디바이스에 네트워크 경로로 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`을 입력합니다.

1. Azure Data Box를 사용하여 오프라인 백업 구성으로 백업 및 보존 정책을 저장하려면 **다음** 을 선택하고 다음 페이지에서 **마침** 을 선택합니다.

   다음 페이지에서는 정책이 성공적으로 저장되었음을 확인합니다.

    ![정책이 저장됨](./media/offline-backup-azure-data-box/policy-saved.png)

1. 이전 페이지에서 **닫기** 를 선택합니다.

1. MARS 에이전트 콘솔의 **작업** 창에서 **지금 백업** 을 선택합니다. 마법사 페이지에서 **백업** 을 선택합니다.

    ![지금 백업 마법사](./media/offline-backup-azure-data-box/backup-now.png)

MARS 에이전트에서 선택한 데이터를 Azure Data Box 디바이스로 백업하기 시작합니다. 이 프로세스는 몇 시간에서 며칠까지 소요될 수 있습니다. 시간은 MARS 에이전트가 있는 서버와 Azure Data Box 디스크 간의 파일 수 및 연결 속도에 따라 달라집니다.

데이터 백업이 완료되면 MARS 에이전트에 다음 페이지와 유사한 페이지가 표시됩니다.

![백업 진행률 표시](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>백업 후 단계

이 섹션에서는 Azure Data Box Disk에 대한 데이터 백업이 성공한 후 수행해야 하는 단계를 설명합니다.

- [Azure Data Box 디스크를 Azure로 제공](../databox/data-box-disk-deploy-picked-up.md)하려면 이 문서의 단계를 수행합니다. Azure Data Box 100TB 디바이스를 사용한 경우 이 단계를 수행하여 [Azure Data Box 디바이스를 Azure로 배송](../databox/data-box-deploy-picked-up.md)합니다.

- Azure Portal에서 [Data Box 작업을 모니터링](../databox/data-box-disk-deploy-upload-verify.md)합니다. Azure Data Box 작업이 완료되면 다음 예약된 백업 시간에 MARS 에이전트가 스토리지 계정에서 Recovery Services 자격 증명 모음에 자동으로 데이터를 옮깁니다. 그런 다음, 복구 지점이 만들어졌으면 백업 작업을 *작업 완료됨* 으로 표시합니다.

    >[!NOTE]
    >MARS 에이전트는 정책을 만드는 동안 예약된 시간에 백업을 트리거합니다. 해당 작업에는 작업 완료 시까지 “Azure Data Box 작업이 완료되기를 기다리는 중” 플래그가 지정됩니다.

- MARS 에이전트에서 초기 백업에 해당하는 복구 지점을 만든 후에는 Azure Data Box 작업과 관련된 스토리지 계정 또는 특정 내용을 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

MARS(Microsoft Azure Recovery Services) 에이전트가 테넌트에서 사용자를 위해 Azure AD(Azure Active Directory) 애플리케이션을 만듭니다. 이 애플리케이션에는 오프라인 시드 정책을 구성할 때 생성 및 업로드된 인증에 사용할 인증서가 필요합니다. Azure PowerShell을 사용하여 Azure AD 애플리케이션에 인증서를 만들어 업로드합니다.

### <a name="problem"></a>문제

오프라인 백업을 구성할 때 Azure PowerShell cmdlet의 버그로 인해 문제가 발생할 수 있습니다. MAB 에이전트가 만든 동일한 Azure AD 애플리케이션에 여러 인증서를 추가하지 못할 수 있습니다. 이 문제는 동일하 서버 또는 다른 서버에 대해 오프라인 시드 정책을 구성한 경우에 영향을 줍니다.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>이 특정 근본 원인으로 인해 문제가 발생한 것인지 확인합니다.

문제가 앞에서 설명한 것과 동일한지 확인하려면 다음 단계 중 하나를 수행합니다.

#### <a name="step-1-of-verification"></a>1단계 확인

오프라인 백업을 구성할 때 MAB 콘솔에 다음 오류 메시지가 표시되는지 확인합니다.

![현재 Azure 계정에 대한 오프라인 백업 정책을 만들 수 없습니다.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>2단계 확인

1. 설치 경로에서 **임시** 폴더를 엽니다. 기본 임시 폴더 경로는 *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp* 입니다. *CBUICurr* 파일을 찾아서 엽니다.

1. *CBUICurr* 파일에서 마지막 줄로 스크롤하여 다음 오류 메시지에 있는 것과 동일한 문제인지 확인합니다. `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`

### <a name="workaround"></a>해결 방법

이 문제를 해결하는 방법으로 다음 단계를 수행하고 정책 구성을 다시 시도합니다.

#### <a name="step-1-of-workaround"></a>해결 방법 1단계

Data Box 작업을 만들 구독에서 관리자 액세스 권한이 있는 다른 계정을 사용하여 MAB UI에 나타나는 PowerShell에 로그인합니다.

#### <a name="step-2-of-workaround"></a>해결 방법 2단계

오프라인 시드가 구성된 다른 서버가 없고 `AzureOfflineBackup_<Azure User Id>` 애플리케이션에 종속된 다른 서버가 없는 경우 이 애플리케이션을 삭제합니다. **Azure Portal** > **Azure Active Directory** > **앱 등록** 을 선택합니다.

>[!NOTE]
> `AzureOfflineBackup_<Azure User Id>` 애플리케이션에 다른 오프라인 시드가 구성되어 있지 않은지, 이 애플리케이션에 종속된 다른 서버가 없는지 확인합니다. **공개 키** 섹션에서 **설정** > **키** 로 이동합니다. 다른 공개 키를 추가하지 않아야 합니다. 다음 스크린샷을 참조하세요.
>
>![퍼블릭 키](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3단계

오프라인 백업을 구성하려는 서버에서 다음 작업을 수행합니다.

1. **컴퓨터 인증서 관리 애플리케이션** > **개인** 탭으로 이동하여 이름이 `CB_AzureADCertforOfflineSeeding_<Timestamp>`인 인증서를 찾습니다.

2. 해당 인증서를 선택하고 **모든 작업** 을 마우스 오른쪽 단추를 클릭하여 비공개 키 없이 .cer 형식으로 **내보내기** 를 선택합니다.

3. 2단계에서 언급한 Azure 오프라인 백업 애플리케이션으로 이동합니다. **설정** > **키** > **공개 키 업로드** 를 선택합니다. 이전 단계에서 내보낸 인증서를 업로드합니다.

    ![공개 키 업로드](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 서버에서 실행 창에 **regedit** 를 입력하여 레지스트리를 엽니다.

5. 레지스트리 *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* 로 이동합니다. **CloudBackupProvider** 를 마우스 오른쪽 단추로 클릭하고 이름이 `AzureADAppCertThumbprint_<Azure User Id>`인 새 문자열 값을 추가합니다.

    >[!NOTE]
    > Azure 사용자 ID를 가져오려면 다음 작업 중 하나를 수행합니다.
    >
    >- Azure에 연결된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 명령을 실행합니다.
    > - 이름이 *CurrentUserId* 인 레지스트리 경로 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup`으로 이동합니다.

6. 이전 단계에서 추가한 문자열을 마우스 오른쪽 단추로 클릭하고 **수정** 을 선택합니다. 2단계에서 내보낸 인증서의 지문을 값에 입력합니다. **확인** 을 선택합니다.

7. 지문 값을 가져오려면 인증서를 두 번 클릭합니다. **세부 정보** 탭을 선택하고 지문 필드가 보일 때까지 아래로 스크롤합니다. **지문** 을 선택하고 값을 복사합니다.

    ![인증서의 지문 필드](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>질문

발생한 문제에 대한 문의 사항 또는 설명은 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)에 문의하세요.
