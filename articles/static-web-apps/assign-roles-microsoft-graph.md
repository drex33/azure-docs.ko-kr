---
title: '자습서: Microsoft Graph를 사용하여 Azure Static Web Apps 역할 할당'
description: 서버리스 함수를 사용하여 Active Directory 그룹 멤버 자격을 기반으로 하여 사용자 지정 사용자 역할을 할당하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: cshoe
keywords: 정적 웹앱 권한 부여, 사용자 역할 할당, 사용자 지정 역할
ms.openlocfilehash: bf9089f7e645dd32bbfd5f30041f76f71a04a5ec
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858832"
---
# <a name="tutorial-assign-custom-roles-with-a-function-and-microsoft-graph"></a>자습서: 함수 및 Microsoft Graph를 사용하여 사용자 지정 역할 할당

이 문서에서는 함수를 사용하여 [Microsoft Graph](https://developer.microsoft.com/graph)를 쿼리하고, Active Directory 그룹 멤버 자격을 기반으로 하여 사용자에게 사용자 지정 역할을 할당하는 방법을 보여 줍니다.

이 자습서에서는 다음에 대해 알아봅니다.

- 정적 웹앱을 배포합니다.
- Azure Active Directory 앱 등록을 만듭니다.
- Azure Active Directory를 사용하여 사용자 지정 인증을 설정합니다.
- 사용자의 Active Directory 그룹 자격을 쿼리하고, 사용자 지정 역할 목록을 반환하는 [서버리스 함수](authentication-authorization.md?tabs=function#role-management)를 구성합니다.

> [!NOTE]
> 이 자습서에서는 [함수를 사용하여 역할을 할당](authentication-authorization.md?tabs=function#role-management)해야 합니다. 함수 기반 역할 관리는 현재 미리 보기에 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- **활성 Azure 계정:** 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- Azure Active Directory 애플리케이션을 만드는 데 충분한 권한이 있어야 합니다.

## <a name="create-a-github-repository"></a>GitHub 리포지토리 만들기

1. 다음 위치로 이동하여 새 리포지토리를 만듭니다.
    - [https://github.com/staticwebdev/roles-function/generate](https://github.com/login?return_to=/staticwebdev/roles-function/generate)

1. 리포지토리 이름을 **my-custom-roles-app** 으로 지정합니다.

1. **템플릿에서 리포지토리 만들기** 를 선택합니다.

## <a name="deploy-the-static-web-app-to-azure"></a>Azure에 정적 웹앱 배포

1. 새 브라우저 창에서 [Azure Portal](https://portal.azure.com)로 이동하고, Azure 계정으로 로그인합니다.

1. 왼쪽 위 모서리에 있는 **리소스 만들기** 를 선택합니다.

1. 검색 상자에서 **정적 웹앱** 을 입력합니다.

1. **Static Web App** 을 선택합니다.

1. **만들기** 를 선택합니다.

1. 다음 정보를 사용하여 Azure Static Web App을 구성합니다.

    | **입력** | **값** | **참고 사항** |
    | ---------- | ---------- | ---------- |
    | _구독_ | Azure 구독 선택 | |
    | _리소스 그룹_ | **my-custom-roles-app-group** 이라는 새 리소스 그룹을 만듭니다. | |
    | _이름_ | **my-custom-roles-app**  | |
    | _호스팅 계획_ | **Standard** | 함수를 사용하여 인증을 사용자 지정하고 역할을 할당하려면 표준 요금제가 필요합니다. |
    | _지역_ | 가장 가까운 지역을 선택합니다. | |
    | _배포 세부 정보_ | 원본으로 **GitHub** 를 선택합니다. | |

1. **GitHub로 로그인** 단추를 선택하고 GitHub로 인증합니다.

1. 리포지토리를 만든 _조직_ 의 이름을 선택합니다.

1. _리포지토리_ 드롭다운에서 **my-custom-roles-app** 을 선택합니다.

1. _분기_ 드롭다운에서 **기본** 을 선택합니다.

1. _빌드 세부 정보_ 섹션에서 이 앱에 대한 구성 세부 정보를 추가합니다.

    | **입력** | **값** | **참고 사항** |
    | ---------- | ---------- | ---------- |
    | _빌드 사전 설정_ | **사용자 지정** | |
    | _앱 위치_ | **frontend** | 앱이 포함된 리포지토리의 폴더 |
    | _API 위치_ | **api** | API가 포함된 리포지토리의 폴더 |
    | _출력 위치_ | | 이 앱에는 빌드 출력이 없습니다. |

1. **검토 + 만들기** 를 선택합니다. 그런 다음, **만들기** 를 선택하여 정적 웹앱을 만들고 첫 번째 배포를 시작합니다.

1. **리소스로 이동** 을 선택하여 새 정적 웹앱을 엽니다.

1. 개요 섹션에서 애플리케이션의 **URL** 을 찾습니다. 이 URL은 Active Directory 인증을 설정하고 앱을 테스트하는 데 필요하므로 이 값을 텍스트 편집기에 복사합니다.

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

1. Azure Portal에서 *Azure Active Directory* 를 검색하여 이동합니다.

1. 메뉴 모음에서 **앱 등록** 을 선택합니다.

1. **+ 새 등록** 을 선택하여 *애플리케이션 등록* 페이지를 엽니다.

1. 애플리케이션의 이름을 입력합니다. 예를 들어 **MyStaticWebApp** 입니다.

1. *지원되는 계정 유형* 의 경우 **이 조직 디렉터리 계정의 계정만** 을 선택합니다.

1. *리디렉션 URI* 에 대해 **웹** 을 선택하고, 정적 웹앱의 Azure Active Directory 로그인 [인증 콜백](authentication-custom.md#authentication-callbacks)을 입력합니다. 예들 들어 `<YOUR_SITE_URL>/.auth/login/aad/callback`입니다.

    `<YOUR_SITE_URL>`을 정적 웹앱의 URL로 바꿉니다.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-app-registration.png" alt-text="앱 등록 만들기":::

1. **등록** 을 선택합니다.

1. 앱 등록이 만들어지면 *기본 정보* 섹션의 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID** 를 텍스트 편집기에 복사합니다. 이러한 값은 정적 웹앱에서 Active Directory 인증을 구성하는 데 필요합니다.

### <a name="enable-id-tokens"></a>ID 토큰 사용

1. 메뉴 모음에서 *인증* 을 선택합니다.

1. *암시적 허용 및 하이브리드 흐름* 섹션에서 **ID 토큰(암시적 및 하이브리드 흐름에 사용됨)** 을 선택합니다.

    :::image type="content" source="media/assign-roles-microsoft-graph/enable-id-tokens.png" alt-text="ID 토큰 사용":::
    
    이 구성은 Static Web Apps에서 사용자를 인증하는 데 필요합니다.

1. **저장** 을 선택합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

1. 메뉴 모음에서 *인증서 및 비밀* 을 선택합니다.

1. *클라이언트 암호* 섹션에서 **새 클라이언트 암호** 를 선택합니다.

1. 클라이언트 암호의 이름을 입력합니다. 예를 들어 **MyStaticWebApp** 입니다.

1. *만료* 필드에 대해 기본값인 _6개월_ 을 그대로 둡니다.

    > [!NOTE]
    > 새 비밀을 생성하고 앱을 해당 값으로 업데이트하여 만료 날짜 이전에 비밀을 회전해야 합니다.

1. **추가** 를 선택합니다.

1. 만든 클라이언트 암호의 **값** 을 적어 둡니다. 이 값은 정적 웹앱에서 Active Directory 인증을 구성하는 데 필요합니다.

    :::image type="content" source="media/assign-roles-microsoft-graph/create-client-secret.png" alt-text="클라이언트 비밀 만들기":::

## <a name="configure-active-directory-authentication"></a>Active Directory 인증 구성

1. 브라우저에서 배포한 정적 웹앱이 포함된 GitHub 리포지토리를 엽니다. *frontend/staticwebapp.config.json* 에서 앱의 구성 파일로 이동합니다. 여기에는 다음 섹션이 포함되어 있습니다.

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
              "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

    > [!NOTE]
    > Microsoft Graph에 대한 액세스 토큰을 가져오려면 `loginParameters` 필드를 `resource=https://graph.microsoft.com`으로 구성해야 합니다.

1. **편집** 단추를 선택하여 파일을 업데이트합니다.

1. `<YOUR_AAD_TENANT_ID>`를 Azure Active Directory의 디렉터리(테넌트) ID로 바꿔 `https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>`의 *openIdIssuer* 값을 업데이트합니다.

1. **기본 분기에 직접 적용** 을 선택하고, **변경 내용 적용** 을 선택합니다.

1. GitHub Actions에서 정적 웹앱을 업데이트하는 트리거를 실행합니다.

1. Azure Portal에서 정적 웹앱 리소스로 이동합니다.

1. 메뉴 모음에서 **구성** 을 선택합니다.

1. *애플리케이션 설정* 섹션에서 다음 설정을 추가합니다.

    | 이름 | 값 |
    |------|-------|
    | `AAD_CLIENT_ID` | *Active Directory 애플리케이션 클라이언트 ID* |
    | `AAD_CLIENT_SECRET` | *Active Directory 애플리케이션 클라이언트 암호 값* |

1. **저장** 을 선택합니다.

## <a name="verify-custom-roles"></a>사용자 지정 역할 확인

샘플 애플리케이션에는 사용자가 미리 정의된 그룹에 있는지 확인하기 위해 Microsoft Graph를 쿼리하는 서버리스 함수(*api/GetRoles/index.js*)가 포함되어 있습니다. 함수는 사용자의 그룹 멤버 자격에 따라 사용자에게 사용자 지정 역할을 할당합니다. 애플리케이션은 이러한 사용자 지정 역할에 따라 특정 경로를 제한하도록 구성됩니다.

1. GitHub 리포지토리에서 *api/GetRoles/index.js* 에 있는 *GetRoles* 함수로 이동합니다. 위쪽 근처에는 사용자 지정 사용자 역할을 Azure Active Directory 그룹에 매핑하는 `roleGroupMappings` 개체가 있습니다.

1. **편집** 단추를 클릭합니다.

1. 개체를 Azure Active Directory 테넌트의 그룹 ID로 업데이트합니다.

    예를 들어 ID가 `6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` 및 `b6059db5-9cef-4b27-9434-bb793aa31805`인 그룹이 있는 경우 개체를 다음으로 업데이트합니다.

    ```js
    const roleGroupMappings = {
      'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
      'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    *GetRoles* 함수는 사용자가 Azure Active Directory를 사용하여 성공적으로 인증될 때마다 호출됩니다. 이 함수는 사용자의 액세스 토큰을 사용하여 Microsoft Graph에서 Active Directory 그룹 멤버 자격을 쿼리합니다. 사용자가 `roleGroupMappings` 개체에 정의된 그룹의 멤버인 경우 함수에서 해당 사용자 지정 역할이 반환됩니다.
    
    위의 예제에서 사용자가 ID가 `b6059db5-9cef-4b27-9434-bb793aa31805`인 Active Directory 그룹의 멤버인 경우 `reader` 역할이 부여됩니다.

1. **기본 분기에 직접 적용** 을 선택하고, **변경 내용 적용** 을 선택합니다.

1. GitHub Actions에서 정적 웹앱을 업데이트하는 트리거를 실행합니다.

1. 배포가 완료되면 앱의 URL로 이동하여 변경 내용을 확인할 수 있습니다.

1. Azure Active Directory를 사용하여 정적 웹앱에 로그인합니다.

1. 로그인하면 ID의 Active Directory 그룹 멤버 자격에 따라 할당된 역할 목록이 샘플 앱에 표시됩니다. 앱의 일부 경로에 대한 액세스는 이러한 역할에 따라 허용되거나 금지됩니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하여 배포된 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

1. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.

1. 이 자습서에서 사용한 리소스 그룹 이름을 선택합니다.

1. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증 및 권한 부여](./authentication-authorization.md)
