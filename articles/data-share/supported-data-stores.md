---
title: Azure Data Share에서 지원되는 데이터 저장소
description: Azure Data Share에서 사용하도록 지원되는 데이터 저장소에 대해 알아봅니다.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: 605405965e4c6bc17321401245866f9b21f3a988
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760064"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share에서 지원되는 데이터 저장소

Azure Data Share는 다양한 데이터 저장소에서 공유하는 기능을 포함하여 유연한 개방형 데이터 공유 기능을 제공합니다. 데이터 공급자는 특정 형식의 데이터 저장소에서 데이터를 공유할 수 있으며, 데이터 소비자는 데이터를 받을 데이터 저장소를 선택할 수 있습니다. 

이 문서에서는 Azure Data Share에서 지원하는 다양한 Azure 데이터 저장소 집합에 대해 알아봅니다. 데이터 공급자와 데이터 소비자가 서로 다른 데이터 저장소를 결합하는 방법에 대해서도 알아봅니다. 

## <a name="supported-data-stores"></a>지원되는 데이터 저장소 

다음 표에서는 Azure Data Share에서 지원하는 데이터 저장소에 대해 설명합니다. 

| 데이터 저장소 | 전체 스냅샷에 따라 공유 | 증분 스냅샷에 따라 공유 | 현재 위치에서 공유 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics(이전의 Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics(작업 영역) 전용 SQL 풀 |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>데이터 저장소 지원 매트릭스

Azure Data Share를 통해 데이터 소비자는 데이터를 허용할 데이터 저장소를 선택할 수 있습니다. 예를 들어 Azure SQL Database에서 공유되는 데이터를 Azure Data Lake Storage Gen2, Azure SQL Database 또는 Azure Synapse Analytics로 받을 수 있습니다. 고객이 수신 데이터 공유를 설정하는 경우 데이터를 받을 형식을 선택할 수 있습니다. 

다음 표에서는 데이터 소비자가 데이터 공유를 허용하고 구성할 때 선택할 수 있는 조합과 옵션을 설명합니다. 자세한 내용은 [데이터 세트 매핑 구성](how-to-configure-mapping.md)을 참조하세요.

| 데이터 저장소 | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (이전의 SQL 데이터 웨어하우스) | Synapse Analytics(작업 영역) 전용 SQL 풀 | 데이터 탐색기
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (이전의 SQL 데이터 웨어하우스) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics(작업 영역) 전용 SQL 풀 | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| 데이터 탐색기 ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>스토리지 계정에서 공유
Azure Data Share는 Azure Data Lake Gen1과 Azure Data Lake Gen2에서 파일, 폴더 및 파일 시스템을 공유하도록 지원합니다. 또한 Azure Blob Storage에서 blob, 폴더 및 컨테이너를 공유할 수 있도록 지원도 합니다. 블록, 추가 또는 페이지 Blob을 공유할 수 있으며 블록 Blob으로 수신됩니다.

스냅샷 기반 공유에서 파일 시스템, 컨테이너 또는 폴더가 공유되는 경우 데이터 소비자는 공유 데이터의 전체 복사본을 만들도록 선택할 수 있습니다. 또는 증분 스냅샷 기능을 사용하여 새 파일이나 업데이트된 파일만 복사할 수 있습니다. 

증분 스냅샷 기능은 파일의 마지막 수정 시간을 기반으로 합니다. 수신된 데이터의 파일과 이름이 동일한 기존 파일은 스냅샷에서 덮어쓰여집니다. 원본에서 삭제된 파일은 대상에서 삭제되지 않습니다. 

자세한 내용은 [Azure Blob Storage 및 Azure Data Lake Storage에서 데이터 공유 및 수신](how-to-share-from-storage.md)을 참조하십시오.

## <a name="share-from-a-sql-based-source"></a>SQL 기반 원본에서 공유
Azure Data Share는 Azure SQL 데이터베이스 및 Azure Synapse Analytics (이전의 Azure SQL 데이터 웨어하우스)에서 테이블 및 뷰를 공유할 수 있도록 지원합니다. Azure Synapse Analytics (작업 영역) 전용 SQL 풀에서 테이블을 공유할 수 있도록 지원합니다. Azure Synapse Analytics(작업 영역) 서버리스 SQL 풀에서 공유하는 기능은 현재 지원되지 않습니다. 

데이터 소비자는 데이터를 Azure Data Lake Storage Gen2 또는 Azure Blob Storage로 CSV 파일 또는 Parquet 파일로 선택하고 받아들일 수 있습니다. 또한 Azure SQL Database 및 Azure Synapse Analytics에 테이블로 데이터를 수락할 수도 있습니다.

Azure Data Lake Storage Gen2 또는 Azure Blob Storage에 데이터를 가져오도록 허용하는 경우 전체 스냅샷은 파일이 이미 있는 경우 대상 파일의 내용을 덮어씁니다. 데이터가 테이블로 수신되고 대상 테이블이 아직 없는 경우, Azure Data Share는 원본 스키마를 사용하여 SQL 테이블을 만듭니다. 이름이 같은 대상 테이블이 이미 있는 경우 삭제되고 최신 전체 스냅샷으로 덮어씁니다. 증분 스냅샷은 현재 지원되지 않습니다.

자세한 내용은 [Azure SQL 데이터베이스 및 Azure Synapse Analytics에서 데이터 공유 및 수신](how-to-share-from-sql.md)을 참조하십시오.

## <a name="share-from-data-explorer"></a>Azure 데이터 탐색기에서 공유
Azure Data Share는 Azure 데이터 탐색기 클러스터에서 전체 데이터베이스를 공유하는 기능을 지원합니다. 데이터 공급자는 데이터베이스 또는 클러스터 수준에서 공유할 수 있습니다. 데이터 공유 API를 사용 하 여 데이터를 공유 하는 경우 특정 테이블을 공유할 수도 있습니다.  

데이터를 데이터베이스 수준에서 공유하는 경우, 데이터 소비자는 데이터 공급자가 공유하는 데이터베이스에만 액세스할 수 있습니다. 공급자가 클러스터 수준에서 데이터를 공유하는 경우 데이터 소비자는 데이터 공급자가 만드는 이후 데이터베이스를 포함하여 공급자 클러스터의 모든 데이터베이스에 액세스할 수 있습니다.

공유 데이터베이스에 액세스하려면 데이터 소비자에게 자체 Azure 데이터 탐색기 클러스터가 필요합니다. 해당 클러스터는 데이터 공급자의 Azure 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에 있어야 합니다. 

공유 관계가 설정되면 Azure Data Share는 공급자의 클러스터와 소비자 클러스터 사이에 기호화된 링크를 만듭니다. 일괄 처리 모드를 사용하여 원본 클러스터에 수집되는 데이터는 몇 분 내에 대상 클러스터에 나타납니다.

자세한 내용은 [Azure 데이터 탐색기에서 데이터 공유 및 수신](/azure/data-explorer/data-share)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.
