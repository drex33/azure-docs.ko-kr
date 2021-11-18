---
title: Automation 자료 준비
description: Azure에서 SAP deployment automation 프레임 워크에 사용할 전체 SAP BOM (자재 자료)을 준비 하는 방법입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: f5d2e83a64cbb1d9b74e67497e531db4e53142b6
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132760032"
---
# <a name="prepare-sap-bom"></a>SAP BOM 준비

[Azure의 SAP 배포 자동화 프레임 워크](automation-deployment-framework.md) 는 bom (자재 청구)을 사용 합니다. BOM은 SAP 시스템을 구성 하는 데 도움이 됩니다. 

자동화 프레임 워크의 GitHub 리포지토리에는 시작 하는 데 사용할 수 있는 [샘플 bom](https://github.com/Azure/sap-automation/tree/main/deploy/ansible/BOM-catalog) 집합이 포함 되어 있습니다. 다른 SAP 응용 프로그램 및 데이터베이스에 대해 Bom을 만들 수도 있습니다. 

Permalinks를 포함 하는 BOM을 생성 하려면 [이 유형의 bom을 만드는 단계를 수행](#permalinks)합니다.

> [!NOTE]
> 이 가이드에서는 고급 배포 항목을 다룹니다. 자동화 프레임 워크를 배포 하는 방법에 대 한 기본 설명은 대신 [시작 가이드](automation-get-started.md) 를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 아직 설치 하지 않은 경우 [SAP 설치 미디어 및 관련 파일을 가져오고 다운로드 하 고 준비](automation-bom-get-files.md) 합니다.
    - Azure storage 계정의 SAP 응용 프로그램 (DB) 또는 HANA 미디어입니다.
- BOM 파일 작업에 사용 되는 YAML 편집기입니다.
- 응용 프로그램 설치 템플릿: 
    - SAP 중앙 서비스 (SCS)
    - SAP 기본 응용 프로그램 서버 (PAS)
    - SAP 추가 응용 프로그램 서버 (.AAS)
- [SAP 미디어를 얻기](automation-bom-get-files.md#acquire-media)위해 만든 폴더에 필요한 스택 파일을 다운로드 합니다. 자세한 내용은 [기본 BOM 준비 방법 가이드](automation-bom-prepare.md)를 참조 하세요.
- Sap 미디어를 [](automation-bom-get-files.md#get-download-basket-manifest) `DownloadBasket.json` [얻기 위해 만든 폴더로](automation-bom-get-files.md#acquire-media)다운로드 되는 sap 다운로드 바구니 매니페스트 ()의 복사본입니다.
    - [Postman 유틸리티](https://www.postman.com/downloads/)의 설치입니다.
- Azure 구독 Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 사용 하려는 데이터베이스에 대 한 작업 권한이 있는 SAP 계정.
- [BOM의 유효성을 검사](#validate-bom)하기 위해 Linux 유형 명령을 실행 하는 시스템입니다. `yamllint`시스템에 및 명령을 설치 `ansible-lint` 합니다.

## <a name="scripted-creation-process"></a>스크립팅된 생성 프로세스

이 프로세스는 [수동 BOM 만들기 프로세스](#manual-creation-process)와 동일한 단계를 자동화 합니다. 이 프로세스를 사용 하기 전에 [스크립트 제한 사항을](#script-limitations) 검토 합니다.

1. Stack 파일 폴더로 이동 합니다.

    ```bash
    cd stackfiles
    ```

1. BOM 생성 스크립트를 실행 합니다. 예제 경로를 유틸리티 폴더의 올바른 경로로 바꿉니다. 다음은 그 예입니다.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/deploy/scripts/generate_bom.sh >../bom.yml
    ```

1. Product 매개 변수 ()에는 `product` SAP 제품 이름을 입력 합니다. 예: `SAP_S4HANA_1809_SP4`. 값을 입력 하지 않으면 스크립트에서 스택 XML 파일의 이름을 확인 하려고 합니다.

1. `bom.yml`검토를 위해 생성 된 파일을 엽니다.

1. 템플릿 섹션 ()을 검토 `templates` 합니다. `file`및 `override_target_location` 값이 올바른지 확인 합니다. 필요한 경우 해당 줄을 편집 하 고 주석 처리 합니다. 다음은 그 예입니다.

    ```yml
    templates:
      # - name:     "S4HANA_2020_ISS_v001 ini file"
      #   file:     S4HANA_2020_ISS_v001.inifile.params
      #   override_target_location: "{{ target_media_location }}/config"
    ```

1. 스택 파일 섹션 ()을 검토 `stackfiles` 합니다. 항목 이름 및 파일이 올바른지 확인 합니다. 필요한 경우 해당 줄을 편집 합니다.

## <a name="script-limitations"></a>스크립트 제한 사항

[스크립팅된 BOM 생성 프로세스](#scripted-creation-process) 에는 다음과 같은 제한 사항이 있습니다.

스크립팅은 HANA2에 대 한 하드 코드 된 종속성이 있습니다. 필요한 종속성 이름과 일치 하도록 BOM 파일을 수동으로 편집 합니다. 다음은 그 예입니다.

```yml
dependencies:
  - name: "HANA2"
```

미디어 매개 변수, 및에 대 한 기본값은 없습니다 `override_target_filename:` `override_target_location` `version:` . BOM 파일을 수동으로 편집 하 여 이러한 매개 변수를 변경 하십시오. 다음은 그 예입니다.

```yml
   - name:     SAPCAR
     archive:  SAPCAR_1320-80000935.EXE
     override_target_filename: SAPCAR.EXE

   - name: "SWPM20SP07"
     archive: "SWPM20SP07_2-80003424.SAR"
     override_target_filename: SWPM.SAR
     sapurl: "https://softwaredownloads.sap.com/file/0020000001812632020"
```

이 스크립트는 SAP Maintenance Planner에서 식별 하는 미디어 파일에 대 한 항목만 생성 합니다. 이러한 제한 사항은 스택 파일을 처리 하기 때문에 발생 합니다 `.xsl` . SAP 실행 패드를 통해 파일을 다운로드 바구니에 별도로 추가 하는 경우 [해당 파일을 BOM에 수동으로 추가](#manual-creation-process)해야 합니다.

## <a name="manual-creation-process"></a>수동 만들기 프로세스

다음 수동 프로세스를 통해 BOM을 만들 수 있습니다. 또 다른 옵션은 [스크립팅된 생성 프로세스](#scripted-creation-process) 를 사용 하 여 동일한 단계를 수행 하는 것입니다.

1. [SAP 미디어를 얻기](automation-bom-get-files.md#acquire-media) 위해 만든 다운로드 폴더를 엽니다.

1. 이라는 빈 YAML 파일을 만듭니다 `bom.yml` .

1. `bom.yml`편집기에서를 엽니다.

1. 빌드 및 대상에 대 한 이름이 있는 BOM 헤더를 추가 합니다. `name`값은 저장소 계정의 BOM 폴더 이름과 동일 해야 합니다. 다음은 그 예입니다.

    ```yml
    name:    'S4HANA_2020_ISS_v001'
    target:  'ABAP PLATFORM 2020'
    ```

1. 대상 위치에 기본값 섹션을 추가 합니다. 대상 서버에서 설치 파일을 복사 하려는 폴더의 경로를 사용 합니다. 일반적으로 다음과 같이를 사용 합니다 `{{ target_media_location }}` .

    ```yml
    defaults:
      target_location: "{{ target_media_location }}/download_basket"
    ```

1. 제품 식별자 섹션을 추가 합니다. 이러한 값은 나중에 템플릿 준비의 일부로 채웁니다. 다음은 그 예입니다.

    ```yml
    product_ids:
      scs:
      db:
      pas:
      aas:
      web:
    ```

1. 재질 섹션을 추가 하 여 필요한 재질 목록을 지정 합니다. 이 섹션에서 다른 Bom에 대 한 종속성을 추가 합니다. 다음은 그 예입니다.

    ```yml
    materials:
    dependencies:
        - name:     HANA2
    ```

1. BOM에 포함할 미디어 목록을 가져옵니다.

    1. 다운로드 바구니 스프레드시트를 엽니다. 이 파일은 XML로 렌더링 됩니다.

    1. 필요한 경우 XML 콘텐츠를 사람이 읽을 수 있는 형식으로 지정 합니다.

    1. 다운로드 바구니의 각 항목에 대해 `String` 및 데이터를 확인 `Number` 합니다. `String`데이터는 파일 이름 (예: `igshelper_17-10010245.sar` ) 및 친숙 한 설명 (예:)을 제공 `SAP IGS Fonts and Textures` 합니다. `Number`BOM에서 각 항목 후에 데이터를 기록 합니다. 

1. 미디어 목록을에 추가 `bom.yml` 합니다. 그러나 이러한 항목의 순서는 중요 하지 않지만 가독성을 위해 관련 항목을 그룹화 하는 것이 좋습니다. `SAPCAR`SAP 다운로드 바구니에이 유틸리티가 포함 된 경우에도 별도로를 추가 합니다. 다음은 그 예입니다.

    ```yml
    media:
        - name:     SAPCAR
          archive:  SAPCAR_1320-80000935.EXE
    
        name: "SAP IGS Fonts and Textures"
          archive: "igshelper_17-10010245.sar"
          # 61489
    
        <...>
    ```

1. 필요에 따라 대상 미디어 위치를 재정의 해야 하는 경우 미디어 항목에 매개 변수를 추가 `override_target_location` 합니다. 예: `override_target_location: "{{ target_media_location }}/config"`.

1. 빈 템플릿 섹션을 추가 합니다.

    ```yml
    templates:
    ```

1. 스택 파일 섹션을 만듭니다. 다음은 그 예입니다.

    ```yml
    stackfiles:
      - name: Download Basket JSON Manifest
         file: downloadbasket.json
    
      - name: Download Basket Spreadsheet
         file: MP_Excel_2001017452_20201030_SWC.xls
    ```

1. 변경 내용을에 저장 `bom.yml` 합니다.

### <a name="permalinks"></a>Permalinks

기능 하는 기본 BOM을 자동으로 생성할 수 있습니다. 그러나 BOM은 기본적으로 SAP 미디어에 영구 Url (permalinks)을 만들지 않습니다. Permalinks를 만들려면 [SAP 미디어를 얻기](automation-bom-get-files.md#acquire-media)전에 추가 단계를 수행 해야 합니다. 

> [!NOTE]
> Permalinks를 사용 하는 전체 SAP BOM의 수동 생성은 [수동으로 기본 BOM을 준비](#manual-creation-process)하는 동안 두 배의 시간이 걸립니다. 

Permalinks를 사용 하 여 BOM을 생성 하려면:

1. `DownloadBasket.json`편집기에서를 엽니다.

1. 각 결과에 대해 줄의 내용을 확인 합니다 `Value` . 다음은 그 예입니다.

    ```json
         "Value": "0020000000703122018|SP_B|SAP IGS Fonts and Textures|61489|1|20201023150931|0"
    ```

1. 세로 막대로 구분된 첫 번째 및 네 번째 값을 복사합니다.

    1. 첫 번째 값은 파일 번호입니다. 예: `0020000000703122018`.

    1. 네 번째 값은 미디어 목록과 일치시키기 위해 사용할 숫자입니다. 예: `61489`.

    1. 필요에 따라 파일 형식을 나타내는 두 번째 값을 복사합니다. 예를 들어 `SP_B` 커널 이진 파일의 경우 , `SPAT` 커널이 아닌 이진 파일의 경우 및 `CD` 데이터베이스 내보내기용입니다.

1. 네 번째 값을 키로 사용하여 다운로드 장바구니를 미디어 목록에 일치시킬 수 있습니다. 값(예: `61489` )을 미디어 항목에 대한 주석으로 추가한 값(예: `# 61489` )과 일치합니다.

1. 의 일치하는 각 항목에 `bom.yml` 대해 SAP URL에 대한 새 값을 추가합니다. URL의 경우 `https://softwaredownloads.sap.com/file/` 해당 항목에 세 번째 값(예: )을 더한 값을 `0020000000703122018` 사용합니다. 다음은 그 예입니다.

    ```yml
    - name: "SAP IGS Fonts and Textures"
      archive: "igshelper_17-10010245.sar"
      sapurl: "https://softwaredownloads.sap.com/file/0020000000703122018"
    ```

## <a name="example-bom-file"></a>예제 BOM 파일

다음 샘플은 S/4HANA 1909 SP2에 대한 예제 BOM 파일의 작은 부분입니다. 

[GitHub 리포지토리](https://github.com/Azure/sap-automation/deploy/ansible/BOM-catalog) 폴더에서 사용 가능한 전체 BOM 파일을 여러 개 찾을 수 있습니다.

```yml
step|BOM Content

---

name:    'S4HANA_2020_ISS_v001'
target:  'ABAP PLATFORM 2020'

defaults:
  target_location: "{{ target_media_location }}/download_basket"

product_ids:
  scs:
  db:
  pas:
  aas:
  web:

materials:
dependencies:
    - name:     HANA2

media:
    - name:     SAPCAR
      archive:  SAPCAR_1320-80000935.EXE

    - name:     SWPM
      archive:  SWPM20SP06_6-80003424.SAR

    - name:     SAP IGS HELPER
      archive:  igshelper_17-10010245.sar

    - name:     SAP HR 6.08
      archive:  SAP_HR608.SAR

    - name:     S4COREOP 104
      archive:  S4COREOP104.SAR

templates:
    - name:     "S4HANA_2020_ISS_v001 ini file"
      file:     S4HANA_2020_ISS_v001.inifile.params
      override_target_location: "{{ target_media_location }}/config"

stackfiles:
    - name: Download Basket JSON Manifest
      file: downloadbasket.json
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Spreadsheet
      file: MP_Excel_2001017452_20201030_SWC.xls
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Plan doc
      file: MP_Plan_2001017452_20201030_.pdf
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Stack text
      file: MP_Stack_2001017452_20201030_.txt
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket Stack XML
      file: MP_Stack_2001017452_20201030_.xml
      override_target_location: "{{ target_media_location }}/config"

    - name: Download Basket permalinks
      file: myDownloadBasketFiles.txt
      override_target_location: "{{ target_media_location }}/config"
```

## <a name="validate-bom"></a>BOM 유효성 검사

Linux 형식 명령을 실행하는 모든 OS에서 BOM 구조의 유효성을 검사할 수 있습니다. Windows 경우 WSL(Linux용 Windows 하위 시스템)을 사용합니다. 또 다른 옵션은 BOM 파일의 복사본이 있는 경우 배포자에서 유효성 검사를 실행하는 것입니다.


1. `check_bom.sh`BOM이 포함된 디렉터리에서 유효성 검사 스크립트를 실행합니다. 다음은 그 예입니다.

    ```bash
    cd ~/Azure_SAP_Automated_Deployment/deploy/scripts/check_bom.sh bom.yml
    ```

1. 출력을 검토합니다. 

### <a name="successful-validation"></a>성공적인 유효성 검사

유효성 검사가 성공적이면 다음 출력이 표시됩니다. 및 명령은 필수 구성 조건 에 이미 `yamllint` `ansible-lint` [설치되어 있습니다.](#prerequisites) 

```output
... yamllint [ok]
... ansible-lint [ok]
... bom structure [ok]
```

### <a name="unsuccessful-validation"></a>유효성 검사 실패

실패한 유효성 검사에는 오류 정보가 포함됩니다. 다음은 그 예입니다.

```output
../documentation/ansible/system-design-deployment/examples/S4HANA_2020_ISS_v001/bom_with_errors.yml
  178:16    error    too many spaces after colon  (colons)
  179:16    error    too many spaces after colon  (colons)
  180:16    error    too many spaces after colon  (colons)
    
... yamllint [errors]
... ansible-lint [ok]
  - Expected to find key 'defaults' in 'bom' (Check name: S4HANA_2020_ISS_v001)
  - Unexpected key 'default in 'bom' (Check name: S4HANA_2020_ISS_v001)
  - Unexpected key 'overide_target_location in 'bom.materials.stackfiles' (Check name: Download Basket Stack text)
... bom structure [errors]
```

## <a name="upload-your-bom"></a>BOM 업로드

permalinks와 함께 BOM을 사용하려면 다음을 수행합니다.

1. [BOM 의 유효성을 검사합니다.](#validate-bom)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Azure 서비스** 아래에서 **리소스 그룹** 을 선택합니다. 또는 `resource groups` 검색 표시줄에 를 입력합니다. 

1. SAP 라이브러리에 대한 리소스 그룹을 선택합니다.

1. 리소스 그룹 페이지의 리소스 테이블에서 스토리지 계정을 `saplib` 선택합니다. 

1. 스토리지 계정 페이지의 메뉴에서 **데이터 스토리지** 아래의 **컨테이너를** 선택합니다.

1. `sap bits`컨테이너를 선택합니다.

1. 컨테이너 페이지에서 보관 및 도구를 업로드합니다.

    1. **업데이트** 단추를 선택합니다.

    1. **파일 선택을 선택합니다.**

    1. 이전에 만든 다운로드 디렉터리로 이동합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SAP 애플리케이션 BOM을 생성하는 방법](automation-bom-templates-db.md)
