---
title: SDK 이벤트 구독
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services Sdk를 사용 하 여 SDK 이벤트를 구독 합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 25f5575340ac35fa79ad7f8a1dc8e611a1820802
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705647"
---
# <a name="subscribe-to-sdk-events"></a>SDK 이벤트 구독

Azure Communication Services Sdk는 동적 이며 많은 속성을 포함 합니다. 이러한 변경을 수행 하는 경우 개발자는 언제 무엇을 변경 하는지 알아야 할 수 있습니다. 방법은 다음과 같습니다.

::: zone pivot="platform-web"
[!INCLUDE [Events JavaScript](./includes/events/events-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Events Android](./includes/events/events-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Events iOS](./includes/events/events-ios.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계
- [호출 퀵 스타트 사용해 보기](../../quickstarts/voice-video-calling/getting-started-with-calling.md)
- [빠른 시작 화상 통화 시도](../../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- [푸시 알림을 사용 하도록 설정 하는 방법 알아보기](./push-notifications.md)