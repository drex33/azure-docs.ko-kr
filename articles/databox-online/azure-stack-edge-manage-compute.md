---
title: Azure Stack Edge Pro FPGA 컴퓨팅 관리
description: 트리거, 모듈, 컴퓨팅 구성 보기와 같은 Edge 컴퓨팅 설정을 관리하는 방법과 Azure Stack Edge Pro FPGA에서 Azure Portal을 통해 구성을 제거하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: 80d19909cc4a2fe843adbf19eed0f8316d64f6db
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460704"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-fpga"></a>Azure Stack Edge Pro FPGA에서 컴퓨팅 관리

이 문서에서는 Azure Stack Edge Pro FPGA에서 컴퓨팅을 관리하는 방법을 설명합니다. 로컬 웹 UI 또는 Azure Portal을 통해 컴퓨팅을 관리할 수 있습니다. Azure Portal에서 모듈, 트리거 및 컴퓨팅 구성을 관리하고 로컬 웹 UI를 사용하여 컴퓨팅 설정을 관리합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 트리거 관리
> * 컴퓨팅 구성 관리


## <a name="manage-triggers"></a>트리거 관리

이벤트는 작업을 수행하려는 클라우드 환경 또는 디바이스에서 발생하는 상황입니다. 예를 들어, 공유에서 파일이 만들어지는 것도 이벤트에 해당합니다. 트리거는 이벤트를 발생시킵니다. Azure Stack Edge Pro FPGA의 경우, 트리거는 파일 이벤트 또는 일정에 대한 응답일 수 있습니다.

- **파일**: 해당 트리거는 파일 생성, 파일 수정과 같은 파일 이벤트에 대한 응답입니다.
- **예약됨**: 해당 트리거는 시작 날짜, 시작 시간 및 반복 간격으로 정의할 수 있는 일정에 대한 응답으로 진행됩니다.


### <a name="add-a-trigger"></a>트리거 추가

트리거를 만들려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **IoT Edge > 트리거** 로 이동합니다. 명령 모음에서 **+ 트리거 추가** 를 선택합니다.

    ![트리거 추가 선택](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. **트리거 추가** 블레이드에서 트리거의 고유한 이름을 지정합니다.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. 트리거의 **유형** 을 선택합니다. 트리거가 파일 이벤트에 대한 응답일 경우 **파일** 을 선택합니다. 트리거를 정의된 시간에 시작하고 지정된 반복 간격으로 실행하려는 경우 **예약됨** 을 선택합니다. 선택한 항목에 따라 다른 옵션 세트가 표시됩니다.

    - **파일 트리거** - 드롭다운 목록에서 탑재된 공유를 선택합니다. 이 공유에서 파일 이벤트가 발생하면 트리거는 Azure 함수를 호출합니다.

        ![SMB 공유 추가](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **예약된 트리거** - 시작 날짜/시간, 반복 간격(시, 분 또는 초)을 지정합니다. 또한 토픽의 이름을 입력합니다. 토픽을 사용하면 트리거를 디바이스에 배포된 모듈로 유연하게 라우팅할 수 있습니다.

        경로 문자열의 예시는 `"route3&quot;: &quot;FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint(&quot;modules/modulename/inputs/input1")"`입니다.

        ![NFS 공유 추가](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. **추가** 를 선택하여 트리거를 만듭니다. 알림은 트리거 생성이 진행 중임을 나타냅니다. 트리거를 만든 후 새 트리거를 반영하도록 블레이드가 업데이트됩니다.
 
    ![업데이트된 트리거 목록](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>트리거 삭제

트리거를 삭제하려면 Azure Portal에서 다음 단계를 수행합니다.

1. 트리거 목록에서 삭제하려는 트리거를 선택합니다.

    ![트리거 선택](media/azure-stack-edge-manage-compute/delete-trigger-1.png)

2. 마우스 오른쪽 단추를 클릭한 다음, **삭제** 를 선택합니다.

    ![삭제 선택](media/azure-stack-edge-manage-compute/delete-trigger-2.png)

3. 확인 메시지가 표시되면 **예** 를 클릭합니다.

    ![삭제 확인](media/azure-stack-edge-manage-compute/delete-trigger-3.png)

트리거 목록이 삭제를 반영하도록 업데이트됩니다.

## <a name="manage-compute-configuration"></a>컴퓨팅 구성 관리

Azure Portal에서 컴퓨팅 구성을 보거나, 기존 컴퓨팅 구성을 제거하거나, 컴퓨팅 구성을 새로 고침하여 Azure Stack Edge Pro FPGA의 IoT 디바이스 및 IoT Edge 디바이스에 대한 액세스 키를 동기화합니다.

### <a name="view-compute-configuration"></a>컴퓨팅 구성 보기

디바이스의 컴퓨팅 구성을 보려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **IoT Edge > 개요** 로 이동합니다. 

    ![컴퓨팅 보기 선택](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. **속성** 페이지로 이동합니다. 디바이스의 컴퓨팅 구성을 기록해 둡니다. 컴퓨팅 구성할 때 IoT Hub 리소스를 만들었을 것입니다. 해당 IoT Hub 리소스 아래에 IoT 디바이스 및 IoT Edge 디바이스가 구성되어 있습니다. IoT Edge 디바이스에서는 Linux 모듈만 실행이 지원됩니다.

    ![구성 보기](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>컴퓨팅 구성 제거

디바이스의 기존 Edge 컴퓨팅 구성을 제거하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **IoT Edge > 개요** 로 이동합니다. 명령 모음에서 **제거** 를 선택합니다.

    ![컴퓨팅 제거 선택](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. 컴퓨팅 구성을 제거한 경우 컴퓨팅을 다시 사용해야 할 때 디바이스를 다시 구성해야 합니다. 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

    ![컴퓨팅 제거 선택 2](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT 디바이스 및 IoT Edge 디바이스 액세스 키 동기화

Azure Stack Edge Pro FPGA에서 컴퓨팅을 구성하면, IoT 디바이스와 IoT Edge 디바이스가 생성됩니다. 이러한 디바이스에는 대칭 액세스 키가 자동으로 할당됩니다. 보안 모범 사례에 따라 이러한 키는 IoT Hub 서비스를 통해 정기적으로 순환됩니다.

이러한 키를 순환하려면 만든 IoT Hub 서비스로 이동한 후 IoT 디바이스 또는 IoT Edge 디바이스를 선택합니다. 각 디바이스에는 기본 액세스 키 및 보조 액세스 키가 있습니다. 기본 액세스 키를 보조 액세스 키에 지정하고 기본 액세스 키를 다시 생성합니다.

IoT 디바이스 및 IoT Edge 디바이스 키가 순환된 경우, 최신 액세스 키를 가져오려면 Azure Stack Edge Pro FPGA에서 구성을 새로 고침해야 합니다. 동기화는 IoT 디바이스 및 IoT Edge 디바이스의 최신 키를 가져오는 데 도움이 됩니다. Azure Stack Edge Pro FPGA는 주 액세스 키만 사용합니다.

디바이스의 액세스 키를 동기화하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **IoT Edge > 개요** 로 이동합니다. 명령 모음에서 **구성 새로 고침** 을 선택합니다.

    ![구성 새로 고침 선택](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

     ![확인 메시지에서 예 선택](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. 동기화가 완료되면 대화 상자를 종료합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 Edge 컴퓨팅 네트워크를 관리](azure-stack-edge-extend-compute-access-modules.md)하는 방법을 알아봅니다.
