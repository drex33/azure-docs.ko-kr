---
title: 새로운 기능 - LUIS(Language Understanding)
description: 이 문서는 Azure Cognitive Services Language Understanding API에 대한 소식으로 정기적으로 업데이트됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: a42798f5af63d9ec43760511a749861a955518cf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643083"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목에는 릴리스 정보, 비디오, 블로그 게시물 및 기타 유형의 정보가 포함될 수 있습니다. 서비스를 최신 상태로 유지하려면 이 페이지에 대한 책갈피를 지정하세요.

## <a name="release-notes"></a>릴리스 정보

### <a name="august-2021"></a>2021년 8월
* 노르웨이 동부 [게시 지역](luis-reference-regions.md#publishing-to-europe).
* 미국 서부 3 [게시 지역](luis-reference-regions.md#other-publishing-regions).

### <a name="april-2021"></a>2021년 4월

* 스위스 북부 [제작 지역](luis-reference-regions.md#publishing-to-europe).

### <a name="january-2021"></a>2021년 1월

* V3 예측 API는 이제 [Bing Spellcheck API](luis-tutorial-bing-spellcheck.md)를 지원합니다.
* 지역 포털(au.luis.ai 및 eu.luis.ai)은 단일 포털 및 URL로 통합됩니다. 이러한 포털 중 하나를 사용 중인 경우 자동으로 luis.ai로 리디렉션됩니다.

### <a name="december-2020"></a>2020년 12월

* 모든 LUIS 사용자는 [LUIS 작성자 리소스로 마이그레이션](luis-migration-authoring.md)해야 함
* REST API를 사용하여 일괄 처리 테스트를 제출하고, 의도 및 엔터티의 정확도 결과를 가져올 수 있게 해주는 새 [평가 엔드포인트](luis-how-to-batch-test.md#batch-testing-using-the-rest-api). v3.0-preview LUIS 엔드포인트부터 사용 가능합니다.

### <a name="june-2020"></a>2020년 6월

* [Preview 3.0 Authoring](luis-migration-authoring-entities.md) SDK -
    * 버전 3.2.0-preview.3- [.NET - NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * 버전 4.0.0-preview.3- [JS - NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* LUIS를 사용하여 DevOps 사례 적용
    * 개념
        * [LUIS에 대한 DevOps 사례](luis-concept-devops-sourcecontrol.md)
        * [LUIS DevOps에 대한 지속적인 통합 및 지속적인 전송](luis-concept-devops-automation.md)
        * [LUIS DevOps에 대한 테스트](luis-concept-devops-testing.md)
    * 방법
        * [GitHub Actions를 사용하여 LUIS 앱 개발에 DevOps 적용](./luis-concept-devops-automation.md)
    * [전체 코드 GitHub 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>2020년 5월 //Build

* **일반 공급(GA)** 으로 릴리스됨:
    * [Language Understanding 컨테이너](luis-container-howto.md)
    * Preview 포털이 [현재 포털](https://www.luis.ai)로 승격되었음, [이전](https://previous.luis.ai) 포털은 계속 사용할 수 있음
    * 새 기계 학습 엔터티 생성 및 레이블 지정 환경
    * 복합 및 단순 엔터티에서 기계 학습 엔터티로 [업그레이드 프로세스](migrate-from-composite-entity.md)
    * 단어 변형 정규화를 위한 [설정](how-to-application-settings-portal.md) 지원
* Preview Authoring API 변경
    * 중첩된 기계 학습 엔터티를 위한 앱 스키마 7.x
    * [필수 기능으로 마이그레이션](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* 개발자를 위한 새 리소스
    * [지속적인 통합 도구](developer-reference-resource.md#continuous-integration-tools)
    * 워크숍 - LUIS를 사용한 [_NLU(Natural Language Understanding)_ 에 대한 모범 사례 알아보기](developer-reference-resource.md#workshops)
* [고객 관리 키](./encrypt-data-at-rest.md) - 자신의 키를 사용하여 LUIS에서 사용하는 모든 데이터를 암호화
* [AI 표시](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding)(비디오) - LUIS의 새로운 기능 확인



### <a name="march-2020"></a>2020년 3월

* 이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

### <a name="november-4-2019---ignite"></a>2019년 11월 4일 - Ignite

* 비디오 - [LUIS 및 Azure Cognitive Services를 사용한 고급 NLU(Natural Language Understanding) 모델 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 개발자 생산성 향상
    * [예측 엔드포인트 V3](luis-migration-api-v3.md)의 일반 공급입니다.
    * `.lu`([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) 형식으로 앱을 가져오고 내보낼 수 있습니다. 이를 통해 효과적인 CI/CD 프로세스를 마련할 수 있습니다.
* 언어 확장
    * [아랍어 및 힌디어](luis-language-support.md)가 공개 미리 보기로 제공됩니다.
* 미리 작성된 모델
    * [미리 작성된 도메인](luis-reference-prebuilt-domains.md)은 현재 일반 공급(GA)됩니다.
    * 일본어 [미리 작성된 엔터티](luis-reference-prebuilt-entities.md#japanese-entity-support) - 나이, 통화, 숫자 및 백분율은 V3에서 지원되지 않습니다.
    * 이탈리아어 [미리 작성된 엔터티](luis-reference-prebuilt-entities.md#italian-entity-support) - 나이, 통화, 차원, 숫자 및 백분율 해상도가 V2에서 변경되었습니다.
* [preview.luis.ai portal](https://preview.luis.ai)의 사용자 환경 개선 - 개선된 레이블 지정 환경으로 복잡한 모델을 빌드 및 디버그할 수 있습니다. 미리 보기 포털 자습서를 사용해 보세요.
    * [의도 전용](tutorial-intents-only.md)
    * [분해 가능한 기계 학습 엔터티](tutorial-machine-learned-entity.md)
* 향상된 언어 이해 기능 - 더 손쉽게 [정교한 언어 모델 빌드](luis-concept-entity-types.md)
* 모델 수준에서 기계 학습 기능을 정의하고 모델을 다른 모델에 대한 신호로 사용할 수 있도록 설정합니다. 예를 들어 엔터티를 의도 및 기타 엔터티에 대한 특성으로 사용합니다.
* 새롭게 확장된 [제한](luis-limits.md) - 구 목록 및 전체 구에 대한 더 높아진 최대 한도, 새로운 모델을 기능 제한으로 제공
* 심층 계층 구조 형식의 텍스트에서 정보를 추출하여 대화 애플리케이션을 더욱 강력하게 만듭니다.

    ![기계 학습 엔터티 이미지](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019년 9월 3일

* Azure 작성 리소스 - [지금 마이그레이션하세요](luis-migration-authoring.md).
    * Azure 리소스당 500개 앱
    * 앱당 100가지 버전
* 미리 빌드된 엔터티에 터키어 지원
* datetimeV2에 이탈리아어 지원

### <a name="july-23-2019"></a>2019년 7월 23일

* 1\.2.3으로 [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 업데이트
    * 이탈리아어 나이, 온도, 차원 및 통화 인식기
    * 영어 휴일 인식 개선으로 추수감사절 기반 날짜를 올바르게 계산.
    * 프랑스어 DateTime 개선으로 날짜 및 시간이 아닌 엔터티의 가양성 감소.
    * 영어 DateRange의 달력/학교/회계 연도 및 머리글자어 지원.
    * 중국어 및 일본어 PhoneNumber 인식 기능 향상.
    * 영어 NumberRange 지원 개선.
    * 성능 향상

### <a name="june-24-2019"></a>2019년 6월 24일

* [OrdinalV2 미리 작성된 엔터티](luis-reference-prebuilt-ordinal-v2.md)로 다음, 이전 및 마지막과 같은 순서 지원. 영어 문화권에만 해당.

### <a name="may-6-2019---build-conference"></a>2019년 5월 6일 - //빌드 컨퍼런스

Build 2019 Conference에서 다음 기능이 발표되었습니다.

* [V3 API 마이그레이션의 미리 보기 가이드](luis-migration-api-v3.md)
* [분석 대시보드 향상](luis-how-to-use-dashboard.md)
* [미리 작성된 도메인 개선](luis-reference-prebuilt-domains.md)
* [동적 목록 엔터티](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [외부 엔터티](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>블로그

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>동영상

### <a name="2019-ignite-videos"></a>2019 Ignite 동영상

[LUIS 및 Azure Cognitive Services를 사용한 고급 NLU(Natural Language Understanding) 모델 | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 빌드 동영상

[Azure Conversational AI를 사용하여 차세대 비즈니스를 위해 비즈니스를 확장하는 방법](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>서비스 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)
