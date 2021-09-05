---
title: Azure Event Hubs의 처리량 단위 자동 스케일 업
description: 처리량 단위(표준 계층)를 자동으로 스케일 업하려면 네임스페이스에서 자동 확장을 사용합니다.
ms.topic: article
ms.date: 05/26/2021
ms.openlocfilehash: 05ea9a76ec5ece9bf522679c85f3671d600d41e8
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531054"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units-standard-tier"></a>Azure Event Hubs 처리량 단위(표준 계층) 자동 스케일 업 
Azure Event Hubs는 확장성이 뛰어난 데이터 스트리밍 플랫폼입니다. 따라서 서비스를 사용하기 시작한 후에 Event Hubs 사용량이 증가합니다. 해당 사용량에 맞게 Event Hubs를 스케일링하고 더 큰 전송 속도를 처리하도록 미리 정해진 [TU(처리량 단위)](event-hubs-scalability.md#throughput-units)를 늘려야 합니다. Event Hubs의 **자동 확장** 기능은 사용량 요구에 맞게 TU 수를 늘려 자동으로 스케일 업합니다. TU를 늘리면 다음 경우에 제한 시나리오를 방지합니다.

* 데이터 수신 속도가 설정된 TU를 초과하는 경우 
* 데이터 송신 요청 속도가 설정된 TU를 초과하는 경우

ServerBusy 오류로 인한 요청 실패 없이 부하가 최소 임계값을 초과하면 Event Hubs 서비스는 처리량을 높입니다.

> [!NOTE]
> **프리미엄** 계층에 대한 자세한 내용은 [Event Hubs 프리미엄](event-hubs-premium-overview.md)을 참조하세요.

## <a name="how-auto-inflate-works-in-standard-tier"></a>표준 계층에서 자동 확장 작동 방법
Event Hubs 트래픽은 TU(표준 계층)에 의해 제어됩니다. TU당 수신 및 송신 속도와 같은 제한의 경우 [Event Hubs 할당량 및 제한](event-hubs-quotas.md)을 참조하세요. 자동 확장 기능을 사용하면 필요한 최소 TU를 선택하여 작은 규모부터 시작할 수 있습니다. 이 기능은 트래픽 증가에 따라 필요한 TU의 최대 제한까지 자동으로 스케일링합니다. 자동 확장의 이점은 다음과 같습니다.

- 작은 규모부터 시작하여 사용량 증가에 따라 확장하는 효율적인 크기 조정 메커니즘입니다.
- 제한 문제 없이 지정된 상한까지 자동으로 크기 조정합니다.
- 크기 조정의 정도나 시점을 제어하므로 크기 조정을 더 잘 제어할 수 있습니다.

> [!NOTE]
> 자동 확장은 수신 또는 송신 속도가 제한 아래로 떨어질 때 TU 수를 *자동* 으로 스케일 다운하지 않습니다. 

 ## <a name="enable-auto-inflate-on-a-namespace"></a>네임스페이스에서 자동 확장 사용
[Azure Portal](https://portal.azure.com) 또는 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate)을 사용하여 표준 계층 Event Hubs 네임스페이스에서 자동 확장을 사용하거나 사용하지 않을 수 있습니다.

프리미엄 Event Hubs 네임스페이스의 경우 기능을 자동으로 사용할 수 있습니다. 이를 사용하지 않을 수 없습니다. 

> [!NOTE]
> 기본 계층 Event Hubs 네임스페이스는 자동 확장을 지원하지 않습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
Azure Portal에서 표준 Event Hubs 네임스페이스를 만들 때 또는 네임스페이스를 만든 후 이 기능을 사용할 수 있습니다. 네임스페이스에 대해 TU를 설정하고 TU의 최대 제한을 지정할 수도 있습니다. 

**Event Hubs 네임스페이스를 만들 때** 자동 확장 기능을 사용할 수 있습니다. 다음 이미지에서는 표준 계층 네임스페이스에 대해 자동 확장 기능을 사용하는 방법과 시작할 TU와 최대 TU 수를 구성하는 방법을 보여 줍니다. 

:::image type="content" source="./media/event-hubs-auto-inflate/event-hubs-auto-inflate.png" alt-text="표준 계층 네임스페이스에 대해 이벤트 허브를 만들 때 자동 확장을 사용하는 스크린샷":::

이 옵션을 사용하면 TU를 통해 작은 규모부터 시작해서 사용량 요구가 증가함에 따라 스케일 업할 수 있습니다. 확장 상한은 가격 책정에 즉시 영향을 미치지 않으며 가격 책정은 시간당 사용된 TU 수에 따라 다릅니다.

자동 확장 기능을 사용하고 기존 설정을 수정하려면 다음 단계를 수행합니다.

1. **Event Hubs 네임스페이스** 페이지에서 왼쪽 메뉴의 **설정** 아래에서 **스케일링** 을 선택합니다.
2. **크기 조정 설정** 페이지에서 **사용** 확인란을 선택합니다(자동 크기 조정 기능을 사용하도록 설정하지 않은 경우).

    :::image type="content" source="./media/event-hubs-auto-inflate/scale-settings.png" alt-text="기존의 표준 네임스페이스에 대해 자동 확장을 사용하는 스크린샷":::
3. **최대** 처리량 단위 수를 입력하거나 스크롤 막대를 사용하여 값을 설정합니다.
4. (선택 사항) 이 페이지 맨 위에서 **최소** 처리량 단위 수를 업데이트합니다.

> [!NOTE]
> 처리량 단위를 늘리는 자동 팽창 구성을 사용하면 Event Hubs 서비스는 처리량이 증가한 이유 및 시기에 대한 정보를 제공하는 진단 로그를 내보냅니다. 이벤트 허브에 진단 로깅을 사용하려면 Azure Portal에서 이벤트 허브 페이지 왼쪽 메뉴에서 **진단 설정** 을 선택합니다. 자세한 내용은 [Azure 이벤트 허브에 대한 진단 로그 설정](monitor-event-hubs-reference.md#resource-logs)을 참조하세요.


## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

Azure Resource Manager 템플릿을 배포하는 동안 자동 확장을 사용하도록 설정할 수 있습니다. 예를 들어 `isAutoInflateEnabled` 속성을 **true** 로 설정하고 `maximumThroughputUnits`를 10으로 설정합니다. 예를 들면 다음과 같습니다.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

전체 템플릿은 GitHub에서 [Event Hubs 네임스페이스 만들기 및 확장 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.eventhub/eventhubs-create-namespace-and-enable-inflate) 템플릿을 참조하세요.


## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](./event-hubs-about.md)
