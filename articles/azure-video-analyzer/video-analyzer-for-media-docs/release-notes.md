---
title: Azure Video Analyzer for Media(이전 이름 Video Indexer) 릴리스 정보 | Microsoft Docs
description: 최신 개발 정보를 확인할 수 있도록 이 문서에서는 Azure Video Analyzer for Media(이전 이름 Video Indexer)의 최신 업데이트에 관한 정보를 제공합니다.
ms.topic: article
ms.custom: references_regions
ms.date: 08/01/2021
ms.author: juliako
ms.openlocfilehash: 115402133ad33b9fa20eb3298db3e342ea2afbbd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461687"
---
# <a name="video-analyzer-for-media-release-notes"></a>Video Analyzer for Media 릴리스 정보

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

최신 Azure Video Analyzer for Media(이전 이름 Video Indexer) 개발 정보를 제공하기 위해 이 문서에서는 다음에 대한 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="november-2021"></a>2021년 11월
 
### <a name="public-preview-of-azure-video-analyzer-for-media-account-management-based-on-arm"></a>ARM 기반 미디어 계정 관리용 Azure Video Analyzer의 공개 미리 보기

미디어 용 Azure 비디오 분석기는 ARM (Azure Resource Manager) 기반 계정 관리의 공개 미리 보기를 소개 합니다. ARM 기반 Api를 활용 하 여 Azure Portal에서 계정을 만들고 편집 하 고 삭제할 수 있습니다.

자세한 내용은 [미디어 계정에 대 한 비디오 분석기 만들기](https://techcommunity.microsoft.com/t5/azure-ai/azure-video-analyzer-for-media-is-now-available-as-an-azure/ba-p/2912422)를 참조 하세요.

## <a name="october-2021"></a>2021년 10월

### <a name="embed-widgets-in-your-app-using-azure-video-analyzer-for-media-package"></a>미디어 패키지용 Azure Video Analyzer를 사용 하 여 앱에 위젯 포함

새 AVAM (미디어 용 Azure Video Analyzer) `@azure/video-analyzer-for-media-widgets` npm 패키지를 사용 하 여 `insights` 앱에 위젯을 추가 하 고 필요에 따라 사용자 지정 합니다.

새 AVAM 패키지를 사용 하면 `iframe` insights 위젯을 포함 하는 요소를 추가 하는 대신 위젯 및 앱 간에 쉽게 포함 하 고 통신할 수 있습니다. [앱에서 미디어 위젯의 비디오 분석기를 포함 하 고 사용자 지정](https://techcommunity.microsoft.com/t5/azure-media-services/embed-and-customize-azure-video-analyzer-for-media-widgets-in/ba-p/2847063)에서 자세히 알아보세요. 

## <a name="august-2021"></a>2021년 8월

### <a name="re-index-video-or-audio-files"></a>비디오 또는 오디오 파일 다시 인덱싱

이제 인덱싱 프로세스 중에 실패 한 비디오 또는 오디오 파일을 다시 인덱싱하는 옵션이 있습니다.

### <a name="improve-accessibility-support"></a>내게 필요한 옵션 지원 향상

CSS, 테마 및 접근성과 관련 된 버그가 수정 되었습니다.

* 고대비
* [포털](https://www.videoindexer.ai)의 계정 설정 및 정보 보기  

## <a name="july-2021"></a>2021년 7월

### <a name="automatic-scaling-of-media-reserved-units"></a>미디어 예약 단위의 자동 스케일링
 
2021년 8월 1일부터 Azure Video Analyzer for Media(이전 이름 Video Indexer)는 [Azure Media Services](../../media-services/latest/media-services-overview.md)에 의한 [MRU(Media Reserved Unit)](../../media-services/latest/concept-media-reserved-units.md) 자동 스케일링을 사용하도록 설정했습니다. 따라서 이제 Azure Video Analyzer for Media를 통해 관리할 필요가 없습니다. 자동 스케일링이 수행되므로, 비즈니스 요구 사항에 따라 가격 최적화(예: 많은 경우 가격 인하)가 가능합니다.

## <a name="june-2021"></a>2021년 6월
 
### <a name="video-analyzer-for-media-deployed-in-six-new-regions"></a>Video Analyzer for Media가 6개의 새 지역에 배포됨
 
이제 프랑스 중부, 미국 중부, 브라질 남부, 미국 중서부, 한국 중부, 일본 서부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다.
  
## <a name="may-2021"></a>2021년 5월

### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>STT(음성 텍스트 변환), 번역, 검색을 위한 새로운 소스 언어 지원

Video Analyzer for Media는 이제 중국어(광둥어)('zh-HK'), 네덜란드어(네덜란드)('Nl-NL'), 체코어('Cs-CZ'), 폴란드어('Pl-PL'), 스웨덴어(스웨덴)('Sv-SE'), 노르웨이어('nb-NO'), 핀란드어('fi-FI'), 캐나다 프랑스어('fr-CA'), 태국어('th-TH'), 아랍어(아랍에미리트)('ar-AE', 'ar-EG'), (이라크)('ar-IQ'), (요르단)('ar-JO'), (쿠웨이트)('ar-KW'), (레바논)('ar-LB'), (오만)('ar-OM'), (카타르)('ar-QA'), (팔레스타인 자치 정부) ('ar-PS'), (시리아)('ar-SY') 및 터키어('tr-TR')의 STT, 번역 및 검색을 지원합니다. 

이러한 언어는 API 및 Video Analyzer for Media 둘 다에서 사용할 수 있습니다. **비디오 원본 언어** 의 콤보 상자에서 언어를 선택합니다.

### <a name="new-theme-for-azure-video-analyzer-for-media"></a>Azure Video Analyzer for Media의 새로운 테마

새 테마를 사용할 수 있습니다. '밝은' 테마 및 '어두운 테마'와 함께 'Azure' 테마를 사용할 수 있습니다. 테마를 선택하려면 웹 사이트의 오른쪽 위 구석에 있는 기어 아이콘을 클릭하고 **사용자 설정** 에서 테마를 찾습니다.
 
### <a name="new-open-source-code-you-can-leverage"></a>활용할 수 있는 새로운 오픈 소스 코드 

[GitHub 리포지토리](https://github.com/Azure-Samples/media-services-video-indexer)에서 다음 세 가지 새로운 GitHub 프로젝트를 사용할 수 있습니다.

* 새로 추가된 [위젯 사용자 지정](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets)을 활용하는 데 도움이 되는 코드
* 비디오 라이브러리에 [사용자 지정 검색](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/VideoSearchWithAutoMLVision)을 추가하는 데 도움이 되는 솔루션
* 비디오 라이브러리에 [중복 제거](https://github.com/Azure-Samples/media-services-video-indexer/commit/6b828f598f5bf61ce1b6dbcbea9e8b87ba11c7b1) 기능을 추가하는 데 도움이 되는 솔루션
 
### <a name="new-option-to-toggle-bounding-boxes-for-observed-people-on-the-player"></a>플레이어의 경계 상자(관찰된 사람들)를 설정/해제하는 새로운 옵션  

고급 비디오 설정을 통해 비디오를 인덱싱하면 새로운 관찰된 사람 기능을 볼 수 있습니다. 미디어 파일에서 사람이 검색되면 미디어 플레이어를 통해 검색된 사용자에 대한 경계 상자를 사용하도록 설정할 수 있습니다.

## <a name="april-2021"></a>2021년 4월

Video Indexer 서비스의 이름이 Azure Video Analyzer for Media로 바뀌었습니다.

### <a name="improved-upload-experience-in-the-portal"></a>포털의 업로드 환경 개선
 
Video Analyzer for Media는 [포털](https://www.videoindexer.ai)에서 다음과 같은 새로운 업로드 환경을 제공합니다. 미디어 파일을 업로드하려면 **미디어 파일** 탭에서 **업로드** 단추를 누릅니다.

### <a name="new-developer-portal-in-available-in-gov-cloud"></a>정부 클라우드에서 새 개발자 포털 사용 가능
 
[Video Analyzer for Media 개발자 포털](https://api-portal.videoindexer.ai)을 이제 미국 정부용 Azure에서도 사용할 수 있습니다.

### <a name="observed-people-tracing-preview"></a>관찰된 사람 추적(미리 보기)  

Azure Video Analyzer for Media는 비디오에서 관찰된 사람을 감지하고 비디오 프레임에서 사람의 위치 및 사람이 나타나는 시간의 정확한 타임스탬프(시작, 종료)와 같은 정보를 제공합니다. API는 해당 신뢰도를 포함하여 감지된 각 사람 인스턴스에 대한 경계 상자 좌표(픽셀 단위)를 반환합니다. 

예를 들어 비디오에 사람이 포함된 경우 감지 작업은 비디오 프레임의 좌표와 함께 사람의 모습을 나열합니다. 이 기능을 사용하여 비디오에서 사람의 경로를 확인할 수 있습니다. 또한 이 기능을 통해 비디오에 동일한 사람의 여러 인스턴스가 있는지 확인할 수 있습니다. 

새로 추가된 관찰된 사람 추적 기능은 **고급 옵션** -> **고급 비디오** 또는 **고급 비디오 + 오디오** 사전 설정(비디오 + 오디오 인덱싱)을 선택하여 파일을 인덱싱할 때 사용할 수 있습니다. 표준 및 기본 인덱싱 사전 설정에는 이 새로운 고급 모델이 포함되지 않습니다. 

Video Analyzer for Media 웹 사이트에서 비디오의 인사이트를 보도록 선택하면 감지된 모든 사람의 썸네일과 함께 관찰된 사람 추적이 페이지에 표시됩니다. 특정인의 썸네일을 선택하고 비디오 플레이어에서 해당 인물이 표시되는 위치를 확인할 수 있습니다.  

Video Analyzer for Media에서 생성된 JSON 파일에서도 이 기능을 사용할 수 있습니다. 자세한 내용은 [비디오에서 관찰된 사람 추적](observed-people-tracing.md)을 참조하세요.

### <a name="detected-acoustic-events-with-audio-effects-detection-preview"></a>**오디오 효과 감지** 를 사용하여 감지된 음향 이벤트(미리 보기)

이제 감지된 음향 이벤트를 선택 자막 파일에서 볼 수 있습니다. 이 파일은 Video Analyzer for Media 포털에서 다운로드할 수 있으며 GetArtifact API에서 아티팩트로 사용할 수 있습니다.

**오디오 효과 감지**(미리 보기) 구성 요소는 다양한 음향 이벤트를 감지하고 이를 다양한 음향 범주(예: 총성, 비명, 군중 반응 등)로 분류합니다. 자세한 내용은 [오디오 효과 감지](audio-effects-detection.md)를 참조하세요.

## <a name="march-2021"></a>2021년 3월

### <a name="audio-analysis"></a>오디오 분석 

이제 다른 가격대의 새로운 오디오 기능 번들에서 오디오 분석을 사용할 수 있습니다. 새로운 **기본 오디오** 분석 사전 설정은 음성 대화 내용 기록, 번역 및 서식 출력 캡션과 자막만 추출하는 저렴한 옵션을 제공합니다. **기본 오디오** 사전 설정은 대화 내용 기록을 위한 줄과 캡션 및 자막 서식 지정을 위한 별도의 줄을 포함하여 청구서에 두 개의 개별 미터를 생성합니다. 가격 책정에 관한 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지를 참조하세요.

새로 추가된 번들은 **비디오 + 오디오 인덱싱** 드롭다운 상자 아래에서 **고급 옵션** -> **기본 오디오** 사전 설정을 선택하여 파일을 인덱싱 또는 다시 인덱싱할 때 사용할 수 있습니다.

### <a name="new-developer-portal"></a>새 개발자 포털 

Video Analyzer for Media에 새로운 [개발자 포털](https://api-portal.videoindexer.ai/)이 생겼습니다. 새 Video Analyzer for Media API를 사용해 보고 다음과 같은 관련 리소스를 모두 한곳에서 찾아보세요. 피드백을 제공하고, 기능을 제안하고, 위젯 코드 샘플과 함께 ['CodePen' 링크](https://codepen.io/videoindexer)를 제공하기 위한 [GitHub 리포지토리](https://github.com/Azure-Samples/media-services-video-indexer), [Stack Overflow](https://stackoverflow.com/questions/tagged/video-indexer), 관련 블로그 게시물과 [Video Analyzer for Media FAQ](faq.yml), [사용자 의견](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858)이 있는 [Video Analyzer for Media 기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer). 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>인사이트 위젯을 위한 고급 사용자 지정 기능 

이제 SDK를 사용하여 Video Analyzer for Media의 인사이트 위젯을 사용자의 서비스에 포함하고 해당 스타일 및 데이터를 사용자 지정할 수 있습니다. SDK는 표준 Video Analyzer for Media 인사이트 위젯 및 완전히 사용자 지정 가능한 인사이트 위젯을 지원합니다. 코드 샘플은 [Video Analyzer for Media GitHub 리포지토리](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization)에서 사용할 수 있습니다. 이 고급 사용자 지정 기능을 사용하는 솔루션 개발자는 사용자 지정 스타일을 적용하고 고객의 고유한 AI 데이터를 제공하고 인사이트 위젯에 제공(Video Analyzer for Media 인사이트 포함 또는 미포함) 할 수 있습니다. 

### <a name="video-analyzer-for-media-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Analyzer for Media가 미국 중북부, 미국 서부, 캐나다 중부에 배포됨 

이제 미국 중북부, 미국 서부, 캐나다 중부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다.
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>STT(음성 텍스트 변환), 번역, 검색을 위한 새로운 소스 언어 지원 

이제 Video Analyzer for Media는 덴마크어('da-DK'), 노르웨이어('nb-NO'), 스웨덴어('sv-SE'), 핀란드어('fi-FI'), 캐나다 프랑스어('fr-CA'), 태국어('th-TH'), 아랍어('ar-BH', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-OM', 'ar-QA', 'ar-S', 'ar-SY'), 터키어('tr-TR')로 STT, 번역 및 검색을 지원합니다. 이러한 언어는 API 및 Video Analyzer for Media 둘 다에서 사용할 수 있습니다. 
 
### <a name="search-by-topic-in-video-analyzer-for-media-website"></a>Video Analyzer for Media 사이트에서 토픽으로 검색 

이제 [Video Analyzer for Media 웹 사이트](https://www.videoindexer.ai/account/login) 페이지 맨 위에 있는 검색 기능을 사용하여 특정 토픽의 비디오를 검색할 수 있습니다. 

## <a name="february-2021"></a>2021년 2월

### <a name="multiple-account-owners"></a>여러 계정 소유자 

Video Analyzer for Media에 계정 소유자 역할을 추가했습니다. 사용자를 추가, 변경, 제거하고 해당 역할을 변경할 수 있습니다. 계정을 공유하는 방법에 관한 자세한 내용은 [사용자 초대](invite-users.md)를 참조하세요.

### <a name="audio-event-detection-public-preview"></a>오디오 이벤트 검색(퍼블릭 미리 보기)

> [!NOTE]
> 이 기능은 평가판 계정에서만 사용할 수 있습니다. 

Video Analyzer for Media는 이제 콘텐츠의 음성이 아닌 세그먼트에서 검색할 수 있는 오디오 효과는 총소리, 유리 깨지는 소리, 경보, 사이렌, 폭발, 개 짖는 소리, 비명, 웃음, 군중 반응(환호, 박수, 야유), 무음입니다. 

새로 추가된 오디오 효과 기능은 **고급 옵션**  ->  **고급 오디오** 사전 설정(비디오 및 오디오 인덱싱)을 선택하여 파일을 인덱싱할 때 사용할 수 있습니다. 표준 인덱싱에는 **무음** 및 **군중 반응** 만 포함됩니다. 

이전 오디오 효과 모델에 포함된 **박수** 이벤트 유형은 이제 **군중 반응** 이벤트 유형의 일부로 추출됩니다.

[Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트에서 비디오를 위한 **인사이트** 를 보기로 선택하면 오디오 효과가 페이지에 표시됩니다.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="오디오 이벤트 검색":::

### <a name="named-entities-enhancement"></a>향상된 명명된 엔터티 기능  

추출한 사용자 및 위치 목록이 전체적으로 확장 및 업데이트되었습니다. 

또한 모델에는 비디오의 '샘’ 또는 '집'과 같이 유명하지 않은 사람 및 상황에 맞는 위치가 포함되어 있습니다. 

## <a name="january-2021"></a>2021년 1월

### <a name="video-analyzer-for-media-is-deployed-on-us-government-cloud"></a>Video Analyzer for Media가 미국 정부 클라우드에 배포됨 

이제 버지니아 지역 및 애리조나 지역에서 미국 정부 클라우드의 Video Analyzer for Media 유료 계정을 만들 수 있습니다. 해당 지역에서 Video Analyzer for Media 평가판 제품은 사용할 수 없습니다. 자세한 내용은 Video Analyzer for Media 설명서를 참조하세요. 

### <a name="video-analyzer-for-media-deployed-in-the-india-central-region"></a>Video Analyzer for Media가 인도 중부 지역에 배포됨 

이제 인도 중부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다. 

### <a name="new-dark-mode-for-the-video-analyzer-for-media-website-experience"></a>Video Analyzer for Media 웹 사이트 환경의 새 다크 모드

이제는 Video Analyzer for Media 웹 사이트 환경을 다크 모드로 사용할 수 있습니다. 어둡게 모드를 사용하도록 설정하려면 설정 패널을 열고 **다크 모드** 옵션을 설정합니다. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="다크 모드 설정":::

## <a name="december-2020"></a>2020년 12월

### <a name="video-analyzer-for-media-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Analyzer for Media가 스위스 서부 및 스위스 북부에 배포됨

이제 스위스 서부 및 스위스 북부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다.

## <a name="october-2020"></a>2020년 10월

### <a name="animated-character-identification-improvements"></a>애니메이션 문자 식별 개선  

Video Analyzer for Media는 Cognitive Services 사용자 지정 비전과의 통합을 통해 애니메이션 콘텐츠에서 캐릭터의 검색, 그룹화, 인식을 지원합니다. 해당 AI 알고리즘의 검색 및 문자 인식 기능을 크게 개선하여, 결과 인사이트 정확도 및 문자 식별이 크게 향상되었습니다.

### <a name="planned-video-analyzer-for-media-website-authenticatication-changes"></a>계획된 Video Analyzer for Media 웹 사이트 인증 변경

2021년 3월 1일부터 더 이상 Facebook 또는 LinkedIn을 사용하여 [Video Analyzer for Media 웹 사이트](https://www.videoindexer.ai/) [개발자 포털](video-indexer-use-apis.md)에 가입 및 로그인할 수 없습니다.

Azure AD, Microsoft, Google 등의 공급자 중 하나를 사용하여 가입하고 로그인할 수 있습니다.

> [!NOTE]
> LinkedIn 및 Facebook에 연결된 Video Analyzer for Media 계정은 2021년 3월 1일 이후에는 액세스할 수 없습니다. 
> 
> 사용자가 소유하고 있는 Azure AD, Microsoft 또는 Google 메일을 Video Analyzer for Media 계정에 [초대](invite-users.md)하여 액세스할 수 있게 해야 합니다. [초대](invite-users.md)의 설명에 따라 지원되는 공급자의 추가 소유자를 추가할 수 있습니다. <br/>
> 또는 유료 계정을 만들고 데이터를 마이그레이션할 수 있습니다.

## <a name="august-2020"></a>2020년 8월

### <a name="mobile-design-for-the-video-analyzer-for-media-website"></a>Video Analyzer for Media 웹 사이트에 대한 모바일 디자인

이제 Video Analyzer for Media 웹 사이트 환경에서 모바일 디바이스를 지원합니다. 사용자 환경이 모바일 화면 크기에 맞게 조정됩니다(사용자 지정 UI 제외). 

### <a name="accessibility-improvements-and-bug-fixes"></a>접근성 향상 및 버그 수정 

WCAG(웹 콘텐츠 접근성 지침)의 일환으로 Video Analyzer for Media 웹 사이트 환경은 Microsoft 접근성 표준의 일부로 등급 C에 맞춥니다. 키보드 탐색, 프로그래밍 방식 액세스, 화면 읽기 프로그램과 관련된 몇 가지 버그를 해결하고 개선 사항을 추가하였습니다. 

## <a name="july-2020"></a>2020년 7월

### <a name="ga-for-multi-language-identification"></a>다중 언어 식별을 위한 GA

다중 언어 ID는 생산적인 사용을 위해 미리 보기에서 GA로 이동하였습니다.

"미리 보기에서 GA로" 전환되었지만, 가격 책정에는 영향이 없습니다.

### <a name="video-analyzer-for-media-website-improvements"></a>Video Analyzer for Media 웹 사이트 개선

#### <a name="adjustments-in-the-video-gallery"></a>비디오 갤러리 조정

추가 필터링 기능을 사용한 심층 인사이트 검색을 위한 새 검색 창이 추가되었으며, 검색 결과도 향상되었습니다.

여러 파일을 사용하여 비디오 아카이브를 정렬하고 관리할 수 있는 새 목록 보기가 제공됩니다.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>간편한 선택 및 구성을 위한 새 패널

간편한 선택 및 사용자 구성 관련 사이드 패널이 추가되어 간단하고 빠른 계정 만들기 및 공유와 구성 설정이 가능합니다.

사이드 패널은 사용자 기본 설정 및 도움말에도 사용됩니다.

## <a name="june-2020"></a>2020년 6월

### <a name="search-by-topics"></a>토픽별 검색

이제 검색 API를 사용하여 특정 토픽으로 비디오를 검색할 수 있습니다(API에만 해당).

토픽은 `textScope`(선택적 매개 변수)의 일부로 추가됩니다. 자세한 내용은 [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos)를 참조하세요.  

### <a name="labels-enhancement"></a>향상된 레이블 기능

레이블 태거가 업그레이드되었으며 이제 식별할 수 있는 시각적 개체 레이블이 추가되었습니다.

## <a name="may-2020"></a>2020년 5월

### <a name="video-analyzer-for-media-deployed-in-the-east-us"></a>Video Analyzer for Media가 미국 동부에 배포됨

이제 미국 동부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다.
 
### <a name="video-analyzer-for-media-url"></a>Video Analyzer for Media URL

Video Analyzer for Media 지역 엔드포인트는 모두 www로만 시작하도록 통합되었습니다. 작업 항목이 필요하지 않습니다.

이제는 위젯을 포함하거나 Video Analyzer for Media 웹 애플리케이션에 로그인할 때 www.videoindexer.ai에 도달합니다.

또한 wus.videoindexer.ai는 www로 리디렉션됩니다. 자세한 내용은 [앱에 Video Analyzer for Media 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요.

## <a name="april-2020"></a>2020년 4월

### <a name="new-widget-parameters-capabilities"></a>새 위젯 매개 변수 기능

**인사이트** 위젯에는 새 매개 변수 `language` 및 `control`이 포함되어 있습니다.

**플레이어** 위젯에는 새 `locale` 매개 변수가 있습니다. `locale` 및 `language` 매개 변수는 플레이어의 언어를 컨트롤합니다.

자세한 내용은 [위젯 형식](video-indexer-embed-widgets.md#widget-types) 섹션을 참조하세요. 

### <a name="new-player-skin"></a>새 플레이어 스킨

업데이트된 디자인으로 출시된 새 플레이어 스킨입니다.

### <a name="prepare-for-upcoming-changes"></a>예정된 변경 관련 준비

* 현재 다음 API는 계정 개체를 반환합니다.

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    계정 개체에는 `Url` [Video Analyzer for Media 웹 사이트](https://www.videoindexer.ai/)의 위치를 가리키는 필드가 있습니다.
유료 계정의 경우 `Url` 필드는 현재 퍼블릭 웹 사이트가 아닌 내부 URL을 가리킵니다.
몇 주 후에는 이를 변경하고 모든 계정(평가판 및 유료)에 관한 [Video Analyzer for Media 웹 사이트](https://www.videoindexer.ai/) URL을 반환할 것입니다.

    내부 URL을 사용하지 마세요. [Video Analyzer for Media 퍼블릭 API](https://api-portal.videoindexer.ai/)를 사용해야 합니다.
* 애플리케이션에 Video Analyzer for Media URL을 포함하는 경우 URL이 [Video Analyzer for Media 웹 사이트](https://www.videoindexer.ai/) 또는 Video Analyzer for Media API 엔드포인트(`https://api.videoindexer.ai`)를 가리키지 않고 지역 엔드포인트(예: `https://wus2.videoindexer.ai`)를 가르키는 경우, URL을 다시 생성합니다.

   다음 중 하나를 수행하여 해당 작업을 수행할 수 있습니다.

    * URL을 Video Analyzer for Media 위젯 API(예: [인사이트 위젯](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))를 가리키는 URL로 바꿉니다.
    * Video Analyzer for Media 웹 사이트를 사용하여 새롭게 포함된 URL 생성:
         
         **재생** 을 눌러 비디오 페이지로 이동하여 **&lt;/&gt; 포함** 단추를 클릭한 후 해당 URL을 사용자 애플리케이션으로 복사합니다.
   
    지역 URL은 지원되지 않으며, 몇 주 후에 차단됩니다.

## <a name="january-2020"></a>2020년 1월
 
### <a name="custom-language-support-for-additional-languages"></a>추가 언어를 위한 사용자 지정 언어 지원

이제 Video Analyzer for Media에서, `ar-SY`, `en-UK`, `en-AU`(API에만 해당)를 위한 사용자 지정 언어 모델을 지원합니다.
 
### <a name="delete-account-timeframe-action-update"></a>계정 시간 프레임 작업 업데이트 삭제

계정 삭제 작업은 이제 48시간이 아니라 90일 이내에 계정을 삭제합니다.
 
### <a name="new-video-analyzer-for-media-github-repository"></a>새로운 Video Analyzer for Media GitHub 리포지토리

이제 여러 프로젝트를 포함하는 새로운 Video Analyzer for Media GitHub, 시작 가이드, 코드 샘플을 사용할 수 있습니다. https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger 업데이트

Video Analyzer for Media에서는 **인증** 및 **작업** 을 단일 [Video Analyzer for Media OpenAPI 사양(swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation)으로 통합했습니다. 개발자는 [Video Analyzer for Media 개발자 포털](https://api-portal.videoindexer.ai/)에서 API를 찾을 수 있습니다.

## <a name="december-2019"></a>2019년 12월

### <a name="update-transcript-with-the-new-api"></a>새 API로 대본 업데이트

[Update-Video-Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API를 사용하여 대본의 특정 섹션을 업데이트합니다.

### <a name="fix-account-configuration-from-the-video-analyzer-for-media-portal"></a>Video Analyzer for Media 포털에서 계정 구성 수정

이제 다음과 같은 문제를 직접 해결하실 수 있도록 Media Services 연결 구성을 업데이트할 수 있습니다. 

* 잘못된 Azure Media Services 리소스
* 암호 변경
* Media Services 리소스가 구독 간에 이동됨  

계정 구성을 수정하려면 Video Analyzer for Media 포털에서 설정 > 계정 탭(소유자로)으로 이동합니다.

### <a name="configure-the-custom-vision-account"></a>사용자 지정 비전 계정 구성

Video Analyzer for Media 포털을 사용하여 유료 계정에서 사용자 지정 비전 계정을 구성합니다(이전에는 API로만 지원). 이렇게 하려면 Video Analyzer for Media 포털에 로그인해서 모델 사용자 지정 > 애니메이션 문자 > 구성을 선택합니다. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>장면, 샷, 키 프레임이 이제 하나의 인사이트 창 안에

이제 장면, 샷, 키 프레임을 더 쉽게 사용하고 탐색할 수 있도록 하나의 인사이트로 병합하였습니다. 원하는 장면을 선택하면 구성된 샷 및 키 프레임을 볼 수 있습니다. 

### <a name="notification-about-a-long-video-name"></a>긴 비디오 이름 관련 알림

비디오 이름이 80자보다 길면 업로드 시 Video Analyzer for Media에 설명 오류가 표시됩니다.

### <a name="streaming-endpoint-is-disabled-notification"></a>스트리밍 엔드포인트는 사용할 수 없는 알림

스트리밍 엔드포인트가 사용하지 않도록 설정된 경우 플레이어 페이지에서 Video Analyzer for Media에 설명 오류가 표시됩니다.

### <a name="error-handling-improvement"></a>오류 처리 개선

상태 코드 409는 비디오를 적극적으로 인덱싱할 때 [비디오 다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) 및 [비디오 인덱스 API 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)에서 반환됩니다. 이는 현재 다시 인덱스 변경 내용을 실수로 재정의하지 않기 위함입니다.

## <a name="november-2019"></a>2019년 11월
 
* 한국어 사용자 지정 언어 모델 지원

    이제 Video Analyzer for Media는 API와 포털 모두에서 한국어(`ko-KR`)로 사용자 지정 언어 모델을 지원합니다. 
* STT(음성 텍스트 변환)에 지원되는 새 언어

    Video Analyzer for Media API는 이제 레반트 아랍어(ar-SY), 영국 영어(en-GB), 호주 영어(en-AU)로 STT를 지원합니다.
    
    비디오 업로드의 경우 zh-HANS를 zh-cn으로 바꿨습니다. 둘 다 지원되지만, 더 정확한 zh-CN를 권장합니다.
    
## <a name="october-2019"></a>2019년 10월
 
* 갤러리에서 애니메이션 문자 검색

    이제 애니메이션 문자를 인덱싱할 때 계정의 비디오 갤러리에서 검색할 수 있습니다. 자세한 내용은 [애니메이션 문자 인식](animated-characters-recognition.md)을 참조하세요.

## <a name="september-2019"></a>2019년 9월
 
IBC 2019에서 여러 가지 기능이 발표되었습니다.
 
* 애니메이션 문자 인식(퍼블릭 미리 보기)

    사용자 지정 비전과의 통합을 통해 애니메이션 콘텐츠에서 그룹 광고 인식 문자를 검색하는 기능입니다. 자세한 내용은 [애니메이션 캐릭터 검색](animated-characters-recognition.md)을 참조하세요.
* 다국어 식별(퍼블릭 미리 보기)

    오디오 트랙에서 여러 언어로 된 세그먼트를 검색하고 이를 기반으로 다국어 음성 텍스트를 만듭니다. 초기 지원 언어: 영어, 스페인어, 독일어, 프랑스어. 자세한 내용은 [자동으로 다국어 콘텐츠 식별 및 전사](multi-language-identification-transcription.md)를 참조하세요.
* 사용자 및 위치에 관한 명명된 엔터티 추출

    NLP(자연어 처리)를 통해 음성 및 시각적 텍스트에서 브랜드, 위치 및 사람을 추출합니다.
* 편집 샷 형식 분류

    클로즈업, 중간 샷, 2인 샷, 실내, 실외 등의 편집 형식으로 샷의 태그를 지정합니다. 자세한 내용은 [편집 샷 형식 검색](scenes-shots-keyframes.md#editorial-shot-type-detection)을 참조하세요.
* 토픽 추론 개선(이제 수준 2 포함)
    
    토픽 추론 모델은 이제 더욱 심도 깊은 IPTC 분류 세분성을 지원합니다. [Azure Media Services 새 AI 기반 혁신](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)에 관한 자세한 내용을 확인하세요.

## <a name="august-2019"></a>2019년 8월
 
### <a name="video-analyzer-for-media-deployed-in-uk-south"></a>Video Analyzer for Media가 영국 남부에 배포됨

이제 영국 남부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다.

### <a name="new-editorial-shot-type-insights-available"></a>새 편집 샷 형식 인사이트 사용 가능

비디오 샷에 추가된 새 태그는 익스트림 클로즈업, 클로즈업, 와이드, 중간, 2인 샷, 실외, 실내, 왼쪽 얼굴, 오른쪽 얼굴(JSON에서 사용 가능)과 같은 콘텐츠 생성 워크플로에 사용되는 일반 편집 문구를 사용하여 해당 항목을 식별하는 편집 "샷 형식"을 제공합니다.

### <a name="new-people-and-locations-entities-extraction-available"></a>새 사용자 및 위치 엔터티 추출 사용 가능

Video Analyzer for Media는 비디오의 OCR 및 대화 내용 기록에서 NLP(자연어 처리)를 통해 명명된 위치 및 사용자를 식별합니다. Video Analyzer for Media는 기계 학습 알고리즘을 사용하여 특정 위치(예: 에펠탑) 또는 사람(예: John Doe)을 비디오에서 호출하는 경우를 인식합니다.

### <a name="keyframes-extraction-in-native-resolution"></a>기본 해상도에서 키 프레임 추출

Video Analyzer for Media에서 추출된 키 프레임은 비디오의 원래 해상도에서 사용할 수 있습니다.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>이미지에서 사용자 지정 얼굴 모델을 학습하기 위한 GA

미리 보기 모드에서 GA(API 및 포털을 통해 사용 가능)로 이동된 이미지에서 얼굴 학습.

> [!NOTE]
> "미리 보기에서 GA로" 전환되었지만, 가격 책정에는 영향이 없습니다.

### <a name="hide-gallery-toggle-option"></a>갤러리 토글 옵션 숨기기

사용자는 포털에서 갤러리 탭을 숨기도록 선택할 수 있습니다(샘플 탭 숨기기와 유사).
 
### <a name="maximum-url-size-increased"></a>최대 URL 크기 증가

비디오를 인덱싱할 때 URL 쿼리 문자열 4096을(2048 대신) 지원합니다.
 
### <a name="support-for-multi-lingual-projects"></a>다국어 프로젝트 지원

이제 다른 언어로 인덱싱되는 비디오를 기반으로 프로젝트를 만들 수 있습니다(API만 해당).

## <a name="july-2019"></a>2019년 7월

### <a name="editor-as-a-widget"></a>위젯으로서의 편집기

이제는 Video Analyzer for Media AI 편집기를 고객 애플리케이션에 포함할 수 있는 위젯으로 사용할 수 있습니다.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>포털에서 선택 자막 파일의 사용자 지정 언어 모델 업데이트

고객은 포털의 사용자 지정 페이지에서 VTT, SRT, TTML 파일 형식을 언어 모델을 위한 입력으로 제공할 수 있습니다.

## <a name="june-2019"></a>2019년 6월

### <a name="video-analyzer-for-media-deployed-to-japan-east"></a>Video Analyzer for Media가 일본 동부에 배포됨

이제 일본 동부 지역에서 Video Analyzer for Media 유료 계정을 만들 수 있습니다.

### <a name="create-and-repair-account-api-preview"></a>계정 API 만들기 및 복구(미리 보기)

[Azure Media Service 연결 엔드포인트 또는 키를 업데이트할](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services) 수 있는 새 API가 추가되었습니다.

### <a name="improve-error-handling-on-upload"></a>업로드 시 오류 처리 개선 

설명 메시지는 기본 Azure Media Services 계정을 잘못 구성할 반환됩니다.

### <a name="player-timeline-keyframes-preview"></a>플레이어 타임라인 키 프레임 미리 보기 

이제 플레이어의 타임라인에서 각 시간의 이미지 미리 보기를 볼 수 있습니다.

### <a name="editor-semi-select"></a>편집기 부분 선택

이제 편집기에서 특정 인사이트 시간 범위를 선택한 결과로 선택된 모든 인사이트의 미리 보기를 볼 수 있습니다.

## <a name="may-2019"></a>2019년 5월

### <a name="update-custom-language-model-from-closed-caption-file"></a>선택 자막 파일에서 사용자 지정 언어 모델 업데이트

[사용자 지정 언어 모델 만들기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) 및 [사용자 지정 언어 모델 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API는 이제 VTT, SRT, TTML 파일 형식을 언어 모델에 관한 입력으로 지원합니다.

[비디오 음성 텍스트 업데이트 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)를 호출하면 자동으로 음성 텍스트가 추가됩니다. 비디오와 연결된 학습 모델도 자동으로 업데이트됩니다. 언어 모델을 사용자 지정하고 학습하는 방법에 관한 자세한 내용은 [Video Analyzer for Media로 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

### <a name="new-download-transcript-formats--txt-and-csv"></a>새 다운로드 음성 텍스트 형식 – TXT 및 CSV

이미 지원되는 선택 자막 형식(SRT, VTT, TTML) 외에도 Video Analyzer for Media는 이제 TXT 및 CSV 형식으로 음성 텍스트 다운로드를 지원합니다.

## <a name="next-steps"></a>다음 단계

[개요](video-indexer-overview.md)
