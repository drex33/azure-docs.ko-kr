---
title: Recovery Services 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 백업과 복구 지점이 저장되는 Recovery Services 자격 증명 모음을 만들고 구성하는 방법에 대해 알아봅니다. 지역 간 복원을 사용하여 보조 지역에서 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: references_regions
ms.openlocfilehash: b301ce0ab2f4b57a03e3ba24d672bb4102a1a997
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110794225"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기 및 구성

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>스토리지 중복 설정

Azure Backup은 자격 증명 모음에 대한 스토리지를 자동으로 처리합니다. 스토리지를 복제하는 방법을 지정해야 합니다.

> [!NOTE]
> Recovery Services 자격 증명 모음에 대한 **스토리지 복제 유형 로컬 중복/지역 중복** 설정 변경은 자격 증명 모음에서 백업을 구성하기 전에 수행해야 합니다. 백업을 구성한 후에는 수정 옵션을 사용할 수 없습니다.
>
>- 백업을 아직 구성하지 않은 경우 [다음 단계에 따라](#set-storage-redundancy) 설정을 검토하고 수정합니다.
>- 백업을 이미 구성했고 GRS에서 LRS로 이동해야 하는 경우 [이 해결 방법을 검토](#how-to-change-from-grs-to-lrs-after-configuring-backup)합니다.

1. **Recovery Services 자격 증명 모음** 창에서 새 자격 증명 모음을 선택합니다. **설정** 섹션에서 **속성** 을 선택합니다.
1. **속성** 의 **백업 구성** 에서 **업데이트** 를 선택합니다.

1. 스토리지 복제 형식을 선택하고 **저장** 을 선택합니다.

     ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Azure를 기본 백업 스토리지 엔드포인트로 사용 중인 경우 기본 **지역 중복** 설정을 계속 사용하는 것이 좋습니다.
   - Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복** 을 선택합니다.
   - [지역](../storage/common/storage-redundancy.md#geo-redundant-storage) 및 [로컬](../storage/common/storage-redundancy.md#locally-redundant-storage) 중복에 대해 자세히 알아봅니다.
   - 지역에서 가동 중지 시간 없는 데이터 가용성을 통한 데이터 상주 보장이 필요할 경우 [영역 중복 스토리지](../storage/common/storage-redundancy.md#zone-redundant-storage)를 선택합니다.

>[!NOTE]
>현재 솔루션은 스냅샷 기반이며 자격 증명 모음으로 이전되는 데이터가 없기 때문에, 자격 증명 모음에 대한 스토리지 복제 설정은 Azure 파일 공유 백업과 관련이 없습니다. 공유 스냅샷은 백업된 파일 공유와 동일한 스토리지 계정에 저장됩니다.

## <a name="set-cross-region-restore"></a>지역 간 복원 설정

**CRR(지역 간 복원)** 옵션을 사용하면 [Azure 쌍을 이루는 보조 지역](../best-practices-availability-paired-regions.md)에서 데이터를 복원할 수 있습니다.

다음과 같은 데이터 원본이 지원됩니다.

- Azure VM(일반 공급)
- Azure VM에서 호스트되는 SQL 데이터베이스(미리 보기)
- Azure VM에서 호스트되는 SAP HANA 데이터베이스(미리 보기)

지역 간 복원을 사용하여 다음을 수행할 수 있습니다.

- 감사 또는 규정 준수 요구 사항이 있는 경우 모의 훈련 수행
- 주 지역에 재해가 있는 경우 데이터 복원

VM을 복원할 때는 VM 또는 그 디스크를 복원할 수 있습니다. Azure VM에 호스트된 SQL/SAP HANA 데이터베이스에서 복원하는 경우 데이터베이스 또는 해당 파일을 복원할 수 있습니다.

이 기능을 선택하려면 **백업 구성** 창에서 **지역 간 복원 사용** 을 선택합니다.

이 프로세스는 스토리지 수준이므로 [가격 책정에 영향을 미칩니다](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>시작하기 전에
>
>- [지원 매트릭스](backup-support-matrix.md#cross-region-restore)에서 지원되는 관리 형식과 지역 목록을 검토합니다.
>- Azure VM의 CRR(지역 간 복원) 기능은 현재 모든 Azure 공용 지역에서 일반 공급됩니다.
>- SQL 및 SAP HANA 데이터베이스에 대한 지역 간 복원은 모든 Azure 공용 지역에서 미리 보기 상태입니다.
>- CRR은 GRS 자격 증명 모음에 대한 자격 증명 모음 수준 옵트인 기능입니다(기본적으로 꺼짐).
>- 옵트인 후에는 보조 지역에서 백업 항목을 사용하는 데 최대 48시간이 걸릴 수 있습니다.
>- 현재 Azure VM용 CRR은 Azure Resource Manager Azure VM 및 암호화된 Azure VM에 대해 지원됩니다. 클래식 Azure VM은 지원되지 않습니다. 추가 관리 형식이 CRR을 지원하는 경우 **자동으로** 등록됩니다.
>- 현재는 보호를 처음으로 시작한 후에 지역 간 복원을 GRS 또는 LRS로 **되돌릴 수 없습니다**.
>- 현재 [RA-GRS(읽기 액세스 지역 중복 스토리지)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 복제는 15분이나, 보조 지역 [RPO](azure-backup-glossary.md#rpo-recovery-point-objective)는 주 지역에서 최대 12시간입니다.

### <a name="configure-cross-region-restore"></a>지역 간 복원 구성

GRS 중복으로 만든 자격 증명 모음에는 지역 간 복원 기능을 구성하는 옵션이 포함됩니다. 모든 GRS 자격 증명 모음에는 설명서에 연결되는 배너가 있습니다. 자격 증명 모음에 대해 CRR을 구성하려면 이 기능을 사용하도록 설정하는 옵션이 있는 백업 구성 창으로 이동합니다.

 ![구성 백업 배너](./media/backup-azure-arm-restore-vms/banner.png)

1. 포털에서 Recovery Services 자격 증명 모음 > **속성**(**설정** 아래)으로 이동합니다.
1. **백업 구성** 아래에서 **업데이트** 를 선택합니다.
1. **이 자격 증명 모음에서 지역 간 복원 사용** 을 선택하여 기능을 사용하도록 설정합니다.

   ![지역 간 복원을 사용하도록 설정](./media/backup-azure-arm-restore-vms/backup-configuration.png)

CRR을 사용한 백업 및 복원에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure VM에 대한 지역 간 복원](backup-azure-arm-restore-vms.md#cross-region-restore)
- [SQL 데이터베이스에 대한 지역 간 복원](restore-sql-database-azure-vm.md#cross-region-restore)
- [SAP HANA 데이터베이스에 대한 지역 간 복원](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>암호화 설정 지정

기본적으로 Recovery Services 자격 증명 모음의 데이터는 플랫폼 관리형 키를 사용하여 암호화됩니다. 이 암호화를 사용하기 위해 사용자의 명시적 작업이 필요하지 않으며 Recovery Services 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.  이 자격 증명 모음에서 백업 데이터를 암호화하기 위해 사용자 고유의 키를 가져오도록 선택할 수 있습니다. 이를 고객 관리형 키라고 합니다. 사용자 고유의 키를 사용하여 백업 데이터를 암호화하려는 경우 이 자격 증명 모음에 대해 보호되는 항목 이전에 암호화 키를 지정해야 합니다. 키를 통한 암호화를 사용하도록 설정한 후에는 되돌릴 수 없습니다.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>고객 관리형 키를 사용하여 암호화하도록 자격 증명 모음 구성

고객 관리형 키로 암호화하도록 자격 증명 모음을 구성한 경우 이 단계를 순서 그대로 수행해야 합니다.

1. Recovery Services 자격 증명 모음에 관리 ID 사용

1. 자격 증명 모음에 사용 권한을 할당하여 Azure Key Vault의 암호화 키에 액세스

1. Azure Key Vault에서 일시 삭제 및 제거 방지 사용

1. Recovery Services 자격 증명 모음에 암호화 키 할당

이 각 단계에 대한 지침은 [이 문서에서](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys) 확인할 수 있습니다.

## <a name="modifying-default-settings"></a>기본 설정 수정

자격 증명 모음에서 백업을 구성하기 전에 **스토리지 복제 유형** 및 **보안 설정** 에 대한 기본 설정을 검토하는 것이 좋습니다.

- 기본적으로 **스토리지 복제 유형** 은 **GRS**(지역 중복)로 설정됩니다. 백업을 구성한 후에는 수정할 수 없습니다.
  - 백업을 아직 구성하지 않은 경우 [다음 단계에 따라](#set-storage-redundancy) 설정을 검토하고 수정합니다.
  - 백업을 이미 구성했고 GRS에서 LRS로 이동해야 하는 경우 [이 해결 방법을 검토](#how-to-change-from-grs-to-lrs-after-configuring-backup)합니다.

- 새로 만든 자격 증명 모음에서는 기본적으로 **일시 삭제** 가 **사용되어** 실수로 인한 삭제 또는 악의적인 삭제로부터 백업 데이터를 보호합니다. [다음 단계에 따라](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 설정을 검토하고 수정합니다.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>백업 구성 후 GRS에서 LRS로 변경하는 방법

GRS에서 LRS(로컬 중복 스토리지)로의 이동을 결정하기 전에 자신의 시나리오에 부합하는 더 낮은 비용과 더 높은 데이터 내구성 간의 장단점을 검토합니다. GRS에서 LRS로 이동해야 한다면 두 가지 중에 선택할 수 있습니다. 백업 데이터 유지에 대한 비즈니스 요구 사항에 따라 달라집니다.

- [이전 백업 데이터를 보존할 필요가 없음](#dont-need-to-preserve-previous-backed-up-data)
- [이전 백업 데이터를 유지해야 함](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>이전 백업 데이터를 보존할 필요가 없음

새 LRS 자격 증명 모음에서 워크로드를 보호하려면 GRS 자격 증명 모음에서 현재 보호 및 데이터를 삭제하고 백업을 다시 구성해야 합니다.

>[!WARNING]
>다음 작업은 파괴적이며 실행 취소할 수 없습니다. 보호된 서버와 연결된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제됩니다. 주의하여 진행하세요.

GRS 자격 증명 모음에서 현재 보호를 중지 및 삭제합니다.

1. GRS 자격 증명 모음 속성에서 일시 삭제를 사용하지 않도록 설정합니다. 일시 삭제를 사용하지 않도록 설정하려면 [다음 단계](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)를 수행합니다.

1. 보호를 중지하고 기존 GRS 자격 증명 모음에서 백업을 삭제합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 항목** 을 선택합니다. LRS 자격 증명 모음으로 이동해야 하는 여기에 나열된 항목은 백업 데이터와 함께 제거해야 합니다. [클라우드에서 보호된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)하는 방법 및 [온-프레미스에서 보호된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-on-premises)하는 방법을 참조하세요.

1. AFS(Azure 파일 공유), SQL 서버 또는 SAP HANA 서버를 이동할 계획인 경우 해당 서버의 등록을 취소해야 합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 인프라** 를 선택합니다. [SQL 서버 등록을 취소](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)하고, [Azure 파일 공유와 연결된 스토리지 계정 등록을 취소](manage-afs-backup.md#unregister-a-storage-account)하고, [SAP HANA 인스턴스 등록을 취소](sap-hana-db-manage.md#unregister-an-sap-hana-instance)하는 방법을 참조하세요.

1. GRS 자격 증명에서 서버가 제거되면 새 LRS 자격 증명 모음에서 워크로드에 대한 백업을 계속 구성합니다.

#### <a name="must-preserve-previous-backed-up-data"></a>이전 백업 데이터를 유지해야 함

GRS 자격 증명 모음에서 현재 보호되는 데이터를 유지하고, 새 LRS 자격 증명 모음에서 보호를 계속해야 하는 경우 일부 워크로드에서 사용할 수 있는 제한된 옵션이 있습니다.

- MARS의 경우 [데이터 보관을 통해 보호를 중지](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)하고 새 LRS 자격 증명 모음에 에이전트를 등록할 수 있습니다.

  - Azure Backup 서비스는 이전 자격 증명 모음의 모든 GRS 복구 지점을 계속 보관합니다.
  - GRS 자격 증명 모음의 복구 지점을 유지하려면 요금을 지불해야 합니다.
  - GRS 자격 증명 모음에 있는 만료되지 않은 복구 지점에 대한 백업된 데이터만 복원할 수 있습니다.
  - LRS 자격 증명 모음에 데이터의 새 초기 복제본을 만들어야 합니다.

- Azure VM의 경우 GRS 자격 증명 모음에서 VM에 대해 [데이터 보관을 통해 보호를 중지](backup-azure-manage-vms.md#stop-protecting-a-vm)하고 VM을 다른 리소스 그룹으로 이동한 다음, LRS 자격 증명 모음에서 해당 VM을 보호할 수 있습니다. 다른 리소스 그룹으로 VM을 이동하는 방법에 대한 [지침 및 제한 사항](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)을 참조하세요.

  VM은 한 번에 하나의 자격 증명 모음에서만 보호할 수 있습니다. 그러나 LRS 리소스 그룹의 VM은 다른 VM으로 간주되므로 새 자격 증명 모음에서 보호할 수 있습니다.

  - Azure Backup 서비스는 GRS 자격 증명 모음에서 백업된 복구 지점을 유지합니다.
  - GRS 자격 증명 모음에서 복구 지점을 유지하려면 요금을 지불해야 합니다. 자세한 내용은 [Azure Backup 가격 책정](azure-backup-pricing.md)을 참조하세요.
  - 필요한 경우 GRS 자격 증명 모음에서 VM을 복원할 수 있습니다.
  - LRS 리소스에 있는 VM의 새 자격 증명에 대한 첫 번째 백업이 초기 복제본이 됩니다.

## <a name="next-steps"></a>다음 단계

Recovery Services 자격 증명 모음에 대해 [알아봅니다.](backup-azure-recovery-services-vault-overview.md)
Recovery Services 자격 증명 모음 삭제에 대해 [알아봅니다.](backup-azure-delete-vault.md)