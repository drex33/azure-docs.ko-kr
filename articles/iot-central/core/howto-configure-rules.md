---
title: Azure IoT Central에서 규칙 및 작업 구성 | Microsoft Docs
description: 이 방법 문서에서는 Azure IoT Central 애플리케이션에서 원격 분석 기반 규칙 및 작업을 구성하는 방법을 보여 줍니다.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5d36c303f1ebc23b317ccd5dcb08d65f5ceaf30b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567153"
---
# <a name="configure-rules"></a>규칙 구성

IoT Central 규칙은 연결된 디바이스에서 능동적으로 모니터링되는 이벤트를 트리거하는 사용자 지정 가능한 응답 도구로 기능합니다. 이어지는 섹션에서는 규칙이 평가되는 방법을 설명합니다. 규칙이 트리거될 때 발생하는 하나 이상의 작업을 정의할 수 있습니다. 이 문서에서는 이메일, 웹후크 및 Azure Monitor 작업 그룹에 대해 설명합니다. 다른 작업 유형에 대해 알아보려면 [워크플로를 사용하여 Azure IoT Central 애플리케이션을 다른 클라우드 서비스와 통합](howto-configure-rules-advanced.md)을 참조하세요.

## <a name="select-target-devices"></a>대상 디바이스 선택

대상 디바이스 섹션을 사용하여 이 규칙을 적용할 디바이스 유형을 선택합니다. 필터를 사용하여 포함해야 하는 디바이스를 세부적으로 조정할 수 있습니다. 필터는 디바이스 템플릿의 속성을 사용하여 디바이스 세트를 필터링합니다. 필터 자체적으로는 작업을 트리거하지 않습니다. 다음 스크린샷에서 대상으로 지정되는 디바이스는 템플릿 유형 **Refrigerator**(냉장고)입니다. 이 필터는 **Manufactured State**(제조됨 상태) 속성이 **Washington**(워싱턴)과 일치하는 경우에만 규칙에 **Refrigerators**(냉장고)를 포함해야 한다고 명시하고 있습니다.

:::image type="content" source="media/howto-configure-rules/filters.png" alt-text="조건 정의를 보여주는 스크린샷":::

## <a name="use-multiple-conditions"></a>여러 조건 사용

조건은 규칙이 트리거되는 상황을 가리킵니다. 규칙에 여러 조건을 추가하고 모든 조건이 true일 때 또는 조건 중 하나라도 true일 때 규칙이 트리거되어야 하는지 여부를 지정할 수 있습니다.  

다음 스크린샷에서 조건은 온도가 70&deg;F보다 크고 습도가 10보다 작은지를 확인합니다. 이러한 문중 하나라도 true이면 규칙이 true로 평가되고 작업이 트리거됩니다.

:::image type="content" source="media/howto-configure-rules/conditions.png" alt-text="스크린샷은 온도 및 습도에 대해 지정된 조건이 있는 냉동 모니터를 보여줍니다.":::

> [!NOTE]
> 현재는 원격 분석 조건만 지원됩니다.  

### <a name="use-a-cloud-property-in-a-value-field"></a>값 필드에 클라우드 속성 사용

디바이스 템플릿에서 조건의 **값** 필드에 있는 클라우드 속성을 참조할 수 있습니다. 클라우드 속성 및 원격 분석 값은 형식이 동일해야 합니다. 예를 들어 **온도** 가 double형이라면 **값** 드롭다운에 double형의 클라우드 속성만 옵션으로 표시됩니다.

이벤트 유형 원격 분석 값을 선택할 경우 **값** 드롭다운에는 **Any**(모두) 옵션이 포함됩니다. **Any**(모두) 옵션은 애플리케이션이 해당 형식의 이벤트를 받을 때 (페이로드와 관계없이) 규칙이 실행됨을 의미합니다.

## <a name="use-aggregate-windowing"></a>집계 창 사용

시간 집계를 지정하여 기간을 기반으로 규칙을 트리거할 수 있습니다. 규칙 조건은 원격 분석 데이터의 집계 기간을 연속 창으로 평가합니다. 규칙에 속성 필터가 있는 경우 해당 필터는 기간의 끝에 적용됩니다. 아래 스크린샷에서 시간 창은 5분입니다. 규칙은 5분에 한 번씩 지난 5분 동안의 원격 분석 데이터를 평가합니다. 데이터는 해당 데이터가 해당 창에 있을 때만 평가됩니다.

:::image type="content" source="media/howto-configure-rules/tumbling-window.png" alt-text="연속 창을 정의하는 방법을 보여주는 다이어그램":::

## <a name="create-an-email-action"></a>이메일 작업 만들기

이메일 작업을 만드는 경우 이메일 주소가 애플리케이션의 **사용자 ID** 여야 하며, 사용자는 애플리케이션에 한 번 이상 로그인했어야 합니다. 이메일에 포함할 메모를 지정할 수도 있습니다. IoT Central은 규칙이 트리거될 때 이메일이 어떻게 표시되는지에 대한 예를 보여줍니다.

:::image type="content" source="media/howto-configure-rules/email-action.png" alt-text="규칙에 대한 이메일 작업을 보여주는 스크린샷":::

## <a name="create-a-webhook-action"></a>웹후크 작업 만들기

웹후크를 사용하면 원격 모니터링 및 알림을 위해 IoT Central 앱을 다른 애플리케이션 및 서비스에 연결할 수 있습니다. 웹후크는 IoT Central 앱에서 규칙이 트리거될 때마다 연결하는 다른 애플리케이션 및 서비스에 자동으로 알립니다. IoT Central 앱은 규칙이 트리거될 때마다 다른 애플리케이션의 HTTP 엔드포인트에 POST 요청을 보냅니다. 페이로드는 디바이스 세부 정보 및 규칙 트리거 세부 정보를 포함합니다.

이 예제에서는 *RequestBin* 에 연결하여 규칙 실행 시 알림이 표시되도록 합니다.

1. [RequestBin](https://requestbin.net/)을 엽니다.

1. 새 RequestBin을 만들고 **Bin URL** 을 복사합니다.

1. 다음과 같이 규칙에 작업을 추가합니다.

    :::image type="content" source="media/howto-configure-rules/webhook-create.png" alt-text="웹후크 만들기 화면을 보여주는 스크린샷":::

1. 웹후크 작업을 선택하고 표시 이름을 입력한 후 RequestBin URL을 **콜백 URL** 로 붙여넣습니다.

1. 규칙을 저장합니다.

이제 규칙이 트리거되면 RequestBin에 새 요청이 표시됩니다.

### <a name="payload"></a>Payload

규칙이 트리거되면 콜백 URL에 HTTP POST 요청을 보냅니다. 요청에는 원격 분석, 디바이스, 규칙 및 애플리케이션 세부 정보가 있는 JSON 페이로드가 포함됩니다. 페이로드는 다음 JSON 코드 조각과 유사합니다.

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```

규칙에서 일정 기간 동안 집계된 원격 분석을 모니터링하는 경우 페이로드에는 다음과 같은 원격 분석 섹션이 포함됩니다.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

### <a name="data-format-change-notice"></a>데이터 형식 변경 알림

**2020년 4월 3일** 이전에 하나 이상의 웹후크를 만들고 저장한 경우에는 웹후크를 삭제한 후 새 웹후크를 만들어야 합니다. 이전 웹후크는 더 이상 사용되지 않는 페이로드 형식을 사용합니다.

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="create-an-azure-monitor-group-action"></a>Azure Monitor 그룹 작업 만들기

이 섹션에서는 [Azure Monitor](../../azure-monitor/overview.md) *작업 그룹* 을 사용하여 여러 작업을 IoT Central 규칙에 연결하는 방법을 설명합니다. 여러 규칙에 작업 그룹을 연결할 수 있습니다. [작업 그룹](../../azure-monitor/alerts/action-groups.md)은 Azure 구독 소유자가 정의한 알림 우선 설정 컬렉션입니다.

[Azure Portal](../../azure-monitor/alerts/action-groups.md) 또는 [Azure Resource Manager 템플릿](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md)에서 작업 그룹을 만들고 관리할 수 있습니다.

작업 그룹은 다음을 수행할 수 있습니다.

- 메일, SMS 등의 알림을 보내거나 음성 통화를 설정합니다.
- 웹후크 호출과 같은 작업을 실행합니다.

다음 스크린샷은 메일 및 SMS 알림을 보내고 웹후크를 호출하는 작업 그룹을 보여 줍니다.

:::image type="content" source="media/howto-configure-rules/action-group.png" alt-text="Azure Portal의 작업 그룹을 보여주는 스크린샷":::

IoT Central 규칙에서 작업 그룹을 사용하려면 작업 그룹이 IoT Central 애플리케이션과 동일한 Azure 구독에 있어야 합니다.

IoT Central의 규칙에 작업을 추가하는 경우 **Azure Monitor 작업 그룹** 을 선택합니다.

Azure 구독에서 작업 그룹을 선택합니다.

:::image type="content" source="media/howto-configure-rules/choose-action-group.png" alt-text="IoT Central 규칙의 작업 그룹을 보여주는 스크린샷":::

**저장** 을 선택합니다. 이제 규칙을 트리거할 때 실행할 작업 목록에 작업 그룹이 표시됩니다.

다음 표에서는 지원되는 작업 형식으로 전송되는 정보를 요약합니다.

| 작업 유형 | 출력 형식 |
| ----------- | -------------- |
| Email       | 표준 IoT Central 메일 템플릿 |
| sms         | Azure IoT Central 경고: ${applicationName}- “${ruleName}”이(가) ${triggerDate} ${triggerTime}에 “${deviceName}”에서 트리거됨 |
| 음성       | Azure I.O.T Central 경고: ${triggerDate} ${triggerTime}에 디바이스 “${deviceName}”에서 애플리케이션 ${applicationName}의 “${ruleName}” 규칙이 트리거됨 |
| 웹후크     | { “schemaId” : “AzureIoTCentralRuleWebhook”, “data”: {[regular webhook payload](howto-create-webhooks.md#payload)}} |

다음 텍스트는 작업 그룹의 예제 SMS 메시지입니다.

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="use-rules-with-iot-edge-modules"></a>IoT Edge 모듈에서 규칙 사용

IoT Edge 모듈에 적용되는 규칙에는 제한이 적용됩니다. 다른 모듈의 원격 분석에 적용되는 규칙은 유효한 규칙으로 평가되지 않습니다. 다음 예에서 규칙의 첫 번째 조건은 모듈 A의 온도 원격 분석에 적용됩니다. 규칙의 두 번째 조건은 모듈 B의 습도 원격 분석에 적용됩니다. 두 조건이 서로 다른 모듈에 적용되므로 잘못된 조건 집합입니다. 규칙이 유효하지 않으며 규칙을 저장하려고 하면 오류를 throw합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 규칙을 구성하는 방법을 알아보았으니 이제 Power Automate 또는 Azure Logic Apps를 사용하여 [고급 규칙을 구성](howto-configure-rules-advanced.md)하는 방법을 알아볼 수 있습니다.
