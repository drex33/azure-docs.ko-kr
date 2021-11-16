---
title: Resource Manager 템플릿을 사용하여 Azure Automation 계정 만들기
titleSuffix: Azure Automation
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Automation 계정을 만드는 방법을 보여줍니다.
services: automation
ms.date: 08/27/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom: mvc, subject-armqs, mode-arm
ms.openlocfilehash: 66fb9a88761b47f0cc704fcf20dd29dd7768777a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487880"
---
# <a name="create-an-azure-automation-account-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure Automation 계정 만들기

Azure Automation은 Azure 및 Azure 이외의 환경에서 일관된 관리를 지원하는 클라우드 기반 자동화 및 구성 서비스를 제공하며, 이 문서에서는 Automation 계정을 만드는 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하는 방법을 보여줍니다. ARM 템플릿을 사용하면 다른 배포 방법과 비교하여 단계가 줄어듭니다. JSON 템플릿은 환경에서 표준 구성으로 사용될 수 있는 매개 변수에 대한 기본값을 지정합니다. 조직에서 공유 액세스에 대한 Azure Storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [ARM 템플릿과 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

이 샘플 템플릿은 다음 단계를 수행합니다.

* Azure Monitor Log Analytics 작업 영역 만들기를 자동화합니다.
* Azure Automation 계정 만들기를 자동화합니다.
* Automation 계정을 Log Analytics 작업 영역에 연결합니다.
* 계정에 샘플 Automation Runbook을 추가합니다.

> [!NOTE]
> ARM 템플릿을 사용하는 경우 Automation 실행 계정 만들기가 지원되지 않습니다. 포털에서 또는 PowerShell을 사용하여 실행 계정을 수동으로 만들려면 [실행 계정 만들기](create-run-as-account.md)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Automation 및 Azure Monitor를 처음 접하는 경우 구성 정보를 반드시 이해해야 합니다. 이렇게 하면 새 Automation 계정에 연결된 Log Analytics 작업 영역을 만들고 구성하고 사용하려고 할 때 쉽게 오류를 방지할 수 있습니다.

* 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해하려면 [추가 세부 정보](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)를 검토하세요.

* [작업 영역 매핑](how-to/region-mappings.md)을 검토하여 지원되는 지역 인라인 또는 매개 변수 파일에서 지정합니다. 특정 Azure 지역에서만 구독의 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.

* Azure Monitor 로그를 처음 접하고 아직 작업 영역을 배포하지 않은 경우 [작업 영역 디자인 지침](../azure-monitor/logs/design-logs-deployment.md)을 검토하세요. 이 문서는 액세스 제어에 대해 알아보고 조직에 권장되는 디자인 구현 전략을 이해하는 데 도움이 됩니다.

## <a name="review-the-template"></a>템플릿 검토

이 문서에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-automation/)에서 가져온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

템플릿에 정의된 Azure 리소스:

* [**Microsoft.OperationalInsights/workspaces**](/azure/templates/microsoft.operationalinsights/workspaces): Azure Log Analytics 작업 영역을 만듭니다.
* [**Microsoft.Automation/automationAccounts**](/azure/templates/microsoft.automation/automationaccounts): Azure Automation 계정을 만듭니다.
* [**Microsoft.Automation/automationAccounts/runbooks**](/azure/templates/microsoft.automation/automationaccounts/runbooks): Azure Automation 계정 Runbook을 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 아래 **Azure에 배포** 단추를 선택하여 Azure에 로그인하고 ARM 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

1. 다음 값을 입력하거나 선택합니다.

    |속성 |Description |
    |---|---|
    |구독 |드롭다운 목록에서 Azure 구독을 선택합니다.|
    |Resource group |드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
    |지역 |이 값은 자동으로 채워집니다.|
    |작업 영역 이름 |새 Log Analytics 작업 영역의 이름을 입력합니다.|
    |SKU | 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값입니다. 2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성하려면 유효한 유일한 Log Analytics 가격 책정 계층은 `PerGB2018`입니다.|
    |데이터 보존 |기본값은 30일입니다.|
    |위치 |이 값은 리소스 그룹에 사용되는 위치로 자동 입력됩니다.|
    |Automation 계정 이름 | 새 Automation 계정의 이름을 입력합니다.|
    |샘플 그래픽 Rrunbook 이름 | 있는 그대로 둡니다.|
    |샘플 그래픽 Runbook 설명 | 있는 그대로 둡니다.|
    |샘플 PowerShell Runbook 이름 | 있는 그대로 둡니다.|
    |샘플 PowerShell Runbook 설명 | 있는 그대로 둡니다.|
    |샘플 Python2Runbook 이름 |있는 그대로 둡니다.|
    |샘플 Python2Runbook 설명 |있는 그대로 둡니다.|
    |_artifacts 위치 |있는 그대로 둡니다.<sup>*</sup> 아티팩트 위치에 대한 URI입니다.|
    |_artifacts 위치 SAS 토큰 | 비워 둡니다. `_artifactsLocation`에 액세스하는 데 필요한 sasToken입니다. 함께 제공되는 스크립트를 사용하여 템플릿이 배포되면 `sasToken`이 자동으로 생성됩니다.|

    <sup>*</sup> PowerShell, CLI 또는 Portal의 템플릿 기능에서 ARM 템플릿을 실행하려고 할 때 `_artifactsLocation` 매개 변수가 올바르게 설정되지 않은 경우 다음과 유사한 오류 메시지가 표시됩니다.
    
    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`
    
    이 오류를 방지하려면 Portal의 템플릿 기능에서 실행하는 경우 `_artifactsLocation` 매개 변수에 대해 다음 값을 지정합니다. `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`
    
    PowerShell에서를 실행하는 경우 매개 변수 및 해당 `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json` 값을 포함합니다.
    
    Azure CLI에서를 실행하는 경우 매개 변수 및 해당 `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json` 값을 포함합니다.
    
    PowerShell/CLI에 대한 참조는 **템플릿 사용** 섹션에서 다음 [Azure Automation 계정 만들기(microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/)를 참조하세요.

1. **검토 + 만들기**, **만들기** 를 차례로 선택합니다. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음 이미지와 비슷한 결과가 출력됩니다.

    ![배포가 완료되었을 때 결과 예](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. 배포가 완료되면 **리소스로 이동** 링크가 포함된 **배포 성공** 알림이 표시됩니다. **리소스 그룹** 페이지에 새 리소스가 나열됩니다. 목록에서 새 Automation 계정을 선택합니다.

1. 왼쪽의 **프로세스 자동화** 에서 **Runbook** 을 선택합니다. **Runbook** 페이지에는 Automation 계정을 사용하여 만든 세 가지 샘플 Runbook이 나열됩니다.

    ![Automation 계정을 사용하여 만든 자습서 Runbook](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

1. 왼쪽의 **관련 리소스** 에서 **연결된 작업 영역** 을 선택합니다. **연결된 작업 영역** 페이지에는 이전에 Automation 계정에 연결된 Log Analytics 작업 영역이 표시됩니다.

    ![Log Analytics 작업 영역에 연결된 Automation 계정](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="next-steps"></a>다음 단계

Automation 계정에 대한 [진단 설정을 구성](automation-manage-send-joblogs-log-analytics.md)하여 Runbook 작업 상태 및 작업 스트림을 연결된 Log Analytics 작업 영역으로 보냅니다.
