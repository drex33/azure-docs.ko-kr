---
title: Azure Portal에서 Azure AD B2C 소비자 사용자 계정 만들기 및 삭제
description: Azure Portal을 사용하여 Azure AD B2C 디렉터리에서 소비자 사용자를 만들고 삭제하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 55151493e18a2a5cc56583b10e905a89dcc74770
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041731"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Azure Portal을 사용하여 Azure AD B2C에서 소비자 사용자 만들기 및 삭제

Azure Active Directory B2C(Azure AD B2C) 디렉터리에서 수동으로 소비자 계정을 만들고자 하는 시나리오가 있을 수 있습니다. Azure AD B2C 디렉터리의 소비자 계정은 사용자가 애플리케이션 중 하나를 사용하기 위해 등록할 때 가장 일반적으로 만들어지지만 프로그래밍 방식으로 Azure Portal을 사용하여 만들 수 있습니다. 이 문서에서는 Azure Portal의 사용자 만들기 및 삭제 방법에 중점을 둡니다.

사용자를 추가하거나 삭제하려면 계정에 *사용자 관리자* 또는 *전역 관리자* 역할이 할당되어야 합니다.

## <a name="types-of-user-accounts"></a>사용자 계정의 유형

[Azure AD B2C의 사용자 계정 개요](user-overview.md)에 설명된 대로 Azure AD B2C 디렉터리에서 만들 수 있는 세 가지 유형의 사용자 계정이 있습니다.

* 작업
* 게스트
* 소비자

이 문서에서는 Azure Portal에서의 **소비자 계정** 작업에 중점을 둡니다. 작업 및 게스트 계정 만들기 및 삭제에 대한 자세한 내용은 [Azure Active Directory를 사용하여 사용자 추가 또는 삭제](../active-directory/fundamentals/add-users-azure-active-directory.md)를 참조하세요.

## <a name="create-a-consumer-user"></a>소비자 사용자 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **관리** 에서 **사용자** 를 선택합니다.
1. **새 사용자** 를 선택합니다.
1. **Azure AD B2C 사용자 만들기** 를 선택합니다.
1. **로그인 방법** 을 선택하고 새 사용자의 **이메일** 주소 또는 **사용자 이름** 을 입력합니다. 여기에서 선택하는 로그인 방법은 Azure AD B2C 테넌트의 *로컬 계정* ID 공급자에 대해 지정한 설정과 일치해야 합니다(Azure AD B2C 테넌트의 **관리** > **ID 공급자** 참조).
1. 사용자의 **이름** 을 입력합니다. 일반적으로 사용자의 전체 이름(이름 및 성)입니다.
1. (선택 사항) 사용자의 로그인 기능을 지연시키려는 경우 **로그인을 차단** 할 수 있습니다. 나중에 Azure Portal에서 사용자의 **프로필** 을 편집하여 로그인을 사용하도록 설정할 수 있습니다.
1. **암호 자동 생성** 또는 **암호 직접 만들기** 를 선택합니다.
1. 사용자의 **이름** 및 **성** 을 지정합니다.
1. **만들기** 를 선택합니다.

**로그인 차단** 을 선택하지 않은 경우 이제 사용자가 지정한 로그인 방법(이메일 또는 사용자 이름)을 사용하여 로그인할 수 있습니다.

## <a name="reset-a-users-password"></a>사용자의 암호 재설정

관리자는 사용자가 암호를 잊어버린 경우 사용자의 암호를 재설정할 수 있습니다. 사용자의 암호를 재설정하면 임시 암호가 사용자에게 자동으로 생성됩니다. 임시 암호는 만료되지 않습니다. 임시 암호가 생성된 이후 얼마나 시간이 경과했는지와 상관없이 다음에 사용자가 로그인할 때에도 암호는 계속 작동합니다. 그런 다음, 사용자는 암호를 영구 암호로 재설정해야 합니다. 

> [!IMPORTANT]
> 사용자 암호를 재설정하기 전에 [Azure Active Directory B2C에서 암호 강제 재설정 흐름을 설정](force-password-reset.md)해야 합니다. 그렇지 않으면 사용자가 로그인할 수 없습니다.

사용자의 암호를 재설정하려면 다음을 수행합니다.

1. Azure AD B2C 디렉터리에서 **사용자** 를 선택한 다음, 암호를 재설정할 사용자를 선택합니다.
1. 재설정할 사용자를 검색하여 선택한 다음, **암호 다시 설정** 을 선택합니다.

    **암호 다시 설정** 옵션이 표시된 **Alain Charon - 프로필** 페이지가 나타납니다.

    ![암호 다시 설정 옵션이 강조 표시된 사용자의 프로필 페이지](media/manage-users-portal/user-profile-reset-password-link.png)

1. **암호 다시 설정** 페이지에서 **암호 다시 설정** 을 선택합니다.
1. 암호를 복사하고 사용자에게 제공합니다. 사용자는 다음 로그인 프로세스 중에 암호를 변경해야 합니다.


## <a name="delete-a-consumer-user"></a>소비자 사용자 삭제

1. Azure AD B2C 디렉터리에서 **사용자** 를 선택한 다음, 삭제하려는 사용자를 선택합니다.
1. **삭제** 를 선택한 다음, **예** 를 선택하여 삭제를 확인합니다.

삭제 후 처음 30일 이내에 사용자를 복원하거나 사용자를 영구적으로 삭제하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 최근에 삭제된 사용자 복원 또는 제거](../active-directory/fundamentals/active-directory-users-restore.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

자동화된 사용자 관리 시나리오의 경우(예: 다른 ID 공급자에서 Azure AD B2C 디렉터리로 사용자 마이그레이션) [Azure AD B2C: 사용자 마이그레이션](user-migration.md)을 참조하세요.
