---
title: Azure Portal에서 Azure Stack Edge Mini R 디바이스에 대한 네트워크 설정을 구성하는 자습서
description: Azure Stack Edge Mini R을 배포하기 위한 자습서에서는 물리적 디바이스에 대한 네트워크, 컴퓨팅 네트워크 및 웹 프록시 설정의 구성을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/12/2021
ms.author: alkohli
ms.openlocfilehash: 68667c495384320731b8a7898e3590aa8487a13e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860722"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>자습서: Azure Stack Edge Mini R에 대한 네트워크 구성

이 자습서에서는 로컬 웹 UI를 사용하여 온보드 GPU가 있는 Azure Stack Edge Mini R 디바이스의 네트워크를 구성하는 방법을 설명합니다.

연결 프로세스를 완료하는 데 20분 정도가 소요됩니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 필수 조건
> * 네트워크 구성
> * 컴퓨팅 네트워크 사용
> * 웹 프록시 구성


## <a name="prerequisites"></a>필수 조건

Azure Stack Edge Mini R 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Mini R 설치](azure-stack-edge-gpu-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.
* [Azure Stack Edge Mini R에 연결](azure-stack-edge-mini-r-deploy-connect.md)에 자세히 설명된 대로 디바이스의 로컬 웹 UI에 연결했습니다.


## <a name="configure-network"></a>네트워크 구성

**시작** 페이지에는 물리적 디바이스를 구성하고 Azure Stack Edge 서비스에 등록하는 데 필요한 다양한 설정이 표시됩니다. 

다음 단계에 따라 디바이스에 대한 네트워크를 구성합니다.

1. 디바이스의 로컬 웹 UI에서 **시작** 페이지로 이동합니다. 

2. 0일 업데이트가 필요한 경우 유선 연결을 사용하여 데이터 포트를 구성하여 여기에서 해당 작업을 수행할 수 있습니다. 이 디바이스에 대해 유선 연결을 설정하는 방법에 대한 자세한 내용은 [디바이스에 케이블 연결](azure-stack-edge-mini-r-deploy-install.md#cable-the-device)을 참조하세요. 업데이트가 끝난 후에는 유선 연결을 제거할 수 있습니다.

3. Wi-Fi 및 서명 체인에 대한 인증서를 만듭니다. 서명 체인과 Wi-Fi 인증서 모두 *.cer* 파일 확장명을 가진 DER 형식이어야 합니다. 자세한 지침은 [인증서 만들기](azure-stack-edge-gpu-manage-certificates.md)를 참조하세요. 인증을 위해 인증서 대신 Wi-Fi 프로필을 사용하는 경우 이 단계는 선택 사항입니다.

   > [!NOTE] 
   > 개인 Wi-Fi 네트워크에서 암호 기반 인증을 사용하는 경우 인증서 단계를 건너뛸 수 있습니다. Wi-Fi 포트를 구성한 다음, Wi-Fi 프로필을 업로드하기만 하면 됩니다.</br></br> WPA2 - 개인 네트워크용 Wi-Fi 프로필에 대해 알아보고 Wi-Fi 프로필을 내보내는 방법에 대해 알아보려면 [Wi-Fi 프로필 사용](azure-stack-edge-mini-r-use-wifi-profiles.md)을 참조하세요.

4. 디바이스에 인증서를 추가합니다. 

   1. 로컬 웹 UI에서 **시작** 으로 이동합니다. **보안** 타일에서 **인증서** 를 선택한 다음, **구성** 을 선택합니다. 

      [![로컬 웹 UI “인증서” 페이지](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

   1. **+ 인증서 추가** 를 선택합니다. 
    
      [![로컬 웹 UI "인증서" 페이지 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

   2. 서명 체인을 업로드하고 **적용** 을 선택합니다.

      ![로컬 웹 UI "인증서" 페이지 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

   3. Wi-Fi 인증서를 사용하여 절차를 반복합니다. 

      ![로컬 웹 UI "인증서" 페이지 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

   4. 새 인증서는 **인증서** 페이지에 표시됩니다. 

      [![로컬 웹 UI "인증서" 페이지 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

   5. **시작** 으로 돌아갑니다.

5. Wi-Fi 포트를 구성합니다. **네트워크** 타일에서 **구성** 을 선택합니다.  

   물리적 디바이스에 5개의 네트워크 인터페이스가 있습니다. 포트 1 및 포트 2는 1Gbps 네트워크 인터페이스입니다. PORT 3 및 PORT 4는 모두 10-Gbps 네트워크 인터페이스입니다. 다섯 번째 포트는 Wi-Fi 포트입니다. 

   [![로컬 웹 UI "네트워크 설정" 페이지 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    

   Wi-Fi 포트를 선택하고 포트 설정을 구성합니다. 

   > [!IMPORTANT]
   > Wi-Fi 포트의 고정 IP 주소를 구성하는 것이 좋습니다.  


   ![로컬 웹 UI "네트워크 설정" 페이지 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

   Wi-Fi 포트 설정을 적용한 후 **네트워크** 페이지를 업데이트합니다.

   ![로컬 웹 UI "네트워크 설정" 페이지 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

6. **Wi-Fi 프로필 추가** 를 선택하고 Wi-Fi 프로필을 업로드합니다. 

   ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)

   무선 네트워크 프로필은 무선 네트워크에 연결할 수 있도록 SSID(네트워크 이름), 암호 키 및 보안 정보를 포함합니다. 네트워크 관리자로부터 사용자 환경에 대한 Wi-Fi 프로필을 가져올 수 있습니다.

   ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

   프로필을 추가하면 새 프로필을 반영하도록 Wi-Fi 프로필 목록이 업데이트됩니다. 프로필에 **연결 상태** 가 **연결 끊김** 으로 표시됩니다. 

   ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)

7. 무선 네트워크 프로필이 성공적으로 로드되면 이 프로필에 연결합니다. **Wi-Fi 프로필에 연결** 을 선택합니다. 

   ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

8. 이전 단계에서 추가한 Wi-Fi 프로필을 선택하고 **적용** 을 선택합니다. 

   ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

   **연결 상태** 가 **연결됨** 으로 업데이트되어야 합니다. 신호의 품질을 나타내기 위해 신호 강도가 업데이트됩니다. 

   ![로컬 웹 UI "포트 Wi-Fi 네트워크 설정" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

   > [!NOTE]
   > 많은 양의 데이터를 전송하려면 무선 네트워크 대신 유선 연결을 사용하는 것이 좋습니다. 

9. 노트북에서 디바이스에 있는 PORT 1의 연결을 끊습니다. 

10. 네트워크 설정을 구성할 때 다음에 유의합니다.

    - 환경에서 DHCP를 사용하도록 설정하면 네트워크 인터페이스가 자동으로 구성됩니다. IP 주소, 서브넷, 게이트웨이 및 DNS가 자동으로 할당됩니다.
    - DHCP를 사용하지 않는 경우 필요에 따라 고정 IP를 할당할 수 있습니다.
    - 네트워크 인터페이스를 IPv4로 구성할 수 있습니다.
    - NIC(네트워크 인터페이스 카드) 팀 구성 또는 링크 집계는 Azure Stack Edge에서 지원되지 않습니다.
    - 모든 포트에 대한 일련 번호는 노드 일련 번호에 해당합니다. K 시리즈 디바이스의 경우 하나의 일련 번호만 표시됩니다.

     > [!NOTE]
     > 디바이스에 연결할 다른 IP 주소가 없으면 네트워크 인터페이스의 로컬 IP 주소를 고정에서 DCHP로 전환하지 않는 것이 좋습니다. 하나의 네트워크 인터페이스를 사용하며 DHCP로 전환하는 경우 DHCP 주소를 확인할 방법이 없습니다. DHCP 주소로 변경하려는 경우 디바이스가 서비스에 등록될 때까지 기다렸다가 변경합니다. 그러면 서비스에 대한 Azure Portal의 **디바이스 속성** 에서 모든 어댑터의 IP를 볼 수 있습니다.

네트워크 설정을 구성하고 적용한 후 **다음: 컴퓨팅** 을 선택하여 컴퓨팅 네트워크를 구성합니다.

## <a name="enable-compute-network"></a>컴퓨팅 네트워크 사용

다음 단계를 수행하여 컴퓨팅을 사용하도록 설정하고 컴퓨팅 네트워크를 구성합니다. 


1. **컴퓨팅** 페이지에서 컴퓨팅을 사용하도록 설정하려는 네트워크 인터페이스를 선택합니다. 

   ![로컬 UI의 컴퓨팅 페이지 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. **네트워크 설정** 대화 상자에서 **사용** 을 선택합니다. 컴퓨팅을 사용하도록 설정하면 해당 네트워크 인터페이스의 디바이스에서 가상 스위치가 생성됩니다. 가상 스위치는 디바이스의 컴퓨팅 인프라에 사용됩니다. 
    
1. **Kubernetes 노드 IP** 를 할당합니다. 이러한 고정 IP 주소는 컴퓨팅 VM에 대한 것입니다.  

   *n*-노드 디바이스의 경우 시작 및 종료 IP 주소를 사용하여 컴퓨팅 VM에 대한 최소 *n+1* IPv4 주소의 연속 범위가 제공됩니다. Azure Stack Edge가 1-노드 디바이스인 경우에는 최소 2개의 연속 IPv4 주소가 제공됩니다.

   > [!IMPORTANT]
   > Azure Stack Edge의 Kubernetes는 pod에 172.27.0.0/16 서브넷을 사용하고 서비스에 172.28.0.0/16 서브넷을 사용합니다. 네트워크에서 사용 중이 아닌지 확인합니다. 이러한 서브넷이 네트워크에서 이미 사용 중인 경우에는 디바이스의 PowerShell 인터페이스에서 `Set-HcsKubeClusterNetworkInfo` cmdlet을 실행하여 이러한 서브넷을 변경할 수 있습니다. 자세한 내용은 [Kubernetes pod 및 서비스 서브넷](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets)을 참조하세요.

1. **Kubernetes 외부 서비스 IP** 를 할당합니다. 이는 부하 분산 IP 주소이기도 합니다. 이러한 연속 IP 주소는 Kubernetes 클러스터 외부에 노출하려는 서비스에 대한 것이며, 노출되는 서비스의 수에 따라 고정 IP 범위를 지정합니다. 
    
   > [!IMPORTANT]
   > 컴퓨팅 모듈에 액세스하려면 Azure Stack Edge Mini R Hub 서비스에 대한 최소 1개의 IP 주소를 지정하는 것이 좋습니다. 그런 다음, 필요에 따라 클러스터 외부에서 액세스해야 하는 다른 서비스/IoT Edge 모듈(서비스/모듈당 1개)에 대한 추가 IP 주소를 지정할 수 있습니다. 서비스 IP 주소는 나중에 업데이트될 수 있습니다. 
    
1. **적용** 을 선택합니다.

   ![로컬 UI의 컴퓨팅 페이지 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. 구성을 적용하는 데 몇 분 정도 걸리며, 브라우저를 새로 고쳐야 할 수도 있습니다. 지정한 포트가 컴퓨팅에 사용하도록 설정된 것을 볼 수 있습니다. 
 
   ![로컬 UI의 컴퓨팅 페이지 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

   완료되면 **다음: 웹 프록시** 를 선택하여 웹 프록시를 구성합니다.  

  
## <a name="configure-web-proxy"></a>웹 프록시 구성

이는 선택적 구성입니다.

> [!IMPORTANT]
> PAC(프록시 자동 구성) 파일은 지원되지 않습니다. PAC 파일은 웹 브라우저 및 다른 사용자 에이전트가 지정된 URL을 가져오는 데 적절한 프록시 서버(액세스 방법)를 자동으로 선택하는 방법을 정의합니다. 프록시의 인증서를 신뢰할 수 없기 때문에 모든 트래픽을 가로채고 읽는(그런 다음, 자체 인증을 사용하여 모든 항목을 다시 서명함) 프록시는 호환되지 않습니다. 일반적으로 투명 프록시는 Azure Stack Edge Mini R에서 잘 작동합니다. 투명하지 않은 웹 프록시는 지원되지 않습니다.


1. **웹 프록시 설정** 페이지에서 다음 단계를 수행합니다.

   1. **웹 프록시 URL** 상자에 `http://host-IP address or FQDN:Port number` 형식으로 URL을 입력합니다. HTTPS URL은 지원되지 않습니다.

   2. 구성한 웹 프록시 설정의 유효성을 검사하고 적용하려면 **적용** 을 선택합니다.

   ![로컬 웹 UI "웹 프록시 설정" 페이지](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)<!--UI text update is needed to remove NTLM from instruction text.-->

2. 설정이 적용된 후 **다음: 디바이스** 를 입력하고 선택합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 필수 조건
> * 네트워크 구성
> * 컴퓨팅 네트워크 사용
> * 웹 프록시 구성


Azure Stack Edge Mini R 디바이스를 설정하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [디바이스 설정 구성](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
