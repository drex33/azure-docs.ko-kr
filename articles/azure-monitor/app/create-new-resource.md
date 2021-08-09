---
title: 새 Azure Application Insights 리소스 만들기 | Microsoft Docs
description: 새 라이브 애플리케이션에 대한 Application Insights 모니터링을 수동으로 설정합니다.
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 37f090f44099dc45d6c258e10b09d164277fcb47
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077179"
---
# <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

Azure Application Insights는 Microsoft Azure *리소스* 에 애플리케이션에 대한 데이터를 표시합니다. 따라서 새 리소스 만들기는 [새 애플리케이션을 모니터링하도록 Application Insights를 설정][start]하는 과정에 포함됩니다. 새 리소스를 만든 후 해당 계측 키를 가져오고 이를 사용하여 Application Insights SDK를 구성할 수 있습니다. 계측 키는 원격 분석을 리소스에 연결합니다.

> [!IMPORTANT]
> [클래식 Application Insights는 사용되지 않습니다](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/). 이 [작업 영역 기반 Application Insights로 업그레이드하는 방법에 관한 지침](convert-classic-resource.md)을 따르세요.

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure에 로그인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

[Azure Portal](https://portal.azure.com)에 로그인하고 Application Insights 리소스를 만듭니다.

![왼쪽 위에 있는 ‘+’ 기호를 클릭합니다. 개발자 도구, Application Insights를 차례로 선택합니다.](./media/create-new-resource/new-app-insights.png)

   | 설정        |  값           | 설명  |
   | ------------- |:-------------|:-----|
   | **이름**      | `Unique value` | 모니터링하는 애플리케이션을 식별하는 이름입니다. |
   | **리소스 그룹**     | `myResourceGroup`      | App Insights 데이터를 호스트할 신규 또는 기존 리소스 그룹의 이름입니다. |
   | **지역** | `East US` | 가까운 위치 또는 앱이 호스팅되는 위치 근처를 선택합니다. |
   | **리소스 모드** | `Classic` 또는 `Workspace-based` | 작업 영역 기반 리소스를 사용하면 Application Insights 원격 분석을 일반적인 Log Analytics 작업 영역으로 보낼 수 있습니다. 자세한 내용은 [작업 영역 기반 리소스에 관한 문서](create-workspace-resource.md)를 참조하세요.

> [!NOTE]
> 서로 다른 리소스 그룹에서 동일한 리소스 이름을 사용할 수 있지만 전역적으로 고유한 이름을 사용하는 것이 좋습니다. 이 이름은 필요한 구문을 간소화하므로 [리소스 간 쿼리를 수행](../logs/cross-workspace-query.md#identifying-an-application)하려는 경우 유용할 수 있습니다.

필수 필드에 적절한 값을 입력한 다음, **검토 + 만들기** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![필수 필드에 값을 입력한 다음, “검토 + 만들기”를 선택합니다.](./media/create-new-resource/review-create.png)

앱이 만들어지면 새 창이 열립니다. 이 창에서 모니터링된 애플리케이션에 관한 성능 및 사용량 데이터를 볼 수 있습니다. 

## <a name="copy-the-instrumentation-key"></a>계측 키 복사

계측 키는 원격 분석 데이터를 연결하려는 리소스를 식별합니다. 계측 키를 복사하고 애플리케이션의 코드에 추가해야 합니다.

> [!IMPORTANT]
> 계측 키보다 [연결 문자열](./sdk-connection-string.md)이 권장됩니다. 새 Azure 지역에서는 계측 키 대신 연결 문자열을 **사용해야 합니다**. 연결 문자열은 원격 분석 데이터를 연결할 리소스를 식별합니다. 또한 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.

## <a name="install-the-sdk-in-your-app"></a>응용 프로그램에 SDK를 설치합니다.

Application Insights SDK를 애플리케이션에 설치합니다. 이 단계는 애플리케이션의 형식에 따라 크게 달라 집니다.

계측 키를 사용하여 [애플리케이션에 설치한 SDK][start]를 구성합니다.

SDK는 표준 모듈을 포함하고 있기 때문에 원격 분석을 전송할 때 추가 코드를 작성할 필요가 없습니다. 사용자 작업을 추적하거나 문제를 보다 세부적으로 진단하려면 [API를 사용][api]하여 사용자 고유의 원격 분석을 보내도록 합니다.

## <a name="creating-a-resource-automatically"></a>자동으로 리소스 만들기

### <a name="powershell"></a>PowerShell

새 Application Insights 리소스 만들기

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>예제

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>결과

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

이 cmdlet에 관한 전체 PowerShell 설명서를 보고 계측 키를 검색하는 방법을 알아보려면 [Azure PowerShell 설명서](/powershell/module/az.applicationinsights/new-azapplicationinsights)를 참조하세요.

### <a name="azure-cli-preview"></a>Azure CLI(미리 보기)

미리 보기 Application Insights Azure CLI 명령에 액세스하려면 먼저 다음을 실행해야 합니다.

```azurecli
 az extension add -n application-insights
```

`az extension add` 명령을 실행하지 않으면 다음 오류 메시지가 표시됩니다. `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

이제 다음을 실행하여 Application Insights 리소스를 만들 수 있습니다.

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>예제

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>결과

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

이 명령에 관한 전체 Azure CLI 설명서를 보고 계측 키를 검색하는 방법을 알아보려면 [Azure CLI 설명서](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [진단 검색](./diagnostic-search.md)
* [메트릭 탐색](../essentials/metrics-charts.md)
* [분석 쿼리 작성](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md
