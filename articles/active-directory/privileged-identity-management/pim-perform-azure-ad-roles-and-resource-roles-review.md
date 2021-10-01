---
title: PIM에서 Azure 리소스 역할 및 Azure AD 역할의 액세스 검토 수행 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 및 Azure AD 역할의 액세스를 검토하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc90d7305ced649e0ea754431f9c8ce54ddc57b
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452754"
---
# <a name="perform-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 수행

PIM(Privileged Identity Management)은 기업이 Azure AD(Active Directory)와 Microsoft 365 또는 Microsoft Intune 같은 Microsoft 온라인 서비스 리소스에 대한 권한 있는 액세스를 관리하는 방법을 단순화합니다. 이 문서의 단계에 따라 역할에 대한 액세스 검토를 수행합니다.

관리 역할에 할당된 경우 조직의 권한 있는 역할 관리자가 작업에 해당 역할이 여전히 필요한지 정기적으로 확인하도록 요청할 수 있습니다. 링크가 포함된 이메일을 받거나 [Azure Portal](https://portal.azure.com)로 바로 이동하여 시작할 수 있습니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자 또는 전역 관리자인 경우 [액세스 검토를 시작하는 방법](pim-create-azure-ad-roles-and-resource-roles-review.md)에서 자세한 내용을 참조하세요.

## <a name="approve-or-deny-access"></a>액세스 승인 또는 거부

사용자가 계속 역할에 액세스해야 하는지 여부에 따라 액세스를 승인하거나 거부할 수 있습니다. 역할을 유지하려면 **승인** 을 선택하고, 더 이상 액세스할 필요가 없는 경우 **거부** 를 선택합니다. 사용자의 할당 상태는 검토가 종료되고 관리자가 결과를 적용할 때까지 변경되지 않습니다. 특정 거부된 사용자에게 결과가 적용될 수 없는 일반적인 시나리오에는 다음이 포함될 수 있습니다.

- **동기화된 온-프레미스 Windows AD 그룹의 멤버 검토**: 온-프레미스 Windows AD에서 동기화되는 그룹은 Azure AD에서 관리할 수 없으므로 멤버 자격을 변경할 수 없습니다.
- **중첩된 그룹이 할당된 역할 검토**: 중첩된 그룹을 통해 멤버 자격이 있는 사용자의 경우 액세스 검토는 중첩된 그룹에 대한 멤버 자격을 제거하지 않으므로 사용자는 검토 중인 역할에 대한 액세스를 유지합니다.
- **사용자를 찾을 수 없음 또는 기타 오류**: 이로 인해 적용 결과가 지원되지 않을 수도 있습니다.

액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 를 선택하고 **Privileged Identity Management** 를 엽니다.
1. **액세스 검토** 를 선택합니다. 보류 중인 액세스 검토가 있으면 액세스 검토 페이지에 표시됩니다.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png" alt-text="Azure AD 역할의 액세스 검토 블레이드가 선택된 Privileged Identity Management 애플리케이션의 스크린샷" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png":::

1. 완료할 검토를 선택합니다.
1. **승인** 또는 **거부** 를 선택합니다. 필요한 경우 **이유 제공 상자** 에 결정에 대한 비즈니스 타당성을 입력합니다.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png" alt-text="Azure AD 역할의 선택된 액세스 검토가 있는 Privileged Identity Management 애플리케이션 스크린샷" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png":::

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 만들기](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 완료](pim-complete-azure-ad-roles-and-resource-roles-review.md)
