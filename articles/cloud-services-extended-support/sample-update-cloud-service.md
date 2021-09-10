---
title: Azure PowerShell 샘플 - Azure Cloud Service(추가 지원) 업데이트
description: Azure Cloud Service(추가 지원) 배포를 업데이트하기 위한 샘플 스크립트
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22d02ef7c395ae6f7cdbbf739ed5107436bd169c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751101"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Azure Cloud Service(추가 지원) 업데이트

이러한 샘플은 기존 Azure Cloud Service(추가 지원) 배포를 업데이트하는 다양한 방법을 다룹니다.

## <a name="add-an-extension-to-existing-cloud-service"></a>기존 Cloud Service에 확장 추가
아래 명령 세트는 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 기존 클라우드 서비스에 RDP 확장을 추가합니다.
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing cloud service extension object
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Cloud Service에서 모든 확장 제거
아래 명령 세트는 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 기존 클라우드 서비스에서 모든 확장을 제거합니다.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfile.Extension = @()
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Cloud Service에서 원격 데스크톱 확장 제거
아래 명령 세트는 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 기존 클라우드 서비스에서 RDP 확장을 제거합니다.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Name -ne "RDPExtension" }
# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>규모 확장/축소 역할 인스턴스
아래 명령 세트에서는 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스에 대한 역할 인스턴스 수를 확장 및 축소하는 방법을 보여줍니다.
```powershell
# Get existing cloud service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfile.Role | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfile.Role | Where-Object {$_.Name -eq "ContosoFrontend"}
$role.SkuCapacity -= 1

# Update cloud service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update cloud service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>다음 단계
Azure Cloud Services(추가 지원)에 대한 자세한 내용은 [Azure Cloud Services(추가 지원) 개요](overview.md)를 참조하세요.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support) 방문
