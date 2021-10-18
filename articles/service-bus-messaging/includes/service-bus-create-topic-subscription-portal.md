---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491abcae4bf73d6b2dcd8172fcb1b69cb7b60d32
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807523"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Azure Portal을 사용하여 항목 만들기
1. **Service Bus 네임스페이스** 페이지의 왼쪽 메뉴에서 **항목** 을 선택합니다.
2. 도구 모음에서 **+ 항목** 을 선택합니다. 
4. 항목의 **이름** 을 입력합니다. 다른 옵션은 기본값 그대로 둡니다.
5. **만들기** 를 선택합니다.

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-topic.png" alt-text="토픽 만들기 페이지를 보여주는 이미지.":::

## <a name="create-a-subscription-to-the-topic"></a>항목에 대한 구독 만들기
1. 이전 섹션에서 만든 **항목** 을 선택합니다. 
    
    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/select-topic.png" alt-text="토픽 목록에서 토픽의 선택을 보여주는 이미지.":::
2. **Service Bus 토픽** 페이지의 도구 모음에서 **+ 구독** 을 선택합니다. 

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png" alt-text="구독 추가 단추를 보여주는 이미지.":::    
3. **구독 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 구독 **이름** 에 **S1** 을 입력합니다.
    1. **최대 전달 횟수** 에 **3** 을 입력합니다.
    1. 그런 다음, **만들기** 를 선택하여 구독을 만듭니다. 

        :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png" alt-text="구독 만들기 페이지를 보여주는 이미지.":::
