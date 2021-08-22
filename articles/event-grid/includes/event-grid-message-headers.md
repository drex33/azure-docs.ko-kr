---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413904"
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


