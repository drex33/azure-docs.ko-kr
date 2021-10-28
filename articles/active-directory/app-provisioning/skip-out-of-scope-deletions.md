---
title: Azure Active Directory 애플리케이션 프로비저닝에서 범위를 벗어난 사용자 삭제 건너뛰기
description: Azure Active Directory에서 범위를 벗어나는 사용자를 프로비저닝 해제하는 기본 동작을 재정의하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 3aa0176db0bba82d1a793fd196c6a7bead4d531f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990751"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope-in-azure-active-directory"></a>Azure Active Directory에서 범위를 벗어나는 사용자 계정 삭제 건너뛰기

기본적으로 Azure AD 프로비저닝 엔진은 범위를 벗어나는 사용자를 일시 삭제하거나 비활성화합니다. 그러나 Workday에서 AD로 사용자 인바운드 프로비저닝과 같은 특정 시나리오의 경우 이 동작이 예상과 다를 수 있으며 이 기본 동작을 재정의하는 것이 좋습니다.  

이 문서에서는 Microsoft Graph API 및 Microsoft Graph API 탐색기를 사용하여 범위를 벗어나는 계정의 처리를 제어하는 플래그 ***SkipOutOfScopeDeletions*** 를 설정하는 방법을 설명합니다. 
* ***SkipOutOfScopeDeletions*** 가 0(false)으로 설정된 경우 범위를 벗어나는 계정은 대상에서 사용하지 않도록 설정됩니다.
* ***SkipOutOfScopeDeletions** _가 1(true)로 설정된 경우 범위를 벗어나는 계정은 대상에서 사용하지 않도록 설정되지 않습니다. 이 플래그는 '프로비저닝 앱' 수준에서 설정되며 Graph API를 사용하여 구성할 수 있습니다. 

이 구성은 Workday에서 Active Directory로 사용자 프로비저닝 앱에서 널리 사용되므로 다음 단계에서는 Workday 애플리케이션의 스크린샷을 보여줍니다. 그러나 ServiceNow, Salesforce, Dropbox 등 다른 모든 앱에서도 이 구성을 사용할 수 있습니다.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1단계: 프로비저닝 앱 서비스 주체 ID(개체 ID) 검색

1. [Azure Portal](https://portal.azure.com)을 시작하고 프로비저닝 애플리케이션의 속성 섹션으로 이동합니다. 예를 들어 Workday에서 AD로 사용자 프로비저닝 애플리케이션 매핑을 내보내려는 경우 해당 앱의 속성 섹션으로 이동합니다. 
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다. 이 값은 앱의 **ServicePrincipalId** 라고도 하고 Graph Explorer 작업에서 사용됩니다.

   ![Workday 앱 서비스 주체 ID](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2단계: Microsoft Graph Explorer에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![Graph 로그인](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3단계: 기존 앱 자격 증명 및 연결 정보 가져오기

Microsoft Graph Explorer에서 [servicePrincipalId]를 [1단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출된 **ServicePrincipalId** 로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![GET 작업 쿼리](./media/skip-out-of-scope-deletions/skip-03.png)

응답을 텍스트 파일로 복사합니다. 이 응답은 아래에 표시된 JSON 텍스트와 비슷합니다. 배포와 관련된 값은 노란색으로 강조 표시되어 있습니다. 녹색으로 강조 표시된 줄을 끝에 추가하고 파란색으로 강조 표시된 Workday 연결 암호를 업데이트합니다. 

   ![GET 작업 응답](./media/skip-out-of-scope-deletions/skip-04.png)

매핑에 추가할 JSON 블록은 다음과 같습니다. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4단계: SkipOutOfScopeDeletions 플래그로 비밀 엔드포인트 업데이트

Graph 탐색기에서 아래 명령을 실행하여 ***SkipOutOfScopeDeletions*** 플래그로 비밀 엔드포인트를 업데이트합니다. 

아래 URL에서 [servicePrincipalId]를 [1단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출한 **ServicePrincipalId** 로 바꿉니다. 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
3단계에서 업데이트된 텍스트를 ‘요청 본문’에 복사합니다. 

   ![PUT 요청](./media/skip-out-of-scope-deletions/skip-05.png)

'쿼리 실행'을 클릭합니다. 

'성공 – 상태 코드 204'가 출력되어야 합니다. 

   ![PUT 응답](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5단계: 범위를 벗어나는 사용자가 사용하지 않도록 설정되지 않았는지 확인

특정 사용자를 건너뛰도록 범위 지정 규칙을 업데이트하여 이 플래그가 예상대로 동작하는지 테스트할 수 있습니다. 아래 예제에서는 새 범위 지정 규칙을 추가하여 ID가 21173인 직원(이전에는 범위 내)을 제외합니다. 

   ![예제 사용자가 강조 표시된 '범위 지정 필터 추가' 섹션을 보여 주는 스크린샷](./media/skip-out-of-scope-deletions/skip-07.png)

다음 프로비저닝 주기에서 Azure AD 프로비저닝 서비스가 사용자 21173이 범위를 벗어난 것을 식별하고, SkipOutOfScopeDeletions 속성이 사용하도록 설정된 경우 해당 사용자에 대한 동기화 규칙이 아래와 같은 메시지를 표시합니다. 

   ![범위 지정 예제](./media/skip-out-of-scope-deletions/skip-08.png)


