---
title: '빠른 시작: C#(Xamarin)용 Speech SDK 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 통해 Speech Service SDK를 사용하여 C# Xamarin용 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: eb5d842d5934a46b98a7707b6f567c26445df70e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253296"
---
이 가이드에서는 .NET을 사용하여 iOS, Android 및 Windows용 최신 고성능 애플리케이션을 빌드하기 위한 오픈 소스 플랫폼인 [Xamarin](/xamarin/get-started/what-is-xamarin)용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다. 패키지 이름을 직접 시작하려면 NuGet 콘솔에서 `Install-Package Microsoft.CognitiveServices.Speech`를 실행합니다.

> [!NOTE]
> Xamarin용 Speech SDK는 Windows Desktop(x86 및 x64) 또는 유니버설 Windows 플랫폼(x86, x64, ARM/ARM64), Android(x86, ARM32/64) 및 iOS(x64 시뮬레이터 및 ARM64)를 지원합니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에는 다음이 필요합니다.

* Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)가 필요합니다. 처음 설치하려면 다시 시작해야 할 수 있습니다.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio 프로젝트를 만들고 Speech SDK를 설치합니다.

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

이제 Speech SDK가 설치되었습니다. 이제 이전 단계에서 만든 "helloworld" 프로젝트를 삭제하거나 다시 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
