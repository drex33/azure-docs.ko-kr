---
title: 모든 Azure SQL Database 진단 및 감사 로그를 Azure Sentinel에 연결
description: Azure Policy를 사용하여 Azure SQL Database 진단 로그 및 보안 감사 로그를 Azure Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0f1cc19aac18f22d86e0ed56eded168c78a26631
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535862"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Azure SQL Database 진단 및 감사 로그 연결

Azure SQL은 사용자의 개입 없이도 업그레이드, 패치, 백업 및 모니터링과 같은 데이터베이스 관리 기능 대부분을 처리하는 완전 관리형 Platform as a Service(PaaS) 데이터베이스 엔진입니다. 

Azure SQL Database 커넥터를 사용하여 데이터베이스의 감사 및 진단 로그를 Azure Sentinel으로 스트림할 수 있으므로 모든 인스턴스에서 작업을 지속적으로 모니터링할 수 있습니다.

- 진단 로그를 연결하면 여러 데이터 형식의 데이터베이스 진단 로그를 Azure Sentinel 작업 영역으로 보낼 수 있습니다.

- 감사 로그를 연결하면 서버 수준에서 모든 Azure SQL 데이터베이스의 보안 감사 로그를 스트림할 수 있습니다.

[Azure SQL Database 진단 원격 분석](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) 및 [Azure SQL 서버 감사](../azure-sql/database/auditing-overview.md)에 대해 자세히 알아보세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 감사 로그를 연결하려면 Azure SQL Server 감사 설정에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- Azure Policy를 사용하여 Azure SQL 데이터베이스 및 서버 리소스에 로그 스트리밍 정책을 적용하려면 정책 할당 범위에 대한 Owner 역할이 있어야 합니다.

## <a name="connect-to-an-azure-sql-database"></a>Azure SQL 데이터베이스에 연결

이 커넥터는 Azure Policy를 사용하여 단일 Azure SQL 로그 스트리밍 구성을 범위로 정의된 인스턴스 컬렉션에 적용합니다. Azure SQL Database 커넥터는 Azure Sentinel에 두 가지 형식의 로그를 전송합니다. 즉, SQL 데이터베이스의 진단 로그와 SQL 서버 수준에서 감사 로그를 전송합니다. Azure SQL 데이터베이스 및 서버에서 **데이터 형식** 아래 커넥터 페이지의 왼쪽에 있는 수집 로그 형식을 확인할 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure SQL Databases** 를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 커넥터 페이지의 **구성** 섹션에서 연결할 수 있는 두 가지 로그 범주를 확인합니다.

### <a name="connect-diagnostics-logs"></a>진단 로그 연결

1. **Azure SQL 데이터베이스에서 진단 로그 대규모 스트림하기** 확장

1. **Azure Policy 할당 시작 마법사** 단추를 선택합니다.

    정책 할당 마법사가 열리고 **배포 - Log Analytics 작업 영역에 SQL 데이터베이스용 진단 설정 구성** 이라는 새 정책을 만들 준비가 됩니다.

    1. **기본** 탭에서 **범위** 아래의 세 점이 있는 단추를 클릭하여 구독을 선택하고 필요에 따라 리소스 그룹을 선택합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭에서 처음 두 설정을 그대로 둡니다. **Log Analytics 작업 영역** 드롭다운 목록에서 Azure Sentinel 작업 영역을 선택합니다. 나머지 드롭다운 필드는 사용 가능한 진단 로그 형식을 나타냅니다. 수집하려는 모든 로그 형식을 “True”로 표시된 채로 둡니다.

    1. 정책은 나중에 추가되는 리소스에 적용됩니다. 기존 리소스에도 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 생성** 확인란에 표시합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

### <a name="connect-audit-logs"></a>감사 로그 연결

1. 커넥터 페이지로 돌아가서 **서버 수준에서 Azure SQL 데이터베이스의 감사 로그 대규모 스트림하기** 를 확장합니다.

1. **Azure Policy 할당 시작 마법사** 단추를 선택합니다.

    정책 할당 마법사가 열리고 **배포 - Log Analytics 작업 영역에 SQL Databases용 감사 설정 구성** 이라는 새 정책을 만들 준비가 됩니다.

    1. **기본** 탭에서 **범위** 아래의 세 점이 있는 단추를 클릭하여 구독을 선택하고 필요에 따라 리소스 그룹을 선택합니다. 설명을 추가할 수도 있습니다.

    1. **매개 변수** 탭의 **Log Analytics 작업 영역** 드롭다운 목록에서 Azure Sentinel 작업 영역을 선택합니다. **효과** 설정을 그대로 둡니다.

    1. 정책은 나중에 추가되는 리소스에 적용됩니다. 기존 리소스에도 정책을 적용하려면 **수정** 탭을 선택하고 **수정 작업 생성** 확인란에 표시합니다.

    1. **검토 + 만들기** 탭에서 **만들기** 를 클릭합니다. 이제 사용자가 선택한 범위에 정책이 할당됩니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 지난 14일 동안 특정 시점에 데이터를 수집한 경우에만 연결 상태 표시기(데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆에 연결 아이콘)가 *연결됨*(녹색)으로 표시됩니다. 데이터 수집 없이 14일이 경과되면 커넥터는 연결 끊김 상태로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Policy를 사용하여 Azure SQL Database 진단 및 감사 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
