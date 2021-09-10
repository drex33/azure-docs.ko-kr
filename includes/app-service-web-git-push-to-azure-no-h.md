---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d79d02054e21ba359637194d00fee9ac01a6d56
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078455"
---
1. `main` 분기를 배포하고 있으므로 App Service 앱의 기본 배포 분기를 `main`로 설정해야 합니다([배포 분기 변경](../articles/app-service/deploy-local-git.md#change-deployment-branch) 참조). Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령으로 `DEPLOYMENT_BRANCH` 앱 설정을 지정합니다. 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. 로컬 터미널 창으로 돌아와서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. *\<deploymentLocalGitUrl-from-create-step>* 를 [웹앱 만들기](#create-a-web-app)에 저장된 Git 원격의 URL로 바꿉니다.

    ```bash
    git remote add azure <deploymentLocalGitUrl-from-create-step>
    ```

1. 다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. Git Credential Manager에서 자격 증명을 묻는 메시지가 표시되면 Azure Portal에 로그인하는 데 사용하는 자격 증명이 아니라 **배포 사용자 구성** 에서 만든 자격 증명을 입력해야 합니다.

    ```bash
    git push azure main
    ```

    이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.
