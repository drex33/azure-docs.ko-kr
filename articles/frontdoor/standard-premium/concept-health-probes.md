---
title: Azure Front Door 표준/프리미엄(미리 보기) 상태 프로브 모니터링
description: 이 문서에서는 Azure Front Door Service가 백 엔드의 상태를 모니터링하는 방법을 이해할 수 있습니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a856ae7d38439d11ba0ddd65ebecdda7a3d2ce48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750206"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Azure Front Door 표준/프리미엄(미리 보기) 상태 프로브 모니터링

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door는 각 백 엔드에 HTTP 또는 HTTPS 요청을 주기적으로 보냅니다. 이러한 요청은 Azure Front Door가 백 엔드 풀에 있는 각 엔드포인트의 상태를 확인할 수 있게 해줍니다. 그런 다음 Front Door는 이러한 프로브 응답을 사용하여 클라이언트 요청을 라우팅하기 위한 '최상의' 백 엔드 리소스를 결정합니다. 

> [!WARNING]
> Front Door는 전 세계적으로 많은 엣지 환경을 포함하므로, 백 엔드에 대한 상태 프로브 볼륨은 구성된 상태 프로브 빈도에 따라 분당 25개 요청에서 분당 1,200개 요청까지 매우 높을 수 있습니다. 기본 프로브 빈도 30초를 사용할 경우 백 엔드 프로브 볼륨은 분당 약 200개 요청이 될 것입니다.

## <a name="supported-protocols"></a>지원되는 프로토콜

Front Door는 HTTP 또는 HTTPS 프로토콜을 통한 프로브 전송을 지원합니다. 이러한 프로브는 라우팅 클라이언트 요청에 대해 구성된 동일한 TCP 포트를 통해 전송되며, 재정의할 수 없습니다.

## <a name="supported-http-methods-for-health-probes"></a>상태 프로브에 지원되는 HTTP 메서드

Front Door는 상태 프로브를 보내기 위해 다음과 같은 HTTP 메서드를 지원합니다.

* **GET:** GET 메서드는 요청 URI로 식별되는 모든 정보(엔터티 형식)를 검색함을 의미합니다.
* **HEAD:** HEAD 메서드는 서버가 응답에서 메시지 본문을 반환해서는 안 된다는 점을 제외하면 GET 메서드와 동일합니다. 새 Front Door 프로필의 경우 기본적으로 프로브 메서드가 HEAD로 설정됩니다.

> [!NOTE]
> 백 엔드에 대한 부하 및 비용을 낮추기 위해 Front Door는 상태 프로브에 HEAD 요청을 사용할 것을 권장합니다.

## <a name="health-probe-responses"></a>상태 프로브 응답

| 응답  | 설명 | 
| ------------- | ------------- |
| 상태 확인  |  200 정상 상태 코드는 백 엔드가 정상임을 나타냅니다. 그 외 코드는 모두 실패로 간주됩니다. 어떠한 이유로(네트워크 오류 포함) 유효한 HTTP 응답이 프로브에 대해 수신되지 않으면 프로브가 실패로 계산됩니다.|
| 대기 시간 측정  | 대기 시간은 응답의 마지막 바이트를 수신하는 순간 프로브 요청을 보내기 직전에 측정된 벽시계 시간입니다. 각 요청에 대한 새 TCP 연결을 사용하므로 이 측정값은 기존 웜 연결을 사용하는 백 엔드에 편향되지 않습니다.  |

## <a name="how-front-door-determines-backend-health"></a>Front Door가 백 엔드 상태를 결정하는 방법

Azure Front Door는 상태를 확인하기 위해 모든 알고리즘에서 아래와 동일한 3단계 프로세스를 사용합니다.

1. 비활성화된 백 엔드를 제외합니다.

1. 상태 프로브 오류가 있는 백 엔드를 제외합니다.

    * 이 선택은 마지막 _n_ 상태 프로브 응답을 확인하여 완료합니다. 최소한 _x_ 가 정상적인 경우 백 엔드는 정상 상태로 간주됩니다.

    * _n_ 은 부하 분산 설정의 SampleSize 속성을 변경하여 구성됩니다.

    * _x_ 는 부하 분산 설정의 SuccessfulSamplesRequired 속성을 변경하여 구성됩니다.

1. 백 엔드 풀의 정상 백 엔드 세트에 대해 Front Door는 각 백 엔드에 대한 대기 시간(왕복 시간)을 추가로 측정하고 유지합니다.

> [!NOTE]
> 단일 끝점이 여러 백 엔드 풀의 멤버인 경우 Azure Front 도어가 백 엔드에 전송 되는 상태 프로브 수를 최적화 하 여 백 엔드에 대 한 부하를 줄입니다. 상태 프로브 요청은 가장 낮은 구성 된 샘플 간격에 따라 전송 됩니다. 모든 풀의 끝점 상태는 동일한 상태 프로브의 응답에 의해 결정 됩니다.

## <a name="complete-health-probe-failure"></a>상태 프로브 실패 완료

상태 프로브가 백 엔드 풀의 모든 백 엔드에 대해 실패한 경우, Front Door는 모든 백 엔드를 정상으로 간주하고 모든 백 엔드에 걸친 라운드 로빈 배포에 트래픽을 라우팅합니다.

백 엔드가 정상 상태를 반환하고 나면, Front Door는 정상 부하 분산 알고리즘을 다시 시작합니다.

## <a name="disabling-health-probes"></a>상태 프로브 사용 설정 해제

백 엔드 풀에 하나의 백 엔드가 있거나 백 엔드 풀에서 하나의 백 엔드만 활성 상태인 경우 상태 프로브를 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 애플리케이션 백 엔드에 대한 부하를 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Front Door 표준/프리미엄을 만드는](create-front-door-portal.md) 방법을 알아봅니다.
