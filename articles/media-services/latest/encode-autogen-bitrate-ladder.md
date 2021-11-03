---
title: Media Services에서 표준 인코더를 사용하여 비디오 인코딩
description: 이 항목은 Media Services의 표준 인코더를 사용하여 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리로 입력 비디오를 인코딩하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 058d80b85a9a669612c64de6f0f705f6d605e419
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065022"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>자동 생성된 비트 전송률 사다리로 인코딩

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>개요

이 문서는 Media Services의 표준 인코더를 사용하여 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리로 입력 비디오를 인코딩하는 방법을 설명합니다. 이 기본 제공 인코더 설정 또는 사전 설정은 입력 해상도 및 비트 전송률을 결코 초과하지 않습니다. 예를 들어, 입력이 3Mbps에서 720p이고 출력이 최적 시 720p로 유지되는 경우 3Mbps보다 낮은 전송률로 시작됩니다.

### <a name="encoding-for-streaming"></a>스트리밍용 인코딩

**변환** 에서 **AdaptiveStreaming** 또는 **H265AdaptiveStreaming** 사전 설정을 사용 하는 경우 HLS 및 대시 같은 스트리밍 프로토콜을 통해 배달에 적합 한 출력을 얻게 됩니다. 이러한 두 가지 사전 설정 중 하나를 사용 하는 경우 서비스는 생성할 비디오 계층 수와 비트 전송률 및 해상도를 지능적으로 결정 합니다. 출력 내용에는 AAC로 인코딩된 오디오와 h.264로 인코딩된 비디오 (AdaptiveStreaming 사전 설정의 경우) 또는 265/HEVC (H265AdaptiveStreaming 기본 설정의 경우) 인 MP4 파일이 포함 됩니다. 출력 MP4 파일은 인터리브 되지 않습니다.

이 사전 설정이 사용된 예를 보려면 [파일 스트리밍](stream-files-dotnet-quickstart.md)을 참조하세요.

## <a name="output"></a>출력

이 섹션에서는 **AdaptiveStreaming**(h.264) 또는 **H265AdaptiveStreaming** (HEVC) 사전 설정으로 인코딩된 결과로 Media Services 인코더에서 생성 되는 출력 비디오 계층의 세 가지 예를 보여 줍니다. 모든 경우에 출력에는 128kbps로 인코딩된 스테레오 오디오와 오디오 전용 MP4 파일이 포함됩니다.

### <a name="example-1"></a>예 1
높이가 "1080"이고 프레임 속도가 "29.970"인 원본은 6개의 비디오 계층을 생성합니다.

|계층|높이|너비|비트 전송률(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>예제 2
높이가 "720"이고 프레임 속도가 "23.970"인 원본은 5개의 비디오 계층을 생성합니다.

|계층|높이|너비|비트 전송률(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>예제 3
높이가 "360"이고 프레임 속도가 "29.970"인 원본은 3개의 비디오 계층을 생성합니다.

|계층|높이|너비|비트 전송률(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|


## <a name="content-aware-encoding-comparison"></a>내용 인식 인코딩 비교

[콘텐츠 인식 인코딩 사전 설정은](./encode-content-aware-concept.md) 사다리에서 사용할 올바른 출력 비트 전송률 및 해상도를 결정 하기 전에 원본 콘텐츠를 분석 하 여 적응 스트리밍 사전 설정 보다 더 나은 솔루션을 제공 합니다.
적응 비트 전송률 스트리밍 사전 설정에서 제공 하는 더 정적 및 고정 사다리를 사용 하기 전에 먼저 [콘텐츠 인식 인코딩 미리 설정을](./encode-content-aware-concept.md) 테스트 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [파일 스트림](stream-files-dotnet-quickstart.md) 
>  [내용 인식 인코딩 미리 설정 사용](./encode-content-aware-concept.md) 
>  [내용 인식 인코딩을 사용 하는 방법](./encode-content-aware-how-to.md)
