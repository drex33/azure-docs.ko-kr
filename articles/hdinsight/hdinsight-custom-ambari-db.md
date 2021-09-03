---
title: Azure HDInsight의 사용자 지정 Apache Ambari 데이터베이스
description: 사용자 지정 Apache Ambari 데이터베이스를 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: d2ab2609338daff846797834be7694a8b1f220e6
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2021
ms.locfileid: "113600942"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>사용자 지정 Ambari DB를 사용하여 HDInsight 클러스터 설정

Apache Ambari를 사용하면 Apache Hadoop 클러스터의 관리와 모니터링이 간소화됩니다. Ambari는 사용하기 쉬운 웹 UI 및 REST API을 제공합니다. Ambari는 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는데 사용됩니다.

[Hdinsight에서 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md) 등의 다른 문서에 설명된 대로 정상적인 클러스터 만들기에서 Ambari는 HDInsight에서 관리되고 사용자가 액세스할 수 없는 [S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier)에 배포됩니다.

사용자 지정 Ambari DB 기능을 사용하면 관리하는 외부 데이터베이스에 새 클러스터를 배포하고 Ambari를 설정할 수 있습니다. 배포에는 Azure Resource Manager 템플릿을 사용합니다. 이 기능은 다음과 같은 이점을 제공합니다.

- 사용자 지정 - 데이터베이스의 크기 및 처리 용량을 선택합니다. 대규모 클러스터 처리 집약적 워크로드가 있는 경우 사양이 낮은 Ambari 데이터베이스는 관리 작업에 대해 병목 상태가 될 수 있습니다.
- 유연성 - 요구 사항에 맞게 데이터베이스를 스케일링할 수 있습니다.
- 제어 - 조직 요구 사항에 맞는 방식으로 데이터베이스에 대한 백업 및 보안을 관리할 수 있습니다.

이 문서의 나머지 부분에서는 다음 사항에 대해 설명합니다.

- 사용자 지정 Ambari DB 기능을 사용하기 위한 요구 사항
- Apache Ambari에 대한 고유한 외부 데이터베이스를 사용하여 HDInsight 클러스터를 프로비저닝하는 데 필요한 단계

## <a name="custom-ambari-db-requirements"></a>사용자 지정 Ambari DB 요구 사항

모든 클러스터 유형 및 버전을 사용하여 사용자 지정 Ambari DB를 배포할 수 있습니다. 여러 클러스터에서 동일한 Ambari DB를 사용할 수 없습니다.

사용자 지정 Ambari DB에는 다음과 같은 기타 요구 사항이 있습니다.

- 데이터베이스 이름에는 하이픈 또는 공백을 포함할 수 없습니다.
- 기존 Azure SQL DB 서버 및 데이터베이스가 있어야 합니다.
- Ambari 설치를 위해 제공하는 데이터베이스는 비어 있어야 합니다. 기본 dbo 스키마에는 테이블이 없어야 합니다.
- 데이터베이스에 연결하는 데 사용되는 사용자에게는 데이터베이스에 대한 SELECT, CREATE TABLE 및 INSERT 권한이 있어야 합니다.
- Ambari를 호스트할 서버에서 [Azure 서비스에 대한 액세스 허용](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) 옵션을 켭니다.
- HDInsight 서비스의 관리 IP 주소는 방화벽 규칙에서 허용해야 합니다. 서버 수준 방화벽 규칙에 추가해야 하는 IP 주소 목록은 [HDInsight 관리 IP 주소](hdinsight-management-ip-addresses.md)를 참조하세요.

외부 데이터베이스에서 Apache Ambari DB를 호스트하는 경우 다음 사항에 주의하세요.

- Ambari를 보유하는 Azure SQL DB의 추가 비용은 사용자의 책임입니다.
- 사용자 지정 Ambari DB를 정기적으로 백업합니다. Azure SQL Database는 백업을 자동으로 생성하지만 백업 보존 기간은 각기 다릅니다. 자세한 내용은 [자동 SQL 데이터베이스 백업에 대해 알아보기](../azure-sql/database/automated-backups-overview.md)를 참조하세요.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>사용자 지정 Ambari DB를 사용하여 클러스터 배포

사용자 고유의 외부 Ambari 데이터베이스를 사용하는 HDInsight 클러스터를 만들려면 [사용자 지정 Ambari DB 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-custom-ambari-db)을 사용합니다.

`azuredeploy.parameters.json`에서 매개 변수를 편집하여 새 클러스터에 대한 정보와 Ambari을 보유할 데이터베이스를 지정합니다.

Azure CLI를 사용하여 배포를 시작할 수 있습니다. `<RESOURCEGROUPNAME>`을 클러스터를 배포하려는 리소스 그룹으로 바꿉니다.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```


> [!WARNING]
> HDInsight 클러스터에 대해 다음 권장 SQL DB 및 Headnode VM을 사용하세요. 모든 프로덕션 환경에 기본 Ambari DB(S0)를 사용하지 마세요. 
>


## <a name="database-and-headnode-sizing"></a>데이터베이스 및 헤드 노드 크기 조정

다음 표에서는 HDInsight 클러스터의 크기에 따라 선택할 Azure SQL DB 계층에 대한 지침을 제공합니다.

| 작업자 노드의 수 | 필수 DB 계층 | 필수 헤드 노드 VM |
|---|---|---|
| <=4 | S0 | 4코어/28GB RAM 이상 |
| >4 && <=8 | S1 | 4코어/28GB RAM 이상 |
| >8 && <=16 | S2 | 4코어/28GB RAM 이상 |
| >16 && <=32 | S3 | 8코어/56GB RAM 이상 |
| >32 && <=64 | S4 | 8코어/56GB RAM 이상 |
| >64 && <=128 | P2 | 16코어/112GB RAM 이상 |
| >128 | 지원에 문의 | 지원에 문의 |

## <a name="next-steps"></a>다음 단계

- [Azure HDInsight에서 외부 메타데이터 저장소 사용](hdinsight-use-external-metadata-stores.md)
