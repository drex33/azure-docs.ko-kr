---
title: Text Analytics API를 사용한 텍스트 마이닝 및 분석 - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Text Analytics API를 사용한 텍스트 마이닝에 대해 알아봅니다. 감정 분석, 언어 감지 및 기타 형태의 자연어 처리에 사용합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/14/2021
ms.author: aahi
keywords: 텍스트 마이닝, 감정 분석, 텍스트 분석
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 804af634c7202fdc3f822e32e7cd1fbf827bfab1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733968"
---
# <a name="what-is-the-text-analytics-api"></a>텍스트 분석 API란?

Text Analytics API는 감성 분석, 오피니언 마이닝, 핵심 구 추출, 언어 감지 및 명명된 엔터티 인식을 포함하는 텍스트 마이닝 및 텍스트 분석을 위한 NLP(자연어 처리) 기능을 제공하는 클라우드 기반 서비스입니다.

이 API는 개발 프로젝트를 위한 클라우드의 기계 학습 및 AI 알고리즘 모음인 [Azure Cognitive Services](../index.yml)의 일부입니다. REST API [버전 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/), [버전 3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1) 또는 [클라이언트 라이브러리](quickstarts/client-libraries-rest-api.md)에서 이러한 기능을 사용할 수 있습니다.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./quickstarts/client-libraries-rest-api.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./how-tos/text-analytics-how-to-call-api.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념](text-analytics-user-scenarios.md)은 서비스 기능에 대한 심층적인 설명을 제공합니다.
* [자습서](./tutorials/tutorial-power-bi-key-phrases.md)는 보다 광범위한 비즈니스 솔루션에서 이 서비스를 구성 요소로 사용하는 방법을 보여 주는 긴 가이드입니다.

## <a name="sentiment-analysis"></a>정서 분석

[감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)(SA)을 사용하면 텍스트에서 긍정적 또는 부정적 감정에 대한 단서를 찾아서 사람들이 브랜드 또는 주제에 대해 어떻게 생각하는지 알아볼 수 있습니다. 

이 기능은 문장 및 문서 수준에서 서비스가 찾은 가장 높은 신뢰도 점수에 따라 감정 레이블(예: "부정", "중립" 및 "긍정")을 제공합니다. 또한 이 기능은 긍정, 중립, 부정 감정에 대한 각 문서 및 그 안의 문장에 대해 0과 1 사이의 신뢰도 점수를 반환합니다. [컨테이너를 사용](how-tos/text-analytics-how-to-install-containers.md)하여 온-프레미스에서 서비스를 실행할 수도 있습니다.

v3.1부터 오피니언 마이닝(OM)은 감정 분석의 기능입니다. NLP(자연어 처리)의 양상 기반 감정 분석이라고도 하는 이 기능은 텍스트의 단어와 관련된 의견에 대한 더 세부적인 정보(예: 제품 또는 서비스의 특성)를 제공합니다.

## <a name="key-phrase-extraction"></a>핵심 문구 추출

[핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)(KPE)을 사용하여 텍스트의 주요 개념을 빠르게 식별합니다. 예를 들어, "음식이 맛있었고 훌륭한 직원이 있었습니다"라는 텍스트에서 핵심 구 추출은 "음식"과 "훌륭한 직원"이라는 주요 논점을 반환합니다.

## <a name="language-detection"></a>언어 검색

언어 감지는 [입력 텍스트가 작성된 언어를 감지](how-tos/text-analytics-how-to-language-detection.md)하고 광범위한 언어, 변형, 방언 및 일부 지역/문화 언어로 요청 시 제출된 모든 문서에 대해 단일 언어 코드를 보고할 수 있습니다. 언어 코드는 신뢰도 점수와 쌍을 이룹니다.

## <a name="named-entity-recognition"></a>명명된 엔터티 인식

NER(명명된 엔터티 인식)은 사람, 장소, 조직, 수량으로 텍스트의 [엔터티를 식별하고 분류](how-tos/text-analytics-how-to-entity-linking.md)할 수 있습니다. 잘 알려진 엔터티도 인식되고 웹에서 더 많은 정보에 연결됩니다.

## <a name="text-summarization"></a>텍스트 요약

[요약](how-tos/extractive-summarization.md)은 원본 콘텐츠 내에서 가장 중요하거나 관련성 있는 정보를 집합적으로 나타내는 문장을 추출하여 텍스트 요약을 생성합니다. 이 기능은 기사, 보고서 또는 문서를 핵심 문장으로 압축합니다.

## <a name="text-analytics-for-health"></a>의료 분야 Text Analytics

의료 분야 Text Analytics는 의사의 메모, 퇴원 요약, 임상 문서 및 전자 의료 레코드 같은 구조화되지 않은 텍스트에서 관련 의료 정보를 추출하고 레이블을 지정하는 Text Analytics API 서비스 기능입니다. 

## <a name="deploy-on-premises-using-docker-containers"></a>Docker 컨테이너를 사용하여 온-프레미스 배포

[Text Analytics 컨테이너를 사용](how-tos/text-analytics-how-to-install-containers.md)하여 온-프레미스에 API 기능을 배포합니다. 이러한 Docker 컨테이너는 규정 준수, 보안 또는 기타 운영상의 이유로 서비스를 데이터에 더 가깝게 가져올 수 있습니다. Text Analytics는 다음과 같은 컨테이너를 제공합니다.

* 감정 분석
* 핵심 구 추출(미리 보기)
* 언어 감지(미리 보기)
* 의료 분야 Text Analytics

## <a name="asynchronous-operations"></a>비동기 작업

`/analyze` 엔드포인트를 사용하면 Text Analytics API의 다양한 기능을 [비동기적](how-tos/text-analytics-how-to-call-api.md)으로 사용할 수 있습니다. NER(명명된 엔터티 인식), KPE(핵심 문구 추출), SA(감정 분석), OM(오피니언 마이닝)은 `/analyze` 엔드포인트의 일부로 사용할 수 있습니다. 단일 호출에서 이러한 기능의 clubbing이 허용됩니다. 문서당 최대 125,000자를 보낼 수 있습니다. 가격은 일반 Text Analytics와 동일합니다.

## <a name="typical-workflow"></a>일반적인 워크플로

간단한 워크플로: 분석을 위해 데이터를 제출하고 코드의 출력을 처리합니다. 분석기는 추가 구성 또는 사용자 지정 없이 있는 그대로 사용됩니다.

1. Text Analytics용 [Azure 리소스를 만듭니다](how-tos/text-analytics-how-to-call-api.md). 그런 다음, 요청을 인증할 수 있도록 생성된 [키를 가져옵니다](how-tos/text-analytics-how-to-call-api.md).

2. 데이터를 구조화되지 않은 원시 텍스트로 포함하는 [요청을 JSON으로 구성](how-tos/text-analytics-how-to-call-api.md#json-schema)합니다.

3. 등록 동안 설정된 엔드포인트에 요청을 게시하고, 감정 분석, 핵심 구 추출, 언어 감지 또는 명명된 엔터티 인식 등, 원하는 리소스를 추가합니다.

4. 응답을 스트리밍하거나 로컬로 저장합니다. 요청에 따라, 결과는 감정 점수, 추출된 핵심 구 모음 또는 언어 코드가 됩니다.

출력은 ID를 기준으로 게시한 각 텍스트 문서에 대한 결과를 포함하는 단일 JSON 문서로 반환됩니다. 추후에 결과를 실행 가능한 정보로 분석하거나, 시각화하거나, 분류할 수 있습니다.

데이터는 계정에 저장되지 않습니다. Text Analytics API에서 수행하는 작업은 상태 비저장 작업입니다. 즉, 제공하는 텍스트가 처리된 후 결과가 즉시 반환됩니다.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>여러 프로그래밍 경험 수준을 위한 Text Analytics

프로그래밍 경험이 많지 않더라도 프로세스에서 Text Analytics API 사용을 시작할 수 있습니다. 이러한 자습서를 통해 API를 사용하여 자신의 경험 수준에 맞는 다양한 방법으로 텍스트를 분석하는 방법을 알아봅니다. 

* 필요한 최소한의 프로그래밍:
    * [Text Analytics 및 Power Automate를 사용하여 Excel에서 정보 추출](tutorials/extract-excel-information.md)
    * [Text Analytics API 및 MS Flow를 사용하여 Yammer 그룹의 코멘트에 대한 감정 식별](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Power BI를 Text Analytics API와 통합하여 고객 피드백 분석](tutorials/tutorial-power-bi-key-phrases.md)
* 권장되는 프로그래밍 환경:
    * [Azure Databricks를 사용하여 스트리밍 데이터에 대한 감정 분석](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Flask를 빌드하여 텍스트 번역, 감정 분석 및 음성 합성](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>지원되는 언어

이 섹션은 검색 가능성을 높이기 위해 별도 문서로 이동되었습니다. 이 콘텐츠에 대해서는 [Text Analytics API의 지원되는 언어](./language-support.md)를 참조하세요.

<a name="data-limits"></a>

## <a name="data-limits"></a>데이터 제한

모든 Text Analytics API 엔드포인트는 원시 텍스트 데이터를 수락합니다. 자세한 내용은 [데이터 제한](concepts/data-limits.md) 문서를 참조하세요.

## <a name="unicode-encoding"></a>유니코드 인코딩

Text Analytics API는 텍스트 표현 및 문자 수 계산에 유니코드 인코딩을 사용합니다. 측정 가능한 문자 수 차이 없이 UTF-8 및 UTF-16으로 요청을 제출할 수 있습니다. 유니코드 코드 포인트는 문자 길이에 대한 추론으로 사용되며, 텍스트 분석 데이터 제한을 위해 동일한 것으로 간주됩니다. [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements)를 사용하여 문자 수를 확인하는 경우 데이터 크기 측정에 사용하는 것과 동일한 방법을 사용합니다.

## <a name="next-steps"></a>다음 단계

+ Text Analytics에 대한 [Azure 리소스를 생성](../cognitive-services-apis-create-account.md)하여 애플리케이션의 키와 엔드포인트를 가져옵니다.

+ [빠른 시작](quickstarts/client-libraries-rest-api.md)를 사용하여 API 호출 보내기를 시작합니다. 최소의 코드로 텍스트를 제출하고, 분석을 선택하고, 결과를 보는 방법을 알아봅니다.

+ 새 릴리스 및 기능에 대한 자세한 내용은 [Text Analytics API의 새로운 기능](whats-new.md)을 참조하세요.

+ Azure Databricks를 사용하여 이 [감정 분석 자습서](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services)를 좀 더 면밀히 살펴보세요.

+ [외부 및 커뮤니티 콘텐츠 페이지](text-analytics-resource-external-community.md)에서 다른 도구 및 기술과 함께 Text Analytics API를 사용하는 방법을 보여 주는 블로그 게시물 및 비디오 목록을 확인하세요.
