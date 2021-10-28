---
title: PIM에서 Azure 리소스 역할 및 Azure AD 역할의 액세스 검토 완료 - Azure AD | Microsoft Docs
description: Azure Active Directory에서 Azure 리소스 및 Azure AD 역할 Privileged Identity Management의 액세스 검토를 완료하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7148284bef15b2e3f40a39652d0cb222ed5793c0
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669989"
---
# <a name="complete-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 완료

[액세스 검토가 시작](pim-create-azure-ad-roles-and-resource-roles-review.md)되면 권한 있는 역할 관리자가 권한이 있는 액세스를 검토할 수 있습니다. Azure AD(Azure Active Directory)의 PIM(Privileged Identity Management)에서는 사용자에게 해당 액세스를 검토하도록 요청하는 메일을 자동으로 보냅니다. 사용자가 메일을 받지 못하는 경우 [액세스 검토를 수행하는 방법](pim-perform-azure-ad-roles-and-resource-roles-review.md)에 대한 지침을 보낼 수 있습니다.

검토가 만들어지면 이 문서의 단계에 따라 검토를 완료하고 결과를 확인합니다.

## <a name="complete-access-reviews"></a>액세스 검토 완료

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. **Azure 리소스** 의 경우 **Privileged Identity Management** 로 이동하고 대시보드의 **관리** 에서 **Azure 리소스** 를 선택합니다. **Azure AD 역할** 의 경우 동일한 대시보드에서 **Azure AD 역할** 을 선택합니다.

2. **Azure 리소스** 의 경우 **Azure 리소스** 에서 리소스를 선택한 다음, 대시보드에서 **액세스 검토** 를 선택합니다. **Azure AD 역할** 의 경우 대시보드에서 **액세스 검토** 로 직접 진행합니다.

3. 관리하려는 액세스 검토를 선택합니다. 다음은 **Azure 리소스** 및 **Azure AD 역할** 모두에 대한 **액세스 검토** 개요의 샘플 스크린샷입니다.

    :::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png" alt-text="역할, 소유자, 시작 날짜, 종료 날짜, 상태를 표시하는 액세스 검토 목록 스크린샷" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png":::

세부 정보 페이지에서 **Azure 리소스** 및 **Azure AD 역할** 의 검토를 관리하는 데 사용할 수 있는 옵션은 다음과 같습니다.

![Azure 리소스의 검토 관리 옵션 - 중지, 다시 설정, 적용, 삭제 스크린샷](media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-menu.png)

### <a name="stop-an-access-review"></a>액세스 검토 중지

모든 액세스 검토는 종료 날짜가 있지만, **중지** 단추를 사용하여 일찍 완료할 수 있습니다. **중지** 단추는 검토 인스턴스가 활성 상태일 때만 선택할 수 있습니다. 검토를 중단한 후에는 다시 시작할 수 없습니다.

### <a name="reset-an-access-review"></a>액세스 검토 다시 설정

검토 인스턴스가 활성 상태이고 검토자가 하나 이상의 결정을 내린 경우 **다시 설정** 단추를 선택하여 액세스 검토를 다시 설정하고 해당 인스턴스에 적용된 모든 결정을 제거할 수 있습니다. 액세스 검토를 초기화하면 모든 사용자가 다시 검토되지 않음으로 표시됩니다.

### <a name="apply-an-access-review"></a>액세스 검토 적용

종료 날짜가 되었거나 수동으로 중지하여 액세스 검토가 완료되면 **적용** 단추가 거부된 사용자의 역할 액세스를 제거합니다. 사용자의 액세스가 검토 중에 거부되었다면 이는 사용자의 역할 할당을 제거할 단계입니다. 검토 생성 시 **자동 적용** 설정이 구성된 경우에는 검토가 수동이 아닌 자동으로 적용되므로 이 단추는 항상 사용하지 않도록 설정됩니다.

### <a name="delete-an-access-review"></a>액세스 검토 삭제

더 이상 검토가 필요 없는 경우 검토를 삭제합니다. Privileged Identity Management 서비스에서 액세스 검토를 제거하려면 **삭제** 단추를 선택합니다.

> [!IMPORTANT]
> 파괴적인 변경을 확인할 필요는 없으므로 해당 검토를 삭제할 것인지 확인합니다.

## <a name="results"></a>결과

**결과** 페이지에서 검토 결과 목록을 보고 다운로드할 수 있습니다.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png" alt-text="Azure AD 역할의 사용자, 결과, 이유, 검토자, 적용자, 결과 적용을 나열한 결과 페이지 스크린샷" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png":::

> [!Note]
> **Azure AD 역할** 에는 역할에 그룹을 할당할 수 있는 역할 할당 가능 그룹의 개념이 있습니다. 이 경우 그룹의 멤버를 확장하는 대신 검토에 그룹이 표시되고 검토자는 전체 그룹을 승인하거나 거부합니다.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png" alt-text="Azure 리소스 역할의 사용자, 결과, 이유, 검토자, 적용자, 결과 적용을 나열한 결과 페이지 스크린샷" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png":::

> [!Note]
>그룹이 **Azure 리소스 역할** 에 할당된 경우 Azure 리소스 역할의 검토자는 중첩된 그룹에서 사용자의 확장된 목록을 볼 수 있습니다. 검토자가 중첩된 그룹의 멤버를 거부하는 경우 사용자가 중첩된 그룹에서 제거되지 않으므로 해당 거부 결과가 제대로 적용되지 않습니다.

## <a name="reviewers"></a>검토자

**검토자** 페이지에서 검토자를 보고 기존 액세스 검토에 추가할 수 있습니다. 여기에서 검토를 완료하도록 검토자에게 이리 알릴 수도 있습니다.

> [!Note]
> 선택한 검토자 유형이 사용자 또는 그룹인 경우 언제든지 더 많은 사용자 또는 그룹을 기본 검토자로 추가할 수 있습니다. 언제든지 기본 검토자를 제거할 수도 있습니다. 검토자 유형이 관리자인 경우 사용자 또는 그룹을 대체 검토자로 추가하여 관리자가 없는 사용자에 대한 검토를 완료할 수 있습니다. 대체 검토자는 제거할 수 없습니다.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png" alt-text="Azure 리소스 역할의 이름 및 사용자 계정 이름을 나열하는 검토자 페이지 스크린샷" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png":::

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 만들기](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [PIM에서 Azure 리소스 및 Azure AD 역할의 액세스 검토 수행](pim-perform-azure-ad-roles-and-resource-roles-review.md)
