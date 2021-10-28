---
title: 그룹에 라이선스 할당 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 그룹 기반 라이선스를 사용하여 사용자에게 라이선스를 할당하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59a4b3c12cf9b801ad8758c2ee3e21956d4073a1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985944"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Azure Active Directory에서 그룹 멤버 자격별로 사용자에게 라이선스 할당

이 문서에서는 Azure AD(Azure Active Directory)에서 사용자 그룹에 제품 라이선스를 할당하고 올바르게 허가를 받았는지 확인하는 과정을 설명합니다.

이 예에서는 Azure AD 조직에 **HR 부서** 라는 보안 그룹이 포함되어 있습니다. 이 그룹에는 인사 부서의 모든 멤버(약 1,000명의 사용자)이 포함되어 있습니다. 사용자가 부서 전체에 Office 365 Enterprise E3 라이선스를 할당하려고 합니다. 제품에 포함된 Yammer Enterprise 서비스는 해당 부서에서 사용할 준비를 갖출 때까지 일시적으로 사용하지 않도록 설정해야 합니다. 또한 동일한 사용자 그룹에 Enterprise Mobility + Security 라이선스를 배포하려고 합니다.

> [!NOTE]
> 일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자에 대해 사용 위치 속성을 지정해야 합니다.
>
> 그룹 라이선스 할당의 경우 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 여러 위치에 사용자가 있는 경우 항상 Azure AD에서 사용자 만들기 흐름의 일부로(예: AAD Connect 구성을 통해) 사용 위치를 설정하는 것이 좋습니다. 이렇게 하면 라이선스 할당의 결과가 항상 정확하고 허용되지 않은 위치에서 사용자에게 서비스가 제공되지 않습니다.

## <a name="step-1-assign-the-required-licenses"></a>1단계: 필요한 라이선스 할당

1. 라이선스 관리자 계정을 사용하여 [**Azure AD 관리 센터**](https://aad.portal.azure.com)에 로그인합니다. 라이선스를 관리하려면 라이선스 관리자, 사용자 관리자 또는 전역 관리자 계정이어야 합니다.

1. **라이선스** 를 선택하여 조직의 모든 라이선스 대상 제품을 보고 관리할 수 있는 페이지를 엽니다.

1. **모든 제품** 에서 제품 이름을 선택하여 Office 365 Enterprise E5 및 Enterprise Mobility + Security E3를 둘 다 선택합니다. 페이지 맨 위에 있는 **할당** 을 선택하여 할당을 시작합니다.

   ![라이선스를 할당할 제품 선택](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. **라이선스 할당** 페이지에서 **사용자 및 그룹** 을 선택하여 사용자 및 그룹 목록을 엽니다.

1. 사용자 또는 그룹을 선택하고 페이지 맨 아래에 있는 **선택** 단추를 사용하여 선택 내용을 확인합니다.

1. **라이선스 할당** 페이지에서 **할당 옵션** 을 클릭하여 앞서 선택한 두 가지 제품에 포함된 모든 서비스 계획을 표시합니다. **Yammer Enterprise** 를 찾은 후 **해제** 하여 제품 라이선스에서 해당 서비스를 사용하지 않도록 설정합니다. **라이선스 옵션** 맨 아래에서 **확인** 을 클릭하여 확인합니다.

   ![라이선스의 서비스 계획 선택](./media/licensing-groups-assign/assignment-options.png)
  
1. **라이선스 할당** 페이지에서 맨 아래에 있는 **할당** 을 클릭하여 할당을 완료합니다.

1. 오른쪽 위 모서리에 프로세스의 상태와 결과를 보여 주는 알림이 표시됩니다. 그룹에 대한 할당을 완료할 수 없으면(예를 들어 그룹의 기존 라이선스 때문에) 알림을 클릭하여 실패 세부 정보를 확인합니다.

그룹에 라이선스를 할당하는 경우 Azure AD는 해당 그룹의 모든 기존 멤버를 처리합니다. 이 프로세스는 그룹 크기에 따라 다소 시간이 걸릴 수 있습니다. 다음 단계에서는 프로세스가 완료되었는지와 문제 해결을 위해 추가적인 관리가 필요한지를 확인하는 방법을 설명합니다.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2단계: 초기 할당이 완료되었는지 확인

1. **Azure Active Directory** > **그룹** 으로 이동합니다. 라이선스가 할당된 그룹을 선택합니다.

1. 그룹 페이지에서 **라이선스** 를 선택합니다. 그러면 사용자에게 라이선스가 완전히 할당되었는지, 확인해야 할 오류가 있는지 신속하게 확인합니다. 다음과 같은 정보가 제공됩니다.

   - 현재 그룹에 할당된 서비스 라이선스. 사용하도록 설정된 특정 서비스를 표시하고 변경을 수행할 항목을 선택합니다.

   - 최신 라이선스 변경의 상태 업데이트. 변경 사항을 처리 중이거나 모든 사용자 멤버에 대해 처리가 완료된 경우 사용할 수 있습니다.

   - 오류 상태에 있는 사용자 라이선스 할당에 대한 정보.

   ![라이선스 오류 및 라이선스 상태](./media/licensing-groups-assign/assignment-errors.png)

1. 라이선스처리에 대한 자세한 내용은 **Azure Active Directory** > **사용자 및 그룹** > *그룹 이름* > **감사 로그** 를 참조하세요. 다음 작업을 확인하세요.

   - 작업: `Start applying group based license to users`. 시스템이 그룹에 대한 라이선스 할당 변경을 선택하고 모든 사용자 구성원에게 적용하기 시작하면 로깅됩니다. 수행된 변경 내용에 대한 정보가 포함됩니다.

   - 작업: `Finish applying group based license to users`. 시스템이 그룹의 모든 사용자에 대한 처리를 완료하면 로깅됩니다. 성공적으로 처리된 사용자 수와 그룹 라이선스를 할당하지 못한 사용자 수에 대한 요약 정보가 포함되어 있습니다.

   [이 섹션을 읽고](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) 감사 로그를 사용하여 그룹 기반 라이선스에 의한 변경 내용을 분석하는 방법에 대해 자세히 알아보세요.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3단계: 라이선스 문제 확인 및 해결

1. **Azure Active Directory** > **그룹** 으로 이동하여 라이선스가 할당된 그룹을 찾습니다.
1. 그룹 페이지에서 **라이선스** 를 선택합니다. 페이지 맨 위에 라이선스를 할당하지 못한 10명의 사용자가 있다는 알림이 표시됩니다. 이 알림을 열면 이 그룹에서 라이선스 오류 상태에 있는 모든 사용자 목록이 표시됩니다.
1. **할당 실패** 열에는 두 제품 라이선스 모두 사용자에게 할당하지 못했다는 내용이 표시됩니다. **상위 실패 원인** 열에는 실패의 원인이 포함되어 있습니다. 이 예에서는 **충돌하는 서비스 계획** 이 실패의 원인입니다.

   ![할당할 수 없는 라이선스](./media/licensing-groups-assign/failed-assignments.png)

1. 사용자를 선택하여 사용자의 **라이선스** 페이지를 엽니다. 이 페이지에는 현재 사용자에게 할당된 모든 라이선스가 표시됩니다. 이 예에서는 사용자에게 **키오스크 사용자** 그룹에서 상속된 Office 365 Enterprise E1 라이선스가 있습니다. 이 라이선스는 시스템에서 **HR Department** 그룹에 적용하려고 하는 E3 라이선스와 충돌합니다. 결과적으로 해당 그룹의 어떠한 라이선스도 사용자에게 할당되지 않았습니다.

   ![사용자의 모든 라이선스 충돌 보기](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. 이 충돌을 해결하기 위해 **키오스크 사용자** 그룹에서 해당 사용자를 제거합니다. Azure AD에서 변경 내용을 처리한 후에 **HR Department** 라이선스가 올바르게 할당됩니다.

## <a name="next-steps"></a>다음 단계

그룹을 사용하여 라이선스를 할당하는 기능 집합에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Active Directory의 그룹 기반 라이선스란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](licensing-groups-resolve-problems.md)
- [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](licensing-groups-migrate-users.md)
- [Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 마이그레이션하는 방법](licensing-groups-change-licenses.md)
- [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](licensing-group-advanced.md)
- [Azure Active Directory의 그룹 기반 라이선싱에 대한 PowerShell 예제](licensing-ps-examples.md)