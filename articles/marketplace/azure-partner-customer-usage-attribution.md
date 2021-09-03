---
title: Azure 고객 사용량 특성
description: 상업용 마켓플레이스에서 Azure 애플리케이션에 대한 고객 사용을 추적하는 개요와 파트너가 개발한 기타 배포가능한 IP를 확인하세요.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-terraform, devx-track-azurepowershell
ms.openlocfilehash: 9536002cb5c3e30f32479b74e761d7128090af26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528436"
---
# <a name="azure-customer-usage-attribution"></a>Azure 고객 사용량 특성

고객 사용량 특성은 파트너로서 IP를 배포하는 동안 생성된 고객 구독에서 Azure 리소스의 사용량을 연결합니다. 내부 Microsoft 시스템에 이러한 연결을 구성하면 소프트웨어를 실행하는 Azure의 메모리 공간을 보다 잘 파악할 수 있습니다. [상업용 마켓플레이스의 Azure 애플리케이션 제품](#commercial-marketplace-azure-apps)의 경우 이 추적 기능을 사용하면 Microsoft 영업 팀에 맞춰 Microsoft 파트너 프로그램에 대한 크레딧을 얻을 수 있습니다. 고객 사용량 특성은 [상용 마켓플레이스의 Azure 가상 머신 제품](marketplace-virtual-machines.md)에 적용되지 않습니다. 최종 고객 구독에서 Azure 사용량을 추적하기 위해 마켓플레이스 게시자가 가상 머신 제품에 대해 수행해야 하는 작업은 없습니다.

고객 사용량 특성은 세 가지 배포 옵션을 지원합니다.

1. Azure Resource Manager 템플릿(상업용 마켓플레이스에서 "솔루션 템플릿" 또는 "관리되는 앱"이라고도 하는 Azure 앱의 일반적인 기반): 파트너는 Azure 솔루션의 인프라와 구성을 정의하는 Resource Manager 템플릿을 만듭니다. Resource Manager 템플릿을 통해 고객은 솔루션의 리소스를 일관되고 반복 가능한 상태로 배포할 수 있습니다.
1. Azure Resource Manager API: 파트너는 Resource Manager API를 호출하여 Resource Manager 템플릿을 배포하거나 Azure 서비스를 직접 프로비전할 수 있습니다.
1. Terraform: 파트너는 Terraform을 사용하여 Resource Manager 템플릿을 배포하거나 Azure 서비스를 직접 배포할 수 있습니다.

[이 문서의 뒷부분](#other-use-cases)에는 상업용 마켓플레이스 외부의 고객 사용량 특성에 대한 보조 사용사례가 설명되어 있습니다.

>[!IMPORTANT]
>- 고객 사용량 특성은 주로 Azure 리소스를 배포하고 관리하도록 설계된 시스템 통합업체, 관리되는 서비스 공급자 또는 도구의 작업을 추적하기 위한 것이 아닙니다.
>- 고객 사용량 특성은 새 배포용이며 이미 배포된 리소스의 추적은 지원하지 않습니다.
>- 일부 Azure 서비스는 고객 사용량 특성과 호환되지 않습니다. AKS(Azure Kubernetes Services), VM Scale Sets 및 Azure Batch에는 사용량을 적게 보고하는 알려진 문제가 있습니다.

## <a name="commercial-marketplace-azure-apps"></a>Azure 앱 상업용 마켓플레이스

상업용 마켓플레이스에 게시된 Azure 앱에서 Azure 사용량 추적은 대부분 자동으로 이루어집니다. [Marketplace Azure 앱 계획의 기술 구성](./azure-app-solution.md#define-the-technical-configuration)의 일부로 Resource Manager 템플릿을 업로드하는 경우 파트너 센터는 Azure Resource Manager에서 읽을 수 있는 추적 ID를 추가합니다.

>[!NOTE]
>애플리케이션 사용이 당사 시스템에서 정확하게 표시되도록 하려면:
>1. 리소스 종류 Microsoft.Resources/deployment에서 추적 ID를 변수로 정의하는 경우 계획의 **기술 구성** 페이지에서 파트너 센터에 표시되는 추적 ID로 변수를 바꿉니다(아래 [Resource Manager 템플릿에 GUID 추가](#add-a-guid-to-a-resource-manager-template) 참조).
>2. Resource Manager 템플릿이 고객 사용 특성 이외의 목적으로 Microsoft.Resources/deployments 유형의 리소스를 사용하는 경우 Microsoft는 귀하를 대신하여 고객 사용 특성 추적 ID를 추가할 수 없습니다. Microsoft.Resources/deployments 유형의 새 리소스를 추가하고 계획의 **기술 구성** 페이지에서 파트너 센터에 표시되는 추적 ID를 추가합니다(아래의 [Resource Manager 템플릿에 GUID 추가](#add-a-guid-to-a-resource-manager-template) 참조).

Azure Resource Manager API를 사용하는 경우 코드에서 리소스를 배포하는 것처럼 Azure Resource Manager에 전달하려면 [아래 지침](#use-resource-manager-apis) 에 따라 추적 ID를 추가해야 합니다. 이 ID는 계획의 기술 구성페이지에서 파트너 센터에 표시됩니다. 

> [!NOTE]
> 기존 Azure 앱의 경우 각 계획의 기술 구성에서 추적 ID를 업데이트하기 위해 3월 2021일에 일회성 마이그레이션이 시작됩니다. 이러한 제품의 이전 배포에 대한 사용량은 Microsoft 시스템에서 계속 추적됩니다.
>
>제품을 업데이트할 때 기본 템플릿 파일에 더 이상 **Microsoft.Resources/deployments** 리소스 유형을 추가할 필요가 없습니다.

## <a name="other-use-cases"></a>기타 사용 사례

고객 사용량 특성을 사용하여 상업용 마켓플레이스에서 사용할 수 없는 솔루션의 Azure 사용현황을 추적할 수 있습니다. 이러한 솔루션은 일반적으로 빠른 시작 리포지토리, 프라이빗 GitHub 리포지토리에 위치하거나 지속형 IP를 생성하는 1:1 고객 참여(예: 배포 가능하고 확장 가능한 앱)를 통해 고객 계약에서 제공됩니다.

몇 가지 수동 단계가 필요합니다.

1. 추적 ID로 사용할 GUID를 하나 이상 만듭니다.
1. 파트너 센터에서 이 GUID를 등록합니다.
1. 등록된 GUID를 Azure 앱 및/또는 사용자 에이전트 문자열에 추가합니다.

### <a name="create-guids"></a>GUID 만들기

파트너 센터가 상업용 마켓플레이스에서 사용자 대신 Azure 앱에 대해 만드는 추적 ID와 달리 CUA를 다르게 사용하는 경우 추적 ID로 사용할 GUID를 만들어야 합니다. GUID는 32자리의 16진수가 있는 고유 참조 식별자입니다. 추적을 위한 GUID를 만들려면 PowerShell을 통하는 등의 방식으로 GUID 생성기를 사용해야 합니다.

```powershell
[guid]::NewGuid()
```

각 제품 및 배포 채널에 대해 고유한 GUID를 만들어야 합니다. 보고를 분할하지 않으려면 제품의 여러 배포 채널에 대해 단일 GUID를 사용하도록 선택할 수 있습니다. 보고는 Microsoft 파트너 네트워크 ID 및 GUID를 통해 발생합니다.

### <a name="register-guids"></a>GUID 등록

GUID는 이후에 파트너 센터에 등록해야 파트너로 연결할 수 있습니다.

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165507)에 로그인합니다.

1. [상업용 마켓플레이스 게시자](https://go.microsoft.com/fwlink/?linkid=2165614)로 등록합니다.

1. 오른쪽 위 모서리에서 **설정**(기어 아이콘)을 선택하고 **계정 설정** 을 선택합니다.

1. **조직 프로필** > **식별자** > **추적 GUID 추가** 를 선택합니다.

1. **GUID** 상자에 추적 GUID를 입력합니다. `pid-` 접두사 없이 GUID만 입력합니다. **설명** 상자에 솔루션 이름 또는 설명을 입력합니다.

1. 여러 GUID를 등록하려면 **추적 GUID 추가** 를 다시 선택합니다. 추가 상자가 페이지에 표시됩니다.

1. **저장** 을 선택합니다.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Resource Manager 템플릿에 GUID 추가

Resource Manager 템플릿에 등록된 GUID를 추가하려면 주 템플릿 파일을 한 번만 수정하면 됩니다.

1. Resource Manager 템플릿을 엽니다.

1. 주 템플릿 파일에서 [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) 형식의 새 리소스를 추가합니다. 리소스는 **mainTemplate.json** 또는 **azuredeploy.json** 파일에만 있어야 하며, 중첩되거나 연결된 템플릿에 있으면 안됩니다.

1. 접두사 뒤에 GUID 값을 입력하여 `pid-` 리소스의 이름으로 입력합니다. 예를 들어 GUID가 eb7927c8-dd66-43e1-b0cf-c346a422063인 경우 리소스 이름은 **pid-eb7927c8-dd66-43e1-b0cf-c346a422063** 가 됩니다. 예:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. 템플릿에서 오류를 확인합니다.

1. 적절한 리포지토리에 템플릿을 다시 게시합니다.

1. [템플릿 배포에서 GUID 성공을 확인](#verify-deployments-tracked-with-a-guid)합니다.

> [!TIP]
> Resource Manager 템플릿 만들기 및 게시에 대한 자세한 내용은 [첫 번째 Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.

### <a name="verify-deployments-tracked-with-a-guid"></a>GUID로 추적되는 배포 확인

템플릿이 수정되고 테스트 배포가 실행되면 다음 PowerShell 스크립트를 사용하여 배포하고 태그를 지정한 리소스를 검색합니다.

이 스크립트를 사용하여 GUID가 Resource Manager 템플릿에 성공적으로 추가되었는지 확인할 수 있습니다. Resource Manager API 또는 Terraform 배포에는 이 스크립트가 적용되지 않습니다.

Azure에 로그인합니다. 스크립트를 실행하기 전에 확인하려는 배포가 있는 구독을 선택합니다. 배포의 구독 컨텍스트 내에서 스크립트를 실행합니다.

배포의 **GUID**(아래에서 "deploymentName"으로 칭함) 및 **resourceGroupName** 이름은 필수 매개 변수입니다.

GitHub에서 [원래의 스크립트](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1)를 가져올 수 있습니다.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>고객에게 알림

파트너는 고객 사용량 특성을 사용하는 배포에 대해 고객에게 알려야 합니다. Microsoft는 이러한 배포와 연결된 Azure 사용량을 파트너에게 보고합니다. 다음 예제에는 이러한 배포에 대해 고객에게 알리는 데 사용할 수 있는 콘텐츠가 포함되어 있습니다. 이 예제에서 \<PARTNER>는 회사 이름으로 바꿉니다. 파트너는 추적에서 고객을 제외할 수 있는 옵션을 포함하여 알림이 해당 데이터 개인 정보 및 수집 정책과 일치하는지 확인해야 합니다.

#### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager 템플릿 배포에 대한 알림

이 템플릿을 배포하면 Microsoft에서 배포된 Azure 리소스를 사용하여 \<PARTNER> 소프트웨어의 설치를 식별할 수 있습니다. Microsoft는 소프트웨어를 지원하는 데 사용되는 이러한 리소스의 상관 관계를 지정할 수 있습니다. Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 데이터는 Microsoft 개인 정보 보호 정책([https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter))에 따라 수집되고 관리됩니다.

#### <a name="notification-for-sdk-or-api-deployments"></a>SDK 또는 API 배포 알림

이 \<PARTNER> 소프트웨어를 배포하면 Microsoft에서 배포된 Azure 리소스를 사용하여 \<PARTNER> 소프트웨어의 설치를 식별할 수 있습니다. Microsoft는 소프트웨어를 지원하는 데 사용되는 이러한 리소스의 상관 관계를 지정할 수 있습니다. Microsoft는 제품에 최상의 환경을 제공하고 비즈니스를 운영하기 위해 이 정보를 수집합니다. 데이터는 Microsoft 개인 정보 보호 정책([https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter))에 따라 수집되고 관리됩니다.

## <a name="use-resource-manager-apis"></a>Resource Manager API 사용

파트너가 Resource Manager REST API를 직접 호출하여 Azure 서비스를 배포하는 방법을 선호하는 경우도 있습니다. Azure는 이러한 호출을 사용하도록 설정하는 [여러 SDK를 지원](../index.yml?pivot=sdkstools)합니다. SDK 중 하나를 사용하거나, REST API를 직접 호출하여 리소스를 배포할 수 있습니다.

고객 사용량 추적을 사용하도록 설정하려면 API 호출을 설계할 때 요청의 사용자 에이전트 헤더에 추적 ID를 포함시킵니다. `pid-` 접두사로 문자열의 형식을 지정합니다. 예:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> 상업용 마켓플레이스에서 Azure 앱과 함께 Resource Manager API를 사용하는 경우 파트너 센터에 제공된 추적 ID를 사용합니다. GUID를 사용하지 마십시오.

다양한 SDK가 Resource Manager API와 다르게 상호 작용하고 코드에는 약간의 변경이 있어야 합니다. 아래 예제에서는 GUID를 사용하는 비상업용 마켓플레이스 접근 방식을 소개하고 다양한 인기 Azure SDK를 다룹니다.

#### <a name="example-python-sdk"></a>예: Python SDK

Python의 경우 **config** 특성을 사용합니다. 이 특성은 UserAgent에만 추가할 수 있습니다. 예:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> 각 클라이언트에 대한 특성을 추가합니다. 글로벌 정적 구성이 없습니다. 모든 클라이언트에서 추적할 수 있도록 클라이언트 팩터리에 태그를 지정할 수 있습니다. 자세한 내용은 [GitHub의 클라이언트 팩터리 샘플](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)을 참조하세요.

#### <a name="example-net-sdk"></a>예: .NET SDK

.NET의 경우 사용자 에이전트를 설정해야 합니다. [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent) 라이브러리를 사용하여 다음 코드를 사용하여 사용자 에이전트를 설정합니다(C# 예제).

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>예: Azure PowerShell

Azure PowerShell을 통해 리소스를 배포하는 경우 다음 방법을 사용하여 GUID를 추가합니다.

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>예: Azure CLI

Azure CLI를 사용하여 GUID를 추가하는 경우 **AZURE_HTTP_USER_AGENT** 스크립트 범위 내에 환경 변수를 설정합니다. 셸 범위에 대한 글로벌 변수를 설정할 수도 있습니다.

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

자세한 내용은 [Go용 Azure SDK](/azure/developer/go/)를 참조하세요.

## <a name="use-terraform"></a>Terraform 사용

Azure 공급자의 1.21.0 릴리스 [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)를 통해 Terraform에 대한 지원이 제공됩니다. 이 지원은 Terraform을 통해 솔루션을 배포하는 모든 파트너와 Azure 공급자(버전 1.21.0 이상)가 배포하고 사용량을 측정하는 모든 리소스에 적용됩니다.

Terraform용 Azure 공급자는 솔루션에 사용되는 추적 GUID를 지정하는 [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id)라는 새로운 선택적 필드를 추가했습니다. 이 필드의 값은 *ARM_PARTNER_ID* 환경 변수에서 소싱될 수도 있습니다.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
*partner_id* 의 값을 등록된 GUID로 설정합니다. GUID에 "pid-"를 접두사로 사용하지 않고 실제 GUID로 설정합니다.

> [!IMPORTANT]
> 상업용 마켓플레이스에서 Azure 앱과 함께 Terraform를 사용하는 경우 파트너 센터에 제공된 전체 추적 ID를 사용합니다. GUID를 사용하지 마십시오.

## <a name="get-support"></a>지원 받기

[파트너 센터에서 상업용 마켓플레이스 프로그램에 대한 지원](support.md)에서 상업용 마켓플레이스의 지원 옵션에 대해 알아봅니다.

### <a name="how-to-submit-a-technical-consultation-request"></a>기술 상담 요청을 제출하는 방법

1. [파트너 기술 서비스](https://aka.ms/TechnicalJourney)을 방문하세요.
1. **클라우드 인프라 및 관리** 를 선택하면 기술 경험을 볼 수 있습니다.
1. **배포 서비스** > **요청 제출** 을 선택합니다.
1. MSA(MPN 계정)나 AAD(파트너 대시보드 계정)를 사용하여 로그인합니다.
1. 양식이 열리면 연락처 정보를 작성하거나 검토합니다. 상담 세부 정보는 미리 채워져 있거나 드롭다운 옵션이 있을 수 있습니다.
1. 제목과 문제에 대한 자세한 설명을 입력합니다.
1. **제출** 을 선택합니다.

[기술 예약 판매 및 배포 서비스 사용](/partner-center/technical-benefits)에서 스크린샷과 함께 단계별 지침을 확인하세요.

Microsoft 파트너 기술 컨설턴트로부터 요구 사항의 범위를 확인하는 전화 약속을 잡기 위한 연락을 받게 됩니다.

## <a name="report"></a>보고서
현재 ISV 파트너의 경우 고객 사용량 특성을 통해 추적되는 Azure 사용량에 대한 보고를 사용할 수 없습니다. 고객 사용량 특성을 다루는 상업용 마켓플레이스 프로그램에 보고 기능을 추가하는 것은 2021년 하반기 목표입니다.

## <a name="faq"></a>FAQ

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>추적 ID는 추가된 후에 변경할 수 있나요?

상업용 마켓플레이스의 Azure 앱에 대한 추적 ID는 파트너 센터를 통해 자동으로 관리됩니다. 그러나 고객은 템플릿을 다운로드하고 추적 ID를 변경하거나 제거할 수 있습니다. 파트너는 제거 또는 편집을 방지하기 위해 추적 ID의 역할을 고객에게 사전에 설명해야 합니다. 추적 ID를 변경하면 새 배포 및 리소스에만 영향을 주며 기존 배포에는 영향을 주지 않습니다.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>GitHub 등의 타사 리포지토리에서 배포된 템플릿을 추적할 수 있나요?

예, 템플릿을 배포할 때 추적 ID가 있는 한 사용량이 추적됩니다. Microsoft 이외의 리포지토리에서 배포된 게시자와 사용자 간의 연결을 유지하려면 먼저 Azure Portal의 상업용 마켓플레이스 목록에서 게시된 템플릿(추적 ID 포함)의 복사본을 다운로드합니다. 해당 버전을 GitHub나 타사 리포지토리에 게시합니다.

템플릿이 상업용 마켓플레이스에 나열되어 있지 않고 등록된 GUID를 포함하는 경우 GitHub 또는 다른 타사 리포지토리에 게시하는 버전에 GUID가 있는지 확인합니다.

#### <a name="does-the-customer-receive-reporting-as-well"></a>고객도 보고를 받나요?

아니요. 고객은 Azure Portal 내에서 모든 리소스 또는 리소스 그룹의 사용량을 추적할 수 있습니다. 고객은 CUA 추적 ID별로 분류된 사용량을 볼 수 없습니다.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>고객 사용량 특성이 사용자의 DPOR(디지털 공식 파트너) 또는 PAL(파트너 관리자 링크)과 유사합니까?

고객 사용량 특성은 Azure 사용량을 파트너의 반복 가능하고 배포 가능한 IP와 연결하여 배포 시 연결을 형성하는 메커니즘입니다. DPOR 및 PAL은 고객이 참여하는 시간 동안 컨설팅(시스템 통합자) 또는 관리(관리되는 서비스 공급자) 파트너를 고객의 관련 Azure 공간과 연결하는 데 사용됩니다.
