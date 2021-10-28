---
title: 액세스 검토를 사용하여 게스트 액세스 관리 - Azure AD
description: Azure Active Directory 액세스 검토를 사용하여 게스트 사용자를 그룹 구성원으로 또는 애플리케이션에 할당된 사용자로 관리합니다.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 4/16/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0ab3a8540a4b2c5050026c78de04796cbbd355
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823066"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Azure AD 액세스 검토를 사용하여 게스트 액세스 관리


Azure AD(Azure Active Directory)를 사용하면 [Azure AD B2B 기능](../external-identities/what-is-b2b.md)을 통해 조직의 경계를 넘나들며 협업을 쉽게 수행할 수 있습니다. 다른 테넌트의 게스트 사용자는 [관리자](../external-identities/add-users-administrator.md) 또는 [다른 사용자](../external-identities/what-is-b2b.md)가 초대할 수 있습니다. 이 기능은 Microsoft 계정과 같은 소셜 ID에도 적용됩니다.

또한 게스트 사용자에게 적절한 액세스 권한을 쉽게 부여할 수도 있습니다. 게스트 본인 또는 의사 결정자에게 액세스 검토에 참여하고 게스트 액세스 권한을 다시 인증(또는 증명)하도록 요청할 수 있습니다. 검토자는 Azure AD에서의 제안 사항을 기반으로 각 사용자의 지속적인 액세스 요구 사항에 대한 입력을 제공할 수 있습니다. 액세스 검토가 완료되면 변경 사항을 적용하거나 액세스가 더 이상 필요 없는 게스트의 액세스를 제거할 수 있습니다.

> [!NOTE]
> 이 문서에서는 게스트 사용자의 액세스 검토에 중점을 둡니다. 게스트 뿐 아니라 모든 사용자의 액세스를 검토하려는 경우 [액세스 검토를 사용하여 사용자 액세스 관리](manage-user-access-with-access-reviews.md)를 참조하세요. 전역 관리자와 같은 관리 역할의 사용자의 구성원 자격을 검토하려면 [Azure AD Privileged Identity Management에서 액세스 검토를 시작하는 방법](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure AD Premium P2

자세한 내용은 [라이선스 요구 사항](access-reviews-overview.md#license-requirements)을 확인하세요.

## <a name="create-and-perform-an-access-review-for-guests"></a>게스트에 대한 액세스 검토 만들기 및 수행

먼저 다음 역할 중 하나에 할당되어야 합니다.
- 전역 관리자
- 사용자 관리자
- (미리 보기) 검토할 그룹의 M365 또는 AAD 보안 그룹 소유자

그런 다음 [ID 거버넌스 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동하여 조직에 대한 액세스 검토가 준비되었는지 확인합니다.

Azure AD는 게스트 사용자를 검토하기 위한 몇 가지 시나리오를 지원합니다.

다음 중 하나를 검토할 수 있습니다.

 - Azure AD에서 하나 이상의 게스트 구성원이 있는 그룹
 - Azure AD에 연결되어 한 명 이상의 게스트 사용자가 할당된 애플리케이션 

Microsoft 365 그룹에 대한 게스트 사용자 액세스를 검토할 때 각 그룹에 대한 검토를 개별적으로 만들거나 모든 Microsoft 365 그룹에서 게스트 사용자의 반복되는 자동 액세스 검토를 설정할 수 있습니다. 다음 비디오는 게스트 사용자의 반복되는 액세스 검토에 대한 자세한 정보를 제공합니다. 

> [!VIDEO https://www.youtube.com/embed/3D2_YW2DwQ8]

각 게스트에게 자신의 액세스를 검토하도록 할지 또는 한 명 이상의 사용자에게 모든 게스트 액세스를 검토하도록 요청할지를 결정할 수 있습니다.

 이러한 시나리오에 대해서는 다음 섹션에서 설명됩니다.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>게스트에게 자신의 그룹 구성원 자격을 검토하도록 요청

액세스 검토를 사용하여 그룹에 초대되고 추가된 사용자가 계속 액세스해야 하는지 확인할 수 있습니다. 게스트에게 해당 그룹에서 자신의 구성원 자격을 검토하도록 쉽게 요청할 수 있습니다.

1. 그룹에 대한 액세스 검토를 만들려면 게스트 사용자 구성원만 포함하도록 검토를 선택하고, 해당 구성원이 직접 검토하도록 합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)를 참조하세요.

2. 각 게스트에게 자신의 구성원 자격을 검토하도록 요청합니다. 기본적으로 초대를 수락한 각 게스트는 Azure AD에서 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다. Azure AD에는 [그룹 또는 애플리케이션에 대한 액세스를 검토하는 방법](perform-access-review.md)에 대한 게스트 지침이 있습니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)를 참조하세요.

4. 지속적인 액세스에 대한 자신의 요구 사항을 거부한 사용자 외에도 응답하지 않은 사용자를 제거할 수 있습니다. 응답하지 않은 사용자는 더 이상 메일을 수신하지 못할 수 있습니다.

5. 그룹이 액세스 관리에 사용되지 않는 경우 초대를 수락하지 않았기 때문에 검토에 참여하도록 선택되지 않은 사용자도 제거할 수 있습니다. 수락되지 않은 경우 초대된 사용자의 전자 메일 주소에 오타가 있는 것일 수 있습니다. 그룹이 메일 그룹으로 사용되면 일부 게스트 사용자가 연락처 개체이므로 참여하도록 선택되지 않았을 수 있습니다.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>스폰서에게 게스트의 그룹 구성원 자격을 검토하도록 요청

그룹 소유자와 같은 스폰서에게 지속적인 그룹 구성원 자격에 대한 게스트의 요구 사항을 검토하도록 요청할 수 있습니다.

1. 그룹에 대한 액세스 검토를 만들려면 게스트 사용자 구성원만 포함하도록 검토를 선택합니다. 그런 후 한 명 이상의 검토자를 지정합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)를 참조하세요.

2. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 [ 그룹 또는 애플리케이션에 대한 액세스 권한을 검토](perform-access-review.md)하는 액세스 패널 링크가 포함된 이메일을 Azure AD에서 받습니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)를 참조하세요.

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>게스트에게 애플리케이션에 대한 자신의 액세스를 검토하도록 요청

액세스 검토를 사용하여 특정 애플리케이션에 초대된 사용자가 계속 액세스해야 하는지 확인할 수 있습니다. 게스트에게 자신의 액세스 요구 사항을 검토하도록 쉽게 요청할 수 있습니다.

1. 애플리케이션에 대한 액세스 검토를 만들려면 게스트만 포함하도록 검토를 선택하고, 해당 사용자가 직접 액세스를 검토하도록 합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)를 참조하세요.

2. 각 게스트에게 애플리케이션에 대한 자신의 액세스를 검토하도록 요청 기본적으로 초대를 수락한 각 게스트는 Azure AD에서 이메일을 받게 됩니다. 이 이메일에는 조직의 액세스 패널의 액세스 검토로 연결되는 링크가 포함되어 있습니다. Azure AD에는 [그룹 또는 애플리케이션에 대한 액세스를 검토하는 방법](perform-access-review.md)에 대한 게스트 지침이 있습니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)를 참조하세요.

4. 지속적인 액세스에 대한 자신의 요구 사항을 거부한 사용자 외에도 응답하지 않은 게스트 사용자를 제거할 수 있습니다. 응답하지 않은 사용자는 더 이상 메일을 수신하지 못할 수 있습니다. 참여하도록 선택되지 않은 게스트 사용자, 특히 최근에 초대되지 않은 사용자도 제거할 수 있습니다. 해당 사용자는 초대를 수락하지 않았으므로 애플리케이션에 대한 액세스 권한이 없습니다. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>스폰서에게 애플리케이션에 대한 게스트의 액세스를 검토하도록 요청

애플리케이션 소유자와 같은 스폰서에게 지속적인 애플리케이션 액세스에 대한 게스트의 요구 사항을 검토하도록 요청할 수 있습니다.

1. 애플리케이션에 대한 액세스 검토를 만들려면 게스트만 포함하도록 검토를 선택합니다. 그런 후 한 명 이상의 사용자를 검토자로 지정합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)를 참조하세요.

2. 입력을 제공하도록 검토자에게 요청합니다. 기본적으로 각 사용자는 [ 그룹 또는 애플리케이션에 대한 액세스 권한을 검토](perform-access-review.md)하는 액세스 패널 링크가 포함된 이메일을 Azure AD에서 받습니다.

3. 검토자가 입력을 제공하면 액세스 검토를 중지하고 변경 내용을 적용합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)를 참조하세요.

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>게스트에게 자신의 일반적 액세스 요구 사항을 검토하도록 요청

일부 조직에서는 게스트가 그룹 구성원 자격을 인식하지 못할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure Portal은 UserType이 Guest인 사용자의 관리 액세스를 허용하지 않았습니다. 경우에 따라 디렉터리의 관리자가 PowerShell을 사용하여 게스트의 UserType 값을 Member로 변경했을 수 있습니다. 이전에 이러한 변경 사항이 디렉터리에서 발생한 경우, 이전에 관리자 액세스 권한이 있던 모든 게스트 사용자가 이전 쿼리에 포함되지 않을 수도 있습니다. 이 경우 게스트의 UserType을 변경하거나 그룹 구성원 자격에 해당 게스트를 수동으로 포함해야 합니다.

1. 적합한 그룹이 아직 없는 경우 Azure AD에 해당 게스트를 구성원으로 하는 보안 그룹을 만듭니다. 예를 들어 수동으로 유지 관리되는 게스트의 구성원 자격으로 그룹을 만들 수 있습니다. 또는 UserType 특성 값이 Guest인 Contoso 테넌트의 사용자에 대해 "Contoso 게스트"와 같은 이름으로 동적 그룹을 만들 수도 있습니다.  효율성을 위해 그룹이 주요 게스트인지 확인합니다. 멤버 사용자는 검토할 필요가 없으므로 멤버 사용자가 있는 그룹은 선택하지 않도록 합니다.  또한 그룹의 멤버인 게스트 사용자는 그룹의 다른 멤버를 볼 수 있습니다.

2. 해당 그룹에 대한 액세스 검토를 만들려면 직접 구성원이 되도록 검토자를 선택합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)를 참조하세요.

3. 각 게스트에게 자신의 구성원 자격을 검토하도록 요청합니다. 기본적으로 초대를 수락한 각 게스트는 Azure AD에서 조직의 액세스 패널에 있는 액세스 검토에 대한 링크가 포함된 전자 메일을 받게 됩니다. Azure AD에는 [그룹 또는 애플리케이션에 대한 액세스를 검토하는 방법](perform-access-review.md)에 대한 게스트 지침이 있습니다.  자신의 초대를 수락하지 않은 게스트는 검토 결과에서 “알림을 받지 않음”으로 표시됩니다.

4. 검토자가 입력을 제공하면 액세스 검토를 중지합니다. 자세한 내용은 [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)를 참조하세요.

5. 거부되었거나 검토를 완료하지 않았거나 이전에 초대를 수락하지 않은 게스트에 대한 게스트 액세스를 제거합니다. 게스트 중 일부가 검토에 참여하도록 선택되었거나 이전에 초대를 수락하지 않았던 연락처인 경우, Azure Portal 또는 PowerShell을 사용하여 해당 계정을 사용하지 않도록 설정할 수 있습니다. 게스트가 더 이상 액세스할 필요가 없고 연락처가 아닌 경우, Azure Portal 또는 PowerShell을 사용하여 사용자 개체를 디렉터리에서 제거하여 게스트 사용자 개체를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)