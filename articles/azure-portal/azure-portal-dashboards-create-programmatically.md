---
title: Azure 대시보드를 프로그래밍 방식으로 만들기
description: Azure Portal에서 대시보드를 템플릿으로 사용하여 프로그래밍 방식으로 Azure 대시보드를 만듭니다. JSON 참조를 포함합니다.
ms.topic: how-to
ms.date: 10/19/2021
ms.openlocfilehash: 15d94cf5efc857adf9bbc0cf98c9e3d34e5bda7d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239830"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure 대시보드를 프로그래밍 방식으로 만들기

이 문서에서는 Azure 대시보드를 프로그래밍 방식으로 만들고 게시하는 과정을 안내합니다. 아래에 표시된 샘플 대시보드는 문서 전체에서 참조됩니다.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/sample-dashboard.png" alt-text="Azure Portal 샘플 대시보드의 스크린샷.":::

## <a name="overview"></a>개요

[Azure Portal](https://portal.azure.com) 공유 대시보드는 가상 머신 및 스토리지 계정과 같은 [리소스입니다.](../azure-resource-manager/management/overview.md) [Azure Resource Manager REST API](/rest/api/), [Azure CLI](/cli/azure)및 [Azure PowerShell 명령](/powershell/azure/get-started-azureps)을 사용하여 프로그래밍 방식으로 리소스를 관리할 수 있습니다.

API에 빌드한 많은 기능으로 리소스를 보다 쉽게 관리할 수 있습니다. 이러한 각 API 및 도구는 리소스를 만들고 나열하며 검색, 수정 및 삭제하는 방법을 제공합니다. 대시보드는 리소스이므로 선호하는 API 또는 도구를 선택하여 사용할 수 있습니다.

어떤 도구를 사용하든 대시보드를 프로그래밍 방식으로 만들려면 대시보드 개체의 JSON 표시를 생성합니다. 이 개체는 대시보드의 타일에 대한 정보를 포함합니다. 크기, 위치, 바인딩되어 있는 리소스 및 모든 사용자 지정이 포함됩니다.

이 JSON 문서를 생성하는 가장 실용적인 방법은 Azure Portal 사용하여 원하는 타일이 있는 초기 대시보드를 만드는 것입니다. 그런 다음 JSON을 내보내고 결과에서 템플릿을 만들어 스크립트, 프로그램 및 배포 도구에서 추가로 수정하고 사용할 수 있습니다.

## <a name="create-a-dashboard"></a>대시보드 만들기

대시보드를 만들려면 [Azure Portal](https://portal.azure.com) 메뉴에서 **대시보드** 를 선택하고 **새 대시보드** 를 선택합니다.

자세한 지침은 Azure Portal [대시보드 만들기를](azure-portal-dashboards.md)참조하세요.

## <a name="share-the-dashboard"></a>대시보드 공유

대시보드를 구성한 후 다음 단계는 **공유** 명령을 사용하여 대시보드를 게시하는 것입니다.

대시보드를 공유하는 경우 게시할 구독 및 리소스 그룹을 선택해야 합니다. 선택한 구독 및 리소스 그룹에 대한 쓰기 권한이 있어야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

자세한 지침은 Azure [역할 기반 액세스 제어를 사용하여 Azure 대시보드 공유를 참조하세요.](azure-portal-dashboard-share-access.md)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>대시보드의 JSON 표현 가져오기

대시보드를 공유하는 데 몇 초밖에 걸리지 않습니다. 완료되면 다음 단계는 **다운로드** 명령을 사용하여 JSON을 내보내는 것입니다.

:::image type="content" source="media/azure-portal-dashboards-create-programmatically/download-command.png" alt-text="Azure Portal 템플릿의 JSON 표현을 내보내는 명령의 스크린샷.":::

## <a name="create-a-template-from-the-json"></a>JSON에서 템플릿 만들기

다음 단계는 이 JSON에서 템플릿을 만드는 것입니다. 적절한 리소스 관리 API, 명령줄 도구 또는 포털 내에서 프로그래밍 방식으로 템플릿을 사용할 수 있습니다.

템플릿을 만들기 위해 대시보드 JSON 구조를 완전히 이해할 필요는 없습니다. 대부분의 경우 각 타일의 구조와 구성을 보존하려고 합니다. 그다음 타일이 가리키는 Azure 리소스 집합을 매개 변수화합니다.

내보낸 JSON 대시보드를 살펴보고 Azure 리소스 ID의 모든 항목을 찾습니다. 예제 대시보드는 모두 단일 Azure 가상 컴퓨터를 가리키는 여러 개의 타일을 포함하고 있습니다. 이는 대시보드가 이 단일 리소스만 살펴보기 때문입니다. 문서의 끝에 포함된 샘플 JSON에서 "/subscriptions"를 검색하는 경우 이 ID가 여러 번 발생합니다.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

나중에 모든 가상 머신에 대해 이 대시보드를 게시하려면 JSON 내에서 이 문자열의 모든 항목을 매개 변수화합니다.

Azure에서 리소스를 만드는 API에는 두 종류가 있습니다.

- 명령적 API는 한 번에 하나의 리소스를 만듭니다. 자세한 내용은 [리소스](/rest/api/resources/resources)를 참조하세요.
- 단일 API 호출을 사용하여 여러 종속 리소스를 만드는 템플릿 기반 배포 시스템입니다. 자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

템플릿 기반 배포는 매개 변수화 및 템플릿을 지원합니다. 이 문서에서는 이 방법을 사용합니다.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>템플릿 배포를 사용하여 템플릿에서 대시보드를 프로그래밍 방식으로 만듭니다.

Azure는 여러 리소스의 배포를 오케스트레이션하는 기능을 제공합니다. 배포할 리소스 집합과 둘 간의 관계를 표현하는 배포 템플릿을 만듭니다.  각 리소스의 JSON 형식은 사용자가 하나씩 작성하는 경우와 같습니다. 차이점은 템플릿 언어는 변수, 매개 변수, 기본 기능 등과 같은 몇 가지 개념을 추가한다는 것입니다. 이 확장 구문은 템플릿 배포 컨텍스트에서만 지원됩니다. 앞에서 설명한 명령적 API와 함께 사용하는 경우에는 작동하지 않습니다. 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/syntax.md)를 참조하세요.

매개 변수화는 템플릿의 매개 변수 구문을 사용하여 수행해야 합니다.  아래 표시된 것처럼 이전에 찾은 리소스 ID의 모든 인스턴스를 바꿉니다.

하드 코드된 리소스 ID가 있는 JSON 속성 예제

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

템플릿 매개 변수를 기반으로 매개 변수화된 버전으로 변환된 JSON 속성 예제

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

다음과 같이 JSON 템플릿 맨 위에 필요한 템플릿 메타데이터와 매개 변수를 선언합니다.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

템플릿을 구성한 후에는 다음 방법 중 하나를 사용하여 템플릿을 배포합니다.

- [REST API](/rest/api/resources/deployments)
- [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Azure CLI](/cli/azure/group/deployment#az_group_deployment_create)
- [Azure Portal 템플릿 배포 페이지](https://portal.azure.com/#create/Microsoft.Template)

다음에는 두 가지 버전의 예제 대시보드 JSON이 표시됩니다. 첫 번째는 리소스에 이미 바인딩된 포털에서 내보낸 버전입니다. 두 번째는 프로그래밍 방식으로 모든 가상 머신에 바인딩될 수 있고 Azure Resource Manager를 사용하여 배포할 수 있는 템플릿 버전입니다.

### <a name="json-representation-of-our-example-dashboard"></a>예제 대시보드의 JSON 표현

이 예제는 이 문서를 따라하면 표시되는 것과 비슷합니다. 지침에서는 이미 배포된 대시보드의 JSON 표현을 내보냈습니다. 하드 코드된 리소스 식별자는 이 대시보드가 특정 Azure 가상 머신을 가리키고 있음을 나타냅니다.

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
        "metadata": { }
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

### <a name="template-representation-of-our-example-dashboard"></a>예제 대시보드의 템플릿 표현

대시보드의 템플릿 버전에서 `virtualMachineName`, `virtualMachineResourceGroup` 및 `dashboardName`라는 세 개의 매개 변수를 정의했습니다.  매개 변수를 사용하면 배포할 때마다 다른 Azure 가상 컴퓨터에서 이 대시보드를 가리키도록 할 수 있습니다. 이 대시보드는 Azure 가상 머신을 가리키도록 프로그래밍 방식으로 구성 및 배포될 수 있습니다. 이 기능을 테스트하는 방법은 다음 템플릿을 복사하여 [Azure Portal의 템플릿 배포 페이지](https://portal.azure.com/#create/Microsoft.Template)에 붙여넣는 것입니다.

이 예제에서는 대시보드를 단독으로 배포하지만 템플릿 언어를 사용하면 여러 리소스를 배포하고 하나 이상의 대시보드를 함께 묶을 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
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
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

매개 변수화된 템플릿을 사용하여 대시보드를 배포하는 예제를 살펴보았으므로 이제 [Azure Resource Manager REST API](/rest/api/), [Azure CLI](/cli/azure) 또는 [Azure PowerShell 명령](/powershell/azure/get-started-azureps)을 사용하여 템플릿을 배포할 수 있습니다.

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Azure CLI를 사용하여 프로그래밍 방식으로 대시보드 만들기

Azure CLI에 대한 환경을 준비합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 예제에서는 [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json)이라는 대시보드를 사용합니다. 각진 대괄호 안에 있는 모든 콘텐츠를 값으로 바꿔야 합니다.

[az portal dashboard create](/cli/azure/portal/dashboard#az_portal_dashboard_create) 명령을 실행하여 템플릿을 기반으로 대시보드를 만듭니다.

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update) 명령을 사용하여 대시보드를 업데이트할 수 있습니다.

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

[az portal dashboard show](/cli/azure/portal/dashboard#az_portal_dashboard_show) 명령을 실행하여 대시보드의 세부 정보를 확인합니다.

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

현재 구독에 대한 모든 대시보드를 보려면 [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list)를 사용합니다.

```azurecli
az portal dashboard list
```

리소스 그룹에 대한 모든 대시보드를 볼 수도 있습니다.

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [Azure 대시보드에서 Markdown 타일을 사용하여 사용자 지정 콘텐츠를 표시하는](azure-portal-markdown-tile.md)방법을 알아봅니다.
- 대시보드에 대한 모든 Azure CLI 명령을 탐색하고 [az portal dashboard를 참조하세요.](/cli/azure/portal/dashboard)
- Azure Portal 설정 및 기본 설정을 관리하는 방법을 [알아봅니다.](set-preferences.md)
