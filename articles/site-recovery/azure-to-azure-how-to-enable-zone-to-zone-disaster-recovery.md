---
title: Azure 가상 머신에 대한 영역 간 재해 복구 사용
description: 이 문서에서는 Azure 가상 머신에 대해 영역 간 재해 복구를 사용하는 시기와 방법을 설명합니다.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 263548b8f19c7cad8a27ec605bf9191478a65408
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131867212"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>가용성 영역 간에 Azure VM 재해 복구 사용

이 문서에서는 동일한 Azure 지역 내의 한 가용성 영역에서 다른 가용성 영역으로 Azure 가상 머신을 복제, 장애 조치(failover) 및 장애 복구(failback)하는 방법을 설명합니다.

>[!NOTE]
>
>- 영역 및 영역 재해 복구에 대 한 지원은 현재 다음 지역으로 제한 됩니다. 동남 아시아, 동아시아, 일본 동부, 대한민국 중부, 오스트레일리아 동부, 인도 중부, 영국 남부, 유럽 서부, 서유럽, 노르웨이 동부, 프랑스 중부, 스웨덴 중부 (관리 되는 액세스), 캐나다 중부, 미국 중부, 미국 중 북부, 미국 동부, 미국 동부 2, 미국 서 부 2, 브라질 남부 및 미국 서 부 3.  
>- Site Recovery는 고객이 영역 간 재해 복구를 사용할 때 배포된 지역 외부로 고객 데이터를 이동하거나 저장하지 않습니다. 고객이 원하는 경우 다른 지역에서 Recovery Services 자격 증명 모음을 선택할 수 있습니다. Recovery Services 자격 증명 모음에는 메타데이터가 포함되지만, 실제 고객 데이터는 없습니다.

Site Recovery 서비스는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 비즈니스 연속성 및 재해 복구 전략에 기여합니다. 지역적으로 가동이 중단된 경우 애플리케이션을 계속 가동하고 실행하기 위해 권장되는 재해 복구 옵션입니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역에는 하나 이상의 데이터 센터가 있습니다. 

VM을 다른 지역의 가용성 영역으로 이동하려면 [이 문서를 검토](../resource-mover/move-region-availability-zone.md)하세요.

## <a name="using-availability-zones-for-disaster-recovery"></a>재해 복구를 위해 가용성 영역 사용 

일반적으로 가용성 영역은 고가용성 구성에서 VM을 배포하는 데 사용됩니다. 자연 재해 발생 시 재해 복구 솔루션으로 사용하기에는 너무 가깝습니다.

그러나 일부 시나리오에서는 가용성 영역을 재해 복구에 활용할 수 있습니다.

- 온-프레미스에서 애플리케이션을 호스팅하는 동안 메트로 재해 복구 전략을 실행했던 많은 고객은 애플리케이션을 Azure로 마이그레이션한 후 이 전략을 모방하는 경우가 있습니다. 이러한 고객은 대규모 물리적 재해의 경우 메트로 재해 복구 전략이 작동하지 않을 수 있다는 사실을 인정하고 이러한 위험을 감수합니다. 이러한 고객의 경우 영역 간 재해 복구를 재해 복구 옵션으로 사용할 수 있습니다.

- 다른 많은 고객은 복잡한 네트워킹 인프라를 보유하고 있으며 관련 비용 및 복잡성으로 인해 보조 지역에서 다시 생성하기를 원하지 않습니다. 영역 간 재해 복구는 가용성 영역 전체에서 중복 네트워킹 개념을 활용하여 구성을 훨씬 간소화함으로써 복잡성을 줄입니다. 이러한 고객은 단순성을 선호하며 재해 복구를 위해 가용성 영역을 사용할 수도 있습니다.

- 동일한 법적 관할권 내에 쌍을 이루는 지역이 없는 일부 지역(예: 동남아시아)의 경우, 영역 간 재해 복구는 애플리케이션과 데이터가 국경을 넘어 이동하지 않기 때문에 법적 준수를 보장하는 데 도움이 되므로 사실상 재해 복구 솔루션으로 사용할 수 있습니다. 

- 영역 간 재해 복구는 Azure 간 재해 복구와 비교할 때 더 짧은 거리 간에 데이터가 복제되므로 대기 시간이 줄어들고 결과적으로 RPO를 낮출 수 있습니다.

이는 강력한 장점이지만 지역 전체에서 자연 재해가 발생할 경우 영역 간 재해 복구로는 복구 요구 사항을 충족하지 못할 가능성이 있습니다.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>영역 간 재해 복구를 위한 네트워킹

위에서 언급했듯이 영역 간 재해 복구는 가용성 영역 전체에서 중복 네트워킹 개념을 활용하여 구성을 훨씬 간소화함으로써 복잡성을 줄입니다. 영역 간 재해 복구 시나리오에서 네트워킹 구성 요소의 동작은 다음과 같습니다. 

- 가상 네트워크: 실제 장애 조치(failover)를 위해 원본 네트워크와 동일한 가상 네트워크를 사용할 수 있습니다. 테스트 장애 조치(failover)를 위해 원본 가상 네트워크에 다른 가상 네트워크를 사용합니다.

- 서브넷: 동일한 서브넷으로의 장애 조치(failover)가 지원됩니다.

- 개인 IP 주소: 고정 IP 주소를 사용하는 경우 이러한 방식으로 구성하면 대상 영역에서 동일한 IP를 사용할 수 있습니다.

- 가속화된 네트워킹: Azure 간 재해 복구와 유사하게 VM SKU에서 지원하는 경우 가속화된 네트워킹을 사용할 수 있습니다.

- 공용 IP 주소: 동일한 지역에서 이전에 만든 표준 공용 IP 주소를 대상 VM에 연결할 수 있습니다. 기본 공용 IP 주소는 가용성 영역 관련 시나리오를 지원하지 않습니다.

- 부하 분산 장치: 표준 부하 분산 장치는 지역 리소스이므로 대상 VM을 동일한 부하 분산 장치의 백 엔드 풀에 연결할 수 있습니다. 새 부하 분산 장치는 필요하지 않습니다.

- 네트워크 보안 그룹: 원본 VM에 적용된 것과 동일한 네트워크 보안 그룹을 사용할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

VM에 대한 영역 간 재해 복구를 배포하기 전에 VM에서 사용하도록 설정된 다른 기능이 영역 간 재해 복구와 상호 운용 가능한지 확인하는 것이 중요합니다.

|기능  | 지원 정책  |
|---------|---------|
|클래식 VM   |     지원되지 않음    |
|ARM VM    |    지원됨    |
|Azure Disk Encryption v1(이중 패스, Azure Active Directory(Azure AD) 사용)     |     지원됨   |
|Azure Disk Encryption v2(단일 패스, Azure AD 사용 안 함)    |    지원됨    |
|관리되지 않는 디스크    |    지원되지 않음    |
|관리 디스크    |    지원됨    |
|고객 관리형 키    |    지원됨    |
|근접 배치 그룹    |    지원됨    |
|백업 상호 운용성    |    파일 수준 백업 및 복원이 지원됩니다. 디스크 및 VM 수준 백업 및 복원이 지원되지 않습니다.    |
|핫 추가/제거    |    영역 간 복제를 사용하도록 설정한 후 디스크를 추가할 수 있습니다. 영역 간 복제를 사용하도록 설정한 후에는 디스크 제거가 지원되지 않습니다.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Site Recovery의 영역 간 재해 복구 설정

### <a name="log-in"></a>로그인

Azure 포털에 로그인합니다.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>영역 Azure 가상 머신에 대한 복제 사용

1. Azure Portal 메뉴에서 가상 머신을 선택하거나 모든 페이지에서가상 머신을 검색하여 선택합니다. 복제할 VM을 선택합니다. 영역 간 재해 복구의 경우 이 VM이 이미 가용성 영역에 있어야 합니다.

2. 작업에서 재해 복구를 선택합니다.

3. 아래와 같이 기본 탭에서 '가용성 영역 간 재해 복구?'에 대해 '예'를 선택합니다.

    ![기본 설정 페이지](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. 모든 기본값을 수락하는 경우 '검토 + 복제 시작'을 클릭한 다음 '복제 시작'을 클릭합니다.

5. 복제 설정을 변경하려면 '다음: 고급 설정'을 클릭합니다.

6. 언제든지 기본값 이외의 값으로 설정을 변경할 수 있습니다. Azure 간 재해 복구 사용자의 경우 이 페이지가 익숙해 보일 수 있습니다. 이 블레이드에 제공된 옵션에 대한 자세한 내용은 [여기](./azure-to-azure-tutorial-enable-replication.md)에서 확인할 수 있습니다.

    ![고급 설정 페이지](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. '다음: 검토 + 복제 시작'을 클릭한 다음 '복제 시작'을 클릭합니다.

## <a name="faqs"></a>FAQ

**1. 영역 간 재해 복구의 가격은 어떻게 책정되나요?**
영역 간 재해 복구의 가격은 Azure 간 재해 복구의 가격과 동일합니다. 자세한 내용은 가격 책정 페이지인 [여기](https://azure.microsoft.com/pricing/details/site-recovery/) 및 [여기](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)에서 확인할 수 있습니다. 영역 간 재해 복구에서 표시되는 송신 요금은 지역 간 재해 복구보다 낮습니다.

**2. RTO 및 RPO에 대한 SLA는 무엇인가요?**
RTO SLA는 전체 Site Recovery의 SLA와 동일합니다. 최대 2시간의 RTO를 보장합니다. RPO에 대해 정의된 SLA는 없습니다.

**3. 보조 영역에서의 용량이 보장되나요?**
Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치(failover)를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 영역에 배포되도록 하는 데도 도움을 줍니다.

**4. 지원되는 운영 체제는 무엇인가요?**
영역 간 재해 복구는 Azure 간 재해 복구와 동일한 운영 체제를 지원합니다. 지원 매트릭스는 [여기](./azure-to-azure-support-matrix.md)를 참조하세요.

**5. 원본 및 대상 리소스 그룹이 동일할 수 있나요?**
아니요, 다른 리소스 그룹으로 장애 조치(failover)해야 합니다.

## <a name="next-steps"></a>다음 단계

재해 복구 훈련, 장애 조치(failover), 다시 보호 및 장애 복구(failback)를 실행하기 위해 따라야 하는 단계는 Azure 간 재해 복구 시나리오의 단계와 동일합니다.

재해 복구 훈련을 수행하려면 [여기](./azure-to-azure-tutorial-dr-drill.md)에 설명된 단계를 따르세요.

보조 영역에서 장애 조치(failover)를 수행하고 VM을 다시 보호하려면 [여기](./azure-to-azure-tutorial-failover-failback.md)에 설명된 단계를 따르세요.

주 영역으로 장애 복구(failback)하려면 [여기](./azure-to-azure-tutorial-failback.md)에 설명된 단계를 따르세요.
