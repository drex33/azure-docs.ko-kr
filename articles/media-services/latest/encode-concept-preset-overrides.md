---
title: 미리 설정 된 재정의를 사용 하 여 작업 제출에 대 한 변환 설정 변경
description: 이 문서에서는 사전 설정 재정의를 사용 하 여 작업 인스턴스당 변환 설정을 조정 하는 방법을 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: johndeu
ms.custom: seodec18
ms.openlocfilehash: 3b748aa9e6b8d9f79572b05d10258839aabfa8f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705632"
---
# <a name="using-preset-overrides-to-control-per-job-settings"></a>사전 설정 재정의를 사용 하 여 작업당 설정 제어

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="transforms-and-jobs-overview"></a>변환 및 작업 개요

Media Services v3을 사용하여 인코딩하려면 [변환](/rest/api/media/transforms) 및 [작업](/rest/api/media/jobs)을 만들어야 합니다. 변환은 인코딩 설정 및 출력에 대한 방법을 정의합니다. 작업은 해당 방법의 인스턴스입니다. 자세한 내용은 [Transform 및 Jobs](transform-jobs-concept.md)를 참조하세요.

Media Services 사용 하 여 분석을 인코딩 또는 사용 하는 경우 변환에 사용자 지정 사전 설정을 정의 하 여 사용할 설정을 정의할 수 있습니다. 모든 시나리오에 대 한 사용자 지정 변환을 만들 필요가 없도록 작업 별로 변환 설정을 재정의 해야 하는 경우도 있습니다. 변환 사전 설정에 대 한 설정을 재정의 하려면 작업을 변환에 제출 하기 전에 [작업 출력 자산의](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) 미리 설정 된 재정의 속성을 사용 하면 됩니다.

## <a name="preset-overrides"></a>미리 설정 재정의

미리 설정 재정의를 사용 하면 변환 개체를 처음 만든 후에 제공 되는 설정을 재정의 하는 사용자 지정 된 기본 설정을 전달할 수 있습니다.  이 속성은 변환에 새 작업을 제출할 때 [작업 출력 자산](/dotnet/api/microsoft.azure.management.media.models.joboutputasset) 에서 사용할 수 있습니다.

이는 사용자 지정 정의 변환의 일부 속성 또는 기본 제공 기본 설정의 속성을 재정의 해야 하는 경우에 유용할 수 있습니다. 예를 들어, [audio analyzer 기본 제공 기본 설정을](/rest/api/media/transforms/create-or-update#audioanalyzerpreset)사용 하는 사용자 지정 변환을 만든 경우 처음에는 영어에 대 한 "en-us"의 오디오 언어 설정을 사용 하도록 해당 기본 설정을 설정 했습니다.  이로 인해 제출 된 각 작업이 음성 텍스트 기록 엔진에 미국 영어로만 전송 됩니다. 해당 변환에 제출 된 모든 작업은 "en-us" 언어 설정으로 잠깁니다. 이 시나리오는 모든 언어에 대해 정의 된 변환을 사용 하 여 해결할 수 있지만이는 관리 하기가 훨씬 어려우며 계정에서 변형 할당량 제한에 도달할 수 있습니다.
이 시나리오를 가장 잘 해결 하려면 작업을 변환에 제출 하기 전에 작업 출력 자산에 미리 설정 된 재정의를 사용 합니다.  그런 다음 단일 "오디오 기록" 변환을 정의 하 고 작업당 필요한 언어 설정을 전달할 수 있습니다.

사전 설정 재정의는 각 작업이 변환에 제출 된 새 사용자 지정 사전 설정 정의를 전달 하는 방법을 제공 합니다. 이 속성은 API의 2021-06-01 버전을 기반으로 하는 모든 SDK 버전의 [작업 출력](/dotnet/api/microsoft.azure.management.media.models.joboutput) 엔터티에서 사용할 수 있습니다.

참조는 REST 설명서에서 작업 출력 엔터티의 [presetOverride](https://github.com/Azure/azure-rest-api-specs/blob/ce90f9b45945c73b8f38649ee6ead390ff6efe7b/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L1960) 속성을 참조 하세요.

> [!NOTE]
> 미리 설정 된 재정의를 사용 하 여 변환에서 정의 된 사전 설정에 대 한 설정을 재정의할 수 있습니다.  특정 사전 설정에서 다른 유형으로 전환할 수 없습니다. 예를 들어, 기본 제공 콘텐츠 인식 인코딩 사전 설정을 사용 하 여 만든 변환을 재정의 하려고 하면 audio analyzer와 같은 다른 사전 설정을 사용 하는 경우 오류 메시지가 생성 됩니다.


## <a name="example-of-preset-override-in-net"></a>.NET의 미리 설정 된 재정의 예제

기본 오디오 분석기 변환과 함께 미리 설정 된 재정의를 사용 하는 방법을 보여 주는 Media Services 용 .net SDK를 사용 하는 전체 예제는 github에서 사용할 수 있습니다.
작업 출력의 미리 설정 된 재정의 속성을 사용 하는 방법에 대 한 자세한 내용은 [오디오 분석기 사전 설정으로 미디어 파일 분석](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer) 샘플을 참조 하세요.

## <a name="sample-code-of-preset-override-in-net"></a>.NET의 미리 설정 된 재정의 샘플 코드

[!code-csharp[Main](../../../media-services-v3-dotnet/AudioAnalytics/AudioAnalyzer/program.cs#PresetOverride)]

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Media Services를 사용하여 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [기본 제공 사전 설정을 사용하여 HTTPS URL에서 인코딩](job-input-from-http-how-to.md)
* [기본 제공 사전 설정을 사용하여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정 빌드](transform-custom-presets-how-to.md)
