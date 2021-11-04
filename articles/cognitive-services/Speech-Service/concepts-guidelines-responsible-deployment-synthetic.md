---
title: 가상 음성 기술에 대한 책임 있는 배포 지침
titleSuffix: Azure Cognitive Services
description: Microsoft의 일반적인 설계 지침에 따라 가상 음성 기술을 사용합니다. 이 지침은 가상 음성 기술을 책임감 있게 개발할 수 있는 가이드를 제공하기 위해 Microsoft에서 성우, 소비자 및 언어 장애가 있는 개인과 함께 수행한 연구를 통해 작성되었습니다.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 6778a892cdc9e112c6550fab71cc635ea17938fa
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510408"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>가상 음성 기술에 대한 책임 있는 배포 지침

이 문서에서는 가상 음성 기술 사용에 대한 Microsoft의 일반적인 설계 지침에 대해 알아봅니다. 이 지침은 가상 음성 기술을 책임감 있게 개발할 수 있는 가이드를 제공하기 위해 Microsoft에서 성우, 소비자 및 언어 장애가 있는 개인과 함께 수행한 연구를 통해 작성되었습니다.

가상 음성 기술의 배포에 대해서는 대부분의 시나리오에서 다음 지침이 적용됩니다.

### <a name="disclose-when-the-voice-is-synthetic"></a>음성이 가상인 경우의 공개
음성이 컴퓨터로 생성되었다는 점을 공개하는 것은 사기와 같은 위험을 최소화할 뿐만 아니라 음성을 제공하는 조직의 신뢰도를 높입니다. [공개 방법](concepts-disclosure-guidelines.md)에 대해 자세히 알아봅니다.

Microsoft의 고객은 사용자 지정 인공신경망 음성이 가상이라는 점을 사용자에게 공개해야 합니다. 
* 특히 유명한 인물의 음성을 이용할 때는 청중에게 충분한 정보를 제공해야 합니다. 사람들은 의식적으로든 무의식적으로든 해당 컨텐츠를 제공하는 사람을 기반으로 정보에 대한 판단을 내리게 됩니다.  예를 들어 영상 재생을 시작할 때 해당 내용을 음성으로 공개할 수 있습니다. 자세한 내용은 [공개 패턴](concepts-disclosure-patterns.md)을 참조하세요.   
* 미성년자 및 어린이를 위해 디자인된 사용 사례의 경우 부모 또는 다른 당사자에게 적절하게 공개하는 것이 좋습니다. 사용 사례가 미성년자 또는 어린이를 대상으로 할 경우 부모 또는 법적 보호 부서에서 가상 미디어 사용에 대해 공개된 내용을 이해하고 미성년자 또는 어린이가 해당 환경을 이용하게 할 것인지에 대한 적절한 결정을 내릴 수 있도록 해야 합니다. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>시나리오에 적합한 음성 유형 선택
가상 음성이 사용되는 컨텍스트와 가상 음성을 사용함으로 인한 잠재적 피해를 신중하게 고려해야 합니다. 예를 들어 개인 메시징, 금융 거래 또는 인간의 적응력이나 공감이 필요한 복잡한 상황 등 위험도가 높은 시나리오에서는 높은 성능의 가상 음성이 적합하지 않을 수 있습니다. 

사용자가 음성 유형에 대해 다양한 기대를 가질 수도 있습니다. 예를 들어 가상 음성으로 민감한 뉴스를 청취하는 경우 어떤 사용자는 보다 감성적이고 사실적인 톤을 좋아할 수도 있고 어떤 사용자는 중립적인 음성을 좋아할 수도 있습니다. 사용자 기본 설정을 보다 잘 이해할 수 있도록 본인의 애플리케이션을 테스트하는 것이 좋습니다.

### <a name="be-transparent-about-capabilities-and-limitations"></a>기능 및 제한 사항에 대해 투명하게
사용자는 고성능 가상 음성 에이전트와 상호 작용할 때 더 높은 기대치를 가질 가능성이 높습니다. 시스템 기능이 이러한 기대를 충족하지 못하는 경우에는 신뢰가 저하될 수 있으며, 불쾌하거나 유해한 환경도 발생할 수 있습니다.

### <a name="provide-optional-human-support"></a>선택 가능한 사용자 지원 제공
모호한 업무 관련 시나리오(예: 전화 지원 센터)에서 사용자는 컴퓨터 에이전트가 본인의 요청에 대해 적절하게 응답할 것이라고 항상 신뢰하지는 않습니다. 이러한 상황에서는 시스템의 음성이나 기능이 아무리 사실적이어도 사람의 지원이 필요할 수 있습니다.

## <a name="considerations-for-voice-talent"></a>성우를 위한 고려 사항
가상 음성을 만들기 위해 성우와 함께 작업할 때 다음과 같은 지침이 적용됩니다.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>성우에게서 의미 있는 동의 얻기
성우는 음성 모델(사용 방법 및 위치)에 대해 컨트롤을 하고 사용에 대한 보상을 받아야 합니다. Microsoft의 가상 Speech Service를 이용하는 고객은 성우로부터 가상 음성을 만드는 데 필요한 권한에 대한 내용을 문서로 확보하고, 성우와 함께 기간, 사용, 컨텐츠 제한 사항 등에 대한 계약서를 작성해야 합니다.  잘 알려진 사람의 가상 음성을 만드는 경우 음성을 제공하는 사람에게 해당 컨텐츠를 편집하거나 승인할 수 있는 방법을 제공해야 합니다.

성우는 해당 기술이 악의적인 일에 사용될 수 있음을 모르고 있을 수 있으며, 시스템 소유자는 성우에게 해당 기술의 기능에 대해 설명해야 합니다. Microsoft의 고객은 가상 음성이 텍스트 음성 변환 서비스를 이용하여 어떻게 개발되고 작동하는지 설명해주는 Microsoft의 [성우에 대한 공개](/legal/cognitive-services/speech-service/disclosure-voice-talent) 내용을 성우에게 직접 공유하거나 성우의 대리인을 통해 공유해야 합니다.

## <a name="considerations-for-those-with-speech-disorders"></a>언어 장애가 있는 사람들을 위한 고려 사항
가상 음성 기술을 만들고 배포하기 위해 언어 장애가 있는 사람과 함께 작업하는 경우 다음 지침이 적용됩니다.

### <a name="provide-guidelines-to-establish-contracts"></a>계약서 작성을 위한 지침 제공
발화의 보조 수단으로 가상 음성을 이용하는 사람과의 계약서 작성을 위한 지침을 제공합니다. 계약서에는 음성의 소유자, 사용 기간, 소유권 이전 조건, 음성 글꼴 삭제 절차, 무단 액세스 방지 방법 등을 명시하는 것을 고려해야 합니다. 또한 권한이 부여된 경우 사망한 후에는 가족 구성원에게 음성 글꼴 소유권을 이전할 수 있도록 해야 합니다.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>음성 패턴의 불일치에 대한 대처
자신의 음성 글꼴을 레코딩하는 언어 장애가 있는 사람의 경우 발화 패턴(혀꼬임, 특정 발음 불가 등)의 불일치로 인해 레코딩 절차가 복잡해질 수 있습니다. 이러한 경우에는 가상 음성 기술 및 기록 세션에서 이를 수용할 수 있어야 합니다(즉, 쉬는 시간을 제공하거나 레코딩 세션 추가).

### <a name="allow-modification-over-time"></a>시간 경과에 따른 수정 허용
언어 장애가 있는 사람이 시간의 경과(예: 어린이에서 청소년이 되는 경우)를 반영하기 위해 가상 음성을 업데이트하고 싶어 할 수 있습니다. 또한 시간이 지남에 따라 선호하는 것이 달라질 수 있으며 피치, 악센트 및 기타 음성 특성을 변경하고 싶어 할 수 있습니다.


## <a name="see-also"></a>참고 항목

* [성우에 대한 공개](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [공개 방법](concepts-disclosure-guidelines.md)
* [디자인 패턴 공개](concepts-disclosure-patterns.md)
