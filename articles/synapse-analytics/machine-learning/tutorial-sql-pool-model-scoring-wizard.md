---
title: '자습서: 전용 SQL 풀을 위한 기계 학습 모델 점수 매기기 마법사'
description: 기계 학습 모델 점수 매기기 마법사를 사용하여 전용 SQL 풀의 데이터를 보강하는 방법에 대한 자습서.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f21fb2d35dc7cb7383d756b4177924420961945f
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506380"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>자습서: 전용 SQL 풀을 위한 기계 학습 모델 점수 매기기 마법사(미리 보기)

예측 기계 학습 모델을 사용하여 전용 SQL 풀에서 데이터를 쉽게 보강하는 방법에 대해 알아봅니다. 현재 데이터 과학자가 만드는 모델은 예측 분석을 위해 데이터 전문가가 손쉽게 액세스할 수 있습니다. Azure Synapse Analytics의 데이터 전문가는 Azure Synapse SQL 풀에 배포하는 데 필요한 Azure Machine Learning 모델 레지스트리에서 모델을 선택하고 예측을 실행하여 데이터를 보강할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> - 예측 기계 학습 모델을 학습하고 Azure Machine Learning 모델 레지스트리에서 모델 등록
> - SQL 채점 마법사를 사용하여 전용 SQL 풀에서 예측 시작

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- Azure Synapse Analytics 작업 영역의 전용 SQL 풀. 자세한 내용은 [전용 SQL 풀 만들기](../quickstart-create-sql-pool-studio.md)를 참조하세요.
- Azure Synapse Analytics 작업 영역의 Azure Machine Learning 연결 서비스. 자세한 내용은 [Azure Synapse에서 Azure Machine Learning 연결된 서비스 만들기](quickstart-integrate-azure-machine-learning.md)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="train-a-model-in-azure-machine-learning"></a>Azure Machine Learning에서 모델 학습

시작하기 전에 sklearn의 버전이 0.20.3인지 확인합니다.

Notebook의 모든 셀을 실행하기 전에 컴퓨팅 인스턴스가 실행 중인지 확인합니다.

![Azure Machine Learning 컴퓨팅 확인을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Azure Machine Learning 작업 영역으로 이동합니다.

1. [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301)를 다운로드합니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에서 Azure Machine Learning 작업 영역을 엽니다.

1. **Notebook** > **파일 업로드** 로 이동합니다. 그런 다음, 다운로드한 **Predict NYC Taxi Tips.ipynb** 파일을 선택하고 업로드합니다.
   ![파일 업로드 단추의 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Notebook을 업로드하여 연 후, **모든 셀 실행** 을 선택합니다.

   셀 중 하나가 실패할 수도 있으며 Azure에 대한 인증을 요청하는 메시지가 표시됩니다. 셀 출력에서 이에 대한 내용을 확인하고 링크를 따라 코드를 입력하여 브라우저에서 인증합니다. 그런 다음, Notebook을 다시 실행합니다.

1. Notebook은 ONNX 모델을 학습시키고 MLflow에 등록합니다. **모델** 로 이동하여 새 모델이 제대로 등록되었는지 확인합니다.
   ![레지스트리의 모델을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. 또한 Notebook을 실행하면 테스트 데이터를 CSV 파일로 내보냅니다. 로컬 컴퓨터에 CSV 파일을 다운로드합니다. 이후에는 CSV 파일을 전용 SQL 풀로 가져오고 데이터를 사용하여 모델을 테스트합니다.

   CSV 파일은 Notebook 파일과 동일한 폴더에 만들어집니다. 바로 표시되지 않는 경우 파일 탐색기에서 **새로 고침** 을 선택합니다.

   ![CSV 파일을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>SQL 채점 마법사를 사용하여 예측 시작

1. Synapse Studio를 사용하여 Azure Synapse 작업 영역을 엽니다.

1. **데이터** > **연결됨** > **스토리지 계정** 으로 이동합니다. 기본 스토리지 계정에 `test_data.csv`를 업로드합니다.

   ![데이터 업로드를 위한 선택 항목을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. **개발** > **SQL 스크립트** 로 이동합니다. 새 SQL 스크립트를 만들어 전용 SQL 풀에 `test_data.csv`를 로드합니다.

   > [!NOTE]
   > 이 스크립트를 실행하기 전에 파일 URL을 업데이트합니다.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![전용 SQL 풀에 데이터 로드](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. **데이터** > **작업 영역** 으로 이동합니다. 전용 SQL 풀 테이블을 마우스 오른쪽 단추로 클릭하여 SQL 점수 매기기 마법사를 엽니다. **Machine Learning** > **모델을 사용하여 예측** 을 선택합니다.

   > [!NOTE]
   > Azure Machine Learning에 대해 연결된 서비스가 생성되지 않은 경우 기계 학습 옵션이 표시되지 않습니다. 이 자습서의 시작 부분에 나오는 [필수 구성 요소](#prerequisites)를 참조하세요.

   ![Machine Learning 옵션을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. 드롭다운 상자에서 연결된 Azure Machine Learning 작업 영역을 선택합니다. 이 단계에서는 선택한 Azure Machine Learning 작업 영역의 모델 레지스트리에서 기계 학습 모델 목록이 로드됩니다. 현재 ONNX 모델만 지원되므로 이 단계에서는 ONNX 모델만 표시됩니다.

1. 방금 학습시킨 모델을 선택한 후 **계속** 을 선택합니다.

   ![Azure Machine Learning 모델을 선택하는 것을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. 테이블 열을 모델 입력에 매핑하고 모델 출력을 지정합니다. 모델을 MLflow 형식으로 저장하고 모델 서명이 채워지면 이름 유사성을 기반으로 하는 논리를 사용하여 자동으로 매핑이 수행됩니다. 인터페이스는 수동 매핑도 지원합니다.

   **계속** 을 선택합니다.

   ![테이블-모델 매핑을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. 생성된 T-SQL 코드는 저장 프로시저 내부에 래핑됩니다. 이 경우 저장 프로시저에 이름을 제공해야 합니다. 메타데이터(버전, 설명 및 기타 정보)를 포함하는 모델 이진 파일은 Azure Machine Learning에서 전용 SQL 풀 테이블로 실제로 복사됩니다. 따라서 모델을 저장할 테이블을 지정해야 합니다. 

   **기존 테이블** 또는 **새로 만들기** 를 선택할 수 있습니다. 작업이 완료되면 **모델 배포 + 편집기 열기** 를 선택하여 모델을 배포하고 T-SQL 예측 스크립트를 생성합니다.

   ![저장 프로시저를 만들기 위한 선택 항목을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. 스크립트가 생성되면 **실행** 을 선택하여 채점을 실행하고 예측을 가져옵니다.

   ![채점 및 예측을 보여 주는 스크린샷](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure Synapse에서 새 Azure Machine Learning 연결된 서비스 만들기](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)
