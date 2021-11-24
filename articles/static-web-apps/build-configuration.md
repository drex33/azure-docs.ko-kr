---
title: Azure 정적 Web Apps에 대 한 빌드 구성
description: Azure Static Web Apps에 대 한 빌드 프로세스를 제어 하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 11/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 0e3912277212fc4cad9b1fc21200ecc1936c3e04
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077323"
---
# <a name="build-configuration-for-azure-static-web-apps"></a>Azure 정적 Web Apps에 대 한 빌드 구성

Azure 정적 Web Apps 빌드 구성은 Azure Pipelines 또는 GitHub 작업을 통해 구동 됩니다.  각 사이트에는 사이트를 빌드하고 배포 하는 방법을 제어 하는 YAML 구성 파일이 있습니다. 이 문서에서는 파일의 구조 및 옵션에 대해 설명합니다.

다음 표에서는 사용 가능한 구성 설정을 보여 줍니다.

| 속성 | 설명 | 필수 |
|---|---|---|
| `app_location` | 이 폴더에는 프런트 엔드 응용 프로그램에 대 한 소스 코드가 포함 되어 있습니다. 값은 GitHub의 리포지토리 루트와 Azure DevOps의 현재 작업 폴더에 상대적입니다. | 예 |
| `api_location` | API 응용 프로그램에 대 한 소스 코드를 포함 하는이 폴더입니다. 값은 GitHub의 리포지토리 루트와 Azure DevOps의 현재 작업 폴더에 상대적입니다. | No |
| `output_location` | 웹 앱에서 빌드 단계를 실행 하는 경우 출력 위치는 공용 파일이 생성 되는 폴더입니다. 대부분의 프로젝트에서는 `output_location` 를 기준으로 합니다 `app_location` . 그러나 .NET 프로젝트의 경우 위치는 게시 출력 폴더를 기준으로 합니다. | No |
| `app_build_command` | Node.js 응용 프로그램의 경우 사용자 지정 명령을 정의 하 여 정적 콘텐츠 응용 프로그램을 빌드할 수 있습니다.<br><br>예를 들어 Angular 애플리케이션에 대한 프로덕션 빌드를 구성하려면 `build-prod`라는 이름의 npm 스크립트를 만들어 `ng build --prod`를 실행하고 `npm run build-prod`를 사용자 지정 명령으로 입력합니다. 이를 비워 두면 워크플로에서 `npm run build` 또는 `npm run build:azure` 명령을 실행하려고 시도합니다. | No |
| `api_build_command` | Node.js 응용 프로그램의 경우 사용자 지정 명령을 정의 하 여 Azure Functions API 응용 프로그램을 빌드할 수 있습니다. | No |
| `skip_app_build` | 프런트 엔드 앱 빌드를 건너뛰려면 값을 `true`로 설정합니다. | No |

이러한 설정을 사용 하 여 정적 웹 앱에 대 한 CI/CD (지속적인 통합/지속적인 업데이트)를 실행 하도록 GitHub 작업 또는 [Azure Pipelines](publish-devops.md) 를 설정할 수 있습니다.

> [!NOTE]
> 현재 `app_build_command` Node.js 빌드에 대해서만 정의할 수 있습니다 `api_build_command` .

## <a name="file-name-and-location"></a>파일 이름 및 위치

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

구성 파일은 GitHub에 의해 생성 되 고 *GitHub/워크플로* 폴더에 저장 되며 형식으로 이름이 지정 `azure-static-web-apps-<RANDOM_NAME>.yml` 됩니다.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

기본적으로 구성 파일은 이름으로 리포지토리의 루트에 저장 됩니다 `azure-pipelines.yml` .

---

## <a name="build-configuration"></a>빌드 구성

다음 샘플 구성은 리포지토리의 변경 내용을 모니터링 합니다. 커밋이 분기로 푸시 될 때 `main` 응용 프로그램은 폴더로 빌드되고의 `app_location` 파일은 `output_location` 공용 웹에 제공 됩니다. 또한 *api* 폴더의 응용 프로그램은 사이트의 경로 아래에서 사용할 수 있습니다 `api` .

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

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
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations ######
          app_location: "src" # App source code path relative to repository root
          api_location: "api" # Api source code path relative to repository root - optional
          output_location: "public" # Built app content directory, relative to app_location - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

이 구성에서:

- `main`분기가 커밋에 대해 모니터링 됩니다.
- 분기에 대 한 끌어오기 [](https://help.github.com/actions/reference/events-that-trigger-workflows) 요청이 `main` 열려 있거나, 동기화 되었거나, 다시 열리거나, 닫힐 때 GitHub 작업 워크플로가 트리거됩니다.
- 는 `build_and_deploy_job` 커밋을 푸시 하거나 속성에 나열 된 분기에 대해 끌어오기 요청을 열 때 실행 `on` 됩니다.
- 는 `app_location` `src` 웹 앱에 대 한 소스 파일이 들어 있는 폴더를 가리킵니다. 이 값을 리포지토리 루트에 설정 하려면을 사용 `/` 합니다.
- 는 `api_location` `api` 사이트의 API 끝점에 대 한 Azure Functions 응용 프로그램이 포함 된 폴더를 가리킵니다. 이 값을 리포지토리 루트에 설정 하려면을 사용 `/` 합니다.
- 는 `output_location` `public` 응용 프로그램의 소스 파일의 최종 버전이 포함 된 폴더를 가리킵니다. 에 상대적 `app_location` 입니다. .NET 프로젝트의 경우 위치는 게시 출력 폴더를 기준으로 합니다.

`repo_token` `action` `azure_static_web_apps_api_token` Azure Static Web Apps에 의해 설정 되므로, 및에 대 한 값을 변경 하지 마세요.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: self
    submodules: true
  - task: AzureStaticWebApp@0
    inputs:
      app_location: 'src' # App source code path relative to cwd
      api_location: 'api' # Api source code path relative to cwd
      output_location: 'public' # Built app content directory relative to app_location - optional
      cwd: '$(System.DefaultWorkingDirectory)/myapp' # Working directory - optional
      azure_static_web_apps_api_token: $(deployment_token)
```

이 구성에서:

- `main`분기가 커밋에 대해 모니터링 됩니다.
- 는 `app_location` `src` 웹 앱에 대 한 소스 파일이 들어 있는 폴더를 가리킵니다. 이 값은 작업 디렉터리 ()를 기준으로 `cwd` 합니다. 작업 디렉터리로 설정 하려면를 사용 `/` 합니다.
- 는 `api_location` `api` 사이트의 API 끝점에 대 한 Azure Functions 응용 프로그램이 포함 된 폴더를 가리킵니다. 이 값은 작업 디렉터리 ()를 기준으로 `cwd` 합니다. 작업 디렉터리로 설정 하려면를 사용 `/` 합니다.
- 는 `output_location` `public` 응용 프로그램의 소스 파일의 최종 버전이 포함 된 폴더를 가리킵니다. 이 값은에 상대적입니다 `app_location` . .NET 프로젝트의 경우 위치는 게시 출력 폴더를 기준으로 합니다.
- 는 `cwd` 작업 디렉터리를 가리키는 절대 경로입니다. 기본값은 `$(System.DefaultWorkingDirectory)`입니다.
- `$(deployment_token)`변수는 [생성 된 Azure DevOps 배포 토큰](./publish-devops.md)을 가리킵니다.

> [!NOTE]
> `app_location` 및 `api_location` 는 작업 디렉터리 ()를 기준으로 해야 하며의 `cwd` 하위 디렉터리 여야 합니다 `cwd` .

---

## <a name="custom-build-commands"></a>사용자 지정 빌드 명령

응용 프로그램 Node.js 응용 프로그램 또는 API 빌드 프로세스 중에 실행 되는 명령에 대 한 세분화 된 제어를 수행할 수 있습니다. 다음 예제에서는 및에 대 한 값을 사용 하 여 빌드를 정의 하는 방법을 보여 줍니다 `app_build_command` `api_build_command` .

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: '/'
  api_location: 'api'
  output_location: 'dist'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
```
# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
...

inputs:
  app_location: 'src'
  api_location: 'api'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  output_location: 'public'
  azure_static_web_apps_api_token: $(deployment_token)
```

---

## <a name="skip-building-front-end-app"></a>프런트 엔드 앱 빌드 건너뛰기

프런트 엔드 앱에 대 한 빌드를 완전히 제어 해야 하는 경우 자동 빌드를 우회 하 고 이전 단계에서 빌드한 앱을 배포할 수 있습니다.

프런트 엔드 앱 빌드를 건너뛰려면:

- `app_location`배포 하려는 파일의 위치로 설정 합니다.
- `skip_app_build`를 `true`로 설정합니다.
- `output_location`빈 문자열 ()로 설정 `''` 합니다.

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src/dist'
  api_location: 'api'
  output_location: ''
  skip_app_build: true
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yml
...

inputs:
  app_location: 'src/dist'
  api_location: 'api'
  output_location: '' # Leave this empty
  skip_app_build: true
  azure_static_web_apps_api_token: $(deployment_token)
```

---

> [!NOTE]
> 프런트 엔드 앱에 대한 빌드만 건너뛸 수 있습니다. API가 있는 경우 항상 빌드됩니다.

## <a name="environment-variables"></a>환경 변수

작업 구성의 `env` 섹션을 통해 빌드에 대한 환경 변수를 설정할 수 있습니다.

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

```yaml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src'
  api_location: 'api'
  output_location: 'public'

env: # Add environment variables here
  HUGO_VERSION: 0.58.0
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Azure DevOps는 현재 환경 변수를 지원 하지 않습니다.

---

## <a name="monorepo-support"></a>monorepo 지원

monorepo는 둘 이상의 애플리케이션에 대한 코드를 포함하는 리포지토리입니다. 기본적으로 Static Web Apps 워크플로 파일은 리포지토리의 모든 파일을 추적하지만 단일 앱을 대상으로 구성을 조정할 수 있습니다.

단일 앱의 대상으로 워크플로 파일을 지정하려면 `push` 및 `pull_request` 섹션에 경로를 지정합니다.

# <a name="github-actions"></a>[GitHub Actions](#tab/github-actions)

Monorepo를 설정 하는 경우 각 정적 앱 구성은 단일 앱에 대 한 파일로 범위가 한정 됩니다. 다른 워크플로 파일은 리포지토리의 _.github/workflows_ 폴더에 나란히 표시됩니다.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

다음 예에서는 _azure-static-web-apps-purple-pond.yml_ 파일의 `push` 및 `pull_request` 섹션에 `paths` 노드를 추가하는 방법을 보여 줍니다.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

이 예제에서는 다음 파일에 대 한 변경 내용만 새 빌드를 트리거합니다.

- _app1_ 폴더 내의 모든 파일
- _api1_ 폴더 내의 모든 파일
- 앱의 _azure-static-web-apps-purple-pond.yml_ 워크플로 파일에 대한 변경 내용

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

단일 리포지토리에서 두 개 이상의 애플리케이션을 지원하려면 별도의 워크플로 파일을 만들고 다른 Azure Pipelines 연결합니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사전 프로덕션 환경에서 끌어오기 요청 검토](review-publish-pull-requests.md)
