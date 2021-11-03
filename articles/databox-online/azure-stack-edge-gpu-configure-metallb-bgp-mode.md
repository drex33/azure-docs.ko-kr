---
title: Azure Stack Edge에서 BGP를 통해 MetalLB 구성
description: Azure Stack에 지 장치에서 부하 분산을 위해 Border Gateway Protocol via MetalLB를 구성 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/24/2021
ms.author: alkohli
ms.openlocfilehash: df4f0170f6dde0995576b0244733dea4ab01515e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052857"
---
# <a name="configure-load-balancing-with-metallb-on-your-azure-stack-edge"></a>Azure Stack Edge에서 MetalLB를 사용 하 여 부하 분산 구성

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 BGP (MetalLB via Border Gateway Protocol)를 사용 하 여 Azure Stack Edge 장치에서 부하 분산을 구성 하는 방법을 설명 합니다. 

## <a name="about-metallb-and-load-balancing"></a>MetalLB 및 부하 분산 정보

MetalLB는 운영 체제 미 설치 Kubernetes 클러스터에 대 한 부하 분산 장치 구현입니다. MetalLB는 두 가지 기능을 제공 합니다 .이 기능은 구성 된 IP 주소 풀에서 Kubernetes 부하 분산 장치 서비스에 IP 주소를 할당 한 다음 외부 네트워크에 IP를 알립니다. MetalLB는 ARP (주소 확인 프로토콜), NDP (인접 검색 프로토콜) 또는 BGP (Border Gateway Protocol)와 같은 표준 라우팅 프로토콜을 통해 이러한 기능을 달성 합니다. 

자세한 내용은 [MetalLB에 대 한 BGP 모드](https://metallb.universe.tf/configuration/#bgp-configuratioN)를 참조 하세요.

## <a name="metallb-on-azure-stack-edge"></a>Azure Stack Edge의 MetalLB

Azure Stack Edge 장치에 설치 된 Calico, MetalLB 및 Core DNS와 같은 여러 네트워킹 구성 요소가 있습니다. MetalLB는 Azure Stack Edge 장치에서 실행 되는 Kubernetes 클러스터에 후크 하 고 클러스터에 형식의 Kubernetes 서비스를 만들 수 있도록 `LoadBalancer` 합니다.

BGP 모드에서 클러스터의 모든 컴퓨터는 사용자가 제어 하는 근처의 라우터를 사용 하 여 BGP 피어 링 세션을 설정 하 고 해당 라우터에 트래픽을 전달 하는 방법을 지시 합니다. BGP (Border Gateway Protocol)를 사용 하는 MetalLB는 장치에서 실행 되는 Kubernetes 클러스터에 대 한 기본 네트워킹 모드가 아닙니다. BGP를 통해 MetalLB을 구성 하려면 랙 (대상) 스위치를 부하 분산 장치로 지정 하 고 피어 세션을 설정 합니다. 

2 개 노드 장치를 사용 하는 경우 하위 장애 조치 (failover) 시간을 얻도록 BGP 모드의 MetalLB를 구성할 수 있습니다. 이 구성은 랙 고성능 스위치에 대 한 액세스 권한이 없을 수 있으므로 표준 구성과 더 관련이 있습니다.

## <a name="configure-metallb"></a>MetalLB 구성

장치의 PowerShell 인터페이스에 연결 하 고 특정 cmdlet을 실행 하 여 BGP 모드에서 MetalLB을 구성할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.
- Compute는 장치의 한 포트에서 사용 하도록 설정 됩니다. 그러면 해당 포트에 가상 스위치가 만들어집니다. 
    - 계산을 사용 하도록 설정 하려면 장치에 대 한 로컬 UI에서 **고급 네트워킹** 페이지로 이동 하 여 계산을 사용 하도록 설정할 포트를 선택 합니다. 
    - **네트워크 설정** 페이지에서 계산에 대 한 포트를 사용 하도록 설정 합니다. 설정을 **적용** 합니다.
- 장치에서 계산에 사용 하도록 설정한 포트와 동일한 서브넷에서 Ip를 사용할 수 있습니다. 

### <a name="configuration"></a>구성

BGP 세션을 사용 하는 MetalLB에 대 한 기본 구성의 경우 다음 정보가 필요 합니다.

- MetalLB가 연결 해야 하는 피어 IP 주소입니다.
- 피어의 ASN (자치 시스템 번호)입니다. BGP를 사용 하려면 피어 세션에 대 한 ASN을 사용 하 여 경로를 발표 해야 합니다.
- ASN MetalLB는를 사용 해야 합니다. ASNs는 1에서 65534 사이의 16 비트 숫자 이며 131072에서 4294967294 사이의 32 비트 숫자입니다.

> [!IMPORTANT]
> BGP 모드에서 MetalLB를 사용 하려면 피어를 지정 해야 합니다. BGP 피어가 지정 되지 않은 경우 MetalLB는 기본 계층 2 모드에서 작동 합니다. 자세한 내용은 [MetalLB의 계층 2 모드](https://metallb.universe.tf/concepts/layer2/)를 참조 하세요. 


BGP 모드에서 MetalLB를 구성 하려면 다음 단계를 수행 합니다.

1. 장치의 [PowerShell 인터페이스로 커넥트](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 합니다.
 
1. Cmdlet을 실행 `Get-HcsExternalVirtualSwitch` 하 여 BGP 모드에 사용할 외부 가상 스위치의 이름을 가져옵니다. 이 가상 스위치는 compute에 포트를 사용 하도록 설정한 경우에 생성 됩니다.

    ```powershell
    Get-HcsExternalVirtualSwitch
    ```
1. Cmdlet을 실행 `Set-HcsBGPPeer` 하 여 BGP 피어 세션을 설정 합니다.

    ```powershell
    Set-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -PeerAsn <ASN for the peer> -SelfAsn <Your ASN> -SwitchName <Name of virtual switch on the port enabled for compute> -HoldTimeInSeconds <Optional hold time in seconds> 
    ```
1. 세션을 설정한 후에는 cmdlet을 실행 `Get-HcsBGPPeers` 하 여 가상 스위치에 있는 피어 세션을 가져옵니다.

    ```powershell
    Get-HcsBGPPeers -SwitchName <Name of virtual switch that you enabled for compute>
    ```
1. Cmdlet을 실행 `Remove-HcsBGPPeer` 하 여 피어 세션을 제거 합니다. 

    ```powershell
    Remove-HcsBGPPeer -PeerAddress <IP address of the port that you enabled for compute> -SwitchName <Name of virtual switch on the port enabled for compute>
    ```
1. 을 실행 `Get-HcsBGPPeers` 하 여 피어 세션이 제거 되었는지 확인 합니다.

출력의 예제는 다음과 같습니다. 

```powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $Name = "dbe-1csphq2.microsoftdatabox.com"
PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to
be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface
to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft
Support could result in an unsupported configuration.
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : False
EnabledForMgmt                : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 3cb2d0ae-6a7b-44cc-8a5d-8eac2d1c0436
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

Name                          : vSwitch2
InterfaceAlias                : {Port3, Port4}
EnableIov                     : False
MacAddressPools               :
IPAddressPools                : {}
BGPPeers                      :
ConfigurationSource           : Dsc
EnabledForCompute             : False
EnabledForStorage             : True
EnabledForMgmt                : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 8dd480c0-8f22-42b1-8621-d2a43f70690d
VirtualNetworks               : {}
EnableEmbeddedTeaming         : True
Vnics                         : {}
Type                          : External

[dbe-1csphq2.microsoftdatabox.com]: PS>Set-HcsBGPPeer -PeerAddress 10.126.77.125 -PeerAsn 64512 -SelfAsn 64513 -SwitchName vSwitch1 -HoldTimeInSeconds 15
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1

PeerAddress   PeerAsn SelfAsn HoldTime
-----------   ------- ------- --------
10.126.77.125 64512   64513         15

[dbe-1csphq2.microsoftdatabox.com]: PS>Remove-HcsBGPPeer -PeerAddress 10.126.77.125 -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>Get-HcsBGPPeers -SwitchName vSwitch1
[dbe-1csphq2.microsoftdatabox.com]: PS>
```

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 장치에서 Kubernetes 클러스터의 네트워킹](azure-stack-edge-gpu-kubernetes-networking.md)에 대해 자세히 알아보세요.