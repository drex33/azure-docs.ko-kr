---
title: 포털에서 HTTP 요청 및 응답 헤더 다시 쓰기 - Azure Application Gateway
description: 게이트웨이를 통해 전달되는 요청 및 응답의 HTTP 헤더를 다시 쓰도록 Azure Portal로 Azure Application Gateway를 구성하는 방법을 알아봅니다.
services: application-gateway
author: KumudD
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 16479588b0e0ec64a17cb3ce0b7a46c49bd2244e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769905"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Azure Application Gateway로 HTTP 요청 및 응답 헤더 다시 쓰기 - Azure Portal

이 문서에서는 Azure Portal로 요청 및 응답의 HTTP 헤더를 다시 쓰도록 [Application Gateway v2 SKU](./application-gateway-autoscaling-zone-redundant.md) 인스턴스를 구성하는 방법을 설명합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 Application Gateway v2 SKU 인스턴스가 있어야 합니다. v1 SKU에서는 헤더 다시 쓰기가 지원되지 않습니다. v2 SKU가 없는 경우 시작하기 전에 [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) 인스턴스를 만듭니다.

## <a name="create-required-objects"></a>필수 개체 만들기

HTTP 헤더 다시 쓰기를 구성하려면 다음 단계를 완료해야 합니다.

1. HTTP 헤더 다시 쓰기에 필요한 개체를 만듭니다.

   - **다시 쓰기 작업**: 다시 쓰려는 요청 및 요청 헤더 필드와 헤더의 새 값을 지정하는 데 사용됩니다. 다시 쓰기 작업과 하나 이상의 다시 쓰기 조건을 연결할 수 있습니다.

   - **다시 쓰기 조건**: 선택적 구성입니다. 다시 쓰기 조건은 HTTP 요청 및 응답의 콘텐츠를 평가합니다. HTTP(S) 요청 또는 응답이 다시 쓰기 조건과 일치하면 다시 쓰기 작업이 수행됩니다.

     작업에 둘 이상의 조건을 연결할 경우 모든 조건이 충족되는 경우에만 작업이 수행됩니다. 즉, 작업은 논리적 AND 작업입니다.

   - **다시 쓰기 규칙**: 여러 다시 쓰기 작업/다시 쓰기 조건 조합을 포함합니다.

   - **규칙 순서**: 다시 쓰기 규칙이 실행되는 순서를 결정하는 데 도움이 됩니다. 이 구성은 다시 쓰기 집합에 다시 쓰기 규칙이 여러 개 있는 경우에 유용합니다. 규칙 시퀀스값이 낮은 다시 쓰기 규칙이 먼저 실행됩니다. 두 다시 쓰기 규칙에 동일한 규칙 시퀀스 값을 할당할 경우 실행 순서는 불명확합니다.

   - **다시 쓰기 집합**: 요청 라우팅 규칙과 연결되는 다시 쓰기 규칙을 여러 개 포함합니다.

2. 다시 쓰기 집합을 라우팅 규칙에 연결합니다. 이 다시 쓰기 구성이 회람 규칙을 통해 원본 수신기에 첨부됩니다. 기본 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 원본 수신기와 연결되어 글로벌 헤더 다시 쓰기가 됩니다. 경로 기반 회람 규칙을 사용하는 경우 헤더 다시 쓰기 구성이 URL 경로 맵에 정의됩니다. 이 경우 사이트의 특정 경로 영역에만 적용됩니다.

여러 HTTP 헤더 다시 쓰기 집합을 만들고 각 다시 쓰기 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에는 다시 쓰기 집합을 하나만 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

## <a name="configure-header-rewrite"></a>헤더 다시 쓰기 구성

이 예제에서는 백엔드 애플리케이션에서 보낸 HTTP 응답의 위치 헤더를 다시 써서 리디렉션 URL을 수정합니다.

1. **모든 리소스** 를 선택하고 애플리케이션 게이트웨이를 선택합니다.

2. 왼쪽 창에서 **다시 쓰기** 를 선택합니다.

3. **다시 쓰기 집합** 을 선택합니다.

   ![다시 쓰기 집합 추가](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 다시 쓰기 집합의 이름을 제공하고 라우팅 규칙과 연결합니다.

   - **이름** 상자에 다시 쓰기 집합의 이름을 입력합니다.
   - **연결된 라우팅 규칙** 목록에 나열된 규칙을 하나 이상 선택합니다. 다른 다시 쓰기 집합과 연결되지 않은 규칙만 선택할 수 있습니다. 다른 다시 쓰기 집합과 이미 연결된 규칙은 흐리게 표시됩니다.
   - **다음** 을 선택합니다.
   
     ![이름 및 연결 추가](media/rewrite-http-headers-portal/name-and-association.png)

5. 다시 쓰기 규칙을 만듭니다.

   - **다시 쓰기 규칙 추가** 를 선택합니다.

     ![다시 쓰기 규칙 추가](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - **다시 쓰기 규칙 이름** 상자에 다시 쓰기 규칙의 이름을 입력합니다. **규칙 시퀀스** 상자에 숫자를 입력합니다.

     ![다시 쓰기 규칙 이름 추가](media/rewrite-http-headers-portal/rule-name.png)

6. 이 예제에서는 azurewebsites.net에 대한 참조를 포함하는 경우에만 위치 헤더를 다시 씁니다. 이렇게 하려면 응답의 위치 헤더에 azurewebsites.net이 포함되어 있는지를 평가하는 조건을 추가합니다.

   - **조건 추가** 를 선택한 다음, **If** 명령이 포함된 상자를 선택하여 펼칩니다.

     ![조건 추가](media/rewrite-http-headers-portal/add-condition.png)

   - **확인할 변수 형식** 목록에서 **HTTP 헤더** 를 선택합니다.

   - **헤더 형식** 목록에서 **응답** 을 선택합니다.

   - 이 예제에서는 공용 헤더인 위치 헤더를 평가하고 **헤더 이름** 아래에서 **공용 헤더** 를 선택합니다.

   - **공용 헤더** 목록에서 **위치** 를 선택합니다.

   - **대/소문자 구분** 에서 **아니요** 를 선택합니다.

   - **연산자** 목록에서 **equal (=)** 을 선택합니다.

   - 정규식 패턴을 입력합니다. 이 예에서는 `(https?)://.*azurewebsites.net(.*)$` 패턴을 사용합니다.

   - **확인** 을 선택합니다.

     ![If 조건 구성](media/rewrite-http-headers-portal/condition.png)

7. 위치 헤더를 다시 쓰는 작업을 추가합니다.

   - **작업 형식** 목록에서 **설정** 을 선택합니다.

   - **헤더 형식** 목록에서 **응답** 을 선택합니다.

   - **헤더 이름** 아래에서 **공용 헤더** 를 선택합니다.

   - **공용 헤더** 목록에서 **위치** 를 선택합니다.

   - 헤더 값을 입력합니다. 이 예제에서는 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`를 헤더 값으로 사용합니다. 이 값은 위치 헤더에서 *azurewebsites.net* 을 *contoso.com* 으로 바꿉니다.

   - **확인** 을 선택합니다.

     ![작업 추가](media/rewrite-http-headers-portal/action.png)

8. **만들기** 를 선택하여 다시 쓰기 집합을 만듭니다.

   ![만들기 선택](media/rewrite-http-headers-portal/create.png)

9. 다시 쓰기 집합 보기가 열립니다. 만든 다시 쓰기 집합이 다시 쓰기 집합 목록에 있는지 확인합니다.

   ![다시 쓰기 집합 보기](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정하는 방법에 관한 자세한 내용은 [공용 헤더 다시 쓰기 시나리오](./rewrite-http-headers-url.md)를 참조하세요.