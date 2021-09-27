---
title: Azure Data Lake Storage 쿼리 가속을 사용하여 데이터 필터링
description: 쿼리 가속을 사용하여 스토리지 계정에서 데이터 하위 집합을 검색합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 840c31dc8110405eee02745f773f14f33ab059df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636784"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage 쿼리 가속을 사용하여 데이터 필터링

이 문서에서는 쿼리 가속을 사용하여 스토리지 계정에서 데이터 하위 집합을 검색하는 방법을 보여 줍니다.

쿼리 가속을 사용하면 애플리케이션 및 분석 프레임워크에서 지정된 작업을 수행하는 데 필요한 데이터만 검색하여 데이터 처리를 획기적으로 최적화하도록 할 수 있습니다. 자세히 알아보려면 [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Storage에 액세스하려면 Azure 구독이 있어야 합니다. 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- **범용 v2** 스토리지 계정. [스토리지 계정 만들기](../common/storage-account-create.md)를 참조하세요.

- SDK 관련 필수 구성 요소를 보려면 탭을 선택합니다.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  해당 사항 없음

  ### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download)

  ### <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

  - [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상

  - [Apache Maven](https://maven.apache.org/download.cgi)

    > [!NOTE]
    > 이 문서에서는 Apache Maven을 사용하여 Java 프로젝트를 만들었다고 가정합니다. Apache Maven을 사용하여 프로젝트를 만드는 방법에 대한 예는 [설정](storage-quickstart-blobs-java.md#setting-up)을 참조하세요.

  ### <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 이상

  ### <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

  Node.js SDK를 사용하는 데 필요한 추가 필수 구성 요소는 없습니다.

---

## <a name="enable-query-acceleration"></a>쿼리 가속을 사용하도록 설정

쿼리 가속을 사용하려면 구독에 쿼리 가속 기능을 등록해야 합니다. 기능이 등록되었는지 확인한 후 Azure Storage 리소스 공급자를 등록해야 합니다.

### <a name="step-1-register-the-query-acceleration-feature"></a>1단계: 쿼리 가속 기능 등록

쿼리 가속을 사용하려면 먼저 구독에 쿼리 가속 기능을 등록해야 합니다.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows PowerShell 명령 창을 엽니다.

1. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

2. ID가 둘 이상의 구독과 연결되어 있으면 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 명령을 사용하여 쿼리 가속 기능을 등록합니다.

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. ID가 둘 이상의 구독과 연결된 경우 스토리지 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. [az feature register](/cli/azure/feature#az_feature_register) 명령을 사용하여 쿼리 가속 기능을 등록합니다.

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>2단계: 기능이 등록되어 있는지 확인

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

등록이 완료되어 있는지 확인하려면 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 사용합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

등록이 완료되어 있는지 확인하려면 [az feature](/cli/azure/feature#az_feature_show) 명령을 사용합니다.

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>3단계: Azure Storage 리소스 공급자 등록

등록이 승인된 후 Azure Storage 리소스 공급자를 다시 등록해야 합니다.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

리소스 공급자를 등록하려면 [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 사용합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

리소스 공급자를 등록하려면 [az provider register](/cli/azure/provider#az_provider_register) 명령을 사용합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>환경 설정

### <a name="step-1-install-packages"></a>1단계: 패키지 설치

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az 모듈 버전 4.6.0 이상을 설치합니다.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

이전 버전의 Az에서 업데이트하려면 다음 명령을 실행합니다.

```powershell
Update-Module -Name Az
```

#### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

1. 명령 프롬프트를 열고 디렉터리(`cd`)를 프로젝트 폴더로 변경합니다. 예는 다음과 같습니다.

   ```console
   cd myProject
   ```

2. `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Blob Storage 클라이언트 라이브러리의 `12.5.0-preview.6` 버전 이상을 설치합니다.

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. 이 문서에 나오는 예에서는 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 라이브러리를 사용하여 CSV 파일을 구문 분석합니다. 해당 라이브러리를 사용하려면 다음 명령을 사용합니다.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

1. 텍스트 편집기에서 프로젝트의 *pom.xml* 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

[pip](https://pypi.org/project/pip/)를 사용하여 Python용 Azure Data Lake Storage 클라이언트 라이브러리를 설치합니다.

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

터미널 창을 열고 다음 명령을 입력하여 JavaScript용 Data Lake 클라이언트 라이브러리를 설치합니다.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>2단계: 문 추가

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

해당 사항 없음

#### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

코드 파일 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

쿼리 가속은 CSV 및 Json 형식의 데이터를 검색합니다. 따라서 사용하기로 선택한 모든 CSV 또는 Json 구문 분석 라이브러리에 대해 using 문을 추가해야 합니다. 이 문서에 나오는 예에서는 NuGet에서 사용 가능한 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 라이브러리를 사용하여 CSV 파일을 구문 분석합니다. 따라서 코드 파일 맨 위에 다음 `using` 문을 추가합니다.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

이 문서에서 제공된 예를 컴파일하려면 다음 `using` 문도 추가해야 합니다.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

코드 파일 맨 위에 다음 `import` 문을 추가합니다.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

코드 파일 맨 위에 다음 import 문을 추가합니다.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

이 문을 코드 파일의 맨 위에 배치하여 `storage-blob` 모듈을 포함합니다.

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

쿼리 가속은 CSV 및 Json 형식의 데이터를 검색합니다. 따라서 사용하기로 선택한 모든 CSV 또는 Json 구문 분석 모듈에 대해 문을 추가해야 합니다. 이 문서에 나오는 예에서는 [fast-csv](https://www.npmjs.com/package/fast-csv) 모듈을 사용하여 CSV 파일을 구문 분석합니다. 따라서 코드 파일의 맨 위에 이 문을 추가합니다.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>필터를 사용하여 데이터 검색

SQL을 사용하여 쿼리 가속 요청에서 행 필터 조건자 및 열 프로젝션을 지정할 수 있습니다. 다음 코드는 스토리지의 CSV 파일을 쿼리하고 세 번째 열이 `Hemingway, Ernest` 값과 일치하는 데이터의 모든 행을 반환합니다.

- SQL 쿼리에서 `BlobStorage` 키워드는 쿼리 중인 파일을 나타내는 데 사용됩니다.

- 열 참조는 첫 번째 열이 `_1`인 `_N`으로 지정됩니다. 원본 파일에 머리글 행이 포함되어 있으면 머리글 행에 지정된 이름으로 열을 참조할 수 있습니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

`BlobQuickQueryClient.QueryAsync` 비동기 메서드는 쿼리 가속 API로 쿼리를 전송한 다음 [Stream](/dotnet/api/system.io.stream) 개체로 결과를 애플리케이션에 다시 스트리밍합니다.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

`BlobQuickQueryClient.openInputStream()` 메서드는 쿼리 가속 API로 쿼리를 전송한 다음 다른 InputStream 개체처럼 읽힐 수 있는 `InputStream` 개체로 결과를 애플리케이션에 다시 스트리밍합니다.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

이 예에서는 쿼리 가속 API로 쿼리를 전송한 다음 결과를 다시 스트리밍합니다. `queryHemingway` 도우미 함수로 전달된 `blob` 개체는 [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 유형입니다. [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 개체를 가져오는 방법에 대해 자세히 알아보려면 [빠른 시작: Node.js에서 JavaScript v12 SDK로 BLOB 관리](storage-quickstart-blobs-nodejs.md)를 참조하세요.

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>특정 열 검색

열의 하위 집합으로 결과의 범위를 지정할 수 있습니다. 이렇게 하면 지정된 계산을 수행하는 데 필요한 열만 검색할 수 있습니다. 네트워크를 통해 전송되는 데이터가 더 적기 때문에 애플리케이션 성능이 향상되고 비용이 절감됩니다.

> [!NOTE]
> 결과 범위를 지정할 수 있는 최대 열 수는 49입니다. 결과에 49개가 넘는 열이 포함되어야 하는 경우 SELECT 식에 와일드카드 문자(`*`)를 사용합니다(예: `SELECT *`).

이 코드는 데이터 집합의 모든 책에 대해 `BibNum` 열만 검색합니다. 또한 원본 파일의 머리글 행에 있는 정보를 사용하여 쿼리의 열을 참조합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

다음 코드에서는 행 필터링 및 열 프로젝션을 동일한 쿼리로 결합합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType
            FROM BlobStorage
            WHERE ItemType IN
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType
        FROM BlobStorage
        WHERE ItemType IN
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs-v12-sdk"></a>[Node.js v12 SDK](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " +
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Storage 쿼리 가속](data-lake-storage-query-acceleration.md)
- [쿼리 가속 SQL 언어 참조](query-acceleration-sql-reference.md)
