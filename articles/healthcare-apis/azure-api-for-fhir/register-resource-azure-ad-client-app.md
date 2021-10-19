---
title: Azure AD에서 리소스 앱 등록 - Azure API for FHIR
description: 클라이언트 애플리케이션이 인증 시 리소스에 대한 액세스를 요청할 수 있도록 Azure Active Directory 리소스(또는 API) 앱을 등록합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: e8453a2b82051dc7e63d1193947747d839478427
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436063"
---
# <a name="register-a-resource-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Azure Active Directory 리소스 애플리케이션을 등록하여 Azure API for FHIR

이 문서에서는 Azure Active Directory 리소스(또는 API) 애플리케이션을 등록하는 방법을 알아봅니다. 리소스 애플리케이션은 FHIR 서버 API 자체의 Azure Active Directory 표현이며 클라이언트 애플리케이션은 인증 시 리소스에 대한 액세스를 요청할 수 있습니다. 리소스 애플리케이션은 OAuth 구문에서 *대상* 그룹이라고도 합니다.

## <a name="azure-api-for-fhir"></a>FHIR용 Azure API

Azure API for FHIR 사용하는 경우 서비스를 배포할 때 리소스 애플리케이션이 자동으로 만들어집니다. 애플리케이션을 배포하는 것과 동일한 Azure Active Directory 테넌트에서 Azure API for FHIR 사용하는 한 이 방법 가이드를 건너뛰고 대신 Azure API for FHIR 배포하여 시작할 수 있습니다.

다른 Azure Active Directory 테넌트(구독과 연결되지 않음)를 사용하는 경우 PowerShell을 사용하여 Azure API for FHIR 리소스 애플리케이션을 테넌트로 가져올 수 있습니다.

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

또는 Azure CLI 사용할 수 있습니다.

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Azure용 FHIR 서버

Azure용 오픈 소스 FHIR 서버를 사용하는 경우 GitHub [리포지션의](https://github.com/microsoft/fhir-server/blob/master/docs/Register-Resource-Application.md) 단계에 따라 리소스 애플리케이션을 등록합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory 리소스 애플리케이션을 등록하는 방법을 배웠습니다. 다음으로 기밀 클라이언트 애플리케이션을 등록합니다.
 
>[!div class="nextstepaction"]
>[기밀 클라이언트 애플리케이션 등록](register-confidential-azure-ad-client-app.md)