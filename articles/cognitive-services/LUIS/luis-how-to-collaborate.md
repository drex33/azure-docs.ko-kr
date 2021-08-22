---
title: 다른 사람과 협업 - LUIS
titleSuffix: Azure Cognitive Services
description: 앱 소유자는 제작 리소스에 기여자를 추가할 수 있습니다. 이러한 기여자는 모델을 수정하고, 앱을 학습하고, 게시할 수 있습니다.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 5a320d6368d4fdecaf4001fc9255c4c0ff3b276c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285593"
---
# <a name="add-contributors-to-your-app"></a>앱에 기여자 추가

앱 소유자는 앱에 기여자를 추가할 수 있습니다. 이러한 기여자는 모델을 수정하고, 앱을 학습하고, 게시할 수 있습니다. 계정을 [마이그레이션](luis-migration-authoring.md)한 후에는 **액세스 제어(IAM)** 페이지를 사용하여 제작 리소스에 대한 Azure Portal에서 _기여자_ 가 관리됩니다. 협력자의 이메일 주소와 _기여자_ 역할을 사용하여 사용자를 추가합니다.

## <a name="add-contributor-to-azure-authoring-resource"></a>Azure 제작 리소스에 기여자 추가

LUIS 제작 환경이 LUIS 포털의 **관리 -> Azure 리소스** 페이지에서 제작 리소스와 연결된 경우 마이그레이션한 것입니다.

Azure Portal에서 LUIS(Language Understanding) 작성 리소스를 찾습니다. 형식은 `LUIS.Authoring`입니다. 리소스의 **Access Control(IAM)** 페이지에서 기여할 사용자에 대한 **기여자** 역할을 추가합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="view-the-app-as-a-contributor"></a>기여자로 앱 보기

기여자로 추가된 후에는 [LUIS 포털에 로그인](sign-in-luis-portal.md)합니다.

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>여러 개의 이메일이 있는 사용자

LUIS 앱에 기여자를 추가하는 경우 정확한 이메일 주소를 지정합니다. Azure AD(Azure Active Directory)에서는 단일 사용자가 둘 이상의 이메일 계정을 함께 사용하도록 허용하지만, LUIS에서는 사용자가 기여자를 추가할 때 지정된 이메일 주소로 로그인하도록 요구합니다.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 리소스

조직에서 Azure AD([Azure Active Directory](../../active-directory/index.yml))를 사용하는 경우 사용자가 LUIS를 사용하려고 할 때 LUIS에 사용자의 액세스 정보에 대한 권한이 필요합니다. LUIS에서는 최소한의 리소스를 필요로 합니다.

관리자가 동의하거나 관리자의 동의가 필요하지 않은 계정으로 가입하려고 하면 자세한 설명이 표시됩니다.

* 조직 계정으로 앱에 로그인하고 앱이 프로필을 읽도록 할 수 있습니다. 또한 앱이 기본 회사 정보를 읽도록 할 수 있습니다. LUIS에 사용자 ID, 이메일, 이름과 같은 기본 프로필 데이터를 읽을 수 있는 권한을 부여합니다.
* 현재 앱을 사용하고 있지 않을 때도 앱이 데이터를 표시하고 업데이트하도록 할 수 있습니다. 사용자의 액세스 토큰을 새로 고치는 데 권한이 필요합니다.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 테넌트 사용자

LUIS는 표준 Azure AD(Azure Active Directory)에서 동의 흐름을 사용합니다.

테넌트 관리자는 Azure AD에서 LUIS를 사용하기 위해 권한을 부여 받아야 하는 사용자와 함께 작업해야 합니다.

* 첫째, 사용자가 LUIS에 로그인하면 관리 승인이 필요한 팝업 대화 상자가 표시됩니다. 사용자는 계속하기 위해 먼저 테넌트 관리자를 연락합니다.
* 둘째, 테넌트 관리자가 LUIS에 로그인하면 동의 흐름 팝업 대화 상자가 표시됩니다. 이 대화 상자에서 관리자는 사용자에게 권한을 부여해야 합니다. 관리자가 권한을 수락하면 사용자는 LUIS를 계속 사용할 수 있습니다. 테넌트 관리자는 LUIS에 로그인하지 않을 경우 LUIS에 대한 [동의](https://account.activedirectory.windowsazure.com/r#/applications)에 액세스할 수 있습니다. 이 페이지에서는 `LUIS` 이름을 포함하는 항목으로 목록을 필터링할 수 있습니다.

테넌트 관리자는 특정 사용자만 LUIS를 사용하도록 지정하려는 경우 다음과 같은 몇 가지 방법을 사용할 수 있습니다.
* "관리자 동의"(Azure AD의 모든 사용자에 대한 동의)를 제공한 다음 엔터프라이즈 애플리케이션 속성 아래의 "사용자 할당 필요"를 "예"로 설정하고, 마지막으로 원하는 사용자만 애플리케이션에 할당/추가합니다. 이 방법을 사용하는 경우에도 관리자는 앱에 "관리자 동의"를 제공하지만, 앱에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 두 번째 솔루션은 [Microsoft Graph의 Azure AD ID 및 액세스 관리 API](/graph/azuread-identity-access-management-concept-overview)를 사용하여 각 특정 사용자에게 동의를 제공하는 것입니다.

Azure Active Directory 사용자 및 동의에 대해 자세히 알아보세요.
* 특정 사용자 세트만 사용 가능하도록 [앱 제한](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)

## <a name="next-steps"></a>다음 단계

* [버전을 사용](luis-how-to-manage-versions.md)하여 앱 수명 주기를 제어하는 방법을 알아봅니다.
* [리소스 작성](luis-how-to-azure-subscription.md) 및 해당 리소스에 [기여자 추가](luis-how-to-collaborate.md)에 대해 이해합니다.
* 제작 및 런타임 리소스를 [만드는 방법](luis-how-to-azure-subscription.md) 알아보기
* 새 [제작 리소스](luis-migration-authoring.md)로 마이그레이션