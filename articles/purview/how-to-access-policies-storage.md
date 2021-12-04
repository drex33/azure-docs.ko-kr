---
title: Azure Storage 대한 데이터 액세스 정책 프로비저닝
description: Azure Purview 액세스 정책에 Azure Storage 통합하고 데이터 소유자가 액세스 정책을 빌드할 수 있도록 하는 방법에 대한 단계별 가이드입니다.
author: ePpnqeqR
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 12/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: abc7d873995fdcc98ea83a1c5a3dc145f855abb1
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543297"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage-preview"></a>Azure Storage 데이터 소유자별 데이터 세트 프로비전(미리 보기)

## <a name="supported-capabilities"></a>지원되는 기능
이 가이드에서는 Azure Purview에서 만들고 관리하는 데이터 액세스 정책을 적용하도록 Azure Storage 구성하는 방법을 설명합니다. Azure Purview 정책 작성은 다음 기능을 지원합니다.
-   Blob 및 ADLS(Azure Data Lake Storage) Gen2에 저장된 데이터에 대한 액세스를 제어하는 데이터 정책입니다.

> [!NOTE]
> 이러한 기능은 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에 사용하면 안 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
>[!IMPORTANT]
> 액세스 정책 기능은 **새** Azure Purview 및 Azure Storage 계정에서만 사용할 수 있습니다.
- 새 테스트 구독을 만들거나 기존의 격리된 테스트 구독을 사용합니다. 이 [가이드에 따라 를 만들](../cost-management-billing/manage/create-subscription.md)수 있습니다.
- 새 Purview 계정을 만듭니다. 빠른 [시작 가이드에 따라 을 만들](create-catalog-portal.md)수 있습니다.
- 아래에 나열된 지역 중 하나에 새 Azure Storage 계정을 만듭니다. 이 [가이드에 따라 를 만들](../storage/common/storage-account-create.md)수 있습니다. Storage 계정 버전만 = 81.x.x >정책 적용을 지원합니다.

[!INCLUDE [supported regions](./includes/storage-access-policy-regions.md)]

## <a name="configuration"></a>구성
[!INCLUDE [access policy enablement storage](./includes/storage-access-policy-enable.md)]

### <a name="opt-in-to-participate-in-azure-purview-data-access-policy-preview"></a>Azure Purview 데이터 액세스 정책 미리 보기에 참여하도록 옵트인
이 기능은 현재 미리 보기 상태입니다. [Purview 데이터 액세스 정책 미리 보기에 옵트인해야](https://aka.ms/opt-in-data-use-policy) 합니다.

### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>Purview를 다른 구독의 리소스 공급자로 등록
Storage 및 Purview 계정이 서로 다른 구독에 있는 경우에만 이 단계를 실행합니다. Azure Purview를 azure 리소스 공급자 및 유형 가이드에 따라 Azure Storage 계정에 대한 구독에서 [리소스 공급자로 등록합니다.](../azure-resource-manager/management/resource-providers-and-types.md)

### <a name="configure-permissions-for-policy-management-actions"></a>정책 관리 작업에 대한 권한 구성
#### <a name="storage-account-permissions"></a>스토리지 계정 권한
Azure Purview에서 *데이터 사용 거버넌스에* 등록하려면 사용자는 Azure Storage 계정에 이러한 역할 조합 **중 하나가** 있어야 합니다.
- IAM *소유자* 
- IAM *기여자* + IAM *사용자 액세스 관리자* 모두
 
이 가이드에 따라 [Azure RBAC 권한을 구성할](../role-based-access-control/check-access.md) 수 있습니다.

#### <a name="purview-account-permissions"></a>Purview 계정 권한
>[!IMPORTANT]
> - 정책 작업은 자식 컬렉션 수준이 아닌 **루트 컬렉션 수준에서만** 지원됩니다.
- 사용자는 *다음을* 수행하려면 루트 컬렉션 수준에서 Purview 데이터 원본 관리자 역할이 필요합니다.
  - 데이터 사용 거버넌스에 대한 원본을 *등록합니다.*
  - 정책 게시
- 정책을 만들거나 편집하려면 루트 컬렉션 수준에서 Purview *정책 작성자* 역할이 필요합니다.

이 [가이드에서](how-to-create-and-manage-collections.md)Purview 역할 할당 관리에 대한 섹션을 확인합니다.

>[!WARNING]
> 권한과 관련된 **알려진 문제**
> - Purview 정책 *작성자* 역할 외에도 사용자는 데이터 소유자 정책을 만들려면 Azure Active Directory(AAD)의 *디렉터리 읽기* 권한자 권한이 필요합니다. AAD Directory Reader에 대한 자세한 내용은 [여기를 클릭하세요.](../active-directory/roles/permissions-reference.md#directory-readers)
> - Purview *정책 작성자* 역할은 정책을 만드는 데 충분하지 않습니다. Purview *데이터 원본 관리자* 역할도 필요합니다.

### <a name="register-and-scan-data-sources-in-purview"></a>Purview에서 데이터 원본 등록 및 검사
Purview에 각 데이터 원본을 등록하고 검사하여 나중에 액세스 정책을 정의합니다. 다음 가이드를 따를 수 있습니다.

-   [Blob Azure Storage 등록 및 검사 - Azure Purview](register-scan-azure-blob-storage-source.md)

-   [ADLS(Azure Data Lake Storage) Gen2 등록 및 검사 - Azure Purview](register-scan-adls-gen2.md)

>[!NOTE]
> 등록하는 동안 원본에 지정한 **이름을** 기록해 둡다. 정책 문을 만들거나 게시할 때 필요합니다. Storage 계정에 사용한 것과 동일한 이름을 지정하는 것이 가장 좋습니다.

등록하는 동안 그림과 같이 데이터 **사용 거버넌스** 토글을 통해 액세스 정책에 대한 데이터 원본을 사용하도록 설정합니다.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="이미지는 정책에 대한 데이터 원본을 등록하는 방법을 보여줍니다.":::

>[!IMPORTANT]
> - 사용자에게 데이터 *사용 거버넌스에* 대한 원본을 등록하려면 Azure Storage *소유자* 및 Purview *데이터 원본 관리자가* 모두 있어야 하지만, 이러한 역할 중에서 독립적으로 등록을 해제할 수 있습니다.
> - *데이터 사용 거버넌스에* 대한 구독을 사용하지 않도록 설정하면 해당 구독에 등록된 모든 자산에 대해서도 사용하지 않도록 설정됩니다.


### <a name="data-use-governance-best-practices"></a>데이터 사용 거버넌스 모범 사례
- *데이터 사용 거버넌스에* 대한 모든 데이터 원본을 등록하고 단일 Azure Purview 계정에서 연결된 모든 액세스 정책을 관리하는 것이 좋습니다.
- 그러나 Purview 계정이 여러 개 있어야 하는 경우 구독에 속하는 모든 데이터 원본은 단일 Purview 계정에서 *데이터 사용 거버넌스에만* 등록할 수 있습니다. Purview 계정 자체는 테넌트 내의 모든 구독에 있을 수 있습니다. 잘못된 구성이 있으면 *데이터 사용 거버넌스* 토글이 회색으로 표시됩니다. 아래 다이어그램에 몇 가지 예제가 있습니다.
    - **사례 1은** Storage 계정이 동일한 구독의 Purview 계정에 등록되는 유효한 구성을 보여 줍니다.
    - **사례 2는** Storage 계정이 다른 구독의 Purview 계정에 등록되는 유효한 구성을 보여 줍니다. 
    - **사례 3은** Storage 계정 S3SA1 및 S3SA2가 모두 구독 3에 속하지만 다른 Purview 계정에 등록되기 때문에 발생하는 잘못된 구성을 보여 줍니다. 

:::image type="content" source="./media/how-to-access-policies-storage/valid-and-invalid configurations.png" alt-text="다이어그램은 여러 Purview 계정을 사용하여 정책을 관리할 때 유효하고 잘못된 구성을 보여 줍니다.":::

## <a name="policy-authoring"></a>정책 작성

이 섹션에서는 Purview 액세스 정책을 만들고, 업데이트하고, 게시하는 단계를 설명합니다.

### <a name="create-a-new-policy"></a>새 정책 만들기

이 섹션에서는 Azure Purview에서 새 정책을 만드는 단계를 설명합니다.

1. Purview 포털에 로그인합니다.

1. 왼쪽 패널을 사용하여 **정책 관리** 앱으로 이동합니다.

1. 정책 페이지에서 **새 정책** 단추를 선택합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-1.png" alt-text="이미지는 데이터 소유자가 정책을 만들 때 Azure Purview의 정책 기능에 액세스할 수 있는 방법을 보여줍니다.":::

1. 새 정책 페이지가 표시됩니다. 정책 **이름** 및 **설명을 입력합니다.**

1. 새 정책에 정책 문을 추가하려면 **새 정책 문** 단추를 선택합니다. 그러면 정책 문 작성기 가 생성됩니다.

    :::image type="content" source="./media/how-to-access-policies-storage/create-new-policy-storage.png" alt-text="이미지는 데이터 소유자가 새 정책 문을 만드는 방법을 보여줍니다.":::

1. **작업** 단추를 선택하고 드롭다운 목록에서 읽기 또는 수정을 선택합니다.

1. **효과** 단추를 선택하고 드롭다운 목록에서 허용을 선택합니다.

1. 데이터 **리소스** 단추를 선택하여 데이터 자산 경로를 제공하는 옵션을 표시합니다.

1. **자산** 상자에 데이터 원본 **유형을** 입력하고 이전에 등록된 데이터 원본의 **이름을** 선택합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-source-type-storage.png" alt-text="이미지는 정책 문을 편집할 때 데이터 소유자가 데이터 리소스를 선택하는 방법을 보여줍니다.":::

1. **계속** 단추를 선택하고 계층 구조를 반대로 전환하여 폴더 또는 파일을 선택합니다. 그런 **다음, 추가** 단추를 선택합니다. 그러면 정책 편집기로 돌아갑니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="이미지는 정책 문을 만들거나 편집할 때 데이터 소유자가 자산을 선택하는 방법을 보여줍니다.":::

1. **주체** 단추를 선택하고 주체 ID를 보안 주체, 그룹 또는 MSI로 입력합니다. 그런 다음, **확인** 단추를 선택합니다. 그러면 정책 편집기로 돌아갑니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="이미지는 정책 문을 만들거나 편집할 때 데이터 소유자가 제목을 선택하는 방법을 보여줍니다.":::

1. #5~#11 단계를 반복하여 정책 문을 더 입력합니다.

1. **저장** 단추를 선택하여 정책을 저장합니다.

> [!WARNING]
> 정책 만들기와 관련된 **알려진 문제**
> - Purview 리소스 집합을 기반으로 정책 문을 만들지 마십시오. Purview 정책 작성 UI에 표시되더라도 아직 적용되지 않습니다. 리소스 집합의 개념에 대한 설명을 보려면 [여기를 클릭하세요.](concept-resource-sets.md)
> - 컨테이너 수준 아래의 데이터 개체를 선택하는 Blob Storage에 대한 정책 문을 만들지 마십시오. Blob 저장소는 컨테이너 수준 에서만 액세스 제어를 제공 하므로 이러한 정책이 적용 되지 않습니다.
> - *데이터 사용* 에 대 한 구독이 사용 하지 않도록 설정 되 면 *데이터 사용* 을 위해 사용 하도록 설정 된 모든 기본 자산이 적절 한 동작인 사용 하지 않도록 설정 됩니다. 그러나 해당 자산을 기반으로 하는 정책 문은 이후에도 허용 됩니다.

### <a name="update-or-delete-a-policy"></a>정책 업데이트 또는 삭제

부서의 범위에서 새 정책을 만드는 단계는 다음과 같습니다.

1. 부서의 범위 포털에 로그인 합니다.

1. 왼쪽 패널을 사용 하 여 부서의 범위 정책 앱으로 이동 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="이미지는 정책을 업데이트 하려는 경우 데이터 소유자가 Azure 부서의 범위의 정책 기능에 액세스 하는 방법을 보여 줍니다.":::

1. 정책 포털에서 부서의 범위의 기존 정책 목록이 표시 됩니다. 업데이트 해야 하는 정책을 선택 합니다.

1. 정책 세부 정보 페이지가 표시 됩니다 (편집 및 삭제 옵션 포함). **편집** 단추를 선택 합니다 .이 단추는이 정책의 문에 대 한 정책 문 작성기를 표시 합니다. 이제이 정책에 있는 문의 모든 부분을 업데이트할 수 있습니다. 정책을 삭제 하려면 **삭제** 단추를 사용 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책 문을 편집 하거나 삭제 하는 방법을 보여 줍니다.":::

### <a name="publish-the-policy"></a>정책 게시

새로 만든 정책이 초안 상태입니다. 게시 프로세스는 새 정책을 거 버 넌 스에서 하나 이상의 데이터 원본에 연결 합니다.
정책을 게시 하는 단계는 다음과 같습니다.

1. 부서의 범위 포털에 로그인 합니다.

1. 왼쪽 패널을 사용 하 여 부서의 범위 정책 앱으로 이동 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="이미지는 정책을 게시 하려는 경우 데이터 소유자가 Azure 부서의 범위의 정책 기능에 액세스 하는 방법을 보여 줍니다.":::

1. 정책 포털에서 부서의 범위의 기존 정책 목록이 표시 됩니다. 게시 해야 하는 정책을 찾습니다. 페이지의 오른쪽 상단 모서리에 있는 **게시** 단추를 선택 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책을 게시할 수 있는 방법을 보여 줍니다.":::

1. 데이터 원본 목록이 표시 됩니다. 이름을 입력 하 여 목록을 필터링 할 수 있습니다. 그런 다음이 정책을 게시할 각 데이터 원본을 선택 하 고 **게시** 단추를 선택 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책이 게시 될 데이터 원본을 선택할 수 있는 방법을 보여 줍니다.":::

>[!NOTE]
> Publish는 백그라운드 작업입니다. 변경 내용이 데이터 원본에 반영 되는 데 최대 **2 시간이** 걸릴 수 있습니다.

## <a name="policy-action-mapping"></a>정책 작업 매핑

이 섹션에는 Azure 부서의 범위 데이터 정책의 작업이 Azure Storage의 특정 작업에 매핑되는 방법에 대 한 참조가 포함 되어 있습니다.

| **부서의 범위 정책 작업** | **데이터 원본 관련 작업**                                                        |
|---------------------------|-----------------------------------------------------------------------------------------|
|||
| *읽기*                    |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/read                |
|||
| *수정*                  |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/read                |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/write               |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/add/action          |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/move/action         |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/blobs/delete              |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/read                      |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/write                     |
|                           |<sub>Microsoft. Storage/storageAccounts/blobServices/containers/delete                    |
|||


## <a name="next-steps"></a>다음 단계
이 방법 가이드에 설명 된 기능과 관련 된 블로그 및 데모를 확인 합니다.

* [Microsoft Ignite 2021에서 제공 되는 Azure 부서의 범위의 새로운 기능](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Azure Storage에 대 한 액세스 정책 데모](https://www.youtube.com/watch?v=CFE8ltT19Ss)
