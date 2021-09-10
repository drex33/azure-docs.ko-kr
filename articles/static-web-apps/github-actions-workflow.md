---
title: Azure Static Web Apps에 대한 GitHub Actions 워크플로
description: GitHub 리포지토리를 사용하여 Azure Static Web Apps에 지속적인 배포를 설정하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9dc0c8a83279e3a70bceebb316536485e337c873
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535807"
---
# <a name="github-actions-workflows-for-azure-static-web-apps"></a>Azure Static Web Apps에 대한 GitHub Actions 워크플로

GitHub 배포를 사용하는 경우 YAML 파일이 사이트 빌드 워크플로를 제어합니다. 이 문서에서는 파일의 구조 및 옵션에 대해 설명합니다.

배포는 개별 [단계로](#steps) 구성된 [작업을](#jobs) 실행하는 [트리거](#triggers)에서 시작됩니다.

> [!NOTE]
> Azure Static Web Apps도 Azure DevOps를 지원합니다. 파이프라인 설정에 대한 정보는 [Azure DevOps로 게시](publish-devops.md)를 참조하세요.

## <a name="file-name-and-location"></a>파일 이름 및 위치

리포지토리를 연결하면 Azure Static Web Apps 워크플로를 제어하는 파일을 생성합니다.

생성된 워크플로 파일을 보려면 다음 단계를 수행합니다.

1. GitHub에서 앱의 리포지토리를 엽니다.
1. **코드** 탭을 선택합니다.
1. **github/workflows** 를 선택합니다.
1. **azure-static-web-apps-<RANDOM_NAME>.yml** 과 유사한 파일을 선택합니다.

## <a name="triggers"></a>트리거

GitHub Actions [트리거](https://help.github.com/actions/reference/events-that-trigger-workflows)는 이벤트 트리거를 기반으로 작업을 실행하도록 GitHub Actions 워크플로에 알립니다. 워크플로 파일에서 `on` 속성을 사용하여 트리거가 나열됩니다.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

이 예제에서 *기본* 분기의 끌어오기 요청이 다음과 같은 경우 워크플로가 시작됩니다.

- 열림
- 동기화됨
- 재개
- closed

워크플로의 이 부분을 사용자 지정하여 다른 분기 또는 다른 이벤트를 대상으로 지정할 수 있습니다.

## <a name="jobs"></a>작업

각 트리거는 이벤트에 대한 응답으로 실행할 일련의 [작업](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs)을 정의합니다.

| Name | Description |
| --- | --- |
| `build_and_deploy_job` | 커밋을 푸시하거나 `on` 속성에 나열된 분기에 대해 끌어오기 요청을 열 때 실행됩니다.          |
| `close_pull_request_job` | 끌어오기 요청을 닫을 때만 실행되며 끌어오기 요청에서 만든 스테이징 환경을 제거합니다. |

## <a name="steps"></a>단계

단계는 작업의 순차적 작업입니다. 단계는 종속성 설치, 테스트 실행 및 프로덕션 환경에 애플리케이션 배포와 같은 작업을 수행합니다.

| 작업 | 단계 |
| --- | --- |
| `build_and_deploy_job` | <li>GitHub Action의 환경에서 저장소를 확인하십시오.<li>리포지토리를 빌드하여 Azure Static Web Apps에 배포합니다. |
| `close_pull_request_job` | <li>끌어오기 요청이 닫혔음을 Azure Static Web Apps에 알립니다. |

## <a name="build-and-deploy"></a>빌드 및 배포

`build_and_deploy_job`라는 단계는 Azure Static Web Apps에서 빌드하고 배포합니다. `with` 섹션에서 배포에 대해 다음 값을 사용자 지정할 수 있습니다.

다음 예제에서는 이러한 값이 워크플로 파일에 어떻게 표시되는지 보여줍니다.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

`repo_token`, `action` 및 `azure_static_web_apps_api_token`의 값을 Azure Static Web Apps 설정되기 때문에 변경하지 마세요.

## <a name="custom-build-commands"></a>사용자 지정 빌드 명령

앱 또는 API 빌드 프로세스 중에 실행되는 명령을 세부적으로 제어할 수 있습니다. 다음 명령은 작업의 `with` 섹션에서 정의할 수 있습니다.

| 명령 | 설명 |
| --- |--- |
| `app_build_command` | 정적 콘텐츠 애플리케이션을 배포하는 동안 실행할 사용자 지정 명령을 정의합니다.<br><br>예를 들어 Angular 애플리케이션에 대한 프로덕션 빌드를 구성하려면 `build-prod`라는 이름의 npm 스크립트를 만들어 `ng build --prod`를 실행하고 `npm run build-prod`를 사용자 지정 명령으로 입력합니다. 이를 비워 두면 워크플로에서 `npm run build` 또는 `npm run build:azure` 명령을 실행하려고 시도합니다. |
| `api_build_command` | Azure Functions API 애플리케이션을 배포하는 동안 실행할 사용자 지정 명령을 정의합니다. |

다음 예제에서는 작업의 섹션 내에서 사용자 지정 빌드 명령을 정의하는 방법을 `with`에서 보여줍니다.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  ###### End of Repository/Build Configurations ######
```

> [!NOTE]
> 현재는 Node.js 빌드에 대한 사용자 지정 빌드 명령만 정의할 수 있습니다. 배포는 항상 사용자 지정 명령 앞에 `npm install`을 호출합니다.

## <a name="skip-building-front-end-app"></a>프런트 엔드 앱 빌드 건너뛰기

프런트 엔드 앱에 대한 빌드를 완전히 제어해야 하는 경우 워크플로에 사용자 지정 단계를 추가할 수 있습니다. 예를 들어 자동 빌드를 무시하고 이전 단계에서 빌드된 앱을 배포하도록 선택할 수 있습니다.

앱 빌드를 건너뛰려면 `skip_app_build`를 `true`로 설정하고 `app_location`을 배포할 폴더의 위치로 설정합니다.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| 속성         | 설명                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | 프런트 엔드 앱 빌드를 건너뛰려면 값을 `true`로 설정합니다. |

> [!NOTE]
> 프런트 엔드 앱에 대한 빌드만 건너뛸 수 있습니다. API가 있는 경우 항상 빌드됩니다.

## <a name="environment-variables"></a>환경 변수

작업 구성의 `env` 섹션을 통해 빌드에 대한 환경 변수를 설정할 수 있습니다.

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
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>monorepo 지원

monorepo는 둘 이상의 애플리케이션에 대한 코드를 포함하는 리포지토리입니다. 기본적으로 Static Web Apps 워크플로 파일은 리포지토리의 모든 파일을 추적하지만 단일 앱을 대상으로 구성을 조정할 수 있습니다.

모노레포를 설정할 때 각 정적 앱에는 단일 앱의 파일로 범위가 한정된 자체 구성 파일이 있습니다. 다른 워크플로 파일은 리포지토리의 _.github/workflows_ 폴더에 나란히 표시됩니다.

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

단일 앱의 대상으로 워크플로 파일을 지정하려면 `push` 및 `pull_request` 섹션에 경로를 지정합니다.

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

이 인스턴스에서 다음 파일에 대한 변경 내용만 새 빌드를 트리거합니다.

- _app1_ 폴더 내의 모든 파일
- _api1_ 폴더 내의 모든 파일
- 앱의 _azure-static-web-apps-purple-pond.yml_ 워크플로 파일에 대한 변경 내용

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사전 프로덕션 환경에서 끌어오기 요청 검토](review-publish-pull-requests.md)
