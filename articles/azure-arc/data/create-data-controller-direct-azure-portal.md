---
title: Azure Portal에서 Azure Arc 데이터 컨트롤러 배포 | 직접 연결 모드
description: Azure Portal에서 데이터 컨트롤러를 직접 연결 모드에서 배포하는 방법을 설명합니다.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: 85a7ca3dc50cb95fc545be6b486a300247837450
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714906"
---
#  <a name="create-azure-arc-data-controller-from-azure-portal---direct-connectivity-mode"></a>Azure Portal에서 Azure Arc 데이터 컨트롤러 만들기 - 직접 연결 모드

이 문서에서는 Azure Portal에서 직접 연결 모드로 Azure Arc 데이터 컨트롤러를 배포하는 방법을 설명합니다. 

## <a name="complete-prerequisites"></a>필수 조건 완료

시작하기 전에 [데이터 컨트롤러 배포 - 직접 연결 모드 - 필수 조건](create-data-controller-direct-prerequisites.md)에서 필수 조건을 완료 했는지 확인합니다.

## <a name="deploy-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 배포

Azure Arc 데이터 컨트롤러 만들기 흐름은 다음 방법 중 하나로 Azure Portal에서 시작할 수 있습니다.

- Azure Portal의 검색 창에서 "Azure Arc 데이터 컨트롤러"를 검색하고 "+ 만들기"를 선택합니다.
- Azure Arc 지원 Kubernetes 클러스터의 개요 페이지에서
  - 설정에서 “확장”을 선택합니다.
  - 확장 개요 페이지에서 "추가"를 선택한 다음 "Azure Arc 데이터 컨트롤러"를 선택합니다.
  - Azure Arc 데이터 컨트롤러 마켓플레이스 갤러리에서 만들기를 선택합니다.
  
이러한 작업 중 하나를 수행하면 흐름 만들기의 Azure Arc 데이터 컨트롤러 필수 구성 요소 페이지로 이동해야 합니다.

- Azure Arc 지원 Kubernetes 클러스터(직접 연결 모드) 옵션이 선택되어 있는지 확인합니다. "다음: 데이터 컨트롤러 세부 정보"를 선택합니다.
- **데이터 컨트롤러 세부 정보** 페이지에서:
  - Azure Arc 데이터 컨트롤러가 프로젝션될 Azure 구독 및 리소스 그룹을 선택합니다.
  - 데이터 컨트롤러의 **이름** 을 입력합니다.
  - 미리 만들어진 **사용자 지정 위치** 를 선택하거나 "새로 만들기"를 선택하여 새 사용자 지정 위치를 만듭니다. 새 사용자 지정 위치를 만들기로 선택한 경우 새 사용자 지정 위치의 이름을 입력하고 드롭다운에서 Azure Arc 지원 Kubernetes 클러스터를 선택합니다. 그런 다음 새 사용자 지정 위치와 연결할 네임스페이스를 입력하고 마지막으로 새 사용자 지정 위치 만들기 창에서 만들기를 선택합니다. [사용자 지정 위치](../kubernetes/conceptual-custom-locations.md)에 대해 자세히 알아보기
  - **Kubernetes 구성** - 드롭다운에서 Kubernetes 배포와 가장 일치하는 Kubernetes 구성 템플릿을 선택합니다. 고유한 설정을 사용하도록 선택하거나 사용하려는 사용자 지정 프로필이 있는 경우 드롭다운에서 사용자 지정 템플릿 옵션을 선택합니다. 오른쪽에 열리는 블레이드에서 Docker 자격 증명, 리포지토리 정보, 이미지 태그, 이미지 풀 정책, 인프라 유형, 데이터에 대한 스토리지 설정, 로그 및 해당 크기, 서비스 유형, 컨트롤러 및 관리 프록시에 대한 포트의 세부 정보를 입력합니다. 모든 필수 정보가 제공되면 적용을 선택합니다. 블레이드 상단에서 "템플릿(JSON) 업로드"를 선택하여 고유한 템플릿 파일을 업로드하도록 선택할 수도 있습니다. 사용자 지정 설정을 사용하고 해당 설정의 복사본을 다운로드하려면 "이 템플릿 다운로드(JSON)"를 사용하세요. [사용자 지정 구성 프로필](create-custom-configuration-template.md)에 대해 자세한 알아보기
  - 환경에 적합한 **서비스 유형** 을 선택합니다.
  - **메트릭 및 로그 대시보드 자격 증명** - Grafana 및 Kibana 대시보드의 자격 증명을 입력합니다.
  - 필요한 모든 정보를 제공한 후 "다음: 추가 설정" 단추를 선택하여 계속 진행합니다.
- **추가 설정** 페이지에서:
  - **메트릭 업로드:** 이 옵션을 선택하여 필요에 따라 메트릭을 Azure Monitor에 업로드하여 메트릭을 분석 및 집계하거나, 경고를 생성하거나, 알림을 보내거나, 자동화된 작업을 트리거할 수 있습니다. 필요한 **모니터링 메트릭 게시자** 역할이 확장의 관리 ID에 부여됩니다. 
  - **로그 업로드:** 기존 Log Analytics 작업 영역에 로그를 자동으로 업로드하려면 이 옵션을 선택합니다. Log Analytics 작업 영역 ID 및 Log Analytics 공유 액세스 키를 입력합니다. 
  - 계속하려면 "다음: 태그"를 선택합니다.
- **태그** 페이지에서 태그의 이름과 값을 입력하고 "다음: 검토 + 만들기"를 선택합니다.
- **검토 + 만들기** 페이지에서 배포 요약을 확인합니다. 모든 설정이 올바른지 확인하고 "만들기"를 선택하여 Azure Arc 데이터 컨트롤러 배포를 시작합니다.

## <a name="monitor-the-creation-from-azure-portal"></a>Azure Portal에서 만들기 모니터링

이전 단계에서 "만들기" 단추를 선택하면 Azure Arc 데이터 컨트롤러 배포 진행 상황을 보여 주는 Azure 배포 개요 페이지가 시작됩니다.

## <a name="monitor-the-creation-from-your-kubernetes-cluster"></a>Kubernetes 클러스터에서 만들기 모니터링

Azure Arc 데이터 컨트롤러 배포의 진행 상황은 다음과 같이 모니터링할 수 있습니다.

- 클러스터에서 ```kubectl get crd ```를 실행하여 CRD가 만들어졌는지 확인합니다.  
- 클러스터에서 ```kubectl get ns```를 실행하여 네임스페이스가 만들어졌는지 확인합니다.
- ```az customlocation list --resource-group <resourcegroup> -o table```을 실행하여 사용자 지정 위치가 만들어졌는지 확인합니다. 
- ```kubectl get pods -ns <namespace>```를 실행하여 Pod 배포 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 SQL Managed Instance 만들기](create-sql-managed-instance.md)

[Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)
