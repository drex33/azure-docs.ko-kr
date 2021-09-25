---
title: 월간 예상 랩 비용 추세 보기
description: 이 문서에는 Azure DevTest Labs의 랩 비용(월간 예상 비용 추세 차트)을 추적하는 방법에 대한 정보가 나와 있습니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2b202479fd7f3b42cb6f4f2dd284ef8981705790
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645040"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩과 관련된 비용 추적
이 문서에는 랩 비용을 추적하는 방법에 대한 정보가 나와 있습니다. 여기에서는 랩의 당월 예상 비용 추세를 확인하는 방법을 보여 줍니다. 또한 이 문서는 랩에서 리소스당 월간 누계 비용을 확인하는 방법을 보여 줍니다.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>월간 예상 랩 비용 추세 보기 
이 섹션에서는 **월간 예상 비용 추세** 차트를 사용하여 당월의 예상 비용 및 예상되는 월간 비용을 확인하는 방법에 대해 알아봅니다. 또한 설정된 값에 도달하면 결과를 보고하도록 DevTest Labs를 트리거하여 랩 비용을 효율적으로 관리하는 방법에 대해 알아봅니다.

월간 예상 비용 추세 차트를 보려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 랩을 선택합니다.  
4. 왼쪽 메뉴에서 **구성 및 정책** 을 선택합니다.  
4. 왼쪽 메뉴의 **비용 추적** 섹션에서 **비용 추세** 를 선택합니다. 다음 스크릿샷은 비용 차트의 예를 보여 줍니다. 
   
    ![비용 차트](./media/devtest-lab-configure-cost-management/graph.png)

    **예상 비용** 값은 현재 월의 현재까지의 예상 비용입니다. **예측 비용** 은 이번 달 전체의 예상 비용으로, 이전 5일 동안의 랩 비용을 사용해서 계산합니다.

    금액은 다음 정수로 올림됩니다. 예를 들면 다음과 같습니다. 

   * 5.01은 6으로 올림됩니다. 
   * 5.50은 6으로 올림됩니다.
   * 5.99는 6으로 올림됩니다.

     위의 차트에 언급되어 있듯이, 기본적으로 차트에 보이는 비용은 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 제안 요율을 사용하여 *예상된* 비용입니다. [랩의 비용 목표를 관리](#managing-cost-targets-for-your-lab)하여 차트에 표시되는 지출 목표를 설정할 수도 있습니다.

     다음 항목은 비용 계산에 포함되지 *않습니다*.

   * Azure DevTest Labs에서는 Azure 청구 API 를 사용하여 랩 비용을 계산하며 CSP 또는 Dreamspark 구독을 지원하지 않으므로 이러한 구독 방식이 현재 지원되지 않습니다.
   * 사용자에 대한 제안 요율. 현재는 Microsoft 또는 Microsoft 파트너와 협상한 제안 요율(구독 아래에 표시됨)을 사용할 수 없습니다. 오직 종량제 요율만 사용됩니다.
   * 사용자 세금
   * 사용자 할인
   * 사용자의 청구 통화. 현재는, 랩 비용이 USD 통화로만 표시됩니다.

### <a name="managing-cost-targets-for-your-lab"></a>랩의 비용 목표 관리
DevTest Labs에서 지출 목표를 설정하고 이후에 월별 예상 비용 추세 차트에서 지출 목표를 살펴보면 랩의 비용을 보다 효율적으로 관리할 수 있습니다. 또한 지정된 지출 목표 또는 임계값에 도달하면 DevTest Labs에서 알림을 보냅니다. 

1. **비용 추세** 페이지에서 **대상 관리** 를 선택합니다.

    ![목표 관리 단추](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. **대상 관리** 페이지에서 지출 대상 및 임계값을 지정합니다. 또한 선택한 각 임계값을 비용 추세 차트에 보고할 것인지 아니면 웹후크 알림을 통해 보고할 것인지 선택할 수 있습니다.

    ![목표 관리 창](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 비용 목표를 추적할 기간을 선택합니다.
      - **월별**: 비용 목표를 월 단위로 추적합니다.
      - **수정됨**: 시작/종료 날짜에 지정한 날짜 범위에 대한 비용 목표가 추적됩니다. 일반적으로 이러한 값은 프로젝트가 실행되도록 예약된 기간을 나타냅니다.
   - **목표 비용** 을 지정합니다. 예를 들면, 정의된 기간 내에 이 랩에서 얼마를 소비할 것인지 계획할 수 있습니다.
   - 지정된 **비용 목표** 의 최대 125%까지 25% 단위로 보고서를 받을 수 있도록 임계값을 사용하거나 사용하지 않도록 선택합니다.
      - **알림**: 이 임계값에 도달하면 지정한 웹후크 URL을 통해 알림이 제공됩니다.
      - **차트에 그리기**: 이 임계값에 도달하면 월간 예상 비용 추세 차트 보기의 설명처럼, 사용자가 볼 수 있는 비용 추세 그래프에 결과가 표시됩니다.
   - 임계값에 도달하면 **알림** 을 받기로 선택하는 경우 웹후크 URL을 지정해야 합니다. 비용 통합 영역에서 **통합을 추가하려면 여기를 클릭하세요.** 를 선택합니다. 구성 알림 창에서 **웹후크 URL** 을 입력한 다음 **확인** 을 선택합니다.

       ![알림 창 구성](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - **알림** 을 지정하는 경우 웹후크 URL을 정의해야 합니다.
     - 마찬가지로, 웹후크 URL을 정의하는 경우 비용 임계값 창에서 **알림** 을 **켜기** 로 설정해야 합니다.
     - 여기에 입력하기 전에 웹후크를 만들어야 합니다.  

       웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-bindings-http-webhook.md)를 참조하세요. 

## <a name="view-cost-by-resource"></a>리소스별 비용 보기 
랩에서 월간 비용 추세 기능을 사용하면 당월에 얼마나 많은 시간이 소요되었는지 확인할 수 있습니다. 또한 지난 7일간의 지출을 기준으로 해당 월끝까지의 지출 프로젝션을 보여 줍니다. 랩에서 지출하는 이유를 이해하는 데 도움을 받으려면, 테이블의 **리소스별** 월간 누계 비용을 보여 주는 **리소스별 비용** 기능을 사용하면 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
3. 랩 목록에서 원하는 랩을 탭합니다.  
4. 왼쪽 메뉴에서 **구성 및 정책** 을 선택합니다.
5. 왼쪽 메뉴의 **비용 추적** 섹션에서 **리소스별 비용** 을 선택합니다. 랩에 연결된 각 리소스와 관련된 비용을 볼 수 있습니다. 

    ![리소스별 비용](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

이 기능을 사용하면 랩 지출 비용을 절감하기 위한 조치를 취할 수 있도록 가장 많은 비용이 드는 리소스를 쉽게 식별할 수 있습니다. 예를 들어, VM 비용은 VM 크기를 기준으로 합니다. VM 크기가 클수록 비용이 더 많이 듭니다. VM 크기와 소유자 규모를 쉽게 찾을 수 있으므로, VM 소유자에게 해당 VM 크기가 필요한 이유와 크기를 줄일 가능성이 있는지 여부를 파악할 수 있습니다.

[자동 종료 정책](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)을 사용하면 하루 중 특정 시간에 랩 VM을 종료하여 비용을 줄일 수 있습니다. 그러나 랩 사용자는 종료 정책을 옵트아웃할 수 있으므로, VM 실행 비용이 증가합니다. 테이블에서 VM을 선택하여 자동 종료 정책이 옵트아웃되었는지 확인할 수 있습니다. 이 경우 VM 소유자에게 VM이 정책에서 옵트아웃된 이유를 확인할 수 있습니다.
 
## <a name="next-steps"></a>다음 단계
다음으로 수행해 볼 작업은 아래와 같습니다.

* [랩 정책 정의](devtest-lab-set-lab-policy.md) – 랩 및 해당 VM의 사용 방식을 관리하는 데 사용되는 다양한 정책을 설정하는 방법을 알아봅니다. 
* [사용자 지정 이미지 만들기](devtest-lab-create-template.md) - VM을 만들 때 사용자 지정 이미지 또는 Marketplace 이미지 중에서 기본 이미지를 지정할 수 있습니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
* [마켓플레이스 이미지 구성](devtest-lab-configure-marketplace-images.md) - DevTest Labs에서 Azure Marketplace 이미지에 기반하여 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
* [랩에서 VM 만들기](devtest-lab-add-vm.md) - 기본 이미지(사용자 지정 또는 Marketplace 이미지)에서 VM을 만드는 방법 및 VM에서 아티팩트 작업 방법에 대해 설명합니다.
