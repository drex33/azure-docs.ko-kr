---
title: 네트워크 기능 관리자 FAQ
titleSuffix: Azure Network Function Manager
description: 네트워크 기능 관리자에 대한 FAQ를 알아봅니다.
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 06/30/2021
ms.author: prmitt
ms.custom: references_regions
ms.openlocfilehash: 64994838cd173ff1d48dcfce9bd75d2a69aadf91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232217"
---
# <a name="azure-network-function-manager-faq-preview"></a>Azure 네트워크 기능 관리자 FAQ(미리 보기)

## <a name="faqs"></a>FAQ

### <a name="i-am-a-network-function-partner-and-want-to-onboard-to-network-function-manager-how-do-i-offer-my-network-function-with-nfm"></a>저는 네트워크 기능 파트너이고 네트워크 기능 관리자에 온보딩하고 싶습니다. NFM으로 네트워크 기능을 제공하려면 어떻게 해야 하나요?

우리의 목표는 고객에게 Azure Stack Edge에서 사용할 수 있는 네트워크 기능을 선택할 수 있는 풍부한 에코시스템을 제공하는 것입니다. 온보딩 요구 사항에 대해 논의하려면 **aznfmpartner@microsoft.com** 으로 이메일을 보내주세요.

### <a name="does-network-function-manager-preview-support-other-azure-edge-devices-in-addition-to-azure-stack-edge-pro-with-gpu"></a>네트워크 기능 관리자 미리 보기가 GPU가 있는 Azure Stack Edge Pro 외에 다른 Azure Edge 디바이스를 지원하나요?

NFM 미리 보기는 현재 일반 공급으로 제공되는, GPU가 있는 Azure Stack Edge Pro에서 사용할 수 있습니다. ASE Pro는 모바일 패킷 코어 및 SD-WAN 에지와 같은 특수 네트워크 기능을 실행하도록 엔지니어링된 HaaS(Hardware-as-a-Service)입니다. 디바이스에는 포트 5 및 6에서 네트워크 가속을 지원하는 6개의 물리적 포트가 장착되어 있습니다. GPU가 있는 Azure Stack Edge Pro 디바이스의 [네트워크 인터페이스 사양](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#network-interface-specifications)을 확인합니다. 네트워크 기능 파트너는 SR-IOV 및 DPDK 기능을 활용하여 네트워크 기능에 우수한 네트워크 성능을 제공할 수 있습니다.

### <a name="what-additional-capabilities-are-available-on-azure-stack-edge-pro-with-gpu-in-addition-to-running-network-functions"></a>네트워크 기능 실행 외에 GPU가 있는 Azure Stack Edge Pro에서 사용할 수 있는 추가 기능은 무엇인가요?

GPU가 있는 ASE(Azure Stack Edge) Pro와 Azure 네트워크 기능 관리자는 [Azure 프라이빗 MEC](../private-multi-access-edge-compute-mec/index.yml) 솔루션의 일부입니다. 이제 ASE 디바이스에서 개인 모바일 네트워크와 VM 또는 컨테이너 기반 에지 애플리케이션을 실행할 수 있습니다. 이를 통해 중요한 비즈니스 애플리케이션에 예측 가능한 SLA를 제공하는 혁신적인 솔루션을 구축할 수 있습니다. 또한 Azure Stack Edge Pro에는 에지에서 동영상 추론 및 기계 학습과 같은 시나리오를 활용할 수 있는 하나 또는 두 개의 [GPU](../databox-online/azure-stack-edge-gpu-technical-specifications-compliance.md#compute-acceleration-specifications)가 장착되어 있습니다.

### <a name="what-is-the-pricing-for-network-function-manager-preview"></a>네트워크 기능 관리자 미리 보기의 가격은 얼마인가요?

Azure 네트워크 기능 관리자 미리 보기는 Azure Stack Edge Pro 디바이스에서 추가 비용 없이 제공됩니다. 네트워크 기능 파트너는 NFM 서비스와 함께 네트워크 기능을 제공하는 데 별도의 요금이 부과될 수 있습니다. 가격 세부 정보는 네트워크 기능 파트너에게 문의합니다.

### <a name="if-my-azure-stack-edge-pro-device-is-in-a-disconnected-mode-or-partially-connected-mode-will-the-network-functions-already-deployed-stop-working"></a>내 Azure Stack Edge Pro 디바이스가 연결 해제 모드 또는 부분 연결 모드인 경우 이미 배포된 네트워크 기능의 작동이 중지되나요?

네트워크 기능 관리자 서비스는 네트워크 기능을 만들거나 삭제하고 디바이스에서 실행되는 네트워크 기능을 모니터링하고 문제를 해결하기 위한 관리 작업을 위해 ASE 디바이스에 대한 네트워크 연결이 필요합니다. 네트워크 기능이 ASE 디바이스에 배포되고 디바이스의 연결이 끊겼거나 기본 네트워크 기능이 부분적으로 연결된 경우 가상 머신은 중단 없이 계속 작동해야 합니다. 이러한 가상 머신에 배포된 네트워크 기능은 구성 관리 및 네트워크 기능 파트너의 추가 네트워크 연결 요구 사항에 따라 다른 요구 사항이 있을 수 있습니다. 네트워크 연결 요구 사항 및 작동 모드는 파트너에게 문의합니다.

### <a name="which-regions-are-supported-for-preview-will-you-add-support-for-additional-azure-regions"></a>어떤 지역이 미리 보기에 지원되나요? 추가 Azure 지역에 대한 지원을 추가하시겠어요?

미리 보기 중에 네트워크 기능 관리자는 다음 지역에서 사용할 수 있습니다.

[!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

GPU가 있는 Azure Stack Edge Pro는 여러 국가에서 원하는 네트워크 기능을 배포하고 실행할 수 있습니다. Azure Stack Edge Pro GPU 디바이스를 사용할 수 있는 모든 국가/지역 목록을 보려면 [Azure Stack Edge Pro GPU 가격 책정](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)으로 이동합니다. **Azure Stack Edge Pro** 탭에서  **가용성** 섹션을 확인합니다.

미리 보기 중에 규제 및 데이터 주권 요구 사항에 따라 Azure Stack Edge 디바이스 및 네트워크 기능 관리자 리소스를 등록할 수 있습니다. 네트워크 기능 관리자 리소스와 연결된 Azure 지역은 클라우드 서비스에서 물리적 디바이스로의 관리 작업을 안내하는 데 사용됩니다.

### <a name="when-i-delete-the-managed-application-for-my-network-function-running-on-azure-stack-edge-will-the-billing-for-network-functions-automatically-stop"></a>Azure Stack Edge에서 실행 중인 네트워크 기능에 대한 관리형 애플리케이션을 삭제하면 네트워크 기능에 대한 청구가 자동으로 중지되나요?

네트워크 기능 관리자를 사용하여 배포된 네트워크 기능에 대한 청구 주기에 대해 네트워크 기능 파트너에게 확인합니다. 각 파트너는 네트워크 기능 제품에 대해 서로 다른 청구 정책을 갖습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [개요](overview.md)를 참조하세요.
