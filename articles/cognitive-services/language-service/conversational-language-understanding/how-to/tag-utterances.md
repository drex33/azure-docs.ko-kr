---
title: 대화형 언어 이해에서 태그를 발화에 지정하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 대화형 언어 이해 프로젝트에서 태그를 발화에 지정합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 697a42d32fd0af80737ad65a8350b719ffc5c7a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100909"
---
# <a name="how-to-tag-utterances"></a>태그를 발화에 지정하는 방법

프로젝트에 대한 [스키마가 빌드](build-schema.md)되면 학습 발화를 프로젝트에 추가해야 합니다. 발화는 사용자가 프로젝트와 상호 작용할 때 사용하는 것과 비슷합니다. 발화를 추가하는 경우 해당 발화가 속하는 의도를 할당해야 합니다. 발화가 추가되면 프로젝트의 엔터티를 레이블로 발화 내의 단어에 지정합니다. 엔터티에 대한 레이블은 다양한 발화에서 일관되어야 합니다. 

태그 지정은 발화를 의도에 할당하고 엔터티를 레이블로 지정하는 프로세스입니다. 태그를 발화에 지정하는 데 시간을 소모할 수 있습니다. 즉, 프로젝트에 대한 기본 기계 학습 모델을 학습시킬 데이터를 도입하고 구체화합니다. 기계 학습 모델은 사용자가 제공하는 예제를 기반으로 하여 일반화됩니다. 더 많은 예제를 제공할수록 모델에서 더 많은 데이터 요소를 사용하여 더 효율적인 일반화를 만들어야 합니다.

> [!NOTE]
>  엔터티의 학습된 구성 요소는 해당 엔터티에 대한 레이블을 발화에 지정하는 경우에만 정의됩니다. 레이블을 학습된 구성 요소에 지정하지 않고 목록 또는 미리 작성된 구성 요소 _만_ 포함하는 엔터티가 있을 수도 있습니다. 자세한 내용은 [엔터티 구성 요소](../concepts/entity-components.md) 문서를 참조하세요.

프로젝트에서 여러 언어를 사용하도록 설정하는 경우 추가할 발화의 언어도 지정해야 합니다. 대화형 언어 이해의 다국어 기능 중 일부는 프로젝트를 주요 언어로 학습시킨 다음, 사용 가능한 다른 언어로 예측할 수 있습니다. 예제를 다른 언어에 추가하면 제대로 작동하지 않는다고 판단되는 경우 모델의 성능이 이러한 언어에서 향상되지만 지원하려는 모든 언어에서 데이터가 중복되지 않도록 방지하세요. 

예를 들어 사용자를 통해 일정 봇의 성능을 향상시키기 위해 개발자는 주로 예제를 영어로 추가하고, 몇 가지 예제를 스페인어 또는 프랑스어로 추가할 수 있습니다. 추가할 수 있는 발화는 다음과 같습니다.

* "_Set a meeting with **Matt** and **Kevin** **tomorrow** at **12 PM**._ "(Matt 및 Kevin과의 모임을 내일 오후 12시로 설정) (영어)
* "_Reply as **tentative** to the **weekly update** meeting._ "(주간 업데이트 모임에 대해 미정으로 회신) (영어)
* "_Cancelar mi **próxima** reunión._ "(다음 모임 취소) (스페인어)

오케스트레이션 워크플로 프로젝트에서 연결된 의도를 학습하는 데 사용되는 데이터는 프로젝트 내에서 제공되지 않습니다. 대신, 프로젝트는 학습 중에 연결된 서비스(예: 연결된 LUIS 애플리케이션, 대화형 언어 이해 프로젝트 또는 사용자 지정 질문 응답 기술 자료)에서 데이터를 가져옵니다. 그러나 서비스에 연결되지 않은 의도를 만드는 경우에도 발화를 해당 의도에 추가해야 합니다.

예를 들어 개발자는 각 기술에 대한 의도를 만들어 해당 일정 프로젝트, 이메일 프로젝트 및 회사 FAQ 기술 자료에 연결할 수 있습니다. 

## <a name="tag-utterances"></a>발화 태그 지정

:::image type="content" source="../media/tag-utterances.png" alt-text="언어 스튜디오에서 태그를 발화에 지정하는 페이지의 스크린샷" lightbox="../media/tag-utterances.png":::

*섹션 1* 에는 발화가 추가됩니다. 드롭다운 목록에서 의도 중 하나, 발화 언어(해당하는 경우) 및 발화 자체를 선택해야 합니다. 발화의 텍스트 상자에서 *Enter* 키를 눌러 발화를 추가합니다.

*섹션 2* 에는 프로젝트의 엔터티가 포함됩니다. 추가한 엔터티 중 하나를 선택한 다음, 마우스로 텍스트 위를 가리키면 *섹션 3* 과 같이 레이블을 발화 내의 엔터티에 지정할 수 있습니다. 여기서 **+ 엔터티 추가** 단추를 눌러 새 엔터티를 추가할 수도 있습니다. 발화 내에서 해당 엔터티의 레이블을 숨길 수도 있습니다. 

*섹션 3* 에는 추가한 발화가 포함됩니다. 레이블을 지정하려는 텍스트 위로 끌면 엔터티의 상황에 맞는 메뉴가 표시됩니다.

:::image type="content" source="../media/label-utterance-menu.png" alt-text="레이블을 추가할 텍스트를 선택하는 예" lightbox="../media/label-utterance-menu.png":::

> [!NOTE]
> LUIS와 달리 겹치는 엔터티에는 레이블을 지정할 수 없습니다. 둘 이상의 엔터티에서 레이블을 동일한 문자로 지정할 수 없습니다.

## <a name="filter-utterances"></a>발화 필터링

**필터** 를 클릭하면 필터 창에서 선택한 의도 및 엔터티와 연결된 발화만 표시됩니다.
[스키마 빌드](./build-schema.md) 페이지에서 의도를 클릭하면 해당 의도가 자동으로 필터링된 **발화 태그 지정** 페이지로 이동합니다. 

## <a name="next-steps"></a>다음 단계
* [모델 학습 및 평가](./train-model.md)
