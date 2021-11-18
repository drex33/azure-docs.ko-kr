---
title: 빠른 시작 - Microsoft Azure IoT Hub에 X.509 인증서 시뮬레이션된 디바이스 프로비저닝
description: Azure IoT Hub Device Provisioning Service에서 X.509 인증서로 인증하는 시뮬레이션된 디바이스를 프로비저닝하는 방법을 알아봅니다.
author: wesmc7777
ms.author: wesmc
ms.date: 09/07/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 23ff38d248343cc02c1e9cbf3d5fb5ffce419bad
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132763034"
---
# <a name="quickstart-provision-an-x509-certificate-simulated-device"></a>빠른 시작: X.509 인증서 시뮬레이션된 디바이스 프로비저닝

이 빠른 시작에서는 Windows 머신에서 시뮬레이션된 디바이스를 만듭니다. 시뮬레이션된 디바이스는 인증에 [X.509 인증서 증명](concepts-x509-attestation.md) 메커니즘을 사용하도록 구성됩니다. 디바이스를 구성한 후에는 Azure IoT Hub Device Provisioning Service를 사용하여 디바이스를 IoT 허브에 프로비저닝합니다.

프로비저닝 프로세스에 익숙하지 않은 경우 [프로비저닝](about-iot-dps.md#provisioning-process) 개요를 검토하세요.  계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다.

이 빠른 시작에서는 Windows 기반 워크스테이션을 위한 솔루션을 보여줍니다. 그러나 Linux에서 절차를 수행할 수도 있습니다. Linux 예제는 [다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법](how-to-provision-multitenant.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* [Azure Portal로 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료합니다.

다음 필수 구성 요소는 Windows 개발 환경을 위한 것입니다. Linux 또는 macOS의 경우 SDK 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.

::: zone pivot="programming-language-ansi-c"

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019를 설치하고 ['C++를 사용한 데스크톱 개발'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) 워크로드를 사용하도록 설정합니다. Visual Studio 2015와 Visual Studio 2017도 지원됩니다. Linux 또는 macOS의 경우 SDK 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.

::: zone-end

::: zone pivot="programming-language-csharp"

* Windows 기반 머신에 [.NET Core 3.1 SDK 이상](https://dotnet.microsoft.com/download)을 설치합니다. 다음 명령을 사용하여 버전을 확인할 수 있습니다.

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* 머신에 [Node.js v4.0 이상](https://nodejs.org)을 설치합니다.

* 머신에 [OpenSSL](https://www.openssl.org/)을 설치하고 명령 창에서 액세스할 수 있는 환경 변수에 추가합니다. 이 라이브러리는 원본에서 빌드하고 설치하거나 [여기](https://sourceforge.net/projects/openssl/)와 같은 [타사](https://wiki.openssl.org/index.php/Binaries)에서 다운로드하고 설치할 수 있습니다.

::: zone-end

::: zone pivot="programming-language-python"

* 머신에 [Python 3.6 이상](https://www.python.org/downloads/)을 설치합니다.

* 머신에 [OpenSSL](https://www.openssl.org/)을 설치하고 명령 창에서 액세스할 수 있는 환경 변수에 추가합니다. 이 라이브러리는 원본에서 빌드하고 설치하거나 [여기](https://sourceforge.net/projects/openssl/)와 같은 [타사](https://wiki.openssl.org/index.php/Binaries)에서 다운로드하고 설치할 수 있습니다.

::: zone-end

::: zone pivot="programming-language-java"

* 머신에 [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) 이상을 설치합니다.

* [Maven](https://maven.apache.org/install.html)을 다운로드하고 설치합니다.

::: zone-end

* 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다. Git이 명령 창에 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 *Git Bash* 가 포함됩니다.

## <a name="prepare-your-development-environment"></a>개발 환경 준비

::: zone pivot="programming-language-ansi-c"

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. 샘플 코드는 디바이스의 부팅 시퀀스 중에 디바이스를 프로비저닝하려고 시도합니다.

1. 최신 [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    >[!IMPORTANT]
    >`CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다. 이전 버전의 CMake 빌드 시스템은 이 문서에 사용된 솔루션 파일을 생성하지 못합니다. 최신 버전의 CMake를 사용해야 합니다.

2. 웹 브라우저를 열고 [Azure IoT C SDK 릴리스 페이지](https://github.com/Azure/azure-iot-sdk-c/releases/latest)로 이동합니다.

3. 페이지 맨 위에서 **태그** 탭을 선택합니다.

4. Azure IoT C SDK의 최신 릴리스에 대한 태그 이름을 복사합니다.

5. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제합니다. (`<release-tag>`을(를) 이전 단계에서 복사한 이름으로 바꿉니다).

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

6. 작업이 완료되면 `azure-iot-sdk-c` 디렉터리에서 다음 명령을 실행합니다.

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

7. 코드 샘플에서는 X.509 인증서를 사용하여 X.509 인증을 통해 증명을 제공합니다. 다음 명령을 실행하여 디바이스 프로비저닝 클라이언트를 포함하는 개발 플랫폼과 관련된 SDK 버전을 빌드합니다. 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    >[!TIP]
    >`cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs)에서 명령을 실행합니다.

8. 빌드가 성공하면 마지막 몇 개의 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다.

2. 다음 명령을 사용하여 [C#용 Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다.

2. 다음 명령을 사용하여 [Node.js용 Azure IoT 샘플](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다.

2. 다음 명령을 사용하여 [Python용 Azure IoT 샘플](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다.

2. 다음 명령을 사용하여 [Java용 Azure IoT 샘플](https://github.com/Azure/azure-iot-sdk-java.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. 필요한 모든 패키지를 다운로드하려면 루트 `azure-iot-sdk-`java 디렉터리로 이동하여 프로젝트를 빌드합니다.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

4. 인증서 생성기 프로젝트로 이동하여 프로젝트를 빌드합니다.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

::: zone-end

## <a name="create-a-self-signed-x509-device-certificate"></a>자체 서명된 X.509 디바이스 인증서 만들기

이 섹션에서는 Azure IoT SDK의 샘플 코드를 사용하여 자체 서명된 X.509 인증서를 만듭니다. 이 인증서는 프로비전 서비스에 업로드되고 서비스에서 확인되어야 합니다.

> [!CAUTION]
> SDK 도구로 만든 인증서는 개발 테스트 용도로만 사용해야 합니다.
> 프로덕션 환경에서는 이러한 인증서를 사용하지 마세요.
> SDK에서 생성한 인증서는 하드 코딩된 암호(예: *1234*)를 포함하며 30일 후에 만료됩니다.
> 프로덕션 사용에 적합한 인증서 가져오기에 대한 자세한 내용은 Azure IoT Hub 설명서에서 [X.509 CA 인증서를 가져오는 방법](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate)을 참조하세요.
>

::: zone pivot="programming-language-csharp"

IoT 디바이스의 디바이스 ID는 인증서의 주체 일반 이름이 됩니다. [디바이스 ID 문자열 요구 사항](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)을 준수하는 주체 이름을 사용해야 합니다.

::: zone-end

X.509 인증서를 만들려면 다음을 수행합니다.

::: zone pivot="programming-language-ansi-c"

### <a name="clone-the-azure-iot-c-sdk"></a>Azure IoT C SDK를 복제합니다.

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)에는 X.509 인증서 체인을 만들고, 해당 체인에서 루트 또는 중간 인증서를 업로드하고, 인증서를 확인하기 위해 서비스를 통해 소유 증명을 수행하는 데 도움이 되는 테스트 도구가 포함되어 있습니다.

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 이미 복제했으면 [다음 섹션](#create-a-test-certificate)으로 건너뜁니다.

1. 웹 브라우저를 열고 [Azure IoT C SDK의 릴리스 페이지](https://github.com/Azure/azure-iot-sdk-c/releases/latest)로 이동합니다.

2. Azure IoT C SDK 최신 릴리스의 태그 이름을 복사합니다.

3. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제합니다. (`<release-tag>`를 이전 단계에서 복사한 태그로 바꿉니다.)

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

4. 테스트 도구는 복제한 리포지토리의 *azure-iot-sdk-c/tools/CACertificates* 에 있습니다.

### <a name="create-a-test-certificate"></a>테스트 인증서 만들기

[샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)의 단계를 따릅니다.

C SDK의 도구 이외에도 *Microsoft Azure IoT SDK for .NET* 의 [그룹 인증서 확인 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)은 기존 X.509 중간 또는 루트 CA 인증서를 사용하여 C#에서 소유 증명을 수행하는 방법을 보여줍니다.

::: zone-end

::: zone pivot="programming-language-csharp"

1. PowerShell 프롬프트에서 디렉터리를 X.509 디바이스 프로비저닝 샘플에 대한 프로젝트 디렉터리로 변경합니다.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. 샘플 코드는 암호로 보호된 PKCS12 형식의 파일(`certificate.pfx`)에 저장된 X.509 인증서를 사용하도록 설정됩니다. 또한 이 빠른 시작의 뒷부분에서 개별 등록을 만들려면 공개 키 인증서 파일(`certificate.cer`)이 필요합니다. 자체 서명된 인증서와 관련 `.cer` 및 `.pfx` 파일을 생성하려면 다음 명령을 실행합니다.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. 스크립트에서 PFX 암호를 묻는 메시지가 표시됩니다. 이 암호를 기억해야 합니다. 나중에 샘플을 실행할 때 필요합니다. 필요에 따라 `certutil` 명령을 실행하여 인증서를 덤프하고 주체 이름을 확인할 수 있습니다.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. 명령 프롬프트를 열고 인증서 생성기 스크립트로 이동하여 프로젝트를 빌드합니다.

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

2. 사용자 고유의 _인증서 이름_ 을 사용하는 스크립트를 실행하여 _리프_ X.509 인증서를 만듭니다. 리프 인증서의 일반 이름은 [등록 ID](./concepts-service.md#registration-id)가 되므로 소문자 영숫자와 하이픈만 사용해야 합니다.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Git Bash 프롬프트에서 다음 명령을 실행합니다.

    # <a name="windows"></a>[Windows](#tab/windows)

    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > 주체 이름(`//CN=Python-device-01`)에 제공된 추가 슬래시는 Windows 플랫폼에서 Git을 사용하여 문자열을 이스케이프하는 데만 필요합니다.

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```

    ---

2. **PEM 암호 입력:** 이라는 메시지가 표시되면 암호 `1234`를 사용합니다.

3. **확인 - PEM 암호 입력:** 이라는 메시지가 표시되면 암호 `1234`를 다시 사용합니다.

테스트 인증서 파일(*python-device.pem*) 및 프라이빗 키 파일(*python-device.key.pem*)이 `openssl` 명령을 실행한 디렉터리에 생성됩니다.

::: zone-end

::: zone pivot="programming-language-java"

1. 이전 단계의 명령 프롬프트를 사용하여 `target` 폴더로 이동합니다.

2. 이전 섹션에서 만든 .jar 파일을 실행합니다.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

3. _Do you want to input common name_(일반 이름을 입력하시겠습니까?)에서 **N** 을 입력합니다. 

4. `Client Cert`의 출력 중 *-----BEGIN CERTIFICATE-----* 에서 *-----END CERTIFICATE-----* 까지를 클립보드에 복사합니다.

   ![개별 인증서 생성기](./media/quick-create-simulated-device-x509/cert-generator-java.png)

5. Windows 머신에 *_X509individual.pem_* 이라는 파일을 만듭니다.

6. 원하는 편집기에서 *_X509individual.pem_* 파일을 열고, 클립보드의 내용을 이 파일에 복사합니다. 

7. 파일을 저장하고 편집기를 닫습니다.

8. 명령 프롬프트의 _Do you want to input Verification Code_(확인 코드를 입력하시겠습니까?)에서 **N** 을 입력하고, 나중에 빠른 시작에서 참조할 수 있도록 프로그램 출력을 열어 둡니다. `Client Cert` 및 `Client Cert Private Key` 값을 복사합니다. 다음 섹션에서 필요합니다.

::: zone-end

## <a name="create-a-device-enrollment"></a>디바이스 등록 만들기

Azure IoT Device Provisioning 서비스는 다음과 같은 두 가지 등록을 지원합니다.

* [등록 그룹](concepts-service.md#enrollment-group): 여러 관련 디바이스를 등록하는 데 사용됩니다.
* [개별 등록](concepts-service.md#individual-enrollment): 단일 디바이스를 등록하는 데 사용됩니다.

이 문서에서는 IoT 허브로 프로비저닝할 단일 디바이스에 대한 개별 등록을 보여줍니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. Device Provisioning Service를 선택합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 페이지 위쪽에서 **+ 개별 등록 추가** 를 선택합니다.

::: zone pivot="programming-language-ansi-c"

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    * **메커니즘:** ID 증명 *메커니즘* 으로 **X.509** 를 선택합니다.
    * **기본 인증서 .pem 또는 .cer 파일:** **파일 선택** 을 선택하고, 이전 섹션에서 만든 *X509testcert.pem* 인증서 파일을 선택합니다.
    * **IoT Hub 디바이스 ID:** *test-docs-cert-device* 를 입력하여 디바이스에 ID를 제공합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 증명을 사용하여 개별 등록으로 디바이스를 추가합니다.":::

::: zone-end

::: zone pivot="programming-language-csharp"

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    * **메커니즘:** ID 증명 *메커니즘* 으로 **X.509** 를 선택합니다.
    * **기본 인증서 .pem 또는 .cer 파일:** **파일 선택** 을 선택하고, 이전 섹션에서 만든 *certificate.cer* 인증서 파일을 선택합니다.
    * **IoT Hub 디바이스 ID:** 는 비워 둡니다. 디바이스 ID가 *iothubx509device1*(X.509 인증서의 CN(일반 이름))로 설정된 디바이스가 프로비전됩니다. 이 일반 이름은 개별 등록 항목의 등록 ID에 사용되는 이름이기도 합니다.
    * 필요에 따라 다음 정보를 입력합니다.
        * 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
        * 디바이스에 대해 원하는 초기 구성으로 **초기 디바이스 쌍 상태** 를 업데이트합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 증명을 사용하여 개별 등록으로 디바이스를 추가합니다.":::

::: zone-end

::: zone pivot="programming-language-nodejs"

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    * **메커니즘:** ID 증명 *메커니즘* 으로 **X.509** 를 선택합니다.
    * **기본 인증서 .pem 또는 .cer 파일:** **파일 선택** 을 선택하고, 이전 섹션에서 만든 *{certificate-name}_cert.pem* 인증서 파일을 선택합니다.
    * 필요에 따라 다음 정보를 입력합니다.
        * 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
        * 고유한 디바이스 ID를 입력합니다. 디바이스 이름을 지정할 때 중요한 데이터가 포함되지 않도록 합니다.
        * 디바이스에 대해 원하는 초기 구성으로 **초기 디바이스 쌍 상태** 를 업데이트합니다.
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 증명을 사용하여 개별 등록으로 디바이스를 추가합니다.":::

::: zone-end

::: zone pivot="programming-language-python"

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    * **메커니즘:** ID 증명 *메커니즘* 으로 **X.509** 를 선택합니다.
    * **기본 인증서 .pem 또는 .cer 파일:** **파일 선택** 을 선택하고, 이전에 만든 테스트 인증서를 사용하는 경우 *python-device.pem* 인증서 파일을 선택합니다.
    * 필요에 따라 다음 정보를 입력합니다.
        * 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
        * 디바이스에 대해 원하는 초기 구성으로 **초기 디바이스 쌍 상태** 를 업데이트합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 증명을 사용하여 개별 등록으로 디바이스를 추가합니다.":::

::: zone-end

::: zone pivot="programming-language-java"

6. **등록 추가** 패널에서 다음 정보를 입력합니다.
   * ID 증명 *메커니즘* 으로 **X.509** 를 선택합니다.
   * *기본 인증서 .pem 또는 .cer 파일* 아래에서 *파일 선택* 을 선택하여 이전 단계에서 만든 *X509individual.pem* 인증서 파일을 선택합니다.  
   * 필요에 따라 다음 정보를 입력합니다.
     * 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
     * 고유한 디바이스 ID를 입력합니다. 디바이스 이름을 지정할 때 중요한 데이터가 포함되지 않도록 합니다. 
     * 디바이스에 대해 원하는 초기 구성으로 **초기 디바이스 쌍 상태** 를 업데이트합니다.
    
    :::image type="content" source="./media/quick-create-simulated-device-x509/device-enrollment.png" alt-text="X.509 증명을 사용하여 개별 등록으로 디바이스를 추가합니다.":::

::: zone-end
    
7. **저장** 을 선택합니다. 그러면 **등록 관리** 로 이동됩니다.

8. **개별 등록** 을 선택합니다. X.509 등록 항목이 등록 테이블에 표시됩니다.

## <a name="prepare-and-run-the-device-provisioning-code"></a>디바이스 프로비저닝 코드 준비 및 실행

::: zone pivot="programming-language-ansi-c"

이 섹션에서는 디바이스의 부팅 시퀀스를 Device Provisioning Service 인스턴스에 보내도록 샘플 코드를 업데이트합니다. 이 부팅 시퀀스를 사용하면 디바이스가 인식되고 Device Provisioning Service 인스턴스에 연결된 IoT Hub에 할당됩니다.

1. Azure Portal에서 Device Provisioning Service의 **개요** 탭을 선택합니다.

2. **_ID 범위_** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="포털에서 ID 범위를 복사합니다.":::

3. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample** 이라는 샘플 프로젝트를 확장합니다. **원본 파일** 을 확장하고, **prov\_dev\_client\_sample.c** 를 엽니다.

4. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_TPM` 대신 `SECURE_DEVICE_TYPE_X509`으로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.

7. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예** 를 선택하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 프로비전 디바이스 클라이언트 샘플을 성공적으로 부팅하고, IoT Hub 정보를 가져오기 위해 프로비전 서비스 인스턴스에 연결하고, 등록하는 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

1. Azure Portal에서 Device Provisioning Service의 **개요** 탭을 선택합니다.

2. **_ID 범위_** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope.png" alt-text="포털에서 ID 범위를 복사합니다.":::

3. 명령 프롬프트 창을 엽니다.

4. 다음 명령을 입력하여 X.509 디바이스 프로비저닝 샘플을 빌드하고 실행합니다(`<IDScope>` 값을 이전 섹션에서 복사한 ID 범위로 대체). 인증서 파일은 기본적으로 *./certificate.pfx* 로 설정되며 .pfx 암호를 입력하라는 메시지가 표시됩니다. 암호를 입력합니다.

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    모든 항목을 매개 변수로 전달하려는 경우 다음 예제 형식을 사용할 수 있습니다.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```

5. 이제 디바이스가 DPS에 연결되고 IoT Hub에 할당됩니다. 그리고 디바이스가 원격 분석 메시지를 허브로 보냅니다.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

1. Azure Portal에서 Device Provisioning Service의 **개요** 탭을 선택합니다. 

2. **_ID 범위_** 및 **전역 디바이스 엔드포인트** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="포털에서 ID 범위를 복사합니다.":::

3. _인증서_ 및 _키_ 를 샘플 폴더에 복사합니다.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

4. 디바이스 테스트 스크립트로 이동하고 프로젝트를 빌드합니다.

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

5. **register\_x509.js** 파일을 다음과 같이 변경합니다.

    * `provisioning host`를 위의 **1단계** 에서 적어 둔 **_전역 디바이스 엔드포인트_** 로 바꿉니다.
    * `id scope`를 위의 **1단계** 에 적어 둔 **_ID 범위_** 로 바꿉니다. 
    * `registration id`를 이전 섹션에서 적어 둔 **_등록 ID_** 로 바꿉니다.
    * `cert filename` 및 `key filename`을 위의 **2단계** 에서 복사한 파일로 바꿉니다.

6. 파일을 저장합니다.

7. 스크립트를 실행하고 디바이스가 성공적으로 프로비저닝되었는지 확인합니다.

    ```cmd/sh
    node register_x509.js
    ``` 

>[!TIP]
>[Azure IoT Hub Node.js 디바이스 SDK](https://github.com/Azure/azure-iot-sdk-node)는 디바이스를 시뮬레이션하는 쉬운 방법을 제공합니다. 자세한 내용은 [디바이스 개념](./concepts-service.md)을 참조하세요.

::: zone-end

::: zone pivot="programming-language-python"

Python 프로비저닝 샘플 [provision_x509 py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py)는 `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` 디렉터리에 있습니다. 이 샘플에서는 6개의 환경 변수를 사용하여 DPS를 사용하여 IoT 디바이스를 인증하고 프로비저닝합니다. 이러한 환경 변수는 다음과 같습니다.

| 변수 이름              | 설명                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  이 값은 DPS 리소스에 연결하는 데 사용되는 글로벌 엔드포인트입니다. |    
| `PROVISIONING_IDSCOPE`     |  이 값은 DPS 리소스의 ID 범위입니다. |    
| `DPS_X509_REGISTRATION_ID` |  이 값은 디바이스의 ID입니다. 또한 디바이스 인증서의 주체 이름과 일치해야 합니다. |    
| `X509_CERT_FILE`           |  디바이스 인증서 파일 이름 |    
| `X509_KEY_FILE`            |  디바이스 인증서의 프라이빗 키 파일 이름입니다. |
| `PASS_PHRASE`              |  인증서 및 프라이빗 키 파일(`1234`)을 암호화하는 데 사용된 암호입니다. |    

1. Azure Portal에서 Device Provisioning Service의 **개요** 탭을 선택합니다.

2. **_ID 범위_** 및 **전역 디바이스 엔드포인트** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="포털에서 ID 범위를 복사합니다.":::

3. Git Bash 프롬프트에서 다음 명령을 사용하여 전역 디바이스 엔드포인트와 ID 범위에 대한 환경 변수를 추가합니다.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

4. IoT 디바이스의 등록 ID는 디바이스 인증서의 주체 이름과 일치해야 합니다. 자체 서명된 테스트 인증서를 생성한 경우 `Python-device-01`은 디바이스의 주체 이름이자 등록 ID입니다.

    디바이스 인증서가 이미 있는 경우 `certutil` 명령을 사용하여 아래와 같이 디바이스에 사용되는 주체 일반 이름을 확인할 수 있습니다.

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

5. Git Bash 프롬프트에서 등록 ID에 대한 환경 변수를 다음과 같이 설정합니다.

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

6. Git Bash 프롬프트에서 인증서 파일, 프라이빗 키 파일 및 암호에 대한 환경 변수를 설정합니다.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

7. [provision_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py)에 대한 코드를 검토합니다. **Python 버전 3.7** 이상을 사용하지 않는 경우 [여기에 설명된 대로 코드를 변경](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk)하여 `asyncio.run(main())`을 대체합니다.

8. 변경 내용을 저장합니다.

9. 예제를 실행합니다. 이 샘플은 디바이스를 허브에 연결하고 프로비저닝한 후 일부 테스트 메시지를 허브에 보냅니다.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Azure Portal에서 Device Provisioning Service의 **개요** 탭을 선택합니다.

2. **_ID 범위_** 및 **전역 디바이스 엔드포인트** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-x509/copy-id-scope-and-global-device-endpoint.png" alt-text="포털에서 ID 범위를 복사합니다.":::

3. 명령 프롬프트를 엽니다. Java SDK 리포지토리의 샘플 프로젝트 폴더로 이동합니다.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

4. 코드에 프로비전 서비스 및 X.509 ID 정보를 입력합니다. 디바이스를 등록하기 전에, 프로비저닝하는 동안 시뮬레이션된 디바이스의 증명을 위해 사용됩니다.

   * 앞에서 설명한 대로 _ID 범위_ 및 _프로비전 서비스 글로벌 엔드포인트_ 를 포함하도록 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` 파일을 편집합니다. 또한 이전 섹션에서 설명한 대로 _클라이언트 인증서_ 및 _클라이언트 인증서 프라이빗 키_ 도 포함합니다.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   * 인증서 및 프라이빗 키를 복사/붙여넣을 때 다음 형식을 사용합니다.
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

5. 샘플을 빌드한 다음, `target` 폴더로 이동하여 이전에 만든 .jar 파일을 실행합니다.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>디바이스 프로비저닝 등록 확인

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 디바이스가 할당된 IoT Hub를 선택합니다.

4. **Explorers** 메뉴에서 **IoT 디바이스** 를 선택합니다.

5. 디바이스가 성공적으로 프로비전된 경우 **상태** 가 *사용* 으로 설정된 디바이스 ID가 목록에 표시됩니다. 디바이스가 표시되지 않으면 페이지 위쪽에서 **새로 고침** 을 선택합니다.

   :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration.png" alt-text="디바이스가 IoT Hub에 등록됨":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-csharp.png" alt-text="CSharp 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-nodejs.png" alt-text="Node.js 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-python.png" alt-text="Python 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-x509/hub-registration-java.png" alt-text="Java 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end


::: zone pivot="programming-language-csharp,programming-language-nodejs,programming-language-python,programming-language-java"

>[!IMPORTANT]
>디바이스에 대한 등록 항목의 기본값으로부터 *초기 디바이스 쌍 상태* 를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

디바이스 클라이언트 샘플에 계속 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 그만하려면 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

### <a name="delete-your-device-enrollment"></a>디바이스 등록 삭제

1. 컴퓨터에서 디바이스 클라이언트 샘플 출력 창을 닫습니다.

2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다.

3. Device Provisioning Service를 선택합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. **개별 등록** 탭을 선택합니다.

6. 이 빠른 시작에 등록한 디바이스의 *등록 ID* 옆에 있는 확인란을 선택합니다.

7. 페이지 위쪽에서 **삭제** 를 선택합니다.

### <a name="delete-your-device-registration-from-iot-hub"></a>IoT Hub에서 디바이스 등록 삭제

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다.

2. IoT Hub를 선택합니다.

3. **Explorers** 메뉴에서 **IoT 디바이스** 를 선택합니다.

4. 이 빠른 시작에서 등록한 디바이스의 *디바이스 ID* 옆에 있는 확인란을 선택합니다.

5. 페이지 위쪽에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

X.509 디바이스를 프로그래밍 방식으로 등록하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service에 X.509 디바이스 등록](quick-enroll-device-x509.md)