---
title: GitHub Actions를 사용하여 Bicep 파일 배포
description: 이 빠른 시작에서는 GitHub 작업을 사용 하 여 Bicep 파일을 배포 하는 방법에 대해 알아봅니다.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/16/2021
ms.custom: github-actions-azure
ms.openlocfilehash: 548688e2359043485df7b60eb51cdaea243c3540
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716871"
---
# <a name="quickstart-deploy-bicep-files-by-using-github-actions"></a>빠른 시작: GitHub 작업을 사용 하 여 Bicep 파일 배포

[GitHub 작업](https://docs.github.com/en/actions) 은 소프트웨어 개발 워크플로를 자동화 하는 GitHub의 기능 모음입니다.

이 빠른 시작에서는 [Azure Resource Manager 배포에 대 한 GitHub 작업](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) 을 사용 하 여 Bicep 파일을 Azure에 배포 하는 작업을 자동화 합니다.

GitHub 작업 및 Bicep 파일에 대 한 간략 한 소개를 제공 합니다. GitHub 작업 및 프로젝트를 설정 하는 방법에 대 한 자세한 단계는 [Learning 경로: Bicep 및 GitHub 작업을 사용 하 여 Azure 리소스 배포](/learn/paths/bicep-github-actions)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.
- Bicep 파일과 워크플로 파일을 저장하는 GitHub 리포지토리입니다. 리포지토리를 만들려면 [새 리포지토리 만들기](https://docs.github.com/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)를 참조하세요.

## <a name="create-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만듭니다. 이 빠른 시작의 뒷부분에서이 리소스 그룹에 Bicep 파일을 배포 합니다.

```azurecli-interactive
az group create -n exampleRG -l westus
```

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

GitHub 작업은 id로 실행 됩니다. [Az ad sp create-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용 하 여 id에 대 한 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 만듭니다.

`myApp` 자리 표시자를 애플리케이션 이름으로 바꿉니다. `{subscription-id}`는 구독 ID로 바꿉니다.

```azurecli-interactive
az ad sp create-for-rbac --name myApp --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/exampleRG --sdk-auth
```

> [!IMPORTANT]
> 이전 예제의 범위가 리소스 그룹으로 제한됩니다. 필요한 최소한의 액세스 권한을 부여 하는 것이 좋습니다.

출력은 아래와 비슷한 App Service 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다. ,, 및 값이 포함 된 섹션만 `clientId` 필요 `clientSecret` `subscriptionId` `tenantId` 합니다.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secrets"></a>GitHub 비밀 구성

Azure 자격 증명, 리소스 그룹 및 구독에 대한 비밀을 만듭니다.

1. [GitHub](https://github.com/)에서 리포지토리로 이동 합니다.

1. **설정 > 비밀 > 새 비밀** 을 차례로 선택합니다.

1. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 `AZURE_CREDENTIALS`의 이름을 지정합니다.

1. `AZURE_RG`라는 이름으로 다른 비밀을 만듭니다. 리소스 그룹의 이름을 비밀의 값 필드 ()에 추가 `exampleRG` 합니다.

1. `AZURE_SUBSCRIPTION`라는 이름으로 다른 비밀을 만듭니다. 비밀의 값 필드에 구독 ID를 추가합니다(예: `90fd3f9d-4c61-432d-99ba-1273f236afa2`).

## <a name="add-a-bicep-file"></a>Bicep 파일 추가

GitHub 리포지토리에 Bicep 파일을 추가합니다. 다음 Bicep 파일은 저장소 계정을 만듭니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep" :::

Bicep 파일에는 3 ~ 11 자의 **Storageprefix** 라는 하나의 매개 변수가 필요 합니다.

파일을 리포지토리의 어디에나 배치할 수 있습니다. 다음 섹션의 workflow 샘플에서는 Bicep 파일의 이름이 **Bicep** 인 것으로 가정 합니다 .이 파일은 리포지토리의 루트에 저장 됩니다.

## <a name="create-workflow"></a>워크플로 만들기

워크플로는 트리거될 때 실행할 단계를 정의 합니다. 리포지토리의 **github/워크플로/** 경로에 있는 yaml (.yml) 파일입니다. 워크플로 파일 확장명은 **.yml** 또는 **.yaml** 일 수 있습니다.

워크플로를 만들려면 다음 단계를 수행 합니다.

1. GitHub 리포지토리의 상단 메뉴에서 **작업** 을 선택합니다.
1. **새 워크플로** 를 선택합니다.
1. **워크플로 직접 설정** 을 선택합니다.
1. **main.yml** 이 아닌 다른 이름을 선호하는 경우 워크플로 파일의 이름을 바꿉니다. 예: **deployBicepFile.yml**
1. Iisnode.yml 파일의 내용을 다음 코드로 바꿉니다.

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy Bicep file
        - name: deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./main.bicep
            parameters: storagePrefix=mystore
            failOnStdErr: false
    ```

    `mystore`를 사용자 고유의 저장소 계정 이름 접두사로 바꿉니다.

    > [!NOTE]
    > ARM 배포 작업에 대신 JSON 형식 매개 변수 파일을 지정할 수 있습니다(예: `.azuredeploy.parameters.json`).

    워크플로 파일의 첫 번째 섹션에는 다음이 포함됩니다.

    - **name**: 워크플로의 이름입니다.
    - **on**: 워크플로를 트리거하는 GitHub 이벤트의 이름입니다. Main 분기에 푸시 이벤트가 있으면 워크플로가 트리거됩니다.

1. **커밋 시작** 을 선택합니다.
1. **기본 분기에 직접 커밋** 을 선택합니다.
1. **새 파일 커밋**(또는 **변경 내용 커밋**)을 선택합니다.

워크플로 파일 또는 Bicep 파일을 업데이트 하면 워크플로가 트리거됩니다. 워크플로는 변경 내용을 커밋한 후 바로 시작 됩니다.

## <a name="check-workflow-status"></a>워크플로 상태 확인

1. **작업** 탭을 선택 합니다. **Create deployStorageAccount. yml** 워크플로가 나열 됩니다. 워크플로를 실행하는 데 1-2분이 소요됩니다.
1. 워크플로를 선택하여 엽니다.
1. 메뉴에서 **ARM 배포 실행** 을 선택하여 배포를 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹 및 리포지토리가 더 이상 필요하지 않은 경우 리소스 그룹과 GitHub 리포지토리를 삭제하여 배포한 리소스를 정리합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bicep 파일 구조 및 구문](file.md)
