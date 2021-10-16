---
title: 상업용 Marketplace를 대상으로 한 Azure 애플리케이션 제품 계획
description: 파트너 센터를 사용 하 여 Azure Marketplace에 대 한 Azure 응용 프로그램 제품을 계획 합니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: d8058181ea56fdfc7465748f7ebf519c51422f64
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070891"
---
# <a name="plan-an-azure-application-offer"></a>Azure 애플리케이션 제안 계획

이 문서에서는 상용 marketplace에 Azure 애플리케이션 제품을 게시 하기 위한 다양 한 옵션 및 요구 사항을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 애플리케이션 제품을 디자인, 빌드 및 테스트하려면 Azure 플랫폼 및 제품을 빌드하는 데 사용되는 기술에 관한 기술적 지식이 둘 다 필요합니다. 엔지니어링 팀에는 다음 Microsoft 기술에 대한 지식이 있어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 관한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)에 관한 실무 지식
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 관한 실무 지식
- [JSON](https://www.json.org/)에 관한 실무 지식

### <a name="technical-documentation-and-resources"></a>기술 문서 및 리소스

상업용 Marketplace용으로 Azure 애플리케이션 제품을 계획할 때 다음 리소스를 검토합니다.

- [Azure Resource Manager 템플릿 이해](../azure-resource-manager/templates/syntax.md)
- 빠른 시작:
    - [Azure Quickstart 템플릿](https://azure.microsoft.com/resources/templates/)
    - [Azure 템플릿 모범 사례 가이드](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [애플리케이션 정의 게시](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [서비스 카탈로그 앱 배포](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- 자습서:
    - [정의 파일 만들기](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- 샘플:
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [관리되는 애플리케이션 솔루션](../azure-resource-manager/managed-applications/sample-projects.md)

[Azure Marketplace에 대 한 솔루션 템플릿 및 관리 되는 응용](https://channel9.msdn.com/Events/Build/2018/BRK3603) 프로그램은 Azure 응용 프로그램 제품 유형에 대 한 포괄적인 소개를 제공 합니다.

- 사용 가능한 제품 유형
- 필요한 기술 자산
- Azure Resource Manager 템플릿을 작성하는 방법
- 앱 UI 개발 및 테스트
- 앱 제품을 게시하는 방법
- 애플리케이션 검토 프로세스

### <a name="suggested-tools"></a>권장되는 도구

Azure 애플리케이션을 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/)(다음 확장 포함):
    - 확장: [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Azure 개발자 도구](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 확인할 수 있습니다. Visual Studio를 사용하는 경우 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)를 참조하세요.

## <a name="listing-options"></a>나열 옵션

제품이 게시된 후에는 제품에 대한 목록 옵션이 제품 목록 페이지의 왼쪽 위 모서리에 단추로 표시됩니다. 예를 들어 다음은 ‘지금 받기’ 단추를 사용하는 Azure Marketplace의 제품 목록 페이지를 보여 주는 스린크샷입니다. 시험 사용을 제공하도록 선택한 경우 ‘시험 사용’ 단추도 표시됩니다.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Azure Marketplace 목록 페이지를 보여 줍니다.":::

## <a name="test-drive"></a>시험 사용

고객이 제품을 구매하기 전에 사용해 볼 수 있는 Azure 애플리케이션 제품에 대한 시험 사용을 사용하도록 선택할 수 있습니다. 시험 사용에 대한 자세한 내용은 [시험 사용이란?](what-is-test-drive.md)을 참조하세요. 다양한 종류의 시험 사용을 구성하는 방법에 대한 자세한 내용은 [시험 사용 기술 구성](test-drive-technical-configuration.md)을 참조하세요.

[시험 사용 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)를 읽어보고 [시험 사용 개요 PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)를 다운로드할 수도 있습니다(팝업 차단기가 꺼져 있는지 확인).

> [!NOTE]
> 훑어보는 경우에도 사용자가 주목해야 하는 정보입니다. 모든 Azure 애플리케이션은 Azure Resource Manager 템플릿을 사용하여 구현되므로 Azure 애플리케이션에 사용할 수 있는 유일한 시험 사용 유형은 [Azure Resource Manager 기반 시험 사용](azure-resource-manager-test-drive.md)입니다.

## <a name="customer-leads"></a>잠재 고객

제품을 CRM(고객 관계 관리) 시스템에 연결하여 고객 정보를 수집해야 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품명, ID, 고객이 찾는 제품이 있는 온라인 스토어와 함께 구성된 CRM 시스템으로 전송됩니다. 상업용 Marketplace는 Azure 테이블을 사용하거나 Power Automate를 사용하여 HTTPS 엔드포인트를 구성하는 옵션을 비롯한 다양한 CRM 시스템을 지원합니다.

제품을 만드는 도중 또는 만든 이후, 언제든지 CRM 연결을 추가하거나 수정할 수 있습니다. 자세한 내용은 [상업용 Marketplace 제품의 잠재 고객](partner-center-portal/commercial-marketplace-get-customer-leads.md)을 참조하세요.

## <a name="categories-and-subcategories"></a>범주 및 하위 범주

1~2개의 범주를 선택하여 적절한 상업용 Marketplace 검색 영역으로 제품을 그룹화할 수 있습니다. 기본 범주와 보조 범주마다 최대 두 개의 하위 범주를 선택할 수 있습니다. 범주와 하위 범주의 전체 목록은 [제품 목록 모범 사례](marketplace-categories-industries.md#categories)를 참조하세요.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 Marketplace에서 제품에 사용할 수 있는 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번 읽고 동의만 하면 되고 사용자 지정 사용 약관을 만들 필요는 없습니다.

표준 계약을 사용하도록 선택할 경우 표준 계약에 범용 수정계약 사용 약관과 최대 10개의 사용자 지정 수정계약을 추가할 수 있습니다. 표준 계약 대신 사용자 고유의 사용 약관을 사용할 수도 있습니다. 이러한 세부 정보는 **속성** 페이지에서 관리합니다. 자세한 내용은 [Microsoft 상업용 Marketplace 표준 계약](standard-contract.md)을 참조하세요.

> [!NOTE]
> 상업용 Marketplace 표준 계약을 사용하여 제품이 게시되면 사용자 고유의 사용자 지정 사용 약관을 이용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약 또는 고유의 사용 약관에 따라 솔루션을 제공합니다. 표준 계약의 사용 약관을 수정하려면 표준 계약 수정계약을 통해 이 작업을 수행할 수 있습니다.

## <a name="offer-listing-details"></a>제품 목록 세부 정보

파트너 센터에서 새 Azure 애플리케이션 제품을 만들 때 제품 목록 페이지에 텍스트, 이미지, 선택적 비디오, 기타 세부 정보를 입력합니다. 다음 예에 표시 된 것 처럼 Azure Marketplace에서 제품 목록을 검색할 때 표시 되는 정보입니다.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="이 제품이 Azure Marketplace에 어떻게 표시되는지 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>설명

1. 로고
2. 범주
3. 지원 주소(링크)
4. 사용 약관
5. 개인 정보 취급 방침 주소(링크)
6. Offer name
7. 요약
8. Description
9. 스크린샷/비디오

다음 스크린샷은 Azure Portal 제품 정보가 표시되는 방식을 보여 줍니다.

[![해당 제품이 Azure Portal에 어떻게 표시되는지 보여 줍니다.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>설명

1. 제목
2. Description
3. 유용한 링크
4. 스크린샷

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 별도의 언급이 없는 경우 다음 항목이 필요합니다.

- **이름**: 이 이름은 상업용 Marketplace에서 제품 목록의 제목으로 표시됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약**: 줄 바꿈 없이 100자 이하의 일반 텍스트 한 문장으로 제품의 목적 또는 기능을 설명합니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **간단한 설명**: 최대 256자의 일반 텍스트를 제공합니다. 이 요약은 제품의 세부 정보 페이지에 표시됩니다.
- **설명**: 이 설명은 Azure Marketplace 목록 개요에 표시됩니다. 가치 제안, 주요 이점, 대상 사용자 베이스, 범주 또는 산업 협회, 앱 내 구매 기회, 제품이 해결할 수 있는 고객 요구 사항 또는 불만 사항, 필요한 정보를 공개하고 자세한 정보를 볼 수 있는 링크를 포함하는 것이 좋습니다.

    이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. HTML 태그를 사용하여 설명의 서식을 지정할 수도 있습니다. 이 상자에는 HTML 태그와 공백을 포함하여 최대 3,000자의 텍스트를 입력할 수 있습니다. 추가 팁은 [유용한 앱 설명 작성](/windows/uwp/publish/write-a-great-app-description)과 [상업용 Marketplace 제품 설명에서 지원되는 HTML 태그](supported-html-tags.md)를 참조하세요.

- **검색 키워드**(선택 사항): 고객이 온라인 스토어에서 제품을 찾는 데 사용할 수 있는 최대 3개의 검색 키워드를 입력합니다. 최상의 결과를 얻기 위해 이 키워드를 설명에도 사용합니다. 제품 **이름** 과 **설명** 을 포함할 필요가 없습니다. 해당 텍스트는 검색에 자동으로 포함됩니다.
- **개인정보처리방침 링크**: 회사의 개인정보처리방침에 대한 URL입니다. 파트너는 유효한 개인정보처리방침을 제공해야 하며 앱이 개인정보 관련 법률 및 규정을 준수하도록 관리할 책임이 있습니다.
- **유용한 링크**(선택 사항): 제품 사용자를 위한 다양한 리소스의 링크를 제공할 수 있습니다. 예를 들어 포럼, FAQ, 릴리스 정보 등이 있습니다.
- **연락처 정보**: 조직의 다음 연락처를 지정해야 합니다.
  - **고객 지원팀 연락처**: 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 이메일을 제공합니다. 고객 지원팀 웹 사이트의 URL도 포함해야 합니다.
  - **엔지니어링 연락처**: 제품에 문제가 있을 때 Microsoft에서 직접 사용할 수 있는 이름, 전화 번호 및 이메일을 제공합니다. 이 연락처 정보는 상업용 Marketplace에 표시되지 않습니다.
  - **CSP 프로그램 연락처**(선택 사항): CSP(클라우드 솔루션 공급자) 프로그램을 옵트인하는 경우 이름, 전화번호, 메일을 제공합니다. 그러면 해당 파트너가 질문이 있을 때 연락할 수 있습니다. 마케팅 자료의 URL을 포함할 수도 있습니다.
- **미디어 – 로고**: **큰** 로고의 PNG 파일을 제공합니다. 파트너 센터에서는 이 파일을 사용하여 **작은** 로고와 **중간** 로고를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.
  - 대형(216x216에서 350x350픽셀까지, 필수)
  - 보통(90x90픽셀, 선택 사항)
  - 작음(48x48 px, 선택 사항)

  이러한 로고는 온라인 스토어의 여러 위치에 사용됩니다.
  - Azure Marketplace 검색 결과에 작은 로고가 나타납니다.
  - 중간 로고는 Microsoft Azure에서 새 리소스를 만들 때 표시됩니다.
  - Azure Marketplace의 제품 목록 페이지에 큰 로고가 표시됩니다.

  로고에 대해서는 다음 지침을 따르세요.

  - Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수를 제한합니다.
  - 포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로 이러한 색을 사용하지 마십시오. 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다.
  - 투명한 배경을 사용하는 경우 로고 및 텍스트는 흰색, 검은색 또는 파란색이 아니어야 합니다.
  - 로고의 모양과 느낌은 평면적이어야 하며 로고나 배경에 그라데이션을 사용하지 마세요. 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 마십시오. 이미지가 흐리면 제출이 거부됩니다.
  - 로고가 늘어나지 않았는지 확인합니다.

- **미디어 - 스크린샷**(선택 사항): 제품의 작동 방식을 보여 주는 스크린샷을 추가하는 것이 좋습니다. 다음 요구 사항에 따라 제품의 작동 방식을 보여 주는 스크린샷을 5개까지 추가할 수 있습니다.
  - 1280x720 픽셀
  - .PNG 파일
  - 자막 포함
- **미디어 - 비디오**(선택 사항): 다음 요구 사항에 따라 제품을 설명하는 비디오를 5개까지 추가할 수 있습니다.
  - Name
  - URL: YouTube 또는 Vimeo에만 호스팅해야 합니다.
  - 썸네일: 1280x720 .PNG 파일

> [!NOTE]
> 제품은 상업용 Marketplace에 게시되는 일반 [상업용 Marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general.md) 충족해야 합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 대상 그룹은 제품이 온라인 스토어에 라이브로 게시되기 전에 제품에 액세스하여 엔드투엔드 기능을 테스트할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 플랜 대상 그룹과 다릅니다. 프라이빗 플랜은 선택한 특정 대상만 사용할 수 있습니다. 이를 통해 특정 고객과 사용자 지정 플랜에 대해 협상할 수 있습니다.

Azure 구독 ID와 각각에 대한 선택적 설명과 함께 미리 보기 대상을 정의합니다. 해당 필드는 고객에게 표시되지 않습니다.

## <a name="technical-configuration"></a>기술 구성

[Marketplace 계량 서비스 API](marketplace-metering-service-apis.md)를 사용하여 계량 이벤트를 내보내는 관리형 애플리케이션의 경우 계량 이벤트를 내보낼 때 서비스에서 사용할 ID를 제공해야 합니다.

이 구성은 [일괄 처리 사용량 이벤트](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)를 사용하려는 경우에 필요합니다. [사용량 이벤트](marketplace-metering-service-apis.md#metered-billing-single-usage-event)를 제출하려는 경우 [인스턴스 메타데이터 서비스](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 [JWT(JSON Web Token) 전달자 토큰](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)을 가져올 수도 있습니다.

- **Azure Active Directory 테넌트 ID**(필수): 두 서비스 간 연결이 인증된 통신을 통해 설정되는지 확인할 수 있도록 Azure Portal 내에서 [Azure AD(Active Directory) 앱을 만들어야](../active-directory/develop/howto-create-service-principal-portal.md) 합니다. Azure AD(Active Directory) 앱의 [테넌트 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동합니다. **표시 이름** 열에서 앱을 선택합니다. 그런 다음, **속성** 을 찾은 후 **디렉터리(테넌트) ID**(예: `50c464d3-4930-494c-963c-1e951d15360e`)를 찾습니다.
- **Azure Active Directory 애플리케이션 ID**(필수): [애플리케이션 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)와 인증 키도 필요합니다. 애플리케이션 ID를 찾으려면 Azure Active Directory에서 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동합니다. **표시 이름** 열에서 앱을 선택한 다음, **애플리케이션(클라이언트) ID**(예: `50c464d3-4930-494c-963c-1e951d15360e`)를 찾습니다. 인증 키를 찾으려면 **설정** 으로 이동하고 **키** 를 선택합니다. 설명 및 기간을 입력해야 숫자 값이 제공됩니다.

> [!NOTE]
> Azure 애플리케이션 ID는 게시자 ID와 연결되며 이 게시자 계정 내에서만 다시 사용할 수 있습니다.

## <a name="additional-sales-opportunities"></a>추가 판매 기회

Microsoft에서 지원하는 마케팅 및 판매 채널을 옵트인하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 프로세스 마지막 부분에서 다음과 같은 두 개의 탭이 표시됩니다.

- **CSP를 통한 재판매**: 이 옵션을 사용하면 Microsoft CSP(클라우드 솔루션 공급자) 파트너가 솔루션을 번들 제품의 일부로 재판매할 수 있습니다. 자세한 내용은 [클라우드 솔루션 공급자 프로그램](./cloud-solution-providers.md)을 참조하세요.
- **Microsoft와 공동 판매**: 이 옵션을 사용하면 Microsoft 영업 팀이 고객의 요구 사항을 평가할 때 IP 공동 판매 적합 솔루션을 고려합니다. 제품 평가를 준비하는 방법에 대한 자세한 내용은 [상업용 Marketplace의 공동 판매 옵션](./co-sell-configure.md)을 참조하세요. IP 공동 판매 요구 사항에 대한 자세한 내용은 [공동 판매 상태의 요구 사항](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)을 참조하세요. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대한 자세한 내용은 [클라우드 솔루션 공급자](cloud-solution-providers.md)를 참조하세요.

자세한 내용은 [Azure Marketplace로 클라우드 비즈니스 확장](https://azuremarketplace.microsoft.com/sell)을 참조하세요.

## <a name="plans"></a>계획

Azure 애플리케이션 제품에는 하나 이상의 플랜이 필요합니다. 플랜은 솔루션 범위, 제한, 관련 가격 책정을 정의합니다(해당하는 경우). 제품에 대한 여러 구독을 만들어 고객에게 다양한 기술 및 가격 옵션을 제공할 수 있습니다.

가격 책정 모델과 프라이빗 플랜을 포함한 플랜에 대한 일반적인 지침은 [상업용 Marketplace 제품에 대한 플랜 및 가격 책정](plans-pricing.md)을 참조하세요. 다음 섹션에서는 Azure 애플리케이션 플랜과 관련된 추가 정보에 대해 설명합니다.

### <a name="types-of-plans"></a>플랜 유형

Azure 애플리케이션 플랜에는 ‘솔루션 템플릿’과 ‘관리형 애플리케이션’이라는 두 종류가 있습니다.  두 플랜 유형 모두 단일 VM(가상 머신) 이외의 솔루션 배포와 구성을 자동화하는 기능을 지원합니다. VM, 네트워킹, 스토리지 리소스를 비롯한 여러 리소스를 제공하여 IaaS 솔루션과 같은 복잡한 솔루션을 제공하는 프로세스를 자동화할 수 있습니다. 두 플랜 유형 모두 VM에 대한 제한 없이 여러 종류의 Azure 리소스를 사용할 수 있습니다.

- **솔루션 템플릿** 플랜은 상업용 Marketplace에 솔루션을 게시하는 주요 방법 중 하나입니다. 솔루션 템플릿 플랜은 상업용 Marketplace에서 거래할 수 없지만 상업용 Marketplace를 통해 청구되는 유료 VM 제품을 배포하는 데 사용할 수 있습니다. 고객이 솔루션을 관리하고 트랜잭션이 다른 플랜을 통해 청구되는 경우 솔루션 템플릿 플랜 유형을 사용합니다. 솔루션 템플릿을 빌드하는 방법에 관한 자세한 내용은 [Azure Resource Manager란?](../azure-resource-manager/management/overview.md)을 참조하세요.
- **관리형 애플리케이션** 플랜을 사용하면 고객을 위한 완전 관리형 턴키 애플리케이션을 쉽게 빌드하고 제공할 수 있습니다. 몇 가지 주요 차이점을 포함하여 솔루션 템플릿 플랜과 동일한 기능을 제공합니다.
    - 리소스는 리소스 그룹에 배포되고 앱 게시자가 관리합니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. 
    - 게시자는 솔루션의 지속적인 지원 비용을 지정하고 상업용 Marketplace를 통해 트랜잭션을 지원합니다.
 
    사용자 또는 고객이 솔루션을 파트너에서 관리하거나 구독 기반 솔루션을 배포해야 하는 경우 관리형 애플리케이션 플랜 유형을 사용합니다. 관리형 애플리케이션의 장점과 유형에 대한 자세한 내용은 [Azure 관리형 애플리케이션 개요](../azure-resource-manager/managed-applications/overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 솔루션 템플릿을 계획하려면 [Azure 애플리케이션 제품에 대한 솔루션 템플릿 플랜](plan-azure-app-solution-template.md)을 참조하세요.
- Azure 관리형 애플리케이션을 계획하려면 [Azure 애플리케이션 제품에 대한 Azure 관리형 애플리케이션 계획](plan-azure-app-managed-app.md)을 참조하세요.