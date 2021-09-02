---
title: 포함 파일
description: 포함 파일
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d26b8d131d5a8ff54b12fc5817718ddd381b720b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "122643261"
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
>- 현재 작업 디렉터리에서 앱까지의 [배포 파일을 압축합니다](../articles/app-service/deploy-zip.md).
>