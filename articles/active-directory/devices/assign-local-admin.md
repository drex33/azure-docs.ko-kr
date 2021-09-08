---
title: Azure AD 조인 디바이스의 로컬 관리자를 관리하는 방법
description: Windows 디바이스의 로컬 관리자 그룹에 Azure 역할을 할당하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15b15b91e08ba404eac0fb2c30df924d2f84731b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866664"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Azure AD 조인 디바이스에서 로컬 관리자 그룹을 관리하는 방법

Windows 디바이스를 관리하려면 로컬 관리자 그룹의 구성원이 되어야 합니다. Azure AD(Azure Active Directory) 조인 프로세스의 일부로, Azure AD는 디바이스에서 이 그룹의 멤버 자격을 업데이트합니다. 비즈니스 요구 사항을 충족하도록 멤버 자격 업데이트를 사용자 지정할 수 있습니다. 멤버 자격 업데이트는 예를 들어 기술 지원팀 직원이 디바이스에 대해 관리자 권한이 필요한 작업을 수행할 수 있도록 하려는 경우에 유용합니다.

이 문서에서는 로컬 관리자 멤버 자격 업데이트의 작동 방법 및 Azure AD 조인 동안 사용자 지정하는 방법을 설명합니다. 이 문서의 내용은 **하이브리드 Azure AD 조인** 디바이스에 적용되지 않습니다.

## <a name="how-it-works"></a>작동 방식

Azure AD 조인을 사용하여 Windows 디바이스를 Azure AD와 연결하는 경우 Azure AD는 디바이스에서 로컬 관리자 그룹에 다음 보안 주체를 추가합니다.

- Azure AD 글로벌 관리자 역할
- Azure AD 조인 디바이스 로컬 관리자 역할 
- Azure AD 조인을 수행하는 사용자   

Azure AD 역할을 로컬 관리자 그룹에 추가하면 디바이스에서 아무것도 수정하지 않고 언제든지 Azure AD에서 디바이스를 관리할 수 있는 사용자를 업데이트할 수 있습니다. 또한 Azure AD는 PoLP(최소 권한의 원칙)를 지원하기 위해 로컬 관리자 그룹에 Azure AD 조인 디바이스 로컬 관리자 역할을 추가합니다. 글로벌 관리자 외에도 사용자가 *오직* 디바이스를 관리하기 위한 디바이스 관리자 역할을 할당받도록 설정할 수 있습니다. 

## <a name="manage-the-global-administrators-role"></a>글로벌 관리자 역할 관리

글로벌 관리자 역할의 멤버 자격을 확인하고 업데이트하려면 다음을 참조하세요.

- [Azure Active Directory에서 관리자 역할의 모든 구성원 보기](../roles/manage-roles-portal.md)
- [Azure Active Directory에서 관리자 역할에 사용자 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>디바이스 관리자 역할 관리 

Azure Portal에 있는 **디바이스** 페이지에서 디바이스 관리자 역할을 관리할 수 있습니다. **디바이스** 페이지를 엽니다.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory* 를 검색하고 선택합니다.
1. **관리** 섹션에서 **디바이스** 를 클릭합니다.
1. **디바이스** 페이지에서 **디바이스 설정** 을 클릭합니다.

디바이스 관리자 역할을 수정하려면 **Azure AD 조인 디바이스의 추가 로컬 관리자** 를 구성합니다.  

![추가 로컬 관리자](./media/assign-local-admin/10.png)

> [!NOTE]
> 이 옵션에는 Azure AD Premium 테넌트가 필요합니다. 

디바이스 관리자는 모든 Azure AD 조인 디바이스에 할당됩니다. 디바이스 관리자를 특정 디바이스 집합으로 범위를 지정할 수 없습니다. 디바이스 관리자 역할 업데이트가 반드시 영향을 받는 사용자에게 즉각적인 영향을 미치는 것은 아닙니다. 사용자가 이미 로그인한 디바이스에서는 아래의 *두* 작업을 수행할 때 권한 상승이 발생합니다.

- 적절한 권한으로 새 Primary Refresh Tokens을 발급하기 위해 Azure AD에 대해 최대 4시간이 경과 되었습니다. 
- 사용자가 잠금/잠금 해제가 아니라 로그아웃 했다가 다시 로그인하여 프로필을 새로 고칠 수 있습니다.
- 사용자는 로컬 관리자 그룹에 나열되지 않으며 기본 새로 고침 토큰을 통해 사용 권한을 받습니다. 

> [!NOTE]
> 위의 작업은 이전에 관련 디바이스에 로그인하지 않은 사용자에게는 적용되지 않습니다. 이 경우, 관리자 권한은 처음으로 디바이스에 로그인 한 후 즉시 적용됩니다. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Azure AD 그룹을 사용하여 관리자 권한 관리(미리 보기)

Windows 10 버전 2004부터 Azure AD 그룹을 사용하여 [제한된 그룹](/windows/client-management/mdm/policy-csp-restrictedgroups) MDM 정책으로 Azure AD 조인 디바이스에 대한 관리자 권한을 관리할 수 있습니다. 이 정책을 사용하면 개별 사용자 또는 Azure AD 그룹을 Azure AD 조인 디바이스에 있는 로컬 관리자 그룹에 할당하여, 여러 디바이스 그룹에 대한 고유한 관리자를 구성하는 세분성을 제공할 수 있습니다. 

> [!NOTE]
> Windows 10 20H2 업데이트부터는 제한된 그룹 정책 대신 [로컬 사용자 및 그룹](/windows/client-management/mdm/policy-csp-localusersandgroups) 정책을 사용하는 것이 좋습니다.

현재는 이러한 정책을 관리하는 Intune에 UI가 없으며, [사용자 지정 OMA-URI 설정](/mem/intune/configuration/custom-settings-windows-10)을 사용하여 이러한 정책을 구성해야 합니다. 이러한 정책 중 하나를 사용하기 위한 몇 가지 고려 사항은 다음과 같습니다. 

- 정책을 통해 Azure AD 그룹을 추가하려면 [그룹용 Microsoft Graph API](/graph/api/resources/group)를 실행하여 가져올 수 있는 그룹의 SID가 필요합니다. SID는 API 응답의 속성 `securityIdentifier`에 의해 정의됩니다.

- 제한된 그룹 정책을 적용하면, 멤버 목록에 없는 그룹의 현재 멤버가 제거됩니다. 따라서 이 정책을 새 멤버 또는 그룹으로 적용하면, 기존 관리자, 즉 디바이스에 조인한 사용자, 디바이스의 디바이스 관리자 역할 및 전역 관리자 역할이 제거됩니다. 기존 멤버를 제거하지 않으려면, 제한된 그룹 정책에서 멤버 목록의 일부로 그것들을 구성해야 합니다. 그룹 멤버 자격에 대한 증분 업데이트를 허용하는 로컬 사용자 및 그룹 정책을 사용하는 경우에는 이 제한 사항이 적용됩니다.

- 두 정책을 모두 사용하는 관리자 권한은 Windows 10 디바이스의 잘 알려진 해당 그룹에 대해서만 평가됩니다(해당 그룹: 관리자, 사용자, 게스트, Power Users, 원격 데스크톱 사용자 및 원격 관리 사용자). 

- Azure AD 그룹을 사용하여 로컬 관리자를 관리하는 것은 하이브리드 Azure AD 조인 또는 Azure AD 등록 디바이스에 적용되지 않습니다.

- Windows 10 버전 2004 이전에는 제한된 그룹 정책이 있었지만, 여기에서 디바이스의 로컬 관리자 그룹의 멤버로 Azure AD 그룹을 지원하지는 않았습니다. 
- 두 정책 중 하나를 사용하여 디바이스에 배포된 Azure AD 그룹은 원격 데스크톱 연결에 적용되지 않습니다. Azure AD 조인 디바이스에 대한 원격 데스크톱 권한을 제어하려면 개별 사용자의 SID를 적절한 그룹에 추가해야 합니다. 

> [!IMPORTANT]
> Azure AD를 사용한 Windows 로그인은 관리자 권한에 대해 최대 20개 그룹의 평가를 지원합니다. 각 디바이스에 20개 이하의 Azure AD 그룹을 지정하여 관리자 권한이 올바르게 할당되었는지 확인하는 것이 좋습니다. 이 제한은 중첩된 그룹에도 적용됩니다. 


## <a name="manage-regular-users"></a>일반 사용자 관리

기본적으로 Azure AD는 디바이스에서 관리자 그룹에 Azure AD 조인을 수행하는 사용자를 추가합니다. 일반 사용자가 로컬 관리자가 되지 않도록 하려면 다음 옵션을 사용할 수 있습니다.

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot은 조인을 수행하는 기본 사용자가 로컬 관리자가 되는 것을 방지하는 옵션을 제공합니다. 이 작업은 [Autopilot 프로필 만들기](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)를 통해 수행할 수 있습니다.
- [대량 등록](/intune/windows-bulk-enroll) - 대량 등록의 컨텍스트에서 수행되는 Azure AD 조인은 자동으로 생성된 사용자의 컨텍스트에서 발생합니다. 디바이스가 조인된 후에 로그인하는 사용자는 관리자 그룹에 추가되지 않습니다.   

## <a name="manually-elevate-a-user-on-a-device"></a>수동으로 디바이스에서 사용자 권한 상승 

Azure AD 조인 프로세스를 사용하는 것 외에, 수동으로 하나의 특정 디바이스에서 일반 사용자를 로컬 관리자로 권한을 상승시킬 수 있습니다. 이 단계를 사용하려면 로컬 관리자 그룹의 구성원이어야 합니다. 

**Windows 10 1709** 릴리스부터 **설정 -> 계정 -> 다른 사용자** 에서 이 작업을 수행할 수 있습니다. **회사 또는 학교 사용자 추가** 를 선택하고 **사용자 계정** 아래에 사용자의 UPN을 입력한 다음, **계정 유형** 에서 ‘관리자’를 선택합니다.  
 
또한 명령 프롬프트를 사용하여 사용자를 추가할 수 있습니다.

- 테넌트 사용자가 온-프레미스 Active Directory에서 동기화되면 `net localgroup administrators /add "Contoso\username"`을 사용합니다.
- 테넌트 사용자가 Azure AD에서 생성되면 `net localgroup administrators /add "AzureAD\UserUpn"`을 사용합니다.

## <a name="considerations"></a>고려 사항 

디바이스 관리자 역할에는 그룹을 할당할 수 없으며, 개별 사용자만 허용됩니다.

디바이스 관리자는 모든 Azure AD 조인 디바이스에 할당됩니다. 특정 디바이스 집합으로 범위를 지정할 수 없습니다.

디바이스 관리자 역할에서 사용자를 제거하는 경우 로그인되어 있는 한 디바이스에서 로컬 관리자 권한을 계속 가집니다. 권한은 다음 번에 로그인하는 중에 또는 새 Primary Refresh Tokens이 발급된 후 해지됩니다. 권한 상승과 유사한 이러한 해지는 최대 4시간까지 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 디바이스를 관리하는 방법에 대한 개요를 보려면 [Azure Portal을 사용하여 디바이스 관리](device-management-azure-portal.md)를 참조하세요.
- 디바이스 기반의 조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 디바이스 기반 조건부 액세스 정책 구성](../conditional-access/require-managed-devices.md)을 참조하세요.
