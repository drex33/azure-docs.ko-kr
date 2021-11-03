---
title: Microsoft AppSource 파트너 센터 Power BI 시각적 개체 제안 만들기
description: 파트너 센터 Power BI 시각적 개체 제안을 만드는 방법을 알아봅니다.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 916013d7bde10e63985324d736a755183200bf3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079361"
---
# <a name="create-a-power-bi-visual-offer"></a>Power BI 시각적 개체 제안 만들기

이 문서에서는 파트너 센터 사용하여 다른 사람들이 검색하고 사용할 수 있도록 Microsoft AppSource [Power BI](https://appsource.microsoft.com) 시각적 개체 제안을 제출하는 방법을 설명합니다.

시작하기 전에 [파트너 센터](./create-account.md)에서 상업용 Marketplace 계정을 만들고 상업용 Marketplace 프로그램에 해당 계정이 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[Power BI 시각적 개체 제안 계획을 검토합니다.](marketplace-power-bi-visual.md) 이 제안에 대한 기술 요구 사항과 이 제안을 만들 때 필요한 정보 및 자산이 설명되어 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 홈페이지에서 **Marketplace 상품** 타일을 선택합니다.

    [ ![파트너 센터 홈페이지에서 보여 주는 Marketplace 제안 타일](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제안 페이지에서 **+ 새 제안** Power BI 시각적  >  **개체를** 선택합니다.

    [![왼쪽 창 메뉴 옵션과 '새 제안' 단추를 표시합니다. ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png) ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png#lightbox)

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요** 를 선택합니다.
1. 개요 페이지에서 + **새 제안**  >  **Power BI 시각적 개체를** 선택합니다.

    :::image type="content" source="media/power-bi-visual/new-offer-power-bi-visual.png" alt-text="왼쪽 창 메뉴 옵션과 '새 제안' 단추를 표시합니다.":::

---

> [!IMPORTANT]
> 제안이 게시되면 파트너 센터 편집한 내용은 제안을 다시 게시한 후에만 AppSource에 표시됩니다. 제안을 변경한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID** 를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 제안 웹 주소 및 Azure Resource Manager 템플릿(해당하는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 게시자 ID가 `testpublisherid`이고 **test-offer-1** 을 입력하는 경우 제안 웹 주소는 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`이 됩니다.
- **만들기** 를 선택한 후에는 제품 ID를 변경할 수 없습니다.
- 제안 ID는 파트너 센터 다른 모든 Power BI 시각적 개체 제안 목록에서 고유해야 합니다.

**제품 별칭** 을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 AppSource에서 사용되지 않습니다. 고객에게 표시되는 제안 이름 및 다른 값과 다릅니다.

**만들기** 를 선택하여 제안을 생성합니다. 파트너 센터에서 **제안 설정** 페이지를 엽니다.

## <a name="setup-details"></a>설정 정보

**추가 구매의** 경우 제안에 서비스 구매 또는 추가 앱 내 구매가 필요한지 여부를 선택합니다.

**Power BI 인증(선택** 사항)의 경우 설명을 주의 깊게 읽고 Power BI 인증을 요청하려면 확인란을 선택합니다. [인증된](/power-bi/developer/visuals/power-bi-custom-visuals-certified) Power BI 시각적 개체는 Microsoft Power BI 팀이 테스트하고 승인한 특정 코드 요구 사항을 충족합니다. 인증 프로세스는 제안 게시를 지연시킬 수 있는 추가 시간이 걸리므로 인증을 *요청하기 전에* Power BI 시각적 개체를 제출하고 게시하는 것이 좋습니다.

## <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

CRM에 연결하는 것은 선택 사항입니다. 자세한 내용은 [상업용 Marketplace 제안의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

왼쪽 탐색 메뉴 **속성** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요.

## <a name="next-steps"></a>다음 단계

- [**제안 속성**](power-bi-visual-properties.md)