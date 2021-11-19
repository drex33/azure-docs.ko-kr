---
title: Azure Portal에서 작업 그룹 만들기 및 관리
description: Azure Portal에서 작업 그룹을 만들고 관리하는 방법에 대해 알아봅니다.
author: dkamstra
ms.topic: conceptual
ms.date: 11/18/2021
ms.author: dukek
ms.openlocfilehash: 7d5458c6754081b5975d8c32920381c5c8fe82e7
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813678"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure Portal에서 작업 그룹 만들기 및 관리
작업 그룹은 Azure 구독 소유자가 정의한 알림 기본 설정 컬렉션입니다. Azure Monitor, Service Health 및 Azure Advisor 경고는 작업 그룹을 사용하여 알림이 트리거되었음을 사용자에게 알립니다. 사용자의 요구 사항에 따라 다양한 경고가 동일한 작업 그룹을 사용할 수도 있고 서로 다른 작업 그룹을 사용할 수도 있습니다. 

이 문서에서는 Azure Portal에서 작업 그룹을 만들고 관리하는 방법을 보여 줍니다.

각 작업은 다음과 같은 속성으로 구성됩니다.

* **유형**: 수행되는 알림 또는 작업입니다. 음성 통화, SMS, 이메일 보내기나, 여러 자동화된 작업 유형 트리거를 예로 들 수 있습니다. 이 문서 뒷부분에 나오는 유형을 참조하세요.
* **Name**: 작업 그룹 내의 고유 식별자입니다.
* **세부 정보**: *유형* 에 따라 달라지는 해당 세부 정보입니다.

Azure 리소스 관리자 템플릿을 사용하여 작업 그룹을 구성하는 방법에 대한 자세한 내용은 [작업 그룹 리소스 관리자 템플릿](./action-groups-create-resource-manager-template.md)을 참조하세요.

작업 그룹은 **글로벌** 서비스이므로 특정 Azure 지역에 대한 종속성이 없습니다. 클라이언트의 요청은 모든 지역의 작업 그룹 서비스에 의해 처리될 수 있습니다. 즉, 서비스의 한 지역이 다운되면 트래픽이 라우팅되고 다른 지역에 의해 자동으로 처리됩니다. 글로벌 *서비스이기* 때문에 클라이언트가 **재해 복구에** 대해 걱정하지 않아도 됩니다. 

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure Portal을 사용하여 작업 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에서 **모니터** 를 검색하여 선택합니다. **모니터** 창은 모든 모니터링 설정과 데이터를 하나의 보기로 통합합니다.

1. **경고** 를 선택한 다음, **작업 관리** 를 선택합니다.

    ![작업 관리 단추](./media/action-groups/manage-action-groups.png)
    
1. **작업 그룹 추가** 를 선택하고 마법사 환경에서 관련 필드를 채웁니다.

    ![“작업 그룹 추가” 명령](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>기본 작업 그룹 설정 구성

**프로젝트 세부 정보** 아래에서 다음을 수행합니다.

**구독** 및 작업 그룹이 저장되는 **리소스 그룹** 을 선택합니다.

**인스턴스 세부 정보** 에서

1. **작업 그룹 이름** 을 입력합니다.

1. **표시 이름** 을 입력합니다. 표시 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

      !["작업 그룹 추가" 대화 상자](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>알림 구성

1. **다음: 알림 >** 단추를 클릭하여 **알림** 탭으로 이동하거나, 화면 맨 위에서 **알림** 탭을 선택합니다.

1. 경고가 트리거될 때 보낼 알림 목록을 정의합니다. 각 작업에 대해 다음을 제공합니다.

    a. **알림 유형**: 송신하려는 알림 유형을 선택합니다. 사용 가능한 옵션은 다음과 같습니다.
      * 메일 Azure Resource Manager 역할 - 특정 구독 수준 ARM 역할에 할당된 사용자에게 메일을 보냅니다.
      * 메일/SMS/푸시/음성 - 이러한 알림 유형을 특정 받는 사람에게 보냅니다.
    
    b. **이름**: 알림에 대해 고유한 이름을 입력합니다.

    다. **세부 정보**: 선택한 알림 유형에 따라 메일 주소, 전화 번호 등을 입력합니다.
    
    d. **일반 경고 스키마**: Azure Monitor의 모든 경고 서비스에서 확장 가능하고 통합된 단일 경고 페이로드를 사용하는 이점을 제공하는 [일반 경고 스키마](./alerts-common-schema.md)를 사용하도록 선택할 수 있습니다.

    ![알림 탭](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>작업 구성

1. **다음: 작업 >** 단추를 클릭하여 **작업** 탭으로 이동하거나, 화면 맨 위에서 **작업** 탭을 선택합니다.

1. 경고가 트리거될 때 트리거할 작업 목록을 정의합니다. 각 작업에 대해 다음을 제공합니다.

    a. **작업 유형**: Automation Runbook, Azure Function, ITSM, 논리 앱, 보안 Webhook, Webhook를 선택합니다.
    
    b. **이름**: 작업에 대해 고유한 이름을 입력합니다.

    다. **세부 정보**: 작업 유형에 따라 webhook URI, Azure 앱, ITSM 연결 또는 Automation Runbook을 입력합니다. ITSM 작업의 경우 **작업 항목** 및 ITSM 도구에 필요한 다른 필드를 추가로 지정합니다.
    
    d. **일반 경고 스키마**: Azure Monitor의 모든 경고 서비스에서 확장 가능하고 통합된 단일 경고 페이로드를 사용하는 이점을 제공하는 [일반 경고 스키마](./alerts-common-schema.md)를 사용하도록 선택할 수 있습니다.
    
    ![작업 탭](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>작업 그룹 만들기

1. 원하는 경우 **태그** 설정을 둘러볼 수 있습니다. 키/값 쌍을 분류용 작업 그룹에 연결할 수 있으며 모든 Azure 리소스에 제공되는 기능입니다.

    ![태그 탭](./media/action-groups/action-group-4-tags.png)
    
1. **검토 + 만들기** 를 클릭하여 설정을 검토합니다. 모든 필수 필드가 선택되었는지 입력의 유효성을 신속하게 검사할 수 있습니다. 문제가 있는 경우 여기에 보고됩니다. 설정을 검토한 후에는 **만들기** 를 클릭하여 작업 그룹을 프로비저닝합니다.
    
    ![리뷰 + 만들기 탭](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> 메일 또는 SMS를 통해 개인에게 알리는 작업을 구성하면 해당 개인이 작업 그룹에 추가되었다는 확인 메시지를 수신합니다.

## <a name="manage-your-action-groups"></a>작업 그룹 관리

작업 그룹을 만든 후 **모니터** 창의 **경고** 방문 페이지에서 **작업 관리** 를 선택하여 **작업 그룹** 을 볼 수 있습니다. 관리하려는 작업 그룹을 선택합니다.

* 작업을 추가, 편집 또는 제거합니다.
* 작업 그룹을 삭제합니다.

## <a name="action-specific-information"></a>작업별 정보

> [!NOTE]
> 아래의 각 항목에 대한 숫자 제한은 [모니터링에 대한 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits)을 참조하세요.  

### <a name="automation-runbook"></a>자동화 Runbook
Runbook 페이로드에 대한 제한 사항은 [Azure 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

작업 그룹에서 Runbook 작업의 수가 제한될 수 있습니다. 

### <a name="azure-app-push-notifications"></a>Azure 앱 푸시 알림
Azure 모바일 앱을 구성할 때 계정 ID로 사용하는 메일 주소를 제공하여 [Azure 모바일 앱](https://azure.microsoft.com/features/azure-portal/mobile-app/)에 대한 푸시 알림을 사용하도록 설정합니다.

작업 그룹에서 Azure 앱 작업의 수가 제한될 수 있습니다.

### <a name="email"></a>Email
다음 이메일 주소에서 이메일이 전송됩니다. 이메일 필터링이 적절하게 구성되었는지 확인합니다.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

작업 그룹에서 이메일 작업의 수가 제한될 수 있습니다. [속도 제한 정보](./alerts-rate-limiting.md) 문서를 참조하세요.

### <a name="email-azure-resource-manager-role"></a>메일 Azure Resource Manager 역할
구독 역할의 멤버에게 이메일을 보냅니다. 이메일은 해당 역할의 **Azure AD 사용자** 멤버에게만 발송됩니다. Azure AD 그룹 또는 서비스 주체에게는 이메일이 전송되지 않습니다.

알림 메일은 *기본 메일* 주소로만 전송됩니다.

*기본 메일* 에 대한 알림을 받지 못하는 경우 다음 단계를 수행해 볼 수 있습니다.

1. Azure Portal에서 *Active Directory* 로 이동합니다.
2. 왼쪽 창에서 모든 사용자를 클릭하면 오른쪽 창에 사용자 목록이 표시됩니다.
3. *기본 메일* 정보를 검토하려는 사용자를 선택합니다.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="사용자 프로필을 검토하는 방법에 대한 예입니다." border="true":::

4. 연락처 정보 아래의 사용자 프로필에서 "메일" 탭이 비어 있는 경우 맨 위에 있는 *편집* 단추를 클릭하고 *기본 메일* 을 추가한 후 맨 위에 있는 *저장* 단추를 누릅니다.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="기본 메일을 추가하는 방법에 대한 예입니다." border="true":::

작업 그룹에서 이메일 작업의 수가 제한될 수 있습니다. [속도 제한 정보](./alerts-rate-limiting.md) 문서를 참조하세요.

*이메일 ARM 역할* 을 설정하는 동안 다음 세 가지 조건이 충족되는지 확인해야 합니다.

1. 역할에 할당되는 엔터티의 유형은 **“사용자”** 여야 합니다.
2. 할당은 **구독** 수준에서 수행해야 합니다.
3. 사용자는 **AAD 프로필** 에 구성된 이메일이 있어야 합니다. 

> [!NOTE]
> 고객이 구독에 새 ARM 역할을 추가한 후 알림을 받기 시작하는 데 최대 **24시간** 이 걸릴 수 있습니다.

### <a name="event-hub-preview"></a>이벤트 허브(미리 보기)
> [!NOTE]
> 이벤트 허브 작업 유형은 현재 *미리 보기* 에 있습니다. 미리 보기 중에는 기능의 가용성에 버그 및 중단이 있을 수 있습니다.

이벤트 허브 작업은 Azure Event [Hub](~/articles/event-hubs/event-hubs-about.md)에 알림을 게시합니다. 그런 다음 이벤트 수신기에서 경고 알림 스트림을 구독할 수 있습니다.

### <a name="function"></a>함수
[Azure Functions](../../azure-functions/functions-get-started.md)에서 기존 HTTP 트리거 엔드포인트를 호출합니다. 요청을 처리하려면 엔드포인트가 HTTP POST 동사를 처리해야 합니다.

함수 작업을 정의하는 경우 함수의 httptrigger 엔드포인트 및 액세스 키가 작업 정의에 저장됩니다. 예: `https://azfunctionurl.azurewebsites.net/api/httptrigger?code=this_is_access_key`. 함수에 대한 액세스 키를 변경하는 경우 작업 그룹에서 함수 동작을 제거하고 다시 만들어야 합니다.

작업 그룹에서 함수 작업의 수가 제한될 수 있습니다.

### <a name="itsm"></a>ITSM
ITSM 작업에는 ITSM 연결이 필요합니다. [ITSM 연결](./itsmc-overview.md)을 만드는 방법에 대해 알아봅니다.

작업 그룹에서 ITSM 작업의 수가 제한될 수 있습니다. 

### <a name="logic-app"></a>논리 앱
작업 그룹에서 논리 앱 작업의 수가 제한될 수 있습니다.

### <a name="secure-webhook"></a>보안 웹후크
작업 그룹 보안 웹후크 작업을 사용하면 Azure Active Directory를 활용하여 작업 그룹과 보호된 웹 API(웹후크 엔드포인트) 간의 연결을 보호할 수 있습니다. 이 기능을 활용하기 위한 전체 워크플로는 아래에 설명되어 있습니다. Azure AD 애플리케이션 및 서비스 주체에 대한 개요는 [Microsoft ID 플랫폼(v2.0) 개요](../../active-directory/develop/v2-overview.md)를 참조하세요.

> [!NOTE]
> webhook 작업을 사용하기 위해 대상 webhook 엔드포인트에서 경고에 대한 세부 정보가 성공적으로 작동할 필요는 없으며, POST 작업의 일부로 제공된 경고 컨텍스트 정보를 구문 분석할 수 있어야 합니다. webhook 엔드포인트에서 경고 컨텍스트 정보를 자체적으로 처리할 수 없는 경우에는 [논리 앱 작업](./action-groups-logic-app.md)과 같은 솔루션을 사용하여 webhook의 예상 데이터 형식과 일치하도록 경고 컨텍스트 정보를 사용자 지정할 수 있습니다.

1. 보호된 웹 API에 대한 Azure AD 애플리케이션을 만듭니다. [보호된 웹 API: 앱 등록](../../active-directory/develop/scenario-protected-web-api-app-registration.md)을 참조하세요.
    - [디먼 앱에서 호출](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)하도록 보호된 API를 구성합니다.
    
2. Azure AD 애플리케이션을 사용하도록 작업 그룹을 설정합니다.

    > [!NOTE]
    > 이 스크립트를 실행하려면 [Azure AD 애플리케이션 관리자 역할](../../active-directory/roles/permissions-reference.md#all-roles)의 멤버여야 합니다.
    
    - PowerShell 스크립트의 Connect-AzureAD 호출을 수정하여 Azure AD 테넌트 ID를 사용합니다.
    - PowerShell 스크립트의 변수 $myAzureADApplicationObjectId를 수정하여 Azure AD 애플리케이션의 개체 ID를 사용합니다.
    - 수정된 스크립트를 실행합니다.
    
3. 작업 그룹 보안 웹후크 작업을 구성합니다.
    - 스크립트에서 $myApp.ObjectId 값을 복사하여 웹후크 작업 정의의 애플리케이션 개체 ID 필드에 입력합니다.
    
    ![보안 웹후크 작업](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>보안 웹후크 PowerShell 스크립트

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
[속도 제한 정보](./alerts-rate-limiting.md) 및 [SMS 경고 동작](./alerts-sms-behavior.md)에서 중요한 추가 정보를 참조하세요. 

작업 그룹에서 SMS 작업의 수가 제한될 수 있습니다.

> [!NOTE]
> Azure Portal 작업 그룹 사용자 인터페이스에서 국가/지역 코드를 선택할 수 없는 경우 해당 국가/지역에 대해서는 SMS가 지원되지 않는 것입니다.  국가/지역 코드를 사용할 수 없는 경우 [사용자 의견](https://feedback.azure.com/d365community/idea/e527eaa6-2025-ec11-b6e6-000d3a4f09d0)에서 사용자의 국가/지역이 추가되도록 투표할 수 있습니다. 그동안에는 작업 그룹에서 해당 국가/지역을 지원하는 타사 SMS 공급자에 대한 웹후크를 호출하도록 하여 문제를 해결합니다.  

지원되는 국가/지역에 대한 가격은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 나와 있습니다.

**SMS 알림이 지원되는 국가 목록**

| 국가 코드 | 국가 이름 |
|:---|:---|
| 61 | 오스트레일리아 |
| 43 | 오스트리아 |
| 32 | 벨기에 |
| 55 | 브라질 |
| 1 |캐나다 |
| 56 | 칠레 |
| 86 | 중국 |
| 420 | 체코 공화국 |
| 45 | 덴마크 |
| 372 | 에스토니아 |
| 358 | 핀란드 |
| 33 | 프랑스 |
| 49 | 독일 |
| 852 | 홍콩 |
| 91 | 인도 |
| 353 | 아일랜드 |
| 972 | 이스라엘 |
| 39 | 이탈리아 |
| 81 | 일본 |
| 352 | 룩셈부르크 |
| 60 | 말레이시아 |
| 52 | 멕시코 |
| 31 | 네덜란드 |
| 64 | 뉴질랜드 |
| 47 | 노르웨이 |
| 351 | 포르투갈 |
| 1 | 푸에르토리코 |
| 40 | 루마니아 |
| 7  | 러시아  |
| 65 | 싱가포르 |
| 27 | 남아프리카 공화국 |
| 82 | 대한민국 |
| 34 | 스페인 |
| 41 | 스위스 |
| 886 | 대만 |
| 971 | 아랍에미리트    |
| 44 | 영국 |
| 1 | 미국 |

### <a name="voice"></a>음성
[속도 제한 정보](./alerts-rate-limiting.md) 문서에서 중요한 추가 동작을 참조하세요.

작업 그룹에서 음성 작업의 수가 제한될 수 있습니다.

> [!NOTE]
> Azure Portal 작업 그룹 사용자 인터페이스에서 국가/지역 코드를 선택할 수 없는 경우 해당 국가/지역에 대해서는 음성 통화가 지원되지 않는 것입니다. 국가/지역 코드를 사용할 수 없는 경우 [사용자 의견](https://feedback.azure.com/d365community/idea/e527eaa6-2025-ec11-b6e6-000d3a4f09d0)에서 사용자의 국가/지역이 추가되도록 투표할 수 있습니다.  그동안에는 작업 그룹에서 해당 국가/지역을 지원하는 타사 음성 통화 공급자에 대한 웹후크를 호출하도록 하여 문제를 해결합니다.  
> 음성 알림에 대한 Azure Portal 작업 그룹에서 현재 지원되는 유일한 국가 코드는 +1(미국)입니다. 

지원되는 국가/지역에 대한 가격은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 나와 있습니다.

### <a name="webhook"></a>웹후크

> [!NOTE]
> webhook 작업을 사용하기 위해 대상 webhook 엔드포인트에서 경고에 대한 세부 정보가 성공적으로 작동할 필요는 없으며, POST 작업의 일부로 제공된 경고 컨텍스트 정보를 구문 분석할 수 있어야 합니다. webhook 엔드포인트에서 경고 컨텍스트 정보를 자체적으로 처리할 수 없는 경우에는 [논리 앱 작업](./action-groups-logic-app.md)과 같은 솔루션을 사용하여 webhook의 예상 데이터 형식과 일치하도록 경고 컨텍스트 정보를 사용자 지정할 수 있습니다.

webhook는 다음 규칙을 사용하여 처리됩니다.
- webhook 호출은 최대 3회 시도됩니다.
- 제한 시간 내에 응답이 수신되지 않거나 HTTP 상태 코드 408, 429, 503 또는 504 중 하나가 반환되면 호출이 다시 시도됩니다.
- 첫 번째 호출은 응답을 위해 10초를 기다립니다.
- 두 번째 및 세 번째 시도는 응답에 대해 30초 동안 대기합니다.
- 3번의 webhook 호출이 실패한 후에는 작업 그룹에서 15분 동안 엔드포인트를 호출하지 않습니다.

원본 IP 주소 범위에 대한 [작업 그룹 IP 주소](../app/ip-addresses.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
* [SMS 경고 동작](./alerts-sms-behavior.md)에 대해 자세히 알아보세요.  
* [활동 로그 경고 웹후크 스키마의 이해](./activity-log-alerts-webhook.md)를 확인해 보세요.  
* [ITSM 커넥터](./itsmc-overview.md)에 대해 자세히 알아보세요.
* 경고의 [속도 제한](./alerts-rate-limiting.md)에 대해 자세히 알아보세요.
* [활동 로그 경고의 개요](./alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [서비스 상태 알림이 게시될 때마다 경고를 구성](../../service-health/alerts-activity-log-service-notifications-portal.md)하는 방법을 알아보세요.
