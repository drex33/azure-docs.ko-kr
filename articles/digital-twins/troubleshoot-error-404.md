---
title: '실패 한 서비스 요청 문제 해결: 오류 404 (하위 도메인을 찾을 수 없음)'
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에서 오류 404 (하위 도메인을 찾을 수 없음) 상태 응답을 진단 하 고 해결 하는 방법을 알아봅니다.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 9/23/2021
ms.openlocfilehash: 91125140632d689eaab18a74b7da75fe3c69800b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132249"
---
# <a name="troubleshooting-failed-service-request-error-404-sub-domain-not-found"></a>실패 한 서비스 요청 문제 해결: 오류 404 (하위 도메인을 찾을 수 없음)

이 문서에서는 Azure Digital Twins에 대한 서비스 요청에서 404 오류를 수신하는 원인 및 해결 단계를 설명합니다. 

## <a name="symptoms"></a>증상

이 오류는 인스턴스에서 다른 [Azure AD(Azure Active Directory) 테넌트](../active-directory/develop/quickstart-create-new-tenant.md)에 속한 서비스 주체 또는 사용자 계정으로 Azure Digital Twins 인스턴스에 액세스할 때 발생할 수 있습니다. ID에 올바른 [역할](concepts-security.md)이 할당된 것처럼 보이지만 API 요청은 `404 Sub-Domain not found` 오류 상태로 인해 실패합니다.

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 #1

Azure Digital Twins를 사용하려면 모든 인증 사용자가 Azure Digital Twins 인스턴스와 동일한 Azure AD 테넌트에 속해야 합니다.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>솔루션 #1

다른 테넌트의 각 페더레이션 ID가 Azure Digital Twins 인스턴스의 "home" 테넌트에서 **토큰** 을 요청하도록 하면 이 문제를 해결할 수 있습니다. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>솔루션 #2

코드에서 `DefaultAzureCredential` 클래스를 사용하고 토큰을 가져온 후에도 이 문제가 계속 발생하는 경우, 인증이 기본적으로 다른 유형으로 내려가더라도 테넌트를 명확히 하도록 `DefaultAzureCredential` 옵션에서 home 테넌트를 지정할 수 있습니다.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>다음 단계

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [Azure Digital Twins 솔루션에 대한 보안](concepts-security.md)
