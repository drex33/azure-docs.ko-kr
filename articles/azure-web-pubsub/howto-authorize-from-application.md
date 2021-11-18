---
title: Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여
description: 이 문서에서는 Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한을 부여하는 방법을 제공합니다.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: ef06bd1bc74b8065cb1f7d12cd6a21584feecbba
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706915"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-azure-applications"></a>Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여

Azure Web PubSub Service는 Azure 애플리케이션의 요청에 권한을 부여하는 azure AD(Azure Active Directory)를 [지원합니다.](../active-directory/develop/app-objects-and-service-principals.md) 

이 문서에서는 Azure 애플리케이션에서 Web PubSub 리소스에 대한 요청에 권한을 부여하도록 Web PubSub 리소스 및 코드를 구성하는 방법을 보여 줍니다.

## <a name="register-an-application"></a>애플리케이션 등록

첫 번째 단계는 Azure 애플리케이션을 등록하는 것입니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Active Directory** 검색하여 선택합니다.
2. **관리** 섹션에서 **앱 등록** 선택합니다.
3. **새 등록** 을 클릭합니다.

    ![애플리케이션 등록 스크린샷](./media/aad-authorization/register-an-application.png)

4. 애플리케이션의 표시 **이름** 을 입력합니다.
5. **등록을 클릭하여 등록을** 확인합니다.

애플리케이션이 등록되면 개요 페이지에서 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID를** 찾을 수 있습니다. 이러한GUID는 다음 단계에서 유용할 수 있습니다.

![애플리케이션의 스크린샷](./media/aad-authorization/application-overview.png)

애플리케이션 등록에 대한 자세한 내용은 다음을 참조하세요.
- [빠른 시작: Microsoft ID 플랫폼 애플리케이션을 등록합니다.](../active-directory/develop/quickstart-register-app.md)

## <a name="add-credentials"></a>자격 증명 추가

인증서와 클라이언트 암호(문자열)를 모두 자격 증명으로 기밀 클라이언트 앱 등록에 추가할 수 있습니다.

### <a name="client-secret"></a>클라이언트 암호

애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 클라이언트 비밀을 만들려면 다음 단계를 수행합니다.

1. **관리** 섹션에서 **인증서 & 비밀을 선택합니다.**
1. 클라이언트 **비밀** 탭에서 **새 클라이언트 암호** 를 클릭합니다.
![클라이언트 암호 만들기 스크린샷](./media/aad-authorization/new-client-secret.png)
1. 클라이언트 암호에 대한 **설명을** 입력하고 **만료 시간** 를 선택합니다.
1. **클라이언트 암호의** 값을 복사한 다음 안전한 위치에 붙여넣습니다. 
    > [!NOTE]
    > 비밀은 한 번만 표시됩니다.
### <a name="certificate"></a>인증서

클라이언트 비밀을 만드는 대신 인증을 업로드할 수도 있습니다.

![인증 업로드 스크린샷](./media/aad-authorization/upload-certificate.png)

자격 증명을 추가하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [자격 증명 추가](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal 역할 할당 추가

이 샘플에서는 Web `Web PubSub Service Owner` PubSub 리소스를 통해 서비스 주체(애플리케이션)에 역할을 할당하는 방법을 보여줍니다. 

> [!Note]
> 역할은 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯한 모든 범위에 할당할 수 있습니다. 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해를 참조하세요.](../role-based-access-control/scope-overview.md)
1. [Azure Portal](https://portal.azure.com/)Web PubSub 리소스로 이동합니다.

1. **Access Control(IAM)를** 클릭하여 Azure Web PubSub에 대한 액세스 제어 설정을 표시합니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷은 Web PubSub 리소스에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

   ![액세스 제어 스크린샷](./media/aad-authorization/access-control.png)

1. **추가 > 역할 할당 추가를** 클릭합니다.

1. **역할** 탭에서 를 `Web PubSub App Server` 선택합니다.

1. **다음** 을 클릭합니다.

   ![역할 할당 추가 스크린샷](./media/aad-authorization/add-role-assignment.png)

1. **멤버** 탭의 **액세스 권한 할당** 섹션에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.

1. **멤버 선택을** 클릭합니다.

3. 역할을 할당할 애플리케이션을 검색하여 선택합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

4. **다음** 을 클릭합니다.

   ![서비스 주체에 역할을 할당하는 스크린샷](./media/aad-authorization/assign-role-to-service-principals.png)

5. **검토 + 할당을** 클릭하여 변경 내용을 확인합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.
Azure 역할 할당을 할당하고 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes"></a>샘플 코드

공식적으로 4개의 프로그래밍 언어를 지원합니다.

- [C#](./howto-create-serviceclient-with-net-and-azure-identity.md)
- [Python](./howto-create-serviceclient-with-python-and-azure-identity.md)
- [Java](./howto-create-serviceclient-with-java-and-azure-identity.md)
- [JavaScript](./howto-create-serviceclient-with-javascript-and-azure-identity.md)

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [Web PubSub용 Azure AD 개요](concept-azure-ad-authorization.md)
- [관리 ID에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여](howto-authorize-from-managed-identity.md)