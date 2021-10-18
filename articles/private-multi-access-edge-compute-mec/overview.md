---
title: Azure 프라이빗 다중 액세스 에지 컴퓨팅
description: 클라우드에서 관리되는 Microsoft 컴퓨팅, 네트워킹 및 애플리케이션 서비스의 포트폴리오를 함께 제공하는 Azure 프라이빗 MEC(다중 액세스 에지 컴퓨팅) 솔루션에 대해 알아봅니다.
author: niravi-msft
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: kumud
ms.openlocfilehash: 1875a2cfaa89a6eeeb45ae2efe84959ff6771268
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707327"
---
# <a name="what-is-azure-private-multi-access-edge-compute"></a>Azure 프라이빗 다중 액세스 에지 컴퓨팅이란?

Azure 프라이빗 MEC(다중 액세스 에지 컴퓨팅)는 클라우드에서 관리되는 Microsoft 컴퓨팅, 네트워킹 및 애플리케이션 서비스의 포트폴리오를 함께 제공하는 솔루션입니다. 이 솔루션을 사용하면 운영자 및 시스템 통합자가 새로운 엔터프라이즈 디지털 혁신 흐름을 위해 엔터프라이즈 에지에서 고성능, 짧은 대기 시간 연결 및 IoT 애플리케이션을 제공할 수 있습니다. 

Azure 프라이빗 MEC는 프라이빗 에지 영역이 진화된 형태로, 가능한 범위를 단일 플랫폼 및 서비스에서 여러 플랫폼 및 기능을 활용하는 솔루션으로 확장합니다. 이러한 기능에는 에지 서비스 및 애플리케이션, 에지 네트워크 함수, 에지 컴퓨팅 옵션, 에지 라디오 및 디바이스가 포함됩니다. 이러한 기능들은 최종 디바이스에 더 가깝게 데이터를 처리하여 비디오 분석, 실시간 로봇 공학 및 글로벌 규모의 혼합 IoT 사용 사례와 같이 대기 시간 및 처리량에 민감한 사용자 시나리오를 개선하는 데 도움이 됩니다. 고객 및 파트너는 Azure 서비스 및 에코시스템 기술 구성 요소의 완전체를 활용하여 빠르고 간편하게 솔루션을 빌드, 배포 및 관리할 수 있습니다. 

## <a name="benefits-to-customers-and-partners"></a>고객 및 파트너에 대한 혜택
- 기업 고객:
    - 증가하는 Azure 서비스 포트폴리오, ISV 및 개발자 에코시스템에 액세스
    - 신뢰할 수 있는 업계 맞춤형 솔루션 선택 사양
    - 1U 공간에서 시작되는 완전 통합형 기술 스택

- 원격 통신 및 시스템 통합업체 파트너:
    - 고객을 위한 간편한 5G 및 Edge 조달, 배포, 관리 및 수익화
    - 제품, 클라우드 서비스 및 애플리케이션의 큐레이팅된 에코시스템 선택 사양
    - Azure 개발자 에코시스템 액세스

- 애플리케이션 ISV:
    - 대기 시간이 매우 짧은 애플리케이션을 개발하기 위한 전기능 플랫폼 
    - 설정된 도구 및 리소스를 갖춘 일관된 개발자 환경
    - Microsoft Marketplace를 통해 배포 스케일링

## <a name="microsoft-capabilities"></a>Microsoft 기능
Azure 프라이빗 MEC에는 Microsoft의 여러 기능이 포함되어 있습니다. 여기에는 네트워크 기능 제품, 관리 서비스, 하드웨어 인프라 및 서비스의 조합이 포함됩니다. 

### <a name="azure-network-functions-offered-via-marketplace"></a>Marketplace를 통해 제공되는 Azure 네트워크 기능

**Metaswitch Fusion Core**: Fusion Core는 4G 또는 5G 라디오를 통해 데이터 네트워크에 연결된 IoT 디바이스 간의 연결에 필요한 모든 네트워크 기능을 지원하는 완전히 컨테이너화된 5G Core 솔루션입니다. 이 솔루션은 다음과 같은 일부 주요 이점을 제공합니다.
 - Azure Marketplace Portal을 통해 Azure Stack Edge에 쉽게 배포 및 프로비저닝
 - 4개의 물리적 코어로 구성된 매우 낮은 컴퓨팅 공간에서 25Gbps 처리량의 고성능 구현
 - 4G 및 5G 독립 실행형 액세스 유형 모두 지원
 - 엔터프라이즈 중심 서비스 보증 및 KPI 대시보드를 위한 기본 제공 도구 
 
Fusion Core를 사용하여 ISV는 라이브 비디오 분석과 같은 동일한 IoT Edge 애플리케이션용 Azure Stack Edge 노드에 애플리케이션을 배포할 수 있습니다. 

**확정된 프라이빗 네트워크 서비스**: 확정된 프라이빗 네트워크 서비스는 엔터프라이즈에 4G 및 5G 관리형 서비스 제품을 제공하려는 모바일 네트워크 운영자 및 관리형 서비스 공급자를 위해 관리형 프라이빗 네트워크 서비스를 제공하는 Azure Marketplace입니다. APNS를 통해 운영자는 기업에게 통신 사업자 수준의 프라이빗 모바일 네트워크를 제공하여 대기 시간이 낮고 대역폭이 높은 엔드투엔드 보안이 필요한 중요 비즈니스용 애플리케이션을 실행하고 운영할 수 있도록 합니다. 프라이빗 및 퍼블릭 운영자 네트워크 간에 완전한 이동성을 제공하는 통합 모바일 네트워크 운영자입니다. 자동화 및 간소화된 운영을 통해 APNS는 수천 개의 엔터프라이즈 에지 위치에서 확장성을 제공하고 Azure를 사용하여 프라이빗 네트워크 및 엔터프라이즈 애플리케이션에서 향상된 보안을 제공합니다. 또한 네트워크의 에지, 모든 클라우드 또는 하이브리드 모드(클라우드에는 컨트롤 플레인이 있고 엔터프라이즈 에지에는 사용자 평면이 있음)에서 전체 모바일 코어를 유연하게 배포할 수 있도록 합니다. 

### <a name="azure-management-services"></a>Azure 관리 서비스

**Azure NFM(네트워크 기능 관리자)** : Azure NFM을 사용하면 일관된 Azure 도구 및 인터페이스를 사용하여 에지에 네트워크 기능을 배포할 수 있습니다. 이 서비스를 사용하여 Azure Stack Edge에 패킷 코어 및 SD-WAN 네트워크 기능을 배포합니다. 자세한 내용은 [Azure 네트워크 기능 관리자](../network-function-manager/overview.md)를 참조하세요.

**Arc 지원 Kubernetes**: Azure Arc 지원 Kubernetes를 사용하면 Azure 내부 또는 외부에 있는 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. 정책 기반 배포를 통해 대규모로 모니터링 및 관리하고 일관된 보안 구성을 대규모로 적용할 수 있습니다. Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. 자세한 내용은 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)를 참조하세요.

### <a name="azure-stack-hardware-and-services"></a>Azure Stack 하드웨어 및 서비스
**Azure Stack Edge**: Azure Stack Edge는 컴퓨팅, 스토리지 및 인텔리전스를 데이터가 만들어지는 에지로 가져오는 디바이스 포트폴리오를 제공합니다. 디바이스는 1-2 NVIDIA T4 GPU가 함께 제공되는 1U 랙 탑재 가능 어플라이언스입니다. Azure IoT Edge를 사용하면 IoT Hub의 컨테이너를 배포 및 관리하고 Azure Stack Edge의 Azure IoT 솔루션과 통합할 수 있습니다. Azure Stack Edge Pro SKU는 에지에서 네트워크 함수를 실행하도록 인증되었습니다. 자세한 내용은 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)를 참조하세요.

**Azure Stack HCI**: Azure Stack HCI는 최신 보안, 성능 및 기능 업데이트를 제공하는 Azure 서비스로 제공되는 새로운 HCI(하이퍼 컨버지드 인프라) 운영 체제입니다. 데이터 센터 또는 에지에서 기존 도구 및 프로세스를 사용하여 Windows 및 Linux VM(가상 머신)을 배포하고 실행합니다. Azure Backup, Azure Monitor 및 Azure Security Center를 사용하여 데이터 센터를 클라우드로 확장합니다. 자세한 내용은 [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/)를 참조하세요.

### <a name="application-services"></a>애플리케이션 서비스

**Azure IoT Edge 런타임**: Azure IoT Edge 런타임을 사용하면 클라우드 네이티브 워크로드와 동일한 도구 및 보안 상태를 사용하여 여러 에지 컴퓨팅 어플라이언스 간에 클라우드 워크로드를 관리하고 배포할 수 있습니다. 자세한 내용은 [Azure IoT Edge 런타임](/windows/ai/windows-ml-container/iot-edge-runtime)을 참조하세요.

**Azure IoT Hub** Azure IoT Edge 런타임: Azure IoT Edge 런타임을 사용하면 클라우드 네이티브 워크로드와 동일한 도구 및 보안 상태를 사용하여 여러 에지 컴퓨팅 어플라이언스 간에 클라우드 워크로드를 관리하고 배포할 수 있습니다. 자세한 내용은 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)를 참조하세요.

**Azure IoT Central**: Azure IoT Central은 예측 가능한 가격 책정 모델 및 글로벌 규모가 기본 제공되는 서비스로, 디바이스 관리 및 데이터 수집을 가능하게 하는 관리형 애플리케이션 플랫폼입니다. 자세한 내용은 [Azure IoT Central](https://azure.microsoft.com/services/iot-central/)을 참조하세요.

**Azure Digital Twins**: Azure Digital Twins를 사용하면 공간 관계, 사용 패턴 및 디바이스 그룹을 물리적 자산 또는 환경의 디지털 복제본으로 바꾸는 기타 비즈니스 컨텍스트를 고려하여 디바이스 센서를 비즈니스 컨텍스트에 따라 모델링할 수 있습니다. 자세한 내용은 [Azure Digital Twins](https://azure.microsoft.com/services/digital-twins/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Metaswitch Fusion Core](metaswitch-fusion-core-overview.md)에 대한 자세한 정보
- [확정된 프라이빗 네트워크 서비스](affirmed-private-network-service-overview.md)에 대한 자세한 정보
