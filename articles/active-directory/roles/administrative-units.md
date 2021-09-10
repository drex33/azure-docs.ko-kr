---
title: Azure Active Directory의 관리 단위 | Microsoft Docs
description: Azure Active Directory에서 보다 세부적인 권한 위임을 위해 관리 단위를 사용합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b91b216c6a813270cf3b30cde0d8450f964668
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734479"
---
# <a name="administrative-units-in-azure-active-directory"></a>Azure Active Directory의 관리 단위

이 문서에서는 Azure AD(Azure Active Directory)의 관리 단위에 대해 설명합니다. 관리 단위는 다른 Azure AD 리소스의 컨테이너가 될 수 있는 Azure AD 리소스입니다. 관리 단위에는 사용자 및 그룹만 포함될 수 있습니다.

관리 단위는 사용자가 정의하는 조직의 모든 부분에 대한 역할의 권한을 제한합니다. 예를 들어 관리 단위를 사용하여 [기술 지원팀 관리자](permissions-reference.md#helpdesk-administrator) 역할을 지역 지원 전문가에게 위임하여 지원하는 지역에서만 사용자를 관리할 수 있도록 할 수 있습니다.

## <a name="deployment-scenario"></a>배포 시나리오

모든 종류의 독립 부서로 구성된 조직에서 관리 단위를 사용하여 관리 범위를 제한하는 것이 유용할 수 있습니다. 수많은 자치 학교(경영대학, 공대 등)로 구성된 대규모 대학의 예를 생각해 보세요. 각 학교에는 액세스를 제어하고, 사용자를 관리하고, 학교에 대한 정책을 설정하는 IT 관리자 팀이 있습니다.

중앙 관리자는 다음과 같은 작업을 할 수 있습니다.

- 경영대학의 관리 단위를 만듭니다.
- 경영대학 내의 학생과 직원으로만 관리 단위를 채웁니다.
- 경영대학 관리 단위에 속한 Azure AD 사용자에 대해서만 관리 권한이 있는 역할을 만듭니다.
- 경영대학 IT 팀을 해당 범위와 함께 역할에 추가합니다.

## <a name="license-requirements"></a>라이선스 요구 사항

관리 단위를 사용하려면 관리 단위 관리자마다 Azure AD Premium P1 라이선스가 필요하고, 관리 단위 멤버에게는 Azure AD Free 라이선스가 있어야 합니다. 요구 사항에 적합한 라이선스를 찾으려면 [체험판 및 프리미엄 버전의 일반적으로 사용할 수 있는 기능 비교](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

## <a name="manage-administrative-units"></a>관리 단위 관리

Azure Portal, PowerShell cmdlet 및 스크립트 또는 Microsoft Graph를 사용하여 관리 단위를 관리할 수 있습니다. 자세한 내용은 다음을 참조하십시오.

- [역할을 만들고, 관리 단위에 추가 또는 제거하고, 정보 채우기](admin-units-manage.md): 전체 방법 절차를 포함합니다.
- [관리 단위 사용](/powershell/azure/active-directory/working-with-administrative-units): PowerShell을 사용하여 관리 단위를 사용하는 방법을 설명합니다.
- [관리 단위 Graph 지원](/graph/api/resources/administrativeunit): 관리 단위용 Microsoft Graph에 대한 자세한 설명서를 제공합니다.

### <a name="plan-your-administrative-units"></a>관리 단위 계획

관리 단위를 사용하여 Azure AD 리소스를 논리적으로 그룹화할 수 있습니다. IT 부서가 전 세계에 흩어져 있는 조직은 관련 지리적 경계를 정의하는 관리 단위를 만들 수 있습니다. 글로벌 조직이 해당 운영에서 반자율적으로(semi-autonomous) 운영되는 하위 조직을 갖는 또 다른 시나리오에서는 관리 단위가 하위 조직을 나타낼 수 있습니다.

관리 단위를 만드는 기준은 조직의 고유한 요구 사항에 따라 결정됩니다. 관리 단위는 Microsoft 365 서비스에서 구조를 정의하는 일반적인 방법입니다. Microsoft 365 서비스에서 사용되는 용도를 염두에 두고 관리 단위를 준비하는 것이 좋습니다. Microsoft 365 전체에서 관리 단위에 속한 공통 리소스를 연결할 수 있을 때 관리 단위를 최대로 활용할 수 있습니다.

조직에서 관리 단위를 만들 때 일반적으로 다음과 같은 단계를 거치게 됩니다.

1. **초기 채택**: 조직에서 초기 조건에 따라 관리 단위 생성를 시작하고, 조건이 점점 구체화되면서 관리 단위 수가 증가합니다.
1. **삭제**: 조건이 정의되면 더 이상 필요 없는 관리 단위가 삭제됩니다.
1. **안정화**: 조직 구조가 정의되었으며 관리 단위 수가 단기간에 크게 변하지 않습니다.

## <a name="currently-supported-scenarios"></a>현재 지원되는 시나리오

전역 관리자 또는 권한 있는 역할 권리자는 Azure Portal을 사용하여 다음을 수행할 수 있습니다.

- 관리 단위 만들기
- 관리 단위의 사용자 및 그룹 멤버 추가
- 관리 단위 범위가 지정된 관리자 역할에 IT 지원을 할당합니다.

관리 단위 범위가 지정된 관리자는 Microsoft 365 관리 센터를 사용하여 관리 단위에 속한 사용자의 기본적인 관리 업무를 수행할 수 있습니다. 관리 단위 범위가 있는 그룹 관리자는 PowerShell, Microsoft Graph 및 Microsoft 365 관리 센터를 사용하여 그룹을 관리할 수 있습니다.

>[!Note]
>이 섹션에서 설명하는 기능만 Microsoft 365 관리 센터에서 사용할 수 있습니다. 관리 단위 범위가 있는 Azure AD 역할에는 조직 수준 기능을 사용할 수 없습니다.

다음 섹션에는 현재 지원되는 관리 단위 시나리오가 설명되어 있습니다.

### <a name="administrative-unit-management"></a>관리 단위 관리

| 사용 권한 |   Graph/PowerShell   | Azure portal | Microsoft 365 관리 센터 |
| --- | --- | --- | --- |
| 관리 단위 만들기 및 삭제   |    지원됨    |   지원됨   |    지원되지 않음 |
| 관리 단위 멤버를 개별적으로 추가 및 제거    |   지원됨    |   지원됨   |    지원되지 않음 |
| CSV 파일을 사용하여 관리 단위 멤버 대량 추가 및 제거   |    지원되지 않음     |  지원됨   |    지원 계획 없음 |
| 관리 단위 범위가 지정된 관리자 할당  |     지원됨    |   지원됨    |   지원되지 않음 |
| 특성에 따라 동적으로 관리 단위 멤버 추가 및 제거 | 지원되지 않음 | 지원되지 않음 | 지원되지 않음

### <a name="user-management"></a>사용자 관리

| 사용 권한 |   Graph/PowerShell   | Azure portal | Microsoft 365 관리 센터 |
| --- | --- | --- | --- |
| 관리 단위 범위를 지정하여 사용자 속성, 암호 및 라이선스 관리   |    지원됨     |  지원됨   |   지원됨 |
| 관리 단위 범위를 지정하여 사용자 로그인 차단 및 차단 해제    |   지원됨   |    지원됨   |    지원됨 |
| 관리 단위 범위를 지정하여 사용자 다단계 인증 자격 증명 관리   |    지원됨   |   지원됨   |   지원되지 않음 |

### <a name="group-management"></a>그룹 관리

| 사용 권한 |   Graph/PowerShell   | Azure portal | Microsoft 365 관리 센터 |
| --- | --- | --- | --- |
| 관리 단위 범위를 지정하여 그룹 속성 및 멤버 관리     |  지원됨   |    지원됨    |  지원되지 않음 |
| 관리 단위 범위를 지정하여 그룹 라이선스 관리   |    지원됨  |    지원됨   |   지원되지 않음 |

관리 단위는 관리 권한에만 범위를 적용합니다. 멤버 또는 관리자가 자신의 [기본 사용자 권한](../fundamentals/users-default-permissions.md)을 사용하여 관리 단위 외부의 다른 사용자, 그룹 또는 리소스를 검색하는 것을 막을 수 없습니다. Microsoft 365 관리 센터에서 범위가 지정된 관리자의 관리 단위 외부의 사용자가 필터링됩니다. 그러나 Azure Portal, PowerShell 및 기타 Microsoft 서비스에서 다른 사용자를 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [관리 단위 관리](admin-units-manage.md)
- [관리 단위에서 사용자 관리](admin-units-add-manage-users.md)
- [관리 단위에서 그룹 관리](admin-units-add-manage-groups.md)
- [관리 단위에 범위가 지정된 역할 할당](admin-units-assign-roles.md)
