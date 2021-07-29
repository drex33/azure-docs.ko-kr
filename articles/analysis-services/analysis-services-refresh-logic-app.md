---
title: Logic Apps를 사용하여 Azure Analysis Services 모델 새로 고침 | Microsoft Docs
description: 이 문서에서는 Azure Logic Apps를 사용하여 Azure Analysis Services에 대한 비동기 새로 고침을 코딩하는 방법을 설명합니다.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019089"
---
# <a name="refresh-with-logic-apps"></a>Logic Apps를 사용하여 새로 고침

Logic Apps 및 REST 호출을 사용하면 쿼리 스케일 아웃을 위한 읽기 전용 복제본 동기화를 포함하여 Azure Analysis 테이블 형식 모델에서 자동화된 데이터 새로 고침 작업을 수행할 수 있습니다.

Azure Analysis Services에서 REST API를 사용하는 방법에 대한 자세한 내용은 [REST API를 사용한 비동기 새로 고침](analysis-services-async-refresh.md)을 참조하세요.

## <a name="authentication"></a>인증

모든 호출은 유효한 Azure Active Directory(OAuth 2) 토큰을 사용하여 인증되어야 합니다.  이 문서의 예제에서는 서비스 주체(SPN)를 사용하여 Azure Analysis Services에 인증합니다. 자세한 내용은 [Azure Portal에서 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

## <a name="design-the-logic-app"></a>논리 앱 설계

> [!IMPORTANT]
> 다음 예제에서는 Azure Analysis Services 방화벽을 사용할 수 없다고 가정합니다. 방화벽을 사용할 수 있는 경우 Azure Analysis Services 방화벽의 승인된 목록에 요청 개시 장치의 공용 IP 주소를 추가해야 합니다. 지역별 Azure Logic Apps IP 범위에 대한 자세한 내용은 [Azure Logic Apps의 제한 및 구성 정보](../logic-apps/logic-apps-limits-and-config.md#configuration)를 참조하세요.

### <a name="prerequisites"></a>필수 구성 요소

#### <a name="create-a-service-principal-spn"></a>SPN(서비스 사용자 이름) 만들기

서비스 사용자 만들기에 대해 알아보려면 [Azure Portal을 사용하여 서비스 사용자 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

#### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services에서 권한 구성
 
직접 만든 서비스 사용자에게는 서버에 대한 서버 관리자 권한이 있어야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

### <a name="configure-the-logic-app"></a>논리 앱 구성

이 예제에서 논리 앱은 HTTP 요청을 받을 때 트리거되도록 설계되었습니다. 이렇게 하면 Azure Data Factory와 같은 오케스트레이션 도구를 사용하여 Azure Analysis Services 모델 새로 고침을 트리거할 수 있습니다.

논리 앱을 만든 후에는 다음을 수행합니다.

1. 논리 앱 디자이너에서 **HTTP 요청을 수신하는 경우** 로 첫 번째 작업을 선택합니다.

   ![HTTP 수신 작업 추가](./media/analysis-services-async-refresh-logic-app/1.png)

이 단계는 논리 앱이 저장된 후 HTTP POST URL로 채웁니다.

2. 새 단계를 추가하고 **HTTP** 를 검색합니다.  

   ![“HTTP” 타일이 선택된 “작업 선택” 섹션 스크린샷](./media/analysis-services-async-refresh-logic-app/9.png)

   ![“HTTP - HTTP” 타일이 선택된 “HTTP” 창 스크린샷](./media/analysis-services-async-refresh-logic-app/10.png)

3. **HTTP** 를 선택하여 이 작업을 추가합니다.

   ![HTTP 작업 추가](./media/analysis-services-async-refresh-logic-app/2.png)

HTTP 작업을 다음과 같이 구성합니다.

|속성  |값  |
|---------|---------|
|**방법**     |POST         |
|**URI**     | https://‘서버 지역’/servers/‘aas 서버 이름’/models/‘데이터베이스 이름’/refreshes   <br /> <br /> 예: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**헤더**     |   Content-Type, application/json <br /> <br />  ![헤더](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**본문**     |   요청 본문을 구성하는 방법에 대한 자세한 내용은 [REST API - POST /refreshes를 사용한 비동기 새로 고침](analysis-services-async-refresh.md#post-refreshes)을 참조하세요. |
|**인증**     |Active Directory OAuth         |
|**테넌트**     |Azure Active Directory TenantId 입력         |
|**대상**     |https://*.asazure.windows.net         |
|**클라이언트 ID**     |서비스 사용자 이름 ClientID 입력         |
|**자격 증명 유형**     |비밀         |
|**비밀**     |서비스 사용자 이름 비밀 입력         |

예:

![완료된 HTTP 작업](./media/analysis-services-async-refresh-logic-app/7.png)

이제 논리 앱을 테스트합니다.  논리 앱 디자이너에서 **실행** 을 클릭합니다.

![Logic Apps 테스트](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Azure Data Factory에서 논리 앱 사용

논리 앱이 저장되면 **HTTP 요청을 수신하는 경우** 작업을 검토한 다음, 이제 생성된 **HTTP POST URL** 을 복사합니다.  Azure Data Factory는 이 URL을 사용하여 논리 앱을 트리거하는 비동기 호출을 수행할 수 있습니다.

다음은 이 작업을 수행하는 Azure Data Factory 웹 작업 예제입니다.

![Data Factory 웹 작업](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>자체 포함 논리 앱 사용

Data Factory와 같은 오케스트레이션 도구를 사용하여 모델 새로 고침을 트리거할 계획이 없는 경우 일정에 따라 새로 고침을 트리거하도록 논리 앱을 설정할 수 있습니다.

위의 예제를 사용할 경우, 첫 번째 작업을 삭제하고 **일정** 작업으로 바꿉니다.

![“일정” 타일이 선택된 “Logic Apps” 페이지 스크린샷](./media/analysis-services-async-refresh-logic-app/12.png)

![“트리거” 페이지 스크린샷](./media/analysis-services-async-refresh-logic-app/13.png)

이 예제에서는 **되풀이** 를 사용합니다.

작업이 추가되면 간격과 빈도를 구성하고 새 매개 변수를 추가한 다음, **시간 선택** 을 선택합니다.

![“시간 선택” 매개 변수가 선택된 “되풀이” 섹션 스크린샷](./media/analysis-services-async-refresh-logic-app/16.png)

원하는 시간을 선택합니다.

![일정 작업](./media/analysis-services-async-refresh-logic-app/15.png)

논리 앱을 저장합니다.

## <a name="next-steps"></a>다음 단계

[샘플](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)