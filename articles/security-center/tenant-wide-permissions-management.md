---
title: Azure Security Center에서 테넌트 전체 권한 부여 및 요청
description: Azure Security Center에서 테넌트 전체 권한을 관리하는 방법 알아보기
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: 99e7e09ac1b73dfb24cea14fe1b407f491e4ade5
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236898"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>테넌트 전체 표시 허용 및 요청

**전역 관리자** 의 Azure AD(Active Directory) 역할을 가진 사용자는 테넌트 전체의 책임이 있지만 Azure Security Center에서 해당 조직 전체 정보를 볼 수 있는 Azure 권한은 없습니다. Azure AD 역할 할당이 Azure 리소스에 대한 액세스 권한을 부여하지 않으므로 권한 상승이 필요합니다. 

## <a name="grant-tenant-wide-permissions-to-yourself"></a>자신에게 테넌트 전체 권한 부여

테넌트 수준 권한을 스스로 부여하려면 다음과 같이 합니다.

1. 조직이 [Azure AD PIM(Privileged Identity Management)](../active-directory/privileged-identity-management/pim-configure.md) 또는 다른 PIM 도구를 사용하여 리소스 액세스를 관리하는 경우 아래 절차에 따라 사용자의 전역 관리자 역할이 활성화되어 있어야 합니다.

1. 테넌트의 루트 관리 그룹에 할당되지 않은 전역 관리자 사용자는 Security Center의 **개요** 페이지를 열고 배너에서 **테넌트 전체 표시** 링크를 누릅니다. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Azure Security Center에서 테넌트 수준 권한 사용.":::

1. 할당할 새 Azure 역할을 선택합니다. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="사용자에게 할당할 테넌트 수준 권한을 정의하는 양식.":::

    > [!TIP]
    > 일반적으로 보안 관리자 역할은 루트 수준에서 정책을 적용해야 하며, 보안 읽기 권한자는 테넌트 수준 가시성을 제공하는 데 충분합니다. 이러한 역할로 부여되는 권한에 대한 자세한 내용은 [보안 관리자 기본 제공 역할 설명](../role-based-access-control/built-in-roles.md#security-admin) 또는 [보안 읽기 권한자 기본 제공 역할 설명](../role-based-access-control/built-in-roles.md#security-reader)을 참조하세요.
    >
    > Security Center와 관련된 해당 역할 간의 차이는 [역할 및 허용되는 작업](security-center-permissions.md#roles-and-allowed-actions)에 있는 표를 참조하세요.

    조직 전체 보기는 테넌트의 루트 관리 그룹 수준에서 역할을 부여하여 수행됩니다.  

1. Azure Portal에서 로그아웃한 다음, 다시 로그인합니다.

1. 액세스 권한을 승격하면 Azure Security Center를 열거나 새로 고쳐 Azure AD 테넌트 아래의 모든 구독에 대한 가시성이 있는지 확인합니다. 

위의 간단한 프로세스는 여러 가지 작업을 자동으로 수행합니다.

1. 사용자의 권한이 일시적으로 승격됩니다.
1. 새 권한을 사용하여 사용자는 루트 관리 그룹의 원하는 Azure RBAC 역할에 할당됩니다.
1. 승격된 권한이 제거됩니다.

Azure AD 권한 승격 프로세스에 대한 자세한 내용은 [모든 Azure 구독 및 관리 그룹 관리를 위한 액세스 권한 승격](../role-based-access-control/elevate-access-global-admin.md)을 참조하세요.


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>권한이 부족할 때 테넌트 전체 권한 요청하기

Security Center에 로그인하여 보기가 제한적이라는 배너가 표시되면 배너를 클릭하여 해당 조직의 전역 관리자에게 요청을 보낼 수 있습니다. 할당받고 싶은 역할을 요청에 포함하면, 전역 관리자가 어떤 역할을 부여할지 결정할 수 있습니다. 

해당 요청을 수락할지 거부할지는 전역 관리자의 결정입니다. 

> [!IMPORTANT]
> 7일에 한 번만 요청을 제출할 수 있습니다.

전역 관리자에게 권한 승격을 요청하려면 다음과 같이 합니다.

1. Azure Portal에서 Azure Security Center를 엽니다.

1. “제한된 정보를 볼 수 있습니다.”라는 배너가 표시되면 배너를 선택합니다.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="사용자에게 테넌트 전체의 사용 권한을 요청할 수 있음을 알리는 배너입니다.":::

1. 상세 요청 양식에서 원하는 역할과 권한이 필요한 이유를 선택합니다.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Azure 전역 관리자에게 테넌트 전체 권한을 요청하기 위한 세부 정보 페이지.":::

1. **액세스 요청** 을 선택합니다.

    전역 관리자에게 메일을 보냅니다. 메일에는 요청을 승인하거나 거부할 수 있는 Security Center의 링크가 포함되어 있습니다.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="새 권한을 얻기 위해 전역 관리자에게 이메일 보내기.":::

    전역 관리자가 **요청 검토** 를 선택하고 프로세스를 완료한 후에는 요청을 보낸 사용자에게 메일이 발송됩니다. 

## <a name="next-steps"></a>다음 단계

다음 관련 페이지에서 Security Center 권한에 대해 자세히 알아보세요.

- [Azure Security Center의 권한](security-center-permissions.md)