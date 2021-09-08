---
title: IoT Edge 모듈을 통해 Azure Stack Edge Pro GPU에 Kubernetes 상태 비저장 앱 배포 | Microsoft Docs
description: 외부 IP를 통해 액세스되는 IoT Edge 모듈을 사용하여 Azure Stack Edge Pro GPU 디바이스에 Kubernetes 상태 비저장 애플리케이션을 배포하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 233afd9b103a250633c5e49776fc0dfda55a0389
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112251"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>IoT Edge 모듈을 사용하여 Azure Stack Edge Pro GPU 디바이스에서 Kubernetes 상태 비저장 애플리케이션 실행

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 IoT Edge 모듈을 사용하여 Azure Stack Edge Pro 디바이스에 상태 비저장 애플리케이션을 배포하는 방법을 설명합니다.

상태 비저장 애플리케이션을 배포하려면 다음 단계를 수행합니다.

- IoT Edge 모듈을 배포하기 전에 필수 조건이 완료되었는지 확인합니다.
- Azure Stack Edge Pro에서 컴퓨팅 네트워크에 액세스할 IoT Edge 모듈을 추가합니다.
- 모듈이 사용하도록 설정된 네트워크 인터페이스에 액세스할 수 있는지 확인합니다.

이 방법 문서에서는 웹 서버 앱 모듈을 사용하여 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 조건을 충족해야 합니다.

- Azure Stack Edge Pro 디바이스. 확인할 사항은 다음과 같습니다.

    - 컴퓨팅 네트워크 설정은 디바이스에서 구성됩니다.
    - 디바이스는 [자습서: 디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md)의 단계에 따라 활성화됩니다.
- 디바이스에서 [자습서: Azure Stack Edge Pro 디바이스의 컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)에 따라 **컴퓨팅 단계 구성** 단계를 완료했습니다. 디바이스에 연결된 IoT Hub 리소스, IoT 디바이스, IoT Edge 디바이스가 있어야 합니다.


## <a name="add-webserver-app-module"></a>웹 서버 앱 모듈 추가

Azure Stack Edge Pro 디바이스에 웹 서버 앱 모듈을 추가하려면 다음 단계를 수행합니다.

1. 디바이스와 연결된 IoT Hub 리소스에서 **자동 디바이스 관리 > IoT Edge** 로 이동합니다.
1. Azure Stack Edge Pro 디바이스와 연결된 IoT Edge 디바이스를 선택하고 클릭합니다. 

    ![IoT Edge 디바이스 선택](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. **모듈 설정** 을 선택합니다. **디바이스에 모듈 설정** 에서 **+ 추가** 를 선택한 다음 **IoT Edge 모듈** 을 선택합니다.

    ![IoT Edge 모듈 선택](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. **IoT Edge 모듈 추가** 에서:

    1. 배포할 웹 서버 앱 모듈의 **이름** 을 지정합니다.
    2. **모듈 설정** 탭에서 모듈 이미지에 대한 **이미지 URI** 를 제공합니다. 제공한 이름 및 태그와 일치하는 모듈이 검색됩니다. 이 경우 `mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine`은 공용 `mcr.microsoft.com` 레지스트리에서 nginx 이미지(1.15.5-alpine으로 태그 지정)를 가져옵니다.

        ![IoT Edge 모듈 추가](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. **컨테이너 만들기 옵션** 탭에서 다음 샘플 코드를 붙여넣습니다.  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        이 구성을 사용하면 TCP 포트 8080(기본 웹 서버 포트는 80임)에서 *http* 를 통한 컴퓨팅 네트워크 IP로 모듈에 액세스할 수 있습니다. **추가** 를 선택합니다.

        ![IoT Edge 사용자 지정 모듈 블레이드에서 포트 정보 지정](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. **검토 + 만들기** 를 선택합니다. 모듈 세부 정보를 검토하고 **만들기** 를 선택합니다.

## <a name="verify-module-access"></a>모듈 액세스 확인

1. 모듈이 성공적으로 배포되어 실행되는지 확인합니다. **모듈** 탭에서 모듈의 런타임 상태는 **실행 중** 이어야 합니다.  

    ![모듈 상태가 실행 중인지 확인](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. 웹 서버 앱의 외부 엔드포인트를 가져오려면 [Kubernetes 대시보드에 액세스](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)합니다. 
1. 대시보드의 왼쪽 창에서 **iotedge** 네임스페이스로 필터링합니다. **검색 및 부하 분산 > 서비스** 로 이동합니다. 나열된 서비스 목록에서 웹 서버 앱 모듈의 외부 엔드포인트를 찾습니다. 

    ![외부 엔드포인트에서 웹 서버 앱에 연결](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. 외부 엔드포인트를 선택하여 새 브라우저 창을 엽니다.

    웹 서버 앱이 실행되고 있는 것을 확인할 수 있습니다.

    ![지정된 포트를 통한 모듈 연결 확인](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>다음 단계

- IoT Edge 모듈을 통해 상태 저장 애플리케이션을 노출하는 방법 알아보기<!--insert link-->.
