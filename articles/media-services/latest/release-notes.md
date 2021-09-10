---
title: Azure Media Services v3 릴리스 정보
description: 최신 개발 정보를 확인할 수 있도록 이 문서에서는 Azure Media Services v3의 최신 업데이트에 대한 정보를 제공합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.custom: references_regions
ms.topic: article
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 46ebdd1f5cb3093b0c1c1a5bc3273cf1aa1afd8f
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634830"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 릴리스 정보

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="july-2021"></a>2021년 7월

### <a name="net-sdk-microsoftazuremanagementmedia--500-release-available-in-nuget-coming-soon---early-september-2021"></a>.NET SDK(Microsoft.Azure.Management.Media) 5.0.0 릴리스를 NuGet에서 사용할 수 있습니다(출시 예정 - 2021년 9월 초)

[Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/5.0.0) .NET SDK 버전 5.0.0이 이제 NuGet에서 출시되었습니다. 이 버전은 Open API(Swagger) ARM Rest API의 [2021-06-01 안정](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01) 버전에서 작동하도록 생성되었습니다.

4\.0.0 릴리스의 변경 사항에 대한 자세한 내용은 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/mediaservices/Microsoft.Azure.Management.Media/CHANGELOG.md)를 참조하세요.

#### <a name="changes-in-the-500-net-sdk-release-coming-soon---early-september-2021"></a>5\.0.0 .NET SDK 릴리스의 변경 내용(출시 예정 - 2021년 9월 초)

* 이제 Media Services 계정에서 시스템 및 사용자가 할당한 관리 ID를 지원합니다.
* Media Services 계정에 **PublicNetworkAccess** 옵션을 추가했습니다. 이 옵션은 Private Link 기능과 함께 사용하여 개인 네트워크의 액세스만 허용하고 모든 공용 네트워크 액세스를 차단할 수 있습니다.
* 기본 통과 - 새로운 라이브 이벤트 유형이 추가됩니다. “기본 통과” 라이브 이벤트는 일부 입력 및 출력 제한이 있는 표준 통과 라이브 이벤트와 유사한 기능을 가지며 할인된 가격으로 제공됩니다.
* **PresetConfigurations** - 출력 설정과 [Content Aware Encoding 사전 설정](./encode-content-aware-concept.md)에 사용되는 최소 및 최대 비트 전송률을 사용자 지정할 수 있습니다. 이렇게 하면 제한된 출력 트랙 수와 해상도를 통해 Content Aware Encoding을 사용할 때 더 정확한 청구 비용을 예측하고 계획할 수 있습니다.

#### <a name="breaking-changes-in-tht-500-net-sdk-release"></a>5\.0.0 .NET SDK 릴리스의 호환성이 손상되는 변경

* **ApiErrorException** 은 다른 모든 Azure SDK와 일관성을 유지하기 위해 **ErrorResponseException** 으로 바뀌었습니다. 예외 본문이 변경되지 않았습니다.
* 미디어 서비스 생성자에는 KeyDelivery 매개 변수 뒤에 새로운 선택적 PublicNetworkAccess 매개 변수가 있습니다.
* MediaServiceIdentity의 유형 속성은 쉼표로 구분된 여러 유형을 수용하기 위해 ManagedIdentityType 열거형에서 문자열로 변경되었습니다. 유형에 유효한 문자열은 SystemAssigned 또는 SystemAssigned,UserAssigned 또는 UserAssigned입니다.

## <a name="june-2021"></a>2021년 6월

### <a name="additional-live-event-ingest-heartbeat-properties-for-improved-diagnostics"></a>향상된 진단을 위한 추가 라이브 이벤트 수집 하트비트 속성

추가 라이브 이벤트 수집 하트비트 속성이 Event Grid 메시지에 추가되었습니다. 여기에는 실시간 수집 중 문제를 진단하는 데 도움이 되는 다음과 같은 새로운 필드가 포함됩니다.  **ingestDriftValue** 는 라이브 이벤트로 푸시하는 원본 수집 인코더에서 네트워크 대기 시간을 모니터링해야 하는 시나리오에서 유용합니다. 이 값이 너무 멀리 벗어나면 네트워크 대기 시간이 너무 길어서 라이브 스트리밍 이벤트가 성공적일 수 없음을 나타낼 수 있습니다.

자세한 내용은 [LiveEventIngestHeartbeat 스키마](./monitoring/media-services-event-schemas.md#liveeventingestheartbeat)를 참조하세요.

### <a name="private-links-support-is-now-ga"></a>프라이빗 링크 지원은 이제 GA입니다.

[프라이빗 링크](../../private-link/index.yml)가 있는 Media Services 사용에 대한 지원이 이제 GA이며 Azure Government 클라우드를 포함한 모든 Azure 지역에서 사용할 수 있습니다.
Azure Private Link를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services 및 Azure 호스트 고객 소유/파트너 서비스에 액세스할 수 있습니다.
가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다.

프라이빗 링크로 Media Services를 사용하는 방법에 대한 자세한 내용은 [프라이빗 링크를 사용하여 Media Services 및 Storage 계정 만들기](./security-private-link-how-to.md)를 참조하세요.

### <a name="new-us-west-3-region-is-ga"></a>새로운 미국 서부 3 지역은 GA입니다.

미국 서부 3 지역은 이제 GA이며 고객이 새 Media Services 계정을 만들 때 사용할 수 있습니다.

### <a name="key-delivery-supports-ip-allowlist-restrictions"></a>키 전송에서는 IP 허용 목록 제한을 지원합니다.

이제 키 전송에 대한 IP 허용 목록 제한으로 Media Services 계정을 구성할 수 있습니다. 새 허용 목록 설정은 SDK와 포털 및 CLI를 통해 Media Services 계정 리소스에서 사용할 수 있습니다.
그러면 운영자는 DRM 라이선스 및 AES-128 콘텐츠 키의 전송을 특정 IPv4 범위로 제한할 수 있습니다.

이 기능을 사용하여 DRM 라이선스 또는 AES-128 키의 모든 퍼블릭 인터넷 전송을 차단하고 개인 네트워크 엔드포인트로의 전송을 제한할 수도 있습니다.

자세한 내용은 [Restrict access to DRM license and AES key delivery using IP allowlists](./drm-content-protection-key-delivery-ip-allow.md)(IP 허용 목록을 사용하여 DRM 라이선스 및 AES 키 전송에 대한 액세스 제한) 문서를 참조하세요.

### <a name="new-samples-for-python-and-nodejs-with-typescript"></a>Python 및 Node.js의 새 샘플(Typescript 포함)
Azure SDK에서 최신 Typescript 지원을 사용하는 **Node.js** 용 샘플이 업데이트되었습니다.

|샘플|설명|
|---|---|
|[라이브 스트리밍](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| 기본 라이브 스트리밍 예제입니다. **경고** 라이브를 사용하는 경우 모든 리소스가 정리되고 포털에서 더는 청구되지 않는지 확인해야 합니다.|
|[HLS 및 DASH 업로드 및 스트림](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| 로컬 파일을 업로드하거나 원본 URL에서 인코딩하는 기본 예제입니다. 샘플에서는 스토리지 SDK를 사용하여 콘텐츠를 다운로드하는 방법을 보여 주고 플레이어에 스트리밍하는 방법을 보여 줍니다. |
|[Playready 및 Widevine DRM을 사용하여 HLS 및 DASH 업로드 및 스트림](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| Widevine 및 PlayReady DRM을 사용하여 인코딩하고 스트림하는 방법을 설명합니다. |
|[비디오 및 오디오 인덱싱을 위한 AI 업로드 및 사용](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| 비디오 및 오디오 분석기 사전 설정을 사용하여 비디오 또는 오디오 파일에서 메타데이터와 인사이트를 생성하는 예제입니다. |


Azure Functions 및 Event Grid를 사용하여 얼굴 편집 사전 설정을 트리거하는 방법을 보여 주는 새로운 **Python** 샘플입니다.

|샘플|설명|
|---|---|
|[이벤트 및 함수를 사용한 얼굴 편집](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased) | 이는 Azure Storage 계정에 있는 즉시 비디오에서 Azure Media Services Face Redactor 작업을 트리거하는 이벤트 기반 접근 방법의 예제입니다. 솔루션에 Azure Media Services, Azure Function, Event Grid 및 Azure Storage를 활용합니다. 솔루션에 대한 전체 설명은 [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md)를 참조하세요. |


## <a name="may-2021"></a>2021년 5월

### <a name="availability-zones-default-support-in-media-services"></a>Media Services의 가용성 영역 기본 지원

이제 Media Services에서 [가용성 영역](concept-availability-zones.md)을 지원하여 동일한 Azure 지역 내에서 오류 격리 위치를 제공합니다.  Media Services 계정은 기본적으로 영역이 중복되며 추가 구성이나 설정이 필요하지 않습니다. 이는 [가용성 영역 지원](../../availability-zones/az-region.md#azure-regions-with-availability-zones)이 있는 지역에만 적용됩니다.


## <a name="march-2021"></a>2021년 3월

### <a name="new-language-support-added-to-the-audioanalyzer-preset"></a>새 언어 지원이 AudioAnalyzer 사전 설정에 추가됨

이제 AudioAnalyzer 사전 설정(기본 및 표준 모드 모두)에서 비디오 전사 및 자막 작성에 추가 언어를 사용할 수 있습니다.

* 영어(호주), ‘en-AU’
* 프랑스어(캐나다), ‘fr-CA’
* 아랍어(바레인), 최신 표준, ‘ar-BH’
* 아랍어(이집트), ‘ar-EG’
* 아랍어(이라크), ‘ar-IQ’
* 아랍어(이스라엘), ‘ar-IL’
* 아랍어(요르단), ‘ar-JO’
* 아랍어(쿠웨이트), ‘ar-KW’
* 아랍어(레바논), ‘ar-LB’
* 아랍어(오만), ‘ar-OM’
* 아랍어(카타르), ‘ar-QA’
* 아랍어(사우디아라비아), ‘ar-SA’
* 덴마크어, ‘da-DK’
* 노르웨이어, ‘nb-NO’
* 스웨덴어, ‘sv-SE’
* 핀란드어, ‘fi-FI’
* 태국어, ‘th-TH’
* 터키어, ‘tr-TR’

[비디오 및 오디오 파일 분석 개념 문서](analyze-video-audio-files-concept.md)에서 사용 가능한 최신 언어를 참조하세요.

## <a name="february-2021"></a>2021년 2월

### <a name="hevc-encoding-support-in-standard-encoder"></a>표준 인코더의 HEVC Encoding 지원

표준 인코더는 이제 8비트 HEVC(H.265) 인코딩 지원을 제공합니다. HEVC 콘텐츠는 ‘hev1’ 형식을 사용하여 동적 패키지 작성 도구를 통해 전달 및 패키지할 수 있습니다.  

HEVC 샘플을 사용하는 새 .NET 사용자 지정 인코딩은 [media-services-v3-dotnet Git Hub 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)에서 사용할 수 있습니다.
이제 사용자 지정 인코딩 외에도 다음과 같은 새로운 기본 제공 HEVC 인코딩 기본 설정을 사용할 수 있습니다.

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720P
- H265SingleBitrate1080p
- H265SingleBitrate4K

이전에 v2 API의 프리미엄 인코더에서 HEVC를 사용 중인 고객은 표준 인코더에서 새로운 HEVC 인코딩 지원을 사용하도록 마이그레이션해야 합니다.

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services v2 API 및 SDK 사용 중단 알림

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>2024년 2월 29일까지 Azure Media Services REST API 및 SDK를 버전 3으로 업데이트

Azure Media Services REST API와 .NET 및 Java용 클라이언트 SDK의 버전 3은 버전 2보다 더 많은 기능을 제공하기 때문에 Azure Media Services REST API와 .NET 및 Java용 클라이언트 SDK 버전 2의 사용을 중지하고 있습니다.

Azure Media Services REST API와 .NET 및 Java용 클라이언트 SDK 버전 3의 다양한 이점을 얻을 수 있도록 더 빨리 전환하는 것이 좋습니다.
버전 3은 다음을 제공합니다.
 
- 연중무휴 라이브 이벤트 지원
- .NET Core, Node.js, Python, Java, Go, Ruby용 ARM REST API, 클라이언트 SDK
- 고객 관리형 키, 신뢰할 수 있는 스토리지 통합, 프라이빗 링크 지원 [등](./migrate-v-2-v-3-migration-benefits.md)

#### <a name="action-required"></a>필요한 작업

워크로드 중단을 최소화하려면 [마이그레이션 가이드](./migrate-v-2-v-3-migration-introduction.md)를 검토하여 2024년 2월 29일 이전에 API 및 SDK 버전 2에서 API 및 SDK 버전 3으로 코드를 전환합니다.
**2024년 2월 29일 이후** Azure Media Services는 REST API 버전 2, ARM 계정 관리 API 버전 2015-10-01 또는 .NET 클라이언트 SDK 버전 2에서 더 이상 트래픽을 허용하지 않습니다. 여기에는 API 버전 2를 호출할 수 있는 타사 오픈 소스 클라이언트 SDK가 포함됩니다.  

공식 [Azure 업데이트 공지](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/)를 참조하세요.

### <a name="standard-encoder-support-for-v2-api-features"></a>v2 API 기능에 대한 표준 인코더 지원

HEVC(H.265) 인코딩에 추가된 새로운 지원 외에도 이제 2020-05-01 버전의 인코딩 API에서 다음 기능을 사용할 수 있습니다.

- 이제 새 **JobInputClip** 지원을 사용한 여러 입력 파일 연결이 지원됩니다.
    - [두 자산을 함께 연결](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)하는 방법을 보여 주는 .NET의 예제를 사용할 수 있습니다.
- 오디오 트랙 선택을 사용하면 고객이 들어오는 오디오 트랙을 선택하여 매핑하고 인코딩에 대한 출력으로 라우팅할 수 있습니다.
    - **AudioTrackDescriptor** 및 트랙 선택 항목에 대한 [REST API OpenAPI](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385)에서 자세한 내용을 참조하세요.
- 인코딩에 대한 선택 항목 추적 – 고객이 여러 비트 전송률 트랙을 포함하는 ABR 원본 파일 또는 라이브 보관 파일에서 트랙을 선택할 수 있습니다. 라이브 이벤트 보관 파일에서 MP4를 생성하는 데 매우 유용합니다.
    - [VideoTrackDescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562)를 참조하세요.
- FaceDetector에 편집(흐리게) 기능이 추가되었습니다.
    - FaceDetector 사전 설정의 [편집](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634) 및 [결합](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649) 모드를 참조하세요.

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>Azure Media Services API의 2020-05-01 버전용 새 클라이언트 SDK 릴리스

사용 가능한 모든 언어에 대한 새로운 클라이언트 SDK 버전을 이제 위의 기능을 통해 사용할 수 있습니다.
패키지 관리자를 사용하여 코드 베이스에서 최신 클라이언트 SDK로 업데이트하세요.

- [.NET SDK 패키지 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js Typescript 버전 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python azure-mgmt-media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-beta.2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>Azure Media Services API의 2020-05-01 버전에서 제공되는 새로운 보안 기능

- **[고객 관리 키](concept-use-customer-managed-keys-byok.md)** : “2020-05-01” 버전 API를 사용하여 만든 계정에 저장된 콘텐츠 키 및 기타 데이터는 계정 키로 암호화됩니다. 고객은 계정 키를 암호화하는 키를 제공할 수 있습니다.

- **[신뢰할 수 있는 스토리지](concept-trusted-storage.md)** : Media Services 계정과 연결된 관리 ID를 사용하여 Azure Storage에 액세스하도록 Media Services를 구성할 수 있습니다. 관리 ID를 사용하여 스토리지 계정에 액세스하는 경우 고객은 Media Services 시나리오를 차단하지 않고 스토리지 계정에서 더 제한적인 네트워크 ACL을 구성할 수 있습니다.

- **[관리 ID](concept-managed-identities.md)** : 고객은 키 자격 증명 모음(고객 관리 키의 경우) 및 스토리지 계정(신뢰할 수 있는 스토리지의 경우)에 대한 액세스를 제공하기 위해 Media Services 계정에 대해 시스템 할당 관리 ID를 사용하도록 설정할 수 있습니다.

### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>isomorphic SDK for JavaScript를 사용하는 Typescript Node.js 샘플 업데이트

Node.js 샘플이 최신 isomorphic SDK를 사용하도록 업데이트되었습니다. 샘플에서 Typescript를 사용하는 방법을 보여 줍니다. 또한 Node.js/Typescript에 대한 새로운 라이브 스트리밍 샘플이 추가되었습니다.

**[media-services-v3-node-tutorials](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** Git Hub 리포지토리에서 최신 샘플을 참조하세요.

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>웜 상태에서 더 빠른 시작을 지원하는 새로운 라이브 독립 모드

이제 라이브 이벤트는 “대기”에 대한 저비용 청구 모드를 지원합니다. 이를 통해 고객은 “핫 풀” 생성 시 더 낮은 비용으로 라이브 이벤트를 미리 할당할 수 있습니다. 그러면 고객은 콜드에서 시작하는 것보다 더 빠르게 라이브 이벤트를 사용하여 실행 상태로 전환할 수 있습니다.  이렇게 하면 채널을 시작하는 시간이 대폭 단축되고 더 낮은 가격 모드로 실행되는 머신의 빠른 핫 풀 할당이 가능합니다.
[여기](https://azure.microsoft.com/pricing/details/media-services)에서 최신 가격 책정 정보를 참조하세요.
대기 상태와 라이브 이벤트의 기타 상태에 대한 자세한 내용은 [라이브 이벤트 상태 및 청구](./live-event-states-billing-concept.md) 문서를 참조하세요.

## <a name="december-2020"></a>2020년 12월

### <a name="regional-availability"></a>국가별 가용성

이제 Azure Portal의 노르웨이 동부 지역에서 Azure Media Services를 사용할 수 있습니다.  이 영역에는 restV2가 없습니다.

## <a name="october-2020"></a>2020년 10월

### <a name="basic-audio-analysis"></a>기본 오디오 분석

이제 오디오 분석 기본 설정에 기본 모드 가격 책정 계층이 포함되어 있습니다. 새로운 기본 오디오 분석기 모드는 음성 전사를 추출하고, 출력 캡션 및 자막의 형식을 지정하는 저렴한 옵션을 제공합니다. 이 모드는 음성-텍스트 변환 전사 및 VTT 자막/캡션 파일 생성을 수행합니다. 이 모드의 출력에는 키워드, 기록, 타이밍 정보만 포함된 Insights JSON 파일이 포함됩니다. 자동 언어 감지 기능과 화자 분할 기능은 이 모드에 포함되어 있지 않습니다. [지원되는 언어](analyze-video-audio-files-concept.md#built-in-presets) 목록을 참조하세요.

Indexer v1 및 Indexer v2를 사용하는 고객은 기본 오디오 분석 사전 설정으로 마이그레이션해야 합니다.

기본 오디오 분석기 모드에 대한 자세한 내용은 [비디오 및 오디오 파일 분석](analyze-video-audio-files-concept.md)을 참조하세요.  REST API에서 기본 오디오 분석기 모드를 사용하는 방법을 알아보려면 [기본 오디오 변환을 만드는 방법](transform-create-basic-audio-how-to.md)을 참조하세요.

### <a name="live-events"></a>라이브 이벤트

이제 라이브 이벤트가 중지될 때 대부분의 속성에 대한 업데이트가 허용됩니다. 또한 사용자는 라이브 이벤트의 입력 및 미리 보기 URL에 대한 정적 호스트 이름에 대한 접두사를 지정할 수 있습니다. 이제 속성의 의도를 더 잘 반영하기 위해 VanityUrl을 `useStaticHostName`이라고 합니다.

이제 라이브 이벤트에는 대기 상태가 있습니다.  [Media Services의 라이브 이벤트 및 라이브 출력](./live-event-outputs-concept.md)을 참조하세요.

라이브 이벤트는 다양한 입력 가로 세로 비율 수신을 지원합니다. 스트레치 모드를 사용하면 고객이 출력의 스트레치 동작을 지정할 수 있습니다.

이제 라이브 인코딩은 0.5 ~ 20초 사이에 고정 키 프레임 간격 조각을 출력하는 기능을 추가합니다.

### <a name="accounts"></a>계정

> [!WARNING]
> 2020-05-01 API 버전을 사용하여 Media Services 계정을 만드는 경우 RESTv2에서 작동하지 않습니다. 

## <a name="august-2020"></a>2020년 8월

### <a name="dynamic-encryption"></a>동적 암호화

이제 기존 PlayReady Protected Interoperable File Format(PIFF 1.1) 암호화에 대한 지원을 동적 패키지 작성 도구에서 사용할 수 있습니다. Microsoft에서 게시한 CENC(Common Encryption 표준)의 초기 초안을 구현한 삼성 및 LG의 기존 스마트 TV 세트에 대한 지원을 제공합니다.  PIFF 1.1 형식은 이전에 Silverlight 클라이언트 라이브러리에서 지원되었던 암호화 형식으로도 알려져 있습니다. 현재 이 암호화 형식의 유일한 사용 사례 시나리오는 PIFF 1.1 암호화를 사용하는 부드러운 스트리밍만 지원하는 일부 지역에서 많은 수의 스마트 TV가 남아 있는 기존 스마트 TV 시장을 대상으로 하는 것입니다.

새 PIFF 1.1 암호화 지원을 사용하려면 스트리밍 로케이터의 URL 경로에서 암호화 값을 ‘piff’로 변경합니다. 자세한 내용은 [Content Protection 개요](drm-content-protection-concept.md)를 참조하세요.
예: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1.1 지원은 Common Encryption의 초기 "Silverlight"버전을 구현한 Smart TV (Samsung, LG)의 이전 버전과 호환되는 솔루션으로 제공됩니다. PlayReady 암호화의 PIFF 1.1 버전을 지원하는 2009-2015년 사이에 출시된 기존 삼성 또는 LG 스마트 TV를 지원하는 데 필요한 경우에만 PIFF 형식을 사용하는 것이 좋습니다. 

## <a name="july-2020"></a>2020년 7월

### <a name="live-transcriptions"></a>라이브 전사

이제 라이브 전사는 19개의 언어와 8개 지역을 지원합니다.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Media Services 및 Azure AD를 사용하여 콘텐츠 보호

[Azure AD를 사용한 엔드투엔드 콘텐츠 보호](./architecture-azure-ad-content-protection.md)라는 자습서를 게시했습니다.

### <a name="high-availability"></a>고가용성

Media Services 및 VOD(주문형 비디오)를 사용한 고가용성 [개요](./architecture-high-availability-encoding-concept.md) 및 [샘플](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)을 게시했습니다.

## <a name="june-2020"></a>2020년 6월

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>IoT Edge의 Live Video Analytics 미리 보기 릴리스

IoT Edge의 Live Video Analytics의 미리 보기가 공개되었습니다. 자세한 내용은 [릴리스 정보](../live-video-analytics-edge/release-notes.md)를 참조하세요.

IoT Edge의 Live Video Analytics는 미디어 서비스 제품군에 대한 확장입니다. 사용자의 에지 디바이스에서 선택한 AI 모델을 사용하여 라이브 비디오를 분석하고 필요에 따라 해당 비디오를 캡처 및 기록할 수 있습니다. 이제 라이브 비디오 파이프라인을 구축하고 작동하는 복잡성을 걱정하지 않고 에지에서 실시간 비디오 분석으로 앱을 빌드할 수 있습니다.

## <a name="may-2020"></a>2020년 5월

현재 Azure Media Services를 일반적으로 사용할 수 있는 지역은 "독일 북부", "독일 중서부", "스위스 북부" 및 "스위스 서부"입니다. 고객은 Azure Portal을 사용하여 이러한 지역에 Media Services를 배포할 수 있습니다.

## <a name="april-2020"></a>2020년 4월

### <a name="improvements-in-documentation"></a>설명서 개선

Azure Media Player 문서가 [Azure 설명서](../azure-media-player/azure-media-player-overview.md)로 마이그레이션되었습니다.

## <a name="january-2020"></a>2020년 1월

### <a name="improvements-in-media-processors"></a>미디어 프로세서 개선

- 비디오 분석에서 인터레이스 소스에 대한 지원 개선 – 이러한 콘텐츠는 이제 유추 엔진으로 전송되기 전에 올바르게 디인터레이스됩니다.
- "최상" 모드로 썸네일을 생성하면 인코더는 이제 30초 이상 검색하여 단색이 아닌 프레임을 선택합니다.

### <a name="azure-government-cloud-updates"></a>Azure Government 클라우드 업데이트

Media Services가 일반 공급되는 Azure Government 지역은 *USGov 애리조나* 및 *USGov 텍사스* 입니다.

## <a name="december-2019"></a>2019년 12월

라이브 및 비디오 주문형 스트리밍 모두의 *Origin-Assist Prefetch* 헤더에 대한 CDN 지원이 추가되었습니다. Akamai CDN을 사용하여 직접 계약을 체결한 고객에게 제공됩니다. Origin-Assist CDN-Prefetch 기능에는 Akamai CDN과 Azure Media Services 원본 간 다음 HTTP 헤더 교환이 포함됩니다.

|HTTP 헤더|값|보낸 사람|받는 사람|목적|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1(기본값) 또는 0 |CDN|원본|CDN 프리페치를 사용하도록 설정|
|CDN-Origin-Assist-Prefetch-Path| 예제: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|원본|CDN|CDN에 프리페치 경로 제공|
|CDN-Origin-Assist-Prefetch-Request|1(프리페치 요청) 또는 0(일반 요청)|CDN|원본|CDN의 요청이 프리페치임을 나타냄|

헤더 교환의 작동 방식을 확인하려면 다음 단계를 수행합니다.

1. Postman 또는 curl을 사용하여 오디오 또는 비디오 세그먼트나 조각의 Media Services 원본 요청을 실행합니다. 요청에 CDN-Origin-Assist-Prefetch-Enabled: 1 헤더를 추가해야 합니다.
2. 응답에는 상대 경로를 값으로 사용하는 CDN-Origin-Assist-Prefetch-Path 헤더가 표시됩니다.

## <a name="november-2019"></a>2019년 11월

### <a name="live-transcription-preview"></a>라이브 전사 미리 보기

라이브 전사는 현재 공개 미리 보기로 제공되며 미국 서부 2 지역에서 사용할 수 있습니다.

라이브 전사는 라이브 이벤트와 함께 추가 기능으로 작동하도록 설계되었습니다.  통과와 표준 또는 프리미엄 인코딩 라이브 이벤트 모두에서 지원됩니다.  이 기능을 사용하면 서비스가 Cognitive Services의 [Speech-To-Text](../../cognitive-services/speech-service/speech-to-text.md) 기능을 사용하여 들어오는 오디오의 음성을 텍스트로 전사합니다. 그러면 이 텍스트를 MPEG-DASH 및 HLS 프로토콜의 비디오 및 오디오와 함께 배달할 수 있게 됩니다. 이 기능이 "실행 중" 상태일 때 라이브 이벤트와 별도로 새 추가 기능 미터를 기준으로 비용이 청구됩니다.  라이브 전사 및 청구에 대한 자세한 내용은 [라이브 전사](live-event-live-transcription-how-to.md)를 참조하세요.

> [!NOTE]
> 현재 라이브 전사는 미국 서부 2 지역에서만 미리 보기 기능으로 사용할 수 있습니다. 현재는 영어(en-us)의 음성 전사만 지원됩니다.

### <a name="content-protection"></a>콘텐츠 보호

9월에 제한된 지역에서 다시 출시된 *토큰 재생 방지* 기능을 이제 모든 지역에서 사용할 수 있습니다.
Media Services 고객은 이제 동일한 토큰을 사용하여 키 또는 라이선스를 요청할 수 있는 횟수에 제한을 설정할 수 있습니다. 자세한 내용은 [토큰 재생 방지](drm-content-protection-concept.md#token-replay-prevention)를 참조하세요.

### <a name="new-recommended-live-encoder-partners"></a>새로운 권장 라이브 인코더 파트너

RTMP 라이브 스트리밍에 다음과 같은 새로운 권장 파트너 인코더에 대한 지원이 추가되었습니다.

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 및 최대 작업 카메라](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>파일 인코딩 기능 향상

- 이제 새로운 콘텐츠 인식 인코딩 사전 설정을 사용할 수 있습니다. 이는 콘텐츠 인식 인코딩을 사용하여 GOP 맞춤 MP4 세트를 생성합니다. 입력 콘텐츠가 지정되면 이 서비스가 입력 콘텐츠에 대한 초기 경량 분석을 수행합니다. 이러한 결과를 사용하여 적응 스트리밍이 배달할 최적의 계층 수, 적절한 비트 전송률 및 해상도 설정을 결정합니다. 이 사전 설정은 복잡성이 낮거나 보통인 비디오에 특히 효과적입니다. 이러한 비디오의 출력 파일은 비트 전송률이 낮지만 여전히 좋은 화질로 제공됩니다. 출력에는 비디오 및 오디오가 인터리브된 MP4 파일이 포함됩니다. 자세한 내용은 [개방형 API 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)을 참조하세요.
- 표준 인코더의 크기 조정기에 대한 성능 및 다중 스레딩 개선. 고객은 특정 조건에서 VOD 인코딩 성능이 5-40% 정도 향상된 것을 알 수 있습니다. 여러 비트 전송률로 인코딩된 복잡성이 낮은 콘텐츠의 성능 향상률이 가장 높습니다. 
- 이제 표준 인코딩은 시간 기반 GOP 설정을 사용하는 경우 VOD 인코딩 중에 VFR(가변 프레임 속도) 콘텐츠에 일정한 GOP 케이던스를 유지합니다.  예를 들어 15-30fps 사이에서 가변하는 혼합 프레임 속도 콘텐츠를 제출하는 고객은 이제 적응 비트 전송률 스트리밍 MP4 파일에 대한 출력에 일정한 GOP 거리가 계산되는 것을 볼 수 있습니다. 그러면 HLS 또는 DASH를 통해 제공 시 트랙 간에 더욱 원활하게 전환할 수 있습니다. 
-  VFR(가변 프레임 속도) 원본 콘텐츠의 AV 동기화 개선

### <a name="azure-video-analyzer-for-media-video-analytics"></a>Azure Video Analyzer for Media, 비디오 분석

- 이제 VideoAnalyzer 사전 설정을 사용하여 추출된 키 프레임이 크기가 조정되는 대신 비디오의 원래 해상도로 설정됩니다. 고해상도 키 프레임 추출은 원본 품질 이미지를 제공하고, Microsoft Computer Vision 및 Custom Vision 서비스에서 제공하는 이미지 기반 AI 모델을 사용하여 비디오에서 더 많은 정보를 얻을 수 있도록 합니다.

## <a name="september-2019"></a>2019년 9월

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>라이브 이벤트의 라이브 선형 인코딩

Media Services v3 라이브 이벤트의 라이브 선형 인코딩의 연중무휴 미리 보기를 발표할 예정입니다.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>미디어 프로세서 사용 중단

*Azure Media Indexer* 및 *Azure Media Indexer 2 미리 보기* 의 사용 중단을 발표할 예정입니다. 사용 중지 날짜는 [레거시 구성 요소](../previous/legacy-components.md) 문서를 참조하세요. Azure Video Analyzer for Media가 이 레거시 미디어 프로세서를 바꿉니다.

자세한 내용은 [Azure Media Indexer 및 Azure Media Indexer 2에서 **Azure Media Services Video Indexer** 로 마이그레이션](../previous/migrate-indexer-v1-v2.md)을 참조하세요.

## <a name="august-2019"></a>2019년 8월

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>남아프리카 공화국 지역 쌍에 Media Services가 제공됨 

이제 남아프리카 공화국 북부와 남아프리카 공화국 서부 지역에서 Media Services를 사용할 수 있습니다.

자세한 내용은 [Media Services v3가 있는 클라우드 및 지역](azure-clouds-regions.md)을 참조하세요.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>미디어 프로세서 사용 중단

WAME(*Windows Azure Media Encoder*) 및 AME(*Azure Media Encoder*) 미디어 프로세서의 사용이 중단될 예정입니다. 사용 중지 날짜는 이 [레거시 구성 요소](../previous/legacy-components.md) 문서를 참조하세요.

자세한 내용은 [WAME에서 Media Encoder Standard로 마이그레이션](../previous/migrate-windows-azure-media-encoder.md) 및 [AME에서 Media Encoder Standard로 마이그레이션](../previous/migrate-azure-media-encoder.md)을 참조하세요.
 
## <a name="july-2019"></a>2019년 7월

### <a name="content-protection"></a>콘텐츠 보호

토큰 제한으로 보호된 콘텐츠를 스트리밍하는 경우 최종 사용자는 키 배달 요청의 일부로 전송되는 토큰을 가져와야 합니다. Media Services 고객은 *토큰 재생 방지* 기능을 통해 동일한 토큰을 사용하여 키 또는 라이선스를 요청하는 횟수에 대한 제한을 설정할 수 있습니다. 자세한 내용은 [토큰 재생 방지](drm-content-protection-concept.md#token-replay-prevention)를 참조하세요.

7월부터 이 미리 보기 기능은 미국 중부 및 미국 중서부에서만 사용할 수 있습니다.

## <a name="june-2019"></a>2019년 6월

### <a name="video-subclipping"></a>비디오 서브클리핑

이제 [작업](/rest/api/media/jobs)을 사용하여 인코딩할 때 비디오를 자르거나 서브클리핑할 수 있습니다. 

이 기능은 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용하여 빌드된 모든 [변환](/rest/api/media/transforms)과 함께 작동합니다. 

예제 참조:

* [.NET을 사용한 비디오 서브클리핑](transform-subclip-video-dotnet-how-to.md)
* [REST를 사용한 비디오 서브클리핑](transform-subclip-video-rest-how-to.md)

## <a name="may-2019"></a>2019년 5월

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Media Services 진단 로그 및 메트릭에 대한 Azure Monitor 지원

이제 Azure Monitor를 사용하여 Media Services에서 내보낸 원격 분석 데이터를 볼 수 있습니다.

* Azure Monitor 진단 로그를 사용하여 Media Services 키 배달 엔드포인트에서 보낸 요청을 모니터링하세요. 
* Media Services [스트리밍 엔드포인트](stream-streaming-endpoint-concept.md)에서 내보낸 메트릭을 모니터링하세요.   

자세한 내용은 [Media Services 메트릭 및 진단 로그 모니터링](monitoring/monitor-media-services-data-reference.md)을 참조하세요.

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>동적 패키징에서 다중 오디오 트랙 지원 

[동적 패키징](encode-dynamic-packaging-concept.md)은 이제 여러 코덱 및 언어가 있는 오디오 트랙이 여러 개인 자산을 스트리밍할 때 HLS 출력(버전 4 이상)에 다중 오디오 트랙을 지원합니다.

### <a name="korea-regional-pair-is-open-for-media-services"></a>한국 지역 쌍에 Media Services가 제공됨 

이제 한국 중부와 한국 남부 지역에서 Media Services를 사용할 수 있습니다. 

자세한 내용은 [Media Services v3가 있는 클라우드 및 지역](azure-clouds-regions.md)을 참조하세요.

### <a name="performance-improvements"></a>성능 향상

Media Services 성능 개선을 포함하는 업데이트가 추가되었습니다.

* 처리에 지원되는 최대 파일 크기가 업데이트되었습니다. [할당량 및 한도](limits-quotas-constraints-reference.md)를 참조하세요.
* [인코딩 속도 향상](concept-media-reserved-units.md)

## <a name="april-2019"></a>2019년 4월

### <a name="new-presets"></a>새로운 사전 설정

* 기본 제공 분석기 사전 설정에 [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)이 추가되었습니다.
* 기본 제공 인코더 사전 설정에 [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset)이 추가되었습니다. 자세한 내용은 [콘텐츠 인식 인코딩](encode-content-aware-concept.md)을 참조하세요. 

## <a name="march-2019"></a>2019년 3월

동적 패키징이 이제 Dolby Atmos를 지원합니다. 자세한 내용은 [동적 패키징으로 지원되는 오디오 코덱](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)을 참조하세요.

이제 스트리밍 로케이터에 적용되는 자산 또는 계정 필터 목록을 지정할 수 있습니다. 자세한 내용은 [스트리밍 로케이터를 사용하여 필터 연결](filters-concept.md#associating-filters-with-streaming-locator)을 참조하세요.

## <a name="february-2019"></a>2019년 2월

이제 Media Services v3가 Azure 국가별 클라우드에서 지원됩니다. 아직은 일부 클라우드에서 일부 기능을 사용할 수 없습니다. 자세한 내용은 [Azure Media Services v3가 있는 클라우드 및 지역](azure-clouds-regions.md)을 참조하세요.

Media Services에 대한 Azure Event Grid 스키마에 [Microsoft.Media.JobOutputProgress](monitoring/media-services-event-schemas.md#monitoring-job-output-progress) 이벤트가 추가되었습니다.

## <a name="january-2019"></a>2019년 1월

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard 및 MPI 파일 

Media Encoder Standard로 인코딩하여 MP4 파일을 생성하는 경우 새 .mpi 파일이 생성되고 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](encode-dynamic-packaging-concept.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

MPI 파일을 수정 또는 제거하거나 해당 파일의 존재 여부에 따른 종속성을 서비스에서 사용하지 않아야 합니다.

## <a name="december-2018"></a>2018년 12월

V3 API의 GA 릴리스업데이트에는 다음이 포함됩니다.
       
* **PresentationTimeRange** 속성은 **자산 필터** 및 **계정 필터** 에 대해 더 이상 '필수'가 아닙니다. 
* **작업** 및 **변환** 의 $top 및 $skip 쿼리 옵션이 제거되었으며 $orderby가 추가되었습니다. 새 순서 지정 기능을 추가되면서, $top 및 $skip 옵션을 이전에 구현하지 않았어도 실수로 노출되는 것으로 확인되었습니다.
* 열거형 확장성을 다시 사용하도록 설정했습니다. 이 기능은 미리 보기 버전의 SDK에서 사용하도록 설정되며 GA 버전에서는 실수로 사용하지 않도록 설정되었습니다.
* 두 개의 미리 정의된 스트리밍 정책 이름이 바뀌었습니다. **SecureStreaming** 은 이제 **MultiDrmCencStreaming** 입니다. **SecureStreamingWithFairPlay** 는 이제 **Predefined_MultiDrmStreaming** 입니다.

## <a name="november-2018"></a>2018년 11월

현재 CLI 2.0 모듈이 [Azure Media Services v3 GA](/cli/azure/ams) – v 2.0.50에 제공됩니다.

### <a name="new-commands"></a>새 명령

- [az ams account](/cli/azure/ams/account)
- [az ams account-filter](/cli/azure/ams/account-filter)
- [az ams asset](/cli/azure/ams/asset)
- [az ams asset-filter](/cli/azure/ams/asset-filter)
- [az ams content-key-policy](/cli/azure/ams/content-key-policy)
- [az ams job](/cli/azure/ams/job)
- [az ams live-event](/cli/azure/ams/live-event)
- [az ams live-output](/cli/azure/ams/live-output)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint)
- [az ams streaming-locator](/cli/azure/ams/streaming-locator)
- [az ams account mru](/cli/azure/ams/account/mru) - 미디어 예약 단위를 관리할 수 있습니다. 자세한 내용은 [미디어 예약 단위 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="new-features-and-breaking-changes"></a>새 기능과 주요 변경 내용

#### <a name="asset-commands"></a>자산 명령

- ```--storage-account``` 및 ```--container``` 인수가 추가되었습니다.
- ```az ams asset get-sas-url``` 명령에서 만료 시간(현재+23시간) 및 사용 권한(읽기)에 대한 기본값이 추가되었습니다.

#### <a name="job-commands"></a>작업 명령

- ```--correlation-data``` 및 ```--label``` 인수가 추가되었습니다.
- ```--output-asset-names```의 이름이 ```--output-assets```으로 바뀌었습니다. 이제 'assetName = label' 형식으로 공백으로 구분된 자산 목록을 허용합니다. 레이블이 없는 자산은 ‘assetName=’과 같이 보낼 수 있습니다.

#### <a name="streaming-locator-commands"></a>스트리밍 로케이터 명령

- ```az ams streaming locator``` 기본 명령이 ```az ams streaming-locator```로 바뀌었습니다.
- ```--streaming-locator-id``` 및 ```--alternative-media-id support``` 인수가 추가되었습니다.
- ```--content-keys argument``` 인수가 업데이트되었습니다.
- ```--content-policy-name```의 이름이 ```--content-key-policy-name```으로 바뀌었습니다.

#### <a name="streaming-policy-commands"></a>스트리밍 정책 명령

- ```az ams streaming policy``` 기본 명령이 ```az ams streaming-policy```로 바뀌었습니다.
- ```az ams streaming-policy create```에 암호화 매개 변수 지원이 추가되었습니다.

#### <a name="transform-commands"></a>변환 명령

- ```--preset-names``` 인수가 ```--preset```으로 바뀌었습니다. 이제 한 번에 1개의 출력/사전 설정만 설정할 수 있습니다(더 추가하려면 ```az ams transform output add```를 실행해야 함). 또한 사용자 정의 JSON에 경로를 전달하여 사용자 정의 StandardEncoderPreset을 설정할 수 있습니다.
- 제거할 출력 인덱스를 전달하여 ```az ams transform output remove```를 수행할 수 있습니다.
- ```az ams transform create``` 및 ```az ams transform output add``` 명령에 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 인수가 추가되었습니다.

## <a name="october-2018---ga"></a>2018년 10월 - GA

이 섹션에서는 AMS(Azure Media Services) 10월 업데이트에 대해 설명합니다.

### <a name="rest-v3-ga-release"></a>REST v3 GA 릴리스

[REST v3 GA 릴리스](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에는 라이브, 계정/자산 수준 매니페스트 필터 및 DRM 지원을 위한 더 많은 API가 포함되어 있습니다.

#### <a name="azure-resource-management"></a>Azure Resource 관리 

Azure Resource 관리가 지원되어 통합 관리 및 API 운영(이제 한 곳에 모든 기능 제공)가 가능합니다.

이 릴리스부터 Resource Manager 템플릿을 사용하여 라이브 이벤트를 만들 수 있습니다.

#### <a name="improvement-of-asset-operations"></a>자산 작업 개선 

다음과 같은 개선 사항이 도입되었습니다.

- HTTP(s) URL 또는 Azure Blob Storage SAS URL에서 수집합니다.
- 자산에 대한 자체 컨테이너 이름을 지정합니다. 
- 보다 쉬운 출력이 지원되어 Azure Functions로 사용자 지정 워크플로를 만들 수 있습니다.

#### <a name="new-transform-object"></a>새로운 변환 개체

새로운 **변환** 개체로 인해 인코딩 모델이 간소화됩니다. 새로운 개체를 통해 Resource Manager 템플릿 및 사전 설정 인코딩을 쉽게 만들고 공유할 수 있습니다. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory 인증 및 Azure RBAC

Azure AD 인증 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure AD에서 역할 또는 사용자별로 보안 변환, LiveEvent, 콘텐츠 키 정책 또는 자산을 보호할 수 있습니다.

#### <a name="client-sdks"></a>클라이언트 SDK  

Media Services v3에 지원되는 언어: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>라이브 인코딩 업데이트

다음과 같은 라이브 인코딩 업데이트가 도입되었습니다.

- 라이브에 대한 짧은 대기 시간(엔드투엔드 10초).
- RTMP 지원 향상(향상된 안정성 및 더 많은 소스 인코더 지원)
- RTMPS 보안 수집

    라이브 이벤트를 만들면 이제 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰(AppId)이 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다. 
- 24시간 코드 변환 지원 
- SCTE35를 통한 RTMP의 광고 신호 지원 향상

#### <a name="improved-event-grid-support"></a>Event Grid 지원 향상

다음과 같은 Event Grid 지원 향상을 확인할 수 있습니다.

- Azure Event Grid 통합으로 Logic Apps 및 Azure Functions를 사용하여 보다 쉽게 개발할 수 있습니다. 
- 인코딩, 라이브 채널 등의 이벤트를 구독하세요.

### <a name="cmaf-support"></a>CMAF 지원

CMAF를 지원하는 Apple HLS(iOS 11+) 및 MPEG-DASH 플레이어에 대해 CMAF 및 'cbcs' 암호화가 지원됩니다.

### <a name="video-indexer"></a>비디오 인덱서

Video Indexer GA 릴리스가 8월에 발표되었습니다. 현재 지원되는 기능에 대한 자세한 내용은 [Video Indexer란?](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md?bc=%2fazure%2fmedia-services%2fvideo-indexer%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fmedia-services%2fvideo-indexer%2ftoc.json)을 참조하세요. 

### <a name="plans-for-changes"></a>변경 계획

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
라이브, 콘텐츠 키 정책, 계정/자산 필터, 스트리밍 정책을 비롯한 모든 기능에 대한 작업을 포함하고 있는 Azure CLI 2.0 모듈이며, 곧 출시될 예정입니다. 

### <a name="known-issues"></a>알려진 문제

Asset 또는 AccountFilters에 대한 API 미리 보기를 사용하는 고객만 다음 문제의 영향을 받습니다.

Media Services v3 CLI 또는 API를 통해 9월 28일에서 10월 12일 사이에 자산 또는 계정 필터를 생성한 경우에는 버전 충돌로 인해 모든 자산 및 계정 필터를 제거하고 다시 생성해야 합니다. 

## <a name="may-2018---preview"></a>2018년 5월 - 미리 보기

### <a name="net-sdk"></a>.NET SDK

.NET SDK에는 다음과 같은 기능이 있습니다.

* 미디어 콘텐츠를 인코딩하거나 분석할 수 있는 **Transform** 및 **Job**. 예를 들어 [스트림 파일](stream-files-tutorial-with-api.md) 및 [분석](analyze-videos-tutorial.md)을 참조하세요.
* 최종 사용자 디바이스에 콘텐츠를 게시하고 스트리밍하는 **스트리밍 로케이터**.
* 콘텐츠를 전송할 때 키 전송 및 콘텐츠 보호(DRM)를 구성하는 **스트리밍 정책** 및 **콘텐츠 키 정책**.
* 라이브 스트리밍 콘텐츠의 수집 및 보관을 구성하는 **라이브 이벤트** 및 **라이브 스트리밍**.
* Azure Storage에 미디어 콘텐츠를 저장하고 게시하는 **Asset**. 
* 실시간 및 주문형 미디어 콘텐츠에 대한 동적 패키징, 암호화 및 스트리밍을 구성하고 확장하는 **스트리밍 엔드포인트**.

### <a name="known-issues"></a>알려진 문제

* 작업을 제출할 때는 HTTPS URL, SAS URL 또는 Azure Blob Storage에 있는 파일의 경로를 사용하여 원본 비디오를 수집하도록 지정할 수 있습니다. 현재까지 Media Services v3은 HTTPS URL을 통해 청크 분할 전송 인코딩을 지원하지 않습니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

[Media Services v2에서 v3으로 이동하기 위한 마이그레이션 지침](migrate-v-2-v-3-migration-introduction.md)

## <a name="next-steps"></a>다음 단계

- [개요](media-services-overview.md)
- [Media Services v2 릴리스 정보](../previous/media-services-release-notes.md)
