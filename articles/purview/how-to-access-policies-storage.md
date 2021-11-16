---
title: Azure Storage에 대 한 데이터 액세스 정책 프로 비전
description: Azure 부서의 범위 액세스 정책에 Azure Storage를 통합 하는 방법에 대 한 단계별 가이드를 통해 데이터 소유자가 액세스 정책을 작성할 수 있습니다.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/15/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: eab2c720aafe2cfd5a1ca46f2549b42d6f644b25
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555720"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>데이터 소유자가 Azure Storage에 대 한 데이터 집합 프로 비전

## <a name="supported-capabilities"></a>지원되는 기능
이 가이드에서는 Azure 부서의 범위에서 만들고 관리 하는 데이터 액세스 정책을 적용 하도록 Azure Storage를 구성 하는 방법을 설명 합니다. Azure 부서의 범위 정책 작성은 다음과 같은 기능을 지원 합니다.
-   Blob 또는 Azure Data Lake Storage (ADLS) Gen2에 저장 된 데이터에 대 한 액세스를 제어 하기 위한 데이터 액세스 정책

> [!IMPORTANT]
> 이러한 기능은 현재 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 작업에 사용 하면 안 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="important-limitations"></a>중요 한 제한 사항
1. 액세스 정책 기능은 새 Azure 부서의 범위 및 Azure Storage 계정 에서만 사용할 수 있습니다.
2. 모든 데이터 원본을 등록 하 여 거 버 넌 스를 사용 하 고 단일 Azure 부서의 범위 계정에서 관련 된 모든 액세스 정책을 관리 합니다.
3. 이 기능은 아래 나열 된 지역 에서만 사용할 수 있습니다. 여기에는 액세스 정책 관리 및 적용 기능이 배포 되어 있습니다.

### <a name="supported-regions"></a>지원되는 지역

#### <a name="azure-purview-management-side"></a>Azure 부서의 범위 (관리 측)
-   북유럽
-   서유럽
-   영국 남부
-   미국 동부
-   미국 동부2
-   미국 중남부
-   미국 서부 2
-   동남아시아
-   오스트레일리아 동부
-   캐나다 중부
-   프랑스 중부

#### <a name="azure-storage-enforcement-side"></a>Azure Storage (적용 쪽)
-   프랑스 중부
-   캐나다 중부

## <a name="prerequisites"></a>필수 조건
> [!Important]
> 이 섹션을 자세히 읽어 보세요. 액세스 정책이 제대로 작동 하려면 여러 필수 구성 요소가 필요 합니다.

### <a name="select-an-isolated-test-subscription"></a>격리 된 테스트 구독 선택
격리 된 테스트 구독을 만들거나 사용 하 고 아래 단계에 따라 새 Azure Storage 계정 및 해당 구독에 새 Azure 부서의 범위 계정을 만듭니다.

### <a name="create-azure-storage-account"></a>Azure Storage 계정 만들기
제한 사항 아래에 나열 된 지역에 새 Azure Storage 계정을 만듭니다. [저장소 계정 만들기-Azure Storage](../storage/common/storage-account-create.md) 를 참조 하세요.

### <a name="configure-azure-storage-to-enforce-access-policies-from-purview"></a>부서의 범위에서 액세스 정책을 적용 하도록 Azure Storage 구성

#### <a name="enable-access-policy-enforcement-in-the-subscription"></a>구독에서 액세스 정책 적용 사용
Azure Storage 계정이 있는 구독에서 액세스 정책 기능이 사용 하도록 설정 되었는지 등록 하 고 확인 하려면 PowerShell에서 다음 명령을 실행 합니다.

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```
마지막 명령의 출력에 "RegistrationState"의 값이 "등록 됨"으로 표시 되는 경우이 기능에 대해 구독을 사용할 수 있습니다.

#### <a name="check-access-permissions-in-azure-storage"></a>Azure Storage에서 액세스 권한 확인
사용자가 나중에 액세스 정책에 대 한 azure 부서의 범위에이 데이터 원본을 등록 하려면 사용자에 게 azure 리소스에 대 한 [사용자 액세스 권한 확인](../role-based-access-control/check-access.md) 을 사용 하 여 Azure Storage 계정에 "소유자" 역할이 있어야 합니다.

### <a name="create-azure-purview-account"></a>Azure 부서의 범위 계정 만들기
새 기능이 활성화 된 지역에서 격리 된 테스트 구독으로 새 Azure 부서의 범위 계정을 만듭니다. 새 부서의 범위 계정을 만들려면  [빠른 시작: Azure Portal에서 Azure 부서의 범위 계정 만들기를 참조 하세요.](create-catalog-portal.md)

### <a name="configure-azure-purview-to-manage-access-policies"></a>액세스 정책을 관리 하도록 Azure 부서의 범위 구성
Azure 부서의 범위에서 액세스 정책을 관리 하도록 설정 하려면 다음 단계를 모두 완료 합니다. 

#### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>옵트인 (Opt in) Azure 부서의 범위 data 사용 정책 미리 보기
이 기능은 현재 미리 보기 상태 이므로 [부서의 범위 data 사용 정책 미리 보기를 옵트인 (Opt in](https://aka.ms/opt-in-data-use-policy) ) 해야 합니다.

#### <a name="register-purview-as-a-resource-provider-in-other-subscriptions"></a>다른 구독의 리소스 공급자로 부서의 범위을 등록 합니다.
액세스를 관리 하려는 Storage 계정이 Azure 부서의 범위 계정이 아닌 다른 구독에 있는 경우에만이 단계를 실행 합니다. 이 가이드에 따라 Azure 부서의 범위을 해당 구독의 리소스 공급자로 등록 합니다.  
[Azure 리소스 공급자 및 종류](../azure-resource-manager/management/resource-providers-and-types.md)

#### <a name="configure-permissions-for-policy-management-actions"></a>정책 관리 작업에 대 한 사용 권한 구성
- 데이터 사용을 위한 원본을 등록 하려면 사용자는 데이터 원본 소유자 이자 부서의 범위 데이터 원본 관리자 여야 합니다. 그러나 이러한 역할은 독립적으로 데이터 사용을 위한 원본 등록을 취소할 수 있습니다.
- 정책 작성/관리 작업을 수행 하려면 사용자가 루트 컬렉션 수준에서 부서의 범위 Policy Author 역할의 일부 여야 합니다.
- 정책을 게시 하려면 사용자가 루트 컬렉션 수준에서 부서의 범위 데이터 원본 관리자 역할의 일부 여야 합니다.

이 가이드의 역할 할당 관리에 대 한 섹션을 참조 하세요. [컬렉션을 만들고 관리 하는 방법](how-to-create-and-manage-collections.md)

또한이 문서 아래쪽의 "알려진 문제" 섹션을 참조 하세요.

#### <a name="register-and-scan-data-sources-in-purview"></a>부서의 범위에서 데이터 원본 등록 및 검색
부서의 범위를 사용 하 여 각 데이터 원본을 등록 하 고 검색 하 여 나중에 액세스 정책을 정의 합니다. 부서의 범위 등록 가이드에 따라 저장소 계정을 등록 합니다.

-   [Azure Storage Blob 등록 및 검색-Azure 부서의 범위](register-scan-azure-blob-storage-source.md)

-   [Azure Data Lake Storage 등록 및 검사 (ADLS) Gen2-Azure 부서의 범위](register-scan-adls-gen2.md)

등록 하는 동안 그림에 표시 된 것 처럼 데이터를 **사용** 하 여 데이터 원본에 대 한 액세스 정책을 사용 하도록 설정 합니다.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="이미지는 정책에 대 한 데이터 원본을 등록 하는 방법을 보여 줍니다.":::

> [!NOTE]
> 설정/해제의 동작으로 인해 지정 된 구독의 모든 데이터 원본이 단일 부서의 범위 계정에 있는 데이터 사용에 대해서만 등록 될 수 있습니다. 해당 부서의 범위 계정 자체는 테 넌 트의 모든 구독에 있을 수 있습니다.

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

## <a name="azure-purview-policy-action-to-azure-storage-action-mapping"></a>Azure Storage 작업 매핑을 위한 Azure 부서의 범위 정책 작업

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

## <a name="known-issues"></a>알려진 문제
현재 릴리스의 알려진 문제입니다.
1. 정책 작성자 역할 외에도 사용자는 Azure Active Directory (AAD)에서 디렉터리 판독기 권한이 있어야 데이터 소유자 정책을 만들 수 있습니다.
1. 정책 작성자 역할은 정책을 만들기에 충분 하지 않습니다. 또한 부서의 범위 데이터 원본 관리자 역할도 필요 합니다.

## <a name="next-steps"></a>다음 단계
이 방법 가이드에 설명 된 기능과 관련 된 블로그 및 데모를 확인 합니다.

* [Microsoft Ignite 2021에서 제공 되는 Azure 부서의 범위의 새로운 기능](https://techcommunity.microsoft.com/t5/azure-purview/what-s-new-in-azure-purview-at-microsoft-ignite-2021/ba-p/2915954)
* [Azure Storage에 대 한 액세스 정책 데모](https://www.youtube.com/watch?v=CFE8ltT19Ss)
