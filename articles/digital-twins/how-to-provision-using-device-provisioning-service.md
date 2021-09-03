---
title: Device Provisioning Service를 사용하여 디바이스 자동 관리
titleSuffix: Azure Digital Twins
description: DPS(Device Provisioning Service)를 사용하여 Azure Digital Twins에서 IoT 디바이스를 프로비저닝하고 사용 중지하는 자동화된 프로세스를 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c611194819255b4d986f4cfa57d15a2e0d73cd42
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528837"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>DPS(Device Provisioning Service)를 사용하여 Azure Digital Twins에서 디바이스 자동 관리

이 문서에서는 Azure Digital Twins를 [DPS(Device Provisioning Service)](../iot-dps/about-iot-dps.md)와 통합하는 방법에 대해 알아봅니다.

이 문서에서 설명하는 솔루션을 통해 Device Provisioning Service를 사용하여 Azure Digital Twins에서 IoT Hub 디바이스를 **_프로비저닝_** 하고 **_사용 중지_** 하는 프로세스를 자동화할 수 있습니다. 

_프로비저닝_ 및 _사용 중지_ 단계에 대한 자세한 내용 및 모든 엔터프라이즈 IoT 프로젝트에 공통적인 일반 디바이스 관리 단계를 보다 잘 이해하려면 IoT Hub의 디바이스 관리 설명서에서 [디바이스 수명 주기 섹션](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

프로비저닝을 설정하려면 먼저 다음을 설정해야 합니다.
* **Azure Digital Twins 인스턴스**. [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)의 지침에 따라 Azure Digital Twins 인스턴스를 만듭니다. Azure Portal([지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))에서 인스턴스의 **_호스트 이름_** 을 수집합니다.
* **IoT Hub**. 지침은 [IoT Hub 빠른 시작](../iot-hub/quickstart-send-telemetry-cli.md)의 "IoT Hub 만들기" 섹션을 참조하세요.
* IoT Hub 데이터를 기반으로 디지털 트윈 정보를 업데이트하는 [Azure 함수](../azure-functions/functions-overview.md). [IoT 허브 데이터 수집](how-to-ingest-iot-hub-data.md)의 지침에 따라 이 Azure 함수를 만듭니다. 이 문서에서 사용할 함수 **_이름_** 을 수집합니다.

또한 이 샘플에서는 Device Provisioning Service를 사용한 프로비저닝을 포함하는 **디바이스 시뮬레이터** 를 사용합니다. 디바이스 시뮬레이터는 [Azure Digital Twins 및 IoT Hub 통합 샘플](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)에 있습니다. 샘플 링크로 이동하여 제목 아래에서 **코드 찾아보기** 단추를 선택하여 머신에서 샘플 프로젝트를 가져옵니다. 그러면 **코드** 단추와 **ZIP 다운로드** 를 선택하여 .zip. 파일로 다운로드할 수 있는 샘플용 GitHub 리포지토리로 이동합니다. 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="GitHub에 대한 digital-twins-iothub-integration 리포지토리의 스크린샷은 zip으로 다운로드하는 단계를 강조 표시합니다." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

다운로드한 폴더의 압축을 풉니다.

컴퓨터에 [Node.js](https://nodejs.org/download)가 설치되어 있어야 합니다. 디바이스 시뮬레이터는 **Node.js**, 버전 10.0.x 이상을 기반으로 합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

이 솔루션에는 Device Provisioning Service를 사용하여 Azure Digital Twins에서 디바이스를 프로비전하고 사용 중지하는 단계가 포함됩니다.

솔루션에 디바이스를 할당하기 위해 자동 온도 조절 디바이스와 DPS 간에 데이터가 전달됩니다. 그런 다음, 데이터는 Azure 함수를 통해 DPS에서 IoT Hub 및 Azure Digital Twins로 전달됩니다.

디바이스를 사용 중지하기 위해 수동 디바이스 삭제의 데이터는 IoT Hub, Event Hubs 및 Azure 함수를 통해 Azure Digital Twins로 전달됩니다.

아래 이미지는 이 아키텍처를 보여줍니다.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="데이터 흐름을 보여주는 엔드투엔드 시나리오의 디바이스 및 여러 Azure 서비스의 다이어그램." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

이 문서는 이러한 전체 아키텍처의 한 부분에 중점을 두는 두 개의 섹션으로 구분되어 있습니다.
* [Device Provisioning Service를 사용하여 디바이스 자동 프로비저닝](#auto-provision-device-using-device-provisioning-service)
* [IoT Hub 수명 주기 이벤트를 사용하여 디바이스 자동 중지](#auto-retire-device-using-iot-hub-lifecycle-events)

## <a name="auto-provision-device-using-device-provisioning-service"></a>Device Provisioning Service를 사용하여 디바이스 자동 프로비저닝

이 섹션에서는 Device Provisioning Service를 Azure Digital Twins에 연결하여 아래 경로를 통해 디바이스를 자동으로 프로비저닝합니다. 이 내용은 [앞](#solution-architecture)에 표시된 전체 아키텍처에서 발췌한 것입니다.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="프로비전 흐름 다이어그램 - 자동 온도 조절기에서 Azure Digital Twins로 데이터를 따라가는 솔루션 아키텍처 다이어그램에서 발췌." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

다음은 프로세스 흐름에 대한 설명입니다.
1. 디바이스는 DPS 엔드포인트에 연결하고 식별 정보를 전달하여 ID를 증명합니다.
2. DPS는 등록 목록에 대해 등록 ID 및 키의 유효성을 검사하여 디바이스 ID의 유효성을 검사하고 [Azure 함수](../azure-functions/functions-overview.md)를 호출하여 할당을 수행합니다.
3. Azure 함수는 Azure Digital Twins에 디바이스에 대한 새 [트윈](concepts-twins-graph.md)을 만듭니다. 트윈은 디바이스의 **등록 ID** 와 동일한 이름을 갖습니다.
4. DPS는 디바이스를 IoT Hub에 등록하고 디바이스에 필요한 트윈 상태를 채웁니다.
5. IoT 허브는 디바이스 ID 정보 및 IoT 허브 연결 정보를 디바이스에 반환합니다. 이제 디바이스에서 IoT 허브에 연결할 수 있습니다.

다음 섹션에서는 이 자동 프로비저닝 디바이스 흐름을 설정하는 단계를 안내합니다.

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service 만들기

Device Provisioning Service를 사용하여 새 디바이스를 프로비저닝하는 경우 등록 ID와 동일한 이름을 사용하여 Azure Digital Twins에서 해당 디바이스에 대한 새 트윈을 만들 수 있습니다.

IoT 디바이스를 프로비저닝하는 데 사용되는 Device Provisioning Service 인스턴스를 만듭니다. 아래의 Azure CLI 지침을 사용하거나 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](../iot-dps/quick-setup-auto-provision.md)에 따라 Azure Portal을 사용할 수 있습니다.

다음 Azure CLI 명령은 Device Provisioning Service를 만듭니다. Device Provisioning Service 이름, 리소스 그룹 및 지역을 지정해야 합니다. Device Provisioning Service를 지원하는 지역을 확인하려면 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)을 방문하세요.
Azure CLI가 [머신에 설치되어 있는 경우](/cli/azure/install-azure-cli) 이 명령은 [Cloud Shell](https://shell.azure.com) 또는 로컬로 실행할 수 있습니다.

```azurecli-interactive
az iot dps create --name <Device-Provisioning-Service-name> --resource-group <resource-group-name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Device Provisioning Service에서 사용할 함수 추가

[필수 조건](#prerequisites) 섹션에서 만든 함수 앱 프로젝트 내에서 Device Provisioning Service에 사용할 새 함수를 만듭니다. 이 함수는 [사용자 지정 할당 정책의](../iot-dps/how-to-use-custom-allocation-policies.md) Device Provisioning Service에서 새 디바이스를 프로비저닝하는 데 사용됩니다.

먼저 컴퓨터의 Visual Studio에서 함수 앱 프로젝트를 열고 다음 단계를 수행합니다.

1. 먼저 Visual Studio의 함수 앱 프로젝트에서 ‘HTTP 트리거’ 형식의 새 함수를 만듭니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)을 참조하세요.

2. 프로젝트에 새 NuGet 패키지를 추가합니다. [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). 코드에 사용된 패키지가 프로젝트에 아직 포함되지 않은 경우 프로젝트에 더 많은 패키지를 추가해야 할 수도 있습니다.

3. 새로 만든 함수 코드 파일에서 다음 코드를 붙여넣고 함수 이름을 *DpsAdtAllocationFunc.cs* 로 바꾼 후 파일을 저장합니다.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

4. *DpsAdtAllocationFunc.cs* 함수를 사용하여 프로젝트를 Azure의 함수 앱에 게시합니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

> [!IMPORTANT]
> [필수 조건 섹션](#prerequisites)에서 함수 앱을 처음 만들 때 이미 함수의 액세스 역할을 할당하고 Azure Digital Twins 인스턴스에 액세스하도록 애플리케이션 설정을 구성했을 수 있습니다. 이 작업은 전체 함수 앱에 대해 한 번만 수행해야 하므로 계속하기 전에 앱에서 작업이 완료되었는지 확인합니다. ‘앱 인증 코드 쓰기’ 문서의 [게시된 앱 구성](how-to-authenticate-client.md#configure-published-app) 섹션에서 지침을 찾을 수 있습니다.

### <a name="create-device-provisioning-enrollment"></a>Device Provisioning 등록 만들기

다음으로 **사용자 지정 할당 함수** 를 사용하여 Device Provisioning Service에서 등록을 만들어야 합니다. Device Provisioning Service 설명서에서 사용자 지정 할당 정책 문서의 [등록 만들기](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) 섹션에 있는 지침에 따라 이 작업을 수행합니다.

이 흐름을 진행하는 동안 다음 옵션을 선택하여 등록을 방금 만든 함수에 연결해야 합니다.

* **허브에 디바이스를 할당할 방법 선택**: 사용자 지정(Azure 함수 사용)
* **이 그룹을 할당할 수 있는 IoT 허브 선택**: IoT 허브 이름을 선택하거나 *새 IoT 허브 연결* 단추를 선택하고 드롭다운에서 IoT 허브를 선택합니다.

그런 다음 *새 함수 선택* 단추를 선택하여 함수 앱을 등록 그룹에 연결합니다. 그 후, 다음 값을 채웁니다.

* **구독**: Azure 구독이 자동으로 채워집니다. 올바른 구독인지 확인합니다.
* **함수 앱**: 함수 앱 이름을 선택합니다.
* **함수**: DpsAdtAllocationFunc 함수를 선택합니다.

세부 정보를 저장합니다.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Azure Portal에 있는 Customs 등록 그룹 세부 정보 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

등록을 만든 후 등록에 대한 **기본 키** 는 나중에 이 문서에 대한 디바이스 시뮬레이터를 구성하는 데 사용됩니다.

### <a name="set-up-the-device-simulator"></a>디바이스 시뮬레이터 설정

이 샘플에서는 Device Provisioning Service를 사용한 프로비저닝을 포함하는 디바이스 시뮬레이터를 사용합니다. 디바이스 시뮬레이터는 [필수 조건](#prerequisites) 섹션에서 다운로드한 [Azure Digital Twins 및 IoT Hub 통합 샘플](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)에 있습니다.

#### <a name="upload-the-model"></a>모델 업로드

디바이스 시뮬레이터는 이 ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`의 모델을 사용하는 자동 온도 조절기 형식 디바이스입니다. 디바이스에 대해 이 형식의 트윈을 만들려면 먼저 이 모델을 Azure Digital Twins에 업로드해야 합니다.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

모델에 대한 자세한 내용은 [모델 관리](how-to-manage-model.md#upload-models)를 참조하세요.

#### <a name="configure-and-run-the-simulator"></a>시뮬레이터 구성 및 실행

명령 창에서 이전에 압축을 푼 다운로드한 샘플 *Azure Digital Twins 및 IoT Hub 통합* 으로 이동한 다음 *device-simulator* 디렉터리로 이동합니다. 다음으로 다음 명령을 사용하여 프로젝트에 대한 종속성을 설치합니다.

```cmd
npm install
```

그런 다음, 디바이스 시뮬레이터 디렉터리에서 .env.template 파일을 .env라는 새 파일에 복사하고 다음 값을 수집하여 설정을 채웁니다.

* PROVISIONING_IDSCOPE: 이 값을 얻으려면 [Azure Portal](https://portal.azure.com/)에서 Device Provisioning Service로 이동한 다음 메뉴 옵션에서 *개요* 를 선택하고 필드 *ID 범위* 를 찾습니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="ID 범위 값을 강조 표시하는 디바이스 프로비저닝 개요 페이지에 대한 Azure Portal 뷰 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: 디바이스에 대한 등록 ID를 선택할 수 있습니다.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: 이전에 설정한 등록의 기본 키입니다. 이 값을 다시 얻으려면 Azure Portal에서 Device Provisioning Service로 이동하고 *등록 관리* 를 선택한 다음 이전에 만든 등록 그룹을 선택하고 *기본 키* 를 복사합니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="SAS 기본 키 값을 강조 표시하는 디바이스 프로비저닝 서비스 등록 관리 페이지에 대한 Azure Portal 뷰 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

이제 위의 값을 사용하여 .env 파일 설정을 업데이트합니다.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device-Provisioning-Service-Scope-ID>"
PROVISIONING_REGISTRATION_ID = "<Device-Registration-ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device-Provisioning-Service-enrollment-primary-SAS-key>"
```

파일을 저장하고 닫습니다.

### <a name="start-running-the-device-simulator"></a>디바이스 시뮬레이터 실행 시작

명령 창의 *device-simulator* 디렉터리에서 다음 명령을 사용하여 디바이스 시뮬레이터를 시작합니다.

```cmd
node .\adt_custom_register.js
```

디바이스를 등록하고 IoT Hub에 연결한 다음 메시지 보내기를 시작해야 합니다.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="디바이스 등록 및 메시지 보내기를 보여주는 명령 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>유효성 검사

이 문서에서 설정한 흐름의 결과로 디바이스가 Azure Digital Twins에 자동으로 등록됩니다. 다음 [Azure Digital Twins CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) 명령을 사용하여 사용자가 만든 Azure Digital Twins 인스턴스에서 디바이스 트윈을 찾습니다.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Azure Digital Twins 인스턴스에서 발견되는 디바이스 트윈이 표시되어야 합니다.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="새로 만든 트윈을 보여 주는 명령 창의 스크린샷" lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>IoT Hub 수명 주기 이벤트를 사용하여 디바이스 자동 중지

이 섹션에서는 Azure Digital Twins에 IoT Hub 수명 주기 이벤트를 연결하여 아래 경로를 통해 디바이스를 자동 중지합니다. 이 내용은 [앞](#solution-architecture)에 표시된 전체 아키텍처에서 발췌한 것입니다.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="사용 중지 디바이스 흐름의 다이어그램 - Azure Digital Twins로 디바이스를 삭제한 후 데이터를 따라 솔루션 아키텍처 다이어그램 발췌." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

다음은 프로세스 흐름에 대한 설명입니다.
1. 외부 또는 수동 프로세스는 IoT Hub에서 디바이스 삭제를 트리거합니다.
2. IoT Hub는 디바이스를 삭제하고 [이벤트 허브](../event-hubs/event-hubs-about.md)로 라우팅되는 [디바이스 수명 주기](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) 이벤트를 생성합니다.
3. Azure 함수는 Azure Digital Twins에서 디바이스 트윈을 삭제합니다.

다음 섹션에서는 이 자동 중지 디바이스 흐름을 설정하는 단계를 안내합니다.

### <a name="create-an-event-hub"></a>이벤트 허브 생성

다음으로 IoT Hub 수명 주기 이벤트를 수신하는 Azure [이벤트 허브](../event-hubs/event-hubs-about.md)를 만듭니다. 

[이벤트 허브 만들기](../event-hubs/event-hubs-create.md) 빠른 시작에 설명된 단계를 따릅니다. 이벤트 허브 이름을 *lifecycleevents* 로 지정합니다. 다음 섹션에서 IoT Hub 경로 및 Azure 함수를 설정할 때 이 이벤트 허브 이름을 사용합니다.

아래 스크린샷에서는 이벤트 허브 생성을 보여 줍니다.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="이름이 lifecycleevents인 이벤트 허브를 만드는 방법을 보여주는 Azure Portal 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>이벤트 허브에 대한 SAS 정책 만들기

다음으로 [SAS(공유 액세스 서명) 정책](../event-hubs/authorize-access-shared-access-signature.md)을 만들어 함수 앱을 사용하여 이벤트 허브를 구성해야 합니다.
이 작업을 수행하려면
1. Azure Portal에서 방금 만든 이벤트 허브로 이동하고 왼쪽의 메뉴 옵션에서 **공유 액세스 정책** 을 선택합니다.
2. **추가** 를 선택합니다. *SAS 정책 추가* 창이 열리면 원하는 정책 이름을 입력하고 *수신 대기* 확인란을 선택합니다.
3. **만들기** 를 선택합니다.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="이벤트 허브 SAS 정책을 추가하는 방법을 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>함수 앱을 사용하여 이벤트 허브 구성

다음으로 [필수 조건](#prerequisites) 섹션에서 설정한 Azure 함수 앱을 구성하여 새 이벤트 허브를 사용합니다. 이렇게 하려면 이벤트 허브의 연결 문자열을 사용하여 함수 앱 내에 환경 변수를 설정합니다.

1. 방금 만든 정책을 열고 **연결 문자열-기본 키** 값을 복사합니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="연결 문자열-기본 키를 복사하는 방법을 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. 다음 Azure CLI 명령을 사용하여 함수 앱 설정에서 연결 문자열을 변수로 추가합니다. Azure CLI가 [머신에 설치되어 있는 경우](/cli/azure/install-azure-cli) 이 명령은 [Cloud Shell](https://shell.azure.com) 또는 로컬로 실행할 수 있습니다.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event-Hubs-SAS-connection-string-Listen>" --resource-group <resource-group> --name <your-function-app-name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>IoT Hub 수명 주기 이벤트를 사용하여 사용 중지하는 함수 추가

[필수 조건](#prerequisites) 섹션에서 만든 함수 앱 프로젝트 내에서 IoT Hub 수명 주기 이벤트를 사용하여 기존 디바이스를 사용 중지하는 새 함수를 만듭니다.

수명 주기 이벤트에 대한 자세한 내용은 [IoT Hub 비-원격 분석 이벤트](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)를 참조하세요. Azure Functions와 함께 Event Hubs를 사용하는 방법에 대한 자세한 내용은 [Azure Functions에 대한 Azure Event Hubs 트리거](../azure-functions/functions-bindings-event-hubs-trigger.md)를 참조하세요.

먼저 컴퓨터의 Visual Studio에서 함수 앱 프로젝트를 열고 다음 단계를 수행합니다.

1. 먼저 Visual Studio의 함수 앱 프로젝트에서 ‘이벤트 허브 트리거’ 형식의 새 함수를 만듭니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)을 참조하세요.

2. 프로젝트에 새 NuGet 패키지를 추가합니다. [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). 코드에 사용된 패키지가 프로젝트에 아직 포함되지 않은 경우 프로젝트에 더 많은 패키지를 추가해야 할 수도 있습니다.

3. 새로 만든 함수 코드 파일에서 다음 코드를 붙여넣고 함수 이름을 *DeleteDeviceInTwinFunc.cs* 로 바꾼 후 파일을 저장합니다.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

4. *DeleteDeviceInTwinFunc.cs* 함수가 있는 프로젝트를 Azure의 함수 앱에 게시합니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

> [!IMPORTANT]
> [필수 조건 섹션](#prerequisites)에서 함수 앱을 처음 만들 때 이미 함수의 액세스 역할을 할당하고 Azure Digital Twins 인스턴스에 액세스하도록 애플리케이션 설정을 구성했을 수 있습니다. 이 작업은 전체 함수 앱에 대해 한 번만 수행해야 하므로 계속하기 전에 앱에서 작업이 완료되었는지 확인합니다. ‘앱 인증 코드 쓰기’ 문서의 [게시된 앱 구성](how-to-authenticate-client.md#configure-published-app) 섹션에서 지침을 찾을 수 있습니다.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>수명 주기 이벤트에 대한 IoT Hub 경로 만들기

이제 디바이스 수명 주기 이벤트를 라우팅하기 위해 IoT Hub 경로를 설정합니다. 이 경우에는 `if (opType == "deleteDeviceIdentity")`에 의해 식별되는 디바이스 삭제 이벤트를 특별히 수신 대기합니다. 그러면 디지털 트윈 항목의 삭제를 트리거하고 디바이스와 디지털 트윈의 사용 중지를 완료합니다.

먼저 IoT 허브에서 이벤트 허브 엔드포인트를 만들어야 합니다. 그런 다음 IoT 허브의 경로를 추가하여 이 이벤트 허브 엔드포인트에 수명 주기 이벤트를 보냅니다.
이벤트 허브 엔드포인트를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 [필수 조건](#prerequisites) 섹션에서 만든 IoT 허브로 이동하고 왼쪽의 메뉴 옵션에서 **메시지 라우팅** 을 선택합니다.
2. **사용자 지정 엔드포인트** 탭을 선택합니다.
3. **+ 추가** 를 선택하고 **이벤트 허브** 를 선택하여 이벤트 허브 형식 엔드포인트를 추가합니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="이벤트 허브 사용자 지정 엔드포인트를 추가하는 방법을 보여주는 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. *이벤트 허브 엔드포인트 추가* 창이 열리면 다음 값을 선택합니다.
    * **엔드포인트 이름**: 엔드포인트 이름을 선택합니다.
    * **이벤트 허브 네임스페이스**: 드롭다운 목록에서 이벤트 허브 네임스페이스를 선택합니다.
    * **이벤트 허브 인스턴스**: 이전 단계에서 만든 이벤트 허브 이름을 선택합니다.
5. **만들기** 를 선택합니다. 다음 단계에서 경로를 추가하려면 이 창을 열어 둡니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="이벤트 허브 엔드포인트를 추가하는 방법을 보여주는 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

다음으로, 삭제 이벤트를 보내는 라우팅 쿼리를 사용하여 위의 단계에서 만든 엔드포인트에 연결하는 경로를 추가합니다. 다음 단계에 따라 경로를 만듭니다.

1. *경로* 탭으로 이동하고 **추가** 를 선택하여 경로를 추가합니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="이벤트를 보낼 경로를 추가하는 방법을 보여주는 Visual Studio 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. *경로 추가* 페이지가 열리면 다음 값을 선택합니다.

   * **이름**: 경로의 이름을 선택합니다. 
   * **엔드포인트**: 드롭다운에서 이전에 만든 이벤트 허브 엔드포인트를 선택합니다.
   * **데이터 원본**: *디바이스 수명 주기 이벤트* 를 선택합니다.
   * **라우팅 쿼리**: `opType='deleteDeviceIdentity'`를 입력합니다. 이 쿼리는 삭제 이벤트만 전송하도록 디바이스 수명 주기 이벤트를 제한합니다.

3. **저장** 을 선택합니다.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="수명 주기 이벤트를 보내는 경로를 추가하는 방법을 보여주는 Azure Portal 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

이 흐름이 완료되면 디바이스 엔드투엔드 사용 중지가 설정된 것입니다.

### <a name="validate"></a>유효성 검사

사용 중지 프로세스를 트리거하려면 IoT Hub에서 디바이스를 수동으로 삭제해야 합니다.

[Azure CLI 명령](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete)을 사용하여 또는 Azure Portal에서 이 작업을 수행할 수 있습니다. Azure Portal에서 디바이스를 삭제하려면 다음 단계를 수행합니다.

1. IoT 허브로 이동하고 왼쪽의 메뉴 옵션에서 **IoT 디바이스** 를 선택합니다. 
2. [이 문서의 전반부](#auto-provision-device-using-device-provisioning-service)에서 선택한 디바이스 등록 ID를 가진 디바이스가 표시됩니다. 또는 Azure Digital Twins에서 트윈이 있는 한 다른 모든 디바이스를 선택하여 삭제할 수 있으므로 디바이스를 삭제한 후 트윈이 자동으로 삭제되는지 확인할 수 있습니다.
3. 디바이스를 선택하고 **삭제** 를 선택합니다.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="IoT 디바이스에서 디바이스 트윈을 삭제하는 방법을 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Azure Digital Twins에 반영된 변경 내용을 확인하는 데 몇 분 정도 걸릴 수 있습니다.

다음 [Azure Digital Twins CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) 명령을 사용하여 Azure Digital Twins 인스턴스에서 디바이스 트윈이 삭제된 것을 확인합니다.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

디바이스 트윈이 더 이상 Azure Digital Twins 인스턴스에서 찾을 수 없어야 합니다.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="트윈을 더 이상 찾을 수 없음을 보여주는 명령 창의 스크린샷." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 더 이상 필요하지 않으면 다음 단계에 따라 삭제합니다.

Azure Cloud Shell 또는 로컬 Azure CLI를 사용하면 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 명령으로 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 그러면 Azure Digital Twins 인스턴스, IoT 허브와 허브 디바이스 등록, 이벤트 그리드 토픽과 관련 구독, 이벤트 허브 네임스페이스 및 스토리지와 같은 관련 리소스를 포함한 두 Azure Functions 앱을 비롯한 리소스 그룹이 제거됩니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

그런 다음 로컬 컴퓨터에서 다운로드한 프로젝트 샘플 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

디바이스에 대해 생성된 디지털 트윈은 Azure Digital Twins에 플랫 계층으로 저장되지만 모델 정보 및 조직에 대한 다중 수준 계층 구조를 사용하여 보강할 수 있습니다. 이 개념에 대한 자세한 내용은 다음을 참조하세요.

* [Digital Twins 및 쌍 그래프](concepts-twins-graph.md)

Azure 함수에서 HTTP 요청을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Functions에 대한 Azure Http 요청 트리거](../azure-functions/functions-bindings-http-webhook-trigger.md)

Azure Digital Twins에 이미 저장된 모델 및 그래프 데이터를 사용하여 이 정보를 자동으로 제공하는 사용자 지정 논리를 작성할 수 있습니다. 트윈 그래프에서 정보를 관리, 업그레이드 및 검색하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Digital Twin 관리](how-to-manage-twin.md)
* [쌍 그래프 쿼리](how-to-query-graph.md)