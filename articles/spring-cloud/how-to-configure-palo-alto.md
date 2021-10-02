---
title: Azure 스프링 클라우드에 대해 Palo Alto를 구성 하는 방법
description: Azure 스프링 클라우드에 대해 Palo Alto를 구성 하는 방법
author: karlerickson
ms.author: vaangadi
ms.topic: how-to
ms.service: spring-cloud
ms.date: 09/17/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a65a6ea1fb2070b6a1879521b257c2738930bf3a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373012"
---
# <a name="how-to-configure-palo-alto-for-azure-spring-cloud"></a>Azure 스프링 클라우드에 대해 Palo Alto를 구성 하는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 문서에서는 Palo Alto 방화벽과 함께 Azure 스프링 클라우드를 사용 하는 방법을 설명 합니다.

예를 들어 [Azure 스프링 클라우드 참조 아키텍처](/azure/spring-cloud/reference-architecture) 는 응용 프로그램을 보호 하기 위한 azure 방화벽을 포함 합니다. 그러나 현재 배포에 Palo Alto 방화벽이 포함 되어 있는 경우이 문서에 설명 된 대로 Azure 스프링 클라우드 배포에서 Azure 방화벽을 생략 하 고 대신 Palo Alto를 사용할 수 있습니다.

규칙 및 주소 와일드 카드와 같은 구성 정보는 Git 리포지토리의 CSV 파일에 보관 해야 합니다. 이 문서에서는 자동화를 사용 하 여 이러한 파일을 Palo Alto에 적용 하는 방법을 보여 줍니다. Palo Alto에 적용 되는 구성을 이해 하려면 [VNET에서 Azure 스프링 클라우드를 실행 하는 고객 책임](/azure/spring-cloud/vnet-customer-responsibilities)을 참조 하세요. 

> [!Note]
> REST Api를 사용 하는 방법을 설명 하는이 문서에서는 PowerShell 변수 구문을 사용 하 여 판단에 남은 이름 및 값을 표시 합니다. 모든 단계에서 동일한 값을 사용 해야 합니다.
>
> Palo Alto에서 TLS/SSL 인증서를 구성한 후에는 `-SkipCertificateCheck` 아래 예제에서 모든 Palo Alto REST API 호출에서 인수를 제거 합니다.
>
> Palo Alto REST Api에 대 한 참조로이 문서를 사용 하면 안 됩니다. 모든 예제는 데모용 으로만 제공 됩니다. 권한 있는 API에 대 한 자세한 내용은 Palo Alto 설명서의 [이동 OS REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/pan-os-rest-api.html) 를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.
* Palo Alto 배포. 배포가 없는 경우 [Azure Marketplace에서 Palo Alto를](https://ms.portal.azure.com/#create/paloaltonetworks.vmseries-ngfwbundle2)프로 비전 할 수 있습니다.
* [PowerShell](/powershell/scripting/install/installing-powershell)
* [Azure CLI](/cli/azure/install-azure-cli)

## <a name="configure-palo-alto"></a>Palo Alto 구성

먼저 Palo Alto VM-Series 방화벽을 구성 합니다. 자세한 지침은 [Azure Marketplace에서 VM-Series 방화벽 배포 (솔루션 템플릿)](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewall-on-azure-solution-template.html)를 참조 하세요. 이러한 지침은 VM-Series 방화벽을 프로 비전 하 고 `Trust` 및 서브넷과 연결 된 `UnTrust` 네트워크 인터페이스 카드를 구성 하는 데 도움이 됩니다. 일관 되 게 유지 하려면 `Hub` 참조 아키텍처에서 가상 네트워크의 주소 공간에이 방화벽을 만들어야 합니다.

[Azure에 대 한 참조 아키텍처 가이드](https://www.paloaltonetworks.com/resources/guides/azure-architecture-guide) 에서는 Azure에서 방화벽을 배포 하기 위한 몇 가지 기술 디자인 모델을 살펴봅니다.

이 문서의 나머지 부분에서는 다음과 같은 두 개의 미리 구성 된 네트워크 영역을 가정 합니다.

* `Trust`-Azure 스프링 클라우드 가상 네트워크를 사용 하 여 피어 링 가상 네트워크에 연결 된 인터페이스를 포함 합니다.
* `UnTrust`-이전에 VM-Series 배포 가이드에서 만든 공용 인터넷에 대 한 인터페이스를 포함 합니다.

## <a name="prepare-csv-files"></a>CSV 파일 준비

다음으로, 세 개의 CSV 파일을 만듭니다.

첫 번째 파일의 이름을 *AzureSpringCloudServices.csv* 합니다. 이 파일에는 Azure 스프링 클라우드의 수신 포트가 포함 되어야 합니다. 다음 예의 값은 데모용 으로만 사용 됩니다. 모든 필수 값은 [VNET에서 Azure 스프링 클라우드를 실행 하는 고객 책임](/azure/spring-cloud/vnet-customer-responsibilities)의 [azure 스프링 클라우드 네트워크 요구 사항](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-network-requirements) 섹션을 참조 하세요.

```CSV
name,protocol,port,tag
ASC_1194,udp,1194,AzureSpringCloud
ASC_443,tcp,443,AzureSpringCloud
ASC_9000,tcp,9000,AzureSpringCloud
ASC_445,tcp,445,AzureSpringCloud
ASC_123,udp,123,AzureSpringCloud
```

두 번째 파일의 이름을 *AzureSpringCloudUrlCategories.csv* 합니다. 이 파일은 Azure 스프링 클라우드에서 수신 하는 데 사용할 수 있는 주소 (와일드 카드 포함)를 포함 해야 합니다. 다음 예의 값은 데모용 으로만 사용 됩니다. 최신 값은 [Azure 스프링 클라우드 FQDN 요구 사항/응용 프로그램 규칙](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-fqdn-requirementsapplication-rules)을 참조 하세요.

```CSV
name,description
*.azmk8s.io,
mcr.microsoft.com,
*.cdn.mscr.io,
*.data.mcr.microsoft.com,
management.azure.com,
*.microsoftonline.com,
*.microsoft.com,
packages.microsoft.com,
acs-mirror.azureedge.net,
mscrl.microsoft.com,
crl.microsoft.com,
crl3.digicert.com
```

세 번째 파일의 이름을 *AzureMonitorAddresses.csv* 합니다. 이 파일에는 Azure Monitor를 사용 하는 경우 Azure Monitor를 통한 메트릭 및 모니터링에 사용할 수 있는 모든 주소와 IP 범위가 포함 되어야 합니다. 다음 예의 값은 데모용 으로만 사용 됩니다. 최신 값은 [Azure Monitor에서 사용 하는 IP 주소](/azure/azure-monitor/app/ip-addresses)를 참조 하세요.

```CSV
name,type,address,tag
40.114.241.141,ip-netmask,40.114.241.141/32,AzureMonitor
104.45.136.42,ip-netmask,104.45.136.42/32,AzureMonitor
40.84.189.107,ip-netmask,40.84.189.107/32,AzureMonitor
168.63.242.221,ip-netmask,168.63.242.221/32,AzureMonitor
52.167.221.184,ip-netmask,52.167.221.184/32,AzureMonitor
live.applicationinsights.azure.com,fqdn,live.applicationinsights.azure.com,AzureMonitor
rt.applicationinsights.microsoft.com,fqdn,rt.applicationinsights.microsoft.com,AzureMonitor
rt.services.visualstudio.com,fqdn,rt.services.visualstudio.com,AzureMonitor

```

## <a name="authenticate-into-palo-alto"></a>Palo Alto으로 인증

다음으로 Palo Alto에 인증 하 고 API 키를 가져와야 합니다. 자세한 내용은 Palo Alto 설명서의 [API 키 가져오기](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-xml-api/get-your-api-key.html) 를 참조 하세요.

다음 예제에서는 PowerShell을 사용 하 여 인증 하 고이 문서의 뒷부분에서 사용 되는 요청 헤더를 생성 합니다.

```powershell
$username=<username for PaloAlto>
$password=<password for PaloAlto>
$authResponse = irm "https://${PaloAltoIpAddress}/api/?type=keygen&user=${username}&password=${password}" -SkipCertificateCheck
$paloAltoHeaders = @{'X-PAN-KEY' = $authResponse.response.result.key; 'Content-Type' = 'application/json' }
```

## <a name="delete-existing-service-group"></a>기존 서비스 그룹 삭제

이전 구성 시도를 수행한 경우 이러한 구성을 다시 설정 하 고 보안 규칙 및 서비스 그룹을 삭제 해야 합니다.

다음 예제와 같이 [보안 규칙 REST API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/create-security-policy-rule-rest-api.html)를 사용 하 여 보안 규칙을 삭제 합니다.

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=${paloAltoSecurityPolicyName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

다음 예제와 같이 서비스 그룹을 삭제 합니다.

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${paloAltoServiceGroupName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

다음 예제와 같이 각 Palo Alto 서비스 ( *AzureSpringCloudServices.csv* 에 정의 됨)를 삭제 합니다.

```powershell
Get-Content .\AzureSpringCloudServices.csv | ConvertFrom-Csv | select name | ForEach-Object {
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${_}"
    Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
}
```

## <a name="create-a-service-and-service-group"></a>서비스 및 서비스 그룹 만들기

이전에 만든 *AzureSpringCloudServices.csv* 파일을 기반으로 서비스 만들기를 자동화 하려면 다음 예제를 사용 합니다.

```powershell
# Define a function to create and submit a Palo Alto service creation request
function New-PaloAltoService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject]
        $ServiceObject
    )
    PROCESS {
        $requestBody = @{
            'entry' = @{
                '@name'    = $ServiceObject.name
                'protocol' = @{
                    $ServiceObject.protocol = @{
                        'port'     = $ServiceObject.port
                        'override' = @{
                            'no' = @{}
                        }

                    }
                }
                'tag'      = @{
                    'member' = @($ServiceObject.tag)
                }
            }
        }

        # Some rules in the CSV may need to conain source ports or descriptions. If these are present, populate them in the request
        if ($ServiceObject.description) {
            $requestBody.entry.description = $ServiceObject.description
        }
        if ($ServiceObject.'source-port') {
            $requestBody.entry.protocol."$($ServiceObject.protocol)".'source-port' = $ServiceObject.'source-port'
        }

        # Send the request
        $name = $requestBody.entry.'@name'
        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${name}"
         Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 9) -Verbose
    }
}

# Now invoke that function for every row in AzureSpringCloudServices.csv
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoService
```

다음 예제와 같이 이러한 서비스에 대 한 서비스 그룹을 만듭니다.

```powershell
# Create a function to consume service definitions and submit a service group creation request
function New-PaloAltoServiceGroup {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject[]]
        $RuleData,

        [Parameter(Mandatory = $true)]
        [string]
        $ServiceGroupName
    )
    begin {
        [array] $names = @()
    }

    process {
        $names += $RuleData.name
    }

    end {
        $requestBody = @{ 'entry' = [ordered] @{
                '@name'   = $ServiceGroupName
                'members' = @{ 'member' = $names }
                'tag'     = @{ 'member' = 'AzureSpringCloud' }
            }
        }

        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${ServiceGroupName}"

        Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json $requestBody) -Verbose
    }
}

# Run that function for all services in AzureSpringCloudServices.csv.
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoServiceGroup -ServiceGroupName 'AzureSpringCloud_SG'
```

## <a name="create-custom-url-categories"></a>사용자 지정 URL 범주 만들기

다음으로, 다음 예제와 같이 Azure 스프링 클라우드에서 송신을 사용 하도록 설정 하기 위해 서비스 그룹에 대 한 사용자 지정 URL 범주를 정의 합니다.

```powershell
# Read Service entries from CSV to enter into Palo Alto
$csvImport = Get-Content ${PSScriptRoot}/AzureSpringCloudUrls.csv | ConvertFrom-Csv

# Convert name column of CSV to add to the Custom URL Group in Palo Alto
$requestBody = @{ 'entry' = [ordered] @{
        '@name' = 'AzureSpringCloud_SG'
        'list'  = @{ 'member' = $csvImport.name }
        'type'  = 'URL List'
    }
} | ConvertTo-Json -Depth 9

$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/CustomURLCategories?location=vsys&vsys=vsys1&name=AzureSpringCloud_SG"

try {
    $existingObject = Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
}
catch {
}

Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body $requestBody -Verbose
```

## <a name="create-a-security-rule"></a>보안 규칙 만들기

다음으로, 보안 규칙을 포함 하는 JSON 파일을 만듭니다. 파일의 이름을 *Securityrule. json* 으로 만들고 다음 콘텐츠를 추가 합니다. `Trust` `UnTrust` [Palo Alto 구성](#configure-palo-alto) 섹션의 앞부분에서 설명한 영역 이름과 일치 하는 두 영역의 이름입니다. 항목에는 `service/member` 이전 단계에서 만든 서비스 그룹의 이름이 포함 됩니다.

```json
{
    "entry": [
        {
            "@name": "azureSpringCloudRule",
            "@location": "vsys",
            "@vsys": "vsys1",
            "to": {
                "member": [
                    "UnTrust"
                ]
            },
            "from": {
                "member": [
                    "Trust"
                ]
            },
            "source-user": {
                "member": [
                    "any"
                ]
            },
            "application": {
                "member": [
                    "any"
                ]
            },
            "service": {
                "member": [
                    "AzureSpringCloud_SG"
                ]
            },
            "hip-profiles": {
                "member": [
                    "any"
                ]
            },
            "action": "allow",
            "category": {
                "member": [
                    "any"
                ]
            },
            "source": {
                "member": [
                    "any"
                ]
            },
            "destination": {
                "member": [
                    "any"
                ]
            }
        }
    ]
}
```

이제 다음 예제와 같이 Palo Alto에이 규칙을 적용 합니다.

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=azureSpringCloudRule"

# Delete the rule if it already exists
try {
    $getResult = Invoke-RestMethod -Headers $paloAltoHeaders -Method Get -SkipCertificateCheck -Uri $url -Verbose
    if ($getResult.'@status' -eq 'success') {
        Invoke-RestMethod -Method Delete  -Headers $paloAltoHeaders -SkipCertificateCheck -Uri $url
    }
}
catch {}

# Create the rule from the JSON file
Invoke-WebRequest -Uri $url -Method Post -Headers $paloAltoHeaders -Body (Get-Content SecurityRule.json) -SkipCertificateCheck
```

## <a name="create-azure-monitor-addresses"></a>Azure Monitor 주소 만들기

그런 다음 *AzureMonitorAddresses.csv* 파일을 사용 하 여 Palo Alto에서 주소 개체를 정의 합니다. 다음 예제 코드에서는이 작업을 자동화 하는 방법을 보여 줍니다.

```powershell
Get-Content ./AzureMonitorAddresses.csv | ConvertFrom-Csv | ForEach-Object {
    $requestBody = @{ 'entry' = [ordered]@{
            '@name' = $_.name
            $_.type = $_.address
            'tag'   = @{ 'member' = @($_.tag) }
        }
    }

    $name = $requestBody.entry.'@name'
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Addresses?location=vsys&vsys=vsys1&name=${name}"

    # Delete the address if it already exists
    try {
        Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    }
    catch {
    }

    # Create the address
    Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 3) -Verbose
}
```

## <a name="commit-changes-to-palo-alto"></a>Palo Alto에 대 한 변경 내용 커밋

위의 변경 내용 중 일부를 커밋하여 활성화 해야 합니다. 다음 REST API 호출을 사용 하 여이 작업을 수행할 수 있습니다.

```powershell
$url = "https://${PaloAltoIpAddress}/api/?type=commit&cmd=<commit></commit>"
Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
```

## <a name="configure-the-security-rules-for-azure-spring-cloud-subnets"></a>Azure 스프링 클라우드 서브넷에 대 한 보안 규칙 구성

다음으로, Palo Alto에서 Azure 스프링 클라우드에 액세스할 수 있는 트래픽을 허용 하는 네트워크 보안 규칙을 추가 합니다. 다음 예에서는 참조 아키텍처를 통해 생성 된 네트워크 NSGs (네트워크 보안 그룹) 및를 참조 합니다. `nsg-spokeapp` `nsg-spokeruntime`

PowerShell 창에서 다음 Azure CLI 명령을 실행 하 여 이러한 각 NSGs에 대해 필요한 네트워크 보안 규칙을 만듭니다 `$PaloAltoAddressPrefix` . 여기서는 Palo Alto의 개인 ip의 CIDR (클래스 간 Inter-Domain 라우팅) 주소입니다.

```azurecli
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeapp' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeruntime' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
```

## <a name="configure-the-next-hop"></a>다음 홉 구성

Palo Alto를 구성한 후에는 Azure 스프링 클라우드가 아웃 바운드 인터넷 액세스에 대 한 다음 홉으로 Palo Alto를 갖도록 구성 합니다. PowerShell 창에서이 구성에 대해 다음 Azure CLI 명령을 사용할 수 있습니다. 다음 변수에 대 한 값을 제공 해야 합니다.

* `$AppResourceGroupName`: Azure 스프링 클라우드를 포함 하는 리소스 그룹의 이름입니다.
* `$AzureSpringCloudServiceSubnetRouteTableName`: Azure 스프링 클라우드 서비스/런타임 서브넷 경로 테이블의 이름입니다. 참조 아키텍처에서는로 설정 됩니다 `rt-spokeruntime` .
* `$AzureSpringCloudAppSubnetRouteTableName`: Azure 스프링 클라우드 앱 서브넷 경로 테이블의 이름입니다. 참조 아키텍처에서는로 설정 됩니다 `rt-spokeapp` .

```azurecli
az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudServiceSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose

az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudAppSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose
```

이제 구성이 완료 되었습니다.

## <a name="next-steps"></a>다음 단계

* [실시간으로 Azure Spring Cloud 앱 로그 스트림](/azure/spring-cloud/how-to-log-streaming)
* [Azure 스프링 클라우드의 Application Insights Java In-Process 에이전트](/azure/spring-cloud/how-to-application-insights)
* [Azure Spring Cloud에 대한 애플리케이션 배포 자동화](/azure/spring-cloud/how-to-cicd)
