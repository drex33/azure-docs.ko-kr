---
title: Azure Cloud Services용 CSCFG/CSDEF를 통해 SKU 정보 재정의(추가 지원)
description: 이 문서에서는 Azure Cloud Services(추가 지원)의 .cscfg 및 .csdef 파일에서 SKU 정보를 재정의하는 방법을 설명합니다.
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 31ff47cd4e110e62769678836bdd803b71369e0b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437882"
---
# <a name="override-sku-settings-in-cscfg-and-csdef-files-for-cloud-services-extended-support"></a>Cloud Services에 대한 .cscfg 및 .csdef 파일의 SKU 설정 재정의(추가 지원)

이 문서에서는 **allowModelOverride** 속성을 사용하여 Azure Cloud Services에서 역할 크기 및 인스턴스 수를 업데이트하는 방법을 설명합니다. 이 속성을 사용하는 경우 서비스 구성(.cscfg) 및 서비스 정의(.csdef) 파일을 업데이트할 필요가 없습니다. 따라서 클라우드 서비스를 다시 패키지하고 다시 배포하지 않고 스케일 업, 스케일 다운, 스케일 인 또는 스케일 아웃할 수 있습니다.

## <a name="set-the-allowmodeloverride-property"></a>allowModelOverride 속성 설정
**allowModelOverride** 속성을 `true` 또는 `false`로 설정할 수 있습니다. 
* **allowModelOverride** 가 `true`로 설정된 경우 API 호출은 .csdef 및 .cscfg 파일을 사용하여 값의 유효성을 검사하지 않고 클라우드 서비스의 역할 크기 및 인스턴스 수를 업데이트합니다. 
   > [!Note]
   > .cscfg 파일이 역할 인스턴스 수를 반영하도록 업데이트됩니다. .csdef 파일(.cspkg 내에 포함됨)은 이전 값을 유지합니다.

* **allowModelOverride** 가 `false`인 경우 역할 크기 및 인스턴스 수 값이 각각 .csdef 및 .cscfg 파일의 값과 일치하지 않으면 API 호출에서 오류를 throw합니다.

기본값은 `false`입니다. 속성이 `true`로 설정된 후 `false`로 다시 설정되는 경우 .csdef 및 .cscfg 파일의 유효성이 다시 검사됩니다.

다음 샘플에서는 ARM(Azure Resource Manager) 템플릿, PowerShell 또는 SDK를 사용하여 **allowModelOverride** 속성을 설정하는 방법을 보여 줍니다.

### <a name="arm-template"></a>ARM 템플릿
여기서 **allowModelOverride** 속성을 `true`로 설정하면 `roleProfile` 섹션에 정의된 역할 속성으로 클라우드 서비스를 업데이트합니다.
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
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

```
### <a name="powershell"></a>PowerShell
새 `New-AzCloudService` cmdlet에서 `AllowModelOverride` 스위치를 설정하면 역할 프로필에 정의된 SKU 속성으로 클라우드 서비스를 업데이트합니다.
```powershell
New-AzCloudService ` 
-Name "ContosoCS" ` 
-ResourceGroupName "ContosOrg" ` 
-Location "East US" `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK)
`AllowModelOverride` 변수를 `true`로 설정하면 역할 프로필에 정의된 SKU 속성으로 클라우드 서비스를 업데이트합니다.

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate("ContosOrg", "ContosoCS", cloudService);
```
### <a name="azure-portal"></a>Azure portal
Azure Portal은 **allowModelOverride** 속성을 사용하여 .csdef 및 .cscfg 파일의 역할 크기 및 인스턴스 수를 재정의할 수 없습니다. 


## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 봅니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 봅니다.
