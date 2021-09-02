---
title: 확장성 및 성능 - Personalizer
titleSuffix: Azure Cognitive Services
description: 고성능이고 트래픽이 많은 웹 사이트 및 애플리케이션은 확장성과 성능을 위해 Personalizer와 관련하여 대기 시간 및 학습 처리량이라는 두 가지 주요 요소를 고려해야 합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 711d1f69485fb7f4c9256ac4a2edbe351ae4619c
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831407"
---
# <a name="scalability-and-performance"></a>확장성 및 성능

고성능이고 트래픽이 많은 웹 사이트 및 애플리케이션은 확장성과 성능을 위해 Personalizer와 관련하여 다음 두 가지 주요 요소를 고려해야 합니다.

* Rank API 호출을 수행할 때 짧은 대기 시간 유지
* 이벤트 입력을 따라잡을 수 있는 학습 처리량 확인

개인 설정은 대부분의 REST API를 통한 통신 전용 호출 기간을 사용하여 순위를 신속하게 반환할 수 있습니다. Azure는 요청에 신속하게 응답할 수 있는 기능을 자동 조정합니다.

##  <a name="low-latency-scenarios"></a>짧은 대기 시간 시나리오

일부 애플리케이션은 순위를 반환할 때 대기 시간이 짧아야 합니다. 대기 시간은 짧아야 합니다.

* 순위가 지정된 콘텐츠를 표시하기까지 사용자가 너무 오랜 시간 기다리지 않도록 합니다.
* 트래픽이 많이 발생하는 서버에서 컴퓨팅 시간 및 네트워크 연결이 부족하지 않도록 합니다.


## <a name="scalability-and-training-throughput"></a>확장성 및 학습 처리량

Personalizer는 Rank API 및 Reward API 후 Personalizer에서 비동기적으로 전송한 메시지를 기반으로 재학습되는 모델을 업데이트하여 작동합니다. 이러한 메시지는 애플리케이션의 경우 Azure EventHub를 사용하여 전송됩니다.

 대부분의 애플리케이션은 Personalizer의 최대 조인 및 학습 처리량을 초과하지 않습니다. 이 최대값에 도달해도 애플리케이션이 느려지지는 않지만 Event Hub 큐가 내부적으로 정리 가능한 속도보다 더 빨리 채워짐을 암시합니다.

## <a name="how-to-estimate-your-throughput-requirements"></a>처리량 요구 사항을 추정하는 방법

* 컨텍스트 및 작업 JSON 문서의 길이를 추가하여 순위 이벤트당 평균 바이트 수를 추정합니다.
* 20MB/초를 이 추정 평균 바이트 수로 나눕니다.

예를 들어, 평균 페이로드에 500개의 기능이 있고 각각 20자로 추정되는 경우 각 이벤트는 대략 10kb입니다. 이러한 추정치를 사용하면 20,000,000/10,000 = 2,000이벤트/초이고, 하루당 이벤트는 약 173,000,000개입니다. 

이 한계에 도달할 경우 지원 팀에 아키텍처 조언을 요청하세요.

## <a name="next-steps"></a>다음 단계

[Personalizer 만들기 및 구성](how-to-settings.md).