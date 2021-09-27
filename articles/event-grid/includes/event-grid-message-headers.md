---
title: 파일 포함
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 09/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ab3994f44ed5a77ba228d9338d40599012597a95
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910391"
---
## <a name="message-headers"></a>메시지 헤더
다음은 메시지 헤더에서 수신되는 속성입니다.

| 속성 이름 | Description |
| ------------- | ----------- | 
| aeg-subscription-name | 이벤트 구독의 이름입니다. |
| aeg-delivery-count | 이벤트를 시도한 횟수입니다. |
| aeg-event-type | <p>이벤트의 유형입니다.</p><p>다음 값 중 하나일 수 있습니다.</p><ul><li>SubscriptionValidation</li><li>알림</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>이벤트의 메타데이터 버전입니다.<p> **Event Grid 이벤트 스키마** 의 경우 이 속성은 메타데이터 버전을 나타내고 **클라우드 이벤트 스키마** 의 경우 **사양 버전** 을 나타냅니다. </p>|
| aeg-data-version | <p>이벤트의 데이터 버전입니다.</p><p>**Event Grid 이벤트 스키마** 의 경우 이 속성은 데이터 버전을 나타내고 **클라우드 이벤트 스키마** 에 대해서는 적용되지 않습니다.</p> |
| aeg-output-event-id | Event Grid 이벤트의 ID입니다. |


