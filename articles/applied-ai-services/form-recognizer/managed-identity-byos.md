---
title: BYOS(사용자 고유의 스토리지 가져오기)로 관리 ID 만들기 및 사용
titleSuffix: Azure Applied AI Services
description: BYOS 계정으로 관리 ID를 만들고 사용하는 방법 이해
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 10/07/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: e1ccf10041c36b26b70bf5c4e5a107c0f6bf19ac
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021622"
---
# <a name="create-and-use-managed-identity-for-your-form-recognizer-resource"></a>Form Recognizer 리소스의 관리 ID 만들기 및 사용

> [!IMPORTANT]
> Azure RBAC(Azure 역할 기반 액세스 제어) 할당은 현재 미리 보기로 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. Azure RBAC 할당은 관리 ID에 대한 권한을 부여하는 데 사용됩니다.

## <a name="what-is-managed-identity"></a>관리 ID란?

Azure 관리 ID는 Azure 관리되는 리소스에 대한 Azure AD(Azure Active Directory) ID와 특정 권한을 만드는 서비스 주체입니다. 관리 ID를 사용하여 Azure AD 인증을 지원하는 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다. 액세스 권한을 부여하려면 Azure RBAC([Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md))를 사용하여 관리 ID에 역할을 할당합니다.  Azure에서 관리 ID를 사용하기 위해 추가되는 비용은 없습니다.

관리 ID는 프라이빗 및 퍼블릭 액세스가 가능한 Azure Blob Storage 계정을 둘 다 지원합니다.  퍼블릭 액세스 권한이 있는 스토리지 계정의 경우 SAS(공유 액세스 서명)를 사용하여 제한된 액세스 권한을 부여하도록 선택할 수 있습니다.   이 문서에서는 Form Recognizer 인스턴스에 대해 시스템 할당 관리 ID를 사용하도록 설정하는 방법을 알아봅니다.

## <a name="private-storage-account-access"></a>프라이빗 스토리지 계정 액세스

 프라이빗 Azure Storage 계정 액세스 및 인증은 [Azure 리소스용 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에서 지원됩니다. VNet(가상 네트워크) 또는 방화벽으로 보호되는 Azure Storage 계정이 있거나 BYOS(사용자 고유의 스토리지 가져오기)를 사용하도록 설정한 경우, Form Recognizer는 스토리지 계정 데이터에 직접 액세스할 수 없습니다. 그러나 관리 ID가 사용하도록 설정되면 Form Recognizer 서비스는 할당된 관리 ID 자격 증명을 사용하여 스토리지 계정에 액세스할 수 있습니다.

> [!NOTE]
>
> * [**FOTT (양식 인식기 샘플 레이블 도구)**](https://fott-2-1.azurewebsites.net/)를 사용 하 여 저장소 데이터를 분석 하려는 경우 VNet 또는 방화벽 뒤에 도구를 배포 해야 합니다.
>
> * 분석 [**수신**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync), [**비즈니스 카드**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync), [**송장**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291), [**ID 문서**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)및 [**사용자 지정 양식**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) api는 요청을 원시 이진 콘텐츠로 게시 하 여 단일 문서에서 데이터를 추출할 수 있습니다. 이러한 시나리오에서는 관리 ID 자격 증명에 대한 요구 사항이 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/) - 계정이 없는 경우 [**체험 계정을 만들**](https://azure.microsoft.com/free/) 수 있습니다.

* Azure Portal의 [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 또는 [**Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 리소스 자세한 단계는 [Azure Portal을 사용하여 Cognitive Services 리소스 만들기](../../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)를 _참조_ 하세요.

* 양식 인식기 리소스와 동일한 지역에 있는 [**Azure blob storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) . 스토리지 계정 내에서 Blob 데이터를 저장하고 구성하는 컨테이너를 만듭니다. 

  * 저장소 계정이 방화벽 뒤에 있는 경우 **다음 구성을 사용 하도록 설정 해야 합니다**. </br></br>

  * 저장소 계정 페이지의 왼쪽 메뉴에서 **보안 + 네트워킹** → **네트워킹** 을 선택 합니다.
    :::image type="content" source="media/managed-identities/security-and-networking-node.png" alt-text="스크린샷: 보안 + 네트워킹 탭":::

  * 주 창에서 **선택한 네트워크에서 액세스 허용** 을 선택 합니다.
  :::image type="content" source="media/managed-identities/firewalls-and-virtual-networks.png" alt-text="스크린샷: 선택한 네트워크 라디오 단추를 선택 합니다.":::

  * 선택한 네트워크 페이지에서 **예외** 범주로 이동 하 고  [**신뢰할 수 있는 서비스 목록에 대 한 Azure 서비스에서이 저장소 계정에 액세스 하도록 허용**](../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) 확인란을 사용 하도록 설정 합니다.

    :::image type="content" source="media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="스크린샷: 신뢰할 수 있는 서비스 허용 확인란, 포털 보기":::
* Azure Portal을 사용하여 [**Azure RBAC(Azure 역할 기반 액세스 제어)**](../../role-based-access-control/role-assignments-portal.md)를 간략히 이해합니다.

## <a name="managed-identity-assignments"></a>관리 ID 할당

관리 ID에는 **시스템 할당** 및 **사용자 할당** 의 두 가지 유형이 있습니다. 현재 Form Recognizer는 시스템 할당 관리 ID에서 지원됩니다. 시스템 할당 관리 ID는 서비스 인스턴스에서 직접 **사용하도록 설정** 됩니다. 기본적으로는 사용하도록 설정되어 있지 않습니다. 리소스로 이동하여 ID 설정을 업데이트해야 합니다. 시스템 할당 관리 ID는 수명 주기 내내 리소스에 연결됩니다. 리소스를 삭제하면 관리 ID도 삭제됩니다.

다음 단계에서는 시스템 할당 관리 ID를 사용하도록 설정하고 Azure Blob Storage 계정에 대한 제한된 액세스 권한을 Form Recognizer에 부여합니다.

## <a name="enable-a-system-assigned-managed-identity"></a>시스템 할당 관리 ID 사용

>[!IMPORTANT]
>
> 시스템 할당 관리 ID를 사용하려면 [**소유자**](../../role-based-access-control/built-in-roles.md#owner) 또는 [**사용자 액세스 관리자**](../../role-based-access-control/built-in-roles.md#user-access-administrator)와 같은 **Microsoft.Authorization/roleAssignments/write** 권한이 필요합니다. 4개 수준인 관리 그룹, 구독, 리소스 그룹 또는 리소스에서 범위를 지정할 수 있습니다.

1. Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **Form Recognizer** 리소스 페이지로 이동합니다.

1. 왼쪽 레일의 **리소스 관리** 목록에서 **ID** 를 선택합니다.

    :::image type="content" source="media/managed-identities/resource-management-identity-tab.png" alt-text="스크린샷: Azure Portal의 리소스 관리 ID 탭":::

1. 기본 창에서 **시스템 할당 상태** 탭을 **켜기** 로 전환합니다.

1. **권한** 에서 **Azure 역할 할당** 을 선택합니다.

    :::image type="content" source="media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="스크린샷: Azure Portal에서 시스템 할당 관리 ID 사용":::

1. Azure 역할 할당 페이지가 열립니다. 드롭다운 메뉴에서 구독을 선택한 다음, **&plus;역할 할당 추가** 를 선택합니다.

    :::image type="content" source="media/managed-identities/azure-role-assignments-page-portal.png" alt-text="스크린샷: Azure Portal의 Azure 역할 할당 페이지":::

    > [!NOTE]
    >
    > 추가 > 역할 할당 추가 옵션이 사용되지 않기 때문에 Azure Portal에서 역할을 할당할 수 없거나 "이 범위에서 역할 할당을 추가할 권한이 없습니다" 권한 오류가 표시되는 경우 현재 스토리지 리소스의 스토리지 범위에서 소유자 또는 사용자 액세스 관리자와 같은 Microsoft.Authorization/roleAssignments/write 권한이 있는 할당된 역할을 가진 사용자로 로그인되어 있는지 확인합니다.

 7. 다음으로, **Storage Blob 데이터 읽기 권한자** 역할을 Form Recognizer 서비스 리소스에 할당합니다. **역할 할당 추가** 팝업 창에서 다음과 같이 필드를 완료하고 **저장** 을 선택합니다.

    | 필드 | 값|
    |------|--------|
    |**범위**| **_Storage_**|
    |**구독**| **_스토리지 리소스와 연결된 구독_** 입니다.|
    |**리소스**| **_스토리지 리소스의 이름_** 입니다.|
    |**역할** | **_Storage Blob 데이터 읽기 권한자_** - Azure Storage Blob 컨테이너 및 데이터에 대한 읽기 액세스를 허용합니다.|

     :::image type="content" source="media/managed-identities/add-role-assignment-window.png" alt-text="스크린샷: Azure Portal의 역할 할당 추가 페이지":::

1. _추가된 역할 할당_ 확인 메시지가 표시된 후 페이지를 새로 고쳐 추가된 역할 할당을 확인합니다.

    :::image type="content" source="media/managed-identities/add-role-assignment-confirmation.png" alt-text="스크린샷: 추가된 역할 할당 확인 팝업 메시지":::

1. 변경이 즉시 표시되지 않으면 기다렸다가 페이지를 한 번 더 새로 고쳐 보세요. 역할 할당을 할당하거나 제거하는 경우 변경 내용이 적용되는 데 최대 30분이 걸릴 수 있습니다.

    :::image type="content" source="media/managed-identities/assigned-roles-window.png" alt-text="스크린샷: Azure 역할 할당 창":::

 이것으로 끝입니다. 시스템 할당 관리 ID를 사용하도록 설정하는 단계를 완료했습니다. 이 ID 자격 증명을 사용하여 BYOS 계정에 저장된 문서 및 파일에 대한 특정 액세스 권한을 Form Recognizer에 부여할 수 있습니다.

## <a name="learn-more-about--managed-identity"></a>관리 ID에 대한 자세한 정보

> [!div class="nextstepaction"]
> [Azure 리소스의 관리 ID: 자주 묻는 질문 - Azure AD](../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)
