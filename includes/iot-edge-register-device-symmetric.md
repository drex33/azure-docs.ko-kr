---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 80328e4ec6ea06937a30c94a0e0f854e03eb81da
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851948"
---
## <a name="register-your-device"></a>디바이스 등록

기본 설정에 따라 **Azure Portal**, **Visual Studio Code** 또는 **Azure CLI** 사용하여 디바이스를 등록할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 IoT 허브에서 IoT Edge 디바이스는 에지가 사용 설정되지 않은 IoT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 디바이스 추가** 를 선택합니다.

   ![Azure Portal에서 IoT Edge 디바이스 추가](./media/iot-edge-register-device-symmetric/portal-add-iot-edge-device.png)

1. **디바이스 만들기** 페이지에서 다음 정보를 입력합니다.

   * 설명 디바이스 ID를 만듭니다. 나중에 사용할 수 있도록 이 디바이스 ID를 기록해 둡다.
   * 인증 유형으로 **대칭 키** 를 선택합니다.
   * 인증 키를 자동으로 생성하고 허브에 새 디바이스를 연결하려면 기본 설정을 사용합니다.

1. **저장** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 해당 작업이 수행되려면 Azure 계정에 로그인하고 허브를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.
1. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다.

   ![Azure IoT Hub 디바이스 확장 섹션](./media/iot-edge-register-device-symmetric/azure-iot-hub-devices.png)

1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.
1. **IoT Hub 선택** 을 선택합니다.
1. Azure 계정에 로그인하지 않은 경우, 프롬프트를 따라 로그인합니다.
1. Azure 구독을 선택합니다.
1. IoT Hub를 선택합니다.

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code를 사용하여 새 디바이스 등록

1. Visual Studio Code Explorer에서 **Azure IoT Hub** 섹션을 확장합니다.
1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다.
1. **IoT Edge 디바이스 만들기** 를 선택합니다.
1. 열리는 텍스트 상자에 디바이스 ID를 입력합니다.

출력 화면에 명령의 결과가 표시됩니다. 디바이스 정보가 인쇄되며, 여기에는 입력한 **deviceId** 와 물리적 디바이스를 IoT Hub에 연결하는 데 사용할 수 있는 **connectionString** 이 포함됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled
   ```

이 명령에는 세 개의 매개 변수가 포함됩니다.

* `--device-id` 또는 `-d`: IoT 허브 내에서 고유한 설명 이름을 제공합니다.
* `--hub-name` 또는 `-n`: IoT 허브의 이름을 제공합니다.
* `--edge-enabled` 또는 `--ee`: 디바이스가 IoT Edge 디바이스임을 선언합니다.

   ![az iot hub device-identity create output](./media/iot-edge-register-device-symmetric/create-edge-device-cli.png)

---

IoT Hub 디바이스를 등록했으므로 IoT Edge 런타임의 설치 및 프로비저닝을 완료하는 데 사용하는 정보를 검색합니다.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>등록된 디바이스 보기 및 프로비전 정보 검색

대칭 키 인증을 사용하는 디바이스에서 IoT Edge 런타임의 설치 및 프로비저닝을 수행하려면 연결 문자열이 필요합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![Azure Portal에서 IoT 허브 내 모든 IoT Edge 디바이스를 확인할 수 있습니다.](./media/iot-edge-register-device-symmetric/portal-view-devices.png)

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

대칭 키를 사용하여 인증하는 디바이스에는 포털에 복사할 수 있는 연결 문자열이 있습니다.

1. 포털에서 **IoT Edge** 페이지의 IoT Edge 디바이스 목록에서 디바이스 ID를 클릭합니다.
2. **기본 연결 문자열** 또는 **보조 연결 문자열** 중 하나의 값을 복사합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![VS Code를 사용하여 IoT 허브 내 모든 IoT Edge 디바이스 보기](./media/iot-edge-register-device-symmetric/view-devices.png)

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. **Azure IoT Hub** 섹션에서 디바이스 ID를 마우스 오른쪽 단추로 클릭합니다.
1. **디바이스 연결 문자열 복사** 를 선택합니다.

   연결 문자열이 클립보드에 복사됩니다.

오른쪽 클릭 메뉴에서 **디바이스 정보 가져오기** 를 선택하여 출력 창에서 연결 문자열을 비롯한 모든 디바이스 정보를 볼 수도 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

디바이스를 설정할 준비가 되면, 물리적 디바이스를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다. [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) 명령을 사용하여 단일 디바이스에 대한 연결 문자열을 반환합니다.

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>`connection-string show` 명령은 Azure IoT 확장 0.9.8 버전에서 도입되었으며 지원 중단된 `show-connection-string` 명령을 대체합니다. 이 명령을 실행 시 오류가 발생하면 확장 버전이 0.9.8 이상으로 업데이트되었는지 확인합니다. 자세한 내용 및 최신 업데이트는 [Azure CLI용 Microsoft Azure IoT 확장](https://github.com/Azure/azure-iot-cli-extension)을 참조하세요.

`device-id` 매개 변수의 값은 대소문자를 구분합니다.

디바이스에서 사용할 연결 문자열을 복사하는 경우 연결 문자열 앞뒤의 따옴표를 포함하지 마세요.

---
