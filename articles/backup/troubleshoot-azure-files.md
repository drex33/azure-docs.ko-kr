---
title: Azure 파일 공유 백업 문제 해결
description: 이 문서에서는 Azure 파일 공유를 보호할 때 발생하는 문제를 해결하는 방법에 대한 내용을 설명합니다.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: eb3e1eadbf83e5a7eb1facc9e67ad707d1cf07f4
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430196"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Azure 파일 공유를 백업하는 동안 발생하는 문제 해결

이 문서에서는 Azure Backup Service를 사용하여 백업을 구성하거나 Azure 파일 공유를 복원하는 동안 발생하는 문제를 해결하기 위한 문제 해결 정보를 제공합니다.

## <a name="common-configuration-issues"></a>일반 구성 문제

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Azure 파일 공유에 대한 백업을 구성할 스토리지 계정을 찾을 수 없음

- 검색이 완료될 때까지 기다립니다.
- 스토리지 계정의 파일 공유가 이미 다른 Recovery Services 자격 증명 모음으로 보호되고 있는지 확인합니다.

  >[!NOTE]
  >스토리지 계정의 모든 파일 공유를 Recovery Services 자격 증명 모음 하나로 보호할 수 있습니다. [이 스크립트](scripts/backup-powershell-script-find-recovery-services-vault.md)를 사용하여 스토리지 계정이 등록된 Recovery Services 자격 증명 모음을 찾을 수 있습니다.

- 지원되지 않는 스토리지 계정에 파일 공유가 있지 않도록 주의해야 합니다. [Azure 파일 공유 백업에 대한 지원 매트릭스](azure-file-share-support-matrix.md)를 참조하여 지원되는 스토리지 계정을 찾을 수 있습니다.
- 스토리지 계정 및 복구 서비스 자격 증명 모음이 동일한 지역에 있는지 확인합니다.
- 스토리지 계정 이름과 리소스 그룹 이름의 결합 길이가 새 스토리지 계정의 경우 84자, 클래식 스토리지 계정의 경우 77자를 초과하지 않는지 확인합니다.
- 스토리지 계정의 방화벽 설정을 확인하여 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스하도록 허용하는 옵션이 사용하도록 설정되어 있는지 확인합니다.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>스토리지 계정의 포털 상태 검색이 실패했다는 오류가 발생합니다.

파트너 구독(CSP 사용)이 있는 경우 오류를 무시합니다. 구독이 CSP를 사용하지 않고 스토리지 계정을 검색할 수 없는 경우 고객 지원으로 문의합니다.

### <a name="selected-storage-account-validation-or-registration-failed"></a>선택한 스토리지 계정 유효성 검사 또는 등록 실패

등록을 다시 시도합니다. 문제가 계속되면 지원에 문의하세요.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>선택한 스토리지 계정에서 파일 공유를 나열하거나 찾을 수 없음

- 리소스 그룹에 스토리지 계정이 있는지 그리고 마지막 유효성 검사 또는 등록 후 자격 증명 모음에서 삭제 또는 이동되지는 않았는지 확인합니다.
- 보호하려는 파일 공유가 삭제되지 않았는지 확인합니다.
- 스토리지 계정이 파일 공유 백업이 지원되는 스토리지 계정인지 확인합니다. [Azure 파일 공유 백업에 대한 지원 매트릭스](azure-file-share-support-matrix.md)를 참조하여 지원되는 스토리지 계정을 찾을 수 있습니다.
- 파일 공유가 이미 동일한 Recovery Services 자격 증명 모음에서 보호되고 있는지 확인합니다.
- 라우팅 기본 설정이 Microsoft 네트워크 라우팅으로 설정되어 있는지 확인하려면 저장소 계정의 네트워크 라우팅 설정을 확인합니다.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>백업 파일 공유 구성(또는 보호 정책 구성)이 실패함

- 구성을 다시 시도하고 문제가 계속되는지 확인합니다.
- 보호하려는 파일 공유가 삭제되지 않았는지 확인합니다.
- 한 번에 여러 파일 공유를 보호하려고 하는데 그 중 일부가 실패하는 경우 실패한 파일 공유에 대해 백업을 다시 구성해 봅니다.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>파일 공유 보호를 해제한 후 Recovery Services 자격 증명 모음을 삭제할 수 없습니다.

Azure Portal에서 **자격 증명 모음** > **백업 인프라** > **스토리지 계정** 을 엽니다. **등록 취소** 를 선택하여 Recovery Services 자격 증명 모음에서 스토리지 계정을 제거합니다.

>[!NOTE]
>Recovery Services 자격 증명 모음은 자격 증명 모음에 등록된 모든 스토리지 계정의 등록을 취소한 후에만 삭제할 수 있습니다.

## <a name="common-backup-or-restore-errors"></a>일반 백업 또는 복원 오류

>[!NOTE]
>백업 또는 복원 작업을 수행할 수 있는 충분한 권한이 있는지 확인하려면 [이 문서](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup)를 참조합니다.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound- 파일 공유를 찾을 수 없어 작업이 실패했습니다

오류 코드: FileShareNotFound

오류 메시지: 파일 공유를 찾을 수 없어 작업이 실패했습니다

보호하려는 파일 공유가 삭제되지 않았는지 확인합니다.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable- 스토리지 계정을 찾을 수 없거나 지원되지 않습니다

오류 코드: UserErrorFileShareEndpointUnreachable

오류 메시지: 스토리지 계정을 찾을 수 없거나 지원되지 않습니다

- 리소스 그룹에 스토리지 계정이 있는지, 마지막 유효성 검사 후 리소스 그룹에서 삭제 또는 제거되지는 않았는지 확인합니다.

- 스토리지 계정이 파일 공유 백업이 지원되는 스토리지 계정인지 확인합니다.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached- 이 파일 공유의 스냅샷 상한에 도달했으며, 기존 스냅샷이 만료되면 더 만들 수 있습니다

오류 코드: AFSMaxSnapshotReached

오류 메시지: 이 파일 공유의 스냅샷 상한에 도달했으며, 기존 스냅샷이 만료되면 더 만들 수 있습니다.

- 파일 공유에 대한 여러 주문형 백업을 만들 때 이 오류가 발생할 수 있습니다.
- Azure Backup에서 만드는 스냅샷을 포함하여 파일 공유 당 최대 200개의 스냅샷 제한이 있습니다. 오래된 이전 백업(또는 스냅샷)은 자동으로 정리됩니다. 최대 한도에 도달할 경우 주문형 백업(또는 스냅샷)을 삭제해야 합니다.

Azure Files 포털에서 요청 시 백업(Azure 파일 공유 스냅샷)을 삭제합니다.

>[!NOTE]
> Azure Backup에서 만든 스냅샷을 삭제하면 복구 지점이 손실됩니다.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound- 지정한 스토리지 계정이 더 이상 존재하지 않아서 작업에 실패했습니다

오류 코드: UserErrorStorageAccountNotFound

오류 메시지: 지정한 스토리지 계정이 더 이상 존재하지 않아서 작업에 실패했습니다.

스토리지 계정이 여전히 있고 삭제되지 않았는지 확인합니다.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound- 제공한 스토리지 계정 세부 정보가 올바르지 않습니다

오류 코드: UserErrorDTSStorageAccountNotFound

오류 메시지: 제공한 스토리지 계정 세부 정보가 올바르지 않습니다.

스토리지 계정이 여전히 있고 삭제되지 않았는지 확인합니다.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound- 리소스 그룹이 존재하지 않습니다

오류 코드: UserErrorResourceGroupNotFound

오류 메시지: 리소스 그룹이 존재하지 않습니다

기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest- 이 파일 공유에 대한 백업 작업이 이미 진행 중입니다

오류 코드: ParallelSnapshotRequest

오류 메시지: 이 파일 공유에 대한 백업 작업이 이미 진행 중입니다.

- 파일 공유 백업은 동일한 파일 공유에 대한 병렬 스냅샷 요청을 지원하지 않습니다.

- 기존 백업 작업이 완료될 때까지 기다린 후 다시 시도합니다. Recovery Services 자격 증명 모음에서 백업 작업을 찾을 수 없으면 동일한 구독에 다른 Recovery Services 자격 증명 모음을 확인합니다.

### <a name="usererrorstorageaccountinternetroutingnotsupported--storage-accounts-with-internet-routing-configuration-are-not-supported-by-azure-backup"></a>UserErrorStorageAccountInternetRoutingNotSupported - 인터넷 라우팅 구성이 있는 저장소 계정은 Azure Backup에서 지원되지 않습니다.

오류 코드: UserErrorStorageAccountInternetRoutingNotSupported

오류 메시지: 인터넷 라우팅 구성을 사용한 저장소 계정은 Azure Backup에서 지원하지 않습니다.

백업된 파일 공유를 호스팅하는 저장소 계정에 대해 설정된 라우팅 기본 설정이 Microsoft 네트워크 라우팅인지 확인합니다.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling- 스토리지 서비스 제한으로 인해 파일 공유 백업 또는 복원이 실패했습니다. 스토리지 서비스가 특정 스토리지 계정에 대한 다른 요청을 처리 중이기 때문에 이 오류가 발생할 수 있습니다

오류 코드: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

오류 메시지: 스토리지 서비스 제한으로 인해 파일 공유 백업 또는 복원이 실패했습니다. 스토리지 서비스가 특정 스토리지 계정에 대한 다른 요청을 처리 중이기 때문에 이 오류가 발생할 수 있습니다.

백업/복원 작업을 나중에 다시 시도합니다.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound- 대상 파일 공유를 찾을 수 없습니다

오류 코드: TargetFileShareNotFound

오류 메시지: 대상 파일 공유를 찾을 수 없습니다.

- 선택한 스토리지 계정이 이미 있으며 대상 파일 공유가 삭제되지 않았는지 확인합니다.

- 스토리지 계정이 파일 공유 백업이 지원되는 스토리지 계정인지 확인합니다.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked- 스토리지 계정이 잠금 상태여서 백업 또는 복원 작업이 실패했습니다

오류 코드: UserErrorStorageAccountIsLocked

오류 메시지: 스토리지 계정이 잠금 상태여서 백업 또는 복원 작업이 실패했습니다.

스토리지 계정에 대한 잠금을 제거하거나 **읽기 잠금** 대신 **삭제 잠금** 을 사용하고 백업 또는 복원 작업을 다시 시도합니다.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached- 실패한 파일 수가 임계값을 초과하여 복구에 실패했습니다

오류 코드: DataTransferServiceCoFLimitReached

오류 메시지: 실패한 파일 수가 임계값을 초과하여 복구에 실패했습니다.

- 복구 실패 이유가 파일(작업 세부 정보에 제공되는 경로)에 나열됩니다. 오류를 해결하고 실패한 파일에 대해서만 복원 작업을 다시 시도합니다.

- 파일 복원이 실패하는 일반적인 이유:

  - 실패한 파일이 현재 사용 중
  - 실패한 파일과 같은 이름의 디렉터리가 부모 디렉터리에 있습니다.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover- 복구할 수 없는 파일이 없어 복구에 실패했습니다

오류 코드: DataTransferServiceAllFilesFailedToRecover

오류 메시지: 복구할 수 없는 파일이 없어 복구에 실패했습니다.

- 복구 실패 이유가 파일(작업 세부 정보에 제공되는 경로)에 나열됩니다. 오류를 해결하고 실패한 파일에 대해서만 복원 작업을 다시 시도합니다.

- 파일 복원이 실패하는 일반적인 이유:

  - 실패한 파일이 현재 사용 중
  - 실패한 파일과 같은 이름의 디렉터리가 부모 디렉터리에 있습니다.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid - 원본의 파일 중 하나가 없기 때문에 복원이 실패합니다

오류 코드: DataTransferServiceSourceUriNotValid

오류 메시지: 원본의 파일 중 하나가 없기 때문에 복원이 실패합니다.

- 선택한 항목이 복구 지점 데이터에 없습니다. 파일을 복구하려면 올바른 파일 목록을 제공하세요.
- 복구 지점에 해당하는 파일 공유 스냅샷은 수동으로 삭제됩니다. 다른 복구 지점을 선택하고 복원 작업을 다시 시도합니다.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked- 복구 작업이 동일한 대상으로 진행 중입니다

오류 코드: UserErrorDTSDestLocked

오류 메시지: 복구 작업이 동일한 대상으로 진행 중입니다.

- 파일 공유 백업은 동일한 대상 파일 공유에 대한 병렬 복구를 지원하지 않습니다.

- 기존 복구가 완료될 때까지 기다린 후 다시 시도합니다. Recovery Services 자격 증명 모음에서 복구 작업을 찾을 수 없으면 동일한 구독에 다른 Recovery Services 자격 증명 모음을 확인합니다.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull- 대상 파일 공유 가득 차서 복원 작업이 실패했습니다

오류 코드: UserErrorTargetFileShareFull

오류 메시지: 대상 파일 공유 가득 차서 복원 작업이 실패했습니다.

복원 데이터를 수용할 수 있도록 대상 파일 공유 크기 할당량을 늘리고 복원 작업을 다시 시도합니다.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient- 대상 파일 공유에 복원에 대한 스토리지 크기 할당량이 부족합니다

오류 코드: UserErrorTargetFileShareQuotaNotSufficient

오류 메시지: 대상 파일 공유에 복원에 대한 스토리지 크기 할당량이 부족합니다

복원 데이터를 수용할 수 있도록 대상 파일 공유 크기 할당량을 늘리고 작업을 다시 시도합니다

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed- 대상 파일 공유와 연결된 파일 동기화 서비스 리소스에서 사전 복원 작업을 수행하는 동안 발생한 오류로 인해 복원 작업이 실패했습니다

오류 코드: File Sync PreRestoreFailed

오류 메시지: 대상 파일 공유와 연결된 파일 동기화 서비스 리소스에서 사전 복원 작업을 수행하는 동안 발생한 오류로 인해 복원 작업이 실패했습니다.

데이터 복원을 나중에 다시 시도합니다. 문제가 지속되면 Microsoft 지원에 문의하세요.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress- 대상 파일 공유에 대한 Azure 파일 동기화 서비스 변경 검색이 진행 중입니다. 대상 파일 공유에 대한 이전 복원에서 변경 내용 검색을 트리거했습니다

오류 코드: AzureFileSyncChangeDetectionInProgress

오류 메시지: 대상 파일 공유에 대한 Azure 파일 동기화 서비스 변경 검색이 진행 중입니다. 대상 파일 공유에 대한 이전 복원에서 변경 내용 검색을 트리거했습니다.

다른 대상 파일 공유를 사용하세요. 또는 복원을 다시 시도하기 전에 대상 파일 공유에 대한 Azure 파일 동기화 서비스 변경 검색이 완료될 때까지 기다릴 수 있습니다.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored- 하나 이상의 파일을 성공적으로 복구할 수 없습니다. 자세한 내용은 위에 제공된 경로에서 실패한 파일 목록을 확인하세요

오류 코드: UserErrorAFSRecoverySomeFilesNotRestored

오류 메시지: 하나 이상의 파일을 성공적으로 복구할 수 없습니다. 자세한 내용은 위에 제공된 경로에서 실패한 파일 목록을 확인하세요.

- 복구 실패 이유가 파일(작업 세부 정보에 제공되는 경로)에 나열됩니다. 이유를 해결하고 실패한 파일에 대해서만 복원 작업을 다시 시도합니다.
- 파일 복원이 실패하는 일반적인 이유:

  - 실패한 파일이 현재 사용 중
  - 실패한 파일과 같은 이름의 디렉터리가 부모 디렉터리에 있습니다.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound- 복구 지점에 해당하는 Azure 파일 공유 스냅샷을 찾을 수 없습니다

오류 코드: UserErrorAFSSourceSnapshotNotFound

오류 메시지: 복구 지점에 해당하는 Azure 파일 공유 스냅샷을 찾을 수 없습니다

- 복구에 사용하려는 복구 지점에 해당하는 파일 공유 스냅샷이 여전히 존재하는지 확인합니다.

  >[!NOTE]
  >Azure Backup에서 만든 파일 공유 스냅샷을 삭제하면 해당 복구 지점을 사용할 수 없게 됩니다. 복구를 보장하려면 스냅샷을 삭제하지 않도록 권장합니다.

- 다른 복원 지점을 선택하여 데이터를 복구해보세요.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget- 다른 복원 작업이 동일한 대상 파일 공유에 대해 진행 중입니다

오류 코드: UserErrorAnotherRestoreInProgressOnSameTarget

오류 메시지: 다른 복원 작업이 동일한 대상 파일 공유에 대해 진행 중입니다

다른 대상 파일 공유를 사용하세요. 또는 취소하거나 다른 복원이 완료될 때까지 기다릴 수 있습니다.

## <a name="common-modify-policy-errors"></a>일반 수정 정책 오류

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation- 이 항목에 대해 다른 구성 보호 작업이 진행 중입니다

오류 코드: BMSUserErrorConflictingProtectionOperation

오류 메시지: 이 항목에 대해 다른 구성 보호 작업이 진행 중입니다.

이전 정책 수정 작업이 완료될 때까지 기다렸다가 나중에 다시 시도하세요.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked- 선택한 항목에 대해 다른 작업이 진행 중입니다

오류 코드: BMSUserErrorObjectLocked

오류 메시지: 선택한 항목에 대해 다른 작업이 진행 중입니다.

진행 중인 다른 작업이 완료될 때까지 기다렸다가 나중에 다시 시도하세요.

## <a name="common-soft-delete-related-errors"></a>일반 일시 삭제 관련 오류

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState- 이 지점과 연결된 스냅샷이 일시 삭제된 상태의 파일 공유에 있으므로 이 복원 지점을 사용할 수 없습니다

오류 코드: UserErrorRestoreAFSInSoftDeleteState

오류 메시지: 이 지점과 연결된 스냅샷이 일시 삭제된 상태의 파일 공유에 있으므로 이 복원 지점을 사용할 수 없습니다.

파일 공유가 일시 삭제된 상태면 복원 작업을 수행할 수 없습니다. 파일 포털에서 파일 공유를 삭제 취소하거나 또는 [삭제 취소 스크립트](scripts/backup-powershell-script-undelete-file-share.md)를 사용한 다음 복원을 시도합니다.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState- 복원 지점 스냅샷이 포함된 연결된 파일 공유가 영구적으로 삭제되었기 때문에 나열된 복원 지점을 사용할 수 없습니다

오류 코드: UserErrorRestoreAFSInDeleteState

오류 메시지: 복원 지점 스냅샷이 포함된 연결된 파일 공유가 영구적으로 삭제되었기 때문에 나열된 복원 지점을 사용할 수 없습니다.

백업된 파일 공유가 삭제되었는지 확인합니다. 일시 삭제 상태인 경우 일시 삭제 보존 기간이 끝났으며 다시 복구되지 않았는지 확인합니다. 이러한 경우 모든 스냅샷이 영구적으로 손실되고 데이터를 복구할 수 없습니다.

>[!NOTE]
> 모든 복원 지점이 손실되지 않도록 백업된 파일 공유를 삭제하지 않거나 일시 삭제 상태인 경우 일시 삭제 보존 기간이 끝나기 전에 삭제를 취소하도록 권장합니다.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState - Azure 파일 공유가 일시 삭제된 상태이기 때문에 백업에 실패했습니다

오류 코드: UserErrorBackupAFSInSoftDeleteState

오류 메시지: Azure 파일 공유가 일시 삭제된 상태이기 때문에 백업에 실패했습니다

**파일 포털** 에서 파일 공유 삭제를 취소하거나 [삭제 취소 스크립트](scripts/backup-powershell-script-undelete-file-share.md)를 사용하여 백업을 계속하고 데이터 영구 삭제를 방지합니다.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState- 연결된 Azure 파일 공유가 영구적으로 삭제되어 백업에 실패했습니다

오류 코드: UserErrorBackupAFSInDeleteState

오류 메시지: 연결된 Azure 파일 공유가 영구적으로 삭제되어 백업에 실패했습니다

백업된 파일 공유가 영구적으로 삭제되었는지 확인합니다. 삭제되었다면 반복되는 백업 실패를 방지하기 위해 파일 공유에 대한 백업을 중지합니다. 보호를 중지하는 방법에 대해 알아보려면 [Azure 파일 공유에 대한 보호 중지](./manage-afs-backup.md#stop-protection-on-a-file-share)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 백업에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 파일 공유 백업](backup-afs.md)
- [Azure 파일 공유 백업 FAQ](backup-azure-files-faq.yml)
