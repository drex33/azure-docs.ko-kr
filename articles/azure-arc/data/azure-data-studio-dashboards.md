---
title: Azure Data Studio 대시보드
description: Azure Data Studio 대시보드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: cea97bab303ce2d009cecc67ed30ec89b0992118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554339"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio 대시보드

[Azure Data Studio](/sql/azure-data-studio/what-is)는 Azure Arc 리소스에 대한 정보를 볼 수 있는 Azure Portal과 유사한 환경을 제공합니다.  이러한 보기를 **대시보드** 라고 하며, Azure Portal에서 지정된 리소스에 대해 볼 수 있는 것과 유사한 레이아웃 및 옵션을 제공하지만, Azure에 사용할 수 있는 연결이 없는 경우 사용자 환경에서 로컬로 해당 정보를 볼 수 있는 유연성을 제공합니다.


## <a name="connecting-to-a-data-controller"></a>데이터 컨트롤러에 연결

### <a name="prerequisites"></a>필수 구성 요소

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 다운로드
- Azure Arc 확장이 설치됨



### <a name="connect"></a>연결

1. Azure Data Studio 열기
2. 왼쪽의 **연결** 탭 선택
3. **Azure Arc 컨트롤러** 라는 패널을 확장 합니다.
4. **커넥트 컨트롤러** 단추를 클릭 합니다. 그러면 오른쪽에 블레이드가 열립니다.
5. 기본적으로 Azure Data Studio는 기본 디렉터리의 kube.config 파일에서 읽으려고 시도 하 고 사용 가능한 kubernetes 클러스터 컨텍스트를 나열 하 고 현재 클러스터 컨텍스트를 미리 선택 합니다. 연결할 올바른 클러스터 인 경우 **네임 스페이스** 입력에 Azure Arc 데이터 컨트롤러가 배포 되는 네임 스페이스를 입력 합니다. Azure Arc 데이터 컨트롤러가 배포 되는 네임 스페이스를 검색 해야 하는 경우 kubernetes 클러스터에서를 실행할 수 있습니다 ```kubectl get datacontrollers -A``` . 
6. 선택적으로 **이름** 에 대 한 입력에 Azure Arc 데이터 컨트롤러의 표시 이름을 추가 합니다.
7. **연결** 을 선택합니다.


이제 데이터 컨트롤러에 연결되었으므로 데이터 컨트롤러 및 모든 SQL 관리형 인스턴스 또는 PostgreSQL 하이퍼스케일 서버 그룹 리소스에 대한 대시보드를 볼 수 있습니다.

## <a name="view-the-data-controller-dashboard"></a>데이터 컨트롤러 대시보드 보기

**Arc 컨트롤러** 확장 가능 패널의 연결 패널에서 데이터 컨트롤러를 마우스 오른쪽 단추로 클릭하고 **관리** 를 선택합니다.

여기에서 이름, 지역, 연결 모드, 리소스 그룹, 구독, 컨트롤러 엔드포인트 및 네임 스페이스와 같은 데이터 컨트롤러 리소스에 대한 세부 정보를 볼 수 있습니다.  데이터 컨트롤러에서 관리하는 모든 관리 데이터베이스 리소스 목록도 볼 수 있습니다.

레이아웃은 Azure Portal의 레이아웃과 비슷하다는 것을 알 수 있습니다.

간편하게 + 새 인스턴스 단추를 클릭하여 SQL 관리형 인스턴스 또는 PostgreSQL 하이퍼스케일 서버 그룹 만들기를 시작할 수 있습니다.

Azure Portal에서 열기 단추를 클릭하여 이 데이터 컨트롤러에 대한 컨텍스트에서 Azure Portal를 열 수도 있습니다.

## <a name="view-the-sql-managed-instance-dashboards"></a>SQL 관리형 인스턴스 대시보드 보기

일부 SQL 관리형 인스턴스를 만든 경우 해당 인스턴스를 관리하는 데이터 컨트롤러 아래에 있는 Azure 데이터 컨트롤러 확장 가능 패널의 연결 패널에 인스턴스가 나열되는 것을 볼 수 있습니다.

지정된 인스턴스에 대한 SQL 관리형 인스턴스 대시보드를 보려면 인스턴스를 마우스 오른쪽 단추로 클릭하고 관리를 선택합니다.

연결 패널이 오른쪽에 표시되고 해당 SQL 인스턴스에 연결하는 데 사용할 로그인/암호를 묻는 메시지가 표시됩니다. 연결 정보를 알고 있는 경우 해당 정보를 입력하고 연결을 클릭합니다.  모르는 경우 취소를 클릭할 수 있습니다.  어느 쪽이든 연결 패널을 닫으면 대시보드로 이동합니다.

개요 탭에서 리소스 그룹, 데이터 컨트롤러, 구독 ID, 상태, 지역 등 SQL 관리형 인스턴스에 대한 세부 정보를 볼 수 있습니다.  해당 SQL 관리형 인스턴스에 대한 컨텍스트에서 Grafana 또는 Kibana 대시 보드로 이동할 수 있는 링크도 볼 수 있습니다.

SQL 관리형 인스턴스에 연결할 수 있는 경우 여기에서 추가 정보를 볼 수 있습니다.

여기에서 SQL 관리형인스턴스를 삭제하거나 Azure Portal을 열어 Azure Portal에서 SQL 관리형 인스턴스를 볼 수 있습니다.

왼쪽의 연결 문자열 탭을 클릭하면 해당 SQL 관리형 인스턴스에 대해 미리 생성된 연결 문자열 목록을 볼 수 있으므로 다양한 다른 애플리케이션 또는 코드에 쉽게 복사하고 붙여넣을 수 있습니다.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>PostgreSQL 하이퍼스케일 서버 그룹 대시보드 보기

일부 PostgreSQL 하이퍼스케일 서버 그룹을 만든 경우 해당 인스턴스를 관리하는 데이터 컨트롤러 아래에 있는 Azure 데이터 컨트롤러 확장 가능 패널의 연결 패널에 인스턴스가 나열되는 것을 볼 수 있습니다.

지정된 서버 그룹에 대한 PostgreSQL 하이퍼스케일 서버 그룹 대시보드를 보려면 서버 그룹을 마우스 오른쪽 단추로 클릭하고 관리를 선택합니다.

개요 탭에서 리소스 그룹, 데이터 컨트롤러, 구독 ID, 상태, 지역 등 서버 그룹에 대한 세부 정보를 볼 수 있습니다.  해당 서버 그룹에 대한 컨텍스트에서 Grafana 또는 Kibana 대시 보드로 이동할 수 있는 링크도 볼 수 있습니다.

여기에서 서버 그룹을 삭제하거나 Azure Portal을 열어 Azure Portal에서 서버 그룹을 볼 수 있습니다.

왼쪽의 연결 문자열 탭을 클릭하면 해당 서버 그룹에 대해 미리 생성된 연결 문자열 목록을 볼 수 있으므로 다양한 다른 애플리케이션 또는 코드에 쉽게 복사하고 붙여넣을 수 있습니다.

왼쪽의 속성 탭을 클릭하면 추가 세부 정보를 볼 수 있습니다.

왼쪽의 리소스 상태 탭을 클릭하면 해당 서버 그룹의 현재 상위 수준 상태를 볼 수 있습니다.

왼쪽의 문제 진단 및 해결 탭을 클릭하면 PostgreSQL 문제 해결 노트북을 시작할 수 있습니다.

왼쪽의 새 지원 요청 탭을 클릭하면 서버 그룹에 대한 컨텍스트에서 Azure Portal을 시작하고 거기에서 Azure 지원 요청을 만들 수 있습니다.