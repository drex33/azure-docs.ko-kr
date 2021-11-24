---
title: 빠른 시작 - 시뮬레이션된 대칭 키 디바이스를 Microsoft Azure IoT Hub에 프로비저닝
description: Azure IoT Hub DPS(Device Provisioning Service)에서 대칭 키로 인증하는 디바이스를 프로비저닝하는 방법을 알아봅니다.
author: wesmc7777
ms.author: wesmc
ms.date: 09/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom: mvc, mode-other
zone_pivot_groups: iot-dps-set1
ms.openlocfilehash: 7a3c3273e7ae5d6f179311da9cfd1ce9e7aaee5f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133058601"
---
# <a name="quickstart-provision-a-simulated-symmetric-key-device"></a>빠른 시작: 시뮬레이션된 대칭 키 디바이스 프로비저닝

이 빠른 시작에서는 Windows 머신에서 시뮬레이션된 디바이스를 만듭니다. 시뮬레이션된 디바이스는 인증에 [대칭 키 증명](concepts-symmetric-key-attestation.md) 메커니즘을 사용하도록 구성됩니다. 디바이스를 구성한 후 Azure IoT Hub Device Provisioning Service를 사용하여 IoT 허브에 프로비저닝합니다.

프로비저닝 프로세스에 익숙하지 않은 경우 [프로비저닝](about-iot-dps.md#provisioning-process) 개요를 검토하세요.

이 빠른 시작에서는 Windows 기반 워크스테이션을 위한 솔루션을 보여 줍니다. 그러나 Linux에서 절차를 수행할 수도 있습니다. Linux 예제는 [다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법](how-to-provision-multitenant.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료합니다.
::: zone pivot="programming-language-ansi-c"

* Windows 개발 환경을 사용하는 경우 [‘C++를 사용한 데스크톱 개발’](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) 워크로드를 사용하도록 설정된 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019를 설치합니다. Visual Studio 2015와 Visual Studio 2017도 지원됩니다. Linux 또는 macOS의 경우 SDK 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.

::: zone-end

::: zone pivot="programming-language-csharp"

* Windows 기반 머신에 [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download) 이상을 설치합니다. 다음 명령을 사용하여 버전을 확인할 수 있습니다.

    ```bash
    dotnet --info
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

* [Node.js v4.0 이상](https://nodejs.org)을 설치합니다.

::: zone-end

::: zone pivot="programming-language-python"

* Windows 기반 머신에 설치된 [Python 3.7](https://www.python.org/downloads/) 이상을 설치합니다. `python --version`을 실행하여 Python 버전을 확인할 수 있습니다.

::: zone-end

::: zone pivot="programming-language-java"

* 머신에 설치된 [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-support-on-azure) 이상을 설치합니다.

* [Maven](https://maven.apache.org/install.html)을 다운로드하고 설치합니다.

::: zone-end

* 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다. Git이 명령 창에 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 *Git Bash* 가 포함됩니다.


<a id="setupdevbox"></a>

## <a name="prepare-your-development-environment"></a>개발 환경 준비

::: zone pivot="programming-language-ansi-c"

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. 샘플 코드는 디바이스의 부팅 시퀀스 중에 디바이스를 프로비저닝하려고 시도합니다.

1. 최신 [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    >[!IMPORTANT]
    >`CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다. 이전 버전의 CMake 빌드 시스템은 이 문서에 사용된 솔루션 파일을 생성하지 못합니다. 최신 버전의 CMake를 사용해야 합니다.

2. 웹 브라우저를 열고 [Azure IoT C SDK 릴리스 페이지](https://github.com/Azure/azure-iot-sdk-c/releases/latest)로 이동합니다.

3. 페이지 맨 위에서 **태그** 탭을 선택합니다.

4. Azure IoT C SDK의 최신 릴리스에 대한 태그 이름을 복사합니다.

5. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제합니다(`<release-tag>`를 이전 단계에서 복사한 태그로 바꾸기).

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

7. 코드 샘플에서는 대칭 키를 사용하여 증명을 제공합니다. 다음 명령을 실행하여 디바이스 프로비저닝 클라이언트를 포함하는 개발 클라이언트 플랫폼과 관련된 SDK 버전을 빌드합니다.

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    >[!TIP]
    >`cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs)에서 명령을 실행합니다.

8. 빌드가 성공적으로 완료되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 16 2019
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.19042.
    -- The C compiler identification is MSVC 19.29.30040.0
    -- The CXX compiler identification is MSVC 19.29.30040.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
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

2. 다음 명령을 사용하여 [Node.js용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

::: zone-end

::: zone pivot="programming-language-python"

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다.

2. 다음 명령을 사용하여 [Python용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

3. 샘플 파일, _provision_symmetric_key.py_ 가 있는 `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` 디렉터리로 이동합니다.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. 다음 명령을 실행하여 _azure-iot-device_ 라이브러리를 설치합니다.

    ```console
    pip install azure-iot-device
    ```

::: zone-end

::: zone pivot="programming-language-java"

1. Git CMD 또는 Git Bash 명령줄 환경을 엽니다.

2. 다음 명령을 사용하여 [Java용 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java.git) GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

3. 필요한 모든 패키지를 다운로드하려면 루트 `azure-iot-sdk-java` 디렉터리로 이동하고 프로젝트를 빌드합니다.

   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```

4. 다음 명령을 실행하여 _azure-iot-device_ 라이브러리를 설치합니다.

   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

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

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

   * **메커니즘**: ID 증명 메커니즘으로 ‘대칭 키’를 선택합니다.

   * **키 자동 생성**: 이 상자를 선택합니다.

   * **등록 ID**: 등록을 식별하는 등록 ID를 입력합니다. 소문자 영숫자 및 대시('-') 문자만을 사용합니다. 예를 들어 *symm-key-device-007* 입니다.

   * **IoT Hub 디바이스 ID:** 디바이스 식별자를 입력합니다.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png" alt-text="디바이스 등록 정보 입력":::

    ::: zone-end

    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-csharp.png" alt-text="C# 디바이스 등록 정보 입력":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-nodejs.png" alt-text="Node.js 디바이스 등록 정보 입력":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-python.png" alt-text="Python 디바이스 등록 정보 입력":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/create-individual-enrollment-java.png" alt-text="Java 디바이스 등록 정보 입력":::

    ::: zone-end

7. **저장** 을 선택합니다. **기본 키** 와 **보조 키** 가 생성되고 등록 항목에 추가되며 **등록 관리** 페이지로 돌아갑니다.

8. 시뮬레이션된 대칭 키 디바이스 등록을 보려면 **개별 등록** 탭을 선택합니다.

9. 디바이스(*symm-key-device-007*)를 선택합니다.

10. 생성된 **기본 키** 의 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/copy-device-enrollment-primary-key.png" alt-text="디바이스 등록의 기본 키 복사":::

<a id="firstbootsequence"></a>

## <a name="prepare-and-run-the-device-provisioning-code"></a>디바이스 프로비저닝 코드 준비 및 실행

::: zone pivot="programming-language-ansi-c"

이 섹션에서는 디바이스의 부팅 시퀀스를 Device Provisioning Service 인스턴스에 보내도록 디바이스 샘플 코드를 업데이트합니다. 이 부팅 시퀀스를 사용하면 디바이스가 인식되고, 인증되며, Device Provisioning Service 인스턴스에 연결된 IoT Hub에 할당됩니다.

샘플 프로비저닝 코드는 다음 작업을 순서대로 수행합니다.

1. 다음 세 개의 매개 변수를 사용하여 Device Provisioning 리소스로 디바이스를 인증합니다.

    * Device Provisioning Service의 ID 범위
    * 디바이스 등록의 등록 ID
    * 디바이스 등록의 기본 대칭 키

2. Device Provisioning Service 인스턴스에 이미 연결된 IoT Hub에 디바이스를 할당합니다.

디바이스 정보를 사용하여 프로비저닝 샘플을 업데이트하고 실행하려면:

1. Device Provisioning Service의 기본 메뉴에서 **개요** 를 선택합니다.

2. **ID 범위** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Device Provisioning Service 엔드포인트 정보 추출":::

3. Visual Studio에서 CMake를 실행하여 생성된 *azure_iot_sdks.sln* 솔루션 파일을 엽니다. 솔루션 파일은 다음 위치에 있습니다.

    ```output

    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln

    ```

    >[!TIP]
    >파일이 Cmake 디렉터리에 생성되지 않는 경우 최신 버전의 CMake 빌드 시스템을 사용했는지 확인합니다.

4. Visual Studio의 ‘솔루션 탐색기’ 창에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample** 이라는 샘플 프로젝트를 확장합니다. **원본 파일** 을 확장하고, **prov\_dev\_client\_sample.c** 를 엽니다.

5. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다.

    ```c
    static const char* id_scope = "0ne00002193";
    ```

6. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

7. **prov\_dev\_client\_sample.c** 에서 주석으로 처리된 `prov_dev_set_symmetric_key_info()`에 대한 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거하고 자리 표시자 값(꺾쇠괄호 포함)을 앞에서 복사한 등록 ID 및 기본 키 값으로 바꿉니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```

8. 파일을 저장합니다.

9. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.

10. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 선택하여 솔루션을 실행합니다. 프로젝트 다시 빌드 프롬프트에서 **예** 를 선택하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 IoT 허브에 할당할 프로비저닝 서비스 인스턴스에 성공적으로 연결하는 디바이스의 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

::: zone-end

::: zone pivot="programming-language-csharp"

샘플 프로비저닝 코드는 다음 작업을 수행합니다.

1. 다음 세 개의 매개 변수를 사용하여 Device Provisioning 리소스로 디바이스를 인증합니다.

    * Device Provisioning Service의 ID 범위
    * 디바이스 등록의 등록 ID
    * 디바이스 등록의 기본 대칭 키

2. Device Provisioning Service 인스턴스에 이미 연결된 IoT Hub에 디바이스를 할당합니다.

3. IoT 허브에 테스트 원격 분석 메시지를 보냅니다.

디바이스 정보를 사용하여 프로비저닝 샘플을 업데이트하고 실행하려면:

1. Device Provisioning Service의 기본 메뉴에서 **개요** 를 선택합니다.

2. **ID 범위** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints.png" alt-text="Device Provisioning Service 엔드포인트 정보 추출":::

3. 명령 프롬프트를 열고 복제된 샘플 리포지토리에서 *SymmetricKeySample* 로 이동합니다.

    ```cmd
    cd azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample
    ```

4. *SymmetricKeySample* 폴더의 *Parameters.cs* 를 텍스트 편집기에서 엽니다. 이 파일은 샘플에서 지원하는 매개 변수를 보여 줍니다. 이 문서에서는 샘플을 실행할 때 처음 세 개의 필수 매개 변수만 사용합니다. 이 파일의 코드를 살펴보세요. 변경할 필요는 없습니다.

    | 매개 변수                         | 필수 | 설명     |
    | :-------------------------------- | :------- | :-------------- |
    | `--s` 또는 `--IdScope`              | True     | DPS 인스턴스의 ID 범위 |
    | `--i` 또는 `--Id`                   | True     | 개별 등록을 사용할 때의 등록 ID 또는 그룹 등록을 사용할 때 원하는 디바이스 ID. |
    | `--p` 또는 `--PrimaryKey`           | True     | 개인 등록 또는 그룹 등록의 기본 키. |
    | `--e` 또는 `--EnrollmentType`       | 거짓    | 등록 유형: `Individual` 또는 `Group`. 기본값은 `Individual`입니다. |
    | `--g` 또는 `--GlobalDeviceEndpoint` | 거짓    | 디바이스에서 연결할 전역 엔드포인트. 기본값은 `global.azure-devices-provisioning.net`입니다. |
    | `--t` 또는 `--TransportType`        | 거짓    | 디바이스 프로비저닝 인스턴스와 통신하는 데 사용할 전송. 기본값은 `Mqtt`입니다. 가능한 값은 `Mqtt`, `Mqtt_WebSocket_Only`, `Mqtt_Tcp_Only`, `Amqp`, `Amqp_WebSocket_Only`, `Amqp_Tcp_only` 및 `Http1`입니다.|

5. *SymmetricKeySample* 폴더의 *ProvisioningDeviceClientSample.cs* 를 텍스트 편집기에서 엽니다. 이 파일은 시뮬레이션된 대칭 키 디바이스를 프로비저닝하기 위해 [SecurityProviderSymmetricKey](/dotnet/api/microsoft.azure.devices.shared.securityprovidersymmetrickey?view=azure-dotnet&preserve-view=true) 클래스와 [ProvisioningDeviceClient](/dotnet/api/microsoft.azure.devices.provisioning.client.provisioningdeviceclient?view=azure-dotnet&preserve-view=true) 클래스가 어떻게 함께 사용되는지 보여 줍니다. 이 파일의 코드를 살펴보세요.  변경할 필요는 없습니다.

6. 세 개의 예제 매개 변수를 바꾼 후 다음 명령을 사용하여 샘플 코드를 빌드하고 실행합니다(`<id-scope>`을 Device Provisioning Service ID 범위의 ID 범위로, `<registration-id>`를 디바이스의 등록 ID로, `<primarykey>`를 디바이스의 기본 키로 바꾸기).

    ```console
    dotnet run --s <id-scope> --i <registration-id> --p <primarykey>
    ```

7. 이제 다음 출력과 비슷하게 표시됩니다. “TestMessage” 문자열은 테스트 메시지로 허브에 전송됩니다.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-nodejs"

샘플 프로비저닝 코드는 다음 작업을 순서대로 수행합니다.

1. 다음 네 개의 매개 변수를 사용하여 Device Provisioning 리소스로 디바이스를 인증합니다.
    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Device Provisioning Service 인스턴스에 이미 연결된 IoT Hub에 디바이스를 할당합니다.

3. IoT 허브에 테스트 원격 분석 메시지를 보냅니다.

디바이스 정보를 사용하여 프로비저닝 샘플을 업데이트하고 실행하려면:

1. Device Provisioning Service의 기본 메뉴에서 **개요** 를 선택합니다.

2. **ID 범위** 및 **서비스 엔드포인트** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Device Provisioning Service 엔드포인트 정보 추출":::

3. Node.js 명령을 실행하기 위한 명령 프롬프트를 열고 다음 디렉터리로 이동합니다.

    ```cmd
    cd azure-iot-sdk-node/provisioning/device/samples
    ```

4. *provisioning/device/samples* 폴더에서 *register_symkey.js* 를 열고, 코드를 검토합니다. 샘플 코드에서는 사용자 지정 페이로드를 설정합니다.

    ```nodejs
    provisioningClient.setProvisioningPayload({a: 'b'});
    ```

    이 빠른 시작에는 필요하지 않으므로 이 코드를 주석으로 처리할 수 있습니다. 사용자 지정 할당 함수를 사용하여 디바이스를 IoT Hub에 할당하려는 경우 사용자 지정 페이로드가 필요합니다. 자세한 내용은 [자습서: 사용자 지정 할당 정책 사용](tutorial-custom-allocation-policies.md)을 참조하세요.

     `provisioningClient.register()` 메서드에서 디바이스 등록을 시도합니다.

    추가 변경은 필요하지 않습니다.

5. 이제 명령 프롬프트에서 다음 환경 변수를 설정합니다(`<id-scope>`을 Device Provisioning Service ID 범위의 ID 범위로, `<registration-id>`를 디바이스의 등록 ID로, `<primarykey>`를 디바이스의 기본 키로, `<provisioning-host>`를 Device Provisioning Service의 서비스 엔드포인트 URL로 바꾸기).

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

6. 다음 명령을 사용하여 샘플 코드를 빌드하고 실행합니다.

   ```console
    npm install
    ```

    ```console
    node register_symkey.js
    ```

7. 이제 다음 출력과 비슷하게 표시됩니다. “Hello World” 문자열이 테스트 메시지로 허브에 전송됩니다.

     ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run --s 0ne00000A0A --i symm-key-csharp-device-01 --p sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==

    Initializing the device provisioning client...
    Initialized for registration Id symm-key-csharp-device-01.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device csharp-device-01 registered to ExampleIoTHub.azure-devices.net.
    Creating symmetric key authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    Enter any key to exit.
    ```

::: zone-end

::: zone pivot="programming-language-python"

샘플 프로비저닝 코드는 다음 작업을 순서대로 수행합니다.

1. 다음 네 개의 매개 변수를 사용하여 Device Provisioning 리소스로 디바이스를 인증합니다.

    * `PROVISIONING_HOST`
    * `PROVISIONING_IDSCOPE`
    * `PROVISIONING_REGISTRATION_ID`
    * `PROVISIONING_SYMMETRIC_KEY`

2. Device Provisioning Service 인스턴스에 이미 연결된 IoT Hub에 디바이스를 할당합니다.

3. IoT 허브에 테스트 원격 분석 메시지를 보냅니다.

디바이스 정보를 사용하여 프로비저닝 샘플을 업데이트하고 실행하려면:

1. Device Provisioning Service의 기본 메뉴에서 **개요** 를 선택합니다.

2. **ID 범위** 및 **서비스 엔드포인트** 값을 복사합니다.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Device Provisioning Service 엔드포인트 정보 추출":::

3. 이제 명령 프롬프트에서 다음 환경 변수를 설정합니다(`<id-scope>`을 Device Provisioning Service ID 범위의 ID 범위로, `<registration-id>`를 디바이스의 등록 ID로, `<primarykey>`를 디바이스의 기본 키로, `<provisioning-host>`를 Device Provisioning Service의 서비스 엔드포인트 URL로 바꾸기).

    ```console
    set PROVISIONING_HOST=<provisioning-host>
    ```

    ```console
    set PROVISIONING_IDSCOPE=<id-scope>
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=<registration-id>
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=<primarykey>
    ```

4. 명령 프롬프트 창에서 다음 디렉터리로 이동합니다.

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

5. *_provision_symmetric_key.py_* 에서 Python 샘플 코드를 실행합니다.

    ```console
    python provision_symmetric_key.py
    ```

6. 이제 다음 출력과 비슷하게 표시됩니다. 몇 가지 예제 풍속 원격 분석 메시지도 테스트로 허브에 전송됩니다.

     ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```

::: zone-end

::: zone pivot="programming-language-java"

샘플 프로비저닝 코드는 다음 작업을 순서대로 수행합니다.

1. 다음 네 개의 매개 변수를 사용하여 Device Provisioning 리소스로 디바이스를 인증합니다.

    * `GLOBAL_ENDPOINT`
    * `SCOPE_ID`
    * `REGISTRATION_ID`
    * `SYMMETRIC_KEY`

2. Device Provisioning Service 인스턴스에 이미 연결된 IoT Hub에 디바이스를 할당합니다.

3. IoT 허브에 테스트 원격 분석 메시지를 보냅니다.

디바이스 정보를 사용하여 프로비저닝 샘플을 업데이트하고 실행하려면:

1. Device Provisioning Service의 기본 메뉴에서 **개요** 를 선택합니다.

2. **ID 범위** 및 **서비스 엔드포인트** 값을 복사합니다. 해당 값은 각각 `SCOPE_ID` 및 `GLOBAL_ENDPOINT`입니다.

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/extract-dps-endpoints-host.png" alt-text="Device Provisioning Service 엔드포인트 정보 추출":::

3. 편집을 위해 Java 디바이스 샘플 코드를 엽니다. 디바이스 샘플 코드에 대한 전체 경로는 다음과 같습니다.

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

4. DPS 및 디바이스 등록을 위해 다음 변수의 값을 바꿉니다(`<id-scope>`을 Device Provisioning Service ID 범위의 ID 범위로, `<registration-id>`를 디바이스의 등록 ID로, `<primarykey>`를 디바이스의 기본 키로, `<provisioning-host>`를 Device Provisioning Service의 서비스 엔드포인트 URL로 바꾸기).

    ```java
    private static final String SCOPE_ID = "<id-scope>";
    private static final String GLOBAL_ENDPOINT = "<provisioning-host>";
    private static final String SYMMETRIC_KEY = "<primarykey>";
    private static final String REGISTRATION_ID = "<registration-id>";
    ```

5. 빌드를 위한 명령 프롬프트를 엽니다. Java SDK 리포지토리의 프로비저닝 샘플 프로젝트 폴더로 이동합니다.

    ```cmd
    cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
    ```

6. 샘플을 빌드한 다음, `target` 폴더로 이동하여 만든 `.jar` 파일을 실행합니다.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

7. 이제 다음 출력과 비슷하게 표시됩니다.

     ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

::: zone-end

## <a name="confirm-your-device-provisioning-registration"></a>디바이스 프로비저닝 등록 확인

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 디바이스가 할당된 IoT Hub를 선택합니다.

4. **Explorers** 메뉴에서 **IoT 디바이스** 를 선택합니다.

5. 디바이스가 성공적으로 프로비전된 경우 **상태** 가 *사용* 으로 설정된 디바이스 ID가 목록에 표시됩니다. 디바이스가 표시되지 않으면 페이지 위쪽에서 **새로 고침** 을 선택합니다.

    :::zone pivot="programming-language-ansi-c"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration.png" alt-text="디바이스가 IoT Hub에 등록됨":::

    ::: zone-end
    :::zone pivot="programming-language-csharp"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-csharp.png" alt-text="CSharp 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

    :::zone pivot="programming-language-nodejs"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-nodejs.png" alt-text="Node.js 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

    :::zone pivot="programming-language-python"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-python.png" alt-text="Python 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

    ::: zone pivot="programming-language-java"

    :::image type="content" source="./media/quick-create-simulated-device-symm-key/hub-registration-java.png" alt-text="Java 디바이스가 IoT 허브에 등록됨":::

    ::: zone-end

> [!NOTE]
> 디바이스에 대한 등록 항목의 기본값으로부터 *초기 디바이스 쌍 상태* 를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.
>

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

X.509 인증서 디바이스 프로비저닝

> [!div class="nextstepaction"]
> [빠른 시작 - Azure IoT C SDK를 사용하여 X.509 디바이스 프로비저닝](quick-create-simulated-device-x509.md)
