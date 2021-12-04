---
title: Azure Service Fabric 네트워킹 모범 사례
description: Azure Service Fabric을 사용하여 네트워크 연결을 관리하기 위한 규칙 및 디자인 고려 사항입니다.
author: chrpap
ms.topic: conceptual
ms.date: 10/29/2021
ms.author: chrpap
ms.openlocfilehash: 65c7140c28516fdcc3b65f9ae4b4a71103013867
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133539594"
---
# <a name="networking"></a>네트워킹

Azure Service Fabric 클러스터를 만들고 관리할 때는 노드와 애플리케이션용 네트워크 연결을 제공합니다. 네트워킹 리소스에는 IP 주소 범위, 가상 네트워크, 부하 분산 장치 및 네트워크 보안 그룹이 포함됩니다. 이 문서에서는 이러한 리소스와 관련된 모범 사례에 대해 알아봅니다.

Azure [Service Fabric 네트워킹 패턴](service-fabric-patterns-networking.md)을 검토하여 기존 가상 네트워크 또는 서브넷, 고정 공용 IP 주소, 내부 전용 부하 분산 디바이스 또는 내부 및 외부 부하 분산 디바이스와 같은 기능을 사용하는 클러스터를 만드는 방법을 알아보세요.

## <a name="infrastructure-networking"></a>인프라 네트워킹
Resource Manager 템플릿에서 *enableAcceleratedNetworking* 을 선언하면 가속화된 네트워킹을 통해 가상 머신 성능을 최대화할 수 있습니다. 아래에는 가속화된 네트워킹을 사용하도록 설정하는 Virtual Machine Scale Set NetworkInterfaceConfigurations의 코드 조각이 나와 있습니다.

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
[가속화된 네트워킹을 사용하는 Linux](/virtual-network/create-vm-accelerated-networking-cli.md) 및 [가속화된 네트워킹을 사용하는 Windows](/virtual-network/create-vm-accelerated-networking-powershell.md)에서 Service Fabric 클러스터를 프로비전할 수 있습니다.

가속화된 네트워킹은 D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2, Ms/Mms를 지원합니다. Service Fabric Windows 클러스터의 경우 2019년 1월 23일에 Standard_DS8_v3 SKU를 사용하여, Service Fabric Linux 클러스터의 경우 2019년 1월 29일에 Standard_DS12_v2를 사용하여 가속화된 네트워킹을 테스트한 결과 정상 작동이 확인되었습니다. 가속화된 네트워킹에는 4개 이상의 vCPU가 필요합니다. 

기존 Service Fabric 클러스터에서 가속화된 네트워킹을 사용하도록 설정하려면 먼저 [Virtual Machine Scale Set를 추가하여 Service Fabric 클러스터를 확장](/virtual-machine-scale-set-scale-node-type-scale-out.md)해 다음 작업을 수행해야 합니다.
1. 가속화된 네트워킹이 사용하도록 설정된 NodeType 프로비전
2. 가속화된 네트워킹이 사용하도록 설정되어 있는 프로비전된 NodeType으로 서비스와 상태 마이그레이션

가속화된 네트워킹을 현재 위치에서 사용하도록 설정하면 가동 중지 시간이 발생하므로, 기존 클러스터에서 가속화된 네트워킹을 사용하려면 인프라를 확장해야 합니다. [기존 NIC에서 가속화된 네트워킹을 사용하도록 설정하기 전에 가용성 세트의 모든 가상 머신을 중지하고 할당을 취소](/virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)해야 하기 때문입니다.

## <a name="cluster-networking"></a>클러스터 네트워킹

* [Service Fabric 네트워킹 패턴](service-fabric-patterns-networking.md)에 요약되어 있는 단계를 수행하면 기존 가상 네트워크에 Service Fabric 클러스터를 배포할 수 있습니다.

* 노드 형식에 대해 클러스터에 대한 인바운드 및 아웃바운드 트래픽을 제한하기 위한 NSG(네트워크 보안 그룹)가 권장됩니다. NSG에서 필요한 포트가 열려 있는지 확인합니다. 

* Service Fabric 시스템 서비스를 포함하는 주 노드 형식은 외부 부하 분산 장치를 통해 표시하지 않아도 되며 [내부 부하 분산 장치](service-fabric-patterns-networking.md#internal-only-load-balancer)를 통해 표시할 수 있습니다.

* 클러스터에는 [고정 공용 IP 주소](service-fabric-patterns-networking.md#static-public-ip-address-1)를 사용합니다.

## <a name="network-security-rules"></a>네트워크 보안 규칙

아래에 설명된 네트워크 보안 그룹 규칙은 일반적인 구성에 권장되는 최소값입니다. 또한 선택적 규칙을 원하는 경우 운영 클러스터에 필요한 규칙도 포함됩니다. 필수 포트를 열지 못하거나 IP/URL을 승인하면 클러스터의 적절한 작동이 차단되며 지원되지 않을 수 있습니다. [자동 OS 이미지 업그레이드는](/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) Windows 업데이트에 권장됩니다. [패치 오케스트레이션 애플리케이션을](service-fabric-patch-orchestration-application.md) 사용하는 경우 ServiceTag [AzureUpdateDelivery와](/virtual-network/service-tags-overview.md) 함께 추가 규칙이 필요합니다.

이러한 규칙은 적절한 운영 클러스터에 필수입니다. 일반적인 구성에 대한 최소값을 설명합니다. 또한 네트워크 피어링 및 jumpbox 개념(예: Azure Bastion)을 사용하여 완전한 보안 잠금을 설정할 수 있습니다. 필수 포트를 열지 못하거나 IP/URL을 승인하면 클러스터의 적절한 작동이 차단되며 지원되지 않을 수 있습니다. [자동 OS 이미지 업그레이드는](/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) Windows 업데이트를 위한 권장 사항입니다. 패치 [오케스트레이션 애플리케이션에는](service-fabric-patch-orchestration-application.md) ServiceTag [AzureUpdateDelivery가](/virtual-network/service-tags-overview.md) 있는 추가 규칙이 필요합니다.

### <a name="inbound"></a>인바운드 
|우선 순위   |Name               |포트        |프로토콜  |원본             |대상       |작업        | 필수
|---        |---                |---         |---       |---                |---               |---           |---
|3900       |Azure portal       |19080       |TCP       |ServiceFabric      |모두               |허용         | 아니요
|3910       |클라이언트 API         |19000       |TCP       |인터넷           |모두               |허용         | 아니요
|3920       |SFX + 클라이언트 API   |19080       |TCP       |인터넷           |모두               |허용         | 예
|3930       |클러스터            |1025-1027   |TCP       |VirtualNetwork     |모두               |허용         | 예
|3940       |Ephemeral          |49152-65534 |TCP       |VirtualNetwork     |모두               |허용         | 예
|3950       |애플리케이션        |20000-30000 |TCP       |VirtualNetwork     |모두               |허용         | 예
|3970       |RDP                |3389-3488   |TCP       |인터넷           |모두               |거부          | 아니요
|3980       |SSH                |22          |TCP       |인터넷           |모두               |거부          | 아니요
|3990       |사용자 지정 엔드포인트    |443         |TCP       |인터넷           |모두               |거부          | 아니요

인바운드 보안 규칙에 대한 자세한 내용:

* **Azure Portal**. 이 포트는 Service Fabric 리소스 공급자가 Azure 관리 포털에 표시 하기 위해 클러스터에 대 한 정보를 쿼리 하는 데 사용 됩니다. Service Fabric 리소스 공급자에서 해당 포트에 액세스할 수 없는 경우 Azure Portal에 '노드를 찾을 수 없음' 또는 'UpgradeServiceNotReachable'과 같은 메시지가 표시되고 노드 및 애플리케이션 목록이 빈 상태로 표시됩니다. 즉, Azure 관리 포털에서 클러스터를 표시하려는 경우 부하 분산 디바이스는 공용 IP 주소를 노출해야 하고 NSG는 들어오는 19080 트래픽을 허용해야 합니다.  

* **클라이언트 API**. PowerShell (클래식)에서 사용 하는 Api에 대 한 클라이언트 연결 끝점입니다. 

* **SFX + 클라이언트 API**. 이 포트는 Service Fabric Explorer에서 클러스터를 찾아보고 관리 하는 데 사용 됩니다. REST/PowerShell (HTTP)/CLI/SNETS와 같은 대부분의 일반적인 Api에서 사용 되는 것과 동일한 방식으로 사용 됩니다. 이 포트는 더 높은 안정성을 보장 하기 위해 Service Fabric 리소스 공급자의 확장 된 관리 작업에 권장 됩니다. 

* **클러스터**. 노드 간 통신에 사용됩니다. 차단되어서는 안 됩니다.

* **임시**. Service Fabric에서는 이러한 포트 중 일부를 애플리케이션 포트로 사용하고, 나머지 포트는 OS에서 사용할 수 있습니다. 또한 이 범위가 OS에 있는 기존 범위에 매핑되므로 어떤 목적이든 여기에 있는 샘플에 지정된 범위를 사용할 수 있습니다. 시작 포트와 끝 포트 간의 차이가 255 이상인지 확인합니다. 이 범위가 OS와 공유되므로 이 차이가 너무 낮으면 충돌이 발생할 수 있습니다. 구성된 동적 포트 범위를 보려면 *netsh int ipv4에서 동적 포트 tcp 보기* 를 실행합니다. 이러한 포트는 Linux 클러스터에 필요하지 않습니다.

* **애플리케이션**. 애플리케이션 포트 범위는 애플리케이션의 엔드포인트 요구 사항을 충족할 수 있을 만큼 충분히 커야 합니다. 이 범위는 컴퓨터의 동적 포트 범위, 즉 구성에 설정된 ephemeralPorts 범위에서 제외해야 합니다. Service Fabric에서는 새 포트가 필요할 때마다 이러한 포트를 사용하여 노드의 해당 포트에 대한 방화벽을 엽니다.

* **RDP**. 선택 사항으로 인터넷 또는 VirtualNetwork for jumpbox 시나리오에 RDP가 필요한 경우입니다. 

* **SSH**. 선택 사항으로 SSH가 인터넷 또는 VirtualNetwork for jumpbox 시나리오에 필요한 경우입니다.

* **사용자 지정 엔드포인트**. 인터넷 액세스 가능 엔드포인트를 사용하도록 설정하는 애플리케이션의 예입니다.

> [!NOTE]
> 인터넷을 원본으로 사용 하는 대부분의 규칙은 CIDR 블록으로 정의 된 알려진 네트워크로 제한 하는 것이 좋습니다.

### <a name="outbound"></a>아웃바운드

|우선 순위   |Name               |포트        |프로토콜  |원본             |대상       |작업        | 필수 
|---        |---                |---         |---       |---                |---               |---           |---
|4010       |리소스 공급자  |443         |TCP       |모두                |ServiceFabric     |허용         | 예
|4020       |이진 파일 다운로드  |443         |TCP       |모두                |AzureFrontDoor.FirstParty |Allow | 예


아웃바운드 보안 규칙에 대한 자세한 내용:

* **리소스 공급자**. ARM 배포 또는 기본 노드 유형 업그레이드와 같은 필수 작업과 같은 관리 작업을 수신 하기 위해 upgradeservice와 Service Fabric 리소스 공급자 간의 연결.

* **이진 파일을 다운로드** 합니다. 업그레이드 서비스는 download.microsoft.com 주소를 사용 하 여 이진 파일을 가져옵니다 .이는 설치, 다시 이미지 및 런타임 업그레이드에 필요 합니다. "내부 전용" 부하 분산 장치의 시나리오에서는 포트 443에 대 한 아웃 바운드 트래픽을 허용 하는 규칙을 사용 하 여 [추가 외부 부하 분산 장치](service-fabric-patterns-networking.md#internal-and-external-load-balancer) 를 추가 해야 합니다. 선택 사항으로 해당 포트는 성공적인 설정 후 차단할 수 있지만, 이러한 경우 업그레이드 패키지를 노드에 배포하거나 짧은 기간 동안 포트를 열어야 하며 나중에 수동으로 업그레이드해야 합니다.

[Nsg 흐름 로그](/network-watcher/network-watcher-nsg-flow-logging-overview.md) 와 [트래픽 분석](/network-watcher/traffic-analytics.md) 을 통해 Azure 방화벽을 사용 하 여 연결 문제를 추적 합니다. [NSG를 사용한 Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) ARM 템플릿은 시작하기 좋은 예제입니다. 

> [!NOTE]
> 기본 네트워크 보안 규칙은 노드 간 통신을 보장 하므로 덮어쓰지 않아야 합니다. [네트워크 보안 그룹-작동 방식](/virtual-network/network-security-group-how-it-works.md)입니다. 또 다른 예는 인증서 해지 목록 검사를 수행 하는 데 포트 80에 대 한 아웃 바운드 연결이 필요 합니다.

## <a name="application-networking"></a>애플리케이션 네트워킹

* Windows 컨테이너 워크로드를 실행하려는 경우 [오픈 네트워킹 모드](service-fabric-networking-modes.md#set-up-open-networking-mode)를 사용하면 서비스 간 통신을 더 쉽게 수행할 수 있습니다.

* [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 또는 [Service Fabric 역방향 프록시](service-fabric-reverseproxy.md) 등의 역방향 프록시를 사용하여 80, 443 등의 일반적인 애플리케이션 포트를 표시합니다.

* Azure 클라우드 스토리지에서 기본 계층을 끌어올 수 없는 에어 갭이 있는 컴퓨터에서 호스트되는 Windows 컨테이너의 경우 Docker 디먼의 [--allow-nondistributable-artifacts](/virtualization/windowscontainers/about/faq.md#how-do-i-make-my-container-images-available-on-air-gapped-machines) 플래그를 사용하여 외부 계층 동작을 재정의합니다.

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
