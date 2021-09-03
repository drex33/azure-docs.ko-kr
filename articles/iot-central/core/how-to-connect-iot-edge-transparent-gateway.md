---
title: Azure IoT Central 애플리케이션에 IoT Edge 투명 게이트웨이 연결
description: IoT Edge 투명 게이트웨이를 통해 디바이스를 IoT Central 애플리케이션에 연결하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 03/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: c0e63d0aee60ae81c14fa8971369c3a50f9b2bde
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461403"
---
# <a name="how-to-connect-devices-through-an-iot-edge-transparent-gateway"></a>IoT Edge 투명 게이트웨이를 통해 디바이스를 연결하는 방법

IoT Edge 디바이스는 로컬 네트워크의 다른 디바이스와 IoT Central 애플리케이션 간의 연결을 제공하는 게이트웨이로 작동할 수 있습니다. 디바이스가 IoT Central 애플리케이션에 직접 액세스할 수 없는 경우 게이트웨이를 사용합니다.

IoT Edge는 [*투명* 및 *변환* 게이트웨이 패턴](../../iot-edge/iot-edge-as-gateway.md)을 지원합니다. 이 문서에서는 투명 게이트웨이 패턴을 구현하는 방법을 요약해서 설명합니다. 이 패턴에서 게이트웨이는 다운스트림 디바이스에서 IoT Central 애플리케이션의 IoT Hub 엔드포인트로 메시지를 전달합니다.

이 문서에서는 가상 머신을 사용하여 다운스트림 디바이스 및 게이트웨이를 호스트합니다. 실제 시나리오에서 다운스트림 디바이스 및 게이트웨이는 로컬 네트워크의 물리적 디바이스에서 실행됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음이 필요합니다.

- 활성화된 Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- **사용자 지정 애플리케이션** 템플릿에서 [생성된 IoT Central 애플리케이션](howto-create-iot-central-application.md). 자세한 내용은 [IoT Central 애플리케이션 만들기](howto-create-iot-central-application.md)를 참조하세요.

이 문서의 단계를 수행하려면 다음 파일을 컴퓨터에 다운로드합니다.

- [자동 온도 조절기 디바이스 모델](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-1.json)
- [투명 게이트웨이 매니페스트](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/EdgeTransparentGatewayManifest.json)

## <a name="add-device-templates"></a>디바이스 템플릿 추가

다운스트림 디바이스와 게이트웨이 디바이스에는 모두 IoT Central에 디바이스 템플릿이 필요합니다. IoT Central을 사용하면 다운스트림 디바이스와 게이트웨이 간의 관계를 모델링할 수 있으므로 연결된 후 보고 관리할 수 있습니다.

다운스트림 디바이스에 대한 디바이스 템플릿을 만들려면 디바이스의 기능을 모델링하는 표준 디바이스 템플릿을 만듭니다. 이 문서에 표시된 예제에서는 자동 온도 조절기 디바이스 모델을 사용합니다.

다운스트림 디바이스에 대한 디바이스 템플릿을 만들려면

1. 디바이스 템플릿을 만들고 템플릿 유형으로 **IoT 디바이스** 를 선택합니다.

1. 마법사의 **사용자 지정** 페이지에서 디바이스 템플릿에 *Thermostat* 과 같은 이름을 입력합니다.

1. 디바이스 템플릿을 만든 후 **모델 가져오기** 를 선택합니다. 이전에 다운로드한 *thermostat-1.json* 파일과 같은 모델을 선택합니다.

1. 자동 온도 조절기에 대한 일부 기본 보기를 생성하려면 보기를 선택한 다음, **기본 보기 생성** 을 선택합니다.

1. 디바이스 템플릿을 게시합니다.

투명 IoT Edge 게이트웨이용 디바이스 템플릿을 만들려면

1. 디바이스 템플릿을 만들고 템플릿 유형으로 **Azure IoT Edge** 를 선택합니다.

1. 마법사의 **사용자 지정** 페이지에서 디바이스 템플릿에 *Edge Gateway* 와 같은 이름을 입력합니다.

1. 마법사의 **사용자 지정** 페이지에서 **다운스트림 디바이스가 포함된 게이트웨이 디바이스** 를 선택합니다.

1. 마법사의 **사용자 지정** 페이지에서 **찾아보기** 를 선택합니다. 이전에 다운로드한 *EdgeTransparentGatewayManifest.json* 파일을 업로드합니다.

1. 다운스트림 디바이스 템플릿에 **관계** 의 항목을 추가합니다.

다음 스크린샷에서는 **자동 온도 조절기** 디바이스 템플릿을 사용하는 다운스트림 디바이스가 있는 IoT Edge 게이트웨이 디바이스에 대한 **관계** 페이지를 보여 줍니다.

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/device-template-relationship.png" alt-text="자동 온도 조절기 다운스트림 디바이스 템플릿과의 IoT Edge 게이트웨이 디바이스 템플릿 관계를 보여 주는 스크린샷":::

위의 스크린샷은 모듈이 정의되지 않은 IoT Edge 게이트웨이 디바이스 템플릿을 보여 줍니다. IoT Edge 런타임이 다운스트림 디바이스에서 IoT Central로 메시지를 전달하기 때문에 투명 게이트웨이에는 모듈이 필요하지 않습니다. 게이트웨이 자체에서 원격 분석을 보내거나, 속성을 동기화하거나, 명령을 처리해야 하는 경우 루트 구성 요소 또는 모듈에서 이러한 기능을 정의할 수 있습니다.

게이트웨이 및 다운스트림 디바이스 템플릿을 게시하기 전에 필요한 클라우드 속성 및 보기를 추가합니다.

## <a name="add-the-devices"></a>디바이스 추가

IoT Central 애플리케이션에 디바이스를 추가하는 경우 다운스트림 디바이스와 투명 게이트웨이 간의 관계를 정의할 수 있습니다.

디바이스를 추가하려면

1. IoT Central 애플리케이션에서 디바이스 페이지로 이동합니다.

1. 투명 게이트웨이 IoT Edge 디바이스의 인스턴스를 추가합니다. 이 문서에서 게이트웨이 디바이스 ID는 `edgegateway`입니다.

1. 하나 이상의 다운스트림 디바이스 인스턴스를 추가합니다. 이 문서에서 다운스트림 디바이스는 ID가 `thermostat1` 및 `thermostat2`인 온도 조절기입니다.

1. 디바이스 목록에서 각 다운스트림 디바이스를 선택하고 **게이트웨이에 연결** 을 선택합니다.

다음 스크린샷에서는 **다운스트림 디바이스** 페이지에서 게이트웨이에 연결된 디바이스 목록을 볼 수 있습니다.

:::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-devices.png" alt-text="투명 게이트웨이에 연결된 다운스트림 디바이스 목록을 보여 주는 스크린샷":::

투명 게이트웨이에서 다운스트림 디바이스는 게이트웨이에서 호스트되는 사용자 지정 모듈이 아니라 게이트웨이 자체에 연결됩니다.

디바이스를 배포하기 전에 다음이 필요합니다.

- IoT Central 애플리케이션의 **ID 범위**
- 게이트웨이 및 다운스트림 디바이스에 대한 **디바이스 ID** 값
- 게이트웨이 및 다운스트림 디바이스에 대한 **기본 키** 값

이러한 값을 찾으려면 디바이스 목록의 각 디바이스로 이동하고 **연결** 을 선택합니다. 계속하기 전에 이러한 값을 기록해 둡니다.

## <a name="deploy-the-gateway-and-devices"></a>게이트웨이 및 디바이스 배포

이 시나리오를 시험해 볼 수 있도록 다음 단계에서는 게이트웨이 및 다운스트림 디바이스를 Azure 가상 머신에 배포하는 방법을 보여 줍니다. 실제 시나리오에서 다운스트림 디바이스 및 게이트웨이는 로컬 네트워크의 물리적 디바이스에서 실행됩니다.

투명 게이트웨이 시나리오를 사용해 보려면 다음 단추를 선택하여 두 개의 Linux 가상 머신을 배포합니다. 가상 머신 하나는 투명 IoT Edge 게이트웨이이고, 다른 하나는 자동 온도 조절기를 시뮬레이트하는 다운스트림 디바이스입니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

두 가상 머신을 배포하고 실행하는 경우 `edgegateway` 가상 머신에서 IoT Edge 게이트웨이 디바이스가 실행 중인지 확인합니다.

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동합니다. IoT Edge 게이트웨이 디바이스가 IoT Central에 연결된 경우 상태가 **프로비저닝됨** 입니다.

1. IoT Edge 게이트웨이 디바이스를 열고 **모듈** 페이지에서 모듈의 상태를 확인합니다. IoT Edge 런타임이 성공적으로 시작되면 **$edgeAgent** 및 **$edgeHub** 모듈의 상태가 **실행 중** 입니다.

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/iot-edge-runtime.png" alt-text="IoT Edge 게이트웨이에서 실행되는 $edgeAgent 및 $edgeHub 모듈을 보여 주는 스크린샷":::

> [!TIP]
> 가상 머신이 시작되는 동안 몇 분 정도 기다려야 할 수 있으며 디바이스가 IoT Central 애플리케이션에 프로비저닝됩니다.

## <a name="configure-the-gateway"></a>게이트웨이 구성

IoT Edge 디바이스가 투명 게이트웨이로 작동하려면 모든 다운스트림 디바이스에 대해 ID를 증명할 수 있는 일부 인증서가 필요합니다. 이 문서에서는 데모 인증서를 사용합니다. 프로덕션 환경에서는 인증 기관의 인증서를 사용합니다.

데모 인증서를 생성하고 게이트웨이 디바이스에 설치하려면

1. SSH를 사용하여 게이트웨이 디바이스 가상 머신에 연결하고 로그인합니다.

1. 다음 명령을 실행하여 IoT Edge 리포지토리를 복제하고 데모 인증서를 생성합니다.

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    이전 명령을 실행한 후 다음 파일이 다음 단계에서 사용할 준비가 됩니다.

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* - IoT Edge 시나리오를 테스트하기 위해 다른 데모 인증서를 모두 만드는 데 사용되는 루트 CA 인증서입니다.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* - *config.yaml* 파일에서 참조되는 디바이스 CA 인증서입니다. 게이트웨이 시나리오에서 이 CA 인증서는 IoT Edge 디바이스가 다운스트림 디바이스에 대해 ID를 확인하는 방법입니다.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* - 디바이스 CA 인증서와 연결된 프라이빗 키입니다.

    이 데모 인증서에 관해 자세히 알아보려면 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](../../iot-edge/how-to-create-test-certificates.md)를 참조하세요.

1. 텍스트 편집기에서 *config.yaml* 파일을 엽니다. 예를 들면 다음과 같습니다.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. `Certificate settings` 설정을 찾습니다. 다음과 같이 인증서 설정의 주석 처리를 제거하고 인증서 설정을 수정합니다.

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    위에 표시된 예제에서는 **AzureUser** 로 로그인하고 “mycacert”라는 디바이스 CA 인증서를 만들었다고 가정합니다.

1. 변경 내용을 저장하고 IoT Edge 런타임을 다시 시작합니다.

    ```bash
    sudo systemctl restart iotedge
    ```

변경 후 IoT Edge 런타임이 성공적으로 시작되면 **$edgeAgent** 및 **$edgeHub** 모듈의 상태가 IoT Central의 게이트웨이 디바이스에 대한 **모듈** 페이지에서 **실행 중** 으로 변경됩니다.

런타임이 시작되지 않으면 *config.yaml* 에서 변경한 내용을 확인하고 [IoT Edge 디바이스 문제 해결](../../iot-edge/troubleshoot.md)을 참조하세요.

이제 투명 게이트웨이가 구성되고 다운스트림 디바이스에서 원격 분석 전달을 시작할 준비가 되었습니다.

## <a name="provision-a-downstream-device"></a>다운스트림 디바이스 프로비저닝

현재 IoT Edge는 IoT Central 애플리케이션에 다운스트림 디바이스를 자동으로 프로비저닝할 수 없습니다. 다음 단계에서는 `thermostat1` 디바이스를 프로비저닝하는 방법을 보여 줍니다. 이러한 단계를 완료하려면 Python 3.6 이상이 설치되고 인터넷에 연결된 환경이 필요합니다. [Azure Cloud Shell](https://shell.azure.com/)에는 Python 3.7이 미리 설치되어 있습니다.

1. 다음 명령을 실행하여 `azure.iot.device` 모듈을 설치합니다.

    ```bash
    pip install azure.iot.device
    ```

1. 다음 명령을 실행하여 프로비저닝을 수행하는 Python 스크립트를 다운로드합니다.

    ```bash
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/provision_device.py
    ```

1. IoT Central 애플리케이션에서 `thermostat1` 다운스트림 디바이스를 프로비저닝하려면 `{your application id scope}` 및 `{your device primary key}`를 대체하여 다음 명령을 실행합니다.

    ```bash
    export IOTHUB_DEVICE_DPS_DEVICE_ID=thermostat1
    export IOTHUB_DEVICE_DPS_ID_SCOPE={your application id scope}
    export IOTHUB_DEVICE_DPS_DEVICE_KEY={your device primary key}
    python provision_device.py
    ```

IoT Central 애플리케이션에서 thermostat1 디바이스에 대한 **디바이스 상태** 가 이제 **프로비저닝됨** 이 되었는지 확인합니다. 

## <a name="configure-a-downstream-device"></a>다운스트림 디바이스 구성

이전 섹션에서는 데모 인증서로 `edgegateway` 가상 머신을 구성하여 게이트웨이로 실행될 수 있도록 했습니다. `leafdevice` 가상 머신은 게이트웨이를 사용하여 IoT Central에 연결하는 자동 온도 조절기 시뮬레이터를 설치할 준비가 되었습니다.

`leafdevice` 가상 머신에는 `edgegateway` 가상 머신에서 만든 루트 CA 인증서의 복사본이 필요합니다. `edgegateway` 가상 머신에서 `leafdevice`의 홈 디렉터리로  파일을 복사합니다. **scp** 명령을 사용하여 Linux 가상 머신 간에 파일을 복사할 수 있습니다.

다운스트림 디바이스에서 게이트웨이로의 연결을 확인하는 방법을 알아보려면 [게이트웨이 연결 테스트](../../iot-edge/how-to-connect-downstream-device.md#test-the-gateway-connection)를 참조하세요.

`leafdevice` 가상 머신에서 자동 온도 조절기 시뮬레이터를 실행하려면

1. Python 샘플을 홈 디렉터리에 다운로드합니다.

    ```bash
    cd ~
    wget https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/transparent-gateway/simple_thermostat.py
    ```

1. Azure IoT 디바이스 Python 모듈을 설치합니다.

    ```bash
    sudo apt update
    sudo apt install python3-pip
    pip3 install azure.iot.device
    ```

1. 환경 변수를 설정하여 샘플을 구성합니다. `{your device shared key}`를 이전에 적어 둔 `thermostat1`의 기본 키로 바꿉니다. 이러한 변수는 게이트웨이 가상 머신의 이름이 `edgegateway`이고 자동 온도 조절기 디바이스의 ID가 `thermostat1`이라고 가정합니다.

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    export IOTHUB_DEVICE_CONNECTION_STRING="HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}"
    export IOTEDGE_ROOT_CA_CERT_PATH=~/azure-iot-test-only.root.ca.cert.pem
    ```

    연결 문자열에 IoT Hub 이름이 아니라 게이트웨이 디바이스 이름이 사용되는 방식을 확인합니다.

1. 코드를 실행하려면 다음 명령을 사용합니다.

    ```bash
    python3 simple_thermostat.py
    ```

    이 명령의 출력은 다음과 같습니다.

    ```output
    Connecting using Connection String HostName=edgegateway;DeviceId=thermostat1;SharedAccessKey={your device shared key}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry for temperature
    Sent message
    Sent message
    Sent message
    ...
    ```

1. IoT Central에서 원격 분석을 보려면 **thermostat1** 디바이스에 대한 **개요** 페이지로 이동합니다.

    :::image type="content" source="media/how-to-connect-iot-edge-transparent-gateway/downstream-device-telemetry.png" alt-text="다운스트림 디바이스의 원격 분석을 보여 주는 스크린샷":::

    **정보** 페이지에서 다운스트림 디바이스에서 전송된 속성 값을 볼 수 있으며, **명령** 페이지에서 다운스트림 디바이스에 대한 명령을 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 IoT Central을 사용하여 투명 게이트웨이를 구성하는 방법을 배웠으므로 제안된 다음 단계는 [IoT Edge](../../iot-edge/about-iot-edge.md)에 대해 자세히 알아보는 것입니다.
