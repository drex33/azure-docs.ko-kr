---
title: Azure HPC Cache 메트릭 및 모니터링
description: Azure HPC Cache 관련 통계 보는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: v-erkel
ms.openlocfilehash: 0963a412ac3939ad9421c2f7c1de7d72c24995ac
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113589790"
---
# <a name="cache-metrics-and-monitoring"></a>캐시 메트릭 및 모니터링

Azure Portal에는 Azure HPC Cache 성능을 모니터링하는 데 사용할 수 있는 기본 제공 시각화 기능이 몇 가지 있습니다.

이 문서에서는 시각화 기능을 찾을 수 있는 위치와 여기에 표시되는 내용을 설명합니다.

캐시 성능 데이터를 보여 주는 HPC Cache 포털 페이지가 네 개 있습니다.

* **개요**
* **메트릭**
* **캐시 보고서**
* **클라이언트 상태**

사이드바의 **모니터링** 메뉴에서 **메트릭**, **캐시 보고서** 및 **클라이언트 상태** 페이지를 엽니다. 캐시의 **개요** 페이지는 사이드바 메뉴의 맨 위 항목입니다.

![HPC Cache용 Azure Portal 인터페이스 왼쪽에 있는 모니터링 메뉴의 스크린샷.](media/monitoring-menu.png)

## <a name="overview-page"></a>개요 페이지

캐시의 **개요** 페이지에는 몇 가지 기본 캐시 통계, 즉 캐시 처리량, 초당 작업, 대기 시간에 대한 그래프가 표시됩니다. 그래프는 페이지 하단의 **Essentials** 섹션에 있습니다.

![샘플 캐시에 대해 위에서 언급한 통계를 보여 주는 세 개의 선 그래프 스크린샷](media/hpc-cache-overview-stats.png)

차트는 Azure의 기본 제공 모니터링 및 분석 도구에 포함됩니다. 이러한 기능에 대한 자세한 내용은 [Azure 모니터링 설명서](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal)를 참조하세요.

## <a name="metrics-page"></a>메트릭 페이지

HPC Cache **메트릭** 페이지는 또 다른 표준 Azure 기능입니다. 페이지의 팁에 따라 고유한 차트를 만들고 기능에 대해 자세히 알아보세요.

## <a name="cache-report"></a>캐시 보고서

**캐시 보고서** 는 캐시의 사용된 공간, 사용 가능한 공간 및 캐시 재활용 속도를 보여 주는 사용자 지정된 차트 집합입니다.

기본적으로 페이지에는 모든 캐시의 스토리지 대상에 대해 집계된 데이터가 표시되지만, **Storage 대상** 컨트롤을 사용하여 특정 스토리지 대상의 캐시 사용량 데이터를 대신 표시할 수 있습니다. 또한 표시할 시간 범위를 사용자 지정할 수 있습니다.

![캐시 보고서 페이지의 스크린샷.](media/cache-report.png)

* **사용된 캐시 공간** 은 클라이언트 컴퓨터에서 요청한 파일을 저장하는 데 사용 중인 캐시 스토리지의 공간 크기입니다. 단일 스토리지 대상을 선택하면 해당 백 엔드 스토리지 시스템의 파일에 사용되는 공간만 표시됩니다.

* **사용 가능한 캐시 공간** 은 캐시 스토리지에 남아 있는, 사용 가능한 용량입니다.

* **캐시 재활용 속도** 는 새로 요청된 파일의 공간을 확보하기 위해 캐시에서 이전 파일을 제거하는 속도를 나타냅니다. 단일 스토리지 대상의 경우 이 계산에는 해당 대상에 저장된 파일만 포함됩니다.

## <a name="client-status"></a>클라이언트 상태

**클라이언트 상태** 페이지에는 Azure HPC Cache에 연결된 클라이언트 컴퓨터가 나열되어 있습니다.

![클라이언트 상태 페이지의 스크린샷.](media/client-status.png)

필터로 테이블을 사용자 지정하여 특정 클라이언트 주소나 주소 범위, 특정 스토리지 대상 또는 개별 캐시 탑재 주소를 표시할 수 있습니다.

표에 표시된 연결 형식은 스토리지 대상에 대한 클라이언트 연결과 관련되어 있습니다.

* `azure_https_backend`는 표준 Azure Blob Storage 시스템에 대한 보안 클라이언트 연결입니다.
* `mount_backend`는 하드웨어 NFS 시스템 또는 NFS 지원 Blob 컨테이너에 대한 NFS 연결입니다.
* `nfs_backend`는 `mount_backend` 연결과 비슷하지만, 클라이언트 탑재 작업에서 시작된 연결 대신 NFS 내부 원격 프로시저 호출에서 사용하는 별도의 연결을 나타냅니다.

이러한 값은 클라이언트와 HPC Cache 간의 연결 문제를 디버깅할 때 유용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 메트릭 및 통계 도구](../azure-monitor/index.yml)에 대한 자세한 정보
* [캐시 관리](hpc-cache-manage.md)
