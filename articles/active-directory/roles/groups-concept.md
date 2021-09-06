---
title: Azure AD 그룹을 사용하여 역할 할당 관리 - Azure Active Directory
description: Azure AD 그룹을 사용하여 Azure Active Directory에서 역할 할당 관리를 간소화할 수 있습니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 07/30/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bebba34e8d323bc60d83151e7b211388776b7b76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536428"
---
# <a name="use-azure-ad-groups-to-manage-role-assignments"></a>Azure AD 그룹을 사용하여 역할 할당 관리

Azure Active Directory(Azure AD)를 사용하면 역할 할당을 위해 Azure AD 그룹을 대상으로 지정할 수 있습니다. 그룹에 역할을 할당하면 전역 관리자 및 권한 있는 역할 관리자의 작업을 최소화하고 Azure AD에서의 역할 할당 관리를 간소화할 수 있습니다.

## <a name="why-assign-roles-to-groups"></a>왜 그룹에 역할을 할당할까요?

Contoso 기업이 자신의 Azure AD 조직에서 직원들의 암호를 관리하고 초기화하기 위해 여러 지역에 걸쳐 인력을 고용했다고 가정해보세요. 권한 있는 역할 관리자 또는 전역 관리자가 각 직원들에게 개별적으로 기술 지원팀 관리자 역할을 할당하도록 요청하는 대신, Contoso_Helpdesk_Administrators 그룹을 만들고 이 그룹에 역할을 할당할 수 있습니다. 사용자가 그룹에 가입하면 역할이 간접적으로 할당됩니다. 그런 후 기존 거버넌스 워크플로를 통해 그룹 멤버 자격의 승인 처리 및 감사를 처리하고 적법한 사용자만 해당 그룹의 멤버가 되고 따라서 기술 지원팀 관리자 역할이 할당되도록 할 수 있습니다.

## <a name="how-role-assignments-to-groups-work"></a>그룹에 대한 역할 할당 작동 방식

그룹에 역할을 할당하려면 `isAssignableToRole` 속성이 `true`로 설정된 새로운 보안 또는 Microsoft 365 그룹을 만들어야 합니다. Azure Portal에서 **Azure AD 역할을 그룹에 할당할 수 있음** 옵션을 **예** 로 설정합니다. 어떤 방법으로든 사용자에게 역할을 할당하는 것과 동일한 방식으로 하나 이상의 Azure AD 역할을 그룹에 할당할 수 있습니다.

![역할 및 관리자 페이지의 스크린샷](./media/groups-concept/role-assignable-group.png)

## <a name="restrictions-for-role-assignable-groups"></a>역할 할당 가능 그룹에 대한 제한 사항

역할 할당 가능 그룹에 대한 제한 사항은 다음과 같습니다.

- 새 그룹에 대해 `isAssignableToRole` 속성 또는 **Azure AD 역할을 그룹에 할당할 수 있음** 옵션만 설정할 수 있습니다.
- `isAssignableToRole` 속성은 **변경 불가능** 합니다. 이 속성을 설정하여 그룹을 생성한 다음에는 변경할 수 없습니다.
- 기존 그룹을 역할 할당 가능 그룹으로 만들 수 없습니다.
- 단일 Azure AD 조직(테넌트)에서 최대 300개의 역할 할당 가능 그룹을 만들 수 있습니다.

## <a name="how-are-role-assignable-groups-protected"></a>역할 할당 가능 그룹은 어떻게 보호되나요?

그룹이 역할에 할당된 경우 그룹 멤버 자격을 관리할 수 있는 IT 관리자도 해당 역할의 멤버 자격을 간접적으로 관리할 수 있습니다. 예를 들어 Contoso_User_Administrators라는 그룹에 사용자 관리자 역할이 할당되었다고 가정해보세요. 그룹 멤버 자격을 수정할 수 있는 Exchange 관리자는 자신을 Contoso_User_Administrators 그룹에 추가하여 사용자 관리자가 될 수 있습니다. 이와 같이 관리자가 의도치 않은 방식으로 자신의 권한을 상승시킬 수 있습니다.

생성 시 `isAssignableToRole` 속성이 `true`로 설정된 그룹에만 역할을 할당할 수 있습니다. 이 속성은 변경 불가능합니다. 이 속성을 설정하여 그룹을 생성한 다음에는 변경할 수 없습니다. 기존 그룹에는 속성을 설정할 수 없습니다.

역할 할당 가능 그룹은 다음 제한 사항을 지정함으로써 발생 가능한 위반을 방지하도록 디자인되었습니다.

- 전역 관리자 및 권한 있는 역할 관리자만 역할 할당 가능 그룹을 만들 수 있습니다.
- 역할 할당 가능 그룹의 멤버 자격 유형은 할당됨이어야 하고 Azure AD 동적 그룹일 수 없습니다. 동적 그룹의 자동화된 채우기로 인해 원치 않는 계정이 그룹에 추가되어 역할이 할당될 수 있습니다.
- 기본적으로 전역 관리자 및 권한 있는 역할 관리자만 역할 할당 가능 그룹의 멤버 자격을 관리할 수 있지만 그룹 소유자를 추가하여 역할 할당 가능 그룹의 관리를 위임할 수 있습니다.
- RoleManagement.ReadWrite.All Microsoft Graph 권한은 이러한 그룹의 멤버 자격을 관리할 수 있어야 합니다. Group.ReadWrite.All은 작동하지 않습니다.
- 권한 상승을 방지하기 위해 권한 있는 인증 관리자 또는 전역 관리자만 자격 증명을 변경하거나 역할 할당 가능 그룹의 멤버 및 소유자의 MFA를 초기화할 수 있습니다.
- 그룹 중첩은 지원되지 않습니다. 그룹을 역할 할당 가능 그룹의 멤버로 추가할 수 없습니다.

## <a name="use-pim-to-make-a-group-eligible-for-a-role-assignment"></a>PIM을 사용하여 역할 할당에 적격하도록 그룹을 수정합니다.

그룹 멤버가 특정 역할에 액세스하지 못하도록 하려면 [Azure AD PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)을 사용하여 역할 할당에 적격하도록 그룹을 수정할 수 있습니다. 그런 후 그룹의 각 멤버가 고정된 기간 동안 역할 할당을 활성화할 수 있는 자격이 부여됩니다.

> [!Note]
> Azure AD 역할을 그룹에 할당할 수 있으려면 업데이트된 PIM 버전을 사용해야 합니다. Azure AD 조직에 PIM API가 사용되기 때문에 이전 PIM이 사용될 수도 있습니다. pim_preview@microsoft.com으로 이메일을 전송하여 조직을 이동하고 API를 업데이트하세요. 자세한 내용은 [PIM의 Azure AD 역할 및 기능](../privileged-identity-management/pim-configure.md)을 참조하세요.

## <a name="scenarios-not-supported"></a>지원되지 않는 시나리오

다음과 같은 시나리오는 지원되지 않습니다.  

- 온-프레미스 그룹에 Azure AD 역할(기본 제공 또는 사용자 지정)을 할당합니다.

## <a name="known-issues"></a>알려진 문제

역할 할당 가능 그룹에 대해 알려진 문제는 다음과 같습니다.

- *Azure AD P2 사용이 허가된 고객만*: 해당 그룹을 삭제한 후에도 여전히 PIM UI에 역할의 적격 멤버가 표시됩니다. 기능적으로 문제는 없지만 단지 Azure Portal의 캐시 문제입니다.  
- 그룹 멤버 자격을 통해 역할 할당에 새 [Exchange 관리 센터](https://admin.exchange.microsoft.com/)를 사용합니다. 이전 Exchange 관리 센터는 아직 해당 기능을 지원하지 않습니다. Exchange PowerShell cmdlet은 예상대로 작동합니다.
- Azure Information Protection 포털(클래식 포털)은 아직 그룹을 통해 역할 멤버 자격을 인식하지 못합니다. [통합 민감도 레이블 플랫폼으로 마이그레이션](/azure/information-protection/configure-policy-migrate-labels)한 다음 Office 365 Security & Compliance 센터를 사용하여 그룹 할당을 통해 역할을 관리할 수 있습니다.
- [앱 관리 센터](https://config.office.com/)는 아직 해당 기능을 지원하지 않습니다. 사용자를 Office 앱 관리자 역할에 직접 할당합니다.

## <a name="license-requirements"></a>라이선스 요구 사항

이 기능을 사용하려면 Azure AD Premium P1 라이선스가 필요합니다. Just-In-Time 역할 활성화를 위해 Privileged Identity Management를 사용하려면 Azure AD Premium P2 라이선스가 필요합니다. 요구 사항에 적합한 라이선스를 찾으려면 [체험판 및 프리미엄 버전의 일반적으로 사용할 수 있는 기능 비교](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [역할 할당 가능 그룹 만들기](groups-create-eligible.md)
- [그룹에 Azure AD 역할 할당](groups-assign-role.md)
