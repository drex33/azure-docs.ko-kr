---
title: 엔터프라이즈 애플리케이션 소유자 할당
titleSuffix: Azure AD
description: Azure Active Directory 애플리케이션에 소유자 할당
services: active-directory
documentationcenter: ''
author: saipradeepb23
manager: celesteDG
ms.service: active-directory
ms.workload: identity
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 08/03/2021
ms.author: saibandaru
ms.openlocfilehash: f08b2c40e5a81804274472a31150c1f0f3187d9e
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659244"
---
# <a name="assign-enterprise-application-owners-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 애플리케이션 소유자 할당

소유자 할당은 특정 애플리케이션 등록 또는 엔터프라이즈 애플리케이션에 대한 Azure AD 구성의 모든 측면을 관리하는 기능을 부여하는 간단한 방법입니다. 소유자인 사용자는 Single Sign-On 구성, 프로비저닝, 사용자 할당과 같은 엔터프라이즈 애플리케이션의 조직 특정 구성을 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자와 달리, 소유자는 자신이 소유한 엔터프라이즈 애플리케이션만 관리할 수 있습니다.

엔터프라이즈 애플리케이션의 소유자가 되려면 사용자여야 합니다. 그룹은 소유자로 할당할 수 없습니다. 소유자는 애플리케이션에 자격 증명을 추가하고 이러한 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 애플리케이션은 소유자보다 많은 권한을 가질 수 있으므로 소유자가 사용자 또는 서비스 사용자로 액세스할 수 있는 권한 상승을 갖습니다. 

애플리케이션 소유자는 애플리케이션의 권한에 따라 애플리케이션을 가장하는 동안 사용자 또는 다른 개체를 만들거나 업데이트할 수 있습니다. 애플리케이션 소유자는 개별 애플리케이션으로 범위가 한정된 애플리케이션 관리자와 동일한 권한을 갖습니다. 자세한 내용은 [Azure AD 기본 제공 역할](../roles/permissions-reference.md#application-administrator)을 참조하세요. 

## <a name="assign-an-owner"></a>소유자 할당

엔터프라이즈 애플리케이션 등록에 소유자를 할당하려면 다음을 수행합니다.

1. 조직의 **애플리케이션 관리자** 역할 또는 **클라우드 애플리케이션 관리자** 역할에 적합한 계정으로 [Azure AD 조직](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)에 로그인합니다.
2. **엔터프라이즈 애플리케이션** 을 선택한 후 소유자를 추가하려는 애플리케이션을 선택합니다.
3. **소유자** 를 선택한 다음, **추가** 를 선택하여 소유자를 선택할 수 있는 사용자 계정 목록을 확인합니다.
4. 애플리케이션의 소유자가 될 사용자 계정을 검색하여 선택합니다.
5. **선택** 을 클릭하여 애플리케이션의 소유자로 선택한 사용자 계정을 추가합니다.

> [!NOTE]
> 사용자 설정 **Azure AD 관리 포털에 대한 액세스 제한** 이 `Yes`으로 설정된 경우, 관리자가 아닌 사용자는 Azure Portal을 사용하여 소유한 애플리케이션을 관리할 수 없습니다. 소유된 엔터프라이즈 애플리케이션에서 수행할 수 있는 작업에 대한 자세한 내용은 [소유된 엔터프라이즈 애플리케이션](../fundamentals/users-default-permissions.md#owned-enterprise-applications)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory에서 앱 등록 권한 위임](../roles/delegate-app-roles.md)
