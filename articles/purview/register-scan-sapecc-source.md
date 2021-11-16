---
title: SAP ECC 원본에 연결 및 관리
description: 이 가이드에서는 Azure Purview에서 SAP ECC에 연결하고 Purview의 기능을 사용하여 SAP ECC 원본을 검사하고 관리하는 방법을 설명합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 853509bbf2148fc4d34463b61c3dfb4a330b79ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486330"
---
# <a name="connect-to-and-manage-sap-ecc-in-azure-purview"></a>Azure Purview에서 SAP ECC에 연결 및 관리

이 문서에서는 SAP ECC를 등록하는 방법과 Azure Purview에서 SAP ECC를 인증하고 상호 작용하는 방법을 간략하게 설명합니다. Azure Purview에 대한 자세한 내용은 [소개 문서](overview.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register)| [예](#scan)| 아니요 | 아니요 | 아니요 | 예| [예**](how-to-lineage-sapecc.md)|

\**데이터 세트가 [데이터 팩터리 복사 작업](how-to-link-azure-data-factory.md)에서 원본/싱크로 사용되는 경우 데이터 계보가 지원됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

* 최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성 가이드](../data-factory/create-self-hosted-integration-runtime.md)를 참조하세요.

    >[!NOTE]
    >SAP ECC를 검색하는 작업은 메모리를 많이 사용하는 작업이므로 대용량 메모리가 있는 머신에 자체 호스팅 통합 런타임을 설치하는 것이 좋습니다(예: 128GB).

* [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 자체 호스팅 통합 런타임이 설치된 가상 머신에 설치되어 있는지 확인합니다.

* Visual Studio 2012용 Visual C++ 재배포 가능 패키지 업데이트 4가 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 이 업데이트가 설치되어 있지 않으면 [여기서 다운로드할 수 있습니다](https://www.microsoft.com/download/details.aspx?id=30679).

* SAP 웹 사이트에서 64비트 [Microsoft .NET용 SAP Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)을 다운로드하고 이를 자체 호스팅 통합 런타임 컴퓨터에 설치합니다. 설치 도중 **선택적 설치 단계** 창에서 **GAC에 어셈블리 설치** 를 선택해야 합니다.

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="필수 구성 요소" border="true":::

* 커넥터에서 [SAP Java Connector(JCo)](https://support.sap.com/en/product/connectors/jco.html) 3.0 API를 사용하여 SAP에서 메타데이터를 읽습니다. Java Connector가 자체 호스팅 통합 런타임이 설치된 가상 머신에서 사용 가능해야 합니다. 환경에 대해 올바른 JCo 배포를 사용하고 있는지 확인합니다. 예를 들어 Microsoft Windows 컴퓨터의 경우 sapjco3.jar 및 sapjco3.dll 파일을 사용할 수 있어야 합니다.

    > [!Note]
    > VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

* [ABAP 함수 배포 가이드](abap-functions-deployment-guide.md)에서 언급한 단계를 따라 SAP 서버에 메타데이터 추출 ABAP 함수 모듈을 배포합니다. SAP 서버에서 RFC 함수 모듈을 만들려면 ABAP 개발자 계정이 필요합니다. 사용자 계정에는 SAP 서버에 연결하고 다음 RFC 함수 모듈을 실행할 수 있는 권한이 있어야 합니다.
  * STFC_CONNECTION(연결 확인)
  * RFC_SYSTEM_INFO(시스템 정보 확인)

## <a name="register"></a>등록

이 섹션에서는 [Purview Studio](https://web.purview.azure.com/)를 사용하여 Azure Purview에 SAP ECC를 등록하는 방법을 설명합니다.

### <a name="authentication-for-registration"></a>등록 인증

SAP ECC 원본에 대해 유일하게 지원되는 인증은 **기본 인증** 입니다.

### <a name="steps-to-register"></a>등록 단계

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
1. **등록** 을 선택합니다.
1. 원본 등록에서 **SAP ECC** 를 선택합니다. **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="SAPECC 등록 옵션" border="true":::

**원본 등록(SAP ECC)** 화면에서 다음을 수행합니다.

1. 카탈로그 내에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

1. SAP ECC 원본에 연결할 **애플리케이션 서버** 이름을 입력합니다. 이는 SAP 애플리케이션 서버 호스트의 IP 주소일 수 있습니다.

1. SAP **시스템 번호** 를 입력합니다. 이는 00~99의 두 자리 정수입니다.

1. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).

1. 마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="SAPECC 등록" border="true":::

## <a name="scan"></a>검사

아래 단계에 따라 SAP ECC를 검사하여 자산을 자동으로 식별하고 데이터를 분류합니다. 일반적인 검사에 대한 자세한 내용은 [검사 및 수집 소개](concept-scans-and-ingestion.md)를 참조하세요.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

1. 관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 만들 수 있습니다.

1. **원본** 으로 이동합니다.

1. 등록된 SAP ECC 원본을 선택합니다.

1. **+ 새 검사** 선택

1. 아래 세부 정보를 제공합니다.

    1. **이름**: 검사 이름

    1. **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    1. **자격 증명**: 데이터 원본에 연결할 자격 증명을 선택합니다. 다음을 수행해야 합니다.

        * 자격 증명을 만드는 동안 기본 인증을 선택합니다.
        * SAP 서버에 연결할 사용자 ID를 사용자 이름 입력 필드에 입력합니다.
        * 비밀 키에 SAP 서버에 연결하는 데 사용되는 사용자 암호를 저장 합니다.

    1. **클라이언트 ID**: SAP 클라이언트 ID를 입력합니다. 이는 000~999 사이의 3자리 숫자입니다.

    1. **JCo 라이브러리 경로**: JCo 라이브러리가 위치하는 디렉터리 경로입니다.

    1. **사용 가능한 최대 메모리:** 검사 프로세스에서 사용할 자체 호스팅 통합 런타임에서 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 SAP ECC 원본의 크기에 따라 달라집니다. 사용 가능한 큰 메모리(예: 100)를 제공하는 것이 좋습니다.

        :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="SAPECC 검사" border="true":::

1. **계속** 을 선택합니다.

1. **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
