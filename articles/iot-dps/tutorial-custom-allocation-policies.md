---
title: Azure IoT Hub DPS(Device Provisioning Service)에서 사용자 지정 할당 정책 사용을 위한 자습서
description: Azure IoT Hub DPS(Device Provisioning Service)에서 사용자 지정 할당 정책 사용을 위한 자습서
author: wesmc7777
ms.author: wesmc
ms.date: 04/23/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 108e73334ec51e05530bd7b0fa7a273cff326bdc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274717"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>자습서: DPS(Device Provisioning Service)에서 사용자 지정 할당 정책 사용

사용자 지정 할당 정책을 사용하면 IoT Hub에 디바이스를 할당하는 방법을 더 구체적으로 제어할 수 있습니다. 이 작업은 프로비저닝 중에 실행되는 [Azure Function](../azure-functions/functions-overview.md)에서 사용자 지정 코드를 사용하여 IoT 허브에 디바이스를 할당하는 방법으로 수행됩니다. 디바이스 프로비저닝 서비스는 Azure 함수 코드를 호출하여 디바이스 및 등록과 관련된 모든 정보를 제공합니다. 실행된 함수 코드는 디바이스를 프로비전하는 데 사용된 IoT Hub 정보를 반환합니다.

사용자 지정 할당 정책을 사용하면 Device Provisioning Service에서 제공하는 정책이 시나리오의 요구 사항을 충족하지 않을 경우 자체 할당 정책을 정의합니다.

예를 들어 프로비저닝 중에 디바이스에서 사용하는 인증서를 검사하고 인증서 속성을 기반으로 IoT 허브에 디바이스를 할당할 수 있습니다. 또는 디바이스에 대한 정보가 데이터베이스에 저장되어 있고 데이터베이스를 쿼리하여 디바이스에 할당해야 하는 IoT 허브를 확인해야 할 수 있습니다.

이 문서에서는 C#으로 작성된 Azure Function으로 대칭 키를 사용하여 토스터 디바이스를 프로비저닝하는 사용자 지정 할당 정책이 있는 등록 그룹을 보여줍니다. 토스터 디바이스로 인식되지 않는 디바이스는 IoT 허브에 프로비저닝되지 않습니다.

디바이스는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)에 포함된 프로비저닝 샘플 코드를 사용하여 프로비저닝을 요청합니다.


이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 사용자 지정 할당 함수를 지원하는 새 Azure 함수 앱 만들기
> * 사용자 지정 할당 정책에 Azure 함수를 사용하여 새 그룹 등록 만들기
> * 두 디바이스에 대한 디바이스 키 만들기
> * [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 예제 디바이스 코드에 대한 개발 환경 설정
> * 디바이스를 실행하여 사용자 지정 할당 정책에 따라 프로비저닝되었는지 확인


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서에서는 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료하여 IoT Hub 및 DPS 인스턴스를 만들었다고 가정합니다.

* 최신 버전의 [Git](https://git-scm.com/download/) 설치

* Windows 개발 환경의 경우 ['C++를 사용한 데스크톱 개발'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) 워크로드를 사용하도록 설정된 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019가 필요합니다. Visual Studio 2015와 Visual Studio 2017도 지원됩니다.

* Linux 또는 macOS의 경우 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>사용자 지정 할당 함수 만들기

이 섹션에서는 사용자 지정 할당 정책을 구현하는 Azure 함수를 만듭니다. 이 함수는 등록 ID에 문자열 접두사 **contoso-toaster** 가 포함되어 있는지 여부에 따라 디바이스를 IoT Hub에 등록해야 하는지 여부를 결정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 홈 페이지에서 **+ 리소스 만들기** 를 선택합니다.

2. *Marketplace 검색* 검색 상자에 "함수 앱"을 입력합니다. 드롭다운 목록에서 **함수 앱** 을 선택한 후 **만들기** 를 선택합니다.

3. **함수 앱** 만들기 페이지의 **기본** 탭에서 새 함수 앱에 대해 다음 설정을 입력하고 **검토 + 만들기** 를 선택합니다.

    **구독**: 구독이 여러 개 있고 원하는 구독이 선택되지 않은 경우 사용할 구독을 선택합니다.

    **리소스 그룹**: 이 필드를 통해 새 리소스 그룹을 만들거나 함수 앱을 포함할 기존 리소스 그룹을 선택할 수 있습니다. 이전에 테스트를 위해 만든 Iot 허브가 포함된 동일한 리소스 그룹(예: **TestResources**)을 선택합니다. 모든 관련 리소스를 한 그룹에 배치하여 다 함께 관리할 수 있습니다.

    **함수 앱 이름**: 고유한 함수 앱 이름을 입력합니다. 이 예제에서는 **contoso-function-app** 을 사용합니다.

    **게시**: **코드** 가 선택되어 있는지 확인합니다.

    **런타임 스택**: 드롭다운에서 **.NET Core** 를 선택합니다.

    **지역**: 리소스 그룹과 동일한 지역을 선택합니다. 이 예제에서는 **미국 서부** 를 사용합니다.

    > [!NOTE]
    > 기본적으로 Application Insights를 사용하도록 설정되어 있습니다. 이 문서에서는 Application Insights가 필요하지 않지만 사용자 지정 할당을 통해 발생하는 문제를 이해하고 조사하는 데 도움이 될 수 있습니다. 원하는 경우 **모니터링** 탭을 선택한 다음, **Application Insights 사용** 에 **아니오** 를 선택하여 Application Insights를 사용하지 않도록 설정할 수 있습니다.

    ![사용자 지정 할당 함수를 호스트할 Azure 함수 앱 만들기](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. **요약** 페이지에서 **만들기** 를 선택하여 함수 앱을 만듭니다. 배포하는 데 몇 분 정도 걸릴 수 있습니다. 완료되면 **리소스로 이동** 을 선택합니다.

5. **함수** 아래 왼쪽 창에서 **함수** 를 클릭한 다음, **+ 추가** 를 클릭하여 새 함수를 추가합니다.

6. 템플릿 페이지에서 **HTTP 트리거** 타일을 선택하고 **함수 만들기** 를 선택합니다. **HttpTrigger1** 이라는 함수가 만들어지고 함수에 대한 개요 페이지가 포털에 표시됩니다.

7. 새 함수에 대해 **코드 + 테스트** 를 클릭합니다. 포털에 **run.csx** 코드 파일의 콘텐츠가 표시됩니다. 

8. **HttpTrigger1** 함수의 코드를 다음 코드로 바꾸고 **저장** 을 선택합니다. 사용자 지정 할당 코드를 사용할 준비가 되었습니다.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. **run.csx** 코드 파일의 바로 아래에 있는 **로그** 를 클릭하여 사용자 지정 할당 함수의 로깅을 모니터링합니다. 


## <a name="create-the-enrollment"></a>등록 만들기

이 섹션에서는 사용자 지정 할당 정책을 사용하는 새 등록 그룹을 만듭니다. 간단한 설명을 위해 이 문서에서는 등록에 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 사용합니다. 더 안전한 솔루션을 위해 신뢰 체인과 함께 [X.509 인증서 증명](concepts-x509-attestation.md)을 사용하는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com)에서 프로비저닝 서비스를 엽니다.

2. 왼쪽 창에서 **등록 관리** 를 선택한 다음, 페이지 맨 위에 있는 **등록 그룹 추가** 단추를 선택합니다.

3. **등록 그룹 추가** 에서 아래 표에 있는 정보를 입력하고 **저장** 단추를 클릭합니다.

    | 필드 | 설명 및/또는 제안 값 |
    | :---- | :----------------------------- |
    | **그룹 이름** | **contoso-custom-allocated-devices** 입력 |
    | **증명 유형** | **대칭 키** 선택 |
    | **키 자동 생성** | 이 확인란은 이미 선택되어 있습니다. |
    | **허브에 디바이스를 할당할 방법 선택** | **사용자 지정(Azure Function 사용)** 을 선택합니다. |
    | **이 그룹을 할당할 수 있는 IoT 허브 선택** | 빠른 시작을 완료할 때 이전에 만든 IoT 허브를 선택합니다. |
    | **Azure Function 선택** | 직접 만든 함수 앱이 포함된 구독을 선택합니다. 그런 다음, 함수에 **contoso-function-app** 및 **HttpTrigger1** 을 선택합니다. |

    ![대칭 키 증명에 대한 사용자 지정 할당 등록 그룹 추가](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 등록을 저장한 후 등록을 다시 열고 **기본 키** 를 기록해 두세요. 키를 생성하려면 먼저 등록을 저장해야 합니다. 이 기본 대칭 키는 나중에 프로비저닝을 시도하는 디바이스에 대해 고유한 디바이스 키를 생성하는 데 사용됩니다. 

## <a name="derive-unique-device-keys"></a>고유한 디바이스 키 파생

디바이스는 기본 대칭 키를 직접 사용하지 않습니다. 대신 기본 키를 사용하여 각 디바이스에 대한 디바이스 키를 파생합니다. 이 섹션에서는 두 개의 고유한 디바이스 키를 만듭니다. 하나의 키는 시뮬레이트된 토스터 디바이스에 사용됩니다. 다른 키는 시뮬레이트된 열 펌프 디바이스에 사용됩니다. 생성된 키를 통해 두 디바이스에서 등록을 시도할 수 있습니다. 사용자 지정 할당 정책 예제 코드에서 허용할 유효한 접미사는 디바이스 등록 ID 하나에만 있습니다. 따라서 하나는 허용되고 다른 하나는 거부됩니다.

디바이스 키를 파생하려면 이전에 적어둔 대칭 키를 사용하여 각 디바이스에 대한 디바이스 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산하고 결과를 Base64 형식으로 변환합니다. 등록 그룹을 사용하여 파생된 디바이스 키를 만드는 방법에 대한 자세한 내용은 [대칭 키 증명](concepts-symmetric-key-attestation.md)의 그룹 등록 섹션을 참조하세요.

이 문서의 예제에서는 아래 코드와 함께 다음 두 디바이스 등록 ID를 사용하여 두 디바이스의 디바이스 키를 계산합니다.

* **contoso-toaster-007**
* **contoso-heatpump-088**


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 IoT 확장에서는 파생된 디바이스 키를 생성할 수 있는 [`compute-device-key`](/cli/azure/iot/dps?view=azure-cli-latest&preserve-view=true#az_iot_dps_compute_device_key) 명령을 제공합니다. 이 명령은 Windows 기반 시스템이나 Linux 시스템의 PowerShell 또는 Bash 셸에서 사용할 수 있습니다.

`--key` 인수 값을 등록 그룹의 **기본 키** 로 바꿉니다.

```azurecli
az iot dps compute-device-key --key oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA== --registration-id contoso-toaster-007

"JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs="
```

```azurecli
az iot dps compute-device-key --key oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA== --registration-id contoso-heatpump-088

"6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg="
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Windows 기반 워크스테이션을 사용하는 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 변수 값을 앞서 등록 그룹을 만든 후에 적어둔 **기본 키** 로 바꿉니다. 아래 코드와 함께 표시된 키 값과 출력은 예시일 뿐입니다.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

# <a name="bash"></a>[Bash](#tab/bash)

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 Bash 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 변수 값을 앞서 등록 그룹을 만든 후에 적어둔 **기본 키** 로 바꿉니다. 아래 코드와 함께 표시된 키 값과 출력은 예시일 뿐입니다.


```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

디바이스는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)에 포함된 프로비저닝 샘플 코드를 사용하여 프로비저닝을 요청합니다.

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용되는 개발 환경을 준비합니다. SDK에는 시뮬레이트된 디바이스의 샘플 코드가 포함되어 있습니다. 이 시뮬레이트된 디바이스는 디바이스의 부팅 시퀀스 중에 프로비저닝을 시도합니다.

이 섹션은 Windows 기반 워크스테이션에 적용됩니다. Linux 예제를 보려면 [다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법](how-to-provision-multitenant.md)에서 VM 설정을 참조하세요.

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

    `cmake`에서 C++ 컴파일러를 찾을 수 없으면 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs)에서 명령을 실행합니다.

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

## <a name="simulate-the-devices"></a>디바이스 시뮬레이트

이 섹션에서는 이전에 설정한 Azure IoT C SDK에 있는 **prov\_dev\_client\_sample** 이라는 프로비저닝 샘플을 업데이트합니다.

이 샘플 코드는 프로비저닝 요청을 Device Provisioning Service 인스턴스에 보내는 디바이스 부팅 시퀀스를 시뮬레이트합니다. 부팅 시퀀스를 통해 토스터 디바이스가 인식되고 사용자 지정 할당 정책을 통해 IoT Hub에 할당됩니다.

1. Azure Portal에서 Device Provisioning Service에 대한 **개요** 탭을 선택하고 **_ID 범위_** 값을 기록해 둡니다.

    ![포털 블레이드에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device-x509/copy-id-scope.png) 

2. Visual Studio에서 이전에 CMake를 실행하여 생성된 **azure_iot_sdks.sln** 솔루션 파일을 엽니다. 솔루션 파일은 다음 위치에 있습니다.

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. `main()` 함수에서 `Prov_Device_Register_Device()`에 대한 호출을 찾습니다. 해당 호출 직전에 [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload)를 사용하는 다음 코드 줄을 추가하여 프로비저닝 중에 사용자 지정 JSON 페이로드를 전달합니다. 사용자 지정 할당 함수에 더 많은 정보를 제공하는 데 사용할 수 있습니다. 등록 ID를 검사하는 대신 디바이스 유형을 전달하는 데에도 사용할 수 있습니다. DPS를 사용하여 사용자 지정 데이터 페이로드를 보내고 받는 방법에 대한 자세한 내용은 [디바이스와 DPS 간에 페이로드를 전송하는 방법](how-to-send-additional-data.md)을 참조하세요.

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.

### <a name="simulate-the-contoso-toaster-device"></a>Contoso 토스터 디바이스 시뮬레이트

1. 토스터 디바이스를 시뮬레이션하려면 **prov\_dev\_client\_sample.c** 에서 주석으로 처리된 `prov_dev_set_symmetric_key_info()` 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 주석 처리를 제거하고 자리 표시자 값(꺾쇠 괄호 포함)을 이전에 생성한 토스터 등록 ID 및 파생된 디바이스 키로 바꿉니다. 아래 표시된 키 값 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 은 예제로만 제공됩니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    파일을 저장합니다.

2. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예** 를 선택하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 텍스트는 토스터 디바이스에 대해 실행 중인 사용자 지정 할당 함수 코드의 로깅 출력 예입니다. 토스터 디바이스용 허브가 성공적으로 선택되었는지 확인합니다. 또한 코드에 추가한 사용자 지정 JSON 콘텐츠를 포함하는 `payload` 멤버를 확인합니다. `deviceRuntimeContext` 내에서 코드를 사용할 수 있습니다.

    이 로깅은 포털의 함수 코드에서 **로그** 를 클릭하여 사용할 수 있습니다.

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    다음 예제 디바이스 출력은 시뮬레이션된 토스터 디바이스를 성공적으로 부팅하고, 사용자 지정 할당 정책을 통해 토스터 IoT 허브에 할당할 Provisioning Service 인스턴스에 연결하는 것을 보여줍니다.

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Contoso 열 펌프 디바이스 시뮬레이트

1. 열 펌프 디바이스를 시뮬레이션하려면 다시 **prov\_dev\_client\_sample.c** 의 `prov_dev_set_symmetric_key_info()` 호출을 이전에 생성한 열 펌프 등록 ID 및 파생된 디바이스 키로 업데이트합니다. 아래 표시된 키 값 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg** 도 예제로만 제공됩니다.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    파일을 저장합니다.

2. Visual Studio 메뉴에서 **디버그** > **디버깅하지 않고 시작** 을 선택하여 솔루션을 실행합니다. 프로젝트를 다시 빌드하라는 프롬프트에서 **예** 를 선택하여 실행하기 전에 프로젝트를 다시 빌드합니다.

    다음 텍스트는 열 펌프 디바이스에 대해 실행 중인 사용자 지정 할당 함수 코드의 로깅 출력 예입니다. 사용자 지정 할당 정책은 이 등록을 거부하고 HTTP 오류 400 잘못된 요청을 표시합니다. 코드에 추가한 사용자 지정 JSON 콘텐츠를 포함하는 `payload` 멤버를 확인합니다. `deviceRuntimeContext` 내에서 코드를 사용할 수 있습니다.

    이 로깅은 포털의 함수 코드에서 **로그** 를 클릭하여 사용할 수 있습니다.

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    다음 예제 디바이스 출력은 시뮬레이션된 열 펌프 디바이스를 부팅하고 Provisioning Service 인스턴스에 연결하여 사용자 지정 할당 정책을 사용하여 IoT 허브에 등록을 시도하는 것을 보여줍니다. 사용자 지정 할당 정책이 토스터 디바이스만 허용하도록 설계되었으므로 오류(`Custom allocation failed with status code: 400`)로 인해 실패합니다.


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 생성된 리소스를 계속 사용하려는 경우 리소스를 그대로 유지할 수 있습니다. 리소스를 계속 사용하지 않으려면 다음 단계를 통해 이 문서에서 만든 모든 리소스를 삭제해야 불필요한 비용을 방지할 수 있습니다.

이러한 단계에서는 **contoso-us-resource-group** 이라는 동일한 리소스 그룹에 표시된 대로 이 문서에서 모든 리소스를 만들었다고 가정합니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

이름별로 리소스 그룹을 삭제하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.

2. **이름별 필터...** 텍스트 상자에 리소스 **contoso-us-resource-group** 을 포함하는 리소스 그룹의 이름을 입력합니다. 

3. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제** 를 선택합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

보다 자세한 사용자 지정 할당 정책 예는 다음을 참조하세요. 

> [!div class="nextstepaction"]
> [사용자 지정 할당 정책을 사용하는 방법](how-to-use-custom-allocation-policies.md)

* 재프로비저닝에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT Hub 디바이스 다시 프로비전 개념](concepts-device-reprovision.md)

* 프로비저닝 해제에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [이전에 자동 프로비저닝된 디바이스의 프로비저닝을 해제하는 방법](how-to-unprovision-devices.md)