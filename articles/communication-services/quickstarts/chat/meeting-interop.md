---
title: Azure Communication Services에서 Teams interop 시작
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication 채팅 SDK를 사용하여 Teams 미팅에 조인하는 방법을 알아봅니다.
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: c3dacc492df4dc749514971b16776ae0c3e2adc7
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254700"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>빠른 시작: 채팅 앱으로 Teams 미팅에 참가



채팅 솔루션을 Microsoft Teams에 연결하여 Azure Communication Services를 시작하세요. 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Teams interop with Android SDK](./includes/meeting-interop-android.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [채팅 영웅 샘플](../../samples/chat-hero-sample.md) 체크 아웃
- [채팅 작동 방식](../../concepts/chat/concepts.md)에 대한 자세한 정보
