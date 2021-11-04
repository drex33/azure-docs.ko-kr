---
title: 휴먼 레이블 대화 기록 지침 - Speech Service
titleSuffix: Azure Cognitive Services
description: 단어를 삭제하거나 잘못 대체하는 경우와 같이 음성 인식 정확도를 향상시키기 위해 오디오 데이터와 함께 휴먼 레이블 대화 기록을 사용할 수 있습니다. 휴먼 레이블 대화 기록은 오디오 파일의 단어별, 약어 대화 내용 기록입니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: eur
ms.openlocfilehash: 35cb9d2ea9f370043639f1c80b2cf901c6bcc510
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508272"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>휴먼 레이블 대화 기록을 만드는 방법

단어를 삭제하거나 잘못 대체했을 때 발생하는 문제, 특히 인식 정확도를 향상시키려는 경우 오디오 데이터와 함께 휴먼 레이블 대화 기록을 사용하는 것이 좋습니다. 휴먼 레이블 대화 기록이란? 이는 간단하며, 오디오 파일의 단어별, 약어 대화 내용 기록입니다.

인식 기능을 향상시키기 위해 많은 대화 내용 기록 데이터 샘플이 필요하며, 1~20시간의 대화 내용 기록 데이터를 제공하는 것이 좋습니다. Speech Service는 교육을 위해 최대 20시간의 오디오를 사용합니다. 이 페이지에서는 고품질의 대화 내용 기록을 만들 수 있도록 설계된 지침을 검토합니다. 이 가이드는 미국 영어, 북경어 중국어 및 독일어 섹션을 사용하여 로캘로 구분됩니다.

> [!NOTE]
> 모든 기본 모델이 오디오 파일의 사용자 지정을 지원하지는 않습니다. 기본 모델이 지원하지 않는 경우 학습은 관련 텍스트가 사용되는 것과 동일한 방식으로 대화 내용 기록의 텍스트를 사용합니다. [언어 지원](language-support.md#speech-to-text)을 참조하여 오디오 데이터를 통한 학습을 지원하는 기본 모델의 목록을 확인하세요.

> [!NOTE]
> 학습에 사용되는 기본 모델을 변경하고 학습 데이터 세트에 오디오가 있는 경우 선택한 새 기본 모델이 [오디오 데이터를 통한 학습을 지원](language-support.md#speech-to-text)하는지 *항상* 확인합니다. 이전에 사용된 기본 모델에서 오디오 데이터를 통한 학습을 지원하지 않고 학습 데이터 세트에 오디오가 포함된 경우 새 기본 모델의 학습 시간이 몇 시간에서 며칠 이상으로 **크게** 증가할 수 있습니다. Speech Service 구독이 학습 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에 있지 **않은** 경우에 특히 그렇습니다.
>
> 위의 단락에 설명된 문제가 발생한 경우 데이터 세트의 오디오 양을 줄이거나 완전히 제거하여 텍스트만 남겨두면 학습 시간을 빠르게 줄일 수 있습니다. 두 번째 옵션은 Speech Service 구독이 학습용 [전용 하드웨어가 있는 지역](custom-speech-overview.md#set-up-your-azure-account)에 있지 **않은** 경우에 매우 권장됩니다.

## <a name="us-english-en-us"></a>미국 영어(en-US)

영어 오디오의 휴먼 레이블 대화 기록은 ASCII 문자만 사용하여 일반 텍스트로 제공되어야 합니다. Latin-1 또는 유니코드 문장 부호를 사용하지 않도록 합니다. 이러한 문자는 워드 프로세싱 애플리케이션에서 텍스트를 복사하거나 웹 페이지에서 데이터를 스크랩할 때 실수로 추가되는 경우가 많습니다. 이러한 문자가 있으면 적절한 ASCII 대체를 사용하여 업데이트해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | 대체 | 참고 |
| ------------------- | ------------ | ----- |
| “Hello world” | "Hello world" | 여는 따옴표와 닫는 따옴표를 적절한 ASCII 문자로 대체했습니다. |
| John’s day | John's day | 아포스트로피를 적절한 ASCII 문자로 대체했습니다. |
| it was good—no, it was great! | it was good--no, it was great! | em 대시는 두 개의 하이픈으로 대체되었습니다. |

### <a name="text-normalization-for-us-english"></a>미국 영어에 대한 텍스트 정규화

텍스트 정규화는 모델을 학습하는 데 사용되는 일관된 형식으로 단어를 변환하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용되지만 휴먼 레이블 대화 기록 데이터를 준비할 때 이러한 지침을 사용하는 것이 좋습니다.

- 약어를 단어로 작성합니다.
- 표준 이외의 숫자 문자열을 단어로 작성합니다(예: 회계 용어).
- 영문자가 아닌 문자 또는 혼합 영숫자 문자는 발음되는 대로 전사해야 합니다.
- 단어로 발음된 약어는 편집할 수 없습니다(예: "레이더", "레이저", "RAM" 또는 "NATO").
- 각 문자가 공백으로 구분되는 별도의 문자로 발음되는 약어를 작성합니다.
- 오디오를 사용하는 경우 오디오와 일치하는 단어(예: "101"을 "one oh one" 또는 "one hundred and one"으로 발음할 수 있음)로 숫자를 기록합니다.
- 반복되는 문자, 단어 또는 단어 그룹을 세 번보다 많이(예: "yeah yeah yeah yeah") 반복하지 않습니다. 이러한 반복이 있는 줄은 Speech Service에 의해 삭제될 수 있습니다.

다음은 대화 내용 기록에서 수행해야 하는 정규화의 몇 가지 예제입니다.

| 원래 텍스트               | 정규화 후 텍스트              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+         | My blood type is O positive           |
| Water is H20                | Water is H 2 O                        |
| Play OU812 by Van Halen     | Play O U 8 1 2 by Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

다음 정규화 규칙은 자동으로 대화 내용 기록에 적용됩니다.

- 소문자를 사용합니다.
- 단어 내에서 아포스트로피를 제외한 모든 문장 부호를 제거합니다.
- 숫자를 달러 양과 같은 단어/발성 형식으로 확장합니다.

다음은 대화 내용 기록에 대해 자동으로 수행되는 정규화의 몇 가지 예제입니다.

| 원래 텍스트                          | 정규화 후 텍스트          |
| -------------------------------------- | --------------------------------- |
| “Holy cow!” said Batman.               | holy cow said batman              |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| It costs \$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>북경어 중국어(zh-CN)

북경어 중국어 오디오의 휴먼 레이블 대화 기록은 바이트 순서 표식을 사용하여 UTF-8로 인코딩해야 합니다. 반자 문장 부호 문자를 사용하지 않아야 합니다. 이러한 문자는 워드 프로세싱 프로그램에서 데이터를 준비하거나 웹 페이지에서 데이터를 스크랩할 때 실수로 포함될 수 있습니다. 이러한 문자가 있으면 적절한 전자 대체를 사용하여 업데이트해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | 대체   | 참고 |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | 여는 따옴표와 닫는 따옴표를 적절한 문자로 대체했습니다. |
| 需要什么帮助? | 需要什么帮助？| 물음표를 적절한 문자로 대체했습니다. |

### <a name="text-normalization-for-mandarin-chinese"></a>북경어 중국어에 대한 텍스트 정규화

텍스트 정규화는 모델을 학습하는 데 사용되는 일관된 형식으로 단어를 변환하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용되지만 휴먼 레이블 대화 기록 데이터를 준비할 때 이러한 지침을 사용하는 것이 좋습니다.

- 약어를 단어로 작성합니다.
- 숫자 문자열은 음성 형식으로 기록합니다.

다음은 대화 내용 기록에서 수행해야 하는 정규화의 몇 가지 예제입니다.

| 원래 텍스트 | 정규화 후 텍스트 |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

다음 정규화 규칙은 자동으로 대화 내용 기록에 적용됩니다.

- 모든 문장 부호 제거
- 숫자를 발성 형식으로 확장
- 전자 문자를 반자 문자로 변환
- 모든 영어 단어에 대문자 사용

다음은 대화 내용 기록에 대해 자동으로 수행되는 정규화의 몇 가지 예제입니다.

| 원래 텍스트 | 정규화 후 텍스트 |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ￥ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>독일어(de-DE) 및 기타 언어

독일 오디오(및 기타 비영어 또는 북경어 중국어)에 대한 휴먼 레이블 대화 기록은 바이트 순서 표식을 사용하여 UTF-8로 인코딩해야 합니다. 각 오디오 파일에 대해 하나의 휴먼 레이블 대화 기록을 제공해야 합니다.

### <a name="text-normalization-for-german"></a>독일어에 대한 텍스트 정규화

텍스트 정규화는 모델을 학습하는 데 사용되는 일관된 형식으로 단어를 변환하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용되지만 휴먼 레이블 대화 기록 데이터를 준비할 때 이러한 지침을 사용하는 것이 좋습니다.

- "."가 아닌 ","로 소수점을 작성합니다.
- "."가 아닌 ":"로 시간 구분 기호를 작성합니다(예: 12:00 Uhr).
- "ca."와 같은 약어는 대체되지 않습니다. 전체 발성 형식을 사용하는 것이 좋습니다.
- 네 개의 기본 수학 연산자(+, -, \* 및 /)를 제거합니다. "plus", "minus", "mal" 및 "geteilt"와 같은 작성 형식으로 바꾸는 것이 좋습니다.
- 비교 연산자가 제거됩니다(=, < 및 >). "gleich", "kleiner als" 및 "grösser als"로 바꾸는 것이 좋습니다.
- 3/4과 같은 분수는 작성 형식(예: 3/4 대신 "drei viertel")으로 작성합니다.
- "€" 기호를 작성 형식 "유로"로 바꿉니다.

다음은 대화 내용 기록에서 수행해야 하는 정규화의 몇 가지 예제입니다.

| 원래 텍스트    | 사용자 정규화 후 텍스트 | 시스템 정규화 후 텍스트       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

다음 정규화 규칙은 자동으로 대화 내용 기록에 적용됩니다.

- 모든 텍스트에 소문자를 사용합니다.
- 다양한 유형의 따옴표를 포함하여 모든 문장 부호를 제거합니다("테스트", '테스트', "테스트„ 및 «테스트»는 허용됨).
- 이 집합 ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬의 특수 문자를 포함하는 행을 버립니다.
- 달러 또는 유로 금액을 포함하여 숫자를 발성 형식으로 확장합니다.
- a, o 및 u에 대해서만 움라우트를 허용합니다. 다른 항목은 "th"로 대체되거나 삭제됩니다.

다음은 대화 내용 기록에 대해 자동으로 수행되는 정규화의 몇 가지 예제입니다.

| 원래 텍스트    | 정규화 후 텍스트 |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

### <a name="text-normalization-for-japanese"></a>일본어에 대한 텍스트 정규화

일본어(ja-JP)에서 각 문장의 최대 길이는 90자입니다. 문장이 긴 줄은 무시됩니다. 긴 텍스트를 추가하려면 사이에 마침표를 삽입합니다.

## <a name="next-steps"></a>다음 단계

- [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)
- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
- [모델 배포](./how-to-custom-speech-train-model.md)
