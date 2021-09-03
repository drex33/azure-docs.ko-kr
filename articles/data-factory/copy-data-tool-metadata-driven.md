---
title: 데이터 복사 도구에서 메타데이터 기반 접근 방식으로 대규모 데이터 복사 파이프라인 구축
description: ADF 데이터 복사 도구의 메타데이터 기반 접근 방식에 대한 정보 제공
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/19/2021
ms.author: yexu
ms.openlocfilehash: ab17ad8c47f7cc49588e5caf556282c40a9c0a76
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538574"
---
# <a name="build-large-scale-data-copy-pipelines-with-metadata-driven-approach-in-copy-data-tool-preview"></a>데이터 복사 도구에서 메타데이터 기반 접근 방식으로 대규모 데이터 복사 파이프라인 구축(미리 보기)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

방대한 양의 개체(예: 수천 개의 테이블)를 복사하거나 다양한 원본에서 데이터를 로드하려는 경우 적절한 방법은 필요한 복사 동작이 있는 개체의 이름 목록을 제어 테이블에 입력한 다음 사용하는 것입니다. 그런 다음 매개 변수화된 파이프라인을 사용하여 제어 테이블에서 동일한 내용을 읽고 그에 따라 작업에 적용합니다.  이렇게 하면 파이프라인을 다시 배포하는 대신 제어 테이블의 개체 이름을 업데이트하는 것만으로 쉽게 복사할 개체 목록을 유지 관리(예: 추가/제거)할 수 있습니다. 뿐만 아니라 정의된 복사 동작으로 어떤 파이프라인/트리거에 의해 어떤 개체가 복사되었는지 한 곳에서 쉽게 확인할 수 있습니다. 

ADF의 데이터 복사 도구는 이러한 메타데이터 기반 데이터 복사 파이프라인을 구축하는 과정을 용이하게 합니다. 마법사 기반 환경에서 직관적인 흐름을 거친 후 도구는 매개 변수화된 파이프라인과 SQL 스크립트를 만들어 그에 따라 외부 제어 테이블을 만들 수 있습니다. 만들어진 스크립트를 실행하여 SQL 데이터베이스에 제어 테이블을 만들면 파이프라인이 제어 테이블에서 메타데이터를 읽고 복사 작업에 자동으로 적용합니다.

## <a name="create-metadata-driven-copy-jobs-from-copy-data-tool"></a>데이터 복사 도구에서 메타데이터 기반 복사 작업 만들기

1. 데이터 복사 도구에서 **메타데이터 기반 복사 작업** 을 선택합니다.

   생성된 파이프라인이 메타데이터를 읽을 수 있도록 제어 테이블의 연결 및 테이블 이름을 입력해야 합니다.

   ![작업 유형 선택](./media/copy-data-tool-metadata-driven/select-task-type.png)

2. **원본 데이터베이스의 연결** 을 입력합니다. [매개 변수화된 연결 서비스](parameterize-linked-services.md)도 사용할 수 있습니다.

   ![매개 변수화된 연결 서비스 선택](./media/copy-data-tool-metadata-driven/select-parameterized-linked-service.png)

3. 복사할 **테이블 이름** 을 선택합니다.

   ![테이블 선택](./media/copy-data-tool-metadata-driven/select-table.png)

   > [!NOTE]
   > 테이블 형식 데이터 저장소를 선택하면 다음 페이지에서 전체 로드 또는 증분 로드를 추가로 선택할 수 있습니다. 스토리지 저장소를 선택하면 다음 페이지에서 추가로 전체 로드만 선택할 수 있습니다. 스토리지 저장소에서만 새 파일을 점진적으로 로드하는 것은 현재 지원되지 않습니다.  

4. **로드 동작** 을 선택합니다.
   >[!TIP]
   >모든 테이블에 대해 전체 복사를 수행하려면 **모든 테이블 전체 로드** 를 선택합니다. 증분 복사를 수행하려면 **각 테이블에 대해 개별적으로 구성** 을 선택하고 **델타 로드** 와 워터마크 열 이름 및 각 테이블에 대해 시작할 값을 선택합니다. 

5. **대상 데이터 저장소** 를 선택합니다. 

6. **설정** 페이지에서 **동시 복사 작업 수** 를 통해 원본 저장소에서 데이터를 동시에 복사할 최대 복사 작업 수를 결정할 수 있습니다. 기본값은 20입니다. 

   ![설정 페이지](./media/copy-data-tool-metadata-driven/settings.png)

7. 파이프라인 배포 후 제어 테이블 및 저장 프로시저 만들기를 위해 UI에서 SQL 스크립트를 복사하거나 다운로드할 수 있습니다. 

   ![스크립트 다운로드](./media/copy-data-tool-metadata-driven/download-scripts.png)

   두 개의 SQL 스크립트가 표시됩니다.
   
    - 첫 번째 SQL 스크립트는 두 개의 제어 테이블을 만드는 데 사용됩니다. 주 제어 테이블은 테이블 목록, 파일 경로 또는 복사 동작을 저장합니다. 연결 제어 테이블은 매개 변수화된 연결 서비스를 사용한 경우 데이터 저장소의 연결 값을 저장합니다.  
    - 두 번째 SQL 스크립트는 저장 프로시저를 만드는 데 사용됩니다. 증분 복사 작업이 매번 완료될 때 주 제어 테이블의 워터마크 값을 업데이트하는 데 사용됩니다. 

8. **SSMS** 를 열어 제어 테이블 서버에 연결하고 두 개의 SQL 스크립트를 실행하여 제어 테이블과 저장 프로시저를 만듭니다.

   ![제어 테이블 만들기 스크립트](./media/copy-data-tool-metadata-driven/create-control-table-script.png)

9. 기본 제어 테이블과 연결 제어 테이블을 쿼리하여 그 안의 메타데이터를 검토합니다.

   **주 제어 테이블**
   ![쿼리 제어 테이블 스크립트1](./media/copy-data-tool-metadata-driven/query-control-table.png)

   **연결 제어 테이블**
   ![쿼리 제어 테이블 스크립트2](./media/copy-data-tool-metadata-driven/query-connection-control-table.png)

10. ADF 포털로 돌아가서 파이프라인을 보고 디버그합니다. "MetadataDrivenCopyTask_### _######"라는 이름으로 만들어진 폴더가 표시됩니다. "MetadataDrivenCopyTask_###_TopLevel"로 이름이 지정된 파이프라인을 **클릭** 하고 **디버그 실행** 을 클릭합니다. 

    다음 매개 변수를 입력해야 합니다.
   
    | 매개 변수 이름 | 설명 | 
    |:--- |:--- |
    |MaxNumberOfConcurrentTasks |파이프라인이 실행되기 전에 실행되는 최대 동시 복사 작업 수를 언제든지 변경할 수 있습니다. 기본값은 데이터 복사 도구에 입력한 값입니다. |
    |MainControlTableName | 항상 기본 제어 테이블 이름을 변경할 수 있으므로 파이프라인은 실행 전에 해당 테이블에서 메타데이터를 가져옵니다.  |
    |ConnectionControlTableName |연결 제어 테이블 이름(선택 사항)을 항상 변경할 수 있으므로 파이프라인은 실행 전에 데이터 저장소 연결과 관련된 메타데이터를 가져옵니다. |
    |MaxNumberOfObjectsReturnedFromLookupActivity |출력 조회 작업의 한계에 도달하지 않도록 조회 작업에 의해 반환되는 최대 개체 수를 정의하는 방법이 있습니다. 대부분의 경우 기본값은 변경할 필요가 없습니다.  |
    |windowStart |폴더 경로로 동적 값(예: yyyy/mm/dd)을 입력할 때 매개 변수는 동적 폴더 경로를 채우기 위해 파이프라인에 현재 트리거 시간을 전달하는 데 사용됩니다. 일정 트리거 또는 연속 창 트리거에 의해 파이프라인이 트리거되면 사용자는 이 매개 변수의 값을 입력할 필요가 없습니다. 샘플 값: 2021-01-25T01:49:28Z | 
   

11. 파이프라인을 작동하려면 트리거를 사용하도록 설정합니다.

    ![트리거 사용](./media/copy-data-tool-metadata-driven/enable-trigger.png)


## <a name="update-control-table-by-copy-data-tool"></a>데이터 복사 도구로 제어 테이블 업데이트
복사할 개체를 추가 또는 제거하거나 각 테이블의 복사 동작을 변경하여 제어 테이블을 언제든지 직접 업데이트할 수 있습니다. 또한 데이터 복사 도구에서 UI 환경을 만들어 제어 테이블을 쉽게 편집할 수 있도록 합니다.

1. 최상위 파이프라인 **MetadataDrivenCopyTask_xxx_TopLevel** 을 마우스 오른쪽 단추로 클릭한 다음 **제어 테이블 편집** 을 선택합니다.

   ![제어 테이블 편집1](./media/copy-data-tool-metadata-driven/edit-control-table.png)

2. 편집할 제어 테이블에서 행을 선택합니다.

   ![제어 테이블 편집2](./media/copy-data-tool-metadata-driven/edit-control-table-select-tables.png)

3. 데이터 복사 도구 배포 절차를 완료하면 새 SQL 스크립트가 제공됩니다. SQL 스크립트를 다시 실행하여 제어 테이블을 업데이트합니다.

   ![제어 테이블 편집3](./media/copy-data-tool-metadata-driven/edit-control-table-create-script.png)

   > [!NOTE]
   > 파이프라인은 재배포되지 않습니다. 새로 만들어진 SQL 스크립트는 제어 테이블만 업데이트하는 데 도움이 됩니다. 

## <a name="control-tables"></a>제어 테이블

### <a name="main-control-table"></a>주 제어 테이블
제어 테이블의 각 행에는 복사할 하나의 개체(예: 하나의 테이블)에 대한 메타데이터가 포함됩니다.

| 열 이름 | 설명 | 
|:--- |:--- |
| Id | 복사할 개체의 고유 ID입니다. |
| SourceObjectSettings | 원본 데이터 세트의 메타데이터입니다. 스키마 이름, 테이블 이름 등이 될 수 있습니다. 이 [예](connector-azure-sql-database.md#dataset-properties)를 참조하세요. |
| SourceConnectionSettingsName | 연결 제어 테이블의 원본 연결 설정 이름입니다. 이는 선택 사항입니다. |
| CopySourceSettings | 복사 작업에 있는 원본 속성의 메타데이터입니다. 쿼리, 파티션 등이 될 수 있습니다. 이 [예](connector-azure-sql-database.md#azure-sql-database-as-the-source)를 참조하세요. |
| SinkObjectSettings | 대상 데이터 세트의 메타데이터입니다. 파일 이름, 폴더 경로, 테이블 이름 등이 될 수 있습니다. 이 [예](connector-azure-data-lake-storage.md#azure-data-lake-storage-gen2-as-a-sink-type)를 참조하세요. 동적 폴더 경로를 지정하면 변수 값이 제어 테이블의 여기에 기록되지 않습니다. |
| SinkConnectionSettingsName | 연결 제어 테이블의 대상 연결 설정 이름입니다. 이는 선택 사항입니다. |
| CopySinkSettings | 복사 작업에서 싱크 속성의 메타데이터입니다. preCopyScript, tableOption 등이 될 수 있습니다. 이 [예](connector-azure-sql-database.md#azure-sql-database-as-the-sink)를 참조하세요. |
| CopyActivitySettings | 복사 작업에서 변환기 속성의 메타데이터입니다. 열 매핑을 정의하는 데 사용됩니다. |
| TopLevelPipelineName | 이 개체를 복사할 수 있는 상위 파이프라인 이름입니다. |
| TriggerName | 파이프라인이 이 개체를 복사하도록 트리거할 수 있는 트리거 이름입니다. 디버그 실행의 경우 이름은 Sandbox입니다. 수동 실행의 경우 이름은 Manual입니다. 예약된 실행의 경우 이름은 연결된 트리거 이름입니다. 여러 이름을 입력할 수 있습니다. |
| DataLoadingBehaviorSettings |전체 로드 및 델타 로드입니다. |
| TaskId | 제어 테이블의 TaskId 다음에 복사할 개체의 순서(ORDER BY [TaskId] DESC)입니다. 복사할 개체가 많지만 동시에 복사할 수 있는 개체 수가 제한되어 있는 경우 각 개체의 TaskId를 변경하여 더 일찍 복사할 수 있는 개체를 결정할 수 있습니다. 기본값은 0입니다. |
| CopyEnabled | 데이터 수집 프로세스에서 항목이 사용하도록 설정되었는지 여부를 지정합니다. 허용되는 값: 1(사용), 0(사용 안 함). 기본값은 1입니다. |

### <a name="connection-control-table"></a>연결 제어 테이블
제어 테이블의 각 행에는 데이터 저장소에 대한 하나의 연결 설정이 포함됩니다.

| 열 이름 | 설명 | 
|:--- |:--- |
| Name | 주 제어 테이블에서 매개 변수화된 연결의 이름입니다. |
| ConnectionSettings | 연결 설정입니다. DB 이름, 서버 이름 등이 될 수 있습니다. |

## <a name="pipelines"></a>Pipelines
데이터 복사 도구에 의해 세 가지 수준의 파이프라인이 생성되는 것을 볼 수 있습니다.

### <a name="metadatadrivencopytask_xxx_toplevel"></a>MetadataDrivenCopyTask_xxx_TopLevel
이 파이프라인은 이 실행에서 복사해야 하는 총 개체(테이블 등) 수를 계산하고 최대 허용 동시 복사 작업을 기반으로 순차 배치 수를 계산한 다음 다른 파이프라인을 실행하여 다른 배치를 순차적으로 복사합니다. 

#### <a name="parameters"></a>매개 변수
| 매개 변수 이름 | 설명 | 
|:--- |:--- |
| MaxNumberOfConcurrentTasks | 파이프라인이 실행되기 전에 실행되는 최대 동시 복사 작업 수를 언제든지 변경할 수 있습니다. 기본값은 데이터 복사 도구에 입력한 값입니다. |
| MainControlTableName | 주 제어 테이블의 테이블 이름입니다. 파이프라인은 실행 전에 이 테이블에서 메타데이터를 가져옵니다. |
| ConnectionControlTableName | 연결 제어 테이블의 테이블 이름(선택 사항)입니다. 파이프라인은 실행 전에 데이터 저장소 연결과 관련된 메타데이터를 가져옵니다. |
| MaxNumberOfObjectsReturnedFromLookupActivity | 출력 조회 작업의 한계에 도달하지 않도록 조회 작업에 의해 반환되는 최대 개체 수를 정의하는 방법이 있습니다. 대부분의 경우 기본값은 변경할 필요가 없습니다.  |
| windowStart | 폴더 경로로 동적 값(예: yyyy/mm/dd)을 입력할 때 매개 변수는 동적 폴더 경로를 채우기 위해 파이프라인에 현재 트리거 시간을 전달하는 데 사용됩니다. 일정 트리거 또는 연속 창 트리거에 의해 파이프라인이 트리거되면 사용자는 이 매개 변수의 값을 입력할 필요가 없습니다. 샘플 값: 2021-01-25T01:49:28Z |

#### <a name="activities"></a>활동
| 작업 이름 | 활동 유형 | Description |
|:--- |:--- |:--- |
| GetSumOfObjectsToCopy  | 조회 | 이 실행에서 복사해야 하는 개체(테이블 등)의 총 수를 계산합니다. |
| CopyBatchesOfObjectsSequentially | ForEach | 최대 허용 동시 복사 작업을 기반으로 순차 배치 수를 정한 다음 다른 파이프라인을 실행하여 다른 배치를 순차적으로 복사합니다.  |
| CopyObjectsInOneBtach | 파이프라인 실행 | 다른 파이프라인을 실행하여 개체 배치 하나를 복사합니다. 이 배치에 속한 개체는 병렬로 복사됩니다. | 


### <a name="metadatadrivencopytask_xxx_-middlelevel"></a>MetadataDrivenCopyTask_xxx_ MiddleLevel
이 파이프라인은 하나의 배치 개체를 복사합니다. 이 배치에 속한 개체는 병렬로 복사됩니다. 

#### <a name="parameters"></a>매개 변수
| 매개 변수 이름 | 설명 | 
|:--- |:--- |
| MaxNumberOfObjectsReturnedFromLookupActivity | 출력 조회 작업의 한계에 도달하지 않도록 조회 작업에 의해 반환되는 최대 개체 수를 정의하는 방법이 있습니다.  대부분의 경우 기본값은 변경할 필요가 없습니다.  | 
| TopLayerPipelineName | 최상위 계층 파이프라인의 이름입니다. | 
| TriggerName | 트리거의 이름입니다. | 
| CurrentSequentialNumberOfBatch | 순차 배치의 ID입니다. | 
| SumOfObjectsToCopy | 복사할 개체의 총 수입니다. | 
| SumOfObjectsToCopyForCurrentBatch | 현재 일괄 처리에서 복사할 개체 수입니다. | 
| MainControlTableName | 주 제어 테이블의 이름입니다. |
| ConnectionControlTableName | 연결 제어 테이블의 이름입니다. |

#### <a name="activities"></a>활동
| 작업 이름 | 활동 유형 | Description |
|:--- |:--- |:--- |
| DivideOneBatchIntoMultipleGroups | ForEach  | 조회 작업의 출력 제한에 도달하지 않도록 단일 배치의 개체를 여러 병렬 그룹으로 나눕니다. |
| GetObjectsPerGroupToCopy | 조회 | 이 그룹에 복사해야 하는 제어 테이블에서 개체(테이블 등)를 가져옵니다. 제어 테이블의 TaskId 다음에 복사할 개체의 순서(ORDER BY [TaskId] DESC)입니다. |
| CopyObjectsInOneGroup | 파이프라인 실행 | 다른 파이프라인을 실행하여 한 그룹에서 개체를 복사합니다. 이 그룹에 속한 개체는 병렬로 복사됩니다. |


### <a name="metadatadrivencopytask_xxx_-bottomlevel"></a>MetadataDrivenCopyTask_xxx_ BottomLevel
이 파이프라인은 한 그룹의 개체를 복사합니다. 이 그룹에 속한 개체는 병렬로 복사됩니다.  

#### <a name="parameters"></a>매개 변수
| 매개 변수 이름 | 설명 | 
|:--- |:--- |
| ObjectsPerGroupToCopy | 현재 그룹에서 복사할 개체의 수입니다. | 
| ConnectionControlTableName | 연결 제어 테이블의 이름입니다. | 
| windowStart | 사용자가 구성한 경우 동적 폴더 경로를 채우기 위해 파이프라인에 현재 트리거 시간을 전달하는 데 사용되었습니다. | 

#### <a name="activities"></a>활동
| 작업 이름 | 활동 유형 | Description |
|:--- |:--- |:--- |
| ListObjectsFromOneGroup | ForEach | 한 그룹의 개체를 나열하고 각 개체를 다운스트림 작업으로 반복합니다. |
| RouteJobsBasedOnLoadingBehavior | 스위치 | 각 개체에 대한 로드 동작을 확인합니다. 기본값이거나 FullLoad인 경우 전체 로드를 수행합니다. DeltaLoad인 경우 워터마크 열을 통해 증분 로드를 수행하여 변경 사항을 식별합니다. |
| FullLoadOneObject | 복사 | 이 개체에 대한 전체 스냅샷을 만들어 대상에 복사합니다. |
| DeltaLoadOneObject | 복사 | 워터마크 열의 값을 비교하여 변경 사항을 식별해 마지막으로 변경된 데이터만 복사합니다. |
| GetMaxWatermarkValue | 조회 | 원본 개체를 쿼리하여 워터마크 열에서 최댓값을 가져옵니다. |
| UpdateWatermarkColumnValue | StoreProcedure | 다음에 사용할 제어 테이블에 새 워터마크 값을 다시 기록합니다. |

### <a name="known-limitations"></a>알려진 제한 사항
- 데이터 복사 도구는 현재 새 파일만 증분 복사하기 위한 메타데이터 기반 수집을 지원하지 않습니다. 그러나 이를 위해 자체 매개 변수화된 파이프라인을 가져올 수 있습니다.
- IR 이름, 데이터베이스 유형, 파일 형식 유형은 ADF에서 매개 변수화할 수 없습니다. 예를 들어 Oracle Server와 SQL Server 모두에서 데이터를 수집하려는 경우 매개 변수화된 두 개의 서로 다른 파이프라인이 필요합니다. 그러나 단일 제어 테이블은 두 세트의 파이프라인에서 공유할 수 있습니다. 



## <a name="next-steps"></a>다음 단계
데이터 복사 도구를 사용하는 다음 자습서를 시도해 보세요.

- [빠른 시작: 데이터 복사 도구를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-copy-data-tool.md)
- [자습서: 데이터 복사 도구를 사용하여 Azure에 데이터 복사](tutorial-copy-data-tool.md) 
- [자습서: 데이터 복사 도구를 사용하여 Azure에 온-프레미스 데이터 복사](tutorial-hybrid-copy-data-tool.md)
