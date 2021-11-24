---
title: '빠른 시작: ADLS Gen2에서 Pandas 데이터 프레임으로 데이터 읽기'
description: Azure Synapse Analytics의 Synapse Studio에서 Python을 사용하여 Azure Data Lake Storage Gen2 계정에서 Pandas 데이터 프레임으로 데이터를 읽습니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.custom: mode-other
ms.openlocfilehash: b92ec647b8937d8a92332f717cf682bfc88b217f
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133038081"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>빠른 시작: Azure Synapse Analytics의 ADLS Gen2에서 Pandas 데이터 프레임으로 데이터 읽기

이 빠른 시작에서는 Python을 사용하여 ADLS(Azure Data Lake Storage) Gen2에서 Azure Synapse Analytics의 Pandas 데이터 프레임으로 데이터를 쉽게 읽는 방법을 알아봅니다.

Synapse Studio Notebook에서 다음을 수행합니다.

- Azure Synapse Analytics 작업 영역에 연결된 Data Lake Storage Gen2의 컨테이너에 연결합니다.
- `spark.read.load`를 사용하여 PySpark Notebook에서 데이터 읽기
- `.toPandas()`를 사용하여 Pandas 데이터 프레임으로 데이터 변환

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- 기본 스토리지로 구성된 Data Lake Storage Gen2가 포함된 Synapse Analytics 작업 영역 - 사용자가 작업하는 Data Lake Storage Gen2 filesystem의 **Storage Blob 데이터 기여자** 가 되어야 합니다. 작업 영역을 만드는 방법에 대한 자세한 내용은 [Synapse 작업 영역 만들기](get-started-create-workspace.md)를 참조하세요.
- 작업 영역의 Apache Spark 풀 - [서버리스 Apache Spark 풀 만들기](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="upload-sample-data-to-adls-gen2"></a>ADLS Gen2에 샘플 데이터 업로드

1. Azure Portal에서 Synapse Studio가 사용하는 것과 동일한 Data Lake Storage Gen2에 컨테이너를 만듭니다. Azure Synapse Analytics 작업 영역에서 기본 연결된 스토리지 계정을 사용하려는 경우 이 단계를 건너뛸 수 있습니다.

1. Synapse Studio에서 **데이터** 를 클릭하고, **연결** 탭을 선택하고, **Azure Data Lake Storage Gen2** 아래에서 컨테이너를 선택합니다.

1. [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) 샘플 파일을 다운로드하여 컨테이너에 업로드합니다.

1. 업로드된 파일을 선택하고 **속성** 을 클릭한 후, **ABFSS 경로** 값을 복사합니다.

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>ADLS Gen2에서 Pandas 데이터 프레임으로 데이터 읽기

1. 왼쪽 창에서 **개발** 을 클릭합니다.

1. **+** 를 클릭하고 “Notebook”을 선택하여 새 Notebook을 만듭니다.

1. **연결 대상** 에서 Apache Spark 풀을 선택합니다. 없는 경우 **Apache Spark 풀 만들기** 를 클릭합니다.

1. Notebook 코드 셀에 다음 Python 코드를 붙여넣고 앞에서 복사한 ABFSS 경로를 삽입합니다.

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. 셀을 실행합니다.

몇 분 후에 다음과 같은 텍스트가 표시됩니다.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics란?](overview-what-is.md)
- [Azure Synapse Analytics 시작](get-started.md)
- [서버리스 Apache Spark 풀 만들기](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
