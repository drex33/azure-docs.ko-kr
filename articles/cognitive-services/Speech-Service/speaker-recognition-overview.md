---
title: Speaker Recognition 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speaker Recognition은 고유한 음성 특성에 따라 화자를 확인 및 식별하는 알고리즘을 제공합니다. 화자 인식은 "누가 말하고 있나요?"라는 질문에 대답하는 데 사용됩니다. 이 문서에서는 Speaker Recognition 서비스의 이점과 기능을 개략적으로 설명합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 스피커 인식, 음성 생체 측정
ms.openlocfilehash: c3f71ab3adc93546b0cd748968a0099743455be8
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123536998"
---
# <a name="what-is-speaker-recognition-preview"></a>Speaker Recognition(미리 보기)이란?

Speaker Recognition 서비스는 고유한 음성 특성에 따라 화자를 확인 및 식별하는 알고리즘을 제공합니다. 화자 인식은 "누가 말하고 있나요?"라는 질문에 대답하는 데 사용됩니다. 단일 화자에 대한 오디오 학습 데이터를 제공하면 해당 화자 음성의 고유한 특성을 기반으로 등록 프로필이 생성됩니다. 그런 다음 이 프로필과 오디오 음성 샘플을 비교 확인하여 화자가 동일한 사람인지 확인하거나(화자 검증) 등록된 화자 프로필 *그룹* 과 오디오 음성 샘플을 비교 확인하여 그룹의 어떤 프로필(화자 식별)과 일치하는지 확인합니다. 반대로 [화자 분할](batch-transcription.md#speaker-separation-diarization)은 일괄 처리 작업에서 화자별로 오디오 세그먼트를 그룹화합니다.

## <a name="speaker-verification"></a>화자 검증

화자 검증은 암호 또는 자유 형식의 음성 입력을 사용하여 등록된 화자의 ID를 확인하는 프로세스를 간소화합니다. 콜 센터의 고객 ID 확인부터 비접촉 시설 액세스에 이르기까지 다양한 솔루션에서 안전하고 마찰 없는 고객 참여를 위해 개인을 확인하는 데 사용할 수 있습니다.

### <a name="how-does-speaker-verification-work"></a>화자 검증 작동 방법

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="화자 검증 순서도":::

화자 검증은 텍스트 종속적이거나 텍스트 독립적일 수 있습니다. **텍스트 종속** 검증을 사용하려면 화자가 등록 및 확인 단계에서 사용할 동일한 암호를 선택해야 합니다. **텍스트 독립** 검증을 사용하면 화자가 등록 및 확인 문구를 일상적인 언어로 말할 수 있습니다.

**텍스트 종속** 검증의 경우 사전 정의된 구문 집합의 암호를 말하여 화자의 음성을 등록합니다. 음성 기능은 오디오 녹음에서 추출되어 고유한 음성 서명을 구성하며 선택한 암호도 인식됩니다. 음성 서명과 암호를 함께 사용하여 화자를 확인합니다. 

**텍스트 독립** 검증은 유사성을 평가하기 위한 용도로만 음성 특징을 추출하므로 등록 중에 화자가 말하는 내용이나 검증할 오디오 샘플에 제한이 없습니다. 

이 API는 오디오가 실제 사람의 오디오인지 등록된 화자의 모방/녹음인지 확인하는 용도로는 사용할 수 없습니다. 

## <a name="speaker-identification"></a>화자 식별

화자 식별은 등록된 화자 그룹 내에서 알 수 없는 화자의 신원을 확인하는 데 사용됩니다. 화자 식별을 사용하면 음성을 개별 화자에게 부여할 수 있으므로 다음과 같이 여러 화자가 있는 시나리오에서 유용합니다.

* 원격 회의 생산성을 위한 지원 솔루션 
* 다중 사용자 디바이스 개인 설정 구축

### <a name="how-does-speaker-identification-work"></a>화자 식별 작동 방법

화자 식별 등록은 **텍스트와 관련이 없으므로** 화자가 오디오에서 말하는 내용에 대한 제한이 없습니다. 화자 검증과 마찬가지로 등록 단계에서 화자의 음성이 녹음되고 음성 특징이 추출되어 고유한 음성 서명을 구성합니다. 식별 단계에서는 입력된 음성 샘플이 등록된 음성의 지정된 목록(각 요청마다 최대 50개)과 비교됩니다.

## <a name="data-security-and-privacy"></a>데이터 보안 및 개인 정보

스피커 등록 데이터는 등록용 음성 오디오 및 음성 서명 기능을 포함하여 보안 시스템에 저장됩니다. 등록용 음성 오디오는 알고리즘이 업그레이드된 경우에만 사용되며 특징을 다시 추출해야 합니다. 서비스는 인식 단계 동안 서비스로 전송되는 음성 녹음 또는 추출된 음성 특징을 보관하지 않습니다. 

데이터 보존 기간은 사용자가 제어합니다. API 호출을 통해 개별 화자의 등록 데이터를 생성, 업데이트 및 삭제할 수 있습니다. 구독이 삭제되면 구독과 연결된 모든 화자 등록 데이터도 삭제됩니다. 

모든 Cognitive Services 리소스와 마찬가지로 Speaker Recognition 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. Speaker Recognition을 위해서는 사용자로부터 적절한 권한을 받았는지 확인해야 합니다. 자세한 내용은 Microsoft Trust Center의  [Cognitive Services 페이지](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) 를 참조하세요. 

## <a name="common-questions-and-solutions"></a>일반적인 질문 및 솔루션

| 질문 | 해결 방법 |
|---------|----------|
| Speaker Recognition은 어떤 시나리오에 사용할 수 있나요? | 콜 센터 고객 검증, 음성 기반 환자 체크인, 회의 전사, 다중 사용자 디바이스 개인 설정|
| 식별과 검증의 차이점은 무엇인가요? | 식별은 화자 그룹에서 어떤 구성원이 말하고 있는지 감지하는 프로세스입니다. 검증은 화자가 알려진 또는 **등록된** 음성과 일치하는지 확인하는 작업입니다.|
| 텍스트 종속 검증과 텍스트 독립 검증의 차이점은 무엇인가요? | 텍스트 종속 검증에는 등록 및 인식을 위한 특정 암호가 필요합니다. 텍스트 독립 검증에는 등록을 위해 더 긴 음성 샘플이 필요하지만 인식하는 동안을 포함하여 어떤 말이든 할 수 있습니다.|
| 어떤 언어가 지원되나요? | 영어, 프랑스어, 스페인어, 중국어, 독일어, 이탈리아어, 일본어 및 포르투갈어 |
| 어떤 Azure 지역이 지원되나요? | Speaker Recognition은 미리 보기 서비스이며 현재 미국 서부 지역에서만 사용할 수 있습니다.|
| 지원되는 오디오 형식은 무엇인가요? | Mono 16비트, 16kHz PCM으로 인코딩된 WAV |
| **수락** 및 **거부** 응답이 정확하지 않습니다. 임계값을 조정하려면 어떻게 해야 하나요? | 최적의 임계값은 시나리오에 따라 크게 다르기 때문에 API는 기본 임계값인 0.5를 기반으로 "수락" 또는 "거부" 여부를 결정합니다. 고급 사용자는 기본 결정을 재정의하고 자신의 시나리오에 따라 결과를 미세 조정하는 것이 좋습니다. |
| 한 명의 화자를 여러 번 등록할 수 있나요? | 예, 텍스트 종속 검증의 경우 최대 50번까지 화자를 등록할 수 있습니다. 텍스트 독립 검증 또는 화자 식별의 경우 최대 300초의 오디오를 등록할 수 있습니다. |
| Azure에는 어떤 데이터가 저장되나요? | 등록 오디오는 음성 프로필을 [삭제](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments)할 때까지 서비스에 저장됩니다. 인식 오디오 샘플은 유지되거나 저장되지 않습니다. |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> * 애플리케이션에서 사용할 수 있는 일반적인 디자인 패턴을 살펴보려면 Speaker Recognition [기본 문서](./get-started-speaker-recognition.md)를 완료하세요.
> * 텍스트 독립 화자 검증에 대해서는 [동영상 자습서](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/)를 참조하세요.