---
title: 라이브 이벤트 및 라이브 출력 개념
description: 이 항목에서는 Azure Media Services v3의 라이브 이벤트 및 라이브 출력에 대한 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 5269be27a0e31e9626cd26960092356bce0d8ff4
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635032"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Media Services의 라이브 이벤트 및 라이브 출력

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 제공할 수 있습니다. Media Services v3에서 라이브 스트리밍 이벤트를 설정하려면 이 문서에서 설명하는 개념을 이해해야 합니다.

> [!TIP]
> Media Services v2 API에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 엔터티는 v2의 **채널** 을 대체하고 **라이브 출력** 은 **프로그램** 을 대체합니다.

## <a name="live-events"></a>라이브 이벤트

[라이브 이벤트](/rest/api/media/liveevents)는 라이브 비디오 피드의 수집 및 처리를 담당합니다. 라이브 이벤트를 만들면 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 기본 및 보조 입력 엔드포인트가 생성됩니다. 원격 라이브 인코더는 [RTMP](https://www.adobe.com/devnet/rtmp.html) 또는 [부드러운 스트리밍](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)(조각난 MP4) 입력 프로토콜을 사용하여 이 입력 엔드포인트에 기여 피드를 전송합니다. RTMP 수집 프로토콜의 경우 콘텐츠를 기본(`rtmp://`)으로 전송하거나 유선으로 안전하게 암호화(`rtmps://`)하여 전송할 수 있습니다. 부드러운 스트리밍 수집 프로토콜의 경우 지원되는 URL 체계는 `http://` 또는 `https://`입니다.  

## <a name="live-event-types"></a>라이브 이벤트 유형

[라이브 이벤트](/rest/api/media/liveevents)는 *통과*(온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 전송) 또는 *라이브 인코딩*(온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 전송)으로 설정할 수 있습니다. 형식은 생성 중에 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)을 사용하여 설정됩니다.

* **LiveEventEncodingType.None**: 온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 보냅니다. 수집된 스트림은 추가적인 처리 없이 라이브 이벤트를 통과합니다. 통과 모드라고도 합니다.
* **LiveEventEncodingType.Standard**: 온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services는 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상 해상도인 경우 **Default720p** 사전 설정은 6개의 해상도/비트 전송률 쌍 집합을 인코딩합니다.
* **LiveEventEncodingType.Premium1080p**: 온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services는 여러 비트 전송률 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 전송률 쌍의 출력 집합을 지정합니다.

### <a name="pass-through"></a>통과

![Media Services를 사용한 통과 라이브 이벤트 예제 다이어그램](./media/live-streaming/pass-through.svg)

통과 **라이브 이벤트** 를 사용하는 경우 온-프레미스 라이브 인코더에 의존하여 다중 비트 전송률이 있는 비디오 스트림을 생성하고 이 스트림을 기여 피드로 하여 라이브 이벤트에 전송(RTMP 또는 조각난 MP4 프로토콜 사용)할 수 있습니다. 그러면 라이브 이벤트는 들어오는 비디오 스트림을 추가 처리 없이 제공합니다. 이러한 통과 라이브 이벤트는 장기 실행 라이브 이벤트 또는 연중무휴 선형 라이브 스트리밍에 최적화되어 있습니다. 이 유형의 라이브 이벤트를 만들 경우 None(LiveEventEncodingType.None)을 지정합니다.

H.264/AVC 또는 H.265/HEVC(원활한 수집만 해당) 비디오 코덱 및 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 4K의 해상도 및 초당 60프레임의 프레임 속도로 기여 피드를 전송할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison-reference.md)를 참조하세요.

> [!NOTE]
> 통과 방법을 사용하면 긴 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 세부 정보를 참조하세요.
>

[DVR을 사용한 라이브 이벤트](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214)에서 통과 라이브 이벤트를 만드는 .NET 코드 예제를 참조하세요.

### <a name="live-encoding"></a>라이브 인코딩  

![Media Services를 사용한 라이브 인코딩 예제 다이어그램](./media/live-streaming/live-encoding.svg)

Media Services에서 라이브 인코딩을 사용하는 경우 단일 비트 전송률 비디오를 기여 피드로 하여 라이브 이벤트에 전송(RTMP 또는 조각난 Mp4 프로토콜 사용)하도록 온-프레미스 라이브 인코더를 구성합니다. 그런 다음, 들어오는 단일 비트 전송률 스트림을 [다중 비트 전송률이 있는 비디오 스트림](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)으로 인코딩하고, MPEG-DASH, HLS, 부드러운 스트리밍 등과 같은 프로토콜을 통해 디바이스를 재생하기 위해 출력을 전달 가능하게 만들도록 라이브 이벤트를 설정합니다.

라이브 인코딩을 사용하는 경우 H.264/AVC 비디오 코덱과 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 1080p의 해상도 및 초당 30프레임의 프레임 속도로만 기여 피드를 전송할 수 있습니다. 통과 라이브 이벤트는 초당 60프레임에서 최대 4K의 해상도를 지원할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison-reference.md)를 참조하세요.

라이브 인코더의 출력에 포함되는 해상도 및 비트 전송률은 사전 설정에 따라 결정됩니다. **표준** 라이브 인코더(LiveEventEncodingType.Standard)를 사용하는 경우 *Default720p* 사전 설정은 3.5Mbps에서 720p부터 200kbps에서 192p까지 6개의 해상도/비트 전송률 쌍의 집합을 지정합니다. 그렇지 않고 **Premium1080p** 라이브 인코더(LiveEventEncodingType.Premium1080p)를 사용하는 경우 *Default1080p* 사전 설정은 3.5Mbps일 때 1080p부터 200kbps일 때 180p까지 6개의 해상도/비트 전송률 쌍 집합을 지정합니다. 자세한 내용은 [시스템 미리 설정](live-event-types-comparison-reference.md#system-presets)을 참조하세요.

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정해야 하는 경우 Azure Portal을 통해 지원 티켓을 엽니다. 원하는 해상도 및 비트 전송률 표를 지정합니다. 720p(표준 라이브 인코더에 대한 사전 설정을 요청하는 경우) 또는 1080p(Premium1080p 라이브 인코더에 대한 사전 설정을 요청하는 경우)에 레이어가 하나만 있는지 확인하세요. 최대 6개 레이어까지 가능합니다.

## <a name="creating-live-events"></a>라이브 이벤트 만들기

### <a name="options"></a>옵션

라이브 이벤트를 만들 때 다음 옵션을 지정할 수 있습니다.

* 라이브 이벤트에 이름과 설명을 지정할 수 있습니다.
* 클라우드 인코딩에는 통과(클라우드 인코딩 없음), 표준(최대 720p) 또는 프리미엄(최대 1080p)이 포함됩니다. 표준 및 프리미엄 인코딩의 경우 인코딩된 비디오의 스트레치 모드를 선택할 수 있습니다.
  * None: 입력 비디오의 픽셀 가로 세로 비율 또는 표시 가로 세로 비율을 고려하지 않고 인코딩 사전 설정에 지정된 출력 해상도를 엄격하게 따릅니다.
  * AutoSize: 출력 해상도를 재정의하고 안쪽 여백 없이 입력의 표시 가로 세로 비율에 맞게 변경합니다. 예를 들어, 입력이 1920x1080이고 인코딩 사전 설정에서 1280x1280을 요청하는 경우 사전 설정의 값이 재정의되고 출력이 1280x720이 되어 16:9의 입력 가로 세로 비율이 유지됩니다.
  * AutoFit: 출력의 안쪽 여백을 채워(레터박스 또는 필러박스 사용) 출력 해상도를 적용하는 동시에 출력의 활성 비디오 영역과 입력의 가로 세로 비율을 동일하게 유지합니다. 예를 들어 입력이 1920x1080이고 인코딩 사전 설정에서 1280x1280을 요청하는 경우 출력은 1280x1280이 되며, 가로 세로 비율이 16:9인 내부 1280x720 사각형과 왼쪽 및 오른쪽에 280픽셀 너비의 필러박스 영역을 포함합니다.
* 스트리밍 프로토콜(현재 RTMP 및 부드러운 스트리밍 프로토콜이 지원됨). 라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜이 필요한 경우 각 스트리밍 프로토콜에 대한 별도의 라이브 이벤트를 만드세요.
* 입력 ID(라이브 이벤트 입력 스트림의 GUID(Globally Unique Identifier)).
* 없음(이 경우 임의의 128비트 16진수 문자열이 사용됨), 라이브 이벤트 이름 사용 또는 사용자 지정 이름 사용을 포함하는 정적 호스트 이름 접두사.  고객 이름을 사용하도록 선택하는 경우 이 값은 사용자 지정 호스트 이름 접두사입니다.
* HLS 출력에서 각 미디어 세그먼트의 기간(초)에 해당하는 입력 키 프레임 간격을 설정하여 라이브 브로드캐스트와 재생 사이의 엔드투엔드 대기 시간을 줄일 수 있습니다. 값은 0.5 ~ 20초 범위의 0이 아닌 정수여야 합니다.  입력 또는 출력 키 프레임 간격을 *모두* 설정하지 않으면 기본값은 2초입니다. 키 프레임 간격은 통과 이벤트에서만 허용됩니다.
* 이벤트를 만드는 경우 해당 이벤트를 자동 시작하도록 설정할 수 있습니다. 자동 시작이 true로 설정되어 있는 경우 라이브 이벤트가 생성 후 시작됩니다. 라이브 이벤트가 실행되는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 또는 스트리밍을 시작할 준비가 되었을 때 이벤트를 시작해도 됩니다.

> [!NOTE]
> 최대 프레임 속도는 표준 및 프리미엄 인코딩에 대해 모두 30fps입니다.

## <a name="standby-mode"></a>대기 모드

라이브 이벤트를 만들 때 대기 모드로 설정할 수 있습니다. 이벤트가 대기 모드인 동안에는 설명, 정적 호스트 이름 접두사를 편집하고 입력 및 미리 보기 액세스 설정을 제한할 수 있습니다.  대기 모드는 여전히 청구 가능 모드이지만 라이브 스트림을 시작할 때와는 다르게 가격이 책정됩니다.

자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing-concept.md)를 참조하세요.

* 수집 및 미리 보기에서 IP 제한입니다. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.252.0)')로 지정할 수 있습니다.
<br/><br/>
지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 0.0.0.0/0으로 설정합니다.<br/>IP 주소는 4개의 숫자를 사용하는 IpV4 주소 또는 CIDR 주소 범위 형식 중 하나여야 합니다.
<br/><br/>
자신의 방화벽에서 특정 IP를 사용하도록 설정하거나 라이브 이벤트의 입력을 Azure IP 주소로 제한하려는 경우 [Azure 데이터 센터 IP 주소 범위](https://www.microsoft.com/download/details.aspx?id=41653)에서 JSON 파일을 다운로드합니다. 이 파일에 대한 자세한 내용은 페이지에서 **세부 정보** 섹션을 선택하세요.

* 이벤트를 만들 때 라이브 대화 내용 기록을 사용하도록 선택할 수 있습니다. 기본적으로 라이브 대화 내용 기록은 사용할 수 없습니다. 라이브 대화 내용 기록에 대한 자세한 내용은 [라이브 대화 내용 기록](live-event-live-transcription-how-to.md)을 참조하세요.

### <a name="naming-rules"></a>이름 지정 규칙

* 최대 라이브 이벤트 이름은 32자입니다.
* 이름은 `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`와 같은 [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 따라야 합니다.

[스트리밍 엔드포인트 명명 규칙](stream-streaming-endpoint-concept.md#naming-convention)도 참조하세요.

> [!TIP]
> GUID를 생성한 다음, 모든 하이픈 및 중괄호(있는 경우)를 제거하면 라이브 이벤트 이름의 고유성을 보장할 수 있습니다. 문자열이 모든 라이브 이벤트에서 고유하게 되며 길이는 32자로 유지됩니다.

## <a name="live-event-ingest-urls"></a>라이브 이벤트 수집 URL

라이브 이벤트가 생성되면 라이브 온-프레미스 인코더에 제공할 수집 URL을 구할 수 있습니다. 라이브 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다. 자세한 내용은 [권장 온-프레미스 라이브 인코더](encode-recommended-on-premises-live-encoders.md)를 참조하세요.

>[!NOTE]
> 2020-05-01 API 릴리스부터는 "베니티" URL을 정적 호스트 이름(useStaticHostname: true)이라고 합니다.


> [!NOTE]
> 수집 URL이 정적이고 예측 가능하여 하드웨어 인코더 설정에서 사용할 수 있도록 하려면 **useStaticHostname** 속성을 true로 설정하고 각 생성 시 **accessToken** 속성을 동일한 GUID로 설정합니다. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>정적(무작위 아님) 수집 RTMP URL에 대한 LiveEvent 및 LiveEventInput 구성 설정 예제

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* 비정적 호스트 이름

    비정적 호스트 이름은 **LiveEvent** 를 만들 때 Media Services v3의 기본 모드입니다. 라이브 이벤트는 약간 더 빠르게 할당되지만 라이브 인코딩 하드웨어 또는 소프트웨어에 필요한 수집 URL이 무작위로 지정됩니다. URL은 라이브 이벤트를 중지/시작하는 경우 변경됩니다. 비정적 호스트 이름은 최종 사용자가 라이브 이벤트를 매우 빠르게 가져와야 하는 앱을 사용하여 스트리밍하고 동적 수집 URL이 문제가 되지 않는 시나리오에서만 유용합니다.

    라이브 이벤트를 만들기 전에 클라이언트 앱에서 수집 URL을 미리 생성할 필요가 없는 경우 라이브 이벤트에 대한 액세스 토큰을 Media Services에서 자동으로 생성하도록 합니다.

* 정적 호스트 이름 

    정적 호스트 이름 모드는 특정 라이브 이벤트의 생성이나 중지/시작 시 변경되지 않는 RTMP 수집 URL을 사용하여 라이브 인코딩 하드웨어 또는 소프트웨어를 미리 구성하려는 대부분의 운영자가 선호합니다. 이들 운영자는 시간이 지나도 변경되지 않는 예측 RTMP 수집 URL을 원합니다. 또한 이 모드는 BlackMagic Atem Mini Pro와 같은 하드웨어 인코딩 디바이스 또는 유사한 하드웨어 인코딩 및 프로덕션 도구의 구성 설정에 정적 RTMP 수집 URL을 푸시해야 하는 경우에도 매우 유용합니다. 

    > [!NOTE]
    > Azure Portal에서는 정적 호스트 이름 URL을 "*정적 호스트 이름 접두사*"라고 합니다.

    API에서 이 모드를 지정하려면 생성 시 `useStaticHostName`을 `true`로 설정합니다(기본값은 `false`). `useStaticHostname`이 true로 설정되면 `hostnamePrefix`는 라이브 이벤트 미리 보기에 할당된 호스트 이름 중 첫 번째 부분과 수집 엔드포인트를 지정합니다. 최종 호스트 이름은 이 접두사, 미디어 서비스 계정 이름 및 Azure Media Services 데이터 센터에 대한 간단한 코드의 조합입니다.

    URL에서 임의의 토큰을 방지하려면 생성 시 사용자 고유의 액세스 토큰(`LiveEventInput.accessToken`)도 전달해야 합니다.  액세스 토큰은 유효한 GUID 문자열(하이픈 포함 여부는 관계없음)이어야 합니다. 모드가 설정된 후에는 업데이트할 수 없습니다.

    액세스 토큰은 Azure 지역 및 Media Services 계정에서 고유해야 합니다. 앱에서 정적 호스트 이름 수집 URL을 사용해야 하는 경우에는 항상 지역, 미디어 서비스 계정 및 라이브 이벤트의 특정 조합으로 사용할 새 GUID 인스턴스를 만드는 것이 좋습니다.

    다음 API를 사용하여 정적 호스트 이름 URL을 사용하도록 설정하고 액세스 토큰을 유효한 GUID(예: `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`)로 설정합니다.  

    |언어|정적 호스트 이름 URL을 사용하도록 설정|액세스 토큰 설정|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>라이브 수집 URL 명명 규칙

* 아래 *임의* 문자열은 128비트 16진수 숫자입니다(0-9 a-f의 32문자로 구성됨).
* *your access token*: 정적 호스트 이름 설정을 사용하는 경우 설정하는 유효한 GUID 문자열입니다. 예: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *stream name*: 특정 연결에 대한 스트림 이름을 나타냅니다. 스트림 이름 값은 일반적으로 사용하는 라이브 인코더에 의해 추가됩니다. 연결을 설명하는 이름을 사용하도록 라이브 인코더를 구성할 수 있습니다(예: "video1_audio1", "video2_audio1", "stream").

#### <a name="non-static-hostname-ingest-url"></a>비정적 호스트 이름 수집 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>부드러운 스트리밍

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>정적 호스트 이름 수집 URL

다음 경로에서 `<live-event-name>`은 이벤트에 지정된 이름 또는 라이브 이벤트를 만들 때 사용된 사용자 지정 이름을 의미합니다.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>부드러운 스트리밍

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>라이브 이벤트 미리 보기 URL

라이브 이벤트가 기여 피드를 수신하기 시작하면 미리 보기 엔드포인트를 사용하여 추가 게시 전에 수신 중인 라이브 스트림을 미리 보고 유효성을 검사할 수 있습니다. 미리 보기 스트림이 양호한지 확인한 후에는 라이브 이벤트를 사용하여 하나 이상의 (미리 생성된) 스트리밍 엔드포인트를 통해 라이브 스트림을 전달 가능하도록 만들 수 있습니다. 이를 수행하려면 라이브 이벤트에서 새 [라이브 출력](/rest/api/media/liveoutputs)을 만듭니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

## <a name="live-event-long-running-operations"></a>라이브 이벤트 장기 실행 작업

자세한 내용은 [장기 실행 작업](media-services-apis-overview.md#long-running-operations)을 참조하세요.

## <a name="live-outputs"></a>라이브 출력

라이브 이벤트로 들어오는 스트림이 있으면 [자산](/rest/api/media/assets), [라이브 출력](/rest/api/media/liveoutputs) 및 [스트리밍 로케이터](/rest/api/media/streaminglocators)를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다.  

라이브 출력에 대한 자세한 내용은 [클라우드 DVR 사용](live-event-cloud-dvr-time-how-to.md)을 참조하세요.
## <a name="live-event-output-questions"></a>라이브 이벤트 출력 관련 질문

[FAQ의 라이브 이벤트 질문](frequently-asked-questions.yml)을 참조하세요.
