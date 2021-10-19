---
title: Azure 애플리케이션 Insights의 statsbeat Microsoft Docs
description: Application Insights sdk 및 자동 계측에 대 한 Statstics
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 440f6651f759bce9340763ea141c8057d5a1e4b8
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167952"
---
# <a name="statsbeat-in-azure-application-insights"></a>Azure 애플리케이션 Insights의 statsbeat

statsbeat Application Insights sdk 및 자동 계측에 대 한 필수 및 필수가 아닌 [사용자 지정 메트릭을](../essentials/metrics-custom-overview.md) 수집 합니다. Statsbeat는 Azure Monitor Application insights 고객을 위한 세 가지 혜택을 제공 합니다.
-   Service Health 및 안정성 (수집 끝점에 대 한 연결의 외부 모니터링)
-   지원 진단 (자가 진단 정보 및 CSS 정보)
-   제품 개선 (디자인 최적화에 대 한 정보)

Statsbeat 트 데이터는 Microsoft 데이터 저장소에 저장 됩니다.  고객의 전반적인 모니터링 볼륨 및 비용에는 영향을 주지 않습니다. 

## <a name="what-data-does-statsbeat-collect"></a>Statsbeat에서 수집 하는 데이터는 무엇 인가요?

Statsbeat는 필수 및 필수가 아닌 메트릭을 수집 합니다.

## <a name="supported-languages"></a>지원되는 언어

| C#                        | Java            | JavaScript                | Node.js         | Python          |
|---------------------------|-----------------|---------------------------|-----------------|-----------------|
| 현재 지원 되지 않음   | 지원됨       | 현재 지원 되지 않음   | 지원됨       | 지원됨       |


### <a name="essential-statsbeat"></a>필수 Statsbeat

#### <a name="network-statsbeat"></a>네트워크 Statsbeat

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|요청 성공 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|요청 실패 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|요청 기간|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|재시도 횟수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|제한 수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|
|예외 수|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`, `Endpoint`, `Host`|

#### <a name="attach-statsbeat"></a>Statsbeat 연결

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|연결|개수| `Resource Provider`, `Resource Provider Identifier`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Operating System`, `Language`, `Version`|

#### <a name="feature-statsbeat"></a>기능 Statsbeat

|메트릭 이름|단위|지원되는 차원|
|-----|-----|-----|
|기능|개수| `Resource Provider`, `Attach Type`, `Instrumentation Key`, `Runtime Version`, `Feature`, `Type`, `Operating System`, `Language`, `Version`|

### <a name="non-essential-statsbeat"></a>필수적이 지 않은 Statsbeat

- 디스크 지 속성의 성공 및 실패를 추적 합니다.
- 라이브 메트릭 네트워크 statsbeat
- Azure metadata service 네트워크 statsbeat
- 프로파일러 네트워크 statsbeat

### <a name="configure-statsbeat"></a>Statsbeat 구성

#### <a name="java"></a>[Java](#tab/java)

필수가 아닌 Statsbeat를 사용 하지 않도록 설정 하려면 구성 파일에 아래 구성을 추가 합니다.

```json
{
  "preview": {
    "statsbeat": {
        "disabled": "true"
    }
  }
}
```

환경 변수를 true로 설정 하 여이 기능을 사용 하지 않도록 설정할 수도 있습니다. 이렇게 하면 `APPLICATIONINSIGHTS_STATSBEAT_DISABLED` json 구성에 지정 된 사용 안 함 보다 우선 적용 됩니다.

#### <a name="node"></a>[Node](#tab/node)

해당 없음

#### <a name="python"></a>[Python](#tab/python)

해당 없음

---
