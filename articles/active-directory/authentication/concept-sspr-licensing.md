---
title: 라이선스 셀프 서비스 암호 재설정 - Azure Active Directory
description: Azure Active Directory 셀프 서비스 암호 재설정 라이선스 요구 사항의 차이점에 대한 자세한 정보
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb6f125310e50bbbc48071a06fcaf1aad0650c46
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458226"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 셀프 서비스 암호 재설정을 위한 라이선스 요구 사항

사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 지원 센터 호출 및 생산성 손실을 줄이기 위해 Azure AD(Azure Active Directory)의 사용자 계정에 SSPR(셀프 서비스 암호 재설정)을 사용할 수 있습니다. SSPR을 구성하는 기능에는 암호 변경, 재설정, 잠금 해제, 온-프레미스 디렉터리에 쓰기 저장이 포함됩니다. 기본 SSPR 기능은 Microsoft 365 Business Standard 이상 및 모든 Azure AD Premium SKU에서 무료로 제공됩니다.

이 문서에서는 셀프 서비스 암호 재설정을 라이선스하고 사용하는 다양한 방법에 대해 자세히 설명합니다. 가격 책정 및 청구에 대한 자세한 내용은 [Azure AD 가격 책정 페이지](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)를 참조하세요.

일부 허가 되지 않은 사용자는 기술적으로 SSPR에 액세스할 수 있지만 서비스의 혜택을 이용 하려는 모든 사용자들은 라이선스가 필요합니다.

> [!NOTE] 
> 일부 테넌트 서비스는 현재 특정 사용자를 한정해 혜택을 제한할 수 없습니다. 사용이 허가 된 사용자에 대한 서비스 혜택을 한정하기 위한 노력을 기울여야 합니다. 대상 기능을 사용할 수 있게 되면 조직에서 잠재적인 서비스 지장을 방지할 수 있습니다.

## <a name="compare-editions-and-features"></a>버전 및 기능 비교

다음 표에서는 암호 변경, 재설정 또는 온-프레미스 쓰기 저장에 대한 다양한 SSPR 시나리오와 기능을 제공하는 SKU를 간략하게 설명합니다.

| 기능 | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 또는 P2 |
| --- |:---:|:---:|:---:|:---:|
| **클라우드 전용 사용자 암호 변경**<br />Azure AD의 사용자가 암호를 알고 있으며 새 암호로 변경하려고 합니다. | ● | ● | ● | ● |
| **클라우드 전용 사용자 암호 재설정**<br />Azure AD의 사용자가 암호를 잊어버려 재설정해야 합니다. | | ● | ● | ● |
| **온-프레미스 쓰기 저장을 사용하여 하이브리드 사용자 암호 변경 또는 재설정**<br />Azure AD Connect를 사용하여 온-프레미스 디렉터리에서 동기화되는 Azure AD 사용자가 암호를 변경하거나 재설정하고 새 암호를 온-프레미스에 다시 쓰려고 합니다. | | | ● | ● |

> [!WARNING]
> 독립 실행형 Microsoft 365 Basic 및 Standard 라이선스 플랜은 온-프레미스 쓰기 저장을 사용한 SSPR을 지원하지 않습니다. 온-프레미스 쓰기 저장 기능을 사용하려면 Azure AD Premium P1, Premium P2 또는 Microsoft 365 Business Premium이 필요합니다. 

요금을 비롯한 라이선스에 대한 더 자세한 정보는 다음 페이지를 참조하세요.


* [보안 및 규정 준수를 위한 Microsoft 365 라이선스 지침](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance)
* [Azure Active Directory 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)
* [Azure Active Directory 기능 및 특성](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>다음 단계

SSPR을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정(SSPR) 사용](tutorial-enable-sspr.md)