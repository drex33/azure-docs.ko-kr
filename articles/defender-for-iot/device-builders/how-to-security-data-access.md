---
title: 보안 및 권장 사항 데이터 액세스
description: Defender for IoT를 사용하는 경우 보안 경고 및 권장 사항 데이터에 액세스하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 8dc6f9d3deb80439d23e220c97f2a24363f0f05b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018620"
---
# <a name="access-your-security-data"></a>보안 데이터 액세스

Defender for IoT는 보안 경고, 권장 사항, 원시 보안 데이터(저장하기로 선택한 경우)를 Log Analytics 작업 영역에 저장합니다.

## <a name="log-analytics"></a>Log Analytics

어떤 Log Analytics 작업 영역을 사용할지 구성하려면 다음을 수행합니다.

1. IoT Hub를 엽니다.
1. **보안** 섹션에서 **설정** 블레이드를 클릭합니다.
1. **데이터 컬렉션** 을 클릭하고 Log Analytics 작업 영역 구성을 변경합니다.

구성 후 Log Analytics 작업 영역에서 경고 및 권장 사항에 액세스하려면 다음을 수행합니다.

1. Defender for IoT에서 경고 또는 권장 사항을 선택합니다.
1. **추가 조사** 를 클릭한 다음 **이 경고가 있는 디바이스를 확인하려면 여기를 클릭하여 DeviceId 열을 보십시오** 를 클릭합니다.

Log Analytics에서 데이터를 쿼리하는 방법에 대한 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../../azure-monitor/logs/get-started-queries.md)을 참조하세요.

## <a name="security-alerts"></a>보안 경고

보안 경고는 Defender for IoT 솔루션에 대해 구성된 Log Analytics 작업 영역의 _AzureSecurityOfThings.SecurityAlert_ 테이블에 저장됩니다.

보안 경고를 살펴보는 데 도움이 되는 유용한 쿼리를 다양하게 제공하고 있습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드를 선택

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Description                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격(brute force attack) 성공           | 디바이스에서 무차별 암호 대입 공격(brute force attack)에 성공함        |    { "Full Source Address": "[\"10.165.12.18:\"]", "User Names": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 디바이스에 로컬 로그인 성공      | 디바이스에 대해 성공한 로컬 로그인이 감지됨     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 디바이스에 로컬 로그인 시도 실패  | 디바이스에 대해 실패한 로컬 로그인 시도가 감지됨 |    { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>디바이스 요약

지난주에 감지된 개별 보안 경고의 수를 IoT 허브, 디바이스, 경고 심각도, 경고 유형별로 그룹화하여 가져옵니다.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 무차별 암호 대입 공격(brute force attack) 성공           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 디바이스에 로컬 로그인 시도 실패  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 디바이스에 로컬 로그인 성공      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 암호화 코인 마이너                     | 4   |

### <a name="iot-hub-summary"></a>IoT 허브 요약

지난주에 경고가 발생한 여러 고유 디바이스를 IoT 허브, 경고 심각도, 경고 유형별로 선택

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 무차별 암호 대입 공격(brute force attack) 성공           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 디바이스에 로컬 로그인 시도 실패  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 높음          | 디바이스에 로컬 로그인 성공      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 중간        | 암호화 코인 마이너                     | 1          |

## <a name="security-recommendations"></a>보안 권장 사항

보안 권장 사항은 Defender for IoT 솔루션에 대해 구성된 Log Analytics 작업 영역의 _AzureSecurityOfThings.SecurityRecommendation_ 테이블에 저장됩니다.

보안 권장 사항을 살펴보는 데 도움이 되는 유용한 쿼리를 다양하게 제공하고 있습니다.

### <a name="sample-records"></a>샘플 레코드

몇 개의 임의 레코드를 선택

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Description | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.06 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | Active | 입력 체인 중 하나에서 허용 방화벽 규칙을 찾음 | 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간 | Active | 입력 체인 중 하나에서 허용 방화벽 규칙을 찾음 | 광범위한 IP 주소 또는 포트에 대한 허용 패턴을 포함하는 방화벽 규칙을 찾았습니다. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>디바이스 요약

IoT 허브, 디바이스, 권장 사항 심각도, 유형별로 그룹화된 고유한 활성 보안 권장 사항의 수를 가져옵니다.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | 개수 |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 높음          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 중간        | 4   |

## <a name="next-steps"></a>다음 단계

- Defender for IoT [개요](overview.md) 읽기
- [디바이스 빌더를 위한 에이전트 기반 솔루션](architecture-agent-based.md)에서 Defender for IoT에 대한 자세한 내용 참조
- [Defender for IoT 경고](concept-security-alerts.md)의 이해 및 검색
- [Defender for IoT 권장 사항](concept-recommendations.md)의 이해 및 검색