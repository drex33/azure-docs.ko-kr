---
title: 직접 라이선스를 사용하는 사용자를 그룹 라이선스에 추가 - Azure AD | Microsoft Docs
description: Azure Active Directory를 사용하여 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7188fa214e2b9407364b1f4d850a6f1c50cc27
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985830"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>개별 라이선스를 사용하는 사용자를 라이선스용 그룹으로 마이그레이션하는 방법

“직접 할당”을 통해, 즉 PowerShell 스크립트 또는 기타 도구를 사용하여 개별 사용자에게 라이선스를 할당하는 방식으로 조직의 사용자에게 기존 라이선스가 배포되었을 수 있습니다. 그룹 기반 라이선스를 사용하여 조직의 라이선스를 관리하기 전에 이와 같은 마이그레이션 계획을 사용하여 기존 솔루션을 그룹 기반 라이선스로 원활하게 바꿀 수 있습니다.

가장 중요한 고려 사항은 그룹 기반 라이선스로의 마이그레이션으로 인해 사용자에게 현재 할당된 라이선스가 일시적으로 손실되는 상황을 피해야 한다는 것입니다. 사용자가 서비스 및 해당 데이터에 액세스하지 못하는 일이 없도록 라이선스를 제거할 수 있는 프로세스는 피해야 합니다.

## <a name="recommended-migration-process"></a>권장 마이그레이션 프로세스

1. 사용자에 대한 라이선스 할당 및 제거를 관리하는 기존 자동화(예: PowerShell) 기능이 있을 것입니다. 이러한 기능은 실행 상태로 둡니다.

1. 새 라이선스 그룹을 만들고(또는 사용할 기존 그룹 결정) 필요한 모든 사용자가 구성원으로 추가되어 있는지 확인합니다.

1. 이러한 그룹에 필요한 라이선스를 할당합니다. 여기서의 기존 자동화(예: PowerShell)가 해당 사용자에게 적용하는 동일한 라이선스 상태가 반영되도록 하는 것이 목표입니다.

1. 해당 그룹의 모든 사용자에게 라이선스가 적용되었는지 확인합니다. 이렇게 적용하려면 각 그룹의 처리 상태를 확인하고 감사 로그를 확인합니다.

   - 라이선스 세부 정보를 확인하여 개별 사용자를 부분적으로 확인할 수 있습니다. 이를 통해 이러한 사용자에게 동일한 라이선스가 "직접" 할당되고 그룹에서 "상속"되었음을 알 수 있습니다.

   - PowerShell 스크립트를 실행하여 [사용자에게 라이선스가 할당된 방법을 확인](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)할 수 있습니다.

   - 동일한 제품 라이선스가 사용자에게 직접 방법과 그룹을 통한 방법으로 모두 할당되어도 해당 사용자에게는 라이선스가 하나만 사용됩니다. 따라서 추가 라이선스에 대해 마이그레이션을 수행할 필요가 없습니다.

1. 오류 상태의 사용자가 있는지 각 그룹을 확인하여 실패한 라이선스 할당이 없는지 확인합니다. 자세한 내용은 [그룹에 대한 라이선스 문제 식별 및 해결](licensing-groups-resolve-problems.md)을 참조하세요.

원래의 직접 할당을 제거할지 고려합니다. 점진적으로 진행하면서 사용자 하위 세트에 대한 결과를 모니터링하는 것이 좋습니다. 사용자에 대한 원래 직접 할당을 그대로 둘 수도 있지만, 사용자가 허가된 그룹에서 제외되는데도 직접 할당된 라이선스를 계속 보유하는 원치 않는 상황이 발생할 수도 있습니다.

## <a name="an-example"></a>예제

조직에 1,000명의 사용자가 있습니다. 모든 사용자에게 Office 365 Enterprise E3 라이선스가 필요합니다. 현재 조직에는 온-프레미스에서 실행되며 사용자가 들어오고 나갈 때 라이선스를 추가 및 제거하는 PowerShell 스크립트가 있습니다. 그러나 조직은 그룹 기반 라이선싱으로 스크립트를 바꾸려고 하므로 Azure AD에서 라이선스를 자동으로 관리할 수 있습니다.

마이그레이션 프로세스는 다음과 같을 수 있습니다.

1. Azure Portal을 사용하여 Azure AD의 **모든 사용자** 그룹에 Office 365 E3 라이선스를 할당합니다.

1. 모든 사용자에 대한 라이선스 할당이 완료되었는지 확인합니다. 각 그룹에 대한 개요 페이지로 이동하고 **라이선스** 를 선택한 후 **라이선스** 블레이드 맨 위에서 처리 상태를 확인합니다.

   - 처리가 완료되었으면 "최신 라이선스 변경 내용이 모든 사용자에게 할당됨"이 표시됩니다.

   - 해당 사용자의 라이선스가 성공적으로 할당되지 않았을 수 있다는 알림은 위쪽에 표시됩니다. 일부 사용자의 라이선스가 부족한가요? 일부 사용자에게 충돌하는 라이선스 계획이 할당되어 상속 그룹이 라이선스를 할당하지 못하나요?

1. 일부 사용자를 검토하여 직접 및 그룹 라이선스가 모두 할당되었는지 확인합니다. 사용자의 프로필 페이지로 이동하고, **라이선스** 를 선택하고, 라이선스 상태를 검사합니다.

   - 다음은 마이그레이션 중에 예상되는 사용자 상태입니다.

      ![마이그레이션 중에 예상되는 사용자 상태](./media/licensing-groups-migrate-users/expected-user-state.png)

     이 경우 사용자에게 직접 및 상속된 라이선스가 모두 있는 것입니다. Office 365 E3가 할당된 것을 볼 수 있습니다.

   - 어떤 서비스가 사용하도록 설정되었는지 보려면 각 라이선스를 선택합니다. 직접 및 그룹 라이선스가 사용자에 대해 정확히 동일한 서비스를 사용하도록 설정했는지 확인하려면 **할당** 을 선택합니다.

1. 직접 및 그룹 라이선스가 동일한 것인지 확인한 후에 사용자에게서 직접 라이선스를 제거할 수 있습니다. 포털에 있는 개별 사용자에게서 라이선스를 제거하여 테스트한 다음 자동화 스크립트를 실행하여 대량으로 제거할 수 있습니다. 포털을 통해 같은 사용자에게서 직접 라이선스를 제거하는 예제는 다음과 같습니다. 라이선스 상태는 변경되지 않지만 직접 할당은 더 이상 표시되지 않습니다.

   ![직접 라이선스가 제거되었는지 확인](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>다음 단계

그룹 라이선스 관리의 다른 시나리오에 관해 자세히 알아봅니다.

- [Azure Active Directory의 그룹 기반 라이선스란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Azure Active Directory에서 그룹에 라이선스 할당](licensing-groups-assign.md)
- [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](licensing-groups-resolve-problems.md)
- [Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 마이그레이션하는 방법](licensing-groups-change-licenses.md)
- [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](licensing-group-advanced.md)
- [Azure Active Directory의 그룹 기반 라이선싱에 대한 PowerShell 예제](licensing-ps-examples.md)
