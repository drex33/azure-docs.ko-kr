---
title: FPGA 디바이스에서 IoT Edge 모듈을 수정하여 Azure Stack Edge Pro GPU 디바이스에서 실행
description: Azure Stack Edge Pro GPU 디바이스에서 실행하기 위해 기존 FPGA 디바이스의 기존 IoT Edge 모듈에 필요한 수정 사항을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.custom: ignite-fall-2021
ms.openlocfilehash: ab3cb4360b9bd0c65ac8567109890fc364411367
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073057"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Azure Stack Edge Pro FPGA 디바이스에서 기존 IoT Edge 모듈 실행

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스의 Kubernetes 기반 IoT Edge 플랫폼에서 실행할 수 있도록 Azure Stack Edge Pro FPGA에서 실행되는 Docker 기반 IoT Edge 모듈에 필요한 변경 사항에 대해 자세히 설명합니다. 

## <a name="about-iot-edge-implementation"></a>IoT Edge 구현 정보 

IoT Edge는 Azure Stack Edge Pro FPGA 디바이스와 Azure Stack Edge Pro GPU 디바이스에서 다르게 구현됩니다. GPU 디바이스의 경우 Kubernetes는 IoT Edge의 호스팅 플랫폼으로 사용됩니다. FPGA 디바이스의 IoT Edge는 Docker 기반 플랫폼을 사용합니다. IoT Edge Docker 기반 애플리케이션 모델은 Kubernetes 네이티브 애플리케이션 모델로 자동 변환됩니다. 그러나 Kubernetes 애플리케이션 모델의 작은 하위 집합만 지원되므로 일부 변경이 여전히 필요할 수 있습니다.

FPGA 디바이스에서 GPU 디바이스로 워크로드를 마이그레이션하는 경우 Kubernetes 플랫폼에서 성공적으로 실행되도록 기존 IoT Edge 모듈을 변경해야 합니다. 스토리지, 네트워킹, 리소스 사용량 및 웹 프록시 요구 사항을 다르게 지정해야 할 수 있습니다. 

## <a name="storage"></a>스토리지

IoT Edge 모듈에 대한 스토리지를 지정할 때 다음 정보를 고려합니다.

- Kubernetes의 컨테이너 스토리지는 볼륨 마운트를 사용하여 지정됩니다.
- Kubernetes에서의 배포에는 영구 스토리지 또는 호스트 경로 연결을 위한 바인딩이 있을 수 없습니다.
    - 지속적 스토리지의 경우 `volume` 유형과 함께 `Mounts`를 사용하세요.
    - 호스트 경로의 경우 `bind` 유형과 함께 `Mounts`를 사용하세요.
- Kubernetes의 IoT Edge의 경우 `Mounts`를 통한 바인딩은 파일이 아닌 디렉터리에 대해서만 작동합니다.

#### <a name="example---storage-via-volume-mounts"></a>예 - 볼륨 마운트를 통한 스토리지 

Docker의 IoT Edge의 경우 호스트 경로 바인딩을 사용하여 디바이스의 공유를 컨테이너 내부 경로에 매핑합니다. 다음은 FPGA 디바이스에서 사용되는 컨테이너 만들기 옵션입니다.

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Kubernetes의 IoT Edge에 대한 호스트 경로의 경우 `bind` 유형과 함께 `Mounts`를 사용하는 예가 여기에 표시됩니다.
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Kubernetes에서 IoT Edge를 실행하는 GPU 디바이스의 경우 볼륨 탑재를 사용하여 스토리지를 지정합니다. 공유를 사용하여 스토리지를 프로비저닝하려면 `Mounts.Source` 값은 GPU 디바이스에 프로비저닝된 SMB 또는 NFS 공유의 이름입니다. `/home/input`는 컨테이너 내에서 볼륨이 액세스할 수 있는 경로입니다. 다음은 GPU 디바이스에서 사용되는 컨테이너 만들기 옵션입니다.

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>네트워크

IoT Edge 모듈에 대한 네트워킹을 지정할 때 다음 정보를 고려합니다. 

- `HostPort` 사양은 클러스터 내부와 외부 모두에서 서비스를 노출하는 데 필요합니다.
    - 클러스터에만 서비스 노출을 제한하는 K8sExperimental 옵션이 필요합니다.
- 모듈 간 통신에는 `HostPort` 사양 및 매핑된 포트를 사용하는 연결이 필요합니다(컨테이너 노출 포트를 사용하지 않음).
- 호스트 네트워킹은 `dnsPolicy = ClusterFirstWithHostNet`과 함께 작동하며 모든 컨테이너(특히 `edgeHub`)가 호스트 네트워크에 있을 필요가 없습니다. <!--Need further clarifications on this one-->
- 동일한 요청에서 TCP, UDP에 대한 포트 매핑을 추가할 수 없습니다.

#### <a name="example---external-access-to-modules"></a>예 - 모듈에 대한 외부 액세스 

포트 바인딩을 지정하는 모든 IoT Edge 모듈의 경우 디바이스의 로컬 UI에 지정된 Kubernetes 외부 서비스 IP 범위를 사용하여 IP 주소가 할당됩니다. 다음 예와 같이 Docker의 IoT Edge와 Kubernetes의 IoT Edge 간에 컨테이너 만들기 옵션에 대한 변경 사항은 없습니다.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

그러나 모듈에 할당된 IP 주소를 쿼리하려면 [서비스 또는 모듈의 IP 주소 가져오기](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)에 설명된 대로 Kubernetes 대시보드를 사용할 수 있습니다. 

또는 [디바이스의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)하고 `iotedge` list 명령을 사용하여 디바이스에서 실행 중인 모든 모듈을 나열할 수 있습니다. [명령 출력](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)은 모듈과 관련된 외부 IP도 나타냅니다.


## <a name="resource-usage"></a>리소스 사용량 

GPU 디바이스에서 Kubernetes 기반 IoT Edge 설정을 사용하면 하드웨어 가속, 메모리 및 CPU 요구 사항과 같은 리소스가 FPGA 디바이스와 다르게 지정됩니다. 

#### <a name="compute-acceleration-usage"></a>가속 사용량 컴퓨팅

FPGA에 모듈을 배포하려면 컨테이너 만들기 옵션을 사용합니다. <!--with Device Bindings--> 다음 구성에 표시된 대로: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
GPU의 경우 다음 최소 구성에 표시된 대로 디바이스 바인딩 대신 리소스 요청 사양을 사용합니다. catapult 대신 nvidia 리소스를 요청하고 `wireserver`를 지정할 필요가 없습니다. 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>메모리 및 CPU 사용량
 
메모리 및 CPU 사용량을 설정하려면 `k8s-experimental` 섹션의 모듈에 대한 프로세서 제한을 사용합니다. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
메모리 및 CPU 사양은 필요하지 않지만 일반적으로 좋은 사례입니다. `requests`를 지정하지 않으면 제한에 설정된 값이 최소 요구 사항으로 사용됩니다. 

모듈에 공유 메모리를 사용하려면 다른 방법이 필요합니다. 예를 들어 [Azure Stack Edge에 Live Video Analytics 배포](../azure-video-analyzer/video-analyzer-docs/overview.md)에 설명된 대로 라이브 비디오 분석과 추론 솔루션 간의 공유 메모리 액세스에 호스트 IPC 모드를 사용할 수 있습니다.


## <a name="web-proxy"></a>웹 프록시 

웹 프록시를 구성할 때 다음 정보를 고려하세요.

네트워크에 웹 프록시를 구성한 경우 FPGA 디바이스의 Docker 기반 IoT Edge 설정에서 `edgeHub` 배포를 위해 다음 환경 변수를 구성합니다.

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs`(웹 프록시가 `Amqp` 트래픽을 허용하지 않는 경우)

GPU 디바이스의 Kubernetes 기반 IoT Edge 설정의 경우 배포 중에 이 추가 변수를 구성해야 합니다.

- `no_proxy`: localhost

- Kubernetes 플랫폼의 IoT Edge 프록시는 포트 35000 및 35001을 사용합니다. 모듈이 이러한 포트에서 실행되지 않아야 합니다. 그렇지 않으면 포트 충돌이 발생할 수 있습니다. 

## <a name="other-differences"></a>기타 차이점

- **배포 전략**: 모듈 업데이트에 대한 배포 동작을 변경해야 할 수 있습니다. IoT Edge 모듈의 기본 동작은 롤링 업데이트입니다. 이 동작은 모듈이 하드웨어 가속 또는 네트워크 포트와 같은 리소스를 사용하는 경우 업데이트된 모듈이 다시 시작되지 않도록 합니다. 이 동작은 특히 GPU 디바이스용 Kubernetes 플랫폼에서 영구 볼륨을 처리할 때 예기치 않은 영향을 미칠 수 있습니다. 이 기본 동작을 다시 정의하려면 모듈의 `k8s-experimental` 섹션에 `Recreate`를 지정할 수 있습니다.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **모듈 이름**: 모듈 이름은 Kubernetes 이름 지정 규칙을 따라야 합니다. Kubernetes를 사용하여 해당 모듈을 IoT Edge로 이동할 때 Docker를 사용하여 IoT Edge에서 실행 중인 모듈의 이름을 변경해야 할 수 있습니다. 명명에 대한 자세한 정보는 [Kubernetes 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)을 참조하세요.
- **기타 옵션**: 
    - FPGA 디바이스에서 작동하는 특정 Docker 만들기 옵션은 GPU 디바이스의 Kubernetes 환경에서 작동하지 않습니다. 예: , like – EntryPoint.<!--can we confirm what exactly is required here-->
    - `:`과 같은 환경 변수는 `__`로 대체해야 합니다.
    - Kubernetes Pod에 대한 **컨테이너 만들기** 상태는 IoT Hub 리소스의 모듈에 대한 **백오프** 상태로 이어집니다. Pod가 이 상태에 있는 데는 여러 가지 이유가 있지만 일반적인 이유는 낮은 네트워크 대역폭 연결을 통해 큰 컨테이너 이미지를 가져오기 때문입니다. Pod가 이 상태에 있으면 모듈이 방금 시작되었지만 IOT Hub에서 모듈의 상태가 **백오프** 로 나타납니다.


## <a name="next-steps"></a>다음 단계

- [모듈을 사용하도록 GPU를 구성](./azure-stack-edge-gpu-configure-gpu-modules.md)하는 방법에 대해 자세히 알아봅니다.
