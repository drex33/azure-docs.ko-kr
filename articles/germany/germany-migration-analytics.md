---
title: Azure 분석 리소스, Azure 독일을 글로벌 Azure로 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 분석 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 93630f536127971e922ddceaf5d1e5fa82ea3ad4
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "122535448"
---
# <a name="migrate-analytics-resources-to-global-azure"></a>글로벌 Azure로 분석 리소스 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]


이 문서에는 Azure 독일에서 글로벌 Azure로 Azure 분석 리소스를 마이그레이션하는 데 도움이 되는 정보가 있습니다.
  
## <a name="event-hubs"></a>Event Hubs

Azure Event Hubs 리소스를 Azure 독일에서 글로벌 Azure로 직접 마이그레이션할 수 없습니다. Event Hubs 서비스에는 데이터 내보내기 또는 가져오기 기능이 없습니다. 하지만 Event Hubs 리소스를 [템플릿으로](../azure-resource-manager/templates/export-template-portal.md) 내보낼 수 있습니다. 그런 다음, 글로벌 Azure 지역에 대해 내보낸 템플릿을 조정하고 리소스를 다시 만듭니다.

> [!NOTE]
> Event Hubs 템플릿 내보내기는 데이터(예: 메시지)를 복사하지 않습니다. 템플릿을 내보내는 경우에만 Event Hubs 메타데이터를 다시 만듭니다.

> [!IMPORTANT]
> 새 지역과 일치하도록 위치, Azure Key Vault 비밀, 인증서 및 기타 GUID를 변경합니다.

### <a name="event-hubs-metadata"></a>Event Hubs 메타데이터

다음 메타데이터 요소는 Event Hubs 템플릿을 내보낼 때 다시 만들어집니다.

- 네임스페이스
- 이벤트 허브(영문)
- 소비자 그룹
- 권한 부여 규칙

추가 정보는 다음 항목을 참조하세요.

- [Event Hubs 개요](../event-hubs/event-hubs-about.md)를 검토하세요.
- [Event Hubs 자습서](../event-hubs/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Azure Service Bus](./germany-migration-integration.md#service-bus) 마이그레이션 단계를 확인합니다.
- [Azure Resource Manager 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md) 방법을 숙지하거나 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 개요를 읽어봅니다.

## <a name="hdinsight"></a>HDInsight

Azure 독일에서 글로벌 Azure로 Azure HDInsight 클러스터를 마이그레이션하려면 다음을 수행합니다.

1. HDInsight 클러스터를 중지합니다.
2. AzCopy 또는 유사한 도구를 사용하여 Azure Storage 계정의 데이터를 새 지역으로 마이그레이션합니다.
3. 글로벌 Azure에서 새 컴퓨팅 리소스를 만든 다음, 마이그레이션된 스토리지 리소스를 기본 연결 스토리지로 연결합니다.

더 특화된 장기 실행 클러스터(Kafka, Spark streaming, Storm 또는 HBase)의 경우 작업을 새 지역으로 전환하는 작업을 오케스트레이션하는 것이 좋습니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure HDInsight 설명서](../hdinsight/index.yml)를 검토합니다.
- [HDInsight 자습서](../hdinsight/index.yml)를 완료하여 지식을 새로 고칩니다.
- [HDInsight 클러스터 크기 조정](../hdinsight/hdinsight-administer-use-powershell.md#scale-clusters)에 관한 도움말은 [PowerShell을 사용하여 HDInsight 관리](../hdinsight/hdinsight-administer-use-powershell.md)를 참조하세요.
- [AzCopy](../storage/common/storage-use-azcopy-v10.md) 사용 방법을 읽어봅니다.

## <a name="stream-analytics"></a>Stream Analytics

Azure 독일에서 글로벌 Azure로 Azure Stream Analytics 서비스를 마이그레이션하려면 Azure Portal을 사용하거나 PowerShell을 사용하여 글로벌 Azure 지역에 전체 설정을 수동으로 다시 만들어야 합니다. Stream Analytics 작업에 대한 수신 및 송신 원본은 모든 지역에 있을 수 있습니다.

추가 정보는 다음 항목을 참조하세요.

- [Stream Analytics 자습서](../stream-analytics/stream-analytics-real-time-fraud-detection.md)를 완료하여 지식을 새로 고칩니다.
- [Stream Analytics 개요](../stream-analytics/stream-analytics-introduction.md)를 검토합니다.
- [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](../stream-analytics/stream-analytics-quick-create-powershell.md) 방법을 학습합니다.

## <a name="sql-database"></a>SQL Database

더 작은 Azure SQL Database 워크로드를 마이그레이션하려면 내보내기 함수를 사용하여 BACPAC 파일을 만듭니다. BACPAC 파일은 메타데이터와 SQL Server 데이터베이스 데이터를 포함하는 압축 파일입니다. BACPAC 파일을 만든 후에는 파일을 대상 환경에 복사하고(예: AzCopy 사용) 가져오기 함수를 사용하여 데이터베이스를 다시 빌드할 수 있습니다. 다음 고려 사항에 유의하시기 바랍니다.

- 내보내기에서 트랜잭션 일관성을 유지하려면 다음 조건 중 하나가 true인지 확인합니다.
  - 내보내는 동안 쓰기 활동이 발생하지 않습니다.
  - SQL 데이터베이스의 트랜잭션 일관성 복사본에서 내보냅니다.
- Azure Blob Storage로 내보내기 위해 BACPAC 파일 크기는 200GB로 제한됩니다. 더 큰 BACPAC 파일의 경우 로컬 스토리지로 내보냅니다.
- SQL Database 내보내기 작업이 20시간보다 오래 걸리면 작업이 취소될 수 있습니다. 성능을 향상시키는 방법에 대한 팁은 다음 문서를 참조하세요.

> [!NOTE]
> 내보내기 작업 도중 서버의 DNS 이름이 변경되므로 내보내기 작업 이후 연결 문자열이 변경됩니다.

추가 정보는 다음 항목을 참조하세요.

- [데이터베이스를 BACPAC 파일로 내보내는](../azure-sql/database/database-export.md) 방법을 알아봅니다.
- [데이터베이스로 BACPAC 파일을 가져오는](../azure-sql/database/database-import.md) 방법을 알아봅니다.
- [Azure SQL Database 설명서](/azure/sql-database/)를 검토하세요.

## <a name="analysis-services"></a>Analysis Services

Azure Analysis Services 모델을 Azure 독일에서 글로벌 Azure로 마이그레이션하려면 [백업 및 복원 작업](../analysis-services/analysis-services-backup.md)을 사용합니다.

데이터가 아닌 모델 메타데이터만 마이그레이션하고자 하는 경우 [SQL Server Data Tools에서 모델을 재배포](../analysis-services/analysis-services-deploy.md)하는 방법도 있습니다.

추가 정보는 다음 항목을 참조하세요.

- [Analysis Services 백업 및 복원](../analysis-services/analysis-services-backup.md)에 대해 알아보세요.
- [Analysis Services 개요](../analysis-services/analysis-services-overview.md)를 검토하세요.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)