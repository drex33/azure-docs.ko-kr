---
title: 권한 관리에서 리소스 카탈로그 만들기 및 관리 - Azure AD
description: Azure Active Directory 권한 관리에서 액세스 패키지와 리소스의 새 컨테이너를 만드는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: ''
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 8/31/2021
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540c207388dfc5b37e2dd03939b14753c3d8ec7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791786"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 리소스 카탈로그 만들기 및 관리

이 문서에서는 Azure AD(Azure Active Directory) 자격 관리에서 리소스 및 액세스 패키지의 카탈로그를 만들고 관리하는 방법을 보여 줍니다.

## <a name="create-a-catalog"></a>카탈로그 만들기

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 리소스 및 액세스 패키지를 그룹화하려는 경우 카탈로그를 생성합니다. 카탈로그를 만드는 사람은 첫 번째 카탈로그 소유자가 됩니다. 카탈로그 소유자는 카탈로그 소유자를 더 추가할 수 있습니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자 또는 카탈로그 작성자

> [!NOTE]
> 사용자 관리자 역할이 할당된 사용자는 더 이상 카탈로그를 만들거나 소유하지 않은 카탈로그에서 액세스 패키지를 관리할 수 없습니다. 조직의 사용자에게 자격 관리에서 카탈로그, 액세스 패키지 또는 정책을 구성할 수 있는 사용자 관리자 역할이 할당된 경우 대신 이러한 사용자에게 Identity Governance 관리자 역할을 할당해야 합니다.

카탈로그를 만들려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** > **ID 거버넌스** 를 차례로 선택합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 선택합니다.

    ![Azure Portal의 권한 관리 카탈로그를 보여 주는 스크린샷](./media/entitlement-management-catalog-create/catalogs.png)

1. **새 카탈로그** 를 선택합니다.

1. 카탈로그의 고유 이름을 입력하고 설명을 입력합니다.

    사용자는 액세스 패키지의 세부 정보에서 해당 정보를 볼 수 있습니다.

1. 이 카탈로그의 액세스 패키지를 만드는 즉시 사용자가 요청할 수 있게 하려면 **사용** 을 **예** 로 설정합니다.

1. 선택한 외부 디렉터리의 사용자가 이 카탈로그의 액세스 패키지를 요청할 수 있게 하려면 **외부 사용자에 대해 사용** 을 **예** 로 설정합니다.

    ![새 카탈로그 창을 보여 주는 스크린샷](./media/entitlement-management-shared/new-catalog.png)

1. **만들기** 를 선택하여 카탈로그를 만듭니다.

## <a name="create-a-catalog-programmatically"></a>프로그래매틱 방식으로 카탈로그 만들기

카탈로그를 프로그래밍 방식으로 만드는 방법에는 두 가지가 있습니다.

### <a name="create-a-catalog-with-microsoft-graph"></a>Microsoft Graph로 카탈로그 만들기

Microsoft Graph를 사용하여 카탈로그를 만들 수 있습니다. 위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션 또는 해당 애플리케이션 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 API를 호출하여 [accessPackageCatalog 만들기](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)를 수행할 수 있습니다.

### <a name="create-a-catalog-with-powershell"></a>PowerShell로 카탈로그 만들기

[Identity Governance용 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 모듈 버전 1.6.0 이상에서 `New-MgEntitlementManagementAccessPackageCatalog` cmdlet을 사용하여 PowerShell에서 카탈로그를 만들 수도 있습니다.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>카탈로그에 리소스를 추가합니다.

액세스 패키지에 리소스를 포함하려면 리소스가 카탈로그에 있어야 합니다. 추가할 수 있는 리소스 유형은 그룹, 애플리케이션, SharePoint Online 사이트입니다. 다음은 그 예입니다.

* 그룹은 클라우드 생성 Microsoft 365 그룹 또는 클라우드 생성 Azure AD 보안 그룹일 수 있습니다. Azure AD에서 소유자 또는 구성원 특성을 변경할 수 없으므로 온-프레미스 Active Directory에서 발생하는 그룹을 리소스로 할당할 수 없습니다. Exchange Online에서 배포 그룹으로 시작된 그룹도 Azure AD에서 수정할 수 없습니다.
* 애플리케이션은 SaaS(Software as a Service) 애플리케이션 및 Azure AD에 통합된 애플리케이션을 포함한 Azure AD 엔터프라이즈 애플리케이션일 수 있습니다. 여러 역할이 있는 애플리케이션에 적절한 리소스를 선택하는 방법에 대한 자세한 내용은 [리소스 역할 추가](entitlement-management-access-package-resources.md#add-resource-roles)를 참조하세요.
* 사이트는 SharePoint Online 사이트 또는 SharePoint Online 사이트 모음일 수 있습니다.

**필수 역할:** [카탈로그에 리소스를 추가하는 데 필요한 역할](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)을 참조하세요.

카탈로그에 리소스를 추가하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** > **ID 거버넌스** 를 차례로 선택합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 선택한 다음, 리소스를 추가하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스** 를 선택합니다.

1. **리소스 추가** 를 선택합니다.

1. **그룹과 팀**, **애플리케이션**, **SharePoint 사이트** 와 같은 리소스 종류를 선택합니다.

    추가하려는 리소스가 표시되지 않거나 리소스를 추가할 수 없으면 필요한 Azure AD 디렉터리 역할과 권한 관리 역할이 있는지 확인합니다. 필요한 역할을 가진 사용자에게 카탈로그에 리소스를 추가하도록 요청해야 할 수 있습니다. 자세한 내용은 [카탈로그에 리소스를 추가하는 데 필요한 역할](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)를 참조하세요.

1. 카탈로그에 추가할 종류의 리소스를 하나 이상 선택합니다.

    ![카탈로그에 리소스 추가 창을 보여 주는 스크린샷](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 작업을 마쳤으면 **추가** 를 선택합니다.

    해당 리소스는 이제 카탈로그 내에서 액세스 패키지에 포함될 수 있습니다.

### <a name="add-resource-attributes-preview-in-the-catalog"></a>카탈로그에서 리소스 특성 추가(미리 보기)

특성은 요청자에게 액세스 요청을 제출하기 전에 응답하도록 요청하는 필수 필드입니다. 이러한 특성에 대한 응답은 승인자에게 표시되고 Azure AD의 사용자 개체에도 스탬프가 표시됩니다. 

> [!NOTE]
>리소스를 포함하는 액세스 패키지에 대한 요청을 제출하려면 먼저 리소스에 설정된 모든 특성에 대해 응답이 필요합니다. 요청자가 응답이 제공하지 않으면 해당 요청이 처리되지 않습니다.

액세스 요청에 대한 특성을 요구하려면 다음을 수행합니다.

1. 왼쪽 메뉴에서 **리소스** 를 클릭합니다. 그러면 이 카탈로그의 리소스 목록이 표시됩니다. 

1. 특성을 추가하려는 리소스 옆에 있는 줄임표를 선택하고 **특성 필요(미리 보기)** 를 선택합니다. 

    ![특성 필요(미리 보기) 선택을 보여 주는 스크린샷](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  특성 유형을 선택합니다.

    1. **기본 제공** 에는 Azure AD 사용자 프로필 특성이 포함됩니다.
    1. **디렉터리 스키마 확장** 은 Azure AD에서 사용자 개체 및 기타 디렉터리 개체에 대한 추가 데이터를 저장하는 방법을 제공합니다. 여기에는 그룹, 테넌트 세부 정보 및 서비스 주체가 포함됩니다. 사용자 개체의 확장 특성만 애플리케이션에 대한 클레임을 내보내는 데 사용할 수 있습니다.
1. **기본 제공** 을 선택한 경우 드롭다운 목록에서 특성을 선택합니다. **디렉터리 스키마 확장** 을 선택한 경우 텍스트 상자에 특성 이름을 입력합니다.

    > [!NOTE]
    > User.mobilePhone 특성은 관리자가 아닌 사용자에 대해서만 업데이트할 수 있습니다. [이 웹 사이트](/graph/permissions-reference#remarks-5)에서 자세히 알아보세요.

1.  요청자가 응답에 사용하게 하려는 응답 형식을 선택합니다. 응답 형식에는 **짧은 텍스트**, **다중 선택** 및 **긴 텍스트** 가 포함됩니다.

1.  다중 선택을 선택하는 경우 **편집 및 지역화** 를 선택하여 응답 옵션을 구성합니다. 
    1. 나타나는 **질문 보기/편집** 창의 **응답 값** 상자에 질문에 대답할 때 요청자에게 제공하려는 응답 옵션을 입력합니다.
    1. 응답 옵션에 대한 언어를 선택합니다. 더 많은 언어를 선택하는 경우 응답 옵션을 지역화할 수 있습니다.
    1. 필요한 만큼 응답을 입력한 다음, **저장** 을 선택합니다.

1. 직접 할당 및 셀프 서비스 요청 중에 특성 값을 편집하려면 **예** 를 선택합니다.

    > [!NOTE]
    > ![특성을 편집할 수 있는 상태를 보여 주는 스크린샷](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - **특성 값이 편집 가능함** 상자에서 **아니요** 를 선택하고 특정 값이 *비어 있는 경우* 사용자가 해당 특성의 값을 입력할 수 있습니다. 저장한 후에는 값을 편집할 수 없습니다. 
    > - **특성 값이 편집 가능함** 상자에서 **아니요** 를 선택하고 특성 값이 *비어 있지 않은 경우* 사용자는 직접 할당 및 셀프 서비스 요청 중에 기존 값을 편집할 수 없습니다.
 
    ![지역화 추가를 보여 주는 스크린샷](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  지역화를 추가하려면 **지역화 추가** 를 선택합니다.

    1. **질문에 대한 지역화 추가** 창에서 선택한 특성과 관련된 질문을 지역화할 언어의 언어 코드를 선택합니다.
    1. 구성한 언어로, **지역화된 텍스트** 상자에 질문을 입력합니다.
    1. 필요한 지역화를 모두 추가한 후에 **저장** 을 선택합니다.

       ![지역화 저장 방법을 보여 주는 스크린샷](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  **특성 필요(미리 보기)** 페이지에서 모든 특성 정보를 완료한 후 **저장** 을 선택합니다.

### <a name="add-a-multi-geo-sharepoint-site"></a>다중 지역 SharePoint 사이트 추가

1. SharePoint에 [다중 지역](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)을 사용하도록 설정한 경우 사이트를 선택할 환경을 선택합니다.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="SharePoint Online 사이트 선택 창을 보여 주는 스크린샷":::

1. 그런 다음, 카탈로그에 추가할 사이트를 선택합니다.

### <a name="add-a-resource-to-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그에 리소스 추가

Microsoft Graph를 사용하여 카탈로그에 리소스를 추가할 수도 있습니다. 적절한 역할의 사용자 또는 위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 포함하는 카탈로그와 리소스 소유자는 API를 호출하여 [accessPackageResourceRequest 만들기](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)를 수행할 수 있습니다. 그러나 애플리케이션 권한이 있는 애플리케이션은 요청 시 사용자 컨텍스트 없이 프로그래밍 방식으로 리소스를 추가할 수 없습니다.

## <a name="remove-resources-from-a-catalog"></a>카탈로그에서 리소스 제거

카탈로그에서 리소스를 제거할 수 있습니다. 리소스는 카탈로그의 액세스 패키지에서 사용되지 않는 경우에만 카탈로그에서 제거할 수 있습니다.

**필수 역할:** [카탈로그에 리소스를 추가하는 데 필요한 역할](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)을 참조하세요.

카탈로그에서 리소스를 제거하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** > **ID 거버넌스** 를 차례로 선택합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 선택한 다음, 리소스를 제거하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스** 를 선택합니다.

1. 이동하려는 리소스를 선택합니다.

1. **제거** 를 선택합니다. 필요에 따라 줄임표( **...** )를 선택한 다음, **리소스 제거** 를 선택합니다.

## <a name="add-more-catalog-owners"></a>더 많은 카탈로그 소유자 추가

카탈로그를 만든 사용자가 첫 번째 카탈로그 소유자가 됩니다. 카탈로그 관리를 위임하려면 카탈로그 소유자 역할에 사용자를 추가합니다. 카탈로그 소유자를 더 추가하면 카탈로그 관리 책임을 공유하는 데 도움이 됩니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자 또는 카탈로그 소유자

카탈로그 소유자 역할에 사용자를 할당하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** > **ID 거버넌스** 를 차례로 선택합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 선택한 후 관리자를 추가할 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자** 를 선택합니다.

    ![카탈로그 역할 및 관리자를 보여 주는 스크린샷](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **소유자 추가** 를 선택하여 해당 역할의 멤버를 선택합니다.

1. **선택** 을 클릭하여 해당 멤버를 추가합니다.

## <a name="edit-a-catalog"></a>카탈로그 편집

카탈로그의 이름 및 설명을 편집할 수 있습니다. 사용자는 액세스 패키지의 세부 정보에서 해당 정보를 볼 수 있습니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자 또는 카탈로그 소유자

카탈로그를 편집하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** > **ID 거버넌스** 를 차례로 선택합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 선택한 다음, 편집할 카탈로그를 엽니다.

1. 카탈로그의 **개요** 페이지에서 **편집** 을 클릭합니다.

1. 카탈로그의 이름, 설명 또는 사용 설정을 편집합니다.

    ![카탈로그 설정 편집을 보여 주는 스크린샷](./media/entitlement-management-shared/catalog-edit.png)

1. **저장** 을 선택합니다.

## <a name="delete-a-catalog"></a>카탈로그 삭제

카탈로그를 삭제할 수 있지만 액세스 패키지가 없는 경우에만 삭제할 수 있습니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자 또는 카탈로그 소유자

카탈로그를 삭제하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** > **ID 거버넌스** 를 차례로 선택합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 선택한 다음, 삭제할 카탈로그를 엽니다.

1. 카탈로그의 **개요** 페이지에서 **삭제** 를 클릭합니다.

1. 표시되는 메시지 상자에서 **예** 를 선택합니다.

### <a name="delete-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그 삭제

Microsoft Graph를 사용하여 카탈로그를 삭제할 수도 있습니다. 위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션을 사용하는 적절한 역할의 사용자는 이 API를 호출하여 [accessPackageCatalog 삭제](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)를 수행합니다.

## <a name="next-steps"></a>다음 단계

[액세스 패키지 관리자에 액세스 거버넌스 위임](entitlement-management-delegate-managers.md)