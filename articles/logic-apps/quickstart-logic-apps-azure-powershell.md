---
title: 빠른 시작 - 다중 테넌트 Azure Logic Apps에서 Azure PowerShell을 사용하여 워크플로 만들기 및 관리
description: Azure PowerShell을 사용하여 다중 테넌트 Azure Logic Apps에서 논리 앱 워크플로를 만들고 관리합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, contperf-fy21q2, mode-other
ms.date: 07/26/2021
ms.openlocfilehash: e9d3fc228dc1038e59a86372bcd563d067e63894
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066859"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-powershell-in-multi-tenant-azure-logic-apps"></a>빠른 시작 - 다중 테넌트 Azure Logic Apps에서 Azure PowerShell으로 워크플로 만들기 및 관리

이 빠른 시작에서는 [Azure PowerShell](/powershell/azure/install-az-ps)을 사용하여 논리 앱을 만들고 관리하는 방법을 보여줍니다. PowerShell에서 논리 앱 워크플로 정의에 대한 JSON 파일을 사용하여 논리 앱을 만들 수 있습니다. 그런 다음, [Az.LogicApp](/powershell/module/az.logicapp/) PowerShell 모듈에서 cmdlet을 실행하여 논리 앱을 관리할 수 있습니다.

Azure Logic Apps를 처음 사용하는 경우에는 [Azure Portal](quickstart-create-first-logic-app-workflow.md), [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) 및 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)를 통해 첫 번째 논리 앱을 만드는 방법을 알아볼 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 로컬 컴퓨터에 설치된 [Az PowerShell](/powershell/azure/install-az-ps) 모듈입니다.
* 논리 앱을 만들 [Azure 리소스 그룹](#example---create-resource-group)입니다.

### <a name="prerequisite-check"></a>필수 구성 요소 확인

시작하기 전에 현재 환경의 유효성을 검사합니다.

* Azure Portal에 로그인하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 실행하여 구독이 활성 상태인지 확인합니다.
* `Get-InstalledModule -Name Az`를 실행하여 Azure PowerShell 버전을 확인합니다. 최신 버전은 [최신 릴리스 정보](/powershell/azure/migrate-az-6.0.0)를 참조하세요.
  * 최신 버전이 없는 경우[Azure PowerShell 모듈 업데이트](/powershell/azure/install-az-ps#update-the-azure-powershell-module)에 따라 설치를 업데이트합니다.

### <a name="example---create-resource-group"></a>예제 - 리소스 그룹 만들기

논리 앱에 대한 리소스 그룹이 아직 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 그룹을 만듭니다. 예를 들어 다음 명령은 `westus` 위치에 `testResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name testResourceGroup -Location westus
```

리소스 그룹이 성공적으로 만들어지면 출력에서 `ProvisioningState`가 `Succeeded`로 표시됩니다.

```Output
ResourceGroupName : testResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup
```

## <a name="workflow-definition"></a>워크플로 정의

Azure PowerShell을 사용하여 [새 논리 앱을 만들거나](#create-logic-apps-from-powershell) [기존 논리 앱을 업데이트](#update-logic-apps-from-powershell)하려면 논리 앱에 대한 워크플로 정의가 필요합니다. Azure Portal에서 **디자이너** 보기를 **코드 보기** 로 전환하면 논리 앱의 기본 워크플로 정의를 JSON 형식으로 볼 수 있습니다.

논리 앱을 만들거나 업데이트하는 명령을 실행하면 워크플로 정의가 매개 변수 설정에 따라 필수 매개 변수(`Definition`) 또는 (`DefinitionFilePath`)로 업로드됩니다. [워크플로 정의 언어 스키마](./logic-apps-workflow-definition-language.md)를 따르는 JSON 파일로 워크플로 정의를 만들어야 합니다.

## <a name="create-logic-apps-from-powershell"></a>PowerShell에서 논리 앱 만들기

Azure PowerShell에서 [`New-AzLogicApp`](/powershell/module/az.logicapp/new-azlogicapp) cmdlet 및 정의에 대한 JSON 파일을 사용하여 논리 앱 워크플로를 만들 수 있습니다.

### <a name="example---create-logic-app"></a>예제 - 논리 앱 만들기

이 예제에서는 `westus` 지역에 있는 `testResourceGroup`이라는 리소스 그룹에 이름이 `testLogicApp`인 워크플로를 만듭니다. JSON 파일 `testDefinition.json`에는 워크플로 정의가 포함됩니다

```azurepowershell-interactive
New-AzLogicApp -ResourceGroupName testResourceGroup -Location westus -Name testLogicApp -DefinitionFilePath .\testDefinition.json
```

워크플로가 성공적으로 생성되면 PowerShell은 새 워크플로 정의를 표시합니다.

## <a name="update-logic-apps-from-powershell"></a>PowerShell에서 논리 앱 업데이트

[`Set-AzLogicApp`](/powershell/module/az.logicapp/set-azlogicapp) cmdlet을 사용하여 Azure PowerShell에서 논리 앱의 워크플로를 업데이트할 수도 있습니다.

### <a name="example---update-logic-app"></a>예제 - 논리 앱 업데이트

이 예제에서는 다른 JSON 정의 파일 `newTestDefinition.json`을 사용하여 [이전 섹션에서 만든 샘플 워크플로](#example---create-logic-app)를 업데이트하는 방법을 보여줍니다.

```azurepowershell-interactive
Set-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp -DefinitionFilePath .\newTestDefinition.json
```

워크플로가 성공적으로 업데이트되면 논리 앱의 업데이트된 워크플로 정의가 PowerShell에 표시됩니다.

## <a name="delete-logic-apps-from-powershell"></a>PowerShell에서 논리 앱 삭제

[`Remove-AzLogicApp`](/powershell/module/az.logicapp/remove-azlogicapp) cmdlet을 사용하여 Azure PowerShell에서 논리 앱의 워크플로를 삭제할 수 있습니다.

### <a name="example---delete-logic-app"></a>예제 - 논리 앱 삭제

이 예제에서는 [이전 섹션에서 만든 샘플 워크플로](#example---create-logic-app)를 삭제합니다.

```azurepowershell-interactive
Remove-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

확인 프롬프트에 `y`로 대답하면 논리 앱이 삭제됩니다.

### <a name="considerations---delete-logic-app"></a>고려 사항 - 논리 앱 삭제

논리 앱을 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Logic Apps 서비스가 진행 중이거나 보류 중인 실행을 취소하기 위해 전력을 다합니다.

  볼륨이나 백로그가 큰 경우에도 대부분의 실행은 완료 또는 시작 전에 취소됩니다. 그러나 취소 프로세스를 완료하는 데 시간이 걸릴 수 있습니다. 한편, 런타임이 취소 프로세스를 진행하는 동안 일부 실행을 선택하여 진행할 수 있습니다.

* Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 워크플로를 삭제한 다음, 동일한 워크플로를 다시 만들면 다시 생성된 워크플로에는 삭제된 워크플로와 동일한 메타데이터가 포함되지 않습니다. 삭제된 워크플로를 호출한 모든 워크플로를 다시 저장해야 합니다. 이렇게 하면 호출자가 다시 생성된 워크플로에 대한 올바른 정보를 가져옵니다. 그렇지 않으면 다시 생성된 워크플로에 대한 호출이 `Unauthorized` 오류와 함께 실패합니다. 이 동작은 Azure 함수를 호출하는 워크플로 및 통합 계정에서 아티팩트를 사용하는 워크플로에도 적용됩니다.

## <a name="show-logic-apps-in-powershell"></a>PowerShell에서 논리 앱 표시

[`Get-AzLogicApp`](/powershell/module/az.logicapp/get-azlogicapp) 명령을 사용하여 특정 논리 앱 워크플로를 가져올 수 있습니다.

### <a name="example---get-logic-app"></a>예제 - 논리 앱 가져오기

이 예제에서는 리소스 그룹 `testResourceGroup`의 논리 앱 `testLogicApp`을 반환합니다.

```azurepowershell-interactive
Get-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

[Microsoft 코드 샘플 브라우저](/samples/browse/?products=azure-logic-apps)에서 더 많은 Logic Apps 스크립트 샘플을 찾을 수 있습니다.
