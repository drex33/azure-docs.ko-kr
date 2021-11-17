---
title: Kubernetes 인스턴스 유형을 만들고 선택 하는 방법 (미리 보기)
description: Azure Machine Learning에서 학습 및 추론 워크 로드에 대 한 Azure Arc 사용 Kubernetes 클러스터 인스턴스 유형을 만들고 선택 합니다.
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46ae66658d5f07e1a44322bf64997a80d66db1c0
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485171"
---
# <a name="create-and-select-kubernetes-instance-types-preview"></a>Kubernetes 인스턴스 유형 만들기 및 선택 (미리 보기)

Azure Arc 사용 Kubernetes 클러스터에서 Azure Machine Learning 교육 및 추론 작업에 대 한 Kubernetes 인스턴스를 만들고 선택 하는 방법에 대해 알아봅니다.

## <a name="what-are-instance-types"></a>인스턴스 유형 이란?

인스턴스 유형은 학습 및 유추 작업을 위해 특정 유형의 계산 노드를 대상으로 지정할 수 있는 Azure Machine Learning 개념입니다.  Azure VM의 경우 인스턴스 형식에 대 한 예는 `STANDARD_D2_V3` 입니다.

Kubernetes 클러스터에서 인스턴스 형식은 두 개의 요소로 정의 됩니다.

* [Nodeselector](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#nodeselector) -a를 `nodeSelector` 사용 하 여 pod가 실행 되어야 하는 노드를 지정할 수 있습니다.  노드에는 해당 레이블이 있어야 합니다.
* [리소스](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) - `resources` 섹션은 pod에 대 한 계산 리소스 (CPU, 메모리 및 Nvidia GPU)를 정의 합니다.

## <a name="create-instance-types"></a>인스턴스 형식 만들기

인스턴스 형식은 Azure Machine Learning 확장과 함께 설치 되는 CRD (사용자 지정 리소스 정의)로 표시 됩니다.  

### <a name="create-a-single-instance-type"></a>단일 인스턴스 유형 만들기

새 인스턴스 유형을 만들려면 CRD 유형의 새 사용자 지정 리소스를 만듭니다.  

예를 들어, CRD가 제공 됩니다 `my_instance_type.yaml` .

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

명령을 사용 `kubectl apply` 하 여 새 인스턴스 유형을 만듭니다.

```bash
kubectl apply -f my_instance_type.yaml
```

이 작업은 다음 속성을 사용 하 여 인스턴스 유형을 만듭니다.

- Pod는 레이블이 있는 노드에 대해서만 예약 됩니다 `mylabel: mylabelvalue` .
- Pod에는 CPU 및 메모리에 대 한 리소스 요청이 할당 됩니다 `700m` `1500Mi` . 
- Pod에는 `1` CPU, `2Gi` 메모리 및 `1` Nvidia GPU의 리소스 제한이 할당 됩니다.

> [!NOTE]
> CRD를 지정 하는 경우 다음 규칙을 참고 하십시오.
> - Nvidia GPU 리소스는 섹션에만 지정 됩니다 `limits` .  자세한 내용은 [Kubernetes 설명서](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/#using-device-plugins)를 참조 하세요.
> - CPU 및 메모리 리소스는 문자열 값입니다.
> - CPU는 millicores ( `100m` ) 또는 전체 숫자 ( `"1"` 에 해당)에서 지정할 수 있습니다 `1000m` .
> - Memory는 전체 숫자 + 접미사로 지정할 수 있습니다 ( `1024Mi` 의 경우 `1024 MiB` ).

### <a name="create-multiple-instance-types"></a>여러 인스턴스 형식 만들기

또한 CRDs를 사용 하 여 한 번에 여러 인스턴스 유형을 만들 수 있습니다.

예를 들어, CRD가 제공 됩니다 `my_instance_type.yaml` .

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

명령을 사용 `kubectl apply` 하 여 여러 인스턴스 유형을 만듭니다.

```bash
kubectl apply -f my_instance_type.yaml
``` 

이 작업을 수행 하면 라는 두 개의 인스턴스 형식이 생성 되 `defaultinstancetype` 고, 다른 하나는 `cpusmall` 리소스 사양을 사용 하는 다른 형식입니다. 기본 인스턴스 유형에 대 한 자세한 내용은이 문서의 [기본 인스턴스 유형](#default-instance-types) 섹션을 참조 하세요.

## <a name="default-instance-types"></a>기본 인스턴스 유형

학습 또는 유추 작업은 인스턴스 유형 없이 제출 되는 경우 기본 인스턴스 유형을 사용 합니다.  

Kubernetes 클러스터에 대 한 기본 인스턴스 유형을 지정 하려면 이름을 사용 하 여 인스턴스 유형을 만들고 `defaultinstancetype` `nodeSelector` `resources` 다른 인스턴스 유형과 같이 및 속성을 정의 합니다.  인스턴스 유형이 기본값으로 자동으로 인식 됩니다.

기본 인스턴스 형식이 정의 되어 있지 않으면 다음과 같은 기본 동작이 적용 됩니다.

* NodeSelector를 적용 하지 않습니다. 즉, pod가 모든 노드에서 예약 될 수 있습니다.
* 작업의 pod에는 기본 리소스가 할당 됩니다.

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
> 기본 인스턴스 유형은 클러스터에 InstanceType 사용자 지정 리소스로 표시 되지 않지만 모든 클라이언트 (Azure Machine Learning studio, Azure CLI, Python SDK)에 표시 됩니다.

## <a name="select-an-instance-type-for-training-workloads"></a>학습 워크 로드에 대 한 인스턴스 유형 선택

Azure Machine Learning 2.0 CLI를 사용 하 여 학습 작업에 대 한 인스턴스 유형을 선택 하려면 `compute` yaml 사양 파일에서 섹션의 일부로 이름을 지정 합니다.  

```yaml
command: python -c "print('Hello world!')"
environment:
  docker:
    image: python
compute:
  target: azureml:<compute_target_name>
  instance_type: <instance_type_name>
```

위의 예제에서을 `<compute_target_name>` Kubernetes 계산 대상의 이름으로 바꾸고를 `<instance_type_name>` 선택 하려는 인스턴스 유형의 이름으로 바꿉니다.

> [!TIP]
> 기본 인스턴스 유형은 의도적으로 적은 리소스를 사용 합니다. 모든 machine learning 워크 로드가 적절 한 리소스를 사용 하 여 성공적으로 실행 되도록 하려면 사용자 지정 인스턴스 유형을 만드는 것이 좋습니다.

## <a name="select-an-instance-type-for-inferencing-workloads"></a>추론 워크 로드에 대 한 인스턴스 유형 선택

Azure Machine Learning 2.0 CLI를 사용 하 여 추론 워크 로드에 대 한 인스턴스 유형을 선택 하려면 해당 이름을 섹션의 일부로 지정 `deployments` 합니다.  예:

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

위의 예제에서을 선택 하려는 `<instance_type_name>` 인스턴스 형식의 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

- [Azure Arc 사용 기계 학습 구성 (미리 보기)](how-to-attach-arc-kubernetes.md)
- [CLI(v2)를 사용한 모델 학습(작업 만들기)](how-to-train-cli.md)
- [온라인 끝점 (미리 보기)을 사용 하 여 machine learning 모델 배포 및 점수 매기기](how-to-deploy-managed-online-endpoints.md)
