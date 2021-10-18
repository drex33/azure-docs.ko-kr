---
title: 이벤트 기반 트리거 만들기
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics에서 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: e2621a6eea481866bb5351623065e7d113e8e9bd
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138670"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>스토리지 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Data Factory 또는 Synapse 파이프라인에서 만들 수 있는 스토리지 이벤트 트리거를 설명합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 고객이 Azure Blob Storage 계정의 파일 도착 또는 삭제처럼 스토리지 계정에서 발생하는 이벤트에 따라 파이프라인을 트리거해야 하는 경우가 많습니다. Data Factory 및 Synapse 파이프라인은 기본적으로 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 해당 이벤트에서 파이프라인을 트리거할 수 있습니다.

10분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요. *Microsoft.EventGrid/eventSubscriptions/* * 작업을 수행할 수 있어야 합니다. 이 작업은 EventGrid EventSubscription 기여자 기본 제공 역할의 일부입니다.

## <a name="create-a-trigger-with-ui"></a>UI로 트리거 만들기

이 섹션에서는 Azure Data Factory 및 Synapse 파이프라인 사용자 인터페이스 내에서 스토리지 이벤트 트리거를 만드는 방법을 살펴봅니다.

1. Data Factory의 **편집** 탭 또는 Azure Synapse의 **통합** 탭으로 전환합니다.

1. 메뉴에서 **트리거** 를 선택한 다음, **새로 만들기/편집** 을 선택합니다.

1. **트리거 추가** 페이지에서 **트리거 선택...** 을 선택한 다음, **+새로 만들기** 를 선택합니다.

1. 트리거 유형 **스토리지 이벤트** 선택

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1.png" alt-text="Data Factory UI에 새 스토리지 이벤트 트리거를 만드는 작성자 페이지의 스크린샷":::
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-1-synapse.png" alt-text=" Azure Synapse UI에 새 스토리지 이벤트 트리거를 만드는 작성자 페이지의 스크린샷.":::

5. Azure 구독 드롭다운에서 또는 스토리지 계정 리소스 ID를 수동으로 사용하여 스토리지 계정을 선택합니다. 이벤트가 발생할 컨테이너를 선택합니다. 컨테이너 선택은 필수이지만 모든 컨테이너를 선택하면 많은 이벤트가 발생할 수 있습니다.

   > [!NOTE]
   > 스토리지 이벤트 트리거는 현재 Azure Data Lake Storage Gen2 및 범용 버전 2 스토리지 계정만 지원합니다. Azure Event Grid 제한으로 인해 Azure Data Factory는 스토리지 계정당 최대 500개의 스토리지 이벤트 트리거를 지원합니다. 제한에 도달하는 경우 권장 사항 및 Event Grid 팀의 평가 시 제한 늘리기에 대해 지원에 문의하세요. 

   > [!NOTE]
   > 새 스토리지 이벤트 트리거를 만들거나 기존 스토리지 이벤트 트리거를 수정하려면, 서비스에 로그인하고 스토리지 이벤트 트리거를 게시하는 데 사용된 Azure 계정에서 스토리지 계정에 적절한 역할 기반 액세스 제어(Azure RBAC) 권한을 보유하고 있어야 합니다. 추가 권한은 필요하지 않습니다. Azure Data Factory 및 Azure Synapse의 서비스 주체에게는 스토리지 계정 또는 Event Grid에 대한 특별 권한이 필요하지 _않습니다_. 액세스 제어에 대한 자세한 내용은 [역할 기반 액세스 제어](#role-based-access-control)를 참조하세요.

1. **Blob 경로 시작 문자** 및 **Blob 경로 마지막 문자** 속성을 통해 이벤트를 수신할 컨테이너, 폴더 및 Blob 이름을 지정할 수 있습니다. 스토리지 이벤트 트리거를 사용하려면 하나 이상의 이러한 속성을 정의해야 합니다. **Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성 모두에 대해 이 문서의 뒷부분에 나오는 예제와 같이 다양한 패턴을 사용할 수 있습니다.

    * **Blob 경로 시작 문자:** Blob 경로는 폴더 경로로 시작해야 합니다. 유효한 값은 `2018/` 및 `2018/april/shoes.csv`이고 컨테이너를 선택하지 않으면 이 필드를 선택할 수 없습니다.
    * **Blob 경로 마지막 문자:** Blob 경로는 파일 이름 또는 확장명으로 끝나야 합니다. 유효한 값은 `shoes.csv` 및 `.csv`이고 컨테이너 및 폴더 이름은 지정된 경우 `/blobs/` 세그먼트로 구분해야 합니다. 예를 들어 이름이 ‘orders’인 컨테이너는 `/orders/blobs/2018/april/shoes.csv` 값을 가질 수 있습니다. 컨테이너의 폴더를 지정하려면 선행 '/' 문자를 생략합니다. 예를 들어 `april/shoes.csv`는 모든 컨테이너에서 이름이 'april'인 폴더에 있는 이름이 `shoes.csv`인 모든 파일에 대해 이벤트를 트리거합니다.
    * **시작하고** **끝나는** Blob 경로는 스토리지 이벤트 트리거에서 허용되는 유일한 패턴 일치입니다. 다른 유형의 와일드카드 일치는 트리거 유형에 대해 지원되지 않습니다.

1. 트리거가 **Blob 생성** 이벤트, **Blob 삭제** 이벤트 또는 둘 다에 응답할지 선택합니다. 지정된 스토리지 위치에서 각각의 이벤트는 트리거와 연결된 Data Factory 및 Synapse 파이프라인을 트리거합니다.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-2.png" alt-text="스토리지 이벤트 트리거 만들기 페이지의 스크린샷":::

1. 트리거가 0바이트인 Blob을 무시할지 여부를 선택합니다.

1. 트리거를 구성한 후 **다음: 데이터 미리 보기** 를 클릭합니다. 이 화면에는 스토리지 이벤트 트리거 구성과 일치하는 기존 Blob이 표시됩니다. 특정 필터가 있는지 확인합니다. 너무 광범위한 필터를 구성하면 많은 생성/삭제된 파일에 대한 일치를 수행하므로 비용에 상당한 영향을 미칠 수 있습니다. 필터 조건이 확인되면 **마침** 을 클릭합니다.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-3.png" alt-text="스토리지 이벤트 트리거 미리 보기 페이지의 스크린샷":::

1. 파이프라인을 이 트리거에 연결하려면 파이프라인 캔버스로 이동하고 **트리거** 를 클릭한 다음, **새로 만들기/편집** 을 선택합니다. 측면 탐색이 나타나면 **트리거 선택...** 드롭다운을 클릭하고 사용자가 만든 트리거를 선택합니다. **다음: 데이터 미리 보기** 를 클릭하여 구성이 정확한지 확인하고 **다음** 을 클릭하여 데이터 미리 보기가 올바른지 유효성을 검사합니다.

1. 파이프라인에 매개 변수가 있는 경우 트리거 실행 매개 변수 측 탐색에서 지정할 수 있습니다. 스토리지 이벤트 트리거는 Blob의 폴더 경로와 파일 이름을 `@triggerBody().folderPath` 및 `@triggerBody().fileName` 속성에 캡처합니다. 파이프라인에서 이러한 속성 값을 사용하려면 속성을 파이프라인 매개 변수에 매핑해야 합니다. 속성을 매개 변수에 매핑한 후 파이프라인 전체에서 `@pipeline().parameters.parameterName` 식을 통해 트리거가 캡처한 값에 액세스할 수 있습니다. 자세한 설명은 [파이프라인의 참조 트리거 메타데이터](how-to-use-trigger-parameterization.md)를 참조하세요.

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image-4.png" alt-text="파이프라인 매개 변수에 대한 스토리지 이벤트 트리거 매핑 속성의 스크린샷":::

   위의 예제에서 트리거는 컨테이너 _샘플 데이터_ 의 폴더 _이벤트 테스트_ 에서 .csv로 끝나는 Blob 경로를 만들 때 발생하도록 구성됩니다. **folderPath** 및 **fileName** 속성은 새 Blob의 위치를 캡처합니다. 예를 들어 MoviesDB.csv가 sample-data/event-testing 경로에 추가된 경우 `@triggerBody().folderPath` 값은 `sample-data/event-testing`, `@triggerBody().fileName` 값은 `moviesDB.csv`가 됩니다. 이 값은 예제에서 `sourceFolder` 및 `sourceFile` 파이프라인 매개 변수에 매핑되며 파이프라인 전체에서 각각 `@pipeline().parameters.sourceFolder` 및 `@pipeline().parameters.sourceFile`로 사용될 수 있습니다.

   > [!NOTE]
   > [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)에서 파이프라인 및 트리거를 만드는 경우 `@trigger().outputs.body.fileName` 및 `@trigger().outputs.body.folderPath`를 매개 변수로 사용해야 합니다. 이러한 두 속성은 Blob 정보를 캡처합니다. `@triggerBody().fileName` 및 `@triggerBody().folderPath`를 사용하는 대신 이러한 속성을 사용합니다.

1. 마쳤으면 **마침** 을 클릭합니다.

## <a name="json-schema"></a>JSON 스키마

다음 표는 스토리지 이벤트 트리거와 관련된 스키마 요소의 개요를 제공합니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **범위** | 스토리지 계정의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | 예, 이러한 값의 조합입니다. |
| **blobPathBeginsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 시작해야 합니다. 예를 들어 `/records/blobs/december/`는 `records` 컨테이너 아래의 `december` 폴더에서 Blob에 대한 트리거만을 시작합니다. | String   | | `blobPathBeginsWith` 또는 `blobPathEndsWith` 속성 중 하나 이상에 대한 값을 제공합니다. |
| **blobPathEndsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 끝나야 합니다. 예를 들어 `december/boxes.csv`는 `december` 폴더에서 `boxes`라는 이름의 Blob에 대한 트리거만을 시작합니다. | String   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **ignoreEmptyBlobs** | 0 바이트 BLOB이 파이프라인 실행을 트리거하는지 여부입니다. 기본적으로 True로 설정됩니다. | 부울 | true 또는 false | 예 |

## <a name="examples-of-storage-event-triggers"></a>스토리지 이벤트 트리거의 예제

이 섹션에는 스토리지 이벤트 트리거 설정의 예제가 제공됩니다.

> [!IMPORTANT]
> 다음 예제에 표시된 대로 컨테이너 및 폴더, 컨테이너 및 파일, 컨테이너, 폴더 및 파일을 지정할 때마다 경로의 `/blobs/` 세그먼트를 포함해야 합니다. **blobPathBeginsWith** 의 경우 UI는 트리거 JSON에서 폴더와 컨테이너 이름 사이에 `/blobs/`를 자동으로 추가합니다.

| 속성 | 예제 | Description |
|---|---|---|
| **다음으로 Blob 경로 시작** | `/containername/` | 컨테이너에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/` | `containername` 컨테이너 및 `foldername` 폴더에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/subfoldername/` | 하위 폴더를 참조할 수도 있습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/file.txt` | `containername` 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `file.txt` | 모든 경로에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `/containername/blobs/file.txt` | `containername` 컨테이너 아래에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `foldername/file.txt` | 모든 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Data Factory 및 Synapse 파이프라인은 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Blob 이벤트에 연결된 파이프라인의 수신 대기를 위한 무단 액세스, 업데이트를 위한 구독, 트리거가 엄격히 금지되도록 합니다.

* 새 스토리지 이벤트 트리거를 만들거나 기존 스토리지 이벤트 트리거를 업데이트하려면, 서비스에 로그인한 Azure 계정에서 관련 스토리지 계정에 적절한 액세스 권한을 보유하고 있어야 합니다. 그렇지 않으면 작업이 실패 하 고 _액세스가 거부_ 됩니다.
* Azure Data Factory 및 Azure Synapse에서는 Event Grid에 대한 특별 권한이 필요하지 않고, 작업의 Data Factory 또는 Azure Synapse 서비스 주체에게 특별 RBAC 권한을 할당할 필요가 _없습니다_.

다음 RBAC 설정은 스토리지 이벤트 트리거에 대해 작동합니다.

* 스토리지 계정에 대한 소유자 역할
* 스토리지 계정에 대한 기여자 역할
* 스토리지 계정 _/subscriptions/####/resourceGroups/####/providers/Microsoft.Storage/storageAccounts/storageAccountName_ 에 대한 _Microsoft.EventGrid/EventSubscriptions/Write_ 권한

서비스가 두 가지 약속을 어떻게 이행하는지 이해하기 위해 한 걸음 뒤로 물러서서 현장 뒤에서 살짝 엿보도록 하겠습니다. Azure Data Factory/Azure Synapse, Storage, Event Grid 간의 통합을 위한 대략적인 워크플로는 다음과 같습니다.

### <a name="create-a-new-storage-event-trigger"></a>새 스토리지 이벤트 트리거 만들기

이 대략적인 워크플로에서는 Azure Data Factory를 Event Grid와 상호 작용하여 스토리지 이벤트 트리거를 만드는 방법에 대해 설명합니다.  Azure Synapse의 경우 데이터 흐름이 동일하며, Synapse 파이프라인은 아래의 다이어그램에서 Data Factory 역할을 수행합니다.

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="스토리지 이벤트 트리거 만들기의 워크플로":::

작업 흐름에서 두 가지 눈에 띄는 호출:

* Azure Data Factory 및 Azure Synapse는 스토리지 계정에 직접적으로 접촉하지는 _않습니다_. 구독을 만드는 요청은 대신 Event Grid에 의해 릴레이되고 처리됩니다. 따라서 이 단계에서는 서비스에 스토리지 계정에 대한 권한이 필요하지 않습니다.

* 서비스 내에서 액세스를 제어하고 권한을 확인합니다. 서비스에서 스토리지 이벤트에 대한 구독 요청을 보내기 전에 사용자 권한을 확인합니다. 구체적으로 말하면, Azure 계정에 로그인하여 스토리지 이벤트 트리거를 만드는 경우 관련 스토리지 계정에 대한 적절한 액세스 권한이 있는지 확인합니다. 권한 검사에 실패하는 경우 트리거 생성도 실패합니다.

### <a name="storage-event-trigger-pipeline-run"></a>스토리지 이벤트에서 파이프라인 실행 트리거

이 대략적인 워크플로는 스토리지 이벤트에서 Event Grid를 통해 파이프라인 실행을 트리거하는 방법을 설명합니다. Azure Synapse의 경우 데이터 흐름이 동일하며, Synapse 파이프라인은 아래의 다이어그램에서 Data Factory 역할을 수행합니다.

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="파이프라인 실행을 트리거하는 스토리지 이벤트의 워크플로":::

서비스 내에서 파이프라인을 트리거하는 이벤트와 관련된 워크플로에서는 주목할 만한 호출이 세 가지 있습니다.

* Event Grid는 스토리지에서 시스템에 메시지를 표시할 때 가능한 한 빨리 메시지를 릴레이하는 푸시 모델을 사용합니다. 이는 풀 시스템이 사용되는 Kafka와 같은 메시징 시스템과 다릅니다.
* 이벤트 트리거는 들어오는 메시지에 대한 활성 수신기 역할을 하며 연결된 파이프라인을 적절하게 트리거합니다.
* 스토리지 이벤트 트리거 자체는 Storage 계정에 대한 직접 연결을 사용하지 않습니다.

  * 즉, 스토리지 계정에서 데이터를 처리하기 위해 파이프라인 내에 복사 또는 다른 활동이 있는 경우 서비스에서 연결된 서비스에 저장된 자격 증명을 사용하여 스토리지와 직접 연결합니다. 연결된 서비스가 적절하게 설정되었는지 확인
  * 그러나 파이프라인에서 스토리지 계정에 대한 참조를 사용하지 않는 경우 Data Factory에 스토리지 계정에 액세스할 수 있는 권한을 부여할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* 트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#trigger-execution)를 참조하세요.
* 파이프라인에서 트리거 메타데이터를 참조하는 방법에 대해 알아보려면 [파이프라인 실행의 참조 트리거 메타데이터](how-to-use-trigger-parameterization.md)를 참조하세요.
