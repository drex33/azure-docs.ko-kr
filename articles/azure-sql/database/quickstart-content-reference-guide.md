---
title: 단일 데이터베이스 빠른 시작 콘텐츠 참조
description: Azure SQL Database를 빠르게 시작하는 데 도움이 되는 모든 빠른 시작의 콘텐츠 참조를 찾습니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: LitKnd
ms.author: kendralittle
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 66964b542dfadaa68892ff044c5098468571758e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367836"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Azure SQL Database의 단일 데이터베이스로 빠르게 시작하기
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[단일 데이터베이스](../index.yml)는 완전 관리형 PaaS DbaaS(platform as a service Database as a Service)이며, 최신 클라우드 기반 애플리케이션에 이상적인 스토리지 엔진입니다. 이 섹션에서는 Azure SQL Database에서 신속하게 단일 데이터베이스를 구성하고 만드는 방법을 알아봅니다.

## <a name="quickstart-overview"></a>빠른 시작 개요

이 섹션에서는 단일 데이터베이스를 신속하게 시작하는 데 사용할 수 있는 문서에 대한 개요를 알아봅니다. 다음 빠른 시작을 통해 신속하게 단일 데이터베이스를 만들고 서버 수준 방화벽 규칙을 구성한 다음, `.bacpac` 파일을 사용하여 데이터베이스를 새 단일 데이터베이스로 가져올 수 있습니다.

- [Azure Portal을 사용하여 단일 데이터베이스 만들기](single-database-create-quickstart.md)
- 데이터베이스를 만든 후에는 [방화벽 규칙을 구성하여 데이터베이스를 보호](firewall-create-server-level-portal-quickstart.md)해야 합니다.
- SQL Server에 Azure SQL Database로 마이그레이션할 기존 데이터베이스가 있는 경우, SQL Server에서 데이터베이스를 분석하고 마이그레이션을 차단할 수 있는 문제를 찾는 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 설치해야 합니다. 문제가 없으면 데이터베이스를 `.bacpac` 파일로 내보내서 [Azure Portal이나 SqlPackage를 사용하여 가져오면](database-import.md) 됩니다.


## <a name="automating-management-operations"></a>관리 작업 자동화

PowerShell 또는 Azure CLI를 사용하여 데이터베이스를 만들고, 구성하고, 크기를 조정할 수 있습니다.

- [PowerShell](scripts/create-and-configure-database-powershell.md) 또는 [Azure CLI](scripts/create-and-configure-database-cli.md)를 사용하여 단일 데이터베이스 만들기 및 구성
- [PowerShell](scripts/monitor-and-scale-database-powershell.md) 또는 [Azure CLI](scripts/monitor-and-scale-database-cli.md)를 사용하여 단일 데이터베이스 업데이트 및 리소스 규모 조정

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>가동 중지 시간을 최소화하면서 단일 데이터베이스 마이그레이션

이러한 빠른 시작에 따라 `.bacpac` 파일을 사용하여 신속하게 데이터베이스를 만들거나 Azure로 가져올 수 있습니다. 그러나 `.bacpac` 및 `.dacpac` 파일은 서로 다른 버전의 SQL Server 및 Azure SQL 내에서 데이터베이스를 신속하게 이동하거나 DevOps 파이프라인에서 연속 통합을 구현하도록 설계되었습니다. 그러나 이 방법은 가동 중지 시간을 최소화하면서 프로덕션 데이터베이스를 마이그레이션하도록 설계되지는 않았습니다. 새 데이터 추가를 중지하고, 원본 데이터베이스를 `.bacpac` 파일로 내보낸 다음, Azure SQL Database로 가져오는 작업이 완료될 때까지 기다려야 합니다. 이 모든 대기 시간은 애플리케이션의 가동 중지 시간으로 이어지고, 특히 대형 데이터베이스에서 더 많이 발생합니다. 프로덕션 데이터베이스를 이동하려면 마이그레이션 가동 중지 시간을 최소화할 수 있는 더 좋은 마이그레이션 방법이 필요합니다. 이렇게 하려면 [DMS(Data Migration Service)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json)를 사용하여 최소한의 가동 중지 시간으로 데이터베이스를 마이그레이션하세요. DMS는 이를 위해 원본 데이터베이스의 변경 내용을 복원 중인 단일 데이터베이스에 점진적으로 푸시합니다. 이 방식을 사용하면 가동 중지 시간을 최소화하면서 원본에서 대상 데이터베이스로 애플리케이션을 신속하게 전환할 수 있습니다.

## <a name="hands-on-learning-modules"></a>실습 학습 모듈

다음 Microsoft Learn 모듈을 사용하면 Azure SQL Database에 대해 무료로 배울 수 있습니다.

- [SQL Database에 데이터베이스를 프로비저닝하여 애플리케이션 데이터 저장](/learn/modules/provision-azure-sql-db/)
- [Azure SQL Database의 데이터베이스를 쿼리하는 ASP.NET 애플리케이션 개발 및 구성](/learn/modules/develop-app-that-queries-azure-sql/)
- [Azure SQL Database에서 단일 데이터베이스 보호](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database에서 지원되는 기능의 개략적인 목록](features-comparison.md)을 찾습니다.
- [데이터베이스를 더 안전하게 보호](secure-database-tutorial.md)하는 방법을 알아봅니다.
- [Azure SQL Database에서 단일 데이터베이스를 사용하는 방법](how-to-content-reference-guide.md)에서 고급 작업 방법을 자세히 알아보세요.
- [PowerShell](powershell-script-content-guide.md) 및 [Azure CLI](az-cli-script-samples-content-guide.md)로 작성된 샘플 스크립트를 찾아봅니다.
- 데이터베이스를 구성하는 데 사용할 수 있는 [관리 API](single-database-manage.md)에 대해 자세히 알아봅니다.
- [온-프레미스 데이터베이스에 적합한 Azure SQL Database 또는 Azure SQL Managed Instance SKU를 식별하세요](/sql/dma/dma-sku-recommend-sql-db/).