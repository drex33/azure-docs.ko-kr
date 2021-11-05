---
title: Azure AD 등록 디바이스란?
description: Azure AD 등록 디바이스가 사용자에게 BYOD(Bring Your Own Device) 또는 모바일 디바이스에 대한 지원을 제공하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/09/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bd3f6993c56187a64ba9db3397a659f9dea098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049922"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 등록 디바이스

Azure AD 등록 디바이스의 목표는 사용자에게 BYOD(Bring Your Own Device) 또는 모바일 디바이스에 대한 지원을 제공하는 것입니다. 이런 경우 사용자가 개인 디바이스를 사용하여 조직 리소스에 액세스할 수 있습니다.

| Azure AD 등록 | 설명 |
| --- | --- |
| **정의** | 조직 계정이 디바이스에 로그인하지 않고도 Azure AD에 등록됩니다. |
| **기본 대상 그룹** | 다음 조건에 해당하는 모든 사용자에게 적용됩니다. |
|   | Bring Your Own Device |
|   | 모바일 디바이스 |
| **디바이스 소유권** | 사용자 또는 조직 |
| **운영 체제** | Windows 10 이상, iOS, Android 및 macOS |
| **프로비전** | Windows 10 이상 – 설정 |
|   | iOS/Android – 회사 포털 또는 Microsoft Authenticator 앱 |
|   | macOS - 회사 포털 |
| **디바이스 로그인 옵션** | 최종 사용자 로컬 자격 증명 |
|   | 암호 |
|   | Windows Hello |
|   | PIN |
|   | 다른 디바이스의 생체 인식 또는 패턴 |
| **디바이스 관리** | 모바일 디바이스 관리(예: Microsoft Intune) |
|   | 모바일 애플리케이션 관리 |
| **주요 기능** | 클라우드 리소스에 대한 SSO |
|   | Intune에 등록된 경우 조건부 액세스 |
|   | 앱 보호 정책을 통한 조건부 액세스 |
|   | Microsoft Authenticator 앱으로 휴대폰 로그인을 사용하도록 설정 |

![Azure AD 등록 디바이스](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 등록 디바이스는 Windows 10 이상의 디바이스에서 Microsoft 계정과 같은 로컬 계정을 사용하여 로그인할 수 있습니다. 이러한 장치에는 조직 리소스에 액세스 하기 위한 Azure AD 계정이 있습니다. 조직의 리소스에 대한 액세스는 해당 Azure AD 계정 및 디바이스 ID에 적용된 조건부 액세스 정책에 따라 추가로 제한할 수 있습니다.

관리자는 Microsoft Intune 같은 MDM(모바일 디바이스 관리) 도구를 사용하여 Azure AD 등록 디바이스를 보호하고 추가로 제어할 수 있습니다. MDM은 스토리지를 암호화하고, 암호 복잡성을 유지하고, 보안 소프트웨어를 업데이트해야 하는 등 조직에서 필요한 구성을 적용하는 수단을 제공합니다. 

Azure AD 등록은 처음으로 작업 애플리케이션에 액세스하거나 Windows 10 또는 Windows 11 설정 메뉴를 사용하여 수동으로 수행할 수 있습니다. 

## <a name="scenarios"></a>시나리오

조직의 사용자가 홈 PC에서 혜택 등록 도구에 액세스하려고 합니다. 조직에서 모든 사용자가 Intune 호환 장치에서 이 도구에 액세스 하도록 요구 합니다. 사용자가 자신의 홈 PC를 Azure AD에 등록하면 사용자에게 리소스에 대한 액세스 권한을 부여하는 데 필요한 Intune 정책이 적용됩니다.

다른 사용자가 루팅된 개인 Android 휴대폰에서 조직 메일에 액세스하려고 합니다. 회사에서 규정 준수 디바이스가 필요하므로 루팅된 모든 디바이스를 차단하는 Intune 준수 정책을 만들었습니다. 직원이 이 디바이스에서 조직 리소스에 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 디바이스 ID 관리](device-management-azure-portal.md)
- [Azure AD에서 부실 디바이스 관리](manage-stale-devices.md)
