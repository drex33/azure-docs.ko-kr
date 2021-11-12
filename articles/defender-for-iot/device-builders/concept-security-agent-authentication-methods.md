---
title: 보안 에이전트 인증 방법
description: Defender for IoT 서비스를 사용할 때 사용할 수 있는 다양한 인증 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 1d5aa69b5a44b07f90b063f741e65236f74e7dbd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331569"
---
# <a name="security-agent-authentication-methods"></a>보안 에이전트 인증 방법

이 문서에서는 IoT Hub로 인증하기 위해 AzureIoTSecurity와 함께 사용할 수 있는 다양한 인증 방법을 설명합니다.

IoT Hub에서 Defender for IoT에 온보딩된 각 디바이스에 대해 Defender-IoT-micro-agent가 필요합니다. Defender for IoT는 디바이스 인증에 두 가지 방법 중 하나를 사용할 수 있습니다. 기존 IoT 솔루션에 가장 적합한 방법을 선택하세요.

- SecurityModule 옵션
- 디바이스 옵션

## <a name="authentication-methods"></a>인증 방법

Defender for IoT AzureIoTSecurity 에이전트가 인증을 수행할 수 있는 두 가지 방법은 다음과 같습니다.

- **Defender-IoT-micro-agent** 인증 모드<br>
해당 에이전트는 디바이스 ID와 독립적으로 Defender-IoT-micro-agent ID를 사용하여 인증됩니다.
보안 에이전트가 Defender-IoT-micro-agent(대칭 키만)를 통해 전용 인증 방법을 사용하도록 하려면 이 인증 유형을 사용합니다.

- **Device** 인증 모드<br>
이 방법에서 보안 에이전트는 먼저 디바이스 ID로 인증합니다. 첫 번째 인증이 완료되면 Defender for IoT 에이전트는 디바이스의 인증 데이터와 REST API를 사용하여 IoT Hub에 **REST** 호출을 수행합니다. 그런 다음 Defender for IoT 에이전트는 IoT Hub에서 Defender-IoT-micro-agent 인증 방법 및 데이터를 요청합니다. 마지막 단계로, Defender for IoT 에이전트는 Defender for IoT 모듈에 대한 인증을 수행합니다.

보안 에이전트가 기존 디바이스 인증 방법(자체 서명된 인증서 또는 대칭 키)을 사용하도록 하려면 이 인증 유형을 사용합니다.

구성 방법을 알아보려면 [보안 에이전트 설치 매개 변수](#security-agent-installation-parameters)를 참조하세요.

## <a name="authentication-methods-known-limitations"></a>인증 방법에 대해 알려진 제한 사항

- **SecurityModule** 인증 모드는 대칭 키 인증만 지원합니다.
- CA 서명된 인증서는 **Device** 인증 모드에서 지원되지 않습니다.

## <a name="security-agent-installation-parameters"></a>보안 에이전트 설치 매개 변수

[보안 에이전트를 배포](how-to-deploy-agent.md)하는 경우 인증 세부 정보를 인수로 제공해야 합니다.
해당 인수는 다음 표에 문서화되어 있습니다.

|Linux 매개 변수 이름 | Windows 매개 변수 이름 | 축약형 매개 변수 |Description|옵션|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|인증 ID| **SecurityModule** 또는 **Device**|
|authentication-method|AuthenticationMethod|aum|인증 방법|**SymmetricKey** 또는 **SelfSignedCertificate**|
|file-path|FilePath|f|인증서 또는 대칭 키를 포함하는 파일의 절대 전체 경로입니다.| |
|host-name|HostName|hn|IoT Hub의 FQDN|예: ContosoIotHub.azure-devices.net|
|DEVICE_ID|DeviceId|di|디바이스 ID|예: MyDevice1|
|certificate-location-kind|CertificateLocationKind|cl|인증서 스토리지 위치|**Localfile** 또는 **Store**|
|

보안 에이전트 설치 스크립트를 사용하는 경우 다음 구성이 자동으로 수행됩니다. 보안 에이전트 인증을 수동으로 편집하려면 구성 파일을 편집합니다.

## <a name="change-authentication-method-after-deployment"></a>배포 후 인증 방법 변경

설치 스크립트를 사용하여 보안 에이전트를 배포하는 경우 구성 파일이 자동으로 생성됩니다.

배포 후 인증 방법을 변경하려면 구성 파일을 수동으로 편집해야 합니다.

### <a name="c-based-security-agent"></a>C# 기반 보안 에이전트

다음 매개 변수를 사용하여 _Authentication.config_ 를 편집합니다.

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C 기반 보안 에이전트

다음 매개 변수를 사용하여 _LocalConfiguration.json_ 을 편집합니다.

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>참고 항목

- [보안 에이전트 개요](security-agent-architecture.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
- [원시 보안 데이터 액세스](how-to-security-data-access.md)
