---
title: Azure Stack Edge Pro GPU 디바이스에 업데이트 설치 | Microsoft Docs
description: Azure Portal, Azure Stack Edge Pro GPU 디바이스의 로컬 웹 UI 및 Kubernetes 클러스터를 사용하여 업데이트를 적용하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 13fc1ad892509d04750e51b593cc7aaf07f0c91a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082933"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 업데이트 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 로컬 웹 UI 및 Azure Portal을 통해 Azure Stack Edge Pro GPU에 업데이트를 설치하는 데 필요한 단계를 설명합니다. Azure Stack Edge Pro 디바이스 및 디바이스의 연결된 Kubernetes 클러스터를 최신 상태로 유지하려면 소프트웨어 업데이트나 핫픽스를 적용합니다.

이 문서에서 설명하는 절차는 다른 버전의 소프트웨어를 사용하여 수행되었지만 현재 소프트웨어 버전에도 동일한 프로세스가 적용됩니다.

## <a name="about-latest-update"></a>최신 업데이트 정보

현재 업데이트는 업데이트 2110입니다. 이 업데이트는 두 개의 업데이트, 즉 장치 업데이트와 Kubernetes 업데이트를 설치 합니다. 이 업데이트에 관련 된 버전은 다음과 같습니다.

- 장치 소프트웨어 버전- **2.2.1758.4034**
- Kubernetes server 버전- **v 1.20.9**
- IoT Edge 버전: **0.1.0-beta15**
- GPU 드라이버 버전: **460.32.03**
- CUDA 버전: **11.2**

이 업데이트의 새로운 기능에 대한 자세한 내용을 보려면 [릴리스 정보](azure-stack-edge-gpu-2110-release-notes.md)로 이동합니다.

**2110 업데이트를 적용 하려면 장치에서 2106를 실행 해야 합니다.** 

- 지원되는 최소 버전을 실행하고 있지 않은 경우에는 *해당 종속성을 충족하지 않으므로 업데이트 패키지를 설치할 수 없습니다* 라는 오류가 표시됩니다. 
- 이전 버전에서 2106으로 업데이트 한 후 2110을 설치할 수 있습니다.

업데이트 또는 핫픽스를 설치하면 디바이스가 다시 시작됩니다. Azure Stack Edge Pro GPU가 단일 노드 디바이스인 경우 진행 중인 모든 I/O가 중단되고 업데이트에 대해 최대 1.5시간의 가동 중지 시간이 발생합니다.

장치에 업데이트를 설치 하려면 다음 단계를 수행 해야 합니다.

1. 업데이트 서버의 위치를 구성 합니다. 
1. Azure Portal UI 또는 로컬 웹 UI를 통해 업데이트를 적용 합니다.

이러한 각 단계는 다음 섹션에 설명되어 있습니다.

## <a name="configure-update-server"></a>업데이트 서버 구성

1. 로컬 웹 UI에서 **구성** > **업데이트 서버** 로 이동합니다.
   
    ![업데이트 구성 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. **업데이트 서버 유형 선택** 의 드롭다운 목록에서 Microsoft 업데이트 서버(기본값) 또는 Windows Server Update Services를 선택합니다.  
   
    Windows Server Update Services에서 업데이트하는 경우 서버 URI를 지정합니다. 해당 URI의 서버는 이 서버에 연결된 모든 디바이스에 대한 업데이트를 배포합니다.

    ![업데이트 구성 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS 서버는 관리 콘솔을 통해 업데이트를 관리하고 배포하는 데 사용됩니다. WSUS 서버는 조직 내 다른 WSUS 서버의 업데이트 원본이 수도 있습니다. 업데이트 원본으로 사용되는 WSUS 서버를 업스트림 서버라고 합니다. WSUS 구현에서 하나 이상의 WSUS 서버를 네트워크에서 사용 가능한 업데이트 정보를 얻으려면 Microsoft 업데이트에 연결할 수 있어야 합니다. 관리자로 서 확인할 수 있습니다-네트워크 보안 및 구성-에 따라 다른 WSUS 서버의 수에 직접 연결 Microsoft Update.
    
    자세한 내용을 보려면 [WSUS(Windows Server Update Services)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)로 이동하세요.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 통해 업데이트를 설치하는 것이 좋습니다. 디바이스는 하루에 한 번 업데이트를 자동으로 검색합니다. 업데이트를 사용할 수 있으면 포털에 알림이 표시됩니다. 그런 다음 업데이트를 다운로드하여 설치할 수 있습니다.

> [!NOTE]
> 업데이트 설치를 진행하기 전에 디바이스가 정상 상태이고 **디바이스가 정상적으로 실행되고 있습니다!** 라는 상태가 표시되는지 확인합니다.

실행 중인 소프트웨어 버전에 따라 설치 프로세스는 약간 다를 수 있습니다. 

- 2106에서 2110로 업데이트 하는 경우에는 한 번의 클릭으로 설치 해야 합니다. 지침은 **버전 2106** 탭을 참조 하세요.
- 2110 이전 버전으로 업데이트 하는 경우 두 번의 설치를 클릭 합니다. 지침은 **버전 2105 및 이전** 탭을 참조 하세요.

### <a name="version-2106"></a>[버전 2106](#tab/version-2106)

[!INCLUDE [azure-stack-edge-install-2110-updates](../../includes/azure-stack-edge-install-2110-updates.md)]

### <a name="version-2105-and-earlier"></a>[버전 2105 및 이전 버전](#tab/version-2105-and-earlier)

1. 장치에 대 한 업데이트를 사용할 수 있는 경우 Azure Stack Edge 리소스의 **개요** 페이지에 알림이 표시 됩니다. 알림을 선택하거나 상단의 명령 모음에서 **디바이스 업데이트** 를 선택합니다. 이렇게 하면 디바이스 소프트웨어 업데이트를 적용할 수 있습니다.

    ![업데이트 후 소프트웨어 버전](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. **디바이스 업데이트** 블레이드에서 릴리스 정보의 새 기능과 관련된 사용 조건을 검토했는지 확인합니다.

    업데이트를 **다운로드하여 설치** 하거나 업데이트를 **다운로드** 하도록 선택할 수 있습니다. 그런 다음 나중에 이러한 업데이트를 설치하도록 선택할 수 있습니다.

    ![업데이트 후 소프트웨어 버전 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    업데이트를 다운로드하여 설치하려면 다운로드를 완료한 후 업데이트가 자동으로 설치되도록 하는 옵션을 선택합니다.

    ![업데이트 후 소프트웨어 버전 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. 업데이트 다운로드가 시작됩니다. 다운로드가 진행 중이라는 알림이 표시됩니다.

    ![업데이트 후 소프트웨어 버전 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    알림 배너가 Azure Portal에도 표시됩니다. 이는 다운로드 진행률을 나타냅니다.

    ![업데이트 후 소프트웨어 버전 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    이 알림을 선택하거나 **디바이스 업데이트** 를 선택하여 업데이트의 자세한 상태를 확인할 수 있습니다.

    ![업데이트 후 소프트웨어 버전 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. 다운로드가 완료되면 완료되었음을 알리도록 알림 배너가 업데이트됩니다. 업데이트를 다운로드하여 설치하도록 선택한 경우 설치가 자동으로 시작됩니다.

    업데이트를 다운로드만 하도록 선택한 경우에는 알림을 선택하여 **디바이스 업데이트** 블레이드를 엽니다. **설치** 를 선택합니다.
  
5. 설치가 진행 중이라는 알림이 표시됩니다. 또한 포털에는 설치가 진행 중임을 나타내는 정보 알림도 표시됩니다. 디바이스가 오프라인 상태가 되고 유지 관리 모드로 전환됩니다.
   
    ![업데이트 후 소프트웨어 버전 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. 이는 1노드 디바이스이므로 업데이트가 설치된 후 디바이스가 다시 시작됩니다. 다시 시작하는 동안 중요한 알림이 디바이스 하트비트가 끊겼음을 나타냅니다.

    ![업데이트 후 소프트웨어 버전 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    경고를 선택하여 해당하는 디바이스 이벤트를 확인합니다.
    
    ![업데이트 후 소프트웨어 버전 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. 시스템을 다시 시작하면 디바이스 소프트웨어가 업데이트를 완료합니다. 업데이트가 완료되면 로컬 웹 UI에서 디바이스 소프트웨어가 업데이트되었음을 확인할 수 있습니다. Kubernetes 소프트웨어 버전이 업데이트되지 않았습니다.

    ![업데이트 후 소프트웨어 버전 13](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. 디바이스 업데이트를 사용할 수 있음을 나타내는 알림 배너가 표시됩니다. 이 배너를 선택하여 디바이스에서 Kubernetes 소프트웨어 업데이트를 시작합니다. 

    ![업데이트 후 소프트웨어 버전 13a](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![업데이트 후 소프트웨어 버전 14](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    상단 명령 모음에서 **업데이트 디바이스** 를 선택하면 업데이트의 진행률을 볼 수 있습니다.  

    ![업데이트 후 소프트웨어 버전 15](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. 업데이트가 설치되면 디바이스 상태가 **디바이스가 정상적으로 실행되고 있습니다!** 로 업데이트됩니다. 

    ![업데이트 후 소프트웨어 버전 16](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    로컬 웹 UI로 이동한 다음, **소프트웨어 업데이트** 페이지로 이동합니다. Kubernetes 업데이트가 성공적으로 설치되었고 소프트웨어 버전에 해당 업데이트가 반영되었는지 확인합니다.

    ![업데이트 후 소프트웨어 버전 17](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


디바이스 소프트웨어 및 Kubernetes 업데이트가 성공적으로 설치되면 배너 알림이 사라집니다. 

---

이제 장치에 최신 버전의 장치 소프트웨어 및 Kubernetes이 있습니다.


## <a name="use-the-local-web-ui"></a>로컬 웹 UI 사용

로컬 웹 UI를 사용하는 경우 다음 두 단계로 구성됩니다.

* 업데이트 또는 핫픽스 다운로드
* 업데이트 또는 핫픽스 설치

이러한 각 단계는 다음 섹션에서 자세히 설명합니다.


### <a name="download-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 다운로드

업데이트를 다운로드하려면 다음 단계를 수행합니다. Microsoft에서 제공하는 위치 또는 Microsoft 업데이트 카탈로그에서 업데이트를 다운로드할 수 있습니다.


Microsoft 업데이트 카탈로그에서 업데이트를 다운로드하려면 다음 단계를 수행합니다.

1. 브라우저를 시작하고 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)으로 이동합니다.

    ![카탈로그 검색](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드하려는 핫픽스의 KB(기술 자료) 번호 또는 업데이트의 단어를 입력합니다. 예를 들어 **Azure Stack Edge** 를 입력한 다음, **검색** 을 클릭합니다.
   
    업데이트 목록은 Azure Stack Edge **업데이트 2110으로** 표시됩니다.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. **다운로드** 를 선택합니다. 다운로드할 두 개의 패키지가 있습니다. <!--KB 4616970 and KB 4616971--> 하나는 디바이스 소프트웨어 업데이트(*SoftwareUpdatePackage.exe*)를 위한 것이고 다른 하나는 Kubernetes 업데이트(*Kubernetes_Package.exe*)를 위한 것입니다. 로컬 시스템의 폴더에 패키지를 다운로드합니다. 디바이스에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

### <a name="install-the-update-or-the-hotfix"></a>업데이트 또는 핫픽스 설치

업데이트 또는 핫픽스를 설치하기 전에 다음을 확인합니다.

 - 업데이트 또는 핫픽스를 호스트에 로컬로 다운로드하거나 네트워크 공유를 통해 액세스할 수 있는지 확인합니다.
 - 로컬 웹 UI의 **개요** 페이지에 표시된 대로 디바이스 상태가 정상입니다.

   ![디바이스 업데이트](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

이 절차를 완료하는 데 약 20분 정도 걸립니다. 다음 단계를 수행하여 업데이트 또는 핫픽스를 설치합니다.

1. 로컬 웹 UI에서 **유지 관리** > **소프트웨어 업데이트** 로 이동합니다. 실행 중인 소프트웨어 버전을 기록해 둡니다. 
   
   ![디바이스 업데이트 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. 업데이트 파일의 경로를 제공합니다. 네트워크 공유에 있는 경우 업데이트 설치 파일로 이동할 수 있습니다. *SoftwareUpdatePackage.exe* 접미사가 포함된 소프트웨어 업데이트 파일을 선택합니다.

   ![디바이스 업데이트 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. **업데이트 적용을** 선택합니다.

   <!--![update device 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)-->

4. 확인하라는 메시지가 표시되면 **예** 를 선택하여 계속합니다. 단일 노드 디바이스인 경우 업데이트가 적용된 후 디바이스를 다시 시작하고 가동 중지 시간이 발생합니다. 
   
   ![디바이스 업데이트 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. 업데이트가 시작됩니다. 디바이스가 성공적으로 업데이트된 후 다시 시작됩니다. 이 시간 동안 로컬 UI에 액세스할 수 없습니다.
   
6. 다시 시작이 완료된 후 **로그인** 페이지가 열립니다. 로컬 웹 UI에서 디바이스 소프트웨어가 업데이트되었는지 확인하려면 **유지 관리** > **소프트웨어 업데이트** 로 이동합니다. 현재 릴리스의 경우 표시된 소프트웨어 버전은 **Azure Stack Edge 2110이어야** 합니다. 


7. 이제 Kubernetes 소프트웨어 버전을 업데이트 합니다. 위의 단계를 반복합니다. *Kubernetes_Package.exe* 접미사가 포함된 Kubernetes 업데이트 파일의 경로를 제공합니다.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. **업데이트 적용** 을 선택합니다.

   <!--![update device 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)-->

9. 확인하라는 메시지가 표시되면 **예** 를 선택하여 계속합니다.

10. Kubernetes 업데이트가 성공적으로 설치된 후 **유지 관리** > **소프트웨어 업데이트** 에 표시되는 소프트웨어에는 변화가 없습니다.

    ![디바이스 업데이트 6](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


## <a name="next-steps"></a>다음 단계

[Azure Stack Edge Pro 관리](azure-stack-edge-manage-access-power-connectivity-mode.md)에 대해 자세히 알아보세요.
