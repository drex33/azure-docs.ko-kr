---
title: ARM 템플릿을 사용 하 여 개인 링크가 있는 Media Services 및 Storage 계정 만들기
titleSuffix: Azure Media Services
description: VNet에 대한 프라이빗 링크를 사용하여 Media Services 계정과 Storage 계정을 만듭니다. ARM(Azure Resource Manager) 템플릿도 두 프라이빗 링크에 대한 DNS를 설정합니다. 마지막으로, 사용자가 프라이빗 링크를 사용해 볼 수 있도록 템플릿이 VM을 만듭니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 6182fd0772d4e669a42f2895518139ab2748ae61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053537"
---
# <a name="create-a-media-services-and-storage-account-with-a-private-link-using-an-arm-template"></a>ARM 템플릿을 사용 하 여 개인 링크가 있는 Media Services 및 Storage 계정 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

VNet에 대한 프라이빗 링크를 사용하여 Media Services 계정과 Storage 계정을 만듭니다. ARM(Azure Resource Manager) 템플릿도 두 프라이빗 링크에 대한 DNS를 설정합니다. 마지막으로, 사용자가 프라이빗 링크를 사용해 볼 수 있도록 템플릿이 VM을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: Azure Portal을 사용하여 ARM 템플릿 만들기 및 배포](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

- Media Services의 경우 템플릿은 키 전송에 대한 프라이빗 링크만 설정합니다.
- VM에 대한 네트워크 보안 그룹은 만들지 않습니다.
- 스토리지 계정 또는 키 전송에 대해 네트워크 액세스 제어가 구성되지 않습니다.

템플릿은 아래 항목을 만듭니다.

- (평상시와 같이) Media Services 계정 및 Storage 계정
- 서브넷이 있는 VNet
- Media Services 계정 및 Storage 계정에 대해 다음 항목을 만듭니다.
  - 프라이빗 엔드포인트
  - 프라이빗 DNS 영역
  - 링크 간 링크(프라이빗 DNS 영역을 VNet에 연결하는 용도)
  - 프라이빗 DNS 영역 그룹(프라이빗 DNS 영역에서 DNS 레코드의 자동 생성을 트리거하는 용도)
- (공용 IP 주소와 네트워크 인터페이스가 있는) VM

[!INCLUDE [Azure Policy Media Services](includes/security-azure-policy-private-links.md)]

## <a name="azure-resource-manager-arm-template-for-private-link"></a>프라이빗 링크에 대한 ARM(Azure Resource Manager) 템플릿

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "vmAdminUsername": {
      "type": "string"
    },
    "vmAdminPassword": {
      "type": "secureString"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v3"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "storageAccountName": {
      "type": "string"
    },
    "mediaServicesAccountName": {
      "type": "string"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
    },
    {
      "type": "Microsoft.Media/mediaservices",
      "apiVersion": "2020-05-01",
      "name": "[parameters('mediaServicesAccountName')]",
      "location": "[parameters('location')]",
      "properties": {
        "storageAccounts": [
          {
            "type": "Primary",
            "id": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
          }
        ]
      },
      "identity": {
        "type": "SystemAssigned"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-08-01",
      "name": "myVnet",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/16"
          ]
        },
        "subnets": [
          {
            "name": "mySubnet",
            "properties": {
              "addressPrefix": "10.0.0.0/24",
              "privateEndpointNetworkPolicies": "Disabled"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "storagePrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "storagePrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
              "groupIds": [
                "blob"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.blob.core.windows.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/storageDnsZoneLink', 'privatelink.blob.core.windows.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/storagePrivateDnsZoneGroup', 'storagePrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'storagePrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "mediaServicesPrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "mediaServicesPrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
              "groupIds": [
                "keydelivery"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.media.azure.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/mediaServicesDnsZoneLink', 'privatelink.media.azure.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/mediaServicesPrivateDnsZoneGroup', 'mediaServicesPrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'mediaServicesPrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2020-08-01",
      "name": "publicIp",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[toLower(parameters('vmName'))]"
        }
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2020-08-01",
      "name": "vmNetworkInterface",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]"
              },
              "subnet": {
                "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
              }
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-12-01",
      "name": "myVM",
      "location": "[parameters('location')]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('vmAdminUsername')]",
          "adminPassword": "[parameters('vmAdminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "osDisk",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "diskSizeGB": 128
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
            }
          ]
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
      ]
    }
  ],
  "metadata": {
    "_generator": {
      "name": "bicep",
      "version": "0.3.126.58533",
      "templateHash": "2006367938138350540"
    }
  }
}
```