---
title: 디바이스 인증서 만들기 - Azure IoT Edge | Microsoft Docs
description: 프로덕션 배포를 준비하려면 Azure IoT Edge 디바이스에서 테스트 인증서를 만들어 설치하고 관리합니다.
author: kgremban
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3866217f0aa90cd3450d0f74e35eaa68cea3c559
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866547"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>IoT Edge 디바이스에서 인증서 관리

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

모든 IoT Edge 디바이스는 인증서를 사용하여 런타임과 디바이스에서 실행되는 모듈 간에 보안 연결을 만듭니다. 게이트웨이로 작동하는 IoT Edge 디바이스는 이러한 동일한 인증서를 사용하여 다운스트림 디바이스에도 연결합니다.

## <a name="install-production-certificates"></a>프로덕션 인증서 설치

IoT Edge를 처음 설치하고 디바이스를 프로비전할 때 서비스를 테스트할 수 있도록 디바이스가 임시 인증서로 설정됩니다.
이러한 임시 인증서는 90일 후에 만료되거나 컴퓨터를 다시 시작하여 다시 설정할 수 있습니다.
프로덕션 시나리오로 이동하거나 게이트웨이 디바이스를 만들려는 경우 사용자 고유의 인증서를 제공해야 합니다.
이 문서에서는 IoT Edge 디바이스에 인증서를 설치하는 단계를 보여줍니다.

여러 종류의 인증서 및 해당 역할에 대한 자세한 내용은 [Azure IoT Edge에서 인증서를 사용하는 방법 이해](iot-edge-certs.md)를 참조하세요.

>[!NOTE]
>이 문서 전체에서 사용되는 "루트 CA"라는 용어는 IoT 솔루션에 대한 인증서 체인의 최상위 기관 공용 인증서를 나타냅니다. 배포된 인증 기관의 인증서 루트나 조직의 인증 기관 루트를 사용할 필요는 없습니다. 대부분의 경우에는 실제로 중간 CA 공용 인증서입니다.

### <a name="prerequisites"></a>사전 요구 사항

* IoT Edge 디바이스.

  IoT Edge 디바이스를 설정하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 빠른 시작 문서 중 하나에 있는 단계에 따라 [가상 Linux 디바이스를 만들거나](quickstart-linux.md) [가상 Windows 디바이스를 만듭니다](quickstart.md).

* 자체 서명된 인증 기관을 사용하거나 신뢰할 수 있는 상업적 인증 기관(예: Baltimore, Verisign, DigiCert 또는 GlobalSign)에서 구매한 루트 CA(인증 기관) 인증서가 있어야 합니다.

  루트 인증 기관이 아직 없지만, 프로덕션 인증서(예: 게이트웨이 시나리오)가 필요한 IoT Edge 기능을 사용해 보려는 경우 [데모 인증서를 만들어 IoT Edge 디바이스 기능을 테스트](how-to-create-test-certificates.md)할 수 있습니다.

### <a name="create-production-certificates"></a>프로덕션 인증서 만들기

사용자 고유의 인증 기관을 사용하여 다음 파일을 만들어야 합니다.

* 루트 CA
* 디바이스 CA 인증서
* 디바이스 CA 프라이빗 키

이 문서에서 *루트 CA* 라고 하는 것은 조직에 대한 최상위 인증 기관이 아닙니다. IoT Edge 시나리오에 대한 최상위 인증 기관으로, IoT Edge 허브 모듈, 사용자 모듈 및 모든 다운스트림 디바이스 간 트러스트를 설정하는 데 사용합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

:::moniker-end

이러한 인증서의 예를 보려면 [샘플 및 자습서용 테스트 CA 인증서 관리](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)에서 데모 인증서를 만드는 스크립트를 검토합니다.

### <a name="install-certificates-on-the-device"></a>디바이스에 인증서 설치

IoT Edge 디바이스에 인증서 체인을 설치하고 새 인증서를 참조하도록 IoT Edge 런타임을 구성합니다.

3개의 인증서 및 키 파일을 IoT Edge 디바이스에 복사합니다. 

샘플 스크립트를 사용하여 [데모 인증서를 만든](how-to-create-test-certificates.md) 경우 3개의 인증서와 키 파일은 다음 경로에 있습니다.

* 디바이스 CA 인증서: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 디바이스 CA 프라이빗 키: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 루트 CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

[Azure Key Vault](../key-vault/index.yml) 또는 [보안 복사 프로토콜](https://www.ssh.com/ssh/scp/) 같은 기능을 사용하여 인증서 파일을 이동할 수 있습니다. IoT Edge 디바이스 자체에서 인증서를 생성한 경우 이 단계를 건너뛸 수 있으며 작업 디렉터리에 대한 경로를 사용합니다.

Windows에서 Linux용 IoT Edge를 사용하는 경우 Azure IoT Edge `id_rsa` 파일에 있는 SSH 키를 사용하여 호스트 OS와 Linux 가상 머신 간의 파일 전송을 인증해야 합니다. `Get-EflowVmAddr` 명령을 사용하여 Linux 가상 머신의 IP 주소를 검색합니다. 그런 후 다음 명령을 사용하여 인증된 SCP를 수행할 수 있습니다.

   ```powershell
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

### <a name="configure-iot-edge-with-the-new-certificates"></a>새 인증서로 IoT Edge 구성

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

# <a name="linux-containers"></a>[Linux 컨테이너](#tab/linux)

1. IoT Edge 보안 디먼 구성 파일: `/etc/iotedge/config.yaml`을 엽니다.

1. config.yaml의 **인증서** 속성을 IoT Edge 디바이스의 인증서 및 키 파일에 대한 파일 URI 경로로 설정합니다. 네 줄의 주석 처리를 제거하려면 인증서 속성 앞의 `#` 문자를 제거합니다. **인증서:** 줄 앞에 공백이 없고 중첩된 항목은 공백 두 칸으로 들여쓰기 되어 있는지 확인합니다. 예를 들면 다음과 같습니다.

   ```yaml
   certificates:
      device_ca_cert: "file:///<path>/<device CA cert>"
      device_ca_pk: "file:///<path>/<device CA key>"
      trusted_ca_certs: "file:///<path>/<root CA cert>"
   ```

1. 사용자 **iotedge** 에 인증서를 보유하는 디렉터리에 대한 읽기 권한이 있는지 확인합니다.

1. 이전에 디바이스에서 IoT Edge에 대한 다른 인증서를 사용한 경우 IoT Edge를 시작하거나 다시 시작하기 전에 다음 두 디렉터리에서 파일을 삭제합니다.

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. IoT Edge를 다시 시작합니다.

   ```bash
   sudo iotedge system restart
   ```

# <a name="windows-containers"></a>[Windows 컨테이너](#tab/windows)

1. IoT Edge 보안 디먼 구성 파일: `C:\ProgramData\iotedge\config.yaml`을 엽니다.

1. config.yaml의 **인증서** 속성을 IoT Edge 디바이스의 인증서 및 키 파일에 대한 파일 URI 경로로 설정합니다. 네 줄의 주석 처리를 제거하려면 인증서 속성 앞의 `#` 문자를 제거합니다. **인증서:** 줄 앞에 공백이 없고 중첩된 항목은 공백 두 칸으로 들여쓰기 되어 있는지 확인합니다. 예를 들면 다음과 같습니다.

   ```yaml
   certificates:
      device_ca_cert: "file:///C:/<path>/<device CA cert>"
      device_ca_pk: "file:///C:/<path>/<device CA key>"
      trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
   ```

1. 이전에 디바이스에서 IoT Edge에 대한 다른 인증서를 사용한 경우 IoT Edge를 시작하거나 다시 시작하기 전에 다음 두 디렉터리에서 파일을 삭제합니다.

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. IoT Edge를 다시 시작합니다.

   ```powershell
   Restart-Service iotedge
   ```
---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge 보안 디먼 구성 파일: `/etc/aziot/config.toml`을 엽니다.

1. 파일의 시작 부분에서 `trust_bundle_cert` 매개 변수를 찾습니다. 이 줄의 주석 처리를 제거하고 디바이스의 루트 CA 인증서에 대한 파일 URI를 제공합니다.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. config.toml 파일에서 `[edge_ca]` 섹션을 찾습니다. 이 섹션에서 줄의 주석 처리를 제거하고 IoT Edge 디바이스의 인증서 및 키 파일에 대한 파일 URI 경로를 제공합니다.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. 서비스에 인증서 및 키를 보유하는 디렉터리에 대한 읽기 권한이 있는지 확인합니다.

   * 프라이빗 키 파일은 **aziotks** 그룹이 소유해야 합니다.
   * 인증서 파일은 **aziotcs** 그룹이 소유해야 합니다.

   >[!TIP]
   >인증서가 읽기 전용인 경우, 즉 사용자가 인증서를 만들었고 IoT Edge 서비스에서 인증서를 교체하지 않도록 하려면 프라이빗 키 파일을 모드 0440으로 설정하고 인증서 파일을 모드 0444로 설정합니다. 초기 파일을 만든 다음 나중에 인증서를 교체하도록 인증서 서비스를 구성한 경우 프라이빗 키 파일을 모드 0660으로 설정하고 인증서 파일을 모드 0664로 설정합니다.

1. 이전에 디바이스에서 IoT Edge에 대해 다른 인증서를 사용한 적이 있는 경우 다음 디렉터리에서 파일을 삭제합니다. IoT Edge는 사용자가 제공한 새 CA 인증서를 사용하여 이를 다시 만듭니다.

   * `/var/lib/aziot/certd/certs`

1. 구성 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="customize-certificate-lifetime"></a>인증서 수명 사용자 지정

IoT Edge은 다음을 비롯한 여러 가지 사례에 디바이스에서 인증서를 자동으로 생성합니다.

* IoT Edge를 설치하고 프로비전할 때 사용자 고유의 프로덕션 인증서를 제공하지 않으면 IoT Edge 보안 관리자에서 자동으로 **디바이스 CA 인증서** 를 생성합니다. 이 자체 서명된 인증서는 프로덕션이 아닌 개발 및 테스트 시나리오에만 사용할 수 있습니다. 이 인증서는 90일 후에 만료됩니다.
* IoT Edge 보안 관리자는 디바이스 CA 인증서로 서명된 **워크로드 CA 인증서** 도 생성합니다.

IoT Edge 디바이스의 다양한 인증서 기능에 대한 자세한 내용은 [Azure IoT Edge가 인증서를 사용하는 방법 이해](iot-edge-certs.md)를 참조하세요.

이러한 두 개의 자동으로 생성된 인증서의 경우 구성 파일에서 플래그를 설정하여 인증서의 수명 기간(일)을 구성할 수 있습니다.

>[!NOTE]
>IoT Edge 보안 관리자에서 만든 세 번째 자동 생성 인증서인 **IoT Edge 허브 서버 인증서** 가 있습니다. 이 인증서는 항상 90일의 수명 기간을 갖지만 만료되기 전에 자동으로 갱신됩니다. 구성 파일에 설정된 자동 생성 CA 수명 값은 이 인증서에 영향을 주지 않습니다.

지정된 일 수 후에 만료되면 디바이스 CA 인증서를 다시 생성하기 위해 IoT Edge를 다시 시작해야 합니다. 디바이스 CA 인증서는 자동으로 갱신되지 않습니다.

<!-- 1.1. -->
:::moniker range="iotedge-2018-06"

# <a name="linux-containers"></a>[Linux 컨테이너](#tab/linux)

1. 인증서 만료를 기본 90일이 아닌 다른 값으로 구성하려면 구성 파일의 **인증서** 섹션에 값(일)을 추가합니다.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

1. 이전에 생성된 인증서를 제거하려면 `hsm` 폴더의 내용을 삭제합니다.

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. IoT Edge 서비스를 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

1. 수명 기간 설정을 확인합니다.

   ```bash
   sudo iotedge check --verbose
   ```

   자동으로 생성된 디바이스 CA 인증서가 만료될 때까지 남은 일 수를 나열하는 **프로덕션 준비 상태: 인증서** 확인의 출력을 확인합니다.

# <a name="windows-containers"></a>[Windows 컨테이너](#tab/windows)

1. 인증서 만료를 기본 90일이 아닌 다른 값으로 구성하려면 구성 파일의 **인증서** 섹션에 값(일)을 추가합니다.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

1. 이전에 생성된 인증서를 제거하려면 `hsm` 폴더의 내용을 삭제합니다.

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. IoT Edge 서비스를 다시 시작합니다.

   ```powershell
   Restart-Service iotedge
   ```

1. 수명 기간 설정을 확인합니다.

   ```powershell
   iotedge check --verbose
   ```

   자동으로 생성된 디바이스 CA 인증서가 만료될 때까지 남은 일 수를 나열하는 **프로덕션 준비 상태: 인증서** 확인의 출력을 확인합니다.

---

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 인증서 만료를 기본 90일이 아닌 다른 값으로 구성하려면 구성 파일의 **Edge CA 인증서(빠른 시작)** 섹션에 값(일)을 추가합니다.

   ```toml
   [edge_ca]
   auto_generated_edge_ca_expiry_days = <value>
   ```

1. 이전에 생성된 인증서를 제거하려면 `certd` 및 `keyd` 폴더의 내용을 삭제합니다. `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. 구성 변경 내용을 적용합니다.

   ```bash
   sudo iotedge config apply
   ```

1. 새 수명 설정을 확인합니다.

   ```bash
   sudo iotedge check --verbose
   ```

   자동으로 생성된 디바이스 CA 인증서가 만료될 때까지 남은 일 수를 나열하는 **프로덕션 준비 상태: 인증서** 확인의 출력을 확인합니다.
:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>다음 단계

프로덕션에 솔루션을 배포하기 전에 IoT Edge 디바이스에 인증서를 설치해야 합니다. [프로덕션에서 IoT Edge 솔루션 배포를 준비](production-checklist.md)하는 방법에 대해 자세히 알아보세요.
