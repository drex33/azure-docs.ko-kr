---
title: 매핑 데이터 흐름의 커넥터 및 형식 문제 해결하기
description: Azure Data Factory에서 커넥터 및 형식과 관련된 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 08/17/2021
ms.openlocfilehash: a4125b5755d262a441d470e8fc970ef55ea74de2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641867"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory에서 매핑 데이터 흐름의 커넥터 및 형식 문제 해결하기


본 문서에서는 ADF(Azure Data Factory)에서의 매핑 데이터 흐름의 커넥터 및 형식에 대한 문제 해결 방법을 살펴봅니다.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="account-kind-of-storage-general-purpose-v1-doesnt-support-service-principal-and-mi-authentication"></a>계정 종류가 스토리지인 경우(범용 v1) 서비스 주체 및 MI 인증이 지원되지 않습니다.

#### <a name="symptoms"></a>증상

데이터 흐름에서 서비스 주체 또는 MI 인증과 함께 Azure Blob Storage(범용 v1)를 사용하는 경우 다음 오류 메시지가 나타날 수 있습니다.

`com.microsoft.dataflow.broker.InvalidOperationException: ServicePrincipal and MI auth are not supported if blob storage kind is Storage (general purpose v1)`

#### <a name="cause"></a>원인

데이터 흐름에서 Azure Blob 연결된 서비스를 사용하는 경우 계정 종류가 비어 있거나 "스토리지"이면 관리 ID 또는 서비스 주체 인증이 지원되지 않습니다. 이 상황은 아래 이미지 1과 이미지 2에 나와 있습니다.

이미지 1: Azure Blob Storage 연결된 서비스의 계정 종류

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-kind.png" alt-text="Azure Blob Storage 연결된 서비스의 스토리지 계정 종류를 보여 주는 스크린샷"::: 

이미지 2: 스토리지 계정 페이지

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-page.png" alt-text="스토리지 계정 페이지를 보여 주는 스크린샷" lightbox="./media/data-flow-troubleshoot-connector-format/storage-account-page.png"::: 


#### <a name="recommendation"></a>권장

이 이슈를 해결하려면 다음 권장 사항을 참조하세요.

- Azure Blob 연결된 서비스에서 스토리지 계정 종류가 **없음** 인 경우 적절한 계정 종류를 지정하고 아래에 표시된 이미지 3을 참조하여 작업을 수행합니다. 또한 이미지 2를 참조하여 스토리지 계정 종류를 확인하고, 계정 종류가 스토리지(범용 v1)가 아닌지 확인합니다.

    이미지 3: Azure Blob Storage 연결된 서비스에서 스토리지 계정 종류 지정

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/specify-storage-account-kind.png" alt-text="Azure Blob Storage 연결된 서비스에서 스토리지 계정 종류를 지정하는 방법을 보여 주는 스크린샷"::: 
    

- 계정 종류가 스토리지(범용 v1)인 경우 스토리지 계정을 **범용 v2** 로 업그레이드하거나 다른 인증을 선택합니다.

    이미지 4: 스토리지 계정을 범용 v2로 업그레이드

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png" alt-text="스토리지 계정을 범용 v2로 업그레이드하는 방법을 보여 주는 스크린샷" lightbox="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png"::: 

## <a name="azure-cosmos-db-and-json-format"></a>Azure Cosmos DB 및 JSON 형식

### <a name="support-customized-schemas-in-the-source"></a>소스의 사용자 지정 스키마 지원하기

#### <a name="symptoms"></a>증상
Cosmos DB/JSON에서 다른 데이터 저장소로 데이터를 이동하거나 전송하기 위해 ADF 데이터 흐름을 사용하려는 경우, 원본 데이터의 열이 일부 누락될 수 있습니다. 

#### <a name="cause"></a>원인 
스키마가 없는 커넥터(열 번호, 열 이름, 행별 열 데이터 형식은 다른 것들과 비교해 달라질 수 있습니다)의 경우, 기본적으로 ADF는 샘플 행(예: 최상위 100행 또는 1,000행 데이터)을 사용해 스키마를 유추하고, 유추 결과를 스키마로 사용해 데이터를 읽습니다. 따라서 샘플 행에 표시되지 않는 추가적인 열이 자신의 데이터 저장소에 있다면 해당 열의 데이터는 읽거나 싱크 데이터 저장소로 이동 또는 전송하지 않습니다.

#### <a name="recommendation"></a>권장
기본 동작을 덮어쓰고 추가 필드를 가져오기 위해 ADF에서는 원본 스키마를 사용자 지정할 수 있는 옵션을 제공합니다. 데이터를 읽기 위해 데이터 흐름 원본 프로젝션의 스키마 유추 결과에서 제외될 수 있는 추가/누락 열을 지정할 수 있으며, 다음 옵션 중 하나를 적용해 사용자 지정된 스키마를 설정할 수 있습니다. 일반적으로는 **옵션-1** 이 더 좋습니다.

- **옵션-1**: 복잡한 스키마로 이루어진 수백만 행이 들어 있는 하나의 대형 파일이나 테이블 또는 컨테이너일 수 있는 원본 소스 데이터를 비교해, 읽으려는 열이 모두 들어 있는 소수의 행으로 이루어진 임시 테이블/컨테이너를 만든 뒤에 다음의 작업으로 넘어갈 수 있습니다. 

    1. 해당 데이터 흐름 소스 **디버그 설정** 을 사용해 샘플 파일/테이블을 갖춘 **프로젝션 가져오기** 가 전체 스키마를 가져오도록 합니다. 다음 그림의 단계를 따를 수 있습니다.<br/>

        :::image type="content" source="./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png" alt-text="원본 스키마를 사용자 지정하는 첫 번째 옵션의 첫 번째 부분을 보여 주는 스크린샷":::<br/>
         1. 데이터 흐름 캔버스에서 **디버그 설정** 을 선택합니다.
         1. 팝업 창에서 **cosmosSource** 탭에 **샘플 테이블** 을 선택하고 **테이블** 블록에 자신의 테이블 이름을 입력합니다.
         1. **저장** 을 선택하여 설정을 저장합니다.
         1. **프로젝션 가져오기** 를 선택합니다.<br/>  
    
    1. 나머지 데이터 이동/전송에 해당 원본 데이터 세트를 사용하도록 **디버그 세팅** 을 되돌립니다. 다음 그림의 단계로 진행할 수 있습니다.<br/>

        :::image type="content" source="./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png" alt-text="원본 스키마를 사용자 지정하는 첫 번째 옵션의 두 번째 부분을 보여 주는 스크린샷"::: <br/>   
         1. 데이터 흐름 캔버스에서 **디버그 설정** 을 선택합니다.
         1. 팝업 창에서 **cosmosSource** 탭에 **원본 데이터 세트** 를 선택합니다.
         1. **저장** 을 선택하여 설정을 저장합니다.<br/>
    
    이후, ADF 데이터 흐름 루틴은 원본 데이터 저장소의 데이터를 읽기 위해 사용자 지정 스키마를 인식 후 사용합니다. <br/>

- **옵션-2**: 해당 스키마 및 원본 데이터의 DSL 언어에 익숙한 경우, 해당 데이터를 읽기 위해 추가/누락 열을 추가하여 수동으로 데이터 흐름 원본 스크립트를 업데이트할 수도 있습니다. 다음 그림에서 그 예시를 보여 주고 있습니다. 

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png" alt-text="원본 스키마를 사용자 지정하는 두 번째 옵션을 보여 주는 스크린샷":::

### <a name="support-map-type-in-the-source"></a>원본에서 맵 형식 지원

#### <a name="symptoms"></a>증상
ADF 데이터 흐름에서 맵 데이터 형식은 Cosmos DB 또는 JSON 원본에서 직접 지원할 수 없으므로 “프로젝션 가져오기”에서 맵 데이터 형식을 가져올 수 없습니다.

#### <a name="cause"></a>원인
Cosmos DB와 JSON의 경우 스키마가 없는 연결이며 관련 spark 커넥터는 샘플 데이터를 사용하여 스키마를 추론한 다음, 해당 스키마를 Cosmos DB/JSON 원본 스키마로 사용합니다. 스키마를 유추할 때 Cosmos DB/JSON spark 커넥터는 개체 데이터를 맵 데이터 형식이 아닌 구조체로만 유추할 수 있으므로 맵 형식을 직접 지원할 수 없습니다.

#### <a name="recommendation"></a>권장 사항 
이 문제를 해결하려면 다음 예제와 단계를 참조하여 Cosmos DB/JSON 원본의 스크립트(DSL)를 수동으로 업데이트하여 맵 데이터 형식 지원을 받습니다.

**예**:

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/script-example.png" alt-text="Cosmos DB/JSON 원본의 스크립트(DSL) 업데이트 예를 보여 주는 스크린샷." lightbox="./media/data-flow-troubleshoot-connector-format/script-example.png"::: 
    
**1단계**: 데이터 흐름 작업의 스크립트를 엽니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/open-script.png" alt-text="데이터 흐름 작업의 스크립트를 여는 방법을 보여 주는 스크린샷." ::: 
    
**2단계**: 위의 예를 참조하여 맵 형식을 지원하도록 DSL을 업데이트합니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/update-dsl.png" alt-text="DSL을 업데이트하는 방법을 보여 주는 스크린샷." ::: 

맵 형식은 다음을 지원합니다.

|형식 |맵 형식이 지원되나요?   |의견|
|-------------------------|-----------|------------|
|Excel, CSV  |No      |둘 다 기본 형식의 표 형식 데이터 소스이므로 맵 형식을 지원할 필요가 없습니다. |
|Orc, Avro |Yes |없음|
|JSON|예 |맵 형식은 직접 지원할 수 없습니다. 이 섹션의 권장 사항 부분에 따라 원본 프로젝션 아래의 스크립트(DSL)를 업데이트합니다.|
|Cosmos DB |예 |맵 형식은 직접 지원할 수 없습니다. 이 섹션의 권장 사항 부분에 따라 원본 프로젝션 아래의 스크립트(DSL)를 업데이트합니다.|
|Parquet |Yes |현재 복잡한 데이터 형식은 parquet 데이터 세트에서 지원되지 않으므로 데이터 흐름 parquet 원본 아래에서 “프로젝션 가져오기”를 사용하여 맵 형식을 가져와야 합니다.|
|XML |No |없음|

### <a name="consume-json-files-generated-by-copy-activities"></a>복사 활동으로 생성된 JSON 파일 사용

#### <a name="symptoms"></a>증상

복사 작업을 사용하여 일부 JSON 파일을 생성한 다음, 데이터 흐름에서 이러한 파일을 읽으려고 하면 `JSON parsing error, unsupported encoding or multiline` 오류 메시지를 나타내며 실패합니다.

#### <a name="cause"></a>원인

JSON에는 복사 및 데이터 흐름 각각에 대해 다음과 같은 제한 사항이 적용됩니다.

- 유니코드 인코딩(utf-8, utf-16, utf-32) JSON 파일의 경우 복사 작업은 항상 BOM을 사용하여 JSON 파일을 생성합니다.
- "단일 문서"를 사용하도록 설정한 데이터 흐름 JSON 원본은 BOM을 사용한 유니코드 인코딩을 지원하지 않습니다.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/enabled-single-document.png" alt-text="사용하도록 설정한 '단일 문서'를 보여 주는 스크린샷"::: 


다음 조건을 충족하는 경우 이슈가 발생합니다.

- 복사 작업에서 사용하는 싱크 데이터 세트는 유니코드 인코딩(utf-8, utf-16, utf-16be, utf-32, utf-32be)으로 설정됩니다. 그렇지 않으면 기본값이 사용됩니다.
- 복사 싱크는 데이터 흐름 JSON 원본에서 "단일 문서"를 사용하도록 설정했는지에 관계 없이 다음 그림과 같이 "개체 배열" 파일 패턴을 사용하도록 설정됩니다. 

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/array-of-objects-pattern.png" alt-text="설정된 '개체 배열' 패턴을 보여 주는 스크린샷"::: 
   
#### <a name="recommendation"></a>권장

- 생성된 파일이 데이터 흐름에서 사용되는 경우 항상 복사 싱크에서 기본 파일 패턴 또는 명시적 "개체 집합" 패턴을 사용합니다.
- 데이터 흐름 JSON 원본에서 "단일 문서" 옵션을 사용하지 않도록 설정합니다.

>[!Note]
> 성능 관점에서 "개체 집합"을 사용하는 것도 좋습니다. 데이터 흐름의 "단일 문서" JSON은 단일 하나의 큰 파일에 대해 병렬 읽기를 사용하도록 설정할 수 없으므로 이 권장 사항은 부정적인 영향을 주지 않습니다.

### <a name="the-query-with-parameters-does-not-work"></a>매개 변수가 있는 쿼리가 작동하지 않음

#### <a name="symptoms"></a>증상

Azure Data Factory의 매핑 데이터 흐름은 매개 변수 사용을 지원합니다. 매개 변수 값은 데이터 흐름 실행 작업을 통해 호출 파이프라인에 의해 설정되고, 매개 변수를 사용하면 데이터 흐름이 범용으로 사용 가능하고 유동적이며 재사용할 수 있게 됩니다. 다음 매개 변수를 사용하여 데이터 흐름 설정 및 식을 매개 변수화할 수 있습니다. [매핑 데이터 흐름 매개 변수화](./parameters-data-flow.md)

매개 변수를 설정하고 데이터 흐름 원본 쿼리에서 매개 변수를 사용했으나 적용되지 않습니다.

#### <a name="cause"></a>원인

이 오류는 잘못된 구성으로 인해 발생합니다.

#### <a name="recommendation"></a>권장

다음 규칙을 사용하여 쿼리에 매개 변수를 설정합니다. 자세한 내용은 [매핑 데이터 흐름에서 식 작성](./concepts-data-flow-expression-builder.md)을 참조하세요.

1. SQL 문의 시작 부분에 큰따옴표를 적용합니다.
2. 매개 변수를 작은따옴표로 묶습니다.
3. 모든 CLAUSE 문에 소문자를 사용합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/set-parameter-in-query.png" alt-text="쿼리에서 설정된 매개 변수를 보여 주는 스크린샷"::: 

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="fail-to-create-files-with-service-principle-authentication"></a>서비스 주체 인증을 사용하여 파일을 만들지 못함

#### <a name="symptoms"></a>증상
여러 다른 원본의 데이터를 ADLS gen1 싱크로 이동하거나 전송하려고 할 때 연결된 서비스의 인증 방법이 서비스 주체 인증인 경우 작업이 실패하고 다음과 같은 오류 메시지가 표시될 수 있습니다.

`org.apache.hadoop.security.AccessControlException: CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.). [2b5e5d92-xxxx-xxxx-xxxx-db4ce6fa0487] failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)`

#### <a name="cause"></a>원인

RWX 권한 또는 데이터 세트 속성이 올바르게 설정되지 않았습니다.

#### <a name="recommendation"></a>권장

- 대상 폴더에 올바른 권한이 없는 경우 [서비스 주체 인증 사용](./connector-azure-data-lake-store.md#use-service-principal-authentication) 문서를 참조하여 Gen1에서 올바른 권한을 할당합니다.

- 대상 폴더에 올바른 권한이 있고 데이터 흐름의 파일 이름 속성을 사용하여 올바른 폴더와 파일 이름을 대상으로 지정하지만, 다음 그림에 표시된 예제와 같이 데이터 세트의 파일 경로 속성이 대상 파일 경로로 설정되지 않은 경우(일반적으로 설정되지 않음) 백 엔드 시스템에서 데이터 세트의 파일 경로를 기준으로 파일을 만들려고 시도하고 데이터 세트의 파일 경로에 올바른 권한이 없기 때문에 이 오류가 발생합니다.
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-path-property.png" alt-text="파일 경로 속성을 보여 주는 스크린샷."::: 
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-name-property.png" alt-text="파일 이름 속성을 보여 주는 스크린샷."::: 

    
    이 이슈를 해결하는 방법에는 다음 두 가지가 있습니다.
    1. WX 권한을 데이터 세트의 파일 경로에 할당합니다.
    1. 데이터 세트의 파일 경로를 WX 권한이 있는 폴더로 설정하고 데이터 흐름에서 rest 폴더 경로 및 파일 이름을 설정합니다.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="failed-with-an-error-error-while-reading-file-xxx-it-is-possible-the-underlying-files-have-been-updated"></a>다음 오류를 발생하며 실패함: "XXX 파일을 읽는 동안 오류가 발생했습니다. 기본 파일이 업데이트 되었을 수 있습니다."

#### <a name="symptoms"></a>증상

ADLS Gen2를 데이터 흐름의 싱크로 사용하며(예: 데이터 미리 보기, 실행 디버그/트리거 등) **싱크** 스테이지에서 **최적화** 탭의 파티션 설정이 기본값이 아닌 경우 다음 오류 메시지를 나타내며 작업이 실패할 수 있습니다.

`Job failed due to reason: Error while reading file abfss:REDACTED_LOCAL_PART@prod.dfs.core.windows.net/import/data/e3342084-930c-4f08-9975-558a3116a1a9/part-00000-tid-7848242374008877624-5df7454e-7b14-4253-a20b-d20b63fe9983-1-1-c000.csv. It is possible the underlying files have been updated. You can explicitly invalidate the cache in Spark by running 'REFRESH TABLE tableName' command in SQL or by recreating the Dataset/DataFrame involved.`

#### <a name="cause"></a>원인

1. MI/SP 인증에 적절한 사용 권한을 할당하지 않았습니다.
1. 원치 않는 파일을 처리하는 사용자 지정된 작업이 있을 수 있으며, 이로 인해 데이터 흐름의 중간 출력에 영향을 주게 됩니다.

#### <a name="recommendation"></a>권장
1. 연결된 서비스에 Gen2에 대한 R/W/E 권한이 있는지 확인합니다. MI auth/SP 인증을 사용하는 경우 적어도 액세스 제어(IAM)에서 Storage Blob 데이터 기여자 역할을 부여합니다.
1. 이름이 규칙과 일치하지 않는 다른 위치로 파일을 이동/삭제하는 특정 작업이 있는지 확인합니다. 데이터 흐름은 먼저 대상 폴더에 파티션 파일을 기록한 다음, 병합 및 이름 바꾸기 작업을 수행하므로 중간 파일의 이름이 규칙과 일치하지 않을 수 있습니다.

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

### <a name="encounter-an-error-failed-with-exception-handshake_failure"></a>오류 발생: 다음 예외가 발생하여 실패했습니다. handshake_failure 

#### <a name="symptoms"></a>증상
Azure PostgreSQL을 데이터를 미리 보고 실행을 디버그/트리거하는 것과 같은 데이터 흐름에서 원본 또는 싱크로 사용하면 다음과 같은 오류 메시지와 함께 작업이 실패하는 것을 확인할 수 있습니다. 

   `PSQLException: SSL error: Received fatal alert: handshake_failure `<br/>
   `Caused by: SSLHandshakeException: Received fatal alert: handshake_failure.`

#### <a name="cause"></a>원인 
Azure PostgreSQL 서버에 대해 유연한 서버 또는 하이퍼스케일(Citus)을 사용하는 경우 Azure Databricks 클러스터에서 Spark를 통해 시스템이 빌드되기 때문에 Azure Databricks에서 시스템이 유연한 서버 또는 하이퍼스케일(Citus)에 연결하지 못하게 차단하는 제한 사항이 나타납니다. 다음 두 링크를 참조할 수 있습니다.
- [핸드셰이크에서 SSL을 사용하여 Azure Databricks에서 Azure PostgreSQL에 연결하지 못함](/answers/questions/170730/handshake-fails-trying-to-connect-from-azure-datab.html)
 
- [MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale](https://github.com/microsoft/MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale/blob/master/Hands-on%20lab/HOL%20step-by%20step%20-%20Real-time%20data%20with%20Azure%20Database%20for%20PostgreSQL%20Hyperscale.md)<br/>
    이 문서에서 다음 그림의 내용을 참조하세요.<br/>

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/handshake-failure-cause-2.png" alt-text="위의 문서에서 참조 콘텐츠를 보여 주는 스크린샷":::

#### <a name="recommendation"></a>권장
복사 작업을 사용하여 이 이슈를 차단 해제할 수 있습니다. 

## <a name="azure-sql-database"></a>Azure SQL Database
 
### <a name="unable-to-connect-to-the-sql-database"></a>SQL Server에 연결할 수 없음

#### <a name="symptoms"></a>증상

Azure SQL Database는 연결된 서비스의 데이터 복사, 데이터 세트 데이터 미리 보기 및 연결 테스트에서 잘 작동할 수 있지만, 데이터 흐름에서 동일한 Azure SQL Database를 원본 또는 싱크로 사용하면 `Cannot connect to SQL database: 'jdbc:sqlserver://powerbasenz.database.windows.net;..., Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access`와 같은 오류를 나타내며 실패합니다.

#### <a name="cause"></a>원인

Azure SQL Database 서버에 잘못된 방화벽 설정이 있어 데이터 흐름 런타임에서 연결할 수 없습니다. 현재, 데이터 흐름을 사용하여 Azure SQL Database를 읽거나 쓰려고 하면 Spark 클러스터를 빌드하여 작업을 실행하는 데 Azure Databricks가 사용되지만 고정 IP 범위가 지원되지 않습니다. 자세한 내용은 [Azure Integration Runtime IP 주소](./azure-integration-runtime-ip-addresses.md)를 참조하세요.

#### <a name="recommendation"></a>권장

Azure SQL Database의 방화벽 설정을 확인하고 고정 IP 범위를 설정하는 대신 "Azure 서비스에 대한 액세스 허용"으로 설정합니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-to-azure-service.png" alt-text="방화벽 설정에서 Azure 서비스에 대한 액세스를 허용하는 방법을 보여 주는 스크린샷"::: 

### <a name="syntax-error-when-using-queries-as-input"></a>쿼리를 입력으로 사용하는 경우 구문 오류 발생

#### <a name="symptoms"></a>증상

Azure SQL의 데이터 흐름 원본에서 쿼리를 입력으로 사용하는 경우 다음과 같은 오류 메시지를 나타내며 실패합니다.

`at Source 'source1': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax XXXXXXXX.`

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/error-detail.png" alt-text="오류 세부 정보를 보여주는 스크린샷."::: 

#### <a name="cause"></a>원인

데이터 흐름 원본에 사용된 쿼리는 하위 쿼리로 실행할 수 있어야 합니다. 실패 이유는 쿼리 구문이 잘못되었거나 하위 쿼리로 실행할 수 없기 때문입니다. SSMS에서 다음 쿼리를 실행하여 확인할 수 있습니다.

`SELECT top(0) * from ($yourQuery) as T_TEMP`

#### <a name="recommendation"></a>권장

올바른 쿼리를 제공하고 먼저 SSMS에서 테스트합니다.

### <a name="failed-with-an-error-sqlserverexception-111212-operation-cannot-be-performed-within-a-transaction"></a>"SQLServerException: 111212; 트랜잭션 내에서 작업을 수행할 수 없습니다." 오류가 발생하며 실패합니다.

#### <a name="symptoms"></a>증상

Azure SQL Database를 데이터 흐름의 싱크로 사용하여 데이터를 미리 보고, 실행을 디버그/트리거하고, 다른 작업을 수행하는 경우 다음과 같은 오류 메시지와 함께 작업이 실패하는 것을 알 수 있습니다.

`{"StatusCode":"DFExecutorUserError","Message":"Job failed due to reason: at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction.","Details":"at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction."}`

#### <a name="cause"></a>원인
" `111212;Operation cannot be performed within a transaction.`" 오류는 Synapse 전용 SQL 풀에서만 발생합니다. 그러나 실수로 Azure SQL Database를 커넥터로 대신 사용하게 됩니다.

#### <a name="recommendation"></a>권장
SQL Database가 Synapse 전용 SQL 풀인지 확인합니다. 이러한 경우 Azure Synapse Analytics를 아래 그림에 표시된 커넥터로 사용하세요.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/synapse-analytics-connector.png" alt-text="Azure Synapse Analytics 커넥터를 보여 주는 스크린샷"::: 

### <a name="data-with-the-decimal-type-become-null"></a>10진 형식의 데이터가 null이 됨

#### <a name="symptoms"></a>증상

SQL 데이터베이스의 테이블에 데이터를 삽입하려고 합니다. 데이터에 10진수 형식이 포함되어 있고 SQL 데이터베이스에서 10진수 형식으로 열에 삽입해야 할 경우 데이터 값이 null로 변경될 수 있습니다.

미리 보기를 수행하는 경우 이전 스테이지에서 다음 그림과 같은 값을 표시하게 됩니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-previous-stage.png" alt-text="이전 스테이지의 값을 보여 주는 스크린샷"::: 

싱크 스테이지에서 아래 그림과 같이 null이 됩니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-sink-stage.png" alt-text="싱크 스테이지의 값을 보여 주는 스크린샷"::: 

#### <a name="cause"></a>원인
10진수 형식에는 소수 자릿수 및 전체 자릿수 속성이 있습니다. 데이터 형식이 싱크 테이블의 데이터 형식과 일치하지 않는 경우 시스템은 대상 10진수가 원래 10진수보다 더 넓고 원래 값이 대상 10진수에서 오버플로되지 않는지 확인합니다. 따라서 값은 null로 캐스팅됩니다.

#### <a name="recommendation"></a>권장
SQL 데이터베이스의 데이터와 테이블 사이에 있는 10진수 형식을 확인하고 비교한 후, 소수 자릿수와 전체 자릿수를 동일하게 변경합니다.

toDecimal(IDecimal, scale, precision)을 사용하여 원래 데이터를 대상 소수 자릿수 및 전체 자릿수로 캐스팅할 수 있는지 확인할 수 있습니다. 이것은 Null을 반환하는 경우 데이터를 캐스팅할 수 없고 데이터를 삽입할 때 추가적으로 캐스팅할 수 없음을 의미합니다.

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="serverless-pool-sql-on-demand-related-issues"></a>서버리스 풀(SQL 주문형) 관련 이슈

#### <a name="symptoms"></a>증상
Azure Synapse Analytics를 사용하고 있으며, 연결된 서비스는 실제로 Synapse 서버리스 풀입니다. 이전 이름은 SQL 주문형 풀이며 서버 이름에 `ondemand`를 포함하여 구분할 수 있습니다(예: `space-ondemand.sql.azuresynapse.net`). 다음과 같이 여러 가지 고유한 오류가 발생할 수 있습니다.<br/>

1. Synapse 서버리스 풀을 싱크로 사용하려는 경우 다음 오류가 발생합니다.<br/>
`Sink results in 0 output columns. Please ensure at least one column is mapped`
1. 원본에서 '준비 모드 사용'을 선택하면 다음 오류가 발생합니다. `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax near 'IDENTITY'.`
1. 외부 테이블에서 데이터를 가져오려는 경우 다음 오류가 발생합니다. `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External table 'dbo' is not accessible because location does not exist or it is used by another process.`
1. 쿼리/뷰에서 서버리스 풀을 통해 Cosmos DB에서 데이터를 가져오려면 다음 오류가 발생합니다. `Job failed due to reason: Connection reset.`
1. 뷰에서 데이터를 가져오려는 경우 다른 오류가 발생할 수 있습니다.

#### <a name="cause"></a>원인
증상의 원인은 아래에 각각 나와 있습니다.
1. 서버리스 풀은 싱크로 사용할 수 없습니다. 데이터베이스로의 데이터 쓰기를 지원하지 않습니다.
1. 서버리스 풀은 준비된 데이터 로드를 지원하지 않으므로 '준비 모드 사용'은 지원되지 않습니다. 
1. 사용하는 인증 방법에 외부 테이블이 참조하는 외부 데이터 원본에 대한 올바른 권한이 없습니다.
1. Synapse 서버리스 풀에 알려진 제한 사항이 있으므로 데이터 흐름에서 Cosmos DB 데이터를 가져오지 못합니다.
1. 뷰는 SQL 문을 기준으로 하는 가상 테이블입니다. 근본 원인은 뷰의 문 내에 있습니다.

#### <a name="recommendation"></a>권장

다음 단계를 적용하여 이슈를 해결할 수 있습니다.
1. 서버리스 풀을 싱크로 사용하지 않는 것이 좋습니다.
1. 서버리스 풀에 대한 원본에서 '준비 모드 사용'을 사용하지 마세요.
1. 외부 테이블 데이터에 대한 권한이 있는 서비스 주체/관리 ID만 해당 데이터를 쿼리할 수 있습니다. ADF에서 사용하는 인증 방법에 대해 외부 데이터 원본에 'Storage Blob 데이터 기여자' 권한을 부여해야 합니다.
    >[!Note]
    > 사용자 암호 인증은 외부 테이블을 쿼리할 수 없습니다. 자세한 내용은 [보안 모델](../synapse-analytics/metadata/database.md#security-model) 문서를 참조할 수 있습니다.

1. 복사 작업을 사용하여 서버리스 풀에서 Cosmos DB 데이터를 가져올 수 있습니다.
1. 엔지니어링 지원 팀에게 뷰를 만드는 SQL 문을 제공하면 문에 인증 이슈 또는 다른 문제가 발생하는지를 분석하는 데 도움이 될 수 있습니다.


### <a name="load-small-size-data-to-data-warehouse-without-staging-is-slow"></a>준비 모드 없이 작은 크기의 데이터를 데이터 웨어하우스로 로드하면 속도가 느려짐 

#### <a name="symptoms"></a>증상
준비 모드 없이 데이터 웨어하우스로 작은 데이터를 로드하는 경우 완료하는 데 시간이 오래 걸립니다. 예를 들어, 데이터 크기가 2MB이지만 완료하는 데 1시간 이상 소요됩니다. 

#### <a name="cause"></a>원인
이 이슈는 크기가 아닌 행 수에 의해 발생합니다. 행 수는 1000개 단위이고, 각 삽입은 독립된 요청으로 패키징되어야 합니다. 제어 노드로 이동하고, 새 트랜잭션을 시작하고, 잠금을 가져온 후 배포 노드로 반복적으로 이동합니다. 대량 로드는 잠금을 한 번 가져오고, 각 배포 노드는 메모리를 효율적으로 일괄 처리하여 삽입을 수행합니다.

2MB가 적은 수의 레코드로 삽입되는 경우에는 속도가 빠릅니다. 예를 들어 각 레코드가 500KB * 4개 행이면 속도가 빠릅니다. 

#### <a name="recommendation"></a>권장
성능을 향상시키려면 준비 모드를 사용하도록 설정해야 합니다.


### <a name="read-empty-string-value--as-null-with-the-enable-staging"></a>준비 모드를 사용하도록 설정하여 빈 문자열 값("")을 NULL로 읽습니다. 

#### <a name="symptoms"></a>증상
데이터 미리 보는 및 실행 디버그/트리거와 같이 Synapse를 데이터 흐름에서 원본으로 사용하고 준비 모드를 사용하도록 설정하여 PolyBase를 사용할 경우 열 값에 빈 문자열 값(`""`)이 포함되어 있으면 null로 변경됩니다.

#### <a name="cause"></a>원인
데이터 흐름 백 엔드에서 Parquet을 PolyBase 형식으로 사용하고, Synapse SQL pool gen2에는 빈 문자열 값을 자동으로 null로 변경하는 알려진 제한이 있습니다.

#### <a name="recommendation"></a>권장
다음 방법으로 이 문제를 해결할 수 있습니다.
1. 데이터 크기가 크지 않은 경우 원본에서 **준비 모드 사용** 을 사용하지 않도록 설정할 수 있지만 성능은 영향을 받습니다.
1. 준비 모드를 사용하도록 설정해야 하는 경우 **iifNull()** 함수를 사용하여 특정 열을 null에서 빈 문자열 값으로 수동으로 변경할 수 있습니다.

### <a name="managed-service-identity-error"></a>관리형 서비스 ID 오류

#### <a name="symptoms"></a>증상
Synapse를 데이터 흐름의 원본/싱크로 사용하여 데이터를 미리 보고, 실행을 디버그/트리거하며, 준비 모드에서 PolyBase를 사용하도록 설정하고, MI(관리 ID) 인증을 사용하도록 준비 모드 저장소의 연결된 서비스(Blob, Gen2 등)를 생성하는 경우 다음 그림에 표시된 오류를 나타내며 작업이 실패할 수 있습니다. <br/>

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/service-identity-error.png" alt-text="서비스 ID 오류를 보여 주는 스크린샷":::

#### <a name="error-message"></a>오류 메시지
`shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Managed Service Identity has not been enabled on this server. Please enable Managed Service Identity and try again.`

#### <a name="cause"></a>원인
1. Synapse 작업 영역에서 SQL 풀을 만드는 경우 PolyBase를 사용하여 준비 모드 저장소에 대한 MI 인증이 이전 SQL 풀에 대해 지원되지 않습니다. 
1. SQL 풀이 이전 DWH(데이터 웨어하우스) 버전인 경우 SQL Server의 MI는 준비 모드 저장소에 할당되지 않습니다.

#### <a name="recommendation"></a>권장
SQL 풀이 Synapse 작업 영역에서 만들어졌는지 확인해야 합니다.

- Synapse 작업 영역에서 SQL 풀을 만든 경우 작업 영역의 MI를 다시 등록해야 합니다. 작업 영역의 MI를 다시 등록하여 이 문제를 해결하려면 다음 단계를 적용할 수 있습니다.
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다.
    1. **관리 ID** 블레이드로 이동합니다.
    1. **파이프라인 허용** 옵션을 선택해야 하는 경우 이 설정을 선택 취소하고 저장해야 합니다.
    1. **파이프라인 허용** 옵션을 선택하고 저장합니다.

- SQL 풀이 이전 DWH 버전인 경우 SQL Server에 대해서만 MI를 사용하도록 설정하고 SQL Server의 MI에 준비 모드 저장소의 사용 권한을 할당합니다. [Azure SQL Database에서 서버에 대한 Virtual Network 서비스 엔드포인트 및 규칙 사용](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps) 문서의 단계를 참조할 수 있습니다.

### <a name="failed-with-an-error-sqlserverexception-not-able-to-validate-external-location-because-the-remote-server-returned-an-error-403"></a>다음 오류를 나타내며 실패함: "SQLServerException: 원격 서버에서 오류를 반환했으므로 외부 위치의 유효성을 검사할 수 없습니다. (403)"

#### <a name="symptoms"></a>증상

SQLDW을 싱크로 사용하여 데이터 흐름 작업을 트리거하고 실행하는 경우 다음과 같은 오류를 나타내며 작업이 실패할 수 있습니다. `"SQLServerException: Not able to validate external location because the remote server returned an error: (403)"`

#### <a name="cause"></a>원인
1. ADLS Gen2 계정의 인증 방법에서 관리 ID를 준비 모드로 사용하는 경우 cx가 인증 구성을 올바르게 설정하지 않을 수 있습니다.
1. VNET 통합 런타임을 사용하여 ADLS Gen2 계정의 인증 방법에서 관리 ID를 준비 모드로 사용해야 합니다. 준비 Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 "신뢰할 수 있는 Microsoft 서비스 허용"이 사용하도록 설정된 관리 ID 인증을 사용해야 합니다.
1. 폴더 이름에 공백 문자 또는 기타 특수 문자가 포함되어 있는지 확인합니다(예: `Space " < > # % |`).
현재 특정 특수 문자를 포함하는 폴더 이름은 데이터 웨어하우스 복사 명령에서 지원되지 않습니다.

#### <a name="recommendation"></a>권장

원인 1의 경우 [AAzure SQL Database에서 서버에 대한 Virtual Network 서비스 엔드포인트 및 규칙 사용](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps) 문서를 참조하여 이 이슈를 해결할 수 있습니다.

원인 2의 경우 다음 옵션 중 하나를 사용하여 해결합니다.

- 옵션-1: VNET 통합 런타임을 사용하여 ADLS Gen2 계정의 인증 방법에서 관리 ID를 준비 모드로 사용해야 합니다.

- 옵션-2: 준비 Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 "신뢰할 수 있는 Microsoft 서비스 허용"이 사용하도록 설정된 관리 ID 인증을 사용해야 합니다. 자세한 내용은 [PolyBase를 사용하여 준비된 복사](./connector-azure-sql-data-warehouse.md#staged-copy-by-using-polybase) 문서를 참조할 수 있습니다.

원인 3의 경우 다음 옵션 중 하나를 사용하여 해결합니다.

- 옵션-1: 폴더 이름을 바꾸고 폴더 이름에 특수 문자를 사용하지 않습니다.
- 옵션-2: 데이터 흐름 스크립트에서 `allowCopyCommand:true` 속성을 제거합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/remove-allow-copy-command-true.png" alt-text="'allowcopycommand:true'를 제거하는 방법을 보여 주는 스크린샷"::: 


### <a name="failed-with-an-error-this-operation-is-not-permitted-on-a-non-empty-directory"></a>다음 오류를 나타내며 실패함: "이 작업은 비어 있지 않은 디렉터리에서 허용되지 않습니다."

#### <a name="symptoms"></a>증상

데이터 흐름에서 Azure Synapse Analytics를 싱크로 사용하여 데이터를 미리 보거나, 실행을 디버그/트리거하거나, 다른 작업을 수행하고, 준비 모드 사용을 true로 설정하면 다음 오류 메시지를 나타내면서 작업이 실패할 수 있습니다.

`DF-SYS-01 at Sink 'sink': Unable to stage data before write. Check configuration/credentials of storage.`<br/>
`org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException: This operation is not permitted on a non-empty directory.`

#### <a name="cause"></a>원인
Azure Blob Storage를 준비된 연결된 서비스로 사용하여 계층적 네임스페이스가 사용하도록 설정된 스토리지 계정에 연결했으며 해당 계정은 연결된 서비스에서 키 인증을 사용합니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-configuration.png" alt-text="스토리지 계정 구성을 보여 주는 스크린샷"::: 

#### <a name="recommendation"></a>권장
스토리지에 대한 Azure Data Lake Gen2 연결된 서비스를 만들고 데이터 흐름 활동에서 Gen2 스토리지를 준비된 연결된 서비스로 선택합니다.

## <a name="common-data-model-format"></a>Common Data Model 형식

### <a name="modeljson-files-with-special-characters"></a>특수 문자가 포함된 Model.json 파일

#### <a name="symptoms"></a>증상 
Model.Json 파일의 최종 이름에 특수 문자가 포함되어 있는 이슈가 발생할 수 있습니다.  

#### <a name="error-message"></a>오류 메시지  
`at Source 'source1': java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: PPDFTable1.csv@snapshot=2020-10-21T18:00:36.9469086Z. ` 

#### <a name="recommendation"></a>권장  
파일 이름의 특수 문자를 바꿉니다. synapse에서는 효과가 있지만 ADF에서는 효과가 없습니다.  

### <a name="no-data-output-in-the-data-preview-or-after-running-pipelines"></a>데이터 미리 보기 또는 파이프라인 실행 후 데이터 출력이 없음

#### <a name="symptoms"></a>증상
CDM에 대한 manifest.json을 사용하는 경우 데이터 미리 보기에 데이터가 표시되지 않거나 파이프라인을 실행한 후에 데이터가 표시되지 않습니다. 머리글만 표시됩니다. 아래 그림에서 이 이슈를 확인할 수 있습니다.<br/>

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/no-data-output.png" alt-text="데이터 출력 없음 증상을 보여 주는 스크린샷":::

#### <a name="cause"></a>원인
매니페스트 문서에서는 CDM 폴더에 대해 설명합니다. 예를 들어 폴더에 있는 엔터티, 해당 엔터티의 참조 및 이 인스턴스에 해당하는 데이터를 설명합니다. 매니페스트 문서에 데이터를 읽을 수 있는 위치를 ADF에 나타내는 `dataPartitions` 정보가 없으며, 이로 인해 제로 데이터가 반환됩니다. 

#### <a name="recommendation"></a>권장
`dataPartitions` 정보를 포함하도록 매니페스트 문서를 업데이트하고, 다음 예제 매니페스트 문서를 참조하여 문서를 업데이트할 수 있습니다. [공통 데이터 모델 메타데이터: 매니페스트 소개-예제 매니페스트 문서](/common-data-model/cdm-manifest#example-manifest-document)

### <a name="json-array-attributes-are-inferred-as-separate-columns"></a>JSON 배열 특성이 별도의 열로 유추됨

#### <a name="symptoms"></a>증상 
CDM 엔터티의 한 특성(문자열 형식)에서 JSON 배열이 데이터로 포함되는 이슈가 발생할 수 있습니다. 이 데이터를 발견하면 ADF는 데이터를 별도의 열로 잘못 유추합니다. 다음 그림에서 볼 수 있듯이 원본(msfp_otherproperties)에 표시된 단일 특성은 CDM 커넥터의 미리 보기에서 별도의 열로 유추됩니다.<br/> 

- CSV 원본 데이터(두 번째 열 참조)에서: <br/>

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/json-array-csv.png" alt-text="CSV 원본 데이터의 특성을 보여 주는 스크린샷":::

- CDM 원본 데이터 미리 보기에서: <br/>

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/json-array-cdm.png" alt-text="CDM 원본 데이터의 별도 열을 보여 주는 스크린샷":::

 
데이터베이스가 드리프트된 열을 매핑하고 데이터 흐름 식을 사용하여 이 특성을 배열로 변환할 수도 있습니다. 그러나 읽을 때 이 특성이 별도의 열로 읽히므로 배열로의 변환이 작동하지 않습니다.  

#### <a name="cause"></a>원인
이 이슈는 해당 열에 대한 JSON 개체 값 내의 쉼표가 원인일 수 있습니다. 데이터 파일은 CSV 파일이어야 하므로 쉼표는 열 값의 끝임을 나타냅니다. 

#### <a name="recommendation"></a>권장
이 문제를 해결하려면 JSON 열을 큰따옴표로 묶어야 하고 백슬래시(`\`)를 포함하는 내부 따옴표를 사용하지 않아야 합니다. 이러한 방식으로 열 값의 내용을 완전히 단일 열로 읽을 수 있습니다.  
  
>[!Note]
>CDM는 열 값의 데이터 형식이 JSON임을 알리지 않고, 문자열임을 알리고 문자열로서 구문 분석합니다.

### <a name="unable-to-fetch-data-in-the-data-flow-preview"></a>데이터 흐름 미리 보기에서 데이터를 가져올 수 없음

#### <a name="symptoms"></a>증상
Power BI에서 생성된 model.json에서 CDM를 사용합니다. 데이터 흐름 미리 보기를 사용하여 CDM 데이터를 미리 볼 때 `No output data.` 오류가 발생합니다.

#### <a name="cause"></a>원인
 다음 코드는 Power BI 데이터 흐름에서 생성되는 model.json 파일의 파티션에 있습니다.
```json
"partitions": [  
{  
"name": "Part001",  
"refreshTime": "2020-10-02T13:26:10.7624605+00:00",  
"location": "https://datalakegen2.dfs.core.windows.net/powerbi/salesEntities/salesPerfByYear.csv @snapshot=2020-10-02T13:26:10.6681248Z"  
}  
```
이 model.json 파일의 경우 이슈는 데이터 파티션 파일의 명명 스키마에 특수 문자가 있으며 현재 '@'를 포함하는 지원되는 파일 경로가 존재하지 않는다는 것입니다.  

#### <a name="recommendation"></a>권장
데이터 파티션 파일 이름 및 model.json 파일에서 `@snapshot=2020-10-02T13:26:10.6681248Z` 부분을 제거한 후 다시 시도하세요. 

### <a name="the-corpus-path-is-null-or-empty"></a>자료 모음 경로가 null이거나 비어 있음

#### <a name="symptoms"></a>증상
모델 형식을 사용하여 데이터 흐름에서 CDM을 사용하는 경우 데이터를 미리 볼 수 없으며 `DF-CDM_005 The corpus path is null or empty` 오류가 발생합니다. 이 오류는 다음 그림에 표시됩니다.  

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/corpus-path-error.png" alt-text="자료 모음 경로 오류를 보여 주는 스크린샷":::

#### <a name="cause"></a>원인
model.json의 데이터 파티션 경로는 데이터 레이크가 아닌 Blob Storage 위치를 가리킵니다. 이 위치에는 ADLS Gen2에 대한 기본 URL인 **.dfs.core.windows.net** 이 있어야 합니다. 

#### <a name="recommendation"></a>권장
이 이슈를 해결하려면 [ADF에서 인라인 데이터 세트 및 공통 데이터 모델에 대한 지원을 데이터 흐름에 추가](https://techcommunity.microsoft.com/t5/azure-data-factory/adf-adds-support-for-inline-datasets-and-common-data-model-to/ba-p/1441798) 문서를 참조할 수 있습니다. 다음 그림에서는 이 문서에서 자료 모음 경로 오류를 수정하는 방법을 보여 줍니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/fix-format-issue.png" alt-text="자료 모음 경로 오류를 수정하는 방법을 보여 주는 스크린샷":::

### <a name="unable-to-read-csv-data-files"></a>CSV 데이터 파일을 읽을 수 없음

#### <a name="symptoms"></a>증상 
인라인 데이터 세트를 매니페스트를 원본으로 사용하는 공통 데이터 모델로 사용하며, 매니페스트 파일, 루트 경로, 엔터티 이름 및 경로를 제공했습니다. 매니페스트에는 CSV 파일 위치가 포함된 데이터 파티션이 있습니다. 한편, 엔터티 스키마와 csv 스키마는 동일하며 모든 유효성 검사가 성공했습니다. 그러나 데이터 미리 보기에서는 데이터가 아닌 스키마만 로드되고 데이터는 표시되지 않습니다. 이 상황은 다음 그림에 나와 있습니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/unable-read-data.png" alt-text="데이터 파일을 읽을 수 없는 이슈를 보여 주는 스크린샷":::

#### <a name="cause"></a>원인
CDM 폴더는 논리적 및 물리적 모델로 구분되지 않으며, CDM 폴더에는 물리적 모델만 있습니다. 다음 두 문서에서는 [논리적 정의](/common-data-model/sdk/logical-definitions) 및 [논리적 엔터티 정의 확인](/common-data-model/sdk/convert-logical-entities-resolved-entities)의 차이점에 대해 설명합니다.<br/> 

#### <a name="recommendation"></a>권장
CDM을 원본으로 사용하는 데이터 흐름의 경우, 논리적 모델을 엔터티 참조로 사용하고, 확인된 물리적 엔터티의 위치 및 데이터 파티션의 위치를 설명하는 매니페스트를 사용하세요. 퍼블릭 CDM github 리포지토리 [CDM-schemaDocuments](https://github.com/microsoft/CDM/tree/master/schemaDocuments) 내에서 논리적 엔터티 정의의 몇 가지 샘플을 확인할 수 있습니다.<br/>

자료 모음을 형성하는 좋은 출발점은 스키마 문서 폴더 내에 파일을 복사하고(github 리포지토리 내의 해당 수준에) 해당 파일을 폴더에 배치하는 것입니다. 그런 다음, 리포지토리 내에서 미리 정의된 논리적 엔터티 중 하나를 사용하여(시작점 또는 참조 지점으로) 논리적 모델을 만들 수 있습니다.<br/>

자료 모음이 설정되면 잘 구성된 CDM 폴더를 올바르게 만들 수 있도록 CDM을 데이터 흐름 내의 싱크로 사용하는 것이 좋습니다. CSV 데이터 세트를 원본으로 사용하고 만든 CDM 모델에 싱크할 수 있습니다.

## <a name="csv-and-excel-format"></a>CSV 및 Excel 형식

### <a name="set-the-quote-character-to-no-quote-char-is-not-supported-in-the-csv"></a>따옴표 문자를 'no quote char'로 설정하는 것이 CSV에서 지원되지 않음
 
#### <a name="symptoms"></a>증상

따옴표 문자를 'no quote char'로 설정하면 CSV에서 지원되지 않는 몇 가지 이슈가 있습니다.

1. 따옴표 문자를 'no quote char'로 설정하면 다중 문자 열 구분 기호가 동일한 문자로 시작하고 끝날 수 없습니다.
2. 따옴표 문자를 'no quote char'로 설정하면 다중 문자 열 구분 기호에 이스케이프 문자 `\`을 사용할 수 없습니다.
3. 따옴표 문자를 'no quote char'로 설정하면 열 값에 행 구분 기호가 포함될 수 없습니다.
4. 열 값에 열 구분 기호가 포함되어 있으면 따옴표 문자 및 이스케이프 문자를 둘 다 비워 둘 수는 없습니다(따옴표 없음 및 이스케이프 없음).

#### <a name="cause"></a>원인

증상의 원인은 각 예제와 함께 아래에 나와 있습니다.
1. 같은 문자로 시작하고 끝납니다.<br/>
`column delimiter: $*^$*`<br/>
`column value: abc$*^    def`<br/>
`csv sink: abc$*^$*^$*def ` <br/>
`will be read as "abc" and "^&*def"`<br/>

2. 다중 문자 구분 기호에 이스케이프 문자가 포함되어 있습니다.<br/>
`column delimiter: \x`<br/>
`escape char:\`<br/>
`column value: "abc\\xdef"`<br/>
이스케이프 문자는 열 구분 기호를 이스케이프하거나 문자를 이스케이프합니다.

3. 열 값에는 행 구분 기호가 포함됩니다. <br/>
`We need quote character to tell if row delimiter is inside column value or not.`

4. 따옴표 문자와 이스케이프 문자는 모두 비어 있고 열 값에는 열 구분 기호가 포함되어 있습니다.<br/>
`Column delimiter: \t`<br/>
`column value: 111\t222\t33\t3`<br/>
`It will be ambigious if it contains 3 columns 111,222,33\t3 or 4 columns 111,222,33,3.`<br/>

#### <a name="recommendation"></a>권장
첫 번째 및 두 번째 증상은 현재 해결할 수 없습니다. 세 번째 및 네 번째 증상의 경우 다음 방법을 적용할 수 있습니다.
- 증상 3의 경우 여러 줄 csv 파일에 대해 'no quote char'를 사용하지 마세요.
- 증상 4의 경우 따옴표 문자 또는 이스케이프 문자를 비어 있지 않은 것으로 설정하거나 데이터 내의 모든 열 구분 기호를 제거할 수 있습니다.

### <a name="read-files-with-different-schemas-error"></a>다른 스키마가 있는 파일 읽기 오류

#### <a name="symptoms"></a>증상

데이터 흐름을 사용하여 스키마가 서로 다른 CSV 및 Excel 파일과 같은 파일을 읽는 경우 데이터 흐름 디버그, 샌드박스 또는 활동 실행이 실패합니다.
- CSV의 경우 파일의 스키마가 다른 경우 데이터의 잘못된 정렬이 있는 것입니다. 

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/schema-error-1.png" alt-text="첫 번째 스키마 오류를 보여 주는 스크린샷":::

- Excel의 경우 파일의 스키마가 다를 때 오류가 발생합니다.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/schema-error-2.png" alt-text="두 번째 스키마 오류를 보여 주는 스크린샷":::

#### <a name="cause"></a>원인

데이터 흐름에서 다른 스키마를 사용하는 파일을 읽는 것은 지원되지 않습니다.

#### <a name="recommendation"></a>권장

데이터 흐름에서 스키마가 서로 다른 CSV 및 Excel 파일과 같은 파일을 전송하려면 아래 방법을 사용하여 해결할 수 있습니다.

- CSV의 경우 다른 파일의 스키마를 수동으로 병합하여 전체 스키마를 얻어야 합니다. 예를 들어, file_1에는 열 `c_1, c_2, c_3`이 있지만, file_2에는 열 `c_3, c_4,... c_10`이 있으므로 병합된 전체 스키마는 `c_1, c_2... c_10`입니다. 그런 다음, 다른 파일도 데이터가 없더라도 동일한 전체 스키마를 가지도록 합니다. 예를 들어, file_x에 열 `c_1, c_2, c_3, c_4`만 있는 경우 파일에 열 `c_5, c_6, ... c_10`을 추가합니다. 그러면 작동될 수 있습니다.

- Excel의 경우 다음 옵션 중 하나를 적용하여 이 문제를 해결할 수 있습니다.

    - **옵션-1**: 다른 파일의 스키마를 수동으로 병합하여 전체 스키마를 얻어야 합니다. 예를 들어, file_1에는 열 `c_1, c_2, c_3`이 있지만, file_2에는 열 `c_3, c_4,... c_10`이 있으므로 병합된 전체 스키마는 `c_1, c_2... c_10`입니다. 그런 다음, 다른 파일도 데이터가 없더라도 동일한 스키마를 가지도록 합니다. 예를 들어 시트 "SHEET_1"이 있는 file_x에는 열 `c_1, c_2, c_3, c_4`만 있는 경우 시트에도 추가 열 `c_5, c_6, ... c_10`을 추가합니다. 그러면 작동될 수 있습니다.
    - **옵션-2**: **range(예: A1:G100) + firstRowAsHeader=false** 를 사용합니다. 그러면 열 이름과 개수가 다른 경우에도 모든 Excel 파일에서 데이터를 로드할 수 있습니다.

## <a name="delta-format"></a>델타 형식

### <a name="the-sink-does-not-support-the-schema-drift-with-upsert-or-update"></a>싱크가 upsert 또는 업데이트를 사용하는 스키마 드리프트를 지원하지 않음

#### <a name="symptoms"></a>증상
매핑 데이터 흐름의 델타 싱크가 upsert/업데이트를 사용하는 스키마 드리프트를 지원하지 않는 이슈가 발생할 수 있습니다. 문제는 델타가 매핑 데이터 흐름에서 대상이 되고 사용자가 업데이트/upsert를 구성할 때 스키마 드리프트가 작동하지 않는다는 것입니다. 

델타에 대한 "초기" 로드 후 열이 원본에 추가되는 경우 후속 작업은 새 열을 찾을 수 없다는 오류와 함께 실패합니다. 이 문제는 행 변경을 사용하여 upsert/업데이트를 수행할 때 발생합니다. 삽입에 대해서만 나타나는 것 같습니다.

#### <a name="error-message"></a>오류 메시지
`DF-SYS-01 at Sink 'SnkDeltaLake': org.apache.spark.sql.AnalysisException: cannot resolve target.BICC_RV in UPDATE clause given columns target. `

#### <a name="cause"></a>원인
데이터 흐름 런타임에 사용되는 io 델타 라이브러리의 제한으로 인한 델타 형식 이슈입니다. 이 이슈는 아직 해결 중입니다.

#### <a name="recommendation"></a>권장
이 문제를 해결하려면 먼저 스키마를 업데이트한 후 데이터를 작성해야 합니다. 다음 단계를 따르기만 하면 됩니다. <br/>
1. 스키마를 업데이트하기 위해 스키마 병합 옵션에 삽입 전용 델타 싱크를 포함하는 하나의 데이터 흐름을 만듭니다. 
1. 1단계를 수행한 후 삭제/upsert/업데이트를 사용하여 스키마를 변경하지 않고 대상 싱크를 수정합니다. <br/>



## <a name="snowflake"></a>Snowflake

### <a name="unable-to-connect-to-the-snowflake-linked-service"></a>Snowflake 연결된 서비스에 연결할 수 없음

#### <a name="symptoms"></a>증상

퍼블릭 네트워크에서 Snowflake 연결된 서비스를 만들 때 다음 오류가 발생하며 자동 확인 통합 런타임을 사용하고 있습니다.

`ERROR [HY000] [Microsoft][Snowflake] (4) REST request for URL https://XXXXXXXX.east-us- 2.azure.snowflakecomputing.com.snowflakecomputing.com:443/session/v1/login-request?requestId=XXXXXXXXXXXXXXXXXXXXXXXXX&request_guid=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` 

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/connection-fail-error.png" alt-text="연결 실패 오류를 보여 주는 스크린샷"::: 

#### <a name="cause"></a>원인

Snowflake 계정 문서에 지정된 형식으로 계정 이름을 적용하지 않았습니다(지역 및 클라우드 플랫폼을 식별하는 추가 세그먼트 포함). 예를 들면 `XXXXXXXX.east-us-2.azure`와 같습니다. 자세한 내용은 [연결된 서비스 속성](./connector-snowflake.md#linked-service-properties) 문서를 참조하세요.

#### <a name="recommendation"></a>권장

이 이슈를 해결하려면 계정 이름 형식을 변경합니다. 역할은 다음 그림에 표시된 역할 중 하나여야 하지만 기본 역할은 **퍼블릭** 입니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/account-role.png" alt-text="계정 역할을 보여 주는 스크린샷"::: 

### <a name="sql-access-control-error-insufficient-privileges-to-operate-on-schema"></a>SQL 액세스 제어 오류: "스키마에 대해 작업할 수 있는 권한이 부족함"

#### <a name="symptoms"></a>증상

데이터 흐름의 Snowflake 원본에서 "프로젝션 가져오기", "데이터 미리 보기" 등을 사용하려고 하면 `net.snowflake.client.jdbc.SnowflakeSQLException: SQL access control error: Insufficient privileges to operate on schema`와 같은 오류가 발생합니다.

#### <a name="cause"></a>원인

이 오류는 잘못된 구성으로 인해 발생합니다. 데이터 흐름을 사용하여 Snowflake 데이터를 읽을 때 ADB(Azure Databricks) 런타임은 Snowflake에 대한 쿼리를 직접 선택하지 않습니다. 대신 임시 스테이지가 만들어지고 데이터를 테이블에서 스테이지로 끌어온 다음, 압축되면 ADB에서 끌어옵니다. 이 프로세스는 아래 그림에 나와 있습니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/snowflake-data-read-model.png" alt-text="Snowflake 데이터 읽기 모델을 보여 주는 스크린샷"::: 

따라서 ADB에서 사용되는 사용자/역할에는 Snowflake에서 이 작업을 수행하는 데 필요한 권한이 있어야 합니다. 그러나 공유에서 데이터베이스가 생성되므로 일반적으로 사용자/역할에는 이러한 권한이 없습니다. 

#### <a name="recommendation"></a>권장
이 이슈를 해결하려면 다른 데이터베이스를 만들고 공유 DB의 맨 위에 뷰를 만들어 ADB에서 액세스할 수 있습니다. 자세한 내용은 [Snowflake](https://community.snowflake.com/s/question/0D50Z000095ktE4SAI/insufficient-privileges-to-operate-on-schema)를 참조하세요.

### <a name="failed-with-an-error-snowflakesqlexception-ip-xxxx-is-not-allowed-to-access-snowflake-contact-your-local-security-administrator"></a>다음 오류를 나타내며 실패함: "SnowflakeSQLException: IP x.x.x.x은(는) Snowflake에 액세스할 수 없습니다. 로컬 보안 관리자에게 문의하세요."

#### <a name="symptoms"></a>증상

Azure Data Factory에서 Snowflake를 사용하는 경우 Snowflake 연결된 서비스에서 연결 테스트를 사용하고, Snowflake 데이터 세트에서 데이터 미리보기/스키마 가져오기를 수행하고, 복사/조회/메타데이터 가져오기 또는 기타 작업을 실행할 수 있습니다. 그러나 데이터 흐름 활동에서 Snowflake를 사용하는 경우 `SnowflakeSQLException: IP 13.66.58.164 is not allowed to access Snowflake. Contact your local security administrator.`와 오류가 발생할 수 있습니다.

#### <a name="cause"></a>원인

Azure Data Factory 데이터 흐름은 고정 IP 범위의 사용을 지원하지 않습니다. [Azure 통합 런타임 IP 주소](./azure-integration-runtime-ip-addresses.md)에서 자세한 정보를 참조할 수 있습니다.

#### <a name="recommendation"></a>권장

이 이슈를 해결하려면 다음 단계를 수행하여 Snowflake 계정 방화벽 설정을 변경할 수 있습니다.

1. 서비스 태그의 IP 범위 목록은 "서비스 태그 IP 범위 다운로드 링크": [다운로드 가능한 JSON 파일을 사용하여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)에서 가져올 수 있습니다.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/ip-range-list.png" alt-text="IP 범위 목록을 보여 주는 스크린샷"::: 

1. "southcentralus" 지역에서 데이터 흐름을 실행하는 경우 이름이 "AzureCloud.southcentralus"인 모든 주소에서 액세스할 수 있도록 허용해야 합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-with-name.png" alt-text="특정 이름을 가진 모든 주소의 액세스를 허용하는 방법을 보여 주는 스크린샷"::: 

### <a name="queries-in-the-source-does-not-work"></a>원본의 쿼리가 작동하지 않음

#### <a name="symptoms"></a>증상

쿼리를 사용하여 Snowflake에서 데이터를 읽으려고 하면 다음과 같은 오류가 발생할 수 있습니다.

1. `SQL compilation error: error line 1 at position 7 invalid identifier 'xxx'`
2. `SQL compilation error: Object 'xxx' does not exist or not authorized.`

#### <a name="cause"></a>원인

이 오류는 잘못된 구성으로 인해 발생합니다.

#### <a name="recommendation"></a>권장

Snowflake의 경우 생성/정의 타임에 식별자를 저장하고 쿼리 및 다른 SQL 문에서 식별자를 확인하기 위해 다음 규칙을 적용합니다.

식별자(테이블 이름, 스키마 이름, 열 이름 등)를 따옴표로 묶지 않은 경우 기본적으로 대문자로 저장되고 확인되며 대/소문자를 구분합니다. 예를 들면 다음과 같습니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png" alt-text="따옴표로 묶지 않은 식별자의 예를 보여 주는 스크린샷"lightbox="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png"::: 

대/소문자를 구분하기 때문에 다음 쿼리를 사용하여 Snowflake 데이터를 읽을 수 있지만 결과는 동일합니다.<br/>
- `Select MovieID, title from Public.TestQuotedTable2`<br/>
- `Select movieId, title from Public.TESTQUOTEDTABLE2`<br/>
- `Select movieID, TITLE from PUBLIC.TESTQUOTEDTABLE2`<br/>

식별자(테이블 이름, 스키마 이름, 열 이름 등)을 큰따옴표로 묶은 경우 대/소문자 구분을 포함하여 입력한 대로 정확하게 저장 및 확인됩니다. 다음 그림의 예제를 참조하세요. 자세한 내용은 [식별자 요구 사항](https://docs.snowflake.com/en/sql-reference/identifiers-syntax.html#identifier-requirements) 문서를 참조하세요.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png" alt-text="큰따옴표로 묶은 식별자의 예를 보여 주는 스크린샷" lightbox="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png"::: 

대/소문자 구분 식별자(테이블 이름, 스키마 이름, 열 이름 등)에 소문자가 있으므로 쿼리를 사용하여 데이터를 읽는 동안 식별자를 따옴표로 묶어야 합니다. 예를 들면 다음과 같습니다. <br/>

- Public. **"testQuotedTable2"** 에서 **"movieId"** , **"title"** 을 선택합니다.

Snowflake 쿼리와 관련된 오류가 발생하는 경우 다음 단계를 수행하여 일부 식별자(테이블 이름, 스키마 이름, 열 이름 등)에서 대/소문자를 구분하는지 확인합니다.

1. Snowflake 서버에 로그인하여(`https://{accountName}.azure.snowflakecomputing.com/`, {accountName}을 계정 이름으로 바꿈) 식별자(테이블 이름, 스키마 이름, 열 이름 등)를 확인합니다.

1. 쿼리를 테스트하고 유효성을 검사하는 워크시트를 만듭니다.
    - `Use database {databaseName}`을 실행하고 {databaseName}을 데이터베이스 이름으로 바꿉니다.
    - 테이블을 사용하여 쿼리를 실행합니다. 예를 들면 다음과 같습니다. `select "movieId", "title" from Public."testQuotedTable2"`
    
1. Snowflak의 SQL 쿼리를 테스트하고 유효성을 검사한 후에는 데이터 흐름 Snowflak 원본에서 직접 사용할 수 있습니다.

### <a name="the-expression-type-does-not-match-the-column-data-type-expecting-variant-but-got-varchar"></a>식 형식이 열 데이터 형식과 일치하지 않습니다. VARIANT가 필요하지만 VARCHAR이 있습니다. 

#### <a name="symptoms"></a>증상

Snowflake 테이블에 데이터를 쓰려고 하면 다음 오류가 발생할 수 있습니다.

`java.sql.BatchUpdateException: SQL compilation error: Expression type does not match column data type, expecting VARIANT but got VARCHAR`

#### <a name="cause"></a>원인

입력 데이터의 열 형식은 문자열로, Snowflake 싱크에 있는 관련 열의 VARIANT 형식과 다릅니다.

복잡한 스키마(배열/맵/구조체)가 있는 데이터를 새 Snowflake 테이블에 저장하면 데이터 흐름 형식이 물리적 형식 VARIANT로 자동 변환됩니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/physical-type-variant.png" alt-text="테이블의 VARIANT 형식을 보여 주는 스크린샷."::: 

관련 값은 아래 그림에 표시된 JSON 문자열로 저장됩니다.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/json-string.png" alt-text="저장된 JSON 문자열을 보여 주는 스크린샷."::: 

#### <a name="recommendation"></a>권장 사항

Snowflake VARIANT의 경우 구조체, 맵 또는 배열 형식인 데이터 흐름 값만 수락할 수 있습니다. 입력 데이터 열의 값이 JSON, XML 또는 기타 문자열이면 다음 옵션 중 하나를 사용하여 이 문제를 해결합니다.

- **옵션-1**: Snowflake를 싱크로 사용하기 전에 [구문 분석 변환](./data-flow-parse.md)을 사용하여 입력 데이터 열 값을 구조체, 맵 또는 배열 형식으로 변환합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/parse-transformation.png" alt-text="구문 분석 변환을 보여 주는 스크린샷."::: 

    > [!Note]
    > VARIANT 형식의 Snowflake 열 값은 기본적으로 Spark에서 문자열로 읽습니다.

- **옵션-2**: Snowflake 서버(`https://{accountName}.azure.snowflakecomputing.com/`, {accountName}을 계정 이름으로 대체)에 로그인하여 Snowflake 대상 테이블의 스키마를 변경합니다. 각 단계에서 쿼리를 실행하여 다음 단계를 적용합니다.
    1. VARCHAR를 사용하여 값을 저장하는 하나의 새 열을 만듭니다. <br/>
        ```SQL
        alter table tablename add newcolumnname varchar;
        ```    
    1. VARIANT 값을 새 열에 복사합니다. <br/>
    
        ```SQL
        update tablename t1 set newcolumnname = t1."details"
        ```
    1. 사용하지 않는 VARIANT 열을 삭제합니다. <br/>
        ```SQL
        alter table tablename drop column "details";
        ```
    1. 새 열의 이름을 이전 이름으로 바꿉니다. <br/>
        ```SQL
        alter table tablename rename column newcolumnname to "details";
        ```

## <a name="next-steps"></a>다음 단계
문제 해결에 관한 자세한 내용은 다음 리소스를 참조하세요.

*  [Azure Data Factory의 매핑 데이터 흐름 문제 해결](data-flow-troubleshoot-guide.md)
*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
