---
title: Azure Cosmos DB에서 Table API 계정으로 기존 데이터 마이그레이션
description: Azure Cosmos DB에서 온-프레미스 또는 클라우드 데이터를 Azure Table API 계정으로 마이그레이션하거나 가져오는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: c47b92d416bd24554c6e4cecc1aadb81c331616e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061271"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

이 자습서에서는 Azure Cosmos DB [테이블 API](introduction.md)로 사용할 데이터 가져오기에 대한 지침을 제공합니다. Azure Table Storage에 저장된 데이터가 있는 경우 데이터 마이그레이션 도구 또는 AzCopy를 사용하여 데이터를 Azure Cosmos DB Table API로 가져올 수 있습니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 마이그레이션 도구를 사용하여 데이터 가져오기
> * AzCopy를 사용하여 데이터 가져오기

## <a name="prerequisites"></a>필수 구성 요소

* **처리량 증가:** 데이터 마이그레이션 기간은 개별 컨테이너 또는 컨테이너 세트에 대해 설정한 처리량에 따라 달라집니다. 대량 데이터 마이그레이션의 경우 처리량을 늘려야 합니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요.

* **Azure Cosmos DB 리소스 만들기:** 데이터 마이그레이션을 시작하기 전에 Azure Portal에서 모든 테이블을 미리 만듭니다. 데이터베이스 수준 처리량이 있는 Azure Cosmos DB 계정으로 마이그레이션하는 경우에는 Azure Cosmos DB 테이블을 만들 때 파티션 키를 제공해야 합니다.

## <a name="data-migration-tool"></a>데이터 마이그레이션 도구

Azure Cosmos DB의 명령줄 데이터 마이그레이션 도구(dt.exe)를 사용하여 기존 Azure Table Storage 데이터를 Table API 계정으로 가져올 수 있습니다. 

테이블 데이터를 마이그레이션하려면 다음을 수행합니다.

1. 마이그레이션 도구를 [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool)에서 다운로드합니다.
2. 시나리오에 맞는 명령줄 인수를 사용하여 `dt.exe`를 실행합니다. `dt.exe`에서 사용하는 형식은 다음과 같습니다.

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

이 명령에 지원되는 옵션은 다음과 같습니다.

* **/ErrorLog:** (선택 사항) 데이터 전송 실패를 리디렉션하는 CSV 파일의 이름입니다.
* **/OverwriteErrorLog:** (선택 사항) 오류 로그 파일을 덮어씁니다.
* **/ProgressUpdateInterval:** 선택 사항으로, 기본값은 `00:00:01`입니다. 화면 데이터 전송 진행률을 새로 고치는 시간 간격입니다.
* **/ErrorDetails:** 선택 사항으로, 기본값은 `None`입니다. 다음 오류에 대한 자세한 오류 정보를 표시하도록 지정합니다. `None`, `Critical` 또는 `All`.
* **/EnableCosmosTableLog:** (선택 사항) 로그를 Azure Cosmos DB 테이블 계정으로 보냅니다. 설정하는 경우 `/CosmosTableLogConnectionString`도 제공되지 않는 한 이 기본값은 대상 계정 연결 문자열입니다. 이는 도구의 여러 인스턴스가 동시에 실행되는 경우에 유용합니다.
* **/CosmosTableLogConnectionString:** (선택 사항) 원격 Azure Cosmos DB 테이블 계정으로 로그를 보내는 연결 문자열입니다.

### <a name="command-line-source-settings"></a>명령줄 원본 설정

Azure Table Storage를 마이그레이션 원본으로 정의하는 경우 다음 원본 옵션을 사용합니다.

* **/s:AzureTable:** Table Storage에서 데이터를 읽습니다.
* **/s.ConnectionString:** 테이블 엔드포인트의 연결 문자열입니다. Azure Portal에서 검색할 수 있습니다.
* **/s.LocationMode:** 선택 사항으로, 기본값은 `PrimaryOnly`입니다. 다음 Table Storage에 연결할 때 사용할 위치 모드를 지정합니다. `PrimaryOnly`, `PrimaryThenSecondary`, `SecondaryOnly`, `SecondaryThenPrimary`
* **/s.Table:** Azure 테이블의 이름입니다.
* **/s.InternalFields:** `RowKey` 및 `PartitionKey`가 가져오기에 필요하므로 테이블 마이그레이션의 경우 `All`로 설정합니다.
* **/s.Filter:** (선택 사항) 적용할 문자열을 필터링합니다.
* **/s.Projection:** (선택 사항) 선택할 열의 목록입니다.

Table Storage에서 가져올 때 원본 연결 문자열을 검색하려면 Azure Portal을 엽니다. **스토리지 계정** > **계정** > **액세스 키** 를 선택하고 **연결 문자열** 을 복사합니다.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="스토리지 계정 > 계정 > 액세스 키 옵션이 표시되고 복사 단추가 강조 표시된 스크린샷.":::

### <a name="command-line-target-settings"></a>명령줄 대상 설정

Azure Cosmos DB Table API를 마이그레이션 대상으로 정의할 때는 다음 대상 옵션을 사용합니다.

* **/t:TableAPIBulk:** 일괄 처리로 데이터를 Azure Cosmos DB Table API에 업로드합니다.
* **/t.ConnectionString:** 테이블 엔드포인트의 연결 문자열입니다.
* **/t.TableName:** 쓸 테이블의 이름을 지정합니다.
* **/t.Overwrite:** 선택 사항으로, 기본값은 `false`입니다. 기존 값을 덮어쓸지 여부를 지정합니다.
* **/t.MaxInputBufferSize:** 선택 사항으로, 기본값은 `1GB`입니다. 싱크로 데이터를 플러시하기 전에 버퍼에 입력할 바이트의 대략적인 추정치입니다.
* **/t.Throughput:** 선택 사항입니다. 지정되지 않은 경우 서비스 기본값입니다. 테이블에 구성할 처리량을 지정합니다.
* **/t.MaxBatchSize:** 선택 사항으로, 기본값은 `2MB`입니다. 일괄 처리 크기(바이트)를 지정합니다.

### <a name="sample-command-source-is-table-storage"></a>샘플 명령: 원본이 Table Storage인 경우

다음은 Table Storage에서 Table API로 가져오는 방법을 보여 주는 명령줄 샘플입니다.

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmos.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>AzCopy를 사용하여 데이터 마이그레이션

AzCopy 명령줄 유틸리티를 사용하여 Table Storage에서 Azure Cosmos DB Table API로 데이터를 마이그레이션할 수도 있습니다. AzCopy를 사용하려면 [Table Storage에서 데이터 내보내기](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)에 설명된 대로 먼저 데이터를 내보내야 합니다. 그런 다음, 다음 명령을 사용하여 데이터를 Azure Cosmos DB Table API로 가져옵니다. [Azure Table Storage](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage)로 가져올 수도 있습니다.

Azure Cosmos DB로 가져올 때는 다음 샘플을 참조하세요. `/Dest` 값은 `core`가 아닌 `cosmosdb`를 사용합니다.

가져오기 명령 예제:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB Table API를 사용하여 데이터를 쿼리하는 방법을 알아봅니다. 

> [!div class="nextstepaction"]
>[데이터는 어떻게 쿼리하나요?](tutorial-query-table.md)




