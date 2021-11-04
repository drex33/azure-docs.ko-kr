---
title: 청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기
description: 청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: b54d77c5abb5e8043368ad1e8bce3d3865e68fd7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558766"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기




## <a name="connectivity-modes---implications-for-billing-data"></a>연결 모드 - 청구 데이터에 대한 영향

Azure Arc 사용 데이터 서비스를 배포할 수 있는 두 가지 모드가 있습니다.

- **간접적으로 연결됨** - Azure에 직접 연결되지 않습니다. 데이터는 내보내기/업로드 프로세스를 통해서만 Azure로 전송됩니다.
- **직접 연결** -이 모드에서는 azure arc 사용 데이터 서비스가 배포 되는 Kubernetes 클러스터와 azure 간에 직접 연결을 제공 하기 위해 azure Arc 사용 Kubernetes 서비스에 대 한 종속성이 제공 됩니다. 이렇게 하면 azure에서 더 많은 기능을 사용할 수 있으며 azure PaaS에서 데이터 서비스를 관리 하는 것 처럼 Azure Portal를 사용 하 여 Azure Arc 사용 데이터 서비스를 관리할 수도 있습니다.  

[연결 모드](./connectivity.md)의 차이점에 대해 자세히 알아볼 수 있습니다.

간접 연결 모드에서 청구 데이터는 Azure Arc 데이터 컨트롤러에서 보안 파일로 주기적으로 내보낸 다음 Azure에 업로드되어 처리됩니다.  곧 출시될 직접 연결 모드에서는 서비스 비용을 거의 실시간으로 볼 수 있도록 청구 데이터가 약 1시간 동안 Azure로 자동 전송됩니다. 간접적으로 연결된 모드에서 데이터를 내보내고 업로드하는 프로세스는 스크립트를 사용하여 자동화할 수도 있고, 이를 수행할 서비스를 구축할 수도 있습니다.

## <a name="upload-billing-data-to-azure---indirectly-connected-mode"></a>Azure-간접 연결 모드로 청구 데이터 업로드

> [!NOTE]
> 사용량 (청구) 데이터의 업로드는 직접 연결 모드에서 자동으로 수행 됩니다. 다음 지침은 간접 연결 모드에만 해당 됩니다. 

청구 데이터를 Azure에 업로드하려면 먼저 다음이 수행되어야 합니다.

1. 아직 없는 경우 Azure Arc 지원 데이터 서비스를 만듭니다. 예를 들어 다음 중 하나를 만듭니다.
   - [Azure Arc에서 Azure SQL Managed Instance 만들기](create-sql-managed-instance.md)
   - [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)
2. 청구 원격 측정 수집 프로세스에서 일부 청구 데이터를 수집할 수 있도록 데이터 서비스를 만든 후 최소 2시간 동안 기다립니다.
3. [리소스 인벤토리, 사용 현황 데이터, 메트릭 및 Azure Monitor 로그 업로드](upload-metrics-and-logs-to-azure-monitor.md) 에 설명 된 단계에 따라 사용량/청구/로그 데이터를 업로드 하는 데 필요한 필수 구성 요소를 설정 하 고, [Azure에 대 한 업로드 사용 데이터](upload-usage-data.md) 를 진행 하 여 청구 데이터를 업로드 합니다. 


## <a name="view-billing-data-in-azure-portal"></a>Azure Portal에서 청구 데이터 보기

Azure Portal에서 청구 데이터를 보려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 화면 상단의 검색창에 **Cost Management** 를 입력하고 Cost Management 서비스를 클릭합니다.
1. **Cost Management 개요** 에서 **Cost Management** 탭을 클릭합니다.
1. 왼쪽에서 **비용 분석** 탭을 클릭합니다.
1. 보기 상단에서 **리소스별 비용** 단추를 클릭합니다.
1. 범위가 데이터 서비스 리소스가 만들어진 구독으로 설정되어 있는지 확인합니다.
1. 보기 상단 근처의 범위 선택기 옆에 있는 보기 드롭다운에서 **리소스별 비용** 을 선택합니다.
1. 날짜 필터가 **이번 달** 또는 데이터 서비스 리소스를 만든 시점을 감안할 때 적절한 다른 시간 범위로 설정되어 있는지 확인합니다.
1. 한 가지 유형의 Azure Arc 지원 데이터 서비스로만 필터링하려면 **필터 추가** 를 클릭하여 **리소스 종류** = `Microsoft.AzureArcData/<data service type>`별로 필터를 추가합니다.
1. 이제 만들어져 Azure에 업로드된 모든 리소스 목록이 표시됩니다. 청구 미터가 $0이므로 비용이 항상 $0임을 알 수 있습니다.

## <a name="download-billing-data"></a>청구 데이터 다운로드

Azure Portal에서 직접 청구 요약 데이터를 다운로드할 수 있습니다.

1. 위의 지침에 따라 도달한 동일한 **비용 분석 -> 리소스 종류별 보기** 보기를 보려면 상단 근처에 있는 다운로드 단추를 클릭합니다.
1. 다운로드 파일 형식(Excel 또는 CSV)을 선택하고 **데이터 다운로드** 단추를 클릭합니다.
1. 선택한 파일 유형에 따라 적절한 편집기에서 파일을 엽니다.

## <a name="export-billing-data"></a>청구 데이터 내보내기

청구 내보내기 작업을 만들어 주기적으로 자동으로 **자세한** 사용량 및 청구 데이터를 Azure Storage 컨테이너로 내보낼 수 있습니다. 이는 청구 기간 동안 지정된 인스턴스가 청구된 시간과 같은 청구 세부 정보를 확인하려는 경우에 유용합니다.

청구 내보내기 작업을 설정하려면 다음 단계를 따릅니다.

1. 왼쪽에서 **내보내기** 를 클릭합니다.
1. **추가** 를 클릭합니다.
1. 이름과 내보내기 빈도를 입력하고 다음을 클릭합니다.
1. 새 스토리지 계정을 만들거나 기존 계정을 사용하도록 선택하고 청구 데이터 파일을 내보낼 스토리지 계정, 컨테이너 및 디렉터리 경로를 지정하는 양식을 작성하고 다음을 클릭합니다.
1. **만들기** 를 클릭합니다.

청구 데이터 내보내기 파일은 약 4시간 후에 사용할 수 있으며 청구 내보내기 작업을 만들 때 지정한 일정에 따라 내보내집니다.

내보낸 청구 데이터 파일을 보려면 다음 단계를 따릅니다.

Azure Portal에서 청구 데이터 파일의 유효성을 검사할 수 있습니다. 

> [!IMPORTANT]
> 청구 내보내기 작업을 만든 후 다음 단계를 진행하기 전에 4시간 동안 기다립니다.

1. 포털 상단의 검색창에 **스토리지 계정** 을 입력하고 **스토리지 계정** 을 클릭합니다.
3. 위에서 청구 내보내기 작업을 만들 때 지정한 스토리지 계정을 클릭합니다.
4. 왼쪽에서 컨테이너를 클릭합니다.
5. 위에서 청구 내보내기 작업을 만들 때 지정한 컨테이너를 클릭합니다.
6. 위에서 청구 내보내기 작업을 만들 때 지정한 폴더를 클릭합니다.
7. 생성된 폴더 및 파일을 드릴다운하고 생성된 .csv 파일 중 하나를 클릭합니다.
8. 파일을 로컬 다운로드 폴더에 저장하는 **다운로드** 단추를 클릭합니다.
9. Excel과 같은 .csv 파일 뷰어를 사용하여 파일을 엽니다.
10. **리소스 유형** = `Microsoft.AzureArcData/<data service resource type`이 있는 행만 표시하도록 결과를 필터링합니다.
11. UsageQuantity 열에서 현재 24시간 동안 인스턴스가 사용된 시간을 볼 수 있습니다.
