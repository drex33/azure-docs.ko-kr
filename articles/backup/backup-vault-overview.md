---
title: Backup 자격 증명 모음 개요
description: Backup 자격 증명 모음 개요입니다.
ms.topic: conceptual
ms.date: 09/08/2021
ms.custom: references_regions
ms.openlocfilehash: afe103e7fb6c2cb59634029c5ae9a75acadf0afc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235889"
---
# <a name="backup-vaults-overview"></a>Backup 자격 증명 모음 개요

이 문서에서는 Backup 자격 증명 모음의 기능을 설명합니다. Backup 자격 증명 모음은 Azure에서 Azure Backup이 지원하는 특정 최신 워크로드에 대한 백업 데이터를 저장하는 스토리지 엔터티입니다. Backup 자격 증명 모음을 사용하여 Azure Database for PostgreSQL 서버, Azure Backup에서 지원할 최신 워크로드 등 다양한 Azure 서비스에 대한 백업 데이터를 저장할 수 있습니다. Backup 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다. Backup 자격 증명 모음은 다음과 같은 기능을 제공하는 Azure의 Azure Resource Manager 모델을 기반으로 합니다.

- **백업 데이터 보호 기능 향상**: Backup 자격 증명 모음에서 Azure Backup은 클라우드 백업을 보호하는 보안 기능을 제공합니다. 이러한 보안 기능을 통해 백업을 보호하고 프로덕션 및 백업 서버가 손상된 경우에도 데이터를 안전하게 복구할 수 있습니다. [자세한 정보](backup-azure-security-feature.md)

- **Azure RBAC(Azure 역할 기반 액세스 제어)** : Azure RBAC는 Azure에서 세밀한 액세스 관리 제어를 제공합니다. [Azure는 다양한 기본 제공 역할을 제공](../role-based-access-control/built-in-roles.md)하고 Azure Backup에는 3가지 [복구 지점을 관리하는 기본 제공 역할](backup-rbac-rs-vault.md)이 포함됩니다. Backup 자격 증명 모음은 Azure RBAC와 호환되어 백업을 제한하고 정의된 집합의 사용자 역할에 대한 액세스를 복원합니다. [자세히 알아보기](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Backup 자격 증명 모음의 스토리지 설정

Backup 자격 증명 모음은 시간에 따라 만들어진 백업 및 복구 지점을 저장하는 엔터티입니다. Backup 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

- Azure Backup은 자격 증명 모음에 대한 스토리지를 자동으로 처리합니다. Backup 자격 증명 모음을 만들 때 비즈니스 요구 사항과 일치하는 스토리지 중복을 선택합니다.

- 스토리지 중복에 대한 자세한 내용은 [지역](../storage/common/storage-redundancy.md#geo-redundant-storage), [로컬](../storage/common/storage-redundancy.md#locally-redundant-storage) 중복에 대한 문서를 참조하세요.

## <a name="encryption-settings-in-the-backup-vault"></a>Backup 자격 증명 모음의 암호화 설정

이 섹션에서는 Backup 자격 증명 모음에 저장된 백업 데이터를 암호화하는 데 사용할 수 있는 옵션을 설명합니다. Azure Backup 서비스는 **백업 관리 서비스** 앱을 사용하여 백업 자격 증명 모음의 관리 ID가 아닌 Azure Key Vault에 액세스합니다.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>플랫폼 관리 키를 사용한 백업 데이터 암호화

기본적으로 모든 데이터는 플랫폼 관리 키를 사용하여 암호화됩니다. 이 암호화를 사용하도록 설정하기 위해 사용자 쪽에서 명시적인 작업을 수행할 필요는 없습니다. Backup 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

Backup 자격 증명 모음은 시간이 지남에 따라 만들어지는 복구 지점을 저장하고 백업 관련 작업을 수행하기 위한 인터페이스를 제공하는 관리 엔터티입니다. 여기에는 주문형 백업 수행, 복원 수행 및 백업 정책 만들기가 포함됩니다.

Backup 자격 증명 모음을 만들려면 다음 단계를 수행합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

<https://portal.azure.com> 에서 Azure Portal에 로그인합니다.

### <a name="create-backup-vault"></a>Backup 자격 증명 모음 만들기

1. 검색 창에 **Backup 자격 증명 모음** 을 입력합니다.
2. **서비스** 에서 **Backup 자격 증명 모음** 을 선택합니다.
3. **백업 자격 증명 모음** 페이지에서 **추가** 를 선택합니다.
4. **기본** 탭의 **프로젝트 세부 정보** 아래에서 올바른 구독이 선택되어 있는지 확인한 다음, **새 리소스 그룹 만들기** 를 선택합니다. 이름으로 *myResourceGroup* 을 입력합니다.

    ![새 리소스 그룹 만들기](./media/backup-vault-overview/new-resource-group.png)

5. **인스턴스 세부 정보** 에서 **Backup 자격 증명 모음 이름** 으로 *myVault* 를 입력하고 원하는 지역을 선택합니다. 이 경우 **지역** 에 *미국 동부* 를 선택합니다.
6. 이제 **스토리지 중복** 을 선택합니다. 자격 증명 모음으로 항목을 보호한 후에는 스토리지 중복을 변경할 수 없습니다.
7. Azure를 기본 백업 스토리지 엔드포인트로 사용 중인 경우 기본 **지역 중복** 설정을 계속 사용하는 것이 좋습니다.
8. Azure를 기본 백업 저장소 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복** 을 선택합니다. [지역](../storage/common/storage-redundancy.md#geo-redundant-storage) 및 [로컬](../storage/common/storage-redundancy.md#locally-redundant-storage) 중복에 대해 자세히 알아봅니다.

    ![스토리지 중복 선택](./media/backup-vault-overview/storage-redundancy.png)

9. 페이지 아래쪽에서 검토 + 만들기 단추를 선택합니다.

    ![검토 + 만들기 선택](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Backup 자격 증명 모음 삭제

이 섹션에서는 Backup 자격 증명 모음을 삭제하는 방법을 설명합니다. 종속성을 제거한 후 자격 증명 모음을 삭제하는 방법에 대한 지침이 포함되어 있습니다.

### <a name="before-you-start"></a>시작하기 전에

다음과 같은 종속성이 있는 Backup 자격 증명 모음을 삭제할 수 없습니다.

- 보호된 데이터 원본(예: PostgreSQL 서버용 Azure database)을 포함하는 자격 증명 모음은 삭제할 수 없습니다.
- 백업 데이터를 포함하는 자격 증명 모음은 삭제할 수 없습니다.

종속성을 제거하지 않고 자격 증명 모음을 삭제하려고 하면 다음 오류 메시지가 표시됩니다.

>백업 자격 증명 모음에 기존 백업 인스턴스 또는 백업 정책이 있으므로 Backup 자격 증명 모음을 삭제할 수 없습니다. 자격 증명 모음에 있는 모든 백업 인스턴스와 백업 정책을 삭제한 후 자격 증명 모음을 삭제합니다.

Backup Vault를 삭제하기 전에 제거해야 할 기존 백업 인스턴스 또는 정책을 누락하지 않도록 **백업 센터** 에서 **데이터 원본 유형** 필터 옵션을 순환해야 합니다.

![데이터 원본 유형](./media/backup-vault-overview/datasource-types.png)

### <a name="proper-way-to-delete-a-vault"></a>자격 증명 모음을 삭제하는 적절한 방법

>[!WARNING]
다음 작업은 파괴적이며 실행 취소할 수 없습니다. 보호된 서버와 연결된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제됩니다. 주의하여 진행하세요.

자격 증명 모음을 제대로 삭제하려면 다음 순서대로 단계를 수행해야 합니다.

- 보호된 항목이 있는지 확인합니다.
  - 왼쪽 탐색 모음에서 **Backup 인스턴스** 로 이동합니다. 여기에 나열된 모든 항목을 먼저 삭제해야 합니다.

이러한 단계를 완료하면 계속해서 자격 증명 모음을 삭제할 수 있습니다.

### <a name="delete-the-backup-vault"></a>Backup 자격 증명 모음 삭제

자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제** 를 선택합니다. 자격 증명 모음을 삭제할 것인지 묻는 확인 텍스트가 표시됩니다.

![자격 증명 모음 삭제](./media/backup-vault-overview/delete-vault.png)

1. 자격 증명 모음을 삭제할 것인지 확인하려면 **예** 를 선택합니다. 자격 증명 모음이 삭제됩니다. 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="monitor-and-manage-the-backup-vault"></a>Backup 자격 증명 모음 모니터링 및 관리

이 섹션에서는 Backup 자격 증명 모음 **개요** 대시보드를 사용하여 Backup 자격 증명 모음을 모니터링하 고 관리하는 방법을 설명합니다. 개요 창에는 **작업** 및 **인스턴스** 라는 두 개의 타일이 포함되어 있습니다.

![개요 대시보드](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Backup 인스턴스 관리

**작업** 타일에는 Backup 자격 증명 모음에 있는 모든 백업 및 복원 관련 작업에 대한 요약 보기가 표시됩니다. 이 타일에서 숫자를 선택하면 특정 데이터 원본 유형, 작업 유형 및 상태의 작업에 대한 자세한 정보를 확인할 수 있습니다.

![Backup 인스턴스](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Backup 작업 관리

**Backup 인스턴스** 타일에서 Backup 자격 증명 모음에 있는 모든 백업 인스턴스의 요약 보기를 확인할 수 있습니다. 이 타일에서 숫자를 선택하면 특정 데이터 원본 유형 및 보호 상태의 백업 인스턴스에 대한 자세한 정보를 확인할 수 있습니다.

![Backup 작업](./media/backup-vault-overview/backup-jobs.png)

## <a name="move-a-backup-vault-across-azure-subscriptionsresource-groups-public-preview"></a>Azure 구독/리소스 그룹 간에 Backup 자격 증명 모음 이동(공개 미리 보기)

이 섹션에서는 Azure Portal 사용하여 Azure 구독 및 리소스 그룹 간에 Backup 자격 증명 모음(Azure Backup 대해 구성됨)을 이동하는 방법을 설명합니다.

>[!Note]
>[PowerShell](/powershell/module/az.resources/move-azresource?view=azps-6.3.0&preserve-view=true) 및 [CLI](/cli/azure/resource?view=azure-cli-latest&preserve-view=true#az_resource_move)를 사용하여 Backup 자격 증명 모음을 다른 리소스 그룹 또는 구독으로 이동할 수도 있습니다.

### <a name="supported-regions"></a>지원되는 지역

구독 및 리소스 그룹 간에 자격 증명 모음 이동은 현재 미국 서부, 미국 중남부, 동아시아, 스위스 북부, 남아프리카 북부, 영국 서부, 미국 중북부, 아랍에미리트 북부, 노르웨이 동부, 오스트레일리아 남동부, 일본 서부, 캐나다 동부, 한국 중부, 오스트레일리아 중부, 미국 중서부, 인도 중부, 인도 중부, 인도 서부, 인도 남부, 인도 남부, 아랍에미리트 중부에서 지원됩니다.  남아프리카 공화국 서부, 노르웨이 서부, 스위스 서부

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-resource-group"></a>Azure Portal 사용하여 Backup 자격 증명 모음을 다른 리소스 그룹으로 이동

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Backup 자격 증명 모음 목록을 열고 이동하려는 자격 증명 모음을 선택합니다.

   자격 증명 모음 대시보드에 자격 증명 모음 세부 정보가 표시됩니다.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-inline.png" alt-text="다른 리소스 그룹으로 이동할 자격 증명 모음의 대시보드를 보여주는 스크린샷." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-expanded.png"::: 

1. 자격 증명 모음 **개요** 메뉴에서 **이동** 을 클릭한 **다음, 다른 리소스 그룹으로 이동을** 선택합니다.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-resource-group-inline.png" alt-text="Backup 자격 증명 모음을 다른 리소스 그룹으로 이동하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-vault-overview/select-move-to-another-resource-group-expanded.png":::
   >[!Note]
   >관리자 구독에만 자격 증명 모음을 이동하는 데 필요한 권한이 있습니다.

1. 리소스 **그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기를** 선택하여 새 리소스 그룹을 만듭니다.

   구독은 동일하게 유지되며 자동으로 채워집니다.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-inline.png" alt-text="기존 리소스 그룹의 선택 항목 또는 새 리소스 그룹 만들기를 보여주는 스크린샷." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-expanded.png":::

1. 이동할 리소스 탭에서 **이동해야** 하는 Backup 자격 증명 모음은 유효성 검사를 거칩니다. 이 프로세스에 몇 분 정도 걸릴 수 있습니다. 유효성 검사가 완료 될 때까지 기다립니다.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-inline.png" alt-text="Backup 자격 증명 모음 유효성 검사 상태를 보여주는 스크린샷." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-expanded.png"::: 

1. _이동된 리소스와 연결된 도구 및 스크립트가 새 리소스 ID_'를 사용하여 확인하도록 업데이트할 때까지 작동하지 않음 확인란을 선택한 다음, **이동을** 선택합니다.
 
   >[!Note]
   >리소스 그룹 또는 구독 간에 자격 증명 모음을 이동한 후 리소스 경로가 변경됩니다. 이동 작업이 완료된 후 도구 및 스크립트를 새 리소스 경로로 업데이트해야 합니다.

이동 작업이 완료되어 자격 증명 모음에서 다른 작업을 수행할 때까지 기다립니다. 이동이 진행되는 동안 수행된 경우 Backup 자격 증명 모음에서 수행된 모든 작업이 실패합니다. 프로세스가 완료되면 Backup 자격 증명 모음이 대상 리소스 그룹에 표시됩니다.

>[!Important]
>자격 증명 모음을 이동하는 동안 오류가 발생하면 [오류 코드 및 문제 해결 섹션을](#error-codes-and-troubleshooting)참조하세요.  

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-subscription"></a>Azure Portal 사용하여 Backup 자격 증명 모음을 다른 구독으로 이동

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Backup 자격 증명 모음 목록을 열고 이동하려는 자격 증명 모음을 선택합니다.
   
   자격 증명 모음 대시보드에 자격 증명 모음 세부 정보가 표시됩니다.

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-inline.png" alt-text="다른 Azure 구독으로 이동할 자격 증명 모음의 대시보드를 보여주는 스크린샷." lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-expanded.png"::: 

1. 자격 증명 모음 **개요** 메뉴에서 **이동** 을 클릭한 **다음, 다른 구독으로 이동을** 선택합니다.

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-subscription-inline.png" alt-text="Backup 자격 증명 모음을 다른 Azure 구독으로 이동하는 옵션을 보여주는 스크린샷." lightbox="./media/backup-vault-overview/select-move-to-another-subscription-expanded.png"::: 
   >[!Note]
   >관리자 구독에만 자격 증명 모음을 이동하는 데 필요한 권한이 있습니다.

1. **구독** 드롭다운 목록에서 기존 구독을 선택합니다.

   구독 간에 자격 증명 모음을 이동하려면 대상 구독이 원본 구독과 동일한 테넌트 안에 있어야 합니다. 자격 증명 모음을 다른 테넌트로 이동하려면 [다른 디렉터리로 구독 전송을 참조하세요.](../role-based-access-control/transfer-subscription.md)

1. 리소스 **그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기를**  선택하여 새 리소스 그룹을 만듭니다.

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-inline.png" alt-text="기존 리소스 그룹을 선택하거나 다른 Azure 구독에서 새 리소스 그룹을 만드는 것을 보여주는 스크린샷." lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-expanded.png":::

1. 이동할 리소스 탭에서 **이동해야** 하는 Backup 자격 증명 모음은 유효성 검사를 거칩니다. 이 프로세스에 몇 분 정도 걸릴 수 있습니다. 유효성 검사가 완료 될 때까지 기다립니다.

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-inline.png" alt-text="다른 Azure 구독으로 이동할 Backup 자격 증명 모음의 유효성 검사 상태를 보여주는 스크린샷." lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-expanded.png"::: 

1. _이동된 리소스와 연결된 도구 및 스크립트가 새 리소스 ID를 사용하여 확인하도록 업데이트할 때까지 작동하지 않음_ 확인란을 선택한 다음, **이동을** 선택합니다.
 
   >[!Note]
   >리소스 그룹 또는 구독 간에 자격 증명 모음을 이동한 후 리소스 경로가 변경됩니다. 이동 작업이 완료된 후 도구 및 스크립트를 새 리소스 경로로 업데이트해야 합니다.

이동 작업이 완료되어 자격 증명 모음에서 다른 작업을 수행할 때까지 기다립니다. 이동이 진행되는 동안 수행된 경우 Backup 자격 증명 모음에서 수행된 모든 작업이 실패합니다. 프로세스가 완료되면 Backup 자격 증명 모음이 대상 구독 및 리소스 그룹에 표시됩니다.

>[!Important]
>자격 증명 모음을 이동하는 동안 오류가 발생하면 [오류 코드 및 문제 해결 섹션을](#error-codes-and-troubleshooting)참조하세요.

### <a name="error-codes-and-troubleshooting"></a>오류 코드 및 문제 해결

Backup 자격 증명 모음 이동 중에 발생할 수 있는 다음과 같은 일반적인 문제를 해결합니다.

#### <a name="backupvaultmoveresourcespartiallysucceeded"></a>BackupVaultMoveResourcesPartiallySucceeded   

**원인:** Backup 자격 증명 모음 이동이 부분적으로만 성공하는 경우 이 오류가 발생할 수 있습니다.

**권장 사항:** 이 문제는 36시간 이내에 자동으로 해결되어야 합니다. 계속되면 Microsoft 지원에 문의하세요.

#### <a name="backupvaultmoveresourcescriticalfailure"></a>BackupVaultMoveResourcesCriticalFailure 

**원인:** Backup 자격 증명 모음 이동이 심각하게 실패할 때 이 오류가 발생할 수 있습니다. 

**권장 사항:** 이 문제는 36시간 이내에 자동으로 해결되어야 합니다. 계속되면 Microsoft 지원에 문의하세요. 

#### <a name="usererrorbackupvaultresourcemoveinprogress"></a>UserErrorBackupVaultResourceMoveInProgress 

**원인:** 이동 중인 Backup 자격 증명 모음에서 작업을 수행하려고 하면 이 오류가 발생할 수 있습니다. 

**권장 사항:** 이동 작업이 완료 될 때까지 기다린 다음 다시 시도 합니다. 
#### <a name="usererrorbackupvaultresourcemovenotallowedformultipleresources"></a>UserErrorBackupVaultResourceMoveNotAllowedForMultipleResources

**원인:** 한 번의 시도에서 여러 Backup 자격 증명 모음을 이동하려고 하면 이 오류가 발생할 수 있습니다. 

**다시 주석 처리:** 모든 이동 작업에 대해 하나의 Backup 자격 증명 모음만 선택되어 있는지 확인합니다. 
#### <a name="usererrorbackupvaultresourcemovenotalloweduntilresourceprovisioned"></a>UserErrorBackupVaultResourceMoveNotAllowedUntilResourceProvisioned

**원인:** 자격 증명 모음이 아직 프로비전되지 않은 경우 이 오류가 발생할 수 있습니다. 

**권장 사항:** 일정 시간 후에 작업을 다시 시도합니다.

#### <a name="backupvaultresourcemoveisnotenabled"></a>BackupVaultResourceMoveIsNotEnabled 

**원인:** Backup 자격 증명 모음에 대한 리소스 이동은 현재 선택한 Azure 지역에서 지원되지 않습니다.

**권장 사항:** Backup 자격 증명 모음을 이동할 지원되는 지역 중 하나를 선택했는지 확인합니다. [지원되는 지역](#supported-regions)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL 데이터베이스에서 백업 구성](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)