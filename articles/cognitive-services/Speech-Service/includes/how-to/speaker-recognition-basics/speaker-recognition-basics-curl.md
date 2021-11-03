---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 11e93561a0f78ad1fb62d1cab1f3e8d8f3f3b293
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069158"
---
이 빠른 시작에서는 다음을 포함하여 Speech SDK를 사용하는 Speaker Recognition에 대한 기본적인 디자인 패턴을 알아봅니다.

* 텍스트 종속 및 텍스트 독립 확인
* 음성 그룹 간의 음성 샘플을 식별하는 화자 식별
* 음성 프로필 삭제

Speaker Recognition 개념에 대한 간략한 설명은 [개요](../../../speaker-recognition-overview.md) 문서를 참조하세요. 지원되는 플랫폼 목록은 왼쪽 탐색의 참조 노드를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Speaker Recognition는 현재 `westus` 지역에서 만든 Azure Speech 리소스에서만 지원되는 *유일한 것* 입니다.

## <a name="text-dependent-verification"></a>텍스트-종속 확인

Speaker Verification는 화자가 알려진 또는 **등록된** 음성으로 일치하는지 확인하는 행위입니다. 첫 번째 단계는 음성 프로필을 **등록** 하는 것입니다. 따라서 서비스에 향후 음성 샘플과 비교할 내용이 있습니다. 다음 예제에서는 **텍스트 종속** 전략을 사용하여 프로필을 등록합니다. 이 전략에서는 특정 암호를 등록 및 검증 모두에 사용해야 합니다. 지원되는 암호 목록은 [참조 문서](/rest/api/speakerrecognition/)를 확인하세요.

먼저 [음성 프로필을 만듭니다](/rest/api/speakerrecognition/verification/textdependent/createprofile). Speech Service 구독 키 및 지역을 이 문서의 각 curl 명령에 삽입해야 합니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

음성 프로필에는 세 가지 유형이 있습니다.

- 텍스트 종속 확인
- 텍스트 독립 확인
- 텍스트 독립 식별

이 경우 텍스트 종속 검증 음성 프로필을 만듭니다. 다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

다음으로 [음성 프로필을 등록합니다](/rest/api/speakerrecognition/verification/textdependent/createenrollment). `--data-binary` 매개 변수 값에 대해 "내 목소리가 내 여권입니다. 나를 확인해 보세요."와 같이 지원되는 암호 중 하나가 포함된 컴퓨터의 오디오 파일을 지정합니다. 이러한 오디오 파일은 [Windows 음성 녹음기](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab)와 같은 앱을 사용하여 녹음하거나 [텍스트 음성 변환](../../../index-text-to-speech.yml)을 사용하여 생성할 수 있습니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

이 응답은 둘 이상의 오디오 샘플을 등록해야 한다고 알려줍니다.

총 세 개의 오디오 샘플이 등록되면 다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

이제 [음성 프로필과 비교하여 오디오 샘플을 검증](/rest/api/speakerrecognition/verification/textdependent/verifyprofile)할 준비가 되었습니다. 이 오디오 샘플은 음성 프로필을 등록하는 데 사용한 샘플과 동일한 암호를 포함해야 합니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept`는 암호가 일치하고 검증에 성공했음을 의미합니다. 또한 응답에는 0.0~1.0 범위의 유사성 점수도 포함되어 있습니다.

완료하려면 [음성 프로필을 삭제합니다](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

응답이 없습니다.

## <a name="text-independent-verification"></a>텍스트 독립 확인

**텍스트-종속** 확인을 하는 것과 달리 **텍스트-독립** 을 확인합니다:

* 특정 암호를 말할 필요가 없고, 모든 항목을 말할 수 있습니다.
* 세 개의 오디오 샘플이 필요하지 않지만, 총 20초의 오디오가 *필요합니다*.

먼저 [텍스트 독립 검증 프로필을 만듭니다](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

다음으로 [음성 프로필을 등록합니다](/rest/api/speakerrecognition/verification/textindependent/createenrollment). 다시 말하지만, 세 개의 오디오 샘플을 제출하는 대신 총 20초의 오디오가 포함된 오디오 샘플을 제출해야 합니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

충분한 오디오 샘플이 제출되면 다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

이제 [음성 프로필과 비교하여 오디오 샘플을 검증](/rest/api/speakerrecognition/verification/textindependent/verifyprofile)할 준비가 되었습니다. 다시 말하지만, 이 오디오 샘플은 암호를 포함할 필요가 없습니다. 총 4초 이상의 오디오가 포함되는 경우 어떤 음성도 포함할 수 있습니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept`는 검증에 성공했음을 의미합니다. 또한 응답에는 0.0~1.0 범위의 유사성 점수도 포함되어 있습니다.

완료하려면 [음성 프로필을 삭제합니다](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

응답이 없습니다.

## <a name="speaker-identification"></a>화자 식별

화자 식별은 지정 등록된 음성 그룹에서 **누가 말하고 있는지** 확인하는 데 사용됩니다. 이 프로세스는 **텍스트 독립 검증** 과 비슷합니다. 주요 차이점은 단일 프로필과 비교하여 검증하는 것이 아니라 한 번에 여러 음성 프로필과 비교하여 검증할 수 있다는 것입니다.

먼저 [텍스트 독립 식별 프로필을 만듭니다](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

다음으로 [음성 프로필을 등록합니다](/rest/api/speakerrecognition/identification/textindependent/createenrollment). 다시 말하지만, 총 20초의 오디오가 포함된 오디오 샘플을 제출해야 합니다. 이러한 샘플은 암호를 포함할 필요가 없습니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

충분한 오디오 샘플이 제출되면 다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

이제 [음성 프로필을 사용하여 오디오 샘플을 식별](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker)할 준비가 되었습니다. 식별 명령은 가능한 음성 프로필 ID의 쉼표로 구분된 목록을 허용합니다. 이 경우 이전에 만든 음성 프로필의 ID만 전달하면 됩니다. 그러나 원하는 경우 각 음성 프로필이 다른 음성의 오디오 샘플에 등록된 여러 음성 프로필 ID를 전달할 수 있습니다.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

다음과 같은 응답이 표시됩니다.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

응답에는 제출한 오디오 샘플과 가장 비슷하게 일치하는 음성 프로필의 ID가 포함되어 있습니다. 또한 유사성 순서에 따라 우선 순위가 지정된 음성 프로필 목록도 포함되어 있습니다.

완료하려면 [음성 프로필을 삭제합니다](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

응답이 없습니다.
