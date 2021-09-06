---
title: Azure Arc 지원 기계 학습(미리 보기)
description: Azure Machine Learning에서 기계 학습 모델을 학습하도록 Azure Arc 지원 Kubernetes 클러스터 구성
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.openlocfilehash: 4a588c2668fee342da074c911ce99fe29a7aa06a
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606182"
---
# <a name="configure-azure-arc-enabled-machine-learning-preview"></a>Azure Arc 지원 기계 학습 구성(미리 보기)

학습용 Azure Arc 지원 기계 학습을 구성하는 방법에 대해 알아봅니다.

## <a name="what-is-azure-arc-enabled-machine-learning"></a>Azure Arc 지원 기계 학습이란?

Azure Arc를 사용하면 온-프레미스, 다중 클라우드 또는 에지 등 모든 Kubernetes 환경에서 Azure 서비스를 실행할 수 있습니다.

Azure Arc 지원 기계 학습을 사용하면 Azure Machine Learning에서 기계 학습 모델을 학습 및 관리하도록 Azure Arc 지원 Kubernetes 클러스터를 구성하여 사용할 수 있습니다.

Azure Arc 지원 기계 학습은 다음의 학습 시나리오를 지원합니다.

* CLI(v2)를 사용하여 모델 학습
  * 분산 학습
  * 하이퍼 매개 변수 비우기
* Azure Machine Learning Python SDK를 사용하여 모델 학습
  * 하이퍼 매개 변수 조정
* 기계 학습 파이프라인 빌드 및 사용
* 아웃바운드 프록시 서버를 사용하여 온-프레미스 모델 학습
* NFS 데이터 저장소를 통해 온-프레미스 모델 학습

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure를 구독하고 있지 않다면 시작하기 전에 [무료 계정을 만듭니다](https://azure.microsoft.com/free).
* Azure Arc 지원 Kubernetes 클러스터. 자세한 내용은 [Azure Arc에 기존 Kubernetes 클러스터 연결 빠른 시작 가이드](../azure-arc/kubernetes/quickstart-connect-cluster.md)를 참조하세요.
* [Azure Arc 지원 Kubernetes 클러스터 확장 필수 구성 요소](../azure-arc/kubernetes/extensions.md#prerequisites)를 처리합니다.
  * Azure CLI 버전 >= 2.24.0
  * Azure CLI k8s-extension 확장 버전 >= 0.4.3
* Azure Machine Learning 작업 영역 아직 구독하지 않는 경우 시작하기 전에 [작업 영역을 만드세요](how-to-manage-workspace.md?tabs=python).
  * Azure Machine Learning Python SDK 버전 >= 1.30

## <a name="deploy-azure-machine-learning-extension"></a>Azure Machine Learning 확장 배포

Azure Arc 지원 Kubernetes에는 Azure Policy 정의, 모니터링, 기계 학습 등 다양한 에이전트를 설치할 수 있는 클러스터 확장 기능이 있습니다. Azure Machine Learning을 사용하려면 *Microsoft.AzureML.Kubernetes* 클러스터 확장을 이용해 Kubernetes 클러스터에 Azure Machine Learning 에이전트를 배포해야 합니다. Azure Machine Learning 확장이 설치되면 클러스터를 Azure Machine Learning 작업 영역에 연결하고 학습에 사용할 수 있습니다.

`k8s-extension` Azure CLI 확장을 사용하여 Azure Arc 지원 Kubernetes 클러스터에 Azure Machine Learning 확장을 배포합니다.

1. Azure에 로그인
    
    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

1. Azure Machine Learning 확장 배포

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --configuration-settings enableTraining=True  --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
    ```

    >[!IMPORTANT]
    > 학습에 Azure Arc 지원 클러스터를 사용하도록 설정하려면 `enableTraining`을 **True** 로 설정해야 합니다. 이 명령을 실행하면 Arc 클러스터와 동일한 리소스 그룹에 Azure Service Bus 및 Azure Relay 리소스가 만들어집니다. 이러한 리소스는 클러스터와 통신하는 데 사용됩니다. 이를 수정하면 학습 컴퓨팅 대상으로 사용되는 연결된 클러스터가 중단됩니다.

    모델 학습을 위해 Azure Machine Learning 확장을 배포할 때 다음 설정을 구성할 수도 있습니다.

    |구성 설정 키 이름  |설명  |
    |--|--|
    | ```enableTraining``` | 기본값은 `False`입니다. `True`로 설정하여 기계 학습 모델 학습을 위한 확장 인스턴스를 만듭니다.  |
    |```logAnalyticsWS```  | 기본값은 `False`입니다. Azure Machine Learning 확장은 Azure LogAnalytics 작업 영역과 통합됩니다. LogAnalytics 작업 영역을 통해 로그 보기 및 분석 기능을 제공하려면 `True`로 설정합니다. LogAnalytics 작업 영역 비용이 적용될 수 있습니다.   |
    |```installNvidiaDevicePlugin```  | 기본값은 `True`입니다. Nvidia GPU 하드웨어에서 학습하려면 Nvidia 디바이스 플러그인이 필요합니다. Azure Machine Learning 확장은 Kubernetes 클러스터에 GPU 하드웨어가 있는지 여부에 관계없이 Azure Machine Learning 인스턴스를 만드는 동안 기본적으로 Nvidia 디바이스 플러그인을 설치합니다. 학습에 GPU를 사용할 계획이 없거나 Nvidia 디바이스 플러그인이 이미 설치되어 있는 경우 `False`로 설정합니다.  |
    |```installBlobfuseSysctl```  | ‘enableTraining=True’인 경우 기본값은 `True`입니다. 학습하려면 Blobfuse 1.3.7이 필요합니다. 확장 인스턴스를 만들 때 Azure Machine Learning에서 기본적으로 Blobfuse를 설치합니다. Blobfuse 1.37이 Kubernetes 클러스터에 이미 설치된 경우 이 구성 설정을 `False`로 설정합니다.   |
    |```installBlobfuseFlexvol```  | ‘enableTraining=True’인 경우 기본값은 `True`입니다. 학습하려면 Blobfuse Flexvolume이 필요합니다. Azure Machine Learning은 기본적으로 기본 경로에 Blobfuse Flexvolume을 설치합니다. Blobfuse Flexvolume이 Kubernetes 클러스터에 이미 설치된 경우 이 구성 설정을 `False`로 설정합니다.   |
    |```volumePluginDir```  | Blobfuse Flexvolume을 설치할 호스트 경로입니다. ‘enableTraining=True’인 경우에만 적용할 수 있습니다. 기본적으로 Azure Machine Learning은 기본 경로 */etc/kubernetes/volumeplugins* 에 Blobfuse Flexvolume을 설치합니다. 이 구성 설정을 지정하여 사용자 지정 설치 위치를 지정합니다.```   |

    > [!WARNING]
    > Nvidia 디바이스 플러그인, Blobfuse 및 Blobfuse Flexvolume이 클러스터에 이미 설치된 경우 다시 설치하면 확장 설치 오류가 발생할 수 있습니다. 설치 오류를 방지하려면 `installNvidiaDevicePlugin`, `installBlobfuseSysctl`, `installBlobfuseFlexvol`을 `False`로 설정합니다.

1. AzureML 확장 배포 확인

    ```azurecli
    az k8s-extension show --name amlarc-compute --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
    ```

    응답에서 `"extensionType": "amlarc-compute"` 및 `"installState": "Installed"`를 찾습니다. 처음 몇 분 동안 `"installState": "Pending"`을 표시할 수 있습니다.

    `installState`에서 **설치됨** 을 표시하면 클러스터를 가리키는 kubeconfig 파일이 있는 머신에서 다음 명령을 실행하여 *azureml* 네임스페이스에 있는 모든 Pod가 *실행 중* 상태인지 확인합니다.

   ```bash
    kubectl get pods -n azureml
   ```

## <a name="attach-arc-cluster-studio"></a>Arc 클러스터 연결(스튜디오)

Azure Arc 지원 Kubernetes 클러스터를 연결하면 작업 영역에서 학습에 사용할 수 있습니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)로 이동합니다.
1. **관리** 에서 **컴퓨팅** 을 선택합니다.
1. **연결된 컴퓨팅** 탭을 선택합니다.
1. **+새로 만들기 > Kubernetes(미리 보기)** 를 선택합니다.

   ![Kubernetes 클러스터 연결](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. 컴퓨팅 이름을 입력하고 드롭다운에서 Azure Arc 지원 Kubernetes 클러스터를 선택합니다.

   ![Kubernetes 클러스터 구성](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster.png)

1. (선택 사항) 고급 시나리오의 경우 구성 파일을 찾아서 업로드합니다.

   ![구성 파일 업로드](./media/how-to-attach-arc-kubernetes/upload-configuration-file.png)

1. **연결** 을 선택합니다.

    연결된 컴퓨팅 탭에서 클러스터의 초기 상태는 *만들기* 입니다. 클러스터가 연결되면 상태가 *성공* 으로 변경됩니다. 그렇지 않으면 상태가 *실패* 로 변경됩니다.

    ![리소스 프로비전](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="advanced-attach-scenario"></a>고급 연결 시나리오

JSON 구성 파일을 사용하여 Azure Arc 지원 Kubernetes 클러스터에서 고급 컴퓨팅 대상 기능을 구성합니다.

다음은 구성 파일의 예입니다.

```json
{
   "namespace": "amlarc-testing",
   "defaultInstanceType": "gpu_instance",
   "instanceTypes": {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
         }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         },
         "resources": {
            "requests": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            },
            "limits": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            }
         }
      }
   }
}
```

다음의 사용자 지정 컴퓨팅 대상 속성은 구성 파일을 사용하여 구성할 수 있습니다.

* `namespace` - 기본값은 `default` 네임스페이스입니다. 작업 및 Pod가 실행되는 네임스페이스입니다. 기본값이 아닌 네임스페이스를 설정하는 경우 네임스페이스가 이미 있어야 합니다. 네임스페이스를 만들려면 클러스터 관리자 권한이 필요합니다.

* `defaultInstanceType` - 학습 작업이 기본적으로 실행되는 인스턴스의 유형입니다. `instanceTypes` 속성이 지정된 경우 `defaultInstanceType`이 필수입니다. `defaultInstanceType` 값은 `instanceTypes` 속성에 정의된 값 중 하나여야 합니다.

    > [!IMPORTANT]
    > 현재 컴퓨터 대상 이름을 사용하는 작업 제출만 지원됩니다. 따라서 구성은 항상 defaultInstanceType으로 기본 설정됩니다.

* `instanceTypes` - 학습 작업에 사용되는 인스턴스 유형 목록입니다. 각 인스턴스 유형은 `nodeSelector` 및 `resources requests/limits` 속성으로 정의됩니다.

  * `nodeSelector` - 클러스터에서 노드를 식별하는 데 사용되는 하나 이상의 노드 레이블입니다. 클러스터 노드용 레이블을 만들려면 클러스터 관리자 권한이 필요합니다. 이 속성을 지정하면, 지정된 노드 레이블을 사용하여 노드에서 학습 작업을 실행하도록 예약됩니다. `nodeSelector`를 사용하여 워크로드 배치를 학습하기 위한 노드의 하위 집합을 대상으로 지정할 수 있습니다. 이는 클러스터에 다른 SKU가 있거나, CPU 또는 GPU 노드와 같은 다양한 유형의 노드가 있는 시나리오에서 유용할 수 있습니다. 예를 들어, 모든 GPU 노드에 대한 노드 레이블을 만들고 GPU 노드 풀에 대한 `instanceType`을 정의할 수 있습니다. 이렇게 하면 학습 작업을 예약할 때 GPU 노드 풀을 독점적으로 대상으로 지정합니다. 

  * `resources requests/limits` - 리소스 요청을 지정하고 실행할 학습 작업 Pod를 제한합니다. 기본값은 CPU 1개, 메모리 4GB입니다.

    >[!IMPORTANT]
    > 기본적으로 클러스터 리소스는 CPU 1개와 메모리 4GB를 사용하여 배포됩니다. 클러스터가 더 적은 리소스로 구성되면 작업을 실행하지 못합니다. 작업이 완료되었는지 확인하려면, 학습 작업 요구 사항에 따라 항상 리소스 요청/제한을 지정하는 것이 좋습니다. 다음은 기본 구성 파일의 예입니다.
    >
    > ```json
    > {
    >    "namespace": "default",
    >    "defaultInstanceType": "defaultInstanceType",
    >    "instanceTypes": {
    >       "defaultInstanceType": {
    >          "nodeSelector": null,
    >          "resources": {
    >             "requests": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             },
    >             "limits": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             }
    >          }
    >       }
    >    }
    > }
    > ```

## <a name="attach-arc-cluster-python-sdk"></a>Arc 클러스터 연결(Python SDK)

다음 Python 코드는 Azure Arc 사용 Kubernetes 클러스터를 연결하고 이를 학습용 컴퓨팅 대상으로 사용하는 방법을 보여 줍니다.

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
    print("creating new compute target...")

    amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id) 
    amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
    amlarc_compute.wait_for_completion(show_output=True)
    
     # For a more detailed view of current KubernetesCompute status, use get_status()
    print(amlarc_compute.get_status().serialize())
```

### <a name="advanced-attach-scenario"></a>고급 연결 시나리오

다음 코드는 네임 스페이스, nodeSelector 또는 리소스 요청/제한과 같은 고급 컴퓨팅 대상 속성을 구성하는 방법을 보여 줍니다.

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
   amlarc_compute = ws.compute_targets[amlarc_compute_name]
   if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
      print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")
   ns = "amlarc-testing"
    
   instance_types = {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
        }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         }
      }
   }

   amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns, default_instance_type="gpu_instance", instance_types = instance_types)
 
   amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
   amlarc_compute.wait_for_completion(show_output=True)
    
   # For a more detailed view of current KubernetesCompute status, use get_status()
   print(amlarc_compute.get_status().serialize())
```

## <a name="next-steps"></a>다음 단계

- [CLI(v2)를 사용하여 모델 학습](how-to-train-cli.md)
- [학습 실행 구성 및 제출](how-to-set-up-training-targets.md)
- [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
- [Scikit-learn을 사용하여 모델 학습](how-to-train-scikit-learn.md)
- [TensorFlow 모델 학습](how-to-train-tensorflow.md)
- [PyTorch 모델 학습](how-to-train-pytorch.md)
- [Azure Machine Learning 파이프라인을 사용하여 학습](how-to-create-machine-learning-pipelines.md)
- [아웃바운드 프록시 서버를 사용하여 온-프레미스 모델 학습](../azure-arc/kubernetes/quickstart-connect-cluster.md#5-connect-using-an-outbound-proxy-server)
