---
title: 들어오는 요청에 대해 필요한 최소 버전의 TLS(전송 계층 보안)를 적용합니다.
titleSuffix: Azure Storage
description: Azure Storage에 대한 요청을 수행하는 클라이언트에 대해 최소 버전의 TLS(전송 계층 보안)가 필요하도록 스토리지 계정을 구성합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/07/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c69e8a5030717dd76a887968f40034595b9cd939
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536265"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>스토리지 계정에 대한 요청에 대해 필요한 최소 버전의 TLS(전송 계층 보안)를 적용합니다.

클라이언트 애플리케이션과 Azure Storage 계정 간의 통신은 TLS(전송 계층 보안)를 사용하여 암호화됩니다. TLS는 인터넷을 통해 클라이언트와 서비스 간에 개인 정보 보호 및 데이터 무결성을 보장하는 표준 암호화 프로토콜입니다. TLS에 대한 자세한 내용은 [전송 계층 보안](https://en.wikipedia.org/wiki/Transport_Layer_Security)을 참조하세요.

현재 Azure Storage는 세 가지 버전의 TLS 프로토콜(1.0, 1.1 및 1.2)을 지원합니다. Azure storage는 퍼블릭 HTTPS 엔드포인트에서 TLS 1.2를 사용하지만, TLS 1.0 및 TLS 1.1은 이전 버전과의 호환성을 위해 계속 지원됩니다.

Azure Storage 계정은 클라이언트에서 가장 오래된 TLS 버전인 TLS 1.0 이상을 사용하여 데이터를 보내고 받을 수 있도록 허용합니다. 보다 엄격한 보안 조치를 적용하기 위해 클라이언트가 최신 버전의 TLS를 사용하여 데이터를 보내고 받도록 스토리지 계정을 구성할 수 있습니다. 스토리지 계정에 최소 버전의 TLS가 필요한 경우 이전 버전을 사용하여 수행한 요청은 실패합니다.

이 문서에서는 스토리지 계정에 대한 보안 TLS를 지속적으로 관리하기 위해 DRAG(검색-수정-감사-거버넌스) 프레임워크를 사용하는 방법을 설명합니다.

클라이언트 애플리케이션에서 요청을 보낼 때 특정 버전의 TLS를 지정하는 방법에 대한 자세한 내용은 [클라이언트 애플리케이션에 대한 TLS(전송 계층 보안) 구성](transport-layer-security-configure-client-version.md)을 참조하세요.

## <a name="detect-the-tls-version-used-by-client-applications"></a>클라이언트 애플리케이션에서 사용하는 TLS 버전 검색

스토리지 계정에 최소 TLS 버전을 적용하면 이전 버전의 TLS를 사용하여 데이터를 전송하는 클라이언트의 요청을 거부할 위험이 있습니다. 최소 TLS 버전을 구성하는 것이 클라이언트 애플리케이션에 얼마나 영향을 줄 수 있는지 파악하기 위해 Microsoft에서 권장하는 방법은 시간 간격 후에 Azure Storage 계정에 대한 로깅을 사용하도록 설정하고 로그를 분석하여 사용 중인 TLS 클라이언트 애플리케이션 버전을 탐지하는 것입니다.

Azure Storage 계정에 요청을 로깅하고 클라이언트에서 사용하는 TLS 버전을 확인하기 위해 Azure Monitor(미리 보기)에서 Azure Storage 로깅을 사용할 수 있습니다. 자세한 내용은 [Azure Storage 모니터링](../blobs/monitor-blob-storage.md)을 참조하세요.

Azure Monitor의 Azure Storage 로깅은 로그 쿼리를 사용하여 로그 데이터를 분석하도록 지원합니다. 로그를 쿼리하려면 Azure Log Analytics 작업 영역을 사용사면 됩니다. 로그 쿼리에 대해 자세히 알아보려면 [자습서: Log Analytics 쿼리 시작하기](../../azure-monitor/logs/log-analytics-tutorial.md)를 참조하세요.

Azure Monitor를 사용하여 Azure Storage 데이터를 로깅하고 Azure Log Analytics를 사용하여 분석하려면 먼저 요청 유형과 관련 데이터를 로깅할 스토리지 서비스를 나타내는 진단 설정을 만들어야 합니다. Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기에서는 Blob(Azure Data Lake Storage Gen2 포함), 파일, 큐 및 테이블에 대한 로그를 사용하도록 설정합니다. Azure Portal에서 진단 설정을 만들려면 다음 단계를 수행합니다.

1. Azure Storage 계정을 포함하는 구독에 새 Log Analytics 작업 영역을 만듭니다. 스토리지 계정에 대한 로깅을 구성하면 Log Analytics 작업 영역에서 로그를 사용할 수 있습니다. 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.
1. Azure Portal의 스토리지 계정으로 이동합니다.
1. 모니터링 섹션에서 **진단 설정(미리 보기)** 을 선택합니다.
1. 요청을 로그할 Azure Storage 서비스를 선택합니다. 예를 들어 Blob Storage에 요청을 로그하려면 **Blob** 을 선택합니다.
1. **진단 설정 추가** 를 선택합니다.
1. 진단 설정의 이름을 제공합니다.
1. **범주 세부 정보** 의 **로그** 섹션에서 로깅할 요청 유형을 선택합니다. 읽기, 쓰기 및 삭제 요청을 로깅할 수 있습니다. 예를 들어 **StorageRead** 및 **StorageWrite** 를 선택하면 선택한 서비스에 대한 읽기 및 쓰기 요청이 로깅됩니다.
1. **대상 세부 정보** 에서 **Log Analytics로 보내기** 를 선택합니다. 다음 이미지와 같이, 구독 및 이전에 만든 Log Analytics 작업 영역을 선택합니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="로깅 요청에 대한 진단 설정을 만드는 방법을 보여주는 스크린샷":::

진단 설정을 만든 후에는 이 설정에 따라 스토리지 계정에 대한 요청이 로그됩니다. 자세한 내용은 [Azure에서 리소스 로그 및 메트릭을 수집하기 위한 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

Azure Monitor의 Azure Storage 로그에서 사용할 수 있는 필드에 대한 참조는 [리소스 로그(미리 보기)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)를 참조하세요.

### <a name="query-logged-requests-by-tls-version"></a>TLS 버전에 의해 로깅된 요청 쿼리

Azure Monitor의 Azure Storage 로그에는 스토리지 계정에 요청을 보내는 데 사용되는 TLS 버전이 포함됩니다. **TlsVersion** 속성을 사용하여 로깅된 요청의 TLS 버전을 확인합니다.

지난 7일간 여러 버전의 TLS를 사용하여 Blob 스토리지에 대해 수행된 요청 수를 확인하려면 Log Analytics 작업 영역을 엽니다. 그런 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

결과에는 각 TLS 버전을 사용하여 수행한 요청 수가 표시됩니다.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="TLS 버전을 반환하는 Log Analytics 쿼리 결과를 보여 주는 스크린샷":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>호출자 IP 주소 및 사용자 에이전트 헤더에 의해 로깅된 요청 쿼리

Azure Monitor의 Azure Storage 로그에는 스토리지 계정에 액세스한 클라이언트 애플리케이션을 평가하는 데 도움이 되는 호출자 IP 주소 및 사용자 에이전트 헤더도 포함되어 있습니다. 해당 값을 분석하여 최신 버전의 TLS를 사용하도록 클라이언트 애플리케이션을 업데이트해야 하는지 또는 최소 TLS 버전으로 전송되지 않은 클라이언트의 요청에 실패해도 문제가 없는지를 결정할 수 있습니다.

지난 7일간 TLS 1.2 이전 버전의 TLS를 사용하여 요청을 수행한 클라이언트를 확인하려면 다음 쿼리를 새 로그 쿼리에 붙여넣고 실행합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>최소 버전의 TLS로 보안 위험 해결

이전 버전의 TLS를 사용하는 클라이언트의 트래픽이 최소라고 확신하거나 이전 버전의 TLS를 사용하여 수행한 요청에 실패해도 문제가 없는 경우 스토리지 계정에 최소 TLS 버전을 적용할 수 있습니다. 클라이언트에서 스토리지 계정에 대한 요청을 수행하기 위해 최소 버전의 TLS를 사용하도록 요구하는 것은 데이터에 대한 보안 위험을 최소화하기 위한 전략의 일부입니다.

> [!IMPORTANT]
> Azure Storage에 연결하는 서비스를 사용하는 경우 스토리지 계정에 필요한 최소 버전을 설정하기 전에 해당 서비스에서 적절한 버전의 TLS를 사용하여 Azure Storage에 요청을 보내는지 확인합니다.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>스토리지 계정에 대한 최소 TLS 버전 구성

스토리지 계정에 대한 최소 TLS 버전을 구성하려면 계정에 대한 **MinimumTlsVersion** 을 설정합니다. Azure Resource Manager 배포 모델을 사용하여 만든 모든 스토리지 계정에 이 속성을 사용할 수 있습니다. Azure Resource Manager 배포 모델에 대한 자세한 내용은 [스토리지 계정 개요](storage-account-overview.md)를 참조하세요.

**MinimumTlsVersion** 속성의 기본값은 설정 방법에 따라 다릅니다. Azure Portal을 사용하여 스토리지 계정을 만드는 경우 최소 TLS 버전은 기본적으로 1.2로 설정됩니다. PowerShell, Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 스토리지 계정을 만들 때 **MinimumTlsVersion** 속성은 기본적으로 설정되지 않으며 명시적으로 설정할 때까지 값을 반환하지 않습니다.

**MinimumTlsVersion** 속성이 설정되지 않은 경우 해당 값은 컨텍스트에 따라 **null** 또는 빈 문자열로 표시될 수 있습니다. 속성이 설정되지 않은 경우 스토리지 계정은 TLS 버전 1.0 이상을 사용하여 전송된 요청을 허용합니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Portal을 사용하여 스토리지 계정을 만드는 경우 최소 TLS 버전은 기본적으로 1.2로 설정됩니다.

Azure Portal을 사용하여 기존 스토리지 계정에 대한 최소 TLS 버전을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 에서 **구성** 을 선택합니다.
1. **최소 TLS 버전** 에서 드롭다운을 사용하여 이 스토리지 계정의 데이터에 액세스하는 데 필요한 최소 버전의 TLS를 선택합니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Azure Portal에서 TLS의 최소 버전을 구성하는 방법을 보여 주는 스크린샷" lightbox="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정에 대한 최소 TLS 버전을 구성하려면 [Azure PowerShell 버전 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 이상을 설치합니다. 다음으로 새 스토리지 계정 또는 기존 스토리지 계정에 대해 **MinimumTLSVersion** 속성을 구성합니다. **MinimumTlsVersion** 에 유효한 값은 `TLS1_0`, `TLS1_1` 및 `TLS1_2`입니다.

다음 예에서는 스토리지 계정을 만들고 **MinimumTLSVersion** 을 TLS 1.1로 설정한 다음, 계정을 업데이트하고 **MinimumTLSVersion** 을 TLS 1.2로 설정합니다. 또한 이 예에서는 각 사례에서 속성 값을 검색합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대한 최소 TLS 버전을 구성하려면 Azure CLI 버전 2.9.0 이상을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 다음으로 새 스토리지 계정 또는 기존 스토리지 계정에 대해 **minimumTlsVersion** 속성을 구성합니다. **minimumTlsVersion** 에 유효한 값은 `TLS1_0`, `TLS1_1` 및 `TLS1_2`입니다.

다음 예에서는 스토리지 계정을 만들고 **minimumTLSVersion** 을 TLS 1.1로 설정합니다. 그런 다음 계정을 업데이트하고 **minimumTLSVersion** 속성을 TLS 1.2로 설정합니다. 또한 이 예에서는 각 사례에서 속성 값을 검색합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[템플릿](#tab/template)

템플릿을 사용하여 스토리지 계정에 대한 최소 TLS 버전을 구성하려면 `TLS1_0` `TLS1_1` 또는 `TLS1_2`로 설정된 **MinimumTLSVersion** 속성의 템플릿을 만듭니다. 다음 단계에서는 Azure Portal에서 템플릿을 만드는 방법을 설명합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.
1. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
1. **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)(미리 보기)** 를 선택하고 **만들기** 를 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣어 새 계정을 만들고 최소 TLS 버전을 TLS 1.2로 설정합니다. 꺾쇠괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. 템플릿을 저장하는 경우
1. 리소스 그룹 매개 변수를 지정한 다음, **검토 + 만들기** 단추를 선택하여 템플릿을 배포하고 **MinimumTLSVersion** 속성이 구성된 스토리지 계정을 만듭니다.

---

> [!NOTE]
> 스토리지 계정에 대한 최소 TLS 버전을 업데이트한 후에는 변경이 완전히 전파되기까지 최대 30초가 걸릴 수 있습니다.

최소 TLS 버전을 구성하려면 Azure Storage Resource Provider의 버전 2019-04-01 이상이 필요합니다. 자세한 내용은 [Azure Storage Resource Provider REST API](/rest/api/storagerp/)를 참조하세요.

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>여러 계정에 필요한 최소 TLS 버전 확인

최적의 성능을 가진 스토리지 계정 집합에서 필요한 최소 TLS 버전을 확인하기 위해 Azure Portal에서 Azure Resource Graph Explorer를 사용할 수 있습니다. Resource Graph Explorer를 사용하는 방법에 대해 자세히 알아보려면 [빠른 시작: Azure Resource Graph Explorer를 사용하여 첫 번째 Resource Graph 쿼리 실행](../../governance/resource-graph/first-query-portal.md)을 참조하세요.

Resource Graph Explorer에서 다음 쿼리를 실행하면 스토리지 계정 목록이 반환되고 각 계정에 대한 최소 TLS 버전이 표시됩니다.

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>클라이언트에서 최소 TLS 버전 테스트

스토리지 계정에 필요한 최소 TLS 버전이 이전 버전의 호출을 금지하는지 테스트하기 위해 이전 버전의 TLS를 사용하도록 클라이언트를 구성할 수 있습니다. 특정 버전의 TLS를 사용하도록 클라이언트를 구성하는 방법에 대한 자세한 내용은 [클라이언트 애플리케이션에 대한 TLS(전송 계층 보안) 구성](transport-layer-security-configure-client-version.md)을 참조하세요.

클라이언트가 계정에 대해 구성된 최소 TLS 버전을 충족하지 않는 TLS 버전을 사용하여 스토리지 계정에 액세스하는 경우 Azure Storage는 오류 코드 400 오류(잘못된 요청)를 반환하고 사용된 TLS 버전이 이 스토리지 계정에 대해 요청을 수행하는 데 허용되지 않음을 나타내는 메시지를 반환합니다.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy를 사용하여 규정 준수 감사

스토리지 계정이 많은 경우 감사를 수행하여 조직에서 요구하는 최소 버전의 TLS에 대해 모든 계정이 구성되어 있는지 확인하는 것이 좋습니다. 규정 준수를 위해 스토리지 계정 집합을 감사하려면 Azure Policy를 사용합니다. Azure Policy는 Azure 리소스에 규칙을 적용하는 정책을 만들고 할당하고 관리하는 데 사용할 수 있는 서비스입니다. Azure Policy는 해당 리소스 가 회사 표준 및 서비스 수준 계약을 준수하도록 유지하는 데 도움이 됩니다. 자세한 내용은 [Azure Policy 개요](../../governance/policy/overview.md)를 참조하세요.

### <a name="create-a-policy-with-an-audit-effect"></a>감사 효과를 사용하여 정책 만들기

Azure Policy는 리소스에 대해 정책 규칙을 평가할 때 발생하는 작업을 결정하는 효과를 지원합니다. 감사 효과는 리소스가 정책을 준수하지 않는 경우 경고를 생성하지만 요청을 중지하지는 않습니다. 효과에 대한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조하세요.

Azure Portal에서 최소 TLS 버전에 대한 감사 효과를 사용하여 정책을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **작성** 섹션에서 **정의** 를 선택합니다.
1. **정책 정의 추가** 를 선택하여 새 정책 정의를 만듭니다.
1. **정의 위치** 필드의 경우 **자세히** 단추를 선택하여 감사 정책 리소스가 있는 위치를 지정합니다.
1. 정책의 이름을 지정합니다. 설명 및 범주를 선택적으로 지정할 수 있습니다.
1. **정책 규칙** 에서 다음 정책 정의를 **policyRule** 섹션에 추가합니다.

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
      }
    }
    ```

1. 해당 정책을 저장합니다.

### <a name="assign-the-policy"></a>정책 할당

다음으로 리소스에 정책을 할당합니다. 정책의 범위는 해당 리소스 및 그 아래에 있는 모든 리소스에 해당합니다. 정책 할당에 대한 자세한 내용은 [Azure Policy 할당 구조](../../governance/policy/concepts/assignment-structure.md)를 참조하세요.

Azure Portal에서 정책을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **작성** 섹션에서 **할당** 을 선택합니다.
1. **정책 할당** 을 선택하여 새 정책 할당을 만듭니다.
1. **범위** 필드에서 정책 할당의 범위를 선택합니다.
1. **정책 정의** 필드에서 **자세히** 단추를 선택한 다음, 이전 섹션에서 정의한 정책을 목록에서 선택합니다.
1. 정책 할당의 이름을 제공합니다. 설명은 선택 사항입니다.
1. **정책 적용** 을 *사용 가능* 으로 유지합니다. 이 설정은 감사 정책에 영향을 주지 않습니다.
1. **검토 + 만들기** 를 선택하여 할당을 만듭니다.

### <a name="view-compliance-report"></a>규정 준수 보고서 보기

정책을 할당한 후에는 규정 준수 보고서를 볼 수 있습니다. 감사 정책에 대한 규정 준수 보고서는 정책을 준수하지 않는 스토리지 계정에 대한 정보를 제공합니다. 자세한 내용은 [정책 규정 준수 데이터 가져오기](../../governance/policy/how-to/get-compliance-data.md)를 참조하세요.

정책 할당을 만든 후 규정 준수 보고서를 사용할 수 있게 되는 데 몇 분 정도 걸릴 수 있습니다.

Azure Portal에서 규정 준수 보고서를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동합니다.
1. **규정 준수** 를 선택합니다.
1. 이전 단계에서 만든 정책 할당의 이름에 대한 결과를 필터링합니다. 이 보고서에는 정책을 준수하지 않는 리소스의 수가 표시됩니다.
1. 정책을 준수하지 않는 스토리지 계정 목록을 포함하여 추가 세부 정보에 대해 보고서를 드릴다운할 수 있습니다.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="최소 TLS 버전의 감사 정책에 대한 규정 준수 보고서를 보여 주는 스크린샷":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Azure Policy를 사용하여 최소 TLS 버전을 적용합니다.

Azure Policy는 Azure 리소스가 요구 사항 및 표준을 준수하도록 하여 클라우드 거버넌스를 지원합니다. 조직의 스토리지 계정에 대한 최소 TLS 버전 요구 사항을 적용하기 위해 정책에서 지정하는 것보다 이전 버전의 TLS로 최소 TLS 요구 사항을 설정하는 새 스토리지 계정을 만들지 못하도록 하는 정책을 만들 수 있습니다. 또한 이 정책은 해당 계정에 대한 최소 TLS 버전 설정이 정책을 준수하지 않는 경우 기존 계정에 대한 모든 구성 변경을 방지합니다.

적용 정책은 거부 효과를 사용하여 최소 TLS 버전이 조직의 표준을 더 이상 준수하지 않도록 스토리지 계정을 만들거나 수정하는 요청을 방지합니다. 효과에 대한 자세한 내용은 [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)를 참조하세요.

TLS 1.2 이전의 최소 TLS 버전에 대해 거부 효과가 적용된 정책을 만들려면 [Azure Policy를 사용하여 규정 준수 감사](#use-azure-policy-to-audit-for-compliance)에 설명된 것과 동일한 단계를 수행하지만 정책 정의의 **policyRule** 섹션에서 다음 JSON을 제공합니다.

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

거부 효과를 사용하여 정책을 만들고 범위에 할당한 후 사용자는 1.2 이전의 최소 TLS 버전으로 스토리지 계정을 만들 수 없습니다. 사용자는 현재 1.2 이전의 최소 TLS 버전을 필요로 하는 기존 스토리지 계정의 구성을 변경할 수 없습니다. 그렇게 하면 오류가 발생합니다. 계정 만들기 또는 구성을 계속하려면 스토리지 계정에 필요한 최소 TLS 버전을 1.2로 설정해야 합니다.

다음 이미지는 거부 효과가 있는 정책에서 최소 TLS 버전을 TLS 1.2로 설정해야 하는 경우 최소 TLS 버전을 TLS 1.0(새 계정에 대한 기본값)으로 설정하여 스토리지 계정을 만들려고 하면 발생하는 오류를 보여 줍니다.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="정책을 위반하여 스토리지 계정을 만들 때 발생하는 오류를 보여 주는 스크린샷":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>최소 버전의 TLS를 요구하는 데 필요한 권한

스토리지 계정에 대한 **MinimumTlsVersion** 속성을 설정하려면 사용자에게 스토리지 계정을 만들고 관리할 수 있는 권한이 있어야 합니다. 해당 권한을 제공하는 Azure RBAC(역할 기반 액세스 제어) 역할에는 **Microsoft.Storage/storageAccounts/write** 또는 **Microsoft.Storage/storageAccounts/\*** 작업이 포함됩니다. 이 작업이 포함된 기본 제공 역할은 다음과 같습니다.

- Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

이러한 역할은 Azure AD(Azure Active Directory)를 통해 스토리지 계정의 데이터에 대한 액세스 권한을 제공하지 않습니다. 그러나 계정 액세스 키에 대한 액세스 권한을 부여하는 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함되어 있습니다. 이 권한을 사용하는 경우 사용자는 계정 액세스 키를 사용하여 스토리지 계정의 모든 데이터에 액세스할 수 있습니다.

사용자가 스토리지 계정에 대해 최소 버전의 TLS를 요구하도록 허용하려면 역할 할당 범위를 저장소 계정 수준 이상으로 지정해야 합니다. 역할 범위에 대한 자세한 내용은 [Azure RBAC의 범위 이해](../../role-based-access-control/scope-overview.md)를 참조하세요.

이러한 역할은 스토리지 계정을 만들거나 해당 속성을 업데이트하는 기능이 필요한 사용자에게만 제한적으로 할당해야 합니다. 최소 권한의 원칙을 사용하여 사용자에게 작업을 수행하는 데 필요한 최소 권한을 부여합니다. Azure RBAC를 사용하여 액세스를 관리하는 방법에 대한 자세한 내용은 [Azure RBAC 모범 사례](../../role-based-access-control/best-practices.md)를 참조하세요.

> [!NOTE]
> 클래식 구독 관리자 역할인 서비스 관리자 및 공동 관리자에는 Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할에 해당하는 항목이 포함됩니다. **소유자** 역할은 모든 작업을 포함하므로 이러한 관리 역할 중 하나가 있는 사용자는 스토리지 계정을 만들고 관리할 수도 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조하세요.

## <a name="network-considerations"></a>네트워크 고려 사항

클라이언트가 스토리지 계정에 요청을 보내면 클라이언트는 요청을 처리하기 전에 먼저 스토리지 계정의 퍼블릭 엔드포인트와의 연결을 설정합니다. 최소 TLS 버전 설정은 연결이 설정된 후에 확인됩니다. 요청에서 설정에 지정된 것보다 이전 버전의 TLS를 사용하는 경우 연결은 계속 성공하지만 결국 요청은 실패합니다. Azure Storage에 대한 퍼블릭 엔드포인트에 대한 자세한 내용은 [리소스 URI 구문](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [클라이언트 애플리케이션에 대한 TLS(전송 계층 보안) 구성](transport-layer-security-configure-client-version.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
