---
ms.openlocfilehash: f9f8a0012a0b9407ce2f5ef39f6232ab8855ad4c
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385388"
---

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [활성 Communication Services 리소스](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>전화 번호 받기

번호 프로비저닝을 시작하려면 [Azure Portal](https://portal.azure.com)에서 Communication Services 리소스로 이동합니다.

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

### <a name="search-for-available-phone-numbers"></a>사용 가능한 전화 번호 검색

리소스 메뉴에서 **전화 번호** 블레이드로 이동합니다.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Communication Services 리소스의 전화 페이지를 보여주는 스크린샷":::

**가져오기** 단추를 눌러 마법사를 시작합니다. **전화 번호** 블레이드의 마법사는 시나리오에 가장 적합한 전화 번호를 선택하는 데 도움이 되는 일련의 질문을 안내합니다. 

먼저 전화 번호를 프로비저닝하려는 **국가/지역** 을 선택해야 합니다. 국가/지역이 선택되면 요구 사항에 가장 적합한 **사용 사례** 를 선택해야 합니다. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="[전화 번호 받기] 보기를 보여주는 스크린샷":::

### <a name="select-your-phone-number-features"></a>전화 번호 기능 선택

전화 번호 구성은 다음 두 단계로 구분됩니다. 

1. [전화 번호 유형](../../../concepts/telephony/plan-solution.md#phone-number-types-in-azure-communication-services) 선택
2. [전화 번호 기능](../../../concepts/telephony/plan-solution.md#phone-number-capabilities-in-azure-communication-services) 선택

**현지** 및 **수신자 부담** 의 두 가지 전화번호 유형 중에서 선택할 수 있습니다. 전화 번호 유형이 선택되면 기능을 선택할 수 있습니다.

이 예에서는 **전화 걸기** 및 **SMS 보내기 및 받기** 기능이 있는 **수신자 부담** 번호 유형을 선택했습니다.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="기능 선택 보기를 보여 주는 스크린샷":::

여기서는 페이지 아래쪽에서 **다음: 전화 번호** 단추를 클릭하여 프로비저닝하려는 전화 번호를 사용자 지정합니다.

### <a name="customizing-phone-numbers"></a>전화 번호 사용자 지정

**전화 번호** 페이지에서 프로비저닝하려는 전화 번호를 사용자 지정합니다.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="[번호 선택] 페이지를 보여주는 스크린샷":::

> [!NOTE]
> 이 빠른 시작에서는 **무료** 전화 번호 유형의 사용자 지정 흐름을 보여 줍니다. **현지** 번호 유형을 선택한 경우 환경이 약간 다를 수 있지만 최종 결과는 동일합니다.

사용 가능한 지역 코드 목록에서 **지역 코드** 를 선택하고, 프로비저닝하려는 수량을 입력한 다음, **검색** 을 클릭하여 선택한 요구 사항을 충족하는 전화 번호를 찾습니다. 요구 사항을 충족하는 전화 번호가 해당 월간 비용과 함께 표시됩니다.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="번호가 예약된 [번호 선택] 페이지를 보여주는 스크린샷":::

> [!NOTE]
> 가용성은 선택한 전화 번호 유형, 위치 및 기능에 따라 달라집니다.
> 번호는 트랜잭션이 만료되기 전까지 짧은 시간 동안 예약됩니다. 트랜잭션이 만료되면 번호를 다시 선택해야 합니다.

구매 요약을 확인하고 주문하려면 페이지 아래쪽에서 **다음: 요약** 단추를 클릭합니다.

### <a name="purchase-phone-numbers"></a>전화 번호 구매

요약 페이지에서 번호 유형, 기능, 전화 번호 및 전화 번호를 프로비저닝하는 데 소요되는 총 월간 비용을 검토합니다.

> [!NOTE]
> 표시된 가격은 선택한 전화 번호를 사용자에게 임대하는 비용을 포함하는 **월별 정기 요금** 입니다. 전화를 걸거나 받을 때 발생하는 **종량제 비용** 은 이 보기에 포함되지 않습니다. 가격표는 [여기](../../../concepts/pricing.md)서 확인할 수 있습니다. 이러한 비용은 번호 유형과 전화를 건 대상에 따라 다릅니다. 예를 들어, 시애틀 지역 번호에서 뉴욕 지역 번호로 전화를 걸 때 분당 요금과 같은 번호에서 영국 핸드폰 번호로 전화를 걸 때 분당 요금이 서로 다를 수 있습니다.

마지막으로, 페이지 아래쪽에서 **주문** 을 클릭하여 확인합니다.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="번호 유형, 기능, 전화 번호, 총 월간 비용이 표시된 요약 페이지를 보여주는 스크린샷":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Azure Portal에서 전화 번호 찾기

[Azure Portal](https://portal.azure.com)에서 Azure Communication Services 리소스로 이동합니다.

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

메뉴에서 [전화 번호] 블레이드를 선택하여 전화 번호를 관리합니다.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Communication Services 리소스의 전화 번호 페이지를 보여주는 스크린샷":::

> [!NOTE]
> 프로비저닝된 번호가 이 페이지에 표시될 때까지 몇 분 정도 걸릴 수 있습니다.


### <a name="update-phone-number-capabilities"></a>전화 번호 기능 업데이트

**전화 번호** 페이지에서 구성할 전화 번호를 선택할 수 있습니다.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="기능 업데이트 페이지를 보여 주는 스크린샷":::

사용 가능한 옵션에서 기능을 선택한 다음, **저장** 을 클릭하여 선택 항목을 적용합니다.

### <a name="release-phone-number"></a>전화 번호 해제

**숫자** 페이지에서 전화 번호를 해제할 수 있습니다.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="전화 번호 해제 페이지를 보여 주는 스크린샷":::

해제할 전화 번호를 선택한 다음, **해제** 단추를 클릭합니다.