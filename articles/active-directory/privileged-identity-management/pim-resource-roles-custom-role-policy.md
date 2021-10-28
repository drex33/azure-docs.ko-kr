---
title: PIM에서 Azure 사용자 지정 역할 사용 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 사용자 지정 역할을 사용하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a114c233372636ca26c847d819103651ad455e97
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668792"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 사용자 지정 역할 사용

Azure AD(Azure Active Directory) 조직에서 권한 있는 역할의 일부 사용자에게 엄격한 PIM(Privileged Identity Management) 설정을 적용하고 다른 사용자에게 더 많은 자율성을 제공해야 하는 경우가 있습니다. Azure 구독에서 실행될 애플리케이션의 개발을 지원하기 위해 조직이 일부 계약 당사자를 채용하는 시나리오를 가정합니다.

리소스 관리자로서 직원이 승인 없이 액세스할 수 있도록 하려고 합니다. 그러나 조직의 리소스에 대한 액세스를 요청할 때 모든 계약 당사자는 승인을 받아야 합니다.

다음 섹션에 설명된 단계를 수행하여 Azure 리소스 역할에 대해 대상 Privileged Identity Management 설정을 지정합니다.

## <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

리소스에 대한 사용자 지정 역할을 만들려면 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)에 설명된 단계를 따릅니다.

사용자 지정 역할을 만들 때 복제하려는 기본 제공 역할을 쉽게 기억할 수 있도록 설명이 포함된 이름을 사용합니다.

> [!NOTE]
> 사용자 지정 역할이 복제하려는 기본 제공 역할의 복제본이 되고, 해당 범위는 기본 제공 역할과 일치하도록 합니다.

## <a name="apply-pim-settings"></a>PIM 설정 적용

역할을 Azure AD 조직에서 생성한 후 Azure Portal의 **Privileged Identity Management - Azure 리소스** 창으로 이동합니다. 역할을 적용하는 리소스를 선택합니다.

![“Privileged Identity Management - Azure 리소스” 창](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

역할의 이러한 멤버에 적용해야 하는 [Privileged Identity Management 역할 설정을 구성](pim-resource-roles-configure-role-settings.md)합니다.

마지막으로 이러한 설정으로 대상으로 할 멤버의 고유 그룹에 [역할을 할당](pim-resource-roles-assign-roles.md)합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Azure의 사용자 지정 역할](../../role-based-access-control/custom-roles.md)