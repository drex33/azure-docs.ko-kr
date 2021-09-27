---
title: 워크플로에서 XML 유효성 검사를 위한 스키마 추가
description: Enterprise 통합 팩을 사용하여 Azure Logic Apps 워크플로에 대한 XML 문서의 유효성을 검사하는 스키마를 추가합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: f80ed9c7fa9aa2d291e4f045b6cfc7da695cb22b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611437"
---
# <a name="add-schemas-to-validate-xml-documents-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps 워크플로에 대한 XML 문서의 유효성을 검사하는 스키마 추가

문서가 유효한 XML을 사용하고 예상 데이터를 미리 정의된 형식으로 포함하는지 확인하기 위해 논리 앱 워크플로는 **XML 유효성 검사** 작업과 함께 XML 스키마를 사용할 수 있습니다. XML 스키마는 [XSD(XML 스키마 정의)를](https://www.w3.org/TR/xmlschema11-1/)사용하여 XML로 표현되는 비즈니스 문서를 설명합니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps 및 Enterprise 통합 팩을 통해 B2B 엔터프라이즈 통합 워크플로를](logic-apps-enterprise-integration-overview.md)검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 스키마를 만들려면 다음 도구를 사용할 수 있습니다.

  * Visual Studio 2019 및 [Microsoft Azure Logic Apps Enterprise Integration Tools 확장.](https://aka.ms/vsenterpriseintegrationtools)

  * Visual Studio 2015 및 [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) 확장

   > [!IMPORTANT]
   > BizTalk Server 확장과 함께 확장을 설치하지 마세요. 두 확장이 있으면 예기치 않은 동작이 발생할 수 있습니다. 이러한 확장 중 하나만 설치되어 있는지 확인합니다.

   > [!NOTE]
   > 고해상도 모니터의 경우 Visual Studio의 [맵 디자이너에서 표시 문제](/visualstudio/designers/disable-dpi-awareness)가 발생할 수 있습니다. 이 표시 문제를 해결하려면 [DPI 인식 안 함 모드에서 Visual Studio를 다시 시작](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process)하거나, [DPIUNAWARE 레지스트리 값](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)을 추가합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결됩니다.

  * **XML 유효성 검사** 작업을 사용하려는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 워크플로에서 아티팩트 사용을 위해 통합 계정을 논리 앱 [리소스에 연결해야](logic-apps-enterprise-integration-create-integration-account.md#link-account) 합니다.

    **논리 앱(소비)** 워크플로에서 사용할 스키마를 만들고 추가하려면 논리 앱 리소스가 아직 필요하지 않습니다. 그러나 워크플로에서 이러한 스키마를 사용할 준비가 되면 논리 앱 리소스에 해당 스키마를 저장하는 연결된 통합 계정이 필요합니다.

  * [ **논리 앱(표준)** 리소스 종류](logic-apps-overview.md#resource-type-and-host-environment-differences)를 사용하는 경우 통합 계정에 스키마를 저장하지 않으므로 기존 논리 앱 리소스가 필요합니다. 대신 Azure Portal 또는 Visual Studio Code 사용하여 논리 앱 리소스에 스키마를 직접 추가할 수 있습니다. 그런 다음 *동일한 논리 앱 리소스* 내의 여러 워크플로에서 이러한 스키마를 사용할 수 있습니다.

    [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업을 사용하는 것과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장하려면 통합 계정이 여전히 필요합니다. 그러나 논리 앱 리소스를 통합 계정에 연결할 필요가 없으므로 연결 기능이 존재하지 않습니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

    > [!NOTE]
    > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

* 스키마가 [2MB 이하](#smaller-schema)이면 Azure Portal에서 *직접* 통합 계정에 스키마를 추가할 수 있습니다. 그러나 스키마가 2MB보다 크고 [스키마에 대한 크기 제한](logic-apps-limits-and-config.md#artifact-capacity-limits)보다 작으면 Azure Storage 계정에 스키마를 업로드할 수 있습니다. 통합 계정에 해당 스키마를 추가하려면 통합 계정에서 스토리지 계정으로 연결할 수 있습니다. 이 작업을 위해 필요한 항목은 다음과 같습니다.

    | 항목 | Description |
    |------|-------------|
    | [Azure Storage 계정](../storage/common/storage-account-overview.md) | 이 계정에서 스키마의 Azure blob 컨테이너를 만듭니다. [스토리지 계정을 만드는](../storage/common/storage-account-create.md) 방법을 알아봅니다. |
    | Blob 컨테이너 | 이 컨테이너에 스키마를 업로드할 수 있습니다. 또한 나중에 통합 계정에 스키마를 추가할 때 이 컨테이너의 콘텐츠 URI가 필요합니다. [blob 컨테이너를 만드는](../storage/blobs/storage-quickstart-blobs-portal.md) 방법을 알아봅니다. |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 이 도구를 사용하면 스토리지 계정 및 blob 컨테이너를 더 쉽게 관리할 수 있습니다. Storage Explorer를 사용하려면 다음 단계를 선택합니다. <p>- Azure Portal에서 스토리지 계정을 선택합니다. 스토리지 계정 메뉴에서 **Storage Explorer** 를 선택합니다. <p>- 데스크톱 버전의 경우 [Azure Storage Explorer를 다운로드 및 설치](https://www.storageexplorer.com/)합니다. 그런 다음, [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)의 단계에 따라 Storage Explorer를 스토리지 계정에 연결합니다. 자세한 정보를 알아보려면 [빠른 시작: Azure Storage Explorer를 사용하여 개체 스토리지에 Blob 만들기](../storage/blobs/quickstart-storage-explorer.md)를 참조하세요.  |
    |||

  **논리 앱(소비)** 리소스 종류에 대해 더 큰 스키마를 추가하려면 [Azure Logic Apps REST API 스키마를](/rest/api/logic/schemas/create-or-update)사용할 수도 있습니다. 그러나 **논리 앱(표준)** 리소스 종류의 경우 현재 Azure Logic Apps REST API 사용할 수 없습니다.

## <a name="limits"></a>제한

* **논리 앱(표준)의** 경우 스키마 파일 크기에 대한 제한이 없습니다.

* **논리 앱(소비)의** 경우 통합 계정 및 아티팩트(예: 스키마)에 대한 제한이 있습니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성 정보](logic-apps-limits-and-config.md#integration-account-limits)를 참조하세요.

  일반적으로 워크플로와 함께 통합 계정을 사용하고 XML의 유효성을 검사하려는 경우 해당 계정에 스키마를 추가하거나 업로드합니다. 통합 계정에 없는 스키마를 참조하거나 가져오는 경우 `xsd:redefine` 요소를 사용할 때 다음과 같은 오류가 표시될 수 있습니다.

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  이 오류를 해결하려면 `xsd:redefine` 대신 `xsd:import` 또는 `xsd:include` 요소를 사용하거나 URI를 사용해야 합니다.

<a name="add-schema"></a>

## <a name="add-schemas"></a>스키마 추가

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. 기본 Azure Search 상자에 `integration accounts`을 입력하고, **통합 계정** 을 선택합니다.

1. 스키마를 추가할 통합 계정을 선택합니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **스키마** 를 선택합니다.

1. **스키마** 창 도구 모음에서 **추가** 를 선택합니다.

스키마(.xsd) 파일의 크기에 따라 [2MB보다 작거나](#smaller-schema)[2MB보다 크고](#larger-schema) 8MB보다 작은 스키마를 업로드하는 단계를 따릅니다.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>2MB보다 작은 스키마 추가

1. 스키마 추가 창에서 **스키마의** 이름을 입력합니다. **작은 파일** 을 선택한 상태로 둡니다. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드하려는 스키마를 찾아 선택합니다.

1. 완료되면 **확인** 을 선택합니다.

   스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2MB보다 큰 스키마 추가

더 큰 스키마를 추가하려면 Azure Storage 계정의 Azure blob 컨테이너에 스키마를 업로드할 수 있습니다. Blob 컨테이너에 퍼블릭 읽기 권한 권한이 있는지 여부에 따라, 스키마 추가 단계가 달라집니다. 먼저 다음 [Blob 컨테이너에 대한 퍼블릭 액세스 수준 설정](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container) 단계를 수행하여 Blob 컨테이너에 퍼블릭 읽기 권한이 있는지 확인합니다.

#### <a name="check-container-access-level"></a>컨테이너 액세스 수준 확인

1. Azure Storage Explorer를 엽니다. 탐색기 창에서 Azure 구독을 아직 확장하지 않은 경우 확장합니다.

1. **Storage 계정** > {*your-storage-account*} > **Blob 컨테이너** 를 확장합니다. Blob 컨테이너를 선택합니다.

1. Blob 컨테이너의 바로 가기 메뉴에서 **공용 액세스 수준 설정** 을 선택합니다.

   * Blob 컨테이너에 최소한의 퍼블릭 액세스 권한이 있는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스로 컨테이너 업로드](#public-access)단계를 따릅니다.

     ![퍼블릭 액세스](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Blob 컨테이너에 퍼블릭 액세스 권한이 없는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스 없이 컨테이너 업로드](#public-access)단계를 따릅니다.

     ![공용 액세스 권한 없음](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>공용 액세스 권한이 있는 컨테이너에 업로드

1. 스토리지 계정에 스키마를 업로드합니다. 오른쪽 창에서 **업로드** 를 선택합니다.

1. 업로드가 완료되면 업로드된 스키마를 선택합니다. 도구 모음에서 스키마 URL을 복사할 수 있도록 **URL 복사** 를 선택합니다.

1. **스키마 추가** 창이 열려 있는 Azure Portal로 돌아갑니다. 어셈블리의 이름을 입력합니다. **대용량 파일(2MB 초과)** 을 선택합니다.

   이제 **스키마** 상자가 아닌 **콘텐츠 URI** 상자가 나타납니다.

1. **콘텐츠 URI** 상자에서 스키마의 URL을 붙여 넣습니다. 스키마 추가를 완료합니다.

스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 스키마가 표시됩니다.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>공용 액세스 권한이 없는 컨테이너에 업로드

1. 스토리지 계정에 스키마를 업로드합니다. 오른쪽 창에서 **업로드** 를 선택합니다.

1. 업로드를 마친 후에 스키마의 SAS(공유 액세스 서명)을 생성합니다. 스키마의 바로 가기 메뉴에서 **공유 액세스 서명 가져오기** 를 선택합니다.

1. **공유 액세스 서명** 창에서 **컨테이너 수준 공유 액세스 서명 URI 생성** > **만들기** 를 선택합니다. SAS URL이 생성되면 **URL** 상자 옆에서 **복사** 를 선택합니다.

1. **스키마 추가** 창이 열려 있는 Azure Portal로 돌아갑니다. **큰 파일** 을 선택합니다.

   이제 **스키마** 상자가 아닌 **콘텐츠 URI** 상자가 나타납니다.

1. **콘텐츠 URI** 상자에서 이전에 생성한 SAS URI를 붙여 넣습니다. 스키마 추가를 완료합니다.

스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 스키마가 표시됩니다.

### <a name="standard"></a>[Standard](#tab/standard)

#### <a name="azure-portal"></a>Azure portal

1. 논리 앱 리소스 메뉴의 **설정** 에서 **스키마** 를 선택합니다.

1. **스키마** 창 도구 모음에서 **추가** 를 선택합니다.

1. 스키마 추가 창에서 **스키마의** 고유한 이름을 입력합니다.

1. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 스키마를 선택합니다.

1. 완료되면 **확인** 을 선택합니다.

   스키마 파일 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 스키마가 표시됩니다.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. 논리 앱 프로젝트의 구조에서 **아티팩트** 폴더를 연 다음 **스키마** 폴더를 엽니다.

1. **스키마** 폴더에서 스키마를 추가합니다.

---

<a name="edit-schema"></a>

## <a name="edit-a-schema"></a>스키마 편집

기존 스키마를 업데이트하려면 원하는 변경 내용이 있는 새 스키마 파일을 업로드해야 합니다. 그러나 편집을 위해 기존 스키마를 먼저 다운로드할 수 있습니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택합니다. 먼저 스키마를 다운로드하고 편집하려면 **스키마** 창 도구 모음에서 **다운로드** 를 선택하고 스키마를 저장합니다.

1. 업데이트된 스키마를 업로드할 준비가 되면 **스키마** 페이지에서 업데이트하려는 스키마를 선택합니다. **스키마** 창 도구 모음에서 **업데이트** 를 선택합니다.

1. 업로드하려는 업데이트된 스키마를 찾아 선택합니다.

1. 완료되면 **확인** 을 선택합니다.

   스키마 파일의 업로드가 끝나면 업데이트된 스키마가 **스키마** 목록에 나타납니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. 아직 열려 있지 않은 경우 [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 에서 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택합니다. 먼저 스키마를 다운로드하고 편집하려면 **스키마** 창 도구 모음에서 **다운로드** 를 선택하고 스키마를 저장합니다.

1. **스키마** 창 도구 모음에서 **추가** 를 선택합니다.

1. 스키마 추가 창에서 **스키마의** 고유한 이름을 입력합니다.

1. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 스키마를 선택합니다.

1. 완료되면 **확인** 을 선택합니다.

   스키마 파일의 업로드가 끝나면 업데이트된 스키마가 **스키마** 목록에 나타납니다.

---

<a name="delete-schema"></a>

## <a name="delete-a-schema"></a>스키마 삭제

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택한 다음 **삭제** 를 선택합니다.

1. 스키마를 삭제할지 확인하려면 **예** 를 선택합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. 아직 열려 있지 않은 경우 [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 에서 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택한 다음 **삭제** 를 선택합니다.

1. 스키마를 삭제할지 확인하려면 **예** 를 선택합니다.

---

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps의 워크플로에 대한 XML 유효성 검사](logic-apps-enterprise-integration-xml-validation.md)
* [Azure Logic Apps의 워크플로용 XML 변환](logic-apps-enterprise-integration-transform.md)