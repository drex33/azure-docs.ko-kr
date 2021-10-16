---
title: Microsoft AppSource에서 Azure Container 제품의 계획 기술 구성을 설정합니다.
description: Microsoft AppSource에서 Azure Container 제품의 계획 기술 구성을 설정합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: f6145e8732252f7d7b407a2d258d2127e6911ddc
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072160"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>Azure Container 제품의 계획 기술 구성 설정

컨테이너 이미지는 프라이빗 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)에 호스트되어야 합니다. 이 페이지를 사용하여 Azure Container Registry 내의 컨테이너 이미지 리포지토리에 대한 참조 정보를 제공합니다.

제품을 제출한 후에는 컨테이너 이미지가 특정 퍼블릭 컨테이너 레지스트리의 Azure Marketplace로 복사됩니다. Azure 사용자의 모듈 사용에 대한 모든 요청은 프라이빗 컨테이너 레지스트리가 아닌 Azure Marketplace 공용 컨테이너 레지스트리에서 제공됩니다.

여러 플랫폼을 대상으로 태그를 사용하여 여러 버전의 모듈 컨테이너 이미지를 제공할 수 있습니다. 태그 및 버전 관리에 대한 자세한 내용은 [Azure Container 기술 자산 준비](azure-container-technical-assets.md)를 참조하세요.

## <a name="image-repository-details"></a>이미지 리포지토리 세부 정보

**Azure 구독 ID** 를 제공하여 리소스 사용량이 보고되고 컨테이너 이미지를 포함하는 Azure Container Registry에 대한 서비스 요금이 청구될 수 있도록 합니다. 이 ID는 Azure Portal의 [구독 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 찾을 수 있습니다.

컨테이너 이미지로 Azure Container Registry를 포함하는 [**Azure 리소스 그룹 이름**](../azure-resource-manager/management/manage-resource-groups-portal.md)을 제공합니다. 리소스 그룹은 위의 구독 ID에서 액세스할 수 있어야 합니다. Azure Portal의 [리소스 그룹](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 페이지에서 이름을 찾을 수 있습니다.

컨테이너 이미지를 포함하는 [**Azure Container Registry 이름**](../container-registry/container-registry-intro.md)을 제공합니다. 컨테이너 레지스트리는 앞에서 제공한 Azure 리소스 그룹에 있어야 합니다. 전체 로그인 서버 이름이 아닌 레지스트리 이름만 제공합니다. 이름에서 **azurecr.io** 를 생략합니다. Azure Portal의 [컨테이너 레지스트리 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)에서 레지스트리 이름을 찾을 수 있습니다.

컨테이너 이미지가 있는 Azure Container Registry와 관련된 [**Azure Container Registry에 대한 관리 사용자 이름**](../container-registry/container-registry-authentication.md#admin-account)을 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호(다음 단계)가 필요합니다. 관리 사용자 이름 및 암호를 가져오려면 Azure CLI(명령줄 인터페이스)를 사용하여 **admin enabled** 속성을 **True** 로 설정합니다. 필요에 따라 Azure Portal에서 **관리 사용자** 를 **사용** 으로 설정할 수 있습니다.

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="컨테이너 레지스트리 업데이트 대화 상자를 보여 줍니다.":::

Azure Container Registry와 연결되고 컨테이너 이미지가 있는 관리 사용자 이름에 대한 **Azure Container Registry의 관리자 암호** 를 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호가 필요합니다. **Container Registry** > **액세스 키** 로 이동하거나 Azure CLI에서 [show 명령](/cli/azure/acr/credential#az-acr-credential-show)을 사용하여 Azure Portal의 암호를 가져올 수 있습니다

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="Azure Portal의 액세스 키 화면을 보여 줍니다.":::

이미지를 포함하는 **Azure Container Registry 내의 리포지토리의 이름** 을 제공합니다. 레지스트리에 이미지를 푸시할 때 리포지토리의 이름을 지정합니다. [Container Registry](https://azure.microsoft.com/services/container-registry/) > **리포지토리 페이지** 로 이동하여 리포지토리의 이름을 찾을 수 있습니다. 자세한 내용은 [Azure Portal에서 컨테이너 레지스트리 리포지토리 보기](../container-registry/container-registry-repositories.md)를 참조하세요. 이름이 설정되면 변경할 수 없습니다. 계정의 각 제품마다 고유한 이름을 사용합니다.

## <a name="image-versions"></a>이미지 버전

고객은 업데이트를 게시할 때 Azure Marketplace에서 자동으로 업데이트를 다운로드할 수 있어야 합니다. 업데이트하지 않으려면 특정 버전의 이미지를 유지할 수 있어야 합니다. 이 작업을 수행하려면 이미지를 업데이트할 때마다 새 이미지 태그를 추가해야 합니다.

지원되는 모든 플랫폼에서 이미지의 최신 버전을 가리키는 **이미지 태그** 를 포함하려면 **이미지 버전 추가** 를 선택합니다. 또한 버전 태그를 포함해야 합니다(예: xx.xx.xx, xx는 숫자). 고객은 여러 플랫폼을 대상으로 하는 [매니페스트 태그](https://github.com/estesp/manifest-tool)를 사용해야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다. 모든 매니페스트 태그(최신 태그 제외)는 X.Y 또는 X.Y.Z로 시작해야 합니다(X, Y, Z는 정수입니다). 예를 들어 최신 태그가 `1.0.1-linux-x64`, `1.0.1-linux-arm32` 및 `1.0.1-windows-arm32`를 가리키는 경우 이러한 6개의 태그는 이 필드에 추가되어야 합니다. 태그 및 버전 관리에 대한 자세한 내용은 [Azure Container 기술 자산 준비](azure-container-technical-assets.md)를 참조하세요.

> [!TIP]
> 테스트 중에 이미지를 식별할 수 있도록 이미지에 테스트 태그를 추가합니다.

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>다음 단계

- **Microsoft와 공동 판매** 하려면(선택 사항) 왼쪽 탐색 메뉴에서 해당 옵션을 선택합니다. 자세한 내용은 [공동 판매 파트너 참여](./co-sell-overview.md)를 참조하세요.
- **CSP(Cloud Solution Partner)를 통해 재판매** 하려면(역시 선택 사항) 왼쪽 탐색 메뉴에서 해당 옵션을 선택합니다. 자세한 내용은 [CSP 파트너를 통한 재판매](cloud-solution-providers.md)를 참조하세요.
- 이러한 작업 중 하나를 설정하지 않았거나 완료한 경우 [제품을 검토하고 게시](review-publish-offer.md)해야 합니다.