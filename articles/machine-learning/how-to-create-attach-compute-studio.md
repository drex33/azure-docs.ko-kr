---
title: 학습 및 배포 컴퓨팅 만들기(스튜디오)
titleSuffix: Azure Machine Learning
description: 스튜디오를 사용하여 기계 학습의 학습 및 배포 컴퓨팅 리소스(컴퓨팅 대상) 만들기
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/16/2021
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.openlocfilehash: e20b1ded0e4468bf600ee4022db18165b5edcbc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251588"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Azure Machine Learning 스튜디오에서 모델 학습 및 배포용 컴퓨팅 대상 만들기

이 문서에서는 Azure Machine 스튜디오에서 컴퓨팅 대상을 만들고 관리하는 방법을 알아봅니다.  다음을 사용하여 컴퓨팅 대상을 만들고 관리할 수도 있습니다.

* Azure Machine Learning 학습 SDK 또는 Azure Machine Learning용 CLI 확장
  * [컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)
  * [컴퓨팅 클러스터](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service 클러스터](how-to-create-attach-kubernetes.md)
  * [다른 컴퓨팅 리소스](how-to-attach-compute-targets.md)
* Azure Machine Learning용 [VS Code 확장](how-to-manage-resources-vscode.md#compute-clusters)

> [!IMPORTANT]
> 이 문서에 표시된 항목(미리 보기)은 현재 퍼블릭 미리 보기에서 확인할 수 있습니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>컴퓨팅 대상이란? 

Azure Machine Learning을 사용하여 다양한 리소스 또는 환경(총체적으로 [__컴퓨팅 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 함)에서 모델을 학습시킬 수 있습니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning 컴퓨팅, Azure HDInsight 또는 원격 가상 머신)입니다.  ["모델 배포 위치 및 방법"](how-to-deploy-and-where.md)의 설명에 따라 모델 배포용 컴퓨팅 대상을 만들 수도 있습니다.

## <a name="view-compute-targets"></a><a id="portal-view"></a>컴퓨팅 대상 보기

작업 영역의 모든 컴퓨팅 대상을 확인하려면 다음 단계를 수행합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)로 이동합니다.
 
1. __관리__ 에서 __컴퓨팅__ 을 선택합니다.

1. 맨 위에 있는 탭을 선택하여 각 형식의 컴퓨팅 대상을 표시합니다.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="컴퓨팅 대상 목록 보기":::

## <a name="start-creation-process"></a><a id="portal-create"></a>만들기 프로세스 시작

컴퓨팅 대상의 목록을 보려면 이전 단계를 수행합니다. 그런 다음, 다음과 같은 단계를 사용하여 컴퓨팅 대상을 만듭니다.

1. 맨 위에서 만들 컴퓨팅 형식에 해당하는 탭을 선택합니다.

1. 컴퓨팅 대상이 없으면 페이지 중간에 있는 **만들기** 를 선택합니다.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="컴퓨팅 대상 만들기":::

1. 컴퓨팅 리소스 목록이 표시되면 목록 위에 있는 **+새로 만들기** 를 선택합니다.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="새로 만들기 선택":::


1. 컴퓨팅 형식의 양식을 작성합니다.

    * [컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md?tabs=azure-studio#create)
    * [컴퓨팅 클러스터](#amlcompute)
    * [유추 클러스터](#inference-clusters)
    * [연결된 컴퓨팅](#attached-compute)

1. __만들기__ 를 선택합니다.

1. 목록에서 컴퓨팅 대상을 선택하여 만들기 작업의 상태를 봅니다.

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="목록에서 컴퓨팅 상태 보기":::

[Azure Machine Learning 컴퓨팅 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md?tabs=azure-studio#create)에 나온 단계를 따릅니다.


## <a name="create-compute-clusters"></a><a name="amlcompute"></a> 컴퓨팅 클러스터 만들기

학습, 일괄 유추 또는 강화 학습 워크로드에 사용되는 단일 또는 다중 노드 컴퓨팅 클러스터를 만듭니다. [위 단계](#portal-create)를 수행하여 컴퓨팅 클러스터를 만듭니다.  그런 다음, 양식을 다음과 같이 작성합니다.

|필드  |설명  |
|---------|---------|
| 위치 | 컴퓨팅 클러스터를 만들 Azure 지역입니다. 기본적으로 이는 작업 영역과 동일한 위치입니다. 작업 영역이 아닌 다른 영역으로 위치를 설정하는 것은 __미리 보기__ 에 제공되며 컴퓨팅 인스턴스가 아닌 __컴퓨팅 클러스터__ 에만 사용할 수 있습니다.</br>작업 영역 또는 데이터 저장소와 다른 지역을 사용하는 경우 네트워크 대기 시간 및 데이터 전송 비용이 증가할 수 있습니다. 클러스터를 만들 때와 클러스터에서 작업을 실행할 때 대기 시간 및 비용이 발생할 수 있습니다. |
|가상 머신 유형 |  CPU 또는 GPU를 선택합니다. 이 영역은 생성되면 변경될 수 없습니다.     |
|가상 머신 우선 순위 | **전용** 또는 **낮은 우선 순위** 를 선택합니다.  우선 순위가 낮은 가상 머신은 더 저렴하지만 컴퓨팅 노드를 보장하지는 않습니다. 작업이 선점될 수도 있습니다.
|가상 머신 크기     |  지원되는 가상 머신 크기는 지역에서 제한될 수 있습니다. [가용성 목록](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 확인합니다.     |

**다음** 을 선택하여 **고급 설정** 으로 진행하고 다음과 같이 양식을 작성합니다.

|필드  |Description  |
|---------|---------|
|컴퓨팅 이름     |  <li>이름은 필수이며 길이는 3~24자여야 합니다.</li><li>유효한 문자는 대소문자, 숫자 및 **-** 문자입니다.</li><li>이름은 문자로 시작해야 합니다.</li><li>이름은 Azure 지역 내 모든 기존 컴퓨팅에서 고유해야 합니다. 선택한 이름이 고유하지 않으면 경고가 표시됩니다.</li><li>이름에 **-** 문자가 사용된 경우 뒤에 문자가 최소 하나 이상 있어야 합니다.</li>     |
|최소 노드 개수 | 프로비전하려는 최소 노드 수입니다. 전용 노드 수를 원하면 여기에 해당 개수를 설정합니다. 최소를 0으로 설정하면 비용이 절약되므로 클러스터가 유휴 상태이면 노드 비용을 지불하지 않습니다. |
|최대 노드 수 | 프로비전하려는 최대 노드 수입니다. 컴퓨팅은 작업이 제출될 때 이 최대 노드 수로 자동 크기 조정됩니다. |
| 규모 축소 전 유휴 시간(초) | 클러스터의 규모를 최소 노드 수로 축소하기 전 유휴 시간입니다. |
| SSH 액세스 사용 | 컴퓨팅 인스턴스(위)에 대해 [SSH 액세스 사용](#enable-ssh)과 동일한 지침을 사용합니다. |
|고급 설정     |  선택 사항입니다. 가상 네트워크를 구성합니다. **리소스 그룹**, **가상 네트워크** 및 **서브넷** 을 지정하여 Azure VNet(Azure Virtual Network) 내에 컴퓨팅 인스턴스를 만듭니다. 자세한 내용은 VNet의 [네트워크 요구 사항](./how-to-secure-training-vnet.md)을 참조하세요.   또한 [관리 ID](#managed-identity)를 연결하여 리소스에 대한 액세스 권한을 부여합니다.     |

### <a name="enable-ssh-access"></a><a name="enable-ssh"></a> SSH 액세스 사용

SSH 액세스는 기본적으로 사용하지 않도록 설정되어 있습니다.  SSH 액세스는 만든 후에는 변경할 수 없습니다. [VS Code 원격](how-to-set-up-vs-code-remote.md)을 사용하여 대화형으로 디버깅하려면 액세스를 사용하도록 설정해야 합니다.  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

컴퓨팅 클러스터가 만들어지고 실행되면 [SSH 액세스로 연결](#ssh-access)을 참조하세요.

### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> 관리 ID 설정

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

클러스터를 만드는 중이나 컴퓨팅 클러스터 세부 정보를 편집할 때 **고급 설정** 에서 **관리 ID 할당** 을 전환하고 시스템 할당 ID나 사용자 할당 ID를 지정합니다.

### <a name="managed-identity-usage"></a>관리 ID 사용량

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="create-inference-clusters"></a><a name="inference-clusters"></a> 유추 클러스터 만들기

> [!IMPORTANT]
> Azure Machine Learning과 함께 Azure Kubernetes Service를 사용하면 다양한 구성 옵션을 사용할 수 있습니다. 네트워킹과 같은 일부 시나리오에는 추가 설정과 구성이 필요합니다. Azure ML과 함께 AKS를 사용하는 방법에 대한 자세한 내용은 [Azure Kubernetes Service 클러스터 만들기 및 연결](how-to-create-attach-kubernetes.md)을 참조하세요.

대규모 유추에 사용할 AKS(Azure Kubernetes Service) 클러스터를 만들거나 연결합니다. [위 단계](#portal-create)를 수행하여 AKS 클러스터를 만듭니다.  그런 다음, 양식을 다음과 같이 작성합니다.


|필드  |Description  |
|---------|---------|
|컴퓨팅 이름     |  <li>이름은 필수입니다. 이름은 2~16자여야 합니다. </li><li>유효한 문자는 대소문자, 숫자 및 **-** 문자입니다.</li><li>이름은 문자로 시작해야 합니다.</li><li>이름은 Azure 지역 내 모든 기존 컴퓨팅에서 고유해야 합니다. 선택한 이름이 고유하지 않으면 경고가 표시됩니다.</li><li>이름에 **-** 문자가 사용된 경우 뒤에 문자가 최소 하나 이상 있어야 합니다.</li>     |
|Kubernetes Service | **새로 만들기** 를 선택하고 나머지 양식을 작성합니다.  또는 **기존 항목 사용** 을 선택한 다음, 구독에서 기존 AKS 클러스터를 선택합니다.
|지역 |  클러스터가 생성될 지역을 선택합니다. |
|가상 머신 크기     |  지원되는 가상 머신 크기는 지역에서 제한될 수 있습니다. [가용성 목록](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)을 확인합니다.     |
|클러스터 용도  | **프로덕션** 또는 **개발-테스트** 를 선택합니다. |
|노드 수 | 노드 수에 가상 머신의 코어 수(vCPU)를 곱한 값이 12보다 크거나 같아야 합니다. |
| 네트워크 구성 | **고급** 을 선택하여 기존 가상 네트워크 내에서 컴퓨팅을 만듭니다. 가상 네트워크의 AKS에 대한 자세한 내용은 [프라이빗 엔드포인트와 가상 네트워크를 사용하여 학습 및 유추 중에 네트워크 격리](./how-to-secure-inferencing-vnet.md)를 참조하세요. |
| SSL 구성 사용 | 컴퓨팅에서 SSL 인증서를 구성할 때 사용 |

## <a name="attach-other-compute"></a><a name="attached-compute"></a> 다른 컴퓨팅 연결

Azure Machine Learning 작업 영역 외부에서 만든 컴퓨팅 대상을 사용하려면 연결해야 합니다. 컴퓨팅 대상을 연결하면 작업 영역에서 사용할 수 있습니다.  **연결된 컴퓨팅** 을 사용하여 **학습** 에 사용되는 컴퓨팅 대상을 연결합니다.  **유추 클러스터** 를 사용하여 **유추** 에 사용되는 AKS 클러스터를 연결합니다.

[위 단계](#portal-create)를 수행하여 컴퓨팅을 연결합니다.  그런 다음, 양식을 다음과 같이 작성합니다.

1. 컴퓨팅 대상의 이름을 입력합니다. 
1. 연결할 컴퓨팅 형식을 선택합니다. Azure Machine Learning 스튜디오에서 모든 컴퓨팅 유형을 연결할 수 있는 것은 아닙니다. 현재 학습용으로 연결할 수 있는 컴퓨팅 유형은 다음과 같습니다.
    * Azure Virtual Machine(Data Science Virtual Machine 연결)
    * Azure Databricks(기계 학습 파이프라인에 사용)
    * Azure Data Lake Analytics(기계 학습 파이프라인에 사용)
    * Azure HDInsight
    * Kubernetes(미리 보기)

1. 양식을 입력하고 필요한 속성에 대한 값을 입력합니다.

    > [!NOTE]
    > Microsoft에서는 암호보다 더 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격에 취약합니다. SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Windows에서 SSH 키를 만들고 사용](../virtual-machines/linux/ssh-from-windows.md)

1. __연결__ 을 선택합니다.

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

> [!IMPORTANT]
> AKS (Azure Kubernetes Services) 또는 Azure Arc 사용 Kubernetes 클러스터를 연결 하려면 구독 소유자 이거나 구독에서 AKS 클러스터 리소스에 액세스할 수 있는 권한이 있어야 합니다. 그렇지 않으면 “새 컴퓨팅 연결” 페이지의 클러스터 목록이 비어 있습니다.

컴퓨팅을 분리하려면 다음 단계를 사용합니다.

1. Azure Machine Learning 스튜디오에서 __컴퓨팅__, __연결된 컴퓨팅__ 및 제거하려는 컴퓨팅을 선택합니다.
1. __분리__ 링크를 사용하여 컴퓨팅을 분리합니다.

## <a name="connect-with-ssh-access"></a><a name="ssh-access"></a> SSH 액세스로 연결

SSH 액세스를 사용하도록 설정된 컴퓨팅 인스턴스 또는 컴퓨팅 클러스터를 만든 경우 액세스에 다음 단계를 사용합니다.

1. 작업 영역 리소스에서 컴퓨팅을 찾습니다.
    1. 왼쪽에서 **Compute** 를 선택합니다.
    1. 위쪽에 있는 탭을 사용하여 **컴퓨팅 인스턴스** 또는 **컴퓨팅 클러스터** 를 선택하여 사용자의 머신을 찾습니다.
1. 리소스 목록에서 컴퓨팅 이름을 선택합니다.
1. 연결 문자열을 찾습니다.

    * **컴퓨팅 인스턴스** 의 경우 **세부 정보** 섹션 맨 위에서 **연결** 을 선택합니다.

        :::image type="content" source="media/how-to-create-attach-studio/details.png" alt-text="스크린샷: 세부 정보 페이지 맨 위에 있는 연결 도구.":::

    * **컴퓨팅 클러스터** 의 경우 위쪽에서 **노드** 를 선택한 다음, 사용자 노드에 해당하는 테이블에서 **연결 문자열** 을 선택합니다.
        :::image type="content" source="media/how-to-create-attach-studio/compute-nodes.png" alt-text="스크린샷: 컴퓨팅 클러스터의 노드에 대한 연결 문자열.":::

1. 연결 문자열을 복사합니다.
1. Windows의 경우 PowerShell 또는 명령 프롬프트 창을 엽니다.
   1. 키가 저장된 디렉터리 또는 폴더로 이동합니다.
   1. 연결 문자열에 -i 플래그를 추가하여 프라이빗 키를 찾고 저장되는 위치를 가리킵니다.
    
      `ssh -i <keyname.pem> azureuser@... (rest of connection string)`

1. Linux 사용자의 경우 [Azure에서 Linux VM에 대한 SSH 키 쌍 만들기 및 사용](../virtual-machines/linux/mac-create-ssh-keys.md)의 단계를 수행합니다.
1. SCP 사용: 

   `scp -i key.pem -P {port} {fileToCopyFromLocal }  azureuser@yourComputeInstancePublicIP:~/{destination}`

## <a name="next-steps"></a>다음 단계

대상을 만들고 작업 영역에 연결한 후 `ComputeTarget` 개체와 함께 [실행 구성](how-to-set-up-training-targets.md)에서 사용합니다.

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* 컴퓨팅 리소스를 사용하여 [학습 실행을 제출](how-to-set-up-training-targets.md)합니다.
* [자습서: 모델 학습](tutorial-train-models-with-aml.md)에서는 관리형 컴퓨팅 대상을 사용하여 모델을 학습시킵니다.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](./how-to-network-security-overview.md)
