---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 44601c97a873138bcd152de2450fe6d98be814df
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122530846"
---
## <a name="prepare-your-repository"></a>리포지토리 준비

Azure App Service 빌드 서버에서 자동화된 빌드 기능을 가져오려면 리포지토리 루트에 프로젝트의 올바른 파일이 있는지 확인합니다.

| 런타임 | 루트 디렉터리 파일 |
|-|-|
| ASP.NET(Windows만 해당) | _*.sln_, _*.csproj_ 또는 _default.aspx_ |
| ASP.NET Core | _*.sln_ 또는 _*.csproj_ |
| PHP | _index.php_ |
| Ruby(Linux만 해당) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ 또는 _package.json_(시작 스크립트 포함) |
| Python | _\*.py_, _requirements.txt_ 또는 _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ 또는 _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ 연속 WebJobs의 경우 _App\_Data/jobs/continuous_ 또는 트리거된 WebJobs의 경우 _App\_Data/jobs/triggered_ 아래에 있습니다. 자세한 내용은 [Kudu WebJobs 설명서](https://github.com/projectkudu/kudu/wiki/WebJobs)를 참조하세요. |
| Functions | [Azure Functions에 대한 연속 배포](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment)를 참조하세요. |

배포를 사용자 지정하려면 리포지토리 루트에 *.deployment* 파일을 포함합니다. 자세한 내용은 [배포 사용자 지정](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) 및 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 참조하세요.

> [!NOTE]
> Visual Studio를 사용하는 경우 [Visual Studio에서 자동으로 리포지토리를 만들도록](/azure/devops/repos/git/creatingrepo?tabs=visual-studio) 합니다. 프로젝트는 Git을 통해 즉시 배포할 준비가 됩니다.
>

