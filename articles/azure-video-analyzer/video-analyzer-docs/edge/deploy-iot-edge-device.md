---
title: IoT Edge 디바이스에 Azure Video Analyzer 배포 - Azure
description: 이 문서에서는 IoT Edge 디바이스에 Azure Video Analyzer를 배포하는 데 도움이 되는 단계를 나열합니다. 예를 들어, 로컬 Linux 컴퓨터에 대한 액세스 권한이 있는 경우 이 작업을 수행할 수 있습니다.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6bd045a6cbc1d85790119564a00783b74f759b97
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556600"
---
# <a name="deploy-azure-video-analyzer-to-an-iot-edge-device"></a>IoT Edge 디바이스에 Azure Video Analyzer 배포

[!INCLUDE [header](includes/edge-env.md)]

이 문서에서는 이전에 설치된 다른 모듈이 없는 IoT Edge 디바이스에 Azure Video Analyzer 에지 모듈을 배포하는 방법을 설명합니다. 이 문서의 단계와 RTSP 지원 IP 카메라를 시뮬레이트하는 모듈을 함께 완료하면 Video Analyzer 계정이 생성되고 Video Analyzer 모듈이 IoT Edge 디바이스에 배포됩니다. 이 프로세스는 Video Analyzer에 대한 빠른 시작 및 자습서와 함께 사용하기 위해 고안되었습니다. 프로덕션에서 사용하기 위해 Video Analyzer 모듈을 배포하려는 경우 [프로덕션 준비 및 모범 사례](production-readiness.md) 문서를 검토해야 합니다.

> [!NOTE]
> 이 문서에 설명된 프로세스는 IoT Edge 디바이스에 설치된 에지 모듈(있는 경우)을 제거합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [지원되는 Linux 운영 체제](../../../iot-edge/support.md#operating-systems) 중 하나를 실행하는 x86-64 또는 ARM64 디바이스
* 활성 구독이 있는 Azure 계정
* [IoT Hub 만들기 및 설정](../../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge 디바이스 등록](../../../iot-edge/how-to-register-device.md)
* [Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치](../../../iot-edge/how-to-install-iot-edge.md)


## <a name="create-resources-on-iot-edge-device"></a>IoT Edge 디바이스에서 리소스 만들기

권한 없는 로컬 사용자 계정으로 IoT Edge 디바이스에서 실행되도록 Azure Video Analyzer 모듈을 구성해야 합니다. 이 모듈은 애플리케이션 구성 데이터를 저장하기 위해 특정 로컬 폴더가 필요합니다. 이 방법 가이드에서는 분석을 위해 실시간으로 비디오 피드를 AVA 모듈에 릴레이하는 [RTSP 시뮬레이터](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555)를 활용하고 있습니다. 이 시뮬레이터는 입력 디렉터리에서 입력으로 사용합니다. 다음 스크립트는 빠른 시작 및 자습서에서 사용할 수 있도록 디바이스를 준비합니다.

https://aka.ms/ava/prepare-device

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

위에서 사용한 디바이스 준비 스크립트는 입력 및 구성 폴더를 만들고, 비디오 입력 파일을 다운로드하고, 올바른 권한으로 사용자 계정을 만드는 작업을 자동화합니다. 명령이 성공적으로 완료되면 에지 디바이스에 생성된 다음 폴더가 표시됩니다. 

* `/home/localedgeuser/samples`
* `/home/localedgeuser/samples/input`
* `/var/lib/videoanalyzer`
* `/var/media`

    라이브 비디오를 시뮬레이트하는 데 사용되는 /home/localedgeuser/samples/input 폴더의 비디오 파일("*.mkv")을 확인합니다. 

## <a name="creating-azure-resources-and-deploying-edge-modules"></a>Azure 리소스 만들기 및 에지 모듈 배포
다음 단계는 필요한 Azure 리소스(Video Analyzer 계정, 스토리지 계정, 사용자 할당 관리 ID)를 만들고, Video Analyzer 에지 모듈을 Video Analyzer 계정에 등록하고, Video Analyzer 에지 모듈 및 RTSP 시뮬레이터 모듈을 IoT Edge 디바이스에 배포하는 것입니다.

**Azure에 배포** 단추를 클릭합니다.

> [!WARNING]
> Percept DK와 같이 에지 모듈이 이미 설치된 IoT Edge 디바이스에는 사용하지 마세요. 또한 Azure Stack Edge에서는 지원되지 않습니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava/click-to-deploy/form)

1. **구독** 선택
2. 기본 설정 **지역** 을 선택
3. IoT Hub 및 IoT Edge 디바이스가 속한 **리소스 그룹** 선택
4. **에지 디바이스가 필요한가요?** 에 대한 드롭다운 메뉴에서 **_기존 에지 디바이스 사용_** 옵션 선택
5. **다음**
![초기 배포 양식 스크린샷](./media/deploy-iot-edge-device/project-details.png) 클릭

1. IoT Edge 디바이스가 연결된 **기존 IoT Hub 이름** 선택
1. **다음** 클릭
![두 번째 배포 양식 스크린샷](./media/deploy-iot-edge-device/iot-hub-name.png)

1. 최종 페이지에서 **만들기** 클릭

Azure 리소스를 만들고 에지 모듈을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="verify-your-deployment"></a>배포 확인

배포를 만든 후 Azure Portal에서 IoT Hub의 IoT Edge 디바이스 페이지로 이동합니다.

1. 해당 세부 정보를 열려면 배포할 대상으로 지정한 IoT Edge 디바이스를 선택합니다.
2. 디바이스 세부 정보에서 모듈이 **배포에 지정되고 디바이스에서 보고됨** 으로 표시되는지 확인합니다.

디바이스에서 모듈을 시작한 다음, IoT Hub에 다시 보고하는 데 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.
상태 코드: 200 – OK는 [IoT Edge 런타임](../../../iot-edge/iot-edge-runtime.md)이 정상이며 제대로 작동하고 있음을 의미합니다.

![IoT Edge 런타임의 상태 값을 보여 주는 스크린샷.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>직접 메서드 호출

다음으로, 직접 메서드를 호출하여 샘플을 테스트해 보겠습니다. avaEdge 모듈에서 제공하는 모든 직접 메서드를 이해하려면 [Azure Video Analyzer에 대한 직접 메서드](direct-methods.md)를 읽어보세요.

1. 만든 에지 모듈을 클릭하면 해당 구성 페이지로 이동합니다.  

    ![에지 모듈의 구성 페이지를 보여 주는 스크린샷.](./media/deploy-iot-edge-device/modules.png)
1. **직접 메서드** 메뉴 옵션을 클릭합니다.

    > [!NOTE] 
    > 현재 페이지에서 볼 수 있듯이 연결 문자열 섹션에 값을 추가해야 할 수도 있습니다. **설정 이름** 섹션에서 아무것도 숨기거나 변경할 필요가 없습니다. 공개하는 것도 괜찮습니다.

    ![직접 메서드](./media/deploy-iot-edge-device/module-details.png)
1. 다음으로 상자에 "pipelineTopologyList"를 `Method Name` 입력합니다.
1. 그런 다음, 페이로드 상자에 아래 JSON 페이로드를 복사하여 붙여넣습니다.
    
   ```
   {
       "@apiVersion": "1.1"
   }
   ```
1. 페이지 위쪽에서 **메서드 호출** 옵션을 클릭합니다.
1. 상자에 상태 200 메시지가 표시되어야 합니다. `Result`

    ![상태 200 메시지](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작하기 – Azure Video Analyzer](get-started-detect-motion-emit-events.md) 진행

> [!TIP]
> 위의 빠른 시작을 진행하는 경우, Visual Studio Code를 사용하여 직접 메서드를 호출할 때 `avasample-iot-edge-device` 대신 이 문서를 통해 IoT Hub에 추가된 디바이스를 사용합니다.
