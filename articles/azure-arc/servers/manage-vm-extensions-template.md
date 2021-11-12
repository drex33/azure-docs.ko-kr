---
title: Azure Resource Manager 템플릿을 사용하여 VM 확장 사용하기
description: 이 문서에서는 Azure Resource Manager 템플릿을 통하여 하이브리드 클라우드 환경에서 실행 중인 Azure Arc 지원 서버에 가상 머신 확장을 배포하는 방법을 설명합니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d0d23aa44de29eb71f64e80b8422a826ef7fa0f4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348020"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>ARM 템플릿을 사용하여 Azure VM 확장 사용하기

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Arc 지원 서버가 지원하는 Azure VM 확장을 배포하는 방법을 보여 줍니다.

Azure Resource Manager 템플릿에 VM 확장을 추가하고 템플릿 배포를 통해 실행할 수 있습니다. Azure Arc 지원되는 서버에서 지원되는 VM 확장을 사용하면 linux 또는 Azure PowerShell 사용하여 Windows 머신에 지원되는 VM 확장을 배포할 수 있습니다. 아래 각각의 샘플에는 템플릿에 제공할 샘플 값이 입력된 템플릿 파일과 매개 변수 파일이 들어 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리하고 프로세스할 수 있지만, 설치는 순차적으로 진행됩니다. 첫 번째 확장 설치가 완료되면 다음 확장을 설치하려고 시도합니다.

> [!NOTE]
> Azure Arc 지원 서버는 Azure 가상 머신에 대한 VM 확장의 배포 및 관리를 지원하지 않습니다. Azure VMs에 관해서는 다음 [VM 확장 개요](../../virtual-machines/extensions/overview.md) 문서를 참조하세요.

## <a name="deploy-the-log-analytics-vm-extension"></a>Log Analytics VM 확장 배포하기

Log Analytics 에이전트를 간편하게 배포하기 위하여 해당 에이전트를 Windows나 Linux에 설치할 수 있는 다음 샘플이 제공됩니다.

### <a name="template-file-for-linux"></a>Linux용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Windows용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

디스크에 해당 템플릿 파일과 매개 변수 파일을 저장하고 배포에 맞는 적절한 값으로 매개 변수 파일을 편집합니다. 그러고 나면 다음 명령을 통하여 해당 확장을 하나의 리소스 그룹 안에서 연결된 모든 컴퓨터에 설치할 수 있습니다. 해당 명령은 *TemplateFile* 매개 변수를 사용하여 해당 템플릿을 지정하며 *TemplateParameterFile* 매개 변수를 사용하여 매개 변수 및 매개 변숫값이 들어 있는 파일을 지정합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>사용자 지정 스크립트 확장을 배포하기

사용자 지정 스크립트 확장을 사용하기 위하여 Windows와 Linux에서 실행되는 다음 샘플이 제공됩니다. 사용자 지정 스크립트 확장에 익숙하지 않다면, [Windows용 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-windows.md)이나 [Linux용 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-linux.md)을 참조하세요. 하이브리드 컴퓨터에 해당 확장을 사용할 때 반드시 알아야 하는 몇 가지 특성이 있습니다.

* Azure VM 사용자 지정 스크립트 확장을 사용하는 지원되는 운영 체제 목록은 Azure Arc 지원 서버에 적용할 수 없습니다. Azure Arc 지원되는 서버에 대해 지원되는 OS 목록은 [여기에서](agent-overview.md#supported-operating-systems)찾을 수 있습니다.

* Azure Virtual Machine Scale Sets 또는 클래식 VM과 관련된 구성 세부 정보는 적용할 수 없습니다.

* 컴퓨터에 외부적으로 스크립트를 다운로드하여야 하며 프록시 서버를 통하여서만 통신이 가능한 경우, [연결된 컴퓨터 에이전트를 구성](manage-agent.md#update-or-remove-proxy-settings)하여 프록시 서버 환경 변수를 설정하여야 합니다.

사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 해당 구성은 Linux와Windows 양쪽의 하이브리드 컴퓨터용으로 아래 제공되는 Azure Resource Manager 템플릿에서 지정됩니다.

### <a name="template-file-for-linux"></a>Linux용 템플릿 파일

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Windows용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>종속성 에이전트 확장 배포하기

Azure Monitor 종속성 에이전트 확장을 사용하기 위하여 Windows와 Linux에서 실행되는 다음 샘플이 제공됩니다. 종속성 에이전트 확장에 익숙하지 않다면, [Azure Monitor 에이전트 개요](../../azure-monitor/agents/agents-overview.md#dependency-agent)를 참조하세요.

### <a name="template-file-for-linux"></a>Linux용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Windows용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-deployment"></a>템플릿 배포

디스크에 템플릿 파일을 저장합니다. 그런 뒤에 다음 명령을 사용하여 연결된 컴퓨터에 해당 확장을 배포할 수 있습니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM 확장 배포하기(미리 보기)

다음 JSON은 키 자격 증명 모음 VM 확장에 대한 스키마를 보여 줍니다(미리 보기). 해당 확장에는 보호됨 설정이 필요 없습니다. 모든 설정이 퍼블릭 정보로 간주됩니다. 해당 확장에는 모니터링되는 인증서, 폴링 빈도, 대상 인증서 저장소 목록이 필요합니다. 특히:

### <a name="template-file-for-linux"></a>Linux용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForLinux')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ignored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          },
          "authenticationSettings": {
                "msiEndpoint":  <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId":  <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

### <a name="template-file-for-windows"></a>Windows용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "linkOnRenewal":{
          "type": "bool"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "requireInitialSync":{
          "type": "bool"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForWindows')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": "3600",
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net"
        },
        "authenticationSettings": {
                "msiEndpoint": <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId": <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

> [!NOTE]
> 관찰된 인증서 URL은 `https://myVaultName.vault.azure.net/secrets/myCertName` 형식이어야 합니다.
>
> `/secrets` 경로가 프라이빗 키를 포함하여 전체 인증서를 반환하지만 `/certificates` 경로에서는 반환하지 않기 때문입니다. 인증서에 대한 자세한 내용은 여기에서 찾을 수 있습니다. [Key Vault 인증서](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>템플릿 배포

디스크에 템플릿 파일을 저장합니다. 그런 뒤에 다음 명령을 사용하여 연결된 컴퓨터에 해당 확장을 배포할 수 있습니다.

> [!NOTE]
> VM 확장에는 키 자격 증명 모음에 인증하기 위하여 할당된 시스템 할당 ID가 필요합니다. Windows 및 Linux Azure Arc 지원 서버에 [대해 관리 ID를 사용하여](managed-identity-authentication.md) Key Vault 인증하는 방법을 참조하세요.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-microsoft-defender-for-cloud-integrated-scanner"></a>클라우드용 Microsoft Defender 통합 스캐너 배포

클라우드용 Microsoft Defender 통합 스캐너 확장을 사용하기 위해 Windows 및 Linux에서 실행하기 위해 다음 샘플이 제공됩니다. 통합 스캐너에 익숙하지 않은 경우 하이브리드 [머신에 대한 클라우드용 Microsoft Defender 취약성 평가 솔루션 개요를](../../security-center/deploy-vulnerability-assessment-vm.md) 참조하세요.

### <a name="template-file-for-windows"></a>Windows용 템플릿 파일

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.HybridCompute/machines/providers/serverVulnerabilityAssessments",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>Linux용 템플릿 파일

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.HybridCompute/machines/providers/serverVulnerabilityAssessments",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-deployment"></a>템플릿 배포

디스크에 템플릿 파일을 저장합니다. 그런 뒤에 다음 명령을 사용하여 연결된 컴퓨터에 해당 확장을 배포할 수 있습니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](manage-vm-extensions-powershell.md)을 사용하여 [Azure Portal](manage-vm-extensions-portal.md)이나 [Azure CLI](manage-vm-extensions-cli.md)에서 VM 확장을 배포하고 관리하며 제거할 수 있습니다.

* 문제 해결 정보는 [VM 확장 문제 해결 안내](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
