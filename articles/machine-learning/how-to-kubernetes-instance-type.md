---
title: Kubernetes 인스턴스 유형을 만들고 선택하는 방법(미리 보기)
description: Azure Machine Learning 워크로드를 학습하고 유추하기 위해 Azure Arc 사용 가능한 Kubernetes 클러스터 인스턴스 유형을 만들고 선택합니다.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: mlops
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0bea2b0fe1b5d08f343fd31ffc14b9252039a49e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103707"
---
# <a name="create-and-select-kubernetes-instance-types-preview"></a>Kubernetes 인스턴스 유형 만들기 및 선택(미리 보기)

Azure Arc 지원 Kubernetes 클러스터에서 Azure Machine Learning 학습 및 추론 워크로드를 위해 Kubernetes 인스턴스를 만들고 선택하는 방법을 알아봅니다.

## <a name="what-are-instance-types"></a>인스턴스 유형이란?

인스턴스 유형은 학습 및 유추 워크로드에 대해 특정 유형의 컴퓨팅 노드를 대상으로 지정할 수 있는 Azure Machine Learning 개념입니다.  Azure VM의 경우 인스턴스 유형에 대한 예제는 `STANDARD_D2_V3` 입니다.

Kubernetes 클러스터에서 인스턴스 유형은 다음 두 요소로 정의됩니다.

* [nodeSelector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector) - `nodeSelector` 을 사용하면 Pod가 실행되어야 하는 노드를 지정할 수 있습니다.  노드에는 해당 레이블이 있어야 합니다.
* [resources](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) - `resources` 섹션은 Pod에 대한 컴퓨팅 리소스(CPU, 메모리 및 Nvidia GPU)를 정의합니다.

## <a name="create-instance-types"></a>인스턴스 유형 만들기

인스턴스 유형은 Azure Machine Learning 확장과 함께 설치된 CRD(사용자 지정 리소스 정의)로 표시됩니다.  

### <a name="create-a-single-instance-type"></a>단일 인스턴스 유형 만들기

새 인스턴스 유형을 만들려면 인스턴스 유형 CRD에 대한 새 사용자 지정 리소스를 만듭니다.  

예를 들어 CRD를 지정합니다. `my_instance_type.yaml`

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceType
metadata:
  name: myinstancetypename
spec:
  nodeSelector:
    mylabel: mylabelvalue
  resources:
    limits:
      cpu: "1"
      nvidia.com/gpu: 1
      memory: "2Gi"
    requests:
      cpu: "700m"
      memory: "1500Mi"
```

명령을 `kubectl apply` 사용하여 새 인스턴스 유형을 만듭니다.

```bash
kubectl apply -f my_instance_type.yaml
```

이 작업은 다음 속성을 가진 인스턴스 형식을 만듭니다.

- Pod는 레이블이 인 노드에서만 `mylabel: mylabelvalue` 예약됩니다.
- Pod에는 CPU 및 메모리의 리소스 요청이 `700m` `1500Mi` 할당됩니다. 
- Pod에는 `1` CPU, 메모리 및 `2Gi` `1` Nvidia GPU의 리소스 제한이 할당됩니다.

> [!NOTE]
> CRD를 지정할 때 다음 규칙을 적어 두는 것이 좋습니다.
> - Nvidia GPU 리소스는 `limits` 섹션에만 지정됩니다.  자세한 내용은 [Kubernetes 설명서 를 참조하세요.](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/#using-device-plugins)
> - CPU 및 메모리 리소스는 문자열 값입니다.
> - CPU는 밀리코어( ) `100m` 또는 전체 숫자(와 동일)로 지정할 수 `"1"` `1000m` 있습니다.
> - 메모리는 전체 숫자 + 접미사(의 경우 )로 지정할 수 `1024Mi` `1024 MiB` 있습니다.

### <a name="create-multiple-instance-types"></a>여러 인스턴스 유형 만들기

CRD를 사용하여 한 번에 여러 인스턴스 유형을 만들 수도 있습니다.

예를 들어 CRD를 지정합니다. `my_instance_type.yaml`

```yaml
apiVersion: amlarc.azureml.com/v1alpha1
kind: InstanceTypeList
items:
  - metadata:
      name: cpusmall
    spec:
      resources:
        limits:
          cpu: "100m"
          nvidia.com/gpu: 0
          memory: "10Mi"
        requests:
          cpu: "100m"
          memory: "1Gi"

  - metadata:
      name: defaultinstancetype
    spec:
      resources:
        limits:
          cpu: "1"
          nvidia.com/gpu: 0
          memory: "1Gi"
        requests:
          cpu: "1"
          memory: "1Gi" 
```

명령을 `kubectl apply` 사용하여 여러 인스턴스 유형을 만듭니다.

```bash
kubectl apply -f my_instance_type.yaml
``` 

이 작업은 서로 다른 리소스 사양을 가진 두 개의 인스턴스 형식을 `defaultinstancetype` `cpusmall` 만듭니다. 기본 인스턴스 유형에 대한 자세한 내용은 이 문서의 [기본 인스턴스 유형](#default-instance-types) 섹션을 참조하세요.

## <a name="default-instance-types"></a>기본 인스턴스 유형

학습 또는 유추 워크로드가 인스턴스 유형 없이 제출되면 기본 인스턴스 유형을 사용합니다.  

Kubernetes 클러스터에 대한 기본 인스턴스 형식을 지정하려면 이름을 사용하여 인스턴스 유형을 만들고 `defaultinstancetype` 다른 인스턴스 형식과 마찬가지로 해당 `nodeSelector` 및 속성을 `resources` 정의합니다.  인스턴스 유형이 자동으로 기본값으로 인식됩니다.

기본 인스턴스 유형이 정의되지 않은 경우 다음 기본 동작이 적용됩니다.

* nodeSelector가 적용되지 않습니다. 즉, Pod가 모든 노드에서 예약될 수 있습니다.
* 워크로드의 Pod에는 기본 리소스가 할당됩니다.

    ```yaml
    resources:
        limits:
        cpu: "0.6"
        memory: "1536Mi"
        requests:
        cpu: "0.6"
        memory: "1536Mi"
    ```

> [!IMPORTANT]
> 기본 인스턴스 유형은 클러스터에서 InstanceType 사용자 지정 리소스로 표시되지 않지만 모든 클라이언트(Azure Machine Learning Studio, Azure CLI, Python SDK)에 표시됩니다.

## <a name="select-an-instance-type-for-training-workloads"></a>학습 워크로드에 대한 인스턴스 유형 선택

Azure Machine Learning 2.0 CLI를 사용하여 학습 작업의 인스턴스 유형을 선택하려면 `compute` YAML 사양 파일에서 섹션의 일부로 해당 이름을 지정합니다.  

```yaml
command: python -c "print('Hello world!')"
environment:
  docker:
    image: python
compute:
  target: azureml:<compute_target_name>
  instance_type: <instance_type_name>
```

위의 예제에서 을 `<compute_target_name>` Kubernetes 컴퓨팅 대상의 이름으로, 를 `<instance_type_name>` 선택하려는 인스턴스 유형의 이름으로 바꿉

> [!TIP]
> 기본 인스턴스 유형은 의도적으로 리소스를 거의 사용하지 않습니다. 모든 기계 학습 워크로드가 적절한 리소스로 성공적으로 실행되도록 하려면 사용자 지정 인스턴스 유형을 만드는 것이 좋습니다.

## <a name="select-an-instance-type-for-inferencing-workloads"></a>추론 워크로드에 대한 인스턴스 유형 선택

Azure Machine Learning 2.0 CLI를 사용하여 워크로드를 유추하기 위한 인스턴스 유형을 선택하려면 섹션의 일부로 해당 이름을 `deployments` 지정합니다.  예:

```yaml
type: online
auth_mode: key
target: azureml:<your compute target name>
traffic:
  blue: 100

deployments:
  - name: blue
    app_insights_enabled: true
    model: 
      name: sklearn_mnist_model
      version: 1
      local_path: ./model/sklearn_mnist_model.pkl
    code_configuration:
      code: 
        local_path: ./script/
      scoring_script: score.py
    instance_type: <instance_type_name>
    environment: 
      name: sklearn-mnist-env
      version: 1
      path: .
      conda_file: file:./model/conda.yml
      docker:
        image: mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210727.v1
```

위의 예제에서 `<instance_type_name>` 을 선택하려는 인스턴스 형식의 이름으로 바꿉합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Arc 지원 기계 학습 구성(미리 보기)](how-to-attach-arc-kubernetes.md)
- [CLI(v2)를 사용한 모델 학습(작업 만들기)](how-to-train-cli.md)
- [온라인 엔드포인트를 사용하여 기계 학습 모델 배포 및 점수 매기기(미리 보기)](how-to-deploy-managed-online-endpoints.md)
