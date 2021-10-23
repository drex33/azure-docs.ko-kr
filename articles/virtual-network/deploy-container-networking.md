---
title: Azure Virtual Network 컨테이너 네트워킹 배포 | Microsoft Docs
description: Kubernetes 클러스터용 Azure Virtual Network CNI(컨테이너 네트워크 인터페이스) 플러그 인을 배포하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 64aa913f6c9c5041356f193dcf0c9945deb852fb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257787"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Azure Virtual Network 컨테이너 네트워크 인터페이스 플러그 인 배포

Azure 가상 머신에 설치되는 Azure Virtual Network CNI(컨테이너 네트워크 인터페이스) 플러그 인 Kubernetes Pod 및 Docker 컨테이너에 가상 네트워크 기능을 제공합니다. 플러그 인에 대해 자세히 알아보려면 [컨테이너가 Azure Virtual Network 기능을 사용하도록 설정](container-networking-overview.md)을 참조하세요. 또한 [고급 네트워킹](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 옵션을 선택하여 AKS(Azure Kubernetes Service)에서 플러그 인을 사용할 수도 있습니다. 이렇게 하면 AKS 컨테이너가 가상 네트워크에 자동으로 배치됩니다.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>ACS-Engine Kubernetes 클러스터용 플러그 인 배포

ACS-Engine은 Azure Resource Manager 템플릿을 사용하여 Kubernetes 클러스터를 배포합니다. 클러스터 구성은 템플릿 생성 시 도구로 전달되는 JSON 파일에서 지정됩니다. 지원되는 클러스터 설정 및 해당 설명의 전체 목록을 자세히 확인하려면 [Microsoft Azure Container Service Engine - 클러스터 정의](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md)를 참조하세요. 플러그 인은 ACS-Engine을 사용하여 만드는 클러스터의 기본 네트워킹 플러그 인입니다. 플러그 인을 구성할 때 중요한 네트워크 구성 설정은 다음과 같습니다.

  | 설정                              | Description                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | 마스터 노드에 할당되는 IP 주소입니다. 필수 설정입니다.                                     |
  | kubernetesConfig 아래의 clusterSubnet | 클러스터가 배포되며 IP 주소가 Pod에 할당되는 가상 네트워크 서브넷의 CIDR입니다.   |
  | masterProfile 아래의 vnetSubnetId     | 클러스터를 배포할 서브넷의 Azure Resource Manager 리소스 ID를 지정합니다.                    |
  | vnetCidr                             | 클러스터가 배포된 가상 네트워크의 CIDR입니다.                                                             |
  | kubeletConfig 아래의 max-Pods         | 모든 에이전트 가상 머신의 최대 Pod 수입니다. 플러그 인의 경우 기본값은 30입니다. 최대 250개를 지정할 수 있습니다.  |

### <a name="example-configuration"></a>구성 예

아래 json 예제는 다음 속성이 적용되는 클러스터용입니다.
-   마스터 노드가 1개이고 에이전트 노드가 2개인 클러스터 
-   마스터 및 에이전트 노드가 모두 있는 *KubeClusterSubnet*(10.0.0.0/20) 서브넷에 배포되는 클러스터

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Kubernetes 클러스터용 플러그 인 배포

Kubernetes 클러스터의 모든 Azure 가상 머신에 플러그 인을 설치하려면 다음 단계를 완료합니다.

1. [플러그 인을 다운로드하여 설치합니다](#download-and-install-the-plug-in).
2. Pod에 IP 주소를 할당할 모든 가상 머신에서 가상 네트워크 IP 주소 풀을 미리 할당합니다. 모든 Azure 가상 머신의 각 네트워크 인터페이스에서는 기본 가상 네트워크 개인 IP 주소가 제공됩니다. Pod용 IP 주소 풀은 다음 옵션 중 하나를 사용하여 가상 머신 네트워크 인터페이스에서 보조 주소(*ipconfigs*)로 추가됩니다.

   - **CLI**: [Azure CLI를 사용해 여러 IP 주소 할당](./ip-services/virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [PowerShell을 사용해 여러 IP 주소 할당](./ip-services/virtual-network-multiple-ip-addresses-powershell.md)
   - **포털**: [Azure Portal을 사용해 여러 IP 주소 할당](./ip-services/virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager 템플릿**: [템플릿을 사용해 여러 IP 주소 할당](./template-samples.md)

   가상 머신에 표시하려는 모든 Pod에 할당하기에 충분한 IP 주소를 추가해야 합니다.

3. 클러스터 생성 중에 kubelet에 `–network-plugin=cni`를 전달하여 클러스터에 네트워킹 기능을 제공할 플러그 인을 선택합니다. Kubernetes는 기본적으로 플러그 인과 구성 파일이 이미 설치되어 있는 디렉터리에서 이러한 항목을 찾습니다.
4. Pod가 인터넷에 액세스하도록 하려면 인터넷 트래픽의 원본 NAT를 수행하도록 Linux 가상 머신에서 다음 *iptables* 규칙을 추가합니다. 다음 예제에서 지정된 IP 범위는 10.0.0.0/8입니다.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   규칙은 지정된 IP 범위로 전송되지 않는 트래픽에 대해 NAT를 수행합니다. 여기서는 이전 범위를 벗어난 모든 트래픽이 인터넷 트래픽이라고 가정합니다. 가상 머신의 가상 네트워크 IP 범위, 피어링된 가상 네트워크의 IP 범위 및 온-프레미스 네트워크의 IP 범위를 지정하도록 선택할 수 있습니다.

   Windows 가상 머신은 소속 서브넷 외부에 대상이 있는 트래픽의 원본 NAT를 자동으로 수행합니다. 사용자 지정 IP 범위를 지정할 수는 없습니다.

이전 단계를 완료하고 나면 Kubernetes 에이전트 가상 머신에 표시된 Pod에 가상 네트워크의 개인 IP 주소가 자동으로 할당됩니다.

## <a name="deploy-plug-in-for-docker-containers"></a>Docker 컨테이너용 플러그 인 배포

1. [플러그 인을 다운로드하여 설치합니다](#download-and-install-the-plug-in).
2. 다음 명령을 사용하여 Docker 컨테이너를 만듭니다.

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

컨테이너가 할당된 풀에서 IP 주소를 자동으로 받기 시작합니다. Docker 컨테이너로의 트래픽을 부하 분산하려는 경우에는 소프트웨어 부하 분산 장치 뒤에 컨테이너를 배치해야 합니다. 또한 가상 머신용 정책과 프로브를 만들 때와 같은 방식으로 부하 분산 장치 프로브를 구성해야 합니다.

### <a name="cni-network-configuration-file"></a>CNI 네트워크 구성 파일

CNI 네트워크 구성 파일은 JSON 형식으로 기술되어 있습니다. 이 파일의 기본 위치는 `/etc/cni/net.d`(Linux) 및 `c:\cni\netconf`(Windows)입니다. 플러그 인의 구성을 지정하는 이 파일은 Windows와 Linux에서 서로 다릅니다. 다음 json은 샘플 Linux 구성 파일입니다. 그 아래에는 몇 가지 주요 설정의 설명이 나와 있습니다. 이 파일은 변경하지 않아도 됩니다.

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>설정 설명

- **cniVersion**: Azure Virtual Network CNI 플러그 인은 [CNI 사양](https://github.com/containernetworking/cni/blob/master/SPEC.md) 버전 0.3.0 및 0.3.1을 지원합니다.
- **name**: 네트워크의 이름입니다. 이 속성은 원하는 고유한 값으로 설정할 수 있습니다.
- **type**: 네트워크 플러그 인의 이름입니다. *azure-vnet* 으로 설정됩니다.
- **mode**: 작동 모드입니다. 이 필드는 선택 사항입니다. 지원되는 모드는 “bridge”뿐입니다. 자세한 내용은 [작동 모드](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md)를 참조하세요.
- **bridge**: 가상 네트워크에 컨테이너를 연결하는 데 사용할 브리지의 이름입니다. 이 필드는 선택 사항입니다. 이 필드에 내용을 입력하지 않으면 플러그 인은 마스터 인터페이스 인덱스를 기준으로 하여 고유한 이름을 자동으로 선택합니다.
- **ipam type**: IPAM 플러그 인의 이름입니다. 항상 *azure-vnet-ipam* 으로 설정됩니다.

## <a name="download-and-install-the-plug-in"></a>플러그 인 다운로드 및 설치

[GitHub](https://github.com/Azure/azure-container-networking/releases)에서 플러그 인을 다운로드합니다. 사용 중인 플랫폼용 최신 버전을 다운로드하세요.

- **Linux**: [azure-vnet-cni-linux-amd64-\<version no.\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<version no.\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

[Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) 또는 [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1)용 설치 스크립트를 컴퓨터에 복사합니다. 컴퓨터의 `scripts` 디렉터리에 스크립트를 저장하고 파일 이름을 `install-cni-plugin.sh`(Linux) 또는 `install-cni-plugin.ps1`(Windows)로 지정합니다. 플러그 인을 설치하려면 사용 중인 플러그 인 버전을 지정하여 플랫폼에 적합한 스크립트를 실행합니다. 예를 들어 *v1.0.12-rc3* 을 지정할 수 있습니다.

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

위의 스크립트는 `/opt/cni/bin`(Linux) 및 `c:\cni\bin`(Windows)에 플러그 인을 설치합니다. 설치된 플러그 인에는 설치 후에 작동하는 간단한 네트워크 구성 파일이 포함되어 있습니다. 이 파일은 업데이트하지 않아도 됩니다. 파일의 설정에 대해 자세히 알아보려면 [CNI 네트워크 구성 파일](#cni-network-configuration-file)을 참조하세요.