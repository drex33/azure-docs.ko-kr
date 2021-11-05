---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3e07cbe5b8b90ff236b9cdc0d12f31af601a253a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842705"
---
## <a name="register-your-device"></a>디바이스 등록

사용자의 기본 설정에 따라 **Azure Portal**, **Visual Studio Code** 또는 **Azure CLI** 를 사용 하 여 장치를 등록할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 IoT 허브에서 IoT Edge 디바이스는 에지가 사용 설정되지 않은 IoT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 디바이스 추가** 를 선택합니다.

   ![Azure Portal에서 IoT Edge 디바이스 추가](./media/iot-edge-register-device-x509/portal-add-iot-edge-device.png)

1. **디바이스 만들기** 페이지에서 다음 정보를 입력합니다.

   * 설명 디바이스 ID를 만듭니다. 나중에 사용할 수 있도록이 장치 ID를 기록해 둡니다.
   * 인증 유형으로 **X.509 자체 서명** 을 선택합니다.
   * 기본 및 보조 ID 인증서 지문을 제공합니다. 지문 값은 SHA-1 해시의 경우 40(16진수), SHA-256 해시의 경우 64(16진수)입니다.

1. **저장** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

현재 Visual Studio Code용 Azure IoT 확장은 x.509 인증서를 통한 디바이스 등록을 지원하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled --auth-method x509_thumbprint --primary-thumbprint primary_SHA_thumbprint_here --secondary-thumbprint secdonary_SHA_thumbprint_here
   ```

이 명령에는 다음 매개 변수가 포함됩니다.

* `--device-id` 또는 `-d`: IoT 허브에 고유한 설명 이름을 제공합니다. 다음 섹션에서 사용할 수 있도록 디바이스 ID를 기록해 둡니다.
* `hub-name` 또는 `-n`: IoT 허브의 이름을 제공합니다.
* `--edge-enabled` 또는 `--ee`: 디바이스가 IoT Edge 디바이스임을 선언합니다.
* `--auth-method` 또는 `--am`: 디바이스에서 사용하려는 인증 유형을 선언합니다. 이 경우에는 x.509 인증서 지문을 사용합니다.
* `--primary-thumbprint` 또는 `--ptp`: 기본 키로 사용할 x.509 인증서 지문을 제공합니다.
* `--secondary-thumbprint` 또는 `--stp`: 보조 키로 사용할 x.509 인증서 지문을 제공합니다.

---

이제 IoT Hub에 등록 된 장치가 있으므로 IoT Edge 런타임의 설치 및 프로 비전을 완료 하는 데 사용 하는 정보를 검색 합니다.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>등록 된 장치 보기 및 프로 비전 정보 검색

X.509 인증서 인증을 사용 하는 장치에는 IoT hub 이름, 장치 이름 및 인증서 파일을 사용 하 여 IoT Edge 런타임의 설치 및 프로 비전을 완료 해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![Azure Portal에서 IoT 허브 내 모든 IoT Edge 디바이스를 확인할 수 있습니다.](./media/iot-edge-register-device-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code를 통해 x.509 인증서를 사용 하는 장치 등록은 지원 되지 않지만 필요한 경우에도 IoT Edge 장치를 볼 수 있습니다.

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![VS Code를 사용하여 IoT 허브 내 모든 IoT Edge 디바이스 보기](./media/iot-edge-register-device-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

---
