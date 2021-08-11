---
title: Video Indexer를 사용하여 비디오에서 정확한 순간 검색
titleSuffix: Azure Media Services
description: Video Indexer를 사용하여 비디오에서 정확한 시간을 검색하는 방법을 알아봅니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030531"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Video Indexer를 사용하여 비디오에서 정확한 순간 검색

이 항목에서는 Video Indexer 웹 사이트를 사용하여 비디오에서 정확한 순간을 검색하는 방법을 보여 줍니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
1. 검색 키워드를 지정하면 계정 라이브러리의 모든 비디오에서 검색이 수행됩니다. 

    **필터** 를 선택하여 검색을 필터링할 수 있습니다. 아래 예제에서는 화면 텍스트만(OCR)으로 표시되는 “Microsoft”를 검색합니다.

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="필터, 텍스트만":::
1. 결과를 보려면 **검색** 을 누릅니다.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="비디오 검색 결과":::

    결과 중 하나를 선택하면 플레이어가 비디오의 정확한 순간으로 이동합니다.
1. 비디오에서 **재생** 을 클릭하거나 원래 검색 결과 중 하나를 선택하여 비디오의 요약된 인사이트를 보고 검색합니다. 

    **인사이트** 를 보고, 검색하고, 편집할 수 있습니다. 인사이트 중 하나를 선택하면 플레이어가 비디오의 정확한 순간으로 이동합니다.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="비디오 인사이트 보기, 검색, 편집":::

    Video Indexer 위젯을 통해 비디오를 포함하면 앱에서 플레이어/인사이트 보기와 동기화를 달성할 수 있습니다. 자세한 내용은 [앱에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요.
1. **타임라인** 탭을 클릭하여 대본을 보고, 검색하고, 편집할 수 있습니다. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="비디오 대본 보기, 검색, 편집":::

    텍스트를 편집하려면 오른쪽 위에서 **편집** 을 선택하고 필요에 따라 텍스트를 변경합니다. 

    오른쪽 위에서 적절한 옵션을 선택하여 대본을 번역하고 다운로드할 수도 있습니다. 

## <a name="embed-download-create-projects"></a>프로젝트 포함, 다운로드, 만들기

비디오 아래에 있는 **</>포함** 을 선택하여 비디오를 포함할 수 있습니다. 자세한 내용은 [애플리케이션에 시각적 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요.

비디오 아래에 있는 **다운로드** 를 클릭하여 원본 비디오, 비디오 인사이트, 대본을 다운로드할 수 있습니다.

**편집기에서 열기** 를 클릭하여 특정 대사와 순간의 비디오를 기반으로 클립을 만들 수 있습니다. 그런 다음, 비디오를 편집하고 프로젝트를 저장합니다. 자세한 내용은 [비디오의 심층 인사이트 사용](use-editor-create-project.md)을 참조하세요.

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="비디오의 프로젝트 포함, 다운로드, 만들기":::

## <a name="next-steps"></a>다음 단계

[Video Indexer REST API를 사용하여 콘텐츠 처리](video-indexer-use-apis.md)
