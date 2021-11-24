---
title: 빠른 시작 - 다중 테넌트 Azure Logic Apps에서 Azure CLI를 사용하여 워크플로 만들기 및 관리
description: CLI를 사용하여 다중 테넌트 Azure Logic Apps에서 논리 앱 워크플로를 만들고 관리합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2, mode-other
ms.date: 05/25/2021
ms.openlocfilehash: d1273349a72c073df241d625755e2e816cca8676
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066892"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-cli-in-multi-tenant-azure-logic-apps"></a>빠른 시작: 다중 테넌트 Azure Logic Apps에서 Azure CLI를 사용하여 워크플로 만들기 및 관리

이 빠른 시작에서는 [Azure CLI Logic Apps 확장](/cli/azure/logic)(`az logic`)을 사용하여 논리 앱을 만들고 관리하는 방법을 보여줍니다. 명령줄에서 논리 앱 워크플로 정의에 대한 JSON 파일을 사용하여 논리 앱을 만들 수 있습니다. 그런 다음, 명령줄에서 `list`, `show`(`get`), `update`, `delete` 등의 작업을 실행하여 논리 앱을 관리할 수 있습니다.

> [!WARNING]
> Azure CLI Logic Apps 확장은 현재 *실험 단계* 이며 *고객 지원에서 다루지 않습니다*. 특히 프로덕션 환경에서 이 확장을 사용하도록 선택하는 경우 이 CLI 확장을 주의해서 사용해야 합니다.

Logic Apps를 처음 사용하는 경우에는 [Azure Portal](quickstart-create-first-logic-app-workflow.md), [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) 및 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)를 통해 첫 번째 논리 앱을 만드는 방법을 알아볼 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 로컬 컴퓨터에 설치된 [Azure CLI](/cli/azure/install-azure-cli)
* 컴퓨터에 설치된 [Logic Apps Azure CLI 확장](/cli/azure/azure-cli-extensions-list). 이 확장을 설치하려면 `az extension add --name logic` 명령을 사용합니다.
* 논리 앱을 만들 [Azure 리소스 그룹](#example---create-resource-group)입니다.

### <a name="prerequisite-check"></a>필수 구성 요소 확인

시작하기 전에 현재 환경의 유효성을 검사합니다.

* Azure Portal에 로그인하고 `az login`을 실행하여 구독이 활성 상태인지 확인합니다.
* 터미널 또는 명령 창에서 `az --version`을 실행하여 Azure CLI 버전을 확인합니다. 최신 버전은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli?tabs=azure-cli)를 참조하세요.
  * 최신 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

### <a name="example---create-resource-group"></a>예제 - 리소스 그룹 만들기

논리 앱에 대한 리소스 그룹이 아직 없는 경우 `az group create` 명령을 사용하여 그룹을 만듭니다. 예를 들어 다음 명령은 `westus` 위치에 `testResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name testResourceGroup --location westus
```

리소스 그룹이 성공적으로 만들어지면 출력에서 `provisioningState`가 `Succeeded`로 표시됩니다.

```output
<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>
```

## <a name="workflow-definition"></a>워크플로 정의

Azure CLI를 사용하여 [새 논리 앱을 만들거나](#create-logic-apps-from-cli) [기존 논리 앱을 업데이트](#update-logic-apps-from-cli)하려면 논리 앱에 대한 워크플로 정의가 필요합니다. Azure Portal에서 **디자이너** 보기를 **코드 보기** 로 전환하면 논리 앱의 기본 워크플로 정의를 JSON 형식으로 볼 수 있습니다.

논리 앱을 만들거나 업데이트하는 명령을 실행하면 워크플로 정의가 필수 매개 변수(`--definition`)로 업로드됩니다. [워크플로 정의 언어 스키마](./logic-apps-workflow-definition-language.md)를 따르는 JSON 파일로 워크플로 정의를 만들어야 합니다.

## <a name="create-logic-apps-from-cli"></a>CLI에서 논리 앱 만들기

Azure CLI에서 [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) 명령과 정의에 대한 JSON 파일을 사용하여 논리 앱 워크플로를 만들 수 있습니다.

```azurecli
az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]
```

명령에 다음과 같은 [필수 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters)가 포함되어야 합니다.

| 매개 변수 | 값 | 설명 |
| --------- | ----- | ----------- |
| 워크플로 정의 | `--definition` | 논리 앱의 [워크플로 정의](#workflow-definition)가 포함된 JSON 파일입니다. |
| 위치 | `--location -l` | 논리 앱이 배치되는 Azure 지역입니다. |
| Name | `--name -n` | 논리 앱의 이름입니다. 이름에는 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 괄호(`()`) 및 마침표(`.`)만 사용할 수 있습니다. 또한 이름은 모든 Azure 지역에서 고유해야 합니다. |
| 리소스 그룹 이름 | `--resource-group -g` | 논리 앱을 만들 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)입니다. 논리 앱에 대한 리소스 그룹이 아직 없는 경우 시작하기 전에 [리소스 그룹을 만듭니다](#example---create-resource-group). |
||||

[선택적 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters)를 추가로 포함시켜 논리 앱의 액세스 제어, 엔드포인트, 통합 계정, 통합 서비스 환경, 상태 및 리소스 태그를 구성할 수도 있습니다.

### <a name="example---create-logic-app"></a>예제 - 논리 앱 만들기

이 예제에서는 `westus` 지역에 있는 `testResourceGroup`이라는 리소스 그룹에 `testLogicApp`이라는 워크플로가 만들어집니다. JSON 파일 `testDefinition.json`에는 워크플로 정의가 포함됩니다

```azurecli-interactive
az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"
```

워크플로가 성공적으로 만들어지면 새 워크플로 정의의 JSON 코드가 CLI에 표시됩니다. 워크플로 만들기가 실패하는 경우 [가능한 오류 목록](#errors)을 참조하세요.

## <a name="update-logic-apps-from-cli"></a>CLI에서 논리 앱 업데이트

Azure CLI에서 [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) 명령을 사용하여 논리 앱의 워크플로를 업데이트할 수도 있습니다.

명령에는 [논리 앱을 만들 때](#create-logic-apps-from-cli)와 동일한 [필수 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters)가 포함되어야 합니다. 논리 앱을 만들 때와 동일한 [선택적 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters)를 추가할 수도 있습니다.

```azurecli
az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]
```

### <a name="example---update-logic-app"></a>예제 - 논리 앱 업데이트

이 예제에서는 [이전 섹션에서 만든 샘플 워크플로](#example---create-logic-app)가 다른 JSON 정의 파일 `newTestDefinition.json`을 사용하고, 설명 값이 포함된 두 개의 리소스 태그 `testTag1` 및 `testTag2`를 추가하도록 업데이트됩니다.

```azurecli-interactive
az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"
```

워크플로가 성공적으로 업데이트되면 논리 앱의 업데이트된 워크플로 정의가 CLI에 표시됩니다. 업데이트가 실패하는 경우 [가능한 오류 목록](#errors)을 참조하세요.

## <a name="delete-logic-apps-from-cli"></a>CLI에서 논리 앱 삭제

Azure CLI에서 [`az logic workflow delete`](/cli/azure/logic/workflow#az_logic_workflow_delete) 명령을 사용하여 논리 앱의 워크플로를 삭제할 수 있습니다.

명령에 다음과 같은 [필수 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_delete-required-parameters)가 포함되어야 합니다.

| 매개 변수 | 값 | Description |
| --------- | ----- | ----------- |
| 이름 | `--name -n` | 논리 앱의 이름입니다. |
| 리소스 그룹 이름 | `-resource-group -g` | 논리 앱이 배치되는 리소스 그룹입니다. |
||||

확인 프롬프트를 건너뛰는 [선택적 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_delete-optional-parameters) `--yes -y`를 포함할 수도 있습니다.

```azurecli
az logic workflow delete --name
                         --resource-group
                         [--yes]
```

이렇게 하면 CLI에서 논리 앱의 삭제를 확인하는 메시지를 표시합니다. 명령에 선택적 매개 변수 `--yes -y`를 사용하여 확인 프롬프트를 건너뛸 수 있습니다.

```output
Are you sure you want to perform this operation? (y/n):
```

[CLI에서 논리 앱을 나열](#list-logic-apps-in-cli)하거나 Azure Portal에서 논리 앱을 검토하여 논리 앱이 삭제되었는지 확인할 수 있습니다.

### <a name="example---delete-logic-app"></a>예제 - 논리 앱 삭제

이 예제에서는 [이전 섹션에서 만든 샘플 워크플로](#example---create-logic-app)를 삭제합니다.

```azurecli-interactive
az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"
```

확인 프롬프트에 `y`로 대답하면 논리 앱이 삭제됩니다.

### <a name="considerations---delete-logic-app"></a>고려 사항 - 논리 앱 삭제

논리 앱을 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Logic Apps 서비스가 진행 중이거나 보류 중인 실행을 취소하기 위해 전력을 다합니다.

  볼륨이나 백로그가 큰 경우에도 대부분의 실행은 완료 또는 시작 전에 취소됩니다. 그러나 취소 프로세스를 완료하는 데 시간이 걸릴 수 있습니다. 한편, 런타임이 취소 프로세스를 진행하는 동안 일부 실행을 선택하여 진행할 수 있습니다.

* Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 워크플로를 삭제한 다음, 동일한 워크플로를 다시 만들면 다시 생성된 워크플로에는 삭제된 워크플로와 동일한 메타데이터가 포함되지 않습니다. 삭제된 워크플로를 호출한 모든 워크플로를 다시 저장해야 합니다. 이렇게 하면 호출자가 다시 생성된 워크플로에 대한 올바른 정보를 가져옵니다. 그렇지 않으면 다시 생성된 워크플로에 대한 호출이 `Unauthorized` 오류와 함께 실패합니다. 이 동작은 Azure 함수를 호출하는 워크플로 및 통합 계정에서 아티팩트를 사용하는 워크플로에도 적용됩니다.

## <a name="show-logic-apps-in-cli"></a>CLI에서 논리 앱 표시

[`az logic workflow show`](/cli/azure/logic/workflow#az_logic_workflow_show) 명령을 사용하여 특정 논리 앱 워크플로를 가져올 수 있습니다.

```azurecli
az logic workflow show --name
                       --resource-group
```

명령에 다음과 같은 [필수 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_show-required-parameters)가 포함되어야 합니다.

| 매개 변수 | 값 | 설명 |
| --------- | ----- | ----------- |
| 이름 | `--name -n` | 논리 앱의 이름입니다. |
| 리소스 그룹 이름 | `--resource-group -g` | 논리 앱이 배치되는 리소스 그룹의 이름입니다. |
||||

### <a name="example---get-logic-app"></a>예제 - 논리 앱 가져오기

이 예제에서는 디버깅을 위해 리소스 그룹 `testResourceGroup`의 논리 앱 `testLogicApp`이 전체 로그와 함께 반환됩니다.

```azurecli-interactive
az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug
```

## <a name="list-logic-apps-in-cli"></a>CLI에서 논리 앱 나열

[`az logic workflow list`](/cli/azure/logic/workflow#az_logic_workflow_list) 명령을 사용하여 논리 앱을 구독별로 나열할 수 있습니다. 이 명령은 논리 앱의 워크플로에 대한 JSON 코드를 반환합니다.

다음과 같은 [선택적 매개 변수](/cli/azure/logic/workflow#az_logic_workflow_list-optional-parameters)로 결과를 필터링할 수 있습니다.

| 매개 변수 | 값 | 설명 |
| --------- | ----- | ----------- |
| 리소스 그룹 이름 | `--resource-group -g` | 결과를 필터링할 리소스 그룹의 이름입니다. |
| 항목 수 | `--top` | 결과에 포함되는 항목의 수입니다. |
| Assert | `--filter` | 목록에 사용 중인 필터의 유형입니다. 상태(`State`), 트리거(`Trigger`) 및 참조되는 리소스의 식별자(`ReferencedResourceId`)를 기준으로 필터링할 수 있습니다. |
||||

```azurecli
az logic workflow list [--filter]
                       [--resource-group]
                       [--top]
```

### <a name="example---list-logic-apps"></a>예제 - 논리 앱 나열

이 예제에서는 리소스 그룹 `testResourceGroup`에서 사용하도록 설정된 모든 워크플로가 ASCII 테이블 형식으로 반환됩니다.

```azurecli-interactive
az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"
```

## <a name="errors"></a>오류

다음 오류는 Azure Logic Apps CLI 확장이 설치되지 않았음을 나타냅니다. 필수 구성 요소의 단계에 따라 컴퓨터에 [Logic Apps 확장](#prerequisites)을 설치하세요.

```output
az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview
```

다음 오류는 워크플로 정의를 업로드하는 파일 경로가 올바르지 않다는 의미일 수 있습니다.

```output
Expecting value: line 1 column 1 (char 0)
```

## <a name="global-parameters"></a>글로벌 매개 변수

`az logic` 명령에 다음과 같은 선택적 글로벌 Azure CLI 매개 변수를 사용할 수 있습니다.

| 매개 변수 | 값 | 설명 |
| --------- | ----- | ----------- |
| 출력 형식 | `--output -o` | 기본 JSON의 [출력 형식](/cli/azure/format-output-azure-cli)을 변경합니다. |
| 오류만 표시 | `--only-show-errors` | 경고를 표시하지 않고 오류만 표시합니다. |
| 자세히 | `--verbose` | 자세한 정보 로그를 표시합니다. |
| 디버그 | `--debug` | 모든 디버그 로그를 표시합니다. |
| 도움말 메시지 | `--help -h` | 도움말 대화 상자를 표시합니다. |
| 쿼리 | `--query` | JSON 출력에 대한 JMESPath 쿼리 문자열을 설정합니다. |
||||

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure/)를 참조하세요.

[Microsoft 코드 샘플 브라우저](/samples/browse/?products=azure-logic-apps)에서 더 많은 Logic Apps CLI 스크립트 샘플을 찾을 수 있습니다.

다음으로, 샘플 스크립트와 워크플로 정의를 사용하여 Azure CLI를 통해 예제 앱 논리를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 스크립트를 사용하여 논리 앱을 만듭니다](sample-logic-apps-cli-script.md).
