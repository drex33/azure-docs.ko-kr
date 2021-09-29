---
title: Azure Communication Services 통화 녹음/녹화 API 빠른 시작
titleSuffix: An Azure Communication Services quickstart document
description: 통화 녹음/녹화 API에 대한 빠른 시작 샘플을 제공합니다.
author: ravithanneeru
manager: GrantMeStrength
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
zone_pivot_groups: acs-csharp-java
ms.openlocfilehash: 397423b0bc86833b9a029c84c0d3e785ffc35049
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671871"
---
# <a name="call-recording-api-quickstart"></a>통화 녹음/녹화 API 빠른 시작

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

이 빠른 시작을 통해 음성 및 영상 통화 녹음/녹화를 시작할 수 있습니다. 이 빠른 시작에서는 [호출 클라이언트 SDK](get-started-with-video-calling.md)를 이미 사용하여 최종 사용자 호출 환경을 빌드했다고 가정합니다. **호출 서버 API 및 SDK** 를 사용하여 기록을 사용하도록 설정하고 관리할 수 있습니다. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [Build Call Recording server sample with C#](./includes/call-recording-samples/recording-server-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Build Call Recording server sample with Java](./includes/call-recording-samples/recording-server-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [영웅 샘플 호출](../../samples/calling-hero-sample.md) 체크 아웃
- [통화 SDK 기능](./calling-client-samples.md)에 대해 알아보기
- [호출 작동 방식](../../concepts/voice-video-calling/about-call-types.md)에 대해 자세히 알아보기
