---
title: Azure Marketplace에서 Azure Container 제품 만들기
description: Azure Marketplace에서 Azure Container 제품 만들기
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 09/27/2021
ms.openlocfilehash: b09500142878ee383ccc20e8b62ed22b0f9478c5
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079880"
---
# <a name="create-an-azure-container-offer"></a>Azure Container 제품 만들기

이 문서에서는 Azure Container 제품을 만드는 방법을 설명합니다. 모든 제품은 표준 요구 사항, 호환성 및 적절한 사례에 대한 솔루션을 확인하는 인증 프로세스를 통해 진행됩니다.

시작하기 전에 [파트너 센터](./create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[Azure 컨테이너 제품 계획](marketplace-containers.md)을 검토합니다. 이 제안에 대한 기술 요구 사항과 이 제안을 만들 때 필요한 정보 및 자산이 설명되어 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 홈페이지에서 **Marketplace 제안** 타일을 선택합니다.

    [![파트너 센터 홈페이지의 Marketplace 제안 타일을 보여 줍니다. ](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제안 페이지에서 **+ 새 제안** Azure  >  **Container를** 선택합니다.

    [![왼쪽 창 메뉴 옵션과 '새 제안' 단추. ](./media/azure-container/new-offer-azure-container-workspaces.png) ](./media/azure-container/new-offer-azure-container-workspaces.png#lightbox)

> [!IMPORTANT]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Azure Container** 를 선택합니다.

    :::image type="content" source="media/azure-container/new-offer-azure-container.png" alt-text="왼쪽 창 메뉴 옵션 및 '새 제품' 단추":::

> [!IMPORTANT]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

---

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 제안 웹 주소 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 게시자 ID가 `testpublisherid`이고 **test-offer-1** 을 입력하는 경우 제안 웹 주소는 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Azure Marketplace에서 사용되지 않습니다. 고객에게 표시되는 제안 이름 및 다른 값과 다릅니다.

**만들기** 를 선택하여 제안을 생성합니다. 파트너 센터에서 **제안 설정** 페이지를 엽니다.

## <a name="alias"></a>Alias

파트너 센터 내에서만 이 제안을 참조하는 데 사용할 설명이 포함된 이름을 입력합니다. 이 제안 별칭(제안을 만들 때 입력한 내용으로 미리 채워져 있음)은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제안 이름과 다릅니다. 나중에 제안 이름을 업데이트하려면 [제안 목록](azure-container-offer-listing.md) 페이지를 참조하세요.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

자세한 내용은 [상업용 Marketplace 제안의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

왼쪽 탐색 메뉴 **속성** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- [제안 속성 구성](azure-container-properties.md) 
- [목록에 제품 추가 모범 사례](gtm-offer-listing-best-practices.md)
