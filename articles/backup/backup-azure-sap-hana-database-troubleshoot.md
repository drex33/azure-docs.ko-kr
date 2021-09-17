---
title: SAP HANA 데이터베이스 백업 오류 문제 해결
description: Azure Backup를 사용하여 SAP HANA 데이터베이스를 백업하는 경우 발생할 수 있는 일반적인 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 05/31/2021
ms.openlocfilehash: 1af52116a08344d36a9364ac9e3b4f468e0abb83
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451964"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure의 SAP HANA 데이터베이스 백업 문제 해결

이 문서에서는 Azure Virtual Machines의 SAP HANA 데이터베이스 백업에 대한 문제 해결 정보를 제공합니다. 현재 지원되는 SAP HANA 백업 시나리오에 대한 자세한 내용은 [시나리오 지원](sap-hana-backup-support-matrix.md#scenario-support)을 참조하세요.

## <a name="prerequisites-and-permissions"></a>필수 조건 및 권한

백업을 구성하기 전에 [필수 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

## <a name="common-user-errors"></a>일반 사용자 오류

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **오류 메시지**      | `Azure Backup does not have required role  privileges to carry out Backup and Restore operations`    |
| ---------------------- | ------------------------------------------------------------ |
| **가능한 원인**    | Backup 사용자(AZUREWLBACKUPHANAUSER)에 **할당된 SAP_INTERNAL_HANA_SUPPORT** 역할이 없거나 역할을 덮어쓸 수 있는 경우 이 오류로 인해 모든 작업이 실패합니다.                          |
| **권장 작업** | SAP HANA 인스턴스에서 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다운로드하여 실행하거나 **SAP_INTERNAL_HANA_SUPPORT** 역할을 백업 사용자(AZUREWLBACKUPHANAUSER)에게 수동으로 할당합니다.<br><br>**참고**<br><br>HANA 2.0 SPS04 Rev 46 이상 버전을 사용하는 경우 이러한 HANA 버전에서 **SAP_INTERNAL_HANA_SUPPORT** 역할의 사용이 더 이상 사용되지 않으므로 이 오류가 발생하지 않습니다. |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| **오류 메시지**      | `Failed to connect to HANA system`                        |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | <ul><li>HANA 인스턴스에 연결하지 못한 경우</li><li>시스템 DB가 오프라인인 경우</li><li>테넌트 DB가 오프라인인 경우</li><li>백업 사용자(AZUREWLBACKUPHANAUSER)에 충분한 권한/권한이 없습니다.</li></ul> |
| **권장 작업** | 시스템이 실행 중인지 확인합니다. 데이터베이스가 실행 중이면 SAP HANA 인스턴스에서 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다운로드하고 실행하여 필요한 권한이 설정되었는지 확인합니다. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| **오류 메시지**      | `The specified SAP HANA instance is either invalid or can't be found`  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | <ul><li>지정된 SAP HANA 인스턴스가 잘못되었거나 찾을 수 없습니다.</li><li>단일 Azure VM의 여러 SAP HANA 인스턴스는 백업할 수 없습니다.</li></ul> |
| **권장 작업** | <ul><li>하나의 HANA 인스턴스만 Azure VM에서 실행되고 있는지 확인합니다.</li><li>DB 검색 창([여기](https://aka.ms/scriptforpermsonhana)에서 찾을 수도 있음)에서 스크립트를 올바른 SAP HANA 인스턴스로 실행하여 문제를 해결합니다.</li></ul> |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| **오류 메시지**      | `Backup log chain is broken`                                    |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | HANA LSN 로그 체인 중단은 다음과 같은 다양한 이유로 트리거될 수 있습니다.<ul><li>Azure Storage 호출이 백업을 커밋하지 못했습니다.</li><li>테넌트 DB가 오프라인 상태입니다.</li><li>확장 업그레이드가 진행 중인 백업 작업을 종료했습니다.</li><li>백업하는 동안 Azure Storage에 연결할 수 없습니다.</li><li>SAP HANA는 백업 프로세스의 트랜잭션을 롤백합니다.</li><li>백업이 완료되었지만 HANA 시스템에서 카탈로그가 아직 업데이트되지 않았습니다.</li><li>Azure Backup 관점에서 백업에 실패했지만 HANA의 관점에서 성공 - 로그 백업/카탈로그 대상이 backint에서 파일 시스템으로 업데이트되었거나 backint 실행 파일이 변경되었을 수 있습니다.</li></ul> |
| **권장 작업** | 이 문제를 해결하기 위해 Azure Backup이 자동 복구 전체 백업을 트리거합니다. 이 자동 복구 백업이 진행되는 동안 모든 로그 백업은 **OperationCancelledBecauseConflictingAutohealOperationRunningUserError** 와 함께 실패하여 HANA에 의해 트리거됩니다. 자동 복구 전체 백업이 완료되면 로그 및 기타 모든 백업이 예상대로 작동을 시작합니다.<br>자동 복구 전체 백업이 트리거되고 24시간 동안 성공한 백업(전체/차등/증분)이 표시되지 않는 경우 Microsoft 지원에 문의하세요.</br> |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| **오류 메시지**      | `SDC to MDC upgrade detected.`                                   |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SDC 시스템이 MDC로 업그레이드되면 백업이 이 오류와 함께 실패합니다. |
| **권장 작업** | 문제를 해결하려면 [SDC에서 MDC로 업그레이드](#sdc-to-mdc-upgrade-with-a-change-in-sid)를 참조하세요. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| **오류 메시지**      | `Backups will fail with this error when the Backint Configuration is incorrectly updated.`                       |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | Azure Backup에 의한 보호 구성 흐름 중에 업데이트된 backint 구성은 고객이 변경하거나 업데이트하는 것입니다. |
| **권장 작업** | 다음(backint) 매개 변수가 설정되었는지 확인합니다.<br><ul><li> [catalog_backup_using_backint:true]</li><li> [enable_accumulated_catalog_backup:false]</li><li> [parallel_data_backup_backint_channels:1]</li><li> [log_backup_timeout_s:900)]</li><li> [backint_response_timeout:7200]</li></ul>backint 기반 매개 변수가 HOST 수준에 있는 경우 제거합니다. 그러나 매개 변수가 호스트 수준에는 없지만 데이터베이스 수준에서 수동으로 수정된 경우에는 데이터베이스 수준 값이 위에 설정되어 있는지 확인합니다. 또는 Azure Portal에서 [백업 데이터 유지로 보호 중지](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)를 실행한 다음 백업 다시 시작을 선택합니다. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|**오류 메시지**  | `The source and target systems for restore are incompatible.`  |
|---------|---------|
|**가능한 원인**   | 원본 및 대상 HANA 데이터베이스와 시스템이 호환되지 않는 경우 이 오류와 함께 복원 흐름이 실패합니다. |
|권장 작업   |   복원 시나리오가 다음과 같은 호환되지 않는 복원 목록에 있지 않은지 확인합니다.<br> **사례 1:** 복원하는 동안 SYSTEMDB의 이름을 바꿀 수 없습니다.<br>**사례 2:** 원본 - SDC 및 대상 - MDC: 원본 데이터베이스를 대상의 SYSTEMDB 또는 테넌트 DB로 복원할 수 없습니다. <br> **사례 3:** 원본 - MDC 및 대상 - SDC: 원본 데이터베이스(SYSTEMDB 또는 테넌트 DB)를 대상으로 복원할 수 없습니다.<br>자세한 내용은 [SAP 지원 실행 패드](https://launchpad.support.sap.com)의 **1642148** 메모를 참조하세요. |

### <a name="usererrorhanapodoesnotexist"></a>UserErrorHANAPODoesNotExist

**오류 메시지** | `Database configured for backup does not exist.`
--------- | --------------------------------
**가능한 원인** | 백업용으로 구성된 데이터베이스가 삭제된 경우 이 데이터베이스에 대해 예약된 백업과 임시 백업이 모두 실패합니다.
**권장 작업** | 데이터베이스가 삭제되었는지 확인합니다. 데이터베이스를 다시 만들거나 데이터베이스에 대한 [보호를 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)(데이터 보유 여부에 관계없이)합니다.

### <a name="usererrorinsufficientprivilegeofdatabaseuser"></a>UserErrorInsufficientPrivilegeOfDatabaseUser

**오류 메시지** |    `Azure Backup does not have enough privileges to carry out Backup and Restore operations.`
---------- | ---------
**가능한 원인** | 사전 등록 스크립트에서 만든 백업 사용자(AZUREWLBACKUPHANAUSER)에 할당된 다음 역할 중 하나 이상이 없습니다.<ul><li>MDC, DATABASE ADMIN 및 BACKUP ADMIN(HANA 2.0 SPS05 이상의 경우): 복원 중에 새 데이터베이스를 만듭니다.</li><li>SDC, BACKUP ADMIN: 복원하는 동안 새 데이터베이스를 만듭니다.</li><li>CATALOG READ: 백업 카탈로그를 읽습니다.</li><li>SAP_INTERNAL_HANA_SUPPORT: 몇 개의 프라이빗 테이블에 액세스합니다. HANA 2.0 SPS04 Rev 46 이전의 SDC 및 MDC 버전에만 필요합니다. HANA 팀의 픽스를 통해 현재 공용 테이블에서 필요한 정보를 가져오기 때문에 HANA 2.0 SPS04 Rev 46 이상에서는 필요하지 않습니다.</li></ul>
**권장 작업** | 문제를 해결하려면 백업 사용자(AZUREWLBACKUPHANAUSER)에게 필요한 역할 및 권한을 수동으로 추가하거나 [SAP HANA 인스턴스](https://aka.ms/scriptforpermsonhana)에서 사전 등록 스크립트를 다운로드하여 실행합니다.

### <a name="usererrordatabaseuserpasswordexpired"></a>UserErrorDatabaseUserPasswordExpired

**오류 메시지** | `Database/Backup user's password expired.`
----------- | -----------
**가능한 원인** | 사전 등록 스크립트에서 만든 데이터베이스/백업 사용자가 암호에 대한 만료를 설정하지 않습니다. 그러나 변경된 경우 이 오류가 표시될 수 있습니다.
**권장 작업** | 문제를 해결하려면 SAP HANA 인스턴스에서 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다운로드하고 실행합니다.

### <a name="usererrorinconsistentssfs"></a>UserErrorInconsistentSSFS

**오류 메시지** | `SAP HANA error`
------------ | ----------
**가능한 원인** | SAP HANA 엔진에서 받은 SSFS(Secure Storage File System) 오류가 일치하지 않습니다.
**권장 작업** | SAP HANA 팀과 협력하여 이 문제를 해결하세요. 자세한 내용은 SAP Note **0002097613** 을 참조하세요.

### <a name="usererrorcannotconnecttoazureactivedirectoryservice"></a>UserErrorCannotConnectToAzureActiveDirectoryService

**오류 메시지** | `Unable to connect to the AAD service from the HANA system.`
--------- | --------
**가능한 원인** | Backup 확장의 플러그 인 서비스 계정인 방화벽 또는 프록시 설정은 AAD에 대한 아웃바운드 연결을 허용하지 않습니다.
**권장 작업** | AAD에 대한 아웃바운드 연결에 성공하려면 방화벽 또는 프록시 설정을 수정합니다.

### <a name="usererrormisconfiguredsslcastore"></a>UserErrorMisConfiguredSslCaStore

**오류 메시지** | `Misconfigured CA store`
-------- | -------
**가능한 원인** | 백업 확장의 플러그인 호스트 프로세스가 루트 CA 저장소(SLES의 경우 _/var/lib/ca-certificates/ca-bundle.pem_ 에 있음)에 액세스할 수 없습니다.
**권장 작업** | `chmod o+r`을 사용하여 원래 권한을 복원하여 CA 저장소 문제를 수정합니다.  그런 다음 백업 및 복원을 수행하기 위해 플러그 인 호스트 서비스를 다시 시작합니다.

### <a name="usererrorbackupfailedasremedialbackupinprogress"></a>UserErrorBackupFailedAsRemedialBackupInProgress

**오류 메시지** | `Remedial Backup in progress.`
---------- | -------
**가능한 원인** | Azure Backup이 수정 전체 백업을 트리거하여 LSN 로그 체인 중단을 처리합니다. 이 수정이 가득 찬 상태에서 포털/CLI를 통해 트리거된 백업(전체/차등/증분)이 이 오류와 함께 실패합니다.
**권장 작업** | 다른 백업을 트리거하기 전에 수정 전체 백업이 성공적으로 완료될 때까지 기다립니다.

### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

**오류 메시지** | `Conflicting operation in progress.`
----------- | -------------
**가능한 원인** | 다른 전체/차등/증분 백업이 이미 진행 중인 상태에서 포털/CLI/기본 HANA 클라이언트를 통해 전체/차등/증분 백업이 트리거됩니다.
**권장 작업** | 새 전체/델타 백업을 트리거하기 전에 활성 백업 작업이 완료될 때까지 기다립니다.

### <a name="operationcancelledbecauseconflictingautohealoperationrunning-usererror"></a>OperationCancelledBecauseConflictingAutohealOperationRunning UserError

**오류 메시지** | `Auto-heal Full backup in progress.`
------- | -------
**가능한 원인** | Azure Backup은 **UserErrorHANALSNValidationFailure** 를 해결하기 위해 자동 복구 전체 백업을 트리거합니다. 이 자동 복구 백업이 진행되는 동안 HANA에 의해 트리거된 모든 로그 백업이 **OperationCancelledBecauseConflictingAutohealOperationRunningUserError** 와 함께 실패합니다.<br>자동 복구 전체 백업이 완료되면 로그 및 기타 모든 백업이 예상대로 작동을 시작합니다.</br>
**권장 작업** | 새 전체/델타 백업을 트리거하기 전에 자동 복구 전체 백업이 완료될 때까지 기다립니다.

### <a name="usererrorhanaprescriptnotrun"></a>UserErrorHanaPreScriptNotRun

**오류 메시지** | `Pre-registration script not run.`
--------- | --------
**가능한 원인** | 환경 설정에 대한 SAP HANA 사전 등록 스크립트가 실행되지 않았습니다.
**권장 작업** | SAP HANA 인스턴스에서 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다운로드하고 실행합니다.


### <a name="usererrortargetpoexistsoverwritenotspecified"></a>UserErrorTargetPOExistsOverwriteNotSpecified

**오류 메시지** | `Target database cannot be overwritten for Restore.`
------- | -------
**가능한 원인** | 대상 데이터베이스가 있지만 덮어쓸 수 없습니다. 강제 덮어쓰기 는 포털/CLI의 복원 흐름에서 설정되지 않습니다.
**권장 작업** | 강제 덮어쓰기 옵션을 선택하여 데이터베이스를 복원하거나 다른 대상 데이터베이스로 복원하세요.

### <a name="usererrorrecoverysysscriptfailedtotriggerrestore"></a>UserErrorRecoverySysScriptFailedToTriggerRestore

**오류 메시지** | `RecoverySys.py could not be run successfully to restore System DB.`
-------- | ---------
**가능한 원인** | 시스템 DB 복원에 실패할 수 있는 원인은 다음과 같습니다.<ul><li>Azure Backup이 HANA 컴퓨터에서 **Recoverysys.py** 를 찾을 수 없습니다. HANA 환경이 제대로 설정되지 않은 경우에 발생합니다.</li><li>**Recoverysys.py** 가 있지만 HANA를 호출하여 복원을 수행하기 위해 이 스크립트를 트리거하지 못했습니다.</li><li>Recoverysys.py가 복원을 수행하기 위해 HANA를 성공적으로 호출했지만 HANA가 복원에 실패했습니다.</li></ul>
**권장 작업** | <ul><li>문제 1의 경우 SAP HANA 팀과 협력하여 문제를 해결하세요.</li><li>2 및 3의 경우 sid-adm 프롬프트에서 HDSetting.sh 명령을 실행하여 로그 추적을 참조하세요. 예: _/usr/sap/SID/HDB00/HDBSetting.sh_.</li></ul>이러한 결과를 SAP HANA 팀과 공유하여 문제를 해결하세요.

### <a name="usererrordbnamenotincorrectformat"></a>UserErrorDBNameNotInCorrectFormat

**오류 메시지** | `Restored database name not in correct format.`
--------- | --------
**가능한 원인** | 제공한 복원된 데이터베이스 이름이 허용되는/예상 형식이 아닙니다.
**권장 작업** | 복원된 데이터베이스 이름이 문자로 시작하고 숫자 또는 밑근 이외의 기호를 포함하지 않아야 합니다.<br>최대 127자만 포함할 수 있으며 "\_SYS_\"로 시작할 수 없습니다.

### <a name="usererrordefaultsidadmdirectorychanged"></a>UserErrorDefaultSidAdmDirectoryChanged

**오류 메시지** | `Default sid-adm directory changed.`
------- | -------
**가능한 원인** | 기본 **sid-adm** 디렉터리가 변경되었으며 이 기본 디렉터리에서 **HDBSetting.sh** 를 사용할 수 없습니다.
**권장 작업** | HXE가 SID인 경우 환경 변수 HOME이 **sid-adm** 사용자로 _/usr/sap/HXE/home_ 으로 설정되어 있는지 확인합니다.

### <a name="usererrorhdbsettingsscriptnotfound"></a>UserErrorHDBsettingsScriptNotFound

**오류 메시지** | `HDBSetting.sh file cannot be found.`
--------- | -------
**가능한 원인** | **&lt;sid&gt;adm** 사용자 환경이 복원을 트리거할 **HDBsettings.sh** 파일을 찾을 수 없어 시스템 데이터베이스 복원에 실패했습니다.
**권장 작업** | SAP HANA 팀과 협력하여 이 문제를 해결하세요.<br><br>HXE가 SID인 경우 환경 변수 HOME이 **sid-adm** 사용자로 _/usr/sap/HXE/home_ 으로 설정되어 있는지 확인합니다.

## <a name="restore-checks"></a>복원 확인

### <a name="single-container-database-sdc-restore"></a>SDC(단일 컨테이너 데이터베이스) 복원

HANA용 SDC(단일 컨테이너 데이터베이스)를 다른 SDC 머신으로 복원하는 동안 입력에 유의합니다. 데이터베이스 이름에는 소문자를 지정하고 괄호로 묶은 "sdc"를 앞에 추가해야 합니다. HANA 인스턴스는 대문자로 표시됩니다.

SDC HANA 인스턴스 "H21"이 백업되었다고 가정합니다. 백업 항목 페이지에서 백업 항목 이름이 **"h21(sdc)"** 으로 표시됩니다. 이 데이터베이스를 다른 대상 SDC(예: H11)로 복원하려고 하는 경우 다음 입력을 제공해야 합니다.

![복원된 SDC 데이터베이스 이름](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

다음 사항에 유의하세요.

- 기본적으로 복원된 db 이름에는 백업 항목 이름이 채워집니다. 이 경우 h21(sdc)입니다.
- 대상을 H11로 선택해도 복원된 db 이름은 자동으로 변경되지 않습니다. **h11(sdc)로 편집해야 합니다**. SDC와 관련하여 복원된 db 이름은 괄호로 묶은 소문자 'sdc'를 앞에 붙인 대상 인스턴스 ID입니다.
- SDC에는 단일 데이터베이스만 있을 수 있으므로 기존 데이터베이스 데이터를 복구 지점 데이터로 재정의하려면 확인란을 선택해야 합니다.
- Linux는 대/소문자를 구분합니다. 따라서 대/소문자를 유지해야 합니다.

### <a name="multiple-container-database-mdc-restore"></a>MDC(여러 컨테이너 데이터베이스) 복원

HANA에 대한 여러 컨테이너 데이터베이스에서 표준 구성은 SYSTEMDB + 1개 이상의 테넌트 DB입니다. 전체 SAP HANA 인스턴스를 복원하는 것은 SYSTEMDB와 테넌트 DB를 모두 복원하는 것입니다. 먼저 SYSTEMDB를 복원한 다음, 테넌트 DB의 복원을 진행합니다. 시스템 DB는 기본적으로 선택한 대상의 시스템 정보를 재정의합니다. 또한 이 복원은 대상 인스턴스의 BackInt 관련 정보를 재정의합니다. 따라서 시스템 DB가 대상 인스턴스로 복원된 후 사전 등록 스크립트를 다시 실행합니다. 그래야만 후속 테넌트 DB 복원 작업이 성공합니다.

## <a name="back-up-a-replicated-vm"></a>복제된 VM 백업

### <a name="scenario-1"></a>시나리오 1

원본 VM은 Azure Site Recovery 또는 Azure VM 백업을 사용하여 복제되었습니다. 새 VM은 이전 VM을 시뮬레이트하도록 빌드되었습니다. 따라서 설정이 정확히 동일합니다. 원래 VM이 삭제되었으며 VM 백업 또는 Azure Site Recovery에서 복원이 수행되었기 때문입니다.

이 시나리오에는 두 가지 가능한 사례가 포함될 수 있습니다. 이러한 두 경우 모두에서 복제된 VM을 백업하는 방법을 알아봅니다.

1. 만든 새 VM의 이름은 동일하며 삭제된 VM과 동일한 리소스 그룹 및 구독에 있습니다.

    - 확장이 VM에 이미 있지만 어떤 서비스에도 표시되지 않습니다.
    - 사전 등록 스크립트 실행
    - Azure Portal에서 동일한 머신에 대한 확장을 다시 등록합니다(**백업** -> **세부 정보 보기** -> 관련 Azure VM 선택 -> 다시 등록).
    - 그러면 기존에 백업한 데이터베이스(삭제된 VM)가 성공적으로 백업되기 시작합니다.

2. 새로 만든 VM에는 다음 중 하나가 지정됩니다.

    - 삭제된 VM과 다른 이름
    - 삭제된 VM과 이름이 같지만 삭제된 VM과 비교하여 다른 리소스 그룹 또는 구독에 있습니다.

    이 경우 다음 단계를 수행합니다.

    - 확장이 VM에 이미 있지만 어떤 서비스에도 표시되지 않습니다.
    - 사전 등록 스크립트 실행
    - 새 데이터베이스를 검색하고 보호하는 경우 포털에 중복된 활성 데이터베이스가 표시되기 시작합니다. 이 문제를 방지하려면 이전 데이터베이스에 대해 [데이터 보관을 통해 보호를 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)합니다. 그런 다음, 나머지 단계를 계속 진행합니다.
    - 백업을 사용하도록 설정할 데이터베이스 검색
    - 이러한 데이터베이스에 대해 백업 사용
    - 기존에 백업한 데이터베이스(삭제된 VM)는 계속 자격 증명 모음에 저장되고 해당 백업은 정책에 따라 보존됩니다.

### <a name="scenario-2"></a>시나리오 2

원본 VM은 Azure Site Recovery 또는 Azure VM 백업을 사용하여 복제되었습니다. 새 VM은 템플릿으로 사용되는 콘텐츠에서 빌드되었습니다. 이 VM은 새 SID를 사용하는 새 VM입니다.

다음 단계에 따라 새 VM에서 백업을 사용하도록 설정하세요.

- 확장이 VM에 이미 있지만 어떤 서비스에도 표시되지 않습니다.
- 사전 등록 스크립트를 실행합니다. 새 VM의 SID를 기준으로 다음과 같은 두 가지 시나리오가 발생할 수 있습니다.
  - 원래 VM과 새 VM의 SID가 동일합니다. 사전 등록 스크립트가 성공적으로 실행됩니다.
  - 원래 VM과 새 VM의 SID가 다릅니다. 사전 등록 스크립트가 실패합니다. 이 시나리오에 대해 도움이 필요하면 지원 서비스에 문의하세요.
- 백업하려는 데이터베이스를 검색합니다.
- 이러한 데이터베이스에 대해 백업을 사용하도록 설정합니다.

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>동일한 VM에서 SDC 버전 업그레이드 또는 MDC 버전 업그레이드

SID 변경을 발생시키지 않는 OS 업그레이드, SDC 버전 변경 또는 MDC 버전 변경은 다음과 같이 처리할 수 있습니다.

- 새 OS 버전, SDC 또는 MDC 버전이 현재 [Azure Backup에서 지원](sap-hana-backup-support-matrix.md#scenario-support)되는지 확인합니다.
- 데이터베이스에 대해 [데이터 보관을 통해 보호 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 업그레이드 또는 업데이트 수행
- 사전 등록 스크립트를 다시 실행합니다. 업그레이드 프로세스에서 [필요한 역할](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)을 제거하는 경우가 종종 있습니다. 사전 등록 스크립트는 필요한 모든 역할을 확인하는 데 유용합니다.
- 데이터베이스에 대한 보호 재개

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SID를 변경하지 않으면서 SDC에서 MDC로 업그레이드

SID 변경을 발생시키지 않는 SDC에서 MDC로의 업그레이드는 다음과 같이 처리할 수 있습니다.

- 새 MDC 버전이 현재 [Azure Backup에서 지원](sap-hana-backup-support-matrix.md#scenario-support)되는지 확인합니다.
- 이전 SDC 데이터베이스에 대해 [데이터 보관을 통해 보호를 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)합니다.
- 업그레이드를 수행합니다. 완료되면 HANA 시스템은 이제 시스템 DB 및 테넌트 DB가 있는 MDC가 됩니다.
- [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다시 실행합니다.
- Azure Portal에서 동일한 머신에 대한 확장을 다시 등록합니다(**백업** -> **세부 정보 보기** -> 관련 Azure VM 선택 -> 다시 등록).
- 동일한 VM에 대해 **DB 다시 검색** 을 선택합니다. 이 작업을 수행하면 3단계의 새 DB가 SDC가 아닌 SYSTEMDB 및 테넌트 DB로 표시됩니다.
- 이전 SDC 데이터베이스는 계속 자격 증명 모음에 존재하며 이전에 백업된 데이터는 정책에 따라 보존됩니다.
- 이러한 데이터베이스에 대한 백업 구성

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SID를 변경하면서 SDC에서 MDC로 업그레이드

SID를 변경하는 SDC에서 MDC로의 업그레이드는 다음과 같이 처리할 수 있습니다.

- 새 MDC 버전이 현재 [Azure Backup에서 지원](sap-hana-backup-support-matrix.md#scenario-support)되는지 확인합니다.
- 이전 SDC 데이터베이스에 대해 **데이터 보관을 통해 보호를 중지** 합니다.
- 업그레이드를 수행합니다. 완료되면 HANA 시스템은 이제 시스템 DB 및 테넌트 DB가 있는 MDC가 됩니다.
- 올바른 세부 정보(새 SID 및 MDC)를 사용하여 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다시 실행합니다. SID의 변경으로 인해 스크립트를 성공적으로 실행하는 데 문제가 있을 수 있습니다. 문제가 발생하는 경우 Azure Backup 지원 서비스에 문의하세요.
- Azure Portal에서 동일한 머신에 대한 확장을 다시 등록합니다(**백업** -> **세부 정보 보기** -> 관련 Azure VM 선택 -> 다시 등록).
- 동일한 VM에 대해 **DB 다시 검색** 을 선택합니다. 이 작업을 수행하면 3단계의 새 DB가 SDC가 아닌 SYSTEMDB 및 테넌트 DB로 표시됩니다.
- 이전 SDC 데이터베이스는 계속 자격 증명 모음에 존재하며 이전에 백업된 데이터는 정책에 따라 보존됩니다.
- 이러한 데이터베이스에 대한 백업 구성

## <a name="re-registration-failures"></a>다시 등록 오류

다시 등록 작업을 트리거하기 전에 하나 이상의 다음 증상을 확인하세요.

- 다음 오류 코드 중 하나를 사용하여 VM에서 모든 작업(예: 백업, 복원 및 구성 백업)이 실패합니다. **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- 백업 항목에 대한 **백업 상태** 영역에 **연결할 수 없음** 이 표시되면 동일한 상태를 유발하는 다른 모든 원인을 제외합니다.

  - VM에서 백업 관련 작업을 수행할 수 있는 권한이 없습니다.
  - VM이 종료되었으므로 백업을 수행할 수 없습니다.
  - 네트워크 문제

다음 이유 중 하나 이상으로 인해 이러한 현상이 발생할 수 있습니다.

- 확장이 삭제되었거나 포털에서 제거되었습니다.
- VM이 내부 디스크 복원을 통해 다시 복원되었습니다.
- 오랜 기간 동안 VM이 종료되었으므로 확장 구성이 만료되었습니다.
- VM이 삭제되고 삭제된 VM과 이름이 같고 동일한 리소스 그룹에 있는 다른 VM이 생성되었습니다.

위의 시나리오에서 VM에 대한 다시 등록 작업을 트리거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- Azure VM의 SAP HANA 데이터베이스 백업에 대해서는 [질문과 대답](./sap-hana-faq-backup-azure-vm.yml)을 검토하세요.
