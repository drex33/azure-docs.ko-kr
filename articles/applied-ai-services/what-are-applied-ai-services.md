---
title: Azure Applied AI Services란 무엇인가요?
titleSuffix: Azure Applied AI Services
description: Applied AI Services에 대한 설명입니다.
keywords: applied ai services, AI, applied ai, ai services, cognitive search, applied ai 기능
author: jeffme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: ''
ms.topic: overview
ms.date: 05/04/2021
ms.author: jeffme
ms.openlocfilehash: 102e8717b1bb6072eb0a52efc6ac3ecdd3e9c165
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837350"
---
# <a name="what-are-azure-applied-ai-services"></a>Azure Applied AI Services란 무엇인가요?

Azure Applied AI Services는 개발자가 주요 비즈니스 시나리오에 AI를 적용하여 데이터의 가치를 신속하게 실현할 수 있게 해주는 높은 수준의 서비스입니다.  Azure Cognitive Services의 AI API를 기반으로 구축된 Azure Applied AI Services는 메트릭 변칙 모니터링 및 진단, 문서의 지식 마이닝, 음성 텍스트 분석을 통한 고객 환경 개선, 교실의 읽기와 쓰기 보강, 문서 이해 등에 이르는 중요한 작업에 맞게 최적화되었습니다. 이전에는 회사에서 여러 AI 기술을 오케스트레이션하고, 비즈니스 논리를 추가하고, 시나리오에 맞게 개발에서 배포로 이동하는 UI를 만들어야 했기 때문에 많은 시간과 리소스가 소모되었고 전문성이 요구되었습니다. 이러한 "시나리오 특정" 서비스는 개발자에게 다음과 같은 이점을 "즉시" 제공합니다.

## <a name="azure-form-recognizer"></a>Azure Form Recognizer

모든 업계의 조직에서 문서 내에 숨겨진 정보를 사용하여 생산성을 높이고, 비즈니스 프로세스를 자동화하고, 지식과 인사이트를 생성할 수 있습니다.  Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 서비스입니다. 문서에서 텍스트, 키/값 쌍, 선택 표시, 테이블 및 구조체를 식별하고 추출합니다. 이 서비스는 원본 파일의 관계, 경계 상자, 신뢰도 등이 포함된 정형 데이터를 출력합니다. 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 정확한 결과를 빠르게 얻을 수 있습니다. Form Recognizer를 사용하여 애플리케이션에서 데이터 입력을 자동화하고 문서 검색 기능을 보강합니다.  Azure Form Recognizer는 Azure Cognitive Services의 OCR, Text Analytics 및 사용자 지정 텍스트를 사용하여 빌드됩니다.

Form Recognizer는 사용자 지정 문서 처리 모델, 송장, 영수증, ID 및 명함용으로 미리 빌드된 모델, 레이아웃 모델로 구성됩니다. 

[Azure Form Recognizer에 대한 자세한 정보](./form-recognizer/index.yml)

## <a name="azure-metrics-advisor"></a>Azure Metrics Advisor

비즈니스, 서비스 및 물리적 자산의 메트릭을 통한 인텔리전스에 따라 올바른 의사 결정을 내릴 수 있도록 하여 조직의 성장을 보호합니다.  Azure Metrics Advisor는 AI를 사용하여 시계열 데이터에서 데이터 모니터링과 변칙 검색을 수행합니다. 이 서비스는 데이터에 모델을 적용하는 프로세스를 자동화하고 데이터 수집, 변칙 검색 및 진단용 API 및 웹 기반 작업 영역 세트를 제공하므로 기계 학습을 몰라도 상관없습니다. 개발자는 이 서비스를 기반으로 AIOps, 예측 유지 관리 및 비즈니스 모니터링 애플리케이션을 빌드할 수 있습니다.  Azure Metrics Advisor는 Azure Cognitive Services의 Anomaly Detector를 사용하여 빌드됩니다.

[Azure Metrics Advisor에 대한 자세한 정보](./metrics-advisor/index.yml)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

작업을 수행하거나 결정을 내릴 수 있도록 모든 콘텐츠에 숨어 있는 중요한 정보를 발굴합니다.  Azure Cognitive Search는 모든 유형의 정보를 보강하는 AI 기능이 기본 제공되는 유일한 클라우드 검색 서비스로, 관련 콘텐츠를 대규모로 식별하고 탐색하는 데 도움이 됩니다. 시각, 언어 및 음성 인식 기술을 사용하거나 사용자 지정 기계 학습 모델을 사용하여 모든 유형의 콘텐츠에서 인사이트를 발굴합니다. 또한 Azure Cognitive Search는 고급 기계 학습 기법을 사용하여 사용자 의도를 이해하고 상황에 따라 가장 관련성이 높은 검색 결과의 순위를 지정하는 의미 체계 검색 기능도 제공합니다. 혁신하는 데 더 많은 시간을 투자하는 대신, 복잡한 클라우드 검색 솔루션을 유지 관리하는 데는 더 적은 시간을 투자합니다.  Azure Cognitive Search는 Azure Cognitive Services의 Computer Vision 및 Text Analytics를 사용하여 빌드됩니다.

[Azure Cognitive Services에 대한 자세한 정보](../search/index.yml)

## <a name="azure-immersive-reader"></a>Azure 몰입형 리더

AI를 사용하여 독해력과 성취도를 높이세요. Azure 몰입형 리더는 새 리더, 언어 학습자 및 학습 차이(예: 난독증)가 있는 사람들을 위해 독해력을 향상시키기 위해 입증된 기술을 구현하는 포괄적으로 설계된 도구입니다. 몰입형 리더 클라이언트 라이브러리를 사용하면 Microsoft Word 및 Microsoft OneNote에 사용된 것과 동일한 기술을 활용하여 웹 애플리케이션을 개선할 수 있습니다. Azure 몰입형 리더는 Azure Cognitive Services의 Translation 및 Text to Speech를 사용하여 빌드됩니다.

[Azure 몰입형 판독기에 대한 자세한 정보](./immersive-reader/index.yml)

## <a name="azure-bot-service"></a>Azure Bot Service

비즈니스 가치를 즉시 실현할 수 있도록 미리 빌드된 대화형 구성 요소를 사용하여 사용자 지정 가능하고 정교한 대화형 환경을 신속하게 만들 수 있습니다.  Azure Bot Service Composer는 개발자와 다양한 전문 분야 기술 팀이 봇을 빌드하는 데 사용되는 오픈 소스 시각적 제작 캔버스입니다. Composer는 LUIS 및 QnA Maker와 같은 언어 이해 서비스를 통합하고 언어 생성을 사용하여 봇 응답을 정교하게 구성하는 데 사용할 수 있습니다. Azure Bot Service는 Azure Cognitive Services의 음성/전화 통신, LUIS 및 QnA Maker를 사용하여 빌드됩니다.

[Azure Bot Service에 대한 자세한 정보](/composer/)

## <a name="azure-video-analyzer"></a>Azure Video Analyzer 

기업이 비디오 또는 AI 전문가 없이도 비디오 인텔리전스 기반의 자동화된 앱을 빌드할 수 있게 해줍니다.  Azure Video Analyzer는 AI 기반 비디오 솔루션 및 애플리케이션을 빌드하는 데 사용되는 서비스입니다. 비디오 스트림에서 실시간 비즈니스 인사이트를 생성하고, 원본 근처에서 데이터를 처리하고, 선택한 AI를 적용할 수 있습니다. 에지 또는 클라우드에서 관심 있는 비디오를 녹화하고 다른 데이터와 결합하여 비즈니스 의사 결정을 내릴 수 있습니다.  Azure Video Analyzer는 Azure Cognitive Services의 공간 분석을 사용하여 빌드됩니다.  Azure Video Analyzer for Media는 Azure Cognitive Services의 Face, Speech, Translation, Text Analytics, Custom Vision 및 텍스트 콘텐츠 조정을 사용하여 빌드됩니다.  

[Azure Video Analytics에 대한 자세한 정보](../azure-video-analyzer/index.yml)

## <a name="certifications-and-compliance"></a>인증 및 규정 준수

Applied AI Services는 CSA STAR 인증, FedRAMP Moderate 및 HIPAA BAA와 같은 인증을 받았습니다. 해당하는 감사 및 보안 검토에 대한 인증을 [다운로드](/samples/browse/?redirectedfrom=TechNet-Gallery "다운로드로 사용 가능한 제품 설명서에서 데이터 공급자 설치 섹션을 참조하세요")할 수 있습니다.

개인 정보 및 데이터 관리를 이해하려면 [보안 센터](https://servicetrust.microsoft.com/ "보안 센터")로 이동하세요.

## <a name="support"></a>지원

Applied AI Services는 지능형 애플리케이션 생성을 진행하는 데 도움이 되는 몇 가지 지원 옵션을 제공합니다. Applied AI Services에는 또한 특정 질문에 답변할 수 있는 강력한 개발자 커뮤니티가 있습니다. 사용 가능한 옵션의 전체 목록은 다음을 참조하세요.

- [UserVoice에 피드백 제출](https://aka.ms/AppliedAIUserVoice)
- [Microsoft Q&A에서 질문하기](/answers/topics/azure-applied-ai-services.html)
- [StackOverflow 문제 해결](https://aka.ms/AppliedAIStackOverflow)