---
author: alkohli
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 24cc360da36e49726cc23b407ada5a3d88c3a2a1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082961"
---
1. 디바이스에 업데이트를 사용할 수 있는 경우 Azure Stack Edge 리소스의 **개요** 페이지에 알림이 표시됩니다. 알림을 선택하거나 상단의 명령 모음에서 **디바이스 업데이트** 를 선택합니다. 이렇게 하면 디바이스 소프트웨어 업데이트를 적용할 수 있습니다.

    ![디바이스 업데이트 선택](media/azure-stack-edge-install-2110-updates/install-updates-portal-1.png)

2. **디바이스 업데이트** 블레이드에서 릴리스 정보의 새 기능과 관련된 사용 조건을 검토했는지 확인합니다.

    업데이트가 디바이스에 다운로드되면 업데이트를 **자동으로 설치하도록** 선택할 수 있습니다. 

    ![업데이트 자동 설치 옵션을 선택합니다.](media/azure-stack-edge-install-2110-updates/install-updates-portal-2.png)    

    업데이트를 다운로드한 다음 **나중에 수동으로 업데이트를 설치할** 수도 있습니다.

    ![나중에 수동으로 업데이트 설치 옵션을 선택합니다.](media/azure-stack-edge-install-2110-updates/install-updates-portal-3.png)

3. 업데이트 다운로드가 시작됩니다. 다운로드가 진행 중이라는 알림이 표시됩니다.

    ![진행 중인 업데이트 다운로드를 표시하는 알림](media/azure-stack-edge-install-2110-updates/install-updates-portal-4.png)

    알림 배너가 Azure Portal에도 표시됩니다. 이는 다운로드 진행률을 나타냅니다. 이 알림을 선택하거나 **디바이스 업데이트** 를 선택하여 업데이트의 자세한 상태를 확인할 수 있습니다.

    ![디바이스 업데이트 블레이드에서 자세한 업데이트 상태 보기](media/azure-stack-edge-install-2110-updates/install-updates-portal-5.png)


4. 다운로드가 완료되면 완료되었음을 알리도록 알림 배너가 업데이트됩니다. 업데이트를 자동으로 설치하도록 선택한 경우 설치가 자동으로 시작됩니다.

    나중에 업데이트를 수동으로 설치하도록 선택한 경우 알림을 선택하여 **디바이스 업데이트 블레이드를** 엽니다. **업데이트 설치를** 선택합니다.
 
    ![업데이트를 다운로드한 후 업데이트 설치를 선택합니다.](media/azure-stack-edge-install-2110-updates/install-updates-portal-6.png)
 
5. 설치가 진행 중이라는 알림이 표시됩니다. 또한 포털에는 설치가 진행 중임을 나타내는 정보 알림도 표시됩니다. 디바이스가 오프라인 상태가 되고 유지 관리 모드로 전환됩니다.
   
    ![디바이스가 유지 관리 중임을 표시하는 배너 알림 ](media/azure-stack-edge-install-2110-updates/install-updates-portal-7.png)

6. 이는 1노드 디바이스이므로 업데이트가 설치된 후 디바이스가 다시 시작됩니다. 

    ![디바이스가 다시 시작 중임을 표시하는 배너 알림](media/azure-stack-edge-install-2110-updates/install-updates-portal-8.png)

7. 시스템을 다시 시작하면 디바이스 소프트웨어가 업데이트를 완료합니다. Kubernetes 소프트웨어 업데이트가 자동으로 시작됩니다. 디바이스가 다시 오프라인으로 전환되고 유지 관리 모드에 있습니다.

    ![디바이스가 유지 관리 모드에 있음을 표시하는 배너 알림](media/azure-stack-edge-install-2110-updates/install-updates-portal-9.png)   


8. 디바이스 소프트웨어 및 Kubernetes 업데이트가 성공적으로 설치되면 배너 알림이 사라집니다. 디바이스 상태가 로 **업데이트됩니다. 디바이스가 온라인 상태입니다.** 

    ![업데이트 완료 및 디바이스가 온라인](media/azure-stack-edge-install-2110-updates/install-updates-portal-10.png)

    로컬 웹 UI로 이동한 다음, **소프트웨어 업데이트** 페이지로 이동합니다. 디바이스 소프트웨어 및 Kubernetes가 성공적으로 업데이트되고 소프트웨어 버전에 해당 버전이 이를 반영하는지 확인합니다.

    ![로컬 UI에서 업데이트된 소프트웨어 버전 보기](media/azure-stack-edge-install-2110-updates/install-updates-portal-11.png)


