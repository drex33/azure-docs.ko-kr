---
title: 랩에서 사용 되는 Azure storage 계정 암호화
description: Azure DevTest Labs에서 랩에서 사용되는 Azure Storage 암호화를 구성하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 337cd45b20b9fe52bb487a3b36bde905541f840b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552588"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에서 사용되는 Azure Storage 암호화
Azure DevTest Labs에서 생성되는 모든 랩은 연결된 Azure Storage 계정을 사용하여 생성됩니다. 스토리지 계정은 다음 용도로 사용됩니다. 

- 가상 머신을 만드는 데 사용할 수 있는 [수식](devtest-lab-manage-formulas.md) 문서를 저장합니다.
- 아티팩트를 적용하여 생성된 배포 및 확장 로그를 포함하는 아티팩트 결과를 저장합니다. 
- [VHD(가상 하드 디스크)를 업로드하여 랩에 사용자 지정 이미지를 만듭니다.](devtest-lab-create-template.md)
- 가상 머신/환경 생성 중에 더 빠르게 검색하기 위해 자주 사용되는 [아티팩트](add-artifact-vm.md) 및 [Azure Resource Manager 템플릿](devtest-lab-create-environment-from-arm.md)을 캐싱합니다.

> [!NOTE]
> 위의 정보는 랩을 운영하는 데 중요합니다. 이 정보는 명시적으로 삭제하지 않는 한 랩 (및 랩 리소스)의 수명 내내 저장됩니다. 이러한 리소스를 수동으로 삭제하면 랩 VM을 만들 때 오류가 발생하거나 나중에 사용할 때 수식이 손상될 수 있습니다. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>스토리지 계정을 찾아 해당 콘텐츠 보기

1. 랩의 홈페이지에 있는 **개요** 페이지에서 **리소스 그룹** 을 선택합니다. 랩을 포함하는 리소스 그룹에 대한 **리소스 그룹** 페이지를 볼 수 있습니다. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="개요 페이지에서 리소스 그룹 선택":::
1. 랩의 Azure Storage 계정을 선택합니다. 랩 스토리지 계정에 대한 명명 규칙은 `a<labNameWithoutInvalidCharacters><4-digit number>`입니다. 예를 들어 랩 이름이 `contosolab`인 경우 스토리지 계정 이름은 `acontosolab7576`일 수 있습니다. 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="랩의 리소스 그룹에서 스토리지 계정을 선택합니다":::.
3. **스토리지 계정** 페이지의 왼쪽 메뉴에서 **Storage Explorer(미리 보기)** 를 선택하고 **BLOB 컨테이너** 를 선택하여 연관 있는 랩 관련 내용을 찾습니다. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Storage Explorer(미리 보기)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>랩 스토리지 계정 암호화
Azure Storage는 클라우드에 데이터를 저장할 때 자동으로 암호화합니다. Azure Storage 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

랩 스토리지 계정의 데이터는 **Microsoft 관리형 키** 로 암호화됩니다. Microsoft 관리형 키를 데이터 암호화에 사용할 수도 있고 자체 키를 사용하여 암호화를 관리할 수도 있습니다. 사용자 고유의 키로 랩 스토리지 계정에 대한 암호화를 관리하도록 선택하는 경우 Blob Storage 및 Azure Files에서 데이터를 암호화/암호 해독하는 데 사용할 Azure Key Vault로 **고객 관리형 키** 를 지정할 수 있습니다. 고객 관리형 키에 대한 자세한 내용은 [Azure Key Vault를 통해 고객 관리 키를 사용하여 Azure Storage 암호화 관리](../storage/common/customer-managed-keys-overview.md)를 참조하세요.

Azure Storage 암호화를 위한 고객 관리형 키를 구성하는 방법을 알아보려면 다음 문서를 참조하세요. 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Azure Blob Storage 수명 주기 관리
앞서 설명한 것처럼 랩의 스토리지 계정에 저장된 정보는 랩이 오류 없이 작동하는 데 매우 중요합니다. 명시적으로 삭제하지 않는 한 이 데이터는 데이터 형식에 따라 랩의 수명 또는 특정 랩 가상 머신의 수명 동안 랩의 스토리지 계정에 계속 남아 있습니다.

### <a name="uploaded-vhds"></a>업로드된 VHD
이러한 VHD는 사용자 지정 이미지를 만드는 데 사용됩니다. 이러한 파일을 제거하면 더 이상 VHD에서 사용자 지정 이미지를 만들 수 없게 됩니다.

### <a name="artifacts-cache"></a>아티팩트 캐시
이러한 캐시는 아티팩트가 적용될 때마다 다시 생성됩니다. 각각 참조된 리포지토리의 최신 콘텐츠를 사용하여 새로 고쳐집니다. 따라서 스토리지 관련 비용을 절약하려고 이 정보를 삭제하는 경우 해당 릴리프는 일시적일 수 있습니다.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager 템플릿 캐시
이러한 캐시는 Azure Resource Manager 기반 템플릿 리포지토리가 연결되고 랩에서 스핀업될 때마다 다시 생성됩니다. 각각 참조된 리포지토리의 최신 콘텐츠를 사용하여 새로 고쳐집니다. 따라서 스토리지 관련 비용을 절약하려고 이 정보를 삭제하는 경우 해당 릴리프는 일시적일 수 있습니다.

### <a name="formulas"></a>수식
이러한 문서는 기존 VM에서 수식을 만드는 옵션과 수식에서 VM을 만드는 옵션 모두를 지원하는 데 사용됩니다. 이러한 수식 문서를 삭제하면 다음 작업을 수행하는 동안 오류가 발생할 수 있습니다.

- 기존 랩 VM에서 수식 만들기
- 수식 만들기 또는 업데이트 
- 수식에서 VM 만들기

### <a name="artifact-results"></a>아티팩트 결과
아티팩트가 적용될 때 랩 VM에서 실행되는 아티팩트의 수와 유형에 따라 각 아티팩트 결과의 크기가 시간이 갈수록 증가할 수 있습니다. 따라서 랩 소유자는 이러한 문서의 수명 주기를 제어하는 것이 좋습니다. 자세한 내용은 [Azure Blob 스토리지 수명 주기 관리](../storage/blobs/lifecycle-management-overview.md)를 참조하세요.

> [!IMPORTANT]
> Azure Storage 계정과 관련된 비용을 줄이려면 이 단계를 수행하는 것이 좋습니다. 

예를 들어 다음 규칙은 아티팩트 결과가 구체적으로 90일 후 만료되는 규칙을 설정하는 데 사용됩니다. 이를 통해 오래된 아티팩트 결과가 정기적으로 스토리지 계정에서 재순환됩니다.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
Azure Storage 암호화를 위한 고객 관리형 키를 구성하는 방법을 알아보려면 다음 문서를 참조하세요. 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)