---
title: Azure Active Directory의 디바이스 ID란?
description: 디바이스 ID 및 관련 사용 사례
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo, ravenn, spunukol, jogro, jploegert
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c997cc1f4ed2be18e06838f6c30864456f7539
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588241"
---
# <a name="what-is-a-device-identity"></a>디바이스 ID란?

[디바이스 ID](/graph/api/resources/device?view=graph-rest-1.0&preserve-view=true)는 Azure AD(Azure Active Directory)의 개체입니다. 이 디바이스 개체는 사용자, 그룹 또는 애플리케이션과 비슷합니다. 디바이스 ID는 액세스 또는 구성 결정을 내릴 때 사용할 수 있는 정보를 관리자에게 제공합니다.

![Azure AD 디바이스 블레이드에 표시된 디바이스](./media/overview/azure-active-directory-devices-all-devices.png)

디바이스 ID를 가져오는 세 가지 방법은 다음과 같습니다.

- Azure AD 등록
- Azure AD 조인
- 하이브리드 Azure AD 조인

디바이스 ID는 [디바이스 기반 조건부 액세스 정책](../conditional-access/require-managed-devices.md) 및 [Microsoft Endpoint Manager를 사용한 모바일 디바이스 관리](/mem/endpoint-manager-overview) 같은 시나리오의 필수 조건입니다.

## <a name="modern-device-scenario"></a>최신 디바이스 시나리오

최신 디바이스 시나리오는 다음 두 가지 방법에 중점을 둡니다. 

- [Azure AD 등록](concept-azure-ad-register.md) 
   - BYOD(Bring Your Own Device)
   - 모바일 디바이스(휴대폰 및 태블릿)
- [Azure AD 조인](concept-azure-ad-register.md)
   - 조직이 소유한 Windows 10 디바이스
   - [Azure에서 VM에서 실행되는 조직의 Windows Server 2019 이상 서버](howto-vm-sign-in-azure-ad-windows.md)

[하이브리드 Azure AD 조인](concept-azure-ad-join-hybrid.md)은 Azure AD 조인을 수행하는 과정의 중간 단계입니다. 하이브리드 Azure AD 조인은 하위 수준 Windows 버전을 Windows 7 및 Server 2008로 되돌리는 지원을 조직에 제공합니다. 세 가지 시나리오는 모두 단일 조직에서 공존할 수 있습니다.

## <a name="resource-access"></a>리소스 액세스

디바이스를 Azure AD에 등록하고 조인하면 클라우드 기반 리소스에 대한 SSO(Seamless Sign-on)가 사용자에게 제공됩니다.

Azure AD 조인된 디바이스는 [조직의 온-프레미스 리소스에 대한 SSO](azuread-join-sso.md)를 활용합니다.

## <a name="provisioning"></a>프로비전

디바이스를 Azure AD로 가져오는 작업은 셀프 서비스 방식으로 수행하거나 관리자가 관리하는 제어된 프로세스로 수행할 수 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure AD 등록 디바이스](concept-azure-ad-register.md)에 대한 자세한 정보
- [Azure AD 조인 디바이스](concept-azure-ad-join.md)에 대한 자세한 정보
- [하이브리드 Azure AD 조인 디바이스](concept-azure-ad-join-hybrid.md)에 대한 자세한 정보
- Azure Portal에서 디바이스 ID를 관리하는 방법에 대한 개요를 보려면 [Azure Portal을 사용하여 디바이스 ID 관리](device-management-azure-portal.md)를 참조하세요.
- 디바이스 기반의 조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 디바이스 기반 조건부 액세스 정책 구성](../conditional-access/require-managed-devices.md)을 참조하세요.
