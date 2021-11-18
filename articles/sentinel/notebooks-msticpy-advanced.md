---
title: Microsoft 센티널의 Jupyter 노트북 및 MSTICPy에 대 한 고급 구성 | Microsoft Docs
description: Microsoft 센티널에서 작업 하는 경우 MSTICPy를 사용 하 여 Jupyter 노트북에 사용할 수 있는 고급 구성에 대해 알아봅니다.
services: sentinel
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 68274be9eb1cc45bf9b96454314246149e90c4ec
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712683"
---
# <a name="advanced-configurations-for-jupyter-notebooks-and-msticpy-in-microsoft-sentinel"></a>Microsoft 센티널의 Jupyter 노트북 및 MSTICPy에 대 한 고급 구성

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널에서 Jupyter 노트북 및 MSTICPy 작업에 대 한 고급 구성을 설명 합니다.

자세한 내용은 [jupyter 노트북을 사용 하 여 보안 위협](notebooks.md) 및 [자습서: Microsoft 센티널에서 jupyter 노트북 및 MSTICPy 시작](notebook-get-started.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서는 [자습서: Microsoft 센티널에서 Jupyter 노트북 및 MSTICPy 시작](notebook-get-started.md)을 참조 하세요. 아래에 설명 된 고급 절차를 계속 하기 전에 자습서를 수행 하는 것이 좋습니다.

## <a name="specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis"></a>Azure 및 Microsoft 센티널 Api에 대 한 인증 매개 변수 지정

이 절차에서는 **msticpyconfig** 파일에서 Microsoft 센티널 및 기타 Azure API 리소스에 대 한 인증 매개 변수를 구성 하는 방법을 설명 합니다.

**MSTICPy 설정 편집기에서 Azure 인증 및 Microsoft 센티널 API 설정을 추가 하려면 다음을 수행 합니다**.

1. 다음 코드를 사용 하 여 다음 셀로 이동 하 고 실행 합니다.

    ```python
    mpedit.set_tab("Data Providers")
    mpedit
    ```

1. **데이터 공급자** 탭에서 **azurecli**  >  **추가** 를 선택 합니다.

1. 사용할 인증 방법을 선택 합니다.

    - [Azure 기본값](notebook-get-started.md#specify-the-azure-cloud-and-azure-authentication-methods)에서 다른 메서드 집합을 사용할 수 있지만이 사용은 일반적인 구성이 아닙니다.
    - **Env** (환경 변수) 인증을 사용 하려는 경우가 아니면 **clientId**, **tenantiId** 및 **clientSecret** 필드는 비워 둡니다.
    - 권장 하지는 않지만 MSTICPy는 인증을 위해 클라이언트 앱 Id 및 암호를 사용 하는 것도 지원 합니다. 이러한 경우 **데이터 공급자** 탭에서 직접 **clientId**, **tenantId** 및 **clientSecret** 필드를 정의 합니다.

1. **파일 저장** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="define-autoloading-query-providers"></a>자동 로드 쿼리 공급자 정의

MSTICPy 함수를 실행할 때 자동으로 로드 하도록 할 쿼리 공급자를 정의 `nbinit.init_notebook` 합니다.

새 노트북을 자주 작성 하는 경우 자동 로드 쿼리 공급자는 피벗 함수 및 참고 책자와 같은 다른 구성 요소 보다 먼저 필요한 공급자가 로드 되도록 하 여 시간을 절약할 수 있습니다.

**자동 로드 쿼리 공급자를 추가 하려면**:

1. 다음 코드를 사용 하 여 다음 셀로 이동 하 고 실행 합니다.

    ```python
    mpedit.set_tab("Autoload QueryProvs")
    mpedit
    ```

1. **Autoload QueryProv** 탭에서 다음을 수행 합니다.

   - Microsoft 센티널 공급자의 경우 연결 하려는 공급자 이름과 작업 영역 이름을 모두 지정 합니다.
   - 다른 쿼리 공급자의 경우 공급자 이름만 지정 합니다.

   각 공급자에는 다음과 같은 선택적 값도 있습니다.

   - **자동 연결:** 이 옵션은 기본적으로 **True** 로 정의 되며 MSTICPy는 로드 직후 공급자에 인증을 시도 합니다. MSTICPy 사용자가 설정에서 공급자에 대 한 자격 증명을 구성 했다고 가정 합니다.

   - **별칭:** MSTICPy가 공급자를 로드 하는 경우 공급자를 Python 변수 이름에 할당 합니다. 기본적으로 Microsoft 센티널 공급자의 경우 변수 이름이 **qryworkspace_name** 되 고 다른 공급자의 경우 **qryprovider_name** 됩니다.

        예를 들어 *ContosoSOC* 작업 영역에 대 한 쿼리 공급자를 로드 하는 경우이 쿼리 공급자는 이름으로 노트북 환경에 만들어집니다 `qry_ContosoSOC` . 더 짧거나 더 쉽게 입력 하 고 기억할 수 있는 항목을 사용 하려면 별칭을 추가 합니다. 공급자 변수 이름은 이며 `qry_<alias>` , 여기서 `<alias>` 은 사용자가 제공한 별칭 이름으로 바뀝니다.

        이 메커니즘을 통해 로드 하는 공급자는 `current_providers` 다음 코드에서와 같이 사용 되는 MSTICPy 특성에도 추가 됩니다.

        ```python
        import msticpy
        msticpy.current_providers
        ```

1. **설정 저장** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="define-autoloaded-msticpy-components"></a>나타냄 MSTICPy 구성 요소 정의

이 절차에서는 함수를 실행할 때 MSTICPy에 의해 자동으로 로드 되는 다른 구성 요소를 정의 하는 방법을 설명 합니다 `nbinit.init_notebook` .

지원 되는 구성 요소는 다음과 같은 순서로 포함 됩니다.

1. **TILookup:** [TI 공급자 라이브러리](notebook-get-started.md#add-threat-intelligence-provider-settings)
1. **Geoip:** 사용 하려는 [Geoip 공급자](notebook-get-started.md#add-geoip-provider-settings)
1. **Azuredata:** [Azure 리소스](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis) 에 대 한 세부 정보를 쿼리 하는 데 사용 하는 모듈
1. **AzureSentinelAPI:** [Microsoft 센티널 API](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis) 를 쿼리 하는 데 사용 하는 모듈
1. **참고 책자:** [Msticnb 패키지](https://msticnb.readthedocs.io/en/latest/) 의 참고 책자
1. **피벗:** 피벗 함수

> [!NOTE]
> 피벗 구성 요소가 엔터티에 연결 하는 피벗 함수를 찾기 위해 로드 된 쿼리 및 기타 공급자가 필요 하기 때문에 구성 요소는이 순서로 로드 됩니다. 자세한 내용은 [MSTICPy 설명서](https://msticpy.readthedocs.io/en/latest/data_analysis/PivotFunctions.html)를 참조 하세요.
>

**자동 로드 된 MSTICPy 구성 요소를 정의 하려면**:

1. 다음 코드를 사용 하 여 다음 셀로 이동 하 고 실행 합니다.

   ```python
   mpedit.set_tab("Autoload Components")
   mpedit
   ```

1. **Autoload 구성 요소** 탭에서 필요에 따라 매개 변수 값을 정의 합니다. 예를 들어 다음과 같습니다.

   - **GeoIpLookup**.  *GeoLiteLookup* 또는 *ipstack* 중에서 사용 하려는 geoip 공급자의 이름을 입력 합니다.  자세한 내용은 [GeoIP 공급자 설정 추가](notebook-get-started.md#add-geoip-provider-settings)를 참조 하세요.

   - **Azuredata 및 AzureSentinelAPI 구성 요소**  다음 값을 정의 합니다.

      - **auth_methods:** AzureCLI에 대 한 기본 설정을 재정의 하 고 선택한 메서드를 사용 하 여 연결 합니다.
      - **자동 연결:** 연결 하지 않고 로드 하려면 false로 설정 합니다.

      자세한 내용은 [Azure 및 Microsoft 센티널 api에 대 한 인증 매개 변수 지정](#specify-authentication-parameters-for-azure-and-microsoft-sentinel-apis)을 참조 하세요.

   - **참고 책자**. **참고 책자** 구성 요소에는 단일 매개 변수 블록 ( **AzureSentinel**)이 있습니다.

      다음 구문을 사용 하 여 Microsoft 센티널 작업 영역을 지정 `workspace:\<workspace name>` 합니다. 작업 영역 이름은 **Microsoft 센티널** 탭에 정의 된 작업 영역 중 하나 여야 합니다.

      함수에 보낼 매개 변수를 더 추가 하려면 `notebooklets init` 줄바꿈으로 구분 된 키: 값 쌍으로 지정 합니다. 예를 들어 다음과 같습니다.

      ```python
      workspace:<workspace name>
      providers=["LocalData","geolitelookup"]
      ```

      자세한 내용은 [MSTICNB (MSTIC) 설명서](https://msticnb.readthedocs.io/en/latest/msticnb.html#msticnb.data_providers.init)를 참조 하세요.

    **TILookup** 및 **Pivot** 과 같은 일부 구성 요소에는 매개 변수가 필요 하지 않습니다.

1. **설정 저장** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="switch-between-python-36-and-38-kernels"></a>Python 3.6 및 3.8 커널 간 전환

Python 3.65 및 3.8 커널 간을 전환 하는 경우 MSTICPy 및 기타 패키지가 예상 대로 설치 되지 않을 수 있습니다.

이는 `!pip install pkg` 첫 번째 환경에서 명령을 올바르게 설치 하지만 두 번째 환경에 올바르게 설치 하지 않는 경우에 발생할 수 있습니다. 이렇게 하면 두 번째 환경에서 패키지를 가져오거나 사용할 수 없는 상황이 발생 합니다.

`!pip install...`Azure ML 노트북에 패키지를 설치 하는 데를 사용 하지 않는 것이 좋습니다. 대신 다음 옵션 중 하나를 사용 합니다.

- **노트북 내 에서% pip 선 마법을 사용** 합니다. 다음을 실행합니다.

  ```python

  %pip install --upgrade msticpy
  ```

- **터미널에서 설치**:

  1. Azure ML 노트북에서 터미널을 열고 다음 명령을 실행 합니다.

     ``` bash
     conda activate azureml_py38
     pip install --upgrade msticpy
     ```

  1. 터미널을 닫고 커널을 다시 시작 합니다.

## <a name="set-an-environment-variable-for-your-msticpyconfigyaml-file"></a>Msticpyconfig 파일에 대 한 환경 변수 설정

Azure ML에서 실행 중이 고 사용자 폴더의 루트에 **msticpyconfig** 파일이 있는 경우 MSTICPy가 자동으로 이러한 설정을 찾습니다. 그러나 다른 환경에서 노트북을 실행 하는 경우이 섹션의 지침에 따라 구성 파일의 위치를 가리키는 환경 변수를 설정 합니다.

환경 변수에서 **msticpyconfig** 파일에 대 한 경로를 정의 하면 알려진 위치에 파일을 저장 하 고 항상 동일한 설정을 로드 하는지 확인할 수 있습니다.

여러 개의 환경 변수를 사용 하는 여러 구성 파일을 사용 하 여 다른 노트북에 다른 설정을 사용 합니다.

1. **Msticpyconfig** 파일의 위치 (예: **~/.msticpyconfig.yaml** 또는 **% userprofile%/msticpyconfig.yaml**)를 결정 합니다.

    **azure ML 사용자**: azure ML 사용자 폴더에 구성 파일을 저장 하는 경우 MSTICPy `init_notebook` 함수 (초기화 셀에서 실행)가 자동으로 파일을 찾아서 사용 하 고 **MSTICPYCONFIG** 환경 변수를 설정할 필요가 없습니다.

    그러나 암호를 파일에 저장 한 경우에는 계산 로컬 드라이브에 구성 파일을 저장 하는 것이 좋습니다. 계산 내부 저장소는 계산을 만든 사용자만 액세스할 수 있는 반면, 공유 저장소는 Azure ML 작업 영역에 액세스할 수 있는 모든 사용자가 액세스할 수 있습니다.

    자세한 내용은 [Azure Machine Learning 계산 인스턴스 란?](../machine-learning/concept-compute-instance.md)을 참조 하세요.

1. 필요한 경우 **msticpyconfig** 파일을 선택한 위치에 복사 합니다.

1. **MSTICPYCONFIG** 환경 변수를 해당 위치를 가리키도록 설정 합니다.

다음 절차 중 하나를 사용 하 여 **MSTICPYCONFIG** 환경 변수를 정의 합니다.

# <a name="windows"></a>[Windows](#tab/windows)

예를 들어 Windows 시스템에서 **MSTICPYCONFIG** 환경 변수를 설정 하려면 다음을 수행 합니다.

1. 필요에 따라 **msticpyconfig** 파일을 계산 인스턴스로 이동 합니다.
1. **시스템 속성** 대화 상자에서 **고급** 탭을 엽니다.
1. 환경 **변수 ...** 를 선택 하 여 **환경 변수** 대화 상자를 엽니다.
1. **시스템 변수** 영역에서 **새로 만들기 ...** 를 선택 하 고 다음과 같이 값을 정의 합니다.

    - **변수 이름**: 다음으로 정의 합니다. `MSTICPYCONFIG`
    - **변수 값**: msticpyconfig 파일의 경로를 입력 **합니다.**

# <a name="linux"></a>[Linux](#tab/linux)

이 절차에서는 **.bashrc** 파일을 업데이트 하 여 Linux 시스템에서 **MSTICPYCONFIG** 환경 변수를 설정 하는 방법을 설명 합니다.

1. 필요에 따라 **msticpyconfig** 파일을 계산 인스턴스로 이동 합니다.

1. Microsoft 센티널 **노트북** 페이지에서와 같은 Azure ML 터미널을 엽니다.

1. **Msticpyconfig** 파일에 액세스할 수 있는지 확인 합니다.

   azure ML 터미널에서 현재 디렉터리는 계산 Linux 시스템에 탑재 된 azure ML 파일 저장소 홈 디렉터리 여야 합니다. 프롬프트는 다음 예제와 유사 합니다.

   ```python
   azureuser@alicecontoso-azml7:~/cloudfiles/code/Users/alicecontoso$
   ```

   을 입력 하 여 **msticpyconfig** 파일을 포함 하 여 홈 디렉터리에 있는 모든 파일을 나열 `ls` 합니다.

1. **Msticpyconfig** 파일을 계산 파일 저장소로 이동 하려면 다음을 입력 합니다.

   ```python
   mv msticpyconfig.yaml ~
   ```

1. 다음 프로세스 중 하나를 사용 하 여 환경 변수에 대 한 .bashrc 파일을 편집 **합니다** .

    |명령  |단계  |
    |---------|---------|
    |**vim**     |     1. 실행: `vim ~/.bashrc` <br>2. **SHIFT + G**  >  **end** 를 눌러 파일의 끝으로 이동 합니다. 3. **을** 입력 하 고 **enter** 키를 눌러 새 줄을 만듭니다. <br>4. 환경 변수를 추가 하 고 **esc** 키를 눌러 명령 모드로 돌아갑니다. <br>5. **wq** 를 입력 하 여 파일을 저장 합니다.    |
    |**nano**     |           1. 실행: `nano ~/.bashrc`<br>        1. **ALT +/** 또는 **옵션 +/** 를 눌러 파일의 끝으로 이동 합니다.<br>        1. 환경 변수를 추가 하 고 파일을 저장 합니다. **Ctrl + X** , **Y** 를 차례로 누릅니다.      |
    |     |         |

    다음 환경 변수 중 하나를 추가 합니다.

    - **Msticpyconfig** 파일을 이동한 경우를 실행 `export MSTICPYCONFIG=~/msticpyconfig.yaml` 합니다.
    - **Msticpyconfig** 파일을 이동 하지 않은 경우를 실행 `export MSTICPYCONFIG=~/cloudfiles/code/Users/<YOURNAME>/msticpyconfig.yaml` 합니다.

# <a name="azure-ml-options"></a>[Azure ML 옵션](#tab/azure-ml)

**msticpyconfig** 파일을 Azure ML 사용자 폴더 이외의 위치에 저장 해야 하는 경우 다음 옵션 중 하나를 사용 합니다.

- **사용자 폴더의 루트에 있는 *nbuser_settings py* 파일** 입니다.  이 프로세스는 **커널 json** 파일을 편집 하는 것 보다 간단 하 고 개입 하기는 하지만 `init_notebook` 노트북 코드의 시작 부분에서 함수를 실행 하는 경우에만 지원 됩니다. 이는 기본 동작 이지만 먼저를 실행 하지 않고 노트북 코드를 실행 하는 경우 `init_notebook` MSTICPy에서 구성 파일을 찾지 못할 수 있습니다.

    1. Azure ML 터미널에서 사용자 이름의 폴더인 사용자 폴더의 루트에 py 파일을 **nbuser_settings** 만듭니다.
    1. Py 파일 **nbuser_settings** 에 다음 줄을 추가 합니다.

        ```python
          import os
          os.environ["MSTICPYCONFIG"] = "~/msticpyconfig.yaml"
        ```

    이 파일은 함수에 의해 자동으로 가져오며 `init_notebook` `MSTICPYCONFIG` 현재 노트북에 대 한 환경 변수를 설정 합니다.

- **Python 커널의 *커널 json* 파일** 입니다. 수동으로 노트북을 실행 하려는 경우와 시작 시에 함수를 호출 하지 않을 경우이 절차를 사용 `init_notebook` 합니다.

  Python 3.6 및 Python 3.8에 대 한 커널이 있습니다. 두 커널을 모두 사용 하는 경우 두 파일을 모두 편집 합니다.

  - **Python 3.8 위치**: */usr/local/share/jupyter/kernels/python38-azureml/kernel.json*
  - **Python 3.6 위치**: */usr/local/share/jupyter/kernels/python3-azureml/kernel.json*

  **Kernel json** 파일에 환경 변수를 설정 하려면 다음을 수행 합니다.

  1. **커널의 json** 파일의 복사본을 만들고 편집기에서 원래를 엽니다. 를 사용 하 여 `sudo` **kernel json** 파일을 덮어써야 할 수 있으며 파일 콘텐츠는 다음 예제와 유사 합니다.

      ```python
      {
         "argv": [
         "/anaconda/envs/azureml_py38/bin/python",
         "-m",
         "ipykernel_launcher",
         "-f",
         "{connection_file}"
         ],
         "display_name": "Python 3.8 - AzureML",
         "language": "python"
      }
      ```

  1. 항목 뒤에 `"language"` 다음 줄을 추가 합니다. `"env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }`

      줄의 끝에 쉼표를 추가 해야 `"language": "python"` 합니다. 예를 들어 다음과 같습니다.

      ```python
      {
          "argv": [
          "/anaconda/envs/azureml_py38/bin/python",
          "-m",
          "ipykernel_launcher",
          "-f",
          "{connection_file}"
          ],
          "display_name": "Python 3.8 - AzureML",
          "language": "python",
          "env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }
      }
      ```

---

> [!NOTE]
> Linux 및 Windows 옵션의 경우 정의 된 환경 변수를 선택 하기 위해 jupyter 서버를 다시 시작 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

|제목  |추가 참조  |
|---------|---------|
|**MSTICPy**     |      - [MSTICPy 패키지 구성](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [MSTICPy 설정 편집기](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [노트북 환경 구성](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [MPSettingsEditor 노트북](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**참고**: Azure 센티널 GitHub (msticpyconfig) 리포지토리에는 주석 처리 섹션이 포함 된 template  파일이 포함 되어 있으므로 설정을 이해 하는 데 도움이 될 수 있습니다.      |
|**Microsoft 센티널 및 Jupyter 노트북**     |      - [Jupyter 노트북: 소개](https://realpython.com/jupyter-notebook-introduction/)<br>    - [MSTICPy 설명서](https://msticpy.readthedocs.io/)<br>    - [Microsoft 센티널 노트북 설명서](notebooks.md)<br>    - [Infosec Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Linux 호스트 탐색기 노트북 연습](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [보안 조사에 Jupyter를 사용 하는 이유](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Microsoft 센티널을 사용한 보안 조사 & 노트북](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Pandas 설명서](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Bokeh 설명서](https://docs.bokeh.org/en/latest/)       |
|     |         |
