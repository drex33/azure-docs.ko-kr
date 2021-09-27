---
title: GitHub 작업을 사용하여 Azure Storage에 정적 사이트 배포
description: GitHub Actions를 사용하여 호스팅하는 Azure Storage 정적 웹 사이트
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/17/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ec1eb55ae54a29d8bb5334993edeee7308dd2de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662536"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트를 배포하는 GitHub Actions 워크플로 설정

워크플로를 사용하여 정적 사이트를 Azure Storage 계정에 배포하여 [GitHub Actions](https://docs.github.com/en/actions)를 시작합니다. GitHub Actions 워크플로를 설정한 후에는 사이트 코드를 변경할 때 GitHub에서 Azure로 사이트를 자동으로 배포할 수 있습니다.

> [!NOTE]
> [Azure Static Web Apps](../../static-web-apps/index.yml)를 사용하는 경우 GitHub Actions 워크플로를 수동으로 설정할 필요가 없습니다.
> Azure Static Web Apps는 자동으로 GitHub Actions 워크플로를 만듭니다.

## <a name="prerequisites"></a>필수 요건

Azure 구독 및 GitHub 계정

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 정적 웹 사이트 코드가 포함된 GitHub 리포지토리. GitHub 계정이 없는 경우 [평가판에 가입](https://github.com/join)하세요.
- Azure Storage에서 호스팅되는 정적 웹 사이트. 자세한 내용은 [Azure Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)를 참조하세요. 이 예를 수행하려면 [Azure CDN](static-website-content-delivery-network.md)도 배포해야 합니다.

> [!NOTE]
> CDN(콘텐츠 전송 네트워크)을 사용하여 전 세계 사용자의 지연 시간을 줄이고 스토리지 계정에 대한 트랜잭션 수를 줄이는 것이 일반적입니다. 클라우드 기반 스토리지 서비스에 정적 콘텐츠를 배포하면 잠재적으로 비용이 많이 드는 컴퓨팅 인스턴스의 필요성이 줄어들 수 있습니다. 자세한 내용은 [정적 콘텐츠 호스팅 패턴](/azure/architecture/patterns/static-content-hosting)을 참조하세요.

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

자리 표시자 `myStaticSite`를 Azure Storage에서 호스팅되는 사이트 이름으로 바꿉니다.

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID 및 리소스 그룹 이름으로 바꿉니다. 출력은 아래와 비슷한 스토리지 계정에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한되어 있습니다.

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

1. [GitHub](https://github.com/)에서 리포지토리를 찾습니다.

1. **설정 > 비밀 > 새 비밀** 을 차례로 선택합니다.

1. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 지정합니다(예: `AZURE_CREDENTIALS`).

    나중에 워크플로 파일을 구성할 때 이 비밀을 Azure 로그인 작업의 `creds` 입력에 사용합니다. 예를 들면 다음과 같습니다.

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>워크플로 추가

1. GitHub 리포지토리에 대한 **작업** 으로 이동합니다.

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub Actions 메뉴 항목":::

1. **워크플로 직접 설정** 을 선택합니다.

1. 워크플로 파일의 `on:` 섹션 뒤에 있는 모든 항목을 삭제합니다. 예를 들어 나머지 워크플로는 다음과 같습니다.

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
    ```

1. 워크플로 이름을 `Blob storage website CI`로 바꾸고, 체크 아웃 및 로그인 작업을 추가합니다. 이러한 작업은 사이트 코드를 체크 아웃하고 이전에 만든 `AZURE_CREDENTIALS` GitHub 비밀을 사용하여 Azure에서 인증합니다.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Azure CLI 작업을 사용하여 Blob Storage에 코드를 업로드하고 CDN 엔드포인트를 제거합니다. `az storage blob upload-batch`의 경우 자리 표시자를 스토리지 계정 이름으로 바꿉니다. 스크립트는 `$web` 컨테이너에 업로드됩니다. `az cdn endpoint purge`의 경우 자리 표시자를 CDN 프로필 이름, CDN 엔드포인트 이름 및 리소스 그룹으로 바꿉니다. CDN 제거 속도를 높이려면 `--no-wait` 옵션을 `az cdn endpoint purge`에 추가할 수 있습니다. 보안을 강화하기 위해 스토리지 계정 키를 사용하여 옵션을 추가할 수도 `--account-key` 있습니다. [](../common/storage-account-keys-manage.md)

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME>  --auth-mode key -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ```

1. Azure에서 로그아웃하는 작업을 추가하여 워크플로를 완성합니다. 완성된 워크플로는 다음과 같습니다. 파일이 리포지토리의 `.github/workflows` 폴더에 표시됩니다.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> --auth-mode key -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"

      # Azure logout
        - name: logout
          run: |
                az logout
          if: always()
    ```

## <a name="review-your-deployment"></a>배포 검토

1. GitHub 리포지토리에 대한 **작업** 으로 이동합니다.

1. 첫 번째 결과를 열어 워크플로 실행에 대한 자세한 로그를 확인합니다.

    :::image type="content" source="../media/index/github-actions-run.png" alt-text="GitHub 작업 실행 로그":::

## <a name="clean-up-resources"></a>리소스 정리

고정적인 웹 사이트 및 GitHub 리포지토리가 더 이상 필요하지 않은 경우 리소스 그룹과 GitHub 리포지토리를 삭제하여 배포한 리소스를 정리합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Static Web Apps에 대해 알아보기](../../static-web-apps/index.yml)
