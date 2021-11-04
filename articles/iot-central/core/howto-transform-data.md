---
title: Azure IoT Central의 데이터 변환 | Microsoft Docs
description: IoT 디바이스는 변환해야 할 수 있는 다양한 형식으로 데이터를 전송합니다. 이 문서에서는 IoT Central에 대해 들어가고 나가는 동안 둘 다 데이터를 변환하는 방법을 설명합니다. 설명된 시나리오에서는 IoT Edge 및 Azure Functions를 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e05cbd17dbfe4f61562856f9244f5b4d0fa65dc9
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577387"
---
# <a name="transform-data-externally-for-iot-central"></a>IoT Central 위해 외부적으로 데이터 변환

IoT 디바이스는 다양한 형식으로 데이터를 전송합니다. IoT Central 애플리케이션에서 디바이스 데이터를 사용하려면 변환을 통해 다음을 수행해야 할 수 있습니다.

- IoT Central 애플리케이션과 호환되는 데이터 형식을 만듭니다.
- 단위를 변환합니다.
- 새 메트릭을 계산합니다.
- 다른 원본의 데이터를 보강합니다.

이 문서에서는 수신 또는 송신 시 IoT Central 외부에서 디바이스 데이터를 변환하는 방법을 보여 줍니다.

다음 다이어그램에서는 변환을 포함하는 세 개의 데이터 경로를 보여 줍니다.

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="수신 및 송신 시 데이터 변환 경로 요약" border="false":::

다음 표에서는 세 가지 예제 변환 형식을 보여 줍니다.

| 변환 | 설명 | 예제  | 참고 |
|------------------------|-------------|----------|-------|
| 메시지 형식         | JSON 메시지로 변환하거나 JSON 메시지를 조작합니다. | CSV에서 JSON으로  | 수신 시. IoT Central은 JSON 메시지 값만 허용합니다. 자세히 알아보려면 [원격 분석, 속성 및 명령 페이로드](concepts-telemetry-properties-commands.md)를 참조하세요. |
| 계산이           | [Azure Functions](../../azure-functions/index.yml)가 실행할 수 있는 수학 함수입니다. | 화씨에서 섭씨로 단위 변환.  | 송신 패턴으로 변환하여 IoT Central에 대한 직접 연결을 통해 스케일링 가능한 디바이스 수신을 활용합니다. 데이터를 변환하면 시각화 및 작업과 같은 IoT Central 기능을 사용할 수 있습니다. |
| 메시지 보강     | 디바이스 속성 또는 원격 분석에 없는 외부 데이터 원본의 보강입니다. 내부 보강에 관해 자세히 알아보려면 [데이터 내보내기를 사용하여 클라우드 대상으로 IoT 데이터 내보내기](howto-export-data.md)를 참조하세요. | 디바이스에서 [위치 데이터](howto-use-location-data.md)를 사용하여 메시지에 날씨 정보를 추가합니다. | 송신 패턴으로 변환하여 IoT Central에 대한 직접 연결을 통해 스케일링 가능한 디바이스 수신을 활용합니다. |

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드의 단계를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="data-transformation-at-ingress"></a>수신 시 데이터 변환

수신 시 디바이스 데이터를 변환할 수 있는 두 가지 옵션이 있습니다.

- **IoT Edge**: IoT Edge 모듈을 사용하여 IoT Central 애플리케이션에 데이터를 보내기 전에 다운스트림 디바이스에서 데이터를 변환합니다.

- **IoT Central 디바이스 브리지**: [IoT Central 디바이스 브리지](howto-build-iotc-device-bridge.md)는 Sigfox, Particle 및 Things Network와 같은 다른 IoT 디바이스 클라우드를 IoT Central에 연결합니다. 디바이스 브리지는 Azure 함수를 사용하여 데이터를 전달하며 디바이스 데이터를 변환하도록 함수를 사용자 지정할 수 있습니다.

### <a name="use-iot-edge-to-transform-device-data"></a>IoT Edge를 사용하여 디바이스 데이터 변환

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="IoT Edge를 사용하여 수신 시 데이터 변환" border="false":::

이 시나리오에서 IoT Edge 모듈은 다운스트림 디바이스의 데이터를 변환한 후 IoT Central 애플리케이션에 전달합니다. 이 시나리오를 구성하는 개략적인 단계는 다음과 같습니다.

1. **IoT Edge 디바이스 설정**: IoT Edge 디바이스를 게이트웨이로 설치 및 프로비저닝하고 IoT Central 애플리케이션에 게이트웨이를 연결합니다.

1. **IoT Edge 디바이스에 다운스트림 디바이스 연결:** 다운스트림 디바이스를 IoT Edge 디바이스에 연결하고 IoT Central 애플리케이션에 프로비저닝합니다.

1. **IoT Edge에서 디바이스 데이터 변환:** IoT Edge 모듈을 만들어 데이터를 변환합니다. 변환된 디바이스 데이터를 IoT Central 애플리케이션에 전달하는 IoT Edge 게이트웨이 디바이스에 모듈을 배포합니다.

1. **확인**: 다운스트림 디바이스에서 게이트웨이로 데이터를 전송하고 변환된 디바이스 데이터가 IoT Central 애플리케이션에 도달하는지 확인합니다.

다음 섹션에 설명된 예제에서 다운스트림 디바이스는 다음 형식의 CSV 데이터를 IoT Edge 게이트웨이 디바이스에 전송합니다.

```csv
"<temperature >, <pressure>, <humidity>"
```

IoT Edge 모듈을 사용하여 데이터가 IoT Central에 전송되기 전에 데이터를 다음 JSON 형식으로 변환하려고 합니다.

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

[![코드 찾아보기](media/common/browse-code.svg)](https://github.com/iot-for-all/iot-central-transform-with-iot-edge)

다음 단계에서는 이 시나리오를 설정하고 구성하는 방법을 보여 줍니다.

### <a name="build-the-custom-module"></a>사용자 지정 모듈 빌드

이 시나리오에서 IoT Edge 디바이스는 다운스트림 디바이스의 데이터를 변환하는 사용자 지정 모듈을 실행합니다. IoT Edge 디바이스를 배포 및 구성하기 전에 다음을 수행해야 합니다.

- 사용자 지정 모듈을 빌드합니다.
- 컨테이너 레지스트리에 사용자 지정 모듈을 추가합니다.

IoT Edge 런타임은 Azure 컨테이너 레지스트리 또는 Docker Hub와 같은 컨테이너 레지스트리에서 사용자 지정 모듈을 다운로드합니다. [Azure Cloud Shell](../../cloud-shell/overview.md)에는 컨테이너 레지스트리를 만들고, 모듈을 빌드하고, 모듈을 레지스트리에 업로드하는 데 필요한 모든 도구가 있습니다.

컨테이너 레지스트리를 만들려면:

1. [Azure Cloud Shell](https://shell.azure.com/)을 열고 Azure 구독에 로그인합니다.

1. 다음 단계를 실행하여 Azure 컨테이너 레지스트리를 만듭니다.

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    `username` 및 `password` 값을 기록해 둡니다. 나중에 사용합니다.

[Azure Cloud Shell](https://shell.azure.com/)에서 사용자 지정 모듈을 빌드하려면:

1. [Azure Cloud Shell](https://shell.azure.com/)에서 적절한 폴더로 이동합니다.
1. 모듈 소스 코드를 포함하는 GitHub 리포지토리를 복제하려면 다음 명령을 실행합니다.

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. 사용자 지정 모듈을 빌드하려면 Azure Cloud Shell에서 다음 명령을 실행합니다.

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    이전 명령은 실행하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="set-up-an-iot-edge-device"></a>IoT Edge 디바이스 설정

이 시나리오에서는 IoT Edge 게이트웨이 디바이스를 사용하여 다운스트림 디바이스의 데이터를 변환합니다. 이 섹션에서는 IoT Central 애플리케이션에서 게이트웨이 및 다운스트림 디바이스용 IoT Central 디바이스 템플릿을 만드는 방법을 설명합니다. IoT Edge 디바이스는 배포 매니페스트를 사용하여 해당 모듈을 구성합니다.

다운스트림 디바이스용 디바이스 템플릿을 만들기 위해 이 시나리오에서는 간단한 자동 온도 조절기 디바이스 모델을 사용합니다.

1. [자동 온도 조절기 디바이스의 디바이스 모델](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json)을 로컬 머신에 다운로드합니다.

1. IoT Central 애플리케이션에 로그인하고 **디바이스 템플릿** 페이지로 이동합니다.

1. **+ 새로 만들기** 를 선택하고, **IoT 디바이스** 를 선택하고, **다음: 사용자 지정** 을 선택합니다.

1. 템플릿 이름으로 ‘자동 온도 조절기’를 입력하고 **다음: 검토** 를 선택합니다. 그런 다음 **만들기** 를 선택합니다.

1. **모델 가져오기** 를 선택하고 이전에 다운로드한 *thermostat-2.json* 파일을 가져옵니다.

1. **게시** 를 선택하여 새 디바이스 템플릿을 게시합니다.

IoT Edge 게이트웨이 디바이스용 디바이스 템플릿을 만들려면:

1. 배포 매니페스트 [moduledeployment.json](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json)의 복사본을 로컬 개발 머신에 저장합니다.

1. 텍스트 편집기에서 *moduledeployment.json* 매니페스트 파일의 로컬 복사본을 엽니다.

1. `registryCredentials` 섹션을 찾고 자리 표시자를 Azure 컨테이너 레지스트리를 만들 때 기록한 값으로 바꿉니다. `address` 값이 `<username>.azurecr.io`처럼 표시됩니다.

1. `settings`에 대한 `transformmodule` 섹션을 찾습니다. `<acr or docker repo>`를 이전 단계에서 사용한 `address` 값으로 바꿉니다. 변경 내용을 저장합니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿** 페이지로 이동합니다.

1. **+ 새로 만들기** 를 선택하고, **Azure IoT Edge** 를 선택한 후, **다음: 사용자 지정** 을 선택합니다.

1. 디바이스 템플릿 이름으로 ‘IoT Edge 게이트웨이 디바이스’를 입력합니다. **게이트웨이 디바이스입니다.** 를 선택합니다. **찾아보기** 를 선택하여 이전에 편집한 *moduledeployment.json* 배포 매니페스트 파일을 업로드합니다.

1. 배포 매니페스트의 유효성을 검사한 후 **다음: 검토** 를 선택한 다음, **만들기** 를 선택합니다.

1. **모델** 에서 **관계** 를 선택합니다. **+ 관계 추가** 를 선택합니다. 표시 이름으로 ‘다운스트림 디바이스’를 입력하고 대상으로 **자동 온도 조절기** 를 선택합니다. **저장** 을 선택합니다.

1. **게시** 를 선택하여 디바이스 템플릿을 게시합니다.

이제 IoT Central 애플리케이션에 두 개의 디바이스 템플릿인 **IoT Edge 게이트웨이 디바이스** 템플릿 및 **자동 온도 조절기** 템플릿(다운스트림 디바이스)이 있습니다.

IoT Central에서 게이트웨이 디바이스를 등록하려면:

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동합니다.

1. **IoT Edge 게이트웨이 디바이스** 를 선택하고 **디바이스 만들기** 를 선택합니다. 디바이스 이름으로 ‘IoT Edge 게이트웨이 디바이스’를 입력하고, 디바이스 ID로 *gateway-01* 을 입력하고, **IoT Edge 게이트웨이 디바이스** 가 디바이스 템플릿으로 선택되었는지 확인합니다. **만들기** 를 선택합니다.

1. 디바이스 목록에서 **IoT Edge 게이트웨이 디바이스** 를 클릭한 다음, **연결** 을 선택합니다.

1. **IoT Edge 게이트웨이 디바이스** 의 **ID 범위**, **디바이스 ID** 및 **기본 키** 값을 기록해 둡니다. 나중에 사용합니다.

IoT Central에서 다운스트림 디바이스를 등록하려면:

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동합니다.

1. **자동 온도 조절기** 를 선택하고 **디바이스 만들기** 를 선택합니다. 디바이스 이름으로 ‘자동 온도 조절기’를 입력하고, 디바이스 ID로 *downstream-01* 을 입력하고, **자동 온도 조절기** 가 디바이스 템플릿으로 선택되었는지 확인합니다. **만들기** 를 선택합니다.

1. 디바이스 목록에서 **자동 온도 조절기** 를 선택한 다음, **게이트웨이에 연결** 을 선택합니다. **IoT Edge 게이트웨이 디바이스** 템플릿 및 **IoT Edge 게이트웨이 디바이스** 인스턴스를 선택합니다. **연결** 을 선택합니다.

1. 디바이스 목록에서 **자동 온도 조절기** 를 클릭한 다음, **연결** 을 선택합니다.

1. **자동 온도 조절기 디바이스** 의 **ID 범위**, **디바이스 ID** 및 **기본 키** 값을 기록해 둡니다. 나중에 사용합니다.

### <a name="deploy-the-gateway-and-downstream-devices"></a>게이트웨이 및 다운스트림 디바이스 배포

편의를 위해 이 문서에서는 Azure 가상 머신을 사용하여 게이트웨이 및 다운스트림 디바이스를 실행합니다. 두 Azure 가상 머신을 만들려면 아래 **Azure에 배포** 단추를 선택하고 다음 표의 정보를 사용하여 **사용자 지정 배포** 양식을 완료합니다.

| 필드 | 값 |
| ----- | ----- |
| Resource group | `ingress-scenario` |
| DNS 레이블 접두사 게이트웨이 | 이 머신의 고유한 DNS 이름(예: `<your name>edgegateway`) |
| DNS 레이블 접두사 다운스트림 | 이 머신의 고유한 DNS 이름(예: `<your name>downstream`) |
| 범위 ID | 이전에 기록해 둔 ID 범위 |
| 디바이스 ID IoT Edge 게이트웨이 | `gateway-01` |
| 디바이스 키 IoT Edge 게이트웨이 | 이전에 기록해 둔 기본 키 |
| 인증 유형 | 암호 |
| 관리자 암호 또는 키 | 두 가상 머신에서 모두 **AzureUser** 계정에 대해 선택한 암호입니다. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

**검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다. **ingress-scenario** 리소스 그룹에서 가상 머신을 만드는 데 몇 분 정도 걸립니다.

IoT Edge 디바이스가 올바르게 실행되고 있는지 확인하려면:

1. IoT Central 애플리케이션을 엽니다. 그런 다음, **디바이스** 페이지의 디바이스 목록에서 **IoT Edge 게이트웨이 디바이스** 로 이동합니다.

1. **모듈** 탭을 선택하고 세 모듈의 상태를 확인합니다. 가상 머신에서 IoT Edge 런타임을 시작하는 데 몇 분 정도 걸립니다. 시작되면 세 모듈의 상태는 **실행 중** 입니다. IoT Edge 런타임이 시작되지 않으면 [IoT Edge 디바이스 문제 해결](../../iot-edge/troubleshoot.md)을 참조하세요.

IoT Edge 디바이스가 게이트웨이로 작동하려면 모든 다운스트림 디바이스에 대해 ID를 증명할 수 있는 일부 인증서가 필요합니다. 이 문서에서는 데모 인증서를 사용합니다. 프로덕션 환경에서는 인증 기관의 인증서를 사용합니다.

데모 인증서를 생성하고 게이트웨이 디바이스에 설치하려면

1. SSH를 사용하여 게이트웨이 디바이스 가상 머신에 연결하고 로그인합니다. Azure Portal에서 이 가상 머신의 DNS 이름을 찾을 수 있습니다. **ingress-scenario** 리소스 그룹에서 **edgegateway** 가상 머신으로 이동합니다.

    > [!TIP]
    > SSH를 사용하여 로컬 머신 또는 Azure Cloud Shell에서 연결하려면 먼저 두 가상 머신에서 모두 SSH 액세스를 위해 포트 22를 열어야 할 수 있습니다.

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

변경 후 IoT Edge 런타임이 시작되면 **$edgeAgent** 및 **$edgeHub** 모듈의 상태가 **실행 중** 으로 변경됩니다. IoT Central에서 게이트웨이 디바이스의 **모듈** 페이지에서 이 상태 값을 확인할 수 있습니다.

런타임이 시작되지 않으면 *config.yaml* 에서 변경한 내용을 확인하고 [IoT Edge 디바이스 문제 해결](../../iot-edge/troubleshoot.md)을 참조하세요.

### <a name="connect-downstream-device-to-iot-edge-device"></a>IoT Edge 디바이스에 다운스트림 디바이스 연결

Azure IoT Edge 게이트웨이 디바이스에 다운스트림 디바이스를 연결하려면:

1. SSH를 사용하여 다운스트림 디바이스 가상 머신에 연결하고 로그인합니다. Azure Portal에서 이 가상 머신의 DNS 이름을 찾을 수 있습니다. **ingress-scenario** 리소스 그룹에서 **leafdevice** 가상 머신으로 이동합니다.

    > [!TIP]
    > SSH를 사용하여 로컬 머신 또는 Azure Cloud Shell에서 연결하려면 먼저 두 가상 머신에서 모두 SSH 액세스를 위해 포트 22를 열어야 할 수 있습니다.

1. 샘플 다운스트림 디바이스의 소스 코드가 포함된 GitHub 리포지토리를 복제하려면 다음 명령을 실행합니다.

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. 게이트웨이 디바이스에서 필요한 인증서를 복사하려면 다음 `scp` 명령을 실행합니다. 이 `scp` 명령은 호스트 이름 `edgegateway`를 사용하여 게이트웨이 가상 머신을 식별합니다. 암호를 입력하라는 메시지가 표시됩니다.

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. *leafdevice* 폴더로 이동하고 필요한 패키지를 설치합니다. 그런 다음, `build` 및 `start` 스크립트를 실행하여 디바이스를 프로비저닝하고 게이트웨이에 연결합니다.

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. 이전에 만든 다운스트림 디바이스의 디바이스 ID, 범위 ID 및 SAS 키를 입력합니다. 호스트 이름에 `edgegateway`를 입력합니다. 명령의 출력은 다음과 같습니다.

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>확인

시나리오가 실행 중인지 확인하려면 IoT Central에서 **IoT Edge 게이트웨이 디바이스** 로 이동합니다.

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="디바이스 페이지에서 변환된 데이터를 보여 주는 스크린샷":::

- **모듈** 을 선택합니다. 세 가지 IoT Edge 모듈인 **$edgeAgent**, **$edgeHub** 및 **transformmodule** 이 실행 중인지 확인합니다.
- **다운스트림 디바이스** 를 선택하고 다운스트림 디바이스가 프로비저닝되었는지 확인합니다.
- **원시 데이터** 를 선택합니다. **모델링되지 않은 데이터** 열의 원격 분석 데이터는 다음과 같습니다.

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

IoT Edge 디바이스는 다운스트림 디바이스의 데이터를 변환하고 있기 때문에 원격 분석은 IoT Central의 게이트웨이 디바이스와 연결됩니다. 원격 분석을 시각화하려면 원격 분석 형식의 정의를 사용하여 새 버전의 **IoT Edge 게이트웨이 디바이스** 템플릿을 만듭니다.

## <a name="data-transformation-at-egress"></a>송신 시 데이터 변환

디바이스를 IoT Central에 연결하고, 디바이스 데이터를 컴퓨팅 엔진으로 내보내서 변환한 다음, 디바이스 관리 및 분석을 위해 변환된 데이터를 다시 IoT Central로 전송할 수 있습니다. 예를 들면 다음과 같습니다.

- 디바이스는 IoT Central로 위치 데이터를 전송합니다.
- IoT Central은 날씨 정보를 사용하여 위치 데이터를 개선하는 컴퓨팅 엔진으로 데이터를 내보냅니다.
- 컴퓨팅 엔진은 향상된 데이터를 다시 IoT Central로 전송합니다.

[IoT Central 디바이스 브리지](https://github.com/Azure/iotc-device-bridge)를 컴퓨팅 엔진으로 사용하여 IoT Central에서 내보낸 데이터를 변환할 수 있습니다.

수신 시 데이터 변환의 장점은 디바이스가 IoT Central에 직접 연결되므로 손쉽게 디바이스에 명령을 전송하거나 디바이스 속성을 업데이트할 수 있다는 것입니다. 그러나 이 방법을 사용하면 월간 할당보다 많은 메시지를 사용할 수 있지만 Azure IoT Central 사용 비용이 증가할 수 있습니다.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>IoT Central 디바이스 브리지를 사용하여 디바이스 데이터 변환

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="IoT Edge를 사용하여 송신 시 데이터 변환" border="false":::

이 시나리오에서 컴퓨팅 엔진은 IoT Central에서 내보낸 디바이스 데이터를 변환한 후 다시 IoT Central 애플리케이션으로 전송합니다. 이 시나리오를 구성하는 개략적인 단계는 다음과 같습니다.

1. **컴퓨팅 엔진 설정:** 데이터 변환을 위한 컴퓨팅 엔진 역할을 하는 IoT Central 디바이스 브리지를 만듭니다.

1. **디바이스 브리지에서 디바이스 데이터 변환:** 데이터 변환 사용 사례에 맞게 디바이스 브리지 함수 코드를 수정하여 디바이스 브리지에서 데이터를 변환합니다.

1. **IoT Central에서 디바이스 브리지로 데이터 흐름 사용:** 변환을 위해 IoT Central에서 디바이스 브리지로 데이터를 내보냅니다. 그런 다음, 변환된 데이터를 다시 IoT Central에 전달합니다. 데이터 내보내기를 만드는 경우 메시지 속성 필터를 사용하여 변환되지 않은 데이터만 내보냅니다.

1. **확인**: 디바이스를 IoT Central 앱에 연결하고 IoT Central에서 원시 디바이스 데이터 및 변환된 데이터를 둘 다 확인합니다.

<!-- To Do - doesn't the device send JSON data? -->
다음 섹션에 설명된 예제에서 디바이스는 다음 형식의 CSV 데이터를 IoT Edge 게이트웨이 디바이스에 전송합니다.

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

디바이스 브리지를 사용하여 다음과 같이 디바이스 데이터를 변환합니다.

- 온도 단위를 섭씨에서 화씨로 변경합니다.
- 위도 및 경도 값에 대해 [Open Weather](https://openweathermap.org/) 서비스에서 끌어온 날씨 데이터를 사용하여 디바이스 데이터를 보강합니다.

이후 디바이스 브리지는 변환된 데이터를 다음 형식으로 IoT Central로 전송합니다.

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

다음 단계에서는 이 시나리오를 설정하고 구성하는 방법을 보여 줍니다.

### <a name="retrieve-your-iot-central-connection-settings"></a>IoT Central 연결 설정 검색

이 시나리오를 설정하기 전에 IoT Central 애플리케이션에서 일부 연결 설정을 가져와야 합니다.

1. IoT Central 애플리케이션에 로그인합니다.

1. **관리 > 디바이스 연결** 로 이동합니다.

1. **ID 범위** 를 기록해 둡니다. 이 값은 나중에 사용합니다.

1. **SaS-IoT-Devices** 등록 그룹을 선택합니다. 공유 액세스 서명 기본 키를 기록해 둡니다. 이 값은 나중에 사용합니다.

### <a name="set-up-a-compute-engine"></a>컴퓨팅 엔진 설정

이 시나리오에서는 IoT Central 디바이스 브리지와 동일한 Azure Functions 배포를 사용합니다. 디바이스 브리지를 배포하려면 아래 **Azure에 배포** 단추를 선택하고 다음 표의 정보를 사용하여 **사용자 지정 배포** 양식을 완료합니다.

| 필드 | 값 |
| ----- | ----- |
| Resource group | `egress-scenario`이라는 새 리소스 그룹 만들기 |
| 지역 | 가장 가까운 지역을 선택합니다. |
| 범위 ID | 이전에 기록해 둔 **ID 범위** 를 사용합니다. |
| IoT Central SAS 키 | **SaS-IoT-Devices** 등록 그룹에 대해 공유 액세스 서명 기본 키를 사용합니다. 이 값은 이전에 기록해 두었습니다. |

[![Azure에 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json).

**검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다. **egress-scenario** 리소스 그룹에서 Azure 함수 및 관련 리소스를 만드는 데 몇 분이 걸립니다.

### <a name="transform-device-data-in-the-device-bridge"></a>디바이스 브리지에서 디바이스 데이터 변환

내보낸 디바이스 데이터를 변환하도록 디바이스 브리지를 구성하려면:

1. Open Weather 서비스에서 애플리케이션 API 키를 가져옵니다. 서비스 사용이 제한된 계정은 무료입니다. 애플리케이션 API 키를 만들려면 [Open Weather 서비스 포털](https://openweathermap.org/)에서 계정을 만들고 지침을 따릅니다. 나중에 Open Weather API 키를 사용합니다.

1. Azure Portal에서 **egress-scenario** 리소스 그룹에서 함수 앱으로 이동합니다.

1. 왼쪽 탐색 영역의 **개발 도구** 에서 **App Service 편집기(미리 보기)** 를 선택합니다.

1. **이동&rarr;** 을 선택하여 **App Service 편집기** 페이지를 엽니다. 다음과 같이 변경합니다.

    1. *wwwroot/IoTCIntegration/index.js* 파일을 엽니다. 이 파일의 모든 코드를 [index.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js)의 코드로 바꿉니다.

    1. 새 *index.js* 에서 이전에 가져온 Open Weather API 키를 사용하여 `openWeatherAppId` 변수 파일을 업데이트합니다.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. 함수가 IoT Central에 전송한 데이터에 메시지 속성을 추가합니다. IoT Central은 이 속성을 사용하여 변환된 데이터 내보내기를 차단합니다. 이렇게 변경하려면 *wwwroot/IoTCIntegration/lib/engine.js* 파일을 엽니다. 다음 코드를 찾습니다.

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        이전 코드 조각의 코드 뒤에 다음 코드를 추가합니다.

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        참조를 위해 [engine.js](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js) 파일의 완료된 예제를 볼 수 있습니다.

1. **App Service 편집기** 의 왼쪽 탐색 영역에서 **콘솔** 을 선택합니다. 다음 명령을 실행하여 필요한 패키지를 설치합니다.

    ```bash
    cd IoTCIntegration
    npm install
    ```

    이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다.

1. **Azure 함수 개요** 페이지로 돌아가서 함수를 다시 시작합니다.

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="함수 다시 시작":::

1. 왼쪽 탐색 영역에서 **함수** 를 선택합니다. 그런 다음, **IoTCIntegration** 을 선택합니다. **코드 + 테스트** 를 선택합니다.

1. 함수 URL을 기록해 둡니다. 나중에 이 값이 필요합니다.

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="함수 URL 가져오기":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>IoT Central에서 디바이스 브리지로 데이터 흐름 사용

이 섹션에서는 Azure IoT Central 애플리케이션을 설정하는 방법을 설명합니다.

먼저 [디바이스 모델](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) 파일을 로컬 머신에 저장합니다.

IoT Central 애플리케이션에 디바이스 템플릿을 추가하려면 IoT Central 애플리케이션으로 이동한 후 다음을 수행합니다.

1. IoT Central 애플리케이션에 로그인하고 **디바이스 템플릿** 페이지로 이동합니다.

1. **+ 새로 만들기** 를 선택하고, **IoT 디바이스** 를 선택하고, **다음: 사용자 지정** 을 선택하고, 템플릿 이름으로 ‘계산 모델’을 입력합니다. 완료되면 **다음: 검토** 를 클릭합니다. 그런 다음 **만들기** 를 선택합니다.

1. **모델 가져오기** 를 선택하고 이전에 다운로드한 *model.json* 으로 이동합니다.

1. 모델을 가져온 후 **게시** 를 선택하여 **계산 모델** 디바이스 템플릿을 게시합니다.

데이터를 디바이스 브리지로 전송하도록 데이터 내보내기를 설정하려면:

1. IoT Central 애플리케이션에서 **데이터 내보내기** 를 선택합니다.

1. **+ 새 대상** 을 선택하여 디바이스 브리지에서 사용할 대상을 만듭니다. 대상 ‘계산 함수’를 호출하고 **대상 형식** 에 대해 **웹후크** 를 선택합니다. 콜백 URL에 대해 이전에 기록해 둔 함수 URL에서 붙여넣기를 선택합니다. **권한 부여** 를 **인증 없음** 으로 유지합니다.

1. 변경 내용을 저장합니다.

1. **+ 새 내보내기** 를 선택하고 ‘계산 내보내기’라는 데이터 내보내기를 만듭니다.

1. 사용 중인 디바이스 템플릿의 디바이스 데이터만 내보내는 필터를 추가합니다. **+ 필터** 를 선택하고, 항목 **디바이스 템플릿** 을 선택하고, 연산자 **같음** 을 선택하고, 방금 만든 **계산 모델** 디바이스 템플릿을 선택합니다.

1. 변환된 데이터와 변환되지 않은 데이터를 구별하는 메시지 필터를 추가합니다. 이 필터는 변환된 값을 다시 디바이스 브리지로 보내는 것을 차단합니다. **+ 메시지 속성 필터** 를 선택하고, 이름 값으로 *computed* 를 입력한 다음, 연산자 **없음** 을 선택합니다. 문자열 `computed`는 디바이스 브리지 예제 코드에서 키워드로 사용됩니다.

1. 대상에 대해 이전에 만든 **계산 함수** 대상을 선택합니다.

1. 변경 내용을 저장합니다. 1분 정도 후에 **내보내기 상태** 가 **정상** 으로 표시됩니다.

### <a name="verify"></a>확인

시나리오를 테스트하는 데 사용하는 샘플 디바이스는 Node.js로 작성됩니다. 로컬 머신에 Node.js 및 NPM이 설치되어 있는지 확인합니다. 이 필수 조건을 설치하지 않으려면 미리 설치되어 있는 [Azure Cloud Shell](https://shell.azure.com/)을 사용합니다.

시나리오를 테스트하는 샘플 디바이스를 실행하려면:

1. 샘플 코드를 포함하는 GitHub 리포지토리를 복제하고 다음 명령을 실행합니다.

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. 샘플 디바이스를 IoT Central 애플리케이션에 연결하려면 *iot-central-compute/device/device.js* 파일에서 연결 설정을 편집합니다. 범위 ID 및 그룹 SAS 키를 이전에 기록해 둔 값으로 바꿉니다.

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    변경 내용을 저장합니다.

1. 다음 명령을 사용하여 필요한 패키지를 설치하고 디바이스를 실행합니다.

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. 이 명령의 결과는 다음 출력과 같습니다.

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. IoT Central 애플리케이션에서 **computeDevice** 라는 디바이스로 이동합니다. **원시 데이터** 보기에는 5초 정도마다 표시되는 두 개의 원격 분석 스트림이 있습니다. 모델링되지 않은 데이터가 포함된 스트림은 원래 원격 분석이고, 모델링된 데이터가 있는 스트림은 함수가 변환한 데이터입니다.

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="원본 및 변환된 원시 데이터를 보여 주는 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

이 가이드의 단계를 수행하는 동안 만든 Azure 리소스가 더 이상 필요하지 않으면 [Azure Portal에서 리소스 그룹](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups)을 삭제합니다.

이 가이드에서 사용한 두 개의 리소스 그룹은 **ingress-scenario** 및 **egress-scenario** 입니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 수신 및 발신 시 둘 다 IoT Central에 대해 디바이스 데이터를 변환하는 여러 가지 옵션에 관해 알아보았습니다. 이 문서에는 두 가지 특정 시나리오에 관한 연습이 포함되었습니다.

- IoT Edge 모듈을 사용하여 데이터가 IoT Central 애플리케이션에 전송되기 전에 다운스트림 디바이스의 데이터를 변환합니다.
- Azure Functions를 사용하여 IoT Central 외부에서 데이터를 변환합니다. 이 시나리오에서 IoT Central은 데이터 내보내기를 사용하여 변환할 들어오는 데이터를 Azure 함수에 전송합니다. 함수는 변환된 데이터를 다시 IoT Central 애플리케이션에 전송합니다.

Azure IoT Central 애플리케이션 외부에서 디바이스 데이터를 변환하는 방법을 알아보았으므로 이제 [분석을 사용하여 IoT Central에서 디바이스 데이터를 분석하는 방법](howto-create-analytics.md)을 알아볼 수 있습니다.
