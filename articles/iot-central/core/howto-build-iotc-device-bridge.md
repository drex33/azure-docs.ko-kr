---
title: Azure IoT Central 디바이스 브리지 배포 | Microsoft Docs
description: IoT Central 디바이스 브리지를 배포하여 다른 IoT 클라우드를 IoT Central에 연결합니다. 다른 IoT 클라우드에는 Sigfox, Particle Device Cloud 및 The Things Network가 포함됩니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: a0777b0d2ee01e0547403e9f34a745a2f94fdb35
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863407"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>IoT Central 디바이스 브리지를 사용하여 다른 IoT 클라우드를 IoT Central에 연결

IoT Central 디바이스 브리지는 다른 IoT 클라우드를 고객의 IoT Central 애플리케이션에 연결하는 오픈 소스 솔루션입니다. 다른 IoT 클라우드에는 [Sigfox](https://www.sigfox.com/), [Particle Device Cloud](https://www.particle.io/) 및 [The Things Network](https://www.thethingsnetwork.org/)가 포함됩니다. 디바이스 브리지는 다른 IoT 클라우드에 연결된 디바이스의 데이터를 IoT Central 애플리케이션에 전달하는 방식으로 작동합니다. 디바이스 브리지는 데이터를 IoT Central로 전달하기만 하고, IoT Central의 명령 또는 속성 업데이트를 다시 디바이스로 전송하지는 않습니다.

디바이스 브리지를 사용하면 강력한 성능의 IoT Central을 Sigfox의 저전력 광역 네트워크에 연결된 자산 추적 디바이스, Particle Device Cloud의 공기 품질 모니터링 디바이스, The Things Network의 토양 습도 모니터링 디바이스 같은 디바이스와 결합할 수 있습니다. 데이터에 대한 규칙 및 분석과 같은 IoT Central 애플리케이션 기능을 사용하고, Power Automate 및 Azure 논리 앱에서 워크플로를 만들고, 데이터를 내보낼 수 있습니다.

디바이스 브리지 솔루션은 함께 작동하여 디바이스 메시지를 변환하고 IoT Central에 전달하는 여러 Azure 리소스를 Azure 구독에 프로비저닝합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드의 단계를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="overview"></a>개요

IoT Central 디바이스 브리지는 GitHub의 오픈 소스 솔루션입니다. 디바이스 브리지는 사용자 지정 Azure Resource Manager 템플릿을 사용하여 Azure Functions의 함수 앱을 비롯한 여러 리소스를 Azure 구독에 배포합니다.

함수 앱은 디바이스 브리지의 핵심입니다. 함수 앱은 간단한 webhook를 통해 다른 IoT 플랫폼에서 HTTP POST 요청을 수신합니다. [Azure IoT Central 디바이스 브리지](https://github.com/Azure/iotc-device-bridge) 리포지토리에는 Sigfox, Particle 및 The Things Network 클라우드를 연결하는 방법을 보여주는 예제가 포함되어 있습니다. 플랫폼에서 함수 앱에 HTTP POST 요청을 보낼 수 있는 경우 사용자 지정 IoT 클라우드에 연결하도록 이 솔루션을 확장할 수 있습니다.

함수 앱은 IoT Central에서 허용하는 형식으로 데이터를 변환한 후 디바이스 프로비저닝 서비스 및 디바이스 클라이언트 API를 사용하여 전달합니다.

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Azure Functions의 스크린샷":::

전달된 메시지의 디바이스 ID를 IoT Central 애플리케이션이 인식하면 디바이스의 원격 분석 데이터가 IoT Central에 표시됩니다. IoT Central 애플리케이션이 디바이스 ID를 인식하지 못하면 함수 앱이 디바이스 ID를 사용하여 새 디바이스를 등록하려고 시도합니다. 새 디바이스가 IoT Central 애플리케이션의 **디바이스** 페이지에 **연결되지 않은 디바이스** 로 표시됩니다. **디바이스** 페이지에서 새 디바이스를 디바이스 템플릿과 연결한 다음, 원격 분석 데이터를 볼 수 있습니다.

## <a name="deploy-the-device-bridge"></a>디바이스 브리지 배포

구독에 디바이스 브리지를 배포하려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **관리 > 디바이스 연결** 페이지로 이동합니다.

    1. **ID 범위** 를 기록해 둡니다. 디바이스 브리지를 배포할 때 이 값을 사용합니다.

    1. 동일한 페이지에서 **SAS-IoT-Devices** 등록 그룹을 엽니다. **SAS-IoT-Devices** 그룹 페이지에서 **기본 키** 를 복사합니다. 디바이스 브리지를 배포할 때 이 값을 사용합니다.

1. 아래의 **Azure에 배포** 단추를 사용하여 함수 앱을 구독에 배포하는 사용자 지정 Resource Manager 템플릿을 엽니다. 이전 단계의 **ID 범위** 및 **기본 키** 를 사용합니다.

    [![Azure에 배포](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

배포가 완료되면 함수에 필요한 NPM 패키지를 설치해야 합니다.

1. Azure Portal에서 구독에 배포된 함수 앱을 엽니다. **개발 도구** > **콘솔** 로 이동합니다. 이 콘솔에서 다음 명령을 실행하여 패키지를 설치합니다.

    ```bash
    cd IoTCIntegration
    npm install
    ```

    이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 경고 메시지는 무시해도 됩니다.

1. 패키지 설치가 완료되면 함수 앱의 **개요** 페이지에서 **다시 시작** 을 선택합니다.

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="다시 시작 스크린샷":::

1. 이제 함수를 사용할 준비가 되었습니다. 외부 시스템은 HTTP POST 요청을 사용하여 디바이스 브리지를 통해 디바이스 데이터를 IoT Central 애플리케이션으로 보낼 수 있습니다. 함수 URL을 얻으려면 **함수 > IoTCIntegration > 코드 + 테스트 > 함수 URL 가져오기** 로 이동합니다.

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="함수 URL 가져오기의 스크린샷":::

디바이스 브리지로 전송되는 메시지 본문은 다음과 같은 형식이어야 합니다.

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

`measurements` 개체의 각 키는 IoT Central 애플리케이션의 디바이스 템플릿에 있는 원격 분석 유형의 이름과 일치해야 합니다. 이 솔루션은 메시지 본문의 인터페이스 ID를 지정하는 것을 지원하지 않습니다. 따라서 두 개의 다른 인터페이스에 같은 이름의 원격 분석 형식이 있는 경우 IoT Central 애플리케이션의 두 원격 분석 스트림에 측정값이 표시됩니다.

본문에 `timestamp` 필드를 포함하여 메시지의 UTC 날짜와 시간을 지정할 수 있습니다. 이 필드는 ISO 8601 형식이어야 합니다. 예들 들어 `2020-06-08T20:16:54.602Z`입니다. 타임스탬프를 포함하지 않으면 현재 날짜와 시간이 사용됩니다.

본문에 `modelId` 필드를 포함할 수 있습니다. 프로비저닝하는 동안 디바이스를 디바이스 템플릿과 연결하려면 이 필드를 사용합니다. 이 기능은 [V3 애플리케이션](howto-faq.yml#how-do-i-get-information-about-my-application-)에서만 지원됩니다.

`deviceId`는 영숫자, 소문자여야 하고 하이픈을 포함할 수 있습니다.

`modelId` 필드를 포함하지 않거나 IoT Central이 모델 ID를 인식하지 못하는 경우 인식할 수 없는 `deviceId`가 있는 메시지는 IoT Central에서 새로운 _연결되지 않은 디바이스_ 를 만듭니다. 운영자는 수동으로 디바이스를 올바른 디바이스 템플릿으로 마이그레이션할 수 있습니다. 자세히 알아보려면 [Azure IoT Central 애플리케이션에서 디바이스 관리 > 템플릿으로 디바이스 마이그레이션](howto-manage-devices-individually.md)을 참조하세요.

[V2 애플리케이션](howto-faq.yml#how-do-i-get-information-about-my-application-)의 **Device Explorer > 연결되지 않은 디바이스** 페이지에 새 디바이스가 표시됩니다. 디바이스에서 들어오는 원격 분석 데이터를 받기 시작하려면 **연결** 을 선택하고 디바이스 템플릿을 선택합니다.

> [!NOTE]
> 디바이스가 템플릿에 연결될 때까지 이 함수에 대한 모든 HTTP 호출은 403 오류 상태를 반환합니다.

Application Insights를 사용하여 함수 앱에 대한 로깅을 전환하려면 Azure Portal에서 함수 앱의 **모니터링 > 로그** 로 이동합니다. **Application Insights 켜기** 를 선택합니다.

## <a name="provisioned-resources"></a>프로비저닝된 리소스

Resource Manager 템플릿은 Azure 구독에서 다음 리소스를 프로비전합니다.

* 함수 앱
* App Service 계획
* 스토리지 계정
* 주요 자격 증명 모음

키 자격 증명 모음은 IoT Central 애플리케이션의 SAS 그룹 키를 저장합니다.

함수 앱은 [사용 플랜](https://azure.microsoft.com/pricing/details/functions/)에서 실행됩니다. 이 옵션은 전용 컴퓨팅 리소스를 제공하지 않지만 디바이스 브리지가 분당 수백 개의 디바이스 메시지를 처리할 수 있도록 하며, 메시지를 보내는 빈도가 낮은 소규모 디바이스 그룹에 적합합니다. 애플리케이션이 수많은 디바이스 메시지를 스트리밍하는 경우 사용 플랜을 전용 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/windows/)로 바꿉니다. 이 플랜은 서버 응답 시간이 더 빠른 전용 컴퓨팅 리소스를 제공합니다. 표준 App Service 요금제를 사용하면 이 리포지토리의 Azure에서 함수가 분당 약 1,500개의 디바이스 메시지를 처리하는 것으로 최대 성능이 관찰되었습니다. 자세히 알아보려면 [Azure Functions 호스팅 옵션](../../azure-functions/functions-scale.md)을 참조하세요.

사용 플랜 대신 전용 App Service 요금제를 사용하려면 배포하기 전에 사용자 지정 템플릿을 편집합니다. **템플릿 편집** 을 선택합니다.

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="템플릿 편집의 스크린샷":::

다음 세그먼트를

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

다음과 같이 바꿉니다.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

다음으로, `"properties": {"SiteConfig": {...}}` 아래에서 `functionapp` 리소스에 대한 구성에 `"alwaysOn": true`가 포함되도록 템플릿을 편집합니다. [alwaysOn 구성](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan)은 함수 앱이 항상 실행되도록 합니다.

## <a name="examples"></a>예제

다음 예제에서는 다양한 IoT 클라우드에 맞게 디바이스 브리지를 구성하는 방법을 간략하게 설명합니다.

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>예제 1: 디바이스 브리지를 통해 Particle 디바이스 연결

디바이스 브리지를 통해 Particle 디바이스를 IoT Central에 연결하려면 Particle 콘솔로 이동하여 새 webhook 통합을 만듭니다. **요청 형식** 을 **JSON** 으로 설정합니다.  **고급 설정** 에서 다음 사용자 지정 본문 형식을 사용합니다.

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": "{{{PARTICLE_EVENT_VALUE}}}"
  }
}
```

함수 앱의 **함수 URL** 에 붙여넣습니다. 그러면 Particle 디바이스가 IoT Central에서 연결되지 않은 디바이스로 표시됩니다. 자세한 내용은 [Here's how to integrate your Particle-powered projects with Azure IoT Central](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) 블로그 게시물을 참조하세요.

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>예제 2: 디바이스 브리지를 통해 Sigfox 디바이스 연결

일부 플랫폼에서는 webhook를 통해 전송되는 디바이스 메시지의 형식을 지정할 수 없습니다. 이러한 시스템의 경우 디바이스 브리지에서 메시지를 처리하려면 먼저 메시지 페이로드를 필요한 본문 형식으로 변환해야 합니다. 변환은 디바이스 브리지를 실행하는 동일한 함수에서 수행할 수 있습니다.

이 섹션에서는 Sigfox webhook 통합의 페이로드를 디바이스 브리지에 필요한 본문 형식으로 변환하는 방법을 보여줍니다. Sigfox 클라우드는 디바이스 데이터를 16진수 문자열 형식으로 전송합니다. 편의를 위해 디바이스 브리지에는 이 형식에 대한 변환 함수가 포함되어 있습니다. 이 함수는 Sigfox 디바이스 페이로드에서 가능한 필드 형식으로 8, 16, 32, 64비트의 `int` 및 `uint`, 32비트 또는 64비트의 `float`, little-endian 및 big-endian을 허용합니다. Sigfox webhook 통합에서 메시지를 처리하려면 함수 앱에서 _IoTCIntegration/index.js_ 파일을 다음과 같이 변경합니다.

메시지 페이로드를 변환하려면 줄 21에서 `handleMessage` 호출 앞에 다음 코드를 추가하고 `payloadDefinition`을 Sigfox 페이로드 정의로 바꿉니다.

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Sigfox 디바이스는 `204` 응답 코드가 필요합니다. 줄 21에서 `handleMessage` 호출 뒤에 다음 코드를 추가합니다.

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>예제 3: 디바이스 브리지를 통해 The Things Network에서 디바이스 연결

The Things Network 디바이스를 IoT Central에 연결하려면 다음을 수행합니다.

* The Things Network: **애플리케이션 > 통합 > 통합 추가 > HTTP 통합** 에서 새 HTTP 통합을 애플리케이션에 추가합니다.
* 애플리케이션의 **애플리케이션 > 페이로드 함수 > 디코더** 에서, 메시지를 함수로 보내기 전에 디바이스 메시지의 페이로드를 JSON으로 자동 변환하는 디코더 함수를 포함시킵니다.

다음 샘플은 이진 데이터의 일반적인 숫자 형식을 디코딩하는 데 사용할 수 있는 JavaScript 디코더 함수를 보여줍니다.

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

통합을 정의한 후에는 함수 앱의 *IoTCIntegration/index.js* 파일에서 21번 줄의 `handleMessage` 호출 앞에 다음 코드를 추가합니다. 이 코드는 HTTP 통합의 본문을 필요한 형식으로 변환합니다.

```javascript
req.body = {
  device: {
    deviceId: req.body.end_device_ids.device_id.toLowerCase()
  },
  measurements: req.body.uplink_message.decoded_payload
};
```

> [!NOTE]
> 이전 코드 조각은 사용자에 게 친숙 한 장치 ID를 사용 합니다. 네트워크 메시지에는를 사용 하 여 액세스할 수 있는 기술 ID도 포함 됩니다 `req.body.dev_eui.toLowerCase()` . 자세한 내용은 [네트워크 데이터 형식 항목](https://www.thethingsindustries.com/docs/reference/data-formats/)을 참조 하세요.

## <a name="limitations"></a>제한 사항

디바이스 브리지는 IoT Central로 메시지를 전달하기만 하고, 메시지를 다시 디바이스로 전송하지는 않습니다. 이러한 이유로 이 디바이스 브리지를 통해 IoT Central에 연결하는 디바이스에서는 속성 및 명령이 작동하지 않습니다. 디바이스 쌍 작업은 지원되지 않으므로 디바이스 브리지를 통해 디바이스 속성을 업데이트할 수 없습니다. 이러한 기능을 사용하려면 디바이스에서 [Azure IoT 디바이스 SDK](../../iot-hub/iot-hub-devguide-sdks.md) 중 하나를 사용하여 IoT Central에 직접 연결해야 합니다.

## <a name="next-steps"></a>다음 단계
IoT Central 디바이스 브리지를 배포하는 방법을 알아보았으므로, 다음과 같은 권장 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [디바이스 관리](howto-manage-devices-individually.md)
