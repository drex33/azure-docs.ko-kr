---
title: '빠른 시작: CLI를 사용하여 Azure Static Web Apps를 통해 첫 번째 정적 사이트 빌드'
description: Azure CLI를 사용하여 정적 사이트를 Azure Static Web Apps에 배포하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 11/17/2021
ms.author: cshoe
ms.custom: mode-api
ms.openlocfilehash: 6aa2563391f5752614f45ad754f6d42b3ce820d7
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045626"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 첫 번째 정적 사이트 빌드

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 빌드하여 프로덕션 환경에 웹 사이트를 게시합니다. 이 빠른 시작에서는 Azure CLI를 사용하여 웹 애플리케이션을 Azure Static 웹앱에 배포합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정
- [Azure CLI](/cli/azure/install-azure-cli) 설치(버전 2.29.0 이상)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

이제 리포지토리가 생성되었으므로 Azure CLI에서 정적 웹앱을 만들 수 있습니다.

1. 다음 명령을 사용하여 Azure CLI에 로그인합니다.

    ```azurecli
    az login
    ```

1. 리소스 그룹을 만듭니다.

    ```bash
    az group create \
      --name my-swa-group \
      --location "eastus2"
    ```

1. GitHub 사용자 이름을 저장할 변수를 만듭니다.

    ```bash
    GITHUB_USER_NAME=<YOUR_GITHUB_USER_NAME>
    ```

    자리 표시자 `<YOUR_GITHUB_USER_NAME>`을 GitHub 사용자 이름으로 바꿉니다.

1. 리포지토리에서 새 정적 웹앱을 만듭니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        --name my-first-static-web-app \
        --resource-group my-swa-group \
        --source https://github.com/$GITHUB_USER_NAME/my-first-static-web-app \
        --location "eastus2" \
        --branch main \
        --app-location "src" \
        --login-with-github
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        --name my-first-static-web-app \
        --resource-group my-swa-group \
        --source https://github.com/$GITHUB_USER_NAME/my-first-static-web-app \
        --location "eastus2" \
        --branch main \
        --app-location "/" \
        --output-location "dist/angular-basic" \
        --login-with-github
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        --name my-first-static-web-app \
        --resource-group my-swa-group \
        --source https://github.com/$GITHUB_USER_NAME/my-first-static-web-app \
        --location "eastus2" \
        --branch main \
        --app-location "/"  \
        --output-location "build"  \
        --login-with-github
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        --name my-first-static-web-app \
        --resource-group my-swa-group \
        --source https://github.com/$GITHUB_USER_NAME/my-first-static-web-app \
        --location "eastus2" \
        --branch main \
        --app-location "/" \
        --output-location "dist"  \
        --login-with-github
    ```

    ---

    > [!IMPORTANT]
    > `--source` 매개 변수에 전달된 URL에는 `.git` 접미사가 포함되어서는 안 됩니다.

    이 명령을 실행하면 CLI가 GitHub 대화형 로그인 환경을 시작합니다. 콘솔에서 다음 메시지와 유사한 줄을 찾습니다.

    > `https://github.com/login/device`로 이동해 사용자 코드 329B-3945를 입력하여 github 개인용 액세스 토큰을 활성화하고 검색하세요.

1. **https://github.com/login/device** 으로 이동합니다.

1. 콘솔의 메시지에 표시된 대로 사용자 코드를 입력합니다.

1. **계속** 단추를 선택합니다.

1. **AzureAppServiceCLI 권한 부여** 단추를 선택합니다.

## <a name="view-the-website"></a>웹 사이트 보기

정적 앱을 배포하는 데는 두 가지 측면이 있습니다. 첫 번째 작업은 앱을 구성하는 기본 Azure 리소스를 만듭니다. 두 번째는 애플리케이션을 빌드하고 게시하는 GitHub Actions 워크플로입니다.

새 정적 사이트로 이동하려면 먼저 배포 빌드가 실행을 완료해야 합니다.

1. 콘솔 창으로 돌아가서 다음 명령을 실행하여 앱과 연결된 URL을 나열합니다.

    ```bash
    az staticwebapp show \
      --name  my-first-static-web-app \
      --query "repositoryUrl"
    ```

    이 명령의 출력은 GitHub 리포지토리에 대한 URL을 반환합니다.

1. **리포지토리 URL** 을 복사하여 브라우저에 붙여넣습니다.

1. **작업** 탭을 선택합니다.

    이 시점에서 Azure는 정적 웹앱을 지원하기 위한 리소스를 만듭니다. 실행 중인 워크플로 옆의 아이콘이 녹색 배경(:::image type="icon" source="media/get-started-cli/checkmark-green-circle.png" border="false":::)의 확인 표시로 바뀔 때까지 기다립니다. 이 작업이 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    성공 아이콘이 표시되면 워크플로가 완료되고 콘솔 창으로 돌아갈 수 있습니다.

1. 다음 명령을 실행하여 웹 사이트의 URL을 쿼리합니다.

    ```bash
    az staticwebapp show \
      --name my-first-static-web-app \
      --query "defaultHostname"
    ```

    URL을 브라우저에 복사하고 웹 사이트로 이동합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 명령을 실행하여 리소스 그룹과 정적 웹앱을 삭제할 수 있습니다.

```azurecli
az group delete \
  --name my-swa-group
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
