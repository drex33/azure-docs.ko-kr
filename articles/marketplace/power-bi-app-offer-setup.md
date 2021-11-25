---
title: Microsoft AppSource(Azure Marketplace)에서 Power BI 앱 제품 만들기
description: Microsoft AppSource(Azure Marketplace)에서 Power BI 앱 제품을 만듭니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 11/22/2021
ms.openlocfilehash: 1f08bc8ea546555e3706f7607f9b72a19563f010
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071662"
---
# <a name="create-a-power-bi-app-offer"></a>Power BI 앱 제품 만들기

이 문서에서는 Power BI 앱 제품을 만드는 방법을 설명합니다. 모든 제품은 표준 요구 사항, 호환성 및 적절한 사례에 대한 솔루션을 확인하는 인증 프로세스를 거칩니다.

시작하기 전에 [파트너 센터](./create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[Power BI 제품 계획](marketplace-power-bi.md)을 검토하세요. 이 제안에 대한 기술 요구 사항과 이 제안을 만들 때 필요한 정보 및 자산이 설명되어 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제안 페이지에서 **+ 새 제안** Power BI  >  **앱을** 선택합니다.

    [![새 제안 단추에서 사용할 수 있는 Power BI 앱 제안 유형을 보여 줍니다. ](./media/power-bi/new-offer-power-bi-app-workspaces.png) ](./media/power-bi/new-offer-power-bi-app-workspaces.png#lightbox)

> [!IMPORTANT]
> 제안을 게시한 후 파트너 센터에서 제안을 편집하면 제안을 다시 게시해야 편집한 내용이 Microsoft AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

**Power BI 앱** 이 표시되지 않거나 사용할 수 없는 경우 계정에 이 제품 유형을 만들 수 있는 권한이 없는 것입니다. 개발자 계정에 등록하는 것을 포함하여 이 제품 유형에 대한 모든 [요구 사항](./marketplace-dynamics-365.md)이 충족되었는지 확인합니다.

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 제안 웹 주소 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 게시자 ID가 `testpublisherid`이고 **test-offer-1** 을 입력하는 경우 제안 웹 주소는 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 AppSource에서 사용되지 않습니다. 고객에게 표시되는 제안 이름 및 다른 값과 다릅니다.
- **만들기** 를 선택한 후에는 이 이름을 변경할 수 없습니다.

**만들기** 를 선택하여 제안을 생성합니다. 파트너 센터에서 **제안 설정** 페이지를 엽니다.

## <a name="alias"></a>Alias

파트너 센터 내에서만 이 제안을 참조하는 데 사용할 설명이 포함된 이름을 입력합니다. 이 제안 별칭(제안을 만들 때 입력한 내용으로 미리 채워져 있음)은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제안 이름과 다릅니다. 나중에 제안 이름을 업데이트하려면 [제안 목록](power-bi-app-offer-listing.md) 페이지를 참조하세요.

## <a name="setup-details"></a>설정 정보

이 섹션은 비어 있으며 Power BI 앱에 적용할 수 없습니다.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

자세한 내용은 [상업용 Marketplace 제안의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

왼쪽 탐색 메뉴 **속성** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- [제안 속성 구성](power-bi-app-properties.md)
