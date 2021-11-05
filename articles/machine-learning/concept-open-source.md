---
title: 오픈 소스 기계 학습 통합
titleSuffix: Azure Machine Learning
description: 오픈 소스 Python 기계 학습 프레임워크를 사용하여 Azure Machine Learning에서 엔드투엔드 기계 학습 솔루션을 학습, 배포, 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.custom: mktng-kw-nov2021
ms.date: 11/04/2021
ms.openlocfilehash: 9809169a8da6965c8d1fdbb741ddd4817f8d2af7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850188"
---
# <a name="use-open-source-machine-learning-libraries-and-platforms-with-azure-machine-learning"></a>Azure Machine Learning 오픈 소스 기계 학습 라이브러리 및 플랫폼 사용

이 문서에서는 Azure Machine Learning 사용할 수 있는 오픈 소스 Python 기계 학습 라이브러리 및 플랫폼에 대해 알아봅니다. 원하는 오픈 소스 프로젝트를 사용하여 엔드투엔드 기계 학습 프로세스를 학습, 배포 및 관리합니다.  Jupyter Notebook, Visual Studio Code 같은 개발 도구를 사용하여 Azure Machine Learning에서 기존 모델과 스크립트를 활용합니다.  

## <a name="train-open-source-machine-learning-models"></a>오픈 소스 기계 학습 모델 학습

기계 학습의 학습 프로세스에는 작업을 완수하거나 문제를 해결하기 위해 데이터에 알고리즘을 적용하는 방법이 포함됩니다. 문제에 따라 작업과 데이터에 가장 적합한 여러 알고리즘을 선택할 수 있습니다. 기계 학습으로 해결할 수 있는 문제에 대한 자세한 내용은 [딥 러닝 및 기계 학습 문서](./concept-deep-learning-vs-machine-learning.md) 및 기계 학습 알고리즘 [치트 시트를 참조하세요.](algorithm-cheat-sheet.md)

### <a name="classical-machine-learning-scikit-learn"></a>고전적 기계 학습: scikit-learn

분류, 클러스터링 및 회귀와 같은 기존 기계 학습 알고리즘 작업과 관련된 학습 작업의 경우 scikit-learn과 같은 작업을 사용할 수 있습니다. 꽃 분류 모델을 학습하는 방법을 알아보려면 [scikit-learn을 사용하여 학습하는 방법 문서를 참조하세요.](how-to-train-scikit-learn.md)

### <a name="neural-networks-pytorch-tensorflow-keras"></a>신경망: PyTorch, TensorFlow, Keras

기계 학습의 하위 집합인 신경망이라고 하는 오픈 소스 기계 학습 알고리즘은 Azure Machine Learning에서 딥 러닝 모델을 학습하는 데 유용합니다.

오픈 소스 딥 러닝 프레임워크와 방법 가이드는 다음과 같습니다.

 *  [PyTorch](https://github.com/pytorch/pytorch): [전이 학습 사용을 통한 딥 러닝 이미지 분류 모델 학습](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [TensorFlow 사용을 통한 수기 숫자 인식](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [keras 사용을 통한 이미지 분석용 신경망 빌드](how-to-train-keras.md)

### <a name="transfer-learning"></a>이전 학습

처음부터 딥 러닝 모델을 교육하려면 많은 양의 시간, 데이터, 컴퓨팅 리소스가 필요한 경우가 많습니다. 전이 학습을 사용하면 학습 프로세스를 단축할 수 있습니다. 전이 학습은 한 가지 문제를 해결하는 과정에서 획득한 지식을 다른 관련 문제에 적용하는 기술입니다. 즉, 기존 모델을 사용하여 용도를 변경할 수 있습니다. 전이 학습에 대한 자세한 내용은 [딥 러닝과 기계 학습 비교 문서](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning)를 참조하세요.

### <a name="reinforcement-learning-ray-rllib"></a>강화 학습: Ray RLLib

강화 학습은 동작, 상태, 보상을 사용하여 모델을 학습하는 AI 기술입니다. 강화 학습 에이전트는 환경의 현재 상태에 따라 지정된 보상을 최대화하는 미리 정의된 동작 집합을 사용하는 방법을 학습합니다. 

[Ray RLLib](https://github.com/ray-project/ray) 프로젝트에는 학습 프로세스 전체에서 스케일링 성능을 높일 수 있는 기능 집합이 포함됩니다. 강화 학습 에이전트는 최적화된 작업 완수 방법을 학습하려고 하기 때문에 Ray RLLib의 반복적인 프로세스는 시간과 리소스를 많이 소모합니다.  또한 Ray RLLib는 TensorFlow, PyTorch와 같은 딥 러닝 프레임워크를 기본적으로 지원합니다.  

Azure Machine Learning에서 Ray RLLib를 사용하는 방법은 [강화 학습 모델 학습 방법](how-to-use-reinforcement-learning.md)을 참조하세요.

### <a name="monitor-model-performance-tensorboard"></a>모델 성능 모니터링: TensorBoard

단일 또는 여러 모델을 학습하려면 모델이 예상대로 작동하는지 확인하기 위해 원하는 메트릭을 시각화하고 검사해야 합니다. [Azure Machine Learning에서 TensorBoard를 사용하여 실험 메트릭을 추적하고 시각화](./how-to-monitor-tensorboard.md)할 수 있습니다.

## <a name="responsible-ml-privacy-and-fairness"></a>책임 있는 ML: 개인 정보 보호 및 공정성

### <a name="preserve-data-privacy-with-differential-privacy"></a>차등 개인 정보 보호로 데이터 개인 정보 보호

기계 학습 모델을 학습하려면 데이터가 필요합니다. 데이터가 중요한 경우에는 안전하고 프라이빗한 데이터인지 반드시 확인해야 합니다. 차등 프라이버시는 데이터 세트에서 정보의 기밀성을 유지하는 기술입니다. 자세한 내용은 [데이터 프라이버시 유지](concept-differential-privacy.md)에 관한 문서를 참조하세요. 

[SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python)와 같은 오픈 소스 차등 프라이버시 도구 키트는 Azure Machine Learning 솔루션에서 [데이터의 프라이버시를 유지](how-to-differential-privacy.md)하는 데 도움이 됩니다.

### <a name="frameworks-for-interpretable-and-fair-models"></a>해석 가능하고 공정한 모델을 위한 프레임워크

기계 학습 시스템은 뱅킹, 교육, 의료 등 다양한 사회 분야에서 사용됩니다. 따라서 의도하지 않은 결과를 방지하기 위해 시스템은 수행하는 예측과 권장 사항에 대해 책임을 져야 합니다.

[InterpretML](https://github.com/interpretml/interpret/)과 Fairlearn https://github.com/fairlearn/fairlearn) 같은 오픈 소스 프레임워크로 Azure Machine Learning에서 작업을 수행하면 보다 투명하고 공정한 기계 학습 모델을 만들 수 있습니다.

공정하고 해석 가능한 모델을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Machine Learning의 모델 해석력](how-to-machine-learning-interpretability.md)
- [기계 학습 모델 해석 및 설명](how-to-machine-learning-interpretability-aml.md)
- [AutoML 모델 설명](how-to-machine-learning-interpretability-automl.md)
- [기계 학습 모델의 공정성 완화](concept-fairness-ml.md)
- [모델 공정성 평가를 위한 Azure Machine Learning 사용](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>모델 배포

모델 학습을 완료하여 프로덕션 준비를 마치면 배포 방법을 선택해야 합니다. Azure Machine Learning은 다양한 배포 대상을 제공합니다. 자세한 내용은 [배포 위치 및 방법 문서](./how-to-deploy-and-where.md)를 참조하세요.

### <a name="standardize-model-formats-with-onnx"></a>ONNX를 통한 모델 형식 표준화

학습 후에는 학습된 매개 변수 등 모델의 콘텐츠가 직렬화되어 파일에 저장됩니다. 각 프레임워크에는 자체 serialization 형식이 있습니다. 다른 프레임워크와 도구를 사용하여 작업할 때는 프레임워크의 요구 사항에 따라 모델을 배포해야 합니다. 프로세스를 표준화하기 위해 ONNX(Open Neural Network Exchange) 형식을 사용할 수 있습니다. ONNX는 AI 모델을 위한 오픈 소스 형식입니다. ONNX에서는 프레임워크 간의 상호 운용성을 지원합니다. 즉, PyTorch와 같이 널리 사용되는 여러 기계 학습 프레임워크 중 하나에서 모델을 학습하고, ONNX 형식으로 변환하며, ML.NET과 같은 다른 프레임워크에서 ONNX 모델을 사용할 수 있습니다.

ONNX 및 ONNX 모델 사용 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [ONNX를 통한 ML 모델 만들기와 가속화](concept-onnx.md)
- [.NET 애플리케이션에서 ONNX 모델 사용](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>모델을 컨테이너로 패키지 및 배포

Docker와 같은 컨테이너 기술은 모델을 웹 서비스로 배포하는 한 가지 방법입니다. 컨테이너는 플랫폼과 리소스에 구애받지 않고 재현 가능한 소프트웨어 환경을 빌드하고 오케스트레이션할 수 있는 방법을 제공합니다. 핵심 기술을 통해 [미리 구성된 환경](./how-to-use-environments.md), [미리 구성된 컨테이너 이미지](./how-to-deploy-custom-container.md) 또는 사용자 지정 환경과 컨테이너 이미지를 사용하여 [Kubernetes 클러스터](./how-to-deploy-azure-kubernetes-service.md?tabs=python)에 기계 학습 모델을 배포할 수 있습니다. GPU 집약적 워크플로의 경우 NVIDIA Triton Inference 서버와 같은 도구를 사용하여 [GPU를 통해 예측할 수 있습니다](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>동형 암호를 통한 보안 배포

배포 보안은 배포 프로세스의 중요한 부분입니다. [암호화된 유추 서비스를 배포](how-to-homomorphic-encryption-seal.md)하려면 `encrypted-inference`오픈 소스 Python 라이브러리를 사용합니다. `encrypted inferencing` 패키지는 동형 암호 라이브러리인 [Microsoft SEAL](https://github.com/Microsoft/SEAL)을 기반으로 하는 바인딩을 제공합니다.

## <a name="machine-learning-operations-mlops"></a>기계 학습 작업(MLOps)

일반적으로 기계 학습을 위한 DevOps로 간주되고 있는 MLOps(Machine Learning Operations)를 통해 보다 투명하고 복원력 있고 재현 가능한 기계 학습 워크플로를 빌드할 수 있습니다. MLOps에 대한 자세한 내용은 [MLOps란? 문서](./concept-model-management-and-deployment.md)를 참조하세요. 

CI(연속 통합), CD(지속적인 배포)와 같은 DevOps 방식을 사용하여 엔드투엔드 기계 학습 수명 주기를 자동화하고 이를 중심으로 거버넌스 데이터를 캡처할 수 있습니다. [GitHub 작업에서 기계 학습 CI/CD 파이프라인](./how-to-github-actions-machine-learning.md)을 정의하여 Azure Machine Learning 학습 및 배포 작업을 실행할 수 있습니다. 

소프트웨어 종속성, 메트릭, 메타데이터, 데이터, 모델 버전 관리를 캡처하는 것은 투명하고 재현 가능하며 감사 가능한 파이프라인을 빌드하기 위한 MLOps 프로세스의 중요한 부분입니다. 이 작업의 경우 [Azure Databricks에서 기계 학습 모델을 학습](./how-to-use-mlflow-azure-databricks.md)할 때뿐만 아니라 [Azure Machine Learning에서도 MLFlow를 사용](how-to-use-mlflow.md)할 수 있습니다. [MLflow 모델을 Azure 웹 서비스로 배포](how-to-deploy-mlflow-models.md)할 수도 있습니다.