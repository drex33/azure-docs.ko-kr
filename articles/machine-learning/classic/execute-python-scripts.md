---
title: 'ML 스튜디오(클래식): Python 스크립트 실행 - Azure'
description: Python 스크립트 실행 모듈을 사용하여 Machine Learning 스튜디오(클래식) 실험 및 웹 서비스에서 Python 코드를 사용하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: b83cf9896c82b4821e38b0245a9330cbaab7fede
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689611"
---
# <a name="execute-python-machine-learning-scripts-in-machine-learning-studio-classic"></a>Machine Learning 스튜디오(클래식)에서 Python 기계 학습 스크립트 실행

**적용 대상:**  ![적용 대상:](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식)  ![적용되지 않는 대상:](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Python은 여러 데이터 과학자들의 도구 상자 중에서 유용하게 쓰이는 도구입니다. 데이터 탐색, 기능 추출, 모델 학습 및 유효성 검사, 배포 등 일반적인 기계 학습 워크플로의 모든 단계에서 사용됩니다.

이 문서에서는 Python 스크립트 실행 모듈을 사용하여 Machine Learning 스튜디오(클래식) 실험 및 웹 서비스에서 Python 코드를 사용하는 방법을 설명합니다.

## <a name="using-the-execute-python-script-module"></a>Python 스크립트 실행 모듈 사용

스튜디오(클래식)에서 Python의 기본 인터페이스는 [Python 스크립트 실행][execute-python-script] 모듈을 통하여 제공됩니다. 최대 세 개의 입력을 허용하고 [R 스크립트 실행][execute-r-script] 모듈과 유사한 출력을 최대 두 개까지 생성합니다. Python 코드는 `azureml_main`이라는 특별히 명명된 진입점 함수를 통해 매개 변수 상자에 입력됩니다.

![Python 스크립트 실행 모듈](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![모듈 매개 변수 상자의 샘플 Python 코드](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>입력 매개 변수

Python 모듈에 입력하는 내용은 Pandas DataFrames로 표시됩니다. `azureml_main` 함수는 최대 두 개의 선택적 Pandas DataFrames를 매개 변수로 받아들입니다.

입력 포트와 함수 매개 변수 간의 매핑은 위치와 관련됩니다.

- 첫 번째로 연결된 입력 포트는 함수의 첫 번째 매개 변수에 매핑됩니다.
- 두 번째 입력(연결된 경우)은 함수의 두 번째 매개 변수에 매핑됩니다.
- 세 번째 입력은 [추가 Python 모듈을 가져오는](#import-modules) 데 사용됩니다.

입력 포트가 `azureml_main` 함수에 매핑되는 방법에 대한 자세한 의미 체계는 아래에 표시되어 있습니다.

![입력 포트 구성 표 및 결과 Python 서명](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>반환 값 출력

`azureml_main` 함수는 튜플, 목록 또는 NumPy 배열과 같은 Python [시퀀스](https://docs.python.org/2/c-api/sequence.html) 내부에 패키지된 단일 Pandas DataFrame을 반환해야 합니다. 해당 시퀀스의 첫 번째 요소가 모듈의 첫 번째 출력 포트에 반환됩니다. 모듈의 두 번째 출력 포트는 [시각화](#visualizations)에 사용되며 반환 값이 필요하지 않습니다. 이 체계는 아래와 같습니다.

![입력 포트를 매개 변수에 매핑하고 출력 포트에 값 반환](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>입력 및 출력 데이터 형식 변환

스튜디오 데이터 세트는 Panda 데이터 프레임과 다릅니다. 따라서 스튜디오(클래식)의 입력 데이터 세트는 Pandas DataFrames로 변환되고, 출력 데이터 프레임은 다시 스튜디오(클래식) 데이터 세트로 변환됩니다. 변환 프로세스 중에는 다음과 같은 번역도 수행됩니다.

 **Python 데이터 형식** | **스튜디오 번역 프로시저** |
| --- | --- |
| 문자열 및 숫자| 있는 그대로 번역 |
| Pandas ‘NA’ | ‘누락 값’으로 번역 |
| 인덱스 벡터 | 지원되지 않음* |
| 문자열이 아닌 열 이름 | 열 이름에서 `str` 호출 |
| 중복된 열 이름 | 숫자 접미사 (1), (2), (3) 등을 추가합니다.

**Python 함수의 모든 입력 데이터 프레임에는 항상 0부터 시작하여 행의 개수에서 1을 뺀 수까지의 64비트 숫자 인덱스가 있습니다.*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>기존 Python 스크립트 모듈 가져오기

Python을 실행하는 데 사용되는 백 엔드는 널리 사용되는 과학적 Python 배포판인 [Anaconda](https://www.anaconda.com/distribution/)를 기반으로 합니다. 데이터 중심 워크로드에 사용되는 가장 일반적인 Python 패키지가 거의 200개 가까이 포함되어 있습니다. 스튜디오(클래식)는 현재 Pip 또는 Conda와 같은 패키지 관리 시스템을 사용하여 외부 라이브러리를 설치하고 관리하는 것을 지원하지 않습니다.  추가 라이브러리를 통합해야 하는 경우 다음 시나리오를 지침으로 사용합니다.

일반적으로 사용하는 사례는 기존 Python 스크립트를 스튜디오(클래식) 실험으로 통합하는 것입니다. [Python 스크립트 실행][execute-python-script] 모듈은 세 번째 입력 포트에서 Python 모듈을 포함하는 Zip 파일을 허용합니다. 이 파일은 런타임에 입력 프레임워크에서 압축이 풀리며, 파일의 내용은 Python 인터프리터의 라이브러리 경로에 추가됩니다. 그러면 `azureml_main` 진입점 함수가 이러한 모듈을 직접 가져올 수 있습니다. 

예를 들어, 간단한 “Hello, World” 함수를 포함하는 Hello.py라는 파일을 살펴보겠습니다.

![Hello.py 파일의 사용자 정의 함수](./media/execute-python-scripts/figure4.png)

다음으로 Hello.py을 포함하는 Hello.zip이라는 파일을 만듭니다.

![사용자 정의 Python 코드가 포함된 Zip 파일](./media/execute-python-scripts/figure5.png)

Zip 파일을 데이터 세트로 스튜디오(클래식)에 업로드합니다. 그리고 다음 이미지에 나와 있는 것처럼 **Python 스크립트 실행** 모듈의 세 번째 입력 포트에 연결하여 Hello.zip 파일에서 Python 코드를 사용하는 실험을 만들고 실행합니다.

![Python 스크립트 실행 모듈에 대한 입력으로 Hello.zip을 사용하는 샘플 실험](./media/execute-python-scripts/figure6a.png)

![Zip 파일로 업로드된 사용자 정의 Python 코드](./media/execute-python-scripts/figure6b.png)

모듈 출력에는 zip 파일의 패키지가 해제되었으며 `print_hello` 함수가 실행되었음이 표시됩니다.

![사용자 정의 함수를 보여 주는 모듈 출력](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure Storage Blob 액세스

다음 단계를 통하여 Azure Blob Storage 계정에 저장된 데이터에 액세스할 수 있습니다.

1. [Python용 Azure Blob Storage 패키지](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip)를 로컬로 다운로드합니다.
1. Zip 파일을 스튜디오(클래식) 작업 영역에 데이터 세트로 업로드합니다.
1. `protocol='http'`를 사용하여 BlobService 개체 만들기

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 스토리지 **구성** 설정 탭에서 **필수 보안 전송** 을 사용하지 않는 것으로 설정

![Azure Portal에서 필수 보안 전송 사용하지 않음](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python 스크립트 운영 가능화

점수 매기기 실험에서 모든 [Python 스크립트 실행][execute-python-script] 모듈은 웹 서비스로 게시될 때 호출됩니다. 예를 들어 아래 이미지는 단일 Python 식을 평가하는 코드가 포함된 채점 실험을 보여 줍니다.

![웹 서비스의 스튜디오 작업 영역](./media/execute-python-scripts/figure3a.png)

![Python Pandas 식](./media/execute-python-scripts/python-script-with-python-pandas.png)

이 실험에서 만든 웹 서비스는 다음 작업을 수행합니다.

1. Python 식을 입력으로 사용(문자열로)
1. Python 인터프리터에 Python 식 보내기
1. 식과 계산된 결과가 모두 포함된 테이블을 반환합니다.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>시각화 작업

MatplotLib를 사용하여 만든 플롯은 [Python 스크립트 실행][execute-python-script]을 통해 반환될 수 있습니다. 그러나 R을 사용할 때 플롯은 이미지로 자동 리디렉션되지 않으므로, 사용자는 명시적으로 모든 플롯을 PNG 파일로 저장해야 합니다.

MatplotLib에서 이미지를 생성하려면 다음 단계를 수행해야 합니다.

1. 기본 Qt 기반 렌더러에서 “AGG”로 백 엔드를 전환합니다.
1. 새 그림 개체를 만듭니다.
1. 축을 가져오고 축에 모든 플롯을 생성합니다.
1. PNG 파일로 그림을 저장합니다.

Pandas에서 scatter_matrix 함수를 사용하여 산점도 플롯 매트릭스를 생성하는 이 프로세스는 다음 이미지에 설명되어 있습니다.

![MatplotLib 그림을 이미지에 저장하기 위한 코드](./media/execute-python-scripts/figure-v1-8.png)

![그림을 보기 위해 Python 스크립트 실행 모듈에서 시각화를 클릭](./media/execute-python-scripts/figure-v2-9a.png)

![Python 코드를 사용하여 샘플 실험을 위한 플롯 시각화](./media/execute-python-scripts/figure-v2-9b.png)

여러 그림을 서로 다른 이미지에 저장하여 반환할 수 있습니다. 스튜디오(클래식) 런타임은 모든 이미지를 선택하고 시각화를 위해 연결합니다.

## <a name="advanced-examples"></a>고급 예제

스튜디오(클래식)에 설치되는 Anaconda 환경에는 NumPy, SciPy, Scikits-Learn 등의 일반 패키지가 포함되어 있습니다. 해당 패키지는 기계 학습 파이프라인의 데이터 처리에 효과적으로 사용될 수 있습니다.

예를 들어 다음 실험과 스크립트에서는 데이터 세트의 기능 중요도 점수를 계산하기 위한 Scikits-Learn의 앙상블 학습자 사용에 대해 설명합니다. 점수는 다른 모델에 공급하기 전에 감독 모드 기능 선택을 수행하는 데 사용할 수 있습니다.

중요도 점수를 계산하고 점수에 따라 기능의 순서를 지정하는 데 사용되는 Python 함수는 다음과 같습니다.

![점수별로 기능의 순위를 지정하는 함수](./media/execute-python-scripts/figure8.png)

다음 실험은 Machine Learning 스튜디오(클래식)의 "Pima Indian Diabetes" 데이터 세트에 있는 기능의 중요도 점수를 컴퓨팅하여 반환합니다.

![Pima Indian Diabetes 데이터 세트에서 Python을 사용하여 기능의 순위를 지정하는 실험](./media/execute-python-scripts/figure9a.png)

![Python 스크립트 실행 모듈의 출력 시각화](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>제한 사항

[Python 스크립트 실행][execute-python-script] 모듈에는 현재 다음과 같은 제한 사항이 있습니다.

### <a name="sandboxed-execution"></a>샌드박스가 적용된 실행

Python 런타임은 현재 샌드박스가 적용되며, 네트워크 또는 로컬 파일 시스템에 대한 액세스를 영구적으로 허용하지 않습니다. 모듈이 완료되면 로컬에 저장된 모든 파일이 격리되어 삭제됩니다. Python 코드는 현재 디렉터리와 하위 디렉터리를 제외하고, 코드가 실행되는 컴퓨터에 있는 대부분의 디렉터리에 액세스할 수 없습니다.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>정교한 개발 및 디버깅 지원 부족

현재 Python 모듈에서는 Intellisense와 디버깅 같은 IDE 기능을 지원하지 않습니다. 또한 모듈이 런타임에서 실패하는 경우 전체 Python 스택 추적을 사용할 수 있습니다. 그러나 스택 추적은 모듈의 출력 로그에서 확인해야 합니다. 현재로서는 IPython과 같은 환경에서 Python 스크립트를 개발하고 디버그한 다음 모듈에 코드를 가져오는 것이 좋습니다.

### <a name="single-data-frame-output"></a>단일 데이터 프레임 출력

Python 진입점은 단일 데이터 프레임을 출력으로 반환할 수만 있습니다. 현재로서는 학습된 모델과 같은 임의의 Python 개체를 스튜디오(클래식) 런타임에 직접 반환할 수 없습니다. 동일한 제한 사항이 있는 [R 스크립트 실행][execute-r-script]과 마찬가지로, 대부분의 경우 개체를 바이트 배열로 만든 다음 데이터 프레임 내부에서 반환할 수 있습니다.

### <a name="inability-to-customize-python-installation"></a>Python 설치를 사용자 지정할 수 없음

현재 사용자 지정 Python 모듈을 추가하는 유일한 방법은 이전에 설명한 zip 파일 메커니즘을 통한 것입니다. 작은 모듈의 경우 이 방법이 가능하지만, 모듈이 크거나(특히 네이티브 DLL이 있는 모듈) 모듈 수가 많은 경우에는 번거로운 방법입니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Python 개발자 센터](https://azure.microsoft.com/develop/python/)를 참조하세요.

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script