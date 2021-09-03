---
title: Azure Stack Edge 디바이스의 Fusion Core 배포
description: Microsoft Azure 및 Metaswitch Networks의 클라우드 솔루션을 배포하여 네트워크의 미래 경쟁력을 확보하고 비용을 절감하며 새로운 비즈니스 모델과 수익원을 창출하는 방법을 알아봅니다.
author: djrmetaswitch
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: drichards
ms.openlocfilehash: 93e7511cbe8202f15dd055467c253044fa617e74
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286927"
---
# <a name="deploy-fusion-core-on-an-azure-stack-edge-device"></a>Azure Stack Edge 디바이스의 Fusion Core 배포

이 문서에서는 Azure Stack Edge 디바이스에 Fusion Core를 배포하는 프로세스에 대한 개략적인 개요를 제공합니다.

## <a name="collect-required-azure-resources"></a>필요한 Azure 리소스 수집

다음이 있어야 합니다.

- GPU가 있는 완전히 배포된 Azure Stack Edge Pro입니다. 디바이스의 포트는 다음과 같이 연결해야 합니다.

  - 포트 5 - LAN
  - 포트 6 - WAN
  - 관리 네트워크에 연결된 포트 1개. 1에서 4까지의 포트를 선택할 수 있습니다. [자습서: GPU가 있는 Azure Stack Edge Pro용 네트워크 구성](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)의 컴퓨팅 네트워크를 사용하도록 설정 단계에 설명된 대로 컴퓨팅에 대해 선택한 포트를 사용하도록 설정해야 합니다.
- 활성 구독 및 다음에 대한 액세스 권한이 있는 Azure 계정.

  - Azure 네트워크 기능 관리자 서비스입니다. 여기에는 리소스 공급자 네임스페이스 Microsoft.HybridNetwork가 있습니다. 자세한 내용은 [Azure 네트워크 기능 관리자란?](../network-function-manager/overview.md)을 참조하세요.
  - Fusion Core - 5G 패킷 코어 관리형 애플리케이션입니다. [https://azuremarketplace.microsoft.com/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview)를 방문하고 **연락처** 단추를 사용하여 액세스 권한을 요청해야 합니다. 당사 영업 담당자가 Fusion Core를 평가하거나 구매하는 방법에 대한 정보를 제공합니다. 이러한 옵션 중 하나를 선택하면 관리형 애플리케이션에 대한 액세스 권한을 제공합니다.
  - 구독 범위의 기본 제공 **소유자** 역할. 조직에서 이것이 불가능한 경우 Metaswitch 지원 담당자에게 문의합니다.
- Azure Stack Edge 디바이스를 나타내는 구성된 **Azure 네트워크 기능 관리자 - 디바이스** 개체입니다.

## <a name="deploy-fusion-core"></a>Fusion Core 배포

다음 다이어그램은 만드는 개체와 설치 후 지속적인 관리 방법을 포함하여 Fusion Core를 배포하는 프로세스를 보여 줍니다.

:::image type="content" source="./media/deploy-metaswitch-solution/fusion-core-on-azure-stack-edge-deployment-process.png" alt-text="Fusion Core 배포 프로세스":::  

Azure Marketplace에서 ASE용 Fusion Core를 선택한 후 Azure 구독에서 선택한 리소스 그룹에 Fusion Core 관리형 애플리케이션의 명명된 인스턴스를 배포합니다.

Fusion Core 관리형 애플리케이션은 ASE(Azure Stack Edge) 디바이스에 설치할 패킷 코어 네트워크 기능 및 릴레이 리소스를 포함하는 관리되는 리소스 그룹을 만듭니다.

- 패킷 코어 네트워크 기능 리소스는 Fusion Core 기본 VM 버전 및 해당 구성 매개 변수를 정의합니다. 기본적으로 Azure Portal에 숨겨져 있습니다.
- 릴레이 리소스에는 모니터링 인터페이스에 대한 설치 및 원격 액세스에 필요한 연결을 제공하는 여러 하이브리드 연결이 포함되어 있습니다.

그러면 Fusion Core가 다음과 같이 ASE 디바이스에 자동으로 배포됩니다.

1. ASE 디바이스는 Fusion Core 기본 VM을 다운로드하고 시작합니다.
1. Metaswitch Service Management 애플리케이션은 Fusion Core 기본 VM 내의 Kubernetes 클러스터에 Fusion Core를 설치하고 프로비저닝합니다.

Fusion Core 설치를 조정하는 것 외에도 Metaswitch Service Management 애플리케이션을 사용하면 Metaswitch 지원 담당자가 시스템 상태를 모니터링하고 진단에 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- Azure Stack Edge 디바이스에 Fusion Core를 배포하는 방법에 대한 단계별 지침을 보려면 [프라이빗 5G Edge Fusion Core 솔루션 가이드를 다운로드](https://go.microsoft.com/fwlink/?linkid=2165096)합니다.
- [프라이빗 5G Edge Fusion Core](metaswitch-fusion-core-overview.md)에 대해 자세히 알아봅니다.