---
title: SCIM(System for Cross-Domain Identity Management) 2.0 프로토콜 준수의 알려진 문제 - Azure AD
description: SCIM 2.0을 지원하는 비갤러리 애플리케이션을 Azure AD에 추가할 때 발생하는 일반적인 프로토콜 호환성 문제를 해결하는 방법
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/25/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: fddb35cd2b610280440ac01fe5ffc9027a59a2b7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021888"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수와 관련하여 알려진 문제 및 해결 방법

Azure AD(Active Directory)는 [SCIM(System for Cross-Domain Identity Management) 2.0 프로토콜 사양](https://tools.ietf.org/html/draft-ietf-scim-api-19)에 정의된 인터페이스를 가진 웹 서비스가 향하는 애플리케이션 또는 시스템에 사용자 및 그룹을 자동으로 프로비전할 수 있습니다. 

Azure AD의 SCIM 2.0 프로토콜 지원은 [Using System for Cross-Domain Identity Management (SCIM) to automatically provision users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md)(SCIM(System for Cross-Domain Identity Management)을 사용하여 Azure Active Directory에서 애플리케이션으로 사용자 및 그룹 자동 프로비전)에서 설명합니다. 여기서는 SCIM 2.0을 지원하는 애플리케이션으로 Azure AD에서 사용자 및 그룹을 자동 프로비전하기 위해 구현하는 프로토콜의 특정 부분을 나열합니다.

이 문서에서는 Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수와 관련한 현재와 과거의 문제 및 이러한 문제를 해결하는 방법을 설명합니다.

## <a name="understanding-the-provisioning-job"></a>프로비저닝 작업 이해
프로비저닝 서비스는 작업 개념을 사용하여 애플리케이션에 대해 작동합니다. jobID는 [진행률 표시줄](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)에서 찾을 수 있습니다. 모든 새 프로비저닝 애플리케이션은 "scim"으로 시작하는 jobID를 사용하여 만들어집니다. SCIM 작업은 서비스의 현재 상태를 나타냅니다. 이전 작업에는 ID "customappsso"가 있습니다. 이 작업은 2018년의 서비스 상태를 나타냅니다. 

갤러리에서 애플리케이션을 사용하는 경우 작업에는 일반적으로 앱의 이름(예: zoom snowFlake, dataBricks 등)이 포함됩니다. 갤러리 애플리케이션을 사용하는 경우 이 설명서를 건너뛸 수 있습니다. 이는 주로 jobID SCIM 또는 customAppSSO를 사용하는 비갤러리 애플리케이션에 적용됩니다.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 준수 문제 및 상태
아래 표에서 fixed로 표시된 항목은 SCIM 작업에서 적절한 동작을 찾을 수 있음을 의미합니다. 변경한 내용에 대해 이전 버전과의 호환성을 보장하기 위해 노력했습니다. 그러나 이전 동작은 구현하지 않는 것이 좋습니다. 새 구현에는 새로운 동작을 사용하고 기존 구현을 업데이트하는 것이 좋습니다.

> [!NOTE]
> 2018년에 변경된 사항의 경우 customappsso 동작으로 되돌릴 수 있습니다. 2018 년 이후 변경된 사항의 경우 URL을 사용하여 이전 동작으로 되돌릴 수 있습니다. 이전 jobID로 되돌리거나 플래그를 사용하여 이전에 변경한 내용에 대해 이전 버전과의 호환성을 보장하기 위해 노력했습니다. 그러나 앞에서 설명한 대로 이전 동작은 구현하지 않는 것이 좋습니다. 새 구현에는 새로운 동작을 사용하고 기존 구현을 업데이트하는 것이 좋습니다.

| **SCIM 2.0 준수 문제** |  **수정?** | **수정 날짜**  |  **이전 버전과의 호환성** |
|---|---|---|
| Azure AD가 애플리케이션의 SCIM 엔드포인트 URL의 루트에 위치하려면 “/scim”이 필요함  | 예  |  2018년 12월 18일 | customappSSO로 다운그레이드 |
| 확장 특성은 특정 이름 앞에 콜론 “:” 대신 점 “.” 표기법을 사용함 |  예  | 2018년 12월 18일  | customappSSO로 다운그레이드 |
| 다중 값 특성의 패치 요청에 있는 경로 필터 구문이 잘못됨 | 예  |  2018년 12월 18일  | customappSSO로 다운그레이드 |
| 그룹 생성 요청에 있는 스키마 URI가 잘못됨 | 예  |  2018년 12월 18일  |  customappSSO로 다운그레이드 |
| 규정 준수를 보장하기 위해 PATCH 동작 업데이트(예: 부울로 활성 및 적절한 그룹 구성원 제거) | 예 | TBD| 기능 플래그 사용 |

## <a name="flags-to-alter-the-scim-behavior"></a>SCIM 동작을 변경하기 위한 플래그
기본 SCIM 클라이언트 동작을 변경하려면 애플리케이션의 테넌트 URL에서 아래 플래그를 사용합니다.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.png" alt-text="이후 동작에 대한 SCIM 플래그입니다.":::

다음 URL을 사용하여 패치 동작을 업데이트하고 SCIM 준수를 보장합니다. 플래그는 다음 동작을 변경합니다.                
- 사용자를 사용하지 않도록 설정하기 위한 요청
- 단일 값 문자열 특성을 추가하기 위한 요청
- 여러 특성을 바꾸기 위한 요청
- 그룹 구성원을 제거하기 위한 요청        

이 동작은 현재 플래그를 사용하는 경우에만 사용할 수 있지만 향후 몇 달 동안 기본 동작이 될 것입니다. 참고로 이 기능 플래그는 현재 주문형 프로비저닝에서 작동하지 않습니다. 
  * **URL(SCIM 준수):** aadOptscim062020
  * **SCIM RFC 참조:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2    

다음은 동기화 엔진이 현재 전송하는 내용과 기능 플래그가 사용하도록 설정된 후 전송되는 요청을 간략하게 설명하는 샘플 요청입니다. 

**사용자를 사용하지 않도록 설정하기 위한 요청:**

**기능 플래그 제외**
  ```json
  {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": "False"
        }
    ]
}
  ```

**기능 플래그 포함**
  ```json
  {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
}
  ```

**단일 값 문자열 특성을 추가하기 위한 요청:**

**기능 플래그 제외**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
    {
        "op": "Add",
        "path": "nickName",
        "value": "Babs"
     }
   ]
}

  ```

**기능 플래그 포함**
  ```json
{
  "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
  "Operations": [
    {
      "op": "add",
      "path": "nickName",
      "value": "Babs"
    }
  ]
}
  ```

**여러 특성을 바꾸기 위한 요청:**

**기능 플래그 제외**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "Replace",
            "path": "displayName",
            "value": "Pvlo"
        },
        {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "TestBcwqnm@test.microsoft.com"
        },
        {
            "op": "Replace",
            "path": "name.givenName",
            "value": "Gtfd"
        },
        {
            "op": "Replace",
            "path": "name.familyName",
            "value": "Pkqf"
        },
        {
            "op": "Replace",
            "path": "externalId",
            "value": "Eqpj"
        },
        {
            "op": "Replace",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber",
            "value": "Eqpj"
        }
    ]
}
  ```

**기능 플래그 포함**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "TestMhvaes@test.microsoft.com"
        },
        {
            "op": "replace",
            "value": {
                "displayName": "Bjfe",
                "name.givenName": "Kkom",
                "name.familyName": "Unua",
                "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber": "Aklq"
            }
        }
    ]
} 
  ```

**그룹 구성원을 제거하기 위한 요청:**

**기능 플래그 제외**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "Remove",
            "path": "members",
            "value": [
                {
                    "value": "u1091"
                }
            ]
        }
    ]
} 
  ```

**기능 플래그 포함**
  ```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"7f4bc1a3-285e-48ae-8202-5accb43efb0e\"]"
        }
    ]
}
  ```


  * **다운그레이드 URL:** 새 SCIM 준수 동작이 비갤러리 애플리케이션에서 기본값이 되면 다음 URL을 사용하여 이전 비SCIM 준수 동작(AzureAdScimPatch2017)으로 롤백할 수 있습니다.



## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>이전 customappsso 작업에서 SCIM 작업으로 업그레이드
아래 단계를 수행하면 기존 customappsso 작업이 삭제되고 새 scim 작업이 생성됩니다. 

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션** 섹션에서 기존의 SCIM 애플리케이션을 찾아 선택합니다.
3. 기존의 SCIM 앱의 **속성** 섹션에서 **개체 ID** 를 복사합니다.
4. 새 웹 브라우저 창에서 https://developer.microsoft.com/graph/graph-explorer로 이동하여 앱이 추가된 Azure AD 테넌트의 관리자로 로그인합니다.
5. Graph 탐색기에서 아래 명령을 실행하여 프로비저닝 작업의 ID를 찾습니다. “[object-id]”를 세 번째 단계에서 복사한 서비스 주체 ID(개체 ID)로 바꿉니다.

   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![작업 가져오기](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "작업 가져오기") 


6. 결과에서 “customappsso” 또는 “scim”으로 시작하는 전체 “ID” 문자열을 복사합니다.
7. 백업을 수행할 수 있도록, 아래 명령을 실행하여 특성 매핑 구성을 검색합니다. 이전과 동일한 [object-id]를 사용하고, [job-id]를 마지막 단계에서 복사한 프로비저닝 작업 ID로 바꿉니다.

   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`

   ![스키마 가져오기](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "스키마 가져오기") 

8. 마지막 단계의 JSON 출력을 복사하여 텍스트 파일에 저장합니다. JSON에는 이전 앱에 추가한 모든 사용자 지정 특성 매핑이 포함되어 있으며, 약 몇 천 줄의 JSON이 있어야 합니다.
9. 아래 명령을 실행하여 프로비저닝 작업을 삭제합니다.

   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 아래 명령을 실행하여 최신 서비스 수정 사항이 있는 새 프로비저닝 작업을 만듭니다.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   "templateId": "scim"   }`

11. 마지막 단계의 결과에서 “scim”으로 시작하는 전체 “ID” 문자열을 복사합니다. 선택적으로, 아래 명령을 실행하여 이전 특성 매핑을 다시 적용하고 [new-job-id]를 방금 복사한 새 작업 ID로 바꾸고 7단계의 JSON 출력을 요청 본문으로 입력합니다.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 첫 번째 웹 브라우저 창으로 돌아가서 애플리케이션에 해당하는 **프로비저닝** 탭을 선택합니다.
13. 구성을 확인한 후 프로비저닝 작업을 시작합니다. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>SCIM 작업에서 customappsso 작업으로 다운그레이드(권장하지 않음)
 이전 동작으로 다시 다운그레이드할 수 있지만 customappsso는 일부 업데이트의 이점을 활용하지 못하고 계속 지원되지 않을 수 있으므로 권장하지 않습니다. 

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션 > 애플리케이션 만들기** 섹션에서 새 **비갤러리** 애플리케이션을 만듭니다.
3. 새 사용자 지정 앱의 **속성** 섹션에서 **개체 ID** 를 복사합니다.
4. 새 웹 브라우저 창에서 https://developer.microsoft.com/graph/graph-explorer로 이동하여 앱이 추가된 Azure AD 테넌트의 관리자로 로그인합니다.
5. Graph 탐색기에서 아래 명령을 실행하여 앱의 프로비저닝 구성을 초기화합니다.
   “[object-id]”를 세 번째 단계에서 복사한 서비스 주체 ID(개체 ID)로 바꿉니다.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`

6. 첫 번째 웹 브라우저 창으로 돌아가서 애플리케이션에 해당하는 **프로비저닝** 탭을 선택합니다.
7. 일반적으로 하는 방법대로 사용자 프로비저닝 구성을 완료합니다.


## <a name="next-steps"></a>다음 단계
[SaaS 애플리케이션에 대한 사용자 프로비전 및 프로비전 해제 구성에 대해 자세히 알아보기](user-provisioning.md)
