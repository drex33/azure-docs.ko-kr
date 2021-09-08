---
title: XML 유효성 검사를 위한 스키마 추가
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 XML 문서 유효성을 검사하는 스키마 추가
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: dc55e70e9ceaa9546890b2ed7dd5df0d705b1a92
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099993"
---
# <a name="add-schemas-to-validate-xml-in-azure-logic-apps"></a>Azure Logic Apps에서 XML의 유효성을 검사하는 스키마 추가

문서가 유효한 XML을 사용하고 있으며 예상된 데이터가 Azure Logic Apps에서 엔터프라이즈 통합 시나리오에 맞는 미리 정의된 형식으로 존재하는지 확인하기 위해 논리 앱은 스키마를 사용할 수 있습니다. 또한 스키마는 논리 앱이 B2B(기업 간) 시나리오에서 교환하는 메시지가 유효한지도 확인할 수 있습니다.

논리 앱을 처음 사용하는 경우 다음 문서를 참조하세요.

* [Azure Logic Apps란? - 리소스 유형 및 호스트 환경](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [단일 테넌트 Azure Logic Apps(표준)를 사용하여 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

* [단일 테넌트 논리 앱 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

* [Azure Logic Apps의 사용량 계량, 청구, 가격 책정 모델](logic-apps-pricing.md)

## <a name="limits"></a>제한

* **표준** 논리 앱 리소스의 경우 스키마 파일 크기에 대한 제한이 없습니다.

* **소비** 논리 앱 리소스의 경우 통합 계정 및 스키마와 같은 아티팩트에 대한 제한이 있습니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성 정보](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 참조하세요.

  일반적으로 워크플로와 함께 통합 계정을 사용하고 XML의 유효성을 검사하려는 경우 해당 계정에 스키마를 추가하거나 업로드합니다. 통합 계정에 없는 스키마를 참조하거나 가져오는 경우 `xsd:redefine` 요소를 사용할 때 다음과 같은 오류가 표시될 수 있습니다.

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  이 오류를 해결하려면 `xsd:redefine` 대신 `xsd:import` 또는 `xsd:include` 요소를 사용하거나 URI를 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* **Logic App(표준)** 리소스 종류를 사용하는 경우 통합 계정이 필요하지 않습니다. 대신 Azure Portal 또는 Visual Studio Code에서 논리 앱 리소스에 스키마를 직접 추가할 수 있습니다. 그런 다음 *동일한 논리 앱 리소스* 내의 여러 워크플로에서 이러한 스키마를 사용할 수 있습니다.

* **Logic App(소비)** 리소스 종류를 사용하는 경우 엔터프라이즈 통합 및 B2B 솔루션에서 사용할 스키마 및 기타 아티팩트를 저장할 수 있는 [통합 계정 리소스](logic-apps-enterprise-integration-create-integration-account.md)가 있어야 합니다. 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결됩니다.

  * XML 유효성 검사 작업을 사용하려는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * 스키마를 사용하려는 경우 논리 앱 리소스에 [연결](logic-apps-enterprise-integration-create-integration-account.md#link-account)됩니다.

    소비 논리 앱 워크플로에서 사용할 스키마를 만들고 추가하려면 아직 논리 앱 리소스가 필요하지 않습니다. 그러나 워크플로에서 이러한 스키마를 사용할 준비가 되면 논리 앱 리소스에 해당 스키마를 저장하는 연결된 통합 계정이 필요합니다.

* 스키마가 [2MB 이하](#smaller-schema)이면 Azure Portal에서 *직접* 통합 계정에 스키마를 추가할 수 있습니다. 그러나 스키마가 2MB보다 크고 [스키마에 대한 크기 제한](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)보다 작으면 Azure Storage 계정에 스키마를 업로드할 수 있습니다. 통합 계정에 해당 스키마를 추가하려면 통합 계정에서 스토리지 계정으로 연결할 수 있습니다. 이 작업을 위해 필요한 항목은 다음과 같습니다.

    | 항목 | Description |
    |------|-------------|
    | [Azure Storage 계정](../storage/common/storage-account-overview.md) | 이 계정에서 스키마의 Azure blob 컨테이너를 만듭니다. [스토리지 계정을 만드는](../storage/common/storage-account-create.md) 방법을 알아봅니다. |
    | Blob 컨테이너 | 이 컨테이너에 스키마를 업로드할 수 있습니다. 또한 나중에 통합 계정에 스키마를 추가할 때 이 컨테이너의 콘텐츠 URI가 필요합니다. [blob 컨테이너를 만드는](../storage/blobs/storage-quickstart-blobs-portal.md) 방법을 알아봅니다. |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 이 도구를 사용하면 스토리지 계정 및 blob 컨테이너를 더 쉽게 관리할 수 있습니다. Storage Explorer를 사용하려면 다음 단계를 선택합니다. <p>- Azure Portal에서 스토리지 계정을 선택합니다. 스토리지 계정 메뉴에서 **Storage Explorer** 를 선택합니다. <p>- 데스크톱 버전의 경우 [Azure Storage Explorer를 다운로드 및 설치](https://www.storageexplorer.com/)합니다. 그런 다음, [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)의 단계에 따라 Storage Explorer를 스토리지 계정에 연결합니다. 자세한 정보를 알아보려면 [빠른 시작: Azure Storage Explorer를 사용하여 개체 스토리지에 Blob 만들기](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)를 참조하세요.  |
    |||

  소비 논리 앱 리소스에 대한 더 큰 지도를 추가하려면 [Azure Logic Apps REST API - 스키마](/rest/api/logic/schemas/create-or-update)를 사용할 수도 있습니다. 그러나 표준 논리 앱 리소스의 경우 현재 Azure Logic Apps REST API를 사용할 수 없습니다.

<a name="add-schemas"></a>

## <a name="add-schemas"></a>스키마 추가

### <a name="consumption-resource"></a>[소비 리소스](#tab/consumption-1)

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. 기본 Azure Search 상자에 `integration accounts`을 입력하고, **통합 계정** 을 선택합니다.

1. 스키마를 추가할 통합 계정을 선택합니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **스키마** 를 선택합니다.

1. **스키마** 창 도구 모음에서 **추가** 를 선택합니다.

스키마(.xsd) 파일의 크기에 따라 [2MB보다 작거나](#smaller-schema)[2MB보다 크고](#larger-schema) 8MB보다 작은 스키마를 업로드하는 단계를 따릅니다.

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>2MB보다 작은 스키마 추가

1. **스키마 추가** 아래에 스키마의 이름을 입력합니다. **작은 파일** 을 선택한 상태로 둡니다. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드하려는 스키마를 찾아 선택합니다.

1. 준비가 되면 **확인** 을 선택합니다.

   스키마 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>2MB보다 큰 스키마 추가

더 큰 스키마를 추가하려면 Azure Storage 계정의 Azure blob 컨테이너에 스키마를 업로드할 수 있습니다. Blob 컨테이너에 퍼블릭 읽기 권한 권한이 있는지 여부에 따라, 스키마 추가 단계가 달라집니다. 먼저 다음 [Blob 컨테이너에 대한 퍼블릭 액세스 수준 설정](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container) 단계를 수행하여 Blob 컨테이너에 퍼블릭 읽기 권한이 있는지 확인합니다.

#### <a name="check-container-access-level"></a>컨테이너 액세스 수준 확인

1. Azure Storage Explorer를 엽니다. 탐색기 창에서 Azure 구독을 아직 확장하지 않은 경우 확장합니다.

1. **Storage 계정** > {*your-storage-account*} > **Blob 컨테이너** 를 확장합니다. Blob 컨테이너를 선택합니다.

1. Blob 컨테이너의 바로 가기 메뉴에서 **공용 액세스 수준 설정** 을 선택합니다.

   * Blob 컨테이너에 최소한의 공용 액세스 권한이 있는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스로 컨테이너 업로드](#public-access) 단계를 따릅니다.

     ![퍼블릭 액세스](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Blob 컨테이너에 공용 액세스 권한이 없는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스 없이 컨테이너 업로드](#public-access) 단계를 따릅니다.

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

### <a name="standard-resource"></a>[표준 리소스](#tab/standard-1)

#### <a name="azure-portal"></a>Azure portal

1. 논리 앱 리소스 메뉴의 **설정** 에서 **스키마** 를 선택합니다.

1. **스키마** 창 도구 모음에서 **추가** 를 선택합니다.

1. **스키마 추가** 아래에 스키마의 고유한 이름을 입력합니다.

1. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 스키마를 선택합니다.

1. 준비가 되면 **확인** 을 선택합니다.

   스키마 파일 업로드가 완료되면 **스키마** 목록에 스키마가 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 스키마가 표시됩니다.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. 논리 앱 프로젝트의 구조에서 **아티팩트** 폴더를 연 다음 **스키마** 폴더를 엽니다.

1. **스키마** 폴더에서 스키마를 추가합니다.

---

## <a name="edit-schemas"></a>스키마 편집

기존 스키마를 업데이트하려면 원하는 변경 내용이 있는 새 스키마 파일을 업로드해야 합니다. 그러나 편집을 위해 기존 스키마를 먼저 다운로드할 수 있습니다.

### <a name="consumption-resource"></a>[소비 리소스](#tab/consumption-2)

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택합니다. 먼저 스키마를 다운로드하고 편집하려면 **스키마** 창 도구 모음에서 **다운로드** 를 선택하고 스키마를 저장합니다.

1. 업데이트된 스키마를 업로드할 준비가 되면 **스키마** 페이지에서 업데이트하려는 스키마를 선택합니다. **스키마** 창 도구 모음에서 **업데이트** 를 선택합니다.

1. 업로드하려는 업데이트된 스키마를 찾아 선택합니다.

   스키마 파일의 업로드가 끝나면 업데이트된 스키마가 **스키마** 목록에 나타납니다.

### <a name="standard-resource"></a>[표준 리소스](#tab/standard-2)

1. 아직 열려 있지 않은 경우 [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 에서 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택합니다. 먼저 스키마를 다운로드하고 편집하려면 **스키마** 창 도구 모음에서 **다운로드** 를 선택하고 스키마를 저장합니다.

1. **스키마** 창 도구 모음에서 **추가** 를 선택합니다.

1. **스키마 추가** 아래에 스키마의 고유한 이름을 입력합니다.

1. **스키마** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 스키마를 선택합니다.

1. 준비가 되면 **확인** 을 선택합니다.

   스키마 파일의 업로드가 끝나면 업데이트된 스키마가 **스키마** 목록에 나타납니다.

---

## <a name="delete-schemas"></a>스키마 삭제

### <a name="consumption-resource"></a>[소비 리소스](#tab/consumption-3)

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택한 다음 **삭제** 를 선택합니다.

1. 스키마를 삭제할지 확인하려면 **예** 를 선택합니다.

### <a name="standard-resource"></a>[표준 리소스](#tab/standard-3)

1. 아직 열려 있지 않은 경우 [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 에서 **스키마** 를 선택합니다.

1. **스키마** 창이 열리면 스키마를 선택한 다음 **삭제** 를 선택합니다.

1. 스키마를 삭제할지 확인하려면 **예** 를 선택합니다.

---

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps의 워크플로에 대한 XML 유효성 검사](logic-apps-enterprise-integration-xml-validation.md)
* [Azure Logic Apps의 워크플로용 XML 변환](logic-apps-enterprise-integration-transform.md)