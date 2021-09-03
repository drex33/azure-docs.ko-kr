---
title: Azure HPC Cache를 사용한 지역 중복 및 장애 조치(failover) 복구
description: Azure HPC Cache를 사용하여 재해 복구를 위한 장애 조치 기능을 제공하는 기술
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: v-erkel
ms.openlocfilehash: a05a281bdf01a01be842e99cc1b08383d0b9c1bc
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539596"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>지역 장애 조치 복구에 여러 캐시 사용

각 Azure HPC Cache 인스턴스는 특정 구독 내에서, 한 지역에서 실행됩니다. 즉, 캐시의 지역 전체 가동이 중단되면 캐시 워크플로가 중단될 수 있습니다.

이 문서에서는 캐시 장애 조치에 두 번째 지역을 사용하여 작업 중단의 위험을 줄이는 전략을 설명합니다.

키가 여러 지역에서 액세스할 수 있는 백 엔드 스토리지를 사용하고 있습니다. 이 스토리지는 적절한 DNS를 지원하는 온-프레미스 NAS 시스템이거나 캐시와 다른 지역에 있는 Azure Blob Storage일 수 있습니다.

주 지역에서 워크플로가 진행될 때 데이터는 지역 외부의 장기 스토리지에 저장됩니다. 캐시 지역을 사용할 수 없게 되면 중복 Azure HPC Cache 인스턴스를 보조 지역에 만들고, 동일한 스토리지에 연결하고, 새 캐시에서 작업을 계속할 수 있습니다.

> [!NOTE]
> 이 장애 조치(failover) 계획에는 *스토리지 계정* 지역의 완전한 중단이 포함되지 않습니다. 또한 Azure HPC Cache는 지역 간 비동기 복사가 HPC Cache 워크플로에 대해 충분히 일관되지 않기 때문에 지리적으로 중복되는 스토리지 계정(GRS 또는 GZRS)을 지원하지 않습니다.
>
> HPC Cache는 [하나의 Azure 지역 내에서 데이터를 복제](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region)하는 LRS(로컬 중복 스토리지) 및 ZRS(영역 중복 스토리지)를 **지원합니다**.
>
> 전체 지역 스토리지 중단으로부터 보호해야 하는 경우 수동 백업 전략을 고려합니다.

## <a name="planning-for-regional-failover"></a>지역 장애 조치 계획

가능한 장애 조치를 위해 준비된 캐시를 설정하려면 다음 단계를 수행합니다.

1. 두 번째 지역에서 백 엔드 스토리지에 액세스할 수 있는지 확인합니다.
1. 기본 캐시 인스턴스를 만들 계획인 경우 두 번째 지역에서도 이 설정 프로세스를 복제하도록 준비해야 합니다. 다음 항목을 포함합니다.

   1. 가상 네트워크 및 서브넷 구조
   1. 캐시 용량
   1. 스토리지 대상 세부 정보, 이름, 네임스페이스 경로
   1. 클라이언트 머신이 캐시와 동일한 지역에 있는 경우 해당 머신에 관한 세부 정보
   1. 캐시 클라이언트에서 사용할 탑재 명령

   > [참고] Azure HPC Cache는 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 사용하거나 해당 API에 직접 액세스하여 프로그래밍 방식으로 만들 수 있습니다. 자세한 내용은 Azure HPC Cache 팀에 문의하세요.

## <a name="failover-example"></a>장애 조치 예제

예를 들어, Azure의 미국 동부 지역에서 Azure HPC Cache를 찾으려고 한다고 가정합니다. 온-프레미스 데이터 센터에 저장된 데이터에 액세스합니다.

미국 서부 2 지역의 캐시를 장애 조치 백업으로 사용할 수 있습니다.

미국 동부에서 캐시를 만들 경우 미국 서부 2에서 배포할 두 번째 캐시를 준비합니다. 스크립팅 또는 템플릿을 사용하여 이 준비를 자동화할 수 있습니다.

미국 동부에서 지역 전체 오류가 발생할 경우 미국 서부 2 지역에서 준비한 캐시를 만듭니다.

캐시를 만든 후 동일한 온-프레미스 데이터 저장소를 가리키는 스토리지 대상을 추가하고 이전 캐시의 스토리지 대상과 동일한 집계된 네임스페이스 경로를 사용합니다.

원래 클라이언트가 영향을 받는 경우 새 캐시와 함께 사용할 새 클라이언트를 미국 서부 2 지역에 만듭니다.

클라이언트가 지역 중단의 영향을 받지 않더라도 모든 클라이언트는 새 캐시를 탑재해야 합니다. 새 캐시에는 이전 캐시와 다른 탑재 주소가 있습니다.

## <a name="learn-more"></a>자세한 정보

Azure 애플리케이션 아키텍처 가이드에는 [지역 전체 서비스 중단에서 복구](/azure/architecture/resiliency/recovery-loss-azure-region)하는 방법에 관한 자세한 정보가 포함됩니다.
