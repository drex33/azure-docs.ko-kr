---
title: 보안 에이전트 시작 문제 해결(Linux)
description: Linux 용 IoT 보안 에이전트에 대해 Microsoft Defender를 사용 하 여 작업을 해결 합니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: c0e85e0628599af88d86c567b559ca6c721ab732
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306000"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>보안 에이전트 문제 해결 가이드(Linux)

이 문서에서는 보안 에이전트 시작 프로세스에서 발생할 수 있는 문제를 해결하는 방법을 설명합니다.

IoT 용 Microsoft Defender 에이전트는 설치 직후에 자동으로 시작 됩니다. 에이전트 시작 프로세스에는 로컬 구성을 읽고, Azure IoT Hub에 연결하고, 원격 쌍 구성을 검색하는 작업이 포함됩니다. 해당 단계 중 하나라도 실패하면 보안 에이전트가 실패할 수 있습니다.

이 문제 해결 가이드에서 다음을 수행하는 방법을 알아봅니다.

- 보안 에이전트가 실행 중인지 확인하기
- 보안 에이전트 오류 가져오기
- 보안 에이전트 오류 이해 및 수정

## <a name="validate-if-the-security-agent-is-running"></a>보안 에이전트가 실행 중인지 확인하기

1. 보안 에이전트가 실행되고 있는지 확인하려면 에이전트를 설치한 후 몇 분 정도 기다렸다가 다음 명령을 실행합니다.
     <br>

    **C 에이전트**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C# 에이전트**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. 명령이 빈 줄을 반환하는 경우 보안 에이전트를 성공적으로 시작할 수 없습니다.

## <a name="force-stop-the-security-agent"></a>보안 에이전트 강제 중지

보안 에이전트를 시작할 수 없는 경우 다음 명령을 사용하여 에이전트를 중지하고 아래 오류 표를 계속 진행합니다.

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>보안 에이전트 오류 가져오기

1. 다음 명령을 실행하여 보안 에이전트 오류를 검색합니다.

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. 보안 에이전트 오류 가져오기 명령은 Defender for IoT 에이전트에 의해 생성된 모든 로그를 검색합니다. 다음 표를 사용하여 오류를 이해하고 올바른 수정 단계를 수행하세요.

> [!Note]
> 오류 로그는 시간순으로 표시됩니다. 수정에 도움이 되도록 각 오류의 타임스탬프를 확인합니다.

## <a name="restart-the-agent"></a>에이전트 다시 시작

1. 보안 에이전트 오류를 찾아 수정한 후 다음 명령을 실행하여 에이전트를 다시 시작해 보세요.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. 에이전트가 시작 프로세스를 계속 진행하지 못하는 경우 이전 프로세스를 반복하여 중지를 검색하고 오류를 검색합니다.

## <a name="understand-security-agent-errors"></a>보안 에이전트 오류 이해

대부분의 보안 에이전트 오류는 다음과 같은 형식으로 표시됩니다.

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| 오류 코드 | 오류 하위 코드 | 오류 세부 정보 | C 재구성 | C# 재구성 |
|--|--|--|--|--|
| 로컬 구성 | 누락된 구성 | 로컬 구성 파일에 구성이 없습니다. 오류 메시지에는 누락된 키가 명시되어야 합니다. | /var/LocalConfiguration.json 파일에 누락된 키를 추가합니다. 자세한 내용은 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md)를 참조하세요. | General.config 파일에 누락된 키를 추가합니다. 자세한 내용은 [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md)를 참조하세요. |
| 로컬 구성 | 구성을 구문 분석할 수 없음 | 구성 값을 구문 분석할 수 없습니다. 오류 메시지에는 구문 분석할 수 없는 키가 명시되어야 합니다. 값이 필요한 형식에 없거나 값이 범위를 벗어났기 때문에 구성 값을 구문 분석할 수 없습니다. | LocalConfiguration 스키마와 일치하도록 /var/LocalConfiguration.json 파일에서 키 값을 수정합니다. 자세한 내용은 [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md)를 참조하세요. | 스키마와 일치하도록 General.config 파일의 키 값을 수정합니다. 자세한 내용은 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md)를 참조하세요. |
| 로컬 구성 | 파일 형식 | 구성 파일을 구문 분석하지 못했습니다. | 구성 파일이 손상되었습니다. 에이전트를 다운로드한 후 다시 설치합니다. | - |
| 원격 구성 | 제한 시간 | 에이전트가 제한 시간 내에 azureiotsecurity 모듈 쌍을 가져올 수 없습니다. | 인증 구성이 올바른지 확인하고 다시 시도합니다. | 에이전트가 제한 시간 내에 azureiotsecurity 모듈 쌍을 가져올 수 없습니다. 인증 구성이 올바른지 확인하고 다시 시도합니다. |
| 인증 | 파일이 없음 | 지정된 경로에 파일이 존재하지 않습니다. | 파일이 지정된 경로에 있는지 확인하거나 **LocalConfiguration.json** 파일로 이동하여 **FilePath** 구성을 변경합니다. | 파일이 지정된 경로에 있는지 확인하거나 **Authentication.config** 파일로 이동하여 **filePath** 구성을 변경합니다. |
| 인증 | 파일 사용 권한 | 에이전트에게 파일을 열 수 있는 권한이 없습니다. | **asciotagent** 사용자에게 지정된 경로에 있는 파일에 대한 읽기 권한을 부여합니다. | 파일에 액세스할 수 있는지 확인합니다. |
| 인증 | 파일 형식 | 지정한 파일이 올바른 형식이 아닙니다. | 파일이 올바른 형식인지 확인합니다. 지원되는 파일 형식은 .pfx 및 .pem입니다. | 파일이 유효한 인증서 파일인지 확인합니다. |
| 인증 | 권한 없음 | 에이전트가 지정된 자격 증명을 사용하여 IoT Hub에 대해 인증할 수 없습니다. | LocalConfiguration 파일에서 인증 구성의 유효성을 검사하고, 인증 구성을 진행하고, 모든 세부 정보가 올바른지 확인하고, 파일의 비밀이 인증된 ID와 일치하는지 확인합니다. | Authentication.config에서 인증 구성의 유효성을 검사하고, 인증 구성을 진행하고, 모든 세부 정보가 올바른지 확인한 다음 파일의 비밀이 인증된 ID와 일치하는지 확인합니다. |
| 인증 | 찾을 수 없음 | 디바이스/모듈을 찾았습니다. | 인증 구성 유효성 검사 - 호스트 이름이 올바르고, 디바이스가 IoT Hub에 있으며, azureiotsecurity 쌍 모듈이 있는지 확인합니다. | 인증 구성 유효성 검사 - 호스트 이름이 올바르고, 디바이스가 IoT Hub에 있으며, azureiotsecurity 쌍 모듈이 있는지 확인합니다. |
| 인증 | 누락된 구성 | *Authentication.config* 파일에 구성이 없습니다. 오류 메시지에는 누락된 키가 명시되어야 합니다. | 누락된 키를 *LocalConfiguration.json* 파일에 추가합니다. | 누락된 키를 *Authentication.config* 파일에 추가합니다. 자세한 내용은 [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md)를 참조하세요. |
| 인증 | 구성을 구문 분석할 수 없음 | 구성 값을 구문 분석할 수 없습니다. 오류 메시지에는 구문 분석할 수 없는 키가 명시되어야 합니다. 값이 필요한 형식이 아니거나 값이 범위를 벗어났기 때문에 구성 값을 구문 분석할 수 없습니다. | **LocalConfiguration.json** 파일에서 키 값을 수정합니다. | 스키마와 일치하도록 **Authentication.config** 파일의 키 값을 수정합니다. 자세한 내용은 [cs-localconfig-reference](azure-iot-security-local-configuration-c.md)를 참조하세요.|

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md) 읽기
- Defender for IoT [디바이스 빌더용 에이전트 기반 솔루션](architecture-agent-based.md)에 대해 자세히 알아보기
- Defender for IoT [서비스](quickstart-onboard-iot-hub.md) 사용
- Defender for IoT 서비스 [Defender for IoT FAQ](resources-agent-frequently-asked-questions.md)를 읽습니다.
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [추천 사항](concept-recommendations.md)을 살펴봅니다.
- 보안 [경고](concept-security-alerts.md) 이해
