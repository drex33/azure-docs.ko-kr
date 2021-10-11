---
title: 자습서 - 사용자 지정 HSM(하드웨어 보안 모듈)을 사용하여 X.509 디바이스를 Azure IoT Hub에 프로비저닝
description: 이 자습서에서는 등록 그룹을 사용합니다. 이 자습서에서는 사용자 지정 HSM(하드웨어 보안 모듈) 및 Azure IoT Hub DPS용 C 디바이스 SDK를 사용하여 X.509 디바이스를 프로비저닝하는 방법을 알아봅니다.
author: wesmc7777
ms.author: wesmc
ms.date: 05/24/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: d431e91eb71d5befe71f134e634fe5ad3c7b392d
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274478"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>자습서: 등록 그룹을 사용하여 여러 X.509 디바이스 프로비저닝

이 자습서에서는 X.509 인증서를 인증에 사용하는 IoT 디바이스 그룹을 프로비저닝하는 방법을 알아봅니다. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 샘플 디바이스 코드는 개발 컴퓨터에서 실행되어 X.509 디바이스의 프로비저닝을 시뮬레이션합니다. 실제 디바이스에서 디바이스 코드는 IoT 디바이스에서 배포되고 실행됩니다.

이 자습서를 계속 진행하기 전에 적어도 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 또한 자동 프로비저닝 프로세스에 익숙하지 않은 경우 [프로비저닝](about-iot-dps.md#provisioning-process) 개요를 검토합니다. 

Azure IoT Device Provisioning Service는 디바이스 프로비저닝에 대해 다음 두 가지 유형의 등록을 지원합니다.

* [등록 그룹](concepts-service.md#enrollment-group): 여러 관련 디바이스를 등록하는 데 사용됩니다.
* [개별 등록](concepts-service.md#individual-enrollment): 단일 디바이스를 등록하는 데 사용됩니다.

이 자습서는 등록 그룹을 사용하여 디바이스 세트를 프로비저닝하는 방법을 보여주는 이전 자습서와 비슷합니다. 그러나 이 자습서에서는 대칭 키 대신 X.509 인증서가 사용됩니다. [대칭 키](./concepts-symmetric-key-attestation.md)를 사용하는 간단한 방법을 보려면 이 섹션의 이전 자습서를 검토하세요.

이 자습서에서는 하드웨어 기반의 보안 스토리지와 상호 작용하기 위한 스텁 구현을 제공하는 [사용자 지정 HSM 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example)을 보여 줍니다. [HSM(하드웨어 보안 모듈)](./concepts-service.md#hardware-security-module)은 디바이스 비밀을 안전한 하드웨어 기반 스토리지에 저장하는 데 사용됩니다. HSM은 대칭 키, X.509 인증서 또는 TPM 증명에 사용하여 비밀을 안전하게 저장할 수 있습니다. 디바이스 비밀의 하드웨어 기반 스토리지는 필수는 아니지만 디바이스 인증서의 프라이빗 키와 같은 중요한 정보를 보호하는 데 적극 권장됩니다.


이 자습서에서는 다음 목표를 달성할 것입니다.

> [!div class="checklist"]
> * X.509 인증서를 사용하여 디바이스 세트를 구성하는 신뢰 인증서 체인 만들기
> * 인증서 체인에 사용되는 서명 인증서를 통해 소유 증명 완료
> * 인증서 체인을 사용하는 새 그룹 등록 만들기
> * [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 코드를 사용하여 디바이스를 프로비저닝할 개발 환경 설정
> * SDK의 사용자 지정 HSM(하드웨어 보안 모듈) 샘플과 함께 인증서 체인을 사용하여 디바이스 프로비저닝

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

디바이스를 시뮬레이션하는 데 사용되는 Windows 개발 환경에 대한 필수 구성 요소는 다음과 같습니다. Linux 또는 macOS의 경우 SDK 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.

* ['C++를 사용한 데스크톱 개발'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) 워크로드를 사용하도록 설정된 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019. Visual Studio 2015와 Visual Studio 2017도 지원됩니다. 

    Visual Studio는 이 문서에서 사용하여 IoT 디바이스에 배포할 디바이스 샘플 코드를 빌드합니다.  이 경우 Visual Studio가 디바이스 자체에 필요한 것은 아닙니다.

* 최신 버전의 [Git](https://git-scm.com/download/) 설치

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. 이 SDK는 DPS에서 X.509 디바이스 프로비저닝에 사용하는 샘플 코드 및 도구를 포함하고 있습니다.

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소([Visual Studio](https://visualstudio.microsoft.com/vs/) 및 ['C++를 사용한 데스크톱 개발'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) 워크로드)를 컴퓨터에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. Azure IoT C SDK의 [최신 릴리스](https://github.com/Azure/azure-iot-sdk-c/releases/latest)에 대한 태그 이름을 찾습니다.

3. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제합니다. 이전 단계에서 찾은 태그를 `-b` 매개 변수의 값으로 사용합니다.

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

4. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 앞에서 만든 `cmake` 디렉터리에는 사용자 지정 HSM 샘플 및 사용자 지정 HSM을 사용하여 X.509 인증을 제공하는 샘플 디바이스 프로비저닝 코드가 포함됩니다. 

    `cmake` 디렉터리에서 다음 명령을 실행하여 사용 중인 개발 플랫폼과 관련된 SDK 버전을 빌드합니다. 이 빌드에는 사용자 지정 HSM 샘플의 참조가 포함됩니다. 

    아래의 `-Dhsm_custom_lib`에 사용되는 경로를 지정할 때, 앞에서 만든 `cmake` 디렉터리의 상대 경로를 사용해야 합니다. 아래에 표시된 상대 경로는 참고를 위한 예시입니다.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    `cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다.

    빌드가 성공적으로 완료되면 Visual Studio 솔루션이 `cmake` 디렉터리에 생성됩니다. 마지막 몇 개의 출력 줄은 다음 출력과 비슷합니다.

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>X.509 인증서 체인 만들기

이 섹션에서는 이 자습서를 사용하여 각 디바이스를 테스트하기 위해 세 개의 인증서로 구성된 X.509 인증서 체인을 생성합니다. 인증서의 계층 구조는 다음과 같습니다.

![자습서 디바이스 인증서 체인](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[루트 인증서](concepts-x509-attestation.md#root-certificate): [소유 증명](how-to-verify-certificates.md)을 완료하여 루트 인증서를 확인할 것입니다. 이렇게 확인하면 DPS는 루트 인증서를 신뢰하고, 루트 인증서로 서명한 인증서를 확인할 수 있습니다.

[중간 인증서](concepts-x509-attestation.md#intermediate-certificate): 중간 인증서를 사용하여 디바이스를 제품 라인, 회사 부서 또는 기타 기준에 따라 논리적으로 그룹화하는 것은 매우 흔한 일입니다. 이 자습서에서는 하나의 중간 인증서로 구성된 인증서 체인을 사용합니다. 중간 인증서는 루트 인증서를 통해 서명됩니다. 이 인증서는 DPS에서 만든 등록 그룹에도 사용되어 디바이스 세트를 논리적으로 그룹화합니다. 이렇게 구성하면 동일한 중간 인증서로 서명된 디바이스 인증서가 있는 디바이스 그룹 전체를 관리할 수 있습니다. 디바이스 그룹을 사용하도록 또는 사용하지 않도록 설정하는 등록 그룹을 만들 수 있습니다. 디바이스 그룹을 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [등록 그룹을 사용하여 X.509 중간 인증서 또는 루트 CA 인증서 허용 안 함](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)을 참조하세요.

[디바이스 인증서](concepts-x509-attestation.md#end-entity-leaf-certificate): 디바이스(리프) 인증서는 중간 인증서로 서명되고 프라이빗 키와 함께 디바이스에 저장됩니다. 이상적으로 이러한 중요한 항목은 HSM을 사용하여 안전하게 저장됩니다. 각 디바이스에서 프로비저닝을 시도할 때 인증서 및 프라이빗 키를 인증서 체인과 함께 제공합니다. 

#### <a name="create-root-and-intermediate-certificates"></a>루트 및 중간 인증서 만들기

인증서 체인의 루트 및 중간 부분을 만들려면 다음을 수행합니다.

> [!IMPORTANT]
> 이 문서에서는 Bash 셸 접근 방식만 사용합니다. PowerShell을 사용할 수는 있지만 이 문서에서는 다루지 않습니다.


1. Git Bash 명령 프롬프트를 엽니다. [샘플 및 자습서에 사용되는 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials)에 있는 Bash 셸 지침에 따라 1단계와 2단계를 완료합니다.

    그러면 인증서 스크립트에 대한 작업 디렉터리가 만들어지고, openssl을 사용하여 인증서 체인에 대한 루트 및 중간 인증서 예제가 생성됩니다. 
    
2. 출력을 보면 자체 서명된 루트 인증서의 위치가 나옵니다. 이 인증서는 나중에 소유권을 확인하기 위해 [소유 증명](how-to-verify-certificates.md)을 거칩니다.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. 출력을 보면 루트 인증서로 서명/발급된 중간 인증서의 위치가 나옵니다. 이 인증서는 나중에 만들 등록 그룹에 사용됩니다.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>디바이스 인증서 만들기

체인의 중간 인증서로 서명된 디바이스 인증서를 만들려면 다음을 수행합니다.

1. 다음 명령을 실행하여 매개 변수로 지정하는 주체 이름을 사용하여 새 디바이스/리프 인증서를 만듭니다. 이 자습서에 지정된 예제 주체 이름 `custom-hsm-device-01`을 사용합니다. 이 주체 이름은 IoT 디바이스의 디바이스 ID가 됩니다. 

    > [!WARNING]
    > 공백이 포함된 주체 이름은 사용하지 마세요. 이 주체 이름은 프로비저닝되는 IoT 디바이스의 디바이스 ID입니다. 따라서 디바이스 ID에 대한 규칙을 따라야 합니다. 자세한 내용은 [디바이스 ID 속성](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)을 참조하세요.

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    다음 출력을 보면 새 디바이스 인증서가 있는 위치를 알 수 있습니다. 디바이스 인증서는 중간 인증서로 서명(발급됨)되었습니다.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. 다음 명령을 실행하여 `custom-hsm-device-01`에 대한 새 디바이스 인증서가 포함된 전체 인증서 체인 파일(.pem)을 만듭니다.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    텍스트 편집기를 사용하여 *./certs/new-device-01-full-chain.cert.pem* 인증서 체인 파일을 엽니다. 인증서 체인 텍스트에는 세 가지 인증서의 전체 체인이 포함됩니다. 이 텍스트는 이 자습서의 뒷부분에 나오는 `custom-hsm-device-01`에 대한 사용자 지정 HSM 디바이스 코드에서 인증서 체인으로 사용됩니다.

    전체 체인 텍스트는 다음과 같은 형식입니다.
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. 새 디바이스 인증서의 프라이빗 키는 *./private/new-device.key.pem* 에 기록되었습니다. 이 키 파일의 이름을 `custom-hsm-device-01` 디바이스에 대한 */private/new-device-01.key.pem* 으로 바꿉니다. 프로비저닝할 때 디바이스에서 이 키의 텍스트가 필요합니다. 이 텍스트는 나중에 사용자 지정 HSM 예제에 추가됩니다.

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > 인증서의 텍스트에는 공개 키 정보만 포함됩니다. 
    >
    > 그러나 디바이스는 디바이스 인증서의 프라이빗 키에 대한 액세스 권한도 필요합니다. 프로비저닝을 시도할 때 디바이스에서 런타임에 해당 키를 사용하여 확인을 수행해야 하기 때문에 이 액세스 권한이 필요합니다. 이 키는 매우 중요하기 때문에 실제 HSM에서는 프라이빗 키를 안전하게 보관할 수 있도록 하드웨어 기반 스토리지를 사용하는 것이 좋습니다.

4. *./certs/new-device.cert.pem* 을 삭제하고 디바이스 ID가 `custom-hsm-device-02`인 두 번째 디바이스에 대해 1~3단계를 반복합니다. *./certs/new-device.cert.pem* 을 삭제해야 합니다. 그렇지 않으면 두 번째 디바이스에 대한 인증서 생성이 실패합니다. 이 문서에서는 전체 체인 인증서 파일만 사용됩니다. 두 번째 디바이스에 다음 값을 사용합니다.

    |   Description                 |  값  |
    | :---------------------------- | :--------- |
    | 주체 이름                  | `custom-hsm-device-02` |
    | 전체 인증서 체인 파일   | *./certs/new-device-02-full-chain.cert.pem* |
    | 프라이빗 키 파일 이름          | *private/new-device-02.key.pem* |
    

## <a name="verify-ownership-of-the-root-certificate"></a>루트 인증서의 소유권 확인

> [!NOTE]
> 2021년 7월 1일부터 [자동 확인](how-to-verify-certificates.md#automatic-verification-of-intermediate-or-root-ca-through-self-attestation)을 통해 인증서 자동 확인을 수행할 수 있습니다.
>

1. [X.509 인증서의 공개 부분 등록 및 확인 코드 가져오기](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code)의 지침에 따라 루트 인증서(`./certs/azure-iot-test-only.root.ca.cert.pem`)를 업로드하고 DPS에서 확인 코드를 가져옵니다.

2. DPS의 루트 인증서 확인 코드를 얻은 후에는 인증서 스크립트 작업 디렉터리에서 다음 명령을 실행하여 확인 인증서를 생성합니다.
 
    이 자습서의 확인 코드는 단지 예시일 뿐입니다. DPS에서 직접 생성한 코드를 사용하세요.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    이 스크립트는 루트 인증서로 서명되고 주체 이름이 확인 코드로 설정된 인증서를 만듭니다. 이 인증서를 통해 DPS는 사용자에게 루트 인증서의 프라이빗 키에 대한 액세스 권한이 있는 것을 확인할 수 있습니다. 스크립트의 출력에서 확인 인증서의 위치를 확인하세요. 이 인증서는 `.pfx` 형식으로 생성됩니다.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. [서명된 확인 인증서 업로드](how-to-verify-certificates.md#upload-the-signed-verification-certificate)에서 설명했듯이, 확인 인증서를 업로드하고 DPS에서 **확인** 을 클릭하여 루트 인증서의 소유 증명을 완료합니다.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Windows 기반 디바이스의 인증서 저장소 업데이트

Windows 이외의 디바이스는 코드에서 인증서 체인을 인증서 저장소로 전달할 수 있습니다.

Windows 기반 디바이스는 서명 인증서(루트 및 중간)를 Windows [인증서 저장소](/windows/win32/secauthn/certificate-stores)에 추가해야 합니다. 그렇지 않으면 TLS(전송 계층 보안)를 사용하는 보안 채널을 통해 서명 인증서가 DPS로 전송되지 않습니다.

> [!TIP]
> C SDK에서 보안 채널(Schannel) 대신 OpenSSL을 사용할 수도 있습니다. OpenSSL을 사용하는 방법에 대한 자세한 내용은 [SDK에서 OpenSSL 사용](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk)을 참조하세요.

Windows 기반 디바이스에서 인증서 저장소에 서명 인증서를 추가하려면 다음을 수행합니다.

1. Git bash 프롬프트에서 서명 인증서가 들어 있는 `certs` 하위 디렉터리로 이동하여 다음과 같이 `.pfx`로 변환합니다.

    루트 인증서:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    중간 인증서:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Windows **시작** 단추를 마우스 오른쪽 단추로 클릭합니다. 그런 다음, **실행** 을 마우스 왼쪽 단추로 클릭합니다. *certmgr.msc* 를 입력하고 **확인** 을 클릭하여 인증서 관리자 MMC 스냅인을 시작합니다.

3. 인증서 관리자의 **인증서 - 현재 사용자** 에서 **신뢰할 수 있는 루트 인증 기관** 을 클릭합니다. 메뉴에서 **작업** > **모든 작업** > **가져오기** 를 클릭하고 `root.pfx`를 가져옵니다.

    * **개인 정보 교환(.pfx)** 으로 검색합니다.
    * `1234`를 암호로 사용합니다.
    * 인증서를 **신뢰할 수 있는 루트 인증 기관** 인증서 저장소에 배치합니다.

4. 인증서 관리자의 **인증서 - 현재 사용자** 에서 **중간 인증 기관** 을 클릭합니다. 메뉴에서 **작업** > **모든 작업** > **가져오기** 를 클릭하고 `intermediate.pfx`를 가져옵니다.

    * **개인 정보 교환(.pfx)** 으로 검색합니다.
    * `1234`를 암호로 사용합니다.
    * 인증서를 **중간 인증 기관** 인증서 저장소에 배치합니다.

이제 Windows 기반 디바이스에서 서명 인증서를 신뢰하며 전체 체인을 DPS로 전송할 수 있습니다.



## <a name="create-an-enrollment-group"></a>등록 그룹 만들기

1. Azure Portal에 로그인하여 왼쪽 메뉴에서 **모든 리소스** 단추를 선택하고 Device Provisioning Service를 엽니다.

2. **등록 관리** 탭을 선택한 다음, 위쪽에서 **등록 그룹 추가** 단추를 선택합니다.

3. **등록 그룹 추가** 패널에서 다음 정보를 입력한 다음, **저장** 단추를 누릅니다.

      ![포털에서 X.509 증명을 위한 등록 그룹 추가](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | 필드        | 값           |
    | :----------- | :-------------- |
    | **그룹 이름** | 이 자습서에서는 **custom-hsm-x509-devices** 를 입력 |
    | **증명 유형** | **인증서** 선택 |
    | **IoT Edge 디바이스** | **False** 선택 |
    | **인증서 종류** | **중간 인증서** 선택 |
    | **기본 인증서 .pem 또는 .cer 파일** | 앞에서 만든 중간 인증서( *./certs/azure-iot-test-only.intermediate.cert.pem*)로 이동합니다. 이 중간 인증서는 이미 업로드하여 확인한 루트 인증서를 통해 서명되었습니다. 확인되면 DPS에서 해당 루트를 신뢰합니다. DPS는 이 등록 그룹과 함께 제공된 중간 인증서가 신뢰할 수 있는 루트에서 실제로 서명되었는지 확인할 수 있습니다. DPS는 실제로 해당 루트 인증서를 통해 서명된 각 중간 인증서를 신뢰하므로 중간 인증서를 통해 서명된 리프 인증서도 확인하고 신뢰할 수 있습니다.  |


## <a name="configure-the-provisioning-device-code"></a>프로비저닝 디바이스 코드 구성

이 섹션에서는 샘플 코드를 Device Provisioning Service 인스턴스 정보로 업데이트합니다. 디바이스가 인증되면 이 섹션에 구성한 Device Provisioning Service 인스턴스에 연결된 IoT 허브에 디바이스가 할당됩니다.

1. Azure Portal에서 Device Provisioning Service에 대한 **개요** 탭을 선택하고 **_ID 범위_** 값을 기록해 둡니다.

    ![포털 블레이드에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device-x509/copy-id-scope.png) 

2. Visual Studio를 시작하고, azure-iot-sdk-c git 리포지토리의 루트에 만든 `cmake` 디렉터리에 생성된 새 솔루션 파일을 엽니다. 솔루션 파일의 이름은 `azure_iot_sdks.sln`입니다.

3. Visual Studio의 솔루션 탐색기에서 **Provisioning_Samples > prov_dev_client_sample > 원본 파일** 로 이동하여 *prov_dev_client_sample.c* 를 엽니다.

4. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_X509`로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.


## <a name="configure-the-custom-hsm-stub-code"></a>사용자 지정 HSM 스텁 코드 구성

실제 보안 하드웨어 기반 스토리지와 이루어지는 구체적인 상호 작용은 하드웨어에 따라 달라집니다. 따라서 이 자습서의 시뮬레이션된 디바이스에서 사용되는 인증서 체인은 사용자 지정 HSM 스텁 코드에서 하드 코딩됩니다. 실제 시나리오에서 인증서 체인은 중요한 정보를 보다 안전하게 보호하기 위해 실제 HSM 하드웨어에 저장됩니다. 그런 다음, 이 샘플에 사용된 스텁 메서드와 비슷한 메서드를 구현하여 해당 하드웨어 기반 스토리지에서 비밀을 읽습니다. 

HSM 하드웨어가 필요하지는 않지만 인증서의 프라이빗 키와 같은 중요한 정보를 보호하는 것이 좋습니다. 샘플에서 실제 HSM을 호출하는 경우 프라이빗 키는 소스 코드에 표시되지 않습니다. 키가 소스 코드에 있으면 해당 키가 코드를 볼 수 있는 모든 사용자에게 공개됩니다. 이 문서에서는 학습을 지원하기 위해서만 이 작업을 수행합니다.

ID가 `custom-hsm-device-01`인 디바이스의 ID를 시뮬레이션하도록 사용자 지정 HSM 스텁 코드를 업데이트하려면 다음 단계를 수행합니다.

1. Visual Studio의 솔루션 탐색기에서 **Provisioning_Samples > custom_hsm_example > 원본 파일** 로 이동하여 *custom_hsm_example.c* 를 엽니다.

2. 디바이스 인증서를 생성할 때 사용한 일반적인 이름을 사용하여 `COMMON_NAME` 문자열 상수의 문자열 값을 업데이트합니다.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. 동일한 파일에서 인증서를 생성한 후 *./certs/new-device-01-full-chain.cert.pem* 에 저장한 인증서 체인 텍스트를 사용하여 `CERTIFICATE` 상수 문자열의 문자열 값을 업데이트해야 합니다.

    인증서 텍스트 구문은 Visual Studio에서 수행한 추가 공백이나 구문 분석 없이 아래 패턴을 따라야 합니다.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    이 단계에서 이 문자열 값을 올바르게 업데이트하는 것은 매우 번거로울 수 있으며 오류가 발생할 수 있습니다. Git Bash 프롬프트에서 적절한 구문을 생성하려면 다음 Bash 셸 명령을 Git Bash 명령 프롬프트에 복사하여 붙여넣고 **ENTER** 를 누릅니다. 이러한 명령은 `CERTIFICATE` 문자열 상수 값에 대한 구문을 생성합니다.

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    새 상수 값에 대한 출력 인증서 텍스트를 복사하여 붙여넣습니다. 


4. 동일한 파일에서 `PRIVATE_KEY` 상수의 문자열 값도 디바이스 인증서의 프라이빗 키로 업데이트해야 합니다.

    프라이빗 키 텍스트 구문은 Visual Studio에서 수행한 추가 공백이나 구문 분석 없이 아래 패턴을 따라야 합니다.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    이 단계에서 이 문자열 값을 올바르게 업데이트하는 것은 매우 번거로울 수 있으며 오류가 발생할 수도 있습니다. Git Bash 프롬프트에서 적절한 구문을 생성하려면 다음 Bash 셸 명령을 복사하여 붙여넣고 **ENTER** 를 누릅니다. 이러한 명령은 `PRIVATE_KEY` 문자열 상수 값에 대한 구문을 생성합니다.

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    새 상수 값에 대한 출력 프라이빗 키 텍스트를 복사하여 붙여넣습니다. 

5. *custom_hsm_example.c* 를 저장합니다.

6. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 메시지가 표시되면 **예** 를 선택하여 프로젝트를 다시 빌드한 후 실행합니다.

    다음 출력은 성공적으로 부팅되고 프로비저닝 서비스에 연결하는 `custom-hsm-device-01` 시뮬레이션된 디바이스에 대한 예제입니다. 이 디바이스는 IoT 허브에 할당되고 등록되었습니다.

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. 포털에서 프로비저닝 서비스와 연결된 IoT 허브로 이동하여 **IoT 디바이스** 탭을 선택합니다. X.509 디바이스가 허브에 성공적으로 프로비저닝되면 디바이스 ID가 **IoT 디바이스** 블레이드에 나타나고 *상태* 는 **사용** 으로 표시됩니다. 위쪽에서 **새로 고침** 단추를 눌러야 할 수도 있습니다. 

    ![사용자 지정 HSM 디바이스가 IoT 허브에 등록됨](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. 디바이스 ID가 `custom-hsm-device-02`인 두 번째 디바이스에 대해 1~7단계를 반복합니다. 해당 디바이스에 대해 다음 값을 사용합니다.

    |   Description                 |  값  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | 전체 인증서 체인        | `input="./certs/new-device-02-full-chain.cert.pem"`을 사용하여 텍스트 생성 |
    | 프라이빗 키                   | `input="./private/new-device-02.key.pem"`을 사용하여 텍스트 생성 |

    다음 출력은 성공적으로 부팅되고 프로비저닝 서비스에 연결하는 `custom-hsm-device-02` 시뮬레이션된 디바이스에 대한 예제입니다. 이 디바이스는 IoT 허브에 할당되고 등록되었습니다.

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>리소스 정리

이 디바이스 클라이언트 샘플을 모두 테스트하고 탐색한 후에는 다음 단계에 따라 이 자습서에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 디바이스 클라이언트 샘플 출력 창을 닫습니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, Device Provisioning Service를 선택합니다. 서비스의 **등록 관리** 를 연 다음, **등록 그룹** 탭을 선택합니다. 이 자습서에서 만든 디바이스 그룹의 *그룹 이름* 옆에 있는 확인란을 선택하고, 창 위쪽에서 **삭제** 단추를 누릅니다. 
1. DPS에서 **인증서** 를 클릭합니다. 이 자습서에서 업로드하고 확인한 각 인증서를 클릭하고 **삭제** 단추를 클릭하여 인증서를 제거합니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 사용자의 IoT 허브를 선택합니다. 허브의 **IoT 디바이스** 를 엽니다. 이 자습서에서 등록한 디바이스의 *디바이스 ID* 옆에 있는 확인란을 선택합니다. 창 아래에서 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 HSM을 사용하여 X.509 디바이스를 IoT 허브에 프로비저닝했습니다. IoT 디바이스를 여러 허브에 프로비저닝하는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
> [자습서: 부하가 분산되는 IoT 허브에 디바이스 프로비저닝](tutorial-provision-multiple-hubs.md)
