---
title: Windows 대칭 키를 사용하여 IoT Edge 디바이스 만들기 및 프로비전 - Azure IoT Edge | Microsoft Docs
description: 대칭 키 증명을 사용하여 디바이스 프로비저닝 서비스에서 Azure IoT Edge에 대한 대규모 프로비저닝 Windows 디바이스 테스트
author: kgremban
ms.author: kgremban
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9e6cae92bb0dd4f8a9eb47966a5dc1e77309de30
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936609"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-symmetric-keys"></a>대칭 키를 사용하여 Windows 대규모로 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 대칭 키를 사용하여 하나 이상의 Windows IoT Edge 디바이스를 자동 프로비전하는 엔드 투 엔드 지침을 제공합니다. Azure IoT Hub DPS(디바이스 [프로비저닝 서비스)를 Azure IoT Edge 디바이스를 자동으로 프로비전할](../iot-dps/index.yml) 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비전 개요를](../iot-dps/about-iot-dps.md#provisioning-process) 검토하세요.

>[!NOTE]
>Windows 컨테이너를 사용하는 Azure IoT Edge는 Azure IoT Edge 버전 1.2부터 지원되지 않습니다.
>
>Window 디바이스에서 IoT Edge를 실행하는 새로운 방법을 사용하려면 [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md)를 참조하세요.
>
>Windows Linux용 Azure IoT Edge를 사용하려는 경우 [해당하는 방법 가이드의](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)단계를 따르면 됩니다.

작업은 다음과 같습니다.

1. 단일 디바이스에 대한 **개별 등록** 또는 디바이스 집합에 대한 **그룹 등록을** 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub에 연결합니다.

대칭 키 증명은 디바이스 프로비전 서비스 인스턴스를 사용하여 디바이스를 인증하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](../iot-dps/concepts-tpm-attestation.md) 또는 [X.509 인증서](../iot-dps/concepts-x509-attestation.md)를 사용하는 디바이스 증명은 더욱 안전하며, 보다 엄격한 보안 요구 사항에 사용해야 합니다. <!-- note links here; they will break -->

## <a name="prerequisites"></a>사전 요구 사항

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge 설치

IoT Edge 디바이스가 될 물리적 또는 가상 Windows 디바이스입니다.

각 디바이스를 식별하려면 *고유한* **등록 ID를** 정의해야 합니다. 디바이스에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다. 이 예제에서는 등록 ID에 대한 `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` 문자열을 형성하는 MAC 주소와 일련 번호의 조합을 사용합니다. 유효한 문자는 소문자 영숫자 및 대시(`-`)입니다.

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

런타임이 디바이스에 설치되면 디바이스 프로비전 서비스에 연결하고 IoT Hub 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값
* 만든 디바이스 **등록 ID**
* 개별 등록의 **기본 키** 또는 그룹 등록을 사용하는 장치의 [파생 키](#derive-a-device-key) 중 하나입니다.

1. 관리자 모드에서 PowerShell 창을 엽니다. PowerShell(x86)이 아닌 IoT Edge를 설치하는 경우 PowerShell의 AMD64 세션을 사용해야 합니다.

1. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용하여 수동으로 프로비저닝하므로 대칭 키 인증을 사용하여 자동 프로비저닝하려면 `-DpsSymmetricKey` 플래그를 사용합니다.

   `paste_scope_id_here`, `paste_registration_id_here` 및`paste_symmetric_key_here`의 자리 표시자 값을 앞에서 수집한 데이터로 바꿉니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here -SymmetricKey paste_symmetric key_here
   ```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하고 디바이스에 IoT Edge 모듈 배포를 시작할 수 있습니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

디바이스 프로비저닝 서비스에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

디바이스 프로비저닝 서비스에서 만든 그룹 등록이 사용되었는지 확인할 수 있습니다. Azure Portal 디바이스 프로비저닝 서비스 인스턴스로 이동합니다. 만든 그룹 등록에 대한 등록 세부 정보를 엽니다. **등록 레코드** 탭으로 이동하여 해당 그룹에 등록된 모든 디바이스를 봅니다.

---

디바이스에서 다음 명령을 사용하여 IoT Edge가 성공적으로 설치되고 시작되는지 확인합니다.

IoT Edge 서비스의 상태를 확인합니다.

```powershell
Get-Service iotedge
```

서비스 로그를 검사합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

## <a name="next-steps"></a>다음 단계

디바이스 프로비전 서비스 등록 프로세스를 사용하면 새 디바이스를 프로비전하는 동시에 디바이스 ID 및 디바이스 쌍 태그를 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.
