---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: ceb52a4e3e2b66388b73bcb63b68913d8dcb467a
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830540"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage 연결 문자열 검색

이전에는 함수 앱에서 사용할 Azure Storage 계정을 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 설정을 *local.settings.json* 파일에 다운로드하면 함수를 로컬로 실행할 때 동일한 계정의 Storage 큐에 해당 연결 쓰기를 사용할 수 있습니다. 

1. 프로젝트의 루트에서 다음 명령을 실행합니다. 여기서 `<APP_NAME>`을 이전 빠른 시작의 함수 앱 이름으로 바꿉니다. 이 명령은 파일의 기존 값을 덮어씁니다.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. *local.settings.json* 을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage`라는 값을 찾습니다. 이 문서의 다른 섹션에서 `AzureWebJobsStorage` 이름과 연결 문자열을 사용합니다.

> [!IMPORTANT]
> *local.settings.json* 에는 Azure에서 다운로드한 비밀이 포함되어 있으므로 항상 이 파일을 원본 제어에서 제외하세요. 로컬 함수 프로젝트를 사용하여 만든 *.gitignore* 파일은 기본적으로 해당 파일이 제외됩니다.