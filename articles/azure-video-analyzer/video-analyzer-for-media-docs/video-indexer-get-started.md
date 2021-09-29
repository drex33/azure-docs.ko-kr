---
title: Azure Video Analyzer for Media(이전의 Video Indexer)에 등록하고 첫 번째 비디오 업로드 - Azure
description: Azure Video Analyzer for Media(이전의 Video Indexer) 포털을 사용하여 등록하고 첫 번째 비디오를 업로드하는 방법에 대해 알아봅니다.
ms.topic: quickstart
ms.subservice: azure-video-analyzer-media
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: bf8b8f640fbcb6155092a50fdbd6e1af66060e07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636157"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>빠른 시작: 가입하고 첫 번째 비디오를 업로드하는 방법

이 시작 빠른 시작에서는 Azure Video Analyzer for Media(이전의 Video Indexer) 웹 사이트에 로그인하고 첫 번째 비디오를 업로드하는 방법을 보여줍니다.

Video Analyzer for Media 계정을 만들 때 평가판 계정(특정의 체험 인덱싱 시간(분)을 가져오는 경우) 또는 유료 옵션(할당량으로 제한되지 않은 경우)을 선택할 수 있습니다. 평가판을 사용하면 Video Analyzer for Media에서 웹 사이트 사용자에게 최대 600분의 체험 인덱싱을 제공하고, API 사용자에게는 최대 2,400분의 체험 인덱싱을 제공합니다. 유료 옵션을 사용하면 [Azure 구독 및 Azure Media Services 계정에 연결되는](connect-to-azure.md) Video Analyzer for Media 계정을 만듭니다. 인덱싱된 시간(분)에 대한 비용을 지불하는 경우 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요. 

## <a name="sign-up-for-video-analyzer-for-media"></a>Video Analyzer for Media에 등록

Video Analyzer for Media를 사용하여 개발을 시작하려면 [Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트로 이동하여 등록합니다.

Video Analyzer for Media를 사용하기 시작하면 저장된 모든 데이터와 업로드된 콘텐츠가 Microsoft 관리 키를 사용하여 미사용으로 암호화됩니다.

> [!NOTE]
> [계획된 Video Analyzer for Media 웹 사이트 인증 변경](./release-notes.md#planned-video-analyzer-for-media-website-authenticatication-changes)을 검토합니다.

## <a name="upload-a-video-using-the-video-analyzer-for-media-website"></a>Video Analyzer for Media 웹 사이트를 사용하여 비디오 업로드

### <a name="supported-file-formats-for-video-analyzer-for-media"></a>Video Analyzer for Media의 지원되는 파일 형식

Video Analyzer for Media와 함께 사용할 수 있는 파일 형식 목록은 [입력 컨테이너/파일 형식](../../media-services/latest/encode-media-encoder-standard-formats-reference.md) 문서를 참조하세요.

### <a name="upload-a-video"></a>비디오 업로드

1. [Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트에 로그인합니다.
1. 비디오를 업로드하려면 **업로드** 단추나 링크를 누릅니다.

    > [!NOTE]
    > 비디오 이름은 80자를 넘지 않아야 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="업로드":::
1. 비디오가 업로드되면 Video Analyzer for Media가 인덱싱 및 비디오 분석을 시작합니다. 진행률이 표시됩니다. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="업로드 진행률":::
1. Video Analyzer for Media가 분석을 완료하면 비디오에 대한 링크와 비디오 분석 결과에 관한 간단한 설명이 포함된 메일을 받게 됩니다. 예: 사람, 음성 및 필기 단어, 토픽 및 명명된 엔터티.
1. 나중에 라이브러리 목록에서 비디오를 찾아 다른 작업을 수행할 수 있습니다. 예: 검색, 다시 인덱싱, 편집.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="업로드가 업로드됨":::

## <a name="supported-browsers"></a>지원되는 브라우저

자세한 내용은 [지원되는 브라우저](video-indexer-overview.md#supported-browsers)를 참조하세요.

## <a name="see-also"></a>참고 항목

자세한 내용은 [비디오 업로드 및 인덱싱](upload-index-videos.md)을 참조하세요.

비디오를 업로드하고 인덱싱한 후 [Video Analyzer for Media 웹 사이트](video-indexer-view-edit.md) 또는 [ 개발자 포털](video-indexer-use-apis.md)을 사용하여 비디오의 인사이트를 확인할 수 있습니다. 

[API 사용 시작](video-indexer-use-apis.md)

## <a name="next-steps"></a>다음 단계

자세한 소개는 [소개 랩](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)을 방문하세요. 

워크숍을 마치면 비디오 및 오디오 콘텐츠에서 추출할 수 있는 정보의 종류를 잘 이해할 수 있을 것이며, 콘텐츠 인텔리전스와 관련된 기회를 식별하고, Azure에서 비디오 AI를 피치하고, Video Analyzer for Media에 대한 여러 시나리오를 시연할 준비가 되어 있을 것입니다.

