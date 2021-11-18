---
title: 자료에 대 한 SAP 미디어 가져오기
description: Azure의 SAP 배포 자동화 프레임 워크에 대 한 제품 청구서 (BOM)에서 사용할 SAP 미디어를 다운로드 하는 방법입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: 85680b151a108e3ee9854b4d531669ba1995bf7d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730457"
---
# <a name="acquire-media-for-bom-creation"></a>BOM 생성을 위한 미디어 획득

[Azure의 SAP 배포 자동화 프레임 워크](automation-deployment-framework.md) 는 bom (자재 청구)을 사용 합니다. BOM을 만들려면 관련 SAP 설치 미디어를 찾아서 다운로드 해야 합니다. 그런 다음 이러한 미디어 파일을 Azure storage 계정에 업로드 해야 합니다.

> [!NOTE]
> 이 가이드에서는 고급 배포 항목을 다룹니다. 자동화 프레임 워크를 배포 하는 방법에 대 한 기본 설명은 대신 [시작 가이드](automation-get-started.md) 를 참조 하세요.

이 가이드는 SAP 응용 프로그램 (DB) 또는 HANA 데이터베이스를 사용 하는 구성에 대 한 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

- SAP 소프트웨어를 다운로드 하 고 유지 관리 계획자에 액세스할 수 있는 권한이 있는 SAP 계정.
- 컴퓨터에 [SAP 다운로드 관리자](https://support.sap.com/en/my-support/software-downloads.html) 를 설치 합니다.
- SAP 시스템에 대 한 정보:
    - SAP 계정 사용자 이름 및 암호
    - 배포할 SAP 시스템 제품 (예: **S/4HANA**)
    - Sap SID (시스템 식별자)
    - 모든 언어 팩 요구 사항
    - 응용 프로그램 인프라에서 사용할 OS (운영 체제)
- Azure 구독 Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="acquire-media"></a>미디어 가져오기

[SAP 설치 미디어 다운로드](#download-media)를 준비 하려면:

1. 컴퓨터에서 stack SAP 다운로드를 위한 고유 디렉터리를 만듭니다. 예들 들어 `~/Downloads/S4HANA_1909_SP2/`입니다.

1. [SAP ONE Support 실행 패드](https://launchpad.support.sap.com/)에 로그인 합니다.

1. 다운로드 바구니를 지웁니다.

    1. **소프트웨어 다운로드** 로 이동 합니다.

    1. **바구니 다운로드** 를 선택 합니다.

    1. 바구니의 모든 항목을 선택 합니다.

    1. 바구니에서 모든 항목을 제거 하려면 **X** 를 선택 합니다.

1. SAPCAR 유틸리티를 다운로드 바구니에 추가 합니다.

    1. 검색 창에서 검색 유형이 **다운로드** 로 설정 되었는지 확인 합니다.

    1. `SAPCAR`검색 창에를 입력 하 고 **검색** 을 선택 합니다.

    1. **다운로드할 수 있는 테이블 항목** 에서 **유지 관리 소프트웨어 구성 요소** 를 사용 하 여 **SAPCAR** 에 대 한 행을 선택 합니다. 이 단계에서는 최신 버전의 유틸리티에 대 한 사용 가능한 다운로드를 필터링 합니다.

    1. 테이블의 드롭다운 메뉴가 올바른 OS 유형을 표시 하는지 확인 합니다. 예들 들어 `LINUX ON X86_64 64BIT`입니다.

    1. SAPCAR 실행 파일의 파일 이름 옆에 있는 확인란을 선택 합니다. 예들 들어 `SAPCAR_1320-80000935.EXE`입니다.

    1. 쇼핑 카트 아이콘을 선택 하 여 선택 항목을 다운로드 바구니에 추가 합니다.

1. [유지 관리 계획자](https://support.sap.com/en/alm/solution-manager/processes-72/maintenance-planner.html)에 로그인 합니다.

1. SAP 시스템을 설계 합니다. 예를 들어 **S/4HANA** 를 사용 하는 경우:

    1. **SAP S/4HANA** 에 대 한 계획을 선택 합니다.

    1. 필요에 따라 유지 관리 계획 이름을 변경 합니다.

    1. **Install NEW S4HANA System** 을 선택 합니다. 
    
    1. **다음** 을 선택합니다.

    1. **새 시스템을 설치** 하려면 사용 중인 SAP SID를 입력 합니다.

    1. **대상 버전** 의 경우 대상 SAP 버전을 선택 합니다. 예: **SAP S/4HANA 2020**.

    1. **대상 스택** 에서 대상 스택을 선택 합니다. 예를 들어 **초기 배송 스택입니다**.

    1. 필요한 경우 **대상 제품 인스턴스** 를 선택 합니다.

    1. **다음** 을 선택합니다.

1. 공동 배포를 디자인 합니다.

    1. **백 엔드를 사용 하 여 공동 배포를** 선택 합니다.

    1. **대상 버전** 에서 codeployment의 대상 버전을 선택 합니다. 예를 들어 sap **FIORI FOR Sap S/4HANA 2020** 입니다.

    1. **대상 스택** 에서 codeployment의 대상 스택을 선택 합니다. 예를 들어 **초기 배송 스택입니다**.

    1. **다음** 을 선택합니다.

1. **계속 계획** 을 선택 합니다. *새 시스템* 을 사용 하는 경우 **다음** 을 선택 합니다. *기존 시스템* 을 사용 하는 경우 다음과 같이 변경 합니다.

    1. **OS/DB 종속 파일** 의 경우 **64 비트 x86_64에서 Linux** 를 선택 합니다.

    1. **선택 확인** 을 선택합니다.

    1. **다음** 을 선택합니다.

1. 필요에 따라 **스택 독립 파일 선택** 에서 ABAP 되지 않은 데이터베이스에 대 한 설정을 구성 합니다. 데이터베이스를 확장 하 고 필요 하지 않은 언어 파일을 선택 취소할 수 있습니다.

1. **다음** 을 선택합니다.

1. 이전에 만든 stack 다운로드 디렉터리에 stack XML 파일을 다운로드 합니다.

    1. **푸시를 선택 하 여 바구니를 다운로드** 합니다.

    1. **추가 다운로드** 를 선택 합니다.

    1. **스택 텍스트 파일 다운로드** 를 선택 합니다.

    1. **PDF 다운로드** 를 선택 합니다.

    1. **Excel로 내보내기** 를 선택합니다.

    1. SAP 실행 패드에서 다시 다운로드 바구니로 이동 합니다. 페이지를 새로 고쳐 새 선택 항목을 확인 해야 할 수도 있습니다.

    1. **T** 아이콘을 선택 하 여 다운로드 바구니에 대 한 url이 포함 된 파일을 다운로드 합니다.

## <a name="get-download-basket-manifest"></a>다운로드 바구니 매니페스트 가져오기

> [!IMPORTANT]
> 스크립팅된 BOM 생성을 실행 하려는 경우에만 다음 단계를 수행 합니다. 이러한 작업은 SAP 다운로드 관리자를 실행 하기 전에 수행 해야 합니다. 스크립팅된 BOM 생성을 실행 하지 않으려는 경우 [다음 섹션으로 건너뜁니다](#download-media).

SAP 다운로드 바구니 매니페스트 JSON 파일 ()을 가져오려면 `DownloadBasket.json` 다음을 수행 합니다.

1. **Postman** 유틸리티를 엽니다.

1. 작업 영역 탭에서 더하기 기호 () 단추를 선택 하 여 새 요청을 추가 **+** 합니다. 요청에 새 페이지가 열립니다.

1. **매개 변수** 탭에서 요청 유형을로 설정 `GET` 합니다.

1. 요청 URL에 대해를 입력 `https://tech.support.sap.com:443/odata/svt/swdcuisrv/DownloadContentSet?_MODE=BASKET_CONTENT&_VERSION=3.1.2&$format=json` 합니다.

1. **Authorization** 탭을 선택합니다.

1. **유형** 에 대해 **기본 인증** 을 선택 합니다.

1. **사용자 이름** 에 대해 SAP 사용자 이름을 입력 합니다.

1. **암호** 에 대해 SAP 암호를 입력 합니다.

1. **Headers** 탭을 선택합니다.

1. Accept-Encoding 및 User-Agent 확인란의 선택을 취소 합니다.

1. **보내기** 단추를 선택합니다.

1. **본문** 탭에서 **Raw** 뷰를 선택 해야 합니다.

1. 원시 JSON 응답 본문을 복사 합니다. 스택 다운로드 디렉터리에 응답을 저장 합니다.

## <a name="download-media"></a>미디어 다운로드

SAP 설치 미디어를 다운로드 하려면 다음을 수행 합니다.

1. 컴퓨터에서 SAP 다운로드 관리자를 실행 합니다.

1. SAP 다운로드 관리자에 로그인 합니다.

1. SAP 다운로드 바구니에 액세스 합니다.

1. 다운로드 디렉터리를 만든 stack 다운로드 디렉터리로 설정 합니다. 예: ' ' ~/Downloads/S4HANA_1909_SP2/'.

1. 다운로드 바구니의 모든 파일을이 디렉터리에 다운로드 합니다.

> [!NOTE]
> SAP 다운로드 Url이 포함 된 텍스트 파일은 항상 `myDownloadBasketFiles.txt` 입니다. 그러나이 파일은 응용 프로그램 또는 데이터베이스에만 적용 됩니다. 이후 섹션에서 사용할 수 있도록이 특정 섹션에 대 한 다른 다운로드와이 파일을 보관 해야 합니다.

## <a name="upload-media"></a>미디어 업로드

SAP 미디어 및 스택 파일을 Azure storage 계정에 업로드 하려면:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure 서비스** 아래에서 **리소스 그룹** 을 선택합니다. 또는 `resource groups` 검색 창에를 입력 합니다. 

1. SAP 라이브러리에 대 한 리소스 그룹을 선택 합니다.

1. 리소스 그룹 페이지에서 `saplib` **리소스** 테이블의 저장소 계정을 선택 합니다.

1. 저장소 계정 페이지의 메뉴에서 **데이터 저장소** 아래에 있는 **컨테이너** 를 선택 합니다.

1. 컨테이너를 선택 `sapbits` 합니다.

1. 컨테이너 페이지에서 보관 및 도구를 업로드 합니다.

    1. **업데이트** 단추를 선택합니다.

    1. **파일 선택을** 선택 합니다.

    1. [이전에 SAP 미디어를 다운로드 한 디렉터리로](#download-media)이동 합니다.

    1. 모든 보관 파일을 선택 합니다. 이러한 파일 이름은,, 및와 유사 `*.SAR` `*.RAR` `*.ZIP` `SAPCAR*.EXE` 합니다.

    1. 고급 **을 선택** 하 여 고급 옵션을 표시 합니다.

    1. **업로드 디렉터리** 에를 입력 `archives` 합니다.

1. 스택 파일을 업로드 합니다.

    1. **업데이트** 단추를 선택합니다.

    1. **파일 선택을** 선택 합니다.

    1. [이전 섹션에서 만든](#acquire-media)다운로드 디렉터리로 이동 합니다.

    1. 모든 스택 파일을 선택 합니다. 이러한 파일 이름은와 비슷합니다 `MP_*.(xml|xls|pdf|txt)` .

    1. 고급 **을 선택** 하 여 고급 옵션을 표시 합니다.

    1. **업로드 디렉터리** 에 대해를 `boms/<Stack_Version>/stackfiles` 입력 `<Stack_Version>` 합니다. 여기서은 제품 정보의 조합입니다. 예를 들어 `S4HANA_2020_ISS_v001` 제품 유형이이 고 `S4HANA` 제품 릴리스가 이며 `2020` `ISS` 초기 소프트웨어 배송에 대 한 Service Pack 이며 스택은입니다 `v001` .

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [BOM 준비](automation-bom-prepare.md)
