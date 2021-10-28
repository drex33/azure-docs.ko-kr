---
제목: 라이브 전사: Azure Media Services 설명: Azure Media Services 라이브 전사에 관한 자세한 정보.  
서비스: media-services documentationcenter: '' 작성자: IngridAtMicrosoft 관리자: femila 편집자: '' ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: ne ms.topic: how-to ms.topic: 2020년 8월 31일 ms.author: inhenkel

---

# <a name="live-transcription-preview"></a>라이브 전사 미리 보기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 미디어 서비스는 다양한 프로토콜에서 비디오, 오디오, 텍스트를 제공합니다. MPEG-DASH 또는 HLS/CMAF를 사용하여 라이브 스트림을 게시하면 비디오 및 오디오와 함께 서비스가 IMSC1.1 호환 TTML로 기록된 텍스트를 제공합니다. 배달은 MPEG-4 Part 30(ISO/IEC 14496-30) 조각으로 패키지됩니다. HLS/TS를 통해 배달을 사용하는 경우 텍스트는 청크 분할 VTT로 배달됩니다.

라이브 전사를 켜면 추가 요금이 적용됩니다. [Media Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/)의 라이브 비디오 섹션에서 가격 정보를 검토하세요.

이 문서에서는 Azure Media Services 사용하여 라이브 이벤트를 스트리밍할 때 라이브 전사를 사용하는 방법을 설명합니다. 계속하기 전에 Media Services v3 REST API를 사용하는 방법을 잘 알고 있어야 합니다. 자세한 내용은 [이 자습서](stream-files-tutorial-with-rest.md)를 참조하세요. 또한 [라이브 스트리밍](stream-live-streaming-concept.md) 개념에 관해 잘 알고 있어야 합니다. [Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md) 자습서를 완료하는 것이 좋습니다.

## <a name="live-transcription-preview-regions-and-languages"></a>라이브 전사 미리 보기 지역 및 언어

다음 지역에서 라이브 전사를 사용할 수 있습니다.

- 동남 아시아
- 서유럽
- 북유럽
- 미국 동부
- 미국 중부
- 미국 중남부
- 미국 서부 2
- 브라질 남부

전사할 수 있는 언어 목록으로, API 언어 코드를 사용합니다.

| 언어 | 언어 코드 |
| -------- | ------------- |
| 카탈로니아어  | ca-ES |
| 덴마크어(덴마크) | da-DK |
| 독일어(독일) | de-DE |
| 영어(오스트레일리아) | en-AU |
| 영어(캐나다) | en-CA |
| 영어(영국) | en-GB |
| 영어(인도) | en-IN |
| 영어(뉴질랜드) | en-NZ |
| 영어(미국) | ko-KR |
| 스페인어(스페인) | es-ES |
| 스페인어(멕시코) | es-MX |
| 핀란드어(핀란드) | fi-FI |
| 프랑스어(캐나다) | fr-CA |
| 프랑스어(프랑스) | fr-FR |
| 이탈리아어(이탈리아) | it-IT |
| 네덜란드어(네덜란드) | nl-NL |
| 포르투갈어(브라질) | pt-BR |
| 포르투갈어(포르투갈) | pt-PT |
| 스웨덴어(스웨덴) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>라이브 전사를 사용하여 라이브 이벤트 만들기

전사를 켠 상태에서 라이브 이벤트를 만들려면 2019-05-01-미리 보기 API 버전을 사용하여 PUT 작업을 보냅니다. 예를 들면 다음과 같습니다.

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

작업에는 다음 본문이 있습니다(여기서 기본 통과 라이브 이벤트는 RTMP를 사용하여 생성되는 프로토콜). 전사 속성이 추가되었습니다.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "PassthroughBasic"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>라이브 이벤트가 시작된 후 전사를 시작하거나 중지합니다.

라이브 이벤트가 실행 상태일 때 라이브 전사를 시작하고 중지할 수 있습니다. 라이브 이벤트를 시작 및 중지하는 방법에 관한 자세한 내용은 [Media Services v3 API로 개발](media-services-apis-overview.md#long-running-operations)에서 장기 실행 작업 섹션을 참조하세요.

라이브 이벤트를 켜거나 전사 언어를 업데이트하려면 라이브 이벤트를 패치하여 "전사" 속성을 포함합니다. 실시간 이벤트를 끄려면 라이브 이벤트 개체에서 "전사" 속성을 제거합니다.  

> [!NOTE]
> 라이브 이벤트 중에 전사를 **두 번 이상** 켜거나 끄는 것은 지원되는 시나리오가 아닙니다.

라이브 전사를 켜는 샘플 호출입니다.

PATCH: ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>전사 배달 및 재생

서비스에서 동적 패키징을 사용하여 다양한 프로토콜에서 비디오, 오디오, 텍스트를 배달하는 방법을 위한 [동적 패키징 개요](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery) 문서를 검토합니다. MPEG-DASH 또는 HLS/CMAF를 사용하여 라이브 스트림을 게시하면 비디오 및 오디오와 함께 서비스가 IMSC1.1 호환 TTML로 기록된 텍스트를 제공합니다. 이 배달은 MPEG-4 Part 30(ISO/IEC 14496-30) 조각으로 패키지됩니다. HLS/TS를 통해 배달을 사용하는 경우 텍스트는 청크 분할된 VTT로 배달됩니다. [Azure Media Player](player-use-azure-media-player-how-to.md)와 같은 웹 플레이어를 사용하여 스트림을 재생할 수 있습니다.  

> [!NOTE]
> Azure Media Player 사용하는 경우 2.3.3 버전 이상을 사용합니다.

## <a name="known-issues"></a>알려진 문제

미리 보기에서 라이브 전사의 알려진 문제는 다음과 같습니다.

- 앱은 [Media Services v3 OpenAPI 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)에 설명된 미리 보기 API를 사용해야 합니다.
- DRM(디지털 권한 관리) 보호는 텍스트 트랙에는 적용되지 않으며 AES 봉투 암호화만 가능합니다.

## <a name="next-steps"></a>다음 단계

* [Media Services 개요](media-services-overview.md)
