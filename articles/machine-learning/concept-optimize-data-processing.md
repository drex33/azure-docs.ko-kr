---
title: 데이터 처리 최적화
titleSuffix: Azure Machine Learning
description: 데이터 처리 속도를 최적화하기 위한 모범 사례 및 Azure Machine Learning에서 대규모 데이터 처리를 지원하는 통합에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: mldata
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: ecd2e128d40693696eb70eb0bd7f45de3a8b07ed
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429132"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 데이터 처리 최적화

이 문서에서는 데이터 처리 속도를 로컬 및 대규모로 최적화하는 데 도움이 되는 모범 사례에 대해 알아봅니다.

Azure Machine Learning은 데이터 처리를 위한 오픈 소스 패키지 및 프레임워크와 통합됩니다. 이러한 통합을 사용하고 이 문서의 모범 사례 권장 사항을 적용하면 로컬 및 대규모로 데이터 처리 속도를 향상시킬 수 있습니다.

## <a name="parquet-and-csv-file-formats"></a>Parquet 및 CSV 파일 형식

데이터 처리를 위한 일반적인 파일 형식은 CSV(쉼표로 구분된 값) 파일입니다. 그러나 기계 학습 작업에는 parquet 파일 형식이 권장됩니다.

[Parquet 파일](https://parquet.apache.org/)은 데이터를 이진 열 형식으로 저장합니다. 이 형식은 데이터를 여러 파일로 분할해야 하는 경우에 유용합니다. 또한 이 형식을 사용하면 기계 학습 실험의 관련 필드를 대상으로 지정할 수 있습니다. 20GB 데이터 파일에서 읽을 필요 없이 ML 모델을 학습하는 데 필요한 열을 선택하여 데이터 로드를 줄일 수 있습니다. Parquet 파일을 압축하여 처리 능력을 최소화하고 차지하는 공간을 줄일 수도 있습니다.

CSV 파일은 Excel에서 쉽게 편집하고 읽을 수 있으므로 일반적으로 데이터를 가져오고 내보내는 데 사용됩니다. CSV의 데이터는 행 기반 형식의 문자열로 저장되며, 파일을 압축하여 데이터 전송 로드를 줄일 수 있습니다. 압축되지 않은 CSV는 약 2~10배로 확장할 수 있으며, 압축된 CSV는 훨씬 더 많이 늘릴 수 있습니다. 따라서 메모리에 있는 5GB CSV가 컴퓨터에 있는 8GB RAM보다 더 잘 확장됩니다. 이 압축 동작은 데이터 전송 대기 시간을 늘릴 수 있으며, 처리할 데이터가 많은 경우에는 적합하지 않습니다. 

## <a name="pandas-dataframe"></a>pandas 데이터 프레임

[Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)은 일반적으로 데이터 조작 및 분석에 사용됩니다. `Pandas`는 1GB 미만의 데이터 크기에 적합하지만 파일 크기가 약 1GB에 도달하면 `pandas` 데이터 프레임의 처리 시간이 느려집니다. 스토리지에 있는 데이터의 크기가 데이터 프레임의 데이터 크기와 동일하지 않기 때문입니다. 예를 들어 CSV 파일의 데이터는 데이터 프레임에서 최대 10배 확장할 수 있기 때문에, 1GB CSV 파일은 데이터 프레임에서 10GB가 될 수 있습니다.

`Pandas`는 단일 스레드이므로 작업이 단일 CPU에서 한 번에 하나씩 수행됩니다. 분산 백 엔드를 사용하여 `Pandas`를 래핑하는 [Modin](https://modin.readthedocs.io/en/latest/) 같은 패키지를 사용하여 단일 Azure Machine Learning 컴퓨팅 인스턴스의 여러 가상 CPU로 워크로드를 쉽게 병렬화할 수 있습니다.

`Modin` 및 [Dask](https://dask.org)를 사용하여 작업을 병렬화하려면 이 코드 줄 `import pandas as pd`를 `import modin.pandas as pd`로 변경하면 됩니다.

## <a name="dataframe-out-of-memory-error"></a>데이터 프레임: 메모리 부족 오류 

일반적으로 데이터 프레임이 컴퓨터에서 사용 가능한 RAM 이상으로 확장되면 *메모리 부족* 오류가 발생합니다. 이 개념은 `Modin` 또는 `Dask`와 같은 분산 프레임워크에도 적용됩니다.  즉, 작업에서 클러스터의 각 노드에 있는 메모리에 데이터 프레임을 로드하려고 하지만 RAM이 부족합니다.

한 가지 해결 방법은 메모리의 데이터 프레임에 맞게 RAM을 늘리는 것입니다. 컴퓨팅 크기와 처리 능력이 RAM 크기의 두 배이면 좋습니다. 따라서 데이터 프레임이 10GB인 경우 RAM이 20GB 이상인 컴퓨팅 대상을 사용하면 데이터 프레임이 메모리에 맞게 조정되고 처리될 수 있습니다. 

가상 CPU(vCPU)가 여러 개 있을 경우, 하나의 파티션이 컴퓨터에서 각 vCPU가 사용할 수 있는 RAM에 충분히 맞아야 합니다. 즉, 16GB RAM, 4개 vCPU가 있는 경우, 각 vCPU당 약 2GB 데이터 프레임이 필요합니다.

### <a name="local-vs-remote"></a>로컬 및 원격 비교

특정 pandas 데이터 프레임 명령은 Azure Machine Learning으로 프로비전한 원격 VM보다 로컬 PC에서 작업할 때 더 빠르게 수행되는 것을 알 수 있습니다. 로컬 PC에는 일반적으로 페이지 파일이 설정되어 있으므로, 실제 메모리에 적합한 것보다 많은 것을 로드할 수 있습니다. 즉, 하드 드라이브가 RAM의 확장으로 사용됩니다. 현재 Azure Machine Learning VM은 페이지 파일 없이 실행되므로, 사용 가능한 실제 RAM만큼의 데이터만 로드할 수 있습니다. 

컴퓨팅이 많은 작업의 경우 처리 속도를 향상시키기 위해 더 큰 VM을 선택하는 것이 좋습니다.

Azure Machine Learning에 [사용할 수 있는 VM 시리즈 및 크기](concept-compute-target.md#supported-vm-series-and-sizes)에 대해 자세히 알아봅니다. 

RAM 사양은 [Dv2-Dsv2](../virtual-machines/dv2-dsv2-series-memory.md) 시리즈 또는 [NC 시리즈](../virtual-machines/nc-series.md)와 같은 해당 VM 시리즈 페이지를 참조하세요.

### <a name="minimize-cpu-workloads"></a>CPU 워크로드 최소화

컴퓨터에 RAM을 더 추가할 수 없는 경우, CPU 워크로드를 최소화하고 처리 시간을 최적화하는 데 도움이 되는 다음 기법을 적용할 수 있습니다. 이러한 권장 사항은 단일 시스템과 분산 시스템 모두에 적용됩니다.

방법 | Description
----|----
압축 | 더 적은 메모리를 사용하고 계산 결과에 큰 영향을 주지 않는 방식으로 데이터에 다른 표현을 사용합니다.<br><br>*예:* 항목을 문자열(항목당 약 10바이트 이상)로 저장하는 대신, 1바이트로 저장할 수 있는 부울, True 또는 False로 저장합니다.
청크 | 하위 집합(청크)의 메모리에 데이터를 로드하거나, 한 번에 하나의 하위 집합에 있는 데이터를 처리하거나, 여러 하위 집합을 병렬로 처리합니다. 이 방법은 모든 데이터를 처리해야 하지만 모든 데이터를 한 번에 메모리에 로드하지 않아도 되는 경우에 가장 잘 작동합니다. <br><br>*예:* 1년치 데이터를 한 번에 처리하는 대신, 한 달에 한 번 데이터를 로드하고 처리합니다.
인덱싱 | 관심 있는 데이터를 찾을 수 있는 위치를 알려 주는 요약 정보인 인덱스를 적용하고 사용합니다. 인덱싱은 전체 집합 대신 데이터의 하위 집합을 사용해야 하는 경우에 유용합니다.<br><br>*예:* 1년치에 달하는 판매 데이터가 월별로 정렬되어 있는 경우, 인덱스를 사용하여 처리하려는 월을 신속하게 검색할 수 있습니다.

## <a name="scale-data-processing"></a>데이터 처리 규모 조정

이전 권장 사항이 충분하지 않으며 데이터에 맞는 가상 머신을 가져올 수 없는 경우 다음을 수행할 수 있습니다. 

* `Spark` 또는 `Dask`와 같은 프레임워크를 사용하여 '메모리 부족' 데이터를 처리합니다. 이 옵션에서 데이터 프레임은 파티션별로 RAM에 로드되고 처리되며 최종 결과가 마지막에 수집됩니다.  

* 분산 프레임워크를 사용하여 클러스터로 스케일 아웃합니다. 이 옵션에서는 데이터 처리 로드가 분할되어 병렬로 작동하는 여러 CPU에서 처리되며 최종 결과가 마지막에 수집됩니다.

### <a name="recommended-distributed-frameworks"></a>권장되는 분산 프레임워크

다음 표에는 코드 기본 설정 또는 데이터 크기에 따라 Azure Machine Learning과 통합하도록 권장되는 분산 프레임워크가 나와 있습니다.

환경 또는 데이터 크기 | 권장
------|------
`Pandas`에 대해 잘 알고 있는 경우| `Modin` 또는 `Dask` 데이터 프레임
`Spark`를 선호하는 경우 | `PySpark`
1GB 미만의 데이터 | `Pandas` 로컬 **또는** 원격 Azure Machine Learning 컴퓨팅 인스턴스
10GB보다 큰 데이터| `Ray`, `Dask` 또는 `Spark`를 사용하여 클러스터로 이동

> [!TIP]
> 대규모 데이터 처리를 위해 [to_dask_dataframe()](/python/api/azureml-core/azureml.data.tabulardataset#to-dask-dataframe-sample-size-10000--dtypes-none--on-error--null---out-of-range-datetime--null--) 메서드를 사용하여 데이터 세트를 데이터 프레임에 로드합니다. 이 메서드는 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning의 데이터 수집 옵션](concept-data-ingestion.md)
* [데이터 세트 생성 및 등록](how-to-create-register-datasets.md)
