---
title: Azure AD 권한 관리에서 액세스 패키지의 리소스 역할 변경 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 기존 액세스 패키지의 리소스 역할을 변경하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c7439263f15a99a7b717bac1dc48e06a77bb66b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257132"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지의 리소스 역할 변경

액세스 패키지 관리자는 새 리소스에 대한 사용자의 액세스 권한을 프로비전하거나 이전 리소스에서 액세스 권한을 제거하는 것에 대한 걱정 없이 언제든지 액세스 패키지의 리소스를 변경할 수 있습니다. 이 문서에서는 기존 액세스 패키지의 리소스 역할을 변경하는 방법을 설명합니다.

이 비디오는 액세스 패키지를 변경하는 방법의 개요를 제공합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>리소스의 카탈로그 확인

액세스 패키지에 리소스를 추가해야 하는 경우 카탈로그에서 필요한 리소스를 사용할 수 있는지 확인해야 합니다. 액세스 패키지 관리자인 경우 리소스를 소유하고 있더라도 카탈로그에 추가할 수 없습니다. 카탈로그에서 사용 가능한 리소스만 사용하도록 제한됩니다.

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭한 다음 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스** 를 클릭하여 이 카탈로그의 리소스 목록을 봅니다.

    ![카탈로그의 리소스 목록](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. 액세스 패키지 관리자이고 카탈로그에 리소스를 추가해야 하는 경우 카탈로그 소유자에게 리소스 추가를 요청할 수 있습니다.

## <a name="add-resource-roles"></a>리소스 역할 추가

리소스 역할이란 리소스와 연결된 권한의 컬렉션입니다. 사용자가 리소스를 요청할 수 있도록 하는 방법은 카탈로그 내 각 리소스로부터 리소스 역할을 액세스 패키지에 추가하는 것입니다. 그룹, 팀, 애플리케이션, SharePoint 사이트에 의해 제공되는 리소스 역할을 추가할 수 있습니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할** 을 클릭합니다.

1. **리소스 역할 추가** 를 클릭하여 액세스 패키지에 리소스 역할 추가 페이지를 엽니다.

    ![액세스 패키지 - 리소스 역할 추가](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. 그룹, 팀, 애플리케이션 또는 SharePoint 사이트를 추가할지 여부에 따라 다음 리소스 역할 섹션 중 하나의 단계를 수행합니다.

## <a name="add-a-group-or-team-resource-role"></a>그룹 또는 팀 리소스 역할 추가

사용자에게 액세스 패키지가 할당된 경우 권한 관리가 자동으로 Microsoft Teams의 그룹 또는 팀에 사용자를 자동으로 추가하도록 할 수 있습니다. 

- 그룹 또는 팀이 액세스 패키지의 일부이고 해당 액세스 패키지에 사용자가 할당된 경우 해당 사용자는 해당 그룹 또는 팀에 추가됩니다(아직 없는 경우).
- 사용자의 액세스 패키지 할당이 만료되는 경우 사용자에게 그룹 또는 팀을 포함하는 다른 액세스 패키지에 대한 할당이 현재 있지 않으면 해당 사용자는 해당 그룹 또는 팀에서 제거됩니다.

어떤 [Azure AD 보안 그룹 또는 Microsoft 365 그룹](../fundamentals/active-directory-groups-create-azure-portal.md)도 선택할 수 있습니다. 관리자는 카탈로그에 어떤 그룹도 추가할 수 있습니다. 그룹의 소유자인 카탈로그 소유자는 카탈로그에 어떤 그룹도 추가할 수 있습니다. 그룹을 선택할 때 다음 Azure AD 제약 조건을 염두에 두어야 합니다.

- 게스트를 비롯하여 그룹 또는 팀에 멤버로 추가되는 사용자는 해당 그룹 또는 팀의 다른 모든 멤버를 볼 수 있습니다.
- Azure AD는 Azure AD Connect를 사용하여 Windows Server Active Directory에서 동기화되었거나 Exchange Online에서 배포 목록으로 만든 그룹의 멤버 자격을 변경할 수 없습니다.  
- 동적 그룹의 멤버 자격은 멤버를 추가하거나 제거하여 업데이트할 수 없으므로 동적 그룹 멤버 자격은 권한 관리와 함께 사용하기에 적합하지 않습니다.

자세한 내용은 [그룹 비교](/office365/admin/create-groups/compare-groups)와 [Microsoft 365 그룹 및 Microsoft Teams](/microsoftteams/office-365-groups)를 참조하세요.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **그룹 및 팀** 을 클릭하여 그룹 선택 창을 엽니다.

1. 액세스 패키지에 포함하려는 그룹 및 팀을 선택합니다.

    ![액세스 패키지 - 리소스 역할 추가 - 그룹 선택](./media/entitlement-management-access-package-resources/group-select.png)

1. **선택** 을 클릭합니다.

    그룹 또는 팀을 선택하면 **하위 유형** 열에 다음 하위 유형 중 하나가 나열됩니다.

    | 하위 유형 | 설명 |
    | --- | --- |
    | 보안 | 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다. |
    | 배포 | 사용자 그룹에 알림을 보내는 데 사용됩니다. |
    | Microsoft 365 | Teams가 지원되지 않는 Microsoft 365 그룹입니다. 회사 내부 및 외부의 사용자 간 협업에 사용됩니다. |
    | 팀 | Teams가 지원되는 Microsoft 365 그룹입니다. 회사 내부 및 외부의 사용자 간 협업에 사용됩니다. |

1. **역할** 목록에서 **소유자** 또는 **멤버** 를 선택합니다.

    일반적으로 멤버 역할을 선택합니다. 소유자 역할을 선택하면 사용자가 다른 멤버 또는 소유자를 추가하거나 제거할 수 있습니다.

    ![액세스 패키지 - 그룹 또는 팀의 리소스 역할 추가](./media/entitlement-management-access-package-resources/group-role.png)

1. **추가** 를 클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 사용자는 그룹 또는 팀이 추가될 때 자동으로 해당 그룹 또는 팀의 멤버가 됩니다.

## <a name="add-an-application-resource-role"></a>애플리케이션 리소스 역할 추가

사용자에게 액세스 패키지가 할당된 경우 Azure AD와 통합된 SaaS 애플리케이션 및 조직의 애플리케이션을 포함한 Azure AD 엔터프라이즈 애플리케이션에 대한 사용자 액세스 권한을 Azure AD가 자동으로 할당하도록 할 수 있습니다. 페더레이션 Single Sign-On을 통해 Azure AD와 통합되는 애플리케이션의 경우 Azure AD는 해당 애플리케이션에 할당된 사용자를 위한 페더레이션 토큰을 발급합니다.

애플리케이션에는 여러 역할이 있을 수 있습니다. 애플리케이션을 액세스 패키지에 추가할 때 해당 애플리케이션에 둘 이상의 역할이 있는 경우 해당 사용자에 적절한 역할을 지정해야 합니다. 애플리케이션을 개발 중인 경우 [방법: 엔터프라이즈 애플리케이션용 SAML 토큰에서 발급된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)에서 애플리케이션에 해당 역할을 추가하는 방법에 대해 자세히 알아볼 수 있습니다.

애플리케이션 역할이 액세스 패키지의 일부인 경우:

- 사용자에게 해당 액세스 패키지가 할당되면 해당 사용자는 해당 애플리케이션 역할에 추가됩니다(아직 없는 경우).
- 사용자의 액세스 패키지 할당이 만료되면 애플리케이션 역할을 포함하는 다른 액세스 패키지에 대한 할당이 사용자에게 있는 경우를 제외하고 사용자의 액세스 권한이 해당 애플리케이션에서 제거됩니다.

다음은 애플리케이션을 선택할 때 고려해야 할 몇 가지 사항입니다.

- 애플리케이션에는 해당 역할에 할당된 그룹도 있을 수 있습니다.  액세스 패키지의 애플리케이션 역할 대신 그룹을 추가하도록 선택할 수 있지만 그러면 내 액세스 포털에서 애플리케이션이 액세스 패키지의 일부로 사용자에게 표시되지 않습니다.
- Azure Portal은 애플리케이션으로 선택될 수 없는 서비스에 대한 서비스 주체를 보여줄 수도 있습니다.  특히 **Exchange Online** 및 **SharePoint Online** 은 디렉터리에 리소스 역할이 있는 애플리케이션이 아니라 서비스이므로 액세스 패키지에 포함될 수 없습니다.  대신 그룹 기반 라이선스를 사용하여 해당 서비스에 액세스해야 하는 사용자에게 적절한 라이선스를 설정합니다.
- 인증을 위해 개인 Microsoft 계정 사용자만 지원하고 디렉터리의 조직 계정을 지원하지 않는 애플리케이션은 애플리케이션 역할이 없으며 액세스 패키지 카탈로그에 추가할 수 없습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **애플리케이션** 을 클릭하여 애플리케이션 선택 창을 엽니다.

1. 액세스 패키지에 포함하려는 애플리케이션을 선택합니다.

    ![액세스 패키지 - 리소스 역할 추가 - 애플리케이션 선택](./media/entitlement-management-access-package-resources/application-select.png)

1. **선택** 을 클릭합니다.

1. **역할** 목록에서 애플리케이션 역할을 선택합니다.

    ![액세스 패키지 - 애플리케이션의 리소스 역할 추가](./media/entitlement-management-access-package-resources/application-role.png)

1. **추가** 를 클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 사용자에게는 애플리케이션이 추가될 때 이 애플리케이션에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint 사이트 리소스 역할 추가

사용자에게 액세스 패키지가 할당된 경우 Azure AD는 SharePoint Online 사이트 또는 SharePoint Online 사이트 모음에 대한 사용자 액세스 권한을 자동으로 할당할 수 있습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **SharePoint 사이트** 를 클릭하여 SharePoint Online 사이트 선택 창을 엽니다.

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="액세스 패키지 - 리소스 역할 추가 - SharePoint 사이트 선택 - 포털 보기":::

1. 액세스 패키지에 포함하려는 SharePoint Online 사이트를 선택합니다.

    ![액세스 패키지 - 리소스 역할 추가 - SharePoint Online 사이트 선택](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. **선택** 을 클릭합니다.

1. **역할** 목록에서 SharePoint Online 사이트 역할을 선택합니다.

    ![액세스 패키지 - SharePoint Online 사이트의 리소스 역할 추가](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. **추가** 를 클릭합니다.

    액세스 패키지에 대한 기존 할당이 있는 사용자에게는 SharePoint Online 사이트가 추가될 때 이 사이트에 대한 액세스 권한이 자동으로 부여됩니다.

## <a name="remove-resource-roles"></a>리소스 역할 제거

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할** 을 클릭합니다.

1. 리소스 역할 목록에서 제거하려는 리소스 역할을 찾습니다.

1. 줄임표( **...** )를 클릭한 다음 **리소스 역할 제거** 를 선택합니다.

    액세스 패키지에 대한 기존 할당이 있는 사용자는 이 리소스 역할이 제거될 때 이 리소스 역할에 대한 액세스 권한이 자동으로 취소됩니다.

## <a name="when-changes-are-applied"></a>변경 내용 적용 시기

권한 관리에서 Azure AD는 액세스 패키지의 할당 및 리소스에 대한 대량 변경을 하루에 여러 번 처리합니다. 따라서 할당을 하거나 액세스 패키지의 리소스 역할을 변경하는 경우 Azure AD에서 해당 변경을 수행하는 데 최대 24시간이 걸릴 수 있으며, 여기에 이러한 변경 내용을 다른 Microsoft Online Services 또는 연결된 SaaS 애플리케이션에 전파하는 시간이 추가됩니다. 변경이 몇 가지 개체에만 영향을 주는 경우 Azure AD에서 변경 내용이 적용되는 데는 몇 분밖에 걸리지 않으며, 그 후 다른 Azure AD 구성 요소에서 해당 변경 내용을 검색하고 SaaS 애플리케이션을 업데이트합니다. 변경이 수천 개의 개체에 영향을 주는 경우에는 변경 내용이 적용되려면 더 오랜 시간이 걸립니다. 예를 들어 2개의 애플리케이션과 100명의 사용자 할당이 있는 액세스 패키지가 있고 이 액세스 패키지에 SharePoint 사이트 역할을 추가하기로 결정하는 경우 모든 사용자가 이 SharePoint 사이트 역할에 포함될 때까지 지연이 있을 수 있습니다. Azure AD 감사 로그, Azure AD 프로비전 로그, SharePoint 사이트 감사 로그를 통해 진행률을 모니터링할 수 있습니다.

팀 구성원을 제거하면 Microsoft 365 그룹에서도 제거됩니다. 팀 채팅 기능을 제거하면 작업이 지연될 수 있습니다. 자세한 내용은 [그룹 멤버 자격](/microsoftteams/office-365-groups#group-membership)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
- [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발행된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)
- [SharePoint Online 소개](/sharepoint/introduction)