---
title: App Service에 파일 배포
description: 다양한 앱 패키지 또는 불연속 라이브러리, 정적 파일 또는 시작 스크립트를 Azure App Service에 배포하는 법 확인
ms.topic: article
ms.date: 08/13/2021
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: be58de78ba82c3c5e99475b00649e2a7ebfdf5e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216942"
---
# <a name="deploy-files-to-app-service"></a>App Service에 파일 배포

이 문서에서는 코드를 ZIP, WAR, JAR 또는 EAR 패키지로서 [Azure App Service](overview.md)에 배포하는 방법을 보여 줍니다. 애플리케이션 패키지와 별도로 개별 파일을 App Service에 배포하는 방법도 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 [App Service 앱을 만들거나](./index.yml)다른 자습서에 대해 만든 앱을 사용합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="deploy-a-zip-package"></a>ZIP 패키지 배포

ZIP 패키지를 배포할 때 App Service는 콘텐츠를 앱의 기본 경로(Windows에서는 `D:\home\site\wwwroot`, Linux에서는 `/home/site/wwwroot`)에 풉니다.

이 ZIP 패키지는 지속적인 통합 기반 배포를 지원하는 동일한 Kudu 서비스를 사용합니다. Kudu는 ZIP 패키지 배포에 대해 다음 기능을 지원합니다. 

- 이전 배포의 남은 파일 삭제.
- 패키지 복원을 포함하는 기본 빌드 프로세스를 켜는 옵션.
- 배포 스크립트 실행을 포함한 배포 사용자 지정.  
- 배포 로그. 
- 패키지 크기 제한은 2048MB입니다.

자세한 내용은 [Kudu 설명서](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)를 참조하세요.

> [!NOTE]
> ZIP 패키지에 있는 파일은 타임스탬프가 이미 배포된 파일의 타임스탬프와 일치하지 않는 경우에만 복사됩니다. 출력을 캐시하는 빌드 프로세스를 사용하여 zip을 생성하면 배포 속도가 빨라질 수 있습니다. 자세한 내용은 [zip 파일이나 url에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deploy](/cli/azure/webapp#az_webapp_deploy) 명령을 사용하여 ZIP 패키지를 웹앱에 배포합니다. CLI 명령은 [Kudu 게시 API](#kudu-publish-api-reference)를 사용하여 파일을 배포하며, 완전히 사용자 지정할 수 있습니다.

다음 예제에서는 ZIP 패키지를 사용자의 사이트에 푸시합니다. `--src-path`의 로컬 ZIP 패키지에 대한 경로를 지정합니다.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path <zip-package-path>
```

이 명령은 ZIP 패키지를 배포한 후 앱을 다시 시작합니다. 

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

다음 예제에서는 `--src-url` 매개 변수를 사용하여 사이트가 ZIP를 끌어올 Azure Storage 계정의 URL을 지정합니다.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

다음 예제에서는 [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp)을 사용하여 ZIP 패키지를 업로드합니다. `<group-name>`, `<app-name>` 및 `<zip-package-path>` 자리 표시자를 바꿉니다.

```powershell
Publish-AzWebApp -ResourceGroupName Default-Web-WestUS -Name MyApp -ArchivePath <zip-package-path> 
```

# <a name="kudu-api"></a>[Kudu API](#tab/api)

다음 예제에서는 cURL 도구를 사용하여 ZIP 패키지를 배포합니다. `<username>`, `<zip-package-path>` 및 `<app-name>` 자리 표시자를 바꿉니다. cURL에서 프롬프트가 표시되면 [배포 암호](deploy-configure-credentials.md)를 입력합니다.

```bash
curl -X POST -u <username> --data-binary @"<zip-package-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=zip
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

다음 예제에서는 `packageUri` 매개 변수를 사용하여 웹앱이 ZIP를 끌어올 Azure Storage 계정의 URL을 지정합니다.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

브라우저에서 `https://<app_name>.scm.azurewebsites.net/ZipDeployUI`으로 이동합니다.

웹 페이지의 파일 탐색기 영역으로 끌어서 [프로젝트 ZIP 패키지 만들기](#create-a-project-zip-package)에서 만든 ZIP 패키지를 업로드합니다.

배포가 진행 중일 때 오른쪽 위 모서리의 아이콘에서는 진행률을 백분율로 보여줍니다. 페이지도 탐색기 영역 아래에서 작업에 대한 자세한 정보 메시지를 표시합니다. 작업이 완료되면 `Deployment successful`이라는 마지막 배포 메시지가 표시됩니다.

지금은 위의 엔드포인트가 Linux App Services에 대해 작동하지 않습니다. 대신 FTP 또는 [ZIP 배포 API](./faq-app-service-linux.yml)를 사용하는 것이 좋습니다.

-----

## <a name="enable-build-automation-for-zip-deploy"></a>ZIP 배포에 빌드 자동화 사용

기본적으로 배포 엔진은 ZIP 패키지를 있는 그대로 실행할 준비가 된 것으로 가정하고 빌드 자동화를 실행하지 않습니다. [Git 배포](deploy-local-git.md)와 동일한 빌드 자동화를 사용하도록 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 `SCM_DO_BUILD_DURING_DEPLOYMENT`앱 설정을 설정합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

자세한 내용은 [Kudu 설명서](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)를 참조하세요.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="deploy-warjarear-packages"></a>WAR/JAR/EAR 패키지 배포

[WAR](https://wikipedia.org/wiki/WAR_(file_format)), [JAR](https://wikipedia.org/wiki/JAR_(file_format)) 또는 [EAR](https://wikipedia.org/wiki/EAR_(file_format)) 패키지를 App Service에 배포하여 Azure CLI, PowerShell 또는 Kudu 게시 API를 사용해 Java 웹앱을 실행할 수 있습니다.

배포 프로세스는 패키지를 공유 파일 드라이브에 올바르게 배치합니다([Kudu 게시 API 참조](#kudu-publish-api-reference)를 참조하세요). 따라서 [FTP](deploy-ftp.md) 또는 WebDeploy를 사용하여 WAR/JAR/EAR 패키지를 배포하는 것은 좋지 않습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deploy](/cli/azure/webapp#az_webapp_deploy) 명령을 사용하여 Tomcat 또는 JBoss EAP에 WAR 패키지를 배포합니다. `--src-path`의 로컬 Java 패키지에 대한 경로를 지정합니다.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path ./<package-name>.war
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

다음 예제에서는 `--src-url` 매개 변수를 사용하여 웹앱이 ZIP를 끌어올 Azure Storage 계정의 URL을 지정합니다.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

CLI 명령은 [Kudu 게시 API](#kudu-publish-api-reference)를 사용하여 패키지를 배포하며, 완전히 사용자 지정할 수 있습니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

다음 예제에서는 [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp)을 사용하여 .war 파일을 업로드합니다. 자리표시자 `<group-name>`, `<app-name>` 및 `<package-path>`를 교체합니다(Azure PowerShell에서는 WAR 및 JAR 파일만 지원됩니다).

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <package-path>
```

# <a name="kudu-api"></a>[Kudu API](#tab/api)

다음 예제에서는 cURL 도구를 사용하여 .war, .jar 또는 .ear 파일을 배포합니다. 자리표시자 `<username>`, `<file-path>`, `<app-name>` 및 `<package-type>`을 교체합니다(상황에 맞게 `war`, `jar` 또는 `ear`로). cURL에서 프롬프트가 표시되면 [배포 암호](deploy-configure-credentials.md)를 입력합니다.

```bash
curl -X POST -u <username> --data-binary @"<file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=<package-type>
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

다음 예제에서는 `packageUri` 매개 변수를 사용하여 웹앱이 WAR을 끌어올 Azure Storage 계정의 URL을 지정합니다. WAR 파일은 JAR 또는 EAR 파일일 수도 있습니다.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

자세한 내용은 [Kudu 게시 API 참조](#kudu-publish-api-reference)를 참조하세요.

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

Kudu UI는 JAR, WAR 또는 EAR 애플리케이션 배포를 지원하지 않습니다. 다른 옵션을 사용하세요.

-----

## <a name="deploy-individual-files"></a>개별 파일 배포

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deploy](/cli/azure/webapp#az_webapp_deploy) 명령과 `--type` 매개 변수를 사용하여 시작 스크립트, 라이브러리, 정적 파일을 웹앱에 배포합니다.

이러한 방식으로 시작 스크립트를 배포하는 경우 App Service에서 자동으로 스크립트를 사용하여 앱을 시작합니다.

CLI 명령은 [Kudu 게시 API](#kudu-publish-api-reference)를 사용하여 파일을 배포하며, 완전히 사용자 지정할 수 있습니다.

### <a name="deploy-a-startup-script"></a>시작 스크립트 배포

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path scripts/startup.sh --type=startup
```

### <a name="deploy-a-library-file"></a>라이브러리 파일 배포

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path driver.jar --type=lib
```

### <a name="deploy-a-static-file"></a>정적 파일 배포

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path config.json --type=static
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

지원 안 됨 Azure CLI 또는 Kudu API를 참조하세요.

# <a name="kudu-api"></a>[Kudu API](#tab/api)

### <a name="deploy-a-startup-script"></a>시작 스크립트 배포

다음 예제에서는 cURL 도구를 사용하여 애플리케이션의 시작 파일을 배포합니다. 자리표시자 `<username>`, `<startup-file-path>` 및 `<app-name>`을 교체하세요. cURL에서 프롬프트가 표시되면 [배포 암호](deploy-configure-credentials.md)를 입력합니다.

```bash
curl -X POST -u <username> --data-binary @"<startup-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=startup
```

### <a name="deploy-a-library-file"></a>라이브러리 파일 배포

다음 예제에서는 cURL 도구를 사용하여 애플리케이션의 라이브러리 파일을 배포합니다. `<username>`, `<lib-file-path>` 및 `<app-name>` 자리 표시자를 바꿉니다. cURL에서 프롬프트가 표시되면 [배포 암호](deploy-configure-credentials.md)를 입력합니다.

```bash
curl -X POST -u <username> --data-binary @"<lib-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=lib&path="/home/site/deployments/tools/my-lib.jar"
```

### <a name="deploy-a-static-file"></a>정적 파일 배포

다음 예제에서는 cURL 도구를 사용하여 애플리케이션의 구성 파일을 배포합니다. `<username>`, `<config-file-path>` 및 `<app-name>` 자리 표시자를 바꿉니다. cURL에서 프롬프트가 표시되면 [배포 암호](deploy-configure-credentials.md)를 입력합니다.

```bash
curl -X POST -u <username> --data-binary @"<config-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=static&path="/home/site/deployments/tools/my-config.json"
```

# <a name="kudu-ui"></a>[Kudu UI](#tab/kudu-ui)

Kudu UI에서는 개별 파일 배포를 지원하지 않습니다. Azure CLI 또는 Kudu REST API를 사용하세요.

-----

## <a name="kudu-publish-api-reference"></a>Kudu 게시 API 참조

`publish` Kudu API를 사용하면 CLI 명령의 동일한 매개 변수를 URL 쿼리 매개 변수로 지정할 수 있습니다. Kudu API를 사용하여 인증하려면 앱의 [배포 자격 증명](deploy-configure-credentials.md#userscope)을 바탕으로 기본 인증을 사용할 수 있습니다.

아래 표에는 사용 가능한 쿼리 매개 변수, 매개 변수의 허용되는 값 및 설명이 표시됩니다.

| 키 | 허용되는 값 | Description | 필수 | Type  |
|-|-|-|-|-|
| `type` | `war`\|`jar`\|`ear`\|`lib`\|`startup`\|`static`\|`zip` | 배포되는 아티팩트의 형식입니다. 기본 대상 경로를 설정하고, 배포를 처리하는 방법을 웹앱에 알리는 역할을 합니다. <br/> - `type=zip`: 콘텐츠를 `/home/site/wwwroot`에 압축 해제하여 ZIP 패키지를 배포합니다. `path` 매개 변수는 선택 사항입니다. <br/> - `type=war`: WAR 패키지를 배포합니다. 기본적으로 WAR 패키지는 `/home/site/wwwroot/app.war`에 배포됩니다. 대상 경로는 `path`를 이용해 지정할 수 있습니다. <br/> - `type=jar`: JAR 패키지를 `/home/site/wwwroot/app.jar`에 배포합니다. `path` 매개 변수는 무시됩니다. <br/> - `type=ear`: EAR 패키지를 `/home/site/wwwroot/app.ear`에 배포합니다. `path` 매개 변수는 무시됩니다. <br/> - `type=lib`: JAR 라이브러리 파일을 배포합니다. 기본적으로 파일은 `/home/site/libs`에 배포됩니다. 대상 경로는 `path`를 이용해 지정할 수 있습니다. <br/> - `type=static`: 정적 파일(예: 스크립트)을 배포합니다. 기본적으로 파일은 `/home/site/scripts`에 배포됩니다. 대상 경로는 `path`를 이용해 지정할 수 있습니다. <br/> - `type=startup`: App Service에서 자동으로 사용자 앱의 시작 스크립트로 사용할 스크립트를 배포합니다. 기본적으로 스크립트는 Windows에서는 `D:\home\site\scripts\<name-of-source>`에, Linux에서는 `home/site/wwwroot/startup.sh`에 배포됩니다. 대상 경로는 `path`를 이용해 지정할 수 있습니다. | 예 | String |
| `restart` | `true`\|`false` | 기본적으로 API는 배포 작업이 끝나면 앱을 다시 시작합니다(`restart=true`). 여러 아티팩트를 배포하려면 모든 다시 시작을 금지하고 마지막 배포에만 `restart=false`를 설정합니다. | 예 | 부울 |
| `clean` | `true`\|`false` | 아티팩트를 배포하기 전에 대상 배포 정리(삭제) 여부를 지정합니다. | 예 | 부울 |
| `ignorestack` | `true`\|`false` | 게시 API는 `WEBSITE_STACK` 환경 변수를 사용하여 사이트의 언어 스택을 바탕으로 안전한 기본값을 선택합니다. 이 매개 변수를 `false`로 설정하면 언어별 기본값이 비활성화됩니다. | 예 | 부울 |
| `path` | `"<absolute-path>"` | 아티팩트를 배포할 절대 경로입니다. 예: `"/home/site/deployments/tools/driver.jar"`, `"/home/site/scripts/helper.sh"` | 예 | String |

## <a name="next-steps"></a>다음 단계

고급 배포 시나리오에 대해서는 [Git를 사용하여 Azure에 배포](deploy-local-git.md)를 시도하세요. Azure로의 Git 기반 배포를 수행하면 버전 제어, 패키지 복원, MSBuild 등을 수행할 수 있습니다.

## <a name="more-resources"></a>추가 리소스

* [Kudu: zip 파일에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service 배포 자격 증명](deploy-ftp.md)
* [환경 변수 및 앱 설정 참조](reference-app-settings.md)
