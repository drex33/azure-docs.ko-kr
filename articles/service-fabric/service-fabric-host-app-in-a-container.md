---
title: Azure Service Fabric에 컨테이너의 .NET 앱 배포
description: Visual Studio를 사용하여 기존.NET 애플리케이션을 컨테이너화하고 Service Fabric에서 로컬로 컨테이너를 디버그하는 방법에 대해 알아봅니다. 컨테이너화된 애플리케이션을 Azure 컨테이너 레지스트리에 푸시하고 Service Fabric 클러스터에 배포합니다. Azure에 배포하는 경우 애플리케이션은 Azure SQL DB를 사용하여 데이터를 유지합니다.
ms.topic: tutorial
ms.date: 07/08/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ae7069b155266b8fc8049b9660d38acbb1103d6c
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236426"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>자습서: Azure Service Fabric에 Windows 컨테이너로 .NET 애플리케이션 배포

이 자습서에서는 기존 ASP.NET 애플리케이션을 컨테이너화하고 Service Fabric 애플리케이션으로 패키지하는 방법을 설명합니다.  Service Fabric 개발 클러스터에서 로컬로 컨테이너를 실행한 다음, Azure에 애플리케이션을 배포합니다.  애플리케이션은 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)에 데이터를 유지합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Visual Studio를 사용하여 기존 애플리케이션 컨테이너화
> * Azure SQL Database에서 데이터베이스 만들기
> * Azure Container Registry 만들기
> * Azure에 Service Fabric 애플리케이션 배포

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

1. Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Windows 기능 **Hyper-V** 및 **컨테이너** 를 사용하도록 설정합니다.
3. Windows 10에서 컨테이너를 실행할 수 있도록 [Windows용 Docker Desktop](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)을 설치합니다.
4. [Service Fabric 런타임 버전 6.2 이상](service-fabric-get-started.md) 및 [Service Fabric SDK 버전 3.1](service-fabric-get-started.md) 이상을 설치합니다.
5. [Visual Studio](https://www.visualstudio.com/)를 설치하고 **Azure 개발** 워크로드와 **ASP.NET 및 웹 개발** 워크로드를 사용하도록 설정합니다.
6. [Azure Powershell][link-azure-powershell-install] 설치

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>Fabrikam Fiber CallCenter를 다운로드하여 실행

1. GitHub에서 [Fabrikam Fiber CallCenter][link-fabrikam-github] 샘플 애플리케이션을 다운로드합니다.

2. Fabrikam Fiber CallCenter 애플리케이션이 오류 없이 빌드되고 실행되는지 확인합니다.  **관리자** 권한으로 Visual Studio를 시작하고 [VS2015\FabrikamFiber.CallCenter.sln][link-fabrikam-github] 파일을 엽니다. F5 키를 눌러 애플리케이션을 실행하고 디버그합니다.

   ![로컬 호스트에서 실행되는 Fabrikam Fiber CallCenter 애플리케이션 홈 페이지의 스크린샷. 이 페이지에는 지원 호출 목록이 포함된 대시보드가 표시됩니다.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Azure SQL DB 만들기

Fabrikam Fiber CallCenter 애플리케이션을 프로덕션 환경에서 실행하는 경우 데이터는 데이터베이스에 유지돼야 합니다. 현재는 컨테이너에 데이터를 유지할 수 있는 방법이 없으므로 프로덕션 데이터를 컨테이너로 SQL Server에 저장할 수 없습니다.

[Azure SQL Database](../azure-sql/database/powershell-script-content-guide.md)를 권장합니다. Azure에서 SQL Server DB를 설정하고 실행하려면 다음 스크립트를 실행합니다.  필요에 따라 스크립트 변수를 수정합니다. *clientIP* 는 개발 컴퓨터의 IP 주소입니다. 스크립트에서 출력되는 서버의 이름을 적어 두세요.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> 회사 방화벽을 갖추고 있다면 개발 컴퓨터의 IP 주소는 인터넷에 노출된 IP 주소가 아닐 수 있습니다. er데이터베이스가 방화벽 규칙에 대해 올바른 IP 주소를 가지고 있는지 확인하려면 [Azure portal](https://portal.azure.com)로 이동하여 SQL Databases 섹션에서 데이터베이스를 찾습니다. 이름을 클릭한 다음, 개요 섹션에서 “서버 방화벽 설정”을 클릭합니다. "클라이언트 IP 주소"는 개발 머신의 IP 주소입니다. "AllowClient" 규칙의 IP 주소와 일치하는지 확인합니다.

## <a name="update-the-web-config"></a>웹 구성 업데이트

**FabrikamFiber.Web** 프로젝트로 돌아가 **web.config** 파일의 연결 문자열이 컨테이너의 SQL Server를 가리키도록 업데이트합니다.  연결 문자열의 *서버* 부분을 이전 스크립트에서 만든 서버 이름으로 업데이트합니다. "fab-fiber-751718376.database.windows.net"와 같은 형식입니다. 다음 XML에서는 `connectionString` 특성만 업데이트해야 합니다. `providerName` 및 `name` 특성은 변경할 필요가 없습니다.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>호스트에서 연결할 수 있기만 하면 어떤 SQL Server든지 로컬 디버깅에 사용할 수 있습니다. 그러나 **localdb** 는 `container -> host` 통신을 지원하지 않습니다. 웹 애플리케이션의 릴리스 버전을 빌드할 때 다른 SQL 데이터베이스를 사용하려면 *web.release.config* 파일에 다른 연결 문자열을 추가합니다.

## <a name="containerize-the-application"></a>애플리케이션 컨테이너화

1. **FabrikamFiber.Web** 프로젝트 > **추가** > **컨테이너 Orchestrator 지원** 을 마우스 오른쪽 단추로 클릭합니다.  **Service Fabric** 을 컨테이너 오케스트레이터로 선택하고 **확인** 을 클릭합니다.

2. 메시지가 표시되면 **예** 를 클릭하여 지금 Docker를 Windows 컨테이너로 전환합니다.

   새 프로젝트 Service Fabric 애플리케이션 프로젝트 **FabrikamFiber.CallCenterApplication** 이 솔루션에서 만들어집니다.  Dockerfile이 **FabrikamFiber.Web** 기존 프로젝트에 추가됩니다.  **PackageRoot** 디렉터리도 **FabrikamFiber.Web** 프로젝트에 추가되며, 이는 새 FabrikamFiber.Web 서비스에 대한 서비스 매니페스트 및 설정을 포함하는 프로젝트입니다.

   이제 컨테이너를 빌드하고 Service Fabric 애플리케이션에 패키지할 준비가 되었습니다. 컴퓨터에서 컨테이너 이미지를 빌드한 후 컨테이너 레지스트리에 푸시하고 실행할 호스트로 끌어올 수 있습니다.

## <a name="run-the-containerized-application-locally"></a>컨테이너화된 애플리케이션을 로컬로 실행

**F5** 키를 눌러 로컬 Service Fabric 개발 클러스터에서 컨테이너의 애플리케이션을 실행하고 디버그합니다. 'ServiceFabricAllowedUsers' 그룹 읽기 및 실행 권한을 Visual Studio 프로젝트 디렉터리에 부여할지 묻는 메시지 상자가 표시되는 경우 **예** 를 클릭합니다.

F5 키를 눌러 다음과 같은 예외를 throw하는 경우에는 올바른 IP가 Azure 데이터베이스 방화벽에 추가되지 않았습니다.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

Azure 데이터베이스 방화벽에 적절한 IP를 추가하려면 다음 명령을 실행합니다.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

애플리케이션이 로컬로 실행되므로 Azure에 배포할 준비를 시작합니다.  컨테이너 이미지는 컨테이너 레지스트리에 저장되어야 합니다.  다음 스크립트를 사용하여 [Azure Container Registry](../container-registry/container-registry-intro.md)를 만듭니다. 컨테이너 레지스트리 이름은 다른 Azure 구독을 통해 표시되므로 고유해야 합니다.
Azure에 애플리케이션을 배포하기 전에 이 레지스트리에 컨테이너 이미지를 푸시합니다.  애플리케이션이 Azure에서 클러스터에 배포되면 컨테이너 이미지는 이 레지스트리에서 가져옵니다.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Azure에서 Service Fabric 클러스터 만들기

Service Fabric 애플리케이션은 네트워크에 연결된 가상 머신 또는 물리적 머신의 집합인 클러스터에서 실행됩니다.  Azure에 애플리케이션을 배포하기 전에 Azure에서 Service Fabric 클러스터를 만듭니다.

다음을 수행할 수 있습니다.

* Visual Studio에서 테스트 클러스터를 만듭니다. 이 옵션을 사용하면 기본 설 구성으로 Visual Studio에서 직접 보안 클러스터정를 만들 수 있습니다.
* [템플릿에서 보안 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

이 자습서는 테스트 시나리오에 적합한 Visual Studio에서 클러스터를 만듭니다. 어떤 다른 방법으로 클러스터를 만들거나 기존 클러스터를 사용하는 경우 연결 엔트포인트를 복사해 붙여넣거나 구독에서 선택할 수 있습니다.

시작하기 전에 솔루션 탐색기에서 FabrikamFiber.Web -> PackageRoot -> ServiceManifest.xml을 엽니다. **엔드포인트** 에 나열된 웹 프런트 엔드에 대한 포트를 적어 두세요.

클러스터를 만드는 경우 다음을 수행합니다.

1. 솔루션 탐색기에서 애플리케이션 프로젝트 **FabrikamFiber.CallCenterApplication** 를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.
2. Azure 계정을 사용하여 로그인하면 구독 정보에 액세스할 수 있습니다.
3. **연결 엔드포인트** 에 대한 드롭다운 아래에서 **새 클러스터 만들기...** 옵션을 선택합니다.
4. **클러스터 만들기** 대화 상자에서 다음 설정을 수정합니다.

    a. **클러스터 이름** 필드에서 클러스터의 이름뿐만 아니라 사용하려는 구독과 위치도 지정합니다. 클러스터 리소스 그룹의 이름을 적어 두세요.

    b. 선택 사항: 노드 수를 수정할 수 있습니다. 기본적으로 Service Fabric 시나리오를 테스트하는 데 필요한 최소 세 개의 노드가 있습니다.

    다. **인증서** 탭을 선택합니다. 이 탭에서 클러스터의 인증서를 보호하는 데 사용할 암호를 입력합니다. 이 인증서는 클러스터를 안전하게 보호하는 데 도움이 됩니다. 인증서를 저장하려는 위치의 경로를 수정할 수도 있습니다. 또한 애플리케이션을 클러스터에 게시하는 데 필요한 단계이므로 Visual Studio에서 사용자에 대한 인증서를 가져올 수도 있습니다.

    >[!NOTE]
    >이 인증서를 가져오는 폴더 경로를 기록해 둡니다. 클러스터를 만든 후 다음 단계는 이 인증서를 가져오는 것입니다.

    d. **VM 세부 정보** 탭을 선택합니다. 클러스터를 구성하는 VM(Virtual Machines)에 사용할 암호를 지정합니다. 사용자 이름과 암호를 사용하여 VM에 원격으로 연결할 수 있습니다. 또한 VM 컴퓨터 크기를 선택해야 하며, 필요한 경우 VM 이미지를 변경할 수도 있습니다.

    > [!IMPORTANT]
    > 실행 중인 컨테이너를 지원하는 SKU를 선택합니다. 클러스터 노드의 Windows Server OS는 컨테이너의 Windows Server OS와 호환되어야 합니다. 자세히 알아보려면 [Windows Server 컨테이너 OS 및 호스트 OS 호환성](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)을 참조하세요. 기본적으로 이 자습서에서는 Windows Server 2016 LTSC를 기반으로 하는 Docker 이미지를 만듭니다. 이 이미지를 기반으로 하는 컨테이너는 컨테이너로 Windows Server 2016 Datacenter를 사용하여 만든 클러스터에서 실행됩니다. 그러나 클러스터를 만들거나 다른 버전의 Windows Server를 기반으로 하는 기존 클러스터를 사용하는 경우 컨테이너가 기반으로 하는 OS 이미지를 변경해야 합니다. **FabrikamFiber.Web** 프로젝트에서 **dockerfile** 을 열고, 이전 버전의 Windows Server를 기반으로 하는 기존 `FROM` 문을 주석 처리하고, [Windows Server Core DockerHub 페이지](https://hub.docker.com/_/microsoft-windows-servercore)에서 원하는 버전의 태그를 기반으로 `FROM` 문을 추가합니다. Windows Server Core 릴리스, 지원 타임라인 및 버전 관리에 대한 추가 정보는 [Windows Server Core 릴리스 정보](/windows-server/get-started/windows-server-release-info)를 참조하세요. 

    e. **고급** 탭에서 클러스터를 배포하는 경우 부하 분산 장치에서 열려는 애플리케이션 포트를 나열합니다. 이 포트는 클러스터를 만들기 전에 적어 둔 포트입니다. 또한 애플리케이션 로그 파일을 라우팅하는 데 사용할 기존 Application Insights 키를 추가할 수도 있습니다.

    f. 설정 수정을 마쳤으면 **만들기** 단추를 선택합니다.

5. 만들기가 완료되는 데 몇 분이 걸립니다. 클러스터가 완전히 만들어지면 출력 창에 표시됩니다.

## <a name="install-the-imported-certificate"></a>가져온 인증서 설치

클러스터 만들기 단계의 일부로 가져온 인증서를 **현재 사용자** 저장소 위치에 설치하고 사용자가 제공한 프라이빗 키 암호를 제공합니다.

제어판에서 **사용자 인증서 관리** 를 열고 인증서가 **인증서 - 현재 사용자** -> **개인** -> **인증서** 에 설치되었는지 확인하여 설치를 확인할 수 있습니다. 인증서는 *[Cluster Name]* . *[Cluster Location]* .cloudapp.azure.com(예: *fabrikamfibercallcenter.southcentralus.cloudapp.azure.com*)과 같아야 합니다. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Azure에서 실행 중인 애플리케이션이 SQL Database에 액세스할 수 있도록 허용

이전에 로컬로 실행 중인 애플리케이션에 액세스 권한을 부여하기 위해 SQL 방화벽 규칙을 만들었습니다.  다음으로 Azure에서 실행 중인 애플리케이션이 SQL DB에 액세스할 수 있도록 설정해야 합니다.  Service Fabric 클러스터에 대한 [가상 네트워크 서비스 엔드포인트](../azure-sql/database/vnet-service-endpoint-rule-overview.md)를 만든 다음, 해당 엔트포인트가 SQL DB에 액세스할 수 있는 규칙을 만듭니다. 클러스터를 만들 때 적어 둔 클러스터 리소스 그룹 변수를 지정합니다.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Azure에 애플리케이션 배포

애플리케이션이 준비되면 Visual Studio에서 직접 Azure의 클러스터에 배포할 수 있습니다.  솔루션 탐색기에서 애플리케이션 프로젝트 **FabrikamFiber.CallCenterApplication** 를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.  **연결 엔트포인트** 에서 이전에 만든 클러스터의 엔드포인트를 선택합니다.  **Azure Container Registry** 에서 이전에 만든 컨테이너 레지스트리를 선택합니다.  Azure에서 애플리케이션을 클러스터에 배포하려면 **게시** 를 클릭합니다.

![애플리케이션 게시][publish-app]

출력 창의 배포 진행률을 따릅니다. 애플리케이션을 배포할 때 클러스터 주소와 애플리케이션 포트에서 브라우저와 유형을 엽니다. 예들 들어 `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`입니다.

![azure.com에서 실행되는 Fabrikam Fiber CallCenter 애플리케이션 홈 페이지의 스크린샷. 이 페이지에는 지원 호출 목록이 포함된 대시보드가 표시됩니다.][fabrikam-web-page-deployed]

페이지를 로드하는 데 실패하거나 인증서를 입력하라는 메시지가 표시되지 않으면 탐색기 경로를 열고(예: `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer`) 새로 설치된 인증서를 선택합니다.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Service Fabric 클러스터와의 CI/CD(지속적인 통합 및 지속적인 배포) 설정

Azure DevOps를 사용하여 Service Fabric 클러스터에 대한 CI/CD 애플리케이션 배포를 구성하는 방법을 알아보려면 [자습서: Service Fabric 클러스터에 CI/CD로 애플리케이션 배포](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)를 참조하세요. 이 자습서에 설명된 프로세스는 이 (FabrikamFiber) 프로젝트와 동일하므로, Voting 샘플 다운로드 과정을 건너뛰고 리포지토리 이름으로 Voting 대신 FabrikamFiber를 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

완료된 경우 만든 모든 리소스를 제거해야 합니다.  가장 간단한 방법은 Service Fabric 클러스터, Azure SQL DB 및 Azure Container Registry를 포함하는 리소스 그룹을 제거하는 것입니다.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * Visual Studio를 사용하여 기존 애플리케이션 컨테이너화
> * Azure SQL Database에서 데이터베이스 만들기
> * Azure Container Registry 만들기
> * Azure에 Service Fabric 애플리케이션 배포

자습서의 다음 부분에서 [Service Fabric 클러스터에 CI/CD로 컨테이너 애플리케이션 배포](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md) 방법을 알아봅니다.

[link-fabrikam-github]: https://github.com/Azure-Samples/service-fabric-dotnet-containerize
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
