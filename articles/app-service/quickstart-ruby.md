---
title: '빠른 시작: Ruby 앱 만들기'
description: 첫 번째 Ruby 앱을 App Service의 Linux 컨테이너에 배포하여 Azure App Service를 시작하세요.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dffb2634b59c54632364d8469244edb6840d7b9d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435423"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>App Service에서 Ruby on Rails 앱 만들기

[Linux의 Azure App Service](overview.md#app-service-on-linux)는 Linux 운영 체제를 사용하여 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작 자습서에서는 [Cloud Shell](../cloud-shell/overview.md)을 사용하여 Linux의 App Service에 Ruby on Rails 앱을 배포하는 방법을 보여줍니다.

> [!NOTE]
> Ruby 개발 스택은 현재 Ruby on Rails만 지원합니다. Sinatra와 같은 다른 플랫폼을 사용하거나 지원되지 않는 Ruby 버전을 사용하려는 경우 [사용자 지정 컨테이너에서 실행](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux)해야 합니다.

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2.6 이상 설치</a>
* <a href="https://git-scm.com/" target="_blank">Git 설치</a>

## <a name="download-the-sample"></a>샘플 다운로드

1. 터미널 창에서 샘플 애플리케이션을 로컬 머신에 복제하고 샘플 코드가 포함된 디렉터리로 이동합니다. 

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. 기본 분기가 `main`인지 확인합니다.

    ```bash
    git branch -m main
    ```
    
    > [!TIP]
    > App Service에는 분기 이름 변경이 필요하지 않습니다. 그러나 많은 리포지토리가 기본 분기를 `main`으로 변경하고 있으므로 이 자습서에서는 `main`에서 리포지토리를 배포하는 방법도 보여 줍니다. 자세한 내용은 [배포 분기 변경](deploy-local-git.md#change-deployment-branch)을 참조하세요.

## <a name="run-the-application-locally"></a>애플리케이션을 로컬로 실행

1. 필요한 gem을 설치합니다. 샘플에 포함된 `Gemfile`이 있으므로 다음 명령을 실행하기만 하면 됩니다.

    ```bash
    bundle install
    ```

1. gem이 설치되면 앱을 시작합니다.

    ```bash
    bundle exec rails server
    ```

1. 웹 브라우저를 사용하여 `http://localhost:3000`으로 이동한 후 앱을 로컬로 테스트합니다.

    ![Hello World가 구성됨](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>웹앱 만들기

1. `myAppServicePlan` App Service 계획에서 [웹앱](overview.md#app-service-on-linux)을 만듭니다. 

    Cloud Shell에서 [`az webapp create`](/cli/azure/webapp) 명령을 사용할 수 있습니다. 다음 예에서 `<app-name>`을 전역적으로 고유한 앱 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9` 및 `-`). 런타임은 `RUBY|2.6`으로 설정됩니다. 지원되는 모든 런타임을 보려면 [`az webapp list-runtimes --linux`](/cli/azure/webapp)를 실행합니다. 

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.6' --deployment-local-git
    ```

    웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>
    
    git 배포를 활성화하여 새 빈 웹앱을 만들었습니다.

    > [!NOTE]
    > Git 원격의 URL은 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 형식으로 `deploymentLocalGitUrl` 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.
    >

1. 기본 제공 이미지를 사용하여 새로 만든 웹앱을 보려면 앱으로 이동합니다. _&lt;app-name>_ 을 웹앱 이름으로 바꿉니다.

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    새로운 웹앱은 다음과 같아야 합니다.

    ![시작 페이지](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>애플리케이션 배포

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)] 

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

## <a name="browse-to-the-app"></a>앱으로 이동

배포가 완료되면 웹앱이 다시 시작될 때까지 10초 정도 기다린 후 웹앱으로 이동하여 결과를 확인합니다.

```bash
http://<app-name>.azurewebsites.net
```

![업데이트된 웹앱](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> 앱을 다시 시작하는 동안 브라우저 또는 `Hey, Ruby developers!` 기본 페이지에서 HTTP 상태 코드 `Error 503 Server unavailable`을 확인할 수 있습니다. 앱을 완전하게 다시 시작하는 데 몇 분 정도 걸릴 수 있습니다.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Postgres과 Ruby on Rails](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby 앱 구성](configure-language-ruby.md)
