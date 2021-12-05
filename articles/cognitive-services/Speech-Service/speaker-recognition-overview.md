---
title: Speaker Recognition 개요 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speaker Recognition은 고유한 음성 특성에 따라 화자를 확인 및 식별하는 알고리즘을 제공합니다. 화자 인식은 "누가 말하고 있나요?"라는 질문에 대답하는 데 사용됩니다. 이 문서에서는 Speaker Recognition 서비스의 이점과 기능을 개략적으로 설명합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: eur
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
keywords: 스피커 인식, 음성 생체 측정
ms.openlocfilehash: 1ba72d444ec7ed0840081fa24b054de108b6569f
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570671"
---
# <a name="what-is-speaker-recognition"></a>Speaker Recognition이란?

Speaker Recognition는 오디오 클립에서 말하는 사용자를 확인 하는 데 도움이 될 수 있습니다. 이 서비스는 음성 biometry을 사용 하 여 고유한 음성 특성에 따라 스피커를 확인 하 고 식별할 수 있습니다. 

단일 화자에 대한 오디오 학습 데이터를 제공하면 해당 화자 음성의 고유한 특성을 기반으로 등록 프로필이 생성됩니다. 그런 다음이 프로필에 대 한 오디오 음성 샘플을 교차 확인 하 여 스피커가 동일한 사람 (스피커 확인) 인지 확인 하거나 등록 된 스피커 프로필 *그룹* 에 대해 오디오 음성 샘플을 교차 확인 하 여 그룹의 프로필 (스피커 식별)과 일치 하는지 확인할 수 있습니다.

> [!IMPORTANT]
> Microsoft에서는 Speaker Recognition에 대한 액세스를 제한합니다. [Azure Cognitive Services Speaker Recognition 제한 된 액세스 검토](https://aka.ms/azure-speaker-recognition)를 통해에 대 한 액세스를 적용할 수 있습니다. 자세한 내용은 [Speaker Recognition에 대 한 제한 된 액세스](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition)를 참조 하세요.

## <a name="speaker-verification"></a>화자 검증

화자 검증은 암호 또는 자유 형식의 음성 입력을 사용하여 등록된 화자의 ID를 확인하는 프로세스를 간소화합니다. 예를 들어 콜 센터 또는 연결 되지 않은 기능 액세스에서 고객 id를 확인 하는 데 사용할 수 있습니다.

### <a name="how-does-speaker-verification-work"></a>화자 검증 작동 방법

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="화자 검증 순서도":::

화자 검증은 텍스트 종속적이거나 텍스트 독립적일 수 있습니다. **텍스트 종속** 검증을 사용하려면 화자가 등록 및 확인 단계에서 사용할 동일한 암호를 선택해야 합니다. **텍스트 독립** 검증을 사용하면 화자가 등록 및 확인 문구를 일상적인 언어로 말할 수 있습니다.

**텍스트 종속** 검증의 경우 사전 정의된 구문 집합의 암호를 말하여 화자의 음성을 등록합니다. 음성 기능은 오디오 녹음에서 추출되어 고유한 음성 서명을 구성하며 선택한 암호도 인식됩니다. 음성 서명과 암호를 함께 사용하여 화자를 확인합니다. 

**텍스트 독립적인** 확인에는 등록을 활성화 하는 초기 활성화 문구 외에 발표자가 등록 중에 표시 되는 항목에 대 한 제한이 없습니다. 확인 하는 오디오 샘플에 대 한 제한은 없습니다. 음성 기능을 추출 하 여 유사성을 평가 하는 것입니다. 

이 API는 오디오가 실제 사람의 오디오인지 등록된 화자의 모방/녹음인지 확인하는 용도로는 사용할 수 없습니다. 

## <a name="speaker-identification"></a>화자 식별

화자 식별은 등록된 화자 그룹 내에서 알 수 없는 화자의 신원을 확인하는 데 사용됩니다. 화자 식별을 사용하면 음성을 개별 화자에게 부여할 수 있으므로 다음과 같이 여러 화자가 있는 시나리오에서 유용합니다.

* 원격 회의 생산성을 위한 지원 솔루션 
* 다중 사용자 디바이스 개인 설정 구축

### <a name="how-does-speaker-identification-work"></a>화자 식별 작동 방법

발표자 식별에 대 한 등록은 **텍스트 독립적** 입니다. 즉, 등록을 활성화 하는 초기 활성화 구와는 발표자가 오디오에 표시 되는 항목에 대 한 제한이 없습니다. Speaker Verification와 마찬가지로, 스피커의 음성이 등록 단계에 기록 되 고, 음성 기능을 추출 하 여 고유한 음성 서명을 형성 합니다. 식별 단계에서는 입력된 음성 샘플이 등록된 음성의 지정된 목록(각 요청마다 최대 50개)과 비교됩니다.

## <a name="data-security-and-privacy"></a>데이터 보안 및 개인 정보

스피커 등록 데이터는 등록용 음성 오디오 및 음성 서명 기능을 포함하여 보안 시스템에 저장됩니다. 등록용 음성 오디오는 알고리즘이 업그레이드된 경우에만 사용되며 특징을 다시 추출해야 합니다. 서비스는 인식 단계 동안 서비스로 전송되는 음성 녹음 또는 추출된 음성 특징을 보관하지 않습니다. 

데이터 보존 기간은 사용자가 제어합니다. API 호출을 통해 개별 화자의 등록 데이터를 생성, 업데이트 및 삭제할 수 있습니다. 구독이 삭제되면 구독과 연결된 모든 화자 등록 데이터도 삭제됩니다. 

모든 Cognitive Services 리소스와 마찬가지로 Speaker Recognition 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. Speaker Recognition을 위해서는 사용자로부터 적절한 권한을 받았는지 확인해야 합니다. 자세한 내용은 [Speaker Recognition에 대 한 데이터 및 개인 정보](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition)에서 확인할 수 있습니다. 자세한 내용은 Microsoft Trust Center의  [Cognitive Services 페이지](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) 를 참조하세요. 

## <a name="common-questions-and-solutions"></a>일반적인 질문 및 솔루션

| 질문 | 해결 방법 |
|---------|----------|
| Speaker Recognition은 어떤 시나리오에 사용할 수 있나요? | 콜 센터 고객 검증, 음성 기반 환자 체크인, 회의 전사, 다중 사용자 디바이스 개인 설정|
| 식별과 검증의 차이점은 무엇인가요? | 식별은 화자 그룹에서 어떤 구성원이 말하고 있는지 감지하는 프로세스입니다. 검증은 화자가 알려진 또는 **등록된** 음성과 일치하는지 확인하는 작업입니다.|
| 텍스트 종속 검증과 텍스트 독립 검증의 차이점은 무엇인가요? | 텍스트 종속 검증에는 등록 및 인식을 위한 특정 암호가 필요합니다. 텍스트 독립적인 확인에는 등록을 위해 특정 활성화 구로 시작 해야 하는 긴 음성 샘플이 필요 하지만 인식 중에는 모든 항목을 사용할 수 있습니다.|
| 어떤 언어가 지원되나요? | 영어, 프랑스어, 스페인어, 중국어, 독일어, 이탈리아어, 일본어 및 포르투갈어 |
| 어떤 Azure 지역이 지원되나요? | Speaker Recognition은 미리 보기 서비스이며 현재 미국 서부 지역에서만 사용할 수 있습니다.|
| 지원되는 오디오 형식은 무엇인가요? | Mono 16 비트, 16Khz PCM-인코드된 WAV |
| **수락** 및 **거부** 응답이 정확하지 않습니다. 임계값을 조정하려면 어떻게 해야 하나요? | 최적 임계값은 시나리오에 따라 달라 지기 때문에 서비스는 기본 임계값 0.5을 기준으로 허용할지 아니면 거부할지를 결정 합니다. 기본 결정을 재정의 하 고 사용자의 시나리오에 따라 결과를 미세 조정 해야 합니다. |
| 한 명의 화자를 여러 번 등록할 수 있나요? | 예, 텍스트 종속 검증의 경우 최대 50번까지 화자를 등록할 수 있습니다. 텍스트 독립 검증 또는 화자 식별의 경우 최대 300초의 오디오를 등록할 수 있습니다. |
| Azure에는 어떤 데이터가 저장되나요? | 등록 오디오는 음성 프로필을 [삭제](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments)할 때까지 서비스에 저장됩니다. 인식 오디오 샘플은 유지되거나 저장되지 않습니다. |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speaker Recognition 빠른 시작](./get-started-speaker-recognition.md) 
