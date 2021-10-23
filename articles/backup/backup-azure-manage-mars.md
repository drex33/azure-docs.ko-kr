---
title: MARS 에이전트 백업 관리 및 모니터링
description: Azure Backup 서비스를 사용하여 MARS(Microsoft Azure Recovery Services) 에이전트 백업을 관리하고 모니터링하는 방법에 대해 알아봅니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: db7bd057cc2f781b1a55b62f28170fe462b11f39
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233566"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup 서비스를 사용하여 MARS(Microsoft Azure Recovery Services) 에이전트 백업 관리

이 문서에서는 Microsoft Azure Recovery Services 에이전트를 사용하여 백업되는 파일 및 폴더를 관리하는 방법을 설명합니다.

## <a name="modify-a-backup-policy"></a>백업 정책 수정

백업 정책을 수정할 때 새 항목을 추가하거나, 백업에서 기존 항목을 제거하거나, 제외 설정을 사용하여 백업에서 파일을 제외할 수 있습니다.

- **항목 추가** - 백업할 새 항목을 추가할 때만 이 옵션을 사용합니다. 기존 항목을 제거하려면 **항목 제거** 또는 **제외 설정** 옵션을 사용합니다.  
- **항목 제거** - 백업 중인 항목을 제거하는 데 이 옵션을 사용합니다.
  - 볼륨 내의 모든 항목을 제거하려면 **항목 제거** 대신 **제외 설정** 을 사용합니다.
  - 볼륨의 모든 선택을 취소하면 항목의 이전 백업이 수정 범위를 제외하고 마지막 백업 시 보존 설정에 따라 보존됩니다.
  - 이러한 항목을 변경하면 첫 번째 전체 백업이 수행되고 새 정책 변경 내용은 이전 백업에 적용되지 않습니다.
  - 전체 볼륨의 선택을 취소하면 보존 정책을 수정하기 위한 범위 없이 이전 백업이 유지됩니다.
- **제외 설정** - 백업에서 특정 항목을 제외하려면 이 옵션을 사용합니다.

### <a name="add-new-items-to-existing-policy"></a>기존 정책에 새 항목 추가

1. **작업** 에서 **백업 예약** 을 선택합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)

2. **정책 항목 선택** 탭에서 **파일 및 폴더의 백업 일정 수정** 을 선택하고 **다음** 을 선택합니다.

    ![정책 항목 선택](./media/backup-azure-manage-mars/select-policy-items.png)

3. **백업 일정 수정 또는 중지** 탭에서 **백업 항목 또는 시간 변경** 을 선택하고 **다음** 을 선택합니다.

    ![백업 수정 또는 예약](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. **백업할 항목 선택** 탭에서 **항목 추가** 를 선택하여 백업할 항목을 추가합니다.

    ![백업 수정 또는 예약 항목 추가](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **항목 선택** 창에서 추가할 파일 또는 폴더를 선택하고 **확인** 을 선택합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-item.png)

6. 다음 단계를 완료하고 **마침** 을 선택하여 작업을 완료합니다.

### <a name="add-exclusion-rules-to-existing-policy"></a>기존 정책에 제외 규칙 추가

제외 규칙을 추가하여 백업하지 않을 파일 및 폴더를 건너뛸 수 있습니다. 새 정책을 정의하거나 기존 정책을 수정하는 동안 이 작업을 수행할 수 있습니다.

1. 작업 창에서 **백업 예약** 을 선택합니다. **백업할 항목 선택** 으로 이동하여 **제외 설정** 을 선택합니다.

    ![제외 설정](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **제외 설정** 에서 **제외 추가** 를 선택합니다.

    ![제외 추가](./media/backup-azure-manage-mars/add-exclusion.png)

3. **제외할 항목 선택** 에서 파일과 폴더를 찾아 제외할 항목을 선택한 다음 **확인** 을 선택합니다.

    ![제외할 항목을 선택합니다.](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 기본적으로 선택한 폴더 내의 모든 **하위 폴더** 가 제외됩니다. **예** 또는 **아니요** 를 선택하여 변경할 수 있습니다. 다음과 같이 제외할 파일 형식을 편집하고 지정할 수 있습니다.

    ![하위 폴더 유형 선택](./media/backup-azure-manage-mars/subfolders-type.png)

5. 다음 단계를 완료하고 **마침** 을 선택하여 작업을 완료합니다.

### <a name="remove-items-from-existing-policy"></a>기존 정책에서 항목 제거

1. 작업 창에서 **백업 예약** 을 선택합니다. **백업할 항목 선택** 으로 이동합니다. 목록에서 백업 일정으로부터 제거할 파일과 폴더를 선택하고 **항목 제거** 를 선택합니다.

    ![제거할 항목을 선택합니다.](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > 정책에서 볼륨을 완전히 제거하는 경우에는 주의하세요.  다시 추가해야 하는 경우 새 볼륨으로 처리됩니다. 다음 예약된 백업에서는 증분 백업 대신 초기 백업(전체 백업)을 수행합니다. 나중에 일시적으로 항목을 제거하고 추가해야 하는 경우 **항목 제거** 대신 **제외 설정** 을 사용하여 전체 백업 대신 증분 백업을 보장하는 것이 좋습니다.

2. 다음 단계를 완료하고 **마침** 을 선택하여 작업을 완료합니다.

## <a name="stop-protecting-files-and-folder-backup"></a>파일 및 폴더 백업 보호 중지

파일 및 폴더 백업 보호를 중지하는 방법에는 다음 두 가지가 있습니다.

- **보호를 중지하고 백업 데이터 보존**.
  - 이 옵션은 보호에서 이후의 모든 백업 작업을 중지합니다.
  - Azure Backup 서비스는 기존 복구 지점을 계속 유지합니다.  
  - 만료되지 않은 복구 지점의 백업 데이터를 복원할 수 있습니다.
  - 보호를 계속하기로 결정한 경우 *백업 일정 다시 사용* 옵션을 사용할 수 있습니다. 그 후에는 데이터가 새 보존 정책에 따라 보존됩니다.
- **보호를 중지하고 백업 데이터 삭제**.
  - 이 옵션은 데이터를 보호하는 이후의 모든 백업 작업을 중지합니다. 자격 증명 모음 보안 기능을 사용하도록 설정하지 않으면 모든 복구 지점이 즉시 삭제됩니다.<br>보안 기능을 사용 하는 경우 삭제는 14 일 지연 되며, *이 백업 항목에 대 한 데이터를 삭제 했습니다. 메시지가 포함 된 경고 전자 메일을 받게 됩니다. 이 데이터는 14 일 동안 일시적으로 사용할 수 있으며, 그 후에는 영구적으로 삭제* 되 고 권장 조치는 *14 일 이내에 백업 항목을 다시 보호 하 여 데이터를 복구 합니다.*<br>이 상태에서 보존 정책은 계속 적용되며 백업 데이터는 청구 가능 상태로 유지됩니다. 자격 증명 모음 보안 기능을 사용하는 방법에 대해 [자세히 알아봅니다](backup-azure-security-feature.md#enable-security-features).
  - 보호를 재개하려면 삭제 작업 후 14일 이내에 서버를 다시 보호합니다. 이 기간 동안 대체 서버로 데이터를 복원할 수도 있습니다.

### <a name="stop-protection-and-retain-backup-data"></a>보호 중지 및 백업 데이터 보존

1. MARS 관리 콘솔을 열고 **작업 창** 으로 이동한 다음 **백업 예약** 을 선택합니다.

    ![백업 예약 선택](./media/backup-azure-manage-mars/mars-actions.png)
1. **정책 항목 선택** 페이지에서 **파일 및 폴더의 백업 일정 수정** 을 선택하고 **다음** 을 선택합니다.

    ![정책 항목 선택](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. **예약된 백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용을 중지하되 일정이 다시 활성화될 때까지 저장된 백업 유지** 를 선택합니다. 그다음에 **다음** 을 선택합니다.

    ![예약된 백업 중지](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **예약된 백업 일시 중지** 에서 정보를 검토하고 **마침** 을 선택합니다.

    ![예약된 백업 일시 중지](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **백업 진행률 수정** 에서 일정 백업 일시 중지가 성공 상태인지 확인하고 **닫기** 를 선택하여 마칩니다.

### <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동한 다음 **백업 예약** 을 선택합니다.
2. **예약된 백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용을 중지하고 저장된 백업 모두 삭제** 를 선택합니다. 그다음에 **다음** 을 선택합니다.

    ![예약된 백업을 수정 또는 중지합니다.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **예약된 백업 중지** 페이지에서 **마침** 을 선택합니다.

    ![예약된 백업을 중지하고 마침을 선택합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성해야 하는 보안 PIN(개인 식별 번호)을 입력하라는 메시지가 표시됩니다. 시작하려면 먼저 Azure Portal에 로그인합니다.
5. **Recovery Services 자격 증명 모음** > **설정** > **속성** 으로 이동합니다.
6. **보안 PIN** 에서 **생성** 을 선택합니다. 이 PIN을 복사합니다. PIN은 5 분 동안만 유효합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인** 을 선택합니다.

    ![보안 PIN을 생성합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. **백업 진행률 수정** 페이지에 *삭제된 백업 데이터는 14일 동안 보존됩니다. 이후 백업 데이터는 영구적으로 삭제됩니다.* 라는 메시지가 표시됩니다.  

    ![백업 수정 진행률](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제한 후 포털에서 다음 단계를 수행합니다.

## <a name="re-enable-protection"></a>보호 다시 사용

데이터를 유지하고 보호를 다시 시작하기로 결정할 동안 보호를 중지한 경우 백업 정책 수정을 사용하여 백업 일정을 다시 사용하도록 설정할 수 있습니다.

1. **작업** 에서 **백업 예약** 을 선택합니다.
1. **백업 일정 다시 사용을 선택합니다. 백업 항목 또는 시간을 수정** 하고 **다음** 을 선택할 수도 있습니다.<br>

    ![백업 일정 다시 사용](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **백업할 항목 선택** 에서 **다음** 을 선택합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/re-enable-next.png)
1. **백업 일정 지정** 에서 백업 일정을 지정하고 **다음** 을 선택합니다.
1. **보존 정책 선택** 에서 보존 기간을 지정하고 **다음** 을 선택합니다.
1. 마지막으로 **확인** 화면에서 정책 세부 정보를 검토하고 **마침** 을 선택합니다.

## <a name="re-generate-passphrase"></a>암호 다시 생성

암호는 MARS 에이전트를 사용하여 Azure에서 온-프레미스 또는 로컬 컴퓨터를 백업 또는 복원하는 동안 데이터를 암호화 및 암호 해독하는 데 사용됩니다. 암호를 분실하거나 잊은 경우 다음 단계를 수행하여 암호를 다시 생성할 수 있습니다(컴퓨터가 Recovery Services 자격 증명 모음에 등록되고 백업이 구성된 경우).

1. MARS 에이전트 콘솔에서 **작업 창** > **속성 변경** 으로 이동합니다. 그런 다음 **암호화 탭** 으로 이동합니다.<br>
1. **암호 변경** 확인란을 선택합니다.<br>
1. 새 암호를 입력하거나 **암호 생성** 을 선택합니다.
1. 새 암호를 저장하려면 **찾아보기** 를 선택합니다.

    ![암호 생성](./media/backup-azure-manage-mars/passphrase.png)

1. **확인** 을 선택하여 변경 사항을 적용합니다.  Recovery Services 자격 증명 모음에 대한 Azure Portal에서 [보안 기능](./backup-azure-security-feature.md#enable-security-features)이 사용된 경우 보안 PIN을 입력하라는 메시지가 표시됩니다. PIN을 받으려면 이 [문서](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)에 나열된 단계를 따릅니다.<br>
1. 포털에서 보안 PIN을 붙여넣고 **확인** 을 선택하여 변경 내용을 적용합니다.<br>

    ![보안 PIN 붙여넣기](./media/backup-azure-manage-mars/passphrase2.png)
1. 암호를 원본 컴퓨터가 아닌 대체 위치(Azure Key Vault 권장)에 안전하게 저장하는 것이 좋습니다. MARS 에이전트를 사용하여 여러 컴퓨터를 백업하는 경우 모든 암호를 추적하세요.

## <a name="validate-passphrase"></a>암호 유효성 검사

MARS 에이전트 버전 2.0.9190.0 이상에서는 암호의 유효성을 검사하여 [업데이트된 요구 사항을](./backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-)충족하는지 확인해야 합니다.

암호의 유효성을 검사하려면 다음 단계를 수행합니다.

1. MARS 콘솔을 엽니다.

   암호의 유효성을 검사하라는 메시지가 맨 위에 표시됩니다. 

1. **유효성 검사** 를 클릭합니다.

   :::image type="content" source="./media/backup-azure-manage-mars/validate-passphrase-prompt-inline.png" alt-text="암호 유효성 검사 프롬프트를 보여주는 스크린샷." lightbox="./media/backup-azure-manage-mars/validate-passphrase-prompt-expanded.png":::

   암호 유효성 검사기가 열리고 현재 암호를 묻는 메시지가 표시됩니다. 암호가 업데이트된 요구 사항을 충족하지 않는 경우 암호를 다시 생성할 수 있는 옵션이 나타납니다.

1. 다음 세부 정보를 통해 암호를 생성합니다.

   - 요구 사항을 충족하는 새 암호입니다.
   - 보안 [PIN(보안 PIN을 생성하는 단계](#generate-security-pin)참조)
   - 새로 생성된 암호를 저장할 서버의 안전한 위치입니다.

   :::image type="content" source="./media/backup-azure-manage-mars/generate-passphrase.png" alt-text="필요한 세부 정보가 있는 암호를 생성하는 프로세스를 보여주는 스크린샷.":::

### <a name="validate-passphrase-for-dpmmabs-agent"></a>DPM/MABS 에이전트에 대한 암호 유효성 검사

DPM/MABS의 경우 관리자 권한 명령 프롬프트에서 암호 유효성 검사 도구를 실행합니다.
   
다음 위치 중 하나에서 도구를 찾을 수 있습니다.

- **System Center Data Protection Manager**
     
  %ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\PassphraseValidator.exe

- **Microsoft Azure Backup 서버**
      
  %ProgramFiles%\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\PassphraseValidator.exe

암호 유효성 검사기가 열리고 현재 암호를 묻는 메시지가 표시됩니다. 암호가 업데이트된 요구 사항을 충족하지 않는 경우 암호를 다시 생성합니다.
   
:::image type="content" source="./media/backup-azure-manage-mars/passphrase-validator-prompts-for-current-passphrase.png" alt-text="현재 암호에 대한 암호 유효성 검사기 프롬프트를 보여주는 스크린샷":::

다음 단계를 사용합니다.

1. 관리 콘솔에서 **관리** 탭으로 이동하고 **온라인**  ->  **구성을** 선택합니다.
1. 구독 **구성 설정 마법사** 에 따라 **암호화 설정** 단계에서 업데이트된 암호를 제공합니다.

:::image type="content" source="./media/backup-azure-manage-mars/configure-subscription-settings-wizard.png" alt-text="구독 설정 구성 마법사 다음의 암호를 제공하는 프로세스를 보여주는 스크린샷.":::

## <a name="generate-security-pin"></a>보안 PIN 생성

1. **Recovery Services 자격 증명 모음** -> **설정** -> **속성** 으로 이동합니다.
1. **보안 PIN** 에서 **생성** 을 선택합니다.
 
PIN을 복사합니다. PIN은 5 분 동안만 유효합니다.

## <a name="managing-backup-data-for-unavailable-machines"></a>사용할 수 없는 컴퓨터의 백업 데이터 관리

이 섹션에서는 MARS로 보호된 원본 컴퓨터가 삭제, 손상되거나 맬웨어/랜섬웨어에 감염 또는 폐기되어 더 이상 사용할 수 없게 되는 시나리오에 대해 설명합니다.

이러한 컴퓨터의 경우 Azure Backup 서비스는 최근 복구 지점이 백업 정책에 지정된 보존 규칙에 따라 만료되지 않도록 합니다(즉, 정리되지 않음). 따라서 컴퓨터를 안전하게 복원할 수 있습니다.  백업된 데이터에 대해 수행할 수 있는 다음 시나리오를 고려하세요.

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>시나리오 1: 원본 컴퓨터를 사용할 수 없으므로 더 이상 백업 데이터를 보존할 필요가 없는 경우

- [이 문서](backup-azure-delete-vault.md#delete-protected-items-on-premises)에 나열된 단계를 사용하여 Azure Portal에서 백업된 데이터를 삭제할 수 있습니다.

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>시나리오 2: 원본 컴퓨터를 사용할 수 없으므로 백업 데이터를 보존해야 하는 경우

MARS의 백업 정책을 관리하는 작업은 포털을 통해서가 아니라 MARS 콘솔을 통해 수행됩니다. 만료되기 전에 기존 복구 지점의 보존 설정을 확장해야 하는 경우 컴퓨터를 복원하고 MARS 콘솔을 설치하고 정책을 확장해야 합니다.

- 컴퓨터를 복원하려면 다음 단계를 수행합니다.
  1. [VM을 대체 대상 컴퓨터로 복원합니다.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. 원본 컴퓨터와 동일한 호스트 이름을 사용하여 대상 컴퓨터를 다시 만듭니다.
  1. 에이전트를 설치하고 동일한 암호를 사용하여 동일한 자격 증명 모음에 다시 등록합니다.
  1. MARS 클라이언트를 시작하여 요구 사항에 따라 보존 기간을 연장합니다.
- MARS를 사용하여 보호되는 새로 복원된 컴퓨터는 백업을 계속 수행합니다.  

## <a name="configuring-antivirus-for-the-mars-agent"></a>MARS 에이전트에 대한 바이러스 백신 구성

MARS 에이전트 작업과의 충돌을 방지하기 위해 바이러스 백신 소프트웨어에 대해 다음과 같은 구성을 사용하는 것이 좋습니다.

1. **경로 제외 추가**: 성능 저하 및 충돌 가능성을 방지하려면 바이러스 백신 소프트웨어의 실시간 모니터링에서 다음 경로를 제외합니다.
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` 및 하위 폴더
    1. **스크래치 폴더**: 스크래치 폴더가 표준 위치에 없으면 제외 항목에도 추가합니다.  스크래치 폴더 위치를 결정하는 [단계는 여기를 참조](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)하세요.
1. **바이너리 제외 추가**: 백업 및 콘솔 활동의 저하를 방지하려면 바이러스 백신 소프트웨어의 실시간 모니터링에서 다음 바이너리에 대한 프로세스를 제외합니다.
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>이러한 경로를 제외하면 대부분의 바이러스 백신 소프트웨어가 충분하지만 일부 경우에도 MARS 에이전트 작업에 방해가 될 수 있습니다. 예기치 않은 오류가 표시되면 바이러스 백신 소프트웨어를 일시적으로 제거하고 모니터링하여 문제가 발생하지 않도록 합니다. 문제가 해결되면 바이러스 백신 소프트웨어 공급업체에 적절한 제품 구성에 대한 지원을 문의하세요.

## <a name="monitor-using-backup-reports"></a>백업 보고서를 사용하여 모니터링

Azure Backup은 Azure Monitor 로그 및 Azure 통합 문서를 사용하는 보고 솔루션을 제공합니다. 시작하려면 자격 증명 모음에 대해 [Backup 보고서를 구성](configure-reports.md)해야 합니다. 구성되면 데이터는 작업 영역으로 전달되기 시작하고 백업 보고서를 사용하여 쿼리할 수 있습니다.

백업 데이터 사용량 및 일일 변동을 모니터링하려면 다음 단계를 수행합니다.

1. 자격 증명 모음의 **개요** 창으로 이동하고 **Backup 보고서** 를 클릭합니다.

1. **Backup 보고서** 블레이드의 **개요** 섹션에서 구성된 로그 분석 작업 영역을 선택합니다. 

1. MARS 에이전트 백업만 보려면 보고서 필터 **백업 솔루션** 을 **Azure Backup 에이전트** 로 설정합니다. 

   해당하는 경우 **구독 이름**, **자격 증명 모음 위치** 및 **자격 증명 모음 이름** 을 설정합니다.
 
    ![보고서 필터 백업 솔루션을 설정합니다.](./media/backup-azure-manage-mars/set-report-filter-backup-solution.png)

1. 청구 항목별 사용량을 보려면 **사용량** 탭으로 이동합니다. 

   청구되는 총 보호된 인스턴스 및 스토리지 사용 데이터가 표시됩니다. 추세 정보를 볼 수도 있습니다.
 
    ![청구된 엔터티의 사용량을 확인합니다.](./media/backup-azure-manage-mars/view-usage-by-billed-entity.png)

1. 보호된 서버의 각 볼륨에 대한 백업 작업으로 추가된 평균 백업 데이터를 보려면 **작업** 탭으로 이동합니다. 
 
    ![평균 백업 데이터를 봅니다.](./media/backup-azure-manage-mars/view-average-backup-data.png)

[다른 보고서 탭](configure-reports.md) 및 [이메일을 통해 해당 보고서 수신 방법](backup-reports-email.md)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

- 지원되는 시나리오 및 제한 사항에 대한 자세한 내용은 [MARS 에이전트에 대한 지원 매트릭스](./backup-support-matrix-mars-agent.md)를 참조하세요.
- [주문형 백업 정책 보존 동작](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)에 대해 자세히 알아봅니다.
- 더 자주 묻는 질문은 [MARS 에이전트 FAQ](backup-azure-file-folder-backup-faq.yml)를 참조하세요.