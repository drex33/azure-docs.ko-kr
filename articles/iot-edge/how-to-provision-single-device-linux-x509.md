---
title: X.509 인증서를 사용하여 Linux에서 IoT Edge 디바이스 만들기 및 프로비전 - Azure IoT Edge | Microsoft Docs
description: X.509 인증서를 사용하여 수동 프로비저닝을 위해 IoT Hub 단일 IoT Edge 디바이스 만들기 및 프로비전
author: v-tcassi
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: v-tcassi
ms.openlocfilehash: 5116774d0ea68f4d78dd0721a3546d9d82cbfdbd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273720"
---
# <a name="create-and-provision-an-iot-edge-device-on-linux-using-x509-certificates"></a>X.509 인증서를 사용하여 Linux에서 IoT Edge 디바이스 만들기 및 프로비전

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 IoT Edge 설치를 포함하여 Linux IoT Edge 디바이스를 등록하고 프로비전하는 엔드투엔드 지침을 제공합니다.

IoT 허브에 연결하는 모든 디바이스에는 클라우드-디바이스 또는 디바이스-클라우드 통신을 추적하는 데 사용되는 디바이스 ID가 있습니다. IoT 허브 호스트 이름, 디바이스 ID, 디바이스에서 IoT Hub 인증에 사용하는 정보를 포함하는 연결 정보로 디바이스를 구성합니다.

이 문서의 단계는 단일 디바이스를 IoT 허브에 연결하는 수동 프로비저닝이라는 프로세스를 안내합니다. 수동 프로비저닝을 위해 IoT Edge 디바이스를 인증하는 두 가지 옵션이 있습니다.

* **대칭 키:** IoT Hub 새 디바이스 ID를 만들 때 서비스에서 두 개의 키를 만듭니다. 디바이스에 키 중 하나를 저장하고 인증 시 IoT Hub 키를 제시합니다.

  이 인증 방식을 사용하면 빠르게 시작할 수 있지만 안전하지 않습니다.

* **X.509 자체 서명**: 두 개의 x.509 ID 인증서를 만들어 디바이스에 저장합니다. IoT Hub에서 새 디바이스 ID를 만들 때 두 인증서의 지문을 모두 제공합니다. 디바이스는 IoT Hub에 인증 시 인증서 하나를 제시되고 IoT Hub는 인증서가 지문과 일치하는지 확인합니다.

  이 인증 방법은 더 안전하며 프로덕션 시나리오에 권장됩니다.

이 문서에서는 X.509 인증서를 인증 방법으로 사용하는 방법을 다룹니다. 대칭 키를 사용하려면 대칭 [키를 사용하여 Linux에서 IoT Edge 디바이스 만들기 및 프로비저닝을 참조하세요.](how-to-provision-single-device-linux-symmetric.md)

> [!NOTE]
> 설정할 디바이스가 많고 각 디바이스를 수동으로 프로비저닝하지 않으려면 다음 문서 중 하나를 읽고 IoT Hub Device Provisioning Service에서 IoT Edge를 사용하는 방법을 알아보세요.
>
> * [X.509 인증서를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-x509.md)
> * [TPM을 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-auto-provision-simulated-device-linux.md)
> * [대칭 키를 사용하여 대규모로 IoT Edge 디바이스 만들기 및 프로비전](how-to-provision-devices-at-scale-linux-symmetric.md)

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 IoT Edge 디바이스 등록 및 디바이스에 IoT Edge 설치에 대해 설명합니다. 이러한 작업에는 이러한 작업을 수행하는 데 사용되는 다양한 필수 구성 조건 및 유틸리티가 있습니다. 계속하기 전에 모든 필수 구성요소가 있는지 확인합니다.

### <a name="device-registration"></a>디바이스 등록

**디바이스를** 등록하는 단계에 Azure Portal , **Visual Studio Code** 또는 **Azure CLI** 사용할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 조건이 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Azure 구독의 평가판 또는 표준 [IoT 허브](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

> [!NOTE]
> 현재 Visual Studio Code용 Azure IoT 확장은 x.509 인증서를 통한 디바이스 등록을 지원하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Azure 구독의 무료 또는 표준 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md).
* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.70 이상이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.

---

### <a name="iot-edge-installation"></a>IoT Edge 설치

X64, ARM32 또는 ARM64 Linux 디바이스.

Microsoft는 Ubuntu Server 18.04 및 Raspberry Pi OS Stretch 운영 체제용 설치 패키지를 제공합니다.

현재 프로덕션 시나리오에 지원되는 운영 체제에 대한 최신 정보는 [Azure IoT Edge 지원 시스템](support.md#operating-systems)을 참조하세요.

>[!NOTE]
>ARM64 디바이스 지원은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

X.509 인증서를 사용하여 수동으로 프로비저닝하려면 IoT Edge 버전 1.0.10 이상이 필요합니다.

X.509 인증서를 사용하여 IoT Edge 디바이스를 프로비저닝하는 경우에는 ‘디바이스 ID 인증서’를 사용합니다. 이 인증서는 IoT Edge 디바이스를 프로비저닝하고 Azure IoT Hub에서 디바이스를 인증하는 데만 사용됩니다. 다른 인증서에 서명하지 않는 리프 인증서입니다. 디바이스 ID 인증서는 IoT Edge 디바이스가 확인을 위해 모듈 또는 다운스트림 디바이스에 제시하는 CA(인증 기관) 인증서와는 별개입니다.

X.509 인증서 인증의 경우 각 디바이스의 인증 정보는 디바이스 ID 인증서에서 가져온 ‘지문’ 형태로 제공됩니다. 디바이스 등록 시 IoT Hub에 이 지문이 제공되어 서비스가 연결될 때 디바이스를 인식할 수 있습니다.

IoT Edge 디바이스에서 CA 인증서를 사용하는 방법에 대한 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해](iot-edge-certs.md)를 참조하세요.

X.509를 사용한 수동 프로비저닝에는 다음 파일이 필요합니다.

* .cer 또는. pem 형식의 일치하는 프라이빗 키 인증서를 포함하는 두 개의 디바이스 ID 인증서.

  하나의 인증서/키 파일 집합이 IoT Edge 런타임에 제공됩니다. 디바이스 ID 인증서를 만들 때 IoT 허브의 디바이스에 원하는 디바이스 ID로 인증서 일반 이름(CN)을 설정합니다.

* 디바이스 ID 인증서에서 가져온 지문.

  지문 값은 SHA-1 해시의 경우 40(16진수), SHA-256 해시의 경우 64(16진수)입니다. 디바이스 등록 시 IoT Hub에 두 지문이 모두 제공됩니다.

인증서가 없는 경우 [IoT Edge 디바이스 기능을 테스트하기 위한 데모 인증서](how-to-create-test-certificates.md)를 만들 수 있습니다. 이 문서의 안내에 따라 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만든 다음 두 개의 IoT Edge 디바이스 ID 인증서를 만듭니다.

인증서에서 지문을 검색하는 한 가지 방법은 다음 openssl 명령을 사용하는 것입니다.

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-your-device"></a>디바이스 등록

기본 설정에 따라 **Azure Portal**, **Visual Studio Code** 또는 **Azure CLI** 사용하여 디바이스를 등록할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal의 IoT 허브에서 IoT Edge 디바이스는 에지가 사용 설정되지 않은 IoT 디바이스와 별도로 만들어지고 관리됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택한 다음 **IoT Edge 디바이스 추가** 를 선택합니다.

   ![Azure Portal에서 IoT Edge 디바이스 추가](./media/how-to-provision-single-device-linux-x509/portal-add-iot-edge-device.png)

1. **디바이스 만들기** 페이지에서 다음 정보를 입력합니다.

   * 설명 디바이스 ID를 만듭니다. 나중에 사용할 수 있도록 이 디바이스 ID를 기록해 둡다.
   * 인증 유형으로 **X.509 자체 서명** 을 선택합니다.
   * 기본 및 보조 ID 인증서 지문을 제공합니다. 지문 값은 SHA-1 해시의 경우 40(16진수), SHA-256 해시의 경우 64(16진수)입니다.

1. **저장** 을 선택합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

현재 Visual Studio Code용 Azure IoT 확장은 x.509 인증서를 통한 디바이스 등록을 지원하지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 [az iot hub device-identity create](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브에서 새 디바이스 ID를 만듭니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

이 명령에는 다음 매개 변수가 포함됩니다.

* `--device-id` 또는 `-d`: IoT 허브에 고유한 설명 이름을 제공합니다. 다음 섹션에서 사용할 수 있도록 디바이스 ID를 기록해 둡니다.
* `hub-name` 또는 `-n`: IoT 허브의 이름을 제공합니다.
* `--edge-enabled` 또는 `--ee`: 디바이스가 IoT Edge 디바이스임을 선언합니다.
* `--auth-method` 또는 `--am`: 디바이스에서 사용하려는 인증 유형을 선언합니다. 이 경우에는 x.509 인증서 지문을 사용합니다.
* `--primary-thumbprint` 또는 `--ptp`: 기본 키로 사용할 x.509 인증서 지문을 제공합니다.
* `--secondary-thumbprint` 또는 `--stp`: 보조 키로 사용할 x.509 인증서 지문을 제공합니다.

---

이제 디바이스가 IoT Hub 등록했으므로 IoT Edge 런타임의 설치 및 프로비저닝을 완료하는 데 사용하는 정보를 검색합니다.

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>등록된 디바이스 보기 및 프로비전 정보 검색

X.509 인증서 인증을 사용하는 디바이스는 IoT Edge 런타임의 설치 및 프로비저닝을 완료하려면 해당 IoT Hub 이름, 디바이스 이름 및 인증서 파일이 필요합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

IoT Hub에 연결된 에지를 사용할 수 있는 모든 디바이스는 **IoT Edge** 페이지에 나열되어 있습니다.

![Azure Portal에서 IoT 허브 내 모든 IoT Edge 디바이스를 확인할 수 있습니다.](./media/how-to-provision-single-device-linux-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code 통해 X.509 인증서를 사용하여 디바이스 등록을 지원하지 않지만 필요한 경우 IoT Edge 디바이스를 볼 수 있습니다.

IoT Hub에 연결하는 모든 디바이스는 Visual Studio Code Explorer의 **Azure IoT Hub** 섹션에 나열됩니다. IoT Edge 디바이스는 아이콘이 다르고 **$edgeAgent** 및 **$edgeHub** 모듈이 각 IoT Edge 디바이스에 배포된다는 점에서 비 Edge 디바이스와 구분됩니다.

![VS Code를 사용하여 IoT 허브 내 모든 IoT Edge 디바이스 보기](./media/how-to-provision-single-device-linux-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) 명령을 사용하여 IoT 허브의 모든 디바이스를 볼 수 있습니다. 다음은 그 예입니다.

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge 디바이스로 등록된 모든 디바이스에서는 **capabilities.iotEdge** 속성이 **true** 로 설정됩니다.

---

## <a name="install-iot-edge"></a>IoT Edge 설치

이 섹션에서는 IoT Edge 위해 Linux 가상 머신 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

IoT Edge 런타임을 설치할 준비가 되기 전에 디바이스에서 완료해야 하는 두 단계가 있습니다. 디바이스는 Microsoft 설치 패키지에 액세스해야 하며 컨테이너 엔진이 설치되어 있어야 합니다.

### <a name="prepare-your-device-to-access-the-microsoft-installation-packages"></a>Microsoft 설치 패키지에 액세스하도록 디바이스 준비

1. 디바이스 운영 체제와 일치하는 리포지토리 구성을 설치합니다.

   * **Ubuntu Server 18.04**:

      ```bash
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
      ```

   * **Raspberry Pi OS Stretch**:

      ```bash
      curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
      ```

1. 생성된 목록을 sources.list.d 디렉터리에 복사합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 공개 키를 설치합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Azure IoT Edge 소프트웨어 패키지에는 각 패키지(`usr/share/doc/{package-name}` 또는 `LICENSE` 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

### <a name="install-a-container-engine-on-your-device"></a>디바이스에 컨테이너 엔진 설치

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 Moby 엔진을 사용하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby 컨테이너 엔진 설치 시 오류가 발생하면 Linux 커널의 Moby 호환성을 확인합니다. 일부 포함된 디바이스의 제조업체는 컨테이너 엔진 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함하는 디바이스 이미지를 제공합니다. Moby에서 제공한 [check-config 스크립트](https://github.com/moby/moby/blob/master/contrib/check-config.sh)를 사용하는 다음 명령을 실행하여 커널 구성을 확인합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

스크립트의 출력에서 `Generally Necessary` 및 `Network Drivers` 아래의 모든 항목을 사용할 수 있는지 확인합니다. 기능이 누락됐다면 소스에서 커널을 다시 빌드하고 적절한 커널 .config 내에 포함하기 위한 관련 모듈을 선택하여 활성화합니다. 마찬가지로 `defconfig` 또는 `menuconfig` 같은 커널 구성 생성기를 사용하는 경우 각각의 기능을 찾아 활성화하고 그에 따라 커널을 다시 빌드합니다. 새로 수정된 커널을 배포한 후에는 check-config 스크립트를 다시 실행하여 필요한 모든 기능이 성공적으로 활성화되었는지 확인합니다.

### <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 이 문서 뒷부분의 [오프라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

**libiothsm-std** 패키지와 함께 IoT Edge 버번 1.1.*을 설치합니다.

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge 버전 1.1은 IoT Edge의 장기 지원 분기입니다. 이전 버전을 실행하는 경우 이전 버전이 더 이상 지원되지 않으므로 최신 패치를 설치하거나 업데이트하는 것이 좋습니다.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 서비스는 IoT Edge 디바이스에서 보안 표준을 제공하고 유지 관리합니다. 서비스는 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

IoT ID 서비스는 IoT Edge 버전 1.2와 함께 도입되었습니다. 이 서비스는 IoT Edge 및 IoT Hub와 통신해야 하는 기타 디바이스 구성 요소에 대한 ID 프로비저닝 및 관리를 다룹니다.

이 섹션의 단계는 인터넷에 연결된 디바이스에 최신 버전을 설치하는 일반적인 프로세스를 보여 줍니다. 시험판 버전과 같은 특정 버전을 설치해야 하거나 오프라인 상태에서 설치해야 하는 경우 이 문서 뒷부분의 [오프라인 또는 특정 버전 설치](#offline-or-specific-version-installation-optional) 단계를 따르세요.

>[!NOTE]
>이 섹션의 단계에서는 IoT Edge 버전 1.2를 설치하는 방법을 보여줍니다.
>
>이전 버전을 실행하는 IoT Edge 디바이스가 이미 있고 1.2로 업그레이드하려는 경우 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)의 단계를 사용합니다. 버전 1.2는 업그레이드에 특정 단계가 필요한 이전 버전의 IoT Edge와 충분히 다릅니다.

디바이스에서 패키지 목록을 업데이트합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 및 IoT ID 서비스 버전을 확인합니다.

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

최신 버전의 IoT Edge 및 IoT id 서비스 패키지를 설치하려면 다음 명령을 사용합니다.

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>클라우드 ID를 사용한 디바이스 프로비전

이제 컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 수 있습니다. 이 단계에서는 클라우드 ID 및 인증 정보를 사용하여 디바이스를 설정합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

파일의 프로비전 구성을 찾고 **Manual provisioning configuration using an X.509 identity certificate** 섹션의 주석 처리를 제거합니다. 다른 프로비전 섹션이 주석 처리되었는지 확인합니다. **provisioning:** 줄 앞에 공백이 없고 중첩된 항목은 공백 두 칸으로 들여쓰기 되어 있는지 확인합니다.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

다음 필드를 업데이트합니다.

* **iothub_hostname**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예들 들어 `{IoT hub name}.azure-devices.net`입니다.
* **device_id**: 디바이스를 등록할 때 제공한 ID입니다.
* **identity_cert**: 디바이스의 ID 인증서에 대한 URI입니다. 예들 들어 `file:///path/identity_certificate.pem`입니다.
* **identity_pk**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 URI입니다. 예들 들어 `file:///path/identity_key.pem`입니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 설치의 일부로 제공되는 템플릿 파일을 기반으로 디바이스의 구성 파일을 만듭니다.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

IoT Edge 디바이스에서 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

파일의 **Provisioning** 섹션을 찾고 x.509 ID 인증서를 사용하여 수동 프로비전 줄의 주석 처리를 제거합니다. 다른 모든 프로비전 섹션을 주석으로 처리해야 합니다.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

다음 필드를 업데이트합니다.

* **iothub_hostname**: 디바이스가 연결될 IoT 허브의 호스트 이름입니다. 예들 들어 `{IoT hub name}.azure-devices.net`입니다.
* **device_id**: 디바이스를 등록할 때 제공한 ID입니다.
* **identity_cert**: 디바이스의 ID 인증서에 대한 URI입니다(예: `file:///path/identity_certificate.pem`). 또는 EST나 로컬 인증 기관을 사용하여 동적으로 인증서를 발급합니다.
* **identity_pk**: 제공된 ID 인증서의 프라이빗 키 파일에 대한 URI입니다(예: )`file:///path/identity_key.pem`. 또는 PKCS#11 URI를 제공한 다음 나중에 **PKCS#11** 섹션에 있는 구성 정보를 구성 파일에 제공합니다.

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에 프로비전 정보를 입력한 후 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>구성 성공 확인

IoT Edge 디바이스에서 런타임이 성공적으로 설치 및 구성되었는지 확인합니다.

>[!TIP]
>`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. IoT Edge 런타임을 설치한 후 처음으로 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 명령 앞에 `sudo`를 사용합니다.

IoT Edge 시스템 서비스가 실행되고 있는지 확인합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

성공적인 상태 응답은 `Ok`입니다.

::: moniker-end

서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

`check` 도구를 사용하여 디바이스의 구성 및 연결 상태를 확인합니다.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>사용 권한이 업데이트된 후에도 항상 `sudo`를 사용하여 검사 도구를 실행합니다. 검사 도구가 구성 상태를 확인하기 위해 구성 파일에 액세스하려면 상승된 권한이 필요합니다.

IoT Edge 디바이스에서 실행되는 모든 모듈을 봅니다. 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.

   ```bash
   sudo iotedge list
   ```

새 IoT Edge 디바이스를 만들면 Azure Portal에 상태 코드 `417 -- The device's deployment configuration is not set`이 표시됩니다. 이 상태는 정상이며 디바이스가 모듈 배포를 수신할 준비가 되었음을 의미합니다.

## <a name="offline-or-specific-version-installation-optional"></a>오프라인 설치 또는 특정 버전 설치(선택 사항)

이 섹션의 단계는 표준 설치 단계에서 다루지 않는 시나리오를 위한 단계입니다. 다음을 포함할 수 있습니다.

* 오프라인 상태에서 IoT Edge 설치
* 릴리스 후보 버전 설치

`apt-get install`을 통해 제공되지 않는 특정 버전의 Azure IoT Edge 런타임을 설치하려는 경우 이 섹션의 단계를 사용합니다. Microsoft 패키지 목록에는 제한된 최신 버전과 해당 하위 버전의 집합만 포함되어 있으므로 이러한 단계는 이전 버전 또는 릴리스 후보 버전을 설치하려는 모든 사용자에게 해당합니다.

curl 명령을 사용하면 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)로 이동하고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전의 **자산** 섹션을 확장합니다.

3. 모든 릴리스에는 IoT Edge 보안 디먼 및 hsmlib에 대한 새 파일이 있어야 합니다. 오프라인 디바이스에 IoT Edge를 설치하려는 경우 미리 새 파일을 다운로드합니다. 그렇지 않으면 다음 명령을 사용하여 해당 구성 요소를 업데이트합니다.

   1. IoT Edge 디바이스의 아키텍처와 일치하는 **libiothsm-std** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   2. 다음 명령의 복사된 링크를 사용하여 해당 버전의 hsmlib를 설치합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. IoT Edge 디바이스의 아키텍처와 일치하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령의 복사된 링크를 사용하여 해당 버전의 IoT Edge 보안 디먼을 설치합니다.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>디바이스가 현재 IoT Edge 버전 1.1 이상을 실행하고 있는 경우 이 섹션의 단계를 수행하기 전에 **iotedge** 및 **libiothsm-std** 패키지를 제거합니다. 자세한 내용은 [1.0 또는 1.1에서 1.2로 업데이트](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)를 참조하세요.

1. [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)로 이동하고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전의 **자산** 섹션을 확장합니다.

3. 모든 릴리스에는 IoT Edge 및 ID 서비스에 대한 새 파일이 있어야 합니다. 오프라인 디바이스에 IoT Edge를 설치하려는 경우 미리 새 파일을 다운로드합니다. 그렇지 않으면 다음 명령을 사용하여 해당 구성 요소를 업데이트합니다.

   1. IoT Edge 디바이스의 아키텍처와 일치하는 **aziot-identity-service** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   2. 다음 명령의 복사된 링크를 사용하여 해당 버전의 ID 서비스를 설치합니다.

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. IoT Edge 디바이스의 아키텍처와 일치하는 **aziot-edge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭하고 링크 주소를 복사합니다.

   4. 다음 명령의 복사된 링크를 사용하여 해당 버전의 IoT Edge를 설치합니다.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

컨테이너 엔진과 IoT Edge 런타임이 디바이스에 설치되었으므로 다음 단계를 수행할 준비가 되었습니다. 이 단계에서는 [클라우드 ID를 사용하여 디바이스를 프로비전](#provision-the-device-with-its-cloud-identity)합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

디바이스에서 IoT Edge 설치를 제거하려면 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

구성 파일을 포함하여 IoT Edge와 연결된 모든 파일을 삭제하려면 `--purge` 플래그를 사용합니다. IoT Edge를 다시 설치하고 나중에 동일한 구성 정보를 사용하려면 이 플래그를 생략합니다.

IoT Edge 런타임을 제거하면 생성된 컨테이너는 중지되지만 디바이스에는 계속 남아 있습니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

```bash
sudo docker ps -a
```

두 개의 런타임 컨테이너를 포함하여 디바이스에서 컨테이너를 삭제합니다.

```bash
sudo docker rm -f <container name>
```

마지막으로 디바이스에서 컨테이너 런타임을 제거합니다.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>다음 단계

디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
