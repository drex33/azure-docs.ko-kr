---
title: Resource Manager 템플릿의 사용자 지정 매개 변수
description: Azure Data Factory 연속 통합 및 배달을 통해 Resource Manager 템플릿에서 사용자 지정 매개 변수를 사용하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6584120a0a66fd1d913fdee86a24ce3d91b2555f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519670"
---
# <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager 템플릿에서 사용자 지정 매개 변수 사용

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

개발 인스턴스에 연결된 Git 리포지토리가 있는 경우 템플릿을 게시하거나 내보내서 생성된 Resource Manager 템플릿의 기본 Resource Manager 템플릿 매개 변수를 재정의할 수 있습니다. 다음 시나리오에서 기본 Resource Manager 매개 변수 구성을 재정의할 수 있습니다.

* 자동화 CI/CD를 사용하고 Resource Manager 배포 중에 일부 속성을 변경하려고 하지만 속성은 기본적으로 매개 변수화되지 않습니다.
* 팩터리가 너무 커서 허용되는 최대 매개 변수(256)를 초과했기 때문에 기본 Resource Manager 템플릿이 유효하지 않습니다.

    다음 세 가지 옵션을 사용하여 사용자 지정 매개 변수 256 제한을 처리할 수 있습니다.    
  
    * 사용자 지정 매개 변수 파일을 사용하여 매개 변수화가 필요하지 않은 속성(예: 기본값을 유지할 수 있는 속성)을 제거하면 매개 변수 개수를 줄일 수 있습니다.
    * 데이터 흐름의 논리를 리팩터링하여 매개 변수를 줄입니다. 예를 들어 파이프라인 매개 변수는 모두 동일한 값을 가지며, 대신 전역 매개 변수를 사용할 수 있습니다.
    * 하나의 데이터 팩터리를 여러 데이터 흐름으로 분할합니다.

기본 Resource Manager 매개 변수 구성을 재정의하려면 **관리** 허브로 이동하고 “소스 제어” 섹션의 **ARM 템플릿** 을 선택합니다. **ARM 매개 변수 구성** 섹션 아래 “매개 변수 구성 편집”의 **편집** 아이콘을 클릭하여 Resource Manager 매개 변수 구성 코드 편집기를 엽니다.

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="사용자 지정 매개 변수 관리":::

> [!NOTE]
> **ARM 매개 변수 구성** 은 “GIT 모드”에서만 사용하도록 설정됩니다. 현재 “라이브 모드” 또는 “Data Factory” 모드에서는 사용할 수 없습니다.

사용자 지정 Resource Manager 매개 변수 구성을 생성하면 git 분기의 루트 폴더에 **arm-template-parameters-definition.json** 이라는 파일이 생성됩니다. 정확한 파일 이름을 사용해야 합니다.

:::image type="content" source="media/continuous-integration-delivery/custom-parameters.png" alt-text="사용자 지정 매개 변수 파일":::

협업 분기에서 게시하는 경우 Data Factory가 이 파일을 읽고 해당 구성을 사용하여 매개 변수화된 속성을 생성합니다. 파일이 없는 경우 기본 템플릿이 사용됩니다.

Resource Manager 템플릿을 내보낼 때 Data Factory는 협업 분기가 아니라 현재 작업 중인 모든 분기에서 이 파일을 읽습니다. 프라이빗 분기에서 파일을 만들거나 편집할 수 있습니다. 여기서 UI의 **ARM 템플릿 내보내기** 를 선택하여 변경 내용을 테스트할 수 있습니다. 그런 다음 협업 분기에 파일을 병합할 수 있습니다.

> [!NOTE]
> 사용자 지정 Resource Manager 매개 변수 구성에서 ARM 템플릿 매개 변수 제한(256)을 변경하지 않습니다. 이를 통해 매개 변수화된 속성의 개수를 선택하고 줄일 수 있습니다.

## <a name="custom-parameter-syntax"></a>사용자 지정 매개 변수 구문

다음은 사용자 지정 매개 변수 파일 **arm-template-parameters-definition.json** 을 만들 때 따라야 할 지침입니다. 이 파일은 각 엔터티 형식에 대한 섹션(트리거, 파이프라인, 연결된 서비스, 데이터 세트, 통합 런타임 및 데이터 흐름)으로 구성되어 있습니다.

* 관련 엔터티 형식 아래에 속성 경로를 입력합니다.
* 속성 이름을 `*`로 설정하면 해당 속성 아래(재귀적이 아닌 첫 번째 수준까지만)의 모든 속성을 매개 변수화하려고 함을 나타냅니다. 이 구성에 대한 예외를 제공할 수도 있습니다.
* 문자열로 속성의 값을 설정하면 속성을 매개 변수화하려고 함을 나타냅니다. `<action>:<name>:<stype>` 형식을 사용합니다.
   *  `<action>`은 다음 문자 중 하나일 수 있습니다.
      * `=`는 현재 값을 매개 변수의 기본값으로 유지함을 의미합니다.
      * `-`는 매개 변수의 기본값을 유지하지 않음을 의미합니다.
      * `|`는 연결 문자열 또는 키와 관련한 Azure Key Vault의 비밀에 대한 특수 사례입니다.
   * `<name>`은 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용합니다. 값이 `-` 문자로 시작하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString`에서 `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>`은 매개 변수의 형식입니다. `<stype>`이 비어 있는 경우 기본 형식은 `string`입니다. 지원되는 값은 `string`, `securestring`, `int`, `bool`, `object`, `secureobject`, `array`입니다.
* 정의 파일에서 배열을 지정하면 템플릿의 일치하는 속성이 배열임을 나타냅니다. Data Factory는 배열의 Integration Runtime 개체에 지정된 정의를 사용하여 배열에 있는 모든 개체를 반복합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스와 관련될 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용됩니다.
* 기본적으로 Key Vault 비밀과 같은 모든 보안 문자열과 연결 문자열, 키, 토큰 등의 보안 문자열은 매개 변수화됩니다.
 
## <a name="sample-parameterization-template"></a>샘플 매개 변수화 템플릿

Resource Manager 매개 변수 구성의 예는 다음과 같습니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/credentials" : {
        "properties": {
            "typeProperties": {
                "resourceId": "="
            }
        }
    }
}
```
다음은 리소스 종류별로 분할된 이전 템플릿의 구성 방법에 대한 설명입니다.

### <a name="pipelines"></a>파이프라인
    
* `activities/typeProperties/waitTimeInSeconds` 경로에 있는 모든 속성은 매개 변수화됩니다. `waitTimeInSeconds`라는 코드 수준 속성(예: `Wait` 작업)이 있는 파이프라인의 모든 작업은 기본 이름을 가진 숫자로 매개 변수화됩니다. 그러나 Resource Manager 템플릿에는 기본값이 없습니다. Resource Manager 배포 중에 입력해야 할 필수 항목입니다.
* 마찬가지로 `headers`(예: `Web` 작업)라는 속성은 형식 `object`(JObject)로 매개 변수화됩니다. 이 값은 원본 팩터리의 값과 동일한 기본값을 갖습니다.

### <a name="integrationruntimes"></a>IntegrationRuntimes

* `typeProperties` 경로 아래에 있는 모든 속성은 해당 기본값을 사용하여 매개 변수화됩니다. 예를 들어 `IntegrationRuntimes` 형식 속성에는 `computeProperties` 및 `ssisProperties`의 두 가지 속성이 있습니다. 두 속성 유형 모두 해당 기본값과 유형(Object)으로 만들어집니다.

### <a name="triggers"></a>트리거

* `typeProperties`에서 두 개의 속성은 매개 변수화됩니다. 첫 번째 값은 기본값을 갖도록 지정된 `maxConcurrency`이며 `string` 형식입니다. 이 값은 기본 매개 변수 이름 `<entityName>_properties_typeProperties_maxConcurrency`를 갖습니다.
* `recurrence` 속성도 매개 변수화됩니다. 이 속성 아래 수준의 모든 속성은 문자열로 매개 변수화되어 기본값 및 매개 변수 이름으로 지정됩니다. 단, `int` 형식으로 매개 변수화되는 `interval` 속성은 예외입니다. 매개 변수 이름에는 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`가 접미사로 붙습니다. 마찬가지로 `freq` 속성은 문자열이며 문자열로 매개 변수화됩니다. 그러나 `freq` 속성은 기본값 없이 매개 변수화됩니다. 이름이 단축되고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

### <a name="linkedservices"></a>LinkedServices

* 연결된 서비스는 고유합니다. 연결된 서비스 및 데이터 세트에는 다양한 형식이 있어 형식별로 사용자 지정할 수 있습니다. 이 예에서는 `AzureDataLakeStore` 형식의 연결된 모든 서비스에 대해 특정 템플릿이 적용됩니다. `*`을 통한 다른 모든 서비스에 대해서는 다른 템플릿이 적용됩니다.
* `connectionString` 속성은 `securestring` 값으로 매개 변수화됩니다. 기본값이 없습니다. `connectionString`이 접미사로 사용되는 축약된 매개 변수 이름이 포함됩니다.
* 속성 `secretAccessKey`는 `AzureKeyVaultSecret`(예: Amazon S3에 연결된 서비스)가 됩니다. 자동으로 Azure Key Vault 비밀로 매개 변수화되고 구성된 키 자격 증명 모음에서 페치됩니다. 키 자격 증명 모음을 자체적으로 매개 변수화할 수도 있습니다.

### <a name="datasets"></a>데이터 세트

* 데이터 세트에 대해 형식별 사용자 지정을 사용할 수 있지만, 명시적으로 \* 수준으로 구성하지 않고도 구성할 수 있습니다. 이전 예에서 `typeProperties` 아래의 모든 데이터 세트 속성은 매개 변수화됩니다.

> [!NOTE]
> **Azure 경고 및 행렬이** 파이프라인에 대해 구성된 경우 현재 ARM 배포에 대한 매개 변수로 지원되지 않습니다. 새 환경에서 경고 및 매트릭스를 다시 적용하려면 [Data Factory 모니터링, 경고 및 매트릭스](./monitor-metrics-alerts.md)를 따르세요.
> 

## <a name="default-parameterization-template"></a>기본 매개 변수화 템플릿

다음은 현재의 기본 매개 변수화 템플릿입니다. 몇 개의 매개 변수만 추가해야 하는 경우 기존 매개 변수화 구조를 유지하므로 이 템플릿을 직접 편집하는 것이 좋습니다.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

## <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>예: 기존 Azure Databricks 대화형 클러스터 ID 매개 변수화

다음 예에서는 기본 매개 변수화 템플릿에 단일 값을 추가하는 방법을 보여 줍니다. Databricks 연결된 서비스의 기존 Azure Databricks 대화형 클러스터 ID를 매개 변수 파일에 추가하려고 합니다. 이 파일은 `Microsoft.DataFactory/factories/linkedServices`의 속성 필드에 `existingClusterId`를 추가하는 것을 제외하고 이전 파일과 동일합니다.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="next-steps"></a>다음 단계

- [연속 통합 및 지속적인 업데이트 개요](continuous-integration-delivery.md)
- [Azure Pipelines 릴리스를 사용 하 여 연속 통합 자동화](continuous-integration-delivery-automate-azure-pipelines.md)
- [리소스 관리자 템플릿을 각 환경으로 수동으로 승격](continuous-integration-delivery-manual-promotion.md)
- [연결된 Resource Manager 템플릿](continuous-integration-delivery-linked-templates.md)
- [핫픽스 프로덕션 환경 사용](continuous-integration-delivery-hotfix-environment.md)
- [ 샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)