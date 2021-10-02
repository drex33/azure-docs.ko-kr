---
title: Kubernetes 대시보드를 통해 Azure Stack Edge Pro 디바이스 모니터링 | Microsoft Docs
description: Kubernetes 대시보드에 액세스하고 사용하여 Azure Stack Edge Pro 디바이스를 모니터링하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2021
ms.author: alkohli
ms.openlocfilehash: 30e46f9425f4015893c08b94382e87cfa93c8be8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363129"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes 대시보드를 사용하여 Azure Stack Edge Pro GPU 디바이스를 모니터링합니다.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Kubernetes 대시보드에 액세스하고 사용하여 Azure Stack Edge Pro GPU 디바이스를 모니터링하는 방법을 설명합니다. 디바이스를 모니터링하려면 Azure Portal의 차트를 사용하거나, Kubernetes 대시보드를 보거나, 디바이스의 PowerShell 인터페이스를 통해 `kubectl` 명령을 실행하면 됩니다. 

이 문서에서는 Kubernetes 대시보드에서 수행할 수 있는 모니터링 작업만 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 디바이스에서 Kubernetes 대시보드에 액세스
> * 디바이스에 배포된 모듈 보기
> * 디바이스에 배포된 애플리케이션의 IP 주소 가져오기
> * 디바이스에 배포된 모듈의 컨테이너 로그 보기


## <a name="about-kubernetes-dashboard"></a>Kubernetes 대시보드 정보

Kubernetes 대시보드는 컨테이너화된 애플리케이션 문제를 해결하는 데 사용할 수 있는 웹 기반 사용자 인터페이스입니다. Kubernetes 대시보드는 Kubernetes `kubectl` 명령줄에 대한 UI 기반 대안입니다. 자세한 내용은 [Kubernetes 대시보드](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)를 참조하세요. 

Azure Stack Edge Pro에서 Kubernetes 대시보드를 *읽기 전용* 모드로 사용하여 Azure Stack Edge Pro 디바이스에서 실행되는 애플리케이션을 간략하게 살펴보고, Kubernetes 클러스터 리소스 상태를 보고, 디바이스에서 발생한 오류를 확인할 수 있습니다.

## <a name="access-dashboard"></a>대시보드에 액세스

Kubernetes 대시보드는 *읽기 전용* 이며 포트 31000의 Kubernetes 마스터 노드에서 실행됩니다. 대시보드에 액세스하려면 다음 단계를 수행합니다. 

1. 디바이스의 로컬 UI에서 **디바이스** 로 이동한 다음, **디바이스 엔드포인트** 로 이동합니다. 
1. **Kubernetes 대시보드 엔드포인트를** 복사합니다. `C:\Windows\System32\Drivers\etc\hosts`Kubernetes 대시보드에 연결할 클라이언트의 파일에 DNS 항목을 만듭니다. 

    `<IP address of the Kubernetes dashboard>    <Kubernetes dashboard endpoint suffix>` 
        
    ![Kubernetes 대시보드 엔드포인트에 대한 DNS 항목 추가](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/add-domain-name-service-entry-hosts-1.png) 

1. **Kubernetes 대시보드** 엔드포인트에 대한 행에서 **구성 다운로드를** 선택합니다. 이 작업은 `kubeconfig` 대시보드에 액세스할 수 있는 를 다운로드합니다. `config.json` 파일을 로컬 시스템에 저장합니다.   

1. 로컬 UI에서 Kubernetes 대시보드 인증서를 다운로드합니다. 
    1. 디바이스의 로컬 UI에서 **인증서** 로 이동합니다.
    1. **Kubernetes 대시보드 엔드포인트 인증서** 에 대한 항목을 찾습니다. 이 항목의 오른쪽에서 **다운로드를** 선택하여 대시보드에 액세스하는 데 사용할 클라이언트 시스템에서 인증서를 다운로드합니다. 

    ![Kubernetes 대시보드 엔드포인트 인증서 다운로드](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/download-kubernetes-dashboard-endpoint-certificate-1.png)  

1. 다운로드한 인증서를 클라이언트에 설치합니다. Windows 클라이언트를 사용하는 경우 다음 단계를 따르세요. 
    1. 인증서를 선택하고 **인증서 가져오기 마법사** 에서 저장소 위치를 **로컬 컴퓨터** 로 선택합니다. 

        ![클라이언트 1에 인증서 설치](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 신뢰할 수 있는 루트 저장소의 로컬 컴퓨터에 인증서를 설치합니다. 

        ![클라이언트 2에 인증서 설치](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 
1. Kubernetes 대시보드 URL을 복사하여 사용하여 브라우저에서 대시보드를 엽니다. **Kubernetes 대시보드 로그인** 페이지에서 다음을 수행합니다.
    
    1. **kubeconfig** 를 선택합니다. 
    1. 줄임표 **...** 를 선택합니다. 로컬 시스템에서 이전에 다운로드한 `kubeconfig`를 찾아 가리킵니다. **로그인** 을 선택합니다.
        ![kubeconfig 파일로 이동](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. 이제 읽기 전용 모드의 Azure Stack Edge Pro 디바이스에 대한 Kubernetes 대시보드가 표시됩니다.

    ![Kubernetes 대시보드 기본 페이지](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>모듈 상태 보기

컴퓨팅 모듈은 비즈니스 논리가 구현된 컨테이너입니다. 대시보드를 사용하여 컴퓨팅 모듈이 Azure Stack Edge Pro 디바이스에 성공적으로 배포됐는지 확인할 수 있습니다.

모듈 상태를 보려면 대시보드에서 다음 단계를 수행합니다.

1. 대시보드 왼쪽 창에서 **네임스페이스** 로 이동합니다. IoT Edge 모듈이 표시되는 네임스페이스를 기준으로 필터링합니다(이 경우 **iotedge**).
1. 왼쪽 창에서 **워크로드 > 배포** 로 이동합니다.
1. 오른쪽 창에 디바이스에 배포된 모든 모듈이 표시됩니다. 이 경우 GettingStartedWithGPU 모듈이 Azure Stack Edge Pro에 배포되었습니다. 모듈이 배포된 것을 확인할 수 있습니다.

    ![모듈 배포 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>서비스 또는 모듈의 IP 주소 가져오기

대시보드를 사용하여 Kubernetes 클러스터 외부에 노출하려는 서비스나 모듈의 IP 주소를 가져올 수 있습니다. 

**컴퓨팅 네트워크 설정** 페이지에서 디바이스의 로컬 웹 UI를 통해 이러한 외부 서비스의 IP 범위를 할당합니다. IoT Edge 모듈을 배포한 후에 특정 모듈이나 서비스에 할당된 IP 주소를 가져올 수 있습니다. 

IP 주소를 가져오려면 대시보드에서 다음 단계를 수행합니다.

1. 대시보드 왼쪽 창에서 **네임스페이스** 로 이동합니다. 외부 서비스가 배포되는 네임스페이스를 기준으로 필터링합니다(이 경우 **iotedge**).
1. 왼쪽 창에서 **검색 및 부하 분산 > 서비스** 로 이동합니다.
1. 오른쪽 창에 Azure Stack Edge Pro 디바이스의 `iotedge` 네임스페이스에서 실행 중인 모든 서비스가 표시됩니다.

    ![외부 서비스에 대한 IP 가져오기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>컨테이너 로그 보기

컨테이너 로그를 확인해야 하는 인스턴스가 있습니다. 대시보드를 사용하여 Kubernetes 클러스터에 배포한 특정 컨테이너의 로그를 가져올 수 있습니다.

컨테이너 로그를 보려면 대시보드에서 다음 단계를 수행합니다.

1. 대시보드 왼쪽 창에서 **네임스페이스** 로 이동합니다. IoT Edge 모듈이 배포된 네임스페이스를 기준으로 필터링합니다(이 경우 **iotedge**).
1. 왼쪽 창에서 **워크로드 > Pod** 로 이동합니다.
1. 오른쪽 창에 디바이스에서 실행 중인 모든 Pod가 표시됩니다. 로그를 보려는 모듈이 실행 중인 Pod를 식별합니다. 식별한 Pod의 세로 줄임표를 선택하고 바로 가기 메뉴에서 **로그** 를 선택합니다.

    ![컨테이너 로그 보기 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. 로그는 대시보드에 기본 제공되는 로그 뷰어에 표시됩니다. 로그를 다운로드할 수도 있습니다.

    ![컨테이너 로그 보기 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>CPU, 메모리 사용량 보기

Azure Stack Edge Pro 디바이스에 대한 Kubernetes 대시보드에는 Kubernetes 리소스에서 CPU 및 메모리 사용량을 집계하는 [메트릭 서버 추가 항목](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/)도 있습니다.
 
예를 들어 모든 네임스페이스의 배포에서 사용되는 CPU 및 메모리를 볼 수 있습니다. 

![모든 배포에서 CPU 및 메모리 사용량 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

특정 네임스페이스를 기준으로 필터링할 수도 있습니다. 다음 예에서는 Azure Arc 배포에 대한 CPU 및 메모리 사용량만 볼 수 있습니다.  

![Azure Arc 배포에 대한 CPU 및 메모리 사용량 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes 메트릭 서버는 [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)와 같은 자동 크기 조정 파이프라인을 제공합니다.


## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용하여 모니터링](azure-stack-edge-gpu-enable-azure-monitor.md)하는 방법을 알아봅니다.
[진단을 실행하고 로그를 수집](azure-stack-edge-gpu-troubleshoot.md)하는 방법을 알아봅니다.
