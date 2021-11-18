---
title: Azure Backup - 보관 계층 개요
description: Azure Backup용 보관 계층 지원에 대해 알아봅니다.
ms.topic: overview
ms.date: 10/23/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: cedc8dbcaaf05c54ae624b4c1ab548a900231cf1
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718491"
---
# <a name="overview-of-archive-tier-in-azure-backup"></a>Azure Backup의 보관 계층 개요

고객은 조직의 준수 규칙에 따라 정의되는 보존 요구 사항이 있는 LTR(장기 보존) 백업 데이터 등의 백업 데이터를 저장하기 위해 Azure Backup을 사용합니다. 대부분의 경우 이전 백업 데이터는 거의 액세스되지 않고 준수 요구 사항에 한해 저장됩니다.

Azure Backup은 스냅샷과 표준 계층 외에도 보관 계층 내 장기 보존 지점의 백업을 지원합니다.

## <a name="supported-workloads"></a>지원되는 워크로드

보관 계층은 다음 워크로드를 지원합니다.

| 워크로드 | 작업 |
| --- | --- |
| Azure 가상 머신 | <ul><li>월간/연간 복구 지점만 해당. 일간/주간 복구 지점은 지원되지 않습니다.  </li><li>사용 기간 >= 3개월(자격 증명 모음 표준 계층) </li><li>남은 보존 기간 >= 6개월 </li><li>활성 상태의 일간/주간 종속성 없음. </li></ul> |
| Azure Virtual Machines의 Azure 가상 머신/SAP HANA 내 SQL Server | <ul><li>전체 복구 지점만 해당. 로그 및 차등은 지원되지 않습니다. </li><li>사용 기간 >= 45일(자격 증명 모음 표준 계층). </li><li>남은 보존 기간 >= 6개월 </li><li>종속성 없음 </li></ul> |

>[!Note]
>- Azure VM의 SQL Server 및 Azure VM의 SAP HANA에 대한 보관 계층 지원이 이제 여러 지역에서 일반적으로 공급됩니다. 지원되는 지역에 대한 자세한 목록은 [지원 매트릭스](#support-matrix)를 참조하세요.
>- Azure Virtual Machines에 대한 보관 계층 지원도 제한된 공개 미리 보기로 제공됩니다. 제한된 공개 미리 보기에 등록하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u)을 채웁니다.

## <a name="supported-clients"></a>지원되는 클라이언트

보관 계층은 다음 클라이언트를 지원합니다.

- [PowerShell](./use-archive-tier-support.md?pivots=client-powershelltier)
- [CLI](./use-archive-tier-support.md?pivots=client-clitier)
- [Azure Portal](./use-archive-tier-support.md?pivots=client-portaltier)

## <a name="how-azure-backup-moves-recovery-points-to-the-vault-archive-tier"></a>Azure Backup에서 복구 지점을 자격 증명 모음-보관 계층으로 이동하는 방법

> [!VIDEO https://www.youtube.com/embed/nQnH5mpiz60?start=416]

## <a name="archive-recommendations-only-for-azure-virtual-machines"></a>보관 권장 사항(Azure Virtual Machines에만 해당)

Azure Virtual Machines에 대한 복구 지점은 증분입니다. 복구 지점이 보관 계층으로 이동하면 전체 복구 지점으로 변환되어 보관 계층의 모든 복구 지점이 서로 독립적이고 격리되도록 합니다. 따라서 전체 백업 스토리지(자격 증명 모음-표준 + 자격 증명 모음-보관)가 늘어날 수 있습니다.

스토리지 증가의 양은 Virtual Machines의 변동 패턴에 따라 다릅니다.

- 복구 지점을 보관 계층으로 이동하는 경우 Virtual Machines의 변동이 높을수록 전체 백업 스토리지는 더 낮습니다.
- Virtual Machines의 변동이 낮은 경우 보관 계층으로 이동하면 백업 스토리지가 증가하여 자격 증명 모음 표준 계층과 자격 증명 모음 보관 계층 간의 가격 차이를 오프셋할 수 있습니다. 따라서 전반적인 비용이 늘어날 수 있습니다.

이 문제를 해결하기 위해 Azure Backup이 권장 사항 집합을 제공합니다. 권장 사항 집합은 복구 지점의 목록을 반환합니다. 이는 보관 계층으로 함께 이동하여 비용을 절감할 수 있도록 합니다.

>[!Note]
>비용 절감은 여러 가지 이유로 인해 발생할 수 있으며 모든 인스턴스에 따라 다를 수 있습니다.

## <a name="modify-protection"></a>보호 수정

Azure Backup은 데이터 원본에 대한 보호를 수정하는 두 가지 방법을 제공합니다.

- 기존 정책 수정
- 새 정책을 사용하여 데이터 원본 보호

두 시나리오 모두 새로운 정책이 표준 계층과 보관 계층에 있는 모든 이전 복구 지점에 적용됩니다. 따라서 정책이 변경된 경우 이전 복구 지점은 삭제될 수 있습니다.

복구 지점이 보관으로 이동되면 180일의 초기 삭제 기간이 적용됩니다. 요금은 비례 배분됩니다. 180일 동안 보관에 유지되지 않는 복구 지점을 삭제한 경우, 180에서 표준 계층에서 소요된 기간(일)을 뺀 값에 해당하는 비용이 발생합니다.

최소 180일 동안 보관에 유지되지 않은 복구 지점 삭제 시 초기 삭제 비용이 발생합니다.

## <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

보호를 중지하고 데이터를 삭제하면 모든 복구 지점이 삭제됩니다. 보관 계층에서 180일간 유지되지 않은 보관의 복구 지점의 경우 복구 지점을 삭제하면 초기 삭제 비용이 발생합니다.

## <a name="archive-tier-pricing"></a>보관 계층 가격 책정

[가격 책정 페이지](azure-backup-pricing.md)에서 보관 계층 가격 책정을 볼 수 있습니다.

## <a name="support-matrix"></a>지원 매트릭스

| 워크로드 | 미리 보기 | 일반 공급 |
| --- | --- | --- |
| Azure Virtual Machines의 Azure 가상 머신/SAP HANA 내 SQL Server | 없음 | 오스트레일리아 동부, 인도 중부, 북유럽, 동남 아시아, 동아시아, 오스트레일리아 남동부, 캐나다 중부, 브라질 남부, 캐나다 동부, 프랑스 중부, 프랑스 남부, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 인도 남부, 영국 서부, 영국 남부, 미국 중부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중서부, 미국 동부, 미국 중남부, 미국 중북부, 서유럽, US Gov 버지니아, US Gov 텍사스, US Gov 애리조나. |
| Azure Virtual Machines | 미국 동부, 미국 동부 2, 미국 중부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 중서부, 미국 중북부, 브라질 남부, 캐나다 동부, 캐나다 중부, 서유럽, 영국 남부, 영국 서부, 동아시아, 일본 동부, 인도 남부, 동남 아시아, 오스트레일리아 동부, 인도 중부, 북유럽, 오스트레일리아 남동부, 프랑스 중부, 프랑스 남부, 일본 서부, 한국 중부, 한국 남부. | 없음 |


## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>보호를 중지하고 데이터를 보존하면 보관 복구 지점은 어떻게 되나요?

복구 지점이 영구적으로 보관 상태로 유지됩니다. 자세한 내용은 [복구 지점에 대한 보호 중지의 영향](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points)을 참조하세요.

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>지역 간 복원이 보관 계층에서 지원되나요?

GRS 자격 증명 모음의 데이터를 표준 계층에서 보관 계층으로 이동하면 데이터가 GRS 보관으로 이동합니다. 지역 간 복원이 사용되는 경우에도 마찬가지입니다. 백업 데이터를 보관 계층으로 이동한 후에는 쌍으로 연결된 지역으로 데이터를 복원할 수 없습니다. 그러나 지역에서 장애가 발생하는 경우 보조 지역의 백업 데이터를 복원할 수 있게 됩니다. 

주 지역의 보관 계층에 있는 복구 지점에서 복원하는 동안 복구 지점은 표준 계층에 복사되고 주 지역과 보조 지역에서 모두 리하이드레이션 기간에 따라 보존됩니다. 이러한 리하이드레이션된 복구 지점에서 지역 간 복원을 수행할 수 있습니다.

### <a name="i-can-see-eligible-recovery-points-for-my-virtual-machine-but-i-cant-seeing-any-recommendation-what-can-be-the-reason"></a>내 Virtual Machine에 적합한 복구 지점은 볼 수 있지만 권장 사항을 볼 수는 없습니다. 어떤 이유가 있을 수 있나요?

Virtual Machines에 대한 복구 지점은 자격 기준을 충족합니다. 보관할 수 있는 복구 지점이 있습니다. 그러나 Virtual Machine의 변동은 부족할 수 있으므로 권장 사항이 없습니다. 이 시나리오에서는 보관할 수 있는 복구 지점을 보관 계층으로 이동할 수 있지만 전체 백업 스토리지 비용은 늘어날 수 있습니다.

### <a name="i-have-stopped-protection-and-retained-data-for-my-workload-can-i-move-the-recovery-points-to-archive-tier"></a>내 워크로드에 대한 보호를 중지하고 데이터를 보존 했습니다. 복구 지점을 보관 계층으로 이동할 수 있나요?

아니요. 특정 워크로드에 대한 보호를 중지한 후에는 해당 복구 지점은 보관 계층으로 이동할 수 없습니다. 복구 지점을 보관 계층으로 이동하려면 데이터 원본에 대 한 보호를 계속해야 합니다.

## <a name="next-steps"></a>다음 단계

- [보관 계층 사용](use-archive-tier-support.md)
- [Azure Backup 가격 책정](azure-backup-pricing.md)