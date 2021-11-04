---
title: Azure 애플리케이션 Insights | Statsbeat Microsoft Docs
description: 애플리케이션 Insights SDK 및 자동 계측에 대한 통계
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 55e727bc05007c69f0144c7f95e17e38f907742e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426889"
---
# <a name="statsbeat-in-azure-application-insights"></a>Azure 애플리케이션 Insights Statsbeat

Statsbeat는 Application Insights SDK 및 자동 계측에 대한 필수 및 필수가 아닌 [사용자 지정 메트릭을](../essentials/metrics-custom-overview.md) 수집합니다. Statsbeat는 Azure Monitor Application Insights 고객에게 다음과 같은 세 가지 이점을 제공합니다.
-   Service Health 및 안정성(검색 엔드포인트에 대한 연결의 외부 모니터링)
-   진단 지원(자가 진단 인사이트 및 CSS 인사이트)
-   제품 개선(디자인 최적화에 대한 인사이트)

Statsbeat 데이터는 Microsoft 데이터 저장소에 저장됩니다.  고객의 전반적인 모니터링 볼륨 및 비용에는 영향을 미치지 않습니다. 

## <a name="what-data-does-statsbeat-collect"></a>Statsbeat는 어떤 데이터를 수집하나요?

Statsbeat는 필수 및 필수가 아닌 메트릭을 수집합니다.

## <a name="supported-languages"></a>지원되는 언어

| C#                        | Java            | JavaScript                | Node.js         | Python          |
|---------------------------|-----------------|---------------------------|-----------------|-----------------|
| 현재 지원되지 않음   | 지원됨       | 현재 지원되지 않음   | 지원됨       | 지원됨       |


### <a name="essential-statsbeat"></a>Essential Statsbeat

#### <a name="network-statsbeat"></a>네트워크 통계비트

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|요청 성공 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|요청 실패 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|요청 기간|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|재시도 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|제한 수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|예외 수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|

#### <a name="attach-statsbeat"></a>Attach Statsbeat

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|연결|개수| `Resource Provider`, `Resource Provider Identifier`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

#### <a name="feature-statsbeat"></a>기능 통계비트

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|기능|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Feature`, `Type`, `Operating System`, `Language`, `Version`|

### <a name="non-essential-statsbeat"></a>필수가 아닌 Statsbeat

다시 시도 가능한 원격 분석을 위해 디스크 지속성을 사용하는 경우 디스크 I/O 오류 추적

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|읽기 실패 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|
|쓰기 실패 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

### <a name="configure-statsbeat"></a>Statsbeat 구성

#### <a name="java"></a>[Java](#tab/java)

필수가 아닌 Statsbeat를 사용하지 않도록 설정하려면 구성 파일에 아래 구성을 추가합니다.

```json
{
  "preview": {
    "statsbeat": {
        "disabled": "true"
    }
  }
}
```

환경 변수를 true로 설정하여 이 기능을 사용하지 않도록 설정할 수도 `APPLICATIONINSIGHTS_STATSBEAT_DISABLED` 있습니다(json 구성에 지정된 사용 안 함보다 우선 적용됨).

#### <a name="node"></a>[Node](#tab/node)

N/A

#### <a name="python"></a>[Python](#tab/python)

해당 없음

---
