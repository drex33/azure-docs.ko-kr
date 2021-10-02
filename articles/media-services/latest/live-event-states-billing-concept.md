---
title: Azure Media Services의 라이브 이벤트 상태 및 청구
description: 이번 토픽에서는 Azure Media Services 라이브 이벤트 상태 및 청구에 대해 간략하게 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 961c563a12a3edf4df5c898f0c8ee8f1a5b2daf0
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390124"
---
# <a name="live-event-states-and-billing"></a>라이브 이벤트 상태 및 청구

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 라이브 이벤트는 상태가 **실행 중** 또는 **대기** 로 전환되는 즉시 청구를 시작합니다. 서비스를 통해 비디오가 흐르지 않더라도 요금이 청구됩니다. 라이브 이벤트에서 요금이 청구되지 않도록 하려면 라이브 이벤트를 중지해야 합니다. 라이브 기록은 라이브 이벤트와 동일한 방식으로 요금이 청구됩니다.

[라이브 이벤트](/rest/api/media/liveevents)의 **LiveEventEncodingType** 이 표준 또는 프리미엄 1080p로 설정된 경우 Media Services는 입력 피드가 손실된 후에도 12시간 동안 여전히 **실행 중** 상태인 라이브 이벤트를 모두 자동으로 종료하여, 실행 중인 **라이브 출력** 이 없도록 합니다. 그러나 라이브 이벤트가 **실행 중** 상태였던 시간에 대한 요금은 청구됩니다.

> [!NOTE]
> 통과(기본 또는 표준) 라이브 이벤트는 자동으로 종료되지 않으며 과도한 청구를 방지하기 위해 API를 통해 명시적으로 중지되어야 합니다.

## <a name="states"></a>상태

라이브 이벤트는 다음 상태 중 하나일 수 있습니다.

|시스템 상태|설명|
|---|---|
|**중지됨**| 자동 시작이 true로 설정되지 않은 경우 라이브 이벤트를 생성한 후의 초기 상태입니다. 이 상태에서는 요금이 청구되지 않습니다. 라이브 이벤트에서 입력을 받을 수 없습니다. |
|**시작 중**| 라이브 이벤트가 시작되고 리소스가 할당됩니다. 이 상태에서는 요금이 청구되지 않습니다.  오류가 발생하면 라이브 이벤트가 중지 상태로 돌아갑니다.|
| **할당** | 라이브 이벤트에서 할당 작업을 호출하였으며 해당 라이브 이벤트에 대해 리소스가 프로비저닝됩니다. 이 작업이 성공적으로 완료되면 라이브 이벤트가 대기 상태로 전환됩니다.
|**대기**| 라이브 이벤트의 리소스가 프로비저닝되었으며 시작될 준비가 되었습니다. 이 상태에서는 요금이 청구됩니다.  이 상태에서도 대부분의 속성을 업데이트할 수 있지만 수집이나 스트리밍은 허용되지 않습니다.
|**실행 중**| 라이브 이벤트 리소스가 할당되고, 수집 및 미리 보기 URL이 생성되었으며, 라이브 스트림을 수신할 수 있습니다. 이 시점에는 청구가 활성 상태입니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다.|
|**중지 중**| 라이브 이벤트가 중지되고, 리소스가 프로비저닝 해제되고 있습니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. |
|**삭제 중**| 라이브 이벤트가 삭제됩니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. |

라이브 이벤트를 만들 때 라이브 기록을 사용하도록 선택할 수 있습니다. 이렇게 하면 라이브 이벤트가 **실행** 상태일 때마다 라이브 기록에 대한 요금이 청구됩니다. 라이브 이벤트에 오디오가 흐르지 않더라도 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](stream-live-streaming-concept.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
