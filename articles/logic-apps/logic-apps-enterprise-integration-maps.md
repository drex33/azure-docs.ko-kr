---
title: XSLT 맵으로 XML 변환
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 XML을 변환하는 XSLT 맵 추가
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 86e7c07ba3ade77ec3913178a8dc24bb135c0a54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528608"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 맵을 사용하여 XML 변환

Azure Logic Apps에서 엔터프라이즈 통합 시나리오의 형식 간에 XML 데이터를 전송하기 위해 논리 앱을 맵, 구체적으로 말하면, XSLT(Extensible Stylesheet Language Transformation) 맵을 사용할 수 있습니다. 맵은 XML 문서의 데이터를 다른 형식으로 변환하는 방법을 설명하는 XML 문서입니다.

예를 들어, YYYMMDD 날짜 형식을 사용하는 고객에게서 정기적으로 B2B 주문 또는 송장을 받는 경우를 가정해 보겠습니다. 그러나 조직은 MMDDYYY 날짜 형식을 사용합니다. 고객 작업 데이터베이스에서 주문 또는 송장 세부 정보를 저장하기 전에 YYYMMDD 날짜 형식을 MMDDYYY 형식으로 변환하는 맵을 정의하여 사용할 수 있습니다.

> [!NOTE]
> Azure Logic Apps 서비스는 XML 변환 처리를 위해 유한한 메모리를 할당합니다. **논리 앱(사용량)** 리소스 종류를 기반으로 논리 앱을 만들고, 맵 또는 페이로드 변환의 메모리 사용량이 높은 경우 이러한 변환이 실패하여 메모리 오류가 발생할 수 있습니다. 이 시나리오를 방지하려면 다음 옵션을 고려하세요.
>
> * 맵 또는 페이로드를 편집하여 메모리 사용량을 줄입니다.
>
> * **논리 앱(표준)** 리소스 유형을 대신 사용하여 논리 앱을 만듭니다.
>
>   이러한 워크플로는 컴퓨팅 및 메모리 리소스에 대한 전용 및 유연한 옵션을 제공하는 단일 테넌트 Azure Logic Apps에서 실행됩니다. 자세한 내용은 다음 설명서를 검토하세요.
>
>   * [Azure Logic Apps란? - 리소스 유형 및 호스트 환경](logic-apps-overview.md#resource-type-and-host-environment-differences)
>   * [단일 테넌트 Azure Logic Apps(표준)를 사용하여 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)
>   * [Azure Logic Apps의 단일 테넌트 및 다중 테넌트와 통합 서비스 환경](single-tenant-overview-compare.md)
>   * [Azure Logic Apps의 사용량 계량, 청구 및 가격 책정 모델](logic-apps-pricing.md)

통합 계정 및 아티팩트(예: 맵)와 관련된 제한에 대해서는 [Azure Logic Apps에 대한 제한 및 구성 정보](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md): 엔터프라이즈 통합 및 B2B(기업 간) 솔루션을 위한 맵 및 기타 아티팩트를 저장합니다.

* 맵이 외부 어셈블리를 참조하는 경우 64비트 어셈블리가 필요합니다. 변환 서비스는 64비트 프로세스를 실행하므로 32비트 어셈블리는 지원되지 않습니다. 32비트 어셈블리에 대한 소스 코드가 있는 경우 코드를 64비트 어셈블리로 다시 컴파일합니다. 소스 코드가 없지만 타사 공급자에서 이진 파일을 가져온 경우 해당 공급자에서 64비트 버전을 가져옵니다. 예를 들어 일부 공급자는 32비트 및 64비트 버전이 모두 있는 패키지에 어셈블리를 제공합니다. 옵션이 있는 경우 64비트 버전을 대신 사용합니다.

* 맵이 외부 어셈블리를 참조하는 경우 *어셈블리 및 맵을 둘 다* 통합 계정에 업로드해야 합니다. [*어셈블리를 먼저 업로드*](#add-assembly)한 후 해당 어셈블리를 참조하는 맵을 업로드해야 합니다.

  어셈블리가 *2MB 이하* 이면 Azure Portal에서 직접 통합 계정에 어셈블리를 추가할 수 있습니다. 그러나 어셈블리 또는 맵이 2MB보다는 크지만 [어셈블리 또는 맵의 크기 제한](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)보다 크지 않으면 다음 옵션을 사용할 수 있습니다.

  * 어셈블리의 경우 어셈블리를 업로드할 수 있는 Azure blob 컨테이너와 해당 컨테이너의 위치가 필요합니다. 이러한 방식으로 나중에 통합 계정에 어셈블리를 추가할 때 해당 위치를 지정할 수 있습니다. 이 작업에는 다음과 같은 항목이 필요합니다.

    | 항목 | Description |
    |------|-------------|
    | [Azure Storage 계정](../storage/common/storage-account-overview.md) | 이 계정에서 어셈블리의 Azure blob 컨테이너를 만듭니다. [스토리지 계정을 만드는](../storage/common/storage-account-create.md) 방법을 알아봅니다. |
    | Blob 컨테이너 | 이 컨테이너에 어셈블리를 업로드할 수 있습니다. 통합 계정에 어셈블리를 추가할 때도 이 컨테이너의 위치가 필요합니다. [blob 컨테이너를 만드는](../storage/blobs/storage-quickstart-blobs-portal.md) 방법을 알아봅니다. |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 이 도구를 사용하면 스토리지 계정 및 blob 컨테이너를 더 쉽게 관리할 수 있습니다. Storage Explorer를 사용하려면 [Azure Storage Explorer를 다운로드하고 설치](https://www.storageexplorer.com/)합니다. 그런 다음, [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)의 단계에 따라 Storage Explorer를 스토리지 계정에 연결합니다. 자세한 정보를 알아보려면 [빠른 시작: Azure Storage Explorer를 사용하여 개체 스토리지에 Blob 만들기](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)를 참조하세요. <p>또는 Azure Portal에서 스토리지 계정을 선택합니다. 스토리지 계정 메뉴에서 **Storage Explorer** 를 선택합니다. |
    |||

  * 맵의 경우 현재 [Azure Logic Apps REST API - 맵](/rest/api/logic/maps/createorupdate)을 사용하여 더 큰 맵을 추가할 수 있습니다.

맵을 만들고 추가할 때 논리 앱은 필요하지 않습니다. 그러나 맵을 사용하려면 논리 앱에서 해당 맵을 저장하는 통합 계정에 연결해야 합니다. [논리 앱을 통합 계정에 연결하는 방법](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)을 알아봅니다. 논리 앱이 아직 없는 경우 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 알아봅니다.

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>참조된 어셈블리 추가

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. 기본 Azure 검색 상자에 `integration accounts`를 입력하고, **통합 계정** 을 선택합니다.

1. 어셈블리를 추가할 통합 계정을 선택합니다. 예를 들면 다음과 같습니다.

1. 통합 계정 메뉴에서 **개요** 를 선택합니다. **설정** 에서 **어셈블리** 를 선택합니다.

1. **어셈블리** 창 도구 모음에서 **추가** 를 선택합니다.

어셈블리 파일의 크기에 따라 [2MB보다 작거나](#smaller-assembly)[2MB보다 크고](#larger-assembly) 8MB보다 작은 어셈블리를 업로드하는 단계를 따릅니다. 통합 계정의 어셈블리 수량 제한에 대해서는 [Azure Logic Apps의 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)을 참조하세요.

> [!NOTE]
> 어셈블리를 변경하는 경우, 맵에 변경 내용이 있는지 여부에 관계없이 맵도 업데이트해야 합니다.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>최대 2MB의 어셈블리 추가

1. **어셈블리 추가** 에서 어셈블리의 이름을 입력합니다. **작은 파일** 을 선택한 상태로 둡니다. **어셈블리** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드하려는 어셈블리를 찾아 선택합니다.

   어셈블리를 선택한 후 **어셈블리 이름** 속성에 어셈블리의 파일 이름이 자동으로 표시됩니다.

1. 준비가 되면 **확인** 을 선택합니다.

   어셈블리 파일 업로드가 완료되면 어셈블리가 **어셈블리** 목록에 나타납니다. 통합 계정의 **개요** 창에 있는 **아티팩트** 에서 업로드한 어셈블리가 표시됩니다.

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>2MB보다 큰 어셈블리 추가

더 큰 어셈블리를 추가하려면 Azure Storage 계정의 Azure blob 컨테이너에 어셈블리를 업로드할 수 있습니다. Blob 컨테이너에 공용 읽기 액세스 권한이 있는지 여부에 따라, 어셈블리 추가 단계가 달라집니다. 먼저 다음 단계를 수행하여 BLOB 컨테이너에 공용 읽기 액세스 권한이 있는지 확인합니다. [BLOB 컨테이너에 대한 공용 액세스 수준 설정](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>컨테이너 액세스 수준 확인

1. Azure Storage Explorer를 엽니다. 탐색기 창에서 Azure 구독을 아직 확장하지 않은 경우 확장합니다.

1. **Storage 계정** > {*your-storage-account*} > **Blob 컨테이너** 를 확장합니다. Blob 컨테이너를 선택합니다.

1. Blob 컨테이너의 바로 가기 메뉴에서 **공용 액세스 수준 설정** 을 선택합니다.

   * Blob 컨테이너에 최소한의 퍼블릭 액세스 권한이 있는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스로 컨테이너 업로드](#public-access-assemblies)단계를 따릅니다.

     ![퍼블릭 액세스](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Blob 컨테이너에 퍼블릭 액세스 권한이 없는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스 없이 컨테이너 업로드](#no-public-access-assemblies)단계를 따릅니다.

     ![공용 액세스 권한 없음](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>공용 액세스 권한이 있는 컨테이너에 업로드

1. 스토리지 계정에 어셈블리를 업로드합니다. 오른쪽 창에서 **업로드** 를 선택합니다.

1. 업로드가 완료되면 업로드된 어셈블리를 선택합니다. 도구 모음에서 어셈블리 URL을 복사할 수 있도록 **URL 복사** 를 선택합니다.

1. **어셈블리 추가** 창이 열려 있는 Azure Portal로 돌아갑니다. 어셈블리의 이름을 입력합니다. **대용량 파일(2MB 초과)** 을 선택합니다.

   이제 **어셈블리** 상자가 아닌 **콘텐츠 URI** 상자가 나타납니다.

1. **콘텐츠 URI** 상자에서 어셈블리의 URL을 붙여 넣습니다. 어셈블리 추가를 완료합니다.

   어셈블리 업로드가 완료되면 스키마가 **어셈블리** 목록에 나타납니다. 통합 계정의 **개요** 창에 있는 **아티팩트** 에서 업로드한 어셈블리가 표시됩니다.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>공용 액세스 권한이 없는 컨테이너에 업로드

1. 스토리지 계정에 어셈블리를 업로드합니다. 오른쪽 창에서 **업로드** 를 선택합니다.

1. 업로드를 마친 후에 어셈블리의 SAS(공유 액세스 서명)을 생성합니다. 어셈블리의 바로 가기 메뉴에서 **공유 액세스 서명 가져오기** 를 선택합니다.

1. **공유 액세스 서명** 창에서 **컨테이너 수준 공유 액세스 서명 URI 생성** > **만들기** 를 선택합니다. SAS URL이 생성되면 **URL** 상자 옆에서 **복사** 를 선택합니다.

1. **어셈블리 추가** 창이 열려 있는 Azure Portal로 돌아갑니다. 어셈블리의 이름을 입력합니다. **대용량 파일(2MB 초과)** 을 선택합니다.

   이제 **어셈블리** 상자가 아닌 **콘텐츠 URI** 상자가 나타납니다.

1. **콘텐츠 URI** 상자에서 이전에 생성한 SAS URI를 붙여넣습니다. 어셈블리 추가를 완료합니다.

어셈블리 업로드가 완료되면 어셈블리가 **스키마** 목록에 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 어셈블리가 표시됩니다.

## <a name="create-maps"></a>맵 만들기

맵으로 사용할 수 있는 XSLT(Extensible Stylesheet Language Transformation) 문서를 만들려면 [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md)을 사용하여 프로젝트를 만들기 위해 Visual Studio 2015 또는 2019를 사용할 수 있습니다. 이 프로젝트에서 두 개의 XML 스키마 파일 간에 항목을 시각적으로 매핑할 수 있는 통합 맵 파일을 빌드할 수 있습니다. 이 프로젝트를 빌드하면 XSLT 문서가 생성됩니다. 통합 계정의 맵 수량 제한에 대해서는 [Azure Logic Apps의 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)을 참조하세요.

## <a name="add-maps"></a>맵 추가

맵이 참조하는 모든 어셈블리를 업로드한 후에는 맵을 업로드할 수 있습니다.

1. 아직 로그인하지 않은 경우 Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 통합 계정이 아직 열리지 않은 경우 기본 Azure 검색 상자에서 `integration accounts`를 입력하고 **통합 계정** 을 선택합니다.

1. 맵을 추가할 통합 계정을 선택합니다.

1. 통합 계정 메뉴에서 **개요** 를 선택합니다. **설정** 아래에서 **맵** 을 선택합니다.

1. **맵** 창 도구 모음에서 **추가** 를 선택합니다.

1. [최대 2MB](#smaller-map) 또는 [2MB 초과](#larger-map) 맵을 추가합니다.

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>최대 2MB의 맵 추가

1. **맵 추가** 에서 맵의 고유한 이름을 입력합니다.

1. **맵 유형** 아래에서 유형(예: **액체**, **XSLT**, **XSLT 2.0** 또는 **XSLT 3.0**)을 선택합니다.

1. **맵** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드하려는 맵을 찾아 선택합니다. 예를 들면 다음과 같습니다.

   **이름** 속성을 비워 두면 맵 파일을 선택한 후에 해당 속성에 맵의 파일 이름이 표시됩니다.

1. 준비가 되면 **확인** 을 선택합니다.

   맵 파일 업로드가 완료되면 **맵** 목록에 해당 맵이 나타납니다.

   통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 맵이 표시됩니다.

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>2MB보다 큰 맵 추가

현재, 더 큰 맵을 추가하려면 [Azure Logic Apps REST API - 맵](/rest/api/logic/maps/createorupdate)을 사용합니다.

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-side window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>맵 편집

기존 맵을 업데이트하려면 원하는 변경 내용이 있는 새 맵 파일을 업로드해야 합니다. 그러나 편집을 위해 기존 맵을 먼저 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **맵** 을 선택합니다.

1. **맵** 창이 열리면 맵을 선택합니다. 맵을 먼저 다운로드하고 편집하려면 **맵** 창 도구 모음에서 **다운로드** 를 선택하고 맵을 저장합니다.

1. 업데이트된 맵을 업로드할 준비가 되면 **맵** 창에서 업데이트하려는 맵을 선택합니다. **맵** 창 도구 모음에서 **업데이트** 를 선택합니다.

1. 업로드하려는 업데이트된 맵을 찾아 선택합니다.

   맵 파일 업로드가 완료되면 업데이트된 **맵** 목록에 해당 맵이 나타납니다.

## <a name="delete-maps"></a>맵 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **맵** 을 선택합니다.

1. **맵** 창이 열리면 맵을 선택하고 **삭제** 를 선택합니다.

1. 맵을 삭제할지 확인하려면 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [스키마에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [변환에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-transform.md)
