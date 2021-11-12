---
title: Azure Content Moderator란?
titleSuffix: Azure Cognitive Services
description: Content Moderator를 사용하여 사용자 생성 콘텐츠에서 부적절한 자료를 추적하고, 플래깅하고, 평가하고, 필터링하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 09/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 콘텐츠 중재자, azure 콘텐츠 중재자, 온라인 중재자, 콘텐츠 필터링 소프트웨어, 콘텐츠 조정 서비스, 콘텐츠 조정
ms.openlocfilehash: 0696e87e4e395896b0b0f57c2c5912264e19cd83
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373349"
---
# <a name="what-is-azure-content-moderator"></a>Azure Content Moderator란?

[!INCLUDE [deprecation notice](includes/tool-deprecation.md)]

Azure Content Moderator는 공격을 받을 수 있거나 위험한 또는 바람직하지 않은 콘텐츠를 처리할 수 있는 AI 서비스입니다. 여기에는 텍스트, 이미지 및 비디오를 검색하고 콘텐츠 플래그를 자동으로 적용하는 AI 기반 콘텐츠 조정 서비스와 사용자 검토자 팀을 위한 온라인 중재자 환경인 검토 도구가 포함됩니다.

앱에 콘텐츠 필터링 소프트웨어를 빌드하여 규정을 준수하거나 사용자에게 의도된 환경을 유지 관리할 수 있습니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.  

* [**빠른 시작**](client-libraries.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.  
* [**방법 가이드**](try-text-api.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.  
* [**개념**](text-moderation-api.md)은 서비스 기능에 대한 심층적인 설명을 제공합니다.  
* [**자습서**](ecommerce-retail-catalog-moderation.md)는 보다 광범위한 비즈니스 솔루션에서 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.  

## <a name="where-its-used"></a>사용 위치

다음은 소프트웨어 개발자나 팀이 콘텐츠 조정 서비스를 요구하는 몇 가지 시나리오입니다.

- 제품 카탈로그 및 기타 사용자 생성 콘텐츠를 조정하는 온라인 마켓플레이스.
- 사용자 생성 게임 아티팩트 및 대화방을 조정하는 게임 회사.
- 사용자가 추가한 이미지, 텍스트 및 비디오를 조정하는 소셜 메시징 플랫폼.
- 중앙에서 콘텐츠를 조정하도록 구현하는 엔터프라이즈 미디어 회사.
- 학생 및 교육자에게 부적절한 콘텐츠를 필터링하는 K-12 교육 솔루션 공급자.

> [!IMPORTANT]
> Content Moderator를 사용하여 불법 아동 착취 이미지를 검색할 수 없습니다. 그러나 자격이 있는 조직은 [PhotoDNA 클라우드 서비스](https://www.microsoft.com/photodna "Microsoft PhotoDNA 클라우드 서비스")를 사용하여 이러한 종류의 콘텐츠를 차단할 수 있습니다.

## <a name="what-it-includes"></a>포함되는 항목

Content Moderator 서비스는 REST 호출 및 .NET SDK 둘 다를 통해 사용 가능한 몇 가지 웹 서비스 API로 구성됩니다. 또한 검토자가 역할을 하는 사람이 서비스를 보조하고 조정 기능을 개선하거나 세부 조정하는 데 사용되는 검토 도구도 포함되어 있습니다.

## <a name="moderation-apis"></a>중재 API

Content Moderator 서비스에는 콘텐츠에 잠재적으로 부적절하거나 불쾌한 자료가 있는지 확인하는 중재 API가 포함되어 있습니다.

![Content Moderator 중재 API의 블록 다이어그램](images/content-moderator-mod-api.png)

다음 표에서는 다양한 유형의 중재 API를 설명합니다.

| API 그룹 | Description |
| ------ | ----------- |
|[**텍스트 조정**](text-moderation-api.md)| 텍스트에서 불쾌감을 주는 콘텐츠, 외설적이거나 선정적인 콘텐츠, 불경한 언어 및 개인 데이터를 검사합니다.|
|[**사용자 지정 용어 목록**](try-terms-list-api.md)| 기본 제공 용어와 함께 사용자 지정 용어 목록에 대해 텍스트를 검사합니다. 사용자 지정 콘텐츠 정책에 따라 사용자 지정 목록을 사용하여 콘텐츠를 차단하거나 허용합니다.|  
|[**이미지 조정**](image-moderation-api.md)| 이미지에서 성인 또는 외설 콘텐츠를 검사하고, OCR(광학 문자 인식) 기능을 사용하여 이미지에서 텍스트를 감지하고, 얼굴을 감지합니다.|
|[**사용자 지정 이미지 목록**](try-image-list-api.md)| 사용자 지정 이미지 목록을 기준으로 이미지를 검사합니다. 사용자 지정 이미지 목록을 사용하여 다시 분류하지 않으려는 일반적인 반복 콘텐츠의 인스턴스를 필터링합니다.|
|[**비디오 조정**](video-moderation-api.md)| 비디오에서 성인 또는 외설 콘텐츠를 검사하고 언급된 콘텐츠의 시간 마커를 반환합니다.|

## <a name="review-apis"></a>API 검토

검토 API를 사용하여 중재 파이프라인과 사용자 검토자를 통합할 수 있습니다. [작업](review-api.md#jobs), [검토](review-api.md#reviews) 및 [워크플로](review-api.md#workflows) 작업을 사용하여 [검토 도구](#review-tool)(아래)로 인간 참여형 워크플로를 만들고 자동화합니다.

> [!NOTE]
> 워크플로 API는 .NET SDK에서 아직 사용할 수 없지만 REST 엔드포인트와 함께 사용할 수는 있습니다.

![Content Moderator 검토 API의 블록 다이어그램](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>검토 도구

Content Moderator 서비스에는 웹 기반 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md)도 포함되어 있습니다. 이 도구는 사용자 중재자가 처리할 콘텐츠 검토를 호스팅합니다. 사용자 입력은 서비스를 학습시키지 않지만 서비스 및 사용자 검토 팀의 협업을 통해 개발자는 효율성과 정확성 사이에 적절히 균형을 이룰 수 있습니다. 검토 도구는 여러 Content Moderator 리소스에 대한 사용자 친화적인 프런트 엔드도 제공합니다.

![Content Moderator 검토 도구 홈페이지](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Content Moderator 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

웹 포털에서 Content Moderator 사용을 시작하려면 [웹에서 Content Moderator 사용해 보기](quick-start.md)를 수행합니다. 또는 [클라이언트 라이브러리 또는 REST API 빠른 시작](client-libraries.md)을 완료하여 코드에서 기본 시나리오를 구현합니다.