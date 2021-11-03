---
title: 그룹 기반 라이선스란? - Azure Active Directory | Microsoft Docs
description: 작동 방법 및 모범 사례를 비롯한 Azure Active Directory 그룹 기반 라이선스에 대해 알아봅니다.
services: active-directory
keywords: Azure AD 라이선스
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f9f734d1adbb930d1fa9342dfbaaf465d8d502d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049497"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Azure Active Directory의 그룹 기반 라이선스란?

Microsoft 365, Enterprise Mobility + Security, Dynamics 365, 기타 유사한 제품 등과 같은 Microsoft 유료 클라우드 서비스를 사용하려면 라이선스가 필요합니다. 이러한 라이선스는 해당 서비스에 액세스해야 하는 각 사용자에게 할당됩니다. 라이선스를 관리하기 위해 관리자는 관리 포털(Office 또는 Azure) 및 PowerShell cmdlet 중 하나를 사용합니다. Azure AD(Azure Active Directory)는 모든 Microsoft 클라우드 서비스에 대한 ID 관리를 지원하는 기본 인프라입니다. Azure AD는 사용자에 대한 라이선스 할당 상태에 대한 정보를 저장합니다.

지금까지 개별 사용자 수준에서만 라이선스를 할당할 수 있었기 때문에 대규모 관리가 어려워질 수 있습니다. 예를 들어 조직이나 부서에 가입하거나 탈퇴하는 사용자와 같이 조직의 변경 내용에 따라 사용자 라이선스를 추가하거나 제거하려면 관리자는 종종 복잡한 PowerShell 스크립트를 작성해야 합니다. 이 스크립트는 클라우드 서비스를 개별적으로 호출합니다.

이러한 문제를 해결하기 위해 이제는 Azure AD에 그룹 기반 라이선스가 포함됩니다. 그룹에 제품 라이선스를 하나 이상 할당할 수 있습니다. Azure AD는 그룹의 모든 멤버에게 라이선스가 할당되도록 합니다. 그룹에 참가하는 새 멤버에게는 적절한 라이선스가 할당됩니다. 멤버가 그룹을 떠날 때 해당 라이선스가 제거됩니다. 이 라이선싱 관리를 사용하면 사용자 기준으로 조직 및 부서 구조에 변경 내용을 반영하기 위해 PowerShell을 통해 라이선스 관리를 자동화할 필요가 없습니다.

## <a name="licensing-requirements"></a>라이선싱 요구 사항
그룹 기반 라이선스를 **활용하는 모든 사용자에게** 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P1 이상의 유료 또는 평가판 구독

- Office 365 Enterprise E3, Office 365 A3, Office 365 GCC G3, Office 365 E3 for GCCH, Office 365 E3 for DOD 이상의 유료 또는 평가판 버전

### <a name="required-number-of-licenses"></a>필요한 라이선스 수
라이선스가 할당된 그룹의 경우 각 고유 구성원에 대한 라이선스도 있어야 합니다. 그룹의 각 구성원에게 라이선스를 할당할 필요는 없지만 모든 구성원을 포함하기에 충분한 라이선스가 있어야 합니다. 예를 들어 테넌트에서 라이선스가 부여된 그룹에 1,000명의 고유 구성원이 있는 경우 라이선싱 계약에 부합하려면 라이선스가 1,000개 이상 있어야 합니다.

## <a name="features"></a>기능

다음은 그룹 기반 라이선스 기능의 주요 특징입니다.

- Azure AD의 보안 그룹에 라이선스를 할당할 수 있습니다. Azure AD Connect를 사용하여 보안 그룹을 온-프레미스에서 동기화할 수 있습니다. 또한 Azure AD(클라우드 전용 그룹이라고도 함)에서 보안 그룹을 직접 만들거나 Azure AD 동적 그룹 기능을 통해 자동으로 만들 수 있습니다.

- 그룹에 제품 라이선스를 할당하면 관리자는 제품에서 서비스 계획을 하나 이상 사용하지 않도록 설정할 수 있습니다. 일반적으로 조직이 제품에 포함된 서비스를 사용할 준비가 아직 되지 않은 경우에 이 할당이 수행됩니다. 예를 들어 관리자가 부서에 Microsoft 365를 할당할 수 있지만 Yammer 서비스를 일시적으로 사용하지 않도록 설정할 수 있습니다.

- 사용자 수준 라이선스를 필요로 하는 모든 Microsoft Clouds Services는 지원됩니다. 이 지원에는 모든 Microsoft 365 제품, Enterprise Mobility + Security, Dynamics 365가 포함됩니다.

- 그룹 기반 라이선스는 현재 [Azure portal](https://portal.azure.com)을 통해서만 사용할 수 있습니다. [Microsoft 365 관리 센터](https://admin.microsoft.com)와 같이 주로 사용자 및 그룹 관리를 위해 다른 관리 포털을 사용하는 경우에 이 작업을 계속 수행할 수 있습니다. 그러나 그룹 수준에서 라이선스를 관리하려면 Azure Portal을 사용해야 합니다.

- Azure AD는 그룹 멤버 자격 변경으로 인해 발생하는 라이선스 수정을 자동으로 관리합니다. 일반적으로 라이선스 수정은 멤버 자격 변경 후 수분 내에 효과가 발생합니다.

- 사용자는 지정된 라이선스 정책을 사용하는 여러 그룹의 멤버가 될 수 있습니다. 또한 사용자는 그룹 외부에서 직접 할당된 일부 라이선스를 보유할 수도 있습니다. 결과 사용자 상태는 모든 할당된 제품 및 서비스 라이선스의 조합입니다. 사용자에게 여러 원본의 동일한 라이선스를 할당하는 경우 라이선스는 한 번만 사용됩니다.

- 어떤 경우에는 사용자에게 라이선스를 할당할 수 없습니다. 예를 들어 테넌트에서 사용할 수 있는 라이선스가 충분하지 않거나 충돌하는 서비스를 동시에 할당했을 수 있습니다. 관리자는 Azure AD가 그룹 라이선스를 완전하게 처리할 수 없는 사용자에 대한 정보에 액세스할 수 있습니다. 그런 다음 해당 정보에 따라 수정 작업을 수행할 수 있습니다.

## <a name="your-feedback-is-welcome"></a>Microsoft는 사용자의 의견을 소중하게 생각합니다.

피드백 또는 기능 요청이 있는 경우 [Azure AD 관리자 포럼](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)을 사용하여 공유해 주세요.

## <a name="next-steps"></a>다음 단계

그룹 기반 라이선스를 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Active Directory에서 그룹에 라이선스 할당](../enterprise-users/licensing-groups-assign.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](../enterprise-users/licensing-groups-resolve-problems.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](../enterprise-users/licensing-groups-migrate-users.md)
* [Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 마이그레이션하는 방법](../enterprise-users/licensing-groups-change-licenses.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](../enterprise-users/licensing-group-advanced.md)
* [Azure Active Directory의 그룹 기반 라이선싱에 대한 PowerShell 예제](../enterprise-users/licensing-ps-examples.md)
