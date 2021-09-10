---
title: Azure AD 조인 디바이스란?
description: Azure AD 조인 디바이스는 사용자 환경 내의 리소스에 액세스하는 디바이스 관리에 도움이 됩니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c39aac2abeb9d569690b056df1a2fb892b11d3c1
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303061"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 조인 디바이스

모든 조직에서 규모 또는 업계에 관계없이 Azure AD 조인 디바이스를 배포할 수 있습니다. Azure AD 조인은 하이브리드 환경에서도 작동하므로 클라우드 및 온-프레미스 앱과 리소스 모두에 액세스할 수 있습니다.

| Azure AD 조인 | Description |
| --- | --- |
| **정의** | 조직 계정에서 디바이스에 로그인하는 데 필요한 Azure AD에만 조인됩니다. |
| **기본 대상 그룹** | 클라우드 전용 및 하이브리드 조직 모두에 적합합니다. |
|   | 조직의 모든 사용자에게 적용됩니다. |
| **디바이스 소유권** | 조직 |
| **운영 체제** | Windows 10 Home을 제외한 모든 Windows 10 디바이스 |
|   | [Azure에서 실행되는 Windows Server 2019 Virtual Machines](howto-vm-sign-in-azure-ad-windows.md)(서버 코어는 지원되지 않음) |
| **프로비전** | 셀프 서비스: Windows 첫 실행 경험(OOBE) 또는 Windows 설정 |
|   | 대량 등록 |
|   | Windows Autopilot |
| **디바이스 로그인 옵션** | 다음을 사용하는 조직 계정: |
|   | 암호 |
|   | 비즈니스용 Windows Hello |
|   | FIDO2.0 보안 키(미리 보기) |
| **디바이스 관리** | 모바일 디바이스 관리(예: Microsoft Intune) |
|   | Microsoft Intune 및 Microsoft Endpoint Configuration Manager를 사용하여 공동 관리 |
| **주요 기능** | 클라우드 및 온-프레미스 리소스 모두에 대한 SSO |
|   | MDM 등록 및 MDM 규정 준수 평가를 통한 조건부 액세스 |
|   | 잠금 화면에서 셀프 서비스 암호 재설정 및 Windows Hello PIN 재설정 |
|   | 여러 디바이스에서 Enterprise State Roaming |

Azure AD 조인 디바이스는 조직의 Azure AD 계정을 사용하여 로그인됩니다. 조직의 리소스에 대한 액세스는 해당 Azure AD 계정 및 디바이스 ID에 적용된 [조건부 액세스 정책](../conditional-access/howto-conditional-access-policy-compliant-device.md)에 따라 추가로 제한할 수 있습니다.

관리자는 Microsoft Intune과 같은 MDM(모바일 디바이스 관리) 도구를 사용하거나 Microsoft Endpoint Configuration Manager를 사용하는 공동 관리 시나리오에서 Azure AD 조인 디바이스를 보호하고 추가로 제어할 수 있습니다. 이러한 도구는 스토리지 암호화, 암호 복잡성, 소프트웨어 설치 및 소프트웨어 업데이트를 요구하는 것처럼 조직에 필요한 구성을 적용할 수 있는 수단을 제공합니다. 관리자는 Configuration Manager를 사용하여 Azure AD 조인 디바이스에서 조직 응용 애플리케이션을 사용할 수 있도록 하여 [비즈니스 및 교육용 Microsoft Store에서 앱을 관리](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)할 수 있습니다.

Azure AD 조인은 OOBE(첫 실행 경험), 대량 등록 또는 [Windows Autopilot](/intune/enrollment-autopilot)과 같은 셀프 서비스 옵션을 사용하여 수행할 수 있습니다.

Azure AD 조인 디바이스는 조직의 네트워크에 있는 경우에도 온-프레미스 리소스에 대한 Single Sign-On을 계속 유지할 수 있습니다. Azure AD에 조인된 디바이스는 여전히 파일, 인쇄 및 기타 애플리케이션과 같은 온-프레미스 서버에 인증할 수 있습니다.

## <a name="scenarios"></a>시나리오

Azure AD 가입은 기본적으로 온-프레미스 Windows Server Active Directory 인프라가 없는 조직을 위해 고안되었으나, 다음과 같은 시나리오에서 확실히 사용할 수 있습니다.

- Azure AD 및 Intune 같은 MDM을 사용하여 클라우드 기반 인프라로 전환하려 합니다.
- 온-프레미스 도메인 가입을 사용할 수 없습니다. 예를 들어 제어되는 태블릿 및 휴대폰과 같은 모바일 디바이스가 필요한 경우입니다.
- 사용자가 기본적으로 Microsoft 365 또는 Azure AD와 통합된 다른 SaaS 앱에 액세스해야 합니다.
- Active Directory 대신 Azure AD에서 사용자 그룹을 관리하고자 합니다. 예를 들어 이 시나리오는 계절 노동자, 계약자 또는 학생에게 적용할 수 있습니다.
- 제한된 온-프레미스 인프라를 사용하는 원격 지사에서 작업자에게 조인 기능을 제공하려고 합니다.

Windows 10 Home을 제외한 모든 Windows 10 디바이스에 대해 Azure AD 조인 디바이스를 구성할 수 있습니다.

Azure AD 가입 디바이스의 목표는 단순화하는 것입니다.

- 회사 소유 디바이스의 Windows 배포
- 모든 Windows 디바이스에서 조직의 앱 및 리소스에 액세스
- 회사 소유 디바이스의 클라우드 기반 관리
- 사용자가 Azure AD 또는 동기화된 Active Directory 회사 또는 학교 계정을 사용하여 디바이스에 로그인

![Azure AD 조인 디바이스](./media/concept-azure-ad-join/azure-ad-joined-device.png)

다음 방법 중 하나를 사용하여 Azure AD Join을 배포할 수 있습니다.

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [대량 배포](/intune/windows-bulk-enroll)
- [셀프 서비스 환경](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>다음 단계

- [Azure AD 조인 구현 계획](azureadjoin-plan.md)
- [Azure AD 조인 디바이스에서 로컬 관리자 그룹을 관리하는 방법](assign-local-admin.md)
- [Azure Portal을 사용하여 디바이스 ID 관리](device-management-azure-portal.md)
- [Azure AD에서 부실 디바이스 관리](manage-stale-devices.md)
