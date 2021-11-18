---
title: 다운스트림 IoT Edge 디바이스 연결 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 디바이스를 구성하여 Azure IoT Edge 게이트웨이 디바이스에 연결하는 방법입니다.
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 7b76f98d13e959529aab2c16776ed34cb0f1a906
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706688"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>다운스트림 IoT Edge 디바이스를 Azure IoT Edge 게이트웨이에 연결

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

이 문서에서는 IoT Edge 게이트웨이와 다운스트림 IoT Edge 디바이스 간에 신뢰할 수 있는 연결을 설정하는 방법에 대한 지침을 제공합니다.

게이트웨이 시나리오에서 IoT Edge 디바이스는 게이트웨이자 다운스트림 디바이스일 수 있습니다. 여러 IoT Edge 게이트웨이를 계층화하여 디바이스 계층 구조를 만들 수 있습니다. 다운스트림(또는 자식) 디바이스는 게이트웨이(또는 부모) 디바이스를 통해 메시지를 인증하고 보내거나 받을 수 있습니다.

게이트웨이 계층 구조의 IoT Edge 디바이스에는 두 가지 구성이 있으며, 이 문서에서는 두 가지를 모두 다룹니다. 첫 번째는 **최상위 계층** IoT Edge 디바이스입니다. 여러 IoT Edge 디바이스가 서로 연결되면, 부모 디바이스는 없지만 IoT Hub에 직접 연결되는 디바이스는 최상위 계층에 있는 것으로 간주합니다. 이 디바이스는 모든 하위 디바이스의 요청을 처리합니다. 두 번째 구성은 계층 구조의 **하위 계층** 에 있는 모든 IoT Edge 디바이스에 해당합니다. 이러한 디바이스는 다른 다운스트림 IoT 및 IoT Edge 디바이스를 위한 게이트웨이의 역할을 맡을 수 있지만 여전히 해당 부모 디바이스를 통해 통신을 라우팅해야 합니다.

일부 네트워크 아키텍처에서는 계층 구조의 상위 IoT Edge 디바이스만 클라우드에 연결할 수 있습니다. 이 구성에서 계층 구조의 하위 계층에 있는 모든 IoT Edge 디바이스는 자신의 게이트웨이(또는 부모) 디바이스 및 다운스트림(또는 자식) 디바이스와만 통신할 수 있습니다.

이 문서의 모든 단계는 [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md) 페이지를 기반으로 합니다. 이는 IoT Edge 디바이스를 다운스트림 IoT 디바이스의 게이트웨이로 설정합니다. 모든 게이트웨이 시나리오에는 다음과 같은 동일한 기본 단계가 적용됩니다.

* **인증**: 게이트웨이 계층 구조의 모든 디바이스에 대한 IoT Hub ID를 만듭니다.
* **권한 부여**: IoT Hub에서 부모/자식 관계를 설정하여 IoT Hub에 연결하는 것과 같이 관련 부모 디바이스에 연결할 수 있는 자식 디바이스에 권한을 부여합니다.
* **게이트웨이 검색**: 자식 디바이스가 로컬 네트워크에서 해당 부모 디바이스를 찾을 수 있는지 확인합니다.
* **보안 연결**: 동일한 체인에 포함된 신뢰할 수 있는 인증서를 사용하여 보안 연결을 설정합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 무료 또는 표준 IoT Hub.
* 최상위 계층 디바이스 한 개 및 하위 계층 디바이스 한 개 이상을 포함하는 두 개 이상의 **IoT Edge 디바이스**. IoT Edge 디바이스를 사용할 수 없는 경우 [Ubuntu 가상 머신에서 Azure IoT Edge를 실행](how-to-install-iot-edge-ubuntuvm.md)할 수 있습니다.
* Azure CLI를 사용하여 디바이스를 만들고 관리하는 경우 Azure IoT 확장 v0.10.6 이상이 설치된 Azure CLI v2.3.1을 보유해야 합니다.

이 문서에서는 시나리오에 적합한 게이트웨이 계층 구조를 만드는 데 도움이 되는 자세한 단계와 옵션을 제공합니다. 단계별 자습서는 [게이트웨이를 사용하여 IoT Edge 디바이스의 계층 구조 만들기](tutorial-nested-iot-edge.md)를 참조하세요.

## <a name="create-a-gateway-hierarchy"></a>게이트웨이 계층 구조 만들기

시나리오에서 IoT Edge 디바이스에 대한 부모/자식 관계를 정의하여 IoT Edge 게이트웨이 계층 구조를 만듭니다. 새 디바이스 ID를 만들 때 부모 디바이스를 설정하거나 기존 디바이스 ID의 부모 및 자식 관계를 관리할 수 있습니다.

부모/자식 관계를 설정하는 단계는 자식 디바이스가 IoT Hub에 연결하는 것과 같이 부모 디바이스에 연결할 수 있도록 권한을 부여합니다.

IoT Edge 디바이스만 부모 디바이스가 될 수 있지만 IoT Edge 디바이스 및 IoT 디바이스는 모두 자식 디바이스가 될 수 있습니다. 부모는 여러 자식을 가지지만 자식은 한 부모만을 가질 수 있는 것과 같습니다. 게이트웨이 계층 구조는 하나의 디바이스의 자식이 다른 디바이스의 부모가 되도록 부모/자식 집합을 함께 연결하여 생성됩니다.

기본적으로 부모에는 최대 100명의 자식이 있을 수 있습니다. 부모 디바이스의 edgeHub 모듈에서 **MaxConnectedClients** 환경 변수를 설정하여 이 제한을 변경할 수 있습니다.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 새 디바이스 ID를 만들 때, 또는 기존 디바이스를 편집하여 부모/자식 관계를 관리할 수 있습니다.

새 IoT Edge 디바이스를 만들 때 해당 허브의 기존 IoT Edge 디바이스 목록에서 부모 및 자식 디바이스를 선택하는 옵션이 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택합니다.
1. **IoT Edge 디바이스 추가** 를 선택합니다.
1. 디바이스 ID 및 인증 설정과 함께 **부모 디바이스를 설정** 하거나 **자식 디바이스를 선택** 할 수 있습니다.
1. 부모 또는 자식으로 사용할 디바이스를 하나 이상 선택합니다.

기존 디바이스에 대한 부모/자식 관계를 만들거나 관리할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택합니다.
1. **IoT Edge 디바이스** 목록에서 관리하려는 디바이스를 선택합니다.
1. **부모 디바이스 설정** 또는 **자식 디바이스 관리** 를 선택합니다.
1. 부모 또는 자식 디바이스를 추가하거나 제거합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 [azure-iot](/cli/azure/iot) 확장은 IoT 리소스를 관리하는 명령을 제공합니다. 새 디바이스 ID를 만들거나 기존 디바이스를 편집하여 IoT 및 IoT Edge 디바이스의 부모/자식 관계를 관리할 수 있습니다.

[az iot hub device-identity](/cli/azure/iot/hub/device-identity) 명령 집합을 사용하여 지정된 디바이스에 대한 부모/자식 관계를 관리할 수 있습니다.

이 `create` 명령에는 디바이스를 만들 때 자식 디바이스를 추가하고 부모 디바이스를 설정하기 위한 매개 변수가 포함되어 있습니다.

`add-children`,`list-children`, 및 `remove-children` 또는 `get-parent`와 `set-parent`를 비롯한 추가 디바이스-ID 명령을 사용하여 기존 디바이스에 대한 부모/자식 관계를 관리할 수 있습니다.

---

>[!NOTE]
>프로그래밍 방식으로 상위-하위 관계를 설정하려는 경우 C#, Java 또는 Node.js [IoT Hub 서비스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용할 수 있습니다.
>
>다음은 C# SDK를 사용하여 [자식 디바이스를 할당하는 예](https://github.com/Azure/azure-iot-sdk-csharp/blob/main/e2e/test/iothub/service/RegistryManagerE2ETests.cs)입니다. `RegistryManager_AddAndRemoveDeviceWithScope()` 작업은 프로그래밍 방식으로 3-계층 구조를 작성하는 방법을 보여 줍니다. IoT Edge 디바이스는 부모로 계층 1에 있습니다. 또 다른 IoT Edge 디바이스는 계층 2에 있으며 자식 및 부모 역할을 합니다. 마지막으로, IoT 디바이스는 가장 낮은 계층 자식 디바이스로 계층 3에 있습니다.

## <a name="prepare-certificates"></a>인증서 준비

동일한 게이트웨이 계층 구조의 여러 디바이스 전반에 일관된 인증서 체인을 설치하여 디바이스 사이의 보안 통신을 구축해야 합니다. IoT Edge 디바이스 또는 IoT 리프 디바이스와 관계없이 계층 구조의 모든 디바이스에는 동일한 루트 CA 인증서의 복사본이 필요합니다. 계층 구조의 각 IoT Edge 디바이스는 해당 루트 CA 인증서를 해당 디바이스 CA 인증서의 루트로 사용합니다.

이 설정을 사용하면 각 다운스트림 IoT Edge 디바이스 또는 IoT 리프 디바이스가 연결된 edgeHub에 공유 루트 CA 인증서로 서명된 서버 인증서가 있는지 확인하여 부모 ID를 확인할 수 있습니다.

<!-- TODO: certificate graphic -->

다음 인증서를 만듭니다.

* 지정된 게이트웨이 계층 구조의 모든 디바이스에 대한 최상위 공유 인증서인 **루트 CA 인증서** 입니다. 이 인증서는 모든 디바이스에 설치됩니다.
* 루트 인증서 체인에 포함하려는 **중간 인증서** 입니다.
* 루트 및 중간 인증서에 의해 생성된 **디바이스 CA 인증서** 및 **프라이빗 키** 입니다. 게이트웨이 계층 구조의 각 IoT Edge 디바이스에 대해 하나의 고유한 디바이스 CA 인증서가 필요합니다.

자체 서명된 인증 기관을 사용하거나 신뢰할 수 있는 상업적 인증 기관(예: Baltimore, Verisign, Digicert 또는 GlobalSign)에서 구매할 수 있습니다.

사용할 자체 인증서가 없는 경우 [IoT Edge 디바이스 기능을 테스트할 데모 인증서를 만들 수 있습니다](how-to-create-test-certificates.md). 이 문서의 단계에 따라 하나의 루트 및 중간 인증서 집합을 만든 다음 각 디바이스에 대한 IoT Edge 디바이스 CA 인증서를 만듭니다.

## <a name="configure-iot-edge-on-devices"></a>디바이스에 IoT Edge 구성

게이트웨이로 IoT Edge를 설정하는 단계는 다운스트림 디바이스로 IoT Edge를 설정하는 것과 매우 비슷합니다.

게이트웨이 검색을 사용하도록 설정하려면 모든 IoT Edge 게이트웨이 디바이스를 하위 디바이스가 로컬 네트워크에서 찾을 때 사용하는 **호스트 이름** 으로 구성되어야 합니다. 모든 다운스트림 IoT Edge 디바이스는 연결할 **parent_hostname** 으로 구성되어야 합니다. 단일 IoT Edge 디바이스가 부모 및 자식 디바이스 둘 다인 경우 두 매개 변수가 모두 필요합니다.

보안 연결을 사용하려면 게이트웨이 시나리오의 모든 IoT Edge 디바이스를 고유한 디바이스 CA 인증서와 게이트웨이 계층 구조의 모든 디바이스에서 공유하는 루트 CA 인증서의 복사본으로 구성해야 합니다.

디바이스에 IoT Edge가 이미 설치되어 있어야 합니다. 그렇지 않은 경우 [단일 Linux IoT Edge 디바이스를 수동으로 프로비전하는](how-to-provision-single-device-linux-symmetric.md)단계를 수행합니다.

이 섹션의 단계에서는 이 문서의 앞부분에서 설명한 **루트 CA 인증서** 및 **디바이스 CA 인증서와 프라이빗 키** 를 참조합니다. 다른 디바이스에서 인증서를 만든 경우 이 디바이스에서 해당 인증서를 사용할 수 있도록 합니다. USB 드라이브와 같이 [Azure Key Vault](../key-vault/general/overview.md)와 같은 서비스 또는 [보안 파일 복사](https://www.ssh.com/ssh/scp/)와 같은 기능을 사용하여 파일을 물리적으로 전송할 수 있습니다.

다음 단계를 사용하여 디바이스에 IoT Edge를 구성합니다.

사용자 **iotedge** 에 인증서 및 키를 보유하는 디렉터리에 대한 읽기 권한이 있는지 확인합니다.

1. 이 IoT Edge 디바이스에 **루트 CA 인증서** 를 설치합니다.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. 인증서 저장소 업데이트

   ```bash
   sudo update-ca-certificates
   ```

   이 명령은 /etc/ssl/certs에 인증서가 하나 추가되었다는 내용을 출력합니다.

1. IoT Edge 구성 파일을 엽니다.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >구성 파일이 아직 디바이스에 존재하지 않는 경우 다음 명령을 사용하여 템플릿 파일을 기준으로 구성 파일을 만듭니다.
   >
   >```bash
   >sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   >```

1. 구성 파일의 섹션에서 **호스트 이름** 을 찾습니다. `hostname` 매개 변수가 포함된 줄에서 주석 처리를 제거하고, 값을 IoT Edge 디바이스의 FQDN(정규화된 도메인 이름) 또는 IP 주소로 업데이트합니다.

   이 매개 변수 값은 다운스트림 디바이스에서 이 게이트웨이에 연결하는 데 사용하는 것입니다. 호스트 이름은 기본적으로 머신 이름을 사용하지만 다운스트림 디바이스를 연결하려면 FQDN 또는 IP 주소가 필요합니다.

   서버 인증서 일반 이름의 문자 제한으로 호스트 이름은 64자 이내여야 합니다.

   게이트웨이 계층 구조 전체에서 호스트 이름 패턴이 일관되어야 합니다. FQDN 또는 IP 주소 중 하나만 사용합니다.

1. ‘이 디바이스가 자식 디바이스인 경우’ **부모 호스트 이름** 섹션을 찾습니다. 부모 디바이스의 `parent_hostname` 구성 파일에서 호스트 이름으로 제공된 것과 일치하는 것과 부모 디바이스의 FQDN 또는 IP 주소가 되도록 매개 변수의 주석 처리를 제거하고 업데이트합니다.

1. **트러스트 번들 인증서** 섹션을 찾습니다. 디바이스에서 루트 CA 인증서에 대한 파일 URI를 사용하여 `trust_bundle_cert` 매개 변수의 주석 처리를 제거하고 업데이트합니다.

1. IoT Edge 디바이스가 시작될 때 올바른 버전의 IoT Edge 에이전트를 사용하는지 확인합니다.

   **기본 Edge 에이전트** 섹션을 찾아 이미지 값이 IoT Edge 버전 1.2인지 확인합니다. 그렇지 않은 경우 업데이트합니다.

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. 구성 파일에서 **에지 CA 인증서** 섹션을 찾습니다. 이 섹션에서 줄의 주석 처리를 제거하고 IoT Edge 디바이스의 인증서 및 키 파일에 대한 파일 URI 경로를 제공합니다.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. 구성 파일을 저장(`Ctrl+O`)하고 닫습니다(`Ctrl+X`).

1. 이전에 IoT Edge에 다른 인증서를 사용한 경우 다음 두 디렉터리의 파일을 삭제하여 새 인증서가 적용되었는지 확인합니다.

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

1. 구성에서 오류를 확인합니다.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge 검사 도구는 컨테이너를 사용하여 일부 진단 검사를 수행합니다. 다운스트림 IoT Edge 디바이스에서 이 도구를 사용하려면, `mcr.microsoft.com/azureiotedge-diagnostics:latest`에 엑세스하거나 또는 개인 컨테이너 레지스트리에 컨테이너 이미지를 포함할 수 있는지 확인합니다.

## <a name="network-isolate-downstream-devices"></a>네트워크 분리 다운스트림 디바이스

이 문서에서 지금까지의 단계는 게이트웨이 또는 다운스트림 디바이스로 IoT Edge 디바이스를 설정하고 둘 사이에 신뢰할 수 있는 연결을 만듭니다. 게이트웨이 디바이스는 인증 및 메시지 라우팅을 비롯하여 다운스트림 디바이스와 IoT Hub 간의 상호 작용을 처리합니다. 다운스트림 IoT Edge 디바이스에 배포된 모듈은 클라우드 서비스에 대한 자체 연결을 만들 수 있습니다.

ISA-95 표준을 따르는 것과 같은 일부 네트워크 아키텍처는 인터넷 연결 수를 최소화하는 것을 추구합니다. 이러한 시나리오에서는 직접 인터넷에 연결하지 않고도 다운스트림 IoT Edge 디바이스를 구성할 수 있습니다. 게이트웨이 디바이스를 통한 라우팅 IoT Hub 통신 외에도 다운스트림 IoT Edge 디바이스는 모든 클라우드 연결에 대해 게이트웨이 디바이스를 사용할 수 있습니다.

이 네트워크 구성에서는 게이트웨이 계층 구조의 최상위 계층에 있는 IoT Edge 디바이스만 클라우드에 직접 연결되어야 합니다. 하위 계층의 IoT Edge 디바이스는 관련 부모 디바이스 또는 자식 디바이스와만 통신할 수 있습니다. 게이트웨이 디바이스의 특수 모듈을 사용하여 다음을 포함한 이 시나리오를 사용할 수 있습니다.

* **API 프록시 모듈** 은 하위에 다른 IoT Edge 디바이스가 있는 모든 IoT Edge 게이트웨이에 필수입니다. 즉, 가장 하위 계층을 제외하고 게이트웨이 계층 구조의 *모든 계층* 에 필요합니다. 이 모듈에서는 [nginx](https://nginx.org) 역방향 프록시를 사용하여 단일 포트에 네트워크 계층을 통해 HTTP 데이터를 라우팅합니다. 모듈 쌍 및 환경 변수를 통해 구성 가능하므로 게이트웨이 시나리오 요구 사항에 맞게 조정할 수 있습니다.

* **Docker 레지스트리 모듈** 은 게이트웨이 계층 구조의 *최상위 계층* 의 IoT Edge 게이트웨이에 배포할 수 있습니다. 이 모듈은 하위 계층에서 모든 IoT Edge 디바이스를 대신하여 컨테이너 이미지를 검색하고 캐싱합니다. 최상위 계층에 이 모듈을 배포하는 대신 로컬 레지스트리를 사용하거나 컨테이너 이미지를 디바이스에 수동으로 로드하고 모듈 풀 정책을 **절대 사용하지 않음** 으로 설정합니다.

* **IoT Edge에서 Azure Blob Storage** 는 게이트웨이 계층 구조의 *최상위 계층* 에 있는 IoT Edge 게이트웨이에 배포할 수 있습니다. 이 모듈은 하위 계층에 있는 모든 IoT Edge 디바이스를 대신하여 Blob을 업로드합니다. Blob을 업로드하는 기능을 사용하면 모듈 로그 업로드 및 지원 번들 업로드와 같이 하위 계층에서 IoT Edge 디바이스에 유용한 문제 해결 기능을 사용할 수도 있습니다.

### <a name="network-configuration"></a>네트워크 구성

최상위 계층의 각 게이트웨이 디바이스에 대해 네트워크 운영자는 다음을 수행해야 합니다.

* 고정 IP 주소 또는 FQDN(정규화된 도메인 이름)을 제공합니다.
* 포트 443(HTTPS) 및 5671(AMQP)을 통해 이 IP 주소에서 Azure IoT Hub 호스트 이름으로 아웃바운드 통신 권한을 부여합니다.
* 포트 443(HTTPS)을 통해 이 IP 주소에서 Azure Container Registry 호스트 이름으로 아웃바운드 통신 권한을 부여합니다.

  API 프록시 모듈은 한 번에 하나의 컨테이너 레지스트리에 대한 연결만 처리할 수 있습니다. Microsoft Container Registry(mcr.microsoft.com)에서 제공하는 퍼블릭 이미지를 포함하여 모든 컨테이너 이미지를 개인 컨테이너 레지스트리에 저장하는 것이 좋습니다.

하위 계층의 각 게이트웨이 디바이스에 대해 네트워크 운영자는 다음을 수행해야 합니다.

* 고정 IP 주소 제공
* 포트 443(HTTPS) 및 5671(AMQP)을 통해 이 IP 주소에서 부모 게이트웨이의 IP 주소로의 아웃바운드 통신 권한을 부여합니다.

### <a name="deploy-modules-to-top-layer-devices"></a>최상위 계층 디바이스에 모듈 배포

게이트웨이 계층 구조의 최상위 계층에 있는 IoT Edge 디바이스에는 디바이스에서 실행할 수 있는 워크로드 모듈 외에도 배포해야 하는 필수 모듈 집합이 있습니다.

API 프록시 모듈은 대부분의 일반 게이트웨이 시나리오를 처리하기 위해 사용자 지정할 수 있도록 설계되었습니다. 이 문서에서는 기본 구성에서 모듈을 설정하는 방법과 예제를 제공합니다. 자세한 내용 및 예제는 [게이트웨이 계층 구조 시나리오에 대한 API 프록시 모듈 구성](how-to-configure-api-proxy-module.md)을 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택합니다.
1. **IoT Edge 디바이스** 목록에서 구성 중인 최상위 계층 디바이스를 선택합니다.
1. **모듈 설정** 을 선택합니다.
1. **IoT Edge 모듈** 섹션에서 **추가** 를 선택한 다음 **Marketplace 모듈** 을 선택합니다.
1. **IoT Edge API 프록시** 모듈을 검색하고 선택합니다.
1. 배포된 모듈 목록에서 API 프록시 모듈의 이름을 선택하고 다음 모듈 설정을 업데이트합니다.
   1. **환경 변수** 탭에서 **NGINX_DEFAULT_PORT** 값을 `443`으로 업데이트합니다.
   1. **컨테이너 만들기 옵션** 탭에서 포트 443을 참조하도록 포트 바인딩을 업데이트합니다.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   이러한 변경은 API 프록시 모듈이 포트 443에서 수신 대기하도록 구성합니다. 포트 바인딩 충돌을 방지하려면 edgeHub 모듈이 포트 443에서 수신 대기하지 않도록 구성해야 합니다. 대신, API 프록시 모듈은 포트 443에서 모든 edgeHub 트래픽을 라우팅합니다.

1. **런타임 설정** 을 선택하고 edgeHub 모듈 만들기 옵션을 찾습니다. 포트 5671 및 8883에 대한 바인딩을 그대로 두고 포트 443에 대한 포트 바인딩을 삭제합니다.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. **저장** 을 선택하여 변경 내용을 런타임 설정에 저장합니다.
1. **추가** 를 다시 선택하고 **IoT Edge 모듈** 을 선택합니다.
1. 배포에 Docker 레지스트리 모듈을 추가하려면 다음 값을 입력합니다.
   1. **IoT Edge 모듈 이름**: `registry`
   1. **모듈 설정** 탭의 **이미지 URI**: `registry:latest`
   1. **환경 변수** 탭에서 다음 환경 변수를 추가합니다.

      * **이름**: `REGISTRY_PROXY_REMOTEURL` **값**: 이 레지스트리 모듈을 매핑하고자 하는 컨테이너 레지스트리의 URL입니다. 예들 들어 `https://myregistry.azurecr`입니다.

        레지스트리 모듈은 하나의 컨테이너 레지스트리에만 매핑할 수 있으므로 모든 컨테이너 이미지를 단일 프라이빗 컨테이너 레지스트리에 포함하는 것이 좋습니다.

      * **이름**: `REGISTRY_PROXY_USERNAME` **값**: 컨테이너 레지스트리를 인증하는 사용자 이름입니다.

      * **이름**: `REGISTRY_PROXY_PASSWORD` **값**: 컨테이너 레지스트리를 인증하기 위한 암호입니다.

   1. **컨테이너 만들기 옵션** 탭에서 다음을 붙여넣습니다.

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. **추가** 를 선택하여 모듈을 배포에 추가합니다.
1. **다음을 선택: 다음 단계로 이동하기 위한 경로** 입니다.
1. 다운스트림 디바이스에서 IoT Hub에 도달하기 위해 디바이스-클라우드 메시지를 사용하도록 설정하려면 모든 메시지를 IoT Hub에 전달하는 경로를 포함합니다. 예를 들면 다음과 같습니다.
    1. **이름**: `Route`
    1. **값**: `FROM /messages/* INTO $upstream`
1. **검토 + 만들기** 를 선택하여 마지막 단계로 이동합니다.
1. **만들기** 를 선택하여 디바이스에 배포합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/)에서 배포 JSON 파일을 만듭니다. 예를 들면 다음과 같습니다.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": "{}"
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

   이 배포 파일은 포트 443에서 수신 대기하도록 API 프록시 모듈을 구성합니다. 포트 바인딩 충돌을 방지하기 위해 파일은 포트 443에서 수신하지 않도록 edgeHub 모듈을 구성합니다. 대신, API 프록시 모듈은 포트 443에서 모든 edgeHub 트래픽을 라우팅합니다.

1. 다음 명령을 입력하여 IoT Edge 디바이스에 대한 배포를 만듭니다.

   ```azurecli
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>하위 계층 디바이스에 모듈 배포

게이트웨이 계층 구조의 하위 계층에 있는 IoT Edge 디바이스에는 디바이스에서 실행하는 워크로드 모듈 외에도 배포해야 하는 필수 모듈이 하나 있습니다.

#### <a name="route-container-image-pulls"></a>컨테이너 이미지 풀 라우팅

게이트웨이 계층 구조의 IoT Edge 디바이스에 필요한 프록시 모듈에 관해 설명하기 전에 하위 계층의 IoT Edge 디바이스가 관련 모듈 이미지를 가져오는 방법을 이해해야 합니다.

하위 계층 디바이스를 클라우드에 연결할 수 없지만, 평소처럼 모듈 이미지를 풀하려면 게이트웨이 계층 구조의 최상위 계층 디바이스가 이러한 요청을 처리하도록 구성해야 합니다. 최상위 계층 디바이스는 컨테이너 레지스트리에 매핑되는 Docker **레지스트리** 모듈을 실행해야 합니다. 그런 다음 컨테이너 요청을 라우팅하도록 API 프록시 모듈을 구성합니다. 이에 관한 세부 정보는 이 문서의 이전 섹션에서 참고하세요. 이 구성에서 하위 계층 디바이스는 클라우드 컨테이너 레지스트리가 아닌 최상위 계층에서 실행되는 레지스트리를 가리켜야 합니다.

예를 들어 `mcr.microsoft.com/azureiotedge-api-proxy:1.0`를 호출하는 대신 하위 계층 디바이스는 `$upstream:443/azureiotedge-api-proxy:1.0`를 호출해야 합니다.

**$upstream** 매개 변수는 하위 계층 디바이스의 부모 디바이스를 가리키며 요청은 레지스트리 모듈에 컨테이너 요청을 라우팅하는 프록시 환경을 포함하는 최상위 계층에 도달할 때까지 모든 계층에 걸쳐 라우팅합니다. 이 예제의 `:443` 포트는 부모 디바이스의 API 프록시 모듈이 수신 대기 중인 포트로 대체되어야 합니다.

API 프록시 모듈은 하나의 레지스트리 모듈에만 라우팅할 수 있으며 각 레지스트리 모듈은 하나의 컨테이너 레지스트리에만 매핑할 수 있습니다. 따라서 하위 계층 디바이스가 풀하는 이미지는 단일 컨테이너 레지스트리에 저장되어야 합니다.

하위 계층 디바이스가 게이트웨이 계층 구조를 통해 모듈 풀 요청을 수행하지 않도록 하려는 경우 또 다른 옵션은 로컬 레지스트리 솔루션을 관리하는 것입니다. 또는 배포를 만들기 전에 모듈 이미지를 디바이스에 푸시한 다음 **imagePullPolicy** 를 **절대 사용하지 않음** 으로 설정합니다.

#### <a name="bootstrap-the-iot-edge-agent"></a>IoT Edge 에이전트 부트스트랩

IoT Edge 에이전트는 모든 IoT Edge 디바이스에서 처음으로 시작되는 런타임 구성 요소로, 다운스트림 IoT Edge 디바이스가 시작될 때 edgeAgent module 이미지에 액세스할 수 있어야 다음 배포에 액세스하고 나머지 모듈 이미지를 시작할 수 있습니다.

IoT Edge 디바이스의 구성 파일로 이동하여 해당 인증 정보, 인증서 및 부모 호스트 이름을 입력하려면 edgeAgent 컨테이너 이미지도 업데이트합니다.

최상위 게이트웨이 디바이스가 컨테이너 이미지 요청을 처리하도록 구성된 경우 `mcr.microsoft.com`을 부모 호스트 이름 및 API 프록시 수신 대기 포트로 바꿉니다. 배포 매니페스트에서는 `$upstream`를 바로 가기로 사용할 수 있지만 이 경우 edgeHub 모듈이 라우팅을 처리해야 하는데, 해당 모듈은 이 시점에서 시작되지 않았습니다. 예를 들면 다음과 같습니다.

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

로컬 컨테이너 레지스트리를 사용하거나 디바이스에서 수동으로 컨테이너 이미지를 제공하는 경우 구성 파일을 적절하게 업데이트합니다.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>런타임 구성 및 프록시 모듈 배포

**API 프록시 모듈** 은 클라우드와 모든 다운스트림 IoT Edge 디바이스 간의 모든 통신을 라우팅하는 데 필요합니다. 다운스트림 IoT Edge 디바이스가 없는 계층의 최하위 계층의 IoT Edge 디바이스에는 이 모듈이 필요하지 않습니다.

API 프록시 모듈은 대부분의 일반 게이트웨이 시나리오를 처리하기 위해 사용자 지정할 수 있도록 설계되었습니다. 이 문서에서는 기본 구성에서 모듈을 설정하는 단계에 관해 간략하게 설명합니다. 자세한 내용 및 예제는 [게이트웨이 계층 구조 시나리오에 대한 API 프록시 모듈 구성](how-to-configure-api-proxy-module.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
1. 탐색 메뉴에서 **IoT Edge** 를 선택합니다.
1. **IoT Edge 디바이스** 목록에서 구성 중인 하위 계층 디바이스를 선택합니다.
1. **모듈 설정** 을 선택합니다.
1. **IoT Edge 모듈** 섹션에서 **추가** 를 선택한 다음 **Marketplace 모듈** 을 선택합니다.
1. **IoT Edge API 프록시** 모듈을 검색하고 선택합니다.
1. 배포된 모듈 목록에서 API 프록시 모듈의 이름을 선택하고 다음 모듈 설정을 업데이트합니다.
   1. **모듈 설정** 탭에서 **이미지 URI** 값을 업데이트합니다. `mcr.microsoft.com`을 `$upstream:443`로 바꿉니다.
   1. **환경 변수** 탭에서 **NGINX_DEFAULT_PORT** 값을 `443`으로 업데이트합니다.
   1. **컨테이너 만들기 옵션** 탭에서 포트 443을 참조하도록 포트 바인딩을 업데이트합니다.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   이러한 변경은 API 프록시 모듈이 포트 443에서 수신 대기하도록 구성합니다. 포트 바인딩 충돌을 방지하려면 edgeHub 모듈이 포트 443에서 수신 대기하지 않도록 구성해야 합니다. 대신, API 프록시 모듈은 포트 443에서 모든 edgeHub 트래픽을 라우팅합니다.

1. **런타임 설정** 을 선택합니다.
1. EdgeHub 모듈 설정을 업데이트합니다.

   1. **이미지** 필드에서 `mcr.microsoft.com`을 `$upstream:443`로 바꿉니다.
   1. **만들기 옵션** 필드에서 포트 5671 및 8883에 대한 바인딩을 그대로 두고 포트 443에 대한 포트 바인딩을 삭제합니다.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. EdgeAgent 모듈 설정을 업데이트합니다.
   1. **이미지** 필드에서 `mcr.microsoft.com`을 `$upstream:443`로 바꿉니다.

1. **저장** 을 선택하여 변경 내용을 런타임 설정에 저장합니다.
1. **다음을 선택: 다음 단계로 이동하기 위한 경로** 입니다.
1. 다운스트림 디바이스에서 IoT Hub에 도달하기 위해 디바이스와 클라우드 간 메시지를 사용하도록 설정하려면 모든 메시지를 `$upstream`에 전달하는 경로를 포함합니다. 하위 계층 디바이스의 경우 업스트림 매개 변수는 부모 디바이스를 가리킵니다. 예를 들면 다음과 같습니다.
    1. **이름**: `Route`
    1. **값**: `FROM /messages/* INTO $upstream`
1. **검토 + 만들기** 를 선택하여 마지막 단계로 이동합니다.
1. **만들기** 를 선택하여 디바이스에 배포합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 디바이스를 게이트웨이로 사용하는 방법](iot-edge-as-gateway.md)

[게이트웨이 계층 구조 시나리오에 대한 API 프록시 모듈 구성](how-to-configure-api-proxy-module.md)