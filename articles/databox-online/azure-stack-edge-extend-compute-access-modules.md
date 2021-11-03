---
title: Azure Stack Edge Pro FPGA 컴퓨팅 네트워크 관리를 사용하여 모듈에 액세스
description: 외부 IP를 통해 모듈에 액세스하도록 Azure Stack Edge Pro FPGA에서 컴퓨팅 네트워크를 확장하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 335b1429cceb60f99fada1b7eaf207fb4a64ce2d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131088220"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro에서 컴퓨팅 네트워크 사용

이 문서에서는 Azure Stack Edge Pro에서 실행되는 모듈이 디바이스에서 사용하도록 설정된 컴퓨팅 네트워크에 액세스할 수 있는 방법을 설명합니다.

네트워크를 구성하려면 다음 단계를 수행합니다.

- Azure Stack Edge Pro 디바이스에서 컴퓨팅용 네트워크 인터페이스 사용
- Azure Stack Edge Pro에서 컴퓨팅 네트워크에 액세스할 모듈 추가
- 모듈이 사용하도록 설정된 네트워크 인터페이스에 액세스할 수 있는지 확인

이 자습서에서는 웹 서버 앱 모듈을 사용하여 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 조건을 충족해야 합니다.

- 디바이스 설정이 완료된 Azure Stack Edge Pro 디바이스
- 디바이스에서 [자습서: Azure Stack Edge Pro를 사용하여 데이터 변환](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute)에 따라 **컴퓨팅 구성** 단계를 완료했습니다. 디바이스에 연결된 IoT Hub 리소스, IoT 디바이스, IoT Edge 디바이스가 있어야 합니다.

## <a name="enable-network-interface-for-compute"></a>컴퓨팅용 네트워크 인터페이스 사용

외부 네트워크를 통해 디바이스에서 실행되는 모듈에 액세스하려면 디바이스의 네트워크 인터페이스에 IP 주소를 할당해야 합니다. 로컬 웹 UI에서 이러한 컴퓨팅 설정을 관리 할 수 있습니다.

로컬 웹 UI에서 다음 단계를 수행하여 컴퓨팅 설정을 구성합니다.

1. 로컬 웹 UI에서 **구성 > 컴퓨팅 설정** 으로 이동합니다.  

2. 디바이스에서 실행할 컴퓨팅 모듈에 연결하는 데 사용할 네트워크 인터페이스를 **사용** 하도록 설정합니다.

    - 고정 IP 주소를 사용하는 경우 네트워크 인터페이스의 IP 주소를 입력합니다.
    - DHCP를 사용하는 경우에는 IP 주소가 자동으로 할당됩니다. 이 예제에서는 DHCP를 사용합니다.

    ![컴퓨팅 설정 사용 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. **적용** 을 선택하여 설정을 적용합니다. DHCP를 사용하는 경우 네트워크 인터페이스에 할당된 IP 주소를 적어 둡니다.

    ![컴퓨팅 설정 사용](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>웹 서버 앱 모듈 추가

Azure Stack Edge Pro 디바이스에 웹 서버 앱 모듈을 추가하려면 다음 단계를 수행합니다.

1. Azure Stack Edge Pro 디바이스와 연결된 IoT Hub 리소스로 이동한 다음 **IoT Edge 디바이스** 를 선택합니다.
2. Azure Stack Edge Pro 디바이스와 연결된 IoT Edge 디바이스를 선택합니다. **디바이스 세부 정보** 에서 **모듈 설정** 을 선택합니다. **모듈 추가** 에서 **+ 추가**, **IoT Edge 모듈** 을 차례로 선택합니다.
3. **IoT Edge 사용자 지정 모듈** 블레이드에서 다음을 수행합니다.

    1. 배포할 웹 서버 앱 모듈의 **이름** 을 지정합니다.
    2. 모듈 이미지의 **이미지 URI** 를 제공합니다. 제공한 이름 및 태그와 일치하는 모듈이 검색됩니다. 이 경우 는 `mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine` 공용 레지스트리에서 nginx 이미지(태그가 로 지정)를 `1.15.5-alpine` `mcr.microsoft.com` 가져옵니다.
    3. **컨테이너 만들기 옵션** 에서 다음 샘플 코드를 붙여넣습니다.  

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

        이 구성을 사용하면 TCP 포트 8080(기본 웹 서버 포트는 80임)에서 *http* 를 통한 컴퓨팅 네트워크 IP로 모듈에 액세스할 수 있습니다.

        ![IoT Edge 사용자 지정 모듈 블레이드에서 포트 정보 지정](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. **저장** 을 선택합니다.

## <a name="verify-module-access"></a>모듈 액세스 확인

1. 모듈이 성공적으로 배포되어 실행되는지 확인합니다. **디바이스 세부 정보** 페이지의 **모듈** 탭에서 모듈의 런타임 상태가 **실행 중** 이어야 합니다.  
2. 웹 서버 앱 모듈에 연결합니다. 브라우저 창을 열고 다음을 입력합니다.

    `http://<compute-network-IP-address>:8080`

    웹 서버 앱이 실행되고 있는 것을 확인할 수 있습니다.

    ![지정된 포트를 통한 모듈 연결 확인](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](azure-stack-edge-manage-users.md)하는 방법을 알아봅니다.
