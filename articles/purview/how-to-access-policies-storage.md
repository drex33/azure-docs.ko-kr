---
title: Azure Storage 대한 데이터 액세스 정책 프로비저닝
description: Azure Purview 액세스 정책에 Azure Storage 통합하고 데이터 소유자가 액세스 정책을 빌드할 수 있도록 하는 방법에 대한 단계별 가이드입니다.
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: ea5285c5fd29bfe34f97c87b2ac0c9bd7a5502a9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425238"
---
# <a name="dataset-provisioning-by-data-owner-for-azure-storage"></a>Azure Storage 대한 데이터 소유자의 데이터 세트 프로비저닝

## <a name="supported-capabilities"></a>지원되는 기능

Purview 정책 작성은 다음 기능을 지원합니다.
-   Blob 또는 ADLS Gen2 파일에 저장된 데이터에 대한 액세스를 제어하는 Azure Storage 대한 데이터 액세스 정책

> [!IMPORTANT]
> 이러한 기능은 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에 사용하면 안 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.



## <a name="prerequisites"></a>사전 요구 사항

### <a name="opt-in-to-participate-in-azure-purview-data-use-policy-preview"></a>Azure Purview 데이터 사용 정책 미리 보기에 참여하도록 옵트인
이 기능은 현재 미리 보기 상태이므로 [Purview 데이터 사용 정책 미리 보기 에 옵트인해야](https://aka.ms/opt-in-data-use-policy)합니다.

### <a name="provision-new-accounts-in-an-isolated-test-subscription"></a>격리된 테스트 구독에서 새 계정 프로비전
아래 단계에 따라 격리된 테스트 구독에서 새 Azure Purview 계정 및 새 Azure Storage 계정을 만듭니다. 그런 다음, 이러한 계정에서 액세스 정책 기능을 사용하도록 설정합니다.

### <a name="supported-regions"></a>지원되는 지역

> [!IMPORTANT]
> 1. 액세스 정책 기능은 새 Azure Purview 및 Azure Storage 계정에서만 사용할 수 있습니다.
> 2. 이 기능은 액세스 정책 기능이 배포되는 아래 나열된 지역에서만 사용할 수 있습니다.

#### <a name="azure-purview"></a>Azure Purview 

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


#### <a name="azure-storage"></a>Azure Storage

-   프랑스 중부
-   캐나다 중부


### <a name="create-azure-purview-account"></a>Azure Purview 계정 만들기

새 기능을 사용하도록 설정된 지역에서 새 기능에 대해 격리된 구독에서 새 Azure Purview 계정을 만듭니다.

새 Purview 계정을 만들려면  [빠른 시작: Azure Portal Azure Purview 계정 만들기를 참조하세요.](create-catalog-portal.md)

### <a name="create-azure-storage-account"></a>Azure Storage 계정 만들기

새 Azure Storage 계정을 만들려면 [스토리지 계정 만들기 - Azure Storage](../storage/common/storage-account-create.md)

### <a name="configure-azure-purview-and-storage-for-access-policies"></a>액세스 정책에 대한 Azure Purview 및 Storage 구성

이 섹션에서는 액세스 정책을 사용하도록 Azure Purview 및 Storage 구성하는 단계를 간략하게 설명합니다.

#### <a name="register-the-access-policies-functionality-in-azure-storage"></a>Azure Storage 액세스 정책 기능 등록

구독에 대해 이 기능이 사용하도록 설정되어 있는지 등록하고 확인하려면 PowerShell에서 다음 명령을 실행합니다.

```powershell
# Install the Az module
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
# Login into the subscription
Connect-AzAccount -Subscription <SubscriptionID>
# Register the feature
Register-AzProviderFeature -FeatureName AllowPurviewPolicyEnforcement -ProviderNamespace Microsoft.Storage
```

마지막 명령의 출력에 "RegistrationState" 값이 "Registered"로 표시되면 이 기능에 대해 구독이 활성화됩니다.

#### <a name="register-and-scan-data-sources-in-purview"></a>Purview에서 데이터 원본 등록 및 검사

정책을 정의하려면 Purview를 사용하여 데이터 원본을 등록하고 검사해야 합니다. Purview 등록 가이드에 따라 스토리지 계정을 등록합니다.

-   [Azure Storage Blob을 검사하는 방법 - Azure Purview](register-scan-azure-blob-storage-source.md)

-   [ADLS(Azure Data Lake Storage) Gen2 등록 및 검사 - Azure Purview](register-scan-adls-gen2.md)

등록하는 동안 그림에 표시된 대로 데이터 원본에서 데이터 사용 거버넌스를 사용하도록 설정합니다.

:::image type="content" source="./media/how-to-access-policies-storage/register-data-source-for-policy.png" alt-text="이미지는 정책에 대한 데이터 원본을 등록하는 방법을 보여줍니다.":::

#### <a name="configure-permissions-for-policy-management-actions"></a>정책 관리 작업에 대한 권한 구성

-   정책 작성/관리 작업을 수행하려면 사용자가 Purview 데이터 권한자 역할의 일부여야 합니다.
-   정책을 게시하려면 사용자가 Purview 데이터 원본 관리자 역할의 일부여야 합니다.

이 가이드의 역할 할당 관리에 대한 섹션을 [참조하세요. 컬렉션을 만들고 관리하는 방법](how-to-create-and-manage-collections.md)

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

1. **계속** 단추를 선택하고 계층을 가로로 전환하여 폴더 또는 파일을 선택합니다. 그런 **다음, 추가** 단추를 선택합니다. 그러면 정책 편집기로 돌아갑니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-asset-storage.png" alt-text="이미지는 정책 문을 만들거나 편집할 때 데이터 소유자가 자산을 선택하는 방법을 보여줍니다.":::

1. **주체** 단추를 선택하고 주체 ID를 보안 주체, 그룹 또는 MSI로 입력합니다. 그런 다음, **확인** 단추를 선택합니다. 그러면 정책 편집기로 돌아갑니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-subject.png" alt-text="이미지는 정책 문을 만들거나 편집할 때 데이터 소유자가 제목을 선택하는 방법을 보여줍니다.":::

1. #5~#11 단계를 반복하여 정책 문을 더 입력합니다.

1. **저장** 단추를 선택하여 정책을 저장합니다.

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

    :::image type="content" source="./media/how-to-access-policies-storage/policy-onboard-guide-2.png" alt-text="이미지는 데이터 소유자가 정책을 게시하려고 할 때 Azure Purview의 정책 기능에 액세스하는 방법을 보여줍니다.":::

1. 정책 포털은 Purview에 기존 정책 목록을 표시합니다. 게시해야 하는 정책을 찾습니다. 페이지의 오른쪽 위 모서리에서 **게시** 단추를 선택합니다.

    :::image type="content" source="./media/how-to-access-policies-storage/publish-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책을 게시하는 방법을 보여줍니다.":::

1. 데이터 원본 목록이 표시됩니다. 목록을 필터링하는 이름을 입력할 수 있습니다. 그런 다음, 이 정책을 게시할 각 데이터 원본을 선택한 **다음, 게시** 단추를 선택합니다. 게시는 백그라운드 작업입니다. 변경 내용이 데이터 원본에 반영되는 데 최대 2시간이 걸릴 수 있습니다.

    :::image type="content" source="./media/how-to-access-policies-storage/select-data-sources-publish-policy-storage.png" alt-text="이미지는 데이터 소유자가 정책을 게시할 데이터 원본을 선택하는 방법을 보여줍니다.":::

## <a name="next-steps"></a>다음 단계

Azure Purview와 관련된 개념을 이해하려면 이 문서를 확인하세요.

* [Azure Purview 개요](overview.md)
