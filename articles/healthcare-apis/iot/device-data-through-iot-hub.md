---
title: Azure IoT Hub를 통해 디바이스 데이터 수신 - Azure Healthcare API
description: 이 자습서에서는 IoT 커넥터를 통해 IoT Hub FHIR 서비스로 디바이스 데이터 라우팅을 사용하도록 설정하는 방법을 알아봅니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: fe478235897b855d079241192108f5d5ebd70fe0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992982"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>자습서: Azure IoT Hub를 통해 디바이스 데이터 수신

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

IoT 커넥터는 IoMT(의료 사물 인터넷) 디바이스에서 전자 의료 기록 교환(FHIR&#174;) 서비스로 데이터를 수집할 수 있는 기능을 제공합니다. 또한 IoT 커넥터는 Azure IoT Hub를 통해 프로비저닝되고 관리되는 디바이스에서도 작동할 수 있습니다. 이 자습서에서는 디바이스 데이터를 Azure IoT Hub에서 IoT 커넥터로 연결하고 라우팅하는 절차를 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 하나 이상의 IoT 커넥터가 있는 FHIR 서비스 리소스 - [Azure Portal 사용하여 IoT 커넥터 배포](deploy-iot-connector-in-azure.md)
- 실제 또는 시뮬레이션된 디바이스와 연결된 Azure IoT Hub 리소스 - [Azure Portal을 사용하여 IoT 허브 만들기](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> Azure IoT Hub 시뮬레이션된 디바이스 애플리케이션을 사용하는 경우 지원되는 다양한 언어 및 시스템 중에서 원하는 애플리케이션을 자유롭게 선택할 수 있습니다.

## <a name="get-connection-string-for-iot-connector"></a>IoT 커넥터에 대한 연결 문자열을 가져옵니다.

Azure IoT IoT 커넥터와 안전하게 연결하려면 허브에 연결 문자열이 필요합니다. 연결 문자열 생성에 설명된 대로 IoT 커넥터에 대한 새 [연결 문자열을](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string)만듭니다. 다음 단계에서 사용할 이 연결 문자열을 금고.

IoT 커넥터는 내부적으로 Azure Event Hub 인스턴스를 사용하여 디바이스 메시지를 받습니다. 위에서 만든 연결 문자열은 기본적으로 이 기본 Event Hub에 대한 연결 문자열입니다.

## <a name="connect-azure-iot-hub-with-iot-connector"></a>IoT 커넥터를 커넥트 Azure IoT Hub

Azure IoT Hub는 디바이스 데이터를 Event Hub, 스토리지 계정 및 Service Bus와 같은 다양한 Azure 서비스로 보내는 기능을 제공하는 [메시지 라우팅](../../iot-hub/iot-hub-devguide-messages-d2c.md)이라는 기능을 지원합니다. IoT 커넥터는 이 기능을 사용하여 Azure IoT Hub에서 해당 Event Hub 엔드포인트로 디바이스 데이터를 연결하고 보냅니다.

> [!NOTE] 
> 현재는 IoT 커넥터의 Event Hub가 고객 구독에서 호스트되지 않으므로 powerShell 또는 CLI 명령만 사용하여 [메시지 라우팅을 만들](../../iot-hub/tutorial-routing.md) 수 있으므로 Azure Portal 통해 표시되지 않습니다. 그러나 PowerShell 또는 CLI를 사용하여 메시지 경로 개체를 추가하면 Azure Portal에서 해당 개체가 표시되고 여기서 관리할 수 있습니다.

메시지 라우팅을 설정하는 작업은 두 단계로 구성됩니다.

### <a name="add-an-endpoint"></a>엔드포인트 추가

이 단계에서는 IoT Hub에서 데이터를 라우팅하는 엔드포인트를 정의합니다. 이 엔드포인트는 기본 설정에 따라 [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 명령 또는 [az iot hub routing-endpoint create](/cli/azure/iot/hub/route#az_iot_hub_route_create) CLI 명령을 사용하여 만듭니다.

다음은 엔드포인트를 만드는 명령과 함께 사용할 매개 변수의 목록입니다.

|PowerShell 매개 변수|CLI 매개 변수|Description|
|---|---|---|
|ResourceGroupName|resource-group|IoT Hub 리소스의 리소스 그룹 이름입니다.|
|Name|hub-name|IoT Hub 리소스의 이름입니다.|
|EndpointName|endpoint-name|만드는 엔드포인트에 할당하려는 이름을 사용합니다.|
|EndpointType|endpoint-type|IoT Hub에서 연결해야 하는 엔드포인트의 유형입니다. 리터럴 값으로 PowerShell의 경우 "EventHub"를 사용하고, CLI의 경우 "eventhub"를 사용합니다.|
|EndpointResourceGroup|endpoint-resource-group|IoT 커넥터의 FHIR 서비스 리소스에 대한 리소스 그룹 이름입니다. FHIR 서비스의 개요 페이지에서 이 값을 얻을 수 있습니다.|
|EndpointSubscriptionID|endpoint-subscription-id|IoT 커넥터의 FHIR 서비스 리소스에 대한 구독 ID입니다. FHIR 서비스의 개요 페이지에서 이 값을 얻을 수 있습니다.|
|ConnectionString|connection-string|IoT 커넥터에 대한 연결 문자열입니다. 이전 단계에서 얻은 값을 사용합니다.|

### <a name="add-a-message-route"></a>메시지 경로 추가
이 단계에서는 위에서 만든 엔드포인트를 사용하여 메시지 경로를 정의합니다. 이 경로는 기본 설정에 따라 [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 명령 또는 [az iot hub route create](/cli/azure/iot/hub/route) CLI 명령을 사용하여 만듭니다.

다음은 메시지 경로를 추가하는 명령과 함께 사용할 매개 변수의 목록입니다.

|PowerShell 매개 변수|CLI 매개 변수|Description|
|---|---|---|
|ResourceGroupName|g|IoT Hub 리소스의 리소스 그룹 이름입니다.|
|Name|hub-name|IoT Hub 리소스의 이름입니다.|
|EndpointName|endpoint-name|위에서 만든 엔드포인트의 이름입니다.|
|RouteName|route-name|만드는 메시지 경로에 할당하려는 이름입니다.|
|원본|source-type|엔드포인트로 보낼 데이터의 형식입니다. 리터럴 값으로 PowerShell의 경우 "DeviceMessages"를 사용하고, CLI의 경우 "devicemessages"를 사용합니다.|

## <a name="send-device-message-to-azure-iot-hub"></a>Azure IoT Hub에 디바이스 메시지 보내기

실제 또는 시뮬레이션된 디바이스를 사용하여 아래에 표시된 심박수 메시지 샘플을 Azure IoT Hub에 보냅니다. 이 메시지는 IoT 커넥터로 라우팅되며, 여기서 메시지는 FHIR 관찰 리소스로 변환되고 FHIR 서비스에 저장됩니다.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> IoT 커넥터로 구성된 [매핑 템플릿을](how-to-use-fhir-mapping-iot.md) 준수하는 디바이스 메시지를 보내야 합니다.

## <a name="view-device-data-in-fhir-service"></a>FHIR 서비스에서 디바이스 데이터 보기

Postman을 사용하여 FHIR 서비스에서 IoT 커넥터가 만든 FHIR 관찰 리소스를 볼 수 있습니다. 자세한 내용은 [Postman을 사용하여 FHIR 서비스에 액세스하고](./../use-postman.md) `GET` 위의 샘플 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 메시지에서 제출된 심박수 값이 있는 Observation FHIR 리소스를 확인하도록 요청합니다.

> [!TIP]
> 사용자에게 FHIR 서비스 데이터 평면에 대한 적절한 액세스 권한이 있는지 확인합니다. [Azure RBAC(Azure 역할 기반 액세스 제어)](../azure-api-for-fhir/configure-azure-rbac.md)를 사용하여 필요한 데이터 평면 역할을 할당합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스 데이터를 IoT 커넥터로 라우팅하도록 Azure IoT Hub를 설정합니다. 다음 단계에서 선택하여 IoT 커넥터에 대해 자세히 알아봅니다.

IoT 커넥터 내의 다양한 데이터 흐름 단계를 이해합니다.

>[!div class="nextstepaction"]
>[IoT 커넥터 데이터 흐름](iot-data-flow.md)

(FHIR&#174;)는 HL7의 등록 상표이며 HL7의 사용 권한으로 사용됩니다.