---
title: Azure API Management 백 엔드 | Microsoft Docs
description: API Management의 사용자 지정 백 엔드에 대해 알아보기
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 09/21/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 008c5624f621f8598963a8f94261a9e369d0ca0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647122"
---
# <a name="backends-in-api-management"></a>API Management의 백 엔드

API Management의 *백 엔드*(또는 *API 백 엔드*)는 프런트 엔드 API와 해당 작업을 구현하는 HTTP 서비스입니다.

특정 API를 가져올 경우 API Management는 자동으로 API 백 엔드를 구성합니다. 예를 들어 가져올 때 백엔드를 구성 하는 API Management.
* [Openapi 사양](import-api-from-oas.md)입니다.
* [SOAP API](import-soap-api.md).
* HTTP로 트리거된 [azure 함수 앱](import-function-app-as-api.md) 또는 [논리 앱](import-logic-app-as-api.md)과 같은 azure 리소스

또한 API Management는 다음과 같은 API 백 엔드로 다른 Azure 리소스를 사용 하도록 지원 합니다.
* [Service Fabric 클러스터](how-to-configure-service-fabric-backend.md)입니다.
* 사용자 지정 서비스입니다. 

백 엔드 서비스에 대 한 요청의 자격 증명을 인증 하 고 API 작업을 정의 하려면 사용자 지정 백 엔드에 추가 구성이 필요 합니다. Azure Portal에서 또는 Azure Api 나 도구를 사용 하 여 사용자 지정 백 엔드를 구성 하 고 관리 합니다.

백 엔드를 만들면 API에서 백 엔드 URL을 참조할 수 있습니다. [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 정책을 사용하여 들어오는 API 요청을 해당 API의 기본 백 엔드 대신 사용자 지정 백 엔드로 리디렉션합니다.

## <a name="benefits-of-backends"></a>백 엔드 이점

사용자 지정 백 엔드에는 다음과 같은 여러 가지 이점이 있습니다.

* 백 엔드 서비스에 대 한 정보를 추상화 하 고 Api와 향상 된 거 버 넌 스를 승격 합니다.  
* 기존 API에 대 한 변환 정책을 구성 하 여 쉽게 사용 됩니다.
* 는 [명명 된 값](api-management-howto-properties.md) 이 헤더 또는 쿼리 매개 변수 인증에 대해 구성 된 경우 API Management 기능을 활용 하 여 Azure Key Vault에서 비밀을 유지 합니다.

## <a name="limitation"></a>제한 사항

**개발자** 및 **Premium** 계층의 경우 [내부 가상 네트워크](api-management-using-with-internal-vnet.md) 에 배포 된 API Management 인스턴스는 `BackendConnectionFailure` 게이트웨이 끝점 url과 백 엔드 url이 같을 때 HTTP 500 오류를 throw 할 수 있습니다. 이러한 제한 사항이 발생 하는 경우 기술 Community 블로그의 자체 연결 된 [API Management 요청 제한 사항 문서 내부 가상 네트워크 모드](https://techcommunity.microsoft.com/t5/azure-paas-blog/self-chained-apim-request-limitation-in-internal-virtual-network/ba-p/1940417) 문서를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 [Service Fabric 백 엔드](how-to-configure-service-fabric-backend.md)를 설정합니다.
* API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) 또는 [Azure Resource Manager 템플릿](../service-fabric/service-fabric-tutorial-deploy-api-management.md)을 사용하여 백 엔드를 구성할 수도 있습니다.

