---
title: '자습서: Azure Static Web Apps에 Hugo 사이트 게시'
description: Azure Static Web Apps에 Hugo 애플리케이션을 배포하는 방법에 대해 알아봅니다.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: aapowell
ms.openlocfilehash: 243caef4a1f2cd1d543e7bbb9f3bc127d703296e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774807"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps"></a>자습서: Azure Static Web Apps에 Hugo 사이트 게시

이 문서에서는 [Azure Static Web Apps](overview.md)에 [Hugo](https://gohugo.io/) 웹 애플리케이션을 만들고 배포하는 방법을 보여줍니다. 최종 결과는 앱이 빌드되고 게시되는 방식을 제어하는 관련 GitHub Actions를 포함하는 새로운 Azure Static Web App입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Hugo 앱 만들기
> - Azure Static Web Apps 설정
> - Azure에 Hugo 앱 배포

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.
- GitHub 계정. 계정이 없는 경우 [체험 계정을 만들](https://github.com/join) 수 있습니다.

## <a name="create-a-hugo-app"></a>Hugo 앱 만들기

Hugo CLI(명령줄 인터페이스)를 사용하여 Hugo 앱을 만듭니다.

1. OS에서 Hugo에 대한 [설치 가이드](https://gohugo.io/getting-started/installing/)를 따르세요.

1. 터미널 열기

1. Hugo CLI를 실행하여 새 앱을 만듭니다.

   ```bash
   hugo new site static-app
   ```

1. 새로 만든 앱으로 이동합니다.

   ```bash
   cd static-app
   ```

1. Git 리포지토리를 초기화합니다.

   ```bash
   git init
   ```

1. 분기 이름이 `main`인지 확인합니다.

   ```bash
   git branch -M main
   ```

1. 다음으로, 테마를 git 하위 모듈로 설치한 다음, Hugo 구성 파일에서 테마를 지정하여 사이트에 테마를 추가합니다.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. 변경 내용을 커밋합니다.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>GitHub에 애플리케이션 푸시

Azure Static Web Apps에 연결하려면 GitHub의 리포지토리가 필요합니다. 다음 단계에서는 사이트용 리포지토리를 만드는 방법을 보여줍니다.

1. **hugo-static-app** 이라는 [https://github.com/new](https://github.com/new)에서 빈 GitHub 리포지토리(추가 정보를 만들지 않음)를 만듭니다.

1. 로컬 리포지토리에 GitHub 리포지토리를 원격으로 추가합니다. 다음 명령에서 `<YOUR_USER_NAME>` 자리 표시자 대신 GitHub 사용자 이름을 추가해야 합니다.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. 로컬 리포지토리를 GitHub로 푸시합니다.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>웹앱 배포

다음 단계에서는 새 정적 사이트 앱을 만들어 프로덕션 환경에 배포하는 방법을 보여줍니다.

### <a name="create-the-application"></a>애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기** 선택
1. **Static Web Apps** 를 검색합니다.
1. **Static Web Apps** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. _기본_ 탭에서 다음 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _구독_ | Azure 구독 이름. |
    | _리소스 그룹_ | **my-hugo-group**  |
    | _이름_ | **hugo-static-app** |
    | _플랜 유형_ | **Free** |
    | _Azure Functions API 및 스테이징 환경을 위한 영역_ | 가장 가까운 지역을 선택합니다. |
    | _원본_ | **GitHub** |

1. **GitHub로 로그인** 을 선택하고 GitHub로 인증합니다.

1. 다음 GitHub 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _조직_ | 원하는 GitHub 조직을 선택합니다. |
    | _리포지토리_ | **hugo-static-app** 을 선택합니다. |
    | _분기_ | **기본** 을 선택합니다. |

1. _빌드 세부 정보_ 섹션의 _빌드 사전 설정_ 드롭다운에서 **Hugo** 를 선택하고 기본값을 유지합니다.

### <a name="review-and-create"></a>검토 후 만들기

1. **검토 + 만들기** 단추를 선택하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기** 를 선택하여 App Service Static Web App 만들기를 시작하고 배포를 위한 GitHub Action을 프로비전합니다.

1. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

1. 리소스 화면에서 _URL_ 링크를 클릭하여 배포된 애플리케이션을 엽니다. GitHub Action이 완료되려면 1~2분 정도 기다려야 할 수 있습니다.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="배포된 애플리케이션":::

#### <a name="custom-hugo-version"></a>사용자 지정 Hugo 버전

정적 웹앱을 생성하면 애플리케이션에 대한 게시 구성 설정이 포함된 [워크플로 파일](./build-configuration.md)이 생성됩니다. `env` 섹션의 `HUGO_VERSION`에 대한 값을 제공하여 워크플로 파일에서 특정 Hugo 버전을 지정할 수 있습니다. 다음 예제 구성에서는 Hugo를 특정 버전으로 설정하는 방법을 보여줍니다.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](custom-domain.md)
