---
title: 약정 계층 가격 책정을 사용하여 Azure Cognitive Services 리소스 만들기
description: 종량제 가격 책정과 다른 약정 계층 가격 책정에 가입하는 방법을 알아봅니다.
author: aahill
ms.author: aahi
ms.custom: subject-cost-optimization, mode-other
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/04/2021
ms.openlocfilehash: e3c29dbaf856efe74b247704854e3e17ba66fbd2
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133068094"
---
# <a name="quickstart-purchase-commitment-tier-pricing"></a>빠른 시작: 구매 약정 계층 가격 책정

Cognitive Services는 종량제 가격 책정 모델과 비교하여 할인된 요금을 제공하는 약정 계층 가격 책정을 제공합니다. 약정 계층 가격 책정을 사용하면 다음 Cognitive Services 기능을 고정 요금으로 사용하도록 약정할 수 있으므로 워크로드 요구 사항에 따라 예측 가능한 총 비용을 확보할 수 있습니다.

* Speech to Text(표준)
* Text to Speech(인공신경망)
* 텍스트 번역(표준)
* Language Understanding 표준(텍스트 요청)
* Azure Cognitive Service for Language
    * 감정 분석
    * 핵심 문구 추출
    * 언어 검색
* Computer Vision - 읽기

약정 계층 가격 책정은 다음 응용 AI 서비스에도 사용할 수 있습니다.
* Form Recognizer - 사용자 지정/청구서

자세한 내용은 [Azure Cognitive Services 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

## <a name="request-approval-to-purchase-a-commitment-plan"></a>약정 플랜 구매에 대한 승인 요청

약정 플랜을 구매하려면 먼저 [온라인 신청서를 제출](https://aka.ms/csgatecommitment)해야 합니다. 애플리케이션이 승인되면 신규 및 기존 Azure 리소스 모두에 대해 Azure Portal에서 약정 계층을 구매할 수 있습니다. 

* 양식에서 Azure 구독 ID와 연결된 회사 이메일 주소를 사용해야 합니다.

* 이메일(받은 편지함 및 정크 메일 폴더 모두)에서 `csgate@microsoft.com`의 신청서 상태에 대한 업데이트를 확인합니다.

승인되면 새 리소스를 만들어 약정 플랜을 사용하거나 기존 리소스를 업데이트할 수 있습니다. 

## <a name="create-a-new-resource"></a>새 리소스 만들기

> [!NOTE]
> 약정 플랜을 구매하고 사용하려면 리소스에 표준 가격 책정 계층이 있어야 합니다. 무료 계층에 있는 리소스에 대한 약정 플랜을 구매하거나 옵션을 참조할 수 없습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 위에 나열된 해당 Cognitive Services 또는 응용 AI 서비스 중 하나에 대해 **새 리소스 만들기** 를 선택합니다. 

2. 해당 정보를 입력하여 리소스를 만듭니다. 표준 가격 책정 계층을 선택해야 합니다.

    :::image type="content" source="media/commitment-tier/create-resource.png" alt-text="Azure Portal에서 리소스 만들기를 보여 주는 스크린샷" lightbox="media/commitment-tier/create-resource.png":::

3. 리소스가 만들어지면 가격 책정을 종량제에서 약정 플랜으로 변경할 수 있습니다.  

## <a name="purchase-a-commitment-plan-by-updating-your-azure-resource"></a>Azure 리소스를 업데이트하여 약정 플랜 구매

1. 승인된 Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 
2. 위에 나열된 해당 기능 중 하나에 대한 Azure 리소스에서 **약정 계층 가격 책정** 을 선택합니다.

    > [!NOTE]
    > 다음과 같은 경우에만 약정 플랜 구매 옵션이 표시됩니다.
    > * 리소스에서 표준 가격 책정 계층을 사용하고 있습니다.
    > * 약정 계층 가격 책정을 구매하도록 승인되었습니다. 
 
1. **변경** 을 선택하여 호스트된 API 및 컨테이너 사용량에 사용 가능한 약정을 확인합니다. 

    :::image type="content" source="media/commitment-tier/commitment-tier-pricing.png" alt-text="Azure Portal의 약정 계층 가격 책정 페이지를 보여 주는 스크린샷" lightbox="media/commitment-tier/commitment-tier-pricing.png":::

4. 표시되는 창에서 **계층** 및 **자동 갱신** 옵션을 모두 선택합니다.

    * **약정 계층** - 기능에 대한 약정 계층입니다. **구매** 를 클릭하면 약정 계층이 즉시 사용하도록 설정되며 약정 금액이 비례 배분 기준으로 청구됩니다.
    
    * **자동 갱신** - 다음 청구 기간부터 현재 약정 플랜을 갱신, 변경 또는 취소하려는 방법을 선택합니다. 자동 갱신하도록 결정하는 경우 **자동 갱신 날짜** 는 다음 청구 기간에 대한 요금이 청구되는 날짜(현지 표준 시간대)입니다. 이 날짜는 달력 월의 시작과 일치합니다.
    

    > [!CAUTION]
    > **구매** 를 클릭하면 선택한 계층에 대한 요금이 청구됩니다. 구매한 약정 플랜은 환불되지 않습니다.
    > 
    > 약정 플랜은 구매 시 첫 달을 제외하고 매월 청구되며, 해당 월의 남은 일 수에 따라 비례 배분(비용 및 할당량)됩니다. 다음 달의 경우 해당 월의 첫째 날에 요금이 발생합니다.

    :::image type="content" source="media/commitment-tier/enable-commitment-plan.png" alt-text="Azure Portal의 약정 계층 가격 책정 및 갱신 세부 정보를 보여 주는 스크린샷" lightbox="media/commitment-tier/enable-commitment-plan-large.png":::


## <a name="overage-pricing"></a>초과분 가격 책정

제공된 할당량을 초과하여 리소스를 사용하는 경우 약정 계층에 언급된 초과분 금액에 따라 추가 사용량에 대한 요금이 부과됩니다.

## <a name="purchase-a-different-commitment-plan"></a>다른 약정 플랜 구매 

약정 플랜에는 달력 월 약정 기간이 있습니다. 약정 플랜은 기본 종량제 가격 모델에서 언제든지 구매할 수 있습니다. 플랜을 구매하면 남은 달에 비례 배분된 요금이 청구됩니다. 약정 기간 중에는 현재 월의 약정 플랜을 변경할 수 없습니다. 그러나 다음 달에 대해서는 다른 약정 플랜을 선택할 수 있습니다. 다음 달에 대한 청구는 다음 달의 첫째 날에 발생합니다.

제공된 것보다 더 큰 약정 플랜이 필요한 경우 `csgate@microsoft.com`에 문의하세요.

## <a name="end-a-commitment-plan"></a>약정 플랜 종료

약정 플랜을 계속 구매하지 않으려는 경우 리소스의 자동 갱신을 **자동 갱신 안 함** 으로 설정할 수 있습니다. 약정 플랜은 표시된 약정 종료 날짜에 만료됩니다. 이 날짜 이후에는 약정 플랜에 대한 요금이 청구되지 않습니다. Azure 리소스를 계속 사용하여 종량제 가격으로 청구되는 API를 호출할 수 있습니다. 매월 마지막 날 자정(UTC)까지 약정 요금제를 종료해야 하며 다음 달에는 요금이 청구되지 않습니다. 

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Services 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [약정 계층 가격 책정 구매 신청서 제출](https://aka.ms/csgatecommitment)
