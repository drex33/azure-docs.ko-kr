---
title: Azure Data Studio를 사용하여 PostgreSQL 인스턴스 관리
description: Azure Data Studio를 사용하여 PostgreSQL 인스턴스 관리
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 1849e317c4245eb1aa96ff96f0ffac8bd8425299
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536983"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Data Studio를 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 관리


이 문서에서는 다음과 같이 방법을 설명합니다.
- 개요, 연결 문자열, 속성, 리소스 상태 등의 대시보드 보기를 사용하여 PostgreSQL 인스턴스 관리
- 데이터 및 스키마 작업

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [azdata, Azure Data Studio, Azure CLI 설치](install-client-tools.md)
- Azure Data Studio에서 **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** , **Azure Arc**, **PostgreSQL** 확장을 설치합니다.

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- [Azure Arc 데이터 컨트롤러](./create-data-controller-indirect-cli.md) 만들기
- Azure Data Studio 시작

## <a name="connect-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 연결

Azure Data Studio에서 노드 **Azure Arc 컨트롤러** 를 확장하고 **연결 컨트롤러** 단추를 선택합니다.

Azure 데이터 컨트롤러에 대한 연결 정보를 입력합니다.

- **컨트롤러 URL:**

    Kubernetes의 컨트롤러에 연결할 URL입니다. `https://<IP_address_of_the_controller>:<Kubernetes_port.` 형식으로 입력합니다. 예:

    ```console
    https://12.345.67.890:30080
    ```
- **사용자 이름:**

    컨트롤러에 연결하는 데 사용하는 사용자 계정의 이름입니다. `az login`을 실행할 때 일반적으로 사용하는 이름입니다. 일반적으로 psql에서 PostgreSQL 데이터베이스 엔진에 연결하는 데 사용하는 PostgreSQL 사용자의 이름은 아닙니다.
- **암호:** 컨트롤러에 연결하는 데 사용하는 사용자 계정의 암호입니다.


Azure Data Studio는 Arc 데이터 컨트롤러를 표시합니다. 컨트롤러를 확장하면 관리하는 PostgreSQL 인스턴스의 목록이 표시됩니다.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 관리

관리할 PostgreSQL 인스턴스를 마우스 오른쪽 단추로 클릭하고 [관리]를 선택합니다.

PostgreSQL 대시보드 보기:

여기에는 창 왼쪽에 다음과 같은 대시보드가 여러 개 나열되어 있습니다.

- **개요:** 사용자 인스턴스에 대한 요약 정보(예: 이름, PostgreSQL 관리 사용자 이름, Azure 구독 ID, 구성, 데이터베이스 엔진 버전, Grafana 및 Kibana의 엔드포인트)를 표시합니다.
- **연결 문자열:** PostgreSQL 인스턴스(예: psql, Node.js, PHP, Ruby 등...)에 연결하는 데 필요할 수 있는 다양한 연결 문자열을 표시합니다.
- **문제 진단 및 해결:** 문제 해결 Notebook을 확장할 때 인스턴스 문제를 해결하는 데 도움이 되는 다양한 리소스를 찾을 수 있는 방문 페이지입니다.
- **새 지원 요청:** 퍼블릭 미리 보기 알림을 시작하는 지원 서비스에 도움을 요청할 수 있는 방문 페이지입니다.

## <a name="work-with-your-data-and-schema"></a>데이터 및 스키마 작업

Azure Data Studio 창의 왼쪽에서 **서버** 노드를 확장합니다.

[연결 추가]를 선택하고 PostgreSQL 인스턴스에 대한 다음 연결 세부 정보를 입력합니다.
- **연결 형식:** PostgreSQL
- **서버 이름:** PostgreSQL 인스턴스의 이름을 입력합니다. 예: postgres01
- **인증 유형:** 암호
- **사용자 이름:** 예를 들어 표준/기본 PostgreSQL 관리 사용자 이름을 사용할 수 있습니다. 이 필드는 대/소문자를 구분합니다.
- **암호:** `az postgres arc-server endpoint -n postgres01` 명령 출력의 psql 연결 문자열에서 PostgreSQL 사용자 이름의 암호를 찾을 수 있습니다.
- **데이터베이스 이름:** 연결하려는 데이터베이스의 이름을 설정합니다. __기본값__ 으로 설정되도록 할 수 있습니다.
- **서버 그룹:** __기본값__ 으로 설정되도록 할 수 있습니다.
- **이름(선택 사항):** 비워 둘 수 있습니다.
- **고급:**
    - **호스트 IP 주소:** Kubernetes 클러스터의 공용 IP 주소입니다.
    - **포트:** PostgreSQL 인스턴스가 수신하는 포트입니다. 이 포트는 `az postgres arc-server endpoint -n postgres01` 명령 출력의 psql 연결 문자열 끝에서 확인할 수 있습니다. Kubernetes에서 수신하고 Azure Data Studio의 Azure 데이터 컨트롤러에 연결할 때 입력한 포트 30080은 아닙니다.
    - **기타 매개 변수:** 자체 명시적이어야 하며, 함께 표시되는 기본값/빈 값과 같이 사용할 수 있습니다.

**[확인]을 선택하고 [연결]** 을 선택하여 서버에 연결합니다.

연결되면 다음 여러 가지를 경험할 수 있습니다.
- **새 쿼리**
- **새 Notebook**
- **서버 표시를 확장하고 데이터베이스 내에서 개체를 찾아보거나 개체에 대해 작업합니다.**
- **...**

## <a name="next-step"></a>다음 단계
[서버 그룹 모니터링](monitor-grafana-kibana.md)