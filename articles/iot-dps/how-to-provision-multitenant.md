---
title: Azure IoT Hub Device Provisioning Service에서 다중 테넌시를 위한 디바이스를 프로비저닝하는 방법
description: Device Provisioning Service(DPS) 인스턴스를 사용하여 다중 테넌시를 위한 디바이스를 프로비저닝하는 방법
author: anastasia-ms
ms.author: v-stharr
ms.topic: how-to
ms.date: 10/02/2021
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 595bd473bb70f4c23554998b7240e4ef0ed1183d
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670319"
---
# <a name="how-to-provision-for-multitenancy"></a>다중 테넌트를 지원하기 위해 장치를 프로비전하는 방법 

이 방법에서는 [할당 정책을](concepts-service.md#allocation-policy)사용하여 여러 시뮬레이션된 대칭 키 디바이스를 IoT Hub 그룹에 안전하게 프로비전하는 방법을 보여줍니다. 프로비전 서비스에서 정의한 할당 정책은 다양한 할당 시나리오를 지원합니다. 가장 일반적인 두 가지 시나리오는 다음과 같습니다.

* **지리적 위치/지리적** 위치: 디바이스가 위치 간에 이동함에 따라 각 위치에 가장 가까운 IoT Hub에 디바이스를 프로비전하여 네트워크 대기 시간이 향상됩니다. 이 시나리오에서는 지역에 걸쳐 있는 IoT Hub 그룹이 등록을 위해 선택됩니다. 이러한 등록에 대해 **최저 대기 시간** 할당 정책이 선택되었습니다. 이 정책에 따라 Device Provisioning Service는 디바이스 대기 시간을 평가하고 IoT Hub 그룹 중에서 가장 가까운 IoT Hub를 확인합니다.

* **다중 테넌시**: IoT 솔루션 내에서 사용되는 디바이스를 특정 IoT Hub 또는 IoT Hub 그룹에 할당해야 할 수 있습니다. 이 솔루션은 특정 테넌트의 모든 디바이스가 특정 IoT Hub 그룹과 통신하도록 요구할 수 있습니다. 일부 경우에는 테넌트가 자신의 IoT Hub를 소유하고 디바이스를 IoT Hub에 할당하도록 요구할 수 있습니다.

이러한 두 시나리오를 결합하는 것이 일반적입니다. 예를 들어 다중 테넌트 IoT 솔루션은 일반적으로 여러 지역에 분산된 IoT Hub 그룹을 사용하여 테넌트 디바이스를 할당합니다. 이러한 테넌트 디바이스는 지리적 위치에 따라 대기 시간이 가장 짧은 그룹의 IoT Hub에 할당할 수 있습니다.

이 문서에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)의 시뮬레이트된 디바이스 샘플을 사용하여 여러 지역의 다중 테넌트 시나리오에서 디바이스를 프로비전하는 방법을 보여 줍니다. 이 문서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * Azure CLI 사용하여 두 개의 지역별 IoT **Hub(미국 서부 2** 및 **미국 동부)를** 만듭니다.
> * 다중 테넌트 등록 만들기
> * Azure CLI 사용하여 동일한 지역에서 디바이스 역할을 하는 두 개의 지역 Linux VM(미국 서부 2 및 **미국 동부)을** 만듭니다. 
> * 두 Linux VM에서 Azure IoT C SDK에 대한 개발 환경 준비
> * 디바이스를 시뮬레이트하여 가장 가까운 지역의 동일한 테넌트에 대해 프로비전되는지 확인합니다.

>[!IMPORTANT]
> 일부 지역에서는 때때로 Virtual Machines 만들기에 제한을 적용할 수 있습니다. 이 가이드를 작성할 당시 *westus2* 및 *eastus* 지역은 VM 생성을 허용했습니다. 이러한 지역 중 하나에서 만들 수 없는 경우 다른 지역을 사용해 볼 수 있습니다. VM을 만들 때 Azure 지리적 지역을 선택하는 방법에 대한 자세한 내용은 [Azure에서 가상 머신에 대한 지역을](../virtual-machines/regions.md) 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* Azure Portal IoT Hub Device Provisioning Service [설정의](./quick-setup-auto-provision.md)단계를 완료합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>두 개의 지역별 IoT Hub 만들기

이 섹션에서는 Azure 리소스 그룹 및 테넌트에 대한 두 개의 새로운 지역 IoT Hub 리소스를 만듭니다. 한 IoT Hub는 **미국 서부 2** 지역에 대해, 다른 하나는 **미국 동부** 지역에 대한 허브입니다.

>[!IMPORTANT]
>이 문서에서 만든 모든 리소스에 대해 동일한 리소스 그룹을 사용하는 것이 좋습니다. 이렇게 하면 작업을 완료한 후 정리가 더 쉬워집니다.

1. Azure Cloud Shell 다음 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create --name contoso-us-resource-group --location eastus
    ```

2. *eastus* 위치에 IoT Hub를 만들고 다음 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령을 사용하여 만든 리소스 그룹에 추가합니다(고유한 `{unique-hub-name}` 이름으로 바꾸기).

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

3. 이제 *westus2* 위치에 IoT Hub를 만들고 다음 [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령을 사용하여 만든 리소스 그룹에 추가합니다(고유한 `{unique-hub-name}` 이름으로 바꾸기).

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location westus2 --sku S1
    ```

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-the-multitenant-enrollment"></a>다중 테넌트 등록 만들기

이 섹션에서는 테넌트 디바이스에 대한 새 등록 그룹을 만듭니다.  

간단한 설명을 위해 이 문서에서는 등록에 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 사용합니다. 더 안전한 솔루션을 위해 신뢰 체인과 함께 [X.509 인증서 증명](concepts-x509-attestation.md)을 사용하는 것이 좋습니다.

1. Azure Portal Device Provisioning Service를 선택합니다.

2. **설정** 메뉴에서 **등록 관리를 선택합니다.**

3. **+ 등록 그룹 추가를** 선택합니다.

4. 등록 **그룹 추가** 페이지에서 다음 정보를 입력합니다.

    **그룹 이름**: *contoso-us-devices* 를 입력합니다.

    **증명 형식**: *대칭 키* 를 선택합니다.

    **키 자동 생성**: 이 확인란은 이미 선택되어 있습니다.

    **허브에 디바이스를 할당할 방법 선택**: *최저 대기 시간* 을 선택합니다.

5. **새 IoT Hub 연결을** 선택합니다.

    :::image type="content" source="./media/how-to-provision-multitenant/create-multitenant-enrollment.png" alt-text="대칭 키 증명에 대한 다중 테넌트 등록 그룹을 추가합니다.":::

6. **IoT Hub에 대한 링크 추가** 페이지에서 다음 정보를 입력합니다.

    **구독**: 여러 구독이 있는 경우 지역별 IoT Hub를 만든 구독을 선택합니다.

    **IoT Hub:** *eastus* 위치에 대해 만든 IoT Hub를 선택합니다.

    **액세스 정책:** *iothubowner 를* 선택합니다.

    :::image type="content" source="./media/how-to-provision-multitenant/link-regional-hubs.png" alt-text="지역별 IoT Hub를 프로비전 서비스와 연결합니다.":::

7. **저장** 을 선택합니다.

8. westgus 위치에 대해 만든 두 번째 IoT Hub에 대해 5~7단계를 *반복합니다.*

9. 이 그룹에 할당할 IoT Hub 선택 드롭다운에서 만든 두 개의 **IoT Hub를 선택합니다.**

    :::image type="content" source="./media/how-to-provision-multitenant/enrollment-regional-hub-group.png" alt-text="연결된 IoT Hub를 선택합니다.":::

10. **저장** 을 선택합니다.

11. 등록 그룹 목록에서 *contoso-us-devices를* 선택합니다.

12. 기본 키 를 *복사합니다.* 이 키는 나중에 시뮬레이션된 두 디바이스에 대해 고유한 디바이스 키를 생성하는 데 사용됩니다.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-primary-key.png" alt-text="기본 키를 복사합니다.":::

## <a name="create-regional-linux-vms"></a>지역별 Linux VM 만들기

이 섹션에서는 두 개의 지역 Linux VM(가상 머신)을 만듭니다. 이러한 VM은 각 지역에서 디바이스 시뮬레이션 샘플을 실행하여 두 지역의 테넌트 디바이스에 대한 디바이스 프로비저닝을 보여 줍니다.

보다 쉽게 정리하기 위해 이러한 VM은 만들어진 IoT Hub *contoso-us-resource-group* 을 포함하는 동일한 리소스 그룹에 추가됩니다. 그러나 VM은 별도의 지역(미국 서부 **2** 및 **미국 동부)에서** 실행됩니다.

1. Azure Cloud Shell 다음 명령을 실행하여 명령에서 다음 매개 변수를 변경한 후 **미국 동부** 지역 VM을 만듭니다.

    **--name**: **미국 동부** 지역별 디바이스 VM의 고유한 이름을 입력합니다.

    **--admin-username**: 고유한 관리 사용자 이름을 사용합니다.

    **--admin-password**: 고유한 관리 사용자 암호를 사용합니다.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

2. 명령이 완료되면 미국 동부 지역 VM에 대한 **publicIpAddress** 값을 복사합니다.

3. Azure Cloud Shell 명령을 실행하여 명령에서 다음 매개 변수를 변경한 후 **미국 서부 2** 지역 VM을 만듭니다.

    **--name:** **미국 서부 2** 지역 디바이스 VM의 고유한 이름을 입력합니다. 

    **--admin-username**: 고유한 관리 사용자 이름을 사용합니다.

    **--admin-password**: 고유한 관리 사용자 암호를 사용합니다.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest2 \
    --location westus2 \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

4. 명령이 완료되면 미국 서부 2 지역 VM에 대한 **publicIpAddress** 값을 복사합니다.

5. 두 개의 명령줄 셸을 엽니다.

6. SSH를 사용하여 각 셸에서 지역별 VM 중 하나에 연결합니다.

    매개 변수로 복사한 관리 사용자 이름 및 공용 IP 주소를 SSH에 전달합니다. 메시지가 표시되면 관리자 암호를 입력합니다.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK 개발 환경 준비

이 섹션에서는 각 VM에서 Azure IoT C SDK를 복제합니다. SDK에는 각 지역에서 테넌트의 디바이스 프로비저닝을 시뮬레이션하는 샘플이 포함되어 있습니다.

각 VM에 대해 다음을 수행합니다.

1. 다음 명령을 사용하여 **CMake,** **g++**, **gcc** 및 [Git을](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 설치합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

2. SDK의 [최신 릴리스에](https://github.com/Azure/azure-iot-sdk-c/releases/latest) 대한 태그 이름을 찾아 복사합니다.

3. 두 VM에서 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 복제합니다.  이전 단계에서 찾은 태그를 `-b` 매개 변수의 값으로 사용합니다.

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

4. 리포지토리 내에 새 **cmake** 폴더를 만들고 해당 폴더로 변경합니다.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

5. 다음 명령을 실행하여 개발 클라이언트 플랫폼에 특정한 SDK 버전을 빌드합니다.

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

6. 빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```bash
    -- IoT Client SDK Version = 1.7.0
    -- Provisioning SDK Version = 1.7.0
    -- Looking for include file stdint.h
    -- Looking for include file stdint.h - found
    -- Looking for include file stdbool.h
    -- Looking for include file stdbool.h - found
    -- target architecture: x86_64
    -- Performing Test CXX_FLAG_CXX11
    -- Performing Test CXX_FLAG_CXX11 - Success
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so (found version "7.58.0")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/azure-iot-sdk-c
    ```

## <a name="derive-unique-device-keys"></a>고유한 디바이스 키 파생

그룹 등록과 함께 대칭 키 증명을 사용할 때는 등록 그룹 키를 직접 사용하지 않습니다. 대신 각 디바이스에 대한 등록 그룹 키에서 고유 키를 파생합니다. 자세한 내용은 [대칭 키를 사용하여 그룹 등록을 참조하세요.](concepts-symmetric-key-attestation.md#group-enrollments)

자습서의 이 부분에서는 그룹 마스터 키에서 디바이스 키를 생성하여 디바이스에 대한 고유한 등록 ID의 [HMAC-SHA256을](https://wikipedia.org/wiki/HMAC) 컴퓨팅합니다. 그러면 결과가 Base64 형식으로 변환됩니다.

>[!IMPORTANT]
>디바이스 코드에 그룹 마스터 키를 포함하지 마세요.

*eastus* 및 *westus 2* **디바이스의** 경우:

1. openssl 을 사용하여 고유 키를 **생성합니다.** 다음 Bash 셸 스크립트를 `{primary-key}` 사용합니다(을 이전에 복사한 등록 그룹의 **기본 키로** 바꾸고 `{contoso-simdevice-east}` 을 각 디바이스에 대한 고유한 등록 ID로 바꿉 소문자 영숫자와 대시('-') 문자를 사용하여 두 개의 ID를 모두 정의합니다.

    ```bash
    KEY={primary-key}
    REG_ID={contoso-simdevice}
    
    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
    ```

2. 스크립트는 다음 키와 같이 출력됩니다.

    ```bash
    p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
    ```

3. 이제 각 테넌트 디바이스에는 프로비전 프로세스 중에 등록 그룹으로 대칭 키 증명을 수행하기 위한 고유한 파생 디바이스 키와 고유한 등록 ID가 있습니다.

## <a name="simulate-the-devices-from-each-region"></a>각 지역에서 디바이스 시뮬레이트

이 섹션에서는 두 지역 VM에 대한 Azure IoT C SDK의 프로비저닝 샘플을 업데이트합니다. 

이 샘플 코드는 프로비전 요청을 Device Provisioning Service 인스턴스에 보내는 디바이스 부팅 시퀀스를 시뮬레이트합니다. 부팅 시퀀스를 통해 디바이스가 인식되고 대기 시간을 기준으로 가장 가까운 IoT Hub에 할당됩니다.

1. Azure Portal Device Provisioning Service에 대한 **개요** 탭을 선택하고 ID **_범위_** 값을 적어 둡다.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-id-scope.png" alt-text="포털 블레이드에서 Device Provisioning Service 엔드포인트 정보를 추출합니다.":::

2. 두 VM에서 편집을 위해 **~/azure-iot-sdk-c/provisioning \_ client/samples/prov \_ dev client \_ \_ sample/prov \_ dev client \_ \_ sample.c를** 엽니다.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

3. 두 VM에서 상수를 찾고 `id_scope` 값을 이전에 복사한 ID **범위** 값으로 바꿉니다. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. 두 VM에서 동일한 파일에서 함수에 대한 정의를 `main()` 찾습니다. 등록 그룹 증명 방법과 일치하도록 아래에 표시된 것처럼 `hsm_type` 변수가 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`로 설정되어 있는지 확인합니다. 

    두 VM에서 파일 변경 내용을 저장합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. 두 VM에서 모두 **prov\_dev\_client\_sample.c** 에서 주석으로 처리된 `prov_dev_set_symmetric_key_info()` 호출을 찾습니다.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    함수 호출의 압축을 풀고 자리 표시자 값(꺾쇠 괄호 포함)을 이전 섹션에서 파생된 각 디바이스에 대한 고유한 등록 ID 및 파생 디바이스 키로 바꿉니다. 아래 표시된 키는 예제 목적으로만 제공됩니다. 이전에 생성한 키를 사용합니다.

    미국 동부:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    미국 서부:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

6. 두 VM에서 파일을 저장합니다.

7. 두 VM에서 아래에 표시된 샘플 폴더로 이동한 후 샘플을 빌드합니다.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

8. 빌드가 성공적으로 수행되면 두 VM에서 **prov\_dev\_client\_sample.exe** 를 실행하여 각 지역에서 테넌트 디바이스를 시뮬레이트합니다. 각 디바이스는 시뮬레이트된 디바이스 지역에 가장 가까운 테넌트 IoT Hub에 할당됩니다.

    시뮬레이션 실행:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    미국 동부 VM의 출력 예제:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    미국 서부 VM의 출력 예제:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 생성된 리소스를 계속 사용하려는 경우 리소스를 그대로 유지할 수 있습니다. 그렇지 않으면 불필요한 요금이 발생하지 않도록 다음 단계를 사용하여 이 문서에서 만든 모든 리소스를 삭제합니다.

이 단계에서는 **contoso-us-resource-group이라는** 동일한 리소스 그룹에 지시된 대로 이 문서의 모든 리소스를 만들었다고 가정합니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 보관할 리소스가 포함된 기존 리소스 그룹 내에 IoT Hub를 만든 경우 리소스 그룹을 삭제하지 말고 IoT Hub 리소스만 삭제하면 됩니다.
>

이름별로 리소스 그룹을 삭제하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **리소스 그룹** 을 선택합니다.

3. **이름별 필터...** 텍스트 상자에 리소스 **contoso-us-resource-group** 을 포함하는 리소스 그룹의 이름을 입력합니다.

4. 결과 목록의 리소스 그룹 오른쪽에서 **...** 를 클릭한 다음, **리소스 그룹 삭제** 를 클릭합니다.

5. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 다시 입력하여 확인한 다음, **삭제** 를 선택합니다. 잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* 다시 프로비전에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT Hub 디바이스 다시 프로비전 개념](concepts-device-reprovision.md)

* 프로비전 해제에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [이전에 자동으로 프로비전된 디바이스의 프로비전을 해제하는 방법](how-to-unprovision-devices.md)