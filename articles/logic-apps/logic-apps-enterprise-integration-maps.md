---
title: XML 변환을 위한 XSLT 맵 추가
description: XSLT 맵을 만들고 추가하여 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 XML 변환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: c597a7d44b620ee33acec028812aa2d1651283ff
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100455"
---
# <a name="add-xslt-maps-for-xml-transformation-in-azure-logic-apps"></a>Azure Logic Apps에서 XML 변환을 위한 XSLT 맵 추가

Azure Logic Apps에서 엔터프라이즈 통합 시나리오의 형식 간에 XML 데이터를 전송하기 위해 논리 앱 리소스는 맵, 구체적으로 말하면, XSLT(Extensible Stylesheet Language Transformation) 맵을 사용할 수 있습니다. 맵은 XML 문서의 데이터를 다른 형식으로 변환하는 방법을 설명하는 XML 문서입니다.

예를 들어, YearMonthDay 날짜 형식(YYYMMDD)을 사용하는 고객에게서 정기적으로 B2B 주문 또는 송장을 받는 경우를 가정해 보겠습니다. 그러나 사용자의 조직은 MonthDayYear 날짜 형식(MMDDYYY)을 사용합니다. 고객 작업 데이터베이스에서 주문 또는 송장 세부 정보를 저장하기 전에 YYYMMDD 형식을 MMDDYYY 형식으로 변환하는 맵을 정의하여 사용할 수 있습니다.

맵을 사용하면 한 문서에서 다른 문서로 이름과 주소를 복사하는 것과 같은 간단한 변환을 정의할 수 있습니다. 또는 기본 맵 작업을 사용하여 더 복잡한 변환을 만들 수 있습니다.

> [!NOTE]
> Azure Logic Apps는 XML 변환을 처리하기 위해 유한 메모리를 할당합니다. **논리 앱(사용량)** 리소스 종류를 기반으로 논리 앱을 만들고, 맵 또는 페이로드 변환의 메모리 사용량이 높은 경우 이러한 변환이 실패하여 메모리 오류가 발생할 수 있습니다. 이 시나리오를 방지하려면 다음 옵션을 고려하세요.
>
> * 맵 또는 페이로드를 편집하여 메모리 사용량을 줄입니다.
>
> * **논리 앱(표준)** 리소스 유형을 대신 사용하여 논리 앱을 만듭니다.
>
>   이러한 워크플로는 컴퓨팅 및 메모리 리소스에 대한 전용 및 유연한 옵션을 제공하는 단일 테넌트 Azure Logic Apps에서 실행됩니다. 
>   그러나 표준 논리 앱 리소스 종류는 현재 맵에서 외부 어셈블리 참조를 지원하지 않습니다. 또한 현재 XSLT(Extensible Stylesheet Language Transformation) 1.0만 지원됩니다.

논리 앱을 처음 사용하는 경우 다음 문서를 참조하세요.

* [Azure Logic Apps란? - 리소스 유형 및 호스트 환경](logic-apps-overview.md#resource-type-and-host-environment-differences)

* [단일 테넌트 Azure Logic Apps(표준)를 사용하여 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

* [단일 테넌트 논리 앱 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

* [Azure Logic Apps의 사용량 계량, 청구 및 가격 책정 모델](logic-apps-pricing.md)

## <a name="limits"></a>제한

* **표준** 논리 앱 리소스의 경우 맵 파일 크기에 대한 제한이 없습니다.

* **소비** 논리 앱 리소스의 경우 통합 계정 및 맵과 같은 아티팩트에 대한 제한이 있습니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성 정보](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* **Logic App(표준)** 리소스 종류를 사용하는 경우 통합 계정이 필요하지 않습니다. 대신 Azure Portal 또는 Visual Studio Code에서 논리 앱 리소스에 직접 맵을 추가할 수 있습니다. 현재 XSLT 1.0만 지원됩니다. 그런 다음 *동일한 논리 앱 리소스* 내의 여러 워크플로에서 이러한 맵을 사용할 수 있습니다.

* **Logic App(소비)** 리소스 종류를 사용하는 경우 엔터프라이즈 통합 및 B2B 솔루션에서 사용할 맵 및 기타 아티팩트를 저장할 수 있는 [통합 계정 리소스](logic-apps-enterprise-integration-create-integration-account.md)가 있어야 합니다. 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결됩니다.

  * **XML 변환** 작업을 사용하려는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * 맵을 사용하려는 논리 앱 리소스에 [연결](logic-apps-enterprise-integration-create-integration-account.md#link-account)됩니다.

    소비 논리 앱 워크플로에서 사용할 맵을 만들고 추가하려면 아직 논리 앱 리소스가 필요하지 않습니다. 그러나 워크플로에서 해당 맵을 사용할 준비가 되면 논리 앱 리소스에 해당 맵을 저장하는 연결된 통합 계정이 필요합니다.

* **논리 앱(소비)** 은 맵에서 외부 어셈블리 참조를 지원하지만 **논리 앱(표준)** 은 현재 이 기능을 지원하지 않습니다. 어셈블리를 참조하면 XSLT 맵에서 사용자 지정 .NET 코드로 직접 호출할 수 있습니다.

  * 64비트 어셈블리가 필요합니다. 변환 서비스는 64비트 프로세스를 실행하므로 32비트 어셈블리는 지원되지 않습니다. 32비트 어셈블리에 대한 소스 코드가 있는 경우 코드를 64비트 어셈블리로 다시 컴파일합니다. 소스 코드가 없지만 타사 공급자에서 이진 파일을 가져온 경우 해당 공급자에서 64비트 버전을 가져옵니다. 예를 들어 일부 공급자는 32비트 및 64비트 버전이 모두 있는 패키지에 어셈블리를 제공합니다. 옵션이 있는 경우 64비트 버전을 대신 사용합니다.

  * 특정 순서로 *어셈블리와 맵 모두* 를 통합 계정에 업로드해야 합니다. [*어셈블리를 먼저 업로드*](#add-assembly)한 후 해당 어셈블리를 참조하는 맵을 업로드해야 합니다.

  * 어셈블리가 [2MB 이하](#smaller-map)이면 Azure Portal에서 *직접* 통합 계정에 어셈블리 및 맵을 추가할 수 있습니다. 그러나 어셈블리 또는 맵이 2MB보다 크지만 [어셈블리 또는 맵의 크기 제한](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)보다 크지 않은 경우 어셈블리와 해당 컨테이너의 위치를 업로드할 수 있는 Azure Blob 컨테이너를 사용할 수 있습니다. 이러한 방식으로 나중에 통합 계정에 어셈블리를 추가할 때 해당 위치를 지정할 수 있습니다. 이 작업에는 다음과 같은 항목이 필요합니다.

    | 항목 | Description |
    |------|-------------|
    | [Azure Storage 계정](../storage/common/storage-account-overview.md) | 이 계정에서 어셈블리의 Azure blob 컨테이너를 만듭니다. [스토리지 계정을 만드는](../storage/common/storage-account-create.md) 방법을 알아봅니다. |
    | Blob 컨테이너 | 이 컨테이너에 어셈블리를 업로드할 수 있습니다. 통합 계정에 어셈블리를 추가할 때도 이 컨테이너의 콘텐츠 URI 위치가 필요합니다. [blob 컨테이너를 만드는](../storage/blobs/storage-quickstart-blobs-portal.md) 방법을 알아봅니다. |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 이 도구를 사용하면 스토리지 계정 및 blob 컨테이너를 더 쉽게 관리할 수 있습니다. Storage Explorer를 사용하려면 [Azure Storage Explorer를 다운로드하고 설치](https://www.storageexplorer.com/)합니다. 그런 다음, [Storage Explorer 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)의 단계에 따라 Storage Explorer를 스토리지 계정에 연결합니다. 자세한 정보를 알아보려면 [빠른 시작: Azure Storage Explorer를 사용하여 개체 스토리지에 Blob 만들기](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)를 참조하세요. <p>또는 Azure Portal에서 스토리지 계정을 선택합니다. 스토리지 계정 메뉴에서 **Storage Explorer** 를 선택합니다. |
    |||

  * 소비 논리 앱 리소스에 대한 더 큰 지도를 추가하려면 [Azure Logic Apps REST API - 맵](/rest/api/logic/maps/createorupdate)을 사용할 수도 있습니다. 그러나 표준 논리 앱 리소스의 경우 현재 Azure Logic Apps REST API를 사용할 수 없습니다.

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies-consumption-resource-only"></a>참조된 어셈블리 추가(소비 리소스만 해당)

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. 기본 Azure 검색 상자에 `integration accounts`를 입력하고, **통합 계정** 을 선택합니다.

1. 어셈블리를 추가할 통합 계정을 선택합니다.

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

     ![퍼블릭 액세스](media/logic-apps-enterprise-integration-maps/azure-blob-container-public-access.png)

   * Blob 컨테이너에 퍼블릭 액세스 권한이 없는 경우 **취소** 를 선택하고 이 페이지 뒷부분에 나오는 다음 [퍼블릭 액세스 없이 컨테이너 업로드](#no-public-access-assemblies)단계를 따릅니다.

     ![공용 액세스 권한 없음](media/logic-apps-enterprise-integration-maps/azure-blob-container-no-public-access.png)

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

<a name="create-maps"></a>

## <a name="create-maps"></a>맵 만들기

맵으로 사용할 수 있는 XSLT(Extensible Stylesheet Language Transformation) 문서를 만들려면 [엔터프라이즈 통합 SDK](https://aka.ms/vsmapsandschemas)를 사용하여 프로젝트를 만들기 위해 Visual Studio 2015 또는 2019를 사용할 수 있습니다. 이 프로젝트에서 두 개의 XML 스키마 파일 간에 항목을 시각적으로 매핑할 수 있는 통합 맵 파일을 빌드할 수 있습니다. 이 프로젝트를 빌드하면 XSLT 문서가 생성됩니다. 통합 계정의 맵 수량 제한에 대해서는 [Azure Logic Apps의 제한 및 구성](logic-apps-limits-and-config.md#artifact-number-limits)을 참조하세요.

맵에는 다음 특성과 어셈블리 코드 호출이 포함된 `CDATA` 섹션이 있어야 합니다.

* `name`은 사용자 지정 어셈블리 이름입니다.

* `namespace`는 사용자 지정 코드가 포함된 어셈블리의 네임스페이스입니다.

다음 예에서는 `XslUtilitiesLib`라는 어셈블리를 참조하고 어셈블리에서 `circumference` 메서드를 호출하는 맵을 보여 줍니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
<msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
</msxsl:script>
<xsl:template match="data">
<circles>
    <xsl:for-each select="circle">
        <circle>
            <xsl:copy-of select="node()"/>
                <circumference>
                    <xsl:value-of select="user:circumference(radius)"/>
                </circumference>
        </circle>
    </xsl:for-each>
</circles>
</xsl:template>
</xsl:stylesheet>
```

## <a name="tools-and-capabilities-for-maps"></a>맵용 도구 및 기능

* Visual Studio와 [엔터프라이즈 통합 SDK](https://aka.ms/vsmapsandschemas)를 사용하여 맵을 만들 때 맵의 그래픽 표현을 사용하게 되며, 이 맵은 만든 모든 관계와 링크를 보여 줍니다.

* 스키마 간에 데이터를 직접 복사할 수 있습니다. Visual Studio용 [엔터프라이즈 통합 SDK](https://aka.ms/vsmapsandschemas)에는 원본 XML 스키마의 요소를 대상 XML 스키마의 해당 요소와 연결하는 선을 그리는 것처럼 간단하게 이 작업을 수행할 수 있는 매퍼가 포함되어 있습니다.

* 문자열 함수, 날짜 시간 함수 등을 포함하여 여러 맵에 대한 연산 또는 함수를 사용할 수 있습니다.  

* 샘플 XML 메시지를 추가하기 위해 맵 테스트 기능을 사용할 수 있습니다. 클릭 한 번으로 만든 맵을 테스트하고 생성된 출력을 검토할 수 있습니다.

## <a name="add-maps"></a>맵 추가

### <a name="consumption-resource"></a>[소비 리소스](#tab/consumption-1)

맵이 참조하는 모든 어셈블리를 업로드한 후에는 맵을 업로드할 수 있습니다.

1. Azure Portal에서 통합 계정이 아직 열리지 않은 경우 기본 Azure 검색 상자에서 `integration accounts`를 입력하고 **통합 계정** 을 선택합니다.

1. 맵을 추가할 통합 계정을 선택합니다.

1. 통합 계정 메뉴에서 **개요** 를 선택합니다. **설정** 아래에서 **맵** 을 선택합니다.

1. **맵** 창 도구 모음에서 **추가** 를 선택합니다.

1. [최대 2MB](#smaller-map) 또는 [2MB 초과](#larger-map) 맵을 추가합니다.

<a name="smaller-map"></a>

#### <a name="add-maps-up-to-2-mb"></a>최대 2MB의 맵 추가

1. **맵 추가** 에서 맵의 고유한 이름을 입력합니다.

1. **맵 유형** 아래에서 유형(예: **액체**, **XSLT**, **XSLT 2.0** 또는 **XSLT 3.0**)을 선택합니다.

1. **맵** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 맵을 선택합니다.

   **이름** 속성을 비워 두면 맵 파일을 선택한 후에 해당 속성에 맵의 파일 이름이 표시됩니다.

1. 준비가 되면 **확인** 을 선택합니다.

   맵 파일 업로드가 완료되면 **맵** 목록에 해당 맵이 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 맵이 표시됩니다.

<a name="larger-map"></a>

#### <a name="add-maps-more-than-2-mb"></a>2MB보다 큰 맵 추가

현재, 더 큰 맵을 추가하려면 [Azure Logic Apps REST API - 맵](/rest/api/logic/maps/createorupdate)을 사용합니다.

### <a name="standard-resource"></a>[표준 리소스](#tab/standard-1)

#### <a name="azure-portal"></a>Azure portal

1. 논리 앱 리소스 메뉴의 **설정** 에서 **맵** 을 선택합니다.

1. **맵** 창 도구 모음에서 **추가** 를 선택합니다.

1. **맵 추가** 에서 맵의 고유한 이름을 입력하고 `.xslt` 확장명 이름을 포함합니다.

1. **맵** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 맵을 선택합니다.

1. 준비가 되면 **확인** 을 선택합니다.

   맵 파일 업로드가 완료되면 **맵** 목록에 해당 맵이 나타납니다. 통합 계정의 **개요** 페이지에 있는 **아티팩트** 에서 업로드한 맵이 표시됩니다.

#### <a name="visual-studio-code"></a>Visual Studio Code

1. 논리 앱 프로젝트의 구조에서 **아티팩트** 폴더를 연 다음 **맵** 폴더를 엽니다.

1. **맵** 폴더에서 맵을 추가합니다.

---

## <a name="edit-maps"></a>맵 편집

기존 맵을 업데이트하려면 원하는 변경 내용이 있는 새 맵 파일을 업로드해야 합니다. 그러나 편집을 위해 기존 맵을 먼저 다운로드할 수 있습니다.

### <a name="consumption-resource"></a>[소비 리소스](#tab/consumption-2)

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **맵** 을 선택합니다.

1. **맵** 창이 열리면 맵을 선택합니다. 맵을 먼저 다운로드하고 편집하려면 **맵** 창 도구 모음에서 **다운로드** 를 선택하고 맵을 저장합니다.

1. 업데이트된 맵을 업로드할 준비가 되면 **맵** 창에서 업데이트하려는 맵을 선택합니다. **맵** 창 도구 모음에서 **업데이트** 를 선택합니다.

1. 업로드하려는 업데이트된 맵을 찾아 선택합니다.

   맵 파일 업로드가 완료되면 업데이트된 **맵** 목록에 해당 맵이 나타납니다.

### <a name="standard-resource"></a>[표준 리소스](#tab/standard-2)

1. 아직 열려 있지 않은 경우 [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 에서 **맵** 을 선택합니다.

1. **맵** 창이 열리면 맵을 선택합니다. 맵을 먼저 다운로드하고 편집하려면 **맵** 창 도구 모음에서 **다운로드** 를 선택하고 맵을 저장합니다.

1. **맵** 창 도구 모음에서 **추가** 를 선택합니다.

1. **맵 추가** 에서 맵의 고유한 이름을 입력하고 `.xslt` 확장명 이름을 포함합니다.

1. **맵** 상자 옆에서 폴더 아이콘을 선택합니다. 업로드할 맵을 선택합니다.

1. 준비가 되면 **확인** 을 선택합니다.

   맵 파일 업로드가 완료되면 업데이트된 **맵** 목록에 해당 맵이 나타납니다.

---

## <a name="delete-maps"></a>맵 삭제

### <a name="consumption-resource"></a>[소비 리소스](#tab/consumption-3)

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 엽니다.

1. 통합 계정 메뉴에서 **설정** 아래의 **맵** 을 선택합니다.

1. **맵** 창이 열리면 맵을 선택한 다음 **삭제** 를 선택합니다.

1. 맵을 삭제할지 확인하려면 **예** 를 선택합니다.

### <a name="standard-resource"></a>[표준 리소스](#tab/standard-3)

1. 아직 열려 있지 않은 경우 [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 에서 **맵** 을 선택합니다.

1. **맵** 창이 열리면 맵을 선택한 다음 **삭제** 를 선택합니다.

1. 맵을 삭제할지 확인하려면 **예** 를 선택합니다.

---

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps의 워크플로용 XML 변환](logic-apps-enterprise-integration-transform.md)
* [Azure Logic Apps의 워크플로에 대한 XML 유효성 검사](logic-apps-enterprise-integration-xml-validation.md)
