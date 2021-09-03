---
title: Azure Media Services v3 질문 컬렉션
description: 이 문서에서는 Azure Media Services v3에 대한 질문과 대답을 제공합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/25/2021
ms.author: inhenkel
ms.openlocfilehash: 8503d2ee4f795deab5b228773809f6b754ee8284
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602877"
---
<!-- NOTE this file is temporary and a placeholder until the FAQ file update is completed. -->

# <a name="media-services-v3-questions-collection"></a>Media Services v3 질문 컬렉션

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 Azure Media Services v3에 대한 질문과 대답을 제공합니다.

## <a name="general"></a>일반

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>Media Services는 고객 데이터를 서비스 지역 외부에 저장하나요?

- 고객은 Azure Media Services 계정에 자신의 스토리지 계정을 연결합니다.  모든 자산 데이터는 이렇게 연결된 스토리지 계정에 저장되고 고객은 이 스토리지의 위치 및 복제 유형을 제어합니다.
- Media Services 계정과 연결된 추가 데이터(콘텐츠 암호화 키, 토큰 확인 키, JobInputHttp url 및 기타 엔터티 메타데이터)는 Media Services 계정에 대해 선택된 지역 내에서 Microsoft 소유의 스토리지에 저장됩니다.
    - 브라질 남부 및 동남 아시아의 [데이터 보존 요구 사항](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information)으로 인해 추가 계정 데이터는 영역 중복 방식으로 저장되며 단일 지역에 포함됩니다. 동남 아시아의 경우 모든 추가 계정 데이터는 싱가포르에 저장되고 브라질 남부의 경우 브라질에 저장됩니다.
    - 브라질 남부 및 동남 아시아 이외의 지역에서 추가 계정 데이터는 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 있는 Microsoft 소유의 스토리지에 저장될 수도 있습니다.
- Azure Media Services는 지역 서비스이며 [고가용성](architecture-high-availability-encoding-concept.md) 또는 데이터 복제를 제공하지 않습니다. 이러한 기능이 필요한 고객은 여러 지역에서 Media Services 계정을 사용하여 솔루션을 빌드하는 것이 좋습니다.  Media Services 주문형 비디오를 사용하여 고가용성을 위한 솔루션을 구축하는 방법을 보여주는 샘플은 가이드로 제공됩니다.

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Media Services v3에 대한 Azure Portal 제한 사항은 무엇인가요?

[Azure Portal](https://portal.azure.com/)을 사용하여 v3 라이브 이벤트 관리, v3 자산 및 작업 확인, API 액세스 정보, 콘텐츠 암호화를 할 수 있습니다. <br/>다른 모든 관리 작업(task)(예: 변환 및 작업(job) 관리 또는 v3 콘텐츠 분석)의 경우 [CLI](/cli/azure/ams) 또는 지원되는 클라이언트 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용합니다.

이전에 Media Services v3 API를 사용하여 비디오를 Media Services 계정에 업로드했거나 라이브 출력을 기반으로 콘텐츠가 생성된 경우 Azure Portal에서 **인코딩**, **분석** 또는 **암호화** 단추가 표시되지 않습니다. Media Services v3 API를 사용하여 이러한 작업을 수행합니다.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Azure Media Services 리소스에 대한 작업을 수행할 수 있는 Azure 역할은 무엇인가요? 

[Media Services 계정에 대한 Azure RBAC(역할 기반 액세스 제어)](security-rbac-concept.md)를 참조하세요.

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Apple iOS 디바이스에 스트리밍하려면 어떻게 해야 하나요?

경로 끝(URL의 **/manifest** 부분 뒤)에 **(format=m3u8-aapl)** 를 추가하여 스트리밍 원본 서버에 Apple iOS 네이티브 디바이스에서 사용할 수 있게 HLS(HTTP Live Streaming) 콘텐츠를 반환하도록 지정합니다. 자세한 내용은 [콘텐츠 배달](encode-dynamic-packaging-concept.md)을 참조하세요.

### <a name="what-is-the-recommended-method-to-process-videos"></a>비디오 처리에 권장하는 방법은 무엇입니까?

[Transforms](/rest/api/media/transforms)는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 변환은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. [작업](/rest/api/media/jobs)은 입력 비디오 또는 오디오 콘텐츠에 변환을 적용하도록 하는 Media Services에 대한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. 자세한 내용은 [Transform 및 Jobs](transform-jobs-concept.md)를 참조하세요.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>비디오를 업로드, 인코딩 및 게시합니다. 스트리밍하려고 할 때 비디오가 재생되지 않는 이유는 무엇인가요?

가장 일반적인 이유 중 하나는 재생하려고 하는 스트리밍 엔드포인트가 실행 중 상태가 아니기 때문입니다.

### <a name="how-does-pagination-work"></a>페이지 매김은 어떻게 작동하나요?

페이지 매김을 사용할 때는 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 의존하지 않아야 합니다. 자세한 내용과 예제는 [필터링, 정렬, 페이징](filter-order-page-entitites-how-to.md)을 참조하세요.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure Media Services v3에서 아직 사용할 수 없는 기능은 무엇입니까?

자세한 내용은 [마이그레이션 가이드](migrate-v-2-v-3-migration-introduction.md)를 참조하세요.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정을 이동하는 프로세스는 무엇인가요?  

자세한 내용은 [구독 간 Media Services 계정 이동](account-move-account-how-to.md)을 참조하세요.

## <a name="live-streaming"></a>라이브 스트리밍 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>브로드캐스트가 완료된 후 라이브 스트림을 어떻게 중지하나요?

클라이언트 쪽이나 서버 쪽에서 접근할 수 있습니다.

#### <a name="client-side"></a>클라이언트 쪽

사용자가 브라우저를 닫을 때 브로드캐스트를 종료하려는 경우 웹 애플리케이션은 사용자에게 메시지를 표시해야 합니다. 이는 웹 애플리케이션에서 처리할 수 있는 브라우저 이벤트입니다.

#### <a name="server-side"></a>서버 쪽

Azure Event Grid 이벤트를 구독하여 라이브 이벤트를 모니터링할 수 있습니다. 자세한 내용은 [EventGrid 이벤트 스키마](monitoring/media-services-event-schemas.md#live-event-types)를 참조하세요.

다음 작업 중 하나를 수행할 수 있습니다.

* 스트림 수준의 [Microsoft.Media.LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md#liveeventencoderdisconnected) 이벤트를 [구독](monitoring/reacting-to-media-services-events.md)하고 라이브 이벤트를 중지하고 삭제하기 위해 잠시 동안 재연결이 들어오는지 모니터링합니다.
* 트랙 수준의 [하트 비트](monitoring/media-services-event-schemas.md#liveeventingestheartbeat) 이벤트를 [구독](monitoring/reacting-to-media-services-events.md)합니다. 들어오는 비트 전송률이 모든 트랙에서 0으로 떨어지거나 마지막 타임스탬프가 더 이상 늘어나지 않으면 라이브 이벤트를 안전하게 종료할 수 있습니다. 하트비트 이벤트는 모든 트랙에 대해 20초마다 들어오므로 약간 많을 수 있습니다.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>라이브 스트림 도중에 브레이크/비디오 및 이미지 슬레이트를 삽입하는 방법은 무엇입니까?

Media Services v3 라이브 인코딩은 아직 라이브 스트리밍 도중에 비디오 또는 이미지 슬레이트를 삽입하는 기능을 지원하지 않습니다. 

[라이브 온-프레미스 인코더](encode-recommended-on-premises-live-encoders.md)를 사용하여 원본 비디오를 바꿀 수 있습니다. Telestream Wirecast, Switcher Studio(iOS), OBS Studio(무료 앱) 등 여러 앱에서 원본을 바꾸는 기능을 제공합니다.

## <a name="content-protection"></a>콘텐츠 보호

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128 암호화되지 않은 키 암호화 또는 DRM 시스템을 사용해야 하나요?

고객들은 종종 AES 암호화 또는 DRM 시스템을 사용해야 할지 여부를 궁금해 합니다. 두 시스템 간의 주요 차이점은 AES 암호화의 경우 콘텐츠 키가 TLS를 통해 클라이언트에 전송되기 때문에 추가 암호화("clear") 없이 키가 전송 중에 암호화된다는 것입니다. 그 결과 콘텐츠 암호 해독에 사용되는 키는 클라이언트 플레이어에 접근할 수 있으며, 일반 텍스트로 클라이언트에 대한 네트워크 추적에서 볼 수 있습니다. AES-128 암호화되지 않은 키 암호화는 뷰어가 신뢰할 만한 당사자(예: 직원이 볼 수 있도록 회사 내에 배포되는 회사 비디오 암호화)인 사용 사례에 적합합니다.

PlayReady, Widevine, FairPlay 등의 DRM 시스템은 모두 AES-128 암호화되지 않은 키와 비교했을 때 콘텐츠 암호 해독에 사용되는 키에 대한 추가 수준의 암호화를 제공합니다. 콘텐츠 키는 TLS에서 제공하는 전송 수준 암호화 외에 DRM 런타임으로 보호하는 키로 암호화됩니다. 또한 암호 해독은 악의적인 사용자가 공격하기에 좀 더 어려운 운영 체제 수준의 보안 환경에서 처리됩니다. DRM은 뷰어가 신뢰할 만한 당사자가 아니고 가장 높은 수준의 보안이 필요한 경우에 권장됩니다.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Azure AD를 사용하지 않고 특정 권한이 있는 사용자에게만 비디오를 보여주려면 어떻게 할까요?

Azure AD(Azure Active Directory)와 같은 특정 토큰 공급자를 사용할 필요가 없습니다. 비대칭 키 암호화를 사용하여 고유한 [JWT](https://jwt.io/) 공급자(보안 토큰 서비스 또는 STS)를 만들 수 있습니다. 사용자 지정 STS에서 자체 비즈니스 논리에 따라 클레임을 추가할 수 있습니다.

발급자, 대상 그룹 및 클레임이 모두 JWT의 내용과 `ContentKeyPolicyRestriction`에서 사용되는 `ContentKeyPolicy` 값 사이에서 정확하게 일치하는지 확인합니다.

자세한 내용은 [Media Services 동적 암호화를 사용하여 콘텐츠 보호](drm-content-protection-concept.md)를 참조하세요.

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>JWT 토큰을 라이선스 또는 키 요청에 사용하기 전에 어디에서, 어떻게 가져왔나요?

프로덕션 환경의 경우 HTTPS 요청 시 JWT 토큰을 발급하는 STS(보안 토큰 서비스, 즉 웹 서비스)가 있어야 합니다. [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithDRM/Program.cs)에 정의된 `GetTokenAsync` 메서드에 있는 코드를 테스트에 사용할 수 있습니다.

사용자가 인증되면 플레이어에서 STS에 토큰을 요청하고 토큰의 값으로 할당합니다. [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)를 사용할 수 있습니다.

대칭 키 또는 비대칭 키를 사용하여 STS를 실행하는 예제는 [JWT 도구](https://aka.ms/jwt)를 참조하세요. 이러한 JWT 토큰을 사용하는 Azure Media Player를 기반으로 하는 플레이어의 예제는 [Azure 미디어 테스트 도구](https://aka.ms/amtest)를 참조하세요. (토큰 입력을 보려면 **player_settings** 링크를 확장합니다.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>AES 암호화를 통해 비디오를 스트림할 수 있도록 요청에 권한을 부여하려면 어떻게 할까요?

올바른 방법은 STS(보안 토큰 서비스)를 활용하는 것입니다. STS에서 사용자 프로필에 따라 서로 다른 클레임(예: "프리미엄 사용자", "기본 사용자", "평가판 사용자")을 추가합니다. JWT에서 서로 다른 클레임을 사용하면 사용자가 각각의 콘텐츠를 볼 수 있습니다. 다른 콘텐츠 또는 자산의 경우는 `ContentKeyPolicyRestriction`이 해당 `RequiredClaims` 값을 갖습니다.

[이 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/main/AMSV3Tutorials/EncryptWithAES/Program.cs)과 같이 Azure Media Services API를 사용하여 라이선스/키 배달을 구성하고 자산을 암호화합니다.

자세한 내용은 다음을 참조하세요.

- [콘텐츠 보호 개요](drm-content-protection-concept.md)
- [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](architecture-design-multi-drm-system.md)

### <a name="should-i-use-http-or-https"></a>HTTP 또는 HTTPS를 사용해야 하나요?
ASP.NET MVC 플레이어 애플리케이션은 다음을 지원해야 합니다.

* Azure AD를 통한 사용자 인증이 HTTPS를 사용해야 함
* 클라이언트 및 Azure AD 간 JWT 교환이 HTTPS를 사용해야 함
* Media Services에서 라이선스 배달이 제공된 경우 클라이언트에 의한 DRM 라이선스 획득 시 HTTPS를 사용해야 함. PlayReady 제품군은 라이선스 배달에 대한 HTTPS를 위임하지 않습니다. PlayReady 라이선스 서버가 Media Services 외부에 있는 경우 HTTP 또는 HTTPS를 사용할 수 있습니다.

ASP.NET 플레이어 애플리케이션은 HTTPS를 사용하는 것이 가장 좋으므로 Media Player는 HTTPS를 사용하는 페이지에 있게 됩니다. 그러나 스트리밍의 경우 HTTP를 선호하므로 혼합 콘텐츠 문제를 고려해야 합니다.

* 브라우저에서는 혼합 콘텐츠를 허용하지 않습니다. 하지만 Silverlight와 같은 플러그 인과 Smooth 및 DASH를 위한 OSMF 플러그 인은 허용합니다. 혼합 콘텐츠는 고객 데이터가 위험해질 수 있는 악성 JavaScript를 주입할 위협이 있기 때문에 보안 문제가 발생할 수 있습니다. 브라우저는 기본적으로 이 기능을 차단합니다. 이 문제를 해결하는 유일한 방법이 서버(원본) 쪽에서 HTTPS 또는 HTTP에 관계없이 모든 도메인을 허용하는 것입니다. 하지만 좋은 방법은 아닙니다.
* 혼합 콘텐츠를 피해야 합니다. 플레이어 애플리케이션과 Media Player 둘 다 HTTP 또는 HTTPS를 사용해야 합니다. 혼합 콘텐츠를 재생할 때 SilverlightSS 기술은 혼합 콘텐츠 경고를 지워야 합니다. FlashSS 기술은 혼합 콘텐츠 경고 없이 혼합 콘텐츠를 처리합니다.
* 스트리밍 엔드포인트가 2014년 8월 전에 만들어진 경우 HTTPS를 지원하지 않습니다. 이 경우 HTTPS에 대한 새 스트리밍 엔드포인트를 만들어 사용하세요.

### <a name="what-about-live-streaming"></a>라이브 스트리밍의 경우는 어떨까요?

정확히 동일한 디자인 및 구현을 통해 프로그램과 연결된 자산을 VOD 자산으로 처리하여 Media Services에서 라이브 스트림을 보호하는 데 도움이 될 수 있습니다. 라이브 콘텐츠의 다중 DRM 보호를 제공하려면 자산을 라이브 입력에 연결하기 전에 마치 VOD 자산인 것처럼 자산에 동일한 설정/처리를 적용합니다.

### <a name="what-about-license-servers-outside-media-services"></a>Media Services 외부에서 라이선스 서버는 어떨까요?

고객들은 자체 데이터 센터 또는 DRM 서비스 공급자가 호스트하는 곳에 있는 라이선스 서버 팜에 투자하는 경우가 있었습니다. Media Services 콘텐츠 보호를 사용하면 하이브리드 모드로 작업할 수 있습니다. DRM 라이선스는 Media Services 외부 서버에서 제공되지만 콘텐츠는 Media Services에서 호스트되고 동적으로 보호될 수 있습니다. 이 경우 다음과 같은 변경을 고려하세요.

* STS는 라이선스 서버 팜에서 허용되고 확인할 수 있는 토큰을 발급해야 합니다. 예를 들어 Axinom에서 제공하는 Widevine 라이선스 서버에는 자격 메시지를 포함하는 특정 JWT가 필요합니다. 이러한 JWT를 발급하는 STS가 있어야 합니다. 
* 더 이상 Media Services에서 라이선스 배달 서비스를 구성하지 않아도 됩니다. `ContentKeyPolicy`를 구성할 때 라이선스 획득 URL(PlayReady, Widevine 및 FairPlay용)을 제공해야 합니다.

> [!NOTE]
> Widevine은 Google에서 제공하는 서비스로, Google의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 및 v3 비교 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Azure Portal을 사용하여 v3 리소스를 관리할 수 있나요?

현재는 [Azure Portal](https://portal.azure.com/)을 사용하여 다음과 같은 작업이 가능합니다.

* Media Services v3에서 [라이브 이벤트](live-event-outputs-concept.md)를 관리합니다. 
* v3 [자산](assets-concept.md)을 확인합니다(관리 안 함). 
* [API 액세스 정보를 가져옵니다](./access-api-howto.md). 

다른 모든 관리 작업(예: [변환 및 작업](transform-jobs-concept.md) 및 [콘텐츠 보호](drm-content-protection-concept.md))의 경우 [REST API](/rest/api/media/), [Azure CLI](/cli/azure/ams) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용합니다.

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3에 AssetFile 개념이 있나요?

Media Services를 Storage SDK 종속성과 분리하기 위해 `AssetFile` 컨셉이 Media Services API에서 제거되었습니다. 이제 Media Services가 아닌 Azure Storage에서 Storage SDK에 속한 정보를 보관합니다. 

자세한 내용은 [Media Services v3로 마이그레이션](migrate-v-2-v-3-migration-introduction.md)을 참조하세요.

### <a name="where-did-client-side-storage-encryption-go"></a>클라이언트 쪽 스토리지 암호화는 어디에 있나요?

이제 서버 쪽 스토리지 암호화(기본적으로 설정됨)를 사용하기 바랍니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](../../storage/common/storage-service-encryption.md)를 참조하세요.

## <a name="offline-streaming"></a>오프라인 스트리밍

### <a name="fairplay-streaming-for-ios"></a>iOS용 FairPlay 스트리밍

다음 질문과 대답은 iOS용 오프라인 FairPlay 스트리밍 문제 해결을 지원합니다.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>왜 오프라인 모드에서는 오디오만 재생되고 비디오는 재생되지 않나요?

이 동작은 의도적으로 샘플 앱의 것입니다. 오프라인 모드 중에 대체 오디오 트랙(HLS의 경우)이 있으면 iOS 10과 iOS 11 모두 대체 오디오 트랙으로 기본 설정됩니다. FPS 오프라인 모드에 대해 이 동작을 보정하려면 스트림에서 대체 오디오 트랙을 제거합니다. Media Services에서 이를 실행하기 위해 동적 매니페스트 필터 **audio-only=false** 를 추가합니다. 즉, HLS URL은 **.ism/manifest(format=m3u8-aapl,audio-only=false)** 로 끝납니다. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>audio-only=false를 추가한 후에도 왜 여전히 오프라인 모드에서 동영상 없이 오디오만 재생되나요?

CDN(콘텐츠 배달 네트워크)에 대한 캐시 키 디자인에 따라 콘텐츠가 캐시될 수 있습니다. 캐시를 제거합니다.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>FPS 오프라인 모드는 iOS 10 외에도 iOS 11에서 지원됩니까?

예. FPS 오프라인 모드는 iOS 10과 iOS 11 모두에서 지원됩니다.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>FPS Server SDK에서 “FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하여 오프라인 재생” 문서를 찾을 수 없는 이유는 무엇인가요?

FPS Server SDK 버전 4부터 이 문서는 “FairPlay Streaming Programming Guide”에 병합되었습니다.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>iOS 디바이스에서 다운로드된/오프라인 파일 구조체는 무엇입니까?

iOS 디바이스에 다운로드된 파일 구조체는 다음 스크린샷과 같습니다. `_keys` 폴더에는 다운로드된 FPS 라이선스가 저징됩니다(각 라이선스 서비스 호스트당 하나의 저장소 파일). `.movpkg` 폴더에는 오디오 및 동영상 콘텐츠가 저장됩니다. 

대시 다음 숫자로 끝나는 이름의 첫 번째 폴더는 동영상 콘텐츠를 포함합니다. 숫자 값은 동영상 변환의 최대 대역폭입니다. 대시에 이어 0으로 끝나는 이름의 두 번째 폴더는 오디오 콘텐츠를 포함합니다. `Data`라는 이름의 세 번째 폴더는 FPS 콘텐츠의 마스터 재생 목록을 포함합니다. 마지막으로, boot.xml은 `.movpkg` 폴더 내용에 대한 전체 설명을 제공합니다. 

![FairPlay iOS 샘플 앱에 대한 오프라인 파일 구조체](media/drm-offline-fairplay-for-ios-concept/offline-fairplay-file-structure.png)

샘플 boot.xml 파일:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Android용 Widevine 스트리밍

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>일부 클라이언트/사용자에게는 영구 라이선스(오프라인 사용 가능)를 제공하고 다른 일부에게는 비영구 라이선스(오프라인 사용 불가)를 제공하려면 어떻게 해야 하나요? 콘텐츠를 복제하고 별도 콘텐츠 키를 사용해야 하나요?

Media Services v3에서는 자산에 여러 `StreamingLocator` 인스턴스를 포함할 수 있기 때문에 다음을 가질 수 있습니다.

* `license_type = "persistent"`, `"persistent"`에 클레임이 있는 `ContentKeyPolicyRestriction`, 해당 `StreamingLocator`를 가진 한 개의 `ContentKeyPolicy` 인스턴스.
* `license_type="nonpersistent"`, `"nonpersistent`에 클레임이 있는 `ContentKeyPolicyRestriction`, 해당 `StreamingLocator`를 가진 또 다른 `ContentKeyPolicy` 인스턴스.
* 서로 다른 `StreamingLocator` 값을 가진 두 개의 `ContentKey` 인스턴스.

사용자 지정 STS의 비즈니스 논리에 따라 JWT 토큰에서 다른 클레임이 발급됩니다. 이 토큰을 사용하여 해당 라이선스만 가져올 수 있고 해당 URL만 재생할 수 있습니다.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Widevine과 Media Services DRM 보안 수준 간의 매핑은 무엇입니까?

Google의 “Widevine DRM 아키텍처 개요”에서는 3가지 보안 수준을 정의하고 있습니다. 그러나 [Widevine 라이선스 템플릿에 대한 Azure Media Services 설명서](drm-widevine-license-template-concept.md)는 5가지 보안 수준(재생을 위한 클라이언트 견고성 요구 사항)을 간략하게 설명합니다. 이 섹션에서는 보안 수준을 매핑하는 방법에 대해 설명합니다.

두 보안 수준 집합은 Google Widevine에 의해 정의됩니다. 차이점은 사용 수준이 아키텍처인지 API인지입니다. 5가지 보안 수준은 Widevine API에서 사용됩니다. `content_key_specs`을 포함하는 `security_level` 개체는 Azure Media Services Widevine 라이선스 서비스에 의해 역직렬화된 후 Widevine 전역 배달 서비스로 전달됩니다. 다음 표에서는 두 보안 수준 집합 간의 매핑을 보여줍니다.

| **Widevine 아키텍처에서 정의되는 보안 수준** |**Widevine API에서 사용되는 보안 수준**|
|---|---| 
| **보안 수준 1**: 모든 콘텐츠 처리, 암호화 및 제어가 TEE(신뢰할 수 있는 실행 환경)에서 수행됩니다. 일부 구현 모델에서 보안 처리는 다른 칩에서 수행될 수 있습니다.|**security_level=5**: 하드웨어 기반의 TEE에서 미디어의 암호화, 디코딩 및 모든 처리(압축 및 압축 해제)를 수행해야 합니다.<br/><br/>**security_level=4**: 하드웨어 기반의 TEE에서 콘텐츠의 암호화 및 디코딩을 수행해야 합니다.|
**보안 수준 2**: 암호화(비디오 처리는 아님)는 TEE에서 수행됩니다. 암호 해독된 버퍼는 애플리케이션 도메인으로 반환되고 별도의 비디오 하드웨어 또는 소프트웨어를 통해 처리됩니다. 그러나 수준 2에서 암호화 정보는 여전히 TEE 내에서만 처리됩니다.| **security_level=3**: 키 자료 및 암호화 작업은 하드웨어 기반의 TEE에서 수행되어야 합니다. |
| **보안 수준 3**: 디바이스에는 TEE가 없습니다. 호스트 운영 체제의 암호화 정보 및 암호 해독된 콘텐츠를 보호하기 위해 적절한 조치가 수행될 수 있습니다. 수준 3 구현에도 하드웨어 암호화 엔진이 포함될 수 있지만 보안이 아닌 성능만 향상시킵니다. | **security_level=2**: 소프트웨어 암호화 및 난독 처리된 디코더가 필요합니다.<br/><br/>**security_level=1**: 소프트웨어 기반 화이트 박스 암호화가 필요합니다.|

#### <a name="why-does-content-download-take-so-long"></a>콘텐츠 다운로드 시간이 너무 오래 걸리는 이유는 무엇인가요?

다운로드 속도를 개선하는 방법으로는 다음 두 가지가 있습니다.

* 사용자가 콘텐츠 다운로드를 위해 원본/스트리밍 엔드포인트 대신 CDN에 연결하기 쉽게 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정합니다. 사용자가 스트리밍 엔드포인트에 도달하는 경우 각 HLS 세그먼트 또는 DASH 조각이 동적으로 패키징되고 암호화됩니다. 각 세그먼트나 조각에 대한 대기 시간이 밀리초 규모에 불과하지만, 1시간 분량의 비디오가 있는 경우 누적 대기 시간이 늘어나 다운로드가 더 오래 걸릴 수 있습니다.
* 사용자에게 모든 콘텐츠가 아닌 비디오 품질 레이어와 오디오 트랙만 선택적으로 다운로드할 수 있는 옵션을 제공합니다. 오프라인 모드에서는 모든 품질 레이어를 다운로드할 필요가 없습니다. 두 가지 방법으로 이 작업을 수행할 수 있습니다.

  * 클라이언트 제어: 다운로드할 비디오 품질 레이어와 오디오 트랙을 플레이어 앱이 자동으로 선택하거나 사용자가 선택하도록 합니다.
  * 서비스 제어: Azure Media Services의 동적 매니페스트 기능을 이용하여 (전역) 필터를 만들 수 있습니다. 이는 HLS 재생 목록 또는 DASH MPD를 단일 비디오 품질 레이어 및 선택된 오디오 트랙으로 제한합니다. 그러면 사용자에게 표시되는 다운로드 URL에 이 필터가 포함됩니다.

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)