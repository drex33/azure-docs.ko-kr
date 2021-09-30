---
title: Azure Automation에서 연결 관리
description: 이 문서에서는 외부 서비스 또는 애플리케이션으로의 Azure Automation 연결을 관리하고 Runbook에서 사용하는 방법을 설명합니다.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4e96045ec49a337d317583826cb3f50c6161f39
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276060"
---
# <a name="manage-connections-in-azure-automation"></a>Azure Automation에서 연결 관리

Azure Automation 연결 자산은 다음과 같은 정보를 포함합니다. 이 정보는 Runbook이나 DSC 구성에서 외부 서비스 또는 애플리케이션으로의 연결에 필요합니다. 

* 인증에 필요한 정보(사용자 이름, 암호 등)
* 연결 정보(URL, 포트 등)

연결 자산은 특정 애플리케이션으로 연결하는 데 필요한 모든 속성을 한곳에서 관리해 주므로 변수를 여러 개 만들 필요가 없습니다. 한곳에서 연결의 값을 편집할 수 있고 단일 매개 변수에서 연결 이름을 Runbook이나 DSC 구성으로 전달할 수 있습니다. Runbook이나 구성은 내부 `Get-AutomationConnection` cmdlet을 사용하여 연결의 속성에 액세스합니다.

연결을 만들 때는 연결 형식을 지정해야 합니다. 연결 형식은 속성 집합을 정의하는 템플릿입니다. 메타데이터 파일로 통합 모듈을 사용하여 Azure Automation에 연결 형식을 추가할 수 있습니다. 통합 모듈이 연결 형식을 포함하고 Automation 계정으로 가져와진 경우에는 [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100))를 사용하여 연결 형식을 만들 수도 있습니다. 

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. Azure Automation은 시스템 관리 키 자격 증명 모음에 키를 저장합니다. 보안 자산을 저장하기 전에 Automation이 키 자격 증명 모음에서 키를 로드한 다음, 자산을 암호화하는 데 사용합니다. 

## <a name="connection-types"></a>연결 형식

Azure Automation은 다음과 같은 기본 제공 연결 형식을 제공합니다.

* `Azure` - 클래식 리소스를 관리하는 데 사용되는 연결을 나타냅니다.
* `AzureServicePrincipal` - Azure 실행 계정에서 사용하는 연결을 나타냅니다.
* `AzureClassicCertificate` - 클래식 Azure 실행 계정에서 사용하는 연결을 나타냅니다.

대부분의 경우 연결 리소스는 [실행 계정](automation-security-overview.md)을 만들 때 생성되므로 별도로 생성할 필요가 없습니다.

## <a name="powershell-cmdlets-to-access-connections"></a>연결에 액세스하는 데 사용되는 PowerShell cmdlet

다음 표에 나와 있는 cmdlet은 PowerShell을 사용하여 Automation 연결을 만들고 관리합니다. Az 모듈의 일부로 제공됩니다.

|Cmdlet|Description|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|연결에 대한 정보를 검색합니다.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|새 연결을 만듭니다.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|기존 연결을 제거합니다.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|기존 연결의 특정 필드 값을 설정합니다.|

## <a name="internal-cmdlets-to-access-connections"></a>연결에 액세스하는 데 사용되는 내부 cmdlet

다음 표에 나와 있는 내부 cmdlet은 Runbook 및 DSC 구성에서 연결에 액세스하는 데 사용됩니다. 이 cmdlet은 전역 모듈 `Orchestrator.AssetManagement.Cmdlets`와 함께 제공됩니다. 자세한 내용은 [내부 cmdlet](shared-resources/modules.md#internal-cmdlets)을 참조하세요.

|내부 cmdlet|Description|
|---|---|
|`Get-AutomationConnection` | 연결의 여러 필드 값을 검색하여 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)로 반환합니다. 이 해시 테이블을 Runbook또는 DSC 구성의 적절한 명령에서 사용할 수 있습니다.|

>[!NOTE]
>변수를 `Get-AutomationConnection`의 `Name` 매개 변수와 함께 사용하면 안 됩니다. 변수를 이렇게 사용하면 디자인 타임에 Runbook 또는 DSC 구성과 연결 자산 간의 종속성 검색이 복잡해질 수 있습니다.

## <a name="python-functions-to-access-connections"></a>연결에 액세스하는 데 사용되는 Python 함수

다음 표에 나와 있는 함수는 Python 2 및 3 Runbook의 연결에 액세스하는 데 사용됩니다. Python 3 Runbook은 현재 미리 보기로 제공됩니다.

| 함수 | Description |
|:---|:---|
| `automationassets.get_automation_connection` | 연결을 검색합니다. 연결의 속성이 있는 사전을 반환합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 `automationassets` 모듈을 가져와야 합니다.

## <a name="create-a-new-connection"></a>새 연결 만들기

### <a name="create-a-new-connection-with-the-azure-portal"></a>Azure Portal을 사용하여 새 연결 만들기

Azure Portal을 사용하여 새 연결을 만들려면:

1. Automation 계정에서 **공유 리소스** 아래의 **연결** 을 클릭합니다.
2. 연결 페이지에서 **+ 연결 추가** 를 클릭합니다.
4. 새 연결 창의 **형식** 필드에서 만들려는 연결 형식을 선택합니다. 옵션에는 `Azure`, `AzureServicePrincipal` 및 `AzureClassicCertificate`이 있습니다. 
5. 양식에 선택한 연결 형식의 속성이 표시됩니다. 양식을 완료하고 **만들기** 를 클릭하여 새 연결을 저장합니다.

### <a name="create-a-new-connection-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 연결 만들기

Windows PowerShell에서 `New-AzAutomationConnection` cmdlet을 사용하여 새 연결을 만듭니다. 이 cmdlet에는 연결 형식으로 정의되는 각 속성의 값을 정의하는 해시 테이블이 필요한 `ConnectionFieldValues` 매개 변수가 있습니다.

다음 예제 명령을 포털에서 실행 계정을 만들어서 새 연결 자산을 만드는 방법의 대안으로 사용할 수 있습니다.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Automation 계정을 만들면 계정에 기본적으로 여러 개의 전역 모듈과 `AzureRunAsConnection` 연결 자산을 만들기 위한 `AzureServicePrincipal` 연결 형식이 포함됩니다. 새 연결 자산을 만들어 다른 인증 방법으로 서비스 또는 애플리케이션에 연결하려고 시도하면 연결 형식이 아직 Automation 계정에 정의되어 있지 않기 때문에 작업에 실패하게 됩니다. 사용자 지정 모듈을 위해 자체 연결 형식을 만드는 방법에 대한 자세한 내용은 [연결 형식 추가](#add-a-connection-type)를 참조하세요.

## <a name="add-a-connection-type"></a>연결 형식 추가

Runbook 또는 DSC 구성이 외부 서비스로 연결되는 경우 통합 모듈이라고 하는 [사용자 지정 모듈](shared-resources/modules.md#custom-modules)에 연결 형식을 정의해야 합니다. 이 모듈에는 연결 형식 속성을 지정하는 메타데이터 파일이 있습니다. 이 파일은 압축된 **.zip** 파일의 모듈 폴더 안에 있는 **&lt;ModuleName&gt;-Automation.json** 입니다. 이 파일에는 모듈이 나타내는 시스템 또는 서비스에 연결하는 데 필요한 연결 필드가 있습니다. 이 파일을 사용하여 필드 이름, 암호화 상태 및 연결 형식의 선택적 상태를 설정할 수 있습니다. 

다음 예제는 **.json** 파일 형식의 템플릿으로, `MyModuleConnection`이라는 사용자 지정 연결 형식의 사용자 이름 및 암호 속성을 정의합니다.

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook 또는 DSC 구성에서 연결 가져오기

내부 `Get-AutomationConnection` cmdlet을 사용하여 Runbook 또는 DSC 구성에서 연결을 검색합니다. 이 cmdlet은 연결에 대한 정보가 아니라 연결 값을 검색하므로 `Get-AzAutomationConnection` cmdlet보다 선호됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 예제에서는 실행 계정을 사용하여 Runbook에서 Azure Resource Manager 리소스를 인증하는 방법을 보여 줍니다. 여기서는 인증서 기반 서비스 주체를 참조하는 실행 계정을 나타내는 연결 자산을 사용합니다.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

다음 예제에서는 Python 2 및 3 Runbook에서 실행 연결을 사용하여 인증하는 방법을 보여 줍니다.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>그래픽 Runbook 예

내부 `Get-AutomationConnection` cmdlet의 활동을 그래픽 Runbook에 추가할 수 있습니다. 그래픽 편집기의 라이브러리 창에서 연결을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가** 를 선택합니다.

![캔버스에 추가](media/automation-connections/connection-add-canvas.png)

다음 이미지에서는 그래픽 Runbook에서 연결 개체를 사용하는 예제를 보여 줍니다. 이 예제에서는 인증을 위해 연결 개체를 사용하는 `Get RunAs Connection` 활동에 대해 `Constant value` 데이터 세트를 사용합니다. `ServicePrincipalCertificate` 매개 변수에는 단일 개체가 필요하기 때문에 여기서는 [파이프라인 링크](automation-graphical-authoring-intro.md#use-links-for-workflow)를 사용합니다.

![연결 가져오기](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>다음 단계

* 연결에 액세스하는 데 사용되는 cmdlet에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.
* Runbook에 대한 일반적인 정보는 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)을 참조하세요.
* DSC 구성에 대한 자세한 내용은 [State Configuration 개요](automation-dsc-overview.md)를 참조하세요.
