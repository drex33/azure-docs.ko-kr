---
title: Azure Media Services 출력 메타데이터 스키마
description: 이 문서에서는 Azure Media Services v3 출력 메타데이터 스키마에 대한 개요를 제공합니다.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f26905366949c2c198e52f78bc7adb734cbb7f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612459"
---
# <a name="output-metadata"></a>출력 메타데이터

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

인코딩 작업은 일부 인코딩 태스크를 수행할 입력 자산(또는 자산)과 연결됩니다. 예를 들어 MP4 파일을 H.264 MP4 적응 비트 전송률 집합으로 인코딩하며, 미리 보기 이미지와 오버레이를 만듭니다. 태스크가 완료되는 즉시 출력 자산이 생성됩니다.  출력 자산에는 비디오, 오디오, 미리 보기, 기타 파일 등이 포함됩니다. 출력 자산에는 출력된 자산에 대한 메타데이터가 있는 파일도 포함됩니다. 메타데이터 JSON 파일의 이름은 `<source_file_name>_manifest.json` 형식을 따릅니다(예: `BigBuckBunny_manifest.json`). *_metadata.json을 검색하고 파일 내의 파일 경로 문자열을 쿼리하여 원본 파일 이름(잘림 없는)을 찾아야 합니다.

Media Services는 메타데이터를 생성하기 위해 선제적으로 입력 자산을 검색하지 않습니다. 입력 자산이 작업에서 처리되는 경우 입력 메타데이터는 아티팩트로만 생성됩니다. 따라서 이 아티팩트는 출력 자산에 기록됩니다. 다른 도구는 입력 자산 및 출력 자산에 대한 메타데이터를 생성하는 데 사용됩니다. 따라서 입력 메타데이터는 출력 메타데이터와 스키마가 약간 다를 수 있습니다.

이 문서에서는 출력 메타데이터(&lt;source_file_name&gt;_manifest.json)의 기초가 되는 JSON 스키마의 요소 및 형식에 대해 설명합니다. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

스키마 코드 및 JSON 예제는 이 문서의 끝에 있습니다.  

## <a name="assetfile"></a>AssetFile

인코딩 작업에 대한 AssetFile 항목의 컬렉션입니다.  

| 이름 | 설명 |
| --- | --- |
| **Sources** |이 AssetFile을 생성하기 위해 처리된 입력/원본 미디어 파일의 컬렉션입니다.<br />예: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 비디오 트랙이 포함될 수 있습니다. <br />[VideoTracks](#videotracks)를 참조하세요. |
| **AudioTracks**|각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 오디오 트랙이 포함될 수 있습니다. 이 요소는 이러한 모든 오디오 트랙의 컬렉션입니다.<br /> 자세한 내용은 [AudioTracks](#audiotracks)를 참조하세요. |
| **이름**<br />필수 |미디어 자산 파일 이름입니다. <br /><br />예: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **크기**<br />필수 |자산 파일의 크기(바이트)입니다. <br /><br />예: `"Size": 32414631`|
| **기간**<br />필수 |콘텐츠 재생 시간입니다. 자세한 내용은 [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html) 형식을 참조하세요. <br /><br />예: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 비디오 트랙이 포함될 수 있습니다. **VideoTracks** 요소는 모든 비디오 트랙의 컬렉션을 나타냅니다.  

| 이름 | 설명 |
| --- | --- |
| **ID**<br /> 필수 |이 비디오 트랙의 인덱스(0부터 시작)입니다.**참고:** 이 **ID** 가 반드시 MP4 파일에 사용되는 TrackID일 필요는 없습니다. <br /><br />예: `"Id": 1`|
| **FourCC**<br />필수 | Ffmpeg에서 보고하는 비디오 코덱 FourCC 코드입니다.  <br /><br />예: `"FourCC": "avc1" | "hev1" | "hvc1"`|
| **프로필** |H264 프로필입니다(H264 코덱에만 적용).  <br /><br />예: `"Profile": "High"` |
| **수준** |H264 수준입니다(H264 코덱에만 적용).  <br /><br />예: `"Level": "3.2"`|
| **Width**<br />필수 |인코딩된 비디오 너비(픽셀)입니다.  <br /><br />예: `"Width": "1280"`|
| **높이**<br />필수 |인코딩된 비디오 높이(픽셀)입니다.  <br /><br />예: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />필수|비디오 디스플레이 가로 세로 비율의 분자입니다.  <br /><br />예: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />필수 |비디오 디스플레이 가로 세로 비율의 분모입니다.  <br /><br />예: `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />필수 |.3f 형식으로 측정된 비디오 프레임 속도입니다.  <br /><br />예: `"Framerate": 29.970`|
| **Bitrate**<br />필수 |AssetFile에서 계산되는 초당 평균 비디오 비트 전송률입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 포함되지 않습니다.  <br /><br />예: `"Bitrate": 3551567`|
| **TargetBitrate**<br />필수 |인코딩 기본 설정을 통해 요청된 대로 이 비디오 트랙의 대상 평균 비트 전송률(bps)입니다. <br /><br />예: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

각각의 실제 AssetFile에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 오디오 트랙이 포함될 수 있습니다. **AudioTracks** 요소는 모든 오디오 트랙의 컬렉션을 나타냅니다.  

| 이름  | 설명 |
| --- | --- |
| **ID**<br />필수  |이 오디오 트랙의 인덱스(0부터 시작)입니다.**참고:** MP4 파일에 사용되는 TrackID일 필요는 없습니다.  <br /><br />예: `"Id": 2`|
| **Codec**  |오디오 트랙 코덱 문자열입니다.  <br /><br />예: `"Codec": "aac"`|
| **언어**|예: `"Language": "eng"`|
| **채널**<br />필수|오디오 채널 수입니다.  <br /><br />예: `"Channels": 2`|
| **SamplingRate**<br />필수 |오디오 샘플링 속도(샘플/초 또는 Hz)입니다.  <br /><br />예: `"SamplingRate": 48000`|
| **Bitrate**<br />필수 |AssetFile에서 계산되는 평균 오디오 비트 전송률(bps)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 포함되지 않습니다.  <br /><br />예: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>JSON 스키마 예제

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md)
