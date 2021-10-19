---
title: Azure CLI 사용하여 액세스 토큰 얻기 - Azure API for FHIR
description: 이 문서에서는 Azure CLI 사용하여 Azure API for FHIR 대한 액세스 토큰을 가져오는 방법을 설명합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: b955fd521224197a3505bd24f7ff0d02ec95fb5d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748943"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Azure CLI를 사용하여 Azure API for FHIR 액세스 토큰 가져오기

이 문서에서는 Azure CLI 사용하여 Azure API for FHIR 대한 액세스 토큰을 가져오는 방법을 알아봅니다. Azure API for FHIR [프로비전할](fhir-paas-portal-quickstart.md)때 서비스에 액세스할 수 있는 사용자 또는 서비스 주체 집합을 구성합니다. 사용자 개체 ID가 허용되는 개체 ID 목록에 있는 경우 Azure CLI를 사용하여 가져온 토큰을 통해 서비스에 액세스할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>토큰 얻기

Azure API for FHIR `resource` `Audience` FHIR 서버 의 URI와 같은 URI가 있는 또는 를 `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` 사용합니다. 토큰을 가져오고 다음 명령을 사용하여 변수()에 저장할 수 있습니다. `$token`

```azurecli-interactive
$token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Azure API for FHIR 함께 사용

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure CLI 사용하여 Azure API for FHIR 대한 액세스 토큰을 가져오는 방법을 배웠습니다. Postman을 사용하여 FHIR API에 액세스하는 방법을 알아보려면 Postman 자습서로 이동합니다.

>[!div class="nextstepaction"]
>[Postman을 사용하여 FHIR 서비스에 액세스](./../use-postman.md)
