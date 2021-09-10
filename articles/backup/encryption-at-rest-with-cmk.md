---
title: 고객 관리형 키를 사용하여 백업 데이터 암호화
description: Azure Backup을 사용하여 CMK(고객 관리형 키)를 통해 백업 데이터를 암호화하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f16974d00f4801f288180814daf9ff5ed4558748
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778722"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>고객 관리형 키를 사용하여 백업 데이터 암호화

Azure Backup을 사용하면 기본적으로 사용하도록 설정된 플랫폼 관리형 키 대신 CMK(고객 관리형 키)를 사용하여 백업 데이터를 암호화할 수 있습니다. 백업 데이터를 암호화하는 데 사용되는 키는 [Azure Key Vault](../key-vault/index.yml)에 저장되어야 합니다.

백업 암호화에 사용되는 암호화 키는 원본에 사용된 것과 다를 수 있습니다. 데이터는 AES 256 기반 DEK(데이터 암호화 키)를 사용하여 보호되며 이는 다시 KEK(사용자의 키)를 사용하여 보호됩니다. 이를 통해 데이터와 키에 대한 모든 권한을 제공합니다. 암호화를 허용하려면 Recovery Services 자격 증명 모음에 Azure Key Vault 암호화 키에 대한 액세스 권한을 부여해야 합니다. 필요한 경우 키를 변경할 수 있습니다.

이 문서에서는 다음 항목을 설명합니다.

- Recovery Services 자격 증명 모음 만들기
- 고객 관리형 키를 사용하여 백업 데이터를 암호화하도록 Recovery Services 자격 증명 모음 구성
- 고객 관리형 키를 사용하여 암호화된 자격 증명 모음에 백업 수행
- 백업에서 데이터 복원

## <a name="before-you-start"></a>시작하기 전에

- 해당 기능을 사용하면 **새 Recovery Services 자격 증명 모음만** 암호화할 수 있습니다. 등록했거나 등록하려고 시도하는 기존 항목을 포함하는 모든 자격 증명 모음은 지원되지 않습니다.

- Recovery Services 자격 증명 모음에 대해 사용하도록 설정되면 고객 관리형 키를 사용하는 암호화는 플랫폼 관리형 키(기본값)를 사용하여 되돌릴 수 없습니다. 암호화 키는 요구 사항에 따라 변경할 수 있습니다.

- 해당 기능은 현재 **MARS 에이전트를 사용하여 백업을 지원하지 않으며** 동일한 경우 CMK 암호화 자격 증명 모음을 사용하지 못할 수 있습니다. MARS 에이전트는 사용자 암호 기반 암호화를 사용합니다. 또한 해당 기능은 클래식 VM의 백업을 지원하지 않습니다.

- 해당 기능은 BitLocker(Windows의 경우) 및 DM-Crypt(Linux의 경우)를 사용하여 VM 디스크의 게스트 기반 암호화를 사용하는 [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)과 관련이 없습니다.

- Recovery Services 자격 증명 모음은 **동일한 지역** 에 있는 Azure Key Vault에 저장된 키로만 암호화할 수 있습니다. 또한 키는 **RSA 키** 여야만 하며 **사용** 상태여야 합니다.

- 현재 리소스 그룹 및 구독에서 CMK 암호화된 Recovery Services 자격 증명 모음의 이동은 지원되지 않습니다.
- 고객 관리형 키로 암호화된 Recovery Services 자격 증명 모음은 백업된 인스턴스의 지역 간 복원을 지원하지 않습니다.
- 고객 관리 키를 사용하여 이미 암호화된 Recovery Services 자격 증명 모음을 새 테넌트로 이동하는 경우, 자격 증명 모음의 관리 ID 및 CMK(새 테넌트에 있어야 함)를 다시 만들고 구성하기 위해 Recovery Services 자격 증명 모음을 업데이트해야 합니다. 이 작업을 수행하지 않으면 백업 및 복원 작업이 실패하기 시작합니다. 또한 구독 내에서 설정된 모든 RBAC(역할 기반 액세스 제어) 권한을 다시 구성해야 합니다.

- 해당 기능은 Azure Portal 및 PowerShell을 통해 구성할 수 있습니다.

    >[!NOTE]
    >Az 모듈 5.3.0 이상을 사용하여 Recovery Services 자격 증명 모음의 백업에 고객 관리 키를 사용합니다.
    
    >[!Warning]
    >백업에 대한 암호화 키를 관리하기 위해 PowerShell을 사용하는 경우 포털에서 키를 업데이트하지 않는 것이 좋습니다.<br>포털에서 키를 업데이트하면 새 모델을 지원하기 위한 PowerShell 업데이트를 사용할 수 있을 때까지 PowerShell을 사용하여 암호화 키를 추가로 업데이트할 수 없습니다. 그러나 Azure Portal에서 키를 계속 업데이트할 수 있습니다.

Recovery Services 자격 증명 모음을 만들고 구성하지 않은 경우 [여기에서 작업을 수행하는 방법](backup-create-rs-vault.md)을 확인할 수 있습니다.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>고객 관리형 키를 사용하여 암호화하도록 자격 증명 모음 구성

이 섹션에는 다음과 같은 단계가 포함됩니다.

1. Recovery Services 자격 증명 모음에 관리 ID 사용

1. 자격 증명 모음에 사용 권한을 할당하여 Azure Key Vault의 암호화 키에 액세스

1. Azure Key Vault에서 일시 삭제 및 제거 방지 사용

1. Recovery Services 자격 증명 모음에 암호화 키 할당

원하는 결과를 달성하려면 이러한 모든 단계를 앞에서 설명한 순서대로 수행해야 합니다. 각 단계는 아래에 자세히 설명되어 있습니다.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 관리 ID 사용

Azure Backup은 시스템 할당 관리 ID와 사용자가 할당한 관리 ID를 사용하여 Azure Key Vault에 저장된 암호화 키에 액세스하기 위해 Recovery Services 자격 증명 모음을 인증합니다. Recovery Services 자격 증명 모음에 관리 ID를 사용하도록 설정하려면 아래에 설명된 단계를 수행하세요.

>[!NOTE]
>일단 사용하도록 설정하면 관리 ID를 일시적이라도 사용하지 **않도록** 설정하면 안 됩니다. 관리 ID를 사용하지 않도록 설정하면 일관성이 없는 동작이 발생할 수 있습니다.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>해당 자격 증명 모음에 대해 시스템 할당 관리 ID 사용

**포털의 경우:**

1. Recovery Services 자격 증명 모음 -> **ID** 로 이동합니다.

    ![Id 설정](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. **시스템 할당** 탭으로 이동합니다.

1. **상태** 를 **켜기** 로 변경합니다.

1. **저장** 을 클릭하여 자격 증명 모음에 대한 ID를 사용하도록 설정합니다.

자격 증명 모음의 시스템 할당 관리 ID인 개체 ID가 생성됩니다.

>[!NOTE]
>일단 사용하도록 설정하면 관리 ID를 일시적이라도 사용하지 않도록 설정하면 안 됩니다. 관리 ID를 사용하지 않도록 설정하면 일관성이 없는 동작이 발생할 수 있습니다.


**PowerShell 사용:**

[Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) 명령을 사용하여 복구 서비스 자격 증명 모음의 시스템이 할당한 관리 ID를 사용으로 설정합니다.

예제:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

출력:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault-in-preview"></a>사용자가 할당한 관리 ID를 자격 증명 모음에 할당(미리 보기)

>[!Note]
>- CMK 암호화를 위해 사용자 할당 관리 ID를 사용하는 자격 증명 모음은 백업에 대해 프라이빗 엔드포인트 사용을 지원하지 않습니다.
>- 특정 네트워크에 대한 액세스를 제한하는 Azure Key Vault는 아직 CMK 암호화를 위한 사용자 할당 관리 ID와 함께 사용할 수 없습니다.

Recovery Services 자격 증명 모음에 사용자가 할당한 관리 ID를 할당하려면 다음 단계를 수행합니다.

1.  Recovery Services 자격 증명 모음 -> **ID** 로 이동합니다.

    ![사용자가 할당한 관리 ID를 자격 증명 모음에 할당](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  **사용자 할당** 탭으로 이동합니다.

1.  **+ 추가** 를 클릭하여 사용자가 할당한 관리 ID를 추가합니다.

1.  열리는 **사용자가 할당한 관리 ID 추가** 블레이드에서 ID에 대한 구독을 선택합니다.

1.  목록에서 ID를 선택합니다. ID 또는 리소스 그룹의 이름을 기준으로 필터링할 수도 있습니다.

1.  완료되면 **추가** 를 클릭하여 ID 할당을 완료합니다.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Recovery Services 자격 증명 모음에 사용 권한을 할당하여 Azure Key Vault의 암호화 키에 액세스

>[!Note]
>사용자가 할당한 ID를 사용하는 경우에는 사용자가 할당한 ID에 동일한 사용 권한을 할당해야 합니다.

이제 Recovery Services 자격 증명 모음에서 암호화 키를 포함하는 Azure Key Vault에 액세스하도록 허용해야 합니다. 이 작업은 Recovery Services 자격 증명 모음의 관리 ID가 Key Vault에 액세스하도록 허용하여 수행됩니다.

**포털의 경우:**

1. **액세스 정책** 으로 이동합니다. **+ 액세스 정책 추가** 로 계속 진행합니다.

    ![액세스 정책 추가](./media/encryption-at-rest-with-cmk/access-policies.png)

1. **키 권한** 에서 **가져오기**, **목록**, **키 래핑 해제**, **키 래핑** 작업을 선택합니다. 허용될 키에 대한 동작을 지정합니다.

    ![키 권한 할당](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. **보안 주체 선택** 으로 이동하여 해당 이름 또는 관리 ID를 사용하여 검색 상자에서 자격 증명 모음을 검색합니다. 자격 증명 모음이 표시되면 선택하고 창의 맨 아래에서 **선택** 을 선택합니다.

    ![보안 주체 선택](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 완료되면 **추가** 를 선택하여 새 액세스 정책을 추가합니다.

1. **저장** 을 선택하여 Azure Key Vault의 액세스 정책에 대한 변경 내용을 저장합니다.

>[!NOTE] 
>_[Key Vault Crypto Officer](../key-vault/general/rbac-guide.md#azure-built-in-roles-for-key-vault-data-plane-operations)_ 역할과 같이 위에서 언급한 권한이 포함된 Recovery Services 자격 증명 모음에 RBAC 역할을 할당할 수도 있습니다.<br><br>이러한 역할에는 위에서 설명한 것 이외의 추가 권한이 포함될 수 있습니다.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Azure Key Vault에서 일시 삭제 및 제거 방지 사용

암호화 키를 저장하는 Azure Key Vault에 대해 **일시 삭제 및 제거 방지를 사용하도록 설정** 해야 합니다. 아래와 같이 Azure Key Vault UI에서 해당 작업을 수행할 수 있습니다. (또는 Key Vault를 만드는 동안 이러한 속성을 설정할 수 있습니다.) 이러한 Key Vault 속성에 대한 자세한 내용은 [여기](../key-vault/general/soft-delete-overview.md)를 참조하세요.

![일시 삭제 및 제거 보호 사용](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

다음 단계를 사용하여 PowerShell을 통해 일시 삭제 및 제거 방지를 사용하도록 설정할 수도 있습니다.

1. Azure 계정에 로그인합니다.

    ```azurepowershell
    Login-AzAccount
    ```

1. 자격 증명 모음을 포함하는 구독을 선택합니다.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. 일시 삭제 사용

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. 제거 방지 사용

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>RS 자격 증명 모음에 암호화 키 할당

>[!NOTE]
> 계속하기 전에 다음 사항을 확인합니다.
>
> - 위에서 언급한 모든 단계가 성공적으로 완료되었습니다.
>   - Recovery Services 자격 증명 모음의 관리 ID를 사용하도록 설정했으며 필요한 권한이 할당됨
>   - Azure Key Vault에 일시 삭제 및 제거 방지가 설정되어 있음
> - CMK 암호화를 사용하도록 설정할 Recovery Services 자격 증명 모음에 보호 또는 등록된 항목이 **없음**

위의 내용이 확인되면 자격 증명 모음에 대한 암호화 키 선택을 계속합니다.

### <a name="to-assign-the-key-in-the-portal"></a>포털에서 키를 할당하려면 다음을 수행합니다.

1. Recovery Services 자격 증명 모음 -> **속성** 으로 이동

    ![암호화 설정](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. **암호화 설정** 에서 **업데이트** 를 선택합니다.

1. 암호화 설정 창에서 **사용자 고유의 키를 사용** 을 선택하고 다음 방법 중 하나를 사용하여 키를 지정합니다. **사용하려는 키가 사용 상태에 있는 RSA 2048 키인지 확인합니다.**

    1. 해당 Recovery Services 자격 증명 모음에서 데이터를 암호화하는 데 사용할 **키 URI** 를 입력합니다. 또한 해당 키를 포함하는 Azure Key Vault가 있는 구독을 지정해야 합니다. 키 URI는 Azure Key Vault의 해당 키에서 가져올 수 있습니다. 키 URI가 올바르게 복사되었는지 확인합니다. 키 식별자와 함께 제공되는 **클립보드로 복사** 단추를 사용하는 것이 좋습니다.

        >[!NOTE]
        >키 URI를 사용하여 암호화 키를 지정하는 경우 키가 자동으로 회전되지 않습니다. 따라서 필요한 경우 새 키를 지정하여 수동으로 키 업데이트를 완료해야 합니다.

        ![키 URI 입력](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. 키 선택기 창의 Key Vault에서 키를 찾아 선택합니다.

        >[!NOTE]
        >키 선택기 창을 사용하여 암호화 키를 지정하는 경우 키의 새 버전을 사용할 때마다 키가 자동으로 회전합니다. 암호화 키 자동 회전을 사용하는 방법에 대해 [자세히 알아보세요](#enabling-auto-rotation-of-encryption-keys).

        ![키 자격 증명 모음에서 키 선택](./media/encryption-at-rest-with-cmk/key-vault.png)

1. **저장** 을 선택합니다.

1. **암호화 키 업데이트의 진행률 및 상태 추적**: 왼쪽 탐색 모음의 **백업 작업** 보기를 사용하여 암호화 키 할당의 진행률과 상태를 추적할 수 있습니다. 상태가 곧 **완료** 로 변경됩니다. 이제 자격 증명 모음에서 지정된 키를 사용하여 모든 데이터를 KEK로 암호화합니다.

    ![상태 완료](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    암호화 키 업데이트도 자격 증명 모음의 활동 로그에 기록됩니다.

    ![활동 로그](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>PowerShell을 사용하여 키를 할당하려면

[Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) 명령을 사용하여 고객 관리 키를 사용하여 암호화를 사용하도록 설정하고 사용할 암호화 키를 할당하거나 업데이트합니다.

예제:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

출력:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> 해당 프로세스는 암호화 키를 업데이트하거나 변경하려는 경우에도 동일합니다. 현재 사용 중인 것과 다른 Key Vault에서 키를 업데이트하고 사용하려는 경우 다음을 확인합니다.
>
> - 키 자격 증명 모음이 Recovery Services 자격 증명 모음과 동일한 지역에 있음
>
> - 키 자격 증명 모음에 일시 삭제 및 제거 방지가 설정되어 있음
>
> - Recovery Services 자격 증명 모음에는 키 자격 증명 모음에 액세스하는 데 필요한 권한이 있습니다.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>고객 관리 키를 사용하여 암호화된 자격 증명 모음에 백업

보호를 계속 구성하기 전에 다음 검사 목록을 준수하는지 확인하는 것이 좋습니다. 비 CMK 암호화된 자격 증명 모음에 대해 항목을 백업(또는 구성을 시도)하도록 구성하면 고객 관리 키를 사용하는 암호화를 사용하도록 설정할 수 없으며 플랫폼 관리 키를 계속 사용하므로 해당 작업이 중요합니다.

>[!IMPORTANT]
> 보호를 구성하기 전에 다음 단계를 **성공적으로** 완료해야 합니다.
>
>1. Recovery Services 자격 증명 모음을 만들었음
>1. Recovery Services 자격 증명 모음의 시스템 할당 관리 ID를 사용하도록 설정했거나 사용자가 할당한 관리 ID를 자격 증명 모음에 할당
>1. Key Vault에서 암호화 키에 액세스할 수 있도록 Recovery Services 자격 증명 모음(또는 사용자가 할당한 관리 ID)에 권한 할당
>1. Key Vault에 대해 일시 삭제 및 제거 방지가 설정됨
>1. Recovery Services 자격 증명 모음에 대한 유효한 암호화 키 할당
>
>위의 모든 단계를 확인한 후에만 백업 구성을 진행합니다.

고객 관리형 키를 사용하여 암호화된 Recovery Services 자격 증명 모음에 대한 백업을 구성하고 수행하는 프로세스는 **환경을 변경하지 않으며** 플랫폼 관리형 키를 사용하는 자격 증명 모음과 동일합니다. 이는 VM 내에서 실행되는 워크로드(예: [SAP HANA](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) 데이터베이스)의 백업뿐만 아니라 [Azure VM 백업](./quick-backup-vm-portal.md)에도 적용됩니다.

## <a name="restoring-data-from-backup"></a>백업에서 데이터 복원

### <a name="vm-backup"></a>VM 백업

Recovery Services 자격 증명 모음에 저장된 데이터는 [여기](./backup-azure-arm-restore-vms.md)에 설명된 단계에 따라 복원할 수 있습니다. 고객 관리형 키를 사용하여 암호화된 Recovery Services 자격 증명 모음에서 복원하는 경우 DES(디스크 암호화 집합)를 사용하여 복원된 데이터를 암호화하도록 선택할 수 있습니다.

#### <a name="restoring-vm--disk"></a>VM/디스크 복원

1. “스냅샷” 복구 지점에서 디스크/VM을 복구하는 경우 복원된 데이터는 원본 VM의 디스크를 암호화하는 데 사용되는 DES로 암호화됩니다.

1. 복구 유형을 “자격 증명 모음”으로 복구 지점에서 복원하는 경우 복원 시 지정된 DES를 사용하여 복원된 데이터를 암호화하도록 선택할 수 있습니다. 또는 DES를 지정하지 않고 데이터 복원을 계속하도록 선택할 수 있습니다. 이 경우 Microsoft 관리형 키를 사용하여 암호화됩니다.

복원을 시작하는 동안 선택된 항목에 관계없이 복원이 완료된 후 복원된 디스크/VM을 암호화할 수 있습니다.

![복원 지점](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>자격 증명 모음 복구 지점에서 복원하는 동안 디스크 암호화 설정 선택

**포털의 경우:**

디스크 암호화 집합은 아래와 같이 복원 창의 암호화 설정에서 지정됩니다.

1. **키를 사용한 디스크 암호화** 에서 **예** 를 선택합니다.

1. 드롭다운에서 복원된 디스크에 사용할 DES를 선택합니다. **DES에 대한 액세스 권한이 있는지 확인합니다.**

>[!NOTE]
>Azure Disk Encryption을 사용하는 VM을 복원하는 경우 복원하는 동안 DES를 선택하는 기능은 사용할 수 없습니다.

![키를 사용하여 디스크 암호화](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**PowerShell 사용**:

매개 변수[`-DiskEncryptionSetId <string>`]와 함께 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) 명령을 사용하여 복원된 디스크를 암호화하는 데 사용할 [DES를 지정](/powershell/module/az.compute/get-azdiskencryptionset)합니다. VM 백업에서 디스크를 복원하는 방법에 대한 자세한 내용은 [이 문서](./backup-azure-vms-automation.md#restore-an-azure-vm)를 참조하세요.

예제:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>파일 복원

파일을 복원할 때 복원 데이터는 대상 위치를 암호화하는 데 사용되는 키를 사용하여 암호화됩니다.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Azure VM에서 SAP HANA/SQL 데이터베이스 복원

Azure VM에서 실행되는 백업된 SAP HANA/SQL 데이터베이스에서 복원하는 경우 복원 데이터는 대상 스토리지 위치에서 사용되는 암호화 키를 사용하여 암호화됩니다. VM 디스크를 암호화하는 데 사용되는 고객 관리형 키 또는 플랫폼 관리형 키일 수 있습니다.

## <a name="additional-topics"></a>추가 항목

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>자격 증명 모음을 만들 때 고객 관리형 키를 사용하여 암호화 사용(미리 보기)

>[!NOTE]
>고객 관리형 키를 사용하여 자격 증명 모음을 만들 때 암호화를 사용하도록 설정하는 것은 제한된 퍼블릭 미리 보기 상태이며 구독 허용 목록이 필요합니다. 미리 보기에 가입하려면 [양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u)을 작성하고 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)으로 문의해 주세요.

구독이 허용 목록에 있으면 **백업 암호화** 탭이 표시됩니다. 이렇게 하면 새 Recovery Services 자격 증명 모음을 만드는 동안 고객 관리형 키를 사용하여 백업에서 암호화를 사용하도록 설정할 수 있습니다. 암호화를 사용하려면 다음 단계를 수행합니다.

1. **기본** 탭 옆의 **백업 암호화** 탭에서 암호화 키 및 암호화에 사용할 ID를 지정합니다.

   ![자격 증명 모음 수준에서 암호화 사용](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >설정은 백업에만 적용되며 선택 사항입니다.

1. 암호화 유형으로 **고객 관리형 키 사용** 을 선택합니다.

1. 암호화에 사용할 키를 지정하려면 적절한 옵션을 선택합니다.

   암호화 키에 대한 URI를 제공하거나 키를 찾아 선택할 수 있습니다. **Key Vault 선택** 옵션을 사용하여 키를 지정하면 암호화 키의 자동 회전이 자동으로 사용하도록 설정됩니다. [자동 회전에 대해 자세히 알아보세요](#enabling-auto-rotation-of-encryption-keys). 

1. 고객 관리형 키를 사용하여 암호화를 관리하려면 사용자가 할당한 관리 ID를 지정합니다. **선택** 을 클릭하여 필요한 ID를 찾아 선택합니다.

1. 완료되면 태그를 추가하고(선택 사항) 자격 증명 모음을 계속 만듭니다.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>암호화 키의 자동 회전 사용

백업을 암호화하는 데 사용해야 하는 고객 관리형 키를 지정하는 경우 다음 방법을 사용하여 지정합니다.

- 키 URI 입력
- Key Vault에서 선택

**Key Vault에서 선택** 옵션을 사용하면 선택한 키에 대해 자동 회전을 사용하도록 설정할 수 있습니다. 이렇게 하면 수동으로 다음 버전으로 업데이트하지 않아도 됩니다. 그러나 해당 옵션을 사용하는 경우:
- 키 버전 업데이트를 적용하는 데 최대 한 시간이 걸릴 수 있습니다.
- 새 버전의 키가 적용되면 키 업데이트 적용 후 하나 이상의 후속 백업 작업에 대한 이전 버전(활성화 상태)도 사용할 수 있어야 합니다.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>고객 관리형 키를 활용하여 암호화를 감사 및 적용하기 위해 Azure 정책 사용(미리 보기)

Azure Backup을 사용하면 Azure 정책을 통해 Recovery Services 자격 증명 모음에 있는 고객 관리형 키를 사용하여 암호화를 감사하고 적용할 수 있습니다. Azure 정책 사용:

- 감사 정책은 2021년 4월 1일 이후에 사용하도록 설정된 고객 관리형 키를 사용하여 암호화된 자격 증명 모음을 감사하는 데 사용할 수 있습니다. 이 날짜 이전에 CMK 암호화를 사용하도록 설정된 자격 증명 모음의 경우 정책이 적용되지 않거나 가음성 결과가 표시될 수 있습니다(즉, **CMK 암호화** 를 사용하도록 설정된 경우에도 이러한 자격 증명 모음이 비규격으로 보고될 수 있음).
- 2021년 4월 1일 이전에 **CMK 암호화** 가 활성화된 자격 증명 모음 감사에 대한 감사 정책을 사용하려면 Azure Portal을 사용하여 암호화 키를 업데이트합니다. 그러면 새 모델로 업그레이드하는 데 도움이 됩니다. 암호화 키를 변경하지 않으려면 키 URI 또는 키 선택 옵션을 통해 동일한 키를 다시 제공합니다. 

   >[!Warning]
    >백업에 대한 암호화 키를 관리하기 위해 PowerShell을 사용하는 경우 포털에서 키를 업데이트하지 않는 것이 좋습니다.<br>포털에서 키를 업데이트하면 새 모델을 지원하기 위한 PowerShell 업데이트를 사용할 수 있을 때까지 PowerShell을 사용하여 암호화 키를 추가로 업데이트할 수 없습니다. 그러나 Azure Portal에서 키를 계속 업데이트할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>고객 관리형 키를 사용하여 기존 백업 자격 증명 모음을 암호화할 수 있나요?

아니요, 새 자격 증명 모음에만 CMK 암호화를 사용하도록 설정할 수 있습니다. 따라서 자격 증명 모음에 보호된 항목이 없었어야 합니다. 실제로 고객 관리형 키를 사용하여 암호화를 사용하도록 설정하기 전에 자격 증명 모음에 대한 항목을 보호하려고 시도한 적이 없어야 합니다.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>자격 증명 모음에 대한 항목을 보호하려고 했지만 실패했고 자격 증명 모음에 보호된 항목이 아직 포함되어 있지 않습니다. 이러한 자격 증명 모음에 CMK 암호화를 사용하도록 설정할 수 있나요?

아니요, 자격 증명 모음은 과거에 항목을 보호하려고 시도한 적이 없어야 합니다.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>CMK 암호화를 사용하는 자격 증명 모음이 있습니다. 나중에 자격 증명 모음으로 보호되는 백업 항목이 있는 경우에도 플랫폼 관리형 키를 사용하여 암호화로 되돌릴 수 있나요?

아니요, CMK 암호화를 사용하도록 설정하면 플랫폼 관리형 키를 사용하도록 되돌릴 수 없습니다. 계획은 요구 사항에 따라 변경할 수 있습니다.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure Backup에 대한 CMK 암호화가 Azure Site Recovery에도 적용되나요?

아니요, 이 문서에서는 백업 데이터에 대한 암호화만 설명합니다. Azure Site Recovery의 경우 서비스에서 사용 가능한 속성을 별도로 설정해야 합니다.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>이 문서의 단계 중 하나를 누락했고 데이터 원본을 보호하는 데 문제가 발생했습니다. CMK 암호화를 계속 사용할 수 있나요?

이 문서의 단계를 따르지 않고 항목 보호를 계속하면 자격 증명 모음에서 고객 관리형 키를 사용하는 암호화를 사용할 수 없게 될 수 있습니다. 따라서 항목 보호를 계속하기 전에 [검사 목록](#backing-up-to-a-vault-encrypted-with-customer-managed-keys)을 참조하는 것이 좋습니다.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>CMK 암호화를 사용하면 백업 비용이 추가되나요?

백업에 CMK 암호화를 사용하는 경우 추가 비용이 발생하지 않습니다. 그러나 키가 저장된 Azure Key Vault 사용에 대한 비용이 계속 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup의 보안 기능 개요](security-overview.md)
