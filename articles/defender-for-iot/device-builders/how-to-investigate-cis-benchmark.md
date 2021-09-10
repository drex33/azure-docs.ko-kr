---
title: CIS 벤치마크 권장 사항 조사
description: OS 기준 권장 사항을 기반으로 하는 기본 및 고급 조사를 수행합니다.
ms.date: 08/11/2021
ms.topic: how-to
ms.openlocfilehash: 295345d43c48736f9f4572c8845f96560ce4f184
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536193"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>OS 기준(CIS 벤치마크 기반) 권장 사항 조사

OS 기준 권장 사항을 기반으로 하는 기본 및 고급 조사를 수행합니다.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>기본 OS 기준 보안 권장 사항 조사  

**IoT Hub** 아래에서 Azure Defender for IoT 포털로 이동하여 OS 기준 권장 사항을 조사할 수 있습니다. 자세한 내용은 [보안 권장 사항을 조사](quickstart-investigate-security-recommendations.md)하는 방법을 참조하세요.

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>고급 OS 기준 보안 권장 사항 조사  

이 섹션에서는 OS 기준 테스트 결과를 보다 잘 이해하고 Azure Log Analytics에서 이벤트를 쿼리하는 방법을 설명합니다.  

고급 OS 기준 보안 권장 사항 조사는 Log Analytics를 사용하는 경우에만 지원됩니다. 계속하기 전에 Defender for IoT를 Log Analytics 작업 영역에 연결합니다. 고급 OS 기준 보안 권장 사항에 대한 자세한 내용은 [Azure Defender for IoT 에이전트 기반 솔루션 구성](how-to-configure-agent-based-solution.md) 방법을 참조하세요.

경고를 위해 Log Analytics에서 IoT 보안 이벤트를 쿼리하려면:

1. **경고** 페이지로 이동합니다.

1. **Log Analytics 작업 영역에서 권장 사항 조사** 를 선택합니다.

권장 사항을 위해 Log Analytics에서 IoT 보안 이벤트를 쿼리하려면:

1. **권장 사항** 페이지로 이동합니다.

1. **Log Analytics 작업 영역에서 권장 사항 조사** 를 선택합니다.

1. 특정 디바이스의 세부 정보를 보려면 **권장 사항 세부 정보** 빠른 보기 페이지에서 **운영 체제(OS) 기준 규칙 세부 정보 표시** 를 선택합니다.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="특정 디바이스의 세부 정보를 봅니다.":::

Log Analytics 작업 영역에서 직접 IoT 보안 이벤트를 쿼리하려면:

1. **로그** 페이지로 이동합니다.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="왼쪽 창에서 로그를 선택합니다.":::

1. **경고 조사** 를 선택하거나 보안 권장 사항 또는 경고에서 **Log Analytics에서 경고 조사** 옵션을 선택합니다.

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>OS 기준 리소스를 조사하는 데 유용한 쿼리는 다음과 같습니다.

> [!Note]
> 다음 쿼리 각각에서 `<device-id>`를 디바이스에 제공한 이름으로 바꿔야 합니다.

### <a name="retrieve-the-latest-information"></a>최신 정보 검색

- **디바이스 플릿 실패**: 다음 쿼리를 실행하여 디바이스 플릿에서 실패한 검사에 대한 최신 정보를 검색합니다.

    ```kusto
    let lastDates = SecurityIoTRawEvent |
    where RawEventName == "Baseline" |
    summarize TimeStamp=max(TimeStamp) by DeviceId;
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId |
    extend event = parse_json(EventDetails) |
    where event.BaselineCheckResult == "FAIL" |
    project DeviceId, event.BaselineCheckId, event.BaselineCheckDescription
    ```

- **특정 디바이스 실패**: 다음 쿼리를 실행하여 특정 디바이스에서 실패한 검사에 대한 최신 정보를 검색합니다.  

    ```kusto
    let id = SecurityIoTRawEvent | 
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)) |
    where TimeGenerated <= now() |
    where RawEventName == "Baseline" |
    where DeviceId == "<device-id>" |
    summarize arg_max(TimeGenerated, IoTRawEventId) |
    project IoTRawEventId;
    SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)), extraDetails = todynamic(EventDetails) |
    where IoTRawEventId == toscalar(id) |
    where extraDetails.BaselineCheckResult == "FAIL" |
    project DeviceId, CceId = extraDetails.BaselineCheckId, Description = extraDetails.BaselineCheckDescription
    ```

- **특정 디바이스 오류**: 다음 쿼리를 실행하여 특정 디바이스에서 오류가 있는 검사에 대한 최신 정보를 검색합니다.

    ```kusto
    let id = SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)) |
    where TimeGenerated <= now() |
    where RawEventName == "Baseline" |
    where DeviceId == "<device-id>" |
    summarize arg_max(TimeGenerated, IoTRawEventId) |
    project IoTRawEventId;
    SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)), extraDetails = todynamic(EventDetails) |
    where IoTRawEventId == toscalar(id) |
    where extraDetails.BaselineCheckResult == "ERROR" |
    project DeviceId, CceId = extraDetails.BaselineCheckId, Description = extraDetails.BaselineCheckDescription
    ```

- **특정 검사에 실패한 디바이스 플릿의 디바이스 목록 업데이트** - 이 쿼리를 실행하여 (디바이스 플릿에서) 특정 검사에 실패한 디바이스의 업데이트된 목록을 검색합니다.  

    ```kusto
    let lastDates = SecurityIoTRawEvent |
    where RawEventName == "Baseline" |
    summarize TimeStamp=max(TimeStamp) by DeviceId;
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId |
    extend event = parse_json(EventDetails) |
    where event.BaselineCheckResult == "FAIL" |
    where event.BaselineCheckId contains "6.2.8" |
    project DeviceId;
    ```

## <a name="next-steps"></a>다음 단계

[보안 권장 사항 조사](quickstart-investigate-security-recommendations.md)
