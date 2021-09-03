---
title: Azure Pipelines를 사용하여 설정을 App Configuration으로 밀어넣기
description: Azure Pipelines를 사용하여 키-값을 App Configuration 저장소로 밀어넣는 방법을 알아봅니다.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 61b50f8e3c5b32401b4f7339627b9b704a4b94fd
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122973"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines를 사용하여 설정을 App Configuration으로 밀어넣기

[Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 작업을 통해 키-값을 구성 파일에서 App Configuration 저장소로 밀어넣을 수 있습니다. 이 작업을 통해 App Configuration 저장소에서 설정을 끌어올 뿐 아니라 App Configuration 저장소로 설정을 밀어넣을 수도 있으므로 파이프라인 내에서 전체 원 기능을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- App Configuration 리소스 - [Azure Portal](https://portal.azure.com)에서 무료로 만듭니다.
- Azure DevOps 프로젝트 - [무료로 한 개 만들기](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration Push 작업 - [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)에서 무료로 다운로드합니다.

## <a name="create-a-service-connection"></a>서비스 연결 만들기

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>역할 할당 추가

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>빌드에 사용

이 섹션에서는 Azure DevOps 빌드 파이프라인에서 Azure App Configuration Push 작업을 사용하는 방법을 설명합니다.

1. **Pipelines** > **Pipelines** 를 클릭하여 빌드 파이프라인 페이지로 이동합니다. 빌드 파이프라인에 대한 설명서는 [여기](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)에서 확인할 수 있습니다.
      - 새 빌드 파이프라인을 만드는 경우 프로세스 마지막 단계의 **검토** 탭에 있는 파이프라인 오른쪽에서 **도우미 표시** 를 선택합니다.
      ![새 파이프라인을 위한 도우미 표시 단추를 보여 주는 스크린샷](./media/new-pipeline-show-assistant.png)
      - 기존 빌드 파이프라인을 사용하는 경우 오른쪽 위에서 **편집** 단추를 클릭합니다.
      ![기존 파이프라인에 대한 편집 단추를 보여 주는 스크린샷](./media/existing-pipeline-show-assistant.png)
1. **Azure App Configuration Push** 작업을 검색합니다.
![검색 상자에서 Azure App Configuration Push의 작업 추가 대화 상자를 보여 주는 스크린샷](./media/add-azure-app-configuration-push-task.png)
1. 작업에 필요한 매개 변수를 구성하여 구성 파일에서 App Configuration 저장소로 키-값을 밀어넣습니다. 매개 변수에 대한 설명은 아래 **매개 변수** 섹션과 각 매개 변수 옆에 있는 도구 설명에 있습니다.
![앱 구성 밀어넣기 작업 매개 변수를 보여 주는 스크린샷](./media/azure-app-configuration-push-parameters.png)
1. 빌드를 저장하고 큐에 넣습니다. 빌드 로그에는 작업 실행 중 발생한 모든 오류가 표시됩니다.

## <a name="use-in-releases"></a>릴리스에 사용

이 섹션에서는 Azure DevOps 릴리스 파이프라인에서 Azure App Configuration Push 작업을 사용하는 방법을 설명합니다.

1. **Pipelines** > **릴리스** 를 선택하여 릴리스 파이프라인 페이지로 이동합니다. 릴리스 파이프라인에 대한 설명서는 [여기](/azure/devops/pipelines/release)에서 확인할 수 있습니다.
1. 기존 릴리스 파이프라인을 선택합니다. 기존 릴리스 파이프라인이 없는 경우 **+ 새로 만들기** 를 선택하여 새 항목을 만듭니다.
1. 오른쪽 위 모서리에서 **편집** 단추를 선택하여 릴리스 파이프라인을 편집합니다.
1. **작업** 드롭다운에서 작업을 추가할 **스테이지** 를 선택합니다. 스테이지에 대한 자세한 내용은 [여기](/azure/devops/pipelines/release/environments)에서 확인할 수 있습니다.
![작업 드롭다운에서 선택된 스테이지를 보여 주는 스크린샷](./media/pipeline-stage-tasks.png)
1. 새 작업을 추가할 작업 옆의 **+** 를 클릭합니다.
![작업 옆의 더하기 단추를 보여 주는 스크린샷](./media/add-task-to-job.png)
1. **작업 추가** 대화 상자에서 검색 상자에 **Azure App Configuration Push** 를 입력하고 이를 선택합니다.
1. 작업 내에서 필요한 매개 변수를 구성하여 구성 파일에서 App Configuration 저장소로 키-값을 밀어넣습니다. 매개 변수에 대한 설명은 아래 **매개 변수** 섹션과 각 매개 변수 옆에 있는 도구 설명에 있습니다.
1. 릴리스를 저장하고 큐에 넣습니다. 릴리스 로그에 작업 실행 중 발생한 모든 오류가 표시됩니다.

## <a name="parameters"></a>매개 변수

다음 매개 변수는 App Configuration Push 작업에 사용됩니다.

- **Azure 구독**: 사용 가능한 Azure 서비스 연결이 포함된 드롭다운입니다. 사용 가능한 Azure 서비스 연결 목록을 업데이트하고 새로 고치려면 텍스트 상자 오른쪽에서 **Azure 구독 새로 고침** 단추를 누릅니다.
- **App Configuration 이름**: 선택한 구독 아래에서 사용 가능한 구성 저장소를 로드하는 드롭다운입니다. 사용 가능한 구성 저장소 목록을 업데이트하고 새로 고치려면 텍스트 상자 오른쪽에서 **App Configuration 이름 새로 고침** 단추를 누릅니다.
- **구성 파일 경로**: 구성 파일에 대한 경로입니다. **구성 파일 경로** 매개 변수는 파일 리포지토리의 루트에서 시작합니다. 구성 파일을 선택하려면 빌드 아티팩트를 통해 찾아볼 수 있습니다. 텍스트 상자 오른쪽에 있는 `...` 단추를 눌러 찾을 수 있습니다. 지원되는 파일 형식은 yaml, json, properties입니다. 다음은 json 형식의 구성 파일 예제입니다.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **구분 기호**: .json 및 .yml 파일을 평면화하는 데 사용되는 구분 기호입니다.
- **깊이**: .json 및 .yml 파일이 평면화될 깊이입니다.
- **접두사**: App Configuration 저장소에 밀어넣은 각 키의 시작 부분에 추가되는 문자열입니다.
- **레이블**: App Configuration 저장소 내의 레이블로 각 키-값에 추가되는 문자열입니다.
- **콘텐츠 형식**: App Configuration 저장소 내의 콘텐츠 형식으로 각 키-값에 추가되는 문자열입니다.
- **태그**: `{"tag1":"val1", "tag2":"val2"}` 형식의 JSON 개체로 App Configuration 저장소에 밀어넣은 각-키 값에 추가되는 태그를 정의합니다.
- **지정된 접두사 및 레이블을 사용하여 저장 중인 다른 모든 키-값 삭제**: 기본값은 **선택 취소** 되어 있습니다.
  - **선택**: 구성 파일에서 새 키-값을 밀어넣기 전에 App Configuration 저장소에서 지정된 접두사 및 레이블과 일치하는 모든 키-값을 제거합니다.
  - **선택 취소**: 구성 파일에서 App Configuration 저장소로 모든 키-값을 밀어넣고 App Configuration 저장소에 있는 다른 모든 키-값을 그대로 유지합니다.



## <a name="troubleshooting"></a>문제 해결

예기치 않은 오류가 발생하면 파이프라인 변수 `system.debug`를 `true`로 설정하여 디버그 로그를 사용하도록 설정할 수 있습니다.

## <a name="faq"></a>FAQ

**여러 구성 파일을 업로드하려면 어떻게 하나요?**

동일한 파이프라인 내에 Azure App Configuration Push 작업의 여러 인스턴스를 만들어 여러 구성 파일을 App Configuration 저장소에 밀어넣습니다.

**이 작업을 사용하여 Key Vault 참조를 만들려면 어떻게 해야 하나요?**

Key Vault 참조를 만들려면 "콘텐츠 유형" 매개 변수를 *application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8* 로 설정합니다. 구성 파일의 모든 키-값이 Key Vault 참조가 아닌 경우 Key Vault 참조 및 일반 키-값을 별도의 구성 파일에 넣고 별도로 푸시합니다.

**구성 저장소에 키-값을 밀어넣으려고 할 때 409 오류가 발생하는 이유는 무엇인가요?**

App Configuration 저장소에 잠긴 키-값을 제거하거나 덮어쓰려고 하면 409 충돌 오류 메시지가 나타납니다.
