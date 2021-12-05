---
title: Azure Storage에 대 한 데이터 액세스 정책 프로 비전
description: Azure 부서의 범위 액세스 정책에 Azure Storage를 통합 하는 방법에 대 한 단계별 가이드를 통해 데이터 소유자가 액세스 정책을 작성할 수 있습니다.
author: ePpnqeqR
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 12/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7409c9690c5121a8638e094780e68ceddb40eb2f
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570253"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage-preview"></a>Azure Storage에 대 한 데이터 소유자의 데이터 집합 프로 비전 (미리 보기)

## <a name="supported-capabilities"></a>지원되는 기능
이 가이드에서는 Azure 부서의 범위에서 만들고 관리 하는 데이터 액세스 정책을 적용 하도록 Azure Storage를 구성 하는 방법을 설명 합니다. Azure 부서의 범위 정책 작성은 다음과 같은 기능을 지원 합니다.
-   Blob 및 Azure Data Lake Storage (ADLS) Gen2에 저장 된 데이터에 대 한 액세스를 제어 하는 데이터 정책

> [!NOTE]
> 이러한 기능은 현재 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 작업에 사용 하면 안 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
>[!IMPORTANT]
> 액세스 정책 기능은 **새** Azure 부서의 범위 및 Azure Storage 계정 에서만 사용할 수 있습니다.
- 새를 만들거나 기존 격리 된 테스트 구독을 사용 합니다. [이 가이드에 따라 하나를 만들](../cost-management-billing/manage/create-subscription.md)수 있습니다.
- 새 부서의 범위 계정을 만듭니다. [빠른 시작 가이드에 따라 새로 만들](create-catalog-portal.md)수 있습니다.
- 아래 나열 된 지역 중 하나에 새 Azure Storage 계정을 만듭니다. [이 가이드에 따라 하나를 만들](../storage/common/storage-account-create.md)수 있습니다. Storage 계정 버전 >= 81. x. x만 정책 적용을 지원 합니다.

[!INCLUDE [supported regions](./includes/storage-access-policy-regions.md)]

## <a name="configuration"></a>구성
[!INCLUDE [access policy enablement storage](./includes/storage-access-policy-enable.md)]

### <a name="opt-in-to-participate-in-azure-purview-data-access-policy-preview"></a>Azure 부서의 범위 data access 정책 미리 보기에 참여 하도록 옵트인
이 기능은 현재 미리 보기 상태입니다. [부서의 범위 data access 정책 미리 보기를 옵트인 (opt in)](https://aka.ms/opt-in-data-use-policy) 해야 합니다.

### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>다른 구독의 리소스 공급자로 부서의 범위을 등록 합니다.
Storage 및 부서의 범위 계정이 서로 다른 구독에 있는 경우에만이 단계를 실행 합니다. [azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 가이드에 따라 Azure Storage 계정에 대 한 구독에 azure 부서의 범위를 리소스 공급자로 등록 합니다.

### <a name="configure-permissions-for-policy-management-actions"></a>정책 관리 작업에 대 한 사용 권한 구성
#### <a name="storage-account-permissions"></a>스토리지 계정 권한
사용자는 Azure 부서의 범위에서 *데이터 사용* 을 위해 등록할 수 있도록 Azure Storage 계정에 이러한 역할 조합 중 **하나가** 있어야 합니다.
- IAM *소유자* 
- IAM *참여자* + Iam *사용자 액세스 관리자*
 
이 [가이드를 따라 AZURE RBAC 사용 권한을 구성할](../role-based-access-control/check-access.md) 수 있습니다.

#### <a name="purview-account-permissions"></a>부서의 범위 계정 사용 권한
>[!IMPORTANT]
> - 정책 작업은 **루트 컬렉션** 수준 에서만 지원 되며 자식 컬렉션 수준에서는 지원 되지 않습니다.
- 사용자가 루트 컬렉션 수준에서 *데이터 원본 관리자* 역할을 부서의 범위 해야 합니다.
  - 데이터의 원본을 등록 *하 여 거 버 넌 스를 사용* 합니다.
  - 정책 게시
- 사용자가 루트 컬렉션 수준에서 *정책 작성자* 역할을 부서의 범위 정책을 만들거나 편집 해야 합니다.

이 [가이드](how-to-create-and-manage-collections.md)의 부서의 범위 역할 할당 관리에 대 한 섹션을 확인 하세요.

>[!WARNING]
> 사용 권한과 관련 된 **알려진 문제**
> - 부서의 범위 *정책 작성자* 역할 외에도 사용자는 Azure Active Directory (AAD)에서 *디렉터리 판독기* 권한이 있어야 데이터 소유자 정책을 만들 수 있습니다. AAD 디렉터리 판독기에 대 한 자세한 내용은 [여기를 클릭](../active-directory/roles/permissions-reference.md#directory-readers) 하세요.
> - 부서의 범위 *정책 작성자* 역할은 정책을 만들기에 충분 하지 않습니다. 또한 부서의 범위 *데이터 원본 관리자* 역할도 필요 합니다.

### <a name="register-and-scan-data-sources-in-purview"></a>부서의 범위에서 데이터 원본 등록 및 검색
부서의 범위를 사용 하 여 각 데이터 원본을 등록 하 고 검색 하 여 나중에 액세스 정책을 정의 합니다. 다음 가이드를 따르면 됩니다.

-   [Azure Storage Blob 등록 및 검색-Azure 부서의 범위](register-scan-azure-blob-storage-source.md)

-   [Azure Data Lake Storage 등록 및 검사 (ADLS) Gen2-Azure 부서의 범위](register-scan-adls-gen2.md)

>[!NOTE]
> 등록 하는 동안 원본에 제공 하는 **이름을** 적어 둡니다. 정책 설명을 만들거나 게시할 때 필요 합니다. Storage 계정에 사용한 것과 동일한 이름을 지정 하는 것이 가장 좋습니다.

등록 하는 동안 그림에 표시 된 것 처럼 데이터를 **사용** 하 여 데이터 원본에 대 한 액세스 정책을 사용 하도록 설정 합니다.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="이미지는 정책에 대 한 데이터 원본을 등록 하는 방법을 보여 줍니다.":::

>[!IMPORTANT]
> - 사용자가 *데이터 사용* 을 위한 원본을 등록 하려면 Azure Storage *Owner* 및 부서의 범위 *데이터 원본 관리자* 가 모두 있어야 하지만 해당 역할은 개별적으로 등록을 취소할 수 있습니다.
> - *데이터 사용* 에 대 한 구독을 사용 하지 않도록 설정 하면 해당 구독에 등록 된 모든 자산에 대해 사용 하지 않도록 설정 됩니다.


### <a name="data-use-governance-best-practices"></a>데이터 사용 관리 모범 사례
- *데이터를 사용* 하는 모든 데이터 원본을 등록 하 고 단일 Azure 부서의 범위 계정에서 관련 된 모든 액세스 정책을 관리 하는 것이 좋습니다.
- 그러나 여러 부서의 범위 계정이 있어야 하는 경우에는 구독에 속하는 모든 데이터 원본이 단일 부서의 범위 계정에서 *데이터 사용 관리* 에 대해서만 등록할 수 있습니다. 해당 부서의 범위 계정 자체는 테 넌 트의 모든 구독에 있을 수 있습니다. 잘못 된 구성이 있으면 *데이터 사용 거 버 넌 스* 전환 설정이 회색으로 표시 됩니다. 몇 가지 예제는 아래 다이어그램에 나와 있습니다.
    - **사례 1** 은 Storage 계정이 동일한 구독의 부서의 범위 계정에 등록 되는 올바른 구성을 보여 줍니다.
    - **사례 2** 는 Storage 계정이 다른 구독의 부서의 범위 계정에 등록 되는 올바른 구성을 보여 줍니다. 
    - **사례 3** 은 S3SA1 및 S3SA2 계정이 모두 구독 3에 속하지만 다른 부서의 범위 계정에 등록 Storage 되기 때문에 잘못 된 구성을 보여 줍니다. 

:::image type="content" source="./media/how-to-access-policies-storage/valid-and-invalid configurations.png" alt-text="여러 부서의 범위 계정을 사용 하 여 정책을 관리 하는 경우 유효한 구성과 유효 하지 않은 구성이 다이어그램에 표시 됩니다.":::

## <a name="policy-authoring"></a>정책 작성

이 섹션에서는 부서의 범위 액세스 정책을 만들고, 업데이트 하 고, 게시 하는 단계에 대해 설명 합니다.

### <a name="create-a-new-policy"></a>새 정책 만들기

이 섹션에서는 Azure 부서의 범위에서 새 정책을 만드는 단계에 대해 설명 합니다.

1. 부서의 범위 포털에 로그인 합니다.

1. 왼쪽 패널을 사용 하 여 **정책 관리** 앱으로 이동 합니다.

1. 정책 페이지에서 **새 정책** 단추를 선택 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-1.png" alt-text="이미지는 정책을 만들려는 경우 데이터 소유자가 Azure 부서의 범위의 정책 기능에 액세스 하는 방법을 보여 줍니다.":::

1. 새 정책 페이지가 표시 됩니다. 정책 **이름** 및 **설명** 을 입력 합니다.

1. 새 정책에 정책 문을 추가 하려면 **새 정책 문** 단추를 선택 합니다. 그러면 정책 문 작성기가 표시 됩니다.

    :::image type="content" source="./media/how-to-access-policies-storage/create-new-policy-storage.png" alt-text="이미지는 데이터 소유자가 새 정책 문을 만드는 방법을 보여 줍니다.":::

1. **작업** 단추를 선택 하 고 드롭다운 목록에서 읽기 또는 수정을 선택 합니다.

1. **효과** 단추를 선택 하 고 드롭다운 목록에서 허용을 선택 합니다.

1. 데이터 **리소스** 단추를 선택 하 여 데이터 자산 경로를 제공 하는 옵션을 표시 합니다.

1. **자산** 상자에서 **데이터 원본 유형을** 입력 하 고 이전에 등록 된 데이터 원본의 **이름을** 선택 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-source-type-storage.png" alt-text="이미지 정책 문을 편집할 때 데이터 소유자가 데이터 리소스를 선택할 수 있는 방법을 보여 줍니다.":::

1. **계속** 단추를 선택 하 고 계층에 가로를 선택 하 여 폴더 또는 파일을 선택 합니다. 그런 다음 **추가** 단추를 선택 합니다. 그러면 정책 편집기로 다시 이동 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="이미지는 정책 문을 만들거나 편집할 때 데이터 소유자가 자산을 선택 하는 방법을 보여 줍니다.":::

1. **제목 단추를** 선택 하 고 주체 id를 보안 주체, 그룹 또는 MSI로 입력 합니다. 그런 다음, **확인** 단추를 선택합니다. 그러면 정책 편집기로 다시 이동 합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="이미지는 정책 문을 만들거나 편집할 때 데이터 소유자가 주제를 선택할 수 있는 방법을 보여 줍니다.":::

1. #11 #5 단계를 반복 하 여 더 많은 정책 문을 입력 합니다.

1. **저장** 단추를 선택 하 여 정책을 저장 합니다.

> [!NOTE]
> 컨테이너 수준 아래 ADLS Gen2 폴더 및 파일에 대 한 정책 문이 지원 됩니다. POSIX 모델을 따릅니다. 즉, Storage 계정의 데이터 개체에 대 한 액세스를 실행 하는 앱은 다음 중 하나가 필요 합니다.
> - 이 [예](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md#access-files-from-the-cluster) 에 표시 된 것 처럼 데이터 개체에 대 한 정규화 된 이름 (즉, 직접 절대 경로)을 Storage 계정에 제공 하도록 지원 합니다.
> - 앱에서 데이터 개체를 탐색 해야 하는 경우 앱에서 사용 하는 id (예: 사용자 또는 서비스 주체)는 부모 컨테이너와 부모 폴더를 나열할 수 있는 권한도 필요 합니다.


> [!WARNING]
> 정책 생성과 관련 된 **알려진 문제**
> - Purview 리소스 집합을 기반으로 정책 문을 만들지 마십시오. Purview 정책 작성 UI에 표시되더라도 아직 적용되지 않습니다. 리소스 집합의 개념에 대한 설명을 보려면 [여기를 클릭하세요.](concept-resource-sets.md)
> - 컨테이너 수준 아래의 데이터 개체를 선택하는 Blob Storage에 대한 정책 문을 만들지 마십시오. Blob Storage는 컨테이너 수준에서만 액세스 제어를 제공하므로 해당 정책이 적용되지 않습니다.
> - *데이터 사용 거버넌스에* 대해 구독을 사용하지 않도록 설정하면 *데이터 사용* 거버넌스에 사용하도록 설정된 모든 기본 자산이 비활성화되며 이는 올바른 동작입니다. 그러나 해당 자산을 기반으로 하는 정책 문은 그 후에도 계속 허용됩니다.

### <a name="update-or-delete-a-policy"></a>정책 업데이트 또는 삭제

Purview에서 새 정책을 만드는 단계는 다음과 같습니다.

1. Purview 포털에 로그인합니다.

1. 왼쪽 패널을 사용하여 Purview 정책 앱으로 이동합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="이미지는 데이터 소유자가 정책을 업데이트하려고 할 때 Azure Purview의 정책 기능에 액세스할 수 있는 방법을 보여줍니다.":::

1. 정책 포털은 Purview에 기존 정책 목록을 표시합니다. 업데이트해야 하는 정책을 선택합니다.

1. 편집 및 삭제 옵션을 포함한 정책 세부 정보 페이지가 표시됩니다. **편집** 단추를 선택하면 이 정책의 문에 대한 정책 문 작성기 가 표시됩니다. 이제 이 정책의 모든 문 부분을 업데이트할 수 있습니다. 정책을 삭제하려면 **삭제** 단추를 사용합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/edit-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책 문을 편집하거나 삭제하는 방법을 보여줍니다.":::

### <a name="publish-the-policy"></a>정책 게시

새로 만든 정책은 초안 상태입니다. 게시 프로세스는 거버넌스에서 하나 이상의 데이터 원본과 새 정책을 연결합니다.
정책을 게시하는 단계는 다음과 같습니다.

1. Purview 포털에 로그인합니다.

1. 왼쪽 패널을 사용하여 Purview 정책 앱으로 이동합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="이미지는 데이터 소유자가 정책을 게시하려고 할 때 Azure Purview의 정책 기능에 액세스할 수 있는 방법을 보여줍니다.":::

1. 정책 포털은 Purview에 기존 정책 목록을 표시합니다. 게시해야 하는 정책을 찾습니다. 페이지의 오른쪽 위 모서리에서 **게시** 단추를 선택합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책을 게시하는 방법을 보여줍니다.":::

1. 데이터 원본 목록이 표시됩니다. 목록을 필터링하는 이름을 입력할 수 있습니다. 그런 다음, 이 정책을 게시할 각 데이터 원본을 선택한 **다음, 게시** 단추를 선택합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책을 게시할 데이터 원본을 선택하는 방법을 보여줍니다.":::

>[!NOTE]
> 게시는 백그라운드 작업입니다. 변경 내용이 데이터 원본에 반영되는 데 최대 **2시간이** 걸릴 수 있습니다.

## <a name="policy-action-mapping"></a>정책 작업 매핑

이 섹션에는 Azure Purview 데이터 정책의 작업이 Azure Storage 특정 작업에 매핑하는 방법에 대한 참조가 포함되어 있습니다.

| **Purview 정책 작업** | **데이터 원본 관련 작업**                                                        |
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
이 방법 가이드에 언급된 기능과 관련된 블로그 및 데모를 확인합니다.

* [Microsoft Ignite 2021의 Azure Purview의 새로운 내용](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Azure Storage 대한 액세스 정책 데모](https://www.youtube.com/watch?v=CFE8ltT19Ss)
