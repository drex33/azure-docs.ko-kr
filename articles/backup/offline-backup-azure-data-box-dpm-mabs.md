---
title: DPM 및 MABS에 대해 Azure Data Box를 사용한 오프라인 백업
description: Azure Data Box를 사용하여 DPM 및 MABS에서 초기 백업 데이터를 오프라인으로 시드할 수 있습니다.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 568baf7351555511b58dba59bec404688a646126
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528451"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs"></a>Azure Data Box를 사용하는, DPM과 MABS에 대한 오프라인 시드

> [!NOTE]
> 이 기능은 DPM(Data Protection Manager) 2019 UR2 이상과 MABS v3 UR2 이상에서 적용할 수 있습니다.

이 문서에서는 Azure Data Box를 사용하여 DPM 및 MABS에서 초기 백업 데이터를 Azure Recovery Services 자격 증명 모음에 오프라인으로 시드하는 방법을 설명합니다.

[Azure Data Box](../databox/data-box-overview.md)를 사용하여 대량 초기 DPM/MABS 백업을 Recovery Services 자격 증명 모음에 오프라인으로(네트워크 사용 안 함) 시드할 수 있습니다. 이 프로세스는 대기 시간이 긴 네트워크를 통해 대량 백업 데이터를 온라인으로 이동하는 데 사용되었을 시간과 네트워크 대역폭을 절약합니다. 이 기능은 현재 미리 보기로 제공됩니다.

Azure Data Box 기반의 오프라인 백업은 [Azure Import/Export 서비스 기반의 오프라인 백업](backup-azure-backup-server-import-export.md)에 비해 다음 두 가지 고유한 이점이 있습니다.

- 사용자 고유의 Azure 호환 디스크와 커넥터를 조달하지 않아도 됩니다. Azure Data Box에서 선택한 [Data Box SKU](https://azure.microsoft.com/services/databox/data/)와 관련된 디스크를 제공합니다.

- Azure Backup(MARS 에이전트)에서 Azure Data Box의 지원되는 SKU에 백업 데이터를 직접 쓸 수 있습니다. 이 기능을 사용하면 초기 백업 데이터에 사용할 스테이징 위치를 프로비저닝하지 않아도 됩니다. 디스크를 포맷하고 해당 데이터를 디스크에 복사하는 유틸리티도 필요하지 않습니다.

## <a name="supported-platforms"></a>지원 플랫폼

지원되는 플랫폼은 다음과 같습니다.

- Windows Server 2019 64비트(Standard, Datacenter, Essentials)
- Windows Server 2016 64비트(Standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>백업 데이터 크기 및 지원되는 Data Box SKU

지원되는 Data Box SKU는 다음과 같습니다.

| 서버당 백업 데이터 크기(MARS에서 압축 후)\* | 지원되는 Azure Data Box SKU |
| --- | --- |
| \<= 7.2TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| 7\.2TB 초과, 80TB 이하\*\* | [Azure Data Box(100TB)](../databox/data-box-overview.md) |

\*일반적인 압축률은 10~20% 사이에서 다양하게 적용됩니다. <br>
\*\*단일 데이터 원본의 초기 백업 데이터가 80TB를 초과할 것으로 예상되는 경우 [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)에 문의하세요.

> [!IMPORTANT]
> 단일 데이터 원본의 초기 백업 데이터는 단일 Azure Data Box 또는 Azure Data Box Disk에 포함되어야 하며, 동일한 SKU 또는 다른 SKU의 여러 디바이스 간에 공유할 수 없습니다. 그러나 단일 Azure Data Box에 여러 데이터 원본의 초기 백업을 포함할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

DPM/MABS에서 실행되는 MARS 에이전트를 [최신 버전](https://aka.ms/azurebackup_agent)(2.0.9171.0 이상)으로 업그레이드해야 합니다.

다음을 확인합니다.

### <a name="azure-subscription-and-required-permissions"></a>Azure 구독 및 필요한 권한

- 유효한 Azure 구독.
- 오프라인 백업 정책을 수행하려는 사용자는 Azure 구독의 소유자여야 합니다.
- 데이터를 시드해야 하는 Recovery Services 자격 증명 모음 및 Data Box 작업을 동일한 구독에서 사용할 수 있어야 합니다.
    > [!NOTE]
    > 대상 스토리지 계정과 Recovery Services 자격 증명 모음은 동일한 지역에 있는 것이 좋습니다. 그러나 이것은 필수 사항이 아닙니다.

### <a name="order-and-receive-the-data-box-device"></a>Data Box 디바이스 주문 및 수령

오프라인 백업을 트리거하기 전에 필요한 Data Box 디바이스가 *배달됨* 상태인지 확인합니다. 요구 사항에 가장 적합한 SKU를 주문하려면 [백업 데이터 크기 및 지원되는 Data Box SKU](#backup-data-size-and-supported-data-box-skus)를 참조하세요. [이 문서](../databox/data-box-disk-deploy-ordered.md)의 단계를 수행하여 Data Box 디바이스를 주문하고 수령합니다.

> [!IMPORTANT]
> **계정 종류** 에서 *BlobStorage* 를 선택하지 마세요. DPM/MABS 서버에는 *BlobStorage* 를 선택한 경우 지원되지 않는 페이지 Blob을 지원하는 계정이 필요합니다. Azure Data Box 작업의 대상 스토리지 계정을 만드는 경우 **계정 종류** 로 **Storage V2(범용 v2)** 를 선택합니다.

![Azure DataBox 설정](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Azure Data Box 디바이스 설정

Azure Data Box 디바이스를 수령한 후, 주문한 Azure Data Box SKU에 따라 아래에서 해당 섹션의 단계를 수행하여 DPM/MABS 서버에서 초기 백업 데이터를 확인하고 전송할 Data Box 디바이스를 설정하고 준비합니다.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box 디스크 설정

Azure Data Box 디스크(각각 최대 8TB)를 하나 이상 주문한 경우 [여기](../databox/data-box-disk-deploy-set-up.md)에 설명된 단계를 수행하여 Data Box 디스크의 포장을 풀고 디스크를 연결 및 잠금 해제합니다.

> [!NOTE]
> DPM/MABS 서버에 USB 포트가 없을 수 있습니다. 해당 시나리오에서는 Azure Data Box 디스크를 다른 서버/클라이언트에 연결하고 디바이스의 루트를 네트워크 공유로 공개할 수 있습니다.

## <a name="setup-azure-data-box"></a>Azure Data Box 설정

Azure Data Box(최대 100TB)를 주문한 경우 [여기](../databox/data-box-deploy-set-up.md)에 설명된 단계를 수행하여 Data Box를 설정합니다.

### <a name="mount-your-azure-data-box-as-local-system"></a>Azure Data Box를 로컬 시스템으로 탑재

DPM/MABS 서버는 시스템 컨텍스트에서 작동하므로 Azure Data Box가 연결된 탑재 경로에도 동일한 수준의 권한을 제공해야 합니다. NFS 프로토콜을 사용하여 Data Box 디바이스를 로컬 시스템으로 탑재할 수 있게 하려면 다음 단계를 수행합니다.

1. DPM/MABS 서버에서 NFS용 클라이언트 기능을 사용하도록 설정합니다.
대체 원본을 지정합니다. *WIM:D:\Sources\Install.wim:4*
2. [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip)에서 DPM/MABS 서버로 **PSExec** 를 다운로드합니다.
3. 관리자 권한 명령 프롬프트를 열고 *PSExec.exe* 를 포함하는 디렉터리를 현재 디렉터리로 사용하여 다음 명령을 실행합니다.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. 위 명령의 실행 결과로 열리는 명령 창은 로컬 시스템 컨텍스트에 있습니다. 이 명령 창을 사용하여 Azure 페이지 Blob 공유를 Windows Server에 네트워크 드라이브로 탑재하는 단계를 실행합니다.
5. [여기](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box)에 설명된 단계를 수행하여 NFS를 통해 DPM/MABS 서버를 Data Box 디바이스에 연결하고 로컬 시스템 명령 프롬프트에서 다음 명령을 실행하여 Azure 페이지 Blob 공유를 탑재합니다.

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. 탑재되면 서버에서 X:에 액세스할 수 있는지 여부를 확인합니다. 액세스할 수 있는 경우 이 문서의 다음 섹션을 계속 진행합니다.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>초기 백업 데이터를 Azure Data Box 디바이스로 전송

1. DPM/MABS 서버에서 [새 보호 그룹 만들기](/system-center/dpm/create-dpm-protection-groups) 단계를 수행합니다. 기존 보호 그룹에 온라인 보호를 추가하는 경우 기존 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **온라인 보호 추가** 를 선택한 다음, **8단계** 부터 시작합니다.
2. **그룹 구성원 선택** 페이지에서 백업하려는 컴퓨터 및 원본을 지정합니다.
3. **데이터 보호 방법 선택** 페이지에서 단기 및 장기 백업을 처리하는 방법을 지정합니다. **온라인 보호 사용** 을 선택해야 합니다.

   ![새 보호 그룹 만들기](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. **단기 목표 선택** 페이지에서 디스크의 단기 스토리지에 백업하는 방법을 지정합니다.
5. **디스크 할당 검토** 페이지에서 보호 그룹에 대해 할당된 스토리지 풀 디스크 공간을 검토합니다.
6. **복제본 만들기 방법 선택** 페이지에서 **네트워크를 통해 자동으로** 를 선택합니다.
7. **일관성 확인 옵션 선택** 페이지에서 일관성 확인을 자동화하는 방법을 선택합니다.
8. **온라인 보호 데이터 지정:** 페이지에서 온라인 보호를 사용하도록 설정하려는 구성원을 선택합니다.

    ![온라인 보호 데이터 지정](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. **온라인 백업 일정 지정** 페이지에서 Azure에 증분 백업이 실행되는 빈도를 지정합니다.
10. **온라인 보존 정책 지정** 페이지에서 일별/주별/월별/연도별 백업을 통해 만들어진 복구 지점이 Azure에서 유지되는 방법을 지정합니다.
11. 마법사의 **온라인 복제 선택** 화면에서 **Microsoft 소유의 디스크를 사용하여 전송** 옵션을 선택하고 **다음** 을 선택합니다.

    ![초기 온라인 복제 선택](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > 이 기능이 미리 보기 상태이므로 MABS v3에서는 **Microsoft 소유 디스크를 사용하여 전송** 을 선택하는 옵션을 사용할 수 없습니다. MABS v3에 이 기능을 사용하려면 [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)으로 문의하세요.

12. 메시지가 표시되면 Azure 구독에 대한 소유자 권한이 있는 사용자 자격 증명을 사용하여 Azure에 로그인합니다. 로그인에 성공하면 다음 화면이 표시됩니다.

    ![로그인 성공 후](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     그런 다음 DPM/MABS 서버는 *배달됨* 상태인 구독의 Data Box 작업을 가져옵니다.

     > [!NOTE]
     > 처음 로그인하는 데 평소보다 시간이 오래 걸립니다. Azure PowerShell 모듈이 백그라운드에서 설치될 뿐만 아니라 Azure AD 애플리케이션이 등록됩니다.
     >
     >  - 설치되는 PowerShell 모듈은 다음과 같습니다.<br>
          - AzureRM.Profile     *5.8.3*<br>
          - AzureRM.Resources   *6.7.3*<br>
          - AzureRM.Storage     *5.2.0*<br>
          - Azure.Storage       *4.6.1*<br>
     >  - Azure AD 애플리케이션은 *AzureOfflineBackup_\<object GUID of the user>* 으로 등록됩니다.

13. Data Box 디스크의 포장을 풀고 디스크를 연결 및 잠금 해제한 올바른 Data Box 주문을 선택합니다. **다음** 을 선택합니다.

    ![DataBox 선택](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. **DataBox 검색** 화면에서 Data Box 디바이스의 경로를 입력하고 **디바이스 검색** 을 선택합니다.

    ![네트워크 경로 입력](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Azure Data Box 디스크의 루트 디렉터리에 대한 네트워크 경로를 제공합니다. 이 디렉터리에는 아래와 같이 이름이 *PageBlob* 인 디렉터리가 포함되어 있어야 합니다.
    >
    > ![USB 드라이브](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > 예를 들어 디스크 경로가 `\\mydomain\myserver\disk1\`이고 *disk1* 에 *PageBlob* 이라는 디렉터리가 포함되어 있으면 DPM/MABS 서버 마법사에서 입력할 경로는 `\\mydomain\myserver\disk1\`이 됩니다.
    > [Azure Data Box 100TB 디바이스를 설정](./offline-backup-azure-data-box.md#set-up-azure-data-box)하는 경우 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 디바이스에 대한 네트워크 경로로 다음을 입력합니다.

15. **다음** 을 선택합니다. **요약** 페이지에서 설정을 검토한 다음 **그룹 만들기** 를 선택합니다.

    ![DataBox 검색](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    다음 화면에서 보호 그룹이 만들어졌는지 확인합니다.

    ![보호 그룹이 만들어짐](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. 위 화면에서 **닫기** 를 선택합니다.

    이렇게 하면 DPM/MABS 디스크에 데이터 초기 복제가 수행됩니다. 보호가 완료되면 그룹 상태가 **보호** 페이지에서 보호 상태를 **정상** 으로 표시합니다.

17. Azure Data Box 디바이스에 오프라인 백업 복사를 시작하려면 **보호 그룹** 을 마우스 오른쪽 단추로 클릭하고 **복구 지점 만들기** 옵션을 선택합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

    ![복구 지점 만들기](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![복구 지점](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   DPM/MABS 서버에서 선택한 데이터를 Azure Data Box 디바이스로 백업하기 시작합니다. 이 작업은 데이터 크기 및 DPM/MABS 서버와 Azure Data Box Disk 간의 연결 속도에 따라 몇 시간에서 며칠까지 걸릴 수 있습니다.

   **모니터링** 창에서 작업 상태를 모니터링할 수 있습니다. 데이터 백업이 완료되면 아래와 유사한 화면이 표시됩니다.

   ![관리자 콘솔](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>백업 후 단계

Azure Data Box Disk로 데이터 백업이 완료되면 다음 단계를 수행합니다.

- [이 문서](../databox/data-box-disk-deploy-picked-up.md)의 단계를 수행하여 Azure Data Box 디스크를 Azure로 배송합니다. Azure Data Box 100TB 디바이스를 사용한 경우 [이 단계](../databox/data-box-deploy-picked-up.md)를 수행하여 Azure Data Box를 Azure로 배송합니다.
- Azure Portal에서 [Data Box 작업을 모니터링](../databox/data-box-disk-deploy-upload-verify.md)합니다. Azure Data Box 작업이 *완료* 되면 DPM/MABS 서버에서 다음 예약된 백업 시간에 스토리지 계정의 데이터를 Recovery Services 자격 증명 모음으로 자동으로 이동합니다. 그런 다음, 복구 지점이 만들어졌으면 백업 작업을 ‘작업 완료’로 표시합니다.

  > [!NOTE]
  > DPM/MABS 서버는 보호 그룹을 만드는 동안 예약된 시간에 백업을 트리거합니다. 그러나 해당 작업에는 작업 완료 시까지 ‘Azure Data Box 작업이 완료되기를 기다리는 중’ 플래그가 지정됩니다.

- DPM/MABS 서버에서 초기 백업에 해당하는 복구 지점을 만든 후에는 Azure Data Box 작업과 관련된 스토리지 계정(또는 특정 내용)을 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

DPM 서버의 MAB(Microsoft Azure Backup) 에이전트는 사용자 테넌트에 Azure AD 애플리케이션을 자동으로 만듭니다. 이 애플리케이션에는 오프라인 시드 정책을 구성할 때 생성 및 업로드된 인증용 인증서가 필요합니다.

인증서를 만들고 Azure AD 애플리케이션에 업로드할 때는 Azure PowerShell을 사용합니다.

### <a name="issue"></a>문제

오프라인 백업을 구성할 때 Azure PowerShell cmdlet의 알려진 코드 오류로 인해 MAB 에이전트가 만든 동일한 Azure AD 애플리케이션에 여러 인증서를 추가할 수 없습니다. 이는 동일하거나 다른 서버에 대해 오프라인 시드 정책을 구성한 경우 영향을 미칩니다.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>이 특정 근본 원인으로 인해 문제가 발생한 것인지 확인합니다.

위 [문제](#issue)로 인해 실패한 것인지 확인하려면 다음 단계 중 하나를 수행합니다.

#### <a name="step-1"></a>1단계

오프라인 백업을 구성할 때 DPM/MABS 콘솔에 다음 오류 메시지 중 하나가 표시되는지 확인합니다.

**이 서버의 인증 정보를 Azure로 업로드할 수 없기 때문에 현재 Azure 계정의 오프라인 백업 정책을 만들 수 없습니다. (ID: 100242)**

:::image type="content" source="./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png" alt-text="Azure Recovery Services 에이전트.":::

**가져오기 작업 상태를 쿼리하고 Recovery Services 자격 증명 모음으로 백업 데이터를 이동하는 데 필요한 Azure에 대한 서비스 호출을 수행할 수 없습니다. (ID:100230)**

:::image type="content" source="./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent-error-screen.png" alt-text="Azure Recovery Services 에이전트의 오류 화면 스크린샷.":::

#### <a name="step-2"></a>2단계

1. 설치 경로에 있는 **Temp** 폴더를 엽니다. 기본 temp 폴더 경로는 *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp* 입니다. *CBUICurr* 파일을 찾아서 엽니다.
2. *CBUICurr* 파일에서 마지막 줄로 스크롤한 다음, “고객 계정에 Azure AD 애플리케이션 자격 증명을 만들 수 없습니다. 예외: KeyId \<some guid>(으)로 기존 자격 증명으로 업데이트할 수 없습니다.”로 인해 실패한 것인지 확인합니다.

### <a name="workaround"></a>해결 방법

이 문제를 해결하려면 다음 단계를 수행하고 정책 구성을 다시 시도합니다.

1. Data Box 작업이 만들어질 구독에 대한 관리자 권한이 있는 다른 계정을 사용하여 DPM/MABS 서버 UI에 표시되는 Azure 로그인 페이지에 로그인합니다.
2. 다른 서버에 오프라인 시드가 구성되어 있지 않고 `AzureOfflineBackup_<Azure User Id>` 애플리케이션에 종속된 다른 서버가 없는 경우에는 **Azure Portal > Azure Active Directory > 앱 등록** 에서 이 애플리케이션을 삭제합니다.

   > [!NOTE]
   > `AzureOfflineBackup_<Azure User Id>` 애플리케이션에 다른 오프라인 시드가 구성되어 있지 않고 이 애플리케이션에 종속된 다른 서버가 없는지 확인합니다. 공개 키 섹션 아래의 **설정 > 키** 로 이동합니다. 다른 **공개 키** 가 추가되어서는 안 됩니다. 다음 스크린샷을 참조하세요.
   >
   > ![퍼블릭 키](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>3단계

오프라인 백업을 구성하려는 DPM/MABS 서버에서 다음 작업을 수행합니다.

1. **컴퓨터 인증서 관리 애플리케이션** > **개인** 탭을 열고 이름이 `CB_AzureADCertforOfflineSeeding_<ResourceId>`인 인증서를 찾습니다.
2. 위 인증서를 선택하고 **모든 작업** 을 마우스 오른쪽 단추로 클릭한 다음, 프라이빗 키 없이 .cer 형식으로 **내보내기** 를 선택합니다.
3. **point 2** 에 설명된 Azure Offline Backup 애플리케이션으로 이동합니다. **설정** > **키** > **퍼블릭 키 업로드** 에서 위 단계를 통해 내보낸 인증서를 업로드합니다.

   ![퍼블릭 키 업로드](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. 서버에서 **실행** 창에 **regedit** 를 입력하여 레지스트리를 엽니다.
5. *Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* 레지스트리로 이동합니다. **CloudBackupProvider** 를 마우스 오른쪽 단추로 클릭하고 이름이 `AzureADAppCertThumbprint_<Azure User Id>`인 새 문자열 값을 추가합니다.

    >[!NOTE]
    > Azure 사용자 ID를 가져오려면 다음 작업 중 하나를 수행합니다.
    >
    >- Azure에 연결된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 명령을 실행합니다.
    > - 이름이 *CurrentUserId* 인 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` 레지스트리 경로로 이동합니다.

6. 위 단계에서 추가한 문자열을 마우스 오른쪽 단추로 클릭하고 **수정** 을 선택합니다. **point 2** 에서 내보낸 인증서의 지문을 값에 입력하고 **확인** 을 선택합니다.
7. 지문 값을 가져오려면 인증서를 두 번 클릭하고 **세부 정보** 를 선택한 다음, 지문 필드가 표시될 때까지 아래로 스크롤합니다. **지문** 을 선택하고 값을 복사합니다.

   ![지문 값](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>다음 단계

- [자체 디스크를 사용한 오프라인 시드(Azure Import/Export 서비스 사용)](backup-azure-backup-server-import-export.md)
