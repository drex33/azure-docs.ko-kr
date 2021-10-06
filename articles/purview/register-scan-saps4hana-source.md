---
title: Azure Purview에서 SAP S/4HANA 원본 등록 및 검사 설정
description: 이 문서에서는 Azure Purview에서 SAP S/4HANA 원본을 등록하고 검사를 설정하는 방법을 설명합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 45ea054394c5f74a6a3a34b9a69df5c5b07a632f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212633"
---
# <a name="register-and-scan-a-sap-s4hana-source"></a>SAP S/4HANA 원본 등록 및 검사

이 문서에서는 Purview에서 SAP S/4HANA 원본을 등록하고 검사를 설정하는 방법을 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SAP S/4HANA 원본은 SAP S/4HANA 인스턴스에서 메타데이터를 추출할 수 있도록 **전체 검사** 를 지원하고, 데이터 자산 간의 **계보** 를 가져옵니다.

## <a name="prerequisites"></a>사전 요구 사항

1.  최신 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 설정합니다.
    자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](../data-factory/create-self-hosted-integration-runtime.md)을 참조하세요.

2.  자체 호스팅 통합 런타임이 설치된 가상 머신에 [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)이 설치되어 있는지 확인합니다.

3.  자체 호스팅 통합 런타임 컴퓨터에 \"Visual C++ 재배포 가능 패키지 2012 업데이트 4\"가 설치되어 있는지 확인합니다. 아직 설치되지 않은 경우 [여기](https://www.microsoft.com/download/details.aspx?id=30679)에서 다운로드하세요.

4.  SAP 웹 사이트에서 64비트 [Microsoft .NET용 SAP Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)을 다운로드하고 이를 자체 호스팅 통합 런타임 컴퓨터에 설치합니다. 설치 도중 **선택적 설치 단계** 창에서 **GAC에 어셈블리 설치** 를 선택해야 합니다.

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="필수 구성 요소" border="true":::

5.  커넥터에서 [SAP Java Connector(JCo)](https://support.sap.com/en/product/connectors/jco.html) 3.0 API를 사용하여 SAP에서 메타데이터를 읽습니다. 따라서 Java Connector가 자체 호스팅 통합 런타임이 설치된 가상 머신에서 사용 가능해야 합니다.
    환경에 대해 올바른 JCo 배포를 사용하고 있는지 확인합니다. 예를 들어 Microsoft Windows 컴퓨터의 경우 sapjco3.jar 및 sapjco3.dll 파일을 사용할 수 있어야 합니다.

    > [!Note] 
    >VM의 모든 계정에서 드라이버에 액세스할 수 있어야 합니다. 사용자 계정에는 설치하지 마세요.

6.  [ABAP 함수 배포 가이드](abap-functions-deployment-guide.md)에서 언급한 단계를 따라 SAP 서버에 메타데이터 추출 ABAP 함수 모듈을 배포합니다. SAP 서버에서 RFC 함수 모듈을 만들려면 ABAP 개발자 계정이 필요합니다. 사용자 계정에는 SAP 서버에 연결하고 다음 RFC 함수 모듈을 실행할 수 있는 권한이 있어야 합니다.
    -   STFC_CONNECTION(연결 확인)
    -   RFC_SYSTEM_INFO(시스템 정보 확인)

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

SAP S/4HANA 원본에 대해 유일하게 지원되는 인증은 **기본 인증** 입니다.

## <a name="register-sap-s4hana-source"></a>SAP S/4HANA 원본 등록

새 SAP S/4HANA 원본을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1.  Purview 계정으로 이동합니다.
2.  왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.
3.  **등록** 을 선택합니다.
4.  원본 등록에서 **SAP S/4HANA** 를 선택합니다. **계속** 을 선택합니다.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="SAPS/4Hana 등록 옵션" border="true":::

**원본 등록(SAP S/4HANA)** 화면에서 다음을 수행합니다.

1.  카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.

2.  SAP S/4HANA 원본에 연결할 **애플리케이션 서버** 이름을 입력합니다. 이는 SAP 애플리케이션 서버 호스트의 IP 주소일 수 있습니다.

3.  SAP **시스템 번호** 를 입력합니다. 이는 00~99의 두 자리 정수입니다.

4.  컬렉션을 선택하거나 새로 만듭니다(선택 사항).

5.  마침을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="SAP S/4HANA 등록" border="true":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1.  관리 센터에서 통합 런타임을 선택합니다. 자체 호스팅 통합 런타임이 설정되어 있는지 확인합니다. 설정되지 않은 경우 [여기](./manage-integration-runtimes.md)에 언급된 단계를 사용하여 자체 호스팅 통합 런타임을 만들 수 있습니다.

2.  **원본** 으로 이동합니다.

3.  등록된 SAP S/4HANA 원본을 선택합니다.

4.  **+ 새 검사** 선택

5.  아래 세부 정보를 제공합니다.

    a.  **이름**: 검사 이름

    b.  **통합 런타임을 통해 연결**: 구성된 자체 호스팅 통합 런타임을 선택합니다.

    다.  **자격 증명**: 데이터 원본에 연결할 자격 증명을 선택합니다. 다음을 수행해야 합니다.

    -   자격 증명을 만드는 동안 기본 인증을 선택합니다.
    -   SAP 서버에 연결할 사용자 ID를 사용자 이름 입력 필드에 입력합니다.
    -   비밀 키에 SAP 서버에 연결하는 데 사용되는 사용자 암호를 저장 합니다.

    d.  **클라이언트 ID:** SAP 시스템 클라이언트 ID를 입력합니다. 클라이언트는 000~999의 세 자리 숫자로 식별됩니다.

    e.  **JCo 라이브러리 경로**: JCo 라이브러리가 위치하는 폴더 경로를 지정합니다.

    f.  **사용 가능한 최대 메모리:** 고객의 VM에서 검사 프로세스를 수행하는 데 사용할 수 있는 최대 메모리(GB)입니다. 이는 검사할 SAP S/4HANA 원본의 크기에 따라 달라집니다.
    > [!Note] 
    > 일반적으로 1,000개 테이블당 1GB 메모리를 제공하세요.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="SAP S/4HANA 검사" border="true":::

6.  **계속** 을 선택합니다.

7.  **검사 트리거** 를 선택합니다. 예약을 설정하거나 검사를 한 번 실행할 수 있습니다.

8.  검사를 검토하고 **저장 및 실행** 을 선택합니다.

## <a name="viewing-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택합니다.

2. 원하는 데이터 원본을 선택합니다. 해당 데이터 원본에 기존 검사 목록이 표시됩니다.

3. 결과를 보려는 검사를 선택합니다.

4. 이 페이지는 각 스캔 실행에 대한 메트릭 및 상태와 함께 모든 이전 검사 실행을 표시합니다. 또한 검사가 예약되었는지 수동인지 여부, 분류가 적용된 자산 수, 발견된 총 자산 수, 검사 시작 및 종료 시간, 총 검사 기간이 표시됩니다.

## <a name="manage-your-scans"></a>검사 관리

검사를 관리하거나 삭제하려면 다음을 수행합니다.

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택한 다음, 원하는 데이터 원본을 선택합니다.

2. 관리하려는 검사를 선택합니다. **편집** 을 선택하여 검사를 편집할 수 있습니다.

3. **삭제** 를 선택하여 검사를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
