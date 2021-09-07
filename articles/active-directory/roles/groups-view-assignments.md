---
title: Azure Active Directory에서 그룹에 할당된 역할 보기 | Microsoft Docs
description: Azure Portal을 사용하여 그룹에 할당된 역할을 볼 수 있는 방법에 대해 알아봅니다. 그룹 및 할당된 역할 보기는 기본 사용자 권한입니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87e59c7ab9bfc5fa9211d84d2d9a855dea97ec1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535905"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹에 할당된 역할 보기

이 섹션에서는 Azure Portal을 사용하여 그룹에 할당된 역할을 볼 수 있는 방법에 대해 설명합니다. 그룹 및 할당된 역할 보기는 기본 사용자 권한입니다.

## <a name="prerequisites"></a>필수 구성 요소

- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **그룹** 을 선택합니다.

1. 관심 있는 역할 할당 가능 그룹을 선택합니다.

1. **할당된 역할** 을 선택합니다. 해당 그룹에 할당된 모든 Azure AD 역할을 볼 수 있습니다.

   ![선택한 그룹에 할당된 모든 역할 보기](./media/groups-view-assignments/view-assignments.png)

## <a name="powershell"></a>PowerShell

### <a name="get-object-id-of-the-group"></a>그룹의 개체 ID 가져오기

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>그룹에 대한 역할 할당 보기

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="microsoft-graph-api"></a>Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>그룹의 개체 ID 가져오기

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>그룹에 대한 역할 할당 가져오기

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [그룹에 할당된 Azure AD 역할 문제 해결](groups-faq-troubleshooting.yml)
