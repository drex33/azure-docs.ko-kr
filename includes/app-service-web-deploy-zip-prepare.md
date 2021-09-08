---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 54a29bb1cc92347d9a2578a4f0ec9febb22553ac
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225478"
---
## <a name="create-a-project-zip-package"></a>프로젝트 ZIP 패키지 만들기

>[!NOTE]
> 파일을 ZIP 패키지로 다운로드한 경우 먼저 파일을 추출합니다. 예를 들어 GitHub에서 ZIP 패키지를 다운로드한 경우 해당 파일을 그대로 배포할 수 없습니다. GitHub에서는 App Service에서 작동하지 않는 추가적인 중첩된 디렉터리를 추가합니다. 
>

로컬 터미널 창에서 앱 프로젝트의 루트 디렉터리로 이동합니다. 

이 디렉터리는 _index.html_, _index.php_ 및 _app.js_ 와 같은 웹앱에 대한 입력 파일을 포함해야 합니다. 또한 _project.json_, _composer.json_, _package.json_, _bower.json_ 및 _requirements.txt_ 와 같은 패키지 관리 파일을 포함할 수 있습니다.

App Service에서 배포 자동화를 실행하려는 경우가 아니라면 모든 빌드 작업(예: `npm`, `bower`, `gulp`, `composer` 및 `pip`)을 실행하고 앱을 실행하는 데 필요한 모든 파일이 있는지 확인합니다. 이 단계는 [패키지를 직접 실행](../articles/app-service/deploy-run-package.md)하려는 경우에 필요합니다.

프로젝트의 모든 것에 대한 ZIP 아카이브를 만듭니다. `dotnet` 프로젝트의 경우 이 폴더는 `dotnet publish` 명령의 출력 폴더입니다. 다음 명령은 터미널의 기본 도구를 사용합니다.

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

