---
title: 호출 중 비디오 관리
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services Sdk를 사용 하 여 비디오 통화를 관리 합니다.
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-web-ios-android
ms.openlocfilehash: 536dc2233ca0ae0773c9e953d35bee6ee4a4da40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705645"
---
# <a name="manage-video-during-calls"></a>호출 중 비디오 관리

Azure Communication Services SDK를 사용 하 여 비디오 통화를 관리 하는 방법을 알아봅니다. 통화 내에서 비디오를 보내고 받는 과정을 관리 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../quickstarts/access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작을 완료 하 여 [응용 프로그램에 음성 통화를 추가](../../quickstarts/voice-video-calling/getting-started-with-calling.md) 합니다.

::: zone pivot="platform-web"
[!INCLUDE [Manage Video Calls JavaScript](./includes/manage-video/manage-video-web.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Manage Video Calls Android](./includes/manage-video/manage-video-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Manage Video Calls iOS](./includes/manage-video/manage-video-ios.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계
- [호출을 관리 하는 방법 알아보기](./manage-calls.md)
- [호출을 기록 하는 방법 알아보기](./record-calls.md)
- [호출을 높여줄 하는 방법 알아보기](./call-transcription.md)
