---
title: '빠른 시작: Azure Arc에서 웹 앱 만들기'
description: Azure Arc의 App Service를 사용하여 첫 번째 웹 앱을 배포하세요.
ms.topic: quickstart
ms.date: 11/02/2021
ms.openlocfilehash: 356e1b5f13d2a4b7e00c3e1c11b5dcbf295d6fd3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455869"
---
# <a name="create-an-app-service-app-on-azure-arc-preview"></a>Azure Arc의 App Service 앱 만들기(미리 보기)

이 빠른 시작에서는 [Azure Arc 지원 Kubernetes 클러스터에 대한 App Service 앱](overview-arc-integration.md)(미리 보기)을 만듭니다. 이 시나리오는 Linux 앱만 지원하며 기본 제공 언어 스택이나 사용자 지정 컨테이너를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [App Service를 실행하도록 Azure Arc 지원 Kubernetes를 설정](manage-create-arc-environment.md)합니다.

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="1-create-a-resource-group"></a>1. 리소스 그룹 만들기

다음 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="2-get-the-custom-location"></a>2. 사용자 지정 위치 가져오기

[!INCLUDE [app-service-arc-get-custom-location](../../includes/app-service-arc-get-custom-location.md)]


## <a name="3-create-an-app"></a>3. 앱 만들기

다음 예제에서는 Node.js 앱을 만듭니다. `<app-name>`을 클러스터에 하나밖에 없는 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9`, `-`입니다). 지원되는 모든 런타임을 보려면 [`az webapp list-runtimes --linux`](/cli/azure/webapp)를 실행합니다.

```azurecli-interactive
 az webapp create \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --runtime 'NODE|12-lts'
```

## <a name="4-deploy-some-code"></a>4. 일부 코드 배포

> [!NOTE]
> `az webapp up`은 공개 미리 보기에서는 지원되지 않습니다.

Git를 사용하여 샘플 Node.js 앱을 가져오고 [ZIP 배포](deploy-zip.md)를 사용하여 배포합니다. `<app-name>`을 웹 앱 이름으로 바꿉니다.

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
cd nodejs-docs-hello-world
zip -r package.zip .
az webapp deployment source config-zip --resource-group myResourceGroup --name <app-name> --src package.zip
```

## <a name="5-get-diagnostic-logs-using-log-analytics"></a>5. Log Analytics를 사용하여 진단 로그 가져오기

> [!NOTE]
> Log Analytics를 사용하려면 [App Service 확장을 설치](manage-create-arc-environment.md#install-the-app-service-extension)할 때 Log Analytics를 사용하도록 설정했어야 합니다. Log Analytics 없이 확장을 설치했다면 이 단계를 건너뜁니다.

[App Service 확장을 이용해 구성한 Log Analytics 작업 영역](manage-create-arc-environment.md#install-the-app-service-extension)으로 이동한 다음 왼쪽 탐색 메뉴에서 Logs를 클릭합니다. 다음 샘플 쿼리를 실행하여 지난 72시간 동안의 로그를 표시합니다. `<app-name>`을 웹 앱 이름으로 바꿉니다. 쿼리를 실행할 때 오류가 발생하면 10~15분 후에 다시 시도합니다(Log Analytics가 애플리케이션에서 로그 수신을 시작하는 데 지연이 있을 수 있음). 

```kusto
let StartTime = ago(72h);
let EndTime = now();
AppServiceConsoleLogs_CL
| where TimeGenerated between (StartTime .. EndTime)
| where AppName_s =~ "<app-name>"
```

Kubernetes 클러스터에서 호스트되는 모든 앱에 대한 애플리케이션 로그가 `AppServiceConsoleLogs_CL`이라는 사용자 지정 로그 테이블에 있는 Log Analytics 작업 영역에 로깅됩니다. 

**Log_s** 에는 지정된 App Service의 애플리케이션 로그가 포함되며 **AppName_s** 에는 App Service 앱 이름이 포함됩니다. Log_s 열에는 애플리케이션 코드를 통해 작성한 로그 외에 컨테이너 시작, 종료 및 Function Apps에 대한 로그도 포함됩니다.

[Kusto 시작하기](../azure-monitor/logs/get-started-queries.md)에서 로그 쿼리에 대해 자세히 알아볼 수 있습니다.

## <a name="optional-deploy-a-custom-container"></a>(선택 사항) 사용자 지정 컨테이너 배포

사용자 지정 컨테이너 앱을 만들려면 `--deployment-container-image-name`을 이용해 [az webapp create](/cli/azure/webapp#az_webapp_create)를 실행합니다. 프라이빗 리포지토리의 경우 `--docker-registry-server-user`와 `--docker-registry-server-password`를 추가합니다.

예를 들어 다음을 시도해 보세요.

```azurecli-interactive
az webapp create \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --deployment-container-image-name mcr.microsoft.com/appsvc/node:12-lts
```

<!-- `TODO: currently gets an error but the app is successfully created: "Error occurred in request., RetryError: HTTPSConnectionPool(host='management.azure.com', port=443): Max retries exceeded with url: /subscriptions/62f3ac8c-ca8d-407b-abd8-04c5496b2221/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/cephalin-arctest4/config/appsettings?api-version=2020-12-01 (Caused by ResponseError('too many 500 error responses',))"` -->

앱을 만든 후 이미지를 업데이트하고 싶다면 [사용자 지정 컨테이너의 Docker 이미지 변경](configure-custom-container.md?pivots=container-linux#change-the-docker-image-of-a-custom-container)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [ASP.NET Core 앱 구성](configure-language-dotnetcore.md?pivots=platform-linux)
- [Node.js 앱 구성](configure-language-nodejs.md?pivots=platform-linux)
- [PHP 앱 구성](configure-language-php.md?pivots=platform-linux)
- [Linux Python 앱 구성](configure-language-python.md)
- [Java 앱 구성](configure-language-java.md?pivots=platform-linux)
- [Linux Ruby 앱 구성](configure-language-ruby.md)
- [사용자 지정 컨테이너 구성](configure-custom-container.md?pivots=container-linux)
