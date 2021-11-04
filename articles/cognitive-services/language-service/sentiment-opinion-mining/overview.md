---
title: Azure Cognitive Service for Language의 감정 분석 및 오피니언 마이닝이란 무엇인가요?
titleSuffix: Azure Cognitive Services
description: 단서를 찾기 위해 텍스트를 마이닝하여 사람들이 항목에 대해 어떻게 생각하는지 알아내는 데 도움이 되는 Azure Cognitive Services의 감정 분석 기능에 대한 개요입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 8807b12f9920fd62901aebed862ecba483e77bb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101218"
---
# <a name="what-is-sentiment-analysis-and-opinion-mining-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language의 감정 분석 및 오피니언 마이닝이란 무엇인가요?

감정 분석 및 오피니언 마이닝은 [Azure Cognitive Service for Language](../overview.md)에서 제공하는 기능으로, 작성된 언어와 관련된 지능형 애플리케이션을 개발하기 위한 클라우드의 기계 학습 및 AI 알고리즘 모음입니다. 이러한 기능은 텍스트를 마이닝하여 긍정적이거나 부정적인 감정에 대한 단서를 찾아 사람들이 브랜드 또는 항목에 대해 어떻게 생각하는지 알아내는 데 도움이 되며 텍스트의 특정 측면과 연관시킬 수 있습니다. 

* [**빠른 시작**](quickstart.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [**방법 가이드**](how-to/call-api.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.

## <a name="sentiment-analysis"></a>정서 분석 

이 감정 분석 기능은 문장 및 문서 수준에서 서비스가 찾은 가장 높은 신뢰도 점수에 따라 감정 레이블(예: "부정", "중립" 및 "긍정")을 제공합니다. 또한 이 기능은 긍정, 중립, 부정 감정에 대한 각 문서 및 그 안의 문장에 대해 0과 1 사이의 신뢰도 점수를 반환합니다. 

### <a name="deploy-on-premises-using-docker-containers"></a>Docker 컨테이너를 사용하여 온-프레미스 배포

사용 가능한 Docker 컨테이너를 사용하여 [감정 분석을 온-프레미스에 배포](how-to/use-containers.md)합니다. 이러한 Docker 컨테이너는 규정 준수, 보안 또는 기타 운영상의 이유로 서비스를 데이터에 더 가깝게 가져올 수 있습니다.

## <a name="opinion-mining"></a>의견 마이닝

오피니언 마이닝은 감정 분석의 기능입니다. NLP(자연어 처리)의 양상 기반 감정 분석이라고도 하는 이 기능은 텍스트의 단어와 관련된 의견에 대한 더 세부적인 정보(예: 제품 또는 서비스의 특성)를 제공합니다.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="responsible-ai"></a>Responsible AI 

AI 시스템에는 기술뿐만 아니라 기술을 사용하는 사람, 영향을 받는 사람, 배포되는 환경이 포함됩니다. [감정 분석을 위한 투명도 참고](/legal/cognitive-services/language-service/transparency-note-sentiment-analysis?context=/azure/cognitive-services/language-service/context/context)를 읽고 시스템에서 책임감 있는 AI 사용 및 배포에 대해 알아봅니다. 자세한 내용은 다음 문서를 참조하세요.

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>다음 단계

엔터티 연결 기능을 사용하여 시작하는 방법에는 두 가지가 있습니다.
* [Language Studio](../language-studio.md): 코드를 작성할 필요 없이 여러 Azure Cognitive Service for Language 기능을 사용해 볼 수 있는 웹 기반 플랫폼입니다.
* REST API 및 클라이언트 라이브러리 SDK를 사용하여 서비스에 요청하는 방법에 대한 지침은 [빠른 시작 문서](quickstart.md)를 참조하세요.  
