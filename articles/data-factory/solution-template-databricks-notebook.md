---
title: Azure Databricks를 통한 변환
description: 솔루션 템플릿을 사용하여 Azure Data Factory의 Databricks 노트북을 통해 데이터를 변환하는 방법을 알아봅니다.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: dc01ba10922901e5c3558e26b0f2ab2718ea34e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743594"
---
# <a name="transformation-with-azure-databricks"></a>Azure Databricks를 통한 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure Data Factory의 **유효성 검사**, **데이터 복사** 및 **Notebook** 작업을 포함하는 엔드투엔드 파이프라인을 만듭니다.

- **유효성 검사** 를 사용하면 복사 및 분석 작업을 트리거하기 전에 원본 데이터 세트가 다운스트림에 대한 사용 준비가 됩니다.

- **데이터 복사** 는 Azure Databricks Notebook에서 DBFS로 탑재된 싱크 스토리지로 원본 데이터 세트를 복제합니다. 해당 방식으로 Spark를 통해 데이터 세트를 직접 사용할 수 있습니다.

- **Notebook** 은 데이터 세트를 변환하는 Databricks Notebook을 트리거합니다. 또한 처리된 폴더 또는 Azure Synapse Analytics에 데이터 세트를 추가합니다.

간단히 말해, 이 자습서의 템플릿은 예약된 트리거를 만들지 않습니다. 필요한 경우에는 추가할 수 있습니다.

:::image type="content" source="media/solution-template-Databricks-notebook/pipeline-example.png" alt-text="파이프라인 다이어그램":::

## <a name="prerequisites"></a>사전 요구 사항

- `sinkdata`싱크로 사용이라는 컨테이너가 있는 Azure Blob Storage 계정

  스토리지 계정 이름, 컨테이너 이름 및 액세스 키를 적어둡니다. 템플릿의 뒷부분에서 해당 값이 필요합니다.

- Azure Databricks 작업 영역

## <a name="import-a-notebook-for-transformation"></a>변환을 위해 Notebook 가져오기

Databricks 작업 영역으로 **변환** Notebook을 가져오려면 다음을 수행합니다.

1. Azure Databricks 작업 영역에 로그인하고 **가져오기** 를 선택합니다.
       :::image type="content" source="media/solution-template-Databricks-notebook/import-notebook.png" alt-text="작업 영역을 가져오기 위한 메뉴 명령":::
   작업 영역 경로는 표시된 경로와 다를 수 있지만 나중에 사용할 수 있습니다.
1. **URL에서 가져오기** 를 선택합니다. 문자 상자에 `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`을 입력합니다.

   :::image type="content" source="media/solution-template-Databricks-notebook/import-from-url.png" alt-text="Notebook 가져오기에 대한 선택 항목":::

1. 이제 스토리지 연결 정보를 사용하여 **변환** Notebook을 업데이트해 보겠습니다.

   가져온 Notebook에서 다음의 코드 조각과 같이 **명령 5** 로 이동합니다.

   - `<storage name>`과 `<access key>`를 사용자의 스토리지 연결 정보로 바꿉니다.
   - `sinkdata`컨테이너에 스토리지 계정을 사용합니다.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Databricks에 액세스할 Data Factory에 대해 **Databricks 액세스 토큰** 을 생성합니다.
   1. Databricks 작업 영역에서 오른쪽 위에 있는 사용자 프로필 아이콘을 선택합니다.
   1. **사용자 설정** 을 선택합니다.
    :::image type="content" source="media/solution-template-Databricks-notebook/user-setting.png" alt-text="사용자 설정에 대한 메뉴 명령":::
   1. **액세스 토큰** 탭에서 **새 토큰 생성** 을 선택합니다.
   1. **생성** 을 선택합니다.

    :::image type="content" source="media/solution-template-Databricks-notebook/generate-new-token.png" alt-text="&quot;생성 &quot; 단추":::

   나중에 Databricks에 연결된 서비스를 만드는 데 사용하기 위해 *액세스 토큰을 저장* 합니다. 액세스 토큰은 `dapi32db32cbb4w6eee18b7d87e45exxxxxx`과 유사합니다.

## <a name="how-to-use-this-template"></a>이 템플릿을 사용하는 방법 알아보기

1. **Azure Databricks로 변환** 으로 이동하여 다음 연결에 대한 새 연결된 서비스를 만듭니다.

   :::image type="content" source="media/solution-template-Databricks-notebook/connections-preview.png" alt-text="연결 설정":::

    - **원본 Blob 연결** -원본 데이터에 액세스합니다.

       이 연습에서는 원본 파일을 포함하는 공용 Blob Storage를 사용할 수 있습니다. 구성에 대한 다음 스크린샷을 참조합니다. 다음 **SAS URL** 을 사용하여 원본 스토리지(읽기 전용 액세스)에 연결합니다.

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        :::image type="content" source="media/solution-template-Databricks-notebook/source-blob-connection.png" alt-text="인증 방법 및 SAS URL에 대한 선택 항목":::

    - **대상 Blob 연결** -복사된 데이터를 저장합니다.

       **새 연결된 서비스** 창에서 싱크 Storage Blob을 선택합니다.

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-blob-connection.png" alt-text="새 연결된 서비스로 싱크 Storage Blob":::

    - **Azure Databricks** -Databricks 클러스터에 연결합니다.

        이전에 생성한 액세스 키를 사용하여 Databricks로 연결된 서비스를 만듭니다. *대화형 클러스터* 가 있는 경우 선택할 수 있습니다. 이 예제에서는 **새 작업 클러스터** 옵션을 사용합니다.

        :::image type="content" source="media/solution-template-Databricks-notebook/databricks-connection.png" alt-text="프라이빗 클러스터에 연결하기 위한 선택 항목":::

1. **이 템플릿 사용** 을 선택합니다. 만들어진 파이프라인을 볼 수 있습니다.

    :::image type="content" source="media/solution-template-Databricks-notebook/new-pipeline.png" alt-text="파이프라인을 만듭니다.":::

## <a name="pipeline-introduction-and-configuration"></a>파이프라인 소개 및 구성

새 파이프라인에서 대부분의 설정은 기본값을 사용하여 자동으로 구성됩니다. 파이프라인의 구성을 검토하고 필요한 변경 작업을 수행합니다.

1. **유효성 검사** 작업 **가용성 플래그** 에서 원본 **데이터 세트** 값이 `SourceAvailabilityDataset`이전에 만든 것으로 설정되어 있는지 확인합니다.

   :::image type="content" source="media/solution-template-Databricks-notebook/validation-settings.png" alt-text="원본 데이터 세트 값":::

1. **데이터** 작업 **파일을 Blob으로 복사** 에서 **원본** 및 **싱크** 탭을 선택합니다. 필요한 경우 설정을 변경합니다.

   - **원본** 탭 :::image type="content" source="media/solution-template-Databricks-notebook/copy-source-settings.png" alt-text="원본 탭":::

   - **싱크** 탭 :::image type="content" source="media/solution-template-Databricks-notebook/copy-sink-settings.png" alt-text=" 싱크 탭":::

1. **Notebook** 활동 **변환** 에서 필요에 따라 경로와 설정을 검토하고 업데이트합니다.

   **Databricks 연결된 서비스** 는 :::image type="content" source="media/solution-template-Databricks-notebook/notebook-activity.png" alt-text="Databricks 연결된 서비스에 대해 채워진 값":::에 표시된 것처럼 이전 단계의 값으로 미리 채워야 합니다.

   **Notebook** 설정을 확인하기 위해서 다음을 수행합니다.
  
    1. **설정** 탭을 선택합니다. **Notebook 경로** 에서 기본 경로가 올바른지 확인합니다. 올바른 Notebook 경로를 찾아서 선택해야 할 수 있습니다.

       :::image type="content" source="media/solution-template-Databricks-notebook/notebook-settings.png" alt-text="Notebook 경로":::

    1. **기본 매개 변수** 선택기를 확장하고 다음 스크린샷에 표시된 것과 일치하는 매개 변수를 확인합니다. 해당 매개 변수는 Data Factory에서 Databricks Notebook으로 전달됩니다.

       :::image type="content" source="media/solution-template-Databricks-notebook/base-parameters.png" alt-text="기본 매개 변수":::

1. **파이프라인 매개 변수가** 다음 스크린샷에 표시된 것과 일치하는지 확인합니다. :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-parameters.png" alt-text="파이프라인 매개 변수":::

1. 데이터 세트에 연결합니다.

    >[!NOTE]
    >아래 데이터 세트에서 파일 경로가 템플릿에 자동으로 지정되었습니다. 변경이 필요한 경우, 연결 오류가 발생할 수 있으므로 **컨테이너** 와 **디렉터리** 에 대한 경로를 지정했는지 확인합니다.

   - **SourceAvailabilityDataset** -원본 데이터를 사용할 수 있는지 확인합니다.

     :::image type="content" source="media/solution-template-Databricks-notebook/source-availability-dataset.png" alt-text="SourceAvailabilityDataset에 대한 연결된 서비스 및 파일 경로 선택 항목":::

   - **SourceFilesDataset** -원본 데이터에 액세스합니다.

       :::image type="content" source="media/solution-template-Databricks-notebook/source-file-dataset.png" alt-text="SourceFilesDataset에 대한 연결된 서비스 및 파일 경로에 대한 선택 항목":::

   - **DestinationFilesDataset** -데이터를 싱크 대상 위치로 복사합니다. 다음 값을 사용합니다.

     - 이전 단계에서 만든 **연결된 서비스** - `sinkBlob_LS`

     - **파일 경로** - `sinkdata/staged_sink`

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-dataset.png" alt-text="DestinationFilesDataset의 연결된 서비스 및 파일 경로에 대한 선택 항목":::

1. **디버그** 를 선택하여 파이프라인을 실행합니다. 자세한 Spark 로그를 보려면 Databricks 로그 링크를 확인합니다.

    :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-run-output.png" alt-text="출력에서 Databricks 로그에 연결":::

    Azure Storage Explorer를 사용하여 데이터 파일을 확인할 수도 있습니다.

    > [!NOTE]
    > Data Factory 파이프라인 실행과 상관관계 지정을 위해 이 예제에서는 Data Factory의 파이프라인 실행 ID를 출력 폴더에 추가합니다. 이렇게 하면 각 실행에 의해 생성된 파일을 추적하는 데 도움이 됩니다.
    > :::image type="content" source="media/solution-template-Databricks-notebook/verify-data-files.png" alt-text="추가된 파이프라인 실행 ID":::

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
