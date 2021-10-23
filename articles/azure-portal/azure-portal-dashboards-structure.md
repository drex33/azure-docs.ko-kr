---
title: Azure 대시보드의 구조
description: 예제 대시보드를 사용 하 여 Azure 대시보드의 JSON 구조를 안내 합니다. 리소스 속성에 대한 참조를 포함합니다.
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: 4f005d6b232a7bba15d55055d49ac1c7adf49866
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239707"
---
# <a name="the-structure-of-azure-dashboards"></a>Azure 대시보드의 구조

이 문서는 다음 대시보드 예제를 사용하여 Azure 대시보드 구조를 안내합니다.

:::image type="content" source="media/azure-portal-dashboards-structure/sample-dashboard.png" alt-text="Azure Portal의 샘플 대시보드 스크린샷":::

공유 [Azure 대시보드는 리소스](../azure-resource-manager/management/overview.md)이므로 이 대시보드를 JSON으로 표현할 수 있습니다.  다음 JSON은 시각화된 위의 대시보드를 나타냅니다.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>일반적인 리소스 속성

JSON의 관련 섹션을 분석해 보겠습니다.  최상위 속성, __id__, __name__, __type__, __location__ 및 __tags__ 속성은 모든 Azure 리소스 종류 간에 공유됩니다. 즉, 대시보드의 콘텐츠와 관련이 없습니다.

### <a name="id"></a>ID

[Azure 리소스의 명명 규칙](/azure/architecture/best-practices/resource-naming)이 적용되는 Azure 리소스 ID입니다. 포털에서 대시보드를 만들 때 일반적으로 guid 형식의 ID를 선택 하지만 프로그래밍 방식으로 만들 때 유효한 이름을 사용할 수 있습니다.

### <a name="name"></a>Name

이름은 구독, 리소스 종류 또는 리소스 그룹 정보를 포함하지 않는 리소스 ID의 세그먼트입니다. 기본적으로 리소스 ID의 마지막 세그먼트입니다.

### <a name="type"></a>형식

모든 대시보드는 __Microsoft.Portal/dashboards__ 형식입니다.

### <a name="location"></a>위치

다른 리소스와 달리 대시보드는 런타임 구성 요소를 포함하지 않습니다.  대시보드의 경우 위치는 대시보드의 JSON 표현을 저장하는 기본 지리적 위치를 나타냅니다. 값은 [구독 리소스의 위치 API](/rest/api/resources/subscriptions)를 사용하여 가져올 수 있는 위치 코드 중 하나여야 합니다.

### <a name="tags"></a>태그

태그는 임의 이름 값 쌍으로 리소스를 구성할 수 있는 Azure 리소스의 일반적인 기능입니다. 대시보드에는 __숨겨진 제목__ 이라는 특수 한 태그가 포함 되어 있습니다. 대시보드에이 속성이 채워져 있으면 포털에서 해당 값이 대시보드의 표시 이름으로 사용 됩니다. Azure 리소스 Id는 이름을 바꿀 수 없지만 태그는 바꿀 수 있습니다. 이 태그는 대시보드에 대한 이름 변경 가능한 표시 이름을 포함하는 방법을 제공합니다.

`"tags": { "hidden-title": "Created via API" }`

### <a name="properties"></a>속성

properties 개체는 __lenses__ 및 __metadata__ 라는 두 속성을 포함합니다. __lenses__ 속성은 대시보드의 타일에 대한 정보를 포함합니다.  __metadata__ 속성은 잠재적인 향후 기능을 위한 속성입니다.

### <a name="lenses"></a>Lenses

__lenses__ 속성은 대시보드를 포함합니다. 이 예제의 lens 개체는 "0" 이라는 단일 속성을 포함 합니다. Lenses는 현재 구현 되지 않은 그룹화 개념입니다. 지금은 모든 대시보드가 "0" 이라는 렌즈 개체에서이 단일 속성을 포함 합니다.

### <a name="parts"></a>부분

“0” 아래의 개체에는 __order__ 및 __parts__ 라는 두 개의 속성이 있습니다.  대시보드의 현재 버전에서 __order__ 는 항상 0입니다. __parts__ 속성은 대시보드의 개별 부분(타일이라고도 함)을 정의하는 개체를 포함합니다.

__parts__ 개체는 각 파트에 대한 속성을 포함합니다. 여기서 속성 이름은 숫자입니다. 이 숫자는 중요 하지 않습니다.

각 개별 파트 개체는 __position__ 및 __metadata__ 를 포함합니다.

### <a name="position"></a>위치

__position__ 속성에는 __x__, __y__, __rowSpan__ 및 __colSpan__ 으로 표현되는 파트에 대한 크기 및 위치 정보를 포함합니다. 값이 그리드 단위를 기준으로 합니다. 다음과 같이 대시보드가 사용자 지정 모드에 있는 경우 그리드 단위가 표시됩니다. 타일의 너비는 그리드 단위 2개이고 높이는 그리드 단위 1개이며 위치가 대시보드 왼쪽 상단 모서리라면 위치 개체는 다음과 같습니다.

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

:::image type="content" source="media/azure-portal-dashboards-structure/grid-units.png" alt-text="Azure Portal 대시보드의 그리드 단위를 보여 주는 스크린샷":::

### <a name="metadata"></a>메타데이터

각 파트는 metadata 속성을 포함하며 개체는 __type__ 이라는 하나의 필수 속성만 포함합니다. 이 문자열은 포털에 표시할 타일을 알려 줍니다. 예제 대시보드는 다음과 같은 타일 유형을 사용합니다.

1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – 모니터링 메트릭을 표시하는 데 사용됨
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – 목록, 링크 등에 대한 기본 서식과 함께 텍스트 또는 이미지를 표시하는 데 사용됨
1. `Extension[azure]/HubsExtension/PartType/VideoPart` - HTML 비디오 태그에서 작동하는 YouTube, Channel9 및 기타 비디오 유형의 비디오를 표시하는 데 사용됨
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Azure 가상 머신의 이름 및 상태를 표시하는 데 사용됨

각 파트 유형에는 고유한 구성이 있습니다. 가능한 구성 속성을 __inputs__, __settings__ 및 __asset__ 이라고 합니다. 

### <a name="inputs"></a>입력

일반적으로 inputs 개체는 타일을 리소스 인스턴스로 바인딩하는 정보를 포함합니다.  샘플 대시보드의 가상 머신 파트는 바인딩을 표현하는 데 Azure 리소스 ID를 사용하는 단일 입력을 포함합니다.  이 리소스 ID 형식은 모든 Azure 리소스 간에 일관됩니다.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```

메트릭 차트 파트는 표시 되는 메트릭에 대 한 정보와 함께 바인딩할 리소스를 표현 하는 단일 입력을 포함 합니다. 네트워크 및 네트워크 아웃 메트릭을 표시 하는 타일에 대 한 입력은 다음과 같습니다.

```json
"inputs":
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="settings"></a>설정

settings 개체는 파트의 구성 가능한 요소를 포함합니다.  샘플 대시보드의 Markdown 파트는 설정을 사용 하 여 사용자 지정 Markdown 콘텐츠를 구성 가능한 제목과 부제와 함께 저장 합니다.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

마찬가지로, 비디오 타일은 재생할 비디오에 대한 포인터를 포함하는 고유한 설정과 자동 재생 설정 및 제목 정보(선택 사항)를 포함합니다.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="asset"></a>자산

최고 수준의 관리 가능한 포털 개체(자산이라고 함)에 바인딩되는 타일에는 자산 개체를 통해 표현되는 관계를 포함합니다.  예제 대시보드에서 가상 머신 타일은 이 자산 설명을 포함합니다.  __idInputName__ 속성은 ID 입력이 자산에 대한 고유한 식별자(이 경우 리소스 ID)를 포함함을 포털에 알립니다. 대부분의 Azure 리소스 종류에는 포털에 정의된 자산이 있습니다.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서](azure-portal-dashboards.md) 또는 [프로그래밍 방식](azure-portal-dashboards-create-programmatically.md)으로 대시보드를 만드는 방법에 대해 알아봅니다.
- [Azure 대시보드에서 markdown 타일을 사용 하 여 사용자 지정 콘텐츠를 표시](azure-portal-markdown-tile.md)하는 방법을 알아봅니다.
