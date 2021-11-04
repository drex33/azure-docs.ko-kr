---
title: Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기
description: Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fe594e7459f32d0639c39b9f694f31e7816ca118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558747"
---
# <a name="view-azure-arc-data-controller-resource-in-azure-portal"></a>Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기

Azure Portal Azure Arc 데이터 컨트롤러를 보려면 kubernetes 클러스터의 사용량/메트릭/로그 데이터 중 하나를 내보내고 Azure에 업로드해야 합니다. 

## <a name="direct-connected-mode"></a>직접 연결 모드
Azure Arc 데이터 컨트롤러가 **직접** 연결된 모드로 배포되면 사용량 현황 데이터가 Azure에 자동으로 업로드되고 kubernetes 리소스가 Azure에 프로젝터됩니다.

## <a name="indirect-connected-mode"></a>간접 연결 모드
**간접** 연결 모드에서는 사용량 현황 데이터, 메트릭 및 로그를 Azure에 업로드 설명한 대로 [사용량 현황 데이터, 메트릭](upload-metrics-and-logs-to-azure-monitor.md)또는 로그 중 하나 이상을 내보내고 Azure에 업로드해야 합니다. 이 작업은 Azure에서 적절한 리소스를 만듭니다.

## <a name="azure-portal"></a>Azure Portal

Azure 또는 [업로드된 사용량에](view-billing-data-in-azure.md)대한 첫 번째 [메트릭 또는 로그 업로드를](upload-metrics-and-logs-to-azure-monitor.md) 완료한 후 Azure Portal Azure Arc 데이터 컨트롤러와 Azure Arc 사용 가능한 SQL 관리형 인스턴스 또는 Azure Arc 지원 Postgres 하이퍼스케일 서버 리소스를 볼 수 있습니다.

URL을 사용하여 Azure Portal  [https://portal.azure.com](https://portal.azure.com) 엽니다.

검색 창에서 이름으로 데이터 컨트롤러를 검색해서 클릭합니다.

