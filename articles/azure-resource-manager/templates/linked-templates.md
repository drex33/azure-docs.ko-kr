---
title: 배포용 링크 템플릿
description: ARM 템플릿(Azure Resource Manager 템플릿)에서 연결된 템플릿을 사용하여 모듈식 템플릿 솔루션을 만드는 방법을 설명합니다. 매개 변수 값을 전달하고 매개 변수 파일 및 동적으로 생성된 URL을 지정하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b8be710611d892913c43e9d500a051a3d3b55ca5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820504"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Azure 리소스를 배포할 때 연결 및 중첩된 템플릿 사용

복잡한 솔루션을 배포하려면 ARM 템플릿(Azure Resource Manager 템플릿)을 여러 관련 템플릿으로 분할한 다음, 주 템플릿을 통해 함께 배포할 수 있습니다. 관련 템플릿은 기본 템플릿 내에 포함된 별도의 파일 또는 템플릿 구문일 수 있습니다. 이 문서에서는 주 템플릿의 링크를 통해 참조되는 별도의 템플릿 파일을 나타내기 위해 **연결된 템플릿** 이라는 용어를 사용합니다. 기본 템플릿 내에 포함된 템플릿 구문을 나타내기 위해 **중첩된 템플릿** 이라는 용어를 사용합니다.

중소기업에게는 단일 템플릿이 더 간편하게 이해하고 유지 관리할 수 있습니다. 모든 리소스 및 값을 단일 파일에서 볼 수 있습니다. 고급 시나리오의 경우 연결된 템플릿을 사용하여 솔루션을 대상 구성 요소로 분할할 수 있습니다. 관련 템플릿을 다른 시나리오에 쉽게 재사용할 수 있습니다.

자습서에 대해서는 [자습서: 연결된 템플릿 배포](./deployment-tutorial-linked-template.md)를 참조하세요.

> [!NOTE]
> 연결된 템플릿 또는 중첩된 템플릿의 경우 배포 모드를 [증분](deployment-modes.md)으로만 설정할 수 있습니다. 그러나 주 템플릿은 전체 모드로 배포할 수 있습니다. 주 템플릿을 전체 모드로 배포하고 연결된 템플릿이나 중첩된 템플릿이 동일한 리소스 그룹을 대상으로 하는 경우 연결된 또는 중첩된 템플릿에 배포된 리소스는 전체 모드 배포에 대한 평가에 포함됩니다. 주 템플릿과 연결된 또는 중첩된 템플릿에 배포된 리소스의 결합된 컬렉션을 리소스 그룹의 기존 리소스와 비교합니다. 이 결합된 컬렉션에 포함되지 않은 모든 리소스는 삭제됩니다.
>
> 연결된 템플릿이나 중첩된 템플릿이 다른 리소스 그룹을 대상으로 하는 경우 해당 배포는 증분 모드를 사용합니다.
>

## <a name="nested-template"></a>중첩된 템플릿

템플릿을 중첩하려면 [배포 리소스](/azure/templates/microsoft.resources/deployments)를 기본 템플릿에 추가합니다. `template` 속성에서 템플릿 구문을 지정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

다음 예제에서는 중첩된 템플릿을 통해 스토리지 계정을 배포합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2021-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>중첩된 템플릿의 식 계산 범위

중첩된 템플릿을 사용하면 템플릿 식이 부모 템플릿 또는 중첩된 템플릿의 범위 내에서 평가되는지 여부를 지정할 수 있습니다. 범위에 따라 [resourceGroup](template-functions-resource.md#resourcegroup) 및 [subscription](template-functions-resource.md#subscription)과 같은 매개 변수, 변수 및 함수를 확인하는 방법이 결정됩니다.

`expressionEvaluationOptions` 속성을 통해 범위를 설정합니다. 기본적으로 `expressionEvaluationOptions` 속성은 `outer`로 설정됩니다. 즉, 부모 템플릿 범위를 사용하게 됩니다. 식이 중첩된 템플릿의 범위 내에서 계산되도록 하려면 값을 `inner`로 설정합니다.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2021-04-01",
  "name": "nestedTemplate1",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
  ...
```

> [!NOTE]
>
> 범위를 `outer`로 설정하면 중첩된 템플릿에 배포한 리소스에 대해 중첩된 템플릿의 출력 섹션에서 `reference` 함수를 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 `inner` 범위를 사용하거나 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

다음 템플릿에서는 범위에 따라 템플릿 식이 확인되는 방법을 보여 줍니다. 부모 템플릿과 중첩된 템플릿 둘 다에 정의된 `exampleVar`이라는 변수를 포함합니다. 변수의 값을 반환합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

`exampleVar`의 값은 `expressionEvaluationOptions`의 `scope` 속성 값에 따라 변경됩니다. 다음 표에서는 두 범위에 대한 결과를 보여 줍니다.

| 평가 범위 | 출력 |
| ----- | ------ |
| inner | 중첩된 템플릿에서 |
| 외부(또는 기본값) | 부모 템플릿에서 |

다음 예에서는 SQL Server를 배포하고 암호에 사용할 Key Vault 암호를 검색합니다. 범위는 Key Vault ID를 동적으로 만들고(외부 템플릿 `parameters`에서 `adminPassword.reference.keyVault` 참조) 중첩된 템플릿에 매개 변수로 전달하므로 `inner`로 설정됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2021-02-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

중첩된 템플릿에서 보안 매개 변수 값을 사용할 때는 주의해야 합니다. 범위를 외부로 설정하는 경우 보안 값은 배포 기록에 일반 텍스트로 저장됩니다. 배포 기록에서 템플릿을 보는 사용자는 보안 값을 볼 수 있습니다. 대신 내부 범위를 사용하거나, 보안 값이 필요한 리소스를 부모 템플릿에 추가합니다.

다음 발췌문에서는 안전한 값과 안전하지 않은 값을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2021-04-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2021-04-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2021-04-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>연결된 템플릿

템플릿에 연결하려면 [배포 리소스](/azure/templates/microsoft.resources/deployments)를 기본 템플릿에 추가합니다. `templateLink` 속성에서 포함할 템플릿의 URI를 지정합니다. 다음 예제에서는 스토리지 계정에 있는 템플릿에 연결합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

연결된 템플릿을 참조하는 경우 `uri` 값은 로컬 파일 또는 로컬 네트워크에서만 사용할 수 있는 파일일 수 없습니다. Azure Resource Manager에서 템플릿에 액세스할 수 있어야 합니다. HTTP 또는 HTTPS로 다운로드 가능한 URI 값을 제공합니다.

HTTP 또는 HTTPS를 포함하는 매개 변수를 사용하여 템플릿을 참조할 수 있습니다. 예를 들어, 일반적인 패턴은 `_artifactsLocation` 매개 변수를 사용하는 것입니다. 연결된 템플릿은 다음과 같은 식을 사용하여 설정할 수 있습니다.

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

GitHub의 템플릿에 연결하는 경우 원시 URL을 사용합니다. 링크의 형식은 `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json`입니다. 원시 링크를 가져오려면 **원시** 를 선택합니다.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="원시 URL 선택":::

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

### <a name="parameters-for-linked-template"></a>연결된 템플릿의 매개 변수

외부 파일이나 인라인에서 연결된 템플릿에 대한 매개 변수를 제공할 수 있습니다. 외부 매개 변수 파일을 제공하는 경우 `parametersLink` 속성을 사용합니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

매개 변수 값을 인라인으로 전달하려면 `parameters` 속성을 사용합니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

인라인 매개 변수와 매개 변수 파일에 대한 링크를 둘 다 사용할 수는 없습니다. `parametersLink` 및 `parameters`를 둘 다 지정하면 오류를 발생하며 배포가 실패합니다.

### <a name="use-relative-path-for-linked-templates"></a>연결된 템플릿에 대한 상대 경로 사용

`Microsoft.Resources/deployments`의 `relativePath` 속성을 사용 하면 연결된 템플릿을 더 쉽게 작성할 수 있습니다. 이 속성은 원격으로 연결된 템플릿을 부모에 상대적인 위치에 배포하는 데 사용할 수 있습니다. 이 기능을 사용하려면 모든 템플릿 파일을 스테이징하고 GitHub 또는 Azure Storage 계정과 같은 원격 URI에서 사용할 수 있어야 합니다. Azure PowerShell 또는 Azure CLI의 URI를 사용하여 주 템플릿을 호출하는 경우 자식 배포 URI는 부모 및 relativePath의 조합입니다.

> [!NOTE]
> templateSpec을 만들 때 `relativePath` 속성에서 참조하는 모든 템플릿은 Azure PowerShell 또는 Azure CLI를 통해 templateSpec 리소스에 패키징됩니다. 파일을 스테이징할 필요는 없습니다. 자세한 내용은 [연결된 템플릿을 사용하여 템플릿 사양 만들기](./template-specs.md#create-a-template-spec-with-linked-templates)를 참조하세요.

폴더 구조가 다음과 같다고 가정합니다.

![Resource Manager 연결된 템플릿 상대 경로](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

다음 템플릿은 ’mainTemplate.json’이 이전 이미지에서 설명한 ‘nestedChild.json’을 배포하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

다음 배포에서 위의 템플릿에 있는 연결된 템플릿의 URI는 **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** 입니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

Azure Storage 계정에 저장된 상대 경로를 사용하여 연결된 템플릿을 배포하려면 `QueryString`/`query-string` 매개 변수를 사용하여 TemplateUri 매개 변수와 함께 사용할 SAS 토큰을 지정합니다. 이 매개 변수는 Azure CLI 버전 2.18 이상 및 Azure PowerShell 버전 5.4 이상에서만 지원됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

QueryString에 선행 "?"가 없는지 확인합니다. 배포는 배포용 URI를 어셈블할 때 물음표를 하나 추가합니다.

## <a name="template-specs"></a>템플릿 사양

액세스 가능한 엔드포인트에서 연결된 템플릿을 유지 관리하는 대신, 배포할 수 있는 단일 엔터티로 주 템플릿과 연결된 템플릿을 패키지하는 [템플릿 사양](template-specs.md)을 만들 수 있습니다. 템플릿 사양은 Azure 구독에 있는 리소스입니다. 이를 통해 조직의 사용자와 안전하게 템플릿을 공유할 수 있습니다. Azure RBAC(역할 기반 액세스 제어)를 사용하여 템플릿 사양에 대한 액세스 권한을 부여합니다. 이 기능은 현재 미리 보기로 제공됩니다.

자세한 내용은 다음을 참조하세요.

- [자습서: 연결된 템플릿을 사용하여 템플릿 사양 만들기](./template-specs-create-linked.md).
- [자습서: 템플릿 사양을 연결된 템플릿으로 배포](./template-specs-deploy-linked-template.md)

## <a name="dependencies"></a>종속성

다른 리소스 종류와 마찬가지로 연결된 템플릿 간에 종속성을 설정할 수 있습니다. 연결된 템플릿의 리소스를 연결된 두 번째 템플릿의 리소스보다 먼저 배포해야 하는 경우에는 두 번째 템플릿을 첫 번째 템플릿에 종속되도록 설정합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

`templateLink` 또는 `parametersLink` 속성에 대해 `contentVersion` 속성을 제공하지 않아도 됩니다. `contentVersion`을 제공하지 않으면 현재 버전의 템플릿이 배포됩니다. 콘텐츠 버전 값을 제공하는 경우에는 연결된 템플릿의 버전과 일치해야 합니다. 그렇지 않으면 오류와 함께 배포에 실패합니다.

## <a name="using-variables-to-link-templates"></a>변수를 사용하여 템플릿 연결

앞의 예제에서는 템플릿 링크에 대한 하드 코딩된 URL 값을 보여 주었습니다. 이 방법은 간단한 템플릿에는 적용될 수 있지만 대규모 모듈식 템플릿 세트에는 잘 작동하지 않습니다. 대신, 주 템플릿에 대한 기본 URL을 보관하는 정적 변수를 만든 다음 해당 기본 URL에서 연결된 템플릿에 대한 URL을 동적으로 만들 수 있습니다. 이 방식의 경우 주 템플릿에서만 정적 변수를 변경하면 되므로 템플릿을 쉽게 이동하거나 분기할 수 있다는 장점이 있습니다. 주 템플릿은 분해된 템플릿 전체에서 올바른 URI를 전달합니다.

다음 예제에서는 기본 URL을 사용하여 연결된 템플릿에 대한 두 개의 URL을 만드는 방법을 보여 줍니다(`sharedTemplateUrl` 및 `vmTemplateUrl`).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/postgre/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

또한 [deployment()](template-functions-deployment.md#deployment) 를 사용하여 현재 템플릿에 대한 기본 URL을 가져올 수 있으며 동일한 위치에 있는 다른 템플릿에 대한 URL를 가져올 수 있습니다. 이 방법은 템플릿 위치가 변경되거나 템플릿 파일에서 URL 하드 코딩을 방지하려는 경우 유용합니다. `templateLink` 속성은 URL을 사용하여 원격 템플릿을 연결할 때만 반환됩니다. 로컬 템플릿을 사용하는 경우 이 속성을 사용할 수 없습니다.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

궁극적으로 `templateLink` 속성의 `uri` 속성에서 해당 변수를 사용합니다.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>copy 사용

중첩된 템플릿을 사용하여 리소스의 여러 인스턴스를 만들려면 `Microsoft.Resources/deployments` 리소스 수준에서 `copy` 요소를 추가합니다. 또는 범위가 `inner`인 경우 중첩된 템플릿 내에 copy를 추가할 수 있습니다.

다음 예제 템플릿에서는 중첩된 템플릿에 `copy`를 사용하는 방법을 보여 줍니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2021-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            // "copy": {
            //   "name": "storagecopy",
            //   "count": 2
            // }
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>연결된 템플릿에서 값 가져오기

연결된 템플릿에서 출력 값을 가져오려면 `"[reference('deploymentName').outputs.propertyName.value]"` 같은 구문으로 속성 값을 검색합니다.

연결된 템플릿에서 출력 속성을 가져올 때 속성 이름에 대시를 포함하지 않아야 합니다.

다음 예에서는 연결된 템플릿을 참조하고 출력 값을 가져오는 방법을 보여 줍니다. 연결된 템플릿이 간단한 메시지를 반환합니다. 먼저, 연결된 템플릿에 대해 다음을 수행합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

주 템플릿은 연결된 템플릿을 배포하고 반환된 값을 가져옵니다. 배포 리소스를 이름으로 참조하고 연결된 템플릿에서 반환한 속성의 이름을 사용합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

다음 예제에서는 공용 IP 주소를 배포하고 해당 공용 IP에 대한 Azure 리소스의 리소스 ID를 반환하는 템플릿을 보여 줍니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Load Balancer를 배포할 때 이전 템플릿의 공용 IP 주소를 사용하려면 템플릿에 연결하고 `Microsoft.Resources/deployments` 리소스에서 종속성을 선언합니다. Load Balancer의 공개 IP 주소는 연결된 템플릿에서 값을 출력하도록 설정됩니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>배포 기록

Resource Manager는 각 템플릿을 배포 기록에서 별도 배포로 처리합니다. 3개의 연결 또는 중첩된 템플릿이 있는 주 템플릿은 배포 기록에 다음과 같이 나타납니다.

![배포 기록](./media/linked-templates/deployment-history.png)

기록에서 이러한 개별 항목을 사용하여 배포 후 출력 값을 가져올 수 있습니다. 다음 템플릿은 공개 IP 주소를 만들고 해당 IP 주소를 출력합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2021-02-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

다음 템플릿은 위에 템플릿에 연결됩니다. 세 개의 공개 IP 주소를 만듭니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

배포 후 다음 PowerShell 스크립트를 통해 출력 값을 검색할 수 있습니다.

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

또는 Bash 셸의 Azure CLI 스크립트:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>외부 템플릿 보호

연결된 템플릿은 외부에서 사용할 수 있어야 하지만 일반에 공개할 필요는 없습니다. 스토리지 계정 소유자만 액세스할 수 있는 프라이빗 스토리지 계정에 템플릿을 추가할 수 있습니다. 그런 다음 배포하는 동안 액세스할 수 있도록 공유 액세스 서명(SAS) 토큰을 만듭니다. 링크된 템플릿 URI에 SAS 토큰을 추가합니다. 토큰이 보안 문자열로 전달되었지만 SAS 토큰을 포함한 링크된 템플릿 URI가 배포 작업에 로그됩니다. 노출을 최소화하려면 토큰에 만료 날짜를 설정합니다.

매개 변수 파일은 SAS 토큰으로 액세스를 제한할 수 있습니다.

현재 [Azure Storage 방화벽](../../storage/common/storage-network-security.md) 뒤에 있는 스토리지 계정의 템플릿에 연결할 수 없습니다.

> [!IMPORTANT]
> SAS 토큰을 사용하여 연결된 템플릿을 보호하는 대신, [템플릿 사양](template-specs.md)을 만드는 것이 좋습니다. 템플릿 사양에서는 주 템플릿과 연결된 템플릿을 Azure 구독에 리소스로 안전하게 저장합니다. Azure RBAC를 사용하여 템플릿을 배포해야 하는 사용자에게 액세스 권한을 부여합니다.

다음 예제에서는 템플릿에 연결할 때 SAS 토큰을 전달하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

PowerShell에서는 다음 명령을 사용하여 컨테이너용 토큰을 얻고 템플릿을 배포합니다. `containerSasToken` 매개 변수가 템플릿에 정의되어 있는지 확인합니다. 이것은 `New-AzResourceGroupDeployment` 명령의 매개 변수가 아닙니다.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Bash 셸의 Azure CLI에서는 컨테이너용 토큰을 얻고 다음 코드를 사용하여 템플릿을 배포합니다.

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 연결된 템플릿의 일반적인 사용 방법을 보여 줍니다.

|기본 템플릿  |연결된 템플릿 |Description  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[연결된 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | 연결된 템플릿에서 문자열을 반환합니다. |
|[공용 IP 주소가 있는 Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[연결된 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |연결된 템플릿에서 공용 IP 주소를 반환하고 부하 분산 장치에서 해당 값을 설정합니다. |
|[여러 IP 주소](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [연결된 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |연결된 템플릿에서 여러 공용 IP 주소를 만듭니다.  |

## <a name="next-steps"></a>다음 단계

- 자습서를 진행하려면 [자습서: 연결된 템플릿 배포](./deployment-tutorial-linked-template.md)를 참조하세요.
- 리소스의 배포 순서를 정의하는 방법에 대한 자세한 내용은 [ARM 템플릿에서 리소스를 배포하는 순서 정의](./resource-dependency.md)를 참조하세요.
- 하나의 리소스를 정의하되 해당 리소스의 여러 인스턴스를 만드는 방법을 알아보려면 [ARM 템플릿의 리소스 반복](copy-resources.md)을 참조하세요.
- 스토리지 계정에서 템플릿을 설정하고 SAS 토큰을 생성하는 절차는 [ 템플릿과 Azure PowerShell로 리소스 배포](deploy-powershell.md) 또는 [ 템플릿과 Azure CLI로 리소스 배포](deploy-cli.md)를 참조하세요.
