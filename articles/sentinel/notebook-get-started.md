---
title: Microsoft 센티널에서 Jupyter 노트북 및 MSTICPy 시작 하기
description: MSTICPy 및 쿼리를 사용 하 여 microsoft 센티널 노트북의 기본 사항을 배울 수 있도록 microsoft 센티널 ML 노트북에 대 한 시작 가이드를 안내 합니다.
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 8b21cd4417969b5ba3c816c6f950b8d4e9779391
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133302622"
---
# <a name="tutorial-get-started-with-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>자습서: Microsoft 센티널에서 Jupyter 노트북 및 MSTICPy 시작 하기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 자습서에서는 microsoft 센티널에서 jupyter 노트북을 실행 하기 위한 기본 구성을 설정 하 고 간단한 데이터 쿼리를 실행 하는 **microsoft 센티널 ML 전자 필기장에 대 한 시작 가이드** 를 실행 하는 방법을 설명 합니다.

**microsoft 센티널 ML 노트북에 대 한 시작 가이드** 에서는 microsoft에서 빌드된 사이버 보안 tools의 Python 라이브러리인 MSTICPy를 사용 하 여 위협 요소 구하기 및 조사 기능을 제공 합니다.

MSTICPy는 고객이 Microsoft 센티널에 대해 작성 해야 하는 코드의 양을 줄이고 다음을 제공 합니다.

- Microsoft 센티널 테이블, Microsoft Defender for Endpoint, Splunk 및 기타 데이터 원본에 대 한 데이터 쿼리 기능
- VirusTotal 및 AlienVault \X와 같은 TI 공급자를 사용한 위협 인텔리전스 조회
- 보강는 IP 주소의 지리적 위치, IoC (손상 표시기) 추출 및 WhoIs 조회와 같은 기능을 합니다.
- 이벤트 타임 라인, 프로세스 트리 및 지역 매핑을 사용 하는 시각화 도구입니다.
- 시계열 분해, 변칙 검색 및 클러스터링과 같은 고급 분석

이 자습서의 단계에서는 microsoft 센티널을 통해 Azure ML 작업 영역에서 **microsoft 센티널 ML 노트북에 대 한 시작 가이드** 를 실행 하는 방법을 설명 합니다. 또한이 자습서를 사용 하 여 로컬을 비롯 한 다른 환경에서 노트북을 실행 하는 유사한 단계를 수행할 수 있습니다.

자세한 내용은 [노트북을 사용 하 여 전원 조사](hunting.md#use-notebooks-to-power-investigations) 및 [Jupyter 노트북](notebooks.md)을 사용 하 여 보안 위협 구하기를 참조 하세요.

> [!NOTE]
> 여러 Microsoft 센티널 노트북은 **자격 증명 스캐너** 노트북과 같은 MSTICPy 또는 PowerShell 및 c # 예제를 사용 하지 않습니다. MSTICpy를 사용 하지 않는 노트북에는이 문서에서 설명 하는 MSTICPy 구성이 필요 하지 않습니다.
>

## <a name="prerequisites"></a>사전 요구 사항

- Microsoft 센티널에서 노트북을 사용 하려면 필요한 권한이 있는지 확인 합니다. 자세한 내용은 [Microsoft 센티널 노트북에 대 한 액세스 관리](notebooks.md#manage-access-to-microsoft-sentinel-notebooks)를 참조 하세요.

- 이 자습서의 단계를 수행 하려면 Python 3.6 이상이 필요 합니다. Azure ML python 3.8 커널 (권장) 또는 python 3.6 커널을 사용할 수 있습니다.

- 이 노트북은 IP 주소에 대해 [Maxmind GeoLite2](https://www.maxmind.com) 지리적 위치 조회 서비스를 사용 합니다. MaxMind GeoLite2 서비스를 사용 하려면 계정 키가 필요 합니다. [Maxmind 등록 페이지](https://www.maxmind.com/en/geolite2/signup)에서 무료 계정 및 키를 등록할 수 있습니다.

- 이 노트북은 [VirusTotal](https://www.virustotal.com) (VT)를 위협 인텔리전스 원본으로 사용 합니다. VirusTotal 위협 인텔리전스 조회를 사용 하려면 VirusTotal 계정 및 API 키가 필요 합니다.

    [VirusTotal 시작 페이지](https://developers.virustotal.com/v3.0/reference#getting-started)에서 무료 VT 계정을 등록할 수 있습니다. 이미 VirusTotal 사용자 인 경우 기존 키를 사용할 수 있습니다.

    > [!WARNING]
    > VT enterprise 키를 사용 하는 경우 **msticpyconfig** 파일 대신 Azure Key Vault에 저장 합니다. 자세한 내용은 MSTICPY 설명서에서 [비밀을 Key Vault 암호로 지정](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets) 을 참조 하세요.
    >
    > 지금 Azure Key Vault를 설정 하지 않으려면 Key Vault 저장소를 설정할 때까지 무료 계정을 등록 하 고 사용 합니다.

## <a name="run-and-initialize-the-getting-started-guide-notebook"></a>시작 가이드 노트 실행 및 초기화

이 절차에서는 노트북을 시작 하 고 MSTICpy를 초기화 하는 방법을 설명 합니다.

1. Microsoft 센티널의 왼쪽에서 **노트북** 을 선택 합니다.

1. **템플릿** 탭에서 **Microsoft 센티널 용 시작 가이드 ML 노트북**  >  **저장 노트북** 을 선택 하 여 Azure ML 작업 영역에 저장 합니다.

    노트북 **시작** 을 선택 하 여 노트북을 실행 합니다. 노트북에는 일련의 셀이 포함 되어 있습니다.

    - *Markdown* 셀에는 전자 필기장 사용 지침과 함께 텍스트 및 그래픽이 포함 됩니다.
    - *코드* 셀에는 노트북 기능을 수행 하는 실행 코드가 포함 됩니다.

    **코드 셀 읽기 및 실행**

    코드 셀을 순서 대로 읽고 실행 합니다. 셀을 건너뛰거나 순서를 벗어난 항목을 실행 하면 노트북에서 나중에 오류가 발생할 수 있습니다.

    각 셀의 왼쪽에 있는 재생 단추를 선택 하 여 각 셀을 실행 합니다. 수행 되는 함수에 따라 셀의 코드를 매우 빠르게 실행 하거나 완료 하는 데 몇 초 정도 걸릴 수 있습니다.

    실행 하는 경우 재생 단추가 로드 회전자로 변경 되 고, 상태는 `Executing` 경과 된 시간과 함께 셀의 아래쪽에 표시 됩니다.

    > [!TIP]
    > 설명 된 대로 노트북이 작동 하지 않는 것으로 보이는 경우 커널을 다시 시작 하 고 처음부터 노트북을 실행 합니다. 예를 들어 **시작 가이드** 노트북의 모든 셀을 실행 하는 데 1 분 이상 걸리면 커널을 다시 시작 하 고 노트북을 다시 실행 해 봅니다.
    >
    > **시작 가이드** 노트북은 jupyter 커널을 다시 시작 하는 것을 포함 하 여 jupyter 노트북의 기본 사용에 대 한 지침을 제공 합니다.
    >

    **Jupyter Notebook** 섹션의 셀 읽기 및 실행을 완료 한 후에는 **노트북 환경 설정** 섹션에서 시작 하 여 구성 작업을 시작할 준비가 된 것입니다.

1. 노트북의 **노트북 환경 설정** 섹션에서 첫 번째 코드 셀을 실행 합니다. 여기에는 다음 코드가 포함 됩니다.

    ```python
    # import some modules needed in this cell
    from pathlib import Path
    from IPython.display import display, HTML

    REQ_PYTHON_VER="3.6"
    REQ_MSTICPY_VER="1.2.3"

    display(HTML("Checking upgrade to latest msticpy version"))
    %pip install --upgrade --quiet msticpy[azuresentinel]>=$REQ_MSTICPY_VER

    # intialize msticpy
    from msticpy.nbtools import nbinit
    nbinit.init_notebook(
    namespace=globals(),
    extra_imports=["urllib.request, urlretrieve"]
    )
    pd.set_option("display.html.table_schema", False)
    ```

    초기화 상태는 출력에 표시 됩니다. 아직 아무것도 구성 하지 않았기 때문에 파일의 누락 된 설정에 대 한 구성 경고가 `Missing msticpyconfig.yaml` 예상 됩니다.

> [!NOTE]
> 대부분의 Microsoft 센티널 노트북은 다음을 수행 하는 MSTICpy 초기화 셀로 시작 합니다.
>
> - 노트북에 필요한 Python 및 MSTICPy의 최소 버전을 정의 합니다.
> - 최신 버전의 MSTICPy가 설치 되어 있는지 확인 합니다.
> - 함수를 가져오고 실행 `init_notebook` 합니다.
>

## <a name="create-your-configuration-file"></a>구성 파일 만들기

기본 초기화 후에는 MSTICPy 작업에 대 한 기본 설정을 사용 하 여 구성 파일을 만들 준비가 되었습니다.

많은 Microsoft 센티널 노트북은 [VirusTotal](https://www.virustotal.com) (VT)와 같은 외부 서비스에 연결 하 여 데이터를 수집 하 고 보강 합니다. 이러한 서비스에 연결 하려면 인증 토큰과 같은 구성 세부 정보를 설정 하 고 저장 해야 합니다. 구성 파일에이 데이터가 있으면 노트북을 사용할 때마다 인증 토큰 및 작업 영역 세부 정보를 입력 하지 않아도 됩니다.

MSTICPy는 다양 한 구성 정보를 저장 하는 데 **msticpyconfig** 를 사용 합니다.  기본적으로 **msticpyconfig** 파일은 노트북 초기화 기능을 통해 생성 됩니다. [Microsoft 센티널 포털에서이 노트북을 복제](#run-and-initialize-the-getting-started-guide-notebook)하는 경우 구성 파일이 microsoft 센티널 작업 영역 데이터로 채워집니다. 이 데이터는 노트북을 시작할 때 Azure ML 작업 영역에 생성 되는 **구성 json** 파일에서 읽습니다. 자세한 내용은 [MSTICPy 패키지 구성 설명서](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)를 참조 하세요.

다음 섹션에서는 **msticpyconfig** 파일에 구성 세부 정보를 추가 하는 방법에 대해 설명 합니다.

> [!NOTE]
> *시작 가이드* 노트북을 다시 실행 하 고 이미 최소 구성 **msticpyconfig** 파일이 있는 경우이 `init_notebook` 함수는 기존 파일을 덮어쓰거나 수정 하지 않습니다.
>

> [!TIP]
> 언제 든 지 MSTICPy 구성 도구에서 **-Help** 드롭다운 메뉴를 선택 하 여 자세한 지침과 자세한 설명서에 대 한 링크를 제공 합니다.
>

### <a name="display-the-msticpy-settings-editor"></a>MSTICPy 설정 편집기를 표시 합니다.

1. 코드 셀에서 다음 코드를 실행 하 여 도구를 가져오고 `MpConfigEdit` **msticpyconfig** 파일에 대 한 설정 편집기를 표시 합니다.

    ```python
    from msticpy.config import MpConfigEdit

    mpedit = MpConfigEdit( "msticpyconfig.yaml")
    mpedit.set_tab("AzureSentinel")
    display(mpedit)
    ```

    예:

    :::image type="content" source="media/notebook-get-started/msticpy-editor.png" alt-text="MSTICPy 설정 편집기의 스크린샷":::

    설정 편집기에 표시 되는 자동으로 생성 된 **msticpyconfig** 파일에는 Microsoft 센티널 섹션에 두 개의 항목이 포함 되어 있습니다. 이러한 정보는 모두 노트북이 복제 된 Microsoft 센티널 작업 영역에 대 한 세부 정보로 채워집니다. 한 항목에는 작업 영역의 이름이 있고 다른 항목에는 **기본** 이름이 지정 됩니다.

    MSTICPy를 사용 하면 여러 Microsoft 센티널 작업 영역에 대 한 구성을 저장 하 고 둘 사이를 전환할 수 있습니다. **기본** 항목을 사용 하면 명시적으로 이름을 지정할 필요 없이 "홈" 작업 영역에 대 한 인증을 기본적으로 수행할 수 있습니다. 작업 영역을 추가 하는 경우 해당 작업 영역 중 하나를 **기본** 항목으로 구성할 수 있습니다.

    > [!NOTE]
    > Azure ML 환경에서 설정 편집기를 표시 하는 데 10-20 초가 걸릴 수 있습니다.

1. 현재 설정을 확인 하 고 **설정 저장** 을 선택 합니다.

### <a name="add-threat-intelligence-provider-settings"></a>위협 인텔리전스 공급자 설정 추가

이 절차에서는 [VIRUSTOTAL API 키](#prerequisites) 를 **msticpyconfig** 파일에 저장 하는 방법에 대해 설명 합니다. Azure Key Vault에 API 키를 업로드 하도록 선택할 수 있지만 Key Vault 설정을 먼저 구성 해야 합니다. 자세한 내용은 [Key Vault 설정 구성](#configure-key-vault-settings)을 참조 하세요.

**MSTICPy 설정 편집기에서 VirusTotal Details를 추가 하려면 다음을 수행 합니다**.

1. 코드 셀에 다음 코드를 입력 하 고를 실행 합니다.

   ```python
   mpedit.set_tab("TI Providers")
   mpedit
   ```

1. **TI 공급자** 탭에서 **add prov**  >  **VirusTotal**  >  **add** 를 선택 합니다.

1. **Auth Key** 에서 **Storage** 옵션 옆에 있는 **텍스트** 를 선택 합니다.

1. **값** 필드에 API 키를 붙여넣습니다.

1. **업데이트** 를 선택 하 고 설정 편집기의 맨 아래에 있는 **설정 저장** 을 선택 합니다.

> [!TIP]
> 지원 되는 다른 위협 인텔리전스 공급자에 대 한 자세한 내용은 MSTICPy 설명서의 [위협 인텔리전스 공급자](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html) 및 [Microsoft 센티널의 위협 인텔리전스 통합](threat-intelligence-integration.md)을 참조 하세요.
>
### <a name="add-geoip-provider-settings"></a>GeoIP 공급자 설정 추가

이 절차에서는 노트북에서 IP 주소에 대 한 지리적 위치 조회 서비스를 사용할 수 있도록 하는 [Maxmind GeoLite2 계정 키](#prerequisites) 를 **msticpyconfig** 파일에 저장 하는 방법에 대해 설명 합니다.

**MSTICPy 설정 편집기에서 GeoIP 공급자 설정을 추가 하려면 다음을 수행 합니다**.

1. 빈 코드 셀에 다음 코드를 입력 하 고를 실행 합니다.

   ```python
   mpedit.set_tab("GeoIP Providers")
   mpedit
   ```

1. **Geoip 공급자** 탭에서 **add prov**  >  **geoiplite**  >  **add** 를 선택 합니다.

1. **값** 필드에 maxmind 계정 키를 입력 합니다.

1. 필요한 경우 다운로드 한 GeoIP 데이터베이스를 저장 하는 기본 **~/.msticpy** 폴더를 업데이트 합니다.

    - Windows에서이 폴더는 **% USERPROFILE%/.msticpy** 에 매핑됩니다.
    - Linux 또는 macOS에서 이 경로는 홈 폴더의 **.msticpy** 폴더에 매핑됩니다.

> [!TIP]
> 지원되는 다른 지리적 위치 조회 서비스에 대한 자세한 내용은 [MSTICPy GeoIP 공급자 설명서를 참조하세요.](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html)
>

### <a name="configure-azure-cloud-settings"></a>Azure 클라우드 설정 구성

조직에서 Azure 퍼블릭 클라우드를 사용하지 않는 경우 Microsoft Sentinel 및 Azure의 데이터를 성공적으로 인증하고 사용하려면 설정에서 이를 지정해야 합니다. 자세한 내용은 [Azure 클라우드 및 기본 Azure 인증 방법 지정을 참조하세요.](#specify-the-azure-cloud-and-azure-authentication-methods)

### <a name="validate-settings"></a>설정 유효성 검사

설정 편집기에서 **설정 유효성 검사를** 선택합니다.

누락된 구성에 대한 경고 메시지가 예상되지만 위협 인텔리전스 공급자 또는 GeoIP 공급자 설정에 대한 경고 메시지가 없어야 합니다.

사용자 환경에 따라 [Key Vault 설정을 구성하거나](#configure-key-vault-settings) [Azure 클라우드를 지정해야](#specify-the-azure-cloud-and-azure-authentication-methods)할 수도 있습니다.

유효성 검사로 인해 변경해야 하는 경우 변경한 다음 설정 **저장을** 선택합니다.

완료되면 **닫기** 단추를 선택하여 유효성 검사 출력을 숨깁니다.

자세한 내용은 [Jupyter Notebook에 대한 고급 구성 및 Microsoft Sentinel의 MSTICPy를 참조하세요.](notebooks-msticpy-advanced.md)

## <a name="load-saved-msticpy-settings"></a>저장된 MSTICPy 설정 로드

구성 [파일 만들기](#create-your-configuration-file) 절차에서 설정을 로컬 **msticpyconfig.yaml** 파일에 저장했습니다.

그러나 MSTICPy는 커널을 다시 시작하거나 다른 Notebook을 실행할 때까지 이러한 설정을 자동으로 다시 로드하지 않습니다. MSTICPy가 새 구성 파일에서 다시 로드하도록 하려면 다음 코드와 함께 다음 코드 셀로 진행하여 실행합니다.

```python
import msticpy
msticpy.settings.refresh_config()
```

## <a name="test-your-notebook"></a>Notebook 테스트

이제 환경을 초기화하고 작업 영역에 대한 기본 설정을 구성했으므로 MSTICPy 클래스를 사용하여 `QueryProvider` Notebook을 테스트합니다. `QueryProvider`  는 데이터 원본(이 경우 Microsoft Sentinel 작업 영역)을 쿼리하고 쿼리된 데이터를 Notebook에서 보고 분석할 수 있도록 합니다.

다음 절차에 따라 클래스의 인스턴스를 `QueryProvider` 만들고, Notebook에서 Microsoft Sentinel에 인증하고, 다양한 매개 변수 옵션을 사용하여 쿼리를 보고 실행합니다.

> [!TIP]
> 여러 `QueryProvider` Microsoft Sentinel 작업 영역 또는 엔드포인트용 Microsoft Defender와 같은 다른 데이터 공급자와 함께 사용할 수 있도록 여러 인스턴스가 로드될 수 있습니다.
>

### <a name="load-the-queryprovider"></a>QueryProvider 로드

에 대한 를 로드하려면  `QueryProvider` `AzureSentinel` 다음 코드를 사용하여 셀로 진행하여 실행합니다.

```python
# Initialize a QueryProvider for Microsoft Sentinel
qry_prov = QueryProvider("AzureSentinel")
```

> [!NOTE]
> Microsoft Sentinel 드라이버를 로드할 때 경고가 표시되는 경우 `Runtime dependency of PyGObject is missing` [오류: *PyGObject의 런타임 종속성이 누락됨을*](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/%22Runtime-dependency-of-PyGObject-is-missing%22-error)참조하세요.
이 경고는 Notebook 기능에 영향을 미치지 않습니다.
>

### <a name="authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook"></a>Notebook에서 Microsoft Sentinel 작업 영역에 인증

Azure 자격 증명으로 디바이스 권한 [부여를](../active-directory/develop/v2-oauth2-device-code.md) 사용하여 Microsoft Sentinel 작업 영역에 인증합니다.

디바이스 권한 부여는 인증 프로세스의 일부로 제공하는 일회성 디바이스 코드를 생성하여 인증에 또 다른 요소를 추가합니다.

**디바이스 권한 부여를 사용하여 인증하려면:**

1. 다음 코드 셀을 실행하여 디바이스 코드를 생성하고 표시합니다.

   ```python
   # Get the Microsoft Sentinel workspace details from msticpyconfig
   # Loading WorkspaceConfig with no parameters uses the details
   # of your Default workspace
   # If you want to connect to a specific workspace use this syntax:
   #    ws_config = WorkspaceConfig(workspace="WorkspaceName")
   # ('WorkspaceName' should be one of the workspaces defined in msticpyconfig.yaml)
   ws_config = WorkspaceConfig()

   # Connect to Microsoft Sentinel with your QueryProvider and config details
   qry_prov.connect(ws_config)
   ```

    예:

    :::image type="content" source="media/notebook-get-started/device-authorization.png" alt-text="디바이스 권한 부여 코드를 보여주는 스크린샷.":::

1. 표시된 코드를 선택하고 클립보드에 복사합니다. 그런 다음, 로 이동하여 [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) 메시지가 표시되는 위치에 코드를 붙여넣습니다.

1. 로그인했다는 확인 메시지가 표시되면 브라우저 탭을 닫고 Microsoft Sentinel의 Notebook으로 돌아갑니다.

   다음과 유사한 출력이 Notebook에 표시됩니다.

   :::image type="content" source="media/notebook-get-started/authorization-complete.png" alt-text="디바이스 권한 부여 프로세스가 완료된 것을 보여 주는 스크린샷":::

**Azure CLI 사용하여 로그인 토큰 캐시**

커널을 다시 시작하거나 다른 Notebook을 실행하는 경우 다시 인증할 필요가 없도록 하려면 Azure CLI 사용하여 로그인 토큰을 캐시하면 됩니다.

Compute 인스턴스의 Azure CLI 구성 요소는 새로 *고침 토큰을* 캐시합니다. 이 토큰은 토큰 시간이 부족할 때까지 다시 사용할 수 있습니다. MSTICPy는 사용할 수 있는 경우 Azure CLI 자격 증명을 자동으로 사용합니다.

Azure CLI 사용하여 인증하려면 빈 셀에 다음을 입력하고 실행합니다.

```python
!az login
```

> [!NOTE]
> Compute 인스턴스를 다시 시작하거나 다른 인스턴스로 전환하는 경우 다시 인증해야 합니다. 자세한 내용은 Microsoft Sentinel Notebooks GitHub 리포지토리 wiki에서 Azure CLI [사용하여 자격 증명 캐싱](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/Caching-credentials-with-Azure-CLI) 섹션을 참조하세요.
>

### <a name="view-the-microsoft-sentinel-workspace-data-schema-and-built-in-msticpy-queries"></a>Microsoft Sentinel 작업 영역 데이터 스키마 및 기본 제공 MSTICPy 쿼리 보기

Microsoft Sentinel QueryProvider에 연결한 후에는 Microsoft Sentinel 작업 영역 데이터 스키마를 쿼리하여 쿼리에 사용할 수 있는 데이터 형식을 이해할 수 있습니다.

Microsoft Sentinel QueryProvider에는 `schema_tables` 스키마 테이블 목록을 제공하는 속성과 각 테이블의 `schema` 열 이름 및 데이터 형식을 포함하는 속성이 있습니다.

**Microsoft Sentinel 스키마에서 처음 10개 테이블을 보려면:**

다음 코드를 사용하여 다음 셀로 진행하여 실행합니다. `[:10]`을 생략하여 작업 영역의 모든 테이블을 나열할 수 있습니다.

```python
# Get list of tables in the Workspace with the 'schema_tables' property
qry_prov.schema_tables[:10]  # Output only a sample of tables for brevity
                             # Remove the "[:10]" to see the whole list
```

다음과 같은 출력이 표시됩니다.

```output
Sample of first 10 tables in the schema
    ['AACAudit',
     'AACHttpRequest',
     'AADDomainServicesAccountLogon',
     'AADDomainServicesAccountManagement',
     'AADDomainServicesDirectoryServiceAccess',
     'AADDomainServicesLogonLogoff',
     'AADDomainServicesPolicyChange',
     'AADDomainServicesPrivilegeUse',
     'AADDomainServicesSystemSecurity',
     'AADManagedIdentitySignInLogs']
```

MSTICPy에는 실행할 수 있는 여러 기본 제공 쿼리도 포함되어 있습니다. 를 통해 사용 가능한 쿼리를 `.list_queries()` 나열하고, 매개 변수로 포함된 물음표( )를 호출하여 쿼리에 대한 특정 세부 정보를 `?` 얻습니다. 또는 쿼리 브라우저에서 쿼리 목록 및 관련 도움말을 볼 수 있습니다.

**사용 가능한 쿼리 샘플을 보려면:**

다음 코드를 사용하여 다음 셀로 진행하여 실행합니다. 생략할 수 있습니다는 `[::5]` 모든 쿼리를 나열 합니다.

```python
# Get a sample of available queries
print(qry_prov.list_queries()[::5])  # showing a sample - remove "[::5]" for whole list
```

다음과 같은 출력이 표시됩니다.

```output
Sample of queries
=================
['Azure.get_vmcomputer_for_host', 'Azure.list_azure_activity_for_account', 'AzureNetwork.az_net_analytics', 'AzureNetwork.get_heartbeat_for_ip', 'AzureSentinel.get_bookmark_by_id', 'Heartbeatget_heartbeat_for_host', 'LinuxSyslog.all_syslog', 'LinuxSyslog.list_logon_failures', 'LinuxSyslog.sudo_activity', 'MultiDataSource.get_timeseries_decompose', 'Network.get_host_for_ip','Office365.list_activity_for_ip', 'SecurityAlert.list_alerts_for_ip', 'ThreatIntelligence.list_indicators_by_filepath', 'WindowsSecurity.get_parent_process', 'WindowsSecurity.list_host_events','WindowsSecurity.list_hosts_matching_commandline', 'WindowsSecurity.list_other_events']
```

**`?` 를 매개 변수로 전달하여 쿼리에 대한 도움말을 얻으려면:**

```python
# Get help about a query by passing "?" as a parameter
qry_prov.Azure.list_all_signins_geo("?")
```

다음과 같은 출력이 표시됩니다.

```output
Help for 'list_all_signins_geo' query
=====================================
Query:  list_all_signins_geo
Data source:  AzureSentinel
Gets Signin data used by morph charts

Parameters
----------
add_query_items: str (optional)
    Additional query clauses
end: datetime (optional)
    Query end time
start: datetime (optional)
    Query start time
    (default value is: -5)
table: str (optional)
    Table name
    (default value is: SigninLogs)
Query:
     {table} | where TimeGenerated >= datetime({start}) | where TimeGenerated <= datetime({end}) | extend Result = iif(ResultType==0, "Sucess", "Failed") | extend Latitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).latitude) | extend Longitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)
```

**스크롤 가능하고 필터링 가능한 목록에서 테이블과 쿼리를 모두 보려면:**

다음 코드를 사용하여 다음 셀로 진행하여 실행합니다.

```python
qry_prov.browse_queries()
```

선택한 쿼리의 경우 모든 필수 및 선택적 매개 변수가 쿼리의 전체 텍스트와 함께 표시됩니다. 예:

:::image type="content" source="media/notebook-get-started/view-tables-queries-in-list.png" alt-text="스크롤 가능하고 필터링 가능한 목록에 표시되는 테이블 및 쿼리의 스크린샷.":::

자세한 내용은 MSTICPy 설명서에서 [미리 정의된 쿼리 실행을](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-a-pre-defined-query) 참조하세요.

> [!NOTE]
> 브라우저에서 쿼리를 실행할 수는 없지만, 각 쿼리의 끝에 예제를 복사하여 붙여넣어 Notebook의 다른 곳에서 실행할 수 있습니다.
>

### <a name="run-queries-with-time-parameters"></a>시간 매개 변수를 통해 쿼리 실행

대부분의 쿼리에는 시간 매개 변수가 필요합니다. 날짜/시간 문자열은 입력하는 데 번거로울 수 있으며 여러 곳에서 수정하면 오류가 발생할 수 있습니다.

각 쿼리 공급자에는 쿼리에 대한 기본 시작 및 종료 시간 매개 변수가 있습니다. 이러한 시간 매개 변수는 시간 매개 변수가 호출 될 때마다 기본적으로 사용 합니다. 컨트롤을 열어 기본 시간 범위를 변경할 수 `query_time` 있습니다. 변경 내용은 다시 변경할 때까지 적용된 상태로 유지합니다.

다음 코드를 사용하여 다음 셀로 진행하여 실행합니다.

```python
# Open the query time control for your query provider
qry_prov.query_time
```

필요에 `start` 따라 및 `end` 시간을 설정합니다. 예:

:::image type="content" source="media/notebook-get-started/set-time-parameters.png" alt-text="쿼리에 대한 기본 시간 매개 변수를 설정하는 스크린샷":::

### <a name="run-a-query-using-the-built-in-time-range"></a>기본 제공 시간 범위를 사용하여 쿼리 실행

쿼리 결과는 스프레드시트 또는 데이터베이스 테이블과 같은 테이블 형식 데이터 구조인 [Pandas DataFrame](https://pandas.pydata.org)로 반환됩니다. [pandas 함수를](https://pandas.pydata.org/docs/user_guide/10min.html) 사용하여 쿼리 결과에 대한 추가 필터링 및 분석을 수행할 수 있습니다.

다음 코드 셀은 쿼리 공급자 기본 시간 설정을 사용하여 쿼리를 실행합니다. 이 범위를 변경하고 코드 셀을 다시 실행하여 새 시간 범위를 쿼리할 수 있습니다.

```python
# The time parameters are taken from the qry_prov time settings
# but you can override this by supplying explict "start" and "end" datetimes
signins_df = qry_prov.Azure.list_all_signins_geo()

# display first 5 rows of any results
# If there is no data, just the column headings display
signins_df.head()
```

출력에는 결과의 처음 5개 행이 표시됩니다. 예:

:::image type="content" source="media/notebook-get-started/run-query-with-built-in-time-range.png" alt-text="기본 제공 시간 범위가 있는 쿼리 실행의 스크린샷.":::

데이터가 없으면 열 머리글만 표시됩니다.

### <a name="run-a-query-using-a-custom-time-range"></a>사용자 지정 시간 범위를 사용하여 쿼리 실행

새 쿼리 시간 개체를 만들어 쿼리에 매개 변수로 전달할 수도 있습니다. 이 매개 변수를 사용하면 쿼리 공급자 기본값에 영향을 주지 않고 다른 시간 범위에 대해 일회성 쿼리를 실행할 수 있습니다.

```python
# Create and display a QueryTime control.
time_range = nbwidgets.QueryTime()
time_range
```

원하는 시간 범위를 설정한 후 쿼리 함수에 시간 범위를 전달하여 이전 코드와 별도의 셀에서 다음 코드를 실행할 수 있습니다.

```python
signins_df = qry_prov.Azure.list_all_signins_geo(time_range)
signins_df.head()
```

`start`및 매개 변수를 사용하여 datetime 값을 Python datetime 또는 날짜-시간 문자열로 전달할 수도 있습니다. `end`

```python
from datetime import datetime, timedelta
q_end = datetime.utc.now()
q_start = end – timedelta(5)
signins_df = qry_prov.Azure.list_all_signins_geo(start=q_start, end=q_end)
```

### <a name="customize-your-queries"></a>쿼리 사용자 지정

추가 쿼리 논리를 추가하여 기본 제공 쿼리를 사용자 지정하거나 함수를 사용하여 전체 쿼리를 실행할 수 `exec_query` 있습니다.

예를 들어 대부분의 기본 제공 쿼리는 `add_query_items` 쿼리에 필터 또는 기타 작업을 추가하기 위해 사용할 수 있는 매개 변수를 지원합니다.

1. 다음 코드 셀을 실행하여 경고 이름별로 경고 수를 요약하는 데이터 프레임을 추가합니다.

    ```python
    from datetime import datetime, timedelta

    qry_prov.SecurityAlert.list_alerts(
       start=datetime.utcnow() - timedelta(28),
        end=datetime.utcnow(),
        add_query_items="| summarize NumAlerts=count() by AlertName"
    )
    ```

1. 쿼리 공급자에 전체 KQL 쿼리 문자열을 전달합니다. 쿼리는 연결된 작업 영역에 대해 실행되고 데이터는 panda DataFrame으로 반환됩니다. 다음을 실행합니다.

    ```python
    # Define your query
    test_query = """
    OfficeActivity
    | where TimeGenerated > ago(1d)
    | take 10
    """

    # Pass the query to your QueryProvider
    office_events_df = qry_prov.exec_query(test_query)
    display(office_events_df.head())

    ```

자세한 내용은 다음을 참조하세요.

- [MSTICPy 쿼리 참조](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataQueries.html)
- [MSTICPy 미리 정의된 쿼리 실행](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-an-pre-defined-query)

### <a name="test-virustotal-and-geolite2"></a>VirusTotal 및 GeoLite2 테스트

**VirusTotal 데이터에서 IP 주소를 확인하려면:**

위협 인텔리전스를 사용하여 IP 주소가 VirusTotal 데이터에 표시되는지 확인하려면 다음 코드를 사용하여 셀을 실행합니다.

```python
# Create your TI provider – note you can re-use the TILookup provider (‘ti’) for
# subsequent queries - you don’t have to create it for each query
ti = TILookup()

# Look up an IP address
ti_resp = ti.lookup_ioc("85.214.149.236")

ti_df = ti.result_to_df(ti_resp)
ti.browse_results(ti_df, severities="all")
```

출력에는 결과에 대한 세부 정보가 표시됩니다. 예:

:::image type="content" source="media/notebook-get-started/test-virustotal-ip.png" alt-text="VirusTotal 데이터에 표시되는 IP 주소의 스크린샷.":::

전체 결과를 보려면 아래로 스크롤해야 합니다. 자세한 내용은 [MSTICPy의 위협 Intel 조회를 참조하세요.](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html)

**지리적 위치 IP 조회를 테스트하려면:**

MaxMind 서비스를 사용하여 IP 주소에 대한 지리적 위치 세부 정보를 얻으려면 다음 코드로 셀을 실행합니다.

```python
# create an instance of the GeoLiteLookup provider – this
# can be re-used for subsequent queries.
geo_ip = GeoLiteLookup()
raw_res, ip_entity = geo_ip.lookup_ip("85.214.149.236")
display(ip_entity[0])
```

출력에는 IP 주소에 대한 지리적 위치 정보가 표시됩니다. 예:

```output
ipaddress
{ 'AdditionalData': {},
  'Address': '85.214.149.236',
  'Location': { 'AdditionalData': {},
                'CountryCode': 'DE',
                'CountryName': 'Germany',
                'Latitude': 51.2993,
                'Longitude': 9.491,
                'Type': 'geolocation',
                'edges': set()},
  'ThreatIntelligence': [],
  'Type': 'ipaddress',
  'edges': set()}
```

> [!NOTE]
> 이 코드를 처음 실행하면 GeoLite 드라이버가 해당 데이터베이스를 다운로드하는 것을 볼 수 있습니다.
>

자세한 내용은 [MSTICPy GeoIP 공급자를 참조하세요.](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html)

## <a name="configure-key-vault-settings"></a>Key Vault 설정 구성

이 섹션은 Azure Key Vault 비밀을 저장하는 경우에만 관련이 있습니다.

Azure Key Vault 비밀을 저장하는 경우 [먼저 Azure 글로벌 KeyVault 관리 포털](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults)에서 Key Vault 만들어야 합니다.

필요한 설정은 자격 증명 모음 속성에서 얻는 모든 값이지만, 일부 설정은 다른 이름을 가질 수 있습니다. 예:

- **VaultName은** Azure Key Vault **속성** 화면의 왼쪽 위에 표시됩니다.
- **TenantId는** **디렉터리 ID로** 표시됩니다.
- **AzureRegion은** **위치로** 표시됩니다.
- **기관은** Azure 서비스에 대한 클라우드입니다.

자격 증명 모음에서 비밀을 검색하려면 **VaultName,** **TenantId** 및 **기관** 값만 필요합니다. MSTICPy에서 자격 증명 모음을 만들도록 선택하는 경우 다른 값이 필요합니다. 자세한 내용은 비밀 Key Vault [비밀 지정을 참조하세요.](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)

KeyRing 사용 옵션이 기본적으로 선택되어 있으며 로컬 **KeyRing에서** Key Vault 자격 증명을 캐시할 수 있습니다. 자세한 내용은 [KeyRing 설명서를 참조하세요.](https://keyring.readthedocs.io/en/latest/index.html)

> [!CAUTION]
> Notebook이 실행 중인 호스트 컴퓨팅을 완전히 신뢰하지 않는 경우 **KeyRing 사용** 옵션을 사용하지 마십시오.
>
> 이 경우 *컴퓨팅은* Notebook 커널이 실행되는 Jupyter 허브 서버이며, 브라우저가 실행 중인 컴퓨터가 아닐 수 있습니다. Azure ML 사용하는 경우 *컴퓨팅은* 선택한 Azure ML Compute 인스턴스가 됩니다. Keyring은 Notebook 커널이 실행되는 호스트에서 캐싱을 수행합니다.
>

**MSTICPy 설정 편집기에서 Key Vault 설정을 추가하려면:**

1. 다음 코드를 사용하여 다음 셀로 진행하여 실행합니다.

    ```python
    mpedit.set_tab("Key Vault")
    mpedit
    ```

1. Key Vault 대한 자격 증명 모음 세부 정보를 입력합니다. 예:

    :::image type="content" source="media/notebook-get-started/add-kv-settings.png" alt-text="Key Vault 설치 섹션의 스크린샷":::

1. **저장을** 선택한 **다음, 설정 저장을** 선택합니다.

### <a name="test-key-vault"></a>테스트 Key Vault

키 자격 증명 모음을 테스트하려면 비밀을 연결하고 볼 수 있는지 확인합니다. 비밀을 추가하지 않은 경우 세부 정보가 표시되지 않습니다. 필요한 경우 Azure Key Vault 포털의 테스트 비밀을 자격 증명 모음에 추가하고 Microsoft Sentinel에 표시되는지 확인합니다.

예:

```Python
mpconfig = MpConfigFile()
mpconfig.refresh_mp_config()
mpconfig.show_kv_secrets()
```

> [!CAUTION]
> 저장된 Notebook에 출력을 표시하지 마십시오. 출력에 실제 비밀이 있는 경우 Notebook을 저장하기 전에 Notebook의 **출력 지우기** 명령을 사용합니다.
>
> 또한 Notebook의 캐시된 복사본을 삭제합니다. 예를 들어 Notebook **디렉터리에서 .ipynb_checkpoints** 하위 폴더를 살펴보고 이 Notebook의 복사본을 삭제합니다. 지워진 출력으로 Notebook을 저장하면 검사점 복사본을 덮어쓰게 됩니다.
>

Key Vault 구성한 후에는 데이터 공급자 및 TI 공급자 섹션에서 **KV로 업로드** 단추를 사용하여 선택한 설정을 자격 증명 모음으로 이동할 수 있습니다. MSTICPy는 설정 경로(예: )를 기반으로 비밀의 기본 이름을 `TIProviders-VirusTotal-Args-AuthKey` 생성합니다.

값이 성공적으로 업로드되면 설정 편집기에서 **값** 필드의 내용이 삭제되고 기본 설정이 자리 표시자 값으로 대체됩니다. MSTICPy는 이 키를 사용하여 키를 검색하려고 할 때 Key Vault 경로를 자동으로 생성해야 함을 나타냅니다.

Key Vault 저장된 필수 비밀이 이미 있는 경우 **값** 필드에 비밀 이름을 입력할 수 있습니다. 비밀이 기본 자격 증명 [모음(Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html#key-vault) 섹션에 지정된 값)에 저장되지 않은 경우 **VaultName/SecretName** 경로를 지정할 수 있습니다.

다른 테넌트에서 자격 증명 모음에서 설정을 가져오는 것은 현재 지원되지 않습니다. 자세한 내용은 비밀 Key Vault [비밀 지정을 참조하세요.](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets)

## <a name="specify-the-azure-cloud-and-azure-authentication-methods"></a>Azure 클라우드 및 Azure 인증 방법 지정

퍼블릭 또는 글로벌 Azure 클라우드가 아닌 소버린 또는 정부 Azure 클라우드를 사용하는 경우 설정에서 적절한 클라우드를 선택해야 합니다. 대부분의 조직에서 글로벌 클라우드가 기본값입니다.

이러한 Azure 설정을 사용하여 Azure 인증 유형에 대한 기본 설정을 정의할 수도 있습니다.

**Azure 클라우드 및 Azure 인증 방법을 지정하려면:**

1. 다음 코드를 사용하여 다음 셀로 진행하여 실행합니다.

    ```python
    mpedit.set_tab("Azure")
    mpedit
    ```

1. 조직에서 사용하는 클라우드를 선택하거나 선택한 기본 **전역** 옵션을 그대로 둡니다.

1. 다음 방법 중 하나 이상을 선택합니다.

    - **환경** 변수에 Azure 자격 증명을 저장합니다.
    - **Msi** - Jupyter 허브가 실행 중인 호스트 또는 가상 머신에 할당된 ID인 관리 서비스 ID를 사용합니다. MSI는 현재 Azure ML Compute 인스턴스에서 지원되지 않습니다.
    - **cli** - 인증된 Azure CLI 세션의 자격 증명을 사용합니다.
    - **대화형** - 일회성 디바이스 코드를 사용하여 대화형 [디바이스](#authenticate-to-your-microsoft-sentinel-workspace-from-your-notebook)권한 부여 흐름을 사용합니다.

    > [!TIP]
    > 대부분의 경우 **cli** 및 **대화형** 와 같은 여러 메서드를 선택하는 것이 좋습니다. Azure 인증은 성공할 때까지 위에 나열된 순서대로 구성된 각 방법을 시도합니다.
    >

1. **저장을** 선택한 **다음, 설정 저장을** 선택합니다.

예:

:::image type="content" source="media/notebook-get-started/settings-for-azure-gov-cloud.png" alt-text="Azure Government 클라우드에 대해 정의된 설정의 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel에서 Jupyter Notebook과 함께 MSTICPy를 사용하는 기본 사항들을 설명했습니다. 자세한 내용은 [Jupyter Notebook에 대한 고급 구성 및 Microsoft Sentinel의 MSTICPy를 참조하세요.](notebooks-msticpy-advanced.md)

다음과 같이 [Microsoft Sentinel Notebooks GitHub 리포지토리에](https://github.com/Azure/Azure-Sentinel-Notebooks)저장된 다른 Notebook을 사용해 볼 수도 있습니다.

- [Cybersec 기능 둘러보기](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/A%20Tour%20of%20Cybersec%20notebook%20features.ipynb)
- [Machine Learning 예제](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/Machine%20Learning%20in%20Notebooks%20Examples.ipynb)
- 호스트, 계정, IP 주소 및 기타 [엔터티에](https://github.com/Azure/Azure-Sentinel-Notebooks/) 대한 세부 정보를 자세히 드릴다운할 수 있는 Entity Explorer 일련의 Notebook.

> [!TIP]
> 다른 Jupyter 환경에서 이 자습서에 설명된 Notebook을 사용하는 경우 Python 3.6 이상에서 지원하는 커널을 사용할 수 있습니다.
>
> Microsoft Sentinel 및 Azure Machine Learning(ML) 외부에서 MSTICPy Notebook을 사용하려면 Python 환경도 구성해야 합니다. 많은 필수 패키지를 포함하는 Anaconda 배포판과 함께 Python 3.6 이상 설치
>

### <a name="more-reading-on-msticpy-and-notebooks"></a>MSTICPy 및 Notebook에 대한 추가 읽기

다음 표에는 MSTICPy, Microsoft Sentinel 및 Jupyter Notebook에 대한 자세한 참조가 나와 있습니다.

|제목  |추가 참조  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy 패키지 구성](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy 설정 편집기](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Notebook 환경 구성](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb)<br>    - [MPSettingsEditor Notebook](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**참고:** `Azure-Sentinel-Notebooks` GitHub 리포지토리에는 주석 처리된 섹션이 있는 *msticpyconfig.yaml* 템플릿도 포함되어 있어 설정을 이해하는 데 도움이 될 수 있습니다.      |
|**Microsoft Sentinel 및 Jupyter Notebook**     |   - [첫 번째 Microsoft Sentinel Notebook 만들기(블로그](https://techcommunity.microsoft.com/t5/microsoft-sentinel-blog/creating-your-first-microsoft-sentinel-notebook/ba-p/2977745) 시리즈)<br>   - [Jupyter Notebook: 소개](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy 설명서](https://msticpy.readthedocs.io/)<br>    - [Microsoft Sentinel Notebooks 설명서](notebooks.md)<br>    - [The Infosec Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Linux 호스트 탐색기 Notebook 연습](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [보안 조사에 Jupyter를 사용하는 이유](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Microsoft Sentinel & Notebook을 사용하여 보안 조사](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas 설명서](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Bokeh 설명서](https://docs.bokeh.org/en/latest/)       |
|     |         |
