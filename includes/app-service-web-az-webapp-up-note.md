---
title: 포함 파일
description: 포함 파일
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/14/2021
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: afaacb79906c07893d835d654b00815ea97199d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577332"
---
> [!NOTE]
> `az webapp up` 명령에는 다음 작업이 포함됩니다.
>
>- 기본 [리소스 그룹](/cli/azure/group#az_group_create)을 만듭니다.
>
>- 기본 [App Service 계획](/cli/azure/appservice/plan#az_appservice_plan_create)을 만듭니다.
>
>- 지정된 이름으로 [앱을 만듭니다](/cli/azure/webapp#az_webapp_create).
>
>- [Zip](../articles/app-service/deploy-zip.md#deploy-a-zip-package)은 [빌드 자동화가 활성화](../articles/app-service/deploy-zip.md#enable-build-automation-for-zip-deploy)된 현재 작업 디렉터리의 모든 파일을 배포합니다.
>
>- `az webapp up` 또는 다른 Azure CLI 명령을 사용하여 나중에 배포할 때 다시 지정할 필요가 없도록 *azure/config* 파일에서 매개 변수를 로컬로 캐시합니다. 캐시된 값은 기본적으로 자동으로 사용됩니다.
>