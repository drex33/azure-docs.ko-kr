---
title: 대규모 Azure Video Analyzer for Media(이전의 Video Indexer)를 사용할 때 고려해야 할 사항 - Azure
titleSuffix: Azure Video Analyzer for Media
description: 이 항목에서는 대규모 Azure Video Analyzer for Media(이전의 Video Indexer)를 사용할 때 고려해야 할 사항에 대해 설명합니다.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: how-to
ms.subservice: azure-video-analyzer-media
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: 8784b82c59575a569730949d71473027cd30479a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566649"
---
# <a name="things-to-consider-when-using-video-analyzer-for-media-at-scale"></a>Video Analyzer for Media를 대규모로 사용 시 고려 사항

Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하여 비디오를 인덱싱하고 비디오 보관이 증가하는 경우 크기를 조정하는 것이 좋습니다. 

이 문서에서는 다음과 같은 질문에 응답합니다.

* 고려해야 할 기술 제약 조건이 있나요?
* 작업을 수행하는 스마트하고 효율적인 방법이 있나요?
* 프로세스에서 과도한 비용 지출을 방지할 수 있나요?

이 문서에서는 대규모 Video Analyzer for Media를 사용하는 방법에 대한 6가지 모범 사례를 제공합니다.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>비디오를 업로드하는 경우 바이트 배열보다 URL을 사용하는 것이 좋습니다.

Video Analyzer for Media는 비디오를 URL에서 업로드하거나 파일을 바이트 배열로 전송하여 직접 업로드할 수 있으며 후자에는 몇 가지 제약 조건이 적용됩니다. 자세한 내용은 [업로드 고려 사항 및 제한 사항](upload-index-videos.md#uploading-considerations-and-limitations)을 참조하세요.

첫째, 파일 크기 제한 사항이 있습니다. URL을 사용하는 동안 업로드 크기가 30GB로 제한되는 것에 비해 바이트 배열 파일의 크기는 2GB로 제한됩니다.

둘째, 성능에 영향을 줄 수 있는 몇 가지 문제와 이어서 스케일링하는 기능만 고려합니다.

* 다중 파트를 사용하여 파일을 보내는 것은 네트워크에 대한 높은 종속성을 의미합니다. 
* 서비스 안정성, 
* 연결, 
* 업로드 속도, 
* World Wide Web의 어딘가에서 손실된 패킷.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="대규모 Video Analyzer for Media 사용을 위한 첫 번째 고려 사항":::

URL을 사용하여 비디오를 업로드하는 경우 미디어 파일 위치의 경로를 제공하면 되고 나머지는 Video Indexer가 처리합니다([비디오 업로드](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API에서 `videoUrl` 필드 참조).

> [!TIP]
> 비디오 업로드 API의 `videoUrl` 선택적 매개 변수를 사용합니다.

URL을 사용하여 비디오를 업로드하는 방법의 예제를 보려면 [이 예제](upload-index-videos.md#code-sample)를 확인하세요. 또는 [SAS URL](../../storage/common/storage-sas-overview.md)을 사용하여 Video Analyzer for Media에 콘텐츠를 제출할 수 있는 스토리지 계정에 콘텐츠를 가져오는 빠르고 안정적인 방법으로 [AzCopy](../../storage/common/storage-use-azcopy-v10.md)를 사용할 수 있습니다. Video Analyzer for Media는 *읽기 전용* SAS URL 사용을 권장합니다.

## <a name="automatic-scaling-of-media-reserved-units"></a>미디어 예약 단위의 자동 크기 조정 

2021년 8월 1일부터 Azure Video Analyzer for Media(이전 이름 Video Indexer)는 [AMS(Azure Media Services)](../../media-services/latest/media-services-overview.md)에 의한 [예약 단위](../../media-services/latest/concept-media-reserved-units.md)(MRU) 자동 스케일링을 사용하도록 설정했습니다. 따라서 이제 Azure Video Analyzer for Media를 통해 관리할 필요가 없습니다. 따라서 자동 스케일링이 수행되므로, 비즈니스 요구 사항에 따라 가격 최적화(예: 많은 경우 가격 인하)가 가능합니다. 

## <a name="respect-throttling"></a>제한 준수

Video Analyzer for Media는 대규모 인덱싱을 처리하도록 제작되었으며, 이를 최대한 활용하려면 시스템의 기능을 파악하고 그에 따라 통합을 설계해야 합니다. 일부 동영상이 업로드되지 않았는지 검색하기 위한 목적만으로 비디오 일괄 처리를 위한 업로드 요청을 보내면 안 됩니다. 그러면 HTTP 429 응답 코드(너무 많은 요청)가 표시됩니다. 이 문제는 [지원하는 분당 동영상 한도](upload-index-videos.md#uploading-considerations-and-limitations)보다 많은 요청을 보냈다는 사실로 인해 발생할 수 있습니다. Video Analyzer for Media는 HTTP 응답에 `retry-after` 헤더를 추가하며 헤더는 다음에 다시 시도해야 하는 경우를 지정합니다. 다음 요청을 시도하기 전에 제한을 준수하는지 확인합니다.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="적절하게 통합 디자인, 제한 준수":::

## <a name="use-callback-url"></a>콜백 URL 사용

업로드 요청을 보낸 때부터 지속적으로 요청 상태를 폴링하는 대신 [콜백 URL](upload-index-videos.md#callbackurl)을 추가하고 Video Analyzer for Media가 업데이트될 때까지 대기할 수 있습니다. 업로드 요청에서 상태가 변경되는 즉시 지정한 URL에 대한 POST 알림을 받습니다.

콜백 URL은 [비디오 업로드 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)의 매개 변수 중 하나로 추가할 수 있습니다. [GitHub 리포지토리](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)에서 코드 샘플을 확인하세요. 

콜백 URL의 경우 HTTP를 통해 트리거되고 다음 흐름을 구현할 수 있는 서버리스 이벤트 기반 플랫폼인 Azure Functions를 사용할 수도 있습니다.

### <a name="callback-url-definition"></a>콜백 URL 정의

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>적합한 인덱싱 매개 변수 사용

Video Analyzer for Media를 대규모로 사용하는 것과 관련된 결정을 내릴 때 요구 사항에 적합한 매개 변수를 사용하여 Video Indexer를 최대한 활용하는 방법을 살펴봅니다. 다양한 매개 변수를 정의하여 비용을 절감하고 비디오의 인덱싱 프로세스를 더 빠르게 만들 수 있는 사용 사례를 살펴봅니다.

비디오를 업로드하고 인덱싱하기 전에 이 간단한 [설명서](upload-index-videos.md)를 읽고 [indexingPreset](upload-index-videos.md#indexingpreset) 및 [streamingPreset](upload-index-videos.md#streamingpreset)을 확인하여 옵션을 더 잘 이해해 봅니다.

예를 들어, 비디오를 시청하지 않으려는 경우 사전 설정을 스트리밍으로 설정하지 않으며, 오디오 인사이트만 필요한 경우 비디오 인사이트를 인덱싱하지 않습니다.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>가장 높은 해상도가 아닌 최적 해상도로 인덱싱

비디오 인덱싱에 필요한 비디오 품질은 무엇인가요?라는 질문을 할 수 있습니다. 

대부분의 경우 인덱싱 성능은 HD(720P) 비디오와 4K 비디오 간에 차이가 거의 없습니다. 결국 동일한 신뢰도로 거의 동일한 인사이트를 얻을 수 있습니다. 업로드하는 동영상의 품질이 높을수록 파일 크기가 커지며 이로 인해 비디오를 업로드하는 데 필요한 컴퓨팅 성능과 시간이 증가합니다.

예를 들어, 얼굴 감지 기능의 경우 더 높은 해상도를 사용하면 작지만 맥락상 중요한 얼굴이 많이 있는 시나리오에 도움이 될 수 있습니다. 그러나 이로 인해 런타임이 2차 증가하고 가양성의 위험이 증가합니다.

따라서 사용 사례에 적합한 결과를 얻는지 확인하고 먼저 로컬로 테스트하는 것이 좋습니다. 동일한 비디오를 720P 및 4K로 업로드하고 얻은 인사이트를 비교합니다.

## <a name="next-steps"></a>다음 단계

[API에서 생성된 Azure Video Analyzer for Media 출력 검사](video-indexer-output-json-v2.md)