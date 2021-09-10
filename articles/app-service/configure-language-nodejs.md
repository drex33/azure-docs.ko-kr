---
title: Node.js 앱 구성
description: Azure App Service에서 네이티브 Windows 인스턴스 또는 미리 빌드된 Linux 컨테이너의 Node.js 앱을 구성하는 방법을 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 04/23/2021
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 14ac7953654941de176bf74bd38787b33b9c864c
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225755"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Azure App Service용 Node.js 앱 구성

Node.js 앱을 모든 필수 NPM 종속성과 함께 배포해야 합니다. App Service 배포 엔진은 [Git 저장소](deploy-local-git.md) 또는 [빌드 자동화가 활성화된](deploy-zip.md#enable-build-automation-for-zip-deploy) [Zip 패키지](deploy-zip.md)를 배포할 때 자동으로 `npm install --production`을 실행합니다. 그러나 [FTP/S](deploy-ftp.md)를 사용하여 파일을 배포하는 경우 필요한 패키지를 수동으로 업로드해야 합니다.

또한 App Service에 배포하는 Node.js 개발자를 위한 주요 개념과 지침을 제공합니다. Azure App Service를 처음 사용하는 경우 먼저 [Node.js 빠른 시작](quickstart-nodejs.md) 및 [MongoDB를 사용하는 Node.js 자습서](tutorial-nodejs-mongodb-app.md)를 따라하세요.

## <a name="show-nodejs-version"></a>Node.js 버전 표시

::: zone pivot="platform-windows"  

현재 Node.js 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

지원되는 모든 Node.js 버전을 표시하려면 `https://<sitename>.scm.azurewebsites.net/api/diagnostics/runtime`로 이동하거나 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

현재 Node.js 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 Node.js 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Node.js 버전 설정

::: zone pivot="platform-windows"  

앱을 [지원되는 Node.js 버전](#show-nodejs-version)으로 설정하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 `WEBSITE_NODE_DEFAULT_VERSION`을 지원되는 버전으로 설정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

이 설정은 런타임에 또한 App Service 빌드 자동화 중 자동화된 패키지 복원 시에 사용할 Node.js 버전을 지정합니다. 이 설정은 주 부 버전만 인식하며 _LTS_ 모니터는 지원되지 않습니다.

> [!NOTE]
> 프로젝트의 `package.json`에서 Node.js 버전을 설정해야 합니다. 배포 엔진은 지원되는 모든 Node.js 버전을 포함하는 별도의 프로세스에서 실행됩니다.

::: zone-end

::: zone pivot="platform-linux"

앱을 [지원되는 Node.js 버전](#show-nodejs-version)으로 설정하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

이 설정은 런타임에 또한 Kudu에서 자동화된 패키지 복원 시에 사용할 Node.js 버전을 지정합니다.

> [!NOTE]
> 프로젝트의 `package.json`에서 Node.js 버전을 설정해야 합니다. 배포 엔진은 지원되는 모든 Node.js 버전을 포함하는 별도의 컨테이너에서 실행됩니다.

::: zone-end

## <a name="get-port-number"></a>포트 번호 가져오기

들어오는 요청을 수신하려면 Node.js 앱이 올바른 포트를 수신 대기해야 합니다.

::: zone pivot="platform-windows"  

Windows의 App Service에서 Node.js 앱은 [IISNode](https://github.com/Azure/iisnode)를 사용하여 호스트되고 Node.js 앱은 `process.env.PORT` 변수에 지정된 포트를 수신 대기해야 합니다. 다음 예제에서는 간단한 Express 앱에서 이 작업을 수행하는 방법을 보여 줍니다.

::: zone-end

::: zone pivot="platform-linux"  

App Service는 Node.js 컨테이너에 환경 변수 `PORT`를 설정하고 해당 포트 번호에서 들어오는 요청을 컨테이너에 전달합니다. 요청을 받으려면 앱이 `process.env.PORT`를 사용하여 해당 포트를 수신 대기해야 합니다. 다음 예제에서는 간단한 Express 앱에서 이 작업을 수행하는 방법을 보여 줍니다.

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

Git을 사용하여 앱을 배포하거나 [빌드 자동화가 활성화된](deploy-zip.md#enable-build-automation-for-zip-deploy) 압축 패키지를 배포하는 경우 App Service 빌드 자동화 단계는 다음 순서입니다.

1. `PRE_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.
1. npm `preinstall` 및 `postinstall` 스크립트를 포함하여 `npm install`을 플래그 없이 실행하고 `devDependencies`도 설치합니다.
1. 빌드 스크립트가 *package.json* 에 지정된 경우 `npm run build`를 실행합니다.
1. build:azure 스트립트가 *package.json* 에 지정된 경우 `npm run build:azure`를 실행합니다.
1. `POST_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.

> [!NOTE]
> [npm docs](https://docs.npmjs.com/misc/scripts)에 설명된 것처럼 `prebuild` 및 `postbuild`라는 스크립트가 지정된 경우 각각 `build` 전후에 실행됩니다. `preinstall` 및 `postinstall`은 각각 `install` 전후에 실행됩니다.

`PRE_BUILD_COMMAND` 및 `POST_BUILD_COMMAND`는 기본적으로 비어 있는 환경 변수입니다. 빌드 전 명령을 실행하려면 `PRE_BUILD_COMMAND`를 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`를 정의합니다.

다음 예제에서는 일련의 명령에 대한 두 변수를 쉼표로 구분하여 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조하세요.

App Service를 실행하고 Linux에서 Node.js 앱을 빌드하는 방법에 대한 자세한 내용은 [Oryx 설명서: Node.js 앱을 인식하고 구축하는 방법](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)을 참조하세요.

## <a name="configure-nodejs-server"></a>Node.js 서버 구성

Node.js 컨테이너는 프로덕션 프로세스 관리자인 [PM2](https://pm2.keymetrics.io/)와 함께 제공됩니다. PM2, NPM 또는 사용자 지정 명령을 사용하여 시작하도록 앱을 구성할 수 있습니다.

|도구|목적|
|--|--|
|[PM2를 사용하여 실행](#run-with-pm2)|**권장** - 프로덕션 또는 스테이징 사용. PM2는 전체 서비스 앱 관리 플랫폼을 제공합니다.|
|[npm start 실행](#run-npm-start)|개발 용도로만 사용합니다.|
|[사용자 지정 명령 실행](#run-custom-command)|개발 또는 스테이징 중 하나.|


### <a name="run-with-pm2"></a>PM2를 사용하여 실행

이 컨테이너는 일반적인 Node.js 파일 중 하나가 프로젝트에 있는 경우 PM2를 사용하여 앱을 자동으로 시작합니다.

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 다음 [PM2 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) 중 하나: *process.json* 및 *ecosystem.config.js*

다음 확장명을 사용하여 사용자 지정 시작 파일을 구성할 수도 있습니다.

- *.js* 파일
- *.json*, *.config.js*, *.yaml*, 또는 *.yml* 확장명의 [PM2 파일](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)

사용자 지정 시작 파일을 추가하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

### <a name="run-custom-command"></a>사용자 지정 명령 실행

App Service는 사용자 지정 명령(예: *run.sh* 와 같은 실행 파일)을 사용하여 앱을 시작할 수 있습니다. 예를 들어 `npm run start:prod`를 실행하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>npm start 실행

`npm start`를 사용하여 앱을 시작하려면 `start` 스크립트가 *package.json* 파일에 있어야 합니다. 예를 들면 다음과 같습니다.

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

프로젝트에서 사용자 지정 *package.json* 을 사용하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```


## <a name="debug-remotely"></a>원격 디버깅

> [!NOTE]
> 원격 디버깅은 현재 미리 보기 상태입니다.

[PM2를 사용하여 실행](#run-with-pm2)하도록 구성하는 경우 *.config.js, *.yml 또는 *.yaml* 을 사용하여 실행하는 경우를 제외하고 [Visual Studio Code](https://code.visualstudio.com/)에서 원격으로 Node.js 앱을 디버그할 수 있습니다.

대부분의 경우 앱에 대한 추가 구성이 필요하지 않습니다. 앱이 *process.json* 파일(기본 또는 사용자 지정)을 사용하여 실행되는 경우 JSON 루트에 `script` 속성이 있어야 합니다. 예를 들면 다음과 같습니다.

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Visual Studio Code에서 원격 디버깅을 설정하려면 [App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)을 설치합니다. 확장 페이지의 지침에 따라 Visual Studio Code에서 Azure에 로그인합니다.

Azure 탐색기에서 디버그할 앱을 찾아 마우스 오른쪽 단추로 클릭하고 **원격 디버깅 시작** 을 선택합니다. **예** 를 클릭하여 앱에서 사용하도록 설정합니다. App Service가 터널 프록시를 시작하고 디버거를 연결합니다. 그런 다음 앱에 대한 요청을 수행하면 디버거가 중단점에서 일시 중지되는 것을 볼 수 있습니다.

디버깅이 완료되면 **연결 끊기** 를 선택하여 디버거를 중지합니다. 메시지가 표시되면 **예** 를 클릭하여 원격 디버깅을 사용하지 않도록 설정해야 합니다. 나중에 사용하지 않도록 설정하려면 Azure 탐색기에서 앱을 다시 마우스 오른쪽 단추로 클릭하고 **원격 디버깅 사용 안 함** 을 선택합니다.

::: zone-end

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](configure-common.md)을 지정할 수 있습니다. 그런 다음 표준 Node.js 패턴을 사용하여 액세스할 수 있습니다. 예를 들어 앱 설정 `NODE_ENV`에 액세스하려면 다음 코드를 사용합니다.

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp 실행

기본적으로 App Service 빌드 자동화는 Node.js 앱이 Git을 통해 또는 [빌드 자동화가 활성화된](deploy-zip.md#enable-build-automation-for-zip-deploy) Zip 배포를 통해 배포되었음을 인식할 때 `npm install --production`을 실행합니다. 앱이 Grunt, Bower 또는 Gulp와 같은 인기 있는 자동화 도구를 필요로 하는 경우 이를 실행하려면 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 제공하여 실행해야 합니다.

리포지토리가 해당 도구를 실행할 수 있게 하려면 *package.json* 의 종속성에 도구를 추가해야 합니다. 예를 들면 다음과 같습니다.

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

로컬 터미널 창에서 디렉터리를 리포지토리 루트로 변경하고 다음 명령을 실행합니다.

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

이제 리포지토리 루트에 *.deployment* 및 *deploy.sh* 라는 두 개의 추가 파일이 있습니다.

*deploy.sh* 를 열고 다음과 같은 `Deployment` 섹션을 찾습니다.

```bash
##################################################################################################################################
# Deployment
# ----------
```

이 섹션은 `npm install --production` 실행으로 끝납니다. 필요한 도구를 실행하는 데 필요한 코드 섹션을 `Deployment` 섹션의 ‘끝’에 추가합니다.

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

배포 스크립트가 사용자 지정 `npm install` 명령도 실행하는 [MEAN.js 샘플의 예제](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)를 참조하세요.

### <a name="bower"></a>Bower

이 코드 조각은 `bower install`을 실행합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

이 코드 조각은 `gulp imagemin`을 실행합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

이 코드 조각은 `grunt`를 실행합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서는 네트워크 부하 분산기에서 [TLS/SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)가 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [Express](https://expressjs.com/)에서는 [신뢰 프록시](https://expressjs.com/guide/behind-proxies.html)를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Application Insights로 모니터링

Application Insights를 사용하면 코드를 변경하지 않고도 애플리케이션의 성능, 예외 및 사용 현황을 모니터링할 수 있습니다. App Insights 에이전트를 연결하려면 포털에서 웹앱으로 이동하고 **설정** 아래에서 **Application Insights** 를 선택한 다음 **Application Insights 켜기** 를 선택합니다. 다음으로, 기존 App Insights 리소스를 선택하거나 새 리소스를 만듭니다. 마지막으로 아래쪽에서 **적용** 을 선택합니다. PowerShell을 사용하여 웹앱을 계측하려면 [다음 지침](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell)을 참조하세요.

이 에이전트는 서버 쪽 Node.js 애플리케이션을 모니터링합니다. 클라이언트 쪽 JavaScript를 모니터링하려면 [JavaScript SDK를 프로젝트에 추가](../azure-monitor/app/javascript.md)합니다. 

자세한 내용은 [Application Insights 확장 릴리스 정보](../azure-monitor/app/web-app-extension-release-notes.md)를 참조하세요.

::: zone-end

## <a name="troubleshooting"></a>문제 해결

작동하는 Node.js 앱이 App Servce에서 다르게 동작하거나 오류가 발생할 경우 다음의 방법을 시도해보세요.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬 상태로 앱을 테스트합니다. App Service가 프로덕션 모드에서 Node.js 앱을 실행하므로 프로젝트가 프로덕션 모드에서 로컬 상태로 예상과 같이 작동하는지 확인해야 합니다. 예를 들면 다음과 같습니다.
    - *package.json* 에 따라, 프로덕션 모드에서 다른 패키지가 설치될 수 있습니다(`dependencies` vs. `devDependencies`).
    - 특정 웹 프레임워크는 프로덕션 모드에서 다른 고정 파일을 배포할 수 있습니다.
    - 특정 웹 프레임워크는 프로덕션 모드에서 실행될 때 사용자 지정 시작 스크립트를 사용합니다.
- App Service에서 앱을 개발 모드로 실행합니다. 예를 들어 [MEAN.js](https://meanjs.org/)에서는 [`NODE_ENV` 앱 설정을 설정](configure-common.md)하여 런타임 시 앱을 개발 모드로 설정할 수 있습니다.

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MongoDB를 사용하는 Node.js 앱](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux FAQ](faq-app-service-linux.yml)

::: zone-end

또는 다음 추가 리소스를 참조하세요.

[환경 변수 및 앱 설정 참조](reference-app-settings.md)