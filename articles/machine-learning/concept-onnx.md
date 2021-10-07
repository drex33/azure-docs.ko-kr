---
title: 'ONNX 모델: 유추 최적화'
titleSuffix: Azure Machine Learning
description: ONNX(Open Neural Network Exchange)를 사용하여 기계 학습 모델의 유추를 최적화하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 348589b133c126f7190955087cc6068e7af90062
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614045"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX 및 Azure Machine Learning: ML 모델을 만들고 가속화합니다

ONNX([Open Neural Network Exchange](https://onnx.ai))를 사용하여 기계 학습 모델의 유추를 최적화하는 방법을 알아봅니다. 추론 또는 모델 채점은 배포된 모델이 예측(주로 프로덕션 데이터에 대한 예측)에 사용되는 단계입니다. 

하드웨어 기능을 최대한 활용하기 위해 모델 및 유추 라이브러리를 조정해야 하므로, 추론(또는 모델 채점)을 위해 기계 학습 모델을 최적화하는 것은 어렵습니다. 다양한 종류의 플랫폼(클라우드/에지, CPU/GPU 등)에서 각각 다른 기능과 특징이 있기 때문에 최적의 성능을 얻기는 매우 어려워집니다. 다양한 플랫폼에서 실행해야 하는 다양한 프레임워크의 모델이 있는 경우 복잡성이 증가합니다. 프레임워크와 하드웨어의 다양한 조합을 모두 최적화하는 데 시간이 많이 걸립니다. 선호하는 프레임워크에서 한 번 학습하고 클라우드 또는 에지에서 실행하는 솔루션이 필요합니다. 그래서 필요한 것이 ONNX입니다.

Microsoft와 Microsoft 파트너 커뮤니티는 기계 학습 모델을 대표하기 위한 개방형 표준으로 ONNX를 만들었습니다. TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet, MATLAB 및 SparkML을 비롯한 [여러 프레임워크](https://onnx.ai/supported-tools)의 모델을 표준 ONNX 형식으로 내보내거나 변환할 수 있습니다. 모델이 ONNX 형식이면 다양한 플랫폼과 디바이스에서 실행할 수 있습니다.

[ONNX 런타임](https://onnxruntime.ai)은 ONNX 모델을 프로덕션에 배포하기 위한 고성능 유추 엔진입니다. 클라우드 및 에지 모두에 최적화되어 있으며, Linux, Windows 및 Mac에서 작동합니다. C++로 작성되었으며 다양한 환경에서 사용하기 위해 C, Python, C#, Java 및 Javascript(Node.js) API도 있습니다. ONNX 런타임은 DNN 및 기존 ML 모델을 모두 지원하며, NVidia GPU의 TensorRT, Intel 프로세서의 OpenVINO, Windows의 DirectML 등과 같은 다양한 하드웨어의 액셀러레이터와 통합됩니다. ONNX 런타임을 사용하면 광범위한 프로덕션 등급 최적화, 테스트 및 지속적인 개선의 이점을 누릴 수 있습니다.

ONNX 런타임은 Bing, Office 및 Azure Cognitive Services 같은 대규모 Microsoft 서비스에서 사용됩니다. 성능 향상은 여러 가지 요인에 따라 달라지지만 이러한 Microsoft 서비스는 __CPU에서 평균 2배의 성능 향상__ 을 보았습니다. Azure Machine Learning Service 외에도 ONNX Runtime은 다음을 비롯하여 Machine Learning 워크로드를 지원하는 다른 제품에서도 실행됩니다.
+ Windows: 런타임은 [windows Machine Learning](/windows/ai/windows-ml/) 의 일부로 Windows에 기본 제공되며 수백만 개의 장치에서 실행됩니다. 
+ Azure SQL 제품군: [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) 및 [Azure SQL Managed Instance](../azure-sql/managed-instance/machine-learning-services-overview.md)의 데이터에 대해 기본 채점을 실행합니다.
+ ML.NET: [ML.NET에서 ONNX 모델을 실행합니다](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![학습, 변환기 및 배포를 보여 주는 ONNX 흐름 다이어그램](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX 모델 가져오기

여러 가지 방법으로 ONNX 모델을 가져올 수 있습니다.
+ Azure Machine Learning에서 또는 [자동화된 Machine Learning 기능](concept-automated-ml.md#automl--onnx)을 사용하여 새 ONNX 모델을 학습합니다(이 문서의 맨 아래에 있는 예시 참조)
+ 다른 형식의 기존 모델을 ONNX로 변환합니다([자습서](https://github.com/onnx/tutorials) 참조) 
+ [ONNX Model Zoo](https://github.com/onnx/models)에서 미리 학습된 ONNX 모델을 가져옵니다.
+ [Azure Custom Vision 서비스](../cognitive-services/custom-vision-service/index.yml)에서 사용자 지정된 ONNX 모델을 생성합니다. 

이미지 분류, 개체 감지 및 텍스트 처리를 비롯한 많은 모델을 ONNX 모델로 대표할 수 있습니다. 성공적으로 변환할 수 없는 모델의 문제가 발생하는 경우, 사용한 해당 변환기의 GitHub에 문제를 제출하세요. 문제가 해결될 때까지 기존 형식 모델을 계속 사용할 수 있습니다.

## <a name="deploy-onnx-models-in-azure"></a>Azure에서 ONNX 모델 배포

Azure Machine Learning을 사용하면 ONNX 모델을 배포, 관리 및 모니터링할 수 있습니다. 표준 [배포 워크플로](concept-model-management-and-deployment.md)와 ONNX Runtime을 사용하면 클라우드에서 호스트되는 REST 엔드포인트를 만들 수 있습니다. 이 문서 끝에 있는 Jupyter Notebooks의 예를 참조하여 엔드포인트를 직접 만들어 보세요. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Python을 사용한 ONNX 런타임 설치 및 사용

ONNX 런타임에 대한 Python 패키지는 [PyPi.org](https://pypi.org)([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu))에서 사용할 수 있습니다. 설치하기 전에 [시스템 요구 사항](https://github.com/Microsoft/onnxruntime#system-requirements)을 읽어 보세요. 

 Python용 ONNX 런타임을 설치하려면 다음 명령 중 하나를 사용합니다. 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Python 스크립트에서 ONNX Runtime을 호출하려면 다음을 사용합니다.    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

일반적으로는 모델과 함께 제공되는 설명서에 모델 사용을 위한 입력과 출력이 나와 있습니다. [Netron](https://github.com/lutzroeder/Netron) 등의 시각화 도구를 사용하여 모델을 볼 수도 있습니다. ONNX Runtime에서는 모델 메타데이터, 입력 및 출력도 쿼리할 수 있습니다.    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

모델을 유추하려면 `run`을 사용하고 반환하려는 출력 목록(모든 출력을 반환하려면 비워 둠)과 입력 값의 맵을 전달합니다. 그러면 출력 목록이 결과로 반환됩니다.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

전체 Python API 참조는 [ONNX 런타임 참조 문서](https://onnxruntime.ai/docs/api/python-api.html)를 참조하세요.  

## <a name="examples"></a>예
ONNX 모델을 생성 및 배포하는 예시 Python Notebook은 [how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

다른 언어의 사용에 대한 샘플은 [Onnx 런타임 GitHub](https://github.com/microsoft/onnxruntime/tree/master/samples)에서 찾을 수 있습니다.

## <a name="more-info"></a>추가 정보

**ONNX** 에 대해 자세히 알아보거나 프로젝트에 참여하세요.
+ [ONNX 프로젝트 웹 사이트](https://onnx.ai)
+ [GitHub의 ONNX 코드](https://github.com/onnx/onnx)

**ONNX Runtime** 에 대해 자세히 알아보거나 프로젝트에 참여하세요.
+ [ONNX 런타임 프로젝트 웹 사이트](https://onnxruntime.ai)
+ [ONNX Runtime GitHub Repo](https://github.com/Microsoft/onnxruntime)
