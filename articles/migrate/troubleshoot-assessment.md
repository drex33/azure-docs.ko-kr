---
title: Azure Migrate에서 평가 문제 해결
description: Azure Migrate에서 평가에 대한 도움을 받습니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.openlocfilehash: eca62eccca6f53935f29a16bca0afc382896212c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810353"
---
# <a name="troubleshoot-assessment"></a>평가 문제 해결

이 문서는 [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)를 사용하여 평가 및 종속성 시각화 문제를 해결하는 데 도움이 됩니다.

## <a name="assessment-readiness-issues"></a>평가 준비 문제

다음 표에는 다음 평가 준비 문제를 해결하기 위한 도움말이 나와 있습니다.

**문제점** | **해결**
--- | ---
지원되지 않는 부팅 유형 | Azure는 공유 EFI 부팅 유형의 VM을 지원하지 않습니다. 마이그레이션을 실행하기 전에 부팅 유형을 BIOS로 변환하세요. <br/><br/>Azure Migrate 서버 마이그레이션을 사용하여 이러한 VM의 마이그레이션을 처리할 수 있습니다. 마이그레이션하는 동안 VM의 부팅 유형을 BIOS로 변환합니다.
조건부로 지원되는 Windows 운영 체제 | 이 운영 체제는 지원 날짜가 종료되었으며 [Azure에서 지원](/troubleshoot/azure/virtual-machines/server-software-support)을 받기 위해 사용자 지정 지원 계약이 필요합니다. Azure로 마이그레이션하기 전에 업그레이드하는 것이 좋습니다. Azure로 마이그레이션하기 위해 [Windows Server 2003을 실행하는 서버 준비](prepare-windows-server-2003-migration.md)에 대한 정보를 검토합니다.
지원되지 않는 Windows 운영 체제 | Azure는 [선택한 Windows OS 버전](/troubleshoot/azure/virtual-machines/server-software-support)만 지원합니다. Azure로 마이그레이션하기 전에 서버를 업그레이드하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure에서는 [선택한 LINUX OS 버전](../virtual-machines/linux/endorsed-distros.md)만 보증합니다. Azure로 마이그레이션하기 전에 서버를 업그레이드하는 것이 좋습니다. 자세한 내용은 [이 웹 사이트](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment)를 참조하세요.
보증되지 않는 Linux OS | 이 서버는 Azure에서 시작될 수 있지만, Azure는 운영 체제를 지원하지 않습니다. Azure로 마이그레이션하기 전에 [보증된 Linux 버전](../virtual-machines/linux/endorsed-distros.md)으로 업그레이드하는 것이 좋습니다.
알 수 없는 운영 체제 | vCenter Server에서는 VM의 운영 체제가 "기타"로 지정되었습니다. 이 동작 때문에 Azure Migrate에서 VM의 Azure 준비 상태를 확인하지 못합니다. 서버를 마이그레이션하기 전에 Azure에서 운영 체제가 [지원되는지](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) 확인합니다.
지원되지 않는 비트 버전 | 32비트 운영 체제를 사용하는 VM은 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 64비트로 업그레이드하는 것이 좋습니다.
Microsoft Visual Studio 구독이 필요합니다. | 서버에서는 Visual Studio 구독을 통해서만 지원되는 Windows 클라이언트 운영 체제를 실행하고 있습니다.
필요한 스토리지 성능을 제공하는 VM을 찾을 수 없음 | 서버에 필요한 스토리지 성능(IOPS(초당 입/출력 작업 수) 및 처리량)이 Azure VM 지원을 초과합니다. 마이그레이션하기 전에 서버에 대한 스토리지 요구 사항을 줄입니다.
필요한 네트워크 성능을 제공하는 VM을 찾을 수 없음 | 서버에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 서버의 네트워킹 요구 사항을 낮춰봅니다.
지정된 위치에서 VM을 찾을 수 없음 | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
부적합한 디스크가 하나 이상 있음 | VM에 연결된 하나 이상의 디스크가 Azure 요구 사항을 충족하지 않습니다.<br/><br/> Azure Migrate: 검색 및 평가는 Ultra 디스크(64TB)에 대한 디스크 제한을 기반으로 디스크를 평가합니다.<br/><br/> VM에 연결된 각 디스크에 대해 디스크 크기가 64TB 미만인지 확인합니다(울트라 SSD 디스크에서 지원됨).<br/><br/> 그렇지 않은 경우 Azure로 마이그레이션하기 전에 디스크 크기를 줄이거나, Azure에서 여러 디스크를 사용하고 [함께 스트라이프](../virtual-machines/premium-storage-performance.md#disk-striping)하여 스토리지 제한을 더 늘려야 합니다. 각 디스크에 필요한 성능(IOPS 및 처리량)이 Azure [관리 가상 머신 디스크](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)에서 지원되는지 확인합니다.
부적합한 네트워크 어댑터가 하나 이상 있음 | 마이그레이션을 시작하기 전에 서버에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 서버에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure Migrate: 검색 및 평가는 최대 64TB 크기의 디스크(Ultra 디스크)를 지원합니다. 마이그레이션을 수행하기 전에 디스크를 64TB 미만으로 축소하거나 Azure에서 여러 디스크를 사용하고 [함께 스트라이프](../virtual-machines/premium-storage-performance.md#disk-striping)하여 스토리지 제한을 더 늘립니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 스토리지 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure에서 적합 한 VM 유형을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 서버의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
제품 통화 RI(예약 인스턴스)에 대한 VM 크기를 찾을 수 없음 | 선택된 RI, 제품 및 통화 조합에 대한 VM 크기를 찾을 수 없기 때문에 서버가 ‘적합하지 않음’으로 표시되었습니다. 평가 속성을 편집하여 유효한 조합을 선택하고 평가를 다시 계산합니다. 
조건부 준비 완료된 인터넷 프로토콜 | Azure VMware Solution 평가에만 적용됩니다. Azure VMware Solution은 IPv6 인터넷 주소를 지원하지 않습니다. 서버가 IPv6에서 검색되면 Azure VMware Solution 팀에 수정 지침을 문의하세요.

## <a name="suggested-migration-tool-in-an-import-based-azure-vmware-solution-assessment-is-unknown"></a>가져오기 기반 Azure VMware Solution 평가에서 제안된 마이그레이션 도구를 알 수 없음

CSV 파일을 통해 가져온 서버의 경우 기본 마이그레이션 도구 및 Azure VMware Solution 평가를 알 수 없습니다. VMware 환경의 서버에는 VMware HCX(하이브리드 클라우드 확장) 솔루션을 사용하는 것이 좋습니다. [자세히 알아보기](../azure-vmware/configure-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM은 Azure VM 평가에서 "조건부로 준비"됩니다.

VMware 및 Hyper-V VM의 경우 Azure VM 평가는 알려진 간격으로 인해 Linux VM을 ‘조건부로 준비’ 상태로 표시합니다. 

- 이 간격 때문에 온-프레미스 VM에 설치된 Linux OS의 부 버전을 검색하지 못하게 됩니다.
- 예를 들어 RHEL 6.10의 경우 현재 Azure VM 평가는 RHEL 6만 OS 버전으로 검색합니다. 이 동작은 vCenter Server와 Hyper-V 호스트가 Linux VM 운영 체제의 커널 버전을 제공하지 않기 때문에 발생합니다.
- Azure가 특정 Linux 버전만 보증하므로, 현재 Linux VM은 Azure VM 평가에서 ‘조건부 준비 완료’로 표시됩니다.
- [Azure Linux 지원](../virtual-machines/linux/endorsed-distros.md)을 검토하여 온-프레미스 VM에서 실행되는 Linux OS가 Azure에서 보증되는지 여부를 확인할 수 있습니다.
- 보증되는 배포판을 확인한 후에는 이 경고를 무시해도 됩니다.

이 간격은 VMware VM에서 [애플리케이션 검색](./how-to-discover-applications.md)을 사용하도록 설정하여 해결할 수 있습니다. Azure VM 평가는 제공된 게스트 자격 증명을 사용하여 VM에서 검색된 운영 체제를 사용합니다. 이 운영 체제 데이터는 Windows 및 Linux VM 둘 다에 대해 적절한 OS 정보를 식별합니다.

## <a name="operating-system-version-not-available"></a>운영 체제 버전을 사용할 수 없음

물리적 서버의 경우 운영 체제 부 버전 정보를 사용할 수 있습니다. 사용할 수 없는 경우 Microsoft 지원에 문의하세요. VMware 환경의 서버에 대해 Azure Migrate는 vCenter Server의 VM에 대해 지정된 운영 체제 정보를 사용합니다. 그러나 vCenter Server는 운영 체제의 부 버전을 제공하지 않습니다. 부 버전을 검색하려면 [애플리케이션 검색](./how-to-discover-applications.md)을 설정하세요. Hyper-V VM의 경우 운영 체제 부 버전 검색이 지원되지 않습니다. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 평가에서 Azure SKU가 온-프레미스보다 더 큼

Azure VM 평가는 평가 유형에 따라 현재 온-프레미스 할당보다 더 많은 코어 및 메모리를 포함하는 Azure VM SKU를 권장할 수 있습니다.

- VM SKU 권장 사항은 평가 속성에 따라 달라집니다.
- 이 권장 사항은 Azure VM 평가에서 사용자가 수행하는 평가의 유형에 따라 영향을 받습니다. 두 가지 유형은 **성능 기반** 또는 **온-프레미스로** 입니다.
- 성능 기반 평가의 경우 Azure VM 평가는 온-프레미스 VM의 데이터 사용률(CPU, 메모리, 디스크 및 네트워크 사용률)을 고려하여 온-프레미스 VM에 적절한 대상 VM SKU를 결정합니다. 또한 효과적인 사용률을 결정할 때 쾌적 인자를 추가합니다.
- 온-프레미스 크기 조정의 경우 성능 데이터는 고려되지 않으며 대상 SKU는 온-프레미스 할당을 기반으로 하는 것이 좋습니다.

권장 사항 예를 하나 살펴보겠습니다.

4코어 8GB 메모리를 사용하고, CPU 사용률과 메모리 사용률은 각각 50%이고, 쾌적 인자가 1.3으로 지정된 온-프레미스 VM이 있습니다.

- 평가가 **온-프레미스로** 인 경우 4코어와 8GB 메모리를 사용하는 Azure VM SKU를 사용하는 것이 좋습니다.
- 평가가 효과적인 CPU 및 메모리 사용률(4코어의 50% *1.3 = 2.6코어 및 8GB 메모리의 50% * 1.3 = 5.3GB 메모리)을 기준으로 하는 **성능 기반** 인 경우 4코어(최근 지원되는 코어 수) 및 8GB 메모리(최근 지원되는 메모리 크기)의 가장 저렴한 VM SKU를 사용하는 것이 좋습니다.
- 평가 크기 조정에 대해 [자세히 알아보세요](concepts-assessment-calculation.md#types-of-assessments).

## <a name="why-is-the-recommended-azure-disk-sku-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 평가에서 권장되는 Azure Disk SKU가 온-프레미스보다 큰 이유는 무엇인가요?

Azure VM 평가는 평가 유형에 따라 더 큰 디스크를 권장할 수 있습니다.

- 디스크 크기 조정은 크기 조정 기준 및 스토리지 유형이라는 두 가지 평가 속성에 따라 달라집니다.
- 크기 조정 기준이 **성능 기반** 이고 스토리지 유형이 **자동** 으로 설정되면 디스크의 IOPS 및 처리량 값을 고려하여 대상 디스크 유형(표준 HDD, 표준 SSD, 프리미엄 또는 Ultra 디스크)을 결정합니다. 디스크 유형의 디스크 SKU가 권장되며 이러한 권장 사항에 온-프레미스 디스크의 크기 요구 사항이 고려됩니다.
- 크기 조정 기준이 **성능 기반** 이고 스토리지 유형이 **프리미엄** 이면 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항에 따라 Azure의 프리미엄 디스크 SKU를 사용하는 것이 좋습니다. 크기 조정 기준이 **온-프레미스로** 이고 스토리지 유형이 **표준 HDD**, **표준 SSD**, **프리미엄** 또는 **Ultra 디스크** 인 경우에도 동일한 논리를 사용하여 디스크 크기를 결정합니다.

예를 들어 32GB 메모리를 포함하는 온-프레미스 디스크가 있지만 디스크의 집계된 읽기/쓰기 IOPS는 800 IOPS라고 가정합니다. Azure VM 평가는 더 높은 IOPS 요구 사항으로 인해 프리미엄 디스크를 권장합니다. 또한 필요한 IOPS 및 크기를 지원할 수 있는 디스크 SKU를 권장합니다. 이 예제에서 가장 가까운 일치 항목은 P15(256GB, 1100IOPS)입니다. 따라서 온-프레미스 디스크에 필요한 크기가 32GB였지만 Azure VM 평가는 온-프레미스 디스크의 높은 IOPS 요구 사항 때문에 더 큰 디스크를 권장한 것입니다.

## <a name="why-is-performance-data-missing-for-some-or-all-vms-in-my-assessment-report"></a>평가 보고서에서 일부 또는 모든 VM의 성능 데이터가 누락된 이유는 무엇인가요?

**성능 기반** 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 VM에 대한 성능 데이터를 수집할 수 없는 경우 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 VM의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터가 누락되었지만 Hyper-V VM을 평가하려는 경우 이러한 VM에서 동적 메모리를 사용하도록 설정했는지 확인합니다. 알려진 문제 때문에 현재 Azure Migrate 어플라이언스가 이러한 VM의 메모리 사용률을 수집할 수 없습니다.
- 모든 성능 카운터가 누락된 경우 평가에 대한 포트 액세스 요구 사항이 충족되는지 확인합니다. [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) 및 [물리적](./migrate-support-matrix-physical.md#port-access) 평가를 위한 포트 액세스 요구 사항에 대해 자세히 알아봅니다.
임의의 성능 카운터가 누락된 경우 Azure Migrate: 검색 및 평가는 할당된 코어/메모리 온-프레미스로 대체하고 해당하는 VM 크기를 권장합니다.

## <a name="why-is-performance-data-missing-for-some-or-all-servers-in-my-azure-vm-or-azure-vmware-solution-assessment-report"></a>내 Azure VM 또는 Azure VMware Solution 평가 보고서에서 일부 또는 모든 서버에 대한 성능 데이터가 누락된 이유는 무엇인가요?

**성능 기반** 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 서버에 대한 성능 데이터를 수집할 수 없으면 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 서버의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터만 누락되었지만 Hyper-V 환경의 서버를 평가하려고 하는지 확인합니다. 이 시나리오에서는 서버에서 동적 메모리를 사용하도록 설정하고 최신 변경 내용을 반영하도록 평가를 다시 계산합니다. 서버에서 동적 메모리를 사용하도록 설정한 경우에만 어플라이언스에서 Hyper-V 환경의 서버에 대한 메모리 사용률 값을 수집할 수 있습니다.
- 모든 성능 카운터가 누락된 경우 포트 443(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

    > [!Note]
    > 임의의 성능 카운터가 누락된 경우 Azure Migrate: 검색 및 평가는 할당된 코어/메모리 온-프레미스로 대체하고 해당하는 VM 크기를 권장합니다.

## <a name="why-is-performance-data-missing-for-some-or-all-sql-instances-or-databases-in-my-azure-sql-assessment"></a>내 Azure SQL 평가에서 일부 또는 모든 SQL 인스턴스 또는 데이터베이스에 대한 성능 데이터가 누락된 이유는 무엇인가요?

성능 데이터가 수집되는지 확인하려면 다음을 확인하세요.

- 평가를 만드는 동안 SQL Server의 전원이 켜져 있는지 확인합니다.
- Azure Migrate에서 SQL 에이전트의 연결 상태가 **연결됨** 인지 확인하고 마지막 하트비트도 확인합니다. 
- 검색된 SQL 인스턴스 창에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 **연결됨** 인지 확인합니다.
- 모든 성능 카운터가 누락된 경우 443 포트(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

성능 카운터가 누락된 경우 Azure SQL 평가에서 해당 인스턴스 또는 데이터베이스에 대해 가장 작은 Azure SQL 구성을 권장합니다.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>내 평가의 신뢰 등급이 낮은 이유는 무엇인가요?

신뢰 등급은 평가를 계산하는 데 필요한 [사용 가능한 데이터 요소](./concepts-assessment-calculation.md#ratings)의 백분율을 기준으로 **성능 기반** 평가에 대해 계산됩니다. 평가는 다음과 같은 이유로 낮은 신뢰 등급을 받을 수 있습니다.

- 평가를 만드는 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 일주일로 설정하여 평가를 만드는 경우 검색 시작 후 일주일 이상 기다려야 데이터 요소가 수집됩니다. 이 기간 동안 기다릴 수 없으면 성능 기간을 더 짧은 기간으로 변경하고 평가를 다시 계산합니다.
- 평가는 일부 또는 모든 서버에 대한 성능 데이터를 평가 기간에 수집할 수 없습니다. 높은 신뢰 등급의 경우 다음을 확인하세요. 
    - 평가 기간에 서버의 전원이 켜져 있는지 확인합니다.
    - 443 포트에서 아웃바운드 연결이 허용되는지 확인합니다.
    - Hyper-V 서버의 경우 동적 메모리가 사용하도록 설정되어 있는지 확인합니다.
    - Azure Migrate 에이전트의 연결 상태가 ‘연결됨’인지 확인합니다. 또한 마지막 하트비트를 확인합니다.
    - Azure SQL 평가의 경우 검색된 SQL 인스턴스 창에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 ‘연결됨’인지 확인합니다.

    신뢰 등급의 최신 변경 내용을 반영하려면 평가를 다시 계산합니다.

- Azure VM 및 Azure VMware Solution 평가의 경우 검색이 시작된 후 만들어진 서버가 거의 없었습니다. 예를 들어 지난 한 달 동안의 성능 기록에 대한 평가를 만들고 있지만 환경에서 불과 일주일 전에 몇몇 서버가 만들어진 경우입니다. 이 경우 새 서버의 성능 데이터가 전체 기간에 대해 제공되지 않으며 신뢰 등급이 낮습니다. [자세히 알아보기](./concepts-assessment-calculation.md#confidence-ratings-performance-based).
- Azure SQL 평가의 경우 검색을 시작한 후 일부 SQL 인스턴스 또는 데이터베이스가 만들어졌습니다. 예를 들어 지난 한 달 동안의 성능 기록에 대한 평가를 만들고 있지만 환경에서 불과 일주일 전에 몇몇 SQL 인스턴스 또는 데이터베이스가 만들어진 경우입니다. 이 경우 새 서버의 성능 데이터가 전체 기간에 대해 제공되지 않으며 신뢰 등급이 낮습니다. [자세히 알아보기](./concepts-azure-sql-assessment-calculation.md#confidence-ratings).

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 평가에 운영 체제 라이선스가 포함되어 있나요?

Azure VM 평가는 현재 Windows Server에 대해서만한 운영 체제 라이선스 비용을 고려합니다. Linux 서버에 대한 라이선스 비용은 현재 고려되지 않습니다.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Azure VM 평가에서 성능 기반 크기 조정은 어떻게 작동하나요?

Azure VM 평가는 온-프레미스 서버의 성능 데이터를 지속적으로 수집하여 Azure에서 VM SKU 및 디스크 SKU를 추천하는 데 사용합니다. 성능 기반 데이터가 수집되는 [방법을 알아봅니다](concepts-assessment-calculation.md#calculate-sizing-performance-based).

## <a name="can-i-migrate-my-disks-to-an-ultra-disk-by-using-azure-migrate"></a>Azure Migrate를 사용하여 디스크를 Ultra 디스크로 마이그레이션할 수 있나요?

아니요. 현재 Azure Migrate와 Azure Site Recovery 모두 Ultra 디스크로의 마이그레이션을 지원하지 않습니다. [이 웹 사이트](../virtual-machines/disks-enable-ultra-ssd.md?tabs=azure-portal#deploy-an-ultra-disk)에서 Ultra disk를 배포하는 단계를 찾으세요.

## <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>내 Ultra 디스크의 프로비저닝된 IOPS 및 처리량이 온-프레미스 IOPS 및 처리량보다 많은 이유는 무엇인가요?

[공식 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)에 따라 Ultra 디스크는 프로비전된 크기, 프로비전된 IOPS 및 프로비전된 처리량을 기준으로 청구됩니다. 예를 들어 20,000 IOPS 및 1,000MB/초의 200GiB Ultra 디스크를 프로비전하고 20시간 후에 삭제한 경우 256GiB의 디스크 크기 제안에 매핑됩니다. 20시간 동안 256GiB, 20,000 IOPS 및 1000MB/초에 대한 요금이 청구됩니다.

프로비전할 IOPS = (검색된 처리량)*1024/256

## <a name="does-the-ultra-disk-recommendation-consider-latency"></a>Ultra 디스크 권장 사항은 대기 시간을 고려하나요?

아니요, 현재 디스크 크기, 총 처리량 및 총 IOPS만 크기 조정 및 비용 계산에 사용됩니다.

## <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>M 시리즈가 Ultra 디스크를 지원하는 것을 확인할 수 있지만 Ultra 디스크가 권장된 평가에서는 ‘이 위치에 대한 VM을 찾을 수 없음’이라고 표시됩니다.

이러한 결과는 Ultra 디스크를 지원하는 모든 VM 크기가 모든 Ultra 디스크 지원 지역에 존재하는 것은 아니기 때문에 가능한 것입니다. 대상 평가 지역을 변경하여 이 서버의 VM 크기를 가져옵니다.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>평가가 잘못된 예약 인스턴스, VM 작동 시간 및 할인율(%) 조합을 사용하여 생성되었다는 경고를 표시하는 이유는 무엇인가요?

**예약 인스턴스** 를 선택하면 **할인율(%)** 및 **VM 작동 시간** 속성은 적용되지 않습니다. 평가가 이러한 속성을 잘못 조합하여 만들어졌기 때문에 **편집** 및 **다시 계산** 단추가 사용하지 않도록 설정됩니다. 새 평가를 만드세요. [자세히 알아보기](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-dont-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>실제 물리적 서버의 일부 네트워크 어댑터에 대한 성능 데이터가 표시되지 않습니다.

이 문제는 물리적 서버에서 Hyper-V 가상화를 사용하도록 설정한 경우에 발생할 수 있습니다. 이러한 서버에서는 제품 간격 때문에 현재 Azure Migrate에서는 물리적 및 가상 네트워크 어댑터를 모두 검색합니다. 네트워크 처리량은 검색된 가상 네트워크 어댑터에만 캡처됩니다.

## <a name="the-recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>물리적 서버의 권장되는 Azure VM SKU가 너무 큽니다.

이 문제는 물리적 서버에서 Hyper-V 가상화를 사용하도록 설정한 경우에 발생할 수 있습니다. 이러한 서버에서는 현재 Azure Migrate에서는 물리적 및 가상 네트워크 어댑터를 모두 검색합니다. 따라서 검색된 네트워크 어댑터의 수가 실제보다 많습니다. Azure VM 평가는 필요한 수의 네트워크 어댑터를 지원할 수 있는 Azure VM을 선택하므로 잠재적으로 VM 크기가 커질 수 있습니다. 네트워크 어댑터 수가 크기 조정에 미치는 영향에 대해 [자세히 알아보세요](./concepts-assessment-calculation.md#calculating-sizing). 이 제품 간격은 향후 해결될 것입니다.

## <a name="the-readiness-category-is-marked-not-ready-for-my-physical-server"></a>물리적 서버에 대한 준비 상태 범주가 ‘준비 안 됨’으로 표시됩니다.

Hyper-V 가상화를 사용하도록 설정한 물리적 서버의 경우 준비 상태 범주가 ‘준비 안 됨’으로 잘못 표시될 수 있습니다. 이러한 서버에서는 제품 간격 때문에 현재 Azure Migrate에서는 물리적 및 가상 어댑터를 모두 검색합니다. 따라서 검색된 네트워크 어댑터의 수가 실제보다 많습니다. **온-프레미스로** 및 **성능 기반** 평가에서 Azure VM 평가는 필요한 수의 네트워크 어댑터를 지원할 수 있는 Azure VM을 선택합니다. 검색된 네트워크 어댑터 수가 Azure VM에서 지원되는 최대 NIC 수인 32보다 크면 서버는 ‘준비되지 않음’으로 표시됩니다. NIC 수가 크기 조정에 미치는 영향에 대해 [자세히 알아보세요](./concepts-assessment-calculation.md#calculating-sizing).

## <a name="the-number-of-discovered-nics-is-higher-than-actual-for-physical-servers"></a>물리적 서버에 대해 실제보다 많은 NIC가 검색됩니다.

이 문제는 물리적 서버에서 Hyper-V 가상화를 사용하도록 설정한 경우에 발생할 수 있습니다. 이러한 서버에서는 현재 Azure Migrate에서는 물리적 및 가상 어댑터를 모두 검색합니다. 따라서 검색된 NIC의 수가 실제보다 많습니다.

## <a name="capture-network-traffic"></a>네트워크 트래픽 캡처

네트워크 트래픽 로그를 수집하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. F12 키를 선택하여 개발자 도구를 시작합니다. 필요한 경우 **탐색 시 항목 지우기** 설정을 선택 취소합니다.
1. **네트워크** 탭을 선택하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log** 를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 빨간색 원이 나타나지 않으면 시작할 검은색 원을 선택합니다.
   - Microsoft Edge 및 Internet Explorer에서 기록이 자동으로 시작됩니다. 그렇지 않으면 녹색 재생 단추를 선택합니다.
1. 오류를 재현해 봅니다.
1. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content(콘텐츠와 함께 HAR로 저장)** 를 선택합니다. 그러면 로그가 .har 파일로 압축되어 내보내집니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택합니다. 그러면 로그가 압축되어 내보내집니다.
1. **콘솔** 탭을 선택하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. **다른 이름으로 저장** 을 선택하여 로그를 내보내고 압축합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사** 를 선택합니다.
1. 개발자 도구를 닫습니다.

## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>내 평가의 운영 체제 데이터는 어디에서 검색되나요?

- VMware VM의 경우 기본적으로 vCenter Server에서 제공하는 운영 체제 데이터입니다.
   - VMware Linux VM의 경우 애플리케이션 검색을 사용하도록 설정하면 게스트 VM에서 OS 세부 정보를 가져옵니다. 평가에서 OS 세부 정보를 확인하려면 **검색된 서버** 보기로 이동하여 **운영 체제** 열의 값 위에 마우스를 올려 놓습니다. 팝업되는 텍스트에서 표시되는 OS 데이터가 vCenter Server에서 수집되었는지 또는 VM 자격 증명을 사용하여 게스트 VM에서 수집되었는지 확인할 수 있습니다.
   - Windows VM의 경우 운영 체제 세부 정보는 항상 vCenter Server에서 가져옵니다.
- Hyper-V VM의 경우 운영 체제 데이터가 Hyper-V 호스트에서 수집됩니다.
- 물리적 서버의 경우 서버에서 가져옵니다.

## <a name="common-web-apps-discovery-errors"></a>일반적인 웹앱 검색 오류

Azure Migrate는 Azure Migrate: 검색 및 평가 도구를 사용하여 Azure App Service로 마이그레이션하기 위해 검색된 ASP.NET 웹앱을 평가하는 옵션을 제공합니다. 시작하려면 [평가](tutorial-assess-webapps.md) 자습서를 참조하세요.

다음 표에 일반적인 App Service 평가 오류가 요약되어 있습니다.

| **오류** | **원인** | **권장 작업** |
|--|--|--|
|**애플리케이션 풀 검사**|IIS 사이트는 다음 애플리케이션 풀을 사용하고 있습니다. {0}.|Azure App Service는 App Service 애플리케이션당 둘 이상의 애플리케이션 풀 구성을 지원하지 않습니다. 워크로드를 단일 애플리케이션 풀로 이동시키고 다른 애플리케이션 풀을 제거합니다.|
|**애플리케이션 풀 ID 검사**|사이트의 애플리케이션 풀이 지원되지 않는 사용자 ID 유형으로 실행 중입니다. {0}|App Service는 LocalSystem 또는 SpecificUser 애플리케이션 풀 ID 유형 사용을 지원하지 않습니다. ApplicationPoolIdentity로 실행되도록 애플리케이션 풀을 설정합니다.|
|**권한 부여 검사**|다음과 같은 지원되지 않는 인증 유형이 검색되었습니다. {0}|App Service 지원 인증 유형 및 구성은 온-프레미스 IIS와 다릅니다. 사이트에서 지원되지 않는 인증 유형을 사용하지 않도록 설정합니다. 마이그레이션이 완료되면 App Service에서 지원하는 인증 유형 중 하나를 사용하여 사이트를 구성할 수 있습니다.|
|**권한 부여 검사 알 수 없음**|모든 사이트 구성에 대해 사용하도록 설정된 인증 유형을 결정할 수 없습니다.|인증 유형을 결정할 수 없습니다. 모든 구성 오류를 수정하고 모든 사이트 콘텐츠 위치가 관리자 그룹에 액세스할 수 있는지 확인합니다.|
|**구성 오류 검사**|다음과 같은 구성 오류가 검색되었습니다. {0}|적용 가능한 모든 구성을 읽지 않고서는 마이그레이션 준비 상태를 결정할 수 없습니다. 모든 구성 오류를 수정합니다. 구성이 유효하고 액세스할 수 있는지 확인합니다.|
|**콘텐츠 크기 검사**|사이트 콘텐츠가 성공적인 마이그레이션을 위해 허용된 최댓값인 2GB보다 큰 것 같습니다.|성공적인 마이그레이션을 위해서는 사이트 콘텐츠가 2GB 미만이어야 합니다. 사이트가 Azure Storage와 같은 정적 콘텐츠에 대해 비 파일 시스템 기반 스토리지 옵션을 사용하도록 전환할 수 있는지 평가합니다.|
|**콘텐츠 크기 검사 알 수 없음**|파일 콘텐츠 크기를 확인할 수 없으며 이는 일반적으로 액세스 문제를 나타냅니다.|사이트를 마이그레이션하려면 콘텐츠에 액세스할 수 있어야 합니다. 사이트가 콘텐츠에 대해 UNC 공유를 사용하지 않고 있고 모든 사이트 콘텐츠 위치가 관리자 그룹에 액세스할 수 있는지 확인합니다.|
|**글로벌 모듈 검사**|다음과 같은 지원되지 않는 글로벌 모듈이 감지되었습니다. {0}|App Service는 제한된 글로벌 모듈을 지원합니다. GlobalModules 섹션에서 지원되지 않는 모듈을 모든 관련 구성과 함께 제거합니다.|
|**ISAPI 필터 검사**|다음과 같은 지원되지 않는 ISAPI 필터가 감지되었습니다. {0}|사용자 지정 ISAPI 필터의 자동 구성은 지원되지 않습니다. 지원되지 않는 ISAPI 필터를 제거합니다.|
|**ISAPI 필터 검사 알 수 없음**|모든 사이트 구성에 대해 존재하는 ISAPI 필터를 확인할 수 없습니다.|사용자 지정 ISAPI 필터의 자동 구성은 지원되지 않습니다. 모든 구성 오류를 수정하고 모든 사이트 콘텐츠 위치가 관리자 그룹에 액세스할 수 있는지 확인합니다.|
|**위치 태그 검사**|applicationHost.config 파일에서 다음과 같은 위치 경로를 찾았습니다. {0}|마이그레이션 방법은 applicationHost.config에서 이동 위치 경로 구성을 지원하지 않습니다. 위치 경로 구성을 사이트의 루트 web.config 파일 또는 적용할 특정 애플리케이션과 연결된 web.config 파일로 이동합니다.|
|**프로토콜 검사**|다음과 같은 지원되지 않는 프로토콜을 사용하는 바인딩을 찾았습니다. {0}|App Service는 HTTP 및 HTTPS 프로토콜만 지원합니다. HTTP 또는 HTTPS가 아닌 프로토콜을 사용하는 바인딩을 제거합니다.|
|**가상 디렉터리 검사**|다음 가상 디렉터리는 UNC 공유에서 호스트됩니다. {0}|마이그레이션은 UNC 공유에서 호스트되는 사이트 콘텐츠 마이그레이션을 지원하지 않습니다. 콘텐츠를 로컬 파일 경로로 이동하거나 Azure Storage와 같은 비 파일 시스템 기반 스토리지 옵션으로 변경하는 것을 고려하세요. 공유 구성을 사용하는 경우 콘텐츠 경로를 수정하기 전에 서버에 대한 공유 구성을 사용하지 않도록 설정합니다.|
|**바인딩 검사**|애플리케이션은 HTTPS를 사용합니다.|App Service의 HTTPS 구성에는 더 많은 수동 단계가 필요합니다. 인증서를 App Service 사이트에 연결하기 위해 다른 마이그레이션 후 단계가 필요합니다.|
|**TCP 포트 검사**|다음과 같은 지원되지 않는 포트에서 바인딩이 검색되었습니다. {0}|App Service는 포트 80 및 443만 지원합니다. 사이트에 요청하는 클라이언트는 포트 80 또는 포트 443을 사용하도록 요청에서 포트를 업데이트해야 합니다.|
|**프레임워크 검사**|다음과 같은 .NET이 아닌 프레임워크 또는 지원되지 않는 .NET 프레임워크 버전이 이 사이트에서 사용 중인 것으로 검색되었습니다. {0}|마이그레이션은 .NET이 아닌 사이트에 대한 프레임워크의 유효성을 검사하지 않습니다. App Service는 여러 프레임워크를 지원하지만 프레임워크마다 마이그레이션 옵션이 다릅니다. 사이트에서 .NET이 아닌 프레임워크를 사용하고 있지 않은지 확인하거나 대체 마이그레이션 옵션을 사용하는 것을 고려합니다.|

## <a name="next-steps"></a>다음 단계

평가를 [만들거나](how-to-create-assessment.md) [사용자 지정](how-to-modify-assessment.md)합니다.