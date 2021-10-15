---
title: Azure Blob Storage에 연결
description: Azure Logic Apps를 사용하여 Azure Storage 계정에서 Blob을 만들고 관리합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/11/2021
tags: connectors
ms.openlocfilehash: 7ceafa54a202433e84535b19e73e99d38726dd0c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064060"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Azure Blob Storage에서 Blob 만들기 및 관리

Azure Logic Apps의 워크플로에서 [Azure Blob Storage 커넥터](/connectors/azureblobconnector/)를 사용 하 여 Azure storage 계정에 blob으로 저장 된 파일에 액세스 하 고 관리할 수 있습니다. 이 커넥터는 워크플로에서 blob 작업에 사용할 수 있는 트리거와 작업을 제공 합니다. 그런 다음 저장소 계정에서 파일을 관리 하는 작업을 자동화할 수 있습니다. 예를 들어 [커넥터 작업](/connectors/azureblobconnector/#actions) 에는 blob 검사, 삭제, 읽기 및 업로드가 포함 됩니다. [사용 가능한 트리거](/connectors/azureblobconnector/#triggers)는 Blob이 추가되거나 수정될 때 발생합니다.

**논리 앱 (소비)** 및 **논리 앱 (표준)** 리소스 유형 모두에서 Blob Storage에 연결할 수 있습니다. 다중 테 넌 트 Azure Logic Apps, 단일 테 넌 트 Azure Logic Apps 및 ISE (integration service environment)에서 논리 앱 워크플로와 함께 커넥터를 사용할 수 있습니다. **논리 앱 (표준)** 을 사용 Blob Storage 기본 제공 작업 *및* 관리 되는 커넥터 작업을 제공 합니다.

> [!IMPORTANT]
> 논리 앱 워크플로는 동일한 지역에 있는 경우 방화벽 뒤에 있는 저장소 계정에 직접 액세스할 수 없습니다. 이에 대 한 해결 방법으로 논리 앱 및 저장소 계정은 서로 다른 지역에 있을 수 있습니다. Azure Logic Apps에서 방화벽 뒤의 저장소 계정으로의 액세스를 설정 하는 방법에 대 한 자세한 내용은이 항목의 뒷부분에 나오는 [방화벽 뒤의 저장소 계정 액세스](#access-storage-accounts-behind-firewalls) 섹션을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

- [Azure Storage 계정 및 스토리지 컨테이너](../storage/blobs/storage-quickstart-blobs-portal.md)

- Blob Storage 계정에 액세스하려는 논리 앱 워크플로. Blob Storage 트리거를 사용 하 여 워크플로를 시작 하려면 [빈 논리 앱 워크플로가](../logic-apps/quickstart-create-first-logic-app-workflow.md)필요 합니다.

## <a name="limits"></a>제한

- [Ise (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행 되는 논리 앱 워크플로의 경우이 커넥터의 ise 레이블 버전은 [ise 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 대신 사용 합니다.

- 기본적으로 Blob Storage 작업은 ‘50MB 이하’의 파일을 읽거나 쓸 수 있습니다. 50MB보다 큰 최대 1,024MB의 파일을 처리하기 위해 Blob Storage 작업은 [메시지 청크](../logic-apps/logic-apps-handle-large-messages.md)를 지원합니다. [**Blob 콘텐츠 가져오기** 작업](/connectors/azureblobconnector/#get-blob-content)은 암시적으로 청크를 사용합니다.

- Blob Storage 트리거는 청크를 지원 하지 않습니다. 파일 콘텐츠를 요청하는 경우 트리거는 50MB 이하의 파일만 선택합니다. 50MB보다 큰 파일을 가져오려면 다음 패턴을 따릅니다.

  - [**Blob이 추가되거나 수정된 경우(속성만)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only))와 같이 파일 속성을 반환하는 Blob Storage 트리거를 사용합니다.

  - 전체 파일을 읽고 청크를 암시적으로 사용하는 Blob Storage [**Blob 콘텐츠 가져오기** 작업](/connectors/azureblobconnector/#get-blob-content)을 사용하여 트리거를 따릅니다.

## <a name="connector-reference"></a>커넥터 참조

이 커넥터에 대 한 자세한 기술 정보 (예: 트리거, 작업 및 제한)는 [커넥터의 참조 페이지를 참조](/connectors/azureblobconnector/)하세요. Blob Storage 커넥터를 사용 하지 않으려면 [대신 Blob 작업을 위한 관리 되는 id와 함께 HTTP 트리거 또는 작업을 사용](#access-blob-storage-with-managed-identities)하면 됩니다.

## <a name="add-blob-storage-trigger"></a>Blob Storage 트리거 추가

Azure Logic Apps에서 모든 워크플로는 특정 이벤트가 발생 하거나 특정 조건이 충족 될 때 발생 하는 [트리거로](../logic-apps/logic-apps-overview.md#logic-app-concepts)시작 해야 합니다.

이 커넥터에는 [**Azure Storage에서 Blob이 추가되거나 수정된 경우** 또는 **Blob이 추가되거나 수정된 경우(속성만)**](/connectors/azureblobconnector/#when-a-blob-is-added-or-modified-(properties-only))라는 하나의 사용 가능한 트리거가 있습니다. 스토리지 컨테이너에서 Blob의 속성이 추가되거나 업데이트된 경우 트리거가 실행합니다. Azure Logic Apps 엔진은 매번 논리 앱 인스턴스를 만들고 워크플로 실행을 시작 합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

다중 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Blob Storage 트리거를 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱 워크플로를 엽니다.

1. 디자이너 검색 상자에서를 필터로 입력 `Azure blob` 합니다. 트리거 목록에서 **blob이 추가 되거나 수정 될 때** 명명 된 트리거를 선택 합니다 (속성만).

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-add.png" alt-text="소비 논리 앱을 포함 하는 Azure Portal 및 workflow designer와 ' blob이 추가 되거나 수정 될 때 (속성만) ' 라는 트리거가 표시 된 스크린샷":::

1. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Azure Blob Storage 연결을 만듭니다](#connect-blob-storage-account).

1. 트리거에 필요한 정보를 입력합니다.

   1. **컨테이너** 속성 값으로 폴더 아이콘을 선택 하 여 blob 저장소 컨테이너를 찾습니다. 또는 경로를 수동으로 입력합니다.

   1. 필요에 따라 다른 트리거 설정을 구성합니다.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-trigger-configure.png" alt-text="매개 변수 구성이 포함 된 Azure Blob 트리거를 보여 주는 스크린샷":::

1. 워크플로에 하나 이상의 작업을 추가합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

### <a name="standard"></a>[Standard](#tab/standard)

단일 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Blob 트리거를 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱 워크플로를 엽니다.

1. 디자이너에서 **작업 선택** 을 선택 합니다. 열리는 **트리거 추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **azure Blob** *기본 제공* 트리거를 찾거나 **azure** 를 선택 하 여 **Azure Blob Storage** *관리 커넥터* 트리거를 찾습니다.

1. 검색 상자에 `Azure blob`를 입력합니다. 트리거 목록에서 **Azure Storage에서 Blob이 추가되거나 수정된 경우** 라는 트리거를 선택합니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-add.png" alt-text="Azure Portal, workflow designer, 표준 논리 앱 워크플로 및 Azure Blob 트리거를 선택 하는 스크린샷":::

1. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Azure Blob Storage 연결을 만듭니다](#connect-blob-storage-account).

1. 트리거에 필요한 정보를 입력합니다. **매개 변수** 탭에서 모니터링 하려는 blob에 대 한 **blob 경로** 를 추가 합니다.

   1. Blob 경로를 찾으려면 Azure Portal에서 스토리지 계정을 엽니다.

   1. 탐색 메뉴의 **데이터 스토리지** 에서 **컨테이너** 를 선택합니다.

   1. Blob 컨테이너를 선택합니다. 컨테이너 탐색 메뉴의 **설정** 에서 **속성** 을 선택합니다.

   1. Blob의 경로인 **URL** 값을 복사합니다. 경로는 `https://<storage-container-name>/<folder-name>/{name}`과 유사합니다. 대신 컨테이너 이름과 폴더 이름을 제공 합니다. 단, 리터럴 문자열은 그대로 유지 `{name}` 합니다.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-trigger-configure.png" alt-text="Blob Storage 트리거와 매개 변수 구성이 포함 된 표준 논리 앱 워크플로에 대 한 workflow designer를 보여 주는 스크린샷":::

1. 하나 이상의 작업을 추가 하 여 워크플로를 계속 만듭니다.

1. 디자이너 도구 모음에서 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

---

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Blob Storage 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

다중 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Blob Storage 작업을 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 워크플로를 엽니다.

1. 워크플로가 비어 있으면 원하는 트리거를 추가 합니다.

   이 예제는 [**되풀이** 트리거](connectors-native-recurrence.md)로 시작합니다.

1. Blob Storage 작업을 추가 하려는 트리거 또는 작업에서 단계 사이에 있는 경우 **새 단계** 또는 **작업 추가** 를 선택 합니다.

1. 디자이너 검색 상자에을 입력 `Azure blob` 합니다. 사용할 Blob Storage 작업을 선택 합니다.

   이 예제에서는 **Get blob content** 라는 동작을 사용 합니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-add.png" alt-text="사용 가능한 Blob Storage 작업 목록을 보여 주는 디자이너의 사용량 논리 앱 스크린샷":::

1. 연결 세부 정보를 묻는 메시지가 표시되면 [Blob Storage 계정에 대한 연결을 만듭니다](#connect-blob-storage-account).

1. 작업에 필요한 정보를 입력합니다.

    1. **Blob** 속성 값으로 폴더 아이콘을 선택 하 여 blob 저장소 컨테이너를 찾습니다. 또는 경로를 수동으로 입력합니다.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-action-configure.png" alt-text="Blob Storage 작업의 구성을 보여 주는 디자이너의 사용량 논리 앱 스크린샷":::

    1. 필요에 따라 다른 작업 설정을 구성합니다.

### <a name="standard"></a>[Standard](#tab/standard)

단일 테 넌 트 Azure Logic Apps에서 논리 앱 워크플로에 Azure Blob 작업을 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 디자이너의 워크플로를 펜으로 진행 합니다.

1. 워크플로가 비어 있으면 원하는 트리거를 추가 합니다.

   이 예제는 [**되풀이** 트리거](connectors-native-recurrence.md)로 시작합니다.

1. Blob Storage 작업을 추가 하려는 트리거 또는 작업에서 **새 단계 삽입** ( **+** ) > **작업 추가** 를 선택 합니다.

1. 디자이너에서 **작업 추가** 가 선택 되어 있는지 확인 합니다. 열리는 작업 **추가** 창에서 **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 하 여 **azure Blob** *기본 제공* 작업을 찾거나 **azure** 를 선택 하 여 **Azure Blob Storage** *관리 커넥터* 작업을 찾습니다.

1. 검색 상자에 `Azure blob`를 입력합니다. 사용 하려는 Azure Blob 작업을 선택 합니다.

   이 예제에서는 blob 콘텐츠를 읽기만 하는 **Azure Storage에서 blob 콘텐츠 읽기** 라는 동작을 사용 합니다. 나중에 콘텐츠를 보려면 다른 커넥터를 사용 하 여 blob 콘텐츠를 포함 하는 파일을 만드는 다른 작업을 추가 합니다. 예를 들어 blob 내용에 따라 파일을 만드는 OneDrive 작업을 추가할 수 있습니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-add.png" alt-text="표준 논리 앱 워크플로 및 사용 가능한 Azure Blob Storage 작업을 포함 하는 Azure Portal 및 workflow designer를 보여 주는 스크린샷":::

1. 연결 세부 정보를 묻는 메시지가 표시되면 [Blob Storage 계정에 대한 연결을 만듭니다](#connect-blob-storage-account).

1. 작업에 필요한 정보를 입력합니다.

    1. **컨테이너 이름** 에 사용 하려는 blob 저장소 컨테이너의 경로를 입력 합니다.

    1. **Blob name** 속성 값에 사용 하려는 blob의 경로를 입력 합니다.

       :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-action-configure.png" alt-text="Blob Storage 트리거 선택을 보여 주는 디자이너의 표준 논리 앱 스크린샷":::

    1. 필요에 따라 다른 작업 설정을 구성합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 논리 앱을 테스트하여 선택한 컨테이너에 Blob이 포함되어 있는지 확인합니다.

---

<a name="connect-blob-storage-account"></a>

## <a name="connect-to-blob-storage-account"></a>Blob Storage 계정으로 커넥트

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

### <a name="consumption"></a>[Consumption](#tab/consumption)

[Azure Blob Storage 트리거](#add-blob-storage-trigger) 또는 [Azure Blob Storage 작업](#add-blob-storage-action)을 구성 하려면 먼저 저장소 계정에 연결 해야 합니다. 연결에는 다음 속성이 필요 합니다.

| 속성 | 필수 | 값 | Description |
|----------|----------|-------|-------------|
| **연결 이름** | 예 | <*connection-name*> | 연결에 사용할 이름입니다. |
| **Storage 계정** | 예 | <*storage-account*> | 목록에서 스토리지 계정을 선택하거나 문자열을 제공합니다. <p>**참고**: 연결 문자열을 찾으려면 저장소 계정의 페이지로 이동 합니다. 탐색 메뉴의 **보안 + 네트워킹** 에서 **액세스 키**  >  **표시 키** 를 선택 합니다. 사용 가능한 연결 문자열 값 중 하나를 복사 합니다. |
|||||

다중 테 넌 트 Azure Logic Apps의 논리 앱 워크플로에서 Azure Blob Storage 연결을 만들려면 다음 단계를 수행 합니다.

1. **연결 이름** 에 연결 이름을 입력 합니다.

1. **Storage 계정** 에 대해 blob 컨테이너가 존재 하는 저장소 계정을 선택 합니다. 또는 **수동으로 연결 정보 입력** 을 선택하여 경로를 직접 제공합니다.

1. **만들기** 를 선택하여 연결을 설정합니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/consumption-connection-create.png" alt-text="소비 논리 앱 워크플로를 사용 하는 workflow designer를 보여 주는 스크린샷 및 Azure Blob Storage 단계에 대 한 새 연결을 추가 하 라는 메시지가 표시 됩니다.":::

> [!NOTE]
> 연결을 만든 후 사용 하려는 다른 기존 Azure Blob 저장소 연결이 있는 경우 트리거 또는 작업 세부 정보 편집기에서 **연결 변경** 을 선택 합니다.

저장소 계정에 연결 하는 데 문제가 있는 경우 [방화벽 뒤에 있는 저장소 계정에 액세스 하는 방법](#access-storage-accounts-behind-firewalls)을 검토 하세요.

### <a name="standard"></a>[Standard](#tab/standard)

[Azure blob 트리거](#add-blob-storage-trigger) 또는 [azure blob 작업](#add-blob-storage-action)을 구성 하려면 먼저 저장소 계정에 연결 해야 합니다. 연결에는 다음 속성이 필요 합니다.

| 속성 | 필수 | 값 | Description |
|----------|----------|-------|-------------|
| **연결 이름** | 예 | <*connection-name*> | 연결에 사용할 이름입니다. |
| **Azure Blob Storage 연결 문자열** | 예 | <*storage-account*> | 목록에서 스토리지 계정을 선택하거나 문자열을 제공합니다. <p>**참고**: 연결 문자열을 찾으려면 저장소 계정의 페이지로 이동 합니다. 탐색 메뉴의 **보안 + 네트워킹** 에서 **액세스 키**  >  **표시 키** 를 선택 합니다. 사용 가능한 연결 문자열 값 중 하나를 복사 합니다. |
|||||

단일 테 넌 트 Azure Logic Apps 논리 앱 워크플로에서 Azure Blob Storage 연결을 만들려면 다음 단계를 수행 합니다.

1. **연결 이름** 에 대해 이 연결의 이름을 입력합니다.

1. **연결 문자열 Azure Blob Storage** 사용 하려는 저장소 계정에 대 한 연결 문자열을 입력 합니다.

1. **만들기** 를 선택하여 연결을 설정합니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/standard-connection-create.png" alt-text="표준 논리 앱 워크플로를 사용 하는 workflow designer와 Azure Blob Storage 단계에 대 한 새 연결을 추가 하 라는 메시지가 표시 되는 스크린샷":::

> [!NOTE]
> 연결을 만든 후 사용 하려는 다른 기존 Azure Blob 저장소 연결이 있는 경우 트리거 또는 작업 세부 정보 편집기에서 **연결 변경** 을 선택 합니다.

저장소 계정에 연결 하는 데 문제가 있는 경우 [방화벽 뒤에 있는 저장소 계정에 액세스 하는 방법](#access-storage-accounts-behind-firewalls)을 검토 하세요.

---

## <a name="access-storage-accounts-behind-firewalls"></a>방화벽 뒤의 스토리지 계정에 액세스

[방화벽과 방화벽 규칙으로 액세스를 제한](../storage/common/storage-network-security.md)하여 Azure Storage 계정에 네트워크 보안을 추가할 수 있습니다. 그러나 이 설정은 스토리지 계정에 액세스해야 하는 Azure와 기타 Microsoft 서비스에 문제를 만듭니다. 데이터 센터의 로컬 통신이 내부 IP 주소를 추상화하므로 IP 제한을 통해 방화벽 규칙을 설정할 수 없습니다.

Blob Storage 커넥터를 사용하여 방화벽 뒤에 있는 스토리지 계정에 액세스하려면 다음을 수행합니다.

- [다른 지역의 스토리지 계정에 액세스](#access-storage-accounts-in-other-regions)
- [신뢰할 수 있는 가상 네트워크를 통해 스토리지 계정에 액세스](#access-storage-accounts-through-trusted-virtual-network)

방화벽 뒤에 있는 스토리지 계정에 액세스하기 위한 기타 솔루션:

- [관리 ID를 사용하여 신뢰할 수 있는 서비스로 스토리지 계정에 액세스](#access-blob-storage-with-managed-identities)
- [Azure API Management를 통해 스토리지 계정에 액세스](#access-storage-accounts-through-azure-api-management)

### <a name="access-storage-accounts-in-other-regions"></a>다른 지역의 스토리지 계정에 액세스

논리 앱 워크플로는 동일한 지역에 있는 경우 방화벽 뒤에 있는 저장소 계정에 직접 액세스할 수 없습니다. 해결 방법으로 논리 앱 리소스를 저장소 계정과 다른 지역에 배치 합니다. 그런 다음, [해당 지역의 관리형 커넥터의 아웃바운드 IP 주소](/connectors/common/outbound-ip-addresses#azure-logic-apps)에 대한 액세스 권한을 부여합니다.

> [!NOTE]
> 이 솔루션은 Azure Table Storage 커넥터와 Azure Queue Storage 커넥터에는 적용되지 않습니다. 대신 Table Storage 또는 Queue Storage에 액세스 하려면 [기본 제공 HTTP 트리거 및 작업을 사용](../logic-apps/logic-apps-http-endpoint.md)합니다.

저장소 계정 방화벽에 아웃 바운드 IP 주소를 추가 하려면 다음 단계를 수행 합니다.

1. 논리 앱 리소스의 지역에 대 한 [관리 되는 커넥터 아웃 바운드 IP 주소](/connectors/common/outbound-ip-addresses#azure-logic-apps) 를 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에서 저장소 계정 리소스를 찾아 엽니다.

1. 저장소 계정 탐색 메뉴의 **보안 + 네트워킹** 에서 **네트워킹** 을 선택 합니다.

   1. 다음 **에서 액세스 허용에서** **선택한 네트워크** 를 선택 하 여 관련 설정을 표시 합니다.

   1. **방화벽** 에서 액세스해야 하는 IP 주소 또는 범위를 추가합니다. 컴퓨터에서 저장소 계정에 액세스 해야 하는 경우 **클라이언트 IP 주소 추가** 를 선택 합니다.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-ip-configure.png" alt-text="허용 목록에 IP 주소와 범위를 추가하는 방화벽 설정을 보여 주는 Azure Portal의 Blob Storage 계정 네트워킹 페이지 스크린샷":::

   1. 완료되면 **저장** 을 선택합니다.

### <a name="access-storage-accounts-through-trusted-virtual-network"></a>신뢰할 수 있는 가상 네트워크를 통해 스토리지 계정에 액세스

관리하는 Azure 가상 네트워크에 스토리지 계정을 넣은 다음 해당 가상 네트워크를 신뢰할 수 있는 가상 네트워크 목록에 추가할 수 있습니다. [신뢰할 수 있는 가상 네트워크](../virtual-network/virtual-networks-overview.md)를 통해 스토리지 계정에 대한 액세스 권한을 논리 앱에 부여하려면 가상 네트워크의 리소스에 연결할 수 있는 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에 해당 논리 앱을 배포해야 합니다. 그런 다음 해당 ISE의 서브넷을 신뢰할 수 있는 목록에 추가할 수 있습니다. Blob Storage 커넥터와 같은 Azure Storage 커넥터는 스토리지 컨테이너에 직접 액세스할 수 있습니다. 이 설정은 ISE에서 서비스 엔드포인트를 사용하는 것과 동일합니다.

### <a name="access-storage-accounts-through-azure-api-management"></a>Azure API Management를 통해 스토리지 계정에 액세스

[API Management](../api-management/api-management-key-concepts.md)에 전용 계층을 사용하는 경우 API Management를 사용하고 방화벽을 통해 후자의 IP 주소를 허용하여 스토리지 API를 앞으로 이동할 수 있습니다. 기본적으로 API Management에서 사용하는 Azure 가상 네트워크를 스토리지 계정의 방화벽 설정에 추가합니다. 그런 다음 API Management 작업 또는 HTTP 작업을 사용하여 Azure Storage API를 호출할 수 있습니다. 그러나 이 옵션을 선택하는 경우 인증 프로세스를 직접 처리해야 합니다. 자세한 내용은 [간단한 엔터프라이즈 통합 아키텍처](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration)를 참조하세요.

## <a name="access-blob-storage-with-managed-identities"></a>관리 ID를 사용하여 Blob Storage 액세스

이 커넥터를 사용하지 않고 Blob Storage 액세스하려면 대신 [인증에 관리 ID를](../active-directory/managed-identities-azure-resources/overview.md) 사용할 수 있습니다. 방화벽을 통한 스토리지 계정에 대한 액세스 권한을 관리 ID와 같은 Microsoft 신뢰할 수 있는 서비스에 부여하는 예외를 만들 수 있습니다.

논리 앱에서 관리 ID를 사용하여 Blob Storage 액세스하려면 다음 단계를 수행합니다.

1. [스토리지 계정에 대한 액세스를 구성합니다.](#configure-storage-account-access)

1. [논리 앱 에 대한 역할 할당을 만듭니다.](#create-role-assignment-logic-app)

1. [논리 앱 에서 관리 ID에 대한 지원을 사용하도록 설정합니다.](#enable-managed-identity-support)

> [!NOTE]
> 이 솔루션의 제한 사항:
>
> - 워크플로에서 HTTP 트리거 또는 작업‘만’ 사용할 수 있습니다.
> - 스토리지 계정 연결을 인증하려면 관리 ID를 설정해야 합니다.
> - 관리 ID로 인증하는 경우에는 기본 제공 Blob Storage 작업을 사용할 수 없습니다.
> - 단일 테넌트 환경의 논리 앱에서는 사용자가 할당한 관리 ID가 아닌 시스템이 할당한 관리 ID만 사용할 수 있고 지원됩니다.

### <a name="configure-storage-account-access"></a>스토리지 계정 액세스 구성

예외 및 관리 ID 지원을 설정하려면 먼저 스토리지 계정에 대한 적절한 액세스를 구성합니다.

1. [Azure Portal](https://portal.azure.com)스토리지 계정 리소스를 찾아 엽니다.

1. 스토리지 계정 탐색 메뉴의 **보안 + 네트워킹** 아래에서 **네트워킹을** 선택합니다.

   1. **에서 액세스 허용에서** **선택한 네트워크를** 선택합니다. 그러면 관련 설정이 표시됩니다.

   1. 컴퓨터에서 스토리지 계정에 액세스해야 하는 경우 **방화벽** 아래에서 **클라이언트 IP 주소 추가를** 선택합니다.

   1. **예외에서** 신뢰할 **수 있는 Microsoft 서비스 이 스토리지 계정에 액세스하도록 허용을** 선택합니다.

      :::image type="content" source="./media/connectors-create-api-azureblobstorage/storage-networking-configure.png" alt-text="허용 설정이 있는 Azure Portal 및 Blob Storage 계정 네트워킹 창을 보여주는 스크린샷":::

   1. 완료되면 **저장** 을 선택합니다.

> [!NOTE]
> 워크플로에서 스토리지 계정에 연결하려고 할 때 **403 사용할** 수 없음 오류가 발생하면 여러 원인이 있을 수 있습니다. 추가 단계를 진행하기 전에 다음 해결 방법을 시도합니다. 먼저 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스할 수 있도록 허용** 을 사용하지 않도록 설정하고 변경 내용을 저장합니다. 그런 다음, 설정을 다시 사용하도록 설정하고 변경 내용을 다시 저장합니다.

<a name="create-role-assignment-logic-app"></a>

### <a name="create-role-assignment-for-logic-app"></a>논리 앱에 대한 역할 할당 만들기

다음으로 논리 앱 리소스에서 [관리 ID 지원을 사용하도록 설정합니다.](../logic-apps/create-managed-service-identity.md)

다음 단계는 다중 테넌트 환경의 소비 논리 앱 및 단일 테넌트 환경의 표준 논리 앱에 대해 동일합니다.

1. [Azure Portal](https://portal.azure.com)논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 탐색 메뉴의 **설정** 아래에서 **ID를 선택합니다.**

1. 시스템 **할당** 창에서 **상태를** **켜기로** 설정하고, 아직 사용하도록 설정하지 않은 경우 **저장을** 선택하고, 변경 내용을 확인합니다. **권한** 에서 **Azure 역할 할당** 을 선택합니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-1.png" alt-text="'ID' 설정 창과 'Azure 역할 할당 권한' 단추가 있는 Azure Portal 및 논리 앱 리소스 메뉴를 보여주는 스크린샷":::

1. **Azure 역할 할당** 창에서 **역할 할당 추가** 를 선택합니다.

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-add-2.png" alt-text="선택한 구독 및 새 역할 할당을 추가하는 단추가 있는 논리 앱 역할 할당 창을 보여주는 스크린샷.":::

1. 역할 **할당 추가** 창에서 다음 값을 가진 새 역할 할당을 설정합니다.

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **범위** | <*resource-scope*> | 역할 할당을 적용하려는 리소스 집합입니다. 이 예제에서는 **Storage** 선택합니다. |
   | **구독** | <*Azure-subscription*> | 스토리지 계정에 대한 Azure 구독입니다. |
   | **리소스** | <*storage-account-name*> | 논리 앱 워크플로에서 액세스하려는 스토리지 계정의 이름입니다. |
   | **역할** | <*role-to-assign*> | 워크플로가 리소스로 작업하는 데 시나리오에 필요한 역할입니다. 이 예제에서는 blob 컨테이너 및 날짜에 대한 읽기, 쓰기 및 삭제 액세스를 허용하는 Storage Blob **데이터 기여자** 가 필요합니다. 사용 권한 세부 정보를 알아보려면 드롭다운 메뉴의 역할 옆에 있는 정보 아이콘 위로 마우스를 이동합니다. |
   ||||

   :::image type="content" source="./media/connectors-create-api-azureblobstorage/role-assignment-configure.png" alt-text="범위, 구독, 리소스, 역할의 설정을 보여 주는 역할 할당 구성 창의 스크린샷":::

1. 완료되면 **저장을** 선택하여 역할 할당 만들기를 완료합니다.

<a name="enable-managed-identity-support"></a>

### <a name="enable-managed-identity-support-on-logic-app"></a>논리 앱에서 관리 ID 지원 사용

다음으로, 워크플로에서 [HTTP 트리거 또는 작업](connectors-native-http.md)을 추가합니다. [관리 ID를 사용하도록 인증 유형을 설정](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)해야 합니다.

다음 단계는 다중 테넌트 환경의 소비 논리 앱 및 단일 테넌트 환경의 표준 논리 앱에 대해 동일합니다.

1. 디자이너에서 논리 앱 워크플로를 엽니다.

1. 시나리오에 따라 **워크플로에 HTTP** 트리거 또는 작업을 추가합니다. 필요한 매개 변수 값을 설정합니다.

   1. 요청에 대한 **메서드를** 선택합니다. 이 예제에서는 HTTP **PUT** 메서드를 사용합니다.

   1. Blob의 **URI** 를 입력합니다. 경로는 `https://<storage-container-name>/<folder-name>/{name}`과 유사합니다. 대신 컨테이너 이름과 폴더 이름을 입력하고 `{name}` 리터럴 문자열을 유지합니다.

   1. **헤더 아래에서** 다음 항목을 추가합니다.

      - 값이 인 Blob 형식 `x-ms-blob-type` `BlockBlob` 헤더입니다.

      - 적절한 값이 있는 API 버전 `x-ms-version` 헤더입니다.

      자세한 내용은 [관리 ID를 사용하여 액세스 인증](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)과 [Azure Storage 서비스에 대한 버전 관리](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests)를 참조하세요.

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-connect.png" alt-text="필요한 'PUT' 매개 변수가 있는 워크플로 디자이너 및 HTTP 트리거 또는 작업을 보여주는 스크린샷.":::

1. 새 **매개 변수 추가** 목록에서 **인증을** 선택하여 [관리 ID 를 구성합니다.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)

    1. **인증** 아래에서 **인증 유형** 속성에 대해 관리 **ID** 를 선택합니다.

    1. 관리 **ID** 속성에 **대해 시스템 할당 관리 ID 를** 선택합니다.

    :::image type="content" source="./media/connectors-create-api-azureblobstorage/managed-identity-authenticate.png" alt-text="관리 ID 인증 매개 변수 설정이 있는 워크플로 디자이너 및 HTTP 작업을 보여주는 스크린샷.":::

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

이제 [Blob service REST API](/rest/api/storageservices/blob-service-rest-api)를 호출하여 필요한 모든 스토리지 작업을 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Logic Apps에 대한 커넥터 개요](apis-list.md)