---
title: Azure Dev/Test developer directory 테 넌 트에 사용자 추가
description: Azure 크레딧 구독에 사용자를 추가 하 고 역할 기반 컨트롤을 사용 하 여 액세스를 관리 하는 방법에 대 한 지침입니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 14ded977ab78e8fbf6baf70d1c59e4aa632927b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053110"
---
# <a name="add-users-to-your-azure-credit-subscription"></a>Azure 크레딧 구독에 사용자 추가  

구독 리소스를 관리 하 고 액세스 하려면 디렉터리 내의 사용자 여야 합니다. 구독 자체에서 계층은 Azure Active Directory (Azure AD) tenancies에 의해 제어 됩니다. Azure AD는 계정 이름 및 암호를 저장 하는 LDAP (lightweight directory protocol)입니다.  

사용자를 추가 하기 전에 비즈니스 계층 구조와 구독 내에서 필요한 액세스 수준을 결정 해야 합니다.  

## <a name="why-do-i-need-to-add-users"></a>사용자를 추가 해야 하는 이유는 무엇 인가요?

1 단계에서는 구독에 새 사용자를 추가 해야 하는지 여부를 확인 합니다. 새 사용자를 추가 해야 하는 이유에 대 한 몇 가지 예는 다음과 같습니다.  

- 프로젝트 작업 중 이며 보안을 모니터링 하기 위한 액세스 권한을 부여 해야 합니다.  
- 조직의 다른 구성원에 게 도움이 될 수 있는 새 API 프로토콜에 대 한 작업  
- 모든 리소스 그룹에 액세스할 수 있도록 구독 수준에서 액세스 권한을 부여 해야 합니다.  
- 비즈니스 수준에서 단일 리소스 그룹은 한 컴퓨터에 로그인 해야 하지만 구독에 액세스할 필요는 없습니다.  
- 작업 중인 프로젝트에 대 한 가시성과 투명도를 제공 하지만 필요한 경우 작업의 특정 부분을 계속 격리 합니다.  
- 참가할 컨설턴트를 사용자 또는 그룹 내에 추가  
- 다른 사람과 공동 작업 하 여 사전 프로덕션 테스트 및 모니터링을 원합니다.  

## <a name="where-do-i-add-users-and-their-roles-within-my-subscription"></a>내 구독 내에서 사용자와 해당 역할을 추가 하는 위치는 어디 인가요?

Azure AD 액세스 관리 내에서 중요 한 기능입니다. [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md) \( Azure RBAC는 \) azure 리소스에 대 한 세분화 된 액세스 관리를 제공 하는 권한 부여 시스템입니다.  

사용자를 추가 해야 하는지 확인 한 후에는 사용자를 추가 하는 위치 및 액세스 해야 하는 리소스를 이해 해야 합니다. 사용자가 액세스할 수 있는 리소스 집합을 범위 라고 합니다.  

작업 중인 프로젝트 및 작업을 모니터링 하 여 보안 프로토콜을 사용 하도록 설정 해야 하는 경우에는 해당 사용자에 게 관리 그룹 내에서 관리자 역할을 사용 하 여 구독 내에서 모든 권한 및 사용 권한을 보유 해야 합니다.  

![Azure의 액세스 수준 스크린샷](media/how-to-add-users-directory/access-management.png "Azure에서 관리 그룹을 사용 하 여 역할 관리")

그러나 다른 개발자 또는 동료와 공동 작업 하는 경우 리소스 또는 리소스 그룹 수준 에서만 액세스 해야 할 수 있습니다.  

Azure rbac [개요](../../role-based-access-control/overview.md) 를 참조 하 여 azure rbac가 작동 하는 방식과 구독 내에서 제공 하는 목적을 보다 잘 이해할 수 있습니다.  

Azure Active Directory를 사용 하 여 사용자를 추가 하거나 삭제 하는 방법  

- [사용자 추가 또는 삭제 - Azure Active Directory | Microsoft Docs](../../active-directory/fundamentals/add-users-azure-active-directory.md)  

- [Azure 역할을 할당 하는 단계-Azure RBAC | Microsoft Docs](../../role-based-access-control/role-assignments-steps.md)  
