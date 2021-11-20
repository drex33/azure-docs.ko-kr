---
title: Azure API Management에서 Service Fabric 백 엔드 설정 | Microsoft Docs
description: Azure Portal을 사용하여 Azure API Management에 Service Fabric 서비스 백 엔드를 만드는 방법
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5f2039b2ec4c4c1afc58d4fbaa1b204eaa9b3e7e
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869158"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Azure Portal을 사용하여 API Management에 Service Fabric 백 엔드 설정

이 문서에서는 Azure Portal을 사용하여 [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) 서비스를 사용자 지정 API 백 엔드로 구성하는 방법을 보여 줍니다. 데모를 위해 기본 상태 비저장 ASP.NET Core 신뢰할 수 있는 서비스를 Service Fabric 백 엔드로 설정하는 방법을 보여 줍니다.

배경 정보는 [API Management의 백 엔드](backends.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Windows를 사용자 지정 백 엔드로 실행하는 Service Fabric 클러스터에서 샘플 서비스를 구성하기 위한 필수 조건:

* **Windows 개발 환경** - [Visual Studio 2019](https://www.visualstudio.com), **Azure 개발**, **ASP.NET 및 웹 개발** 및 **.NET Core 플랫폼 간 개발** 워크로드를 설치합니다. 그런 후 [.NET 개발 환경](../service-fabric/service-fabric-get-started.md)을 설정합니다.

* **Service Fabric 클러스터** - [자습서: Azure 가상 네트워크에 Windows를 실행하는 Service Fabric 클러스터 배포](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md)를 참조하세요. 기존 X.509 인증서를 사용하여 클러스터를 만들거나 테스트 목적으로 자체 서명된 새 인증서를 만들 수 있습니다. 클러스터는 가상 네트워크에 만들어집니다.

* **샘플 Service Fabric 앱** - [Azure의 Service Fabric과 API Management 통합](../service-fabric/service-fabric-tutorial-deploy-api-management.md)에 설명된 대로 웹 API 앱을 만들고 Service Fabric 클러스터에 배포합니다.

    다음 단계는 기본 웹 API 프로젝트 템플릿을 사용하여 기본적인 상태 비저장 ASP.NET Core 신뢰할 수 있는 서비스를 만듭니다. 나중에 Azure API Management를 통해 이 서비스에 대한 HTTP 엔드포인트를 노출합니다.

    애플리케이션 이름을 기록해 둡니다(예: `fabric:/myApplication/myService`). 

* **API Management 인스턴스** - **프리미엄** 또는 **개발자** 계층 및 Service Fabric 클러스터와 동일한 지역에 있는 기존 또는 새 API Management 인스턴스입니다. 필요한 경우 [API Management 인스턴스를 만듭니다](get-started-create-service-instance.md).

* **가상 네트워크** - Service Fabric 클러스터용으로 만든 가상 네트워크에 API Management 인스턴스를 추가합니다. API Management를 사용하려면 가상 네트워크에서 전용 서브넷이 필요합니다.

  API Management 인스턴스에 대한 가상 네트워크 연결을 사용하도록 설정하는 단계는 [가상 네트워크에서 Azure API Management를 사용하는 방법](api-management-using-with-vnet.md)을 참조하세요.

## <a name="create-backend---portal"></a>백 엔드 만들기 - 포털

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>API Management에 Service Fabric 클러스터 인증서 추가

Service Fabric 클러스터 인증서는 클러스터와 연결된 Azure Key Vault에 저장되고 관리됩니다. 이 인증서를 API Management 인스턴스에 클라이언트 인증서로 추가합니다.

API Management 인스턴스에 인증서를 추가하는 단계는 [Azure API Management에서 클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](api-management-howto-mutual-certificates.md)을 참조하세요. 

> [!NOTE]   
> 키 자격 증명 모음 인증서를 참조하여 API Management에 인증서를 추가하는 것이 좋습니다. 

### <a name="add-service-fabric-backend"></a>Service Fabric 백 엔드 추가

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **API** 에서 **백 엔드** >  **+ 추가** 를 선택합니다.
1. 백 엔드 이름과 설명(선택 사항)을 입력합니다.
1. **유형** 에서 **Service Fabric** 을 선택합니다.
1. **런타임 URL** 에서 API Management가 요청을 전달할 Service Fabric 백 엔드 서비스의 이름을 입력합니다. 예: `fabric:/myApplication/myService`. 
1. **파티션 확인 재시도 최대 횟수** 에 0에서 10 사이의 숫자를 입력합니다.
1. Service Fabric 클러스터의 관리 엔드포인트를 입력합니다. 이 엔드포인트는 포트 `19080`에 있는 클러스터의 URL입니다(예: `https://mysfcluster.eastus.cloudapp.azure.com:19080`).
1. **클라이언트 인증서** 에서, 이전 섹션에서 API Management 인스턴스에 추가한 Service Fabric 클러스터 인증서를 선택합니다.
1. **관리 엔드포인트 권한 부여 메서드** 에서 TLS 통신을 위해 Service Fabric 클러스터 관리 서비스에서 사용하는 인증서의 지문 또는 서버 X509 이름을 입력합니다.
1. **인증서 체인 확인** 및 **인증서 이름 확인** 설정을 사용하도록 설정합니다.
1. **권한 부여 자격 증명** 에서 필요한 경우 자격 증명을 제공하여 Service Fabric에서 구성된 백 엔드 서비스에 연결합니다. 이 시나리오에 사용된 샘플 앱의 경우 권한 부여 자격 증명이 필요하지 않습니다.
1. **만들기** 를 선택합니다.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Service Fabric 백 엔드 만들기":::

## <a name="use-the-backend"></a>백 엔드 사용

사용자 지정 백 엔드를 사용하려면 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 정책을 사용하여 참조하세요. 이 정책은 들어오는 API 요청의 기본 백 엔드 서비스 기준 URL을 지정된 백 엔드로 변환합니다. 이 경우에는 Service Fabric 백 엔드로 변환합니다. 

`set-backend-service` 정책은 API 설정에 지정된 것과 다른 백 엔드로 들어오는 요청을 변환하는 기존 API에 대해 유용할 수 있습니다. 이 문서의 데모를 위해 테스트 API를 만들고 API 요청을 Service Fabric 백 엔드로 보내는 정책을 설정합니다. 

### <a name="create-api"></a>API 만들기

[API 수동 추가](add-api-manually.md)의 단계에 따라 빈 API를 만듭니다.

* API 설정에서 **웹 서비스 URL** 을 비워 둡니다.
* *패브릭* 과 같은 **API URL 접미사** 를 추가합니다.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="빈 API 만들기":::

### <a name="add-get-operation-to-the-api"></a>API에 GET 작업 추가

[Service Fabric 백 엔드 서비스 배포](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)에서 확인했듯이 Service Fabric 클러스터에 배포된 샘플 ASP.NET Core 서비스는 URL 경로 `/api/values`에서 단일 HTTP GET 작업을 지원합니다.

해당 경로의 기본 응답은 두 문자열로 된 JSON 배열입니다.

```json
["value1", "value2"]
```

API Management와 클러스터의 통합을 테스트하려면 해당 GET 작업을 경로 `/api/values`의 API에 추가합니다.

1. 이전 단계에서 만든 API를 선택합니다.
1. **+ 작업 추가** 를 선택합니다.
1. **프런트 엔드** 창에서 다음 값을 입력하고 **저장** 을 선택합니다.

     | 설정             | 값                             | 
    |---------------------|-----------------------------------|
    | **표시 이름**    | *백 엔드 테스트*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="API에 GET 작업 추가":::

### <a name="configure-set-backend-policy"></a>`set-backend` 정책 구성

테스트 API에 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 정책을 추가합니다.

1. **디자인** 탭의 **인바운드 처리** 섹션에서 코드 편집기( **</>** ) 아이콘을 선택합니다. 
1. **&lt;인바운드&gt;** 요소 내부에 커서를 놓습니다.
1. 정책 `set-service-backend` 문을 추가합니다. 
      * `backend-id`에서 Service Fabric 백 엔드의 이름을 대체합니다.

      * `sf-resolve-condition`는 서비스 위치를 다시 해결하고 요청을 다시 추가하기 위한 조건입니다. 재시도 횟수는 백 엔드를 구성할 때 설정되었습니다. 예를 들어:

      ```xml
      <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"/>
    ```
1. **저장** 을 선택합니다.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="set-backend-service 정책 구성":::

> [!NOTE]
> Service Fabric 클러스터에서 하나 이상의 노드가 다운되거나 제거되면 API Management 자동 알림을 받지 못하고 이러한 노드로 트래픽을 계속 보냅니다. 이러한 경우를 처리하려면 다음과 유사한 해결 조건을 구성합니다. `sf-resolve-condition="@((int)context.Response.StatusCode != 200 || context.LastError?.Reason == "BackendConnectionFailure" || context.LastError?.Reason == "Timeout")"`

### <a name="test-backend-api"></a>백 엔드 API 테스트

1. **테스트** 탭에서 이전 섹션에서 만든 **GET** 작업을 선택합니다.
1. **보내기** 를 선택합니다.

올바르게 구성된 경우 HTTP 응답은 HTTP 성공 코드를 표시하고 백 엔드 Service Fabric 서비스에서 반환된 JSON을 표시합니다.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Service Fabric 백 엔드 테스트":::

## <a name="next-steps"></a>다음 단계

* 요청을 백 엔드로 전달하기 위한 [정책 구성](api-management-advanced-policies.md) 방법 알아보기
* API Management [REST API](/rest/api/apimanagement/2021-01-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) 또는 [Azure Resource Manager 템플릿](../service-fabric/service-fabric-tutorial-deploy-api-management.md)을 사용하여 백 엔드를 구성할 수도 있습니다.
