---
title: Azure PowerShell 스크립트 샘플 - APIM 서비스 만들기 | Microsoft Docs
description: APIM(API Management) 서비스를 만드는 방법에 대해 알아봅니다. 샘플 스크립트를 참조하고 사용 가능한 추가 리소스를 확인합니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.topic: sample
ms.date: 11/16/2017
ms.author: danlep
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f6740e89197b8a2e11285a8c6ea41b593c3ed638
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641335"
---
# <a name="create-an-api-management-service"></a>API Management 서비스 만들기

이 샘플 스크립트는 개발자 SKU API Management 서비스를 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 1.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/api-management/create-apim-service/create_apim_service.ps1 "Create a service")]

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure API Management에 대한 추가 Azure PowerShell 샘플은 [PowerShell 샘플](../powershell-samples.md)에서 찾을 수 있습니다.
