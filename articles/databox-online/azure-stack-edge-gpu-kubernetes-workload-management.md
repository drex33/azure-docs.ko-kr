---
title: Azure Stack Edge Pro 디바이스에서의 Kubernetes 워크로드 관리 이해| Microsoft Docs
description: Azure Stack Edge Pro 디바이스에서 Kubernetes 워크로드를 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: a0b3a5d0cbbae9f25a845f79db35d1049f93f776
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710177"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 디바이스에서의 Kubernetes 워크로드 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro 디바이스에서 컴퓨팅 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터를 만든 후 Pod의 Kubernetes 클러스터에 컨테이너화된 애플리케이션을 배포할 수 있습니다. Kubernetes 클러스터에서 작업을 배포하는 방법에는 여러 가지가 있습니다. 

이 문서에서는 Azure Stack Edge Pro 디바이스에 워크로드를 배포하는 데 사용할 수 있는 다양한 방법을 설명합니다.

## <a name="workload-types"></a>워크로드 유형

Azure Stack Edge Pro 디바이스에 배포할 수 있는 두 가지 일반적인 워크로드 유형은 상태 비저장 애플리케이션과 상태 저장 애플리케이션입니다.

- **상태 비저장 애플리케이션** 은 상태를 보존하지 않고 영구 스토리지에 데이터를 저장하지 않습니다. 모든 사용자 및 세션 데이터는 클라이언트에 유지됩니다. 상태 비저장 애플리케이션의 몇 가지 예로는 Nginx 같은 웹 프런트 엔드와 기타 웹 애플리케이션이 있습니다.

    Kubernetes 배포를 만들어 클러스터에 상태 비저장 애플리케이션을 배포할 수 있습니다. 

- **상태 저장 애플리케이션** 은 해당 상태를 저장해야 합니다. 상태 저장 애플리케이션은 영구 볼륨과 같은 영구 스토리지를 사용하여 서버 또는 다른 사용자가 사용하도록 데이터를 저장합니다. 상태 저장 애플리케이션의 예로는 [Azure SQL Edge](../azure-sql-edge/overview.md) 및 MongoDB와 같은 데이터베이스가 있습니다.

    Kubernetes 배포를 만들어 상태 저장 애플리케이션을 배포할 수 있습니다. 

## <a name="deployment-flow"></a>배포 흐름

Azure Stack Edge Pro 디바이스에 애플리케이션을 배포하려면: 
 
1. **액세스 구성**: 먼저 PowerShell Runspace를 사용하여 사용자를 만들고 네임스페이스를 만든 다음 해당 네임스페이스에 대한 사용자 액세스 권한을 부여합니다.
2. **스토리지 구성**: 다음으로 Azure Portal에서 Azure Stack Edge 리소스를 사용하여 배포할 상태 저장 애플리케이션의 정적 또는 동적 프로비전을 통해 영구 볼륨을 만듭니다.
3. **네트워킹 구성**: 마지막으로 서비스를 사용하여 Kubernetes 클러스터 외부와 내부에서 애플리케이션을 공개합니다.
 
## <a name="deployment-types"></a>배포 형식

워크로드를 배포하는 세 가지 기본적 방법이 있습니다. 이러한 각 배포 방법론을 사용하면 디바이스의 고유한 네임스페이스에 연결한 다음 상태 비저장 또는 상태 저장 애플리케이션을 배포할 수 있습니다.

![Kubernetes 워크로드 배포](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **로컬 배포**: 이 배포는 Kubernetes `yamls`를 배포할 수 있는 `kubectl` 등의 명령줄 액세스 도구를 통해 이루어집니다. `kubeconfig` 파일을 통해 Azure Stack Edge Pro에서 Kubernetes 클러스터에 액세스합니다. 자세한 내용은 [kubectl을 통해 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조하세요.

- **IoT Edge 배포**: Azure IoT Hub에 연결되는 IoT Edge를 통해 배포됩니다. `iotedge` 네임스페이스를 통해 Azure Stack Edge Pro 디바이스의 Kubernetes 클러스터에 연결합니다. 이 네임스페이스에 배포된 IoT Edge 에이전트는 Azure에 대한 연결을 담당합니다. Azure DevOps CI/CD를 사용하여 `IoT Edge deployment.json` 구성을 적용합니다. 네임스페이스 및 IoT Edge 관리는 클라우드 운영자에 의해 수행됩니다.

- **Azure Arc 지원 Kubernetes 배포:** Azure Arc 지원 Kubernetes는 Kubernetes 클러스터에 애플리케이션을 배포할 수 있는 하이브리드 관리 도구입니다. `azure-arc namespace`를 통해 Azure Stack Edge Pro 디바이스의 Kubernetes 클러스터에 연결합니다. 이 네임스페이스에 배포된 에이전트는 Azure에 대한 연결을 담당합니다. GitOps 기반 구성 관리를 사용하여 배포 구성을 적용합니다. 
    
    Azure Arc 사용하도록 설정된 Kubernetes를 사용하면 컨테이너에 대한 Azure Monitor 사용하여 클러스터를 보고 모니터링할 수도 있습니다. 자세한 내용은 Azure Arc [지원 Kubernetes란?을(를) 참조하십시오.](../azure-arc/kubernetes/overview.md)
    
    2021년 3월부터 Azure Arc 사용 가능한 Kubernetes는 일반적으로 사용자에게 제공되며 표준 사용 요금이 적용됩니다. 가치 있는 미리 보기 고객은 Azure Arc 지원 Kubernetes를 Azure Stack Edge 디바이스에 대해 무료로 사용할 수 있습니다. 미리 보기 제안을 이용하려면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 만드세요.

    1. **문제점 유형** 에서 **청구** 를 선택합니다.
    2. **구독** 아래에서 구독을 선택합니다.
    3. **서비스** 에서 **내 서비스** 를 선택한 다음 **Azure Stack Edge** 를 선택합니다.
    4. **리소스** 에서 리소스를 선택합니다.
    5. **요약** 에서 문제에 대한 설명을 입력합니다.
    6. **문제 유형** 에서 **예기치 않은 요금** 을 선택합니다.
    7. **문제 하위 유형** 에서 **무료 평가판 요금 이해 도움말** 을 선택합니다.


## <a name="choose-the-deployment-type"></a>배포 유형 선택

애플리케이션을 배포할 때 다음 정보를 고려하세요.

- **단일 또는 여러 형식**: 단일 배포 옵션 또는 다양한 배포 옵션의 조합을 선택할 수 있습니다.
- **클라우드 또는 로컬**: 애플리케이션에 따라 kubectl을 통한 로컬 배포나 IoT Edge 및 Azure Arc를 통한 클라우드 배포를 선택할 수 있습니다. 
    - 로컬 배포를 선택하면 Azure Stack Edge Pro 디바이스가 배포된 네트워크로 제한됩니다.
    - 배포할 수 있는 클라우드 에이전트가 있는 경우 클라우드 운영자를 배포하고 클라우드 관리를 사용해야 합니다.
- **IoT 또는 Azure Arc**: 배포 선택은 제품 시나리오의 의도에 따라서도 달라집니다. IoT 또는 IoT 에코시스템과 보다 긴밀하게 통합된 애플리케이션이나 컨테이너를 배포하는 경우 IoT Edge를 선택하여 애플리케이션을 배포합니다. 기존 Kubernetes 배포가 있는 경우 Azure Arc를 선택하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

kubectl을 통해 애플리케이션을 로컬로 배포하려면 다음을 참조하세요.

- [kubectl을 통해 Azure Stack Edge Pro에 상태 비저장 애플리케이션 배포](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)

IoT Edge를 통해 앱을 배포하려면 다음을 참조하세요.

- [IoT Edge를 통해 Azure Stack Edge Pro에 샘플 모듈 배포](azure-stack-edge-gpu-deploy-sample-module.md)

Azure Arc를 통해 앱을 배포하려면 다음을 참조하세요.

- [Azure Arc를 사용하여 애플리케이션 배포](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)
