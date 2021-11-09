---
title: Azure Pipelines를 사용하여 설정을 App Configuration으로 끌어오기
description: Azure Pipelines를 사용하여 키-값을 App Configuration 저장소로 끌어오는 방법을 알아봅니다.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 2bee0e603d3cde38e56146900d682cc5e2b40bdc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056476"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines를 사용하여 설정을 App Configuration으로 끌어오기

[Azure App Configuration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) 작업은 App Configuration 저장소에서 키-값을 끌어오고 이후 작업에서 사용될 수 있는 Azure 파이프라인 변수로 설정합니다. 이 작업은 구성 파일에서 App Configuration 저장소로 키 값을 푸시하는 [Azure App Configuration 푸시](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) 작업을 보완합니다. 자세한 내용은 [Azure Pipelines를 사용하여 App Configuration에 설정 푸시](push-kv-devops-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- App Configuration 저장소 - [Azure Portal](https://portal.azure.com)에서 무료로 만드세요.
- Azure DevOps 프로젝트 - [무료로 만들기](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration 작업 - [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)에서 무료로 다운로드하세요. 
- [노드 10](https://nodejs.org/en/blog/release/v10.21.0/) -자체 호스팅 에이전트에서 작업을 실행 하는 사용자 

## <a name="create-a-service-connection"></a>서비스 연결 만들기

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>역할 할당 추가

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>빌드에 사용

이 섹션에서는 Azure DevOps 빌드 파이프라인에서 Azure App Configuration 작업을 사용하는 방법을 설명합니다.

1. **파이프라인** > **파이프라인** 을 클릭하여 빌드 파이프라인 페이지로 이동합니다. 빌드 파이프라인 설명서는 [첫 번째 파이프라인 만들기](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)를 참조하세요.
      - 새 빌드 파이프라인을 만드는 경우 프로세스의 마지막 단계의 **검토** 탭에 있는 파이프라인 오른쪽에서 **길잡이 표시** 를 선택합니다.
      ![새 파이프라인을 위한 도우미 표시 단추를 보여 주는 스크린샷](./media/new-pipeline-show-assistant.png)
      - 기존 빌드 파이프라인을 사용하는 경우 오른쪽 위에서 **편집** 단추를 클릭합니다.
      ![기존 파이프라인에 대한 편집 단추를 보여주는 스크린샷입니다.](./media/existing-pipeline-show-assistant.png)
1. **Azure App Configuration** 작업을 검색합니다.
![검색 상자에서 Azure App Configuration의 작업 추가 대화 상자를 보여주는 스크린샷입니다.](./media/add-azure-app-configuration-task.png)
1. App Configuration 저장소에서 키-값을 끌어오기 위해 작업에 필요한 매개변수를 구성합니다. 매개변수에 대한 설명은 아래 **매개변수** 섹션과 각 매개변수 옆에 있는 도구 설명에 있습니다.
      - **Azure 구독** 매개변수를 이전 단계에서 만든 서비스 연결의 이름으로 설정합니다.
      - **App Configuration 이름** 을 App Configuration 저장소의 리소스 이름으로 설정합니다.
      - 나머지 매개변수는 기본값을 그대로 둡니다.
![App Configuration 작업 매개변수를 보여주는 스크린샷입니다.](./media/azure-app-configuration-parameters.png)
1. 빌드를 저장하고 큐에 넣습니다. 빌드 로그에는 작업 실행 중 발생한 모든 오류가 표시됩니다.

## <a name="use-in-releases"></a>릴리스에 사용

이 섹션에서는 Azure DevOps 릴리스 파이프라인에서 Azure App Configuration 작업을 사용하는 방법을 설명합니다.

1. **파이프라인** > **릴리스** 를 선택하여 릴리스 파이프라인 페이지로 이동합니다. 릴리스 파이프라인 설명서는 [릴리스 파이프라인](/azure/devops/pipelines/release)을 참조하세요.
1. 기존 릴리스 파이프라인을 선택합니다. 아직 없으면 **새 파이프라인** 을 클릭하여 새 항목을 만듭니다.
1. 오른쪽 위 모서리에서 **편집** 단추를 선택하여 릴리스 파이프라인을 편집합니다.
1. **작업** 드롭다운에서 작업을 추가할 **스테이지** 를 선택합니다. 스테이지에 대한 자세한 내용은 [여기](/azure/devops/pipelines/release/environments)에서 확인할 수 있습니다.
![작업 드롭다운에서 선택된 스테이지를 보여 주는 스크린샷](./media/pipeline-stage-tasks.png)
1. 새 작업을 추가할 작업 옆의 **+** 를 클릭합니다.
![작업 옆에 있는 더하기 단추를 보여주는 스크린샷입니다.](./media/add-task-to-job.png)
1. **Azure App Configuration** 작업을 검색합니다.
![검색 상자에서 Azure App Configuration의 작업 추가 대화 상자를 보여주는 스크린샷입니다.](./media/add-azure-app-configuration-task.png)
1. App Configuration 저장소에서 키-값을 끌어오기 위해 작업 내에서 필요한 매개변수를 구성합니다. 매개변수에 대한 설명은 아래 **매개변수** 섹션과 각 매개변수 옆에 있는 도구 설명에 있습니다.
      - **Azure 구독** 매개변수를 이전 단계에서 만든 서비스 연결의 이름으로 설정합니다.
      - **App Configuration 이름** 을 App Configuration 저장소의 리소스 이름으로 설정합니다.
      - 나머지 매개변수는 기본값을 그대로 둡니다.
1. 릴리스를 저장하고 큐에 넣습니다. 릴리스 로그에 작업 실행 중 발생한 모든 오류가 표시됩니다.

## <a name="parameters"></a>매개 변수

다음 매개변수는 Azure App Configuration 작업에 사용됩니다.

- **Azure 구독**: 사용 가능한 Azure 서비스 연결이 포함된 드롭다운입니다. 사용 가능한 Azure 서비스 연결 목록을 업데이트하고 새로 고치려면 텍스트 상자 오른쪽에서 **Azure 구독 새로 고침** 단추를 누릅니다.
- **App Configuration 이름**: 선택한 구독 아래에서 사용 가능한 구성 저장소를 로드하는 드롭다운입니다. 사용 가능한 구성 저장소 목록을 업데이트하고 새로고침하려면 텍스트 상자 오른쪽에서 **App Configuration 이름 새로고침** 단추를 누릅니다.
- **키 필터**: 필터를 사용하여 Azure App Configuration에서 요청된 키-값을 선택할 수 있습니다. 값이 *이면 모든 키-값을 선택합니다. 자세한 내용은 [키 값 쿼리](concept-key-value.md#query-key-values)를 참조하세요.
- **레이블**: App Configuration 저장소에서 키-값을 선택할 때 사용할 레이블을 지정합니다. 레이블이 제공되지 않았으면 레이블이 없는 키-값이 검색됩니다. , * 문자는 허용되지 않습니다.
- **키 접두사 자르기**: 변수로 설정하기 전 App Configuration 키에서 잘라야 하는 하나 이상의 접두사를 지정합니다. 여러 접두사는 줄 바꿈 문자로 구분할 수 있습니다.

## <a name="use-key-values-in-subsequent-tasks"></a>후속 작업에서 키-값 사용

App Configuration에서 가져온 키-값은 환경 변수로 액세스할 수 있는 파이프라인 변수로 설정됩니다. 환경 변수의 키는 지정된 경우 접두사를 자른 후 App Configuration에서 검색되는 키-값의 키입니다.

예를 들어 후속 작업이 PowerShell 스크립트를 실행할 경우 다음과 같이 'myBuildSetting' 키에 키-값을 사용할 수 있습니다.
```powershell
echo "$env:myBuildSetting"
```
그리고 값이 콘솔에 출력됩니다.

> [!NOTE]
> App Configuration 내의 Azure Key Vault 참조가 확인되고 [비밀 변수](/azure/devops/pipelines/process/variables#secret-variables)로 설정됩니다. Azure 파이프라인에서 비밀 변수는 로그에서 마스킹되어 표시됩니다. 암호 변수는 작업에 환경 변수로 전달되지 않으며 대신 입력으로 전달되어야 합니다. 

## <a name="troubleshooting"></a>문제 해결

예기치 않은 오류가 발생하면 파이프라인 변수 `system.debug`를 `true`로 설정하여 디버그 로그를 사용하도록 설정할 수 있습니다.

## <a name="faq"></a>FAQ

**여러 키 및 레이블로부터 내 구성을 만들려면 어떻게 할까요?**

일부 경우에 따라 default 및 dev와 같은 여러 레이블로부터 구성을 만들어야 할 수 있습니다. 이러한 시나리오를 구현하기 위해 하나의 파이프라인에 여러 App Configuration 작업을 사용할 수 있습니다. 이후 단계에서 한 작업으로 가져오는 키-값은 이전 단계의 모든 값을 대체합니다. 앞에 설명한 예제에서는 하나의 작업으로 default 레이블의 키-값을 선택할 수 있지만 두 번째 작업에서는 dev 레이블의 키-값을 선택할 수 있습니다. dev 레이블의 키는 default 레이블의 동일한 키를 재정의합니다.
