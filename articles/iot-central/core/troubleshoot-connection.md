---
title: Azure IoT Central에서 디바이스 연결 문제 해결 | Microsoft Docs
description: 디바이스의 데이터가 IoT Central에 표시되지 않는 문제 해결
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: a929e5d4bccc3a6b2d27125de6aad4472364d67e
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179149"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>디바이스의 데이터가 Azure IoT Central에 표시되지 않는 문제 해결 

이 문서를 통해 디바이스가 IoT Central로 보내는 데이터가 애플리케이션에 표시되지 않는 이유를 알 수 있습니다.

조사해야 할 두 가지 주요 영역은 다음과 같습니다.

- 디바이스 연결 문제
  - 디바이스에 잘못된 자격 증명이 있는 것과 같은 인증 문제
  - 네트워크 연결 문제
  - 디바이스가 승인되지 않았거나 차단됨
- 디바이스 페이로드 셰이프 문제

이 문제 해결 가이드는 디바이스 연결 문제 및 디바이스 페이로드 셰이프 문제에 초점을 맞춥니다.

## <a name="device-connectivity-issues"></a>디바이스 연결 문제

이 섹션은 데이터가 IoT Central에 도달하는지 여부를 확인하는 데 도움이 됩니다.

아직 설치하지 않은 경우 `az cli` 도구 및 `azure-iot` 확장을 설치합니다.

`az cli`를 설치하는 방법에 대해 알아보려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

`azure-iot` 확장을 [설치](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation)하려면 다음 명령을 실행합니다.

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> 확장 명령을 처음 실행할 때 `uamqp` 라이브러리를 설치하라는 메시지가 표시될 수 있습니다.

`azure-iot` 확장을 설치했으면 디바이스를 시작하여 보내는 메시지가 IoT Central로 이동하는지 확인합니다.

다음 명령을 사용하여 IoT Central 애플리케이션이 있는 구독에 로그인합니다.

```azurecli
az login
az account set --subscription <your-subscription-id>
```

디바이스가 보내는 원격 분석을 모니터링하려면 다음 명령을 사용합니다.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

디바이스가 IoT Central에 성공적으로 연결되면 다음과 유사한 출력이 표시됩니다.

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

디바이스가 IoT Central과 교환 중인 속성 업데이트를 모니터링하려면 다음 미리 보기 명령을 사용합니다.

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

디바이스가 속성 업데이트를 성공적으로 전송하면 다음과 유사한 출력이 표시됩니다.

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

터미널에 데이터가 표시되는 경우 데이터가 IoT Central 애플리케이션까지 도달하게 됩니다.

몇 분 후에도 데이터가 표시되지 않으면 출력이 중단된 경우를 위해 키보드에서 `Enter` 또는 `return` 키를 누릅니다.

여전히 터미널에 데이터가 표시되지 않으면 디바이스에 네트워크 연결 문제가 있거나 IoT Central로 데이터를 올바르게 전송하지 못할 가능성이 높습니다.

### <a name="check-the-provisioning-status-of-your-device"></a>디바이스의 프로비저닝 상태 확인

데이터가 모니터에 표시되지 않는 경우 다음 명령을 실행하여 디바이스의 프로비저닝 상태를 확인합니다.

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

다음 출력은 연결에서 차단된 디바이스의 예제를 보여 줍니다.

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| 디바이스 프로비저닝 상태 | Description | 가능한 완화 방법 |
| - | - | - |
| 프로비전됨 | 즉시 인식할 수 있는 문제가 없습니다. | 해당 없음 |
| 등록됨 | 디바이스가 아직 IoT Central에 연결되지 않았습니다. | 디바이스 로그에 연결 문제가 있는지 확인합니다. |
| 차단 | 디바이스가 IoT Central에 연결되지 못하도록 차단되었습니다. | 디바이스가 IoT Central 애플리케이션에 연결되지 못하도록 차단되었습니다. IoT Central에서 디바이스의 차단을 해제하고 다시 시도합니다. 자세히 알아보려면 [디바이스 차단](concepts-get-connected.md#device-status-values)을 참조하세요. |
| 승인되지 않음 | 디바이스가 승인되지 않았습니다. | IoT Central 애플리케이션에 연결할 디바이스가 승인되지 않았습니다. IoT Central에서 디바이스를 승인하고 다시 시도합니다. 자세히 알아보려면 [디바이스 승인](concepts-get-connected.md#device-registration)을 참조하세요. |
| 연결 안 됨 | 디바이스가 디바이스 템플릿과 연결되어 있지 않습니다. | IoT Central이 데이터를 구문 분석하는 방법을 알 수 있도록 디바이스를 디바이스 템플릿에 연결합니다. |

자세히 알아보려면 [디바이스 상태 코드](concepts-get-connected.md#device-status-values)를 참조하세요.

### <a name="error-codes"></a>오류 코드

데이터가 `monitor-events`에 표시되지 않는 이유를 여전히 진단할 수 없는 경우 다음 단계는 디바이스에서 보고된 오류 코드를 찾아내는 것입니다.

디바이스에서 디버깅 세션을 시작하거나 디바이스에서 로그를 수집합니다. 디바이스에서 보고하는 오류 코드를 확인합니다.

다음 표에서는 일반적인 오류 코드와 완화할 수 있는 작업을 보여 줍니다.

인증 흐름과 관련된 문제가 발생하는 경우:

| 오류 코드 | Description | 가능한 완화 방법 |
| - | - | - |
| 400 | 요청 분문이 잘못되었습니다. 예를 들어 요청 본문을 구문 분석할 수 없거나 개체의 유효성을 검사할 수 없습니다. | 증명 흐름의 일부로 올바른 요청 본문을 보내고 있는지 확인하거나 디바이스 SDK를 사용합니다. |
| 401 | 권한 부여 토큰의 유효성을 검사할 수 없습니다. 예를 들어 만료되었거나 요청의 URI에 적용되지 않습니다. 이 오류 코드는 TPM 증명 흐름의 일부로 디바이스에도 반환됩니다. | 디바이스에 올바른 자격 증명이 있는지 확인합니다. |
| 404 | Device Provisioning Service 인스턴스 또는 등록과 같은 리소스가 없습니다. | [고객 지원팀을 통해 티켓을 제출](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)합니다. |
| 412 | 요청의 `ETag`가 RFC7232에 따라 기존 리소스의 `ETag`와 일치하지 않습니다. | [고객 지원팀을 통해 티켓을 제출](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)합니다. |
| 429 | 서비스로 인해 작업이 제한되고 있습니다. 특정 서비스 제한은 [IoT Hub Device Provisioning Service 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)을 참조하세요. | 메시지 빈도를 줄이고 더 많은 디바이스로 책임을 분담합니다. |
| 500 | 내부 오류가 발생했습니다. | [고객 지원팀을 통해 티켓을 제출](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)하여 더 유용하게 사용할 수 있는지 확인할 수 있습니다. |

### <a name="detailed-authorization-error-codes"></a>자세한 권한 부여 오류 코드

| 오류 | 하위 오류 코드 | 참고 |
| - | - | - |
| 401 권한 없음 | 401002 | 장치에서 잘못 되었거나 만료 된 자격 증명을 사용 하 고 있습니다. 이 오류는 DPS에서 보고 됩니다. |
| 401 권한 없음 | 400209 | 장치가 운영자의 승인을 기다리고 있거나 운영자에 의해 차단 되었습니다. |
| 401 IoTHubUnauthorized |  | 장치에서 만료 된 보안 토큰을 사용 하 고 있습니다. 이 오류는 IoT Hub에서 보고 됩니다. |
| 401 IoTHubUnauthorized | DEVICE_DISABLED | 장치가이 IoT hub에서 사용 하지 않도록 설정 되어 있고 다른 IoT hub로 이동 했습니다. 디바이스를 다시 프로비전합니다. |
| 401 IoTHubUnauthorized | DEVICE_BLOCKED | 운영자가 이 디바이스를 차단했습니다. |



### <a name="file-upload-error-codes"></a>파일 업로드 오류 코드

다음은 디바이스가 클라우드에 파일을 업로드하려고 할 때 표시될 수 있는 일반적인 오류 코드 목록입니다. 디바이스에서 파일을 업로드하려면 먼저 애플리케이션에서 [디바이스 파일 업로드](howto-configure-file-uploads.md)를 구성해야 합니다.

| 오류 코드 | Description | 가능한 완화 방법 |
| - | - | - |
| 403006  | 동시 파일 업로드 작업 수를 초과했습니다. 각 디바이스 클라이언트는 동시 파일 업로드 10개로 제한됩니다. | 디바이스가 파일 업로드 작업이 완료되었음을 IoT Central에 즉시 알리는지 확인합니다. 이 방법으로 문제가 해결되지 않으면 요청 시간 초과를 줄여 보세요. |

## <a name="payload-shape-issues"></a>페이로드 셰이프 문제

디바이스가 IoT Central로 데이터를 전송하는 것을 확인한 후 다음 단계는 디바이스가 올바른 형식으로 데이터를 전송하고 있는지 확인하는 것입니다.

IoT Central에 디바이스 데이터가 표시되지 않는 일반적인 문제에는 두 가지 주요 범주가 있습니다.

- 디바이스 데이터와 디바이스 템플릿 불일치:
  - 오타 또는 대/소문자를 일치 문제와 같은 이름 지정이 일치하지 않습니다.
  - 스키마가 디바이스 템플릿에 정의되어 있지 않은 모델링되지 않은 속성입니다.
  - 템플릿에서 `boolean`으로 정의된 형식과 같이 스키마가 일치하지 않지만 데이터는 문자열입니다.
  - 동일한 원격 분석 이름이 여러 인터페이스에 정의되지만 디바이스가 IoT 플러그 앤 플레이 규격이 아닙니다.
- 데이터 셰이프가 잘못된 JSON입니다. 자세히 알아보려면 [원격 분석, 속성, 명령 페이로드](concepts-telemetry-properties-commands.md)를 참조하세요.

문제가 있는 범주를 검색하려면 시나리오에 가장 적합한 명령을 실행합니다.

- 원격 분석의 유효성을 검사하려면 미리 보기 명령을 사용합니다.

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- 속성 업데이트의 유효성을 검사하려면 미리 보기 명령을 사용합니다.

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

`validate` 명령을 처음 실행할 때 `uamqp` 라이브러리를 설치하라는 메시지가 표시될 수 있습니다.

다음 출력에는 유효성 검사 명령의 오류 및 경고 메시지의 예가 표시됩니다.

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

GUI를 사용하려면 IoT Central **원시 데이터** 보기를 사용하여 모델링되지 않은 것이 있는지 확인합니다. **원시 데이터** 보기는 디바이스가 잘못된 형식의 JSON을 보내고 있는지 여부를 감지하지 못합니다.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="원시 데이터 보기 스크린샷":::

문제가 감지되면 디바이스 펌웨어를 업데이트하거나 이전에 모델링되지 않은 데이터를 모델링하는 새 디바이스 템플릿을 만들어야 할 수 있습니다.

데이터를 올바르게 모델링하는 새 템플릿을 만들도록 선택한 경우 이전 템플릿에서 새 템플릿으로 디바이스를 마이그레이션합니다. 자세히 알아보려면 [Azure IoT Central 애플리케이션에서 디바이스 관리](howto-manage-devices-individually.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

도움이 더 필요하면 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)의 Azure 전문가에게 문의하세요. 또는 [Azure 지원 티켓](https://portal.azure.com/#create/Microsoft.Support)을 제출할 수 있습니다.

자세한 내용은 [Azure IoT 지원 및 도움말 옵션](../../iot-fundamentals/iot-support-help.md)을 참조하세요.
