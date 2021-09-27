---
title: 클라이언트에 호출 전사 상태 표시
titleSuffix: An Azure Communication Services how-to guide
description: Azure Communication Services SDK를 사용하여 호출 전사 상태 표시
author: probableprime
ms.author: rifox
ms.service: azure-communication-services
ms.subservice: calling
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: 9304f7905b12362f8026e59af8cf294ed76fc42e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635744"
---
# <a name="display-call-transcription-state-on-the-client"></a>클라이언트에 호출 전사 상태 표시

[!INCLUDE [Public Preview Disclaimer](../../includes/public-preview-include-document.md)]

통화 전사를 사용하는 경우 통화가 전사되고 있음을 사용자에게 알릴 수 있습니다. 방법은 다음과 같습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../quickstarts/access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작 완료를 완료하여 [애플리케이션에 음성 통화 추가](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

::: zone pivot="platform-android"
[!INCLUDE [Call transcription client-side Android](./includes/call-transcription/call-transcription-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Call transcription client-side iOS](./includes/call-transcription/call-transcription-ios.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계
- [비디오를 관리하는 방법 알아보기](./manage-video.md)
- [호출을 관리하는 방법 알아보기](./manage-calls.md)
- [호출을 기록하는 방법 알아보기](./record-calls.md)