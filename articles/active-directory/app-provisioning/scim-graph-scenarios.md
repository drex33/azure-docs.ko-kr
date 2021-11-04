---
title: SCIM, Microsoft Graph 및 Azure Active Directory를 사용하여 사용자를 프로비전하고 데이터를 사용하여 앱 보강
description: SCIM과 Microsoft Graph를 함께 사용하여 사용자를 프로비전하고 Azure Active Directory에서 필요한 데이터를 사용하여 애플리케이션을 보강합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f97ff11a1a7e3829ef16aa4e903f88e2ad66949a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432760"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM과 Microsoft Graph를 함께 사용하여 사용자를 프로비전하고 필요한 데이터를 사용하여 애플리케이션을 보강합니다.

**대상 사용자:** 이 문서는 Azure AD(Azure Active Directory)와 통합할 애플리케이션을 빌드하는 개발자를 대상으로 합니다. Zoom, ServiceNow 및 DropBox와 같은 Azure AD와 이미 통합된 애플리케이션을 사용하려는 경우 이 문서를 건너뛰고 애플리케이션별 [자습서](../saas-apps/tutorial-list.md)를 검토하거나 [프로비저닝 서비스의 작동 방식](./how-provisioning-works.md)을 검토할 수 있습니다.

**일반적인 시나리오**

Azure AD는 프로비저닝에 사용할 수 있는 기본 제공 서비스와 애플리케이션을 빌드하는 확장 가능한 플랫폼을 제공합니다. 의사 결정 트리는 개발자가 [SCIM](https://aka.ms/scimoverview) 및 [Microsoft Graph](/graph/overview)를 사용하여 프로비저닝을 자동화하는 방법을 간략하게 설명합니다. 

> [!div class="checklist"]
> * 내 애플리케이션에서 자동으로 사용자 만들기
> * 더 이상 액세스할 수 없을 때 애플리케이션에서 사용자를 자동으로 제거
> * 프로비저닝을 위해 여러 ID 공급자와 애플리케이션 통합
> * Teams, Outlook 및 Office와 같은 Microsoft 서비스의 데이터를 사용하여 애플리케이션을 보강합니다.
> * Azure AD 및 Active Directory에서 사용자 및 그룹을 자동으로 만들고, 업데이트하고, 삭제

![SCIM Graph 의사 결정 트리](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>시나리오 1: 내 앱에서 자동으로 사용자 만들기
현재 IT 관리자는 수동으로 사용자 계정을 만들거나 CSV 파일을 내 애플리케이션에 주기적으로 업로드하여 사용자를 프로비전합니다. 이 프로세스는 고객에게 시간이 많이 소요되고 내 애플리케이션 채택 속도가 느려집니다. 사용자를 만들기 위한 이름, 이메일, userPrincipalName 등의 기본 사용자 정보만 있으면 됩니다. 

**권장 사항**: 
* 고객이 다양한 IdP를 사용하고 서로 통합될 동기화 엔진을 유지 관리하지 않으려는 경우 SCIM 준수 [/사용자](https://aka.ms/scimreferencecode) 엔드포인트를 지원합니다. 고객은 이 엔드포인트를 사용하여 Azure AD 프로비저닝 서비스와 통합하고 액세스해야 할 때 자동으로 사용자 계정을 만들 수 있습니다. 엔드포인트를 한 번에 빌드할 수 있으며 모든 IdP와 호환됩니다. SCIM을 사용하여 사용자를 만드는 방법에 대한 아래의 예제 요청을 확인하세요.
* Azure AD의 사용자 개체에서 찾은 사용자 데이터와 Microsoft에서 다른 데이터가 필요한 경우 사용자 프로비저닝을 위한 SCIM 엔드포인트를 빌드하고 Microsoft Graph로 호출하여 나머지 데이터를 가져오는 것이 좋습니다. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>시나리오 2: 내 앱에서 자동으로 사용자 제거
내 애플리케이션을 사용하는 고객은 보안에 중점을 두며 직원들이 더 이상 필요하지 않은 경우 계정을 제거하기 위한 거버넌스 요구 사항이 있습니다. 내 애플리케이션에서 프로비저닝 해제를 자동화하려면 어떻게 해야 하나요?

**권장 사항:** SCIM 준수/사용자 엔드포인트를 지원합니다. Azure AD 프로비저닝 서비스는 사용자에게 더 이상 액세스 권한이 없는 경우 사용하지 않도록 설정하고 삭제하는 요청을 보냅니다. 사용자 비활성화 및 삭제를 모두 지원하는 것이 좋습니다. 비활성화 및 삭제 요청은 다음 예제와 같이 표시됩니다. 

사용자 사용 안 함
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
사용자 삭제
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>시나리오 3: 내 앱에서 그룹 멤버 자격 관리 자동화
내 애플리케이션은 다양한 리소스에 액세스하기 위해 그룹을 사용하고, 고객은 Azure AD에 있는 그룹을 다시 사용하려고 합니다. Azure AD에서 그룹을 가져오고 멤버 자격 변경으로 계속 업데이트하려면 어떻게 해야 하나요?  

**권장 사항:** SCIM 준수/그룹 [엔드포인트](https://aka.ms/scimreferencecode)를 지원합니다. Azure AD 프로비저닝 서비스는 애플리케이션에서 그룹을 만들고 멤버 자격 업데이트를 관리하는 것을 담당합니다. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>시나리오 4: Teams, Outlook 및 OneDrive와 같은 Microsoft 서비스의 데이터를 사용하여 내 앱을 보강합니다.
내 애플리케이션은 Microsoft Teams에 기본 제공되며 메시지 데이터에 의존합니다. 또한 OneDrive의 사용자에 대한 파일을 저장합니다. 이러한 서비스 및 Microsoft의 데이터를 사용하여 내 애플리케이션을 보강하려면 어떻게 해야 하나요?

**권장 사항:** Microsoft 데이터에 액세스할 수 있는 진입점은 [Microsoft Graph](/graph/)입니다. 각 워크로드는 필요한 데이터를 사용하여 API를 노출합니다. 위의 시나리오에 대해 [SCIM 프로비저닝](./use-scim-to-provision-users-and-groups.md)과 함께 Microsoft Graph를 사용할 수 있습니다. SCIM을 사용하여 그래프를 호출하는 동안 기본 사용자 특성을 애플리케이션에 프로비전하여 필요한 다른 데이터를 가져올 수 있습니다. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>시나리오 5: Teams, Outlook 및 Azure AD와 같은 Microsoft 서비스의 변경 내용 추적
Teams와 Outlook 메시지에 대한 변경 내용을 추적하고 실시간으로 응답할 수 있어야 합니다. 이러한 변경 내용을 내 애플리케이션에 푸시하려면 어떻게 해야 하나요?

**권장 사항:** Microsoft Graph는 다양한 리소스에 대한 [변경 알림](/graph/webhooks) 및 [변경 내용 추적](/graph/delta-query-overview) 기능을 제공합니다. 변경 알림에 대한 다음 제한 사항에 유의하세요.
- 이벤트 수신기가 이벤트를 승인하지만 어떤 이유로든 동작하지 않으면 이벤트가 손실될 수 있습니다.
- 변경 내용이 수신되는 순서는 시간순이 아닐 수도 있습니다.
- 변경 알림은 항상 [리소스 데이터](/graph/webhooks-with-resource-data)를 포함하지 않습니다. 위의 이유로 개발자는 동기화 시나리오에 대한 변경 내용 추적과 함께 변경 알림을 사용하는 경우가 많습니다. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>시나리오 6: Azure AD에서 사용자 및 그룹 프로비저닝
내 애플리케이션은 Azure AD에서 고객이 필요로 하는 사용자에 대한 정보를 만듭니다. 직원 고용, 사용자를 위한 전화 번호를 만드는 커뮤니케이션 앱 또는 Azure AD에서 중요한 데이터를 생성하는 다른 앱을 관리하는 HR 애플리케이션일 수 있습니다. Azure AD의 사용자 레코드를 해당 데이터로 어떻게 채울 수 있나요? 

**권장 사항** Microsoft Graph는 사용자를 Azure AD에 프로비전하기 위해 현재와 통합할 수 있는/사용자 및/그룹 엔드포인트를 노출합니다. Azure Active Directory는 해당 사용자를 Active Directory에 다시 작성하는 것을 지원하지 않습니다. 

> [!NOTE]
> Microsoft에는 Workday 및 SuccessFactors와 같은 HR 애플리케이션에서 데이터를 가져오는 프로비저닝 서비스가 있습니다. 이러한 통합은 Microsoft에서 빌드하고 관리합니다. 새 HR 애플리케이션을 서비스에 온보딩하기 위해 [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)에서 요청할 수 있습니다. 

## <a name="related-articles"></a>관련된 문서

- [동기화 Microsoft Graph 설명서 검토](/graph/api/resources/synchronization-overview)
- [사용자 지정 SCIM 앱을 Azure AD와 통합](use-scim-to-provision-users-and-groups.md)
