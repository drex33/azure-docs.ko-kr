---
title: 하이브리드 Azure AD 조인 디바이스란 무엇인가?
description: 디바이스 ID 관리가 사용자 환경의 리소스에 액세스하는 디바이스를 관리하는 데 어떻게 도움이 되는지 알아봅니다.
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
ms.openlocfilehash: 083dcc0dc5d6ffdf25f6d6f631a8e6f5788ce8b1
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303043"
---
# <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

기존 Active Directory 구현이 있는 조직은 하이브리드 Azure AD 조인 디바이스를 구현하여 Azure Active Directory(Azure AD)에서 제공하는 일부 기능을 활용할 수 있습니다. 이러한 디바이스는 Azure Active Directory에 등록되고 온-프레미스 Active Directory에 조인되어 있습니다.

하이브리드 Azure AD 조인 디바이스에는 주기적으로 온-프레미스 도메인 컨트롤러에 대한 네트워크 가시선이 필요합니다. 이 연결이 없으면 디바이스를 사용할 수 없게 됩니다. 이 요구 사항이 문제가 되는 경우, 디바이스의 [Azure AD 조인](concept-azure-ad-join.md)을 고려하세요.

| 하이브리드 Azure AD 조인 | 설명 |
| --- | --- |
| **정의** | 조직 계정에서 디바이스에 로그인해야 하는 온-프레미스 AD 및 Azure AD에 조인됩니다. |
| **기본 대상 그룹** | 기존 온-프레미스 AD 인프라를 사용하는 하이브리드 조직에 적합합니다. |
|   | 조직의 모든 사용자에게 적용됩니다. |
| **디바이스 소유권** | 조직 |
| **운영 체제** | Windows 10, 8.1, 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016, 2019 |
| **프로비전** | Windows 10, Windows Server 2016/2019 |
|   | Azure AD Connect 또는 ADFS 구성을 통한 IT 및 autojoin의 도메인 조인 |
|   | Azure AD Connect 또는 ADFS 구성을 통한 Windows Autopilot 및 autojoin의 도메인 조인 |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 - MSI 필요 |
| **디바이스 로그인 옵션** | 다음을 사용하는 조직 계정: |
|   | 암호 |
|   | Win10용 Windows Hello for Business |
| **디바이스 관리** | 그룹 정책 |
|   | Microsoft Intune을 사용하여 Configuration Manager 독립 실행 또는 공동 관리 |
| **주요 기능** | 클라우드 및 온-프레미스 리소스 모두에 대한 SSO |
|   | 공동 관리되는 경우 도메인 조인 또는 Intune을 통한 조건부 액세스 |
|   | 잠금 화면에서 셀프 서비스 암호 재설정 및 Windows Hello PIN 재설정 |
|   | 여러 디바이스에서 Enterprise State Roaming |

![하이브리드 Azure AD 가입 디바이스](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>시나리오

다음과 같은 경우 Azure AD 하이브리드 조인된 디바이스를 사용합니다.

- Windows 7 및 8.1를 실행하는 하위 수준 디바이스를 지원합니다.
- 그룹 정책을 계속 사용하여 디바이스 구성을 관리하려고 합니다.
- 기존 이미징 솔루션을 계속 사용하여 디바이스를 배포하고 구성하려고 합니다.
- Active Directory 컴퓨터 인증을 사용하는 디바이스에 Win32 앱을 배포했습니다.

## <a name="next-steps"></a>다음 단계

- [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md)
- [Azure Portal을 사용하여 디바이스 ID 관리](device-management-azure-portal.md)
- [Azure AD에서 부실 디바이스 관리](manage-stale-devices.md)
