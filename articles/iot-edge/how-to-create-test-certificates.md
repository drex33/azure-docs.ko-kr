---
title: 테스트 인증서 만들기 - Azure IoT Edge | Microsoft Docs
description: 테스트 인증서를 만들고 프로덕션 배포를 위해 Azure IoT Edge 디바이스에 설치하는 방법을 알아봅니다.
author: kgremban
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 460c8f4d5d59b0f43d0706587dafab60289b1984
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610981"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 디바이스에는 런타임, 모듈 및 다운스트림 디바이스 간의 보안 통신용 인증서가 필요합니다.
필요한 인증서를 만들 수 있는 인증 기관이 없는 경우 데모 인증서를 사용하여 테스트 환경에서 IoT Edge 기능을 테스트해 볼 수 있습니다.
이 문서에서는 IoT Edge에서 테스트를 위해 제공하는 인증서 생성 스크립트의 기능을 설명합니다.

이러한 인증서는 30일 후에 만료되며 프로덕션 시나리오에서 사용할 수 없습니다.

모든 머신에서 인증서를 만든 다음 IoT Edge 디바이스에 복사할 수 있습니다.
IoT Edge 디바이스 자체에서 인증서를 생성하는 대신 기본 머신을 사용하여 인증서를 만드는 것이 더 간편합니다.
기본 머신을 사용하여 스크립트를 한 번 설정하고 이를 사용하여 여러 디바이스에 대한 인증서를 만들 수 있습니다.

IoT Edge 시나리오를 테스트하기 위한 데모 인증서를 만들려면 다음 단계를 따르세요.

1. 디바이스에서 인증서를 생성하는 [스크립트를 설정](#set-up-scripts)합니다.
2. 시나리오에 대한 다른 모든 인증서에 서명하는 데 사용하는 [루트 CA 인증서를 만듭니다](#create-root-ca-certificate).
3. 테스트하려는 시나리오에 필요한 인증서를 생성합니다.
   * 수동 또는 IoT Hub Device Provisioning Service를 사용하여 X.509 인증서 인증을 사용하는 디바이스를 프로비저닝하기 위해 [IoT Edge 디바이스 ID 인증서를 만듭니다](#create-iot-edge-device-identity-certificates).
   * 게이트웨이 시나리오에서 IoT Edge 디바이스에 대한 [IoT Edge 디바이스 CA 인증서를 만듭니다](#create-iot-edge-device-ca-certificates).
   * 게이트웨이 시나리오에서 다운스트림 디바이스를 인증하기 위한 [다운스트림 디바이스 인증서를 만듭니다](#create-downstream-device-certificates).

## <a name="prerequisites"></a>필수 구성 요소

Git이 설치된 개발 머신

## <a name="set-up-scripts"></a>스크립트 설정

GitHub의 IoT Edge 리포지토리에는 데모 인증서를 만드는 데 사용할 수 있는 인증서 생성 스크립트가 포함되어 있습니다.
이 섹션에서는 Windows 또는 Linux 환경의 컴퓨터에서 실행할 스크립트를 준비하는 방법에 대한 지침을 제공합니다.
Linux 머신을 사용하는 경우 [Linux에서 설정하기](#set-up-on-linux)로 건너뜁니다.

### <a name="set-up-on-windows"></a>Windows에서 설정하기

Windows 디바이스에서 데모 인증서를 만들려면 OpenSSL을 설치한 다음 생성 스크립트를 복제하여 PowerShell에서 로컬로 실행되도록 설정해야 합니다.

#### <a name="install-openssl"></a>OpenSSL 설치

인증서를 생성하는 데 사용하는 머신에서 Windows용 OpenSSL을 설치합니다.
Windows 디바이스에 이미 OpenSSL을 설치한 경우 PATH 환경 변수에서 openssl.exe를 사용할 수 있는지 확인하세요.

다음 옵션을 포함하여 OpenSSL을 설치하는 몇 가지 방법이 있습니다.

* **더 손쉬운 방법:** 예를 들어 [SourceForge의 OpenSSL](https://sourceforge.net/projects/openssl/)에서 [타사 OpenSSL 이진 파일](https://wiki.openssl.org/index.php/Binaries)을 다운로드하여 설치합니다. PATH 환경 변수에 openssl.exe에 대한 전체 경로를 추가합니다.

* **권장 사항:** 직접 또는 [vcpkg](https://github.com/Microsoft/vcpkg)를 통해 OpenSSL 소스 코드를 다운로드하고 머신에서 이진 파일을 빌드합니다. 아래에 나열된 지침은 vcpkg를 사용하여 간단한 단계를 통해 소스 코드를 다운로드하고, 컴파일하고, Windows 머신에 OpenSSL을 설치합니다.

   1. vcpkg를 설치하려는 디렉터리로 이동합니다. 지침에 따라 [vcpkg](https://github.com/Microsoft/vcpkg)를 다운로드하고 설치합니다.

   2. vcpkg가 설치되면 PowerShell 프롬프트에서 다음 명령을 실행하여 Windows x64용 OpenSSL 패키지를 설치합니다. 설치를 완료하는 데 일반적으로 약 5분이 걸립니다.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. openssl.exe 파일을 호출할 수 있도록 `<vcpkg path>\installed\x64-windows\tools\openssl`을 PATH 환경 변수에 추가합니다.

#### <a name="prepare-scripts-in-powershell"></a>PowerShell에서 스크립트 준비

Azure IoT Edge Git 리포지토리에는 테스트 인증서를 생성하는 데 사용할 수 있는 스크립트가 포함됩니다.
이 섹션에서는 IoT Edge 리포지토리를 복제하고 스크립트를 실행합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다.

2. 데모 인증서를 생성하는 스크립트가 포함된 IoT Edge Git 리포지토리를 복제합니다. `git clone` 명령을 사용하거나 [ZIP을 다운로드](https://github.com/Azure/iotedge/archive/master.zip)합니다.

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 작업하려는 디렉터리로 이동합니다. 이 문서 전체에서 이 디렉터리 *\<WRKDIR>* 를 호출합니다. 모든 인증서 및 키가 이 작업 디렉터리에 만들어집니다.

4. 복제된 리포지토리에서 작업 디렉터리로 구성 및 스크립트 파일을 복사합니다.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   리포지토리를 ZIP으로 다운로드한 경우 폴더 이름은 `iotedge-master`이고 나머지 경로는 동일합니다.

5. 스크립트를 실행하도록 PowerShell을 활성화합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 스크립트에서 사용된 함수를 PowerShell의 글로벌 네임스페이스로 가져옵니다.

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 창에는 이 스크립트에 의해 생성된 인증서는 테스트 목적으로만 사용되고 프로덕션 시나리오에서는 사용할 수 없다는 경고가 표시됩니다.

7. OpenSSL이 올바르게 설치되었는지 확인하고 기존 인증서와 이름이 중복되지 않는지 확인합니다. 문제가 있는 경우 스크립트 출력값은 시스템에서 이를 해결하는 방법을 설명합니다.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Linux에서 설정하기

Linux 디바이스에서 데모 인증서를 만들려면 생성 스크립트를 복제하여 bash에서 로컬로 실행되도록 설정해야 합니다.

1. 데모 인증서를 생성하는 스크립트가 포함된 IoT Edge Git 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 작업하려는 디렉터리로 이동합니다. 문서 전체에서 이 디렉터리를 *\<WRKDIR>* 로 참조합니다. 모든 인증서 및 키 파일은 이 디렉터리에 생성됩니다.
  
3. 복제된 IoT Edge 리포지토리에서 작업 디렉터리로 구성 및 스크립트 파일을 복사합니다.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>루트 CA 인증서 만들기

루트 CA 인증서는 IoT Edge 시나리오를 테스트하기 위한 다른 모든 데모 인증서를 만드는 데 사용됩니다.
동일한 루트 CA 인증서를 계속 사용하여 여러 IoT Edge 및 다운스트림 디바이스에 대한 데모 인증서를 만들 수 있습니다.

작업 폴더에 루트 CA 인증서가 이미 있는 경우에는 새 인증서를 만들지 마세요.
새 루트 CA 인증서는 이전 버전을 덮어쓰고 이전 인증서에서 만든 다운스트림 인증서의 작동이 중단됩니다.
여러 루트 CA 인증서를 원하는 경우 별도의 폴더에서 관리해야 합니다.

이 섹션의 단계를 진행하기 전에 데모 인증서 생성 스크립트를 사용하여 작업 디렉터리를 준비하려면 [스크립트 설정](#set-up-scripts) 섹션의 단계를 따르세요.

### <a name="windows"></a>Windows

1. 인증서 생성 스크립트를 배치한 작업 디렉터리로 이동합니다.

1. 루트 CA 인증서를 만들고 중간 인증서에 서명하도록 합니다. 인증서는 모두 작업 디렉터리에 저장됩니다. 

   ```powershell
   New-CACertsCertChain rsa
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만들지만, 아티클이 **루트 CA 인증서** 를 요청하는 경우 다음 파일을 사용합니다.

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   
   다음 섹션에 설명된 대로 IoT Edge 디바이스 및 리프 디바이스에 대한 추가 인증서를 만들려면 먼저 이 인증서가 있어야 합니다.

### <a name="linux"></a>Linux

1. 인증서 생성 스크립트를 배치한 작업 디렉터리로 이동합니다.

1. 루트 CA 인증서 및 중간 인증서를 만듭니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만들지만, 아티클이 **루트 CA 인증서** 를 요청하는 경우 다음 파일을 사용합니다.

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>IoT Edge 디바이스 ID 인증서 만들기

X.509 인증서 인증을 사용하려는 경우 디바이스 ID 인증서를 사용하여 IoT Edge 디바이스를 프로비저닝합니다. 이러한 인증서는 Azure IoT Hub DPS(Device Provisioning Service)를 통해 수동 프로비저닝 또는 자동 프로비저닝을 사용하는지에 따라 작동합니다.

디바이스 ID 인증서는 IoT Edge 디바이스에서 구성 파일의 **프로비전** 섹션으로 이동합니다.

이 섹션의 단계를 진행하기 전에 [스크립트 설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따르세요.

### <a name="windows"></a>Windows

다음 명령을 사용하여 IoT Edge 디바이스 ID 인증서 및 프라이빗 키를 만듭니다.

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

이 명령에 전달하는 이름은 IoT Hub에서 IoT Edge 디바이스의 디바이스 ID가 됩니다.

새로운 디바이스 ID 명령은 DPS에서 개별 등록 생성 및 IoT Edge 런타임 설치 시 사용하는 세 가지 인증서 및 키 파일을 포함한 여러 인증서 및 키 파일을 만듭니다.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

DPS의 IoT Edge 디바이스에 대한 개별 등록을 위해서는 `iot-edge-device-identity-<name>.cert.pem`을 사용합니다. IoT Hub IoT Edge 디바이스를 등록하려면 `iot-edge-device-identity-<name>-full-chain.cert.pem` 및 `iot-edge-device-identity-<name>.key.pem` 인증서를 사용합니다. 자세한 내용은 [x.509 인증서를 사용하여 IoT Edge 디바이스 만들기 및 프로비저닝](how-to-provision-devices-at-scale-windows-x509.md)을 참조하세요.

### <a name="linux"></a>Linux

다음 명령을 사용하여 IoT Edge 디바이스 ID 인증서 및 프라이빗 키를 만듭니다.

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

이 명령에 전달하는 이름은 IoT Hub에서 IoT Edge 디바이스의 디바이스 ID가 됩니다.

이 스크립트는 DPS에서 개별 등록 생성 및 IoT Edge 런타임 설치 시 사용하는 세 가지 인증서 및 키 파일을 포함한 인증서 및 키 파일을 만듭니다.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>IoT Edge 디바이스 CA 인증서 만들기

프로덕션으로 이동하는 모든 IoT Edge 디바이스에는 구성 파일에서 참조되는 디바이스 CA 인증서가 필요합니다.
디바이스 CA 인증서는 디바이스에서 실행되는 모듈에 대한 인증서를 만듭니다.
디바이스 CA 인증서는 IoT Edge 디바이스가 다운스트림 디바이스에 대한 ID를 확인하는 방법이기 때문에 게이트웨이 시나리오에도 필요합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
디바이스 CA 인증서는 IoT Edge 디바이스에서 config.yaml 파일의 **인증서** 섹션으로 이동합니다.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
디바이스 CA 인증서는 IoT Edge 디바이스에서 config.toml 파일의 **Edge CA** 섹션으로 이동합니다.
:::moniker-end

이 섹션의 단계를 진행하기 전에 [스크립트 설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따르세요.

### <a name="windows"></a>Windows

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다음 명령을 사용하여 IoT Edge 디바이스 CA 인증서 및 프라이빗 키를 만듭니다. CA 인증서의 이름을 입력합니다.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   이 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 IoT Edge 디바이스에 복사하고 구성 파일에서 참조해야 합니다.

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

**New-CACertsEdgeDevice** 명령에 전달되는 이름은 구성 파일의 호스트 이름 매개 변수 또는 IoT Hub의 디바이스 ID와 달라야 합니다.

### <a name="linux"></a>Linux

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다음 명령을 사용하여 IoT Edge 디바이스 CA 인증서 및 프라이빗 키를 만듭니다. CA 인증서의 이름을 입력합니다.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 IoT Edge 디바이스에 복사하고 구성 파일에서 참조해야 합니다.

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

**create_edge_device_ca_certificate** 명령으로 전달되는 이름은 구성 파일의 호스트 이름 매개 변수와 IoT Hub의 디바이스 ID와 달라야 합니다.

## <a name="create-downstream-device-certificates"></a>다운스트림 디바이스 인증서 만들기

게이트웨이 시나리오에 대한 다운스트림 IoT 디바이스를 설정하고 X.509 인증을 사용하려는 경우 다운스트림 디바이스에 대한 데모 인증서를 생성할 수 있습니다.
대칭 키 인증을 사용하려는 경우에는 다운스트림 디바이스에 대한 추가 인증서를 만들 필요가 없습니다.
X.509 인증서를 사용하여 IoT 디바이스를 인증하는 방법에는 자체 서명된 인증서를 사용하거나 CA(인증 기관) 서명 인증서를 사용하는 두 가지 방법이 있습니다.
X.509 자체 서명 인증(지문 인증이라고도 함)의 경우 IoT 디바이스에 배치할 새 인증서를 만들어야 합니다.
이러한 인증서에는 인증을 위해 IoT Hub와 공유하는 지문이 있습니다.
X.509 CA(인증 기관) 서명 인증의 경우 IoT Hub에 등록된 루트 CA 인증서를 사용하여 IoT 디바이스에 대한 인증서에 서명해야 합니다.
루트 CA 인증서 또는 중간 인증서에서 발급한 인증서를 사용하는 모든 디바이스는 인증이 허용됩니다.

인증서 생성 스크립트를 통해 이러한 인증 시나리오 중 하나를 테스트하는 데모 인증서를 만들 수 있습니다.

이 섹션의 단계를 진행하기 전에 [스크립트 설정](#set-up-scripts) 및 [루트 CA 인증서 만들기](#create-root-ca-certificate) 섹션의 단계를 따르세요.

### <a name="self-signed-certificates"></a>자체 서명된 인증서

자체 서명된 인증서를 사용하여 IoT 디바이스를 인증하는 경우 솔루션에 대한 루트 CA 인증서를 기반으로 디바이스 인증서를 만들어야 합니다.
그런 다음 IoT Hub에 제공할 인증서에서 16진수 “지문”을 검색합니다.
IoT 디바이스에는 IoT Hub를 사용하여 인증할 수 있도록 디바이스 인증서의 복사본도 필요합니다.

#### <a name="windows"></a>Windows

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다운스트림 디바이스에 대해 두 개의 인증서(기본 및 보조)를 만듭니다. 사용하기 쉬운 명명 규칙의 예로는 IoT 디바이스 이름으로 인증서를 만든 다음 기본 또는 보조 레이블을 만드는 것입니다. 예를 들면 다음과 같습니다.

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 디바이스에 복사하여 IoT Hub에 연결하는 애플리케이션에서 참조해야 합니다.

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 각 인증서에서 SHA1 지문(IoT Hub 컨텍스트에서는 thumbprint)을 검색합니다. 지문은 40자의 16진수 문자열입니다. 다음 openssl 명령을 사용하여 인증서를 확인하고 지문을 찾습니다.

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   이 명령을 기본 인증서 및 보조 인증서에 대해 한 번씩 두 번 실행합니다. 자체 서명된 X.509 인증서를 사용하여 새 IoT 디바이스를 등록할 때 두 인증서의 지문을 제공합니다.

#### <a name="linux"></a>Linux

1. 인증서 생성 스크립트 및 루트 CA 인증서가 있는 작업 디렉터리로 이동합니다.

2. 다운스트림 디바이스에 대해 두 개의 인증서(기본 및 보조)를 만듭니다. 사용하기 쉬운 명명 규칙의 예로는 IoT 디바이스 이름으로 인증서를 만든 다음 기본 또는 보조 레이블을 만드는 것입니다. 예를 들면 다음과 같습니다.

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 디바이스에 복사하여 IoT Hub에 연결하는 애플리케이션에서 참조해야 합니다.

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 각 인증서에서 SHA1 지문(IoT Hub 컨텍스트에서는 thumbprint)을 검색합니다. 지문은 40자의 16진수 문자열입니다. 다음 openssl 명령을 사용하여 인증서를 확인하고 지문을 찾습니다.

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   자체 서명된 X.509 인증서를 사용하여 새 IoT 디바이스를 등록할 때 기본 및 보조 지문 모두를 제공합니다.

### <a name="ca-signed-certificates"></a>CA 서명 인증서

CA 서명된 인증서를 사용하여 IoT 디바이스를 인증하는 경우 솔루션에 대한 루트 CA 인증서를 IoT Hub에 업로드해야 합니다.
그런 다음 루트 CA 인증서를 소유하고 있음을 IoT Hub에 증명하기 위한 확인 작업을 수행합니다.
마지막으로 IoT Hub로 인증할 수 있도록 IoT 디바이스에 넣을 디바이스 인증서를 만들기 위해 동일한 루트 CA 인증서를 사용합니다.

이 섹션의 인증서는 IoT Hub X.509 인증서 자습서 시리즈의 단계를 위한 것입니다. 이 시리즈의 소개 내용은 [퍼블릭 키 암호화 및 X.509 퍼블릭 키 인프라 이해](../iot-hub/tutorial-x509-introduction.md)를 참조하세요.

#### <a name="windows"></a>Windows

1. 작업 디렉터리`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`의 루트 CA 인증서 파일을 IoT Hub에 업로드합니다.

2. 루트 CA 인증서를 소유하고 있는지 확인하기 위해 Azure Portal에서 제공된 코드를 사용합니다.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 다운스트림 디바이스에 대한 인증서 체인을 만듭니다. 등록된 디바이스 ID를 IoT Hub에 사용합니다.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 디바이스에 복사하여 IoT Hub에 연결하는 애플리케이션에서 참조해야 합니다.

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 작업 디렉터리`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`의 루트 CA 인증서 파일을 IoT Hub에 업로드합니다.

2. 루트 CA 인증서를 소유하고 있는지 확인하기 위해 Azure Portal에서 제공된 코드를 사용합니다.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 다운스트림 디바이스에 대한 인증서 체인을 만듭니다. 등록된 디바이스 ID를 IoT Hub에 사용합니다.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   이 스크립트 명령은 여러 인증서 및 키 파일을 만듭니다. 다음 인증서 및 키 쌍을 다운스트림 IoT 디바이스에 복사하여 IoT Hub에 연결하는 애플리케이션에서 참조해야 합니다.

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
