---
title: '빠른 시작: Azure Active Directory 테넌트 만들기'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 인증 및 권한 부여를 위해 Microsoft ID 플랫폼을 사용하는 애플리케이션 개발에 사용할 Azure Active Directory 테넌트를 만드는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cc30f39d20235bf33cfcaa49f9a98e14ab29aee0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471368"
---
# <a name="quickstart-set-up-a-tenant"></a>빠른 시작: 테넌트 설정

ID 및 액세스 관리를 위해 Microsoft ID 플랫폼을 사용하는 앱을 빌드하려면 Azure AD(Azure Active Directory) *테넌트* 에 액세스할 수 있어야 합니다. 이는 Azure AD 테넌트에 있으며, 여기서 앱을 등록 및 관리하고, Microsoft 365 및 다른 웹 API의 데이터에 대한 액세스를 구성하고, 조건부 액세스와 같은 기능을 사용하도록 설정합니다.

테넌트는 조직을 나타냅니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 시작할 때 수신하는 Azure AD의 전용 인스턴스입니다. 이러한 관계는 Azure, Microsoft Intune 또는 Microsoft 365에 가입하면 시작될 수 있습니다.

각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다. 회사 및 학교 ID, 고객 ID(Azure AD B2C 테넌트인 경우) 및 앱 등록은 고유하게 표현됩니다. 테넌트 내의 앱 등록은 테넌트 또는 모든 테넌트 내의 계정의 인증만 허용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>환경 유형 결정

두 가지 유형의 환경을 만들 수 있습니다. 환경은 전적으로 앱에서 인증할 사용자 유형에 따라 결정합니다. 

이 빠른 시작에서는 빌드할 앱 유형에 대한 두 가지 시나리오를 다룹니다.

* 회사 및 학교(Azure AD) 계정 또는 Microsoft 계정(예: Outlook.com 및 Live.com)
* 소셜 및 로컬(Azure AD B2C) 계정

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>회사 및 학교 계정 또는 개인 Microsoft 계정

회사 및 학교 계정이나 개인 Microsoft 계정에 대한 환경을 구축하려면 기존 Azure AD 테넌트를 사용하거나 새 테넌트를 만들면 됩니다.
### <a name="use-an-existing-azure-ad-tenant"></a>기존 Azure AD 테넌트 사용

대부분의 개발자는 이미 Azure AD 테넌트(예: Microsoft 365 또는 Azure 구독)와 연결된 서비스 또는 구독을 통해 테넌트를 확보하고 있습니다.

테넌트를 확인하려면 다음을 수행하세요.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다. 애플리케이션을 관리하는 데 사용할 계정을 사용합니다.
1. 오른쪽 위 모서리를 확인합니다. 테넌트가 있는 경우 자동으로 로그인됩니다. 계정 이름 바로 아래에 테넌트 이름이 표시됩니다.
   * 이름, 이메일 주소, 디렉터리 또는 테넌트 ID(GUID) 및 도메인을 보려면 계정 이름을 마우스로 가리킵니다.
   * 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다.

> [!TIP]
> 테넌트 ID를 찾으려면 다음을 수행하면 됩니다.
> * 계정 이름을 마우스로 가리켜 디렉터리/테넌트 ID를 확인합니다.
> * Azure Portal에서 **Azure Active Directory** > **속성** > **테넌트 ID** 를 검색하여 선택합니다.

계정과 연결된 테넌트가 없으면 계정 이름 아래에 GUID가 표시됩니다. Azure AD 테넌트를 만들어야만 앱 등록 등의 작업을 수행할 수 있습니다.

### <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

Azure AD 테넌트가 아직 없거나 개발을 위한 새 테넌트를 만들려는 경우 [Azure AD에서 새 테넌트 만들기](../fundamentals/active-directory-access-create-new-tenant.md)를 참조하세요. 또는 Azure Portal에서 [디렉터리 생성 환경](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)을 사용 합니다. 

새 테넌트를 만들려면 다음 정보를 제공합니다.

- **조직 이름**
- **초기 도메인** - 초기 도메인 `<domainname>.onmicrosoft.com`은 편집하거나 삭제할 수 없습니다. 나중에 사용자 지정 도메인 이름을 추가할 수 있습니다.
- **국가 또는 지역**

> [!NOTE]
> 테넌트의 이름을 지정할 때는 영숫자를 사용하세요. 특수 문자는 허용되지 않습니다. 이름은 256자를 초과하면 안 됩니다.

## <a name="social-and-local-accounts"></a>소셜 및 로컬 계정

소셜 및 로컬 계정에 로그인하는 앱 빌드를 시작하려면 Azure AD B2C 테넌트를 만드세요. 시작하려면 [Azure AD B2C 테넌트 만들기](../../active-directory-b2c/tutorial-create-tenant.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱을 등록](quickstart-register-app.md)하여 Microsoft ID 플랫폼과 통합합니다.
