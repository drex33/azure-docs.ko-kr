---
title: 호출에 푸시 알림을 사용하도록 설정합니다.
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 호출에 푸시 알림을 사용하도록 설정합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 87e9aa1741808e60af14eb32e1837bc87c280062
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635720"
---
# <a name="enable-push-notifications-for-calls"></a>호출에 푸시 알림 사용

여기서는 Azure Communication Services 호출에 푸시 알림을 사용하도록 설정하는 방법을 알아봅니다. 이를 설정하면 사용자가 수신 전화를 받을 때 사용자가 응답할 수 있음을 알 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../quickstarts/access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작 완료를 완료하여 [애플리케이션에 음성 통화 추가](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Enable push notifications Android](./includes/push-notifications/push-notifications-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Enable push notifications iOS](./includes/push-notifications/push-notifications-ios.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계
- [이벤트를 구독하는 방법 알아보기](./events.md)
- [호출을 관리하는 방법 알아보기](./manage-calls.md)
- [비디오를 관리하는 방법 알아보기](./manage-video.md)
