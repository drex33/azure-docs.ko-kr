---
title: 관리 ID 만들기 및 사용
titleSuffix: Azure Cognitive Services
description: Azure Portal에서 관리 ID를 만들고 사용하는 방법 이해
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 07/08/2021
ms.author: lajanuar
ms.openlocfilehash: 340121b40845369fe05e36a302556543078629eb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289125"
---
# <a name="create-and-use-managed-identity-for-document-translation"></a>문서 번역의 관리 ID 만들기 및 사용

> [!IMPORTANT]
>
> 문서 번역의 관리 ID는 현재 글로벌 지역에서 사용할 수 없습니다. 문서 번역 작업에 관리 ID를 사용하려는 경우 글로벌이 아닌 Azure 지역에 [Translator 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).

## <a name="what-is-managed-identity"></a>관리 ID란?

 Azure 관리 ID는 Azure 관리되는 리소스에 대한 Azure AD(Azure Active Directory) ID와 특정 권한을 만드는 서비스 주체입니다. 관리 ID를 사용하여 Azure AD 인증을 지원하는 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다. 액세스 권한을 부여하려면 Azure RBAC([Azure 역할 기반 액세스 제어](../../../role-based-access-control/overview.md))를 사용하여 관리 ID에 역할을 할당합니다.  Azure에서 관리 ID를 사용하기 위해 추가되는 비용은 없습니다.

관리 ID는 프라이빗 및 퍼블릭 액세스가 가능한 Azure Blob Storage 계정을 둘 다 지원합니다.  퍼블릭 액세스 권한이 있는 스토리지 계정의 경우 SAS(공유 액세스 서명)를 사용하여 제한된 액세스 권한을 부여하도록 선택할 수 있습니다.  이 문서에서는 시스템이 할당한 관리 ID를 사용하여 Azure Blob Storage 계정에서 번역 문서에 대한 액세스를 관리하는 방법을 살펴봅니다.

> [!NOTE]
>
> 퍼블릭 인터넷에서 사용할 수 있는 Azure Blob Storage 계정을 사용하는 모든 작업의 경우 제한된 기간 동안 제한된 권한이 있는 **SAS**(공유 액세스 서명) URL을 제공하고 POST 요청에서 전달할 수 있습니다.
>
> * SAS URL을 검색하려면 Azure Portal의 스토리지 리소스로 이동하고 **Storage Explorer** 탭을 선택합니다.
> * 컨테이너로 이동하고 마우스 오른쪽 단추를 클릭한 후 **공유 액세스 서명 가져오기** 를 선택합니다. 스토리지 계정 자체가 아니라 컨테이너에 대한 SAS를 가져오는 것이 중요합니다.
> * **읽기**, **쓰기**, **삭제** 및 **목록 보기** 권한이 선택되어 있는지 확인하고 **만들기** 를 클릭합니다.
> * 그런 다음, **URL** 섹션의 값을 임시 위치에 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/) - 계정이 없는 경우 [**체험 계정을 만들**](https://azure.microsoft.com/free/) 수 있습니다.

* **글로벌이 아닌** 지역에 할당된 [**단일 서비스 Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)(다중 서비스 Cognitive Services 아님) 리소스. 자세한 단계는 [Azure Portal을 사용하여 Cognitive Services 리소스 만들기](../../cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)를 ‘참조’하세요.

* Translator 리소스와 동일한 지역의 [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). 스토리지 계정 내에서 Blob 데이터를 저장하고 구성하는 컨테이너를 만듭니다. 계정에 방화벽이 있는 경우 [신뢰할 수 있는 Azure 서비스에 대한 예외](../../../storage/common/storage-network-security.md?tabs=azure-portal#manage-exceptions) 확인란이 선택되어 있어야 합니다.

    :::image type="content" source="../media/managed-identities/allow-trusted-services-checkbox-portal-view.png" alt-text="스크린샷: 신뢰할 수 있는 서비스 허용 확인란, 포털 보기":::

* Azure Portal을 사용하여 [**Azure RBAC(Azure 역할 기반 액세스 제어)** ](../../../role-based-access-control/role-assignments-portal.md)를 간략히 이해합니다.

## <a name="managed-identity-assignments"></a>관리 ID 할당

관리 ID에는 **시스템 할당** 및 **사용자 할당** 의 두 가지 유형이 있습니다.  현재 문서 번역은 시스템이 할당한 관리 ID에서 지원됩니다. 시스템이 할당한 관리 ID는 서비스 인스턴스에서 직접 **사용하도록 설정** 됩니다. 기본적으로 사용하도록 설정되지 않습니다. 리소스로 이동하고 ID 설정을 업데이트해야 합니다. 시스템이 할당한 관리 ID는 수명 주기 내내 리소스에 연결됩니다. 리소스를 삭제하면 관리 ID도 삭제됩니다.

다음 단계에서는 시스템이 할당한 관리 ID를 사용하도록 설정하고 Azure Blob Storage 계정에 대한 제한된 액세스 권한을 Translator 리소스에 부여합니다.

## <a name="enable-a-system-assigned-managed-identity-using-the-azure-portal"></a>Azure Portal을 통해 시스템이 할당한 관리 ID 사용

>[!IMPORTANT]
>
> 시스템이 할당한 관리 ID를 사용하려면 [**소유자**](../../../role-based-access-control/built-in-roles.md#owner) 또는 [**사용자 액세스 관리자**](../../../role-based-access-control/built-in-roles.md#user-access-administrator)와 같은 **Microsoft.Authorization/roleAssignments/write** 권한이 필요합니다. 4개 수준인 관리 그룹, 구독, 리소스 그룹 또는 리소스에서 범위를 지정할 수 있습니다.

1. Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **Translator** 리소스 페이지로 이동합니다.

1. 왼쪽 레일의 **리소스 관리** 목록에서 **ID** 를 선택합니다.

    :::image type="content" source="../media/managed-identities/resource-management-identity-tab.png" alt-text="스크린샷: Azure Portal의 리소스 관리 ID 탭":::

1. 기본 창에서 **시스템 할당 상태** 탭을 **켜기** 로 전환합니다.

1. **권한** 에서 **Azure 역할 할당** 을 선택합니다.

    :::image type="content" source="../media/managed-identities/enable-system-assigned-managed-identity-portal.png" alt-text="스크린샷: Azure Portal에서 시스템이 할당한 관리 ID 사용":::

1. Azure 역할 할당 페이지가 열립니다. 드롭다운 메뉴에서 구독을 선택한 다음, **&plus;역할 할당 추가** 를 선택합니다.

    :::image type="content" source="../media/managed-identities/azure-role-assignments-page-portal.png" alt-text="스크린샷: Azure Portal의 Azure 역할 할당 페이지":::

>[!NOTE]
>
> 추가 > 역할 할당 추가 옵션이 사용되지 않기 때문에 Azure Portal에서 역할을 할당할 수 없거나 “이 범위에서 역할 할당을 추가할 권한이 없습니다.” 권한 오류가 표시되는 경우 현재 스토리지 리소스의 스토리지 범위에서 [**소유자**](../../../role-based-access-control/built-in-roles.md#owner) 또는 [**사용자 액세스 관리자**](../../../role-based-access-control/built-in-roles.md#user-access-administrator)와 같은 Microsoft.Authorization/roleAssignments/write 권한이 있는 할당된 역할을 가진 사용자로 로그인되어 있는지 확인합니다.

7. 다음으로, **Storage Blob 데이터 기여자** 역할을 Translator 서비스 리소스에 할당하려고 합니다. **역할 할당 추가** 팝업 창에서 다음과 같이 필드를 완료하고 **저장** 을 선택합니다.

    | 필드 | 값|
    |------|--------|
    |**범위**| **_스토리지_**.|
    |**구독**| **_스토리지 리소스와 연결된 구독_**.|
    |**리소스**| **_스토리지 리소스의 이름_**.|
    |**역할** | **_Storage Blob 데이터 기여자_**.|

     :::image type="content" source="../media/managed-identities/add-role-assignment-window.png" alt-text="스크린샷: Azure Portal의 역할 할당 추가 페이지":::

1. _추가된 역할 할당_ 확인 메시지가 표시된 후 페이지를 새로 고쳐 추가된 역할 할당을 확인합니다. 

    :::image type="content" source="../media/managed-identities/add-role-assignment-confirmation.png" alt-text="스크린샷: 추가된 역할 할당 확인 팝업 메시지":::

1. 변경이 즉시 표시되지 않으면 기다렸다가 페이지를 한 번 더 새로 고쳐 보세요. 역할 할당을 할당하거나 제거하는 경우 변경 내용이 적용되는 데 최대 30분이 걸릴 수 있습니다.

    :::image type="content" source="../media/managed-identities/assigned-roles-window.png" alt-text="스크린샷: Azure 역할 할당 창":::

 좋습니다! 시스템이 할당한 관리 ID를 사용하도록 설정하는 단계를 완료했습니다. 이 ID 자격 증명을 사용하여 스토리지 리소스에 대한 Translator 관련 액세스 권한을 부여할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 리소스의 관리 ID: 자주 묻는 질문](../../../active-directory/managed-identities-azure-resources/managed-identities-faq.md)

> [!div class="nextstepaction"]
>[관리 ID를 사용하여 액세스 토큰 획득](../../../app-service/overview-managed-identity.md?tabs=dotnet#obtain-tokens-for-azure-resources)