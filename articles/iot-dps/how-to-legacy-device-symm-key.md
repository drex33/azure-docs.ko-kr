---
title: 대칭 키를 사용한 디바이스 프로비전 - Azure IoT Hub Device Provisioning Service
description: 대칭 키를 사용하여 DPS(Device Provisioning Service) 인스턴스로 디바이스를 프로비전하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 04/23/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: c2330203ce2617bf7cc42b8c7549d11a73185f12
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458248"
---
# <a name="how-to-provision-devices-using-symmetric-key-enrollment-groups"></a>대칭 키 등록 그룹을 사용하여 디바이스를 프로비전하는 방법

이 문서에서는 등록 그룹을 사용 하 여 여러 시뮬레이션 된 대칭 키 장치를 단일 IoT Hub에 안전 하 게 프로 비전 하는 방법을 보여 줍니다.

일부 디바이스에는 디바이스를 안전하게 식별하는 데 사용할 수 있는 인증서, TPM 또는 다른 보안 기능이 없을 수 있습니다. 디바이스 프로비저닝 서비스는 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 포함합니다. MAC 주소 또는 일련 번호와 같은 고유 정보 기반 디바이스를 식별하는 데 대칭 키 증명을 사용할 수 있습니다.

[HSM(하드웨어 보안 모듈)](concepts-service.md#hardware-security-module) 및 인증서를 쉽게 설치할 수 있는 경우 디바이스를 식별하고 프로비전하는 더 나은 방법이 될 수 있습니다. HSM을 사용하면 모든 디바이스에 배포되는 코드를 업데이트하지 않아도 되며, 디바이스 이미지에 포함되는 비밀 키가 없습니다. 이 문서에서는 HSM 또는 인증서가 모두 실행 가능한 옵션이 아니라고 가정합니다. 그러나 이러한 디바이스를 프로비전하는 디바이스 프로비저닝 서비스를 사용하기 위해 디바이스 코드를 업데이트하는 몇 가지 방법이 있다고 가정합니다. 

이 문서는 또한 마스터 그룹 키 또는 파생된 디바이스 키에 대한 무단 액세스를 방지하기 위해 보안 환경에서 디바이스 업데이트가 수행된다고 가정합니다.

이 문서는 Windows 기반 워크스테이션에 적용됩니다. 그러나 Linux에서 절차를 수행할 수 있습니다. Linux 예제는 [다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법](how-to-provision-multitenant.md)을 참조하세요.

> [!NOTE]
> 이 문서에서 사용되는 샘플은 C 언어로 작성됩니다. 또한 [C# 디바이스 프로비저닝 대칭 키 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/provisioning/Samples/device/SymmetricKeySample)을 사용할 수도 있습니다. 이 샘플을 사용하려면 [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) 리포지토리를 다운로드하거나 복제하고 샘플 코드의 인라인 지침을 따르세요. 이 문서의 지침을 따라 포털을 사용하여 대칭 키 등록 그룹을 만들고 샘플 실행에 필요한 ID 범위 및 등록 그룹 기본 및 보조 키를 찾을 수 있습니다. 또한 샘플을 사용하여 개별 등록을 만들 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md) 빠른 시작을 완료해야 합니다.

다음 필수 구성 요소는 Windows 개발 환경을 위한 것입니다. Linux 또는 macOS의 경우 SDK 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.

* ['C++를 사용한 데스크톱 개발'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) 워크로드를 사용하도록 설정된 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019. Visual Studio 2015와 Visual Studio 2017도 지원됩니다.

* 최신 버전의 [Git](https://git-scm.com/download/) 설치

## <a name="overview"></a>개요

해당 디바이스를 식별하는 정보에 따라 각 디바이스에 대해 고유한 등록 ID가 정의됩니다. 예를 들어 MAC 주소 또는 일련 번호입니다.

[대칭 키 증명](concepts-symmetric-key-attestation.md)을 사용하는 등록 그룹은 디바이스 프로비저닝 서비스를 사용하여 만들어집니다. 등록 그룹은 그룹 마스터 키를 포함합니다. 각 디바이스에 대한 고유한 디바이스 키를 생성하기 위해 각 고유 등록 ID를 해시하는 데 마스터 키가 사용됩니다. 디바이스는 해당 고유한 등록 ID로 파생된 디바이스 키를 사용하여 디바이스 프로비저닝 서비스로 증명되고 IoT 허브에 할당됩니다.

이 문서에서 설명 하는 장치 코드는 [퀵 스타트: 시뮬레이션 된 대칭 키 장치 프로 비전](quick-create-simulated-device-symm-key.md)과 동일한 패턴을 따릅니다. 코드에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 샘플을 사용하여 디바이스를 시뮬레이션합니다. 시뮬레이션된 디바이스는 빠른 시작에서 설명된 것처럼 개별 등록 대신 등록 그룹으로 증명됩니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. 

SDK에는 시뮬레이트된 디바이스의 샘플 코드가 포함되어 있습니다. 이 시뮬레이트된 디바이스는 디바이스의 부팅 시퀀스 중에 프로비저닝을 시도합니다.

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. SDK의 [최신 릴리스](https://github.com/Azure/azure-iot-sdk-c/releases/latest)에 대한 태그 이름을 찾습니다.

3. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제합니다. 이전 단계에서 찾은 태그를 `-b` 매개 변수의 값으로 사용합니다.

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

4. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. `azure-iot-sdk-c` 디렉터리에서 다음 명령을 실행합니다.

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 개발 클라이언트 플랫폼에 관련된 SDK 버전을 빌드하는 다음 명령을 실행합니다. 또한 시뮬레이션된 디바이스에 대한 Visual Studio 솔루션이 `cmake` 디렉터리에서 생성됩니다. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    `cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 

    빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>대칭 키 등록 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Device Provisioning Service 인스턴스를 엽니다.

2. **등록 관리** 탭을 선택한 다음, 페이지 위쪽에 있는 **등록 그룹 추가** 단추를 클릭합니다. 

3. **등록 그룹 추가** 에서 다음 정보를 입력하고 **저장** 단추를 클릭합니다.

   - **그룹 이름**: **mylegacydevices** 를 입력합니다.

   - **증명 형식**: **대칭 키** 를 선택합니다.

   - **키 자동 생성**: 이 상자를 선택합니다.

   - **허브에 디바이스를 할당할 방법 선택**: 특정 허브에 할당할 수 있도록 **정적 구성** 을 선택합니다.

   - **이 그룹을 할당할 IoT 허브 선택**: 사용자의 허브 중 하나를 선택합니다.

     ![대칭 키 증명에 대한 등록 그룹 추가](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. 등록을 저장하면 **기본 키** 및 **보조 키** 가 생성되고 등록 항목에 추가됩니다. 대칭 키 등록 그룹이 *등록 그룹* 탭의 *그룹 이름* 열 아래에 **mylegacydevices** 로 표시됩니다. 

    등록을 열고 생성된 **기본 키** 의 값을 복사합니다. 이 키는 마스터 그룹 키입니다.


## <a name="choose-a-unique-registration-id-for-the-device"></a>디바이스에 대한 고유한 등록 ID를 선택합니다.

각 디바이스를 식별하기 위한 고유한 등록 ID가 정의되어야 합니다. 디바이스에서 MAC 주소, 일련 번호 또는 고유 정보를 사용할 수 있습니다. 

이 예제에서는 등록 ID에 대한 다음 문자열을 형성하는 MAC 주소와 일련 번호의 조합을 사용합니다.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

각 디바이스에 대해 고유한 등록 ID를 만듭니다. 유효한 문자는 소문자 영숫자 및 대시('-')입니다.


## <a name="derive-a-device-key"></a>디바이스 키 파생 

디바이스 키를 생성하려면 등록 그룹 마스터 키를 사용하여 각 디바이스에 대해 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산합니다. 그러면 결과는 각 디바이스에 대해 Base64 형식으로 변환됩니다.

> [!WARNING]
> 각 디바이스의 디바이스 코드는 그 디바이스에 해당하는 파생된 디바이스 키만 포함해야 합니다. 디바이스 코드에 그룹 마스터 키는 포함하지 않습니다. 손상된 마스터 키는 해당 키로 인증되는 모든 디바이스의 보안을 손상할 가능성이 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 IoT 확장에서는 파생된 디바이스 키를 생성할 수 있는 [`compute-device-key`](/cli/azure/iot/dps?view=azure-cli-latest&preserve-view=true#az_iot_dps_compute_device_key) 명령을 제공합니다. 이 명령은 Windows 기반 시스템이나 Linux 시스템의 PowerShell 또는 Bash 셸에서 사용할 수 있습니다.

`--key` 인수 값을 등록 그룹의 **기본 키** 로 바꿉니다.

`--registration-id` 인수의 값을 등록 ID로 바꿉니다.

```azurecli
az iot dps compute-device-key --key 8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw== --registration-id sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

결과 예제:

```azurecli
"Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc="
```
# <a name="windows"></a>[Windows](#tab/windows)

Windows 기반 워크스테이션을 사용하는 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 의 값을 이전에 적어 둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 등록 ID로 바꿉니다.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 의 값을 이전에 적어 둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 등록 ID로 바꿉니다.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---

각 디바이스는 파생된 디바이스 키와 고유한 등록 ID를 사용하여 프로비전하는 동안 등록 그룹과의 대칭 키 증명을 수행합니다.



## <a name="create-a-device-image-to-provision"></a>프로비전할 디바이스 이미지 만들기

이 섹션에서는 이전에 설정한 Azure IoT C SDK에 있는 **prov\_dev\_client\_sample** 이라는 프로비저닝 샘플을 업데이트합니다. 

이 샘플 코드는 프로비저닝 요청을 Device Provisioning Service 인스턴스에 보내는 디바이스 부팅 시퀀스를 시뮬레이트합니다. 부팅 시퀀스를 통해 디바이스가 인식되고 등록 그룹에서 구성한 IoT 허브에 할당됩니다. 이는 등록 그룹을 사용하여 프로비전되는 각 디바이스에 대해 완료됩니다.

1. Azure Portal Device Provisioning Service에 대한 **개요** 탭을 선택하고 ID **_범위_** 값을 적어 둡다.

    ![포털 블레이드에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device-x509/copy-id-scope.png) 

2. Visual Studio에서 이전에 CMake를 실행하여 생성된 **azure_iot_sdks.sln** 솔루션 파일을 엽니다. 솔루션 파일은 다음 위치에 있습니다.

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample** 이라는 샘플 프로젝트를 확장합니다. **원본 파일** 을 확장하고, **prov\_dev\_client\_sample.c** 를 엽니다.

4. `id_scope` 상수를 찾고, 값을 앞에서 복사한 **ID 범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 동일한 파일에서 `main()` 함수에 대한 정의를 찾습니다. 아래와 같이 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`로 설정되었는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **prov\_dev\_client\_sample.c** 에서 주석으로 처리된 `prov_dev_set_symmetric_key_info()` 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거하고 자리 표시자 값(꺾쇠 괄호 포함)을 디바이스의 고유 등록 ID 및 사용자가 생성한 파생된 디바이스 키로 바꿉니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    파일을 저장합니다.

7. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다. 

8. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예** 를 클릭하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 출력은 시뮬레이션된 디바이스를 성공적으로 부팅하고, IoT 허브에 할당할 프로비저닝 서비스 인스턴스에 연결하는 예제입니다.

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. 포털에서 시뮬레이션된 디바이스가 할당된 IoT 허브로 이동하고, **IoT 디바이스** 탭을 클릭합니다. 시뮬레이션된 디바이스가 허브에 성공적으로 프로비전되면 *상태* 가 **사용** 인 디바이스 ID가 **IoT 디바이스** 블레이드에 표시됩니다. 위쪽에서 **새로 고침** 단추를 클릭해야 할 수 있습니다. 

    ![디바이스가 IoT Hub에 등록됨](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>보안에 대한 우려

이 경우 파생된 디바이스 키가 각 디바이스의 이미지에 포함된 채로 있으므로 권장되는 보안 모범 사례가 아닙니다. 이러한 이유로 보안과 사용 편의성은 종종 상충합니다. 사용자는 본인의 요구 사항에 따라 디바이스의 보안을 철저히 검토해야 합니다.


## <a name="next-steps"></a>다음 단계

* 재프로비전에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT Hub 디바이스 다시 프로비전 개념](concepts-device-reprovision.md)

> [!div class="nextstepaction"]
> [빠른 시작: 시뮬레이션된 대칭 키 디바이스 프로비전](quick-create-simulated-device-symm-key.md)

* 프로비전 해제에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [이전에 자동으로 프로비전된 디바이스의 프로비전을 해제하는 방법](how-to-unprovision-devices.md)
