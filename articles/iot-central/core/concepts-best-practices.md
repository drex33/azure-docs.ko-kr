---
title: Azure IoT Central의 디바이스 개발 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 디바이스 연결에 대한 모범 사례를 간략하게 설명합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: a3cbfa17d3b063ddcef90820dc31a080a768cbcd
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493766"
---
# <a name="best-practices-for-device-development"></a>디바이스 개발을 위한 모범 사례

이러한 권장 사항은 IoT Central의 기본 제공 재해 복구 및 자동 크기 조정을 활용하기 위해 디바이스를 구현하는 방법을 보여줍니다.

다음 목록에서는 디바이스가 IoT Central 연결할 때의 상위 수준 흐름을 보여줍니다.

1. DPS를 사용하여 디바이스를 프로비전하고 디바이스 연결 문자열을 가져옵니다.

1. 연결 문자열을 사용하여 IoT Central의 내부 IoT Hub 엔드포인트에 연결합니다. IoT Central 애플리케이션과 데이터를 주고 받습니다.

1. 디바이스에서 연결 오류가 발생하면 오류 형식에 따라 연결을 다시 시도하거나 디바이스를 다시 프로비전합니다.

## <a name="use-dps-to-provision-the-device"></a>DPS를 사용하여 디바이스 프로비전

DPS를 사용하여 디바이스를 프로비전하려면 IoT Central 애플리케이션의 범위 ID, 자격 증명 및 디바이스 ID를 사용합니다. 자격 증명 형식에 대한 자세한 내용은 [X.509 그룹 등록](concepts-get-connected.md#x509-group-enrollment) 및 [SAS 그룹 등록](concepts-get-connected.md#sas-group-enrollment)을 참조하세요. 디바이스 ID에 대한 자세한 내용은 [디바이스 등록](concepts-get-connected.md#device-registration)을 참조하세요.

성공하면 DPS는 디바이스가 IoT Central 애플리케이션에 연결하는 데 사용할 수 있는 연결 문자열을 반환합니다. 프로비저닝 오류를 해결하려면 [디바이스의 프로비저닝 상태 확인](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)을 참조하세요.

디바이스는 이후 연결에 사용할 연결 문자열을 캐시할 수 있습니다. 그러나 [연결 실패를 처리](#handle-connection-failures)하도록 디바이스를 준비해야 합니다.

## <a name="connect-to-iot-central"></a>IoT Central에 연결

연결 문자열을 사용하여 IoT Central의 내부 IoT Hub 엔드포인트에 연결합니다. 연결을 사용하면 원격 분석을 IoT Central 애플리케이션으로 보내고, 속성 값을 IoT Central 애플리케이션과 동기화하고, IoT Central 애플리케이션에서 보낸 명령에 응답할 수 있습니다.

## <a name="handle-connection-failures"></a>연결 실패 처리

확장 또는 재해 복구를 위해 IoT Central은 기본 IoT 허브를 업데이트할 수 있습니다. 연결을 유지하려면 디바이스 코드가 새 IoT Hub 엔드포인트에 대한 연결을 설정하여 특정 연결 오류를 처리해야 합니다.

디바이스 연결 시 다음 오류 중 하나가 발생하면 DPS로 프로비저닝 단계를 다시 수행하여 새 연결 문자열을 가져와야 합니다. 이러한 오류는 디바이스가 사용하는 연결 문자열이 더 이상 유효하지 않음을 의미합니다.

- 연결할 수 없는 IoT Hub 엔드포인트입니다.
- 만료된 보안 토큰입니다.
- IoT Hub에서 디바이스를 사용할 수 없습니다.

디바이스에서 연결할 때 다음 오류가 발생하는 경우 백오프 전략을 사용하여 연결을 다시 시도해야 합니다. 이러한 오류는 디바이스가 사용하는 연결 문자열이 여전히 유효하지만 일시적인 조건으로 인해 디바이스 연결이 중지됨을 의미합니다.

- 운영자 차단 디바이스.
- 서비스의 내부 오류 500.

디바이스 오류 코드에 대해 자세히 알아보려면 [디바이스 연결 문제 해결](troubleshoot-connection.md)을 참조하세요.

## <a name="test-failover-capabilities"></a>테스트 장애 조치 기능

Azure CLI를 사용하여 장치 클라이언트 코드의 장애 조치 기능을 테스트할 수 있습니다. CLI 명령은 장치 등록을 일시적으로 다른 내부 IoT hub로 전환하는 방식으로 작동합니다. 장치가 여전히 원격 분석을 보내고 IoT Central 애플리케이션의 명령에 응답하는지 확인하여 장치 장애 조치가 작동하는지 확인할 수 있습니다.

장치에 대한 장애 조치 테스트를 실행하려면 다음 명령을 실행합니다.

```azurecli
az iot central device manual-failover \
    --app-id {Application ID of your IoT Central application} \
    --device-id {Device ID of the device you're testing} \
    --ttl-minutes {How to wait before moving the device back to it's original IoT hub}
```

> [!TIP]
> **애플리케이션 ID** 를 찾으려면, IoT Central 애플리케이션에서 **관리 > 애플리케이션** 으로 이동합니다.

명령이 성공하면 다음과 유사한 출력이 표시됩니다.

```output
Command group 'iot central device' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
{
  "hubIdentifier": "6bd4...bafa",
  "message": "Success! This device is now being failed over. You can check your device'’'s status using 'iot central device registration-info' command. The device will revert to its original hub at Tue, 18 May 2021 11:03:45 GMT. You can choose to failback earlier using device-manual-failback command. Learn more: https://aka.ms/iotc-device-test"
}
```

CLI 명령에 대해 자세히 알아보려면 [az iot central device manual-failover](/cli/azure/iot/central/device#az_iot_central_device_manual_failover)를 참조하세요.

이제 장치의 원격 분석이 IoT Central 애플리케이션에 도달하고 있는지 확인할 수 있습니다.

다양한 프로그래밍 언어에서 장애 조치를 처리하는 샘플 장치 코드를 보려면 [IoT 고가용성 클라이언트](https://github.com/iot-for-all/iot-central-high-availability-clients)를 참조하세요.

## <a name="next-steps"></a>다음 단계

몇 가지 제안되는 다음 단계는 다음과 같습니다.

- [자습서: 클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)에서 SAS 토큰을 사용하는 방법을 보여 주는 샘플 코드를 검토합니다.
- [IoT Central 애플리케이션용 Node.js 디바이스 SDK를 사용하여 X.509 인증서로 디바이스를 연결하는 방법](how-to-connect-devices-x509.md)을 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
- [Azure IoT Central 애플리케이션에서 새 IoT 디바이스 유형을 정의](./howto-set-up-template.md)하는 방법을 알아봅니다.
- [Azure IoT Edge 디바이스 및 Azure IoT Central](./concepts-iot-edge.md)을 참조합니다.
