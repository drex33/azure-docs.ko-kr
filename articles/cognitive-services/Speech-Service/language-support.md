---
title: 언어 지원 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service는 음성 번역과 함께 음성 텍스트 변환 및 텍스트 음성 변환을 위해 다양한 언어를 지원합니다. 이 문서에서는 서비스 기능별 언어 지원에 대한 종합 목록을 제공합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: eur
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: da7d2acfe48d4d598e2bbbfddccb55e2a7d20241
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505368"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Speech Service에 대한 언어 및 음성 지원

언어 지원은 Speech Service 기능에 따라 달라 집니다. 다음 표에는 음성 텍스트 변환, [텍스트](#text-to-speech)음성 변환, [음성](#speech-to-text)번역 및 [Speaker Recognition](#speaker-recognition) 서비스 제공에 대한 [언어](#speech-translation) 지원이 요약되어 있습니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

Microsoft Speech SDK와 REST API는 모두 다음 언어(로캘)를 지원합니다. 

정확성을 향상시키기 위해 사용자 지정은 **오디오 + 휴먼 레이블 음성 텍스트** 또는 **관련 텍스트: 문장** 을 업로드하여 언어의 하위 집합에 제공됩니다. **오디오 + 휴먼 레이블 대화 기록** 을 사용하는 음향 모델의 사용자 지정 지원은 아래 나열된 특정 기본 모델로 제한됩니다. 다른 기본 모델 및 언어는 **관련 텍스트: 문장** 과 마찬가지로 사용자 지정 모델을 학습하기 위해 스크립트의 텍스트만 사용합니다. 사용자 지정에 대한 자세한 내용은 [Custom Speech 시작](./custom-speech-overview.md)을 참조하세요.

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| 언어                 | 로캘(BCP-47) | 사용자 지정  | [언어 식별](how-to-automatic-language-detection.md) | [발음 평가](how-to-pronunciation-assessment.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|--------------------------|
| 아랍어(알제리아)                   | `ar-DZ` | 텍스트                                   |                           |                          |
| 아랍어(바레인), 최신 표준  | `ar-BH` | 텍스트                                   |                           |                          |
| 아랍어(이집트)                     | `ar-EG` | 텍스트                                   | 예                          |                          |
| 아랍어(이라크)                      | `ar-IQ` | 텍스트                                   |                           |                          |
| 아랍어(이스라엘)                    | `ar-IL` | 텍스트                                   |                           |                          |
| 아랍어(요르단)                    | `ar-JO` | 텍스트                                   |                           |                          |
| 아랍어(쿠웨이트)                    | `ar-KW` | 텍스트                                   |                           |                          |
| 아랍어(레바논)                   | `ar-LB` | 텍스트                                   |                           |                          |
| 아랍어(리비아)                     | `ar-LY` | 텍스트                                   |                           |                          |
| 아랍어(모로코)                   | `ar-MA` | 텍스트                                   |                           |                          |
| 아랍어(오만)                      | `ar-OM` | 텍스트                                   |                           |                          |
| 아랍어(카타르)                     | `ar-QA` | 텍스트                                   |                           |                          |
| 아랍어(사우디아라비아)              | `ar-SA` | 텍스트                                   |                           |                          |
| 아랍어(팔레스타인 자치 정부)     | `ar-PS` | 텍스트                                   |                           |                          |
| 아랍어(시리아)                     | `ar-SY` | 텍스트                                   |                           |                          |
| 아랍어(튀니지)                   | `ar-TN` | 텍스트                                   |                           |                          |
| 아랍어(아랍에미리트)      | `ar-AE` | 텍스트                                   |                           |                          |
| 아랍어(예멘)                     | `ar-YE` | 텍스트                                   |                           |                          |
| 불가리아어(불가리아)               | `bg-BG` | 텍스트                                   |                           |                          |
| 카탈로니아어(스페인)                    | `ca-ES` | 텍스트<br>발음                  | 예                          |                          |
| 중국어(광둥어, 번체)   | `zh-HK` | 오디오(20201015)<br>텍스트                 |        예                   |                          |
| 중국어(북경어, 간체)     | `zh-CN` | 오디오(20200910)<br>텍스트                 |     예                      | 예                         |
| 중국어(대만어)       | `zh-TW` | 오디오(20190701, 20201015)<br>텍스트                 |           예                |                          |
| 크로아티아어(크로아티아)                 | `hr-HR` | 텍스트<br>발음                  |                           |                          |
| 체코어(체코)             | `cs-CZ` | 텍스트<br>발음                  |                           |                          |
| 덴마크어(덴마크)                   | `da-DK` | 텍스트<br>발음                  | 예                          |                          |
| 네덜란드어(네덜란드)                | `nl-NL` | 오디오(20201015)<br>텍스트<br>발음|    예                       |                          |
| 영어(오스트레일리아)                | `en-AU` | 오디오(20201019)<br>텍스트<br>발음| 예                          |                          |
| 영어(캐나다)                   | `en-CA` | 오디오(20201019)<br>텍스트<br>발음| 예                          |                          |
| 영어(가나)                    | `en-GH` | 텍스트<br>발음                  |                           |                          |
| 영어(홍콩)                | `en-HK` | 텍스트<br>발음                  |                           |                          |
| 영어(인도)                    | `en-IN` | 오디오(20200923)<br>텍스트<br>발음 |                          |                          |
| 영어(아일랜드)                  | `en-IE` | 텍스트<br>발음                  |                           |                          |
| 영어 (케냐)                    | `en-KE` | 텍스트<br>발음                  |                           |                          |
| 영어(뉴질랜드)              | `en-NZ` | 오디오(20201019)<br>텍스트<br>발음 |                          |                          |
| 영어(나이지리아)                  | `en-NG` | 텍스트<br>발음                  |                           |                          |
| 영어(필리핀)              | `en-PH` | 텍스트<br>발음                  |                           |                          |
| 영어(싱가포르)                | `en-SG` | 텍스트<br>발음                  |                           |                          |
| 영어(남아프리카 공화국)             | `en-ZA` | 텍스트<br>발음                  |                           |                          |
| 영어(탄자니아)                 | `en-TZ` | 텍스트<br>발음                  |                           |                          |
| 영어(영국)           | `en-GB` | 오디오(20201019)<br>텍스트<br>발음| 예                          | 예                         |
| 영어(미국)            | `en-US` | 오디오(20201019, 20210223)<br>텍스트<br>발음| 예                          | 예                         |
| 에스토니아어(에스토니아)                  | `et-EE` | 텍스트<br>발음                  |                           |                          |
| 필리핀어(필리핀)             | `fil-PH`| 텍스트<br>발음                  |                           |                          |
| 핀란드어(핀란드)                  | `fi-FI` | 텍스트<br>발음                  |     예                      |                          |
| 프랑스어(캐나다)                    | `fr-CA` | 오디오(20201015)<br>텍스트<br>발음|     예                      |                          |
| 프랑스어(프랑스)                    | `fr-FR` | 오디오(20201015)<br>텍스트<br>발음|      예                     |                          |
| 프랑스어(스위스)               | `fr-CH` | 텍스트<br>발음                  |                           |                          |
| 독일어(오스트리아)                   | `de-AT` | 텍스트<br>발음                  |                           |                          |
| 독일어(스위스)               | `de-CH` | 텍스트<br>발음                  |                           |                          |
| 독일어(독일)                   | `de-DE` | 오디오(20190701, 20200619, 20201127)<br>텍스트<br>발음|  예                         |                          |
| 그리스어(그리스)                     | `el-GR` | 텍스트                                   |  예                         |                          |
| 구자라트어(인도)                  | `gu-IN` | 텍스트                                   |                           |                          |
| 히브리어(이스라엘)                    | `he-IL` | 텍스트                                   |                           |                          |
| 힌디어(인도)                      | `hi-IN` | 오디오(20200701)<br>텍스트                 |     예                      |                          |
| 헝가리어(헝가리)                | `hu-HU` | 텍스트<br>발음                  |                           |                          |
| 인도네시아어(인도네시아)             | `id-ID` | 텍스트<br>발음                  |                           |                          |
| 아일랜드어(아일랜드)                    | `ga-IE` | 텍스트<br>발음                  |                           |                          |
| 이탈리아어(이탈리아)                    | `it-IT` | 오디오(20201016)<br>텍스트<br>발음|      예                     |                          |
| 일본어(일본)                   | `ja-JP` | 텍스트                                   |      예                     |                          |
| 카나다어(인도)                   | `kn-IN` | 텍스트                                   |                           |                          |
| 한국어(한국)                     | `ko-KR` | 오디오(20201015)<br>텍스트                 |      예                     |                          |
| 라트비아어(라트비아)                   | `lv-LV` | 텍스트<br>발음                  |                           |                          |
| 리투아니아어(리투아니아)             | `lt-LT` | 텍스트<br>발음                  |                           |                          |
| 말레이어(말레이시아)                   | `ms-MY` | 텍스트                                   |                           |                          |
| 몰타어(몰타)                    | `mt-MT` | 텍스트                                   |                           |                          |
| 마라티어(인도)                    | `mr-IN` | 텍스트                                   |                           |                          |
| 노르웨이어(복말)(노르웨이)         | `nb-NO` | 텍스트                                   |     예                      |                          |
| 페르시아어(이란)                     | `fa-IR` | 텍스트                                   |                           |                          |
| 폴란드어(폴란드)                    | `pl-PL` | 텍스트<br>발음                  |       예                    |                          |
| 포르투갈어(브라질)                | `pt-BR` | 오디오(20190620, 20201015)<br>텍스트<br>발음|          예                 |                          |
| 포르투갈어(포르투갈)              | `pt-PT` | 텍스트<br>발음                  |             예              |                          |
| 루마니아어(루마니아)                 | `ro-RO` | 텍스트<br>발음                  |  예                         |                          |
| 러시아어(러시아)                   | `ru-RU` | 오디오(20200907)<br>텍스트                 |                예           |                          |
| 슬로바키아어(슬로바키아)                  | `sk-SK` | 텍스트<br>발음                  |                           |                          |
| 슬로베니아어(슬로베니아)               | `sl-SI` | 텍스트<br>발음                  |                           |                          |
| 스페인어(아르헨티나)                | `es-AR` | 텍스트<br>발음                  |                           |                          |
| 스페인어(볼리비아)                  | `es-BO` | 텍스트<br>발음                  |                           |                          |
| 스페인어(칠레)                    | `es-CL` | 텍스트<br>발음                  |                           |                          |
| 스페인어(콜롬비아)                 | `es-CO` | 텍스트<br>발음                  |                           |                          |
| 스페인어(코스타리카)               | `es-CR` | 텍스트<br>발음                  |                           |                          |
| 스페인어(쿠바)                     | `es-CU` | 텍스트<br>발음                  |                           |                          |
| 스페인어(도미니카 공화국)       | `es-DO` | 텍스트<br>발음                  |                           |                          |
| 스페인어(에콰도르)                  | `es-EC` | 텍스트<br>발음                  |                           |                          |
| 스페인어(엘살바도르)              | `es-SV` | 텍스트<br>발음                  |                           |                          |
| 스페인어(적도 기니)        | `es-GQ` | 텍스트                                   |                           |                          |
| 스페인어(과테말라)                | `es-GT` | 텍스트<br>발음                  |                           |                          |
| 스페인어(온두라스)                 | `es-HN` | 텍스트<br>발음                  |                           |                          |
| 스페인어(멕시코)                   | `es-MX` | 오디오(20200907)<br>텍스트<br>발음|    예                       |                          |
| 스페인어(니카라과)                | `es-NI` | 텍스트<br>발음                  |                           |                          |
| 스페인어(파나마)                   | `es-PA` | 텍스트<br>발음                  |                           |                          |
| 스페인어(파라과이)                 | `es-PY` | 텍스트<br>발음                  |                           |                          |
| 스페인어(페루)                     | `es-PE` | 텍스트<br>발음                  |                           |                          |
| 스페인어(푸에르토리코)              | `es-PR` | 텍스트<br>발음                  |                           |                          |
| 스페인어(스페인)                    | `es-ES` | 오디오(20201015)<br>텍스트<br>발음|  예                         |                          |
| 스페인어(우루과이)                  | `es-UY` | 텍스트<br>발음                  |                           |                          |
| 스페인어(미국)                      | `es-US` | 텍스트<br>발음                  |                           |                          |
| 스페인어(베네수엘라)                | `es-VE` | 텍스트<br>발음                  |                           |                          |
| 스와힐리어(케냐)                    | `sw-KE` | 텍스트                                   |                           |                          |
| 스웨덴어(스웨덴)                   | `sv-SE` | 텍스트<br>발음                  |   예                        |                          |
| 타밀어(인도)                      | `ta-IN` | 텍스트                                   |                           |                          |
| 텔루구어(인도)                     | `te-IN` | 텍스트                                   |                           |                          |
| 태국어(태국)                    | `th-TH` | 텍스트                                   |      예                     |                          |
| 터키어(터키)                   | `tr-TR` | 텍스트                                   |                           |                          |
| 베트남어(베트남)               | `vi-VN` | 텍스트                                   |                           |                          |

## <a name="text-to-speech"></a>텍스트 음성 변환

Microsoft Speech SDK 및 REST API는 이러한 음성을 제공하며, 해당 음성 각각은 로캘로 식별되는 특정 언어를 지원합니다. [음성 목록 API](rest-text-to-speech.md#get-a-list-of-voices)를 통해 각 특정 영역/엔드포인트에 지원되는 언어 및 음성의 전체 목록을 가져올 수도 있습니다. 

> [!IMPORTANT]
> 가격 책정은 표준, 사용자 지정 및 인공신경망 음성마다 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 페이지를 참조하세요.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 텍스트-음성 변환은 심층 신경망에서 지원되는 새로운 형식의 음성 합성입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다.

인공신경망 음성을 사용하여 챗봇 및 음성 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, eBook 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

> [!NOTE]
> 인공신경망 음성은 24khz 샘플 속도를 사용하는 샘플에서 생성됩니다.
> 모든 음성이 합성될 때 다른 샘플 속도로 업샘플링하거나 다운샘플링할 수 있습니다.

| 언어 | Locale | 성별 | 음성 이름 | 스타일 지원 |
|---|---|---|---|---|
| 아프리칸스어(남아프리카 공화국) | af-ZA | Female | `af-ZA-AdriNeural` <sup>새로 생성</sup>  | 일반 |
| 아프리칸스어(남아프리카 공화국) | af-ZA | Male | `af-ZA-WillemNeural` <sup>새로 생성</sup>  | 일반 |
| 암하라어(에티오피아) | am-ET | Female | `am-ET-MekdesNeural` <sup>새로 생성</sup>  | 일반 |
| 암하라어(에티오피아) | am-ET | Male | `am-ET-AmehaNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(알제리아) | ar-AR-DZ | Female | `ar-DZ-AminaNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(알제리아) | ar-AR-DZ | Male | `ar-DZ-IsmaelNeural` <sup>새로 생성</sup>  | 일반 |
| Arabic (Bahrain) | ar-BH | Female | `ar-BH-LailaNeural` <sup>새로 생성</sup>  | 일반 |
| Arabic (Bahrain) | ar-BH | Male | `ar-BH-AliNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(이집트) | `ar-EG` | Female | `ar-EG-SalmaNeural` | 일반 |
| 아랍어(이집트) | `ar-EG` | Male | `ar-EG-ShakirNeural` | 일반 |
| 아랍어(이라크) | ar-IQ | Female | `ar-IQ-RanaNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(이라크) | ar-IQ | Male | `ar-IQ-BasselNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(요르단) | ar-JO | Female | `ar-JO-Sana Neural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(요르단) | ar-JO | Male | `ar-JO-Taim Neural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(쿠웨이트) | ar-KW | Female | `ar-KW-NouraNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(쿠웨이트) | ar-KW | Male | `ar-KW-FahedNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(리비아) | ar-LY | Female | `ar-LY-ImanNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(리비아) | ar-LY | Male | `ar-LY-OmarNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(모로코) | ar-MA | Female | `ar-MA-MounaNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(모로코) | ar-MA | Male | `ar-MA-JamalNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(카타르) | ar-QA | Female | `ar-QA-AmalNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(카타르) | ar-QA | Male | `ar-QA-MoazNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(사우디아라비아) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | 일반 |
| 아랍어(사우디아라비아) | `ar-SA` | Male | `ar-SA-HamedNeural` | 일반 |
| 아랍어(시리아) | ar-SY | Female | `ar-SY-AmanyNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(시리아) | ar-SY | Male | `ar-SY-LaithNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(튀니지) | ar-TN | Female | `ar-TN-ReemNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(튀니지) | ar-TN | Male | `ar-TN-HediNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(아랍에미리트) | ar-AE | Female | `ar-AE-FatimaNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(아랍에미리트) | ar-AE | Male | `ar-AE-HamdanNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(예멘) | ar-YE | Female | `ar-YE-MaryamNeural` <sup>새로 생성</sup>  | 일반 |
| 아랍어(예멘) | ar-YE | Male | `ar-YE-SalehNeural` <sup>새로 생성</sup>  | 일반 |
| 벵골어(방글라데시) | bn-BD | Female | `bn-BD-NabanitaNeural` <sup>새로 생성</sup>  | 일반 |
| 벵골어(방글라데시) | bn-BD | Male | `bn-BD-PradeepNeural` <sup>새로 생성</sup>  | 일반 |
| 불가리아어(불가리아) | `bg-BG` | Female | `bg-BG-KalinaNeural` | 일반 |
| 불가리아어(불가리아) | `bg-BG` | Male | `bg-BG-BorislavNeural` | 일반 |
| 버마어어(대만) | my-MM | Female | `my-MM-NilarNeural` <sup>새로 생성</sup>  | 일반 |
| 버마어어(대만) | my-MM | Male | `my-MM-ThihaNeural` <sup>새로 생성</sup>  | 일반 |
| 카탈로니아어(스페인) | `ca-ES` | Female | `ca-ES-AlbaNeural` | 일반 |
| 카탈로니아어(스페인) | `ca-ES` | Female | `ca-ES-JoanaNeural` | 일반 |
| 카탈로니아어(스페인) | `ca-ES` | Male | `ca-ES-EnricNeural` | 일반 |
| 중국어(광둥어, 번체) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 일반 |
| 중국어(광둥어, 번체) | `zh-HK` | Female | `zh-HK-HiuMaanNeural` | 일반 |
| 중국어(광둥어, 번체) | `zh-HK` | Male | `zh-HK-WanLungNeural` | 일반 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaohanNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaomoNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용할 수 있는 일반, 여러 역할 재생 및 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoruiNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 시니어 음성, 여러 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 음성 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoxuanNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용할 수 있는 일반, 여러 역할 재생 및 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | 이야기 내레이션에 최적화된 어린이 음성 |
| 중국어(북경어, 간체) | `zh-CN` | Male   | `zh-CN-YunxiNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Male | `zh-CN-YunyangNeural` | 뉴스 읽기에 최적화되어 있습니다.<br /> [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 여러 음성 스타일 |
| 중국어(북경어, 간체) | `zh-CN` | Male | `zh-CN-YunyeNeural` | 이야기 내레이션에 최적화 |
| 중국어(대만어) | `zh-TW` | Female | `zh-TW-HsiaoChenNeural` | 일반 |
| 중국어(대만어) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 일반 |
| 중국어(대만어) | `zh-TW` | Male | `zh-TW-YunJheNeural` | 일반 |
| 크로아티아어(크로아티아) | `hr-HR` | Female | `hr-HR-GabrijelaNeural` | 일반 |
| 크로아티아어(크로아티아) | `hr-HR` | Male | `hr-HR-SreckoNeural` | 일반 |
| 체코어(체코) | `cs-CZ` | Female | `cs-CZ-VlastaNeural` | 일반 |
| 체코어(체코) | `cs-CZ` | Male | `cs-CZ-AntoninNeural` | 일반 |
| 덴마크어(덴마크) | `da-DK` | Female | `da-DK-ChristelNeural` | 일반 |
| 덴마크어(덴마크) | `da-DK` | Male | `da-DK-JeppeNeural` | 일반 |
| 독일어(벨기에) | `nl-BE` | Female | `nl-BE-DenaNeural` | 일반 | 
| 독일어(벨기에) | `nl-BE` | Male | `nl-BE-ArnaudNeural` | 일반 | 
| 네덜란드어(네덜란드) | `nl-NL` | Female | `nl-NL-ColetteNeural` | 일반 |
| 네덜란드어(네덜란드) | `nl-NL` | Female | `nl-NL-FennaNeural` | 일반 |
| 네덜란드어(네덜란드) | `nl-NL` | Male | `nl-NL-MaartenNeural` | 일반 |
| 영어(오스트레일리아) | `en-AU` | Female | `en-AU-NatashaNeural` | 일반 |
| 영어(오스트레일리아) | `en-AU` | Male | `en-AU-WilliamNeural` | 일반 |
| 영어(캐나다) | `en-CA` | Female | `en-CA-ClaraNeural` | 일반 |
| 영어(캐나다) | `en-CA` | Male | `en-CA-LiamNeural` | 일반 |
| 영어(홍콩) | `en-HK` | Female | `en-HK-YanNeural` | 일반 |
| 영어(홍콩) | `en-HK` | Male | `en-HK-SamNeural` | 일반 |
| 영어(인도) | `en-IN` | Female | `en-IN-NeerjaNeural` | 일반 |
| 영어(인도) | `en-IN` | Male | `en-IN-PrabhatNeural` | 일반 |
| 영어(아일랜드) | `en-IE` | Female | `en-IE-EmilyNeural` | 일반 |
| 영어(아일랜드) | `en-IE` | Male | `en-IE-ConnorNeural` | 일반 |
| 영어 (케냐) | en-KE | Female | `en-KE-AsiliaNeural` <sup>새로 생성</sup>  | 일반 |
| 영어 (케냐) | en-KE | Male | `en-KE-ChilembaNeural` <sup>새로 생성</sup>  | 일반 |
| 영어(뉴질랜드) | `en-NZ` | Female | `en-NZ-MollyNeural` | 일반 |
| 영어(뉴질랜드) | `en-NZ` | Male | `en-NZ-MitchellNeural` | 일반 |
| 영어(나이지리아) | en-NG | Female | `en-NG-EzinneNeural` <sup>새로 생성</sup>  | 일반 |
| 영어(나이지리아) | en-NG | Male | `en-NG-AbeoNeural` <sup>새로 생성</sup>  | 일반 |
| 영어(필리핀) | `en-PH` | Female | `en-PH-RosaNeural` | 일반 | 
| 영어(필리핀) | `en-PH` | Male | `en-PH-JamesNeural` | 일반 | 
| 영어(싱가포르) | `en-SG` | Female | `en-SG-LunaNeural` | 일반 |
| 영어(싱가포르) | `en-SG` | Male | `en-SG-WayneNeural` | 일반 |
| 영어(남아프리카 공화국) | `en-ZA` | Female | `en-ZA-LeahNeural` | 일반 |
| 영어(남아프리카 공화국) | `en-ZA` | Male | `en-ZA-LukeNeural` | 일반 |
| 영어(탄자니아) | en-TZ | Female | `en-TZ-ImaniNeural` <sup>새로 생성</sup>  | 일반 |
| 영어(탄자니아) | en-TZ | Male | `en-TZ-ElimuNeural` <sup>새로 생성</sup>  | 일반 |
| 영어(영국) | `en-GB` | Female | `en-GB-LibbyNeural` | 일반 |
| 영어(영국) | `en-GB` | Female | `en-GB-MiaNeural`<sup>10월 30일 사용이 사용되었습니다. 아래 참조</sup> | 일반 |
| 영어(영국) | `en-GB` | Female | `en-GB-SoniaNeural` | 일반 |
| 영어(영국) | `en-GB` | Male | `en-GB-RyanNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-AmberNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-AriaNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 음성 스타일 |
| 영어(미국) | `en-US` | Female | `en-US-AshleyNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-CoraNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-ElizabethNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-JennyNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 음성 스타일 |
| 영어(미국) | `en-US` | Female | `en-US-MichelleNeural`| 일반 |
| 영어(미국) | `en-US` | Female | `en-US-MonicaNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-SaraNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 음성 스타일 |
| 영어(미국) | `en-US` | Kid | `en-US-AnaNeural`| 일반 |
| 영어(미국) | `en-US` | Male | `en-US-BrandonNeural` | 일반 |
| 영어(미국) | `en-US` | Male | `en-US-ChristopherNeural`  | 일반 |
| 영어(미국) | `en-US` | Male | `en-US-EricNeural` | 일반 |
| 영어(미국) | `en-US` | Male | `en-US-GuyNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 음성 스타일 |
| 영어(미국) | `en-US` | Male | `en-US-JacobNeural` | 일반 |
| 에스토니아어(에스토니아) | `et-EE` | Female | `et-EE-AnuNeural` | 일반 |
| 에스토니아어(에스토니아) | `et-EE` | Male | `et-EE-KertNeural` | 일반 |
| 필리핀어(필리핀) | fil-PH | Female | `fil-PH-BlessicaNeural` <sup>새로 생성</sup>  | 일반 |
| 필리핀어(필리핀) | fil-PH | Male | `fil-PH-AngeloNeural` <sup>새로 생성</sup>  | 일반 |
| 핀란드어(핀란드) | `fi-FI` | Female | `fi-FI-NooraNeural` | 일반 |
| 핀란드어(핀란드) | `fi-FI` | Female | `fi-FI-SelmaNeural` | 일반 |
| 핀란드어(핀란드) | `fi-FI` | Male | `fi-FI-HarriNeural` | 일반 |
| 프랑스어(벨기에) | `fr-BE` | Female | `fr-BE-CharlineNeural` | 일반 | 
| 프랑스어(벨기에) | `fr-BE` | Male | `fr-BE-GerardNeural` | 일반 | 
| 프랑스어(캐나다) | `fr-CA` | Female | `fr-CA-SylvieNeural` | 일반 |
| 프랑스어(캐나다) | `fr-CA` | Male | `fr-CA-AntoineNeural` | 일반 |
| 프랑스어(캐나다) | `fr-CA` | Male | `fr-CA-JeanNeural` | 일반 |
| 프랑스어(프랑스) | `fr-FR` | Female | `fr-FR-DeniseNeural` | 일반 |
| 프랑스어(프랑스) | `fr-FR` | Male | `fr-FR-HenriNeural` | 일반 |
| 프랑스어(스위스) | `fr-CH` | Female | `fr-CH-ArianeNeural` | 일반 |
| 프랑스어(스위스) | `fr-CH` | Male | `fr-CH-FabriceNeural` | 일반 |
| 갈리시아어(스페인) | gl-ES | Female | `gl-ES-SabelaNeural` <sup>새로 생성</sup>  | 일반 |
| 갈리시아어(스페인) | gl-ES | Male | `gl-ES-RoiNeural` <sup>새로 생성</sup>  | 일반 |
| 독일어(오스트리아) | `de-AT` | Female | `de-AT-IngridNeural` | 일반 |
| 독일어(오스트리아) | `de-AT` | Male | `de-AT-JonasNeural` | 일반 |
| 독일어(독일) | `de-DE` | Female | `de-DE-KatjaNeural` | 일반 |
| 독일어(독일) | `de-DE` | Male | `de-DE-ConradNeural` | 일반 |
| 독일어(스위스) | `de-CH` | Female | `de-CH-LeniNeural` | 일반 |
| 독일어(스위스) | `de-CH` | Male | `de-CH-JanNeural` | 일반 |
| 그리스어(그리스) | `el-GR` | Female | `el-GR-AthinaNeural` | 일반 |
| 그리스어(그리스) | `el-GR` | Male | `el-GR-NestorasNeural` | 일반 |
| 구자라트어(인도) | `gu-IN` | Female | `gu-IN-DhwaniNeural` | 일반 |
| 구자라트어(인도) | `gu-IN` | Male | `gu-IN-NiranjanNeural` | 일반 |
| 히브리어(이스라엘) | `he-IL` | Female | `he-IL-HilaNeural` | 일반 |
| 히브리어(이스라엘) | `he-IL` | Male | `he-IL-AvriNeural` | 일반 |
| 힌디어(인도) | `hi-IN` | Female | `hi-IN-SwaraNeural` | 일반 |
| 힌디어(인도) | `hi-IN` | Male | `hi-IN-MadhurNeural` | 일반 |
| 헝가리어(헝가리) | `hu-HU` | Female | `hu-HU-NoemiNeural` | 일반 |
| 헝가리어(헝가리) | `hu-HU` | Male | `hu-HU-TamasNeural` | 일반 |
| 인도네시아어(인도네시아) | `id-ID` | Female | `id-ID-GadisNeural` | 일반 |
| 인도네시아어(인도네시아) | `id-ID` | Male | `id-ID-ArdiNeural` | 일반 |
| 아일랜드어(아일랜드) | `ga-IE` | Female | `ga-IE-OrlaNeural` | 일반 |
| 아일랜드어(아일랜드) | `ga-IE` | Male | `ga-IE-ColmNeural` | 일반 |
| 이탈리아어(이탈리아) | `it-IT` | Female | `it-IT-ElsaNeural` | 일반 |
| 이탈리아어(이탈리아) | `it-IT` | Female | `it-IT-IsabellaNeural` | 일반 |
| 이탈리아어(이탈리아) | `it-IT` | Male | `it-IT-DiegoNeural` | 일반 |
| 일본어(일본) | `ja-JP` | Female | `ja-JP-NanamiNeural` | 일반 |
| 일본어(일본) | `ja-JP` | Male | `ja-JP-KeitaNeural` | 일반 |
| Javanese(포르투갈어) | jv-ID | Female | `jv-ID-SitiNeural` <sup>새로 생성</sup>  | 일반 |
| Javanese(포르투갈어) | jv-ID | Male | `jv-ID-DimasNeural` <sup>새로 생성</sup>  | 일반 |
| 크메르어(캄보디아) | km-KH | Female | `km-KH-SreymomNeural` <sup>새로 생성</sup>  | 일반 |
| 크메르어(캄보디아) | km-KH | Male | `km-KH-PisethNeural` <sup>새로 생성</sup>  | 일반 |
| 한국어(한국) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 일반 |
| 한국어(한국) | `ko-KR` | Male | `ko-KR-InJoonNeural` | 일반 |
| 라트비아어(라트비아) | `lv-LV` | Female | `lv-LV-EveritaNeural` | 일반 |
| 라트비아어(라트비아) | `lv-LV` | Male | `lv-LV-NilsNeural` | 일반 |
| 리투아니아어(리투아니아) | `lt-LT` | Female | `lt-LT-OnaNeural` | 일반 |
| 리투아니아어(리투아니아) | `lt-LT` | Male | `lt-LT-LeonasNeural` | 일반 |
| 말레이어(말레이시아) | `ms-MY` | Female | `ms-MY-YasminNeural` | 일반 |
| 말레이어(말레이시아) | `ms-MY` | Male | `ms-MY-OsmanNeural` | 일반 |
| 몰타어(몰타) | `mt-MT` | Female | `mt-MT-GraceNeural` | 일반 |
| 몰타어(몰타) | `mt-MT` | Male | `mt-MT-JosephNeural` | 일반 |
| 마라티어(인도) | `mr-IN` | Female | `mr-IN-AarohiNeural` | 일반 |
| 마라티어(인도) | `mr-IN` | Male | `mr-IN-ManoharNeural` | 일반 |
| 노르웨이어(복말)(노르웨이) | `nb-NO` | Female | `nb-NO-IselinNeural` | 일반 |
| 노르웨이어(복말)(노르웨이) | `nb-NO` | Female | `nb-NO-PernilleNeural` | 일반 |
| 노르웨이어(복말)(노르웨이) | `nb-NO` | Male | `nb-NO-FinnNeural` | 일반 |
| 페르시아어(이란) | fa-IR | Female | `fa-IR-DilaraNeural` <sup>새로 생성</sup>  | 일반 |
| 페르시아어(이란) | fa-IR | Male | `fa-IR-FaridNeural` <sup>새로 생성</sup>  | 일반 |
| 폴란드어(폴란드) | `pl-PL` | Female | `pl-PL-AgnieszkaNeural` | 일반 |
| 폴란드어(폴란드) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | 일반 |
| 폴란드어(폴란드) | `pl-PL` | Male | `pl-PL-MarekNeural` | 일반 |
| 포르투갈어(브라질) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | [SSML을 통해](speech-synthesis-markup.md#adjust-speaking-styles) 사용 가능한 일반, 여러 음성 스타일 |
| 포르투갈어(브라질) | `pt-BR` | Male | `pt-BR-AntonioNeural` | 일반 |
| 포르투갈어(포르투갈) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 일반 |
| 포르투갈어(포르투갈) | `pt-PT` | Female | `pt-PT-RaquelNeural` | 일반 |
| 포르투갈어(포르투갈) | `pt-PT` | Male | `pt-PT-DuarteNeural` | 일반 |
| 루마니아어(루마니아) | `ro-RO` | Female | `ro-RO-AlinaNeural` | 일반 |
| 루마니아어(루마니아) | `ro-RO` | Male | `ro-RO-EmilNeural` | 일반 |
| 러시아어(러시아) | `ru-RU` | Female | `ru-RU-DariyaNeural` | 일반 |
| 러시아어(러시아) | `ru-RU` | Female | `ru-RU-SvetlanaNeural` | 일반 |
| 러시아어(러시아) | `ru-RU` | Male | `ru-RU-DmitryNeural` | 일반 |
| 슬로바키아어(슬로바키아) | `sk-SK` | Female | `sk-SK-ViktoriaNeural` | 일반 |
| 슬로바키아어(슬로바키아) | `sk-SK` | Male | `sk-SK-LukasNeural` | 일반 |
| 슬로베니아어(슬로베니아) | `sl-SI` | Female | `sl-SI-PetraNeural` | 일반 |
| 슬로베니아어(슬로베니아) | `sl-SI` | Male | `sl-SI-RokNeural` | 일반 |
| 2018년 3월 21일, | so-SO | Female | `so-SO-UbaxNeural` <sup>새로 생성</sup>  | 일반 |
| 2018년 3월 21일, | so-SO | Male | `so-SO-MuuseNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(아르헨티나) | `es-AR` | Female | `es-AR-ElenaNeural` | 일반 |
| 스페인어(아르헨티나) | `es-AR` | Male | `es-AR-TomasNeural` | 일반 |
| 스페인어(볼리비아) | es-BO | Female | `es-BO-SofiaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(볼리비아) | es-BO | Male | `es-BO-MarceloNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(칠레) | es-CL | Female | `es-CL-CatalinaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(칠레) | es-CL | Male | `es-CL-LorenzoNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(콜롬비아) | `es-CO` | Female | `es-CO-SalomeNeural` | 일반 |
| 스페인어(콜롬비아) | `es-CO` | Male | `es-CO-GonzaloNeural` | 일반 |
| 스페인어(코스타리카) | es-CR | Female | `es-CR-MariaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(코스타리카) | es-CR | Male | `es-CR-JuanNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(쿠바) | es-CU | Female | `es-CU-BelkysNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(쿠바) | es-CU | Male | `es-CU-ManuelNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(도미니카 공화국) | es-DO | Female | `es-DO-RamonaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(도미니카 공화국) | es-DO | Male | `es-DO-EmilioNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(에콰도르) | es-EC | Female | `es-EC-AndreaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(에콰도르) | es-EC | Male | `es-EC-LuisNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(엘살바도르) | es-SV | Female | `es-SV-LorenaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(엘살바도르) | es-SV | Male | `es-SV-RodrigoNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(적도 기니) | es-GQ | Female | `es-GQ-TeresaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(적도 기니) | es-GQ | Male | `es-GQ-JavierNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(과테말라) | es-GT | Female | `es-GT-MartaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(과테말라) | es-GT | Male | `es-GT-AndresNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(온두라스) | es-HN | Female | `es-HN-KarlaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(온두라스) | es-HN | Male | `es-HN-CarlosNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(멕시코) | `es-MX` | Female | `es-MX-DaliaNeural` | 일반 |
| 스페인어(멕시코) | `es-MX` | Male | `es-MX-JorgeNeural` | 일반 |
| 스페인어(니카라과) | es-NI | Female | `es-NI-YolandaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(니카라과) | es-NI | Male | `es-NI-FedericoNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(파나마) | es-PA | Female | `es-PA-MargaritaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(파나마) | es-PA | Male | `es-PA-RobertoNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(파라과이) | es-PY | Female | `es-PY-TaniaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(파라과이) | es-PY | Male | `es-PY-MarioNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(페루) | es-PE | Female | `es-PE-CamilaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(페루) | es-PE | Male | `es-PE-AlexNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(푸에르토리코) | es-PR | Female | `es-PR-Karina Neural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(푸에르토리코) | es-PR | Male | `es-PR-Victor Neural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(스페인) | `es-ES` | Female | `es-ES-ElviraNeural` | 일반 |
| 스페인어(스페인) | `es-ES` | Male | `es-ES-AlvaroNeural` | 일반 |
| 스페인어(우루과이) | es-UY | Female | `es-UY-ValentinaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(우루과이) | es-UY | Male | `es-UY-MateoNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(미국) | `es-US` | Female | `es-US-PalomaNeural` | 일반 |
| 스페인어(미국) | `es-US` | Male | `es-US-AlonsoNeural` | 일반 |
| 스페인어(베네수엘라) | es-VE | Female | `es-VE-PaolaNeural` <sup>새로 생성</sup>  | 일반 |
| 스페인어(베네수엘라) | es-VE | Male | `es-VE-SebastianNeural` <sup>새로 생성</sup>  | 일반 |
| 순다 문자 (인도네시아) | su ID | Female | `su-ID-TutiNeural` <sup>새로 생성</sup>  | 일반 |
| 순다 문자 (인도네시아) | su ID | Male | `su-ID-JajangNeural` <sup>새로 생성</sup>  | 일반 |
| 스와힐리어(케냐) | `sw-KE` | Female | `sw-KE-ZuriNeural` | 일반 |
| 스와힐리어(케냐) | `sw-KE` | Male | `sw-KE-RafikiNeural` | 일반 |
| 스와힐리어 (탄자니아) | sw-TZ | Female | `sw-TZ-RehemaNeural` <sup>새로 생성</sup>  | 일반 |
| 스와힐리어 (탄자니아) | sw-TZ | Male | `sw-TZ-DaudiNeural` <sup>새로 생성</sup>  | 일반 |
| 스웨덴어(스웨덴) | `sv-SE` | Female | `sv-SE-HilleviNeural` | 일반 |
| 스웨덴어(스웨덴) | `sv-SE` | Female | `sv-SE-SofieNeural` | 일반 |
| 스웨덴어(스웨덴) | `sv-SE` | Male | `sv-SE-MattiasNeural` | 일반 |
| 타밀어(인도) | `ta-IN` | Female | `ta-IN-PallaviNeural` | 일반 |
| 타밀어(인도) | `ta-IN` | Male | `ta-IN-ValluvarNeural` | 일반 |
| 타밀어 (싱가포르) | ta-SG | Female | `ta-SG-VenbaNeural` <sup>새로 생성</sup>  | 일반 |
| 타밀어 (싱가포르) | ta-SG | Male | `ta-SG-AnbuNeural` <sup>새로 생성</sup>  | 일반 |
| 타밀어 (스리랑카) | ta-LK | Female | `ta-LK-SaranyaNeural` <sup>새로 생성</sup>  | 일반 |
| 타밀어 (스리랑카) | ta-LK | Male | `ta-LK-KumarNeural` <sup>새로 생성</sup>  | 일반 |
| 텔루구어(인도) | `te-IN` | Female | `te-IN-ShrutiNeural` | 일반 |
| 텔루구어(인도) | `te-IN` | Male | `te-IN-MohanNeural` | 일반 |
| 태국어(태국) | `th-TH` | Female | `th-TH-AcharaNeural` | 일반 |
| 태국어(태국) | `th-TH` | Female | `th-TH-PremwadeeNeural` | 일반 |
| 태국어(태국) | `th-TH` | Male | `th-TH-NiwatNeural` | 일반 |
| 터키어(터키) | `tr-TR` | Female | `tr-TR-EmelNeural` | 일반 |
| 터키어(터키) | `tr-TR` | Male | `tr-TR-AhmetNeural` | 일반 |
| 우크라이나어(우크라이나) | `uk-UA` | Female | `uk-UA-PolinaNeural` | 일반 | 
| 우크라이나어(우크라이나) | `uk-UA` | Male | `uk-UA-OstapNeural` | 일반 | 
| 우르두어 (인도) | 사용자 | Female | `ur-IN-GulNeural` <sup>새로 생성</sup>  | 일반 |
| 우르두어 (인도) | 사용자 | Male | `ur-IN-SalmanNeural` <sup>새로 생성</sup>  | 일반 |
| 우르두어(파키스탄) | `ur-PK` | Female | `ur-PK-UzmaNeural`  | 일반 | 
| 우르두어(파키스탄) | `ur-PK` | Male | `ur-PK-AsadNeural` | 일반 | 
| 우즈베크어 (우즈베키스탄) | uz-UZ | Female | `uz-UZ-MadinaNeural` <sup>새로 생성</sup>  | 일반 |
| 우즈베크어 (우즈베키스탄) | uz-UZ | Male | `uz-UZ-SardorNeural` <sup>새로 생성</sup>  | 일반 |
| 베트남어(베트남) | `vi-VN` | Female | `vi-VN-HoaiMyNeural` | 일반 |
| 베트남어(베트남) | `vi-VN` | Male | `vi-VN-NamMinhNeural` | 일반 |
| 웨일스어(영국) | `cy-GB` | Female | `cy-GB-NiaNeural` | 일반 | 
| 웨일스어(영국) | `cy-GB` | Male | `cy-GB-AledNeural` | 일반 | 
| 줄루어 (남아프리카 공화국) | zu-za-ZA | Female | `zu-ZA-ThandoNeural` <sup>새로 생성</sup>  | 일반 |
| 줄루어 (남아프리카 공화국) | zu-za-ZA | Male | `zu-ZA-ThembaNeural` <sup>새로 생성</sup>  | 일반 |

> [!IMPORTANT]
> 영어 (영국) 음성은 `en-GB-MiaNeural` **2021 년 10 월 30** 일에 사용 중지 됩니다. 에 대 한 모든 서비스 요청 `en-GB-MiaNeural` 은 `en-GB-SoniaNeural` **30 년 10 2021 월 30** 일 이후에 자동으로 다시 전달 됩니다.
> 컨테이너 신경망을 사용 하는 경우 **2021 년 10 월 30** 일 전에 최신 버전을 [다운로드](speech-container-howto.md#get-the-container-image-with-docker-pull) 하 여 배포 하세요. **2021 년 10 월 30** 일 이후에는 이전 버전의 모든 요청이 거부 됩니다.

#### <a name="neural-voices-in-preview"></a>미리 보기로 제공되는 인공신경망 음성

아래 인공신경망 음성은 공개 미리 보기로 제공됩니다. 

| 언어                         | Locale  | 성별 | 음성 이름                             | 스타일 지원 |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| 영어(미국) | `en-US` | Female | `en-US-JennyMultilingualNeural` <sup>새로 생성</sup> | [SSML을 사용](speech-synthesis-markup.md#create-an-ssml-document)하여 사용할 수 있는 일반적인 다국어 기능 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaochenNeural` <sup>새로 생성</sup> | 갑작스런 대화에 최적화 됨 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoyanNeural` <sup>새로 생성</sup> | 고객 서비스에 최적화 |
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoshuangNeural` <sup>새로 생성</sup> | 자식 스토리 및 채팅에 최적화된 자식 음성 [SSML을 사용하여](speech-synthesis-markup.md#adjust-speaking-styles) 사용할 수 있는 여러 음성 스타일|
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-XiaoqiuNeural` <sup>새로 생성</sup> | 내레이션에 최적화 |

> [!IMPORTANT]
> 공개 미리 보기로 제공되는 음성은 미국 동부, 서유럽 및 동남 아시아의 3개 서비스 지역에서만 사용할 수 있습니다.

> [!TIP]
> `en-US-JennyNeuralMultilingual`은 여러 언어를 지원합니다. 지원되는 언어 목록은 [음성 목록 API](rest-text-to-speech.md#get-a-list-of-voices)를 확인하세요.

국가별 가용성에 대한 자세한 내용은 [지역](regions.md#neural-and-standard-voices)을 참조하세요.

말하기 스타일과 같은 인공신경망 음성을 구성하고 조정하는 방법에 대해 알아보려면 [음성 합성 마크업 언어](speech-synthesis-markup.md#adjust-speaking-styles)를 참조하세요.

> [!IMPORTANT]
> `en-US-JessaNeural` 음성이 `en-US-AriaNeural`로 변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환하세요.

> [!TIP]
> 음성 합성 요청에서 "Microsoft Server Speech 텍스트 음성 변환 음성(en-US, ChristopherNeural)"과 같은 전체 서비스 이름 매핑을 계속 사용할 수 있습니다.

### <a name="standard-voices"></a>표준 음성

75개를 초과하는 표준 음성은 45개 이상의 언어 및 로캘에서 사용할 수 있으며 텍스트를 합성된 음성으로 변환할 수 있습니다. 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#neural-and-standard-voices)을 참조하세요.

> [!IMPORTANT]
> 표준 음성은 **2024년 8월 31일** 에 중단되며 그 날짜 이후에는 더 이상 지원되지 않습니다.**2021년 8월 31일** 이전에 만든 모든 기존 Speech 구독에 전송된 전자 메일에서 이를 발표했습니다. 사용 중지 **기간(2021년 8월**  -  **31일 2024년 8월 31일)** 동안 기존 표준 음성 사용자는 표준 음성을 계속 사용할 수 있지만 모든 새 사용자/새 음성 리소스는 신경망 음성을 선택해야 합니다.

> [!NOTE]
> 두 가지 예외를 제외하고 16khz 샘플 속도를 사용하는 샘플에서 표준 음성이 생성됩니다.
> **en-US-AriaRUS** 및 **en-US-GuyRUS** 음성도 24khz 샘플 속도를 사용하는 샘플에서 생성됩니다.
> 모든 음성이 합성될 때 다른 샘플 속도로 업샘플링하거나 다운샘플링할 수 있습니다.

| 언어 | 로캘(BCP-47) | 성별 | 음성 이름 |
|--|--|--|--|
| 아랍어(이집트) | `ar-EG` | Female | `ar-EG-Hoda`|
| 아랍어(사우디아라비아) | `ar-SA` | Male | `ar-SA-Naayf`|
| 불가리아어(불가리아) | `bg-BG` | Male | `bg-BG-Ivan`|
| 카탈로니아어(스페인) | `ca-ES` | Female | `ca-ES-HerenaRUS`|
| 중국어(광둥어, 번체) | `zh-HK` | Male | `zh-HK-Danny`|
| 중국어(광둥어, 번체) | `zh-HK` | Female | `zh-HK-TracyRUS`|
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-HuihuiRUS`|
| 중국어(북경어, 간체) | `zh-CN` | Male | `zh-CN-Kangkang`|
| 중국어(북경어, 간체) | `zh-CN` | Female | `zh-CN-Yaoyao`|
| 중국어(대만어) |  `zh-TW` | Female | `zh-TW-HanHanRUS`|
| 중국어(대만어) |  `zh-TW` | Female | `zh-TW-Yating`|
| 중국어(대만어) |  `zh-TW` | Male | `zh-TW-Zhiwei`|
| 크로아티아어(크로아티아) | `hr-HR` | Male | `hr-HR-Matej`|
| 체코어(체코) | `cs-CZ` | Male | `cs-CZ-Jakub`|
| 덴마크어(덴마크) | `da-DK` | Female | `da-DK-HelleRUS`|
| 네덜란드어(네덜란드) | `nl-NL` | Female | `nl-NL-HannaRUS`|
| 영어(오스트레일리아) | `en-AU` | Female | `en-AU-Catherine`|
| 영어(오스트레일리아) | `en-AU` | Female | `en-AU-HayleyRUS`|
| 영어(캐나다) | `en-CA` | Female | `en-CA-HeatherRUS`|
| 영어(캐나다) | `en-CA` | Female | `en-CA-Linda`|
| 영어(인도) | `en-IN` | Female | `en-IN-Heera`|
| 영어(인도) | `en-IN` | Female | `en-IN-PriyaRUS`|
| 영어(인도) | `en-IN` | Male | `en-IN-Ravi`|
| 영어(아일랜드) | `en-IE` | Male | `en-IE-Sean`|
| 영어(영국) | `en-GB` | Male | `en-GB-George`|
| 영어(영국) | `en-GB` | Female | `en-GB-HazelRUS`|
| 영어(영국) | `en-GB` | Female | `en-GB-Susan`|
| 영어(미국) | `en-US` | Male | `en-US-BenjaminRUS`|
| 영어(미국) | `en-US` | Male | `en-US-GuyRUS`|
| 영어(미국) | `en-US` | Female | `en-US-AriaRUS`|
| 영어(미국) | `en-US` | Female | `en-US-ZiraRUS`|
| 핀란드어(핀란드) | `fi-FI` | Female | `fi-FI-HeidiRUS`|
| 프랑스어(캐나다) | `fr-CA` | Female | `fr-CA-Caroline`|
| 프랑스어(캐나다) | `fr-CA` | Female | `fr-CA-HarmonieRUS`|
| 프랑스어(프랑스) | `fr-FR` | Female | `fr-FR-HortenseRUS`|
| 프랑스어(프랑스) | `fr-FR` | Female | `fr-FR-Julie`|
| 프랑스어(프랑스) | `fr-FR` | Male | `fr-FR-Paul`|
| 프랑스어(스위스) | `fr-CH` | Male | `fr-CH-Guillaume`|
| 독일어(오스트리아) | `de-AT` | Male | `de-AT-Michael`|
| 독일어(독일) | `de-DE` | Female | `de-DE-HeddaRUS`|
| 독일어(독일) | `de-DE` | Male | `de-DE-Stefan`|
| 독일어(스위스) | `de-CH` | Male | `de-CH-Karsten`|
| 그리스어(그리스) | `el-GR` | Male | `el-GR-Stefanos`|
| 히브리어(이스라엘) | `he-IL` | Male | `he-IL-Asaf`|
| 힌디어(인도) | `hi-IN` | Male | `hi-IN-Hemant`|
| 힌디어(인도) | `hi-IN` | Female | `hi-IN-Kalpana`|
| 헝가리어(헝가리) | `hu-HU` | Male | `hu-HU-Szabolcs`|
| 인도네시아어(인도네시아) | `id-ID` | Male | `id-ID-Andika`|
| 이탈리아어(이탈리아) | `it-IT` | Male | `it-IT-Cosimo`|
| 이탈리아어(이탈리아) | `it-IT` | Female | `it-IT-LuciaRUS`|
| 일본어(일본) | `ja-JP` | Female | `ja-JP-Ayumi`|
| 일본어(일본) | `ja-JP` | Female | `ja-JP-HarukaRUS`|
| 일본어(일본) | `ja-JP` | Male | `ja-JP-Ichiro`|
| 한국어(한국) | `ko-KR` | Female | `ko-KR-HeamiRUS`|
| 말레이어(말레이시아) | `ms-MY` | Male | `ms-MY-Rizwan`|
| 노르웨이어(복말)(노르웨이) | `nb-NO` | Female | `nb-NO-HuldaRUS`|
| 폴란드어(폴란드) | `pl-PL` | Female | `pl-PL-PaulinaRUS`|
| 포르투갈어(브라질) | `pt-BR` | Male | `pt-BR-Daniel`|
| 포르투갈어(브라질) | `pt-BR` | Female | `pt-BR-HeloisaRUS`|
| 포르투갈어(포르투갈) | `pt-PT` | Female | `pt-PT-HeliaRUS`|
| 루마니아어(루마니아) | `ro-RO` | Male | `ro-RO-Andrei`|
| 러시아어(러시아) | `ru-RU` | Female | `ru-RU-EkaterinaRUS`|
| 러시아어(러시아) | `ru-RU` | Female | `ru-RU-Irina`|
| 러시아어(러시아) | `ru-RU` | Male | `ru-RU-Pavel`|
| 슬로바키아어(슬로바키아) | `sk-SK` | Male | `sk-SK-Filip`|
| 슬로베니아어(슬로베니아) | `sl-SI` | Male | `sl-SI-Lado`|
| 스페인어(멕시코) | `es-MX` | Female | `es-MX-HildaRUS`|
| 스페인어(멕시코) | `es-MX` | Male | `es-MX-Raul`|
| 스페인어(스페인) | `es-ES` | Female | `es-ES-HelenaRUS`|
| 스페인어(스페인) | `es-ES` | Female | `es-ES-Laura`|
| 스페인어(스페인) | `es-ES` | Male | `es-ES-Pablo`|
| 스웨덴어(스웨덴) | `sv-SE` | Female | `sv-SE-HedvigRUS`|
| 타밀어(인도) | `ta-IN` | Male | `ta-IN-Valluvar`|
| 텔루구어(인도) | `te-IN` | Female | `te-IN-Chitra`|
| 태국어(태국) | `th-TH` | Male | `th-TH-Pattara`|
| 터키어(터키) | `tr-TR` | Female | `tr-TR-SedaRUS`|
| 베트남어(베트남) | `vi-VN` | Male | `vi-VN-An` |

> [!IMPORTANT]
> `en-US-Jessa` 음성이 `en-US-Aria`로 변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환하세요.

> [!TIP]
> 음성 합성 요청에서 "Microsoft Server Speech 텍스트 음성 변환 음성(en-US, AriaRUS)"과 같은 전체 서비스 이름 매핑을 계속 사용할 수 있습니다.

### <a name="customization"></a>사용자 지정

Custom Voice는 인공신경망 계층(즉, 사용자 지정 신경망 음성)에서 사용할 수 있습니다. 인공신경망 TTS 기술 및 다국어 다중 화자 유니버설 모델을 기반으로 하는 사용자 지정 신경망 음성을 사용하면 말하기 스타일이 다양한 합성 음성 또는 적응 가능한 크로스 언어를 만들 수 있습니다. 지원되는 언어는 아래를 확인하세요.  

> [!IMPORTANT]
> 통계 매개 변수 및 사용자 지정 음성의 동시 학습 방법을 포함한 표준 계층은 더 이상 사용되지 않으며 2024년 2월 29일에 사용 중지됩니다. 비 인공신경망/표준 Custom Voice를 사용하는 경우, 즉시 사용자 지정 신경망 음성으로 마이그레이션하여 더 나은 품질을 활용하고 책임감 있게 음성을 배포합니다. 

| 언어 | Locale | 신경 | 교차 다국어 |
|--|--|--|--|
| 아랍어(이집트) | `ar-EG` | 예 | 아니요 |
| 불가리아어(불가리아) | `bg-BG` | 예 | 아니요 |
| 중국어(북경어, 간체) | `zh-CN` | 예 | 예 |
| 중국어(북경어, 간체), 영어 이중 언어 | `zh-CN` 이중 언어 | 예 | 예 |
| 중국어(대만어) | `zh-TW` | 예 | 아니요 |
| 체코어(체코) | `cs-CZ` | 예 | 아니요 |
| 네덜란드어(네덜란드) | `nl-NL` | 예 | 아니요 |
| 영어(오스트레일리아) | `en-AU` | 예 | 예 |
| 영어(캐나다) | `en-CA` | 예 | 아니요 |
| 영어(인도) | `en-IN` | 예 | 아니요 |
| 영어(아일랜드) | `en-IE` | 예 | 아니요 |
| 영어(영국) | `en-GB` | 예 | 예 |
| 영어(미국) | `en-US` | 예 | 예 |
| 프랑스어(캐나다) | `fr-CA` | 예 | 예 |
| 프랑스어(프랑스) | `fr-FR` | 예 | 예 |
| 독일어(오스트리아) | `de-AT` | 예 | 아니요 |
| 독일어(독일) | `de-DE` | 예 | 예 |
| 헝가리어(헝가리) | `hu-HU` | 예 | 아니요 |
| 이탈리아어(이탈리아) | `it-IT` | 예 | 예 |
| 일본어(일본) | `ja-JP` | 예 | 예 |
| 한국어(한국) | `ko-KR` | 예 | 예 |
| 노르웨이어(복말)(노르웨이) | `nb-NO` | 예 | 아니요 |
| 포르투갈어(브라질) | `pt-BR` | 예 | 예 |
| 포르투갈어(포르투갈) | `pt-PT` | 예 | 아니요 |
| 러시아어(러시아) | `ru-RU` | 예 | 예 |
| 슬로바키아어(슬로바키아) | `sk-SK` | 예 | 아니요 |
| 스페인어(멕시코) | `es-MX` | 예 | 예 |
| 스페인어(스페인) | `es-ES` | 예 | 예 |
| 터키어(터키) | `tr-TR` | 예 | 아니요 |
| 베트남어(베트남) | `vi-VN` | 예 | 아니요 |

사용자 지정 음성 모델을 학습하는 데 사용할 학습 데이터와 일치하는 올바른 로캘을 선택합니다. 예를 들어, 보유하고 있는 기록 데이터가 영국식 영어로 말하는 경우 `en-GB`를 선택합니다.

> [!NOTE]
> Custom Voice에서는 이중 언어 모델 학습(중국어-영어 이중 언어 제외)을 지원하지 않습니다. 영어도 말할 수 있는 중국어 음성을 학습하려면 "중국어-영어 이중 언어"를 선택합니다. 표준 메서드를 사용하는 중국어-영어 이중 언어 모델 학습은 북유럽과 미국 중북부에서만 사용할 수 있습니다. 사용자 지정 신경망 음성 학습은 영국 남부와 미국 동부에서 사용할 수 있습니다.

## <a name="speech-translation"></a>음성 번역

**Speech Translation** API는 음성 간 음성 및 음성을 텍스트로 번역에 대해 다른 언어를 지원합니다. 원본 언어는 항상 음성 텍스트 변환 언어 테이블에 나와 있는 것이어야 합니다. 사용 가능한 대상 언어는 번역 대상이 음성인지 또는 텍스트인지에 따라 달라집니다. 들어오는 음성을  [지원되는 언어](https://www.microsoft.com/translator/business/languages/)로 번역할 수 있습니다. [음성 합성](language-support.md#text-languages)에 언어의 하위 집합을 사용할 수 있습니다.

### <a name="text-languages"></a>텍스트 언어

| 텍스트 언어           | 언어 코드 |
|:------------------------|:-------------:|
| 아프리칸스어 | `af` |
| 알바니아어 | `sq` |
| 암하라어 | `am` |
| 아랍어 | `ar` |
| 아르메니아어 | `hy` |
| 아삼어 | `as` |
| 아제르바이잔어 | `az` |
| 벵골어 | `bn` |
| 보스니아어(라틴 문자) | `bs` |
| 불가리아어 | `bg` |
| 광둥어(번체) | `yue` |
| 카탈로니아어 | `ca` |
| 중국어(문어체) | `lzh` |
| 중국어 간체 | `zh-Hans` |
| 중국어 번체 | `zh-Hant` |
| 크로아티아어 | `hr` |
| 체코어 | `cs` |
| 덴마크어 | `da` |
| 다리어 | `prs` |
| 네덜란드어 | `nl` |
| 영어 | `en` |
| 에스토니아어 | `et` |
| 피지어 | `fj` |
| 필리핀어 | `fil` |
| 핀란드어 | `fi` |
| 프랑스어 | `fr` |
| 프랑스어(캐나다) | `fr-ca` |
| 독일어 | `de` |
| 그리스어 | `el` |
| 구자라트어 | `gu` |
| 아이티 크리올 | `ht` |
| 히브리어 | `he` |
| 힌디어 | `hi` |
| 몽 다오어 | `mww` |
| 헝가리어 | `hu` |
| 아이슬란드어 | `is` |
| 인도네시아어 | `id` |
| 이눅티투트어 | `iu` |
| 아일랜드어 | `ga` |
| 이탈리아어 | `it` |
| 일본어 | `ja` |
| 칸나다어 | `kn` |
| 카자흐어 | `kk` |
| 크메르어 | `km` |
| 클링곤어 | `tlh-Latn` |
| 클링곤어(plqaD) | `tlh-Piqd` |
| 한국어 | `ko` |
| 쿠르드어(중부) | `ku` |
| 쿠르드어(북부) | `kmr` |
| 라오어 | `lo` |
| 라트비아어 | `lv` |
| 리투아니아어 | `lt` |
| 마다가스카르어 | `mg` |
| 말레이어 | `ms` |
| 말라얄람어 | `ml` |
| 몰타어 | `mt` |
| 마오리어 | `mi` |
| 마라티어 | `mr` |
| 미얀마 | `my` |
| 네팔어 | `ne` |
| 노르웨이어 | `nb` |
| 오리야어 | `or` |
| 파슈토어 | `ps` |
| 페르시아어 | `fa` |
| 폴란드어 | `pl` |
| 포르투갈어(브라질) | `pt` |
| 포르투갈어(포르투갈) | `pt-pt` |
| 펀잡어 | `pa` |
| 케레타로 오토미어 | `otq` |
| 루마니아어 | `ro` |
| 러시아어 | `ru` |
| 사모아어 | `sm` |
| 세르비아어(키릴 자모) | `sr-Cyrl` |
| 세르비아어(라틴 문자) | `sr-Latn` |
| 슬로바키아어 | `sk` |
| 슬로베니아어 | `sl` |
| 스페인어 | `es` |
| 스와힐리어 | `sw` |
| 스웨덴어 | `sv` |
| 타히티어 | `ty` |
| 타밀어 | `ta` |
| 텔루구어 | `te` |
| 태국어 | `th` |
| 티그리냐어 | `ti` |
| 통가어 | `to` |
| 터키어 | `tr` |
| 우크라이나어 | `uk` |
| 우르두어 | `ur` |
| 베트남어 | `vi` |
| 웨일스어 | `cy` |
| 유카텍 마야어 | `yua` |

## <a name="speaker-recognition"></a>Speaker Recognition

화자 인식은 대부분 언어에 구애받지 않습니다. 여러 언어의 다양한 데이터 원본을 결합하여 텍스트 독립적 화자 인식을 위한 유니버설 모델을 빌드했습니다. 다음 표에 표시되는 언어 및 로케일에 대한 모델을 조정하고 평가했습니다. Speaker Recognition에 대한 자세한 내용은 [개요](speaker-recognition-overview.md)를 참조하세요.

| 언어 | 로캘(BCP-47) | 텍스트 종속 확인 | 텍스트 독립 확인 | 텍스트 독립 식별 |
|----|----|----|----|----|
|영어(미국)  |  `en-US`  |  예  |  예  |  예 |
|중국어(북경어, 간체) | `zh-CN`     |     해당 없음 |     예 |     예|
|영어(오스트레일리아)     | `en-AU`    | 해당 없음     | 예     | 예|
|영어(캐나다)     | `en-CA`     | 해당 없음 |     예 |     예|
|영어(인도)     | `en-IN`     | 해당 없음 |     예 |     예|
|영어(영국)     | `en-GB`     | 해당 없음     | 예     | 예|
|프랑스어(캐나다)     | `fr-CA`     | 해당 없음     | 예 |     예|
|프랑스어(프랑스)     | `fr-FR`     | 해당 없음     | 예     | 예|
|독일어(독일)     | `de-DE`     | 해당 없음     | 예     | 예|
|이탈리아어 | `it-IT`     |     해당 없음     | 예 |     예|
|일본어     | `ja-JP` | 해당 없음     | 예     | 예|
|포르투갈어(브라질) | `pt-BR` |     해당 없음 |     예 |     예|
|스페인어(멕시코)     | `es-MX`     | 해당 없음 |     예 |     예|
|스페인어(스페인)     | `es-ES` | 해당 없음     | 예 |     예|

## <a name="custom-keyword-and-keyword-verification"></a>사용자 지정 키워드 및 키워드 확인

다음 표에서는 사용자 지정 키워드 및 키워드 확인에 지원되는 언어를 간략하게 설명합니다.

| 언어 | 로캘(BCP-47) | 사용자 지정 키워드 | 키워드 확인 |
| -------- | --------------- | -------------- | -------------------- |
| 중국어(북경어, 간체) | zh-CN | 예 | 예 |
| 영어(미국) | ko-KR | 예 | 예 |
| 일본어(일본) | ja-JP | 아니요 | 예 |
| 포르투갈어(브라질) | pt-BR | 아니요 | 예 |

## <a name="next-steps"></a>다음 단계

* [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](./get-started-speech-to-text.md?pivots=programming-language-chsarp)
