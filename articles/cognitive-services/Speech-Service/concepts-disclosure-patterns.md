---
title: 공개 디자인 패턴
titleSuffix: Azure Cognitive Services
description: 공개에 대한 디자인 패턴 및 모범 사례입니다.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 7a0bf694aca369e5ed774a8bb704973d49683afb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507988"
---
# <a name="disclosure-design-patterns"></a>디자인 패턴 공개
가상 음성 경험에 적합한 [공개 수준](concepts-disclosure-guidelines.md#disclosure-assessment)을 결정했으므로 이제 잠재적 인 디자인 패턴을 탐색할 차례입니다.
## <a name="overview"></a>개요
가상 음성 경험에 적용할 수 있는 다양한 공개 디자인 패턴이 있습니다. 공개 평가의 결과가 '높은 공개'인 경우 가상 음성의 원본을 완전히 전달하는 [**명시적 공개**](#explicit-disclosure)를 권장합니다. [**암시적 공개**](#implicit-disclosure)에는 필요한 공개 수준이 높음 또는 낮음인지에 관계없이 음성 경험에 유용한 신호와 상호 작용 패턴이 포함됩니다.
![다양한 공개 패턴](media/responsible-ai/disclosure-patterns/affordances.png)






| 명시적 공개 패턴                                                                                                                                                                                    | 암시적 공개 패턴                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[투명 소개](#transparent-introduction)<br> [구두 투명 소개](#verbal-transparent-introduction)<br>  [명시적 저자명](#explicit-byline)<br>  [사용자 지정 및 보정](#customization-and-calibration)<br> [부모로서의 공개](#parental-disclosure)<br> [음성이 어떻게 만들어졌는지에 대해 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [기능 공개](#capability-disclosure)<br>[암시적 신호 및 피드백](#implicit-cues--feedback)<br> [대화형 투명도](#conversational-transparency) |



다음 차트를 사용하여 가상 음성에 적용되는 패턴을 직접 참조하세요. 이 차트의 다른 조건 중 일부는 시나리오에도 적용될 수 있습니다.<br/>



| 가상 음성 경험을 구현하는 경우... | 권장 사항 | 디자인 패턴 |
| --- | --- | --- |
| 최대 공개 필요  | 사용자가 연관성을 구축하는 데 도움이 되도록 하나 이상의 명시적 패턴과 암시적 신호를 미리 사용합니다. |[명시적 공개](#explicit-disclosure)<br>[암시적 공개](#implicit-disclosure)  |
| 최소 공개 필요 | 공개는 최소이거나 불필요할 수 있지만 일부 암시적 패턴을 활용할 수 있습니다. | [기능 공개](#capability-disclosure)<br>[대화형 투명도](#conversational-transparency)  |
| 참여도가 높음 | 장기적으로 구축하고 사용자 경험을 따라 공개에 대한 여러 진입점을 제공합니다. 온보딩 경험이 있는 것이 좋습니다. | [투명 소개](#transparent-introduction)<br>[사용자 지정 및 보정](#customization-and-calibration)<br>[기능 공개](#capability-disclosure) |
| 주 대상 그룹으로 어린이 포함 | 부모를 주 공개 대상으로 지정하고 어린이에게 공개를 효과적으로 전달할 수 있도록 합니다.  | [부모로서의 공개](#parental-disclosure)<br>[구두 투명 소개](#verbal-transparent-introduction)<br> [암시적 공개](#implicit-disclosure)<br> [대화형 투명도](#conversational-transparency)  |
| 주 대상 그룹으로 시각 장애인 또는 시력이 약한 사용자 포함  | 모든 사용자를 포함하고 모든 형태의 시각적 공개에 관련 대체 텍스트 또는 음향 효과가 있는지 확인합니다. 명암비와 표시 크기에 대한 접근성 표준을 준수합니다. 청각 신호를 사용하여 공개를 전달합니다.  | [구두 투명 소개](#verbal-transparent-introduction) <br>[청각 신호](#implicit-cues--feedback)<br>[촉각 신호](#implicit-cues--feedback)<br>[대화형 투명도](#conversational-transparency)<br>[접근성 표준](https://www.microsoft.com/accessibility) |
| 화면이 없거나 디바이스가 없거나 음성을 기본 또는 유일한 상호 작용 모드로 사용합니다. | 청각 신호를 사용하여 공개를 전달합니다. | [구두 투명 소개](#verbal-transparent-introduction) <br> [청각 신호](#implicit-cues--feedback)  |
| 잠재적으로 여러 사용자/청취인(예: 여러 가구의 개인 비서)을 포함합니다.  | 다양한 사용자 컨텍스트와 이해 수준을 염두에 두고 사용자 경험에서 여러 번의 공개 기회를 제공합니다.  | [투명 소개(재방문 사용자)](#transparent-introduction)<br> [음성이 어떻게 만들어졌는지에 대해 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [대화형 투명도](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>명시적 공개
가상 음성 경험에 높은 공개가 필요한 경우 다음의 명시적 패턴 중 하나 이상을 사용하여 가상 특성을 명확하게 명시하는 것이 가장 좋습니다.
### <a name="transparent-introduction"></a>투명 소개

음성 경험이 시작되기 전에 음성과 기능의 원본에 대해 완전히 투명해지는 방식으로 디지털 도우미를 소개합니다. 이 패턴을 사용하는 최적의 순간은 새로운 사용자를 온보딩하거나 재방문 사용자에게 새로운 기능을 소개할 때입니다. 소개 중 암시적 신호를 구현하면 사용자가 디지털 에이전트의 가상 특성에 대한 멘탈 모델을 구성하는 데 도움이 됩니다.

#### <a name="first-time-user-experience"></a>첫 번째 사용자 경험

![첫 실행 경험 중 투명 소개](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*새 사용자를 온보딩하는 동안 가상 음성이 소개됩니다.*

권장 사항
- 음성이 인공임을 설명 합니다(예: &quot;디지털&quot;).
- 에이전트가 수행할 수 있는 일을 설명합니다.
- 음성의 원본을 명시적으로 명시합니다.
- 가상 음성에 대해 자세히 알아볼 수 있는 진입점을 제공합니다.

#### <a name="returning-user-experience"></a>재방문 사용자 경험

사용자가 온보딩 경험을 건너뛰는 경우 처음으로 음성을 트리거할 때까지 투명한 소개 경험에 대한 진입점을 계속 제공합니다.
<br/>

![재방문 사용자 경험 중 투명 소개](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*가상 음성 경험에 대한 일관된 진입점을 제공합니다. 사용자 경험의 어느 시점에서나 음성을 처음 트리거할 때 사용자가 온보딩 경험으로 돌아갈 수 있도록 합니다.*


### <a name="verbal-transparent-introduction"></a>구두 투명 소개

디지털 도우미 음성의 원본을 명시하는 음성 프롬프트는 그 자체로 공개를 달성하기에 충분할 만큼 명시적입니다. 이 패턴은 음성이 사용 가능한 유일한 상호 작용 모드인 높은 공개 시나리오에 가장 적합합니다.
<br/>

![구두로 투명 소개](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*사용자 경험에서 이미 사람의 목소리를 소개하거나 특성을 지정할 수 있는 순간이 있을 때 투명 소개를 사용합니다.*


![첫 번째 사용자의 구두로 음성 투명 소개](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*투명성을 강화하기 위해 음성 작업자는 첫 번째 사용자의 가상 음성 원본을 공개할 수 있습니다.*

### <a name="explicit-byline"></a>명시적 저자명

사용자가 음성을 트리거하기 위해 오디오 플레이어 또는 대화형 구성 요소와 상호 작용하는 경우 이 패턴을 사용합니다.


![뉴스 미디어 시나리오의 명시적 저자명](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*명시적 저자명은 음성이 어디에서 왔는지에 대한 특성입니다.*

권장 사항

- 가상 음성에 대해 자세히 알아보기 위한 진입점 제공

### <a name="customization-and-calibration"></a>사용자 지정 및 보정

디지털 도우미의 응답 방식(예: 음성이 들리는 방식)을 제어하는 사용자 정의 컨트롤을 제공합니다.  사용자가 특정 목표를 염두에 두고 자신의 용어로 시스템과 상호 작용하면 정의에 따라 시스템은 이것이 실제 사람이 아님을 이미 파악한 것입니다.

#### <a name="user-control"></a>사용자 정의 컨트롤

가상 음성 경험에 의미 있고 눈에 띄는 영향을 주는 선택 사항을 제공합니다.

![사용자 기본 설정](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*사용자 기본 설정을 통해 사용자는 경험을 사용자 지정하고 개선할 수 있습니다.*

권장 사항

- 사용자가 음성(예: 언어 및 음성 유형 선택)을 사용자 지정할 수 있습니다.
- 사용자에게 자신의 고유한 음성(예: 음성 보정, 사용자 지정 명령)에 응답하도록 시스템을 가르치는 방법을 제공합니다.
- 사용자 생성 또는 상황별 상호 작용을 위한 최적화(예: 미리 알림)

#### <a name="persona-customization"></a>가상 사용자의 사용자 지정

디지털 도우미의 음성을 사용자 지정하는 방법을 제공합니다. 유명인이나 널리 알려진 사람을 기반으로 한 음성인 경우 사용자가 음성을 미리 볼 때 시각적 소개와 음성 소개를 모두 사용하는 것이 좋습니다.

![음성 사용자 지정](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*음성 세트에서 선택하는 기능을 제공하면 인공 특성을 전달하는 데 도움이 됩니다.*

권장 사항
- 사용자가 각 음성의 소리를 미리 볼 수 있도록 허용
- 각 음성에 대해 인증 소개 사용
- 가상 음성에 대해 자세히 알아보기 위한 진입점 제공

### <a name="parental-disclosure"></a>부모로서의 공개

COPPA 규정 준수 외에도 주 대상 그룹이 어린이고 노출 수준이 높은 경우 부모에게 공개합니다. 민감한 사용의 경우 성인이 가상 음성 사용을 인정할 때까지 경험을 제어하는 것이 좋습니다. 부모가 자녀에게 메시지를 알리도록 합니다.

![부모를 위한 공개](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*부모에게 최적화된 투명 소개는 어린이가 음성과 상호 작용하기 전에 성인이 음성의 가상 특성을 인식하도록 합니다.*

권장 사항

- 공개할 주 대상 그룹으로 부모를 지정합니다.
- 부모가 자녀에게 공개를 알리도록 합니다.
- 가상 음성에 대해 자세히 알아보기 위한 진입점 제공
- 부모에게 공개 내용을 읽었음을 보여주는 간단한 &quot;보호&quot; 질문을 요청하여 경험을 제어합니다.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>음성이 어떻게 만들어졌는지에 대해 자세히 알아볼 수 있는 기회 제공

가상 음성 기술에 대한 자세한 정보를 제공하는 페이지, 팝업 또는 외부 사이트에 대한 상황에 맞는 진입점을 제공합니다. 예를 들어 온보딩 중 또는 사용자가 대화 중에 추가 정보를 요구할 때 자세히 알아볼 수 있는 링크를 표시할 수 있습니다.

![자세히 알아보기 위한 진입점](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*가상 음성에 대해 자세히 알아보는 기회를 제공하기 위한 진입점의 예입니다.*

사용자가 가상 음성에 대한 자세한 정보를 요청하면 기본 목표는 가상 음성의 원본에 대해 교육하고 기술에 대해 투명한 것입니다.

![사용자에게 가상 음성에 대한 추가 정보 제공](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*추가 정보는 외부 사이트 도움말 사이트에서 제공할 수 있습니다.*

권장 사항

- 복잡한 개념을 단순화하고 법률 및 기술 용어 사용 방지
- 개인 정보 및 사용 약관 문에 이 콘텐츠 포함 안 함
- 콘텐츠를 간결하게 유지하고 가능한 경우 이미지 사용

## <a name="implicit-disclosure"></a>암시적 공개

일관성은 사용자 경험 전반에 걸쳐 암시적으로 공개를 달성하기 위한 핵심입니다. 디바이스와 상호 작용 모드에서 시각 및 청각 신호를 일관되게 사용하면 암시적 패턴과 명시적 공개 간의 연관성을 구축하는 데 도움이 될 수 있습니다.

![암시적 신호의 일관성](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>암시적 신호 및 피드백

의인화는 에이전트의 실제 시각적 표현에서 음성, 소리, 빛의 패턴, 튀는 모양, 심지어 디바이스의 진동에 이르는 다양한 방식으로 나타날 수 있습니다. 가상 사용자를 정의할 때 매우 사람과 같은 아바타를 목표로 하기보다는 암시적 신호와 피드백 패턴을 활용합니다. 이는 보다 명시적인 공개의 필요성을 최소화하는 한 가지 방법입니다.

![시각적 신호 및 피드백](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*이러한 단서는 너무 사람 같지 않게 에이전트를 의인화하는 데 도움이 됩니다. 또한 시간이 지남에 따라 일관되게 사용되면 자체적으로 효과적인 공개 메커니즘이 될 수 있습니다.*

다음과 같은 유형의 신호를 통합할 때 사용자 경험의 다양한 상호 작용 모드를 고려합니다.

| 시각 신호                                                                                                                                                               | 청각 신호                                                      | 촉각 신호 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  아바타 <br>반응형 실시간 신호(예: 애니메이션)<br> 화면이 아닌 신호(예: 디바이스의 조명 및 패턴)<br>  | Sonicon(예: 짧고 독특한 소리, 일련의 음표) | Vibration   |

### <a name="capability-disclosure"></a>기능 공개

디지털 도우미가 무엇을 할 수 있는지에 대한 정확한 기대치를 설정하여 암시적으로 공개를 구현할 수 있습니다. 사용자가 디지털 도우미와 상호 작용하는 방법을 배우고 경험의 초기 단계에서 가상 음성에 대해 자세히 알아볼 수 있는 상황별 도움말을 제공할 수 있도록 샘플 명령을 제공합니다.

![사용자가 만들 수 있는 대화에 대한 기본 응답의 예입니다.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>대화형 투명도

대화가 예상치 못한 경로에 빠지면 기대치를 재설정하고 투명성을 강화하고 사용자를 성공적인 경로로 유도하는 데 도움이 되는 기본 응답을 만드는 것을 고려하십시오. 대화에서 명시적 공개를 사용할 기회도 있습니다.

![예기치 않은 경로 처리](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
에이전트에게 보내는 업무 외 또는 &quot;개인적&quot; 질문은 사용자에게 에이전트의 가상 특성을 상기시키고 적절하게 참여하거나 실제 사람에게 리디렉션하도록 유도하기에 좋은 기회입니다.

![업무 외 질문 처리](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>공개 시기

사용자 경험 전반에 걸쳐 공개 기회가 많습니다. 첫 번째 사용, 두 번째 사용, n번째 사용을 위해 디자인할 뿐 아니라 시스템에서 실수를 하거나 사용자가 에이전트 기능의 한계를 발견할 때와 같이 투명성을 강조하지 &quot;못하는&quot; 순간도 포착합니다.

![사용자 경험 전반의 공개 기회](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

다양한 공개 기회를 강조 표시하는 표준 디지털 도우미 사용자 경험의 예입니다.

### <a name="up-front"></a>선행

공개를 위한 최적의 순간은 사람이 가상 음성과 처음으로 상호 작용할 때입니다.개인 음성 도우미 시나리오에서 이는 온보딩 중 또는 사용자가 처음으로 경험을 언박싱할 때입니다. 다른 시나리오에서는 가상 음성이 웹 사이트의 콘텐츠를 처음으로 읽을 때나 사용자가 가상 캐릭터와 처음으로 상호 작용할 때일 수 있습니다.

- [투명 소개](#transparent-introduction)
- [기능 공개](#capability-disclosure)
- [사용자 지정 및 보정](#customization-and-calibration)
- [암시적 신호](#implicit-cues--feedback)

### <a name="upon-request"></a>요청 시

사용자는 요청 시 사용자 경험 중 언제든지 추가 정보에 쉽게 액세스하고 기본 설정을 제어하고 투명한 통신을 수신할 수 있어야 합니다.

- [음성이 어떻게 만들어졌는지에 대해 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [사용자 지정 및 보정](#customization-and-calibration)
- [대화형 투명도](#conversational-transparency)

### <a name="continuously"></a>계속

사용자 경험을 계속 향상시키는 암시적 디자인 패턴을 사용합니다.

- [기능 공개](#capability-disclosure)
- [암시적 신호](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>시스템에서 실패할 경우

공개를 실패의 기회로 사용합니다.

- [대화형 투명도](#conversational-transparency)
- [음성이 어떻게 만들어졌는지에 대해 자세히 알아볼 수 있는 기회 제공](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [사람에게 핸드오프](#conversational-transparency)

## <a name="additional-resources"></a>추가 리소스
- [Microsoft 봇 지침](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 디자인 지침](/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 음성 디자인 지침](/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality 음성 명령 지침](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>참고 항목

* [성우에 대한 공개](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [가상 음성 기술에 대한 책임 있는 배포 지침](concepts-guidelines-responsible-deployment-synthetic.md)
* [공개 방법](concepts-disclosure-guidelines.md)
