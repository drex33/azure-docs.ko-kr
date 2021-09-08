---
title: 인덱싱 중 기본 제공 텍스트 및 이미지 처리
titleSuffix: Azure Cognitive Search
description: 데이터 추출, 자연어 및 이미지 처리 기술은 Azure Cognitive Search 보강 파이프라인의 원시 콘텐츠에 의미 체계와 구조를 추가합니다.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 161813b7da42a5697fdb595e6a7050923e28fca2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038895"
---
# <a name="built-in-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>인덱싱 중 텍스트 및 이미지 처리를 위한 기본 제공 기술(Azure Cognitive Search)

이 문서에서는 구조화되지 않은 원시 텍스트 및 이미지 파일에서 콘텐츠 및 구조를 추출하도록 [기술 세트](cognitive-search-working-with-skillsets.md)에 포함할 수 있는 Azure Cognitive Search와 함께 제공되는 기술을 설명합니다. *기술* 은 어떤 방식으로든 콘텐츠를 변환하는 원자성 작업입니다. 텍스트를 인식하거나 추출하는 작업인 경우가 많지만 이미 만들어진 보강을 변형하는 유틸리티 기술일 수도 있습니다. 일반적으로 출력은 전체 텍스트 쿼리에서 사용할 수 있도록 텍스트 기반입니다. 

## <a name="built-in-skills"></a>기본 제공 기술

기본 제공 기술은 Microsoft의 미리 학습된 모델을 기반으로 하므로 고유한 학습 데이터를 사용하여 모델을 학습할 수 없습니다. Cognitive Resources API를 호출하는 기술은 해당 서비스에 대한 종속성을 가지며 [리소스를 연결](cognitive-search-attach-cognitive-services.md)할 때 Cognitive Services 종량제 가격으로 청구됩니다. 다른 기술은 Azure Cognitive Search를 통해 계량되거나 무료로 사용할 수 있는 유틸리티 기술입니다.

다음 표에서는 기본 제공 기술을 열거하고 설명합니다.

| OData 형식  | Description | 요금제 기준 |
|-------|-------------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md) | 사용자 지정 사용자 정의 목록에서 텍스트를 찾습니다.| Azure Cognitive Search([가격 책정](https://azure.microsoft.com/pricing/details/search/)) |
| [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | 이 기술은 미리 학습된 모델을 통해 용어 배치, 언어 규칙, 다른 용어에 대한 근접성 및 원본 데이터 내에서 용어가 비정상적인 정도에 따라 중요한 문구를 검색합니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 이 기술은 미리 학습된 모델을 통해 사용된 언어를 검색합니다(문서당 하나의 언어 ID). 동일한 텍스트 세그먼트 내에서 여러 언어가 사용되는 경우 주로 사용된 언어의 LCID가 출력됩니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 필드 컬렉션에서 단일 필드로 텍스트를 통합합니다.  | 해당 없음 |
| [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) | 이 기술은 미리 학습된 모델을 사용하여 Wikipedia의 문서에 대해 인식된 엔터티에 대한 링크를 생성합니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) | 이 기술은 미리 학습된 모델을 사용해 고정된 범주 집합(`"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"DateTime"`, `"URL"`, `"Email"`, `"PersonType"`, `"Event"`, `"Product"`, `"Skill"`, `"Address"`, `"Phone Number"` 및 `"IP Address"` 필드)에 대한 엔터티를 설정합니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | 이 기술은 미리 학습된 모델을 사용하여 지정된 텍스트에서 개인 정보를 추출합니다. 또한 이 기술은 검색된 개인 정보 엔터티를 텍스트에서 마스킹하기 위한 다양한 옵션을 제공합니다.  | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md)  | 이 기술은 미리 학습된 모델을 사용하여 레코드별 기준으로 문장 및 문서 수준에서 서비스를 통해 찾은 가장 높은 신뢰도 점수에 따라 감정 레이블(예: "부정", "중립" 및 "긍정")을 할당합니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 콘텐츠를 증분 방식으로 보강하거나 보충할 수 있도록 텍스트를 페이지로 분할합니다. | 해당 없음 |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | 이 기술은 미리 학습된 모델을 사용하여 정규화 또는 지역화 사용 사례를 위해 입력 텍스트를 다양한 언어로 번역합니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 이 기술은 이미지 검색 알고리즘을 사용하여 이미지의 콘텐츠를 식별하고 텍스트 설명을 생성합니다. | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) | 
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 광학 문자 인식 | Cognitive Services([가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)) |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 조건을 기준으로 필터링, 기본값 할당 및 데이터 병합을 허용합니다. | 해당 없음 |
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | 보강 파이프라인 내 파일에서 콘텐츠를 추출합니다. | Azure Cognitive Search([가격 책정](https://azure.microsoft.com/pricing/details/search/))
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 출력을 복합 형식(전체 이름, 여러 줄로 이루어진 주소 또는 성과 개인 식별자의 조합에 사용될 수 있는 다중 파트 데이터 형식)에 매핑합니다. | 해당 없음 |

## <a name="custom-skills"></a>사용자 지정 기술

[사용자 지정 기술](cognitive-search-custom-skill-web-api.md)은 웹을 디자인, 개발 및 배포하는 모듈입니다. 그런 다음, 기술 세트 내에서 모듈을 사용자 지정 기술로 호출할 수 있습니다.

| 형식  | Description | 요금제 기준 |
|-------|-------------|-------------|
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 사용자 지정 웹 API에 대해 HTTP 호출을 수행하여 AI 보강 파이프라인의 확장성 허용 | 솔루션에서 요금제 Azure 서비스를 사용하지 않는 한 없음 |
| [Microsoft.Skills.Custom.AmlSkill](cognitive-search-aml-skill.md) | Azure Machine Learning 모델을 사용하여 AI 보강 파이프라인의 확장성 허용 | 솔루션에서 요금제 Azure 서비스를 사용하지 않는 한 없음 |

사용자 지정 기술 생성에 대한 지침은 [사용자 지정 인터페이스 정의](cognitive-search-custom-skill-interface.md) 및 [예제: AI 보강의 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [사용자 지정 기술 인터페이스 정의](cognitive-search-custom-skill-interface.md)
+ [자습서: AI를 사용하는 보강 인덱싱](cognitive-search-tutorial-blob.md)
