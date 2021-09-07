---
title: 셀프 서비스 가입 사용자 흐름 추가 - Azure AD
description: 조직에서 빌드한 앱에 대한 사용자 흐름을 만듭니다. 그런 다음, 해당 앱을 방문하는 사용자는 사용자 흐름에 구성된 옵션을 사용하여 게스트 계정을 얻을 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/26/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36bc5a283665abb03bd1c49a0a067b551bcc979d
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114720933"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app"></a>앱에 셀프 서비스 가입 사용자 흐름 추가

빌드하는 애플리케이션에 대해 사용자가 앱에 등록하고 새 게스트 계정을 만들 수 있는 사용자 흐름을 만들 수 있습니다. 셀프 서비스 등록 사용자 흐름은 등록하는 동안 사용자가 따라야 하는 일련의 단계, 사용자가 사용하도록 허용할 ID 공급자 및 수집하려는 사용자 특성을 정의합니다. 하나 이상의 애플리케이션을 단일 사용자 흐름과 연결할 수 있습니다.

> [!NOTE]
> 사용자 흐름을 조직에서 빌드한 앱과 연결할 수 있습니다. 사용자 흐름은 SharePoint 또는 팀과 같은 Microsoft 앱에 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="add-identity-providers-optional"></a>ID 공급자 추가(선택 사항)

Azure AD는 셀프 서비스 가입의 기본 ID 공급자입니다. 즉, 사용자는 기본적으로 Azure AD 계정을 사용하여 가입할 수 있습니다. 셀프 서비스 등록 사용자 흐름에서 Google 및 Facebook과 같은 소셜 ID 공급자, Microsoft 계정 및 메일 일회성 암호도 포함할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Microsoft 계정 ID 공급자](microsoft-account.md)
- [이메일 일회용 암호 인증](one-time-passcode.md)
- [소셜 ID 공급자 목록에 Facebook 추가](facebook-federation.md)
- [소셜 ID 공급자 목록에 Google 추가](google-federation.md)

### <a name="define-custom-attributes-optional"></a>사용자 지정 특성 정의(선택 사항)

사용자 특성은 셀프 서비스 가입 중에 사용자로부터 수집된 값입니다. Azure AD에는 기본 제공 특성 세트가 함께 제공되지만 사용자 흐름에 사용할 사용자 지정 특성을 직접 만들 수 있습니다. 또한 Microsoft Graph API를 사용하여 이러한 특성을 읽고 쓸 수도 있습니다. [사용자 흐름의 사용자 지정 특성 정의](user-flow-add-custom-attributes.md)를 참조하세요.

## <a name="enable-self-service-sign-up-for-your-tenant"></a>테넌트에 셀프 서비스 가입을 사용하도록 설정

애플리케이션에 셀프 서비스 가입 사용자 흐름을 추가하려면 먼저 테넌트에 이 기능을 사용하도록 설정해야 합니다. 사용하도록 설정하면 사용자 흐름에 컨트롤이 제공되고 이를 통해 사용자 흐름을 애플리케이션과 연결할 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. **사용자 설정** 을 선택한 다음, **외부 사용자** 에서 **외부 협업 설정 관리** 를 선택합니다.
4. **사용자 흐름을 통해 게스트 셀프 서비스 가입 사용** 토글을 **예** 로 설정합니다.

   ![셀프 서비스 가입 사용](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. **저장** 을 선택합니다.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>셀프 서비스 가입을 위한 사용자 흐름 만들기

다음으로, 셀프 서비스 가입을 위한 사용자 흐름을 만들고 애플리케이션에 추가합니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **사용자 흐름** 을 선택하고 **새 사용자 흐름** 을 선택합니다.

   ![새 사용자 흐름 단추 추가](media/self-service-sign-up-user-flow/new-user-flow.png)

5. **만들기** 페이지에서 사용자 흐름의 **이름** 을 입력합니다. 이름 앞에 **B2X_1_** 가 자동으로 추가되는 것을 확인합니다.
6. **ID 공급자** 목록에서 외부 사용자가 애플리케이션에 로그인하는 데 사용할 수 있는 ID 공급자를 하나 이상 선택합니다. **Azure Active Directory 가입** 이 기본적으로 선택됩니다. ID 공급자를 추가하는 방법에 대한 자세한 내용은 이 문서의 앞부분에 나오는 [시작하기 전에](#before-you-begin)를 참조하세요.
7. **사용자 특성** 에서 사용자로부터 수집하려는 특성을 선택합니다. 추가 특성의 경우 **더 보기** 를 선택합니다. 예를 들어 **자세히 보기** 를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호** 의 특성 및 클레임을 선택합니다. **확인** 을 선택합니다.

   ![새 사용자 흐름 만들기 페이지](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> 사용자가 처음으로 가입할 때만 특성을 수집할 수 있습니다. 사용자가 가입한 후에는 사용자 흐름을 변경하더라도 특성 정보를 수집하라는 메시지가 더 이상 표시되지 않습니다.

8. **만들기** 를 선택합니다.
9. **사용자 흐름** 목록에 새 사용자 흐름이 표시됩니다. 필요한 경우 페이지를 새로 고칩니다.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>특성 컬렉션 양식의 레이아웃 선택

가입 페이지에서 특성이 표시되는 순서를 선택할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
2. **외부 ID** 를 선택하고 **사용자 흐름** 을 선택합니다.
3. 목록에서 셀프 서비스 가입 사용자 흐름을 선택합니다.
4. **사용자 지정** 에서 **페이지 레이아웃** 을 선택합니다.
5. 수집하도록 선택한 특성이 나열됩니다. 표시 순서를 변경하려면 특성을 선택하고 **위로 이동**, **아래로 이동**, **맨 위로 이동** 또는 **맨 아래로 이동** 을 선택합니다.
6. **저장** 을 선택합니다.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>셀프 서비스 가입 사용자 흐름에 애플리케이션 추가

이제 애플리케이션을 사용자 흐름과 연결할 수 있습니다.

1. Azure AD 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Services** 아래에서 **Azure Active Directory** 를 선택합니다.
3. 왼쪽 메뉴에서 **외부 ID** 를 선택합니다.
4. **셀프 서비스 가입** 에서 **사용자 흐름** 을 선택합니다.
5. 목록에서 셀프 서비스 가입 사용자 흐름을 선택합니다.
6. 왼쪽 메뉴의 **사용** 에서 **애플리케이션** 을 선택합니다.
7. **애플리케이션 추가** 를 선택합니다.

   ![사용자 흐름에 애플리케이션 할당](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. 목록에서 애플리케이션을 선택합니다. 또는 검색 상자를 사용하여 애플리케이션을 찾은 다음, 선택합니다.
9. **선택** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [소셜 ID 공급자 목록에 Google 추가](google-federation.md)
- [소셜 ID 공급자 목록에 Facebook 추가](facebook-federation.md)
- [API 커넥터를 사용하여 웹 API를 통해 사용자 흐름 사용자 지정 및 확장](api-connectors-overview.md)
- [사용자 흐름에 사용자 지정 승인 워크플로 추가](self-service-sign-up-add-approvals.md)
