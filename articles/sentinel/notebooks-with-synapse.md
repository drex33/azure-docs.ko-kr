---
title: Microsoft Sentinel Notebook 및 Azure Synapse 통합을 사용하는 대규모 보안 분석
description: 이 문서에서는 Microsoft Sentinel Notebook을 사용하여 Azure Synapse Analytics 빅 데이터 쿼리를 실행하는 방법을 설명합니다.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 10914037b239bf2301881a18c6095ba9364af4ae
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517333"
---
# <a name="large-scale-security-analytics-using-microsoft-sentinel-notebooks-and-azure-synapse-integration-public-preview"></a>Microsoft Sentinel Notebook 및 Azure Synapse 통합을 사용하는 대규모 보안 분석(공개 미리 보기)

> [!IMPORTANT]
> Azure Synapse Analytics Microsoft Sentinel Notebook 통합은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

Microsoft Sentinel Notebook을 Azure Synapse Analytics 통합하면 대규모 보안 분석이 가능합니다.

KQL 및 Log Analytics는 Microsoft Sentinel에서 데이터를 쿼리하고 분석하기 위한 기본 도구 및 솔루션이지만, Azure Synapse 기본 제공 데이터 레이크 액세스 및 Apache Spark 분산 처리 엔진을 사용하여 빅 데이터 분석을 위한 추가 기능을 제공합니다.

Azure Synapse 통합하면 다음을 제공합니다.

- **비용** 최적화, 완전 관리형 Azure Synapse Apache Spark 컴퓨팅 풀을 사용하는 빅 데이터 분석 보안

- **비용 효율적인 Data Lake 액세스를** 통해 Azure Blob Storage 기반으로 구축된 빅 데이터 분석 전용 기능 집합인 Azure Data Lake Storage Gen2를 통해 기록 데이터에 대한 분석을 빌드할 수 있습니다.

- 데이터 원본을 여러 원본 및 형식의 보안 작업 워크플로에 **유연하게 통합할** 수 있습니다.

- Python과 함께 Spark 프레임워크를 사용하기 위한 **Python 기반 API인 PySpark를** 사용하면 Python에 이미 익숙한 경우 새 프로그래밍 언어를 배울 필요가 줄어듭니다.

예를 들어 Azure Synapse Notebook을 사용하여 네트워크 방화벽 로그에서 비정상적인 동작을 헌팅하여 잠재적인 네트워크 탐지를 감지하거나 Log Analytics 작업 영역에서 수집된 데이터를 기반으로 기계 학습 모델을 학습시키고 빌드할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

### <a name="understand-microsoft-sentinel-notebooks"></a>Microsoft Sentinel Notebook 이해

이 문서의 절차를 수행하기 전에 일반적으로 Microsoft Sentinel Notebook에 대해 알아보는 것이 좋습니다.

시작하려면 [Jupyter Notebook을 사용하여 보안 위협 헌트](notebooks.md) 및 [자습서: Microsoft Sentinel에서 Jupyter Notebook 및 MSTICPy 시작을](notebook-get-started.md)참조하세요.

### <a name="required-roles-and-permissions"></a>필요한 역할 및 권한

Microsoft Sentinel Notebook에서 Azure Synapse 사용하려면 다음 역할 및 권한이 있어야 합니다.

|형식  |세부 정보  |
|---------|---------|
|**Microsoft Sentinel**     |- **Microsoft Sentinel에서** Notebook을 저장하고 시작하기 위한 Microsoft Sentinel 기여자 역할         |
|**Azure Machine Learning**     |- 필요한 경우 새 Azure Machine Learning 작업 영역을 만드는 리소스 그룹 수준 **소유자** 또는 **기여자** 역할입니다. <br>- Microsoft Sentinel Notebook을 실행하는 Azure Machine Learning 작업 영역에 대한 **기여자** 역할입니다.    <br><br>자세한 내용은 [Azure Machine Learning 작업 영역에 대한 액세스 관리를 참조하세요.](/azure/machine-learning/how-to-assign-roles)     |
|**Azure Synapse Analytics**     | - 새 Azure Synapse 작업 영역을 만드는 리소스 그룹 수준 **소유자** 역할입니다.<br>- 쿼리를 실행할 Azure Synapse 작업 영역에 대한 **기여자** 역할입니다. <br>- Synapse Studio 대한 Azure Synapse Analytics **기여자** 역할   <br><br>자세한 내용은 [Synapse 에서 일반적인 작업을 수행하는 데 필요한 역할 이해를 참조하세요.](/azure/synapse-analytics/security/synapse-workspace-understand-what-role-you-need)     |
|**Azure Data Lake Storage Gen2**     | - Log Analytics 작업 영역에서 데이터를 내보내는 Azure Log Analytics **기여자** 역할<br>- 데이터 레이크에서 데이터를 쿼리하는 Azure Blob Storage 기여자 역할  <br><br>자세한 내용은 [Azure 역할 할당을 참조하세요.](/azure/storage/blobs/assign-azure-role-data-access?tabs=portal)|
|     |         |

### <a name="connect-to-azure-ml-and-synapse-workspaces"></a>Azure ML 및 Synapse 작업 영역에 커넥트

Azure Synapse Microsoft Sentinel Notebook을 사용하려면 먼저 Azure Machine Learning 작업 영역과 Azure Synapse 작업 영역에 모두 연결해야 합니다.

**Azure Machine Learning 작업 영역을 만들거나 연결하려면:**

Azure Machine Learning 작업 영역은 Microsoft Sentinel에서 Notebook을 사용하기 위한 기본 요구 사항입니다.

아직 연결되지 않은 경우 [Jupyter Notebook을 사용하여 보안 위협 헌트](notebooks.md)를 참조하세요.

**새 Azure Synapse 작업 영역을 만들려면:**

Microsoft Sentinel **Notebooks** 페이지의 맨 위에서 **Azure Synapse 구성을** 선택한 다음, **새 Azure Synapse 작업 영역 만들기를** 선택합니다.

> [!NOTE]
> Azure Data Lake Storage Gen2는 모든 Azure Synapse 작업 영역과 함께 제공되는 기본 제공 Data Lake입니다. Microsoft Sentinel 작업 영역과 동일한 지역에 있는 새 Data Lake를 선택하거나 만들어야 합니다. 이 문서는 이 문서의 후반부에 설명된 대로 데이터를 내보낼 때 필요합니다.
>

자세한 내용은 Azure Synapse [설명서를 참조하세요.](/azure/synapse-analytics/quickstart-create-workspace)


## <a name="configure-your-azure-synapse-analytics-integration"></a>Azure Synapse Analytics 통합 구성

Microsoft Sentinel은 기본 제공 Azure Synapse - **Azure ML 구성 및 Azure Synapse Analytics** Notebook을 제공하여 Azure Synapse 통합하는 데 필요한 구성을 안내합니다.

> [!NOTE]
> Azure Synapse 통합 구성은 일회성 절차이며 Microsoft Sentinel 작업 영역에 대해 이 Notebook을 한 번만 실행하면 됩니다.
>

**Azure Synapse 실행하려면 Azure ML 구성하고 Notebook을 Azure Synapse Analytics.**

1. Microsoft Sentinel **Notebook 페이지에서** 템플릿 **탭을** 선택하고 검색 창에 **Synapse를** 입력하여 Notebook을 찾습니다.

1. Azure Synapse - **Azure ML 구성 및 Azure Synapse Analytics** Notebook을 찾아서 선택한 다음, 오른쪽 아래에서 Notebook 템플릿 **복제를** 선택합니다.

    Notebook **복제** 창에서 필요한 경우 Notebook 이름을 수정하고 [Azure Machine Learning 작업 영역을](#connect-to-azure-ml-and-synapse-workspaces)선택한 **다음, 저장을** 선택합니다.

1. Notebook을 배포한 후 **Notebook 시작** 을 선택하여 엽니다.

    Notebook이 Microsoft Sentinel 내의 Azure ML 작업 영역에서 열립니다. 자세한 내용은 [Azure ML 작업 영역에서 Notebook 시작 을 참조하세요.](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace)

1. Notebook의 초기 단계에서 셀을 실행하여 필요한 Python 라이브러리 및 함수를 로드하고 Azure 리소스에 인증합니다.

1. 4단계 Spark 풀 **Azure Synapse 구성의** 셀을 실행하여 빅 데이터 쿼리를 실행할 때 사용할 새 [Azure Synapse Apache Spark 풀을](/azure/synapse-analytics/spark/apache-spark-pool-configurations) 만듭니다.

1. Azure ML 작업 영역 **및 연결된 서비스 구성의** 5단계에서 셀을 실행하여 Azure ML 작업 영역이 Azure Synapse 작업 영역과 통신할 수 있도록 합니다. 자세한 내용은 [Azure Synapse Analytics 및 Azure Machine Learning 작업 영역 연결 및 Apache Spark 풀 연결을 참조하세요.](/azure/machine-learning/how-to-link-synapse-ml-workspaces)

1. Azure **Log Analytics에서 Azure Data Lake Storage Gen2로 데이터 내보내기** 6단계의 셀을 실행하여 Azure Log Analytics에서 Azure Data Lake Storage 쿼리에 사용할 데이터를 내보냅니다.

데이터가 Azure Data Lake Storage 있으면 Azure Synapse 사용하여 빅 데이터 쿼리 실행을 시작할 준비가 된 것입니다. 자세한 내용은 [Azure Monitor Log Analytics 데이터 내보내기 를](/azure/azure-monitor/logs/logs-data-export?tabs=portal)참조하세요.

## <a name="hunt-on-historical-data-at-scale"></a>대규모로 기록 데이터 헌터 찾기

Microsoft Sentinel은 기본 제공 **Azure Synapse - Apache Spark Notebook을 사용하여 잠재적 네트워크 탐지를** 제공합니다. 이 Notebook을 실제 샘플 보안 시나리오에 대한 템플릿으로 사용하여 Microsoft Sentinel 및 Azure Synapse 빅 데이터 헌팅을 시작합니다.

**Microsoft Sentinel 및 Azure Synapse 사용하여 잠재적인 네트워크 탐지를 검색하려면:**

1. Microsoft Sentinel **Notebook 페이지에서** 템플릿 **탭을** 선택하고 검색 창에 **Synapse를** 입력하여 Notebook을 찾습니다.

1. Azure Synapse 찾아서 선택합니다. **- Apache Spark Notebook을 사용하여 잠재적인 네트워크 탐지를** 검색한 다음, 오른쪽 아래에서 **Notebook 템플릿 복제를** 선택합니다.

    Notebook **복제** 창에서 필요한 경우 Notebook 이름을 수정하고 [Azure Machine Learning 작업 영역을](#connect-to-azure-ml-and-synapse-workspaces)선택한 **다음, 저장을** 선택합니다.

1. Notebook을 배포한 후 **Notebook 시작** 을 선택하여 엽니다.

    Notebook이 Microsoft Sentinel 내의 Azure ML 작업 영역에서 열립니다. 자세한 내용은 [Azure ML 작업 영역에서 Notebook 시작 을 참조하세요.](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace)

1. Notebook의 초기 단계에서 셀을 실행하여 필요한 Python 라이브러리 및 함수를 로드하고 Azure 리소스에 인증합니다.

1. **Spark 세션 시작이라는** 레이블이 붙은 셀에 도착하면 셀을 실행하여 Azure Synapse 세션을 사용하기 시작하고, Azure ML 컴퓨팅을 사용하는 대신 데이터 준비 및 데이터 랭글 태스크에 대한 컴퓨팅으로 Apache Spark 풀을 사용합니다.

1. 후속 셀을 실행하여 현재 Azure Data Lake Storage 저장된 데이터에 대한 쿼리를 구성하고 실행합니다. 예를 들어 특정 시간 범위의 데이터를 포함하도록 [조회 기간을 업데이트합니다.](#define-your-data-lookback-period)

1. 쿼리가 완료되면 Azure Data Lake Storage 결과를 Log Analytics 작업 영역으로 다시 내보냅니다.

    **ADLS에서 결과 내보내기** 단계에 표시된 다음 코드는 쿼리 결과를 단일 JSON 파일로 저장합니다. 디렉터리 이름을 정의하고 셀을 실행합니다.

    ```python
    %%synapse
    dir_name = "<dir-name>"  # specify desired directory name
    new_path = adls_path + dir_name
    csl_beacon_pd = csl_beacon_df.coalesce(1).write.format("json").save(new_path)
    ```

1. 데이터를 내보낸 후 Spark 세션을 중지할 수 있습니다. Spark 세션을 중지한 후 이후 쿼리는 페이지 맨 위에 있는 컴퓨팅 필드에 표시된 기본 Azure ML **컴퓨팅을** 사용하여 실행됩니다.

    **Spark 세션 중지** 단계에서 셀을 실행합니다.

    ```python
    %synapse stop
    ```

1. Azure Data Lake Storage 쿼리 결과를 사용하여 JSON 파일을 로컬 파일 시스템으로 내보냅니다.

    **ADLS에서 로컬 파일 시스템으로 결과 내보내기,** **ADLS에서 파일 다운로드** 및 **결과 표시** 단계의 코드를 사용하여 JSON 파일을 로컬로 저장하고 확인합니다.

1. 결과를 로컬로 저장한 후 추가 데이터로 보강하고 시각화를 실행할 수 있습니다. 예를 들어 **Azure Synapse - Apache Spark Notebook을 사용하여 잠재적 네트워크 탐지를 검색하면** 다음 작업을 수행할 수 있는 추가 단계가 제공됩니다.

    - IP 주소 GeoLocation, WhoIs 및 기타 위협 인텔리전스 데이터를 사용하여 결과를 보강하여 비정상적인 네트워크 동작을 보다 완전하게 파악할 수 있습니다.
    - MSTICPy 시각화를 실행하여 원격 네트워크 연결 또는 기타 이벤트의 배포를 살펴보면서 위치를 매핑합니다.

    추가 조사를 위해 결과를 Microsoft Sentinel에 다시 쓸 수 있습니다. 예를 들어 결과에서 사용자 지정 인시던트, 감시 목록 또는 헌팅 책갈피를 만들 수 있습니다.

    > [!TIP]
    > 이러한 단계를 사용하여 잠재적인 네트워크 탐지를 감지하거나 템플릿으로 사용하여 조직의 요구에 맞게 수정합니다.
    >

## <a name="manage-your-azure-synapse-session-from-microsoft-sentinel"></a>Microsoft Sentinel에서 Azure Synapse 세션 관리

Azure Synapse 세션에 없는 경우 Microsoft Sentinel은 Notebook 페이지 맨 위에 있는 컴퓨팅 필드에서 선택한 Azure ML **컴퓨팅으로** 기본 **설정됩니다.**

여기에서 복사할 수 있는 다음 코드 또는 **Azure Synapse - Apache Spark Notebook을 사용하여 잠재적 네트워크 탐지를 검색하여 Azure Synapse** 세션을 시작하고 중지합니다.

### <a name="start-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Microsoft Sentinel 내에서 Azure Synapse 세션 시작

다음 코드를 실행하세요.

```python
%synapse start -w $amlworkspace -s $subscription_id -r $resource_group -c $synapse_spark_compute
```

를 사용하여 모든 후속 코드 `%%synapse` 셀을 시작하여 시작한 Synapse 세션을 사용합니다.

예를 들어:

```python
%%synapse

# Primary storage info
account_name = '<storage account name>' # fill in your primary account name
container_name = '<container name>' # fill in your container name
subscription_id = '<subscription if>' # fill in your subscription id
resource_group = '<resource group>' # fill in your resource groups for ADLS
workspace_name = '<Microsoft Sentinel/log analytics workspace name>' # fill in your workspace name
device_vendor = "Fortinet"  # Replace your desired network vendor from commonsecuritylogs

# Datetime and lookback parameters
end_date = "<enter date in the format yyyy-MM-dd e.g.2021-09-17>"  # fill in your input date
lookback_days = 21 # fill in lookback days if you want to run it on historical data. make sure you have historical data available in ADLS
```

### <a name="define-your-data-lookback-period"></a>데이터 lookback 기간 정의

이 샘플 노트북의 빅 데이터 쿼리는 `end-date` 매개 변수를 사용 하거나 시간 범위를 사용 하 여 미리 정의 된 날짜에서 데이터를 실행할 수 있습니다.

예를 들어:

- 특정 날짜의 데이터에 관심이 있는 경우 11 월 15 2021 일을 현재 날짜로 지정 하 고, 쿼리는 2021 15,의 데이터 에서만 실행 됩니다. 

- 쿼리의 시간 범위를 더 길게 정의 하려면 현재 날짜 외에 lookback 매개 변수를 정의 합니다. 예를 들어 `lookback_days` 매개 변수가 days로 설정 되 `21` 고 `end_date` 매개 변수가로 설정 되 면 `2021-11-17` 쿼리는 21 일의 데이터를 확인 하 고 11 월 2021 17 일에서 다시 계산 합니다.

**Azure Synapse-Apache Spark 노트북을 사용 하 여 잠재적인 네트워크 표지 검색** 에서 **데이터 준비 단계** 에서이 코드를 찾을 수 있습니다.

예를 들어:

```python
# Datetime and lookback parameters
end_date = "2021-11-17>"  # fill in your input date
lookback_days = "21" # fill in lookback days if you want to run it on historical data. Make sure you have historical data available in ADLS
```

위의 예제에서 쿼리는 2021 년 10 월 28 일 사이의 데이터에서 실행 됩니다.

### <a name="stop-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Microsoft 센티널 내에서 Azure Synapse 세션 중지

다음 코드를 실행하세요.

```python
%synapse stop
```

### <a name="switch-azure-synapse-workspaces-in-microsoft-sentinel"></a>Microsoft 센티널에서 Azure Synapse 작업 영역 전환

현재 로그인 한 것과 다른 Synapse 작업 영역을 관리 하거나 선택 하려면 다음 방법 중 하나를 사용 합니다.

- **azure ML와 새 azure Synapse 작업 영역 간에 연결 된 서비스를 이미 만든 경우** 다음을 수행 합니다.

    1. `linkservice`다음 코드 셀에 매개 변수의 이름을 입력 한 다음 셀 및 후속 셀을 다시 실행 합니다.

        ```python
        amlworkspace = "<aml workspace name>"  # fill in your AML workspace name
        subscription_id = "<subscription id>" # fill in your subscription id
        resource_group = '<resource group of AML workspace>' # fill in your resource groups for AML workspace
        linkedservice = '<linked service name>' # fill in your linked service created to connect to synapse workspace
        ```

    1. 등록 되어 연결 된 서비스에 연결 된 Azure Synapse Spark 풀의 이름을 제공 해야 합니다.

        ```python
        synapse_spark_compute = "<synapse spark compute>"
        ```

- **azure ML와 azure Synapse 작업 영역 간에 연결 된 서비스가 아직 없는 경우** azure **Synapse – azure ML 및 azure Synapse Analytics** 노트북을 실행 하 여 azure Synapse을 실행 하기 전에 연결 된 서비스를 구성 하도록 합니다. Apache Spark 노트북을 **사용 하 여 잠재적인 네트워크를 검색** 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Jupyter 노트북을 사용 하 여 보안 위협 구하기](notebooks.md)
- [자습서: Microsoft 센티널에서 Jupyter 노트북 및 MSTICPy 시작 하기](notebook-get-started.md)
- [Azure Synapse Analytics 및 Azure Machine Learning 작업 영역을 연결하고 Apache Spark 풀 연결(미리 보기)](/azure/machine-learning/how-to-link-synapse-ml-workspaces)
