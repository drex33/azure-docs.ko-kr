---
title: Azure 독일에서 글로벌 Azure로 Azure 관리 도구 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 관리 도구를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 06/22/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 157dded56e3323ed6389f84bd173ba435e8bdc5b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "122528074"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>관리 도구 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closure info](../../includes/germany-closure-info.md)]

이 문서에는 Azure 독일에서 글로벌 Azure로 Azure 관리 도구를 마이그레이션하는 데 도움이 되는 정보가 있습니다.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager는 원활한 마이그레이션을 완료하는 데 도움이 될 수 있습니다. 그러나 Azure 독일에서 만든 Traffic Manager 프로필을 글로벌 Azure로 마이그레이션할 수 없습니다. (마이그레이션하는 동안 Traffic Manager 엔드포인트를 대상 환경으로 마이그레이션하므로 Traffic Manager 프로필을 업데이트해야 합니다.)

원본 환경에서 실행 중인 동안 Traffic Manager를 사용하여 대상 환경에서 추가 엔드포인트를 정의할 수 있습니다. Traffic Manager가 새 환경에서 실행 중인 경우 원본 환경에서 아직 마이그레이션하지 않은 엔드포인트를 계속 정의할 수 있습니다. 이 시나리오를 [파란색-녹색 시나리오](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)라고 합니다. 시나리오에는 다음 단계가 포함됩니다.

1. 글로벌 Azure에서 새 Traffic Manager 프로필을 만듭니다.
1. Azure 독일에서 엔드포인트를 정의합니다.
1. DNS CNAME 레코드를 새 Traffic Manager 프로필로 변경합니다.
1. 이전 Traffic Manager 프로필을 끕니다.
1. 엔드포인트를 마이그레이션하고 구성합니다. Azure 독일의 각 엔드포인트의 경우:
   1. 엔드포인트를 글로벌 Azure로 마이그레이션합니다.
   1. 새 엔드포인트를 사용하도록 Traffic Manager 프로필을 변경합니다.

추가 정보는 다음 항목을 참조하세요.

- [Traffic Manager 자습서](../traffic-manager/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Traffic Manager 개요](../traffic-manager/traffic-manager-overview.md)를 검토합니다.
- [Traffic Manager 프로필을 만드는 방법](../traffic-manager/quickstart-create-traffic-manager-profile.md)을 알아보세요.
- [파란색-녹색 시나리오](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)에 대해 읽습니다.

## <a name="azure-backup"></a>Azure Backup

Azure Backup 서비스는 데이터를 백업하고 Microsoft Azure 클라우드에서 복구할 수 있는 간단하고, 안전하며, 비용 효율적인 솔루션을 제공합니다. 이제 PowerShell cmdlet을 통해 GEC(독일 중부) 및 GNE(독일 북동부)에서 GWC(독일 서부 중부)로 백업 데이터를 이동할 수 있습니다.

### <a name="prerequisite-for-moving-hybrid-workloads"></a>하이브리드 워크로드 이동을 위한 필수 조건

이동 작업이 시작되면 기존 자격 증명 모음에서 백업이 중지됩니다. 따라서 지역에서 백업 데이터 이동을 시작하기 전에 하이브리드 워크로드(DPM(Data Protection Manager) 서버/MABS(Azure Backup Server)/MARS(Microsoft Azure Recovery Services)에 대한 GWC의 새 자격 증명 모음에서 데이터를 보호하는 것이 중요합니다.
새 자격 증명 모음에서 보호를 시작하려면 다음을 수행합니다.

1. GWC에서 새 자격 증명 모음(VaultN)을 만듭니다.
1. DPM 서버/MABS/MARS 에이전트를 VaultN에 다시 등록합니다.
1. 정책을 할당하고 백업을 시작합니다.

초기 백업은 증분 백업이 뒤따르는 전체 복사본이 됩니다.

>[!Important]
>- 백업 데이터 이동 작업을 시작하기 전에 VaultN의 첫 번째 전체 백업이 완료되었는지 확인합니다.
>- DPM/MABS의 경우 대상 자격 증명 모음에서 데이터를 복원하는 데 필요하므로 안전한 위치에 있는 원래 자격 증명 모음의 암호를 유지 관리합니다. 원래 암호가 없으면 원본 자격 증명 모음에서 복원할 수 없습니다.

### <a name="step-1-download-the-resources"></a>1단계: 리소스 다운로드

필요한 리소스를 다운로드하여 설치합니다.

1. 최신 버전의 PowerShell(PowerShell 7)을 [다운로드](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3)합니다.
1. Azure Cloud Shell에서 사용할 수 있는 Az.RecoveryServices 모듈 버전 4.2.0을 사용합니다.
1. 모든 MARS 에이전트를 최신 버전으로 [업데이트](https://aka.ms/azurebackup_agent)합니다.
1. 암호의 유효성을 검사합니다. 재생성해야 하는 경우 [유효성 검사 단계](https://support.microsoft.com/topic/mandatory-update-for-azure-backup-for-microsoft-azure-recovery-services-agent-411e371c-aace-e134-de6b-bf9fda48026e#section-3)를 따릅니다.

### <a name="step-2-create-a-target-vault-in-gwc"></a>2단계: GWC에서 대상 자격 증명 모음 만들기

GWC에서 대상 자격 증명 모음(Vault 2)을 만듭니다. 자격 증명 모음을 만드는 방법을 알아보려면 [Recovery Services 자격 증명 모음 만들기 및 구성](../backup/backup-create-rs-vault.md)을 참조하세요.

>[!Note]
>- 자격 증명 모음에 보호된 항목이 없는지 확인합니다.
>- 대상 자격 증명 모음에 LRS(로컬 중복 스토리지) 또는 GRS(지역 중복 스토리지)와 같은 필수 중복이 있는지 확인합니다.

### <a name="step-3---use-powershell-to-trigger-backup-data-move"></a>3단계: - PowerShell을 사용하여 백업 데이터 이동 트리거

#### <a name="get-the-source-vault-from-gne-or-gec"></a>GNE 또는 GEC에서 원본 자격 증명 모음 가져오기

다음 cmdlet을 실행합니다.

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$srcVault = Get-AzRecoveryServicesVault -name “srcVault” -ResourceGroupName “TestSourceRG”`

>[!Note]
>- `srcVault` = 원본 자격 증명 모음
>- `TestSourceRG` = 원본 리소스 그룹

#### <a name="get-the-target-vault-in-gwc"></a>GWC에서 대상 자격 증명 모음 가져오기

다음 cmdlet을 실행합니다.

1. `Connect-AzAccount`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$trgVault = Get-AzRecoveryServicesVault -name “targetVault” -ResourceGroupName “TestTargetRG”`

>[!Note]
>- `targetVault` = 대상 자격 증명 모음
>- `TestTargetRG` = 리소스 그룹 테스트

#### <a name="perform-validation"></a>유효성 검사 수행
 
다음 cmdlet을 실행합니다.

1. `$validated = $false`
1. `$validated = Test-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault`

#### <a name="initializeprepare-ds-move"></a>DS 이동 초기화/준비

다음 cmdlet을 실행합니다.

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -SubscriptionName $srcSub`
1. ```azurepowershell
   if($validated) {
           $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault
                       }
   ```
1. `$corr`

#### <a name="trigger-ds-move"></a>트리거 DS 이동

다음 cmdlet을 실행합니다.

1. `Connect-AzAccount`
1. `Set-AzContext -SubscriptionName $trgSub`
1. `Copy-AzRecoveryServicesVault - CorrelationIdForDataMove $corr -SourceVault $srcVault -TargetVault $trgVault -Force`

`Get-AzRecoveryServicesBackupJob` cmdlet을 사용하여 작업을 모니터링할 수 있습니다.

>[!Note] 
>- 백업 데이터 이동 작업 중에는 모든 백업 항목이 임시 상태로 이동됩니다. 이 상태에서는 새 RP(복구 지점)가 만들어지지 않고 이전 RP가 정리되지 않습니다.
>- 이 기능은 GEC 및 GNE에서 사용하도록 설정되므로 작은 자격 증명 모음에서 이러한 단계를 수행하고 이동을 검증하는 것이 좋습니다. 성공하면 모든 자격 증명 모음에서 다음 단계를 수행합니다.
>- 백업 데이터 이동이 전체 자격 증명 모음에 대해 트리거됨과 함께 컨테이너(VM, DPM 및 MABS 서버, MARS 에이전트)별로 이동이 발생합니다. **작업** 섹션에서 컨테이너당 이동 진행 상황을 추적합니다. 

 ![이동 작업의 진행 상황을 모니터링합니다.](./media/germany-migration-management-tools/track-move-jobs.png)

이동 작업 중에는 원본 자격 증명 모음에서 다음 작업이 차단됩니다.

- 새 예약된 백업
- 데이터 삭제를 사용하여 백업 중지
- 데이터 삭제
- 백업 다시 시작
- 정책 수정

### <a name="step-4-check-the-status-of-the-move-job"></a>4단계: 이동 작업 상태 확인

백업 데이터 이동 작업은 컨테이너별로 발생합니다. Azure VM 백업의 경우 VM 백업은 컨테이너로 간주됩니다. 백업 데이터 이동 작업의 진행률을 나타내기 위해 모든 컨테이너에 대해 작업이 만들어집니다.

작업을 모니터링하려면 다음 cmdlet을 실행합니다.

1. `Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `$Jobs = Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `Get-AzRecoveryServicesBackupJobDetail -Job $Jobs[0] -VaultId $trgVault.ID`
1. `$JobDetails.ErrorDetails`

### <a name="step-5-post-move-operations"></a>5단계: 이동 작업 게시

모든 컨테이너에서 대상 자격 증명 모음으로의 백업 데이터 이동 작업이 완료되면 VM 백업에 대한 추가 작업이 필요하지 않습니다.



#### <a name="verify-the-movement-of-containers-is-complete"></a>컨테이너 이동이 완료되었는지 확인

원본 자격 증명 모음의 모든 컨테이너가 대상 자격 증명 모음으로 이동했는지 확인하려면 대상 자격 증명 모음으로 이동하여 해당 자격 증명 모음의 모든 컨테이너를 확인합니다.

다음 cmdlet을 실행하여 원본 자격 증명 모음에서 대상 자격 증명 모음으로 이동된 모든 VM을 나열합니다.

```azurepowershell
Get-AzRecoveryServicesBackupContainer -BackupManagementType “AzureVM” -VaultId $trgVault.ID
```

#### <a name="verify-the-movement-of-policies-is-complete"></a>정책 이동이 완료되었는지 확인

백업 데이터가 새 지역으로 성공적으로 이동되면 원본 자격 증명 모음의 Azure VM 백업 항목에 적용된 모든 정책이 대상 자격 증명 모음에 적용됩니다.

모든 정책이 원본 자격 증명 모음에서 대상 자격 증명 모음으로 이동했는지 확인하려면 대상 자격 증명 모음으로 이동하고 다음 cmdlet을 실행하여 이동된 모든 정책 목록을 가져옵니다.

```azurepowershell
Get-AzRecoveryServicesBackupProtectionPolicy -VaultId $trgVault.ID
```

이러한 정책은 이동 작업 후 백업 데이터에 계속 적용되므로 이동된 복구 지점의 수명 주기 관리가 계속됩니다.

여러 복구 지점(이동 프로세스 중에 만료되었거나 이동 프로세스 직후 만료될 수 있음)이 갑자기 정리되는 것을 방지하기 위해 이전 RP(복구 지점) 정리는 이동 후 10일 동안 일시 중지됩니다. 이 기간 동안에는 이전 RP로 인해 발생한 추가 데이터에 대한 요금이 청구되지 않습니다.

>[!Important]
>이러한 이전 RP에서 복구해야 하는 경우 이 10일 기간 내에 백업 데이터 이동을 즉시 복구합니다. 이 안전 기간이 완료되면 각 백업 항목에 적용된 정책이 적용되고 이전 RP의 정리가 적용됩니다.

#### <a name="restore-operations"></a>복원 작업

**Azure Virtual Machines 복원**

Azure Virtual Machines의 경우 대상 자격 증명 모음의 복구 지점에서 복원할 수 있습니다.

#### <a name="configure-mars-agent"></a>MARS 에이전트 구성

1. 대상 자격 증명 모음에 다시 등록합니다.
1. 복구 지점에서 복원합니다.
1. 사후 복구를 새 자격 증명 모음(VaultN)에 다시 등록하고 백업을 재개합니다.

>[!Note]
>MARS 에이전트가 대상 자격 증명 모음에 등록되어 있는 동안에는 새 백업이 수행되지 않습니다.

#### <a name="configure-dpmmabs"></a>DPM/MABS 구성

**권장됨**

외부 DPM 방법을 사용하여 복원을 수행합니다. 자세한 내용은 [Azure Backup Server에서 데이터 복구](../backup/backup-azure-alternate-dpm-server.md)를 참조하세요.

>[!Note]
>- OLR(원래 위치 복구)은 지원되지 않습니다.
>- 백업은 등록된 모든 컴퓨터에 대해 VaultN에서 계속됩니다.

**기타 옵션**

OLR(원래 위치 복구):

1. 대상 자격 증명 모음에 DPM 서버/MABS를 다시 등록합니다.
1. 복원 작업을 수행합니다.
1. DPM 서버/MABS를 새 자격 증명 모음에 다시 등록합니다.

>[!Note]
>DPM 사용의 제한 사항: <br><br> <ul><li>DPM 서버를 대상 자격 증명 모음에 연결하면 DPM 서버에 등록된 모든 컴퓨터에 대한 백업 작업이 중지됩니다.</li><li>복원 후 DPM 서버가 새 자격 증명 모음에 다시 등록되면 백업을 재개하기 전에 일관성 검사가 수행됩니다(완료하는 데 걸리는 시간은 데이터 양에 따라 다름).</li></ul>

### <a name="error-codes"></a>오류 코드

#### <a name="usererrorconflictingdatamoveonvault"></a>UserErrorConflictingDataMoveOnVault 

**메시지:** 자격 증명 모음에 현재 실행 중인 다른 데이터 이동 작업이 있습니다. 

**시나리오:** 원본 자격 증명 모음에서 데이터 이동 작업을 시도하고 있는 반면 다른 데이터 이동 작업은 이미 동일한 원본 자격 증명 모음에서 실행 중입니다.

**권장 작업:** 현재 데이터 이동 작업이 완료될 때까지 기다렸다가 다시 시도합니다.

#### <a name="usererroroperationnotallowedduringdatamove"></a>UserErrorOperationNotAllowedDuringDataMove

**메시지:** 데이터 이동 작업이 진행 중이므로 이 작업은 허용되지 않습니다. 

**시나리오:** 데이터 이동 작업이 진행 중인 동안 원본 자격 증명 모음에서 다음 작업이 허용되지 않습니다.
 
- 데이터 보존을 사용하여 백업 중지 
- 데이터 삭제를 사용하여 백업 중지 
- 백업 데이터 삭제 
- 백업 다시 시작 
- 정책 수정

**권장 작업:** 데이터 이동 작업이 완료될 때까지 기다렸다가 다시 시도합니다. 지원되는 작업에 대해 [자세히 알아봅니다](#azure-backup).

#### <a name="usererrornocontainersfoundfordatamove"></a>UserErrorNoContainersFoundForDataMove 

**메시지:** 이 자격 증명 모음에는 데이터 이동 작업에 대해 지원되는 컨테이너가 없습니다. 

**시나리오:** 이 메시지는 다음과 같은 경우에 표시됩니다.

- 원본 자격 증명 모음에는 컨테이너가 전혀 없습니다. 
- 원본 자격 증명 모음에는 지원되지 않는 컨테이너만 있습니다. 
- 원본 자격 증명 모음에는 이전에 일부 대상 자격 증명 모음으로 이동했으며 API에서 IgnoreMoved = true를 전달한 모든 컨테이너가 있습니다.

**권장 작업:** 데이터 이동이 지원되는 컨테이너에 대해 [알아봅니다](#azure-backup).

#### <a name="usererrordatamovenotsupportedatcontainerlevel"></a>UserErrorDataMoveNotSupportedAtContainerLevel 

**메시지:** 데이터 이동 작업은 컨테이너 수준에서 지원되지 않습니다. 

**시나리오:** 컨테이너 수준 데이터 이동 작업을 선택했습니다. 

**권장 작업:** 자격 증명 모음 수준 데이터 이동 작업을 시도합니다.

### <a name="usererrordatamovenotallowedcontainer-registrationinprogress"></a>UserErrorDataMoveNotAllowedContainer RegistrationInProgress 

**메시지:** 컨테이너 등록 작업이 원본 자격 증명 모음에서 실행되고 있으므로 데이터 이동 작업이 허용되지 않습니다. 

**시나리오:** 데이터 이동을 시도했을 때 원본 자격 증명 모음에서 컨테이너 등록 작업이 진행 중입니다. 

**권장 작업:** 잠시 후 데이터 이동 작업을 시도합니다.

#### <a name="usererrordatamovenotallowedtargetvaultnotempty"></a>UserErrorDataMoveNotAllowedTargetVaultNotEmpty 

**메시지:** 대상 자격 증명 모음에 일부 컨테이너가 이미 등록되어 있으므로 데이터 이동 작업이 허용되지 않습니다. 

**시나리오:** 선택한 대상 자격 증명 모음에 이미 등록된 일부 컨테이너가 있습니다. 

**권장 작업:** 빈 대상 자격 증명 모음에서 데이터 이동 작업을 시도합니다.

#### <a name="usererrorunsupportedsourceregionfordatamove"></a>UserErrorUnsupportedSourceRegionForDataMove 

**메시지:** 이 지역에서는 데이터 이동 작업이 지원되지 않습니다. 

**시나리오:** 원본 지역이 유효하지 않습니다.

**권장 작업:** 데이터 이동을 위해 [지원되는 지역 목록](#azure-backup)을 확인합니다.

#### <a name="usererrorunsupportedtargetregionfordatamove"></a>UserErrorUnsupportedTargetRegionForDataMove 

**메시지:** 이 지역으로 데이터 이동 작업은 지원되지 않습니다.

**시나리오:** 대상 지역 ID가 유효하지 않습니다. 

**권장 작업:** 데이터 이동을 위해 [지원되는 지역 목록](#azure-backup)을 확인합니다.


#### <a name="usererrordatamovetargetvaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveTargetVaultWithPrivate EndpointNotSupported 

**메시지:** 선택한 대상 자격 증명 모음에 프라이빗 엔드포인트가 있으므로 데이터를 이동할 수 없습니다. 

**시나리오:** 프라이빗 엔드포인트가 대상 자격 증명 모음에서 사용하도록 설정됩니다. 

**권장 작업:** 프라이빗 엔드포인트를 삭제하고 이동 작업을 다시 시도합니다. 지원되는 작업에 대해 [자세히 알아봅니다](#azure-backup).

### <a name="usererrordatamovesourcevaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveSourceVaultWithPrivate EndpointNotSupported 

**메시지:** 선택한 원본 자격 증명 모음에 프라이빗 엔드포인트가 있으므로 데이터를 이동할 수 없습니다.

**시나리오:** 원본 자격 증명 모음에서 프라이빗 엔드포인트가 사용하도록 설정됩니다.

**권장 작업:** 프라이빗 엔드포인트를 삭제하고 이동 작업을 다시 시도합니다. 지원되는 작업에 대해 [자세히 알아봅니다](../backup/private-endpoints.md#deleting-private-endpoints).

#### <a name="usererrordatamovesourcevaultwithcmk-notsupported"></a>UserErrorDataMoveSourceVaultWithCMK NotSupported 

**메시지:** 선택한 원본 자격 증명 모음이 암호화를 사용하도록 설정되어 있으므로 데이터를 이동할 수 없습니다. 

**시나리오:** 원본 자격 증명 모음에서 CMK(고객 관리형 키)가 사용하도록 설정됩니다.

**권장 작업:** 지원되는 작업에 대해 [알아봅니다](#azure-backup).

#### <a name="usererrordatamovetargetvaultwithcmknotsupported"></a>UserErrorDataMoveTargetVaultWithCMKNotSupported 

**메시지:** 선택한 대상 자격 증명 모음이 암호화를 사용하도록 설정되어 있으므로 데이터를 이동할 수 없습니다. 

**시나리오:** CMK(고객 관리형 키)가 대상 자격 증명 모음에서 사용하도록 설정됩니다.

**권장 작업:** 지원되는 작업에 대해 [알아봅니다](#azure-backup).

## <a name="scheduler"></a>Scheduler

Azure Scheduler가 사용 중지됩니다. 예약 작업을 만들려면 대신 글로벌 Azure에서 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용할 수 있습니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure Logic Apps 자습서](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)를 완료하여 자세히 알아봅니다.
- [Azure Logic Apps 개요](../logic-apps/logic-apps-overview.md)를 검토합니다.

## <a name="network-watcher"></a>Network Watcher

Azure 독일에서 글로벌 Azure로 Azure Network Watcher 인스턴스를 마이그레이션하는 것은 현재 지원되지 않습니다. 글로벌 Azure에서 새 Network Watcher 인스턴스를 만들고 구성하는 것이 좋습니다. 그런 다음 이전 환경과 새 환경 간의 결과를 비교합니다. 

추가 정보는 다음 항목을 참조하세요.

- [Network Watcher 자습서](../network-watcher/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Network Watcher 개요](../network-watcher/network-watcher-monitoring-overview.md)를 검토하세요.
- [네트워크 보안 그룹 흐름 로그](../network-watcher/network-watcher-nsg-flow-logging-portal.md)에 대해 알아봅니다.
- [연결 모니터](../network-watcher/connection-monitor.md)를 참조하세요.

## <a name="site-recovery"></a>Site Recovery

현재 Azure Site Recovery 설정을 글로벌 Azure로 마이그레이션할 수 없습니다. 글로벌 Azure에서 새 Site Recovery 솔루션을 설정해야 합니다.

Site Recovery에 대한 자세한 내용과 Azure 독일에서 글로벌 Azure로 VM을 마이그레이션하는 방법을 알아보려면 [Site Recovery 사용 방법](./germany-migration-compute.md#compute-iaas)을 참조하세요.

다음 단계별 자습서를 완료하여 지식을 새로 고칩니다.

- [Azure 간 재해 복구](../site-recovery/azure-to-azure-about-networking.md)
- [VMware와 Azure 간 재해 복구](../site-recovery/site-recovery-deployment-planner.md)
- [Hyper-V와 Azure 간 재해 복구](../site-recovery/hyper-v-deployment-planner-overview.md)

## <a name="azure-policies"></a>Azure 정책

Azure 독일에서 글로벌 Azure로 정책을 직접 마이그레이션할 수 없습니다. 마이그레이션 중에 할당된 정책의 범위는 일반적으로 변경됩니다. 이 시나리오에서와 같이 대상 환경에서 구독이 다른 경우 특히 그렇습니다. 그러나 정책 정의를 보존하고 글로벌 Azure에서 재사용할 수 있습니다.

Azure CLI에서 다음 명령을 실행하여 현재 환경의 모든 정책을 나열합니다.

> [!NOTE]
> 다음 명령을 실행하기 전에 Azure CLI에서 AzureGermanCloud 환경으로 전환해야 합니다.


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

**PolicyType** 값이 **Custom** 인 정책만 내보냅니다. **policyRule** 을 파일로 내보냅니다. 다음 예에서는 사용자 지정 정책 "독일 중부만 허용"(약식 버전: `allowgconly`)을 현재 폴더의 파일로 내보냅니다. 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

내보내기 파일은 다음 예와 비슷하게 표시됩니다.

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

다음으로 글로벌 Azure 환경으로 전환합니다. 파일을 편집하여 정책 규칙을 수정합니다. 예를 들어 `germanycentral`를 `westeurope`로 변경합니다.

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

새 정책을 만듭니다.

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

이제 `allowweonly`라는 새 정책이 있습니다. 이 정책은 서유럽만 지역으로 허용합니다.

새 환경의 범위에 적절하게 정책을 할당합니다. 다음 명령을 실행하여 Azure 독일의 이전 할당을 문서화할 수 있습니다.

```azurecli
az policy assignment list
```

추가 정보는 다음 항목을 참조하세요.

- [Azure 정책 자습서](../governance/policy/tutorials/create-and-manage.md)를 완료하여 지식을 새로 고칩니다.
- [Azure CLI를 사용하여 정책 보기](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli) 또는 [PowerShell을 사용하여 정책을 보는 방법](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell)을 알아봅니다.
- [Azure CLI를 사용하여 정책 정의 만들기](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli) 또는 [PowerShell을 사용하여 정책 정의 만들기](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell) 방법을 알아봅니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="where-can-i-move-the-backup-data"></a>백업 데이터는 어디로 이동할 수 있나요?

독일 중부(GEC) 및 독일 북동부(GNE)의 RSV(복구 서비스 자격 증명 모음)에서 GWC(독일 중서부)로 백업 데이터를 이동할 수 있습니다.

### <a name="what-backup-data-can-i-move"></a>어떤 백업 데이터를 이동할 수 있나요?

2021년 6월 21일부터 다음 워크로드에 대한 백업 데이터를 한 지역에서 다른 지역으로 이동할 수 있습니다.

- Azure Virtual Machines
- 하이브리드 워크로드
- MARS(Microsoft Azure Recovery Services) 에이전트를 사용한 파일/폴더 백업
- DPM(Data Protection Manager) 서버
- Azure Backup 서버(MABS)

### <a name="how-can-i-move-backup-data-to-another-region"></a>백업 데이터를 다른 지역에 이동할 수 있나요?

기존 지역의 데이터가 손실되지 않도록 Azure Backup은 백업 데이터를 GEC 및 GNE에서 GWC로 이동할 수 있도록 설정했습니다.

마이그레이션이 진행되는 동안 백업은 GEC 및 GNE에서 중지됩니다. 따라서 마이그레이션 작업을 시작하기 전에 새 지역의 워크로드를 보호하는 것이 필수적입니다.

### <a name="what-to-do-if-the-backup-data-move-operation-fails"></a>백업 데이터 이동 작업이 실패하면 어떻게 해야 하나요?

백업 데이터는 다음 오류 시나리오로 인해 발생할 수 있습니다.

| 오류 메시지    | 원인 |
| --- | --- |
| 빈 대상 자격 증명 모음을 제공하세요. 대상 자격 증명 모음에는 백업 항목이나 백업 컨테이너가 없어야 합니다. | 이미 일부 보호된 항목이 있는 대상 자격 증명 모음을 선택했습니다. |
| Azure Backup 데이터는 지원되는 대상 지역으로만 이동할 수 있습니다. | 이동이 지원되는 지역 중 하나가 아닌 지역에서 대상 자격 증명 모음을 선택했습니다. |

비어 있는 새 대상 자격 증명 모음으로 동일한 명령(아래 참조)을 실행하여 백업을 처음부터 다시 시도해야 하거나 플래그로 표시하여 원본 자격 증명 모음에서 실패한 항목을 다시 시도하고 이동할 수 있습니다.

```azurepowershell
   if($validated) {
                              $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault -RetryOnlyFailed
                      }
```

### <a name="is-there-a-cost-involved-in-moving-this-backup-data"></a>이 백업 데이터를 이동하는 데 비용이 발생하나요?

아니요. 한 지역에서 다른 지역으로 백업 데이터를 이동하는 데 드는 추가 비용은 없습니다. Azure Backup은 지역 간 데이터 이동 비용을 부담합니다. 이동 작업이 완료된 후 10일 동안만 비용이 청구되지 않습니다. 이 기간이 지나면 대상 자격 증명 모음에서 청구가 시작됩니다.

### <a name="if-i-face-issues-in-moving-backup-data-whom-should-i-contact"></a>백업 데이터 이동에 문제가 있는 경우 누구에게 문의해야 하나요?

백업 데이터가 GEC 또는 GNE에서 GWC로 이동하는 데 문제가 있는 경우 [GESupportAzBackup@microsoft.com](mailto:GESupportAzBackup@microsoft.com)으로 문의해 주세요.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [미디어](./germany-migration-media.md)
