---
title: Defender for IoT 보안 에이전트 로컬 구성(C#)
description: Defender for IoT 보안 서비스인 C#용 보안 에이전트 로컬 구성 파일에 대해 알아봅니다.
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: bfd70e8576bb139feb39d4a15e7076da0a4e0131
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293595"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>로컬 구성 파일 이해(C# 에이전트)

Defender for IoT 보안 에이전트는 로컬 구성 파일의 구성을 사용합니다.

보안 에이전트는 에이전트가 실행되기 시작할 때 구성 파일을 한 번 읽습니다. 로컬 구성 파일에 있는 구성에는 인증 구성과 기타 에이전트 관련 구성이 모두 포함됩니다.

C# 보안 에이전트는 여러 구성 파일을 사용합니다.

- **General.config** - 에이전트 관련 구성입니다.
- **Authentication.config** - 인증 관련 구성입니다(인증 세부 정보 포함).
- **SecurityIotInterface.config** - IoT 관련 구성입니다.

구성 파일에는 기본 구성이 포함되어 있습니다. 에이전트를 설치하는 동안 인증 구성이 채워지고, 에이전트가 다시 시작될 때 구성 파일이 변경됩니다.

## <a name="configuration-file-location"></a>구성 파일 위치

Linux의 경우:

- 운영 체제 구성 파일은 `/var/ASCIoTAgent`에 있습니다.

Windows의 경우:

- 운영 체제 구성 파일은 보안 에이전트의 디렉터리 내에 있습니다.

### <a name="generalconfig-configurations"></a>General.config 구성

| 구성 이름 | 가능한 값 | 세부 정보 |
|:-----------|:---------------|:--------|
| agentId | GUID | 에이전트 고유 식별자입니다. |
| readRemoteConfigurationTimeout | TimeSpan | IoT Hub에서 원격 구성을 가져오는 기간입니다. 에이전트가 지정된 시간 내에 구성을 가져올 수 없는 경우 작업이 시간 초과됩니다.|
| schedulerInterval | TimeSpan | 내부 스케줄러 간격입니다. |
| producerInterval | TimeSpan | 이벤트 생산자 작업자 간격입니다. |
| consumerInterval | TimeSpan | 이벤트 소비자 작업자 간격입니다. |
| highPriorityQueueSizePercentage | 0 < number < 1 | 우선 순위가 높은 메시지 전용 총 캐시 부분입니다. |
| logLevel | ‘Off’, ‘Fatal’, ‘Error’, ‘Warning’, ‘Information’, ‘Debug’  | 이 심각도 이상의 로그 메시지는 디버그 콘솔에 로그됩니다(Linux의 Syslog). |
| fileLogLevel |  ‘Off’, ‘Fatal’, ‘Error’, ‘Warning’, ‘Information’, ‘Debug’| 이 심각도 이상의 로그 메시지는 파일에 로그됩니다(Linux의 Syslog). |
| diagnosticVerbosityLevel | ‘None’, ‘Some’, ‘All’, | 진단 이벤트의 자세한 표시 수준입니다. None - 진단 이벤트가 전송되지 않습니다. Some - 중요도가 높은 진단 이벤트만 전송됩니다. All - 모든 로그가 진단 이벤트로 전송됩니다. |
| logFilePath | Path to file | fileLogLevel > Off인 경우 로그는 이 파일에 로그됩니다. |
| defaultEventPriority | ‘High’, ‘Low’, ‘Off’ | 기본 이벤트 우선 순위입니다. |

### <a name="generalconfig-example"></a>General.config 예제

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| 구성 이름 | 가능한 값 | 세부 정보 |
|:-----------|:---------------|:--------|
| moduleName | 문자열 | Defender-IoT-micro-agent ID의 이름입니다. 이 이름은 디바이스의 모듈 ID 이름과 일치해야 합니다. |
| deviceId | 문자열 | 디바이스의 Azure IoT Hub 등록 ID입니다. |
| schedulerInterval | TimeSpan string | 내부 스케줄러 간격입니다. |
| gatewayHostname | 문자열 | Azure Iot Hub의 호스트 이름입니다. 일반적으로 \<my-hub\> .azure-devices.net |
| filePath | string - path to file | 인증 암호를 포함하는 파일의 경로입니다.|
| type | ‘SymmetricKey’, ‘SelfSignedCertificate’ | 인증을 위한 사용자 비밀입니다. 사용자 비밀이 대칭 키인 경우 *SymmetricKey* 를 선택하고, 비밀이 자체 서명된 인증서인 경우 자체 서명된 인증서를 선택합니다. |
| identity | ‘DPS’, ‘Module’, ‘Device’ | 인증 ID - DPS를 통해 인증하는 경우 DPS, 모듈 자격 증명을 사용하여 인증하는 경우 Module, 디바이스 자격 증명을 사용하여 인증하는 경우 Device.
| certificateLocationKind |  ‘LocalFile’, ‘Store’ | 인증서가 파일에 저장된 경우 LocalFile, 인증서가 인증서 저장소에 있는 경우 Store. |
| idScope | 문자열 | DPS의 ID 범위입니다. |
| registrationId | 문자열  | DPS 디바이스 등록 ID입니다. |
|

### <a name="authenticationconfig-example"></a>Authentication.config 예제

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| 구성 이름 | 가능한 값 | 세부 정보 |
|:-----------|:---------------|:--------|
| transportType | ‘Ampq’ ‘Mqtt’ | IoT Hub 전송 유형입니다. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config 예제

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md) 읽기
- Defender for IoT [에이전트 기반 솔루션 아키텍처](architecture-agent-based.md)에 관해 자세히 알아보기
- Defender for IoT [서비스](quickstart-onboard-iot-hub.md) 사용
- Defender for IoT 서비스 [FAQ](resources-agent-frequently-asked-questions.md) 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- 보안 [경고](concept-security-alerts.md) 이해
