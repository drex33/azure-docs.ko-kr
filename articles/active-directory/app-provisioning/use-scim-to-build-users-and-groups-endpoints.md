---
title: Azure Active Directory에서 앱에 사용자 프로비전하기 위한 SCIM 엔드포인트 빌드
description: SCIM 엔드포인트를 개발하고, SCIM API를 Azure Active Directory와 통합하고, 사용자 및 그룹을 클라우드 애플리케이션에 자동으로 프로비전하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5f1799b6a8fe0ff4feb69797deec11c29c69a81c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990599"
---
# <a name="tutorial-develop-a-sample-scim-endpoint-in-azure-active-directory"></a>자습서: Azure Active Directory에서 샘플 SCIM 엔드포인트 개발

새 엔드포인트를 처음부터 빌드하지 않도록 [SCIM(System for Cross-domain Identity Management)](https://aka.ms/scimoverview)으로 시작할 수 있는 몇 가지 [참조 코드](https://aka.ms/scimreferencecode)를 만들었습니다. 5분 내에 코드 없이 SCIM 엔드포인트를 실행할 수 있습니다.

이 자습서에서는 Azure에서 SCIM 참조 코드를 배포하고 Postman을 사용하거나 Azure AD(Azure Active Directory) SCIM 클라이언트와 통합하여 테스트하는 방법을 설명합니다. 이 자습서는 SCIM을 시작하려는 개발자 또는 SCIM 엔드포인트를 테스트하고자 하는 개발자용입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure에서 SCIM 엔드포인트를 배포합니다.
> * SCIM 엔드포인트를 테스트합니다.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Azure에서 SCIM 엔드포인트 배포

이 단계에서는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 및 [Azure App Service](../../app-service/index.yml)를 사용하여 SCIM 엔드포인트를 서비스에 배포합니다. SCIM 참조 코드를 로컬로 실행하거나 온-프레미스 서버에서 호스팅하거나 다른 외부 서비스에 배포할 수도 있습니다.

1. GitHub에서 [참조 코드](https://github.com/AzureAD/SCIMReferenceCode)로 이동하고 **복제 또는 다운로드** 를 선택합니다.

1. **바탕 화면에서 열기** 를 선택하거나, 링크를 복사하고, Visual Studio를 열고, **복제 또는 체크 아웃 코드** 를 선택하여 복사된 링크를 입력하고 로컬 복사본을 만듭니다.

1. Visual Studio에서 호스팅 리소스에 대한 액세스 권한이 있는 계정에 로그인해야 합니다.

1. 솔루션 탐색기에서 *Microsoft.SCIM.sln* 을 열고 *Microsoft.SCIM.WebHostSample* 파일을 마우스 오른쪽 단추로 클릭합니다. **게시** 를 선택합니다.

    ![샘플 파일을 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 이 솔루션을 로컬로 실행하려면 프로젝트를 두 번 클릭하고 **IIS Express** 를 선택하여 로컬 호스트 URL이 있는 웹 페이지로 프로젝트를 시작합니다.

1. **프로필 만들기** 를 선택하고 **App Service** 및 **새로 만들기** 가 선택되어 있는지 확인합니다.

    ![게시 창을 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 단계를 따라 대화 상자 옵션에서 앱 이름을 원하는 이름으로 바꿉니다. 이 이름은 앱과 SCIM 엔드포인트 URL에 모두 사용됩니다.

    ![새 앱 서비스를 만드는 과정을 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 사용할 리소스 그룹을 선택하고 **게시** 를 선택합니다.

    ![새 앱 서비스를 게시하는 과정을 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. **Azure App Service** > **구성** 에서 애플리케이션으로 이동하고 **새 애플리케이션 설정** 을 선택하여 `https://sts.windows.net/<tenant_id>/` 값으로 *Token__TokenIssuer* 설정을 추가합니다. `<tenant_id>`를 Azure AD 테넌트 ID로 바꿉니다. [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)을 사용하여 SCIM 엔드포인트를 테스트하려면 `Development` 값으로 *ASPNETCORE_ENVIRONMENT* 설정을 추가합니다.

   ![애플리케이션 설정 창을 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)에서 엔터프라이즈 애플리케이션을 사용하여 엔드포인트를 테스트하는 경우 두 가지 옵션이 있습니다. `Development`에 환경을 유지하고 `/scim/token` 엔드포인트에서 테스트 토큰을 제공하거나, 환경을 `Production`으로 변경하고 토큰 필드를 비워 둘 수 있습니다.

정말 간단하죠. 이제 SCIM 엔드포인트가 게시되고 Azure App Service URL을 사용하여 SCIM 엔드포인트를 테스트할 수 있습니다.

## <a name="test-your-scim-endpoint"></a>SCIM 엔드포인트 테스트

SCIM 엔드포인트에 요청하려면 권한이 부여되어야 합니다. SCIM 표준에는 쿠키, 기본 인증, TLS 클라이언트 인증 또는 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)에 명시된 메서드를 비롯하여 인증 및 권한 부여에 대한 여러 옵션이 있습니다.

사용자 이름 및 암호와 같이 보안되지 않은 메서드는 OAuth와 같은 보다 안전한 메서드에 사용하지 않도록 합니다. Azure AD는 긴 전달자 토큰(갤러리 및 비 갤러리 애플리케이션의 경우) 및 OAuth 권한 부여(갤러리 애플리케이션의 경우)를 지원합니다.

> [!NOTE]
> 리포지토리에 제공된 권한 부여 메서드는 테스트 전용입니다. Azure AD와 통합하는 경우 권한 부여 지침을 검토합니다. [SCIM 엔드포인트에 대한 프로비전 계획](use-scim-to-provision-users-and-groups.md)을 참조하세요.

개발 환경에서는 참조 코드와 같이 프로덕션에 안전하지 않은 기능을 사용하여 보안 토큰 유효성 검사의 동작을 제어할 수 있습니다. 토큰 유효성 검사 코드는 자체 서명된 보안 토큰을 사용하고 서명 키는 구성 파일에 저장됩니다. *appsettings.Development.json* 파일의 **Token:IssuerSigningKey** 매개 변수를 참조하세요.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> `/scim/token` 엔드포인트에 **GET** 요청을 보내면 구성된 키를 사용하여 토큰이 발급됩니다. 이 토큰은 후속 권한 부여를 위한 전달자 토큰으로 사용할 수 있습니다.

기본 토큰 유효성 검사 코드는 Azure AD 토큰을 사용하도록 구성되며, *appsettings.json* 파일의 **Token:TokenIssuer** 매개 변수를 사용하여 발급 테넌트를 구성해야 합니다.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman을 사용하여 엔드포인트 테스트

SCIM 엔드포인트를 배포한 후에는 SCIM RFC와 호환되는지 테스트할 수 있습니다. 이 예에서는 사용자와 그룹에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업, 필터링, 그룹 멤버 자격 업데이트 및 사용자 비활성화의 유효성을 검사하는 Postman의 테스트 집합을 제공합니다.

엔드포인트는 `{host}/scim/` 디렉터리에 있으며 표준 HTTP 요청을 사용하여 상호 작용할 수 있습니다. `/scim/` 경로를 수정하려면 **AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **컨트롤러** 에서 *ControllerConstant.cs* 를 참조하세요.

> [!NOTE]
> HTTP 엔드포인트는 로컬 테스트에만 사용할 수 있습니다. Azure AD 프로비전 서비스는 엔드포인트에서 HTTPS를 지원해야 합니다.

1. [Postman](https://www.getpostman.com/downloads/)을 다운로드하고 애플리케이션을 시작합니다.
1. `https://aka.ms/ProvisioningPostman` 링크를 복사하여 Postman에 붙여넣어 테스트 컬렉션을 가져옵니다.

    ![Postman에서 테스트 컬렉션을 가져오는 과정을 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 다음 변수가 있는 테스트 환경을 만듭니다.

   |환경|변수|값|
   |-|-|-|
   |IIS Express를 사용하여 로컬로 프로젝트 실행|||
   ||**서버**|`localhost`|
   ||**포트**|`:44359` *(반드시 **`:`** 입력)*|
   ||**Api**|`scim`|
   |Kestrel을 사용하여 로컬로 프로젝트 실행|||
   ||**서버**|`localhost`|
   ||**포트**|`:5001` *(반드시 **`:`** 입력)*|
   ||**Api**|`scim`|
   |Azure에서 엔드포인트 호스트|||
   ||**서버**|*(SCIM URL 입력)*|
   ||**포트**|*(비워 둠)*|
   ||**Api**|`scim`|

1. Postman 컬렉션의 **Get Key** 를 사용하여 토큰 엔드포인트에 **GET** 요청을 보내고 후속 요청에 대한 **토큰** 변수에 저장할 보안 토큰을 검색합니다.

   ![Postman Get Key 폴더를 보여 주는 스크린샷.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > SCIM 엔드포인트를 안전하게 만들려면 연결하기 전에 보안 토큰이 필요합니다. 이 자습서에서는 `{host}/scim/token` 엔드포인트를 사용하여 자체 서명된 토큰을 생성합니다.

정말 간단하죠. 이제 **Postman** 컬렉션을 실행하여 SCIM 엔드포인트 기능을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클라이언트에 대한 상호 운용성을 가진 SCIM 호환 사용자 및 그룹 엔드포인트를 개발하려면 [SCIM 클라이언트 구현](http://www.simplecloud.info/#Implementations2)을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: SCIM 엔드포인트에 대한 프로비전 개발 및 계획](use-scim-to-provision-users-and-groups.md)
> [자습서: 갤러리 앱에 대한 프로비전 구성](configure-automatic-user-provisioning-portal.md)