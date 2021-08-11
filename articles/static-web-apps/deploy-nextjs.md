---
title: '자습서: Azure Static Web Apps에서 정적으로 렌더링된 Next.js 웹 사이트 배포'
description: Azure Static Web Apps를 사용하여 Next.js 동적 사이트를 생성하고 배포합니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: eb4a9c69ed29b1f13ab2769044c0067779a5e195
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454135"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>Azure Static Web Apps에서 정적으로 렌더링된 Next.js 웹 사이트 배포

이 자습서에서는 [Azure Static Web Apps](overview.md)에 [Next.js](https://nextjs.org) 생성 정적 웹 사이트를 배포하는 방법을 알아봅니다. 시작하려면 Next.js 앱을 설정, 구성 및 배포하는 방법을 알아봅니다. 이 프로세스 동안 Next.js를 사용하여 정적 페이지를 생성할 때 자주 발생하는 일반적인 문제를 처리하는 방법에 대해서도 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).
- GitHub 계정. [체험 계정을 만듭니다](https://github.com/join).
- 설치된 [Node.js](https://nodejs.org).

## <a name="set-up-a-nextjs-app"></a>Next.js 앱 설정

Next.js CLI를 사용하여 앱을 만드는 대신 기존 Next.js 앱이 포함된 스타터 리포지토리를 사용할 수 있습니다. 이 리포지토리는 일반적인 배포 문제를 강조하는 동적 경로가 있는 Next.js 앱을 제공합니다. 동적 경로에는 잠시 후 자세히 알아볼 추가 배포 구성이 필요합니다.

시작하려면 템플릿 리포지토리에서 GitHub 계정 아래에 새 리포지토리를 만듭니다.

1. [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)로 이동합니다.
1. 리포지토리 이름을 **nextjs-starter** 로 지정
1. 그런 다음, 새 리포지토리를 머신에 복제합니다. `<YOUR_GITHUB_ACCOUNT_NAME>`을 계정 이름으로 바꿔야 합니다.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. 새로 복제된 Next.js 앱으로 이동합니다.

   ```bash
   cd nextjs-starter
   ```

1. 종속성을 설치합니다.

    ```bash
    npm install
    ```

1. 개발 중인 Next.js 앱을 시작합니다.

    ```bash
    npm run dev
    ```

`http://localhost:3000`으로 이동하여 앱을 엽니다. 이 경우 기본 설정 브라우저에서 다음 웹 사이트를 열어 볼 수 있습니다.

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Next.js 앱 시작":::

프레임워크/라이브러리를 클릭하면 선택한 항목에 대한 세부 정보 페이지가 표시됩니다.

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="세부 정보 페이지":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Next.js 빌드에서 정적 웹 사이트 생성

`npm run build`를 사용하여 Next.js를 빌드하면 앱은 정적 사이트가 아닌 기존 웹앱으로 빌드됩니다. 정적 사이트를 생성하려면 다음 애플리케이션 구성을 사용합니다.

1. 정적 경로를 구성하려면 프로젝트 루트에 _next.config.js_ 라는 파일을 만들고 다음 코드를 추가합니다.

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```

      이 구성은 `/`를 `/` 경로에 대해 제공되는 Next.js 페이지와 _pages/index.js_ 페이지 파일에 매핑합니다.

1. `next export` 명령을 사용하여 빌드 후 정적 사이트도 생성하도록 _package.json_ 의 빌드 스크립트를 업데이트합니다. `export` 명령은 정적 사이트를 생성합니다.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    이제 이 명령을 사용하면 Static Web Apps는 커밋을 푸시할 때마다 `build` 스크립트를 실행합니다.

1. 정적 사이트를 생성합니다.

    ```bash
    npm run build
    ```

    정적 사이트가 생성되어 작업 디렉터리의 루트에 있는 _out_ 폴더로 복사됩니다.

    > [!NOTE]
    > 이 폴더는 배포할 때 CI/CD에 의해 생성되어야 하므로 _.gitignore_ 파일에 나열됩니다.

## <a name="push-your-static-website-to-github"></a>정적 웹 사이트를 GitHub로 푸시

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 배포하고 지정된 분기에 푸시된 모든 커밋에 대해 이 작업을 계속합니다. 다음 명령을 사용하여 변경 내용을 GitHub에 동기화합니다.

1. 변경된 모든 파일 스테이징

    ```bash
    git add .
    ```

1. 모든 변경 내용 커밋

    ```bash
    git commit -m "Update build config"
    ```

1. GitHub에 변경 내용을 푸시합니다.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>정적 웹 사이트 배포

다음 단계에서는 GitHub에 방금 푸시한 앱을 Azure Static Web Apps로 연결하는 방법을 보여줍니다. Azure에 있으면 애플리케이션을 프로덕션 환경에 배포할 수 있습니다.

### <a name="create-a-static-app"></a>정적 앱 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기** 를 선택합니다.
1. **Static Web Apps** 를 검색합니다.
1. **Static Web Apps** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. _기본_ 탭에서 다음 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _구독_ | Azure 구독 이름. |
    | _리소스 그룹_ | **my-nextjs-group**  |
    | _이름_ | **my-nextjs-app** |
    | _플랜 유형_ | **Free** |
    | _Azure Functions API 및 스테이징 환경을 위한 영역_ | 가장 가까운 지역을 선택합니다. |
    | _원본_ | **GitHub** |

1. **GitHub로 로그인** 을 선택하고 GitHub로 인증합니다.

1. 다음 GitHub 값을 입력합니다.

    | 속성 | 값 |
    | --- | --- |
    | _조직_ | 원하는 GitHub 조직을 선택합니다. |
    | _리포지토리_ | **nextjs-starter** 를 선택합니다. |
    | _분기_ | **기본** 을 선택합니다. |

1. _빌드 세부 정보_ 섹션의 _빌드 사전 설정_ 드롭다운에서 **사용자 지정** 을 선택하고 기본값을 유지합니다.

1. _앱 위치_ 에서 상자에 **/** 를 입력합니다.
1. _Api 위치_ 상자를 비워 둡니다.
1. _출력 위치_ 상자에 **out** 을 입력합니다.

### <a name="review-and-create"></a>검토 후 만들기

1. **검토 + 만들기** 단추를 선택하여 세부 정보가 모두 올바른지 확인합니다.

1. **만들기** 를 선택하여 App Service Static Web App 만들기를 시작하고 배포를 위한 GitHub Action을 프로비전합니다.

1. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

1. _개요_ 창에서 *URL* 링크를 클릭하여 배포된 애플리케이션을 엽니다.

웹 사이트에서 즉시 로드를 기록하는 경우 백그라운드 GitHub Actions 워크플로가 계속 실행됩니다. 워크플로가 완료되면 브라우저 새로 고침을 클릭하여 웹앱을 볼 수 있습니다.
웹 사이트에서 즉시 로드를 기록하는 경우 백그라운드 GitHub Actions 워크플로가 계속 실행됩니다. 워크플로가 완료되면 브라우저 새로 고침을 클릭하여 웹앱을 볼 수 있습니다.

리포지토리의 작업으로 이동하여 작업 워크플로의 상태를 확인할 수 있습니다.

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>변경 내용 동기화

앱을 만들 때 Azure Static Web Apps는 리포지토리에 GitHub Actions 워크플로 파일을 만들었습니다. git 기록이 동기화되도록 이 파일을 로컬 리포지토리로 가져와야 합니다.

터미널로 돌아가 다음 `git pull origin main` 명령을 실행합니다.

## <a name="configure-dynamic-routes"></a>동적 경로 구성

새로 배포된 사이트로 이동하여 프레임워크 또는 라이브러리 로고 중 하나를 클릭합니다. 세부 정보 페이지를 가져오는 대신 404 오류 페이지가 표시됩니다.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="동적 경로의 404 오류":::

이 오류가 발생하는 이유는 Next.js가 애플리케이션 구성에 따라 홈페이지만 생성했기 때문입니다.

## <a name="generate-static-pages-from-dynamic-routes"></a>동적 경로에서 정적 페이지 생성

1. Next.js가 사용 가능한 모든 데이터 목록을 사용하여 각 프레임워크/라이브러리용 정적 페이지를 생성하도록 _next.config.js_ 파일을 업데이트합니다.

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > `exportPathMap` 함수는 비동기 함수이므로 이 함수에서 API에 대한 요청을 수행하고 반환된 목록을 사용하여 경로를 생성할 수 있습니다.

2. GitHub 리포지토리에 새 변경 내용을 푸시하고 GitHub 작업에서 사이트를 다시 빌드하는 동안 몇 분 정도 기다립니다. 빌드가 완료되면 404 오류가 사라집니다.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="동적 경로의 404 오류 수정":::

> [!div class="nextstepaction"]
> [사용자 지정 도메인 설정](custom-domain.md)
