---
title: 통화 관리
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 호출을 관리합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4abf92e2d3003d8944ea75c411bc9699716e4684
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705646"
---
# <a name="manage-calls"></a>통화 관리

Azure Communication Services SDKS를 사용하여 호출을 관리하는 방법을 알아봅니다. 전화를 걸고 참가자 및 속성을 관리하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../quickstarts/access-tokens.md)에 대한 자세한 정보
- 선택 사항: [애플리케이션에 통화 추가 시작](../../quickstarts/voice-video-calling/getting-started-with-calling.md)에 대한 빠른 시작을 완료합니다.

::: zone pivot="platform-web"
[!INCLUDE [Manage Calls JavaScript](./includes/manage-calls/manage-calls-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Calls Android](./includes/manage-calls/manage-calls-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Calls iOS](./includes/manage-calls/manage-calls-ios.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Manage Calls Windows](./includes/manage-calls/manage-calls-windows.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계
- [비디오를 관리하는 방법 알아보기](./manage-video.md)
- [호출을 기록하는 방법 알아보기](./record-calls.md)
- [호출을 전사하는 방법 알아보기](./call-transcription.md)