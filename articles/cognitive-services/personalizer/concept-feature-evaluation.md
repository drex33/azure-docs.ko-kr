---
title: 기능 평가 - Personalizer
titleSuffix: Azure Cognitive Services
description: Azure Portal의 Personalizer 리소스에서 평가를 실행하는 경우 Personalizer는 모델에 영향을 주는 컨텍스트 및 작업 기능에 대한 정보를 제공합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 3d504293da96ea6f0c22c2a54563b39b92e4485e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831020"
---
# <a name="feature-evaluation"></a>기능 평가

[Azure Portal](https://portal.azure.com)의 Personalizer 리소스에서 평가를 실행하는 경우 Personalizer는 모델에 영향을 주는 컨텍스트 및 작업 기능에 대한 정보를 제공합니다. 

이 기능은 다음과 같은 경우에 유용합니다.

* 모델에 더 중요한 기능에서 영감을 받아 사용할 수 있는 추가 기능을 가정하는 경우
* 중요하지 않은 기능을 확인하고 잠재적으로 제거하거나 사용에 영향을 미칠 수 있는 사항을 추가로 분석하려는 경우
* 카탈로그에 가져오는 새 콘텐츠 또는 제품에 대한 편집 또는 큐레이션에 대한 지침을 제공하려는 경우
* Personalizer로 기능을 전송할 때 발생하는 일반적인 문제 및 실수를 해결하려는 경우

중요한 기능일수록 모델에서 더 높은 가중치를 둡니다. 이러한 기능은 가중치가 더 높기 때문에 Personalizer가 더 높은 보상을 얻을 때 제공되는 경향이 있습니다.

## <a name="getting-feature-importance-evaluation"></a>기능 중요도 평가 가져오기

기능 중요도 결과를 보려면 평가를 실행해야 합니다. 평가 기간 동안 관찰된 기능 이름을 기반으로 사람이 읽을 수 있는 기능 레이블을 만듭니다.

기능 중요도에 대한 결과 정보는 현재 Personalizer 온라인 모델을 나타냅니다. 평가는 현재 온라인 학습 정책을 사용하여 평가 중 수행된 모든 학습을 마친 후, 평가 기간 종료일에 저장된 모델의 기능 중요성을 분석합니다. 

기능 중요도 결과는 평가 중에 테스트되거나 만들어진 다른 정책과 모델을 나타내지 않습니다.  평가 기간이 끝난 후 Personalizer로 전송되는 기능도 평가에 포함되지 않습니다.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>기능 중요도 평가를 해석하는 방법

Personalizer는 유사한 중요도를 가진 기능 "그룹"을 만들어 기능을 평가합니다. 한 그룹이 다른 그룹보다 전반적으로 더 중요할 수 있지만 그룹 내에서 기능은 알파벳순으로 나타납니다.

각 기능에 대한 정보는 다음과 같습니다.

* 기능이 컨텍스트나 작업에서 제공되는지 여부.
* 기능 키 및 값.

예를 들어, 아이스크림 가게 주문 앱은 "Context.Weather:Hot"을 매우 중요한 기능으로 볼 수 있습니다.

Personalizer는 함께 고려할 때 더 높은 보상을 생성하는 기능의 상관 관계를 표시합니다.

예를 들어 "Context.Weather:Hot *과* Action.MenuItem:IceCream"뿐만 아니라 "Context.Weather:Cold *와* Action.MenuItem:WarmTea"가 표시될 수 있습니다.

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>기능 평가에 따라 수행할 수 있는 작업

### <a name="imagine-additional-features-you-could-use"></a>사용할 수 있는 추가 기능 가정

모델의 더 중요한 기능에서 영감 얻기. 예를 들어 비디오 모바일 앱에 "Context.MobileBattery:Low"가 표시되는 경우 연결 형식에서 고객이 다른 비디오 클립을 볼 수 있도록 선택한 다음 연결 형식 및 대역폭에 대한 기능을 앱에 추가할 수도 있습니다.

### <a name="see-what-features-are-not-important"></a>중요하지 않은 기능 보기

잠재적으로 중요하지 않은 기능을 제거하거나 사용에 영향을 줄 수 있는 사항을 추가로 분석합니다. 여러 가지 이유로 기능 순위가 낮을 수 있습니다. 그 이유 중 하나는 기능이 사용자 동작에 영향을 주지 않기 때문입니다. 그러나 이 기능이 사용자에게 보이지 않기 때문일 수도 있습니다. 

예를 들어 비디오 사이트에서 'Action.VideoResolution=4k'가 중요도가 낮은 기능임을 확인할 수 있는데 이는 사용자 조사 결과와 모순되는 것입니다. 그 원인은 애플리케이션이 비디오 해상도를 언급하거나 표시하지 않아 사용자가 이를 기반으로 동작을 변경하지 않기 때문일 수 있습니다.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>편집 또는 큐레이션 팀에 지침 제공

카탈로그로 가져올 가치가 있는 새로운 콘텐츠 또는 제품에 대한 지침을 제공합니다. Personalizer는 사람의 인사이트와 팀을 보강하는 도구로 설계되었습니다. 이 작업을 수행하는 한 가지 방법은 동작을 유도하는 제품, 문서 또는 콘텐츠에 대한 정보를 편집 그룹에 제공하는 것입니다. 예를 들어, 비디오 애플리케이션 시나리오는 "Action.VideoEntities.Cat:true"라는 중요한 기능이 있음을 보여 주어 편집 팀이 더 많은 고양이 비디오를 가져오도록 요청할 수 있습니다.

### <a name="troubleshoot-common-problems-and-mistakes"></a>일반적인 문제 및 실수 해결

애플리케이션 코드를 변경하여 일반적인 문제와 실수를 해결할 수 있으므로 부적절하거나 형식이 잘못된 기능이 Personalizer로 전송되지 않습니다. 

기능을 전송할 때 발생하는 일반적인 실수는 다음과 같습니다.

* PII(개인 식별 정보) 전송. 한 개인에 특정된 PII(예: 이름, 전화 번호, 신용 카드 번호, IP 주소)를 Personalizer와 함께 사용해서는 안 됩니다. 애플리케이션에서 사용자를 추적해야 하는 경우에는 식별되지 않는 UUID 또는 다른 UserID 번호를 사용합니다. 대부분의 시나리오에서 문제가 발생할 수 있습니다.
* 사용자 수가 많으면 각 사용자의 상호 작용이 모든 모집단의 상호 작용보다 더 중요하게 평가되지 않으므로 사용자 ID(PII가 아닌 경우에도)를 전송하면 값보다 더 많은 노이즈가 모델에 추가될 수 있습니다.
* 기능화된 시간 값 대신 날짜-시간 필드를 정확한 타임스탬프로 전송. Context.TimeStamp.Day=Monday 또는 "Context.TimeStamp.Hour"="13"과 같은 기능을 사용하면 더 유용합니다. 각각에 대해 최대 7개 또는 24개의 기능 값이 있습니다. 그러나 "Context.TimeStamp":"1985-04-12T23:20:50.52Z"는 너무 정확해서 다시는 발생하지 않기 때문에 학습할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

Personalizer를 사용하여 [확장성 및 성능](concepts-scalability-performance.md) 이해

