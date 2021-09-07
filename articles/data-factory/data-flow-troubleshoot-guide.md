---
title: 매핑 데이터 흐름 문제 해결
description: Azure Data Factory의 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 08/18/2021
ms.openlocfilehash: 56a59d4acd9c1f8ed51f16e7c39cfb5cba949b6b
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539271"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory의 매핑 데이터 흐름 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 매핑 데이터 흐름에 관한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-error-codes-and-messages"></a>일반적인 오류 코드 및 메시지 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-Executor-SourceInvalidPayload
- **메시지**: 컨테이너가 없어 데이터 미리 보기, 디버그, 파이프라인 데이터 흐름 실행 실패
- **원인**: 데이터 세트에 스토리지에 없는 컨테이너가 포함되어 있습니다.
- **권장 사항**: 데이터 세트에서 참조되는 컨테이너가 있으며 액세스할 수 있는지 확인합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-Executor-SystemInvalidJson

- **메시지**: JSON 구문 분석 오류, 지원되지 않는 인코딩 또는 여러 줄
- **원인**: JSON 파일의 가능한 문제: 지원되지 않은 인코딩, 손상된 바이트, 여러 중첩 줄에 단일 문서로 JSON 원본 사용.
- **권장 사항**: JSON 파일의 인코딩이 지원되는지 확인합니다. JSON 데이터 세트를 사용하는 원본 변환에서 **JSON 설정** 을 확장하고 **단일 문서** 를 설정합니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-Executor-BroadcastTimeout

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60초, 작업 실행에서 300초 안에 데이터를 생성하는지 확인
- **원인** : 브로드캐스트에는 디버그 실행 시 60초, 작업 실행 시 300초의 기본 시간 제한이 있습니다. 브로드캐스트용으로 선택한 스트림이 너무 커서 이 한도 안에 데이터를 생성하지 못합니다.
- **권장 사항**: 데이터 흐름 변환에서 조인, 존재 및 조회에 대해 **최적화** 탭을 선택합니다. 브로드캐스트의 기본 옵션은 **자동** 입니다. **자동** 이 설정되어 있거나 **고정** 에서 브로드캐스트하도록 왼쪽이나 오른쪽을 수동으로 설정할 경우 더 큰 Azure IR(통합 런타임) 구성을 설정하거나 브로드캐스트를 끌 수 있습니다. 데이터 흐름에서 최상의 성능을 얻으려면 **자동** 을 사용하여 Spark가 브로드캐스트하도록 허용하고 메모리 최적화 Azure IR을 사용하는 것이 좋습니다. 
 
  디버그 파이프라인 실행의 디버그 테스트 실행에서 데이터 흐름을 실행하는 경우 이 조건이 더 자주 발생할 수 있습니다. 더 빠른 디버깅 환경을 유지 관리하기 위해 Azure Data Factory에서 브로드캐스트 시간 제한을 60초로 제한하기 때문입니다. 시간 제한을 트리거된 실행의 시간 제한인 300초로 연장할 수 있습니다. 이렇게 하려면 **디버그** > **활동 런타임 사용** 옵션을 사용하여 데이터 흐름 실행 파이프라인 작업에 정의된 Azure IR을 사용할 수 있습니다.

- **메시지**: 브로드캐스트 조인 시간 제한 오류입니다. 조인/존재/ 조회 변환에서 브로드캐스트 옵션의 ‘끄기’를 선택하여 이 이슈를 방지할 수 있습니다. 성능 향상을 위해 조인 옵션을 브로드캐스트하려는 경우 브로드캐스트 스트림이 디버그 실행 시 60초, 작업 실행 시 300초 이내에 데이터를 생성할 수 있는지 확인합니다.
- **원인** : 브로드캐스트에는 디버그 실행 시 60초, 작업 실행 시 300초의 기본 시간 제한이 있습니다. 브로드캐스트 조인에서 브로드캐스트용으로 선택한 스트림이 너무 커서 이 한도 안에 데이터를 생성하지 못합니다. 브로드캐스트 조인을 사용하지 않으면 데이터 흐름별 기본 브로드캐스트가 같은 한도에 도달할 수 있습니다.
- **권장 사항**: 브로드캐스트 옵션을 끄거나, 처리하는 데 60초가 넘게 걸릴 수 있는 대용량 데이터 스트림은 브로드캐스트하지 않습니다. 더 작은 스트림을 브로드캐스트하도록 선택합니다. 대형 Azure SQL Data Warehouse 테이블과 원본 파일을 선택하는 것은 일반적으로 좋지 않습니다. 브로드캐스트 조인이 없는 경우 이 오류가 발생하면 더 큰 클러스터를 사용합니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-Executor-Conversion

- **메시지**: 잘못된 문자로 인해 날짜 또는 시간 변환 실패
- **원인**: 데이터가 예상된 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식을 사용합니다.

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-Executor-InvalidColumn
- **메시지**: 쿼리에 열 이름을 지정해야 합니다. SQL 함수를 사용하는 경우 별칭 설정
- **원인**: 열 이름이 지정되지 않았습니다.
- **권장 사항**: min() 또는 max()와 같은 SQL 함수를 사용하는 경우 별칭을 설정합니다.

### <a name="error-code-df-executor-drivererror"></a>오류 코드: DF-Executor-DriverError
- **메시지**: INT96은 ADF Dataflow에서 지원하지 않는 레거시 타임스탬프 형식입니다. 열 형식을 최신 형식으로 업그레이드하는 것이 좋습니다.
- **원인**: 드라이버 오류입니다.
- **권장 사항**: INT96은 Azure Data Factory 데이터 흐름에서 지원하지 않는 레거시 타임스탬프 형식입니다. 열 형식을 최신 형식으로 업그레이드하는 것이 좋습니다.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>오류 코드: DF-Executor-BlockCountExceedsLimitError
- **메시지**: 커밋되지 않은 블록 수는 최대 한도인 100,000개의 블록을 초과할 수 없습니다. Blob 구성을 확인하세요.
- **원인**: blob에서 커밋되지 않은 블록의 최대수는 100,000입니다.
- **권장 사항**:이 문제에 관한 자세한 내용은 Microsoft 제품 팀에 문의하세요.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>오류 코드: DF-Executor-PartitionDirectoryError
- **메시지**: 지정된 원본 경로에 파티션된 여러 디렉터리(예: &lt;원본 경로&gt;/<파티션 루트 디렉터리 1>/a=10/b=20, &lt;원본 경로&gt;/&lt;파티션 루트 디렉터리 2&gt;/c=10/d=30) 또는 다른 파일이나 파티션되지 않은 디렉터리가 포함된 파티션된 디렉터리(예: &lt;원본 경로&gt;/&lt;파티션 루트 디렉터리 1&gt;/a=10/b=20, &lt;원본 경로&gt;/Directory 2/file1)가 있습니다. 원본 경로에서 파티션 루트 디렉터리를 제거하고 개별 원본 변환을 통해 읽습니다.
- **원인**: 원본 경로에 여러 개의 파티션된 디렉터리가 있거나 다른 파일 또는 파티션되지 않은 디렉터리가 있는 파티션된 디렉터리가 있습니다.
- **권장 사항**: 원본 경로에서 파티션된 루트 디렉터리를 제거하고 별도의 원본 변환을 통해 읽습니다.

### <a name="error-code-df-executor-invalidtype"></a>오류 코드: DF-Executor-InvalidType
- **메시지**: 매개 변수 형식이 전달된 값 형식과 일치하는지 확인합니다. 파이프라인에서 부동 매개 변수를 전달하는 기능은 현재 지원되지 않습니다.
- **원인**: 선언된 형식과 실제 매개 변수 값 사이에 데이터 형식이 호환되지 않습니다.
- **권장 사항**: 데이터 흐름에 전달된 매개 변수 값이 선언된 형식과 일치하는지 확인합니다.

### <a name="error-code-df-executor-parseerror"></a>오류 코드: DF-Executor-ParseError
- **메시지**: 식을 구문 분석할 수 없습니다.
- **원인**: 잘못된 형식으로 인해 식에서 구문 분석 오류가 발생했습니다.
- **권장 사항**: 식의 형식을 확인합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-Executor-SystemImplicitCartesian
- **메시지**: INNER 조인에 대한 암시적 카테시안 제품은 지원되지 않습니다. 대신 CROSS 조인을 사용하세요. 조인에 사용된 열은 행에 대해 고유 키를 만들어야 합니다.
- **원인**: 논리 플랜 간 내부 조인의 암시적 카티전 제품은 지원되지 않습니다. 조인에 열을 사용하는 경우 고유 키를 만듭니다.
- **권장 사항**: 같지 않음 기반 조인의 경우 CROSS JOIN을 사용합니다.

### <a name="error-code-getcommand-outputasync-failed"></a>오류 코드: GetCommand OutputAsync 실패
- **메시지**: 데이터 흐름 디버그 및 데이터 미리 보기 중: GetCommand OutputAsync 실패 ...
- **원인**: 이 오류는 백 엔드 서비스 오류입니다. 
- **권장 사항**: 작업을 다시 시도하고 디버깅 세션을 다시 시작합니다. 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하세요. 

### <a name="error-code-df-executor-outofmemoryerror"></a>오류 코드: DF-Executor-OutOfMemoryError
 
- **메시지**: 실행 중 클러스터의 메모리 부족 이슈가 발생했습니다. 코어 수가 더 큰 통합 런타임 및/또는 메모리 최적화 컴퓨팅 형식을 사용하여 다시 시도합니다.
- **원인**: 클러스터의 메모리가 부족합니다.
- **권장 사항**: 디버그 클러스터는 개발용입니다. 데이터 샘플링 및 적절한 컴퓨팅 형식과 크기를 사용하여 페이로드를 실행합니다. 성능 팁은 [매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)을 참조하세요.

### <a name="error-code-df-executor-illegalargument"></a>오류 코드: DF-Executor-illegalArgument

- **메시지**: 연결된 서비스의 액세스 키가 올바른지 확인합니다.
- **원인**: 계정 이름 또는 액세스 키가 잘못되었습니다.
- **권장 사항**: 연결된 서비스에서 지정한 계정 이름 또는 액세스 키가 올바른지 확인합니다. 

### <a name="error-code-df-executor-columnunavailable"></a>오류 코드: DF-Executor-ColumnUnavailable
- **메시지**: 식에 사용된 열 이름을 사용할 수 없거나 잘못되었습니다.
- **원인**: 식에 잘못되었거나 사용할 수 없는 열 이름이 사용되었습니다.
- **권장 사항**: 식에 사용된 열 이름을 확인합니다.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>오류 코드: DF-Executor-OutOfDiskSpaceError
- **메시지**: 내부 서버 오류
- **원인**: 클러스터의 디스크 공간이 부족합니다.
- **권장 사항**: 파이프라인을 다시 시도합니다. 그래도 문제가 해결되지 않으면 고객 지원에 문의하세요.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>오류 코드: DF-Executor-StoreIsNotDefined
- **메시지**: 저장소 구성이 정의되어 있지 않습니다. 이 오류는 파이프라인의 잘못된 매개 변수 할당 때문에 발생할 수 있습니다.
- **원인**: 제공한 저장소 구성이 올바르지 않습니다.
- **권장 사항**: 파이프라인에서 매개 변수 값 할당을 확인합니다. 매개 변수 식에 잘못된 문자가 포함되어 있을 수 있습니다.


### <a name="error-code-4502"></a>오류 코드: 4502
- **메시지**: Integration Runtime의 제한으로 인해 실패를 초래하는 동시 MappingDataflow 실행이 상당히 있습니다.
- **원인**: 많은 수의 데이터 흐름 작업 실행이 통합 런타임에서 동시에 발생합니다. 자세한 내용은 [Azure Data Factory 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)를 참조하세요.
- **권장 사항**: 추가 데이터 흐름 작업을 병렬로 실행하려면 여러 통합 런타임에 배포합니다.

### <a name="error-code-4510"></a>오류 코드: 4510
- **메시지**: 실행 중에 예기치 않은 오류가 발생했습니다. 
- **원인**: 디버그 클러스터는 작업 클러스터와 다르게 작동하기 때문에 시간이 지남에 따라 과도한 디버그 실행으로 인해 클러스터가 손상되어 메모리 문제가 발생되고 갑자기 다시 시작될 수 있습니다.
- **권장 사항**: 디버그 클러스터를 다시 시작합니다. 디버그 세션 중에 여러 데이터 흐름을 실행하는 경우 작업 수준 실행이 주 디버그 클러스터에 부담을 주지 않으면서 별도의 세션을 만들므로 대신 작업 실행을 사용합니다.

### <a name="error-code-invalidtemplate"></a>오류 코드: InvalidTemplate
- **메시지**: 파이프라인 식을 평가할 수 없습니다.
- **원인**: 구문 오류 때문에 데이터 흐름 작업에 전달된 파이프라인 식이 올바르게 처리되지 않습니다.
- **권장 사항**: 작업 모니터링에서 작업을 확인하여 식을 확인합니다.

### <a name="error-code-2011"></a>오류 코드: 2011
- **메시지**: 작업이 Azure Integration Runtime에서 실행 중이었고 자체 호스팅 통합 런타임을 통해 연결된 데이터 저장소 또는 컴퓨팅의 자격 증명을 암호 해독하지 못했습니다. 이 작업과 연관된 연결된 서비스의 구성을 확인하고 적절한 통합 런타임 형식을 사용하는지 확인합니다.
- **원인**: 데이터 흐름은 자체 호스팅 통합 런타임에서 연결된 서비스를 지원하지 않습니다.
- **권장 사항**: 관리되는 Virtual Network 통합 런타임에서 실행되도록 데이터 흐름을 구성합니다.

### <a name="error-code-df-xml-invalidvalidationmode"></a>오류 코드: DF-Xml-InvalidValidationMode
- **메시지**: 잘못된 xml 유효성 검사 모드가 제공되었습니다.
- **원인**: 제공한 XML 유효성 검사 모드가 올바르지 않습니다.
- **권장 사항**: 매개 변수 값을 확인하고 올바른 유효성 검사 모드를 지정합니다.

### <a name="error-code-df-xml-invaliddatafield"></a>오류 코드: DF-Xml-InvalidDataField
- **메시지**: 손상된 레코드의 필드는 문자열 형식이어야 하며 null을 허용해야 합니다.
- **원인**: XML 원본에 제공한 열 `\"_corrupt_record\"`의 데이터 형식이 올바르지 않습니다.
- **권장 사항**: XML 원본의 `\"_corrupt_record\"` 열에 문자열 데이터 형식이 포함되어 있고 null 허용인지 확인합니다.

### <a name="error-code-df-xml-malformedfile"></a>오류 코드: DF-Xml-MalformedFile
- **메시지**: FAILFAST 모드의 경로가 있는 잘못된 형식의 xml입니다.
- **원인**: 경로가 있는 잘못된 형식의 XML이 FAILFAST 모드에 있습니다.
- **권장 사항**: XML 파일의 콘텐츠를 올바른 형식으로 업데이트합니다.

### <a name="error-code-df-xml-invalidreferenceresource"></a>오류 코드: DF-Xml-InvalidReferenceResource
- **메시지**: xml 데이터 파일의 참조 리소스를 확인할 수 없습니다.
- **원인**: XML 데이터 파일의 참조 리소스를 확인할 수 없습니다.
- **권장 사항**: XML 데이터 파일에서 참조 리소스를 확인해야 합니다.

### <a name="error-code-df-xml-invalidschema"></a>오류 코드: DF-Xml-InvalidSchema
- **메시지**: 스키마 유효성 검사에 실패했습니다.
- **원인**: XML 원본에서 제공한 스키마가 잘못되었습니다.
- **권장 사항**: XML 원본의 스키마 설정을 확인하여 원본 데이터의 스키마 하위 집합인지 확인합니다.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>오류 코드: DF-Xml-UnsupportedExternalReferenceResource
- **메시지**: xml 데이터 파일의 외부 참조 리소스는 지원되지 않습니다.
- **원인**: XML 데이터 파일의 외부 참조 리소스는 지원되지 않습니다.
- **권장 사항**: 현재 외부 참조 리소스가 지원되지 않는 경우 XML 파일 콘텐츠를 업데이트합니다.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>오류 코드: DF-GEN2-InvalidAccountConfiguration
- **Message**: 계정 키, tenant/spnId/spnCredential/spnCredentialType 또는 miServiceUri/miServiceToken 중 하나를 지정해야 합니다.
- **원인**: ADLS Gen2 연결된 서비스에서 제공한 자격 증명이 잘못되었습니다.
- **권장 사항**: 올바른 자격 증명 구성을 갖도록 ADLS Gen2 연결된 서비스를 업데이트합니다.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>오류 코드: DF-GEN2-InvalidAuthConfiguration
- **메시지**: 세 가지 인증 메서드(Key, ServicePrincipal 및 MI) 중 하나만 지정할 수 있습니다.
- **원인**: ADLS gen2 연결된 서비스에서 제공한 인증 메서드가 잘못되었습니다.
- **권장 사항**: 키, ServicePrincipal 및 MI의 세 가지 인증 방법 중 하나를 사용하도록 ADLS Gen2 연결된 서비스를 업데이트합니다.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>오류 코드: DF-GEN2-InvalidServicePrincipalCredentialType
- **메시지**: 서비스 주체 자격 증명 유형이 잘못되었습니다.
- **원인**: 서비스 주체 자격 증명 유형이 잘못되었습니다.
- **권장 사항**: ADLS Gen2 연결된 서비스를 업데이트하여 올바른 서비스 주체 자격 증명 유형을 설정합니다.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>오류 코드: DF-Blob-InvalidAccountConfiguration
- **메시지**: 계정 키 또는 sas 토큰 중 하나를 지정해야 합니다.
- **원인**: Azure Blob 연결된 서비스에서 제공한 자격 증명이 잘못되었습니다.
- **권장 사항**: Azure Blob 연결된 서비스에 계정 키 또는 SAS 토큰을 사용합니다.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>오류 코드: DF-Blob-InvalidAuthConfiguration
- **메시지**: 두 가지 인증 방법(키, SAS) 중 하나만 지정할 수 있습니다.
- **원인**: 연결된 서비스에서 제공한 인증 방법이 잘못되었습니다.
- **권장 사항**: Azure Blob 연결된 서비스에 키 또는 SAS 인증을 사용합니다.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>오류 코드: DF-Cosmos-PartitionKeyMissed
- **메시지**: 업데이트 및 삭제 작업의 파티션 키 경로를 지정해야 합니다.
- **원인**: Azure Cosmos DB 싱크에서 파티션 키 경로가 누락되었습니다.
- **권장 사항**: Azure Cosmos DB 싱크 설정에서 제공하는 파티션 키를 사용합니다.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>오류 코드: DF-Cosmos-InvalidPartitionKey
- **메시지**: 업데이트 및 삭제 작업의 파티션 키 경로는 비워둘 수 없습니다.
- **원인**: 업데이트 및 삭제 작업의 파티션 키 경로가 비어 있습니다.
- **권장 사항**: Azure Cosmos DB 싱크 설정에서 제공하는 파티션 키를 사용합니다.


### <a name="error-code-df-cosmos-idpropertymissed"></a>오류 코드: DF-Cosmos-IdPropertyMissed
- **메시지**: 삭제 및 업데이트 작업의 ‘id’ 속성을 매핑해야 합니다.
- **원인**: 업데이트 및 삭제 작업에 대한 `id` 속성이 누락 되었습니다.
- **권장 사항**: Cosmos DB 싱크 설정에서 입력 데이터에 `id` 열이 있는지 확인합니다. 그렇지 않으면 **선택 또는 파생 변환** 을 사용하여 싱크 전에 이 열을 생성합니다.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>오류 코드: DF-Cosmos-InvalidPartitionKeyContent
- **메시지**: 파티션 키는 /로 시작해야 합니다.
- **원인**: 제공한 파티션 키가 잘못되었습니다.
- **권장 사항**: Cosmos DB 싱크 설정에서 파티션 키가 `/`로 시작되는지 확인합니다(예: `/movieId`).

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>오류 코드: DF-Cosmos-InvalidPartitionKey
- **메시지**: 삭제 및 업데이트 작업에 대한 싱크에서 파티션 키가 매핑되지 않았습니다.
- **원인**: 제공한 파티션 키가 잘못되었습니다.
- **권장 사항**: Cosmos DB 싱크 설정에서 컨테이너의 파티션 키와 동일한 올바른 파티션 키를 사용합니다.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>오류 코드: DF-Cosmos-InvalidConnectionMode
- **메시지**: 연결 모드가 잘못되었습니다.
- **원인**: 제공한 연결 모드가 잘못되었습니다.
- **권장 사항**: Cosmos DB 설정에서 지원되는 모드가 **Gateway** 및 **DirectHttps** 인지 확인합니다.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>오류 코드: DF-Cosmos-InvalidAccountConfiguration
- **메시지**: accountName이나 accountEndpoint를 지정해야 합니다.
- **원인**: 제공한 계정 정보가 잘못되었습니다.
- **권장 사항**: Cosmos DB 연결된 서비스에서 계정 이름 또는 계정 엔드포인트를 지정합니다.

### <a name="error-code-df-github-writenotsupported"></a>오류 코드: DF-Github-WriteNotSupported
- **메시지**: Github 저장소에서는 쓰기를 허용하지 않습니다.
- **원인**: GitHub 저장소는 읽기 전용입니다.
- **권장 사항**: 저장소 엔터티 정의를 다른 위치에 둡니다.
  
### <a name="error-code-df-pgsql-invalidcredential"></a>오류 코드: DF-PGSQL-InvalidCredential
- **메시지**: 사용자/암호를 지정해야 합니다.
- **원인**: 사용자/암호가 누락되었습니다.
- **권장 사항**: 관련된 PostgreSQL 연결된 서비스의 자격 증명 설정이 올바른지 확인합니다.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>오류 코드: DF-Snowflake-InvalidStageConfiguration
- **메시지**: blob 스토리지 형식만 snowflake 읽기/쓰기 작업의 스테이지로 사용할 수 있습니다.
- **원인**: Snowflake에서 제공한 준비 구성이 잘못되었습니다.
- **권장 사항**: Azure Blob 연결된 서비스만 사용하도록 Snowflake 준비 설정을 업데이트합니다.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>오류 코드: DF-Snowflake-InvalidStageConfiguration
- **메시지**: Azure Blob + SAS 인증을 사용하여 Snowflake 스테이지 속성을 지정해야 합니다.
- **원인**: Snowflake에서 제공한 준비 구성이 잘못되었습니다.
- **권장 사항**: Snowflake 준비 설정에서 Azure BLOB + SAS 인증만 지정합니다.

### <a name="error-code-df-snowflake-invaliddatatype"></a>오류 코드: DF-Snowflake-InvalidDataType
- **메시지**: spark 형식은 snowflake에서 지원되지 않습니다.
- **원인**: Snowflake에서 제공한 데이터 형식이 잘못되었습니다.
- **권장 사항**: Snowflake 싱크를 적용하기 전에 파생 변환을 사용하여 입력 데이터의 관련 열을 문자열 형식으로 업데이트합니다.

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>오류 코드: DF-Hive-InvalidBlobStagingConfiguration
- **메시지**: Blob Storage 준비 속성을 지정해야 합니다.
- **원인**: Hive에서 제공한 준비 구성이 잘못되었습니다.
- **권장 사항**: 준비 프로세스로 사용되는 관련 Blob 연결된 서비스에서 계정 키, 계정 이름 및 컨테이너가 올바르게 설정되었는지 확인합니다.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>오류 코드: DF-Hive-InvalidGen2StagingConfiguration
- **메시지**: ADLS Gen2 스토리지 준비에서는 서비스 주체 키 자격 증명만 지원합니다.
- **원인**: Hive에서 제공한 준비 구성이 잘못되었습니다.
- **권장 사항**: 준비 프로세스로 사용되는 관련 ADLS Gen2 연결된 서비스를 업데이트합니다. 현재는 서비스 주체 키 자격 증명만 지원됩니다.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>오류 코드: DF-Hive-InvalidGen2StagingConfiguration
- **메시지**: ADLS Gen2 스토리지 준비 속성을 지정해야 합니다. 키, tenant/spnId/spnKey 또는 miServiceUri/miServiceToken 중 하나가 필요합니다.
- **원인**: Hive에서 제공한 준비 구성이 잘못되었습니다.
- **권장 사항**: 관련 ADLS Gen2 연결된 서비스를 Hive에서 준비 프로세스로 사용되는 올바른 자격 증명으로 업데이트합니다.

### <a name="error-code-df-hive-invaliddatatype"></a>오류 코드: DF-Hive-InvalidDataType
- **메시지**: 지원되지 않는 열입니다.
- **원인**: 제공한 열이 지원되지 않습니다.
- **권장 사항**: Hive에서 지원되는 데이터 형식과 일치하도록 입력 데이터의 열을 업데이트합니다.

### <a name="error-code-df-hive-invalidstoragetype"></a>오류 코드: DF-Hive-InvalidStorageType
- **메시지**: 스토리지 형식은 blob 또는 gen2일 수 있습니다.
- **원인**: Azure Blob 또는 ADLS Gen2 스토리지 유형만 지원됩니다.
- **권장 사항**: Azure Blob 또는 ADLS Gen2에서 적합한 스토리지 유형을 선택합니다.

### <a name="error-code-df-delimited-invalidconfiguration"></a>오류 코드: DF-Delimited-InvalidConfiguration
- **메시지**: 빈 줄 또는 사용자 지정 헤더 중 하나를 지정해야 합니다.
- **원인**: 제공한 구분 기호 구성이 잘못되었습니다.
- **권장 사항**: 빈 줄 또는 사용자 지정 헤더 중 하나를 지정하도록 CSV 설정을 업데이트합니다.

### <a name="error-code-df-delimited-columndelimitermissed"></a>오류 코드: DF-Delimited-ColumnDelimiterMissed
- **메시지**: 구문 분석에는 열 구분 기호가 필요합니다.
- **원인**: 열 구분 기호가 누락되었습니다.
- **권장 사항**: CSV 설정에서 구문 분석에 필요한 열 구분 기호가 있는지 확인합니다. 

### <a name="error-code-df-mssql-invalidcredential"></a>오류 코드: DF-MSSQL-InvalidCredential
- **메시지**: user/pwd, tenant/spnId/spnKey 또는 miServiceUri/miServiceToken 중 하나를 지정해야 합니다.
- **원인**: MSSQL 연결된 서비스에서 제공한 자격 증명이 잘못되었습니다.
- **권장 사항**: 관련된 MSSQL 연결된 서비스를 적절한 자격 증명으로 업데이트하고 **user/pwd**, **tenant/spnId/spnKey** 또는 **miServiceUri/miServiceToken** 중 하나를 지정해야 합니다.

### <a name="error-code-df-mssql-invaliddatatype"></a>오류 코드: DF-MSSQL-InvalidDataType
- **메시지**: 지원되지 않는 필드입니다.
- **원인**: 지원되지 않는 필드를 제공했습니다.
- **권장 사항**: MSSQL에서 지원하는 데이터 형식과 일치하도록 입력 데이터 열을 수정합니다.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>오류 코드: DF-MSSQL-InvalidAuthConfiguration
- **메시지**: 세 가지 인증 메서드(Key, ServicePrincipal 및 MI) 중 하나만 지정할 수 있습니다.
- **원인**: MSSQL 연결된 서비스에서 제공한 인증 방법이 잘못되었습니다.
- **권장 사항**: 관련된 MSSQL 연결된 서비스에서 세 가지 인증 방법(키, ServicePrincipal 및 MI) 중 하나만 지정할 수 있습니다.

### <a name="error-code-df-mssql-invalidcloudtype"></a>오류 코드: DF-MSSQL-InvalidCloudType
- **메시지**: 클라우드 형식이 잘못되었습니다.
- **원인**: 제공한 클라우드 형식이 잘못되었습니다.
- **권장 사항**: 관련된 MSSQL 연결된 서비스에서 클라우드 형식을 확인합니다.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>오류 코드: DF-SQLDW-InvalidBlobStagingConfiguration
- **메시지**: Blob Storage 준비 속성을 지정해야 합니다.
- **원인**: 제공한 Blob 스토리지 준비 설정이 잘못되었습니다.
- **권장 사항**: 준비에 사용된 Blob 연결된 서비스의 속성이 올바른지 확인합니다.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>오류 코드: DF-SQLDW-InvalidStorageType
- **메시지**: 스토리지 형식은 blob 또는 gen2일 수 있습니다.
- **원인**: 준비를 위해 제공한 스토리지 형식이 잘못되었습니다.
- **권장 사항**: 준비에 사용되는 연결된 서비스의 스토리지 형식이 Blob 또는 Gen2인지 확인합니다.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>오류 코드: DF-SQLDW-InvalidGen2StagingConfiguration
- **메시지**: ADLS Gen2 스토리지 준비에서는 서비스 주체 키 자격 증명만 지원합니다.
- **원인**: ADLS gen2 스토리지 준비를 위해 제공한 자격 증명이 잘못되었습니다.
- **권장 사항**: 준비에 사용한 Gen2 연결된 서비스의 서비스 주체 키 자격 증명을 사용합니다.
 

### <a name="error-code-df-sqldw-invalidconfiguration"></a>오류 코드: DF-SQLDW-InvalidConfiguration
- **메시지**: ADLS Gen2 스토리지 준비 속성을 지정해야 합니다. 키, tenant/spnId/spnCredential/spnCredentialType 또는 miServiceUri/miServiceToken 중 하나가 필요합니다.
- **원인**: 제공한 ADLS Gen2 준비 속성이 잘못되었습니다.
- **권장 사항**: **키**, **tenant/spnId/spnCredential/spnCredentialType** 또는 **miServiceUri/miServiceToken** 중 하나를 사용하도록 ADLS Gen2 스토리지 준비 설정을 업데이트합니다.

### <a name="error-code-df-delta-invalidconfiguration"></a>오류 코드: DF-DELTA-InvalidConfiguration
- **메시지**: 타임스탬프와 버전은 동시에 설정할 수 없습니다.
- **원인**: 타임스탬프와 버전은 동시에 설정할 수 없습니다.
- **권장 사항**: 델타 설정에서 타임스탬프 또는 버전을 설정합니다.

### <a name="error-code-df-delta-keycolumnmissed"></a>오류 코드: DF-DELTA-KeyColumnMissed
- **메시지**: 삽입 불가능 작업의 키 열을 지정해야 합니다.
- **원인**: 삽입이 불가능한 작업의 키 열이 누락되었습니다.
- **권장 사항**: 삽입이 불가능한 작업을 수행할 수 있도록 델타 싱크에서 키 열을 지정합니다.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>오류 코드: DF-DELTA-InvalidTableOperationSettings
- **메시지**: 다시 만들기 및 자르기 옵션을 둘 다 지정할 수는 없습니다.
- **원인**: 다시 만들기 및 자르기 옵션을 동시에 지정할 수 없습니다.
- **권장 사항**: 다시 만들기 또는 자르기 작업을 수행할 수 있도록 델타 설정을 업데이트합니다.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>오류 코드: DF-Excel-WorksheetConfigMissed
- **메시지**: Excel 시트 이름이나 인덱스가 필요합니다.
- **원인**: 제공한 Excel 워크시트 구성이 잘못되었습니다.
- **권장 사항**: 매개 변수 값을 확인하고 Excel 데이터를 읽을 시트 이름 또는 인덱스를 지정합니다.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>오류 코드: DF-Excel-InvalidWorksheetConfiguration
- **메시지**: Excel 시트 이름과 인덱스는 동시에 존재할 수 없습니다.
- **원인**: Excel 시트 이름과 인덱스가 동시에 제공됩니다.
- **권장 사항**: 매개 변수 값을 확인하고 Excel 데이터를 읽을 시트 이름 또는 인덱스를 지정합니다.

### <a name="error-code-df-excel-invalidrange"></a>오류 코드: DF-Excel-InvalidRange
- **메시지**: 잘못된 범위를 제공했습니다.
- **원인**: 제공한 범위가 잘못되었습니다.
- **권장 사항**: 매개 변수 값을 확인하고 올바른 범위를 [Azure Data Factory-Dataset 속성의 Excel 형식](./format-excel.md#dataset-properties) 참조로 지정합니다.

### <a name="error-code-df-excel-worksheetnotexist"></a>오류 코드: DF-Excel-WorksheetNotExist
- **메시지**: Excel 워크시트가 없습니다.
- **원인**: 제공한 워크시트 이름 또는 인덱스가 잘못되었습니다.
- **권장 사항**: 매개 변수 값을 확인하고 Excel 데이터를 읽을 올바른 시트 이름 또는 인덱스를 지정합니다.

### <a name="error-code-df-excel-differentschemanotsupport"></a>오류 코드: DF-Excel-DifferentSchemaNotSupport
- **메시지**: 스키마가 다른 읽기 Excel 파일은 현재 지원되지 않습니다.
- **원인**: 현재는 스키마가 다른 Excel 파일을 읽을 수 없습니다.
- **권장 사항**: 이 문제를 해결하려면 다음 옵션 중 하나를 적용합니다.
    1. **ForEach** + **데이터 흐름** 작업을 사용하여 Excel 워크시트를 하나씩 읽습니다. 
    1. 데이터를 읽기 전에 동일한 열을 갖도록 각 워크시트 스키마를 수동으로 업데이트합니다.

### <a name="error-code-df-excel-invaliddatatype"></a>오류 코드: DF-Excel-InvalidDataType
- **메시지**: 데이터 형식이 지원되지 않습니다.
- **원인**: 지원되지 않는 데이터 형식입니다.
- **권장 사항**: 관련 입력 데이터 열의 데이터 형식을 **'string'** 으로 변경합니다.

### <a name="error-code-df-excel-invalidfile"></a>오류 코드: DF-Excel-InvalidFile
- **메시지**: .xlsx와 .xls만 지원되는 중에 잘못된 excel 파일이 제공되었습니다.
- **원인**: 제공한 Excel 파일이 잘못되었습니다.
- **권장 사항**: 와일드카드를 사용하여 데이터를 읽기 전에 `.xls` 및 `.xlsx` Excel 파일을 필터링하여 가져옵니다.

### <a name="error-code-df-executor-outofmemorysparkbroadcasterror"></a>오류 코드: DF-Executor-OutOfMemorySparkBroadcastError
- **메시지**: 왼쪽/오른쪽 옵션을 사용하여 명시적으로 브로드캐스트된 데이터 세트는 노드의 메모리에 맞게 충분히 작아야 합니다. join/exists/lookup 변환에서 브로드캐스트 옵션 '끄기'를 선택하여 이 문제를 방지하거나 메모리가 더 많은 통합 런타임을 사용하면 됩니다.
- **원인**: 브로드캐스트된 테이블의 크기가 노드 메모리의 제한을 훨씬 초과합니다.
- **권장 사항**: 브로드캐스트 왼쪽/오른쪽 옵션은 노드의 메모리 크기에 맞출 수 있는 작은 데이터 세트에만 사용해야 하므로, 노드 크기를 적절하게 구성하거나 브로드캐스트 옵션을 해제해야 합니다.

### <a name="error-code-df-mssql-invalidfirewallsetting"></a>오류 코드: DF-MSSQL-InvalidFirewallSetting
- **메시지**: 호스트에 TCP/IP 연결을 설정하지 못했습니다. SQL Server 인스턴스가 호스트에서 실행 중이고 포트에서 TCP/IP 연결을 허용하는지 확인하세요. 포트에 대한 TCP 연결이 방화벽에 의해 차단되지 않는지 확인하세요.
- **원인**: SQL 데이터베이스의 방화벽 설정이 액세스 데이터 흐름을 차단합니다.
- **권장 사항**: SQL 데이터베이스에 대한 방화벽 설정을 확인하고 Azure 서비스 및 리소스가 이 서버에 액세스하도록 허용합니다.

### <a name="error-code-df-executor-acquirestoragememoryfailed"></a>오류 코드: DF-Executor-AcquireStorageMemoryFailed
- **메시지**: 언롤 메모리를 스토리지 메모리로 전송하지 못했습니다. 실행하는 동안 클러스터에 메모리가 부족합니다. 더 많은 코어가 있는 통합 런타임 및/또는 메모리 최적화 컴퓨팅 형식을 사용하여 다시 시도하세요.
- **원인**: 클러스터에 메모리가 부족합니다.
- **권장 사항**: 더 많은 코어가 있는 통합 런타임 및/또는 메모리 최적화 컴퓨팅 형식을 사용하세요.

### <a name="error-code-df-cosmos-deletedatafailed"></a>오류 코드: DF-Cosmos-DeleteDataFailed
- **메시지**: 3회 재시도 후에도 cosmos에서 데이터를 삭제하지 못했습니다.
- **원인**: Cosmos 컬렉션 처리량이 작아서 제한 조건을 충족했거나 행 데이터가 Cosmo에 없습니다.
- **권장 사항**: 이 문제를 해결하려면 다음 작업을 수행합니다.
    1. 오류가 404인 경우 관련 행 데이터가 Cosmos 컬렉션에 있는지 확인합니다. 
    1. 오류가 제한인 경우 Cosmos 컬렉션 처리량을 늘리거나 자동 스케일링으로 설정합니다.
    1. 오류가 요청 시간 초과인 경우 Cosmos 싱크에서 '일괄 처리 크기'를 더 작은 값(예: 1000)으로 설정합니다.

### <a name="error-code-df-sqldw-errorrowsfound"></a>오류 코드: DF-SQLDW-ErrorRowsFound
- **원인**: Azure Synapse Analytics 싱크에 쓸 때 오류/잘못된 행이 발견되었습니다.
- **권장 사항**: 거부된 데이터 스토리지 위치가 구성된 경우 이 위치에서 오류 행을 찾습니다.

### <a name="error-code-df-sqldw-exporterrorrowfailed"></a>오류 코드: DF-SQLDW-ExportErrorRowFailed
- **메시지**: 스토리지에 오류 행을 쓰는 동안 예외가 발생했습니다.
- **원인**: 스토리지에 오류 행을 쓰는 동안 예외가 발생했습니다.
- **권장 사항**: 거부된 데이터 연결된 서비스 구성을 확인합니다.

### <a name="error-code-df-executor-fieldnotexist"></a>오류 코드: DF-Executor-FieldNotExist
- **메시지**: 구조체의 필드가 없습니다.
- **원인**: 잘못되었거나 사용할 수 없는 필드 이름이 식에 사용됩니다.
- **권장 사항**: 식에 사용된 열 이름을 확인합니다.

### <a name="error-code-df-xml-invalidelement"></a>오류 코드: DF-Xml-InvalidElement
- **메시지**: XML 요소에 변환할 수 없는 하위 요소 또는 특성이 있습니다.
- **원인**: XML 요소에 변환할 수 없는 하위 요소 또는 특성이 있습니다.
- **권장 사항**: XML 요소에 올바른 하위 요소 또는 특성이 있도록 XML 파일을 업데이트합니다.

### <a name="error-code-df-gen2-invalidcloudtype"></a>오류 코드: DF-GEN2-InvalidCloudType
- **메시지**: 클라우드 형식이 잘못되었습니다.
- **원인**: 제공한 클라우드 형식이 잘못되었습니다.
- **권장 사항**: 관련 ADLS Gen2 연결된 서비스에서 클라우드 형식을 확인합니다.

### <a name="error-code-df-blob-invalidcloudtype"></a>오류 코드: DF-Blob-InvalidCloudType
- **메시지**: 클라우드 형식이 잘못되었습니다.
- **원인**: 제공한 클라우드 형식이 잘못되었습니다.
- **권장 사항**: 관련 Azure Blob 연결된 서비스에서 클라우드 형식을 확인합니다.

### <a name="error-code-df-cosmos-failtoresetthroughput"></a>오류 코드: DF-Cosmos-FailToResetThroughput
- **메시지**: 다른 스케일링 작업이 진행 중이므로 Cosmos DB 처리량 스케일링 작업을 수행할 수 없습니다. 잠시 후 다시 시도하세요.
- **원인**: 다른 스케일링 작업이 진행 중이므로 Cosmos DB 처리량 스케일링 작업을 수행할 수 없습니다.
- **권장 사항**: Cosmos 계정에 로그인하고, 자동 스케일링되도록 컨테이너 처리량을 수동으로 변경하거나 데이터 흐름 후에 사용자 지정 활동을 추가하여 처리량을 다시 설정합니다.

### <a name="error-code-df-executor-invalidpath"></a>오류 코드: DF-Executor-InvalidPath
- **메시지**: 경로가 파일로 확인되지 않습니다. 파일/폴더가 존재하고 숨겨져 있지 않은지 확인하세요.
- **원인**: 제공한 파일/폴더 경로가 잘못되어 찾거나 액세스할 수 없습니다.
- **권장 사항**: 파일/폴더 경로를 검사하여 존재하는 파일/폴더 경로이며 스토리지에서 액세스할 수 있는지 확인합니다.

### <a name="error-code-df-executor-invalidpartitionfilenames"></a>오류 코드: DF-Executor-InvalidPartitionFileNames
- **메시지**: 파일 이름 옵션은 파티션별로 설정되지만 파일 이름에 빈 값이 있으면 안 됩니다.
- **원인**: 제공한 파티션 파일 이름이 잘못되었습니다.
- **권장 사항**: 싱크 설정의 파일 이름 값이 올바른지 확인합니다.

### <a name="error-code-df-executor-invalidoutputcolumns"></a>오류 코드: DF-Executor-InvalidOutputColumns
- **메시지**: 결과에 출력 열이 없습니다. 적어도 하나 이상의 열이 매핑되어야 합니다.
- **원인**: 열이 매핑되지 않았습니다.
- **권장 사항**: 싱크 스키마를 확인하여 하나 이상의 열이 매핑되도록 합니다.

### <a name="error-code-df-executor-invalidinputcolumns"></a>오류 코드: DF-Executor-InvalidInputColumns
- **메시지**: 원본 데이터의 스키마에서 원본 구성의 열을 찾을 수 없습니다.
- **원인**: 원본에 제공한 열이 잘못되었습니다.
- **권장 사항**: 원본 구성의 열을 검사하여 해당 열이 원본 데이터 스키마의 하위 집합인지 확인합니다.

### <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>오류 코드: DF-AdobeIntegration-InvalidMapToFilter
- **메시지**: 사용자 지정 리소스의 키/Id 중 하나만 필터에 매핑되어야 합니다.
- **원인**: 제공한 구성이 잘못되었습니다.
- **권장 사항**: AdobeIntegration 설정에서 사용자 지정 리소스의 키/Id 중 하나만 필터에 매핑되도록 합니다.

### <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>오류 코드: DF-AdobeIntegration-InvalidPartitionConfiguration
- **메시지**: 단일 파티션만 지원됩니다. 파티션 스키마는 RoundRobin 또는 Hash일 수 있습니다.
- **원인**: 제공한 파티션 구성이 잘못되었습니다.
- **권장 사항**: AdobeIntegration 설정에서 단일 파티션만 설정되었고 파티션 스키마가 RoundRobin 또는 Hash일 수 있는지 확인합니다.

### <a name="error-code-df-adobeintegration-keycolumnmissed"></a>오류 코드: DF-AdobeIntegration-KeyColumnMissed
- **메시지**: 삽입 불가능 작업의 키 열을 지정해야 합니다.
- **원인**: 키 열이 누락되었습니다.
- **권장 사항**: 삽입 불가능 작업에 대한 키 열이 지정되도록 AdobeIntegration 설정을 업데이트합니다.

### <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>오류 코드: DF-AdobeIntegration-InvalidPartitionType
- **메시지**: 파티션 형식은 roundRobin이어야 합니다.
- **원인**: 제공한 파티션 형식이 잘못되었습니다.
- **권장 사항**: 파티션 형식이 RoundRobin이 되도록 AdobeIntegration 설정을 업데이트합니다.

### <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>오류 코드: DF-AdobeIntegration-InvalidPrivacyRegulation
- **메시지**: 현재 지원되는 유일한 개인 정보 보호 규정은 'GDPR'입니다.
- **원인**: 제공한 개인 정보 구성이 잘못되었습니다.
- **권장 사항**: 개인 정보 'GDPR'만 지원되므로 AdobeIntegration 설정을 업데이트합니다.

### <a name="error-code-df-executor-remoterpcclientdisassociated"></a>오류 코드: DF-Executor-RemoteRPCClientDisassociated
- **메시지**: 원격 RPC 클라이언트의 연결이 끊어졌습니다. 컨테이너가 임계값을 초과하거나 네트워크 문제로 인한 것 같습니다.
- **원인**: 일시적인 네트워크 문제로 인해 또는 spark 클러스터의 한 노드에 메모리가 부족하여 데이터 흐름 작업을 실행하지 못했습니다.
- **권장 사항**: 이 문제를 해결하려면 다음 옵션 중 하나를 사용합니다.
  - 옵션-1: 강력한 클러스터(드라이브 및 실행기 노드 둘 다에 빅 데이터를 처리하기에 충분한 메모리 포함)를 사용하여 "컴퓨팅 유형"을 "메모리 최적화"로 설정하여 데이터 흐름 파이프라인을 실행합니다. 설정은 아래 그림에 표시되어 있습니다.
        
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-compute-type.png" alt-text="컴퓨팅 유형의 구성을 보여 주는 스크린샷":::   

  - 옵션-2: 더 큰 클러스터 크기(예: 48개 코어)를 사용하여 데이터 흐름 파이프라인을 실행합니다. [클러스터 크기](./concepts-data-flow-performance.md#cluster-size) 문서를 통해 클러스터 크기에 대해 자세히 알아볼 수 있습니다.
  
  - 옵션-3: 입력 데이터를 다시 분할합니다. 데이터 흐름 spark 클러스터에서 실행되는 태스크의 경우 하나의 파티션이 하나의 태스크이고 하나의 노드에서 실행됩니다. 한 파티션의 데이터가 너무 큰 경우 노드에서 실행 중인 관련 태스크는 노드 자체보다 더 많은 메모리를 사용해야 하며, 이로 인해 오류가 발생합니다. 따라서 데이터 기울이기를 방지하기 위해 다시 분할을 사용할 수 있으며, 메모리 소비량이 너무 많지 않을 때 각 파티션의 데이터 크기를 평균 상태로 유지합니다.
    
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-partition.png" alt-text="파티션 구성을 보여 주는 스크린샷":::

    > [!NOTE]
    >  입력 데이터의 데이터 크기 또는 파티션 수를 평가한 다음, "최적화" 아래에 적절한 파티션 수를 설정해야 합니다. 예를 들어, 데이터 흐름 파이프라인 실행에서 사용하는 클러스터는 8개 코어이며 각 코어의 메모리는 20GB이지만 입력 데이터는 10개의 파티션이 있는 1000GB입니다. 데이터 흐름을 직접 실행하는 경우에는 1000GB/10 > 20GB이기 때문에 OOM 문제를 충족하므로 재파티션 수를 100(1000GB/100 < 20GB)으로 설정하는 것이 좋습니다.
    
  - 옵션-4: 소스/싱크/변환 설정을 조정하고 최적화합니다. 예를 들어 한 컨테이너에 있는 모든 파일을 복사하고 와일드카드 패턴을 사용하지 마세요. 자세한 내용은 [매핑 데이터 흐름 성능 및 튜닝 가이드](./concepts-data-flow-performance.md)를 참조하세요.

### <a name="error-code-df-mssql-errorrowsfound"></a>오류 코드: DF-MSSQL-ErrorRowsFound
- **원인**: Azure SQL Database 싱크에 쓰는 동안 오류/잘못된 행이 발견되었습니다.
- **권장 사항**: 거부된 데이터 스토리지 위치가 구성된 경우 이 위치에서 오류 행을 찾으세요.

### <a name="error-code-df-mssql-exporterrorrowfailed"></a>오류 코드: DF-MSSQL-ExportErrorRowFailed
- **메시지**: 스토리지에 오류 행을 쓰는 동안 예외가 발생했습니다.
- **원인**: 스토리지에 오류 행을 쓰는 동안 예외가 발생했습니다.
- **권장 사항**: 거부된 데이터의 연결된 서비스 구성을 확인합니다.

### <a name="error-code-df-synapse-invaliddatabasetype"></a>오류 코드: DF-Synapse-InvalidDatabaseType
- **메시지**: 데이터베이스 유형이 지원되지 않습니다.
- **원인**: 데이터베이스 유형이 지원되지 않습니다.
- **권장 사항**: 데이터베이스 유형을 확인하고 적절한 유형으로 변경합니다.

### <a name="error-code-df-synapse-invalidformat"></a>오류 코드: DF-Synapse-InvalidFormat
- **메시지**: 형식이 지원되지 않습니다.
- **원인**: 형식이 지원되지 않습니다. 
- **권장 사항**: 형식을 확인하고 적절한 형식으로 변경합니다.

### <a name="error-code-df-synapse-invalidtabledbname"></a>오류 코드: DF-Synapse-InvalidTableDBName
- **원인**: 테이블/데이터베이스 이름이 잘못되었습니다.
- **권장 사항**: 테이블/데이터베이스을 올바른 이름으로 변경합니다. 유효한 이름에는 영문자, 숫자 및 `_`만 사용할 수 있습니다.

### <a name="error-code-df-synapse-invalidoperation"></a>오류 코드: DF-Synapse-InvalidOperation
- **원인**: 이 작업은 지원되지 않습니다.
- **권장 사항**: 잘못된 작업을 변경합니다.

### <a name="error-code-df-synapse-dbnotexist"></a>오류 코드: DF-Synapse-DBNotExist
- **원인**: 데이터베이스가 없습니다.
- **권장 사항**: 데이터베이스가 있는지 확인합니다.

### <a name="error-code-df-synapse-storedprocedurenotsupported"></a>오류 코드: DF-Synapse-StoredProcedureNotSupported
- **메시지**: 서버리스(주문형) 풀의 경우 '저장 프로시저'를 원본으로 사용할 수 없습니다.
- **원인**: 서버리스 풀에 제한이 있습니다.
- **권장 사항**: '쿼리'를 원본으로 다시 사용하거나 저장 프로시저를 뷰로 저장한 다음, '테이블'을 원본으로 사용하여 뷰에서 직접 읽습니다.

### <a name="error-code-df-executor-broadcastfailure"></a>오류 코드: DF-Executor-BroadcastFailure
- **메시지**: 브로드캐스트 교환 동안 데이터 흐름을 실행하지 못했습니다. 잠재적 원인에는 원본의 잘못 구성된 연결 또는 브로드캐스트 조인 시간 초과 오류가 포함됩니다. 원본이 올바르게 구성되었는지 확인하려면 데이터 흐름 디버그 세션에서 연결을 테스트하거나 원본 데이터 미리 보기를 실행하세요. 브로드캐스트 조인 시간 초과를 방지하기 위해 조인/존재/조회 변환에서 '해제' 브로드캐스트 옵션을 선택할 수 있습니다. 성능 향상을 위해 브로드캐스트 옵션을 사용하려는 경우 브로드캐스트 스트림이 디버그 실행 시 60초, 작업 실행 시 300초 이내에 데이터를 생성할 수 있는지 확인합니다. 문제가 지속되면 고객 지원 서비스에 문의하세요.

- **원인**:  
    1. 원본 연결/구성 오류로 인해 조인/존재/조회 변환에서 브로드캐스트 오류가 발생할 수 있습니다.
    2. 브로드캐스트에는 디버그 실행 시 60초, 작업 실행 시 300초의 기본 시간 제한이 있습니다. 브로드캐스트 조인에서 브로드캐스트용으로 선택한 스트림이 너무 커서 이 한도 안에 데이터를 생성하지 못하는 것 같습니다. 브로드캐스트 조인을 사용하지 않으면 데이터 흐름별로 수행되는 기본 브로드캐스트가 같은 한도에 도달할 수 있습니다.

- **권장 사항**:
    1. 원본에서 데이터 미리 보기를 수행하여 원본이 올바르게 구성되었는지 확인합니다. 
    1. 브로드캐스트 옵션을 끄거나, 처리하는 데 60초가 넘게 걸릴 수 있는 대용량 데이터 스트림은 브로드캐스트하지 않습니다. 대신, 더 작은 스트림을 브로드캐스트하도록 선택합니다. 
    1. 많은 SQL/Data Warehouse 테이블 및 원본 파일은 일반적으로 브로드캐스트에 적합하지 않습니다. 
    1. 브로드캐스트 조인이 없는 경우 이 오류가 발생하면 더 큰 클러스터를 사용합니다. 
    1. 문제가 지속되면 고객 지원 서비스에 문의하세요.

### <a name="error-code-df-cosmos-shorttypenotsupport"></a>오류 코드: DF-Cosmos-ShortTypeNotSupport
- **메시지**: Cosmos DB에서는 Short 데이터 형식이 지원되지 않습니다.
- **원인**: Azure Cosmos DB에서는 short 데이터 형식이 지원되지 않습니다.
- **권장 사항**: Cosmos 싱크에서 사용하기 전에 관련 열을 short에서 integer로 변환하는 파생 변환을 추가합니다.

### <a name="error-code-df-blob-functionnotsupport"></a>오류 코드: DF-Blob-FunctionNotSupport
- **메시지**: 이 엔드포인트는 BlobStorageEvents, SoftDelete 또는 AutomaticSnapshot을 지원하지 않습니다. 이 엔드포인트를 사용하려면 이러한 계정 기능을 사용하지 않도록 설정하세요.
- **원인**: 서비스 주체 또는 관리 ID 인증을 사용하여 Azure Blob Storage 연결된 서비스를 만드는 경우에는 데이터 흐름에서 Azure Blob Storage 이벤트, 일시 삭제 또는 자동 스냅샷이 지원되지 않습니다.
- **권장 사항**: Azure Blob 계정에서 Azure Blob Storage 이벤트, 일시 삭제 또는 자동 스냅샷 기능을 사용하지 않도록 설정하거나 키 인증을 사용하여 연결된 서비스를 만듭니다.

### <a name="error-code-df-cosmos-invalidaccountkey"></a>오류 코드: DF-Cosmos-InvalidAccountKey
- **메시지**: 입력 인증 토큰이 요청을 처리할 수 없습니다. 필요한 페이로드가 프로토콜에 따라 작성되었는지 확인하고 사용 중인 키를 확인하세요.
- **원인**: Azure Cosmos DB 데이터를 읽고 쓸 수 있는 충분한 권한이 없습니다.
- **권장 사항**: 읽기-쓰기 키를 사용하여 Azure Cosmos DB에 액세스하세요.

## <a name="miscellaneous-troubleshooting-tips"></a>기타 문제 해결 팁
- **이슈**: 예기치 않은 예외가 발생하여 실행에 실패했습니다.
    - **메시지**: 데이터 흐름 활동 실행 중: 예기치 않은 예외로 실행에 실패했습니다.
    - **원인**: 이 오류는 백 엔드 서비스 오류입니다. 작업을 다시 시도하고 디버깅 세션을 다시 시작합니다.
    - **권장 사항**: 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하세요.

-  **이슈**: 디버그 데이터 미리 보기 중에 조인에 관한 출력 데이터가 없습니다.
    - **메시지**: 샘플링된 행이 너무 적기 때문에 발생할 수 있는 null 값이나 누락된 값이 많이 있습니다. 디버그 행 한도를 업데이트하고 데이터를 새로 고쳐 보세요.
    - **원인**: 조인 조건이 행과 일치하지 않았거나 데이터 미리 보기 중에 많은 수의 null 값이 발생했습니다.
    - **권장 사항**: **디버그 설정** 에서 원본 행 한도의 행 수를 늘립니다. 추가 데이터를 처리할 수 있을 만큼 큰 데이터 흐름 클러스터가 있는 Azure IR을 선택해야 합니다.
    
- **이슈**: 여러 줄로 된 CSV 파일이 있는 원본에서 유효성 검사 오류가 발생했습니다. 
    - **메시지**: 다음 오류 메시지 중 하나가 표시될 수 있습니다.
        - 마지막 열이 null이거나 누락되었습니다.
        - 원본에서 스키마 유효성 검사가 실패합니다.
        - 스키마 가져오기는 UX에 올바르게 표시되지 않으며 마지막 열의 이름에는 줄 바꿈 문자가 있습니다.
    - **원인**: 매핑 데이터 흐름에서 \r\n을 행 구분 기호로 사용하면 여러 줄 CSV 원본 파일이 현재 작동하지 않습니다. 캐리지 리턴 시 추가 줄이 있으면 오류가 발생할 수 있습니다. 
    - **권장 사항**: \r\n이 아닌 \n을 행 구분 기호로 사용하여 원본에서 파일을 생성합니다. 또는 복사 작업을 사용하여 행 구분 기호로 \n을 사용하도록 CSV 파일을 변환합니다.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침
1. 데이터 세트 연결의 상태를 확인합니다. 각 원본 및 싱크 변환에서 사용 중인 데이터 세트마다 연결된 서비스로 이동하여 연결을 테스트합니다.
2. 데이터 흐름 디자이너에서 파일과 테이블의 연결 상태를 확인합니다. 디버그 모드로 원본 변환에서 **데이터 미리 보기** 를 선택하여 데이터에 액세스할 수 있는지 확인합니다.
3. 데이터 미리 보기에서 모든 항목이 올바르면 파이프라인 디자이너로 이동하여 파이프라인 활동에 데이터 흐름을 배치합니다. 엔드투엔드 테스트에 대해 파이프라인을 디버깅합니다.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>데이터 흐름의 CSV/CDM 형식 개선 

**Azure Data Factory V2에서 매핑 데이터 흐름의 구분된 텍스트 또는 CDM 형식** 을 사용하는 경우 **2021년 5월 1일** 부터 데이터 흐름에서 구분된 텍스트/CDM의 개선 때문에 기존 파이프라인의 동작이 변경될 수 있습니다. 

개선 전에 다음과 같은 이슈가 발생할 수 있지만, 개선 후 이슈가 수정되었습니다. 이 개선 사항이 영향을 미치는지 확인하려면 다음 콘텐츠를 읽어 보세요. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>시나리오 1: 예기치 않은 행 구분 기호 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 여러 줄 설정이 True 또는 CDM으로 설정된 구분된 텍스트를 원본으로 사용합니다.
 - 첫 번째 행에 128자가 넘게 있습니다. 
 - 데이터 파일의 행 구분 기호가 `\n`이 아닙니다.

 개선 전에 기본 행 구분 기호 `\n`은 구분된 텍스트 파일을 구문 분석하는 데 예기치 않게 사용될 수 있습니다. 여러 줄 설정이 True로 설정되면 행 구분 기호 설정이 무효화되고 행 구분 기호가 처음 128자를 기준으로 자동 감지되기 ​​때문입니다. 실제 행 구분 기호를 검색하지 못하면 다시 `\n`으로 대체됩니다.  

 개선 후에 세 개의 행 구분 기호, 즉 `\r`, `\n`, `\r\n` 중 하나가 작동합니다.
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>
   다음 열의 경우:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   개선 전에 `\r`은 열 값에 저장됩니다. 구문 분석된 열 결과는 다음과 같습니다.<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>시나리오 2: ‘\r\n’이 포함된 열 값을 잘못 읽는 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 여러 줄 설정이 True 또는 CDM으로 설정된 구분된 텍스트를 원본으로 사용합니다. 
 - 행 구분 기호는 `\r\n`입니다.

 개선 전에 열 값을 읽을 때, 열 값의 `\r\n`은 `\n`으로 잘못 바뀔 수 있습니다. 

 개선 후 열 값의 `\r\n`은 `\n`으로 바뀌지 않습니다.

 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.
 
 **예제**:<br/>
  
 다음 열의 경우:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 개선 전에 구문 분석된 열 결과는 다음과 같습니다.<br/>
  **`A\n`**` B C`<br/>

 개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>시나리오 3: ‘\n’이 포함된 열 값을 잘못 쓰는 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 구분된 텍스트를 싱크로 사용합니다.
 - 열 값에 `\n`이 포함되어 있습니다.
 - 행 구분 기호가 `\r\n`로 설정되었습니다.
 
 개선 전에 열 값을 쓸 때, 열 값의 `\n`은 `\r\n`으로 잘못 바뀔 수 있습니다. 

 개선 후 열 값의 `\n`은 `\r\n`으로 바뀌지 않습니다.
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>

 다음 열의 경우:<br/>
 **`A\n`**` B C`<br/>

 개선 전, CSV 싱크는 다음과 같습니다.<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 개선 후, CSV 싱크는 다음과 같아야 합니다.<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>시나리오 4: 빈 문자열을 NULL로 잘못 읽을 때 이슈 발생
 
 다음 조건에 해당하는 경우 영향을 받습니다.
 - 구분된 텍스트를 원본으로 사용합니다. 
 - NULL 값은 비어 있지 않은 값으로 설정됩니다. 
 - 열 값이 빈 문자열이며 따옴표가 없습니다. 
 
 개선 전에, 따옴표가 없는 빈 문자열의 열 값은 NULL로 읽습니다. 

 개선 후, 빈 문자열은 NULL 값으로 구문 분석되지 않습니다. 
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>

 다음 열의 경우:<br/>
  `A, ,B, `<br/>

 개선 전에 구문 분석된 열 결과는 다음과 같습니다.<br/>
  `A null B null`<br/>

 개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>내부 서버 오류

다음은 내부 서버 오류를 일으킬 수 있는 특정 시나리오입니다.

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>시나리오 1: 적절한 컴퓨팅 크기/유형 및 기타 요인을 선택하지 않음

  데이터 흐름 실행의 성공 여부는 컴퓨팅 크기/유형, 처리할 원본/싱크 수, 파티션 사양, 포함된 변환, 데이터 세트 크기, 데이터 왜곡도 등의 많은 요인에 따라 달라집니다.<br/>
  
  자세한 내용은 [Azure Integration Runtime 최적화](concepts-data-flow-performance.md#ir)를 참조하세요.

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>시나리오 2: 병렬 작업으로 디버그 세션 사용

  파이프라인에서 ForEach와 같은 구문으로 데이터 흐름 디버그 세션을 사용하여 실행을 트리거하면 여러 병렬 실행이 동일한 클러스터에 제출될 수 있습니다. 메모리 부족 등의 리소스 문제로 인해 이러한 작업을 실행하는 동안 클러스터 오류와 관련된 문제가 발생할 수 있습니다.<br/>
  
  변경 내용을 게시한 후에 파이프라인 작업에 정의한 적절한 통합 런타임 구성으로 실행을 제출하려면 **지금 트리거** 또는 **디버그** > **작업 런타임 사용** 옵션을 선택합니다.

#### <a name="scenario-3-transient-issues"></a>시나리오 3: 일시적인 문제

  실행에 관련된 마이크로 서비스의 일시적인 문제로 인해 실행이 실패할 수 있습니다.<br/>
  
  파이프라인 작업에서 다시 시도를 구성하면 일시적인 문제로 인한 문제를 해결할 수 있습니다. 자세한 내용은 [작업 정책](concepts-pipelines-activities.md#activity-json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

문제 해결에 관한 자세한 내용은 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
