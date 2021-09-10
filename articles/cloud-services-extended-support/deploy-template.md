---
title: Azure Cloud Service(추가 지원) 배포 - 템플릿
description: ARM 템플릿을 사용하여 Azure Cloud Service(추가 지원)를 배포합니다.
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: de46eb9fca550d95caa8aa5d42449ab10d1bb9a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741124"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>ARM 템플릿을 사용하여 Cloud Service(추가 지원)를 배포합니다.

이 문서에서는 [ARM 템플릿](../azure-resource-manager/templates/overview.md)을 사용하여 Cloud Service(추가 지원) 배포를 만드는 방법에 대해 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

1. Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다.

2. [Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md) 또는 [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md)을 사용하여 새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우 이 단계는 선택 사항입니다.
 
3. [Azure Portal](../storage/common/storage-account-create.md?tabs=azure-portal) 또는 [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)을 사용하여 새 스토리지 계정을 만듭니다. 기존 스토리지 계정을 사용하는 경우 이 단계는 선택 사항입니다.

4. [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) 또는 [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container)을 사용하여 패키지(.cspkg) 및 서비스 구성(.cscfg) 파일을 스토리지 계정에 업로드합니다. 이 자습서의 뒷부분에서 ARM 템플릿에 추가할 두 파일의 SAS URI를 가져옵니다.

5. (선택 사항) 키 자격 증명 모음을 만들고 인증서를 업로드합니다.

    -  인증서를 클라우드 서비스에 연결하여 서비스와의 보안 통신을 실행할 수 있습니다. 인증서를 사용하려면 해당 지문을 서비스 구성(.cscfg) 파일에 지정하고 키 자격 증명 모음에 업로드해야 합니다. 키 자격 증명 모음은 [Azure Portal](../key-vault/general/quick-create-portal.md) 또는 [PowerShell](../key-vault/general/quick-create-powershell.md)을 통해 만들 수 있습니다.
    - 연결된 키 자격 증명 모음은 클라우드 서비스와 동일한 지역 및 구독에 있어야 합니다.
    - Cloud Services(추가 지원) 리소스가 Key Vault에서 인증서를 검색할 수 있도록 연결된 키 자격 증명 모음에 적절한 권한을 사용하도록 설정해야 합니다. 자세한 내용은 [인증서 및 Key Vault](certificates-and-key-vault.md)를 참조하세요.
    - 키 자격 증명 모음은 아래 단계에 표시된 ARM 템플릿의 OsProfile 섹션에서 참조해야 합니다.

## <a name="deploy-a-cloud-service-extended-support"></a>Cloud Service(추가 지원) 배포

> [!NOTE]
> [Azure Portal](https://portal.azure.com)을 통해 ARM 템플릿과 매개 변수 파일을 더 쉽고 빠르게 생성할 수 있습니다. [생성된 ARM 템플릿을 포털에서 다운로드](generate-template-portal.md)하여 Powershell을 통해 클라우드 서비스를 만들 수 있습니다.
 
1. 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 서비스 구성(.cscfg) 파일의 참조와 일치해야 합니다. 기존 가상 네트워크를 사용하는 경우 ARM 템플릿에서 이 섹션을 생략합니다.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     새 가상 네트워크를 만드는 경우 다음을 `dependsOn` 섹션에 추가하여 클라우드 서비스를 만들기 전에 플랫폼에서 가상 네트워크를 만들도록 합니다.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. 공용 IP 주소를 만들고, 필요에 따라 공용 IP 주소의 DNS 레이블 속성을 설정합니다. 고정 IP를 사용하는 경우 서비스 구성(.cscfg) 파일에서 예약된 IP로 참조해야 합니다. 기존 IP 주소를 사용하는 경우 이 단계를 건너뛰고 IP 주소 정보를 ARM 템플릿의 부하 분산 장치 구성 설정에 직접 추가합니다.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     새 IP 주소를 만드는 경우 다음을 `dependsOn` 섹션에 추가하여 클라우드 서비스를 만들기 전에 플랫폼에서 IP 주소를 만들도록 합니다.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. 템플릿 내에서 가상 네트워크 또는 공용 IP를 배포하는 경우 적절한 `dependsOn` 참조를 추가하여 클라우드 서비스(추가 지원) 개체를 만듭니다. 

    ```json
    {
      "apiVersion": "2021-03-01",
      "type": "Microsoft.Compute/cloudServices",
      "name": "[variables('cloudServiceName')]",
      "location": "[parameters('location')]",
      "tags": {
        "DeploymentLabel": "[parameters('deploymentLabel')]",
        "DeployFromVisualStudio": "true"
      },
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
        "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
      ],
      "properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]"
      }
    }
    ```
4. 클라우드 서비스에 대한 네트워크 프로필 개체를 만들고 공용 IP 주소를 부하 분산 장치의 프런트 엔드에 연결합니다. 부하 분산 장치는 플랫폼에서 자동으로 만들어집니다. 

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

5. 키 자격 증명 모음 참조를 ARM 템플릿의  `OsProfile` 섹션에 추가합니다. Key Vault는 Cloud Services(추가 지원)에 연결된 인증서를 저장하는 데 사용됩니다. 인증서를 Key Vault에 추가한 다음, 서비스 구성(.cscfg) 파일에서 인증서 지문을 참조합니다. 또한 Cloud Services(추가 지원) 리소스가 Key Vault에서 비밀로 저장된 인증서를 검색할 수 있도록 포털에서 '배포를 위한 Azure Virtual Machines'에 Key Vault '액세스 정책'을 사용하도록 설정해야 합니다. 키 자격 증명 모음은 클라우드 서비스와 동일한 지역 및 구독에 있어야 하며 고유한 이름을 사용해야 합니다. 자세한 내용은 [Cloud Services(추가 지원)에서 인증서 사용](certificates-and-key-vault.md)을 참조하세요.
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault는 키 자격 증명 모음에 대한 ARM 리소스 ID입니다. 이 정보는 키 자격 증명 모음의 속성 섹션에서 리소스 ID를 찾아서 확인할 수 있습니다.
    > - certificateUrl은 **비밀 식별자** 로 레이블이 지정된 키 자격 증명 모음의 인증서로 이동하여 확인할 수 있습니다.  
   >  - certificateUrl은 https://{keyvault-endpoin}/secrets/{secretname}/{secret-id} 형식이어야 합니다.

6. 역할 프로필을 만듭니다. ARM 템플릿의 서비스 구성(.cscfg), 서비스 정의(.csdef) 및 역할 프로필 섹션에서 역할 수, 역할 이름, 각 역할의 인스턴스 수 및 크기가 동일한지 확인합니다.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

7. (선택 사항) 확장을 클라우드 서비스에 추가하는 확장 프로필을 만듭니다. 다음 예제에서는 원격 데스크톱 및 Windows Azure 진단 확장을 추가합니다.
   > [!Note] 
   > 원격 데스크톱의 암호는 8-123자 사이이고 다음과 같은 암호 복잡성 요구 사항 중 3개 이상을 만족해야 합니다. 1) 대문자 포함 2) 소문자 포함 3) 숫자 포함 4) 특수 문자 포함 5) 제어 문자 사용 불가

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

8. 전체 템플릿을 검토합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

9. 템플릿 및 매개 변수 파일(템플릿 파일의 매개 변수 정의)을 배포하여 클라우드 서비스(확장 지원) 배포를 만듭니다. 필요에 따라 이러한 [샘플 템플릿](https://github.com/Azure-Samples/cloud-services-extended-support)을 참조하세요.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>다음 단계 

- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support)를 방문합니다.
