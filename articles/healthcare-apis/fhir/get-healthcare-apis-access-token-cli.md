---
title: Azure CLI 또는 Azure PowerShell 사용하여 액세스 토큰 얻기 - FHIR 서비스
description: 이 문서에서는 Azure CLI 또는 Azure PowerShell 사용하여 FHIR 서비스에 대한 액세스 토큰을 가져오는 방법을 설명합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 672089ac061430121916efff67280c08c1c6943f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619443"
---
# <a name="get-access-token-for-fhir-service-using-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell 사용하여 FHIR 서비스에 대한 액세스 토큰을 얻습니다.

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 문서에서는 Azure CLI 사용하여 Azure Healthcare API(여기서는 FHIR 서비스라고 함)에서 FHIR 서비스에 대한 액세스 토큰을 가져오는 방법을 알아봅니다. [FHIR 서비스를 프로비전할](fhir-portal-quickstart.md)때 서비스에 액세스할 수 있는 사용자 또는 서비스 주체 집합을 구성합니다. 사용자 개체 ID가 허용되는 개체 ID 목록에 있는 경우 Azure CLI를 사용하여 가져온 토큰을 통해 서비스에 액세스할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

이 문서에서는 Azure PowerShell 사용하여 Azure Healthcare API(여기서는 FHIR 서비스라고 함)에서 FHIR 서비스에 대한 액세스 토큰을 가져오는 방법을 알아봅니다. [FHIR 서비스를 프로비전할](fhir-portal-quickstart.md)때 서비스에 액세스할 수 있는 사용자 또는 서비스 주체 집합을 구성합니다. 사용자 개체 ID가 허용된 개체 ID 목록에 있는 경우 Azure PowerShell 사용하여 가져온 토큰을 사용하여 서비스에 액세스할 수 있습니다.

[!INCLUDE [azure-powershell-requirements.md](../../../includes/azure-powershell-requirements.md)]

---
## <a name="obtain-a-token"></a>토큰 얻기

FHIR 서비스는 `resource` `Audience` FHIR 서버 의 URI와 같은 URI가 있는 또는 를 `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 사용합니다. 토큰을 가져오고 다음 명령을 사용하여 변수()에 저장할 수 있습니다. `$token`

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-with-fhir-service"></a>FHIR 서비스와 함께 사용

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

---

>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR API에 액세스](../use-postman.md)
