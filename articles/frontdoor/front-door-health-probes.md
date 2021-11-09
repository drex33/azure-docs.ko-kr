---
title: Azure Front Door - 백 엔드 상태 모니터링 | Microsoft Docs
description: 이 문서는 Azure Front Door 백 엔드의 상태를 모니터링하는 방법을 이해하는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2021
ms.author: duau
ms.openlocfilehash: 101d769fce7baaac629c756b3f7fad173f736b6c
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053710"
---
# <a name="health-probes"></a>상태 프로브

지정된 Front Door 환경에 대한 각 백 엔드의 상태 및 근접성을 확인하기 위해 각 Front Door 환경은 구성된 각각의 백 엔드로 가상 HTTP/HTTPS 요청을 주기적으로 보냅니다. 그런 다음, Front Door는 프로브의 이러한 응답을 사용하여 클라이언트 요청을 라우팅할 '최상의' 백 엔드 리소스를 결정합니다. 

> [!WARNING]
> Front Door는 전 세계적으로 많은 엣지 환경을 포함하므로, 백 엔드에 대한 상태 프로브 볼륨은 구성된 상태 프로브 빈도에 따라 분당 25개 요청에서 분당 1,200개 요청까지 매우 높을 수 있습니다. 기본 프로브 빈도가 30초인 경우 백 엔드 프로브 볼륨은 분당 약 200개 요청이 있어야 합니다.

> [!NOTE]
> Front Door HTTP/HTTPS 프로브는 값이 `Edge Health Probe`로 설정된 `User-Agent` 헤더와 함께 전송됩니다. 

## <a name="supported-protocols"></a>지원되는 프로토콜

Front Door는 HTTP 또는 HTTPS 프로토콜을 통한 프로브 전송을 지원합니다. 이러한 프로브는 라우팅 클라이언트 요청에 대해 구성된 동일한 TCP 포트를 통해 전송되며, 재정의할 수 없습니다.

## <a name="supported-http-methods-for-health-probes"></a>상태 프로브에 지원되는 HTTP 메서드

Front Door는 상태 프로브를 보내기 위해 다음과 같은 HTTP 메서드를 지원합니다.

1. **GET:** GET 메서드는 요청 URI로 식별되는 모든 정보(엔터티 형식)를 검색함을 의미합니다.
2. **HEAD:** HEAD 메서드는 서버가 응답에서 메시지 본문을 반환해서는 안 된다는 점을 제외하면 GET 메서드와 동일합니다. 새 Front Door 프로필의 경우 기본적으로 프로브 메서드가 HEAD로 설정됩니다.

> [!NOTE]
> 백 엔드에 대한 부하 및 비용을 낮추기 위해 Front Door는 상태 프로브에 HEAD 요청을 사용할 것을 권장합니다.

## <a name="health-probe-responses"></a>상태 프로브 응답

| 응답  | Description | 
| ------------- | ------------- |
| 상태 확인  | 200 정상 상태 코드는 백 엔드가 정상임을 나타냅니다. 그 외 코드는 모두 실패로 간주됩니다. 어떠한 이유로(네트워크 오류 포함) 유효한 HTTP 응답이 프로브에 대해 수신되지 않으면 프로브가 실패로 계산됩니다.|
| 대기 시간 측정  | 대기 시간은 응답의 마지막 바이트를 수신하는 순간 프로브 요청을 보내기 직전에 측정된 벽시계 시간입니다. 각 요청에 대한 새 TCP 연결을 사용하므로 이 측정값은 기존 웜 연결을 사용하는 백 엔드에 편향되지 않습니다.  |

## <a name="how-front-door-determines-backend-health"></a>Front Door가 백 엔드 상태를 결정하는 방법

Azure Front Door는 상태를 확인하기 위해 모든 알고리즘에서 아래와 동일한 3단계 프로세스를 사용합니다.

1. 비활성화된 백 엔드를 제외합니다.

2. 상태 프로브 오류가 있는 백 엔드를 제외합니다.
    * 이 선택은 마지막 _n_ 상태 프로브 응답을 확인하여 완료합니다. 최소한 _x_ 가 정상적인 경우 백 엔드는 정상 상태로 간주됩니다.

    * _n_ 은 부하 분산 설정의 SampleSize 속성을 변경하여 구성됩니다.

    * _x_ 는 부하 분산 설정의 SuccessfulSamplesRequired 속성을 변경하여 구성됩니다.

3. 백 엔드 풀의 정상 백 엔드 세트에 대해 Front Door는 각 백 엔드에 대한 대기 시간(왕복 시간)을 추가로 측정하고 유지합니다.

> [!NOTE]
> 단일 엔드포인트가 여러 백 엔드 풀의 멤버인 경우 Azure Front Door 백 엔드에 전송되는 상태 프로브 수를 최적화하여 백 엔드의 부하를 줄입니다. 상태 프로브 요청은 가장 낮은 구성된 샘플 간격에 따라 전송됩니다. 모든 풀에서 엔드포인트의 상태가 동일한 상태 프로브의 응답에 의해 결정됩니다.

## <a name="complete-health-probe-failure"></a>상태 프로브 실패 완료

상태 프로브가 백 엔드 풀의 모든 백 엔드에 대해 실패한 경우, Front Door는 모든 백 엔드를 정상으로 간주하고 모든 백 엔드에 걸친 라운드 로빈 배포에 트래픽을 라우팅합니다.

백 엔드가 정상 상태를 반환하고 나면, Front Door는 정상 부하 분산 알고리즘을 다시 시작합니다.

## <a name="disabling-health-probes"></a>상태 프로브 사용 안 함

백 엔드 풀에 단일 백 엔드가 있는 경우 상태 프로브를 사용하지 않도록 설정하여 애플리케이션 백 엔드에 대한 부하를 줄일 수 있습니다. 백 엔드 풀에 백 엔드가 여러 개 있지만 그중 하나만 활성화된 상태인 경우에도 상태 프로브를 비활성화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door 표준/프리미엄을 만드는](standard-premium/create-front-door-portal.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
