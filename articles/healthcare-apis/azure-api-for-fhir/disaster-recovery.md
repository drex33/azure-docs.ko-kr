---
title: Azure API for FHIR 재해 복구
description: 이 문서에서는 Azure API for FHIR 재해 복구 기능을 사용하도록 설정하는 방법을 알아봅니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/03/2021
ms.author: zxue
ms.openlocfilehash: c060581bb25e8708893ac9c3e48e694a0b86c50d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436273"
---
# <a name="disaster-recovery-for-azure-api-for-fhir"></a>Azure API for FHIR 재해 복구

Azure API for FHIR® FHIR(전자 의료 기록 교환®)을 기반으로 하는 완전 관리형 서비스입니다. 비즈니스 및 규정 준수 요구 사항을 충족하기 위해 Azure API for FHIR DR(재해 복구) 기능을 사용할 수 있습니다.

DR 기능은 15분의 RPO(복구 지점 목표)와 60분의 RTO(복구 시간 목표)를 제공합니다.

 ## <a name="how-to-enable-dr"></a>DR을 사용하도록 설정하는 방법 
  
DR 기능을 사용하도록 설정하려면 일회성 지원 티켓을 만듭니다. Azure 쌍을 이루는 지역 또는 Azure API for FHIR 지원되는 다른 지역을 선택할 수 있습니다. Microsoft 지원 팀은 지원 우선 순위에 따라 DR 기능을 사용하도록 설정합니다.

## <a name="how-the-dr-process-works"></a>DR 프로세스의 작동 방식

DR 프로세스에는 다음 단계가 포함됩니다. 
* 데이터 복제
* 자동 장애 조치(automatic failover)
* 영향을 받는 지역 복구
* 수동 장애 조치(failback)

### <a name="data-replication-in-the-secondary-region"></a>보조 지역의 데이터 복제

기본적으로 Azure API for FHIR 백업 및 복원을 통해 데이터 보호를 제공합니다. 재해 복구 기능을 사용하도록 설정하면 데이터 복제가 시작됩니다. 데이터 복제본은 보조 Azure 지역에서 자동으로 만들어지고 동기화됩니다. 초기 데이터 복제는 데이터 양에 따라 몇 분에서 몇 시간 또는 더 오래 걸릴 수 있습니다. 보조 데이터 복제본은 주 데이터의 복제입니다. 서비스를 복구하는 데 직접 사용되며 복구 프로세스 속도를 높여줍니다.

처리량 RU/s는 주 지역과 보조 지역에서 동일한 값을 가져야 합니다.

[![Azure Traffic Manager. ](media/disaster-recovery/azure-traffic-manager.png) ](media/disaster-recovery/azure-traffic-manager.png#lightbox)

### <a name="automatic-failover"></a>자동 장애 조치(automatic failover)

주 지역이 중단되는 동안 Azure API for FHIR 자동으로 보조 지역으로 장애 Azure API for FHIR 동일한 서비스 엔드포인트가 사용됩니다. 서비스는 1시간 이내에 다시 시작될 것으로 예상되며 잠재적인 데이터 손실은 최대 15분 분량의 데이터입니다. 다른 구성 변경이 필요할 수 있습니다. 자세한 내용은 [DR의 구성 변경 내용을](#configuration-changes-in-dr)참조하세요.

[![재해 복구에서 장애 조치(failover). ](media/disaster-recovery/failover-in-disaster-recovery.png) ](media/disaster-recovery/failover-in-disaster-recovery.png#lightbox)

### <a name="affected-region-recovery"></a>영향을 받는 지역 복구

영향을 받는 지역이 복구되면 보조 지역으로 자동으로 사용할 수 있으며 데이터 복제가 다시 시작됩니다. 데이터 복구 프로세스를 시작하거나 장애 복구 단계가 완료될 때까지 기다릴 수 있습니다.

[![재해 복구의 복제. ](media/disaster-recovery/replication-in-disaster-recovery.png) ](media/disaster-recovery/replication-in-disaster-recovery.png#lightbox)

컴퓨팅이 복구된 지역으로 장애 복구되고 데이터가 복구되지 않은 경우 잠재적인 네트워크 대기 시간이 있을 수 있습니다. 주된 이유는 컴퓨팅과 데이터가 서로 다른 두 지역에 있기 때문입니다. 수동 트리거를 통해 데이터가 복구된 지역으로 장애 복구되는 즉시 네트워크 대기 시간이 자동으로 사라집니다.

[![네트워크 대기 시간. ](media/disaster-recovery/network-latency.png) ](media/disaster-recovery/network-latency.png#lightbox)


### <a name="manual-failback"></a>수동 장애 조치(failback)

컴퓨팅이 복구된 지역으로 자동으로 장애 복구됩니다. 데이터는 스크립트를 사용하여 Microsoft 지원 팀에 의해 수동으로 복구된 지역으로 다시 전환됩니다. 

[![재해 복구에서 장애 복구(failback). ](media/disaster-recovery/failback-in-disaster-recovery.png) ](media/disaster-recovery/failback-in-disaster-recovery.png#lightbox)

## <a name="configuration-changes-in-dr"></a>DR의 구성 변경 내용

Private Link, CMK(고객 관리형 키), IoMT FHIR 커넥터(의료 사물 인터넷) 및 $export 사용하는 경우 다른 구성 변경이 필요할 수 있습니다.

### <a name="private-link"></a>프라이빗 링크

Azure API for FHIR 프로비전되기 전이나 후에 프라이빗 링크 기능을 사용하도록 설정할 수 있습니다. DR 기능을 사용하도록 설정하기 전이나 후에 프라이빗 링크를 프로비전할 수도 있습니다. DR에 대한 Private Link 구성할 준비가 되면 아래 목록을 참조하세요.

* 주 지역에서 Azure Private Link 구성합니다. 보조 지역에서는 이 단계가 필요하지 않습니다. 자세한 내용은 [프라이빗 링크 구성을 참조하세요.](/azure/healthcare-apis/fhir/configure-private-link)

* 주 지역에 하나의 Azure VNet을 만들고 보조 지역에 다른 VNet을 만듭니다. 자세한 내용은 [Azure Portal 사용하여 가상 네트워크 만들기를 참조하세요.](../../virtual-network/quick-create-portal.md)

* 주 지역에서 VNet은 보조 지역 VNet에 대한 VNet 피어링을 만듭니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

* 기본 설정을 사용하거나 필요에 따라 구성을 조정할 수 있습니다. 중요한 점은 트래픽이 두 가상 네트워크 간에 흐를 수 있다는 것입니다.

* 프라이빗 DNS가 설정되면 보조 지역의 VNet을 수동으로 "가상 네트워크 링크"로 설정해야 합니다. 기본 VNet은 Private Link 엔드포인트 만들기 흐름의 일부로 이미 추가되어 있어야 합니다. 자세한 내용은 [가상 네트워크 링크를 참조하세요.](../../dns/private-dns-virtual-network-links.md)

* 필요에 따라 주 지역 VNet에 하나의 VM을 설정하고, 다른 하나는 보조 지역 VNet에 설정합니다. 두 VM에서 Azure API for FHIR 액세스할 수 있습니다.

프라이빗 링크 기능은 지역 가동 중단 시와 장애 조치(failback)가 완료된 후에도 계속 작동해야 합니다. 자세한 내용은 [프라이빗 링크 구성을](/azure/healthcare-apis/fhir/configure-private-link)참조하세요.

> [!NOTE]
> 가상 네트워크 및 VNet 피어링을 구성해도 데이터 복제에는 영향을 주지 않습니다.

### <a name="cmk"></a>CMK

구독에서 관리되는 키를 호스트하는 키 자격 증명 모음에 액세스할 수 있는 경우 Azure API for FHIR 대한 액세스가 유지됩니다. Key Vault 연결을 다시 설정하는 데 최대 20분이 걸릴 수 있기 때문에 일시적인 가동 중지 시간이 발생할 수 있습니다. 자세한 내용은 [Azure Key Vault 가용성 및 중복성](../../key-vault/general/disaster-recovery-guidance.md)을 참조하세요.  

### <a name="export"></a>$export

내보내기 작업은 작업 상태에 대한 업데이트 없이 10분 후에 다른 지역에서 선택됩니다. 지역 가동 중단 시 스토리지 계정을 복구하기 위한 Azure Storage에 대한 지침을 따르세요. 자세한 내용은 [재해 복구 및 저장소 계정 장애 조치(failover)](../../storage/common/storage-disaster-recovery-guidance.md)를 참조하세요. 

Azure API for FHIR 시스템 ID에 동일한 권한을 부여해야 합니다. 또한 스토리지 계정이 선택한 네트워크로 구성된 경우 [FHIR 데이터를 내보내는 방법을 참조하세요.](/azure/healthcare-apis/fhir/export-data)

### <a name="iomt-fhir-connector"></a>IoMT FHIR 커넥터

실패한 지역이 복원될 때까지 기존 연결이 작동하지 않습니다. 장애 조치(failover)가 완료되고 FHIR 서버에 액세스할 수 있게 되면 새 연결을 만들 수 있습니다. 이 새 연결은 장애 조치(failback)가 발생할 때 계속 작동합니다.

> [!NOTE]
> IoMT 커넥터는 미리 보기 기능이며 재해 복구를 지원하지 않습니다. 

## <a name="how-to-test-dr"></a>DR을 테스트하는 방법

필수는 아니지만 비프로덕션 환경에서 DR 기능을 테스트할 수 있습니다. DR 테스트의 경우 데이터만 포함되고 컴퓨팅은 포함되지 않습니다. 

DR 테스트에 대해 다음 단계를 고려합니다.

* 테스트 데이터를 통해 테스트 환경을 준비합니다. 적은 양의 데이터가 있는 서비스 인스턴스를 사용하여 데이터를 복제하는 시간을 줄이는 것이 좋습니다.
 
* 지원 티켓을 만들고 테스트 환경의 Azure API for FHIR 대한 Azure 구독 및 서비스 이름을 제공합니다.

* DR 테스트와 마찬가지로 테스트 계획을 수립합니다.
 
* Microsoft 지원 팀은 DR 기능을 사용하도록 설정하고 장애 조치(failover)가 수행된 것을 확인합니다.

* DR 테스트를 수행하고 데이터 손실 및 네트워크 대기 시간 문제를 포함해야 하는 테스트 결과를 기록합니다. 

* 장애 조치(failback)의 경우 장애 조치(failback) 단계를 완료하도록 Microsoft 지원 팀에 알립니다.
 
* (선택 사항) Microsoft 지원 팀과 피드백을 공유합니다.


> [!NOTE]
> DR 테스트는 테스트 중에 테스트 환경의 비용을 두 배로 낮출 것입니다. DR 테스트가 완료되고 DR 기능이 비활성화된 후에는 추가 비용이 발생하지 않습니다.

## <a name="cost-of-disaster-recovery"></a>재해 복구 비용

보조 지역의 환경에서 실행되는 컴퓨팅 및 데이터 복제본의 데이터로 인해 재해 복구 기능에 추가 비용이 발생합니다. 자세한 가격 책정 정보는 [Azure API for FHIR 가격 책정]( https://azure.microsoft.com/pricing/details/azure-api-for-fhir) 웹 페이지를 참조하세요.

> [!NOTE]
> DR 제품에는 Azure API for FHIR , 1.0에 [대한 SLA가](https://azure.microsoft.com/support/legal/sla/azure-api-for-fhir/v1_0/)적용됩니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR DR의 작동 방식과 이를 사용하도록 설정하는 방법을 배웠습니다. Azure API for FHIR 지원되는 다른 기능에 대해 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[FHIR 지원 기능](fhir-features-supported.md)