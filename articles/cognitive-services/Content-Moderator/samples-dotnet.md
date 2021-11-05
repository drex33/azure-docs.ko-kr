---
title: 코드 샘플 - Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: SDK를 통해 .NET 애플리케이션에서 Azure Cognitive Services Content Moderator를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 14750f4bfcab2ffcbb9763d9242ed09273fdc6cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450422"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK 샘플

다음 목록에는 .NET용 Azure Content Moderator SDK를 사용하여 빌드된 코드 샘플에 대한 링크가 포함되어 있습니다.

## <a name="moderation"></a>조정

- **이미지 조정**: [성인 및 외설 콘텐츠, 텍스트 및 얼굴에 대한 이미지를 평가합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). [.NET SDK 빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)을 참조하세요.
- **사용자 지정 이미지**: [사용자 지정 이미지 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). [.NET SDK 빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)을 참조하세요.

> [!NOTE]
> 최대 **5개 이미지 목록** 으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.
>

- **텍스트 조정**: [욕설 및 개인 데이터에 대한 텍스트를 차단합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). [.NET SDK 빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)을 참조하세요.
- **사용자 지정 용어**: [사용자 지정 용어 목록을 사용하여 조정합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). [.NET SDK 빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)을 참조하세요.

> [!NOTE]
> 최대 **5개 용어 목록** 으로 제한되고, 각 목록은 **10,000개 용어를 초과하지 않아야** 합니다.
>

- **비디오 조정**: [성인 및 외설 콘텐츠에 대한 비디오를 검사하고 결과를 가져옵니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). [빠른 시작](video-moderation-api.md)을 참조하세요.

## <a name="review"></a>검토

- **이미지 작업**: [검토를 검사하고 만드는 조정 작업을 시작합니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). [빠른 시작](moderation-jobs-quickstart-dotnet.md)을 참조하세요.
- **이미지 검토**: [사람이 참여하는 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). [빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)을 참조하세요.
- **비디오 검토**: [사람이 참여하는 비디오 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조하세요.
- **비디오 대본 검토**: [사람이 참여하는 비디오 대본 검토를 만듭니다](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). [빠른 시작](video-reviews-quickstart-dotnet.md)을 참조하세요.

[GitHub의 Content Moderator .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)에서 모든 .NET 샘플을 참조하세요.