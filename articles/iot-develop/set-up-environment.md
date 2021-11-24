---
title: 자습서 - IoT 플러그 앤 플레이에 필요한 IoT 리소스 설정 | Microsoft Docs
description: 자습서 - IoT 플러그 앤 플레이 빠른 시작과 자습서에 사용할 IoT Hub 및 Device Provisioning Service 인스턴스를 만듭니다.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-develop
services: iot-develop
ms.custom: mode-other
ms.openlocfilehash: 41489066dcad97a06c400d7a22616e92dcb593ee
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133044143"
---
# <a name="tutorial-set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>자습서: IoT 플러그 앤 플레이 빠른 시작 및 자습서에 사용할 환경 설정

IoT 플러그 앤 플레이 빠른 시작 및 자습서를 완료하려면 먼저 Azure 구독에서 IoT 허브 및 DPS(Device Provisioning Service)를 구성해야 합니다. 또한 샘플 애플리케이션과 Azure IoT 탐색기 도구에서 사용하는 모델 파일의 로컬 복사본이 필요합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-the-resources"></a>리소스 만들기

다음과 같이 리소스에 대한 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

IoT Hub를 만듭니다. 다음 명령은 우리가 만들려는 IoT 허브의 이름으로 `my-pnp-hub`를 사용합니다. `my-pnp-hub` 대신 사용할 고유한 IoT 허브 이름을 선택합니다.

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

DPS 인스턴스를 만듭니다. 다음 명령은 우리가 만들려는 DPS 인스턴스의 이름으로 `my-pnp-dps`를 사용합니다. `my-pnp-dps` 대신 사용할 고유한 DPS 인스턴스 이름을 선택합니다.

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

DPS 인스턴스를 IoT 허브에 연결하려면 다음 명령을 사용합니다. `my-pnp-dps` 및 `my-pnp-hub`를 앞에서 선택한 고유한 이름으로 바꿉니다.

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>설정 검색

일부 빠른 시작 및 자습서에서는 IoT 허브에 연결 문자열을 사용합니다. Azure IoT 탐색기 도구를 설정할 때에도 연결 문자열이 필요합니다. 지금 연결 문자열을 검색하여 적어 둡니다. `my-pnp-hub`를 IoT 허브에 대해 선택한 고유한 이름으로 바꿉니다.

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

대부분의 빠른 시작 및 자습서에서는 DPS 구성의 *ID 범위* 를 사용합니다. 지금 ID 범위를 검색하고 적어 둡니다. `my-pnp-dps`를 DPS 인스턴스에 대해 선택한 고유한 이름으로 바꿉니다.

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

모든 빠른 시작 및 자습서에서는 DPS 디바이스 등록을 사용합니다. 다음 명령을 사용하여 DPS 인스턴스에 `my-pnp-device` *개별 디바이스 등록* 을 만듭니다. `my-pnp-dps`를 DPS 인스턴스에 대해 선택한 고유한 이름으로 바꿉니다. 빠른 시작 및 자습서에서 사용할 등록 ID와 기본 키 값을 기록해 둡니다.

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>환경 변수 만들기

DPS(Device Provisioning Service)를 사용하여 IoT 허브에 연결하도록 빠른 시작 및 자습서의 샘플을 구성하는 다음과 같은 5개의 환경 변수를 만듭니다.

* **IOTHUB_DEVICE_SECURITY_TYPE**: `DPS` 값입니다.
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: 앞에서 기록해 둔 DPS ID 범위입니다.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: `my-pnp-device` 값입니다.
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: 앞에서 기록해 둔 등록 기본 키입니다.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: `global.azure-devices-provisioning.net` 값입니다.

서비스 샘플은 연결할 허브 및 디바이스를 식별하려면 다음과 같은 환경 변수가 필요합니다.

* **IOTHUB_CONNECTION_STRING**: 이전에 기록한 IoT 허브 연결 문자열입니다.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

예를 들어 Linux bash 셸에서는 다음과 같습니다.

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Windows 명령줄에서는 다음과 같습니다.

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>모델 파일 다운로드

빠른 시작 및 자습서에서는 온도 컨트롤러 및 자동 온도 조절기 디바이스의 샘플 모델 파일을 사용합니다. 이 샘플 모델 파일을 다운로드하는 방법은 다음과 같습니다.

1. *모델* 이라는 폴더를 로컬 머신에 만듭니다.

1. [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)을 마우스 오른쪽 단추로 클릭하고 JSON 파일을 *모델* 폴더에 저장합니다.

1. [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json)을 마우스 오른쪽 단추로 클릭하고 JSON 파일을 *모델* 폴더에 저장합니다.

## <a name="install-the-azure-iot-explorer"></a>Azure IoT 탐색기 설치

빠른 시작 및 자습서에서는 **Azure IoT 탐색기** 도구를 사용합니다. [Azure IoT 탐색기 릴리스](https://github.com/Azure/azure-iot-explorer/releases)로 이동하여 최신 릴리스의 자산 목록을 확장하세요. 사용 중인 운영 체제에 맞는 최신 버전의 애플리케이션을 다운로드하여 설치합니다.

도구를 처음으로 실행하면 IoT 허브 연결 문자열을 입력하라는 메시지가 표시됩니다. 앞에서 기록해 둔 연결 문자열을 사용합니다.

앞에서 다운로드한 모델 파일을 사용하도록 도구를 구성합니다. 도구의 홈 페이지에서 **IoT 플러그 앤 플레이 설정** 을 선택한 다음, **+ 추가 > 로컬 폴더** 를 선택합니다. 앞에서 만든 *models* 폴더를 선택합니다. **저장** 을 선택하여 설정을 저장합니다.

자세히 알아보려면 [Azure IoT 탐색기 설치 및 사용](../iot-fundamentals/howto-use-iot-explorer.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

모든 IoT 플러그 앤 플레이 빠른 시작과 자습서에서 IoT 허브 및 DPS 인스턴스를 사용할 수 있으므로 이 문서의 단계를 한 번만 완료하면 됩니다. 모두 마쳤으면 다음 명령을 사용하여 구독에서 리소스를 제거해도 됩니다.

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>다음 단계

환경을 설정했으므로, 이제 다음과 같은 빠른 시작 또는 자습서 중 하나를 시도할 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 IoT Hub에 연결](tutorial-connect-device.md)
