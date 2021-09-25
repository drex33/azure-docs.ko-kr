---
title: 클라이언트에서 통화 기록 관리
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 클라이언트에서 통화 기록을 관리합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.topic: how-to
ms.subservice: calling
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 5ef33ef46b208f66fa7d31b20bb603cd543880f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705644"
---
# <a name="manage-call-recording-on-the-client"></a>클라이언트에서 통화 기록 관리

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

[통화 기록](../../concepts/voice-video-calling/call-recording.md)을 사용하면 사용자가 Azure Communication Services 호출을 기록할 수 있습니다. 여기서는 클라이언트 쪽에서 기록을 관리하는 방법을 알아봅니다. 이 작업을 수행하려면 서버 [쪽](../../quickstarts/voice-video-calling/call-recording-sample.md) 기록을 설정해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../quickstarts/access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작 완료를 완료하여 [애플리케이션에 음성 통화 추가](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-web"
[!INCLUDE [Record Calls Client-side JavaScript](./includes/record-calls/record-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Record Calls Client-side Android](./includes/record-calls/record-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Record Calls Client-side iOS](./includes/record-calls/record-calls-ios.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계
- [호출을 관리하는 방법 알아보기](./manage-calls.md)
- [비디오를 관리하는 방법 알아보기](./manage-video.md)
- [호출을 전사하는 방법 알아보기](./call-transcription.md)