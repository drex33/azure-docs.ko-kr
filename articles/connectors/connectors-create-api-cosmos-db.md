---
title: Azure Cosmos DB에 연결
description: Azure Logic Apps를 사용 하 여 Azure Cosmos DB 문서를 처리 하 고 만듭니다.
services: logic-apps
ms.suite: integration
author: jcocchi
ms.author: jucocchi
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
tags: connectors
ms.openlocfilehash: 7a5025e284933284d910412fb2f9771dee43e31d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103591"
---
# <a name="process-and-create-azure-cosmos-db-documents-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Azure Cosmos DB 문서 처리 및 만들기

Azure Logic Apps의 워크플로에서 [Azure Cosmos DB 커넥터](/connectors/documentdb/)를 사용 하 여 Azure Cosmos DB에 연결 하 고 문서 작업을 수행할 수 있습니다. 이 커넥터는 워크플로에서 Azure Cosmos DB 작업에 사용할 수 있는 트리거와 작업을 제공 합니다. 자동화 된 워크플로를 만들어 문서를 관리할 수 있습니다. 예를 들어 작업에는 문서 만들기 또는 업데이트, 읽기, 쿼리 및 삭제가 포함 됩니다.

[*관리 되는 커넥터*](managed.md) 작업을 사용 하 여 **논리 앱 (소비)** 및 **논리 앱 (표준)** 리소스 종류의 Azure Cosmos DB에 연결할 수 있습니다. **논리 앱 (표준)** 의 경우, Azure Cosmos DB는 현재 미리 보기로 제공 되는 [*기본*](built-in.md) 제공 작업을 제공 하며, 다양 한 기능, 더 나은 성능 및 높은 처리량을 제공 합니다. 예를 들어 **논리 앱 (표준)** 리소스 유형으로 작업 하는 경우 기본 제공 트리거를 사용 하 여 Azure Cosmos DB 컨테이너의 변경 내용에 응답할 수 있습니다. 논리 앱 워크플로에서 다른 작업 및 트리거와 Azure Cosmos DB 작업을 결합 하 여 이벤트 소싱 및 일반 데이터 처리와 같은 시나리오를 사용할 수 있습니다.

> [!NOTE]
> 현재 **논리 앱 (표준)** 리소스의 상태 저장 워크플로만 관리 되는 커넥터 작업과 기본 제공 작업을 모두 사용할 수 있습니다. 상태 비저장 워크플로는 기본 제공 작업만 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

- [Azure Cosmos DB 계정](../cosmos-db/sql/create-cosmosdb-resources-portal.md).

- Azure Cosmos DB 계정에 액세스 하려는 논리 앱 워크플로입니다. Azure Cosmos DB 트리거를 사용 하려면 [ **논리 앱 (표준)** 리소스 형식을 사용 하 여 논리 앱을 만들고](../logic-apps/create-single-tenant-workflows-azure-portal.md)빈 워크플로를 추가 해야 합니다.

## <a name="add-azure-cosmos-db-trigger"></a>Azure Cosmos DB 트리거 추가

Azure Logic Apps에서 모든 워크플로는 특정 이벤트가 발생 하거나 특정 조건이 충족 될 때 발생 하는 [트리거로](../logic-apps/logic-apps-overview.md#logic-app-concepts)시작 해야 합니다.

**논리 앱 (표준)** 리소스 유형으로 작업 하는 경우 **항목이 만들어지거나 수정 (미리 보기) 될 때** 호출 되는 기본 제공 트리거는 [Azure Cosmos DB 변경 피드 패턴](../cosmos-db/sql/change-feed-design-patterns.md)을 기반으로 합니다. **논리 앱 (소비)** 리소스 유형에 대해서는이 트리거를 사용할 수 없습니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

**논리 앱 (소비)** 리소스 유형에는 Azure Cosmos DB 트리거를 사용할 수 없습니다.

### <a name="standard"></a>[Standard](#tab/standard)

단일 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Cosmos DB 기본 제공 트리거를 추가 하려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱 워크플로를 엽니다.

1. 트리거를 찾으려면 다음 단계를 사용 합니다.

   1. 디자이너에서 **작업 선택** 을 선택 합니다.

   1. **트리거 추가** 창이 열리면 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 합니다.

   1. 검색 상자에 `Azure Cosmos DB`를 입력합니다. 트리거 목록에서 **항목을 만들거나 수정 (미리 보기) 할 때** 이름이 지정 된 트리거를 선택 합니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-add.png" alt-text="' 항목을 만들거나 수정 하는 경우 (미리 보기) ' 라는 트리거를 선택 하 여 표준 논리 앱 워크플로에 대 한 Azure Portal 및 디자이너를 보여 주는 스크린샷":::

1. 연결 정보를 입력 하 라는 메시지가 표시 되 면 [지금 Azure Cosmos DB에 대 한 연결을 만듭니다](#connect-to-azure-cosmos-db).

1. **매개 변수** 탭에서 트리거에 필요한 정보를 제공 합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **데이터베이스 Id** | Yes | <*데이터베이스 이름*> | 모니터링 하려는 컨테이너가 포함 된 데이터베이스의 이름입니다. 이 데이터베이스에도 임대 컨테이너가 있어야 합니다. 임대 컨테이너가 아직 없는 경우 커넥터는 이후 단계에서 사용자를 위해 하나를 만듭니다. |
   | **모니터링 된 컨테이너 Id** | Yes | <*컨테이너 이름*> | 모니터링할 컨테이너의 이름입니다. 이 컨테이너는 지정 된 데이터베이스에 이미 있어야 합니다. |
   | **임대 컨테이너 Id** |  Yes | <*임대-컨테이너-이름*> | 만들려는 기존 임대 컨테이너 또는 새 컨테이너의 이름입니다. 트리거는 `leases` 일반적인 기본 이름으로 미리 채워집니다. |
   | **임대 컨테이너 만들기** | 아니요 | **아니요** 또는 **예** | 지정 된 데이터베이스에 임대 컨테이너가 이미 있는 경우 **아니요** 를 선택 합니다. 트리거가이 컨테이너를 만들도록 하려면 **예** 를 선택 합니다. **예** 를 선택 하는 경우 **새 매개 변수 추가** 목록을 열고 **임대 컨테이너 처리량** 속성을 선택 해야 합니다. 이 컨테이너에 대해 프로 비전 할 [요청 단위 (RUs)](../cosmos-db/request-units.md) 의 수를 입력 합니다. |
   |||||

   다음 이미지는 예제 트리거를 보여 줍니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-trigger-parameters.png" alt-text="Azure Cosmos DB 트리거와 매개 변수 구성이 포함 된 표준 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

   > [!Note]
   > 트리거는 Azure Cosmos DB에서 만들어지거나 수정 된 각 항목에 대해 여러 워크플로 실행을 생성 하므로이 트리거의 동적 콘텐츠 출력은 항상 단일 항목입니다.

1. 워크플로에 원하는 다른 작업을 추가 합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

---

## <a name="add-azure-cosmos-db-action"></a>Azure Cosmos DB 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. Azure Cosmos DB 커넥터는 **논리 앱 (소비)** 및 **논리 앱 (표준)** 리소스 종류에 대 한 작업을 제공 합니다. 각 리소스 종류에 대 한 다음 예제에서는 문서를 만들거나 업데이트 하는 작업을 사용 하는 방법을 보여 줍니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

다중 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Cosmos DB 작업을 추가 하려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 워크플로를 엽니다.

1. 워크플로가 비어 있으면 원하는 트리거를 추가 합니다.

   이 예에서는 [ **HTTP 요청을 받을 때** 트리거](connectors-native-reqres.md#add-request-trigger)를 시작 합니다.

1. Azure Cosmos DB 작업을 추가 하려는 트리거 또는 작업에서 단계 사이에 있는 경우 **새 단계** 또는 **작업 추가** 를 선택 합니다.

1. 디자이너 검색 상자에을 입력 `Azure Cosmos DB` 합니다. 사용할 Azure Cosmos DB 작업을 선택 합니다.

   이 예에서는 **문서 만들기 또는 업데이트 (V3)** 라는 동작을 사용 합니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-action-add.png" alt-text="사용 가능한 Azure Cosmos DB 작업을 사용 하 여 소비 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. 연결 정보를 입력 하 라는 메시지가 표시 되 면 [지금 Azure Cosmos DB 계정에 대 한 연결을 만듭니다](#connect-to-azure-cosmos-db).

1. 작업에 필요한 정보를 입력합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **Azure Cosmos DB 계정 이름** | Yes | **연결 설정 사용 (<*Cosmos-DB-계정 이름*>)** 을 선택 하거나 수동으로 이름을 입력 합니다. | Azure Cosmos DB 계정의 계정 이름입니다. |
   | **데이터베이스 ID** | Yes | <*데이터베이스 ID*> | 연결 하려는 데이터베이스입니다. |
   | **컨테이너 ID** | Yes | <*컨테이너 ID*> | 쿼리해야 하는 컨테이너입니다. |
   | **Document** | Yes | <*JSON-문서*> | 만들려는 JSON 문서입니다. 이 예제에서는 트리거 출력의 요청 본문을 사용 합니다. <p><p>**팁**: 추가할 동적 콘텐츠 목록에 HTTP 트리거의 **본문** 토큰이 나타나지 않는 경우 트리거 이름 옆에 있는 **자세히 보기** 를 선택 합니다. <p><p>**참고**: 본문이 올바른 형식의 JSON 인지 확인 하 고 최소한 `id` 문서에 대 한 속성 및 파티션 키 속성을 포함 해야 합니다. 지정 된 및 파티션 키가 있는 문서가 이미 있는 경우 `id` 문서가 업데이트 됩니다. 그렇지 않으면 새 문서가 만들어집니다. |
   |||||

   다음 그림에서는 예제 작업을 보여 줍니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-create-action.png" alt-text="Azure Cosmos DB ' V3 (문서 만들기 또는 업데이트) ' 작업 및 매개 변수 구성을 사용 하 여 소비 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. 필요에 따라 다른 동작 설정을 구성 합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 테스트 하 여 워크플로에서 지정 된 컨테이너에 문서를 만들었는지 확인 합니다.

### <a name="standard"></a>[Standard](#tab/standard)

단일 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Cosmos DB 작업을 추가 하려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 워크플로를 엽니다.

1. 워크플로가 비어 있으면 원하는 트리거를 추가 합니다.

   이 예에서는 기본 스키마 정의를 사용 하 여 만들려는 항목을 나타내는 [ **HTTP 요청을 받을 때** 트리거](connectors-native-reqres.md#add-request-trigger)를 시작 합니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="' HTTP 요청을 받은 경우 ' 트리거 및 매개 변수 구성이 포함 된 표준 논리 앱 워크플로에 대 한 Azure Portal 및 디자이너를 보여 주는 스크린샷":::

1. Azure Cosmos DB 작업을 추가 하려는 트리거 또는 작업에서 **새 단계 삽입** ( **+** ) > **작업 추가** 를 선택 합니다.

1. 디자이너에서 **작업 추가** 가 선택 되어 있는지 확인 합니다. 열리는 작업 **추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **Azure Cosmos DB** 작업을 찾습니다.

   > [!NOTE]
   > 상태 저장 워크플로가 있는 경우 **Azure** 탭에서 관리 되는 *커넥터* 작업을 사용할 수 있지만 원하는 *기본 제공* 작업을 사용할 수 없는 경우에만이 작업을 사용 합니다.

1. 검색 상자에 `Azure Cosmos DB`를 입력합니다. 사용할 Azure Cosmos DB 작업을 선택 합니다.

   이 예제에서는 새 항목을 만들거나 기존 항목을 업데이트 하는 **항목 만들기 또는 업데이트 (미리 보기)** 라는 동작을 사용 합니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-action-add.png" alt-text="표준 논리 앱 워크플로에 대 한 디자이너와 사용 가능한 Azure Cosmos DB 작업을 보여 주는 스크린샷":::

1. 연결 정보를 입력 하 라는 메시지가 표시 되 면 [지금 Azure Cosmos DB 계정에 대 한 연결을 만듭니다](#connect-to-azure-cosmos-db).

1. 작업에 필요한 정보를 입력합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **데이터베이스 Id** | Yes | <*데이터베이스 ID*> | 연결 하려는 데이터베이스입니다. |
   | **컨테이너 Id** | Yes | <*컨테이너 ID*> | 쿼리해야 하는 컨테이너입니다. |
   | **Item** | Yes | <*JSON-문서*> | 만들려는 JSON 문서입니다. 이 예제에서는 트리거 출력의 요청 본문을 사용 합니다. <p><p>**팁**: 추가할 동적 콘텐츠 목록에 HTTP 트리거의 **본문** 토큰이 나타나지 않는 경우 트리거 이름 옆에 있는 **자세히 보기** 를 선택 합니다. <p><p>**참고**: 본문이 올바른 형식의 JSON 인지 확인 하 고 최소한 `id` 문서에 대 한 속성 및 파티션 키 속성을 포함 해야 합니다. 지정 된 및 파티션 키가 있는 문서가 이미 있는 경우 `id` 문서가 업데이트 됩니다. 그렇지 않으면 새 문서가 만들어집니다. | 
   | **파티션 키** | Yes | <*파티션-키*> | 만들려는 문서의 파티션 키 값입니다. 이 속성이 표시 되지 않으면 **새 매개 변수 추가** 목록을 열고 **파티션 키** 를 선택 합니다. <p><p>**중요**:이 속성은 별표 (*)와 함께 필수로 표시 되지 않지만 워크플로가 성공 하려면 속성이 실제로 필요 합니다. |
   |||||

   다음 그림에서는 예제 작업을 보여 줍니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-create-action.png" alt-text="Azure Cosmos DB ' 항목 만들기 또는 업데이트 ' 작업 및 매개 변수 구성을 사용 하 여 표준 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. 필요에 따라 다른 동작 설정을 구성 합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 테스트 하 여 워크플로에서 지정 된 컨테이너에 문서를 만들었는지 확인 합니다.

---

## <a name="connect-to-azure-cosmos-db"></a>Azure Cosmos DB에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[Azure Cosmos DB 트리거](#add-azure-cosmos-db-trigger) 또는 [Azure Cosmos DB 작업](#add-azure-cosmos-db-action)을 구성 하려면 먼저 데이터베이스 계정에 연결 해야 합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

**논리 앱 (소비)** 워크플로에서 Azure Cosmos DB 연결에는 다음과 같은 속성 값이 필요 합니다.

| 속성 | 필수 | 값 | Description |
|----------|----------|-------|-------------|
| **연결 이름** | 예 | <*connection-name*> | 연결에 사용할 이름입니다. |
| **인증 유형** | Yes | <*연결 형식*> | 사용할 인증 유형입니다. 이 예제에서는 **액세스 키** 를 사용 합니다. <p><p>- **액세스 키** 를 선택 하는 경우 나머지 필수 속성 값을 제공 하 여 연결을 만듭니다. <p><p>- **azure ad 통합** 을 선택 하는 경우 다른 속성 값은 필요 하지 않지만 [azure ad 인증 및 Cosmos DB 커넥터](/connectors/documentdb/#azure-ad-authentication-and-cosmos-db-connector)에 대 한 단계를 수행 하 여 연결을 구성 해야 합니다.  |
| **Cosmos DB 계정에 대 한 액세스 키** | Yes | <*액세스 키*> | 이 연결에 사용할 Azure Cosmos DB 계정의 선택 키입니다. 이 값은 읽기/쓰기 키 또는 읽기 전용 키 중 하나입니다. <p><p>**참고**: 키를 찾으려면 Azure Cosmos DB 계정 페이지로 이동 합니다. 탐색 메뉴의 **설정** 에서 **키** 를 선택 합니다. 사용 가능한 키 값 중 하나를 복사 합니다. |
| **계정 Id** | Yes | <*계정을-ID*> | 이 연결에 사용할 Azure Cosmos DB 계정의 이름입니다. |
|||||

다음 이미지는 연결의 예를 보여 줍니다.

:::image type="content" source="./media/connectors-create-api-cosmos-db/consumption-connection-configure.png" alt-text="소비 논리 앱 워크플로에 대 한 연결 구성 Azure Cosmos DB 예제를 보여 주는 스크린샷":::

> [!NOTE]
> 연결을 만든 후 대신 사용 하려는 기존 Azure Cosmos DB 연결이 있거나 다른 새 연결을 만들려면 트리거 또는 작업 세부 정보 편집기에서 **연결 변경** 을 선택 합니다.

### <a name="standard"></a>[Standard](#tab/standard)

**논리 앱 (표준)** 워크플로에서 Azure Cosmos DB 연결에는 다음과 같은 속성 값이 필요 합니다.

| 속성 | 필수 | 값 | Description |
|----------|----------|-------|-------------|
| **연결 이름** | 예 | <*connection-name*> | 연결에 사용할 이름입니다. |
| **연결 문자열** | Yes | <*연결 문자열*> | 연결에 사용할 Azure Cosmos DB 연결 문자열입니다. <p><p>**참고**: 연결 문자열을 찾으려면 Azure Cosmos DB 계정 페이지로 이동 합니다. 탐색 메뉴의 **설정** 에서 **키** 를 선택 합니다. 사용 가능한 연결 문자열 값 중 하나를 복사 합니다. |
|||||

다음 이미지는 연결의 예를 보여 줍니다.

:::image type="content" source="./media/connectors-create-api-cosmos-db/standard-connection-configure.png" alt-text="표준 논리 앱 워크플로에 대 한 연결 구성 Azure Cosmos DB 예제를 보여 주는 스크린샷":::

> [!NOTE]
> 연결을 만든 후 대신 사용 하려는 기존 Azure Cosmos DB 연결이 있거나 다른 새 연결을 만들려면 트리거 또는 작업 세부 정보 편집기에서 **연결 변경** 을 선택 합니다.

---

## <a name="connector-reference"></a>커넥터 참조

Azure Cosmos DB *관리 커넥터* 작업 (예: 트리거, 작업 및 제한)에 대 한 참조 정보는 [커넥터의 참조 페이지](/connectors/documentdb/)를 검토 하십시오.

Azure Cosmos DB *기본 제공* 작업에 대 한 해당 참조 페이지는 없습니다. 대신, 자세한 내용은 다음 표를 참조 하십시오.

| Type | Name | 매개 변수 |
|------|------|------------|
| 트리거 | 항목을 만들거나 수정할 때 | - **데이터베이스 Id**: 필수 항목입니다. 모니터링 및 임대 컨테이너를 포함 하는 데이터베이스의 이름입니다. <br>- **모니터링 된 컨테이너 Id**: 필수 항목입니다. 모니터링할 컨테이너의 이름입니다. <br>- **임대 컨테이너 Id**: 필수 항목입니다. 임대를 저장 하는 데 사용 되는 컨테이너의 이름입니다. <br>- **임대 컨테이너 만들기**: 필수 항목입니다. True 이면 임대 컨테이너가 아직 존재 하지 않는 경우 만듭니다. <br>- **컨테이너 처리량 임대**: 선택 사항입니다. 임대 컨테이너를 만들 때 할당할 요청 단위 수입니다. |
| 작업 | 항목 만들기 또는 업데이트 | - **데이터베이스 Id**: 필수 항목입니다. 데이터베이스의 이름입니다. <br>- **컨테이너 Id**: 필수 항목입니다. 컨테이너의 이름입니다. <br>- **Item**: 필수 항목입니다. 만들거나 업데이트할 항목입니다. <br>- **파티션 키**: 필수 항목입니다. 요청 된 항목에 대 한 파티션 키 값입니다. <br>- **Is Upsert**: 선택 사항입니다. True 이면 기존 항목을 바꿉니다. 그렇지 않으면 항목을 만듭니다. |
| 작업 | 대량으로 많은 항목 만들기 또는 업데이트 | 이 작업은 높은 처리량 시나리오에 대해 최적화 되어 있으며 작업에서 Azure Cosmos DB 컨테이너에 생성 될 항목을 전송 하기 전에 추가 처리가 수행 됩니다. 항목 수가 많은 경우이 추가 처리는 총 요청 시간을 단축 합니다. 적은 수의 항목에 대해 이러한 추가 오버 헤드로 인해 단일 항목 만들기 작업을 여러 개 사용 하는 것 보다 성능이 저하 될 수 있습니다. <p><p>- **데이터베이스 Id**: 필수 항목입니다. 데이터베이스의 이름입니다. <br>- **컨테이너 Id**: 필수 항목입니다. 컨테이너의 이름입니다. <br>- **Item**: 필수 항목입니다. 만들거나 업데이트할 항목의 배열입니다. <br>- **Is Upsert**: 선택 사항입니다. True 이면 기존 항목을 바꿉니다. 그렇지 않으면 항목을 만듭니다. |
| 작업 | 항목 읽기 | - **데이터베이스 Id**: 필수 항목입니다. 데이터베이스의 이름입니다. <br>- **컨테이너 Id**: 필수 항목입니다. 컨테이너의 이름입니다. <br>- **항목 id**: 필수 항목입니다. `id`요청 된 항목의 값입니다. <br>- **파티션 키**: 필수 항목입니다. 요청 된 항목에 대 한 파티션 키 값입니다. |
| 작업 | 항목 삭제 | - **데이터베이스 Id**: 필수 항목입니다. 데이터베이스의 이름입니다. <br>- **컨테이너 Id** 필수. 컨테이너의 이름입니다. <br>- **항목 id**: 필수 항목입니다. `id`요청 된 항목의 값입니다. <br>- **파티션 키**: 필수 항목입니다. 요청 된 항목에 대 한 파티션 키 값입니다. |
| 작업 | 쿼리 항목 | - **데이터베이스 Id**: 필수 항목입니다. 데이터베이스의 이름입니다. <br>- **컨테이너 Id**: 필수 항목입니다. 컨테이너의 이름입니다. <br>- **Sql 쿼리**: 필수 항목입니다. 쿼리 텍스트 SQL Azure Cosmos DB입니다.  <br>- **파티션 키**: 선택 사항입니다. 요청에 대 한 파티션 키 값입니다 (있는 경우). <br>- **연속 토큰**: 선택 사항입니다. Azure Cosmos DB 서비스에서 제공 하는이 쿼리의 연속 토큰입니다 (있는 경우). <br>- **최대 항목 수**: 선택 사항입니다. 쿼리에서 반환할 최대 항목 수입니다. |
||||

## <a name="best-practices-for-azure-cosmos-db-built-in-operations"></a>Azure Cosmos DB 기본 제공 작업에 대 한 모범 사례

### <a name="get-iterable-results-from-the-query-items-action-by-using-expressions"></a>식을 사용 하 여 쿼리 항목 작업에서 반복 가능한 결과 가져오기

**논리 앱 (표준)** 워크플로의 기본 제공 **쿼리 항목** 작업의 출력 형식은 비 반복 가능한 개체입니다. 쿼리 결과를 처리 하기 위해 반복 가능한 개체로 가져오려면 다음 단계를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 워크플로를 엽니다.

1. 워크플로가 비어 있으면 원하는 트리거를 추가 합니다.

   이 예제는 [**되풀이** 트리거](connectors-native-recurrence.md)로 시작합니다.

1. Azure Cosmos DB 작업을 추가 하려는 트리거 또는 작업에서 **새 단계 삽입** ( **+** ) > **작업 추가** 를 선택 합니다.

1. 디자이너에서 **작업 추가** 가 선택 되어 있는지 확인 합니다. 열리는 작업 **추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **Azure Cosmos DB** 작업을 찾습니다.

1. 검색 상자에 `Azure Cosmos DB`를 입력합니다. **쿼리 항목 (미리 보기)** 동작을 선택 합니다.

1. 연결 정보를 입력 하 라는 메시지가 표시 되 면 [Azure Cosmos DB 계정에](#connect-to-azure-cosmos-db)대 한 연결을 만듭니다.

1. 작업에 필요한 정보를 입력합니다.

   | 속성 | 필수 | 값 | 설명 |
   |------------|----------|-------|-------------|
   | **데이터베이스 Id** | Yes | <*데이터베이스 ID*> | 연결 하려는 데이터베이스입니다. |
   | **컨테이너 Id** | Yes | <*컨테이너 ID*> | 쿼리해야 하는 컨테이너입니다. |
   | **SQL 쿼리입니다** | Yes | <*sql-쿼리*> | 요청에 대 한 SQL 쿼리입니다. |
   |||||

   다음 그림에서는 예제 작업을 보여 줍니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-action.png" alt-text="Azure Cosmos DB ' 쿼리 항목 ' 작업 및 매개 변수 구성을 사용 하 여 표준 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. 필요에 따라 다른 동작 설정을 구성 합니다.

1. 작업에서 **새 단계 삽입** ( **+** ) > **작업 추가** 를 선택 합니다. 열리는 **작업 추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **각 작업에 대 한** 를 찾아서 추가 합니다.

1. **쿼리 항목** 작업에서 출력을 변환 하려면 다음 단계를 사용 합니다.

   1. **각** 작업 세부 정보 창에서 **이전 단계에서 출력 선택** 상자 내부를 클릭 하 여 동적 콘텐츠 목록이 표시 되도록 합니다. **식 탭을** 선택 하 여 식 편집기를 엽니다.

   1. 함수 목록에서 **참조 함수** 섹션을 찾고 식 편집기 상자에 나타나는 **outputs (actionName)** 함수를 선택 합니다.

   1. 편집기 상자에서 `'Query_items'` **출력 (actionName)** 함수에 대 한 매개 변수로를 입력 `?['body']?['item']?['results']` 하 고, 쿼리 결과를 사용 하 여 배열에 액세스 하 고, **확인** 을 선택 합니다. 전체 식은 다음 형식을 따릅니다.

      `outputs('Query_items')?['body']?['item']?['results']`

      :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-query-configure.png" alt-text="Azure Cosmos DB 쿼리 결과를 처리 하는 ' for each ' 작업을 사용 하는 표준 논리 앱 워크플로에 대 한 Azure Portal 및 디자이너를 보여 주는 스크린샷":::

1. **For each** 루프에서 원하는 다른 작업을 추가 합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 테스트 하 여 워크플로에서 원하는 출력을 반환 하는지 확인 합니다.

### <a name="confirm-action-success-by-checking-the-operation-status-code"></a>작업 상태 코드를 확인 하 여 작업 성공 확인

**논리 앱 (표준)** 워크플로에서 Azure Cosmos DB 기본 제공 작업을 사용 하는 경우 서비스 호출에서 반환 된 상태 코드를 확인 하는 각 작업 후에 Azure Cosmos DB 서비스에 대 한 단계를 수행 해야 합니다. 이러한 방식으로 워크플로의 후속 작업은 시나리오에 따라 Azure Cosmos DB 서비스에서 반환 하는 다양 한 상태 코드에 반응할 수 있습니다.

> [!NOTE]
> 현재 다른 관리 되는 커넥터 작업 및 기본 제공 작업과 달리 Azure Cosmos DB 작업에서 반환 된 상태 코드는 Azure Cosmos DB 서비스로 전송 되는 서비스 호출에서 반환 된 상태 코드와 다릅니다. 작업의 상태 코드는 동적 콘텐츠 목록에서 사용할 수 있으며, 서비스 호출의 상태 코드는 응답 개체의에서 반환 되 고 중첩 됩니다 `body` .
>
> 다음 단계에서는 응답 개체의를 구문 분석 하는 식을 사용 하 여 서비스 호출의 상태 코드에 액세스 하는 방법을 보여 줍니다 `body` .

예를 들어 다음 단계에서는 **항목 읽기** 작업을 사용 하는 워크플로를 만들고 서비스 호출에서 반환 되는 상태 코드를 Azure Cosmos DB 서비스에 확인 하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 워크플로를 엽니다.

1. 워크플로가 비어 있으면 원하는 트리거를 추가 합니다.

   이 예에서는 기본 스키마 정의를 사용 하 여 읽으려는 문서를 나타내는 [ **HTTP 요청을 받을 때** 트리거](connectors-native-reqres.md#)를 사용 하 여 시작 합니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-http-trigger.png" alt-text="요청 트리거 및 매개 변수 구성을 사용 하 여 표준 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. Azure Cosmos DB 작업을 추가 하려는 트리거 또는 작업에서 **새 단계 삽입** ( **+** ) > **작업 추가** 를 선택 합니다.

1. 디자이너에서 **작업 추가** 가 선택 되어 있는지 확인 합니다. 열리는 작업 **추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **Azure Cosmos DB** 작업을 찾습니다.

1. 검색 상자에 `Azure Cosmos DB`를 입력합니다. **쿼리 항목 (미리 보기)** 동작을 선택 합니다.

1. 연결 정보를 입력 하 라는 메시지가 표시 되 면 [Azure Cosmos DB 계정에](#connect-to-azure-cosmos-db)대 한 연결을 만듭니다.

1. 작업에 필요한 정보를 입력합니다.

   | 속성 | 필수 | 값 | 설명 |
   |------------|----------|-------|-------------|
   | **데이터베이스 Id** | Yes | <*데이터베이스 ID*> | 연결 하려는 데이터베이스입니다. |
   | **컨테이너 Id** | Yes | <*컨테이너 ID*> | 쿼리해야 하는 컨테이너입니다. |
   | **항목 Id** | Yes | <*항목 ID*> | `id`읽으려는 문서에 대 한 값입니다. |
   | **파티션 키** | Yes | <*파티션-키*> | 읽을 문서의 파티션 키 값입니다. |
   |||||

   다음 그림에서는 예제 작업을 보여 줍니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-read-configure.png" alt-text="Azure Cosmos DB ' 항목 읽기 ' 작업 및 매개 변수 구성을 사용 하는 표준 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. 필요에 따라 다른 동작 설정을 구성 합니다.

1. 작업에서 **새 단계 삽입** ( **+** ) > **작업 추가** 를 선택 합니다. 열리는 **작업 추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **조건** 작업을 찾아서 추가 합니다.

1. 조건에서 가장 왼쪽에 있는 **값** 상자를 클릭 하 여 동적 콘텐츠 목록이 표시 되도록 합니다. **식 탭을** 선택 하 여 식 편집기를 엽니다.

1. 함수 목록에서 **참조 함수** 섹션을 찾고 식 편집기 상자에 나타나는 **outputs (actionName)** 함수를 선택 합니다.

   :::image type="content" source="./media/connectors-create-api-cosmos-db/standard-condition-configure.png" alt-text="조건 구성이 있는 표준 논리 앱 워크플로에 대 한 디자이너를 보여 주는 스크린샷":::

1. 식 편집기 상자에서 `'Read_an_item'` **출력 (actionName)** 함수에 대 한 매개 변수로를 입력 `?['body']?['statusCode']` 한 다음 **확인** 을 선택 합니다.

   이 식은 `body` 응답 개체의 콘텐츠를 구문 분석 하 여 Azure Cosmos DB에 대 한 읽기 작업을 실행 하는 서비스 호출에서 반환 된 상태 코드에 액세스 합니다. 전체 식은 다음 형식을 따릅니다.

   `outputs('Read_an_item')?['body']?['statusCode']`

   > [!NOTE]
   > Azure Cosmos DB 동작에서 반환 된 상태 코드는 실제로 Azure Cosmos DB에 전송 된 호출에서 반환 된 상태 코드와 다릅니다. 작업의 상태 코드는 동적 콘텐츠 목록에서 사용할 수 있으며, 서비스 호출의 상태 코드는 응답 개체의에서 반환 되 고 중첩 됩니다 `body` .

1. 조건의 가운데 상자와 가장 오른쪽에 있는 **값 선택** 상자에서 워크플로에 적합 한 값을 지정 합니다. 이 예제에서 조건은 상태 코드가와 같은지 여부를 확인 합니다 `OK` .

   > [!NOTE]
   > 상태 코드는 숫자가 아닌 텍스트로 표시 됩니다.

1. 조건의 **True** 및 **False** 경로에서 시나리오에 적합 한 다른 작업을 추가 합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 테스트 하 여 워크플로에서 원하는 출력을 반환 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Logic Apps에 대한 커넥터 개요](apis-list.md)
