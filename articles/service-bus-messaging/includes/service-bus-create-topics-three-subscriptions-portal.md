---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3a5413591ba637364bbb42a7e7aedf77b40fb898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565702"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Azure Portal을 사용하여 항목 만들기
1. **Service Bus 네임스페이스** 페이지의 왼쪽 메뉴에서 **항목** 을 선택합니다.
2. 도구 모음에서 **+ 항목** 을 선택합니다. 
4. 항목의 **이름** 을 입력합니다. 다른 옵션은 기본값 그대로 둡니다.
5. **만들기** 를 선택합니다.

    ![토픽 만들기](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>항목에 대한 구독 만들기
1. 이전 섹션에서 만든 **항목** 을 선택합니다. 
    
    ![항목 선택](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. **Service Bus 항목** 페이지의 왼쪽 메뉴에서 **구독** 을 선택한 다음, 도구 모음에서 **+ 구독** 을 선택합니다. 
    
    ![[구독 추가] 단추](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. **구독 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 구독 **이름** 에 **S1** 을 입력합니다.
    1. **최대 전달 횟수** 에 **3** 을 입력합니다.
    1. 그런 다음, **만들기** 를 선택하여 구독을 만듭니다. 

        ![[구독 만들기] 페이지](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. 이전 단계를 두 번 반복하여 **S2** 및 **S3** 라는 구독을 만듭니다.