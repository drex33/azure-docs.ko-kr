---
title: Azure 주문형 미디어 인코더 개요 | Microsoft Docs
description: Azure Media Services는 클라우드에서 미디어의 인코딩에 대한 여러 옵션을 제공합니다. 이 문서에서는 Azure 주문형 미디어 인코더에 대해 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f45069f0e2184e77d701a76a56f0cbd42ed63bd1
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706314"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Azure 주문형 미디어 인코더 개요

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Azure Media Services는 클라우드에서 미디어의 인코딩에 대한 여러 옵션을 제공합니다.

Media Services로 시작하는 경우 코덱과 파일 형식 간의 차이점을 이해하는 것은 중요합니다.
코덱은 압축/압축 해제 알고리즘을 구현하는 소프트웨어이고 파일 형식은 압축된 비디오를 보관하는 컨테이너입니다.

Media Services는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 Media Services에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

Azure Media Services 계정이 만들어지면 **기본** 스트리밍 엔드포인트가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 스트리밍 엔드포인트에 대한 요금은 엔드포인트가 **실행 중** 상태일 때마다 발생합니다.

Media Services는 다음의 주문형 인코더를 지원합니다.

* [미디어 인코더 표준](media-services-encode-asset.md#media-encoder-standard)

이 문서에서는 주문형 미디어 인코더에 대한 간략한 개요와 보다 자세한 정보를 제공하는 문서에 대한 링크를 제공합니다.

기본적으로 각 Media Services 계정은 한번에 하나의 인코딩 작업을 활성화할 수 있습니다. 구입한 각 인코딩 예약 단위에 대해 하나씩, 여러 인코딩 작업을 동시에 실행할 수 있는 인코딩 단위를 예약할 수 있습니다. 자세한 내용은 [인코딩 단위 크기 조정](media-services-scale-media-processing-overview.md)을 참조하세요.

## <a name="media-encoder-standard"></a>미디어 인코더 표준

### <a name="how-to-use"></a>사용 방법
[미디어 인코더 표준으로 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>형식
[형식 및 코덱](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>기본 설정
미디어 인코더 표준은 [여기](./media-services-mes-presets-overview.md)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다.

### <a name="input-and-output-metadata"></a>입력 및 출력 메타데이터
인코더 입력 메타데이터는 [여기](media-services-input-metadata-schema.md)에서 설명합니다.

인코더 출력 메타데이터는 [여기](media-services-output-metadata-schema.md)에서 설명합니다.

### <a name="generate-thumbnails"></a>미리 보기 생성
자세한 내용은 [미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법](media-services-advanced-encoding-with-mes.md)을 참조하세요.

### <a name="trim-videos-clipping"></a>비디오 자르기(클리핑)
자세한 내용은 [미디어 인코더 표준을 사용하여 비디오를 자르는 방법](media-services-advanced-encoding-with-mes.md#trim_video)을 참조하세요.

### <a name="create-overlays"></a>오버레이 만들기
자세한 내용은 [미디어 인코더 표준을 사용하여 오버레이를 만드는 방법](media-services-advanced-encoding-with-mes.md#overlay)을 참조하세요.

### <a name="see-also"></a>참고 항목
[Media Services 블로그](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>알려진 문제
입력된 비디오에 자막이 없는 경우, 출력 자산은 빈 TTML 파일을 포함합니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>관련된 문서
* [미디어 인코더 표준 사전 설정을 사용자 지정하여 고급 인코딩 작업 수행](media-services-custom-mes-presets-with-dotnet.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/