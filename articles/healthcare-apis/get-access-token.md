---
title: Azure CLI 또는 Azure PowerShell 사용하여 액세스 토큰을 얻습니다.
description: 이 문서에서는 Azure CLI 또는 Azure PowerShell 사용하여 의료 API에 대한 액세스 토큰을 가져오는 방법을 설명합니다.
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d350669abca87156cc5e735151f4a905d98745a8
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814657"
---
# <a name="get-access-token-using-azure-cli-or-azure-powershell"></a>Azure CLI 또는 Azure PowerShell 사용하여 액세스 토큰을 얻습니다.

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 PowerShell 및 Azure CLI 사용하여 FHIR 서비스 및 DICOM 서비스에 대한 액세스 토큰을 가져오는 방법을 알아봅니다. 의료 API를 시작하는 방법에 대한 자세한 내용은 [FHIR을 시작하는 방법]() 또는 [DICOM을 시작하는 방법을 참조하세요.]() 

---
## <a name="obtain-a-token-for-the-fhir-service"></a>FHIR 서비스에 대한 토큰 얻기

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

## <a name="use-the-token-with-the-fhir-service"></a>FHIR 서비스에서 토큰 사용

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

## <a name="obtain-a-token-for-the-dicom-service"></a>DICOM 서비스에 대한 토큰 얻기

DICOM 서비스는 `resource` `Audience` DICOM 서버 의 URI와 같은 URI가 있는 또는 를  `https://dicom.healthcareapis.azure.com` 사용합니다. 토큰을 가져오고 다음 명령을 사용하여 변수()에 저장할 수 있습니다. `$token`


```Azure CLICopy
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-the-tokenb-with-the-dicom-service"></a>DICOM 서비스에서 토큰b 사용

```Azure CLICopy
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>다음 단계

- [Postman을 사용하여 FHIR에 액세스](use-postman.md)
- [FHIR Sing Rest Client에 액세스](using-rest-client.md)
- [cUrl을 사용하여 DICOM에 액세스](dicom/dicomweb-standard-apis-curl.md)

