---
title: '빠른 시작: Table API와 .NET - Azure Cosmos DB'
description: 이 빠른 시작에서는 .NET 애플리케이션에서 Azure.Data.Tables SDK를 사용하여 Azure Cosmos DB Table API에 액세스하는 방법을 보여줍니다.
author: DavidCBerry13
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/26/2021
ms.author: daberry
ms.custom: devx-track-csharp
ms.openlocfilehash: b13ac1d3d72a28538a2bdbdc149017848105562c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619674"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>빠른 시작: .NET SDK 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

이 빠른 시작에서는 .NET 애플리케이션에서 Azure Cosmos DB [Table API](introduction.md)에 액세스하는 방법을 보여줍니다.  Cosmos DB Table API는 애플리케이션이 클라우드에 정형 NoSQL 데이터를 저장할 수 있는, 스키마 없는 데이터 저장소입니다.  데이터가 스키마 없는 디자인에 저장되므로, 새 특성이 있는 개체가 테이블에 추가되면 테이블에 새 속성(열)이 자동으로 추가됩니다.

.NET 애플리케이션은 [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) NuGet 패키지를 사용하여 Cosmos DB Table API에 액세스할 수 있습니다.  [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables/) 패키지는 .NET Framework(4.7.2 이상) 및 .NET Core(2.0 이상) 애플리케이션에서 모두 작동하는 [.NET Standard 2.0](/dotnet/standard/net-standard) 라이브러리입니다.

## <a name="prerequisites"></a>필수 구성 요소

샘플 애플리케이션은 [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet/3.1)로 작성되었지만, 원칙은 .NET Framework 및 .NET Core 애플리케이션 모두에 적용됩니다.  [Visual Studio](https://www.visualstudio.com/downloads/), [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) 또는 [Visual Studio Code](https://code.visualstudio.com/)를 IDE로 사용할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="sample-application"></a>애플리케이션 예제

이 자습서의 샘플 애플리케이션은 리포지토리 [https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet](https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet)에서 복제하거나 다운로드할 수 있습니다.  스타터와 완료된 앱은 모두 샘플 리포지토리에 포함됩니다.

```bash
git clone https://github.com/Azure-Samples/msdocs-azure-data-tables-sdk-dotnet
```

샘플 애플리케이션은 날씨 데이터를 예제로 사용하여 Table API의 기능을 보여줍니다. 날씨 관측을 나타내는 개체는 Table API를 사용하여 저장 및 검색됩니다. 여기에는 Table API의 스키마 없는 기능을 보여주는 추가 속성이 포함된 개체 저장이 포함됩니다.

:::image type="content" source="./media/create-table-dotnet/table-api-app-finished-application-720px.png" alt-text="Table API를 사용하여 Cosmos DB 테이블에 저장된 데이터를 보여주는 완성된 애플리케이션의 스크린샷" lightbox="./media/create-table-dotnet/table-api-app-finished-application.png":::

## <a name="1---create-an-azure-cosmos-db-account"></a>1 - Azure Cosmos DB 계정 만들기

먼저 애플리케이션에서 사용되는 테이블을 포함할 Cosmos DB 테이블 API 계정을 만들어야 합니다.  이 작업은 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 수행할 수 있습니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에 로그인하고 다음 지침에 따라 Cosmos DB 계정을 만듭니다.

| 지침    | 스크린샷 |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1-240px.png" alt-text="위쪽 도구 모음의 검색 상자를 사용하여 Azure에서 Cosmos DB 계정을 찾는 방법을 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2-240px.png" alt-text="Azure의 Cosmos DB 계정 페이지에서 [만들기] 단추 위치를 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3-240px.png" alt-text="선택할 올바른 옵션으로 Azure Table 옵션을 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-3.png":::           |
| [!INCLUDE [Create cosmos db account step 1](<./includes/create-table-dotnet/create-cosmos-db-acct-4.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4-240px.png" alt-text="[Cosmos DB 계정 만들기] 페이지에서 필드를 채우는 방법을 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-account-table-api-4.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB 계정은 [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 만듭니다. Cosmos DB 내에서 테이블 스토리지를 사용하도록 `--capabilities EnableTable` 옵션을 포함해야 합니다.  모든 Azure 리소스가 리소스 그룹에 포함되어야 하므로, 다음 코드 조각은 Cosmos DB 계정에 대한 리소스 그룹도 만듭니다.

Cosmos DB 계정 이름은 3자~44자 사이여야 하며 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다.  또한 Cosmos DB 계정 이름은 Azure에서 고유해야 합니다.

Azure CLI 명령은 [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure CLI가 설치된](/cli/azure/install-azure-cli) 워크스테이션에서 실행할 수 있습니다.

일반적으로 Cosmos DB 계정 만들기 프로세스가 완료될 때까지 몇 분 정도 걸립니다.

```azurecli
LOCATION='eastus'
RESOURCE_GROUP_NAME='rg-msdocs-tables-sdk-demo'
COSMOS_ACCOUNT_NAME='cosmos-msdocs-tables-sdk-demo-123'    # change 123 to a unique set of characters for a unique name
COSMOS_TABLE_NAME='WeatherData'

az group create \
    --location $LOCATION \
    --name $RESOURCE_GROUP_NAME

az cosmosdb create \
    --name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --capabilities EnableTable
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Cosmos DB 계정은 [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) cmdlet을 사용하여 만듭니다. Cosmos DB 내에서 테이블 스토리지를 사용하도록 `-ApiKind "Table"` 옵션을 포함해야 합니다.  모든 Azure 리소스가 리소스 그룹에 포함되어야 하므로, 다음 코드 조각은 Azure Cosmos DB 계정에 대한 리소스 그룹도 만듭니다.

Azure Cosmos DB 계정 이름은 3자~44자 사이여야 하며 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다.  또한 Azure Cosmos DB 계정 이름은 Azure에서 고유해야 합니다.

Azure PowerShell 명령은 [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell이 설치된](/powershell/azure/install-az-ps) 워크스테이션에서 실행할 수 있습니다.

일반적으로 Cosmos DB 계정 만들기 프로세스가 완료될 때까지 몇 분 정도 걸립니다.

```azurepowershell
$location = 'eastus'
$resourceGroupName = 'rg-msdocs-tables-sdk-demo'
$cosmosAccountName = 'cosmos-msdocs-tables-sdk-demo-123'  # change 123 to a unique set of characters for a unique name

# Create a resource group
New-AzResourceGroup `
    -Location $location `
    -Name $resourceGroupName

# Create an Azure Cosmos DB 
New-AzCosmosDBAccount `
    -Name $cosmosAccountName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -ApiKind "Table"
```

---

## <a name="2---create-a-table"></a>2 - 테이블 만들기

다음으로, Cosmos DB 계정 내에 애플리케이션에서 사용할 테이블을 만들어야 합니다.  기존 데이터베이스와 달리, 테이블의 속성(열)이 아니라 테이블의 이름만 지정하면 됩니다.  데이터가 테이블에 로드되면 필요에 따라 속성(열)이 자동으로 만들어집니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에서 다음 단계를 완료하여 Cosmos DB 계정 내에 테이블을 만듭니다.

| 지침    | 스크린샷 |
|:----------------|-----------:|
| [!INCLUDE [Create cosmos db table step 1](<./includes/create-table-dotnet/create-cosmos-table-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1-240px.png" alt-text="위쪽 도구 모음의 검색 상자를 사용하여 Cosmos DB 계정을 찾는 방법을 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-1.png":::           |
| [!INCLUDE [Create cosmos db table step 2](<./includes/create-table-dotnet/create-cosmos-table-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2-240px.png" alt-text="[테이블 추가] 단추의 위치를 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-2.png":::           |
| [!INCLUDE [Create cosmos db table step 3](<./includes/create-table-dotnet/create-cosmos-table-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3-240px.png" alt-text="Cosmos DB 테이블에 대한 [새 테이블] 대화 상자를 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-create-cosmos-db-table-api-3.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Cosmos DB의 테이블은 [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create) 명령을 사용하여 만듭니다.

```azurecli
COSMOS_TABLE_NAME='WeatherData'

az cosmosdb table create \
    --account-name $COSMOS_ACCOUNT_NAME \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_TABLE_NAME \
    --throughput 400
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Cosmos DB의 테이블은 [New-AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) cmdlet을 사용하여 만듭니다.

```azurepowershell
$cosmosTableName = 'WeatherData'

# Create the table for the application to use
New-AzCosmosDBTable `
    -Name $cosmosTableName `
    -AccountName $cosmosAccountName `
    -ResourceGroupName $resourceGroupName
```

---

## <a name="3---get-cosmos-db-connection-string"></a>3 - Cosmos DB 연결 문자열 가져오기

Cosmos DB의 테이블에 액세스하려면 앱에 CosmosDB Storage 계정에 대한 테이블 연결 문자열이 필요합니다.  이 연결 문자열은 Azure Portal, Azure CLI 또는 Azure PowerShell을 사용하여 검색할 수 있습니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

| 지침    | 스크린샷 |
|:----------------|-----------:|
| [!INCLUDE [Get cosmos db table connection string step 1](<./includes/create-table-dotnet/get-cosmos-connection-string-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1-240px.png" alt-text="Cosmos DB 페이지에서 연결 문자열 링크의 위치를 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-1.png":::           |
| [!INCLUDE [Get cosmos db table connection string step 2](<./includes/create-table-dotnet/get-cosmos-connection-string-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2-240px.png" alt-text="애플리케이션에서 선택하고 사용할 연결 문자열을 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-cosmos-db-table-connection-string-2.png":::           |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 기본 테이블 스토리지 연결 문자열을 가져오려면 [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) 명령과 `--type connection-strings` 옵션을 사용합니다.  이 명령은 [JMESPath 쿼리](https://jmespath.org/)를 사용하여 기본 테이블 연결 문자열만 표시합니다.

```azurecli
# This gets the primary Table connection string
az cosmosdb keys list \
    --type connection-strings \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $COSMOS_ACCOUNT_NAME \
    --query "connectionStrings[?description=='Primary Table Connection String'].connectionString" \
    --output tsv
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 기본 테이블 스토리지 연결 문자열을 가져오려면 [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) cmdlet을 사용합니다.

```azurepowershell
# This gets the primary Table connection string  
 $(Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $cosmosAccountName `
    -Type "ConnectionStrings")."Primary Table Connection String"
```

---

Cosmos DB 계정에 대한 연결 문자열은 앱 비밀로 간주되며 다른 앱 비밀 또는 암호처럼 보호해야 합니다.  이 예제에서는 [비밀 관리자 도구](/aspnet/core/security/app-secrets#secret-manager)를 사용하여 개발 중에 연결 문자열을 저장하고 애플리케이션에 제공합니다.  비밀 관리자 도구는 Visual Studio 또는 .NET CLI에서 액세스할 수 있습니다.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio에서 비밀 관리자 도구를 열려면 프로젝트를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **사용자 비밀 관리** 를 선택합니다.  그러면 프로젝트의 *secrets.json* 파일이 열립니다.  파일의 내용을 아래 JSON으로 바꾸고, Cosmos DB 테이블 연결 문자열에서 바꿉니다.

```json
{
  "ConnectionStrings": {
    "CosmosTableApi": "<cosmos db table connection string>"
  }  
}
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

비밀 관리자를 사용하려면 먼저 `dotnet user-secrets init` 명령을 사용하여 프로젝트에 대해 초기화해야 합니다.

```dotnetcli
dotnet user-secrets init
```

그런 다음, `dotnet user-secrets set` 명령을 사용하여 Cosmos DB 테이블 연결 문자열을 비밀로 추가합니다.

```dotnetcli
dotnet user-secrets set "ConnectionStrings:CosmosTableApi" "<cosmos db table connection string>"
```

---

## <a name="4---install-azuredatatables-nuget-package"></a>4 - Azure.Data.Tables NuGet 패키지 설치

.NET 애플리케이션에서 Cosmos DB Table API에 액세스하려면 [Azure.Data.Tables](https://www.nuget.org/packages/Azure.Data.Tables) NuGet 패키지를 설치합니다.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```PowerShell
Install-Package Azure.Data.Tables
```

### <a name="net-cli"></a>[.NET CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Azure.Data.Tables
```

---

## <a name="5---configure-the-table-client-in-startupcs"></a>5 - Startup.cs에서 테이블 클라이언트 구성

Azure SDK는 클라이언트 개체를 통해 Azure와 통신하여 Azure에 대해 여러 가지 작업을 실행합니다.  [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체는 Cosmos DB Table API와 통신하는 데 사용되는 개체입니다.

애플리케이션은 일반적으로 애플리케이션 전체에서 사용할 [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체를 테이블마다 하나씩 만듭니다.  이 작업을 수행하려면 DI(종속성 주입)를 사용하고 [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체를 싱글톤으로 등록하는 것이 좋습니다.  Azure SDK에서 DI 사용에 대한 자세한 내용은 [.NET용 Azure SDK를 사용하여 종속성 주입](/dotnet/azure/sdk/dependency-injection)을 참조하세요.

애플리케이션의 `Startup.cs` 파일에서 다음 코드 조각과 일치하도록 ConfigureServices() 메서드를 편집합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddMvcOptions(options =>
        {
            options.Filters.Add(new ValidationFilter());
        });
    
    var connectionString = Configuration.GetConnectionString("CosmosTableApi");
    services.AddSingleton<TableClient>(new TableClient(connectionString, "WeatherData"));
    
    services.AddSingleton<TablesService>();
}
```

또한 Startup.cs 파일의 맨 위에 있는 명령문을 사용하여 다음을 추가해야 합니다.

```csharp
using Azure.Data.Tables;
```

## <a name="6---implement-cosmos-db-table-operations"></a>6 - Cosmos DB 테이블 작업 구현

샘플 앱에 대한 모든 Cosmos DB 테이블 작업은 *Services* 디렉터리에 있는 `TableService` 클래스에서 구현됩니다.  `Azure.Data.Tables` SDK 패키지의 개체와 함께 사용하려면 이 파일의 맨 위에 있는 `Azure` 및 `Azure.Data.Tables` 네임스페이스를 가져와야 합니다.

```csharp
using Azure;
using Azure.Data.Tables;
```

[TableClient](/dotnet/api/azure.data.tables.tableclient) 개체를 이 클래스에 삽입할 수 있도록 `TableService` 클래스의 시작 부분에 [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체의 멤버 변수와 생성자를 추가합니다.

```csharp
private TableClient _tableClient;

public TablesService(TableClient tableClient)
{
    _tableClient = tableClient;
}
```

### <a name="get-rows-from-a-table"></a>테이블에서 행 가져오기

[TableClient](/dotnet/api/azure.data.tables.tableclient) 클래스에는 테이블에서 행을 선택할 수 있는 [Query](/dotnet/api/azure.data.tables.tableclient.query)라는 메서드가 포함되어 있습니다.  이 예제에서는 이 메서드에 전달되는 매개 변수가 없으므로 테이블의 모든 행이 선택됩니다.

또한 이 메서드는 모델 클래스 데이터가 반환될 형식을 지정하는 [ITableEntity](/dotnet/api/azure.data.tables.itableentity) 형식의 제네릭 매개 변수를 사용합니다. 이 경우 기본 제공 클래스 [TableEntity](/dotnet/api/azure.data.tables.itableentity)가 사용됩니다. 즉, `Query` 메서드는 `Pageable\<TableEntity\>` 컬렉션을 결과로 반환합니다.

```csharp
public IEnumerable<WeatherDataModel> GetAllRows()
{
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>();

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

`Azure.Data.Tables` 패키지에 정의된 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 클래스에는 테이블의 파티션 키 및 행 키 값에 대한 속성이 있습니다.  이러한 두 값을 테이블에 있는 행의 고유 키 값으로 사용할 수 있습니다.  이 예제 애플리케이션에서는 기상 관측소(도시)의 이름이 파티션 키에 저장되고 관측 날짜/시간이 행 키에 저장됩니다.  그 외의 모든 속성(온도, 습도, 풍속)은 `TableEntity` 개체의 사전에 저장됩니다.

[TableEntity](/dotnet/api/azure.data.tables.tableentity) 개체를 자체 정의의 개체에 매핑하는 것이 일반적입니다.  샘플 애플리케이션은 이러한 목적을 위해 *Models* 디렉터리에 `WeatherDataModel` 클래스를 정의합니다.  이 클래스는 파티션 키와 행 키가 매핑되는 관측소 이름 및 관측 날짜에 대한 속성을 갖고 있으며, 이러한 값에 대한 보다 의미 있는 속성 이름을 제공합니다.  그런 다음, 사전을 사용하여 개체의 나머지 속성을 저장합니다.  행에 임의의 속성이 여러 개 있을 수 있고 모델 개체가 모든 속성을 캡처할 수 있기를 원하기 때문에 테이블 스토리지를 사용할 때 일반적인 패턴입니다.  이 클래스에는 클래스의 속성을 나열하는 메서드도 포함되어 있습니다.

```csharp
public class WeatherDataModel 
{
    // Captures all of the weather data properties -- temp, humidity, wind speed, etc
    private Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public DateTimeOffset? Timestamp { get; set; }

    public string Etag { get; set; }

    public object this[string name] 
    { 
        get => ( ContainsProperty(name)) ? _properties[name] : null; 
        set => _properties[name] = value; 
    }
    
    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);       
}
```

`MapTableEntityToWeatherDataModel` 메서드는 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 개체를 `WeatherDataModel` 개체에 매핑하는 데 사용됩니다.  [TableEntity](/dotnet/api/azure.data.tables.itableentity) 개체에는 개체에 대한 테이블에 포함된 모든 속성 이름(실질적으로는 테이블의 이 행에 대한 열 이름)을 가져오는 [Keys](/dotnet/api/azure.data.tables.tableentity.keys) 속성이 포함되어 있습니다.  `MapTableEntityToWeatherDataModel` 메서드는 `PartitionKey`, `RowKey`, `Timestamp` 및 `Etag` 속성을 직접 매핑한 다음, `Keys` 속성을 사용하여 `TableEntity` 개체의 다른 속성을 반복하고 이미 직접 매핑된 속성을 제외한 나머지 속성을 `WeatherDataModel` 개체에 매핑합니다.

다음 코드 블록과 일치하도록 `MapTableEntityToWeatherDataModel` 메서드의 코드를 편집합니다.

```csharp
public WeatherDataModel MapTableEntityToWeatherDataModel(TableEntity entity)
{
    WeatherDataModel observation = new WeatherDataModel();
    observation.StationName = entity.PartitionKey;
    observation.ObservationDate = entity.RowKey;
    observation.Timestamp = entity.Timestamp;
    observation.Etag = entity.ETag.ToString();

    var measurements = entity.Keys.Where(key => !EXCLUDE_TABLE_ENTITY_KEYS.Contains(key));
    foreach (var key in measurements)
    {
        observation[key] = entity[key];
    }
    return observation;            
}
```

### <a name="filter-rows-returned-from-a-table"></a>테이블에서 반환된 행 필터링

테이블에서 반환된 행을 필터링하려면 OData 스타일 필터 문자열을 [Query](/dotnet/api/azure.data.tables.tableclient.query) 메서드에 전달하면 됩니다. 예를 들어 2021년 7월 1일 자정부터 2021년 7월 2일 자정(포함) 사이의 모든 시카고 날씨 판독값을 가져오려면 다음 필터 문자열을 전달합니다.

```odata
PartitionKey eq 'Chicago' and RowKey ge '2021-07-01 12:00 AM' and RowKey le '2021-07-02 12:00 AM'
```

OData 웹 사이트의 [필터 시스템 쿼리 옵션](https://www.odata.org/documentation/odata-version-2-0/uri-conventions/) 섹션에서 모든 OData 필터 연산자를 확인할 수 있습니다.

예제 애플리케이션에서 `FilterResultsInputModel` 개체는 사용자가 제공한 필터 조건을 캡처하도록 설계되었습니다.

```csharp
public class FilterResultsInputModel : IValidatableObject
{
    public string PartitionKey { get; set; }
    public string RowKeyDateStart { get; set; }
    public string RowKeyTimeStart { get; set; }
    public string RowKeyDateEnd { get; set; }
    public string RowKeyTimeEnd { get; set; }
    [Range(-100, +200)]
    public double? MinTemperature { get; set; }
    [Range(-100,200)]
    public double? MaxTemperature { get; set; }
    [Range(0, 300)]
    public double? MinPrecipitation { get; set; }
    [Range(0,300)]
    public double? MaxPrecipitation { get; set; }
}
```

이 개체는 `TableService` 클래스의 `GetFilteredRows` 메서드에 전달되면 null이 아닌 각 속성 값에 대한 필터 문자열을 만듭니다.  그런 다음, 모든 값을 "and" 절과 조인하여 결합된 필터 문자열을 만듭니다.  이 결합된 필터 문자열은 [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체의 [Query](/dotnet/api/azure.data.tables.tableclient.query) 메서드에 전달되며 필터 문자열과 일치하는 행만 반환됩니다.  비슷한 메서드를 코드에 사용하여 애플리케이션에 필요한 적절한 필터 문자열을 생성할 수 있습니다.

```csharp
public IEnumerable<WeatherDataModel> GetFilteredRows(FilterResultsInputModel inputModel)
{
    List<string> filters = new List<string>();

    if (!String.IsNullOrEmpty(inputModel.PartitionKey))
        filters.Add($"PartitionKey eq '{inputModel.PartitionKey}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateStart) && !String.IsNullOrEmpty(inputModel.RowKeyTimeStart))
        filters.Add($"RowKey ge '{inputModel.RowKeyDateStart} {inputModel.RowKeyTimeStart}'");
    if (!String.IsNullOrEmpty(inputModel.RowKeyDateEnd) && !String.IsNullOrEmpty(inputModel.RowKeyTimeEnd))
        filters.Add($"RowKey le '{inputModel.RowKeyDateEnd} {inputModel.RowKeyTimeEnd}'");
    if (inputModel.MinTemperature.HasValue)
        filters.Add($"Temperature ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxTemperature.HasValue)
        filters.Add($"Temperature le {inputModel.MaxTemperature.Value}");
    if (inputModel.MinPrecipitation.HasValue)
        filters.Add($"Precipitation ge {inputModel.MinTemperature.Value}");
    if (inputModel.MaxPrecipitation.HasValue)
        filters.Add($"Precipitation le {inputModel.MaxTemperature.Value}");

    string filter = String.Join(" and ", filters);
    Pageable<TableEntity> entities = _tableClient.Query<TableEntity>(filter);

    return entities.Select(e => MapTableEntityToWeatherDataModel(e));
}
```

### <a name="insert-data-using-a-tableentity-object"></a>TableEntity 개체를 사용하여 데이터 삽입

테이블에 데이터를 추가하는 가장 간단한 방법은 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 개체를 사용하는 것입니다.  이 예제에서는 데이터가 입력 모델 개체에서 [TableEntity](/dotnet/api/azure.data.tables.itableentity) 개체로 매핑됩니다.  기상 관측소 이름과 관측 날짜/시간을 나타내는 입력 개체의 속성은 각각 [PartitionKey](/dotnet/api/azure.data.tables.tableentity.partitionkey) 및 [RowKey](/dotnet/api/azure.data.tables.tableentity.rowkey) 속성에 매핑되어 테이블의 행에 대한 고유 키를 형성합니다.  그런 다음, 입력 모델 개체의 추가 속성이 TableEntity 개체의 사전 속성에 매핑됩니다.  마지막으로, [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체의 [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) 메서드를 사용하여 테이블에 데이터를 삽입합니다.

다음 코드를 포함하도록 예제 애플리케이션에서 `InsertTableEntity` 클래스를 수정합니다.

```csharp
public void InsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.AddEntity(entity);
}
```

### <a name="upsert-data-using-a-tableentity-object"></a>TableEntity 개체를 사용하여 데이터 upsert

해당 테이블에 이미 있는 파티션 키/행 키 조합으로 테이블에 행을 삽입하려고 하면 오류가 발생합니다.  이러한 이유로 테이블에 행을 추가할 때 AddEntity 메서드 대신 [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity)를 사용하는 것이 좋습니다.  지정된 파티션 키/행 키 조합이 테이블에 이미 있는 경우 [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) 메서드는 기존 행을 업데이트합니다.  그렇지 않으면 행이 테이블에 추가됩니다.

```csharp
public void UpsertTableEntity(WeatherInputModel model)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = model.StationName;
    entity.RowKey = $"{model.ObservationDate} {model.ObservationTime}";

    // The other values are added like a items to a dictionary
    entity["Temperature"] = model.Temperature;
    entity["Humidity"] = model.Humidity;
    entity["Barometer"] = model.Barometer;
    entity["WindDirection"] = model.WindDirection;
    entity["WindSpeed"] = model.WindSpeed;
    entity["Precipitation"] = model.Precipitation;

    _tableClient.UpsertEntity(entity);
}
```

### <a name="insert-or-upsert-data-with-variable-properties"></a>변수 속성이 있는 데이터 삽입 또는 upsert

Cosmos DB Table API를 사용하면 좋은 중 하나는 테이블에 로드되는 개체에 새 속성이 포함된 경우 해당 속성이 테이블에 자동으로 추가되고 값이 Cosmos DB에 저장된다는 것입니다.  기존 데이터베이스에서 하는 것처럼 열을 추가하기 위해 ALTER TABLE과 같은 DDL 문을 실행할 필요가 없습니다.

이 모델은 시간에 따라 캡처해야 하는 데이터를 추가하거나 수정할 수 있는 데이터 원본을 처리할 때 또는 다른 입력에서 애플리케이션에 다른 데이터를 제공할 때 애플리케이션에 유연성을 제공합니다. 샘플 애플리케이션에서는 기본 날씨 데이터뿐 아니라 몇 가지 추가 값도 전송하는 기상 관측소를 시뮬레이션할 수 있습니다. 이러한 새 속성을 가진 개체가 처음으로 테이블에 저장되면 해당 속성(열)이 테이블에 자동으로 추가됩니다.

샘플 애플리케이션에서 `ExpandableWeatherObject` 클래스는 개체의 속성 세트를 지원하기 위해 내부 사전을 중심으로 빌드됩니다.  이 클래스는 개체가 임의의 속성 세트를 포함해야 하는 경우의 일반적인 패턴을 나타냅니다.

```csharp
public class ExpandableWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }

    public string ObservationDate { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

Table API를 사용하여 이러한 개체를 삽입하거나 upsert하려면 확장 가능한 개체의 속성을 [TableEntity](/dotnet/api/azure.data.tables.tableentity) 개체에 매핑하고, [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체에서 [AddEntity](/dotnet/api/azure.data.tables.tableclient.addentity) 또는 [UpsertEntity](/dotnet/api/azure.data.tables.tableclient.upsertentity) 메서드를 적절하게 사용합니다.

```csharp
public void InsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.AddEntity(entity);
}

        
public void UpsertExpandableData(ExpandableWeatherObject weatherObject)
{
    TableEntity entity = new TableEntity();
    entity.PartitionKey = weatherObject.StationName;
    entity.RowKey = weatherObject.ObservationDate;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }
    _tableClient.UpsertEntity(entity);
}

```
  
### <a name="update-an-entity"></a>엔터티 업데이트

[TableClient](/dotnet/api/azure.data.tables.tableclient) 개체에서 [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) 메서드를 호출하여 엔터티를 업데이트할 수 있습니다.  Table API를 사용하여 저장된 엔터티(행)에는 임의의 속성 세트가 포함될 수 있으므로, 앞에서 설명한 `ExpandableWeatherObject`와 유사한 사전 개체를 중심으로 업데이트 개체를 만드는 것이 좋은 경우가 자주 있습니다.  이 경우 유일한 차이점은 업데이트 중에 동시성 제어에 사용되는 `Etag` 속성이 추가된다는 것입니다.

```csharp
public class UpdateWeatherObject
{
    public Dictionary<string, object> _properties = new Dictionary<string, object>();

    public string StationName { get; set; }
    public string ObservationDate { get; set; }
    public string Etag { get; set; }

    public object this[string name]
    {
        get => (ContainsProperty(name)) ? _properties[name] : null;
        set => _properties[name] = value;
    }

    public ICollection<string> PropertyNames => _properties.Keys;

    public int PropertyCount => _properties.Count;

    public bool ContainsProperty(string name) => _properties.ContainsKey(name);
}
```

샘플 앱에서 이 개체는 `TableService` 클래스의 `UpdateEntity` 메서드에 전달됩니다.  이 메서드는 먼저 [TableClient](/dotnet/api/azure.data.tables.tableclient)의 [GetEntity](/dotnet/api/azure.data.tables.tableclient.getentity) 메서드를 사용하여 Table API의 기존 엔터티를 로드합니다.  그런 다음, 해당 엔터티 개체를 업데이트하고 `UpdateEntity` 메서드를 사용하여 업데이트를 데이터베이스에 저장합니다.  [UpdateEntity](/dotnet/api/azure.data.tables.tableclient.updateentity) 메서드가 개체의 현재 Etag를 사용하여 개체가 처음 로드된 이후에 변경되지 않도록 하는 방법을 잘 보세요.  개의치 않고 엔터티를 업데이트하려는 경우 `Etag.Any` 값을 `UpdateEntity` 메서드에 전달하면 됩니다.

```csharp
public void UpdateEntity(UpdateWeatherObject weatherObject)
{
    string partitionKey = weatherObject.StationName;
    string rowKey = weatherObject.ObservationDate;

    // Use the partition key and row key to get the entity
    TableEntity entity = _tableClient.GetEntity<TableEntity>(partitionKey, rowKey).Value;

    foreach (string propertyName in weatherObject.PropertyNames)
    {
        var value = weatherObject[propertyName];
        entity[propertyName] = value;
    }

    _tableClient.UpdateEntity(entity, new ETag(weatherObject.Etag));
}
```

### <a name="remove-an-entity"></a>엔터티 제거

테이블에서 엔터티를 제거하려면 개체의 파티션 키와 행 키를 사용하여 [TableClient](/dotnet/api/azure.data.tables.tableclient) 개체에서 [DeleteEntity](/dotnet/api/azure.data.tables.tableclient.deleteentity) 메서드를 호출합니다.

```csharp
public void RemoveEntity(string partitionKey, string rowKey)
{
    _tableClient.DeleteEntity(partitionKey, rowKey);           
}
```

## <a name="7---run-the-code"></a>7 - 코드 실행

샘플 애플리케이션을 실행하여 Cosmos DB Table API와 상호 작용합니다.  애플리케이션을 처음 실행하면 테이블이 비어 있기 때문에 데이터가 없습니다.  애플리케이션 맨 위에 있는 단추를 사용하여 테이블에 데이터를 추가합니다.

:::image type="content" source="./media/create-table-dotnet/table-api-app-data-insert-buttons-480px.png" alt-text="Table API를 사용하여 Cosmos DB에 데이터를 삽입하는 데 사용되는 단추의 위치를 보여주는 애플리케이션의 스크린샷" lightbox="./media/create-table-dotnet/table-api-app-data-insert-buttons.png":::

**테이블 엔터티를 사용하여 삽입** 단추를 선택하면 `TableEntity` 개체를 사용하여 새 행을 삽입하거나 upsert할 수 있는 대화 상자가 열립니다.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-table-entity-480px.png" alt-text="TableEntity 개체를 사용하여 데이터를 삽입하는 데 사용되는 대화 상자를 보여주는 애플리케이션의 스크린샷" lightbox="./media/create-table-dotnet/table-api-app-insert-table-entity.png":::

**확장 가능한 데이터를 사용하여 삽입** 단추를 선택하면 사용자 지정 속성을 사용하여 개체를 삽입할 수 있는 대화 상자가 나타나고, Cosmos DB Table API가 필요할 때 자동으로 테이블에 속성(열)을 추가하는 방법을 보여줍니다.  *사용자 지정 필드 추가* 단추를 사용하여 하나 이상의 새 속성을 추가하고 이 기능을 실습해 보세요.

:::image type="content" source="./media/create-table-dotnet/table-api-app-insert-expandable-entity-480px.png" alt-text="사용자 지정 필드가 포함된 개체를 사용하여 데이터를 삽입하는 데 사용되는 대화 상자를 보여주는 애플리케이션의 스크린샷" lightbox="./media/create-table-dotnet/table-api-app-insert-expandable-entity.png":::

**샘플 데이터 삽입** 단추를 사용하여 일부 샘플 데이터를 Cosmos DB 테이블에 로드합니다.

:::image type="content" source="./media/create-table-dotnet/table-api-app-sample-data-insert-480px.png" alt-text="[샘플 데이터 삽입] 단추의 위치를 보여주는 애플리케이션의 스크린샷" lightbox="./media/create-table-dotnet/table-api-app-sample-data-insert.png":::

위쪽 메뉴에서 **결과 필터링** 항목을 선택하여 [결과 필터링] 페이지로 이동합니다.  이 페이지에서 필터 조건을 입력하여 필터 절을 빌드하고 Cosmos DB Table API에 전달하는 방법을 실습해 보세요.

:::image type="content" source="./media/create-table-dotnet/table-api-app-filter-data-480px.png" alt-text="[결과 필터링] 페이지가 표시되고 이 페이지로 이동하는 데 사용되는 메뉴 항목이 강조 표시된 애플리케이션의 스크린샷" lightbox="./media/create-table-dotnet/table-api-app-filter-data.png":::

## <a name="clean-up-resources"></a>리소스 정리

샘플 애플리케이션을 마쳤으면 Azure 계정에서 이 문서와 관련된 모든 Azure 리소스를 제거해야 합니다.  이렇게 하려면 리소스 그룹을 삭제하면 됩니다.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에서 다음을 수행하여 리소스 그룹을 삭제할 수 있습니다.

| 지침    | 스크린샷 |
|:----------------|-----------:|
| [!INCLUDE [Delete resource group step 1](<./includes/create-table-dotnet/remove-resource-group-1.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-1-240px.png" alt-text="리소스 그룹을 검색하는 방법을 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-1.png"::: |
| [!INCLUDE [Delete resource group step 2](<./includes/create-table-dotnet/remove-resource-group-2.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-2-240px.png" alt-text="[리소스 그룹 삭제] 단추의 위치를 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-2.png"::: |
| [!INCLUDE [Delete resource group step 3](<./includes/create-table-dotnet/remove-resource-group-3.md>)] | :::image type="content" source="./media/create-table-dotnet/azure-portal-remove-resource-group-3-240px.png" alt-text="리소스 그룹 삭제에 대한 확인 대화 상자를 보여주는 스크린샷" lightbox="./media/create-table-dotnet/azure-portal-remove-resource-group-3.png"::: |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 리소스 그룹을 삭제하려면 [az group delete](/cli/azure/group#az_group_delete) 명령에서 삭제할 리소스 그룹의 이름을 입력합니다.  리소스 그룹을 삭제하면 리소스 그룹에 포함된 리소스도 함께 삭제됩니다.

```azurecli
az group delete --name $RESOURCE_GROUP_NAME
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령에서 삭제할 리소스 그룹의 이름을 입력합니다.  리소스 그룹을 삭제하면 리소스 그룹에 포함된 리소스도 함께 삭제됩니다.

```azurepowershell
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 테이블을 만들고, 앱을 실행하는 방법을 알아보았습니다.  이제 테이블 API를 사용하여 데이터를 쿼리할 수 있습니다.  

> [!div class="nextstepaction"]
> [Table API로 테이블 데이터 가져오기](table-import.md)
