---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/29/2021
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 44b076a132bce92c6a1b063cc7eaa70d44bb6c62
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658440"
---
`myAppServicePlan` App Service 계획에서 [웹앱](../articles/app-service/overview.md#app-service-on-linux)을 만듭니다. 

Cloud Shell에서 [`az webapp create`](/cli/azure/webapp) 명령을 사용할 수 있습니다. 다음 예에서 `<app-name>`을 전역적으로 고유한 앱 이름으로 바꿉니다(유효한 문자는 `a-z`, `0-9` 및 `-`). 런타임은 `DOTNET|5.0`으로 설정됩니다. 지원되는 모든 런타임을 보려면 [`az webapp list-runtimes --linux`](/cli/azure/webapp)를 실행합니다. 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'DOTNET|5.0' --deployment-local-git
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

git 배포가 활성화된 Linux 컨테이너에 빈 웹앱을 만들었습니다.

> [!NOTE]
> Git 원격의 URL은 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 형식으로 `deploymentLocalGitUrl` 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.
>
