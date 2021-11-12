---
title: 의심스러운 디바이스 조사
description: 이 방법 가이드에서는 Defender for IoT로 Log Analytics를 사용하여 의심스러운 IoT 디바이스를 조사하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: b28a153a8cd6226a91d9ce19161d30e6d20cfbdb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293519"
---
# <a name="investigate-a-suspicious-iot-device"></a>의심스러운 IoT 디바이스 조사

Defender for IoT 서비스 경고는 IoT 디바이스에서 의심스러운 활동에 관여하는 것으로 의심되거나 디바이스가 손상되었다는 표시가 나타나는 경우 명확한 표시를 제공합니다.

이 가이드에서는 제공된 조사 제안을 사용하여 조직에 발생할 수 있는 잠재적 위험을 확인하고, 수정 방법을 결정하며 나중에 발생할 수 있는 유사한 공격을 방지할 최상의 방법을 찾습니다.

> [!div class="checklist"]
> * 디바이스 데이터 찾기
> * kql 쿼리를 사용하여 조사

## <a name="how-can-i-access-my-data"></a>데이터에 액세스하는 방법

기본적으로 Defender for IoT는 Log Analytics 작업 영역에 보안 경고 및 권장 사항을 저장합니다. 원시 보안 데이터를 저장하도록 선택할 수도 있습니다.

데이터 스토리지에 대한 Log Analytics 작업 영역을 찾으려면 다음을 수행합니다.

1. IoT Hub를 엽니다.
1. **보안** 에서 **설정** 을 선택하고 **데이터 수집** 을 선택합니다.
1. Log Analytics 작업 영역의 구성 세부 정보를 변경합니다.
1. **저장** 을 선택합니다.

구성이 완료되면 다음을 수행하여 Log Analytics 작업 영역에 저장된 데이터에 액세스합니다.

1. IoT Hub에서 Defender for IoT 경고를 선택합니다.
1. **추가 조사** 를 선택합니다.
1. **이 경고가 있는 디바이스를 확인하려면 여기를 클릭하여 DeviceId 열을 보십시오** 를 선택합니다.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>의심스러운 IoT 디바이스에 대한 조사 단계

IoT 디바이스에 대한 인사이트 및 원시 데이터를 보려면 Log Analytics 작업 영역으로 이동하여 [데이터에 액세스](#how-can-i-access-my-data)합니다.

아래 샘플 KQL 쿼리를 참조하여 디바이스에서 경고와 활동을 조사하는 작업을 시작하세요.

### <a name="related-alerts"></a>관련 경고

다음 KQL 쿼리를 사용하여 비슷한 시간에 다른 경고가 트리거되었는지 확인할 수 있습니다.

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>액세스 권한이 있는 사용자

이 디바이스에 액세스할 수 있는 사용자를 확인하려면 다음 kql 쿼리를 사용합니다.

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
이 데이터를 사용하여 다음을 검색합니다.

- 어떤 사용자가 디바이스에 액세스할 수 있습니까?
- 액세스 권한이 있는 사용자에게 필요한 권한 수준이 있나요?

### <a name="open-ports"></a>포트 열기

디바이스에서 현재 사용 중이거나 사용된 포트를 확인하려면 다음 KQL 쿼리를 사용합니다.

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

이 데이터를 사용하여 다음을 검색합니다.

- 디바이스에서 현재 활성 중인 수신 대기 소켓은 무엇입니까?
- 현재 활성 상태인 수신 대기 소켓이 허용되나요?
- 디바이스에 연결된 의심스러운 원격 주소가 있나요?

### <a name="user-logins"></a>사용자 로그인

디바이스에 로그인한 사용자를 확인하려면 다음 KQL 쿼리를 사용합니다.

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

이 쿼리 결과를 사용하여 다음을 검색합니다.

- 어느 사용자가 디바이스에 로그인했나요?
- 로그인한 사용자는 로그인해야 하는 사용자인가요?
- 로그인한 사용자가 예상 IP 주소 또는 예상치 않은 IP 주소에서 연결했나요?

### <a name="process-list"></a>프로세스 목록

프로세스 목록이 예상과 같은지 알아보려면 다음 KQL 쿼리를 수행합니다.

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

이 쿼리 결과를 사용하여 다음을 검색합니다.

- 디바이스에서 의심스러운 프로세스가 실행되고 있습니까?
- 적절한 사용자가 프로세스를 실행했습니까?
- 명령줄 실행에 올바른 예상 인수가 포함되었나요?

## <a name="next-steps"></a>다음 단계

디바이스를 조사하고 위험을 더 잘 이해한 후에는 IoT 솔루션 보안 상태를 향상시키기 위해 [사용자 지정 경고 구성](quickstart-create-custom-alerts.md)을 고려할 수 있습니다. 디바이스 에이전트가 아직 없는 경우 결과를 향상시키려면 [보안 에이전트를 배포](how-to-deploy-agent.md)하거나 [기존 디바이스 에이전트의 구성을 변경](how-to-agent-configuration.md)하는 것이 좋습니다.
