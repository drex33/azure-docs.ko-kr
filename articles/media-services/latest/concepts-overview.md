---
title: Media Services 용어 및 개념
description: Azure Media Services에 대한 용어 및 개념에 대해 알아봅니다.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 876e4c33f2f2b848b8155ef0be1650d75e1e9f49
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538830"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services 용어 및 개념

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 항목에서는 Azure Media Services 용어 및 개념에 대한 간략한 개요를 제공합니다. 또한 이 문서에서는 Media Services v3 개념 및 기능에 대해 자세히 설명하는 문서에 대한 링크도 제공합니다.

이 항목에는 개발을 시작하기 전에 검토해야 하는 기본 개념이 설명되어 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 용어

|용어|설명|
|---|---|
|라이브 이벤트|**라이브 이벤트** 는 비디오, 오디오 및 실시간 메타데이터의 라이브 스트림을 수집, 코드 변환(선택 사항) 및 패키징하기 위한 파이프라인을 나타냅니다.<br/><br/>Media Services v2 API에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 는 v2의 **채널** 엔터티를 대체합니다. 자세한 내용은 [v2에서 v3으로 마이그레이션](migrate-v-2-v-3-migration-introduction.md)을 참조하세요.|
|스트리밍 엔드포인트/패키징/원본|**스트리밍 엔드포인트** 는 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 애플리케이션에 직접 전송할 수 있는 원본 서비스와 동적(Just-In-Time) 패키징을 나타냅니다. 일반 스트리밍 미디어 프로토콜(HLS 또는 DASH) 중 하나를 사용합니다. 또한 **스트리밍 엔드포인트** 는 업계 최고의 DRM(디지털 권한 관리 시스템)에 대한 동적(Just-In-Time) 암호화를 제공합니다.<br/><br/>미디어 스트리밍 업계에서는 이 서비스를 일반적으로 **패키지 작성 도구** 또는 **원본** 이라고 합니다.  이 기능에 대한 업계의 다른 일반적인 용어에는 JITP(Just-In-Time 패키지 작성 도구) 또는 JITE(Just-In-Time 암호화)가 포함됩니다.

## <a name="media-services-v3-concepts"></a>Media Services v3 개념

|개념|Description|링크|
|---|---|---|
|자산 및 콘텐츠 업로드|Azure에서 미디어 콘텐츠를 관리, 암호화, 인코딩, 분석 및 스트리밍하려면 Media Services 계정을 만들고 디지털 파일을 **자산** 에 업로드해야 합니다.|[클라우드 업로드 및 스토리지](storage-account-concept.md)<br/><br/>[자산 개념](assets-concept.md)|
|콘텐츠 인코딩|고품질 디지털 미디어 파일을 자산에 업로드하면 다양한 브라우저와 디바이스에서 재생할 수 있는 형식으로 인코딩할 수 있습니다. <br/><br/>Media Services v3를 사용하여 인코딩하려면 **Transforms** 및 **Jobs** 를 만들어야 합니다.|[Transforms 및 Jobs](transform-jobs-concept.md)<br/><br/>[Media Services를 사용하여 인코딩](encode-concept.md)|
|콘텐츠 분석(미디어용 비디오 분석기)|Media Services v3를 사용하면 Media Services v3 기본 설정을 사용하여 비디오 및 오디오 파일에서 인사이트를 추출할 수 있습니다. Media Services v3 기본 설정을 사용하여 콘텐츠를 분석하려면 **Transforms** 및 **Jobs** 를 만들어야 합니다.<br/><br/>더 자세한 인사이트가 필요한 경우 [미디어용 비디오 분석기](../../azure-video-analyzer/video-analyzer-for-media-docs/index.yml)를 직접 사용합니다.|[비디오 및 오디오 파일 분석](analyze-video-audio-files-concept.md)|
|패키징 및 제공|콘텐츠가 인코딩되면 **동적 패키징** 을 활용할 수 있습니다. Media Services에서 **스트리밍 엔드포인트** 는 클라이언트 플레이어에 미디어 콘텐츠를 전송하는 데 사용되는 동적 패키징 서비스입니다. 출력 자산의 비디오를 재생용 클라이언트에 사용할 수 있도록 하려면 **스트리밍 로케이터** 를 만든 다음, 스트리밍 URL을 빌드해야 합니다. <br/><br/>**스트리밍 로케이터** 를 만들 때 자산 이름 외에도 **스트리밍 정책** 을 지정해야 합니다. **스트리밍 정책** 을 사용하면 **스트리밍 로케이터** 에 대한 스트리밍 프로토콜 및 암호화 옵션(있는 경우)을 정의할 수 있습니다. 동적 패키징은 콘텐츠를 라이브 또는 주문형으로 스트리밍할 때 사용됩니다. <br/><br/>Media Services **동적 매니페스트** 를 사용하여 비디오의 특정 변환이나 하위 클립을 스트리밍할 수 있습니다. 또한 미리 인코딩된 콘텐츠가 있거나 타사 인코더로 이미 인코딩된 콘텐츠가 있는 경우 AMS 원본 서비스를 사용하여 콘텐츠를 스트림할 수 있습니다. 사전 인코딩된 원본 파일을 사용하는 예는 샘플 - [기존 MP4 스트리밍](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamExistingMp4)을 참조하세요. |[동적 패키징](encode-dynamic-packaging-concept.md)<br/><br/>[스트리밍 엔드포인트](stream-streaming-endpoint-concept.md)<br/><br/>[스트리밍 로케이터](stream-streaming-locators-concept.md)<br/><br/>[스트리밍 정책](stream-streaming-policy-concept.md)<br/><br/>[동적 매니페스트](filters-dynamic-manifest-concept.md)<br/><br/>[필터](filters-concept.md)|
|콘텐츠 보호|Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine, Apple FairPlay 등 세 가지 주요 DRM 시스템 중 하나로 동적으로 암호화된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. <br/><br/>스트림에 대한 암호화 옵션을 지정하려면 **콘텐츠 키 정책** 을 만들고 해당 정책을 **스트리밍 로케이터** 에 연결합니다. **콘텐츠 키 정책** 을 사용하여 콘텐츠 키를 최종 클라이언트에 전송하는 방법을 구성할 수 있습니다.<br/><br/> 동일한 옵션이 필요할 때마다 정책을 다시 사용하세요.| [콘텐츠 키 정책](drm-content-key-policy-concept.md)<br/><br/>[콘텐츠 보호](drm-content-protection-concept.md)|
|라이브 스트리밍|Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전송할 수 있습니다. **라이브 이벤트** 는 라이브 비디오 피드 수집 및 처리를 담당합니다. **라이브 이벤트** 를 만들면 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 입력 엔드포인트가 생성됩니다. **라이브 이벤트** 로 들어오는 스트림이 있으면 **자산**, **라이브 출력** 및 **스트리밍 로케이터** 를 만들어 스트리밍 이벤트를 시작할 수 있습니다. **라이브 출력** 은 스트림을 **자산** 에 보관하고 **스트리밍 엔드포인트** 를 통해 시청자가 스트림을 사용할 수 있도록 합니다. 라이브 이벤트는 *통과*(온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 전송) 또는 *라이브 인코딩*(온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 전송)으로 설정할 수 있습니다. |[라이브 스트리밍 개요](stream-live-streaming-concept.md)<br/><br/>[라이브 이벤트 및 라이브 출력](live-event-outputs-concept.md)|
|Event Grid를 사용하여 모니터링|작업의 진행률을 보려면 **Event Grid** 를 사용합니다. Media Services는 라이브 이벤트 유형도 내보냅니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. |[Event Grid 이벤트 처리](monitoring/reacting-to-media-services-events.md)<br/><br/>[스키마](monitoring/media-services-event-schemas.md)|
|Azure Monitor를 사용하여 모니터링|Azure Monitor를 사용하여 앱의 성능을 파악하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링합니다.|[메트릭 및 진단 로그](monitoring/monitor-media-services-data-reference.md)<br/><br/>[진단 로그 스키마](monitoring/monitor-media-services-data-reference.md)|
|플레이어 클라이언트|HLS 또는 DASH 스트리밍 프로토콜을 지원하는 모든 플레이어 프레임워크를 사용할 수 있습니다. 시장에는 많은 오픈 소스 및 상용 플레이어(Shaka, Hls.js, Video.js, Theo Player, Bitmovin Player 등)가 있으며 HLS 및 DASH에 대한 기본 제공 브라우저 및 OS 수준 스트리밍 지원이 있습니다.  Azure Media Player를 사용하여 Media Services에서 스트리밍한 미디어 콘텐츠를 다양한 브라우저에서 재생할 수도 있습니다. Azure Media Player는 적응 스트리밍 환경을 제공할 수 있는 HTML5, MSE(미디어 원본 확장) 및 EME(암호화된 미디어 확장)와 같은 업계 표준을 사용합니다. |[Azure Media Player 개요](player-use-azure-media-player-how-to.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [원격 파일 및 스트림 비디오 인코딩 – REST](stream-files-tutorial-with-rest.md)
* [업로드된 파일 및 스트림 비디오 인코딩 – .NET](stream-files-tutorial-with-api.md)
* [라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
* [비디오 분석 - .NET](analyze-videos-tutorial.md)
* [AES-128 동적 암호화 - .NET](drm-playready-license-template-concept.md)
* [다중 DRM으로 동적으로 암호화 - .NET](drm-protect-with-drm-tutorial.md)
