---
title: Azure Arc 지원 데이터 서비스란?
description: Azure Arc 지원 데이터 서비스를 소개합니다.
ms.custom: references_regions
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 5a2bd61c2e59e5933361cc5d64462ba50a12d836
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737119"
---
# <a name="what-are-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스란?

Azure Arc를 통해 선택한 인프라 및 Kubernetes를 사용하여 온-프레미스, 에지 및 퍼블릭 클라우드 환경의 Azure 데이터 서비스를 실행할 수 있습니다.

현재 다음과 같은 Azure Arc 지원 데이터 서비스를 사용할 수 있습니다. 

- SQL Managed Instance
- PostgreSQL 하이퍼스케일(미리 보기)

Azure Arc 지원 데이터 서비스가 하이브리드 작업 환경을 지원하는 방식에 대한 소개는 다음 소개 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows//Inside-Azure-for-IT/Choose-the-right-data-solution-for-your-hybrid-environment/player?format=ny]

## <a name="always-current"></a>항상 최신

Azure Arc 지원 SQL 관리형 인스턴스 및 Azure Arc 지원 PostgreSQL 하이퍼스케일과 같은 Azure Arc 지원 데이터 서비스는 Azure의 환경과 비슷한 서비스 패치와 새로운 기능이 포함된 업데이트를 자주 받습니다. 사용자에게는 Microsoft Container Registry의 업데이트가 제공되며, 배포 상황은 사용자의 정책을 기반으로 사용자가 설정합니다. 이러한 방식으로 온-프레미스 데이터베이스는 제어를 유지하는 동시에 최신 상태를 유지할 수 있습니다. Azure Arc 지원 데이터 서비스는 구독 서비스이므로 더 이상 데이터베이스에 대한 지원이 종료되는 상황을 겪지 않아도 됩니다.

## <a name="elastic-scale"></a>탄력적 확장

온-프레미스의 클라우드와 유사한 탄력성을 통해 인프라의 사용 가능한 용량에 따라 Azure에서와 거의 동일한 방식으로 데이터베이스를 확장 또는 축소할 수 있습니다. 이 기능은 어떤 규모에서든 데이터를 실시간으로 수집 및 쿼리해야 하는 시나리오를 포함하여 일시적 요구 사항이 있는 버스트 시나리오를 1초 미만의 응답 시간으로 충족할 수 있습니다. 또한 Azure Database for PostgreSQL 하이퍼스케일의 고유한 하이퍼 크기 조정 배포 옵션을 사용하여 데이터베이스 인스턴스를 스케일 아웃할 수도 있습니다. 이 기능은 고유한 스케일 *아웃* 읽기 및 쓰기를 사용하여 데이터 워크로드에 용량 최적화를 추가적으로 제공합니다.

## <a name="self-service-provisioning"></a>셀프서비스 프로비저닝

또한 Azure Arc는 빠른 배포 및 대규모 자동화와 같은 다른 클라우드 이점도 제공합니다. Kubernetes 기반 오케스트레이션 덕분에 GUI 또는 CLI 도구를 사용하여 몇 초만에 데이터베이스를 배포할 수 있습니다.

## <a name="unified-management"></a>통합 관리

이제 Azure Portal, Azure Data Studio, Azure CLI(`az`)(`arcdata` 확장 포함)와 같은 익숙한 도구를 사용하여 Azure Arc로 배포된 모든 데이터 자산에 대한 통합된 보기를 얻을 수 있습니다. 사용자 환경 및 Azure에서 다양한 관계형 데이터베이스를 보고 관리할 수 있을 뿐만 아니라, 기본 인프라 용량과 상태를 분석하기 위해 Kubernetes API에서 로그와 원격 분석을 가져올 수 있습니다. 이제는 지역화된 로그 분석 및 성능 모니터링 외에도 전체 자산 전반에 걸친 종합적인 운영 인사이트에도 Azure Monitor를 활용할 수 있습니다.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="disconnected-scenario-support"></a>연결되지 않은 시나리오 지원

셀프서비스 프로비저닝, 자동화된 백업/복원 및 모니터링과 같은 대부분의 서비스는 Azure에 대한 직접 연결의 사용 여부에 관계 없이 인프라에서 로컬로 실행할 수 있습니다. Azure에 직접 연결하면 Azure Monitor와 같은 다른 Azure 서비스와의 통합을 위한 추가 옵션이 제공되며, 전 세계 어디에서나 Azure Portal 및 Azure Resource Manager API를 사용하여 Azure Arc 지원 데이터 서비스를 관리할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

다음 표에서는 현재 Arc 지원 데이터 서비스에 대해 지원되는 시나리오를 설명합니다.

|Azure 지역  |직접 연결 모드  |간접 연결 모드  |
|---------|---------|---------|
|미국 동부|사용 가능|사용 가능
|미국 동부 2|사용 가능|사용 가능
|미국 서부 2|사용 가능|사용 가능
|미국 중부|사용할 수 없음|사용 가능
|미국 중남부|사용 가능|사용 가능
|영국 남부|사용 가능|사용 가능
|프랑스 중부|사용 가능|사용 가능
|서유럽 |사용 가능 |사용 가능
|북유럽|사용 가능|사용 가능
|일본 동부|사용할 수 없음|사용 가능
|한국 중부|사용할 수 없음|사용 가능
|동아시아|사용할 수 없음|사용 가능
|동남아시아|사용 가능|사용 가능
|오스트레일리아 동부|사용 가능|사용 가능

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**  
> AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)를 사용하여 빠르게 시작하세요.
>
>또한 배포하기 쉬운 모든 Azure Arc 관련 항목의 샌드박스인 [Jumpstart ArcBox](https://azurearcjumpstart.io/azure_jumpstart_arcbox/)를 배포합니다. ArcBox는 단일 Azure 구독 및 리소스 그룹 내에서 완전히 자체 포함되도록 설계되었으므로 사용 가능한 Azure 구독을 초과하지 않고 사용 가능한 모든 Azure Arc 지원 기술을 쉽게 실습할 수 있습니다.

[클라이언트 도구 설치](install-client-tools.md)

[Azure Arc 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)(먼저 클라이언트 도구를 설치해야 함)

[Azure Arc에서 Azure SQL 관리형 인스턴스 만들기](create-sql-managed-instance.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)

[Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
