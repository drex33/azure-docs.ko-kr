---
title: '빠른 시작: PowerShell을 사용하여 Azure API for FHIR 배포'
description: 이 빠른 시작에서는 PowerShell을 사용하여 Azure API for FHIR을 배포하는 방법을 알아봅니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: zxue
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 4c0e0467d2a5381f9cb562a93c9e709f6c845101
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071809"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure API for FHIR 배포

이 빠른 시작에서는 PowerShell을 사용하여 Azure API for FHIR을 배포하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Azure API for FHIR 리소스 공급자 등록

`Microsoft.HealthcareApis` 리소스 공급자가 구독에 아직 등록되지 않은 경우 다음을 사용하여 등록할 수 있습니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Azure 리소스 그룹 만들기

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR 배포

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> 설치한 `Az` PowerShell 모듈의 버전에 따라 프로비저닝된 FHIR 서버는 [로컬 RBAC](configure-local-rbac.md)를 사용하도록 구성되어 있으며 현재 로그인된 PowerShell 사용자가 배포된 FHIR 서비스에 대해 허용된 ID 개체 ID 목록에 설정되도록 할 수 있습니다. 앞으로 데이터 평면 역할을 할당하기 위해 [Azure RBAC를 사용](configure-azure-rbac.md)하는 것이 좋습니다. Azure RBAC 모드를 사용하도록 설정하려면 배포 후에 이 사용자 개체 ID를 삭제해야 할 수도 있습니다.


## <a name="fetch-capability-statement"></a>Fetch 기능 문

FHIR 기능 문을 가져와 Azure API for FHIR 계정이 실행 중인지 확인할 수 있습니다.

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹을 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Azure API for FHIR을 구독에 배포했습니다. Azure API for FHIR에서 추가 설정을 설정하려면 추가 설정 방법 가이드로 이동합니다. Azure API for FHIR 사용을 시작할 준비가 되면 애플리케이션을 등록하는 방법에 대해 자세히 읽어보세요.

>[!div class="nextstepaction"]
>[Azure API for FHIR의 추가 설정](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[애플리케이션 등록 개요](fhir-app-registration.md)
