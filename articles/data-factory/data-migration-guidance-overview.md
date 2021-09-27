---
title: 데이터 레이크 및 데이터 웨어하우스에서 Azure로 데이터 마이그레이션
description: Azure Data Factory를 사용하여 데이터 레이크 및 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션합니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 6175447ddb249e04a939219caa722c55dae65749
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749077"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory를 사용하여 데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

데이터 레이크 또는 EDW(엔터프라이즈 데이터 웨어하우스)를 Microsoft Azure로 마이그레이션하려면 Azure Data Factory를 사용하는 것이 좋습니다. Azure Data Factory는 다음과 같은 시나리오에 적합합니다.

- Amazon S3(Amazon Simple Storage Service) 또는 온-프레미스 HDFS(Hadoop Distributed File System)에서 Azure로 빅 데이터 워크로드 마이그레이션
- Oracle Exadata, Netezza, Teradata 또는 Amazon Redshift에서 Azure로 EDW 마이그레이션

Azure Data Factory는 데이터 레이크 마이그레이션의 경우 페타바이트(PB) 데이터를 이동하고 데이터 웨어하우스 마이그레이션의 경우 수십 테라바이트(TB)의 데이터를 이동할 수 있습니다.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>데이터 마이그레이션에 Azure Data Factory를 사용할 수 있는 이유

- Azure Data Factory는 성능, 복원력 및 확장성이 뛰어난 서버리스 방식으로 데이터를 이동할 수 있도록 처리 능력을 쉽게 확장할 수 있습니다. 또한 사용한 만큼만 요금을 지불합니다. 다음 사항도 유의해야 합니다. 
  - Azure Data Factory에는 데이터 볼륨 또는 파일 수에 대한 제한이 없습니다.
  - Azure Data Factory는 네트워크 및 스토리지 대역폭을 완벽하게 사용하여 사용자 환경에서 가장 높은 데이터 이동 처리량을 달성할 수 있습니다.
  - Azure Data Factory는 종량제 방법을 사용하므로 Azure로의 데이터 마이그레이션을 실행하는 데 실제로 사용하는 시간에 대해서만 비용을 지불합니다.  
- Azure Data Factory는 일회성 기록 로드와 예약된 증분 로드를 모두 수행할 수 있습니다.
- Azure Data Factory는 Azure IR(Integration Runtime)을 사용하여 공개적으로 액세스할 수 있는 데이터 레이크와 웨어하우스 엔드포인트 간에 데이터를 이동합니다. 또한 VNet(Azure Virtual Network) 내에 있거나 방화벽 뒤에 있는 데이터 레이크 및 웨어하우스 엔드포인트에 대한 데이터를 이동할 때 자체 호스팅 IR을 사용할 수도 있습니다.
- Azure Data Factory는 엔터프라이즈급 보안을 지원합니다. 따라서 보안 서비스 간 통합에 MSI(Windows Installer) 또는 서비스 ID를 사용하고 자격 증명 관리에 Azure Key Vault를 사용할 수 있습니다.
- Azure Data Factory은 코드 없는 제작 환경과 풍부한 기본 제공 모니터링 대시보드를 제공합니다.  

## <a name="online-vs-offline-data-migration"></a>온라인 데이터 마이그레이션과 오프라인 마이그레이션 비교

Azure Data Factory는 네트워크(인터넷, ER 또는 VPN)를 통해 데이터를 전송하는 표준 온라인 데이터 마이그레이션 도구입니다. 반면 오프라인 데이터 마이그레이션을 사용하는 경우, 사용자는 조직에서 Azure Data Center로 데이터 전송 디바이스를 제공합니다.  

온라인 마이그레이션 방법과 오프라인 마이그레이션 방법 중에서 선택할 때 다음 세 가지 주요 사항을 고려해야 합니다.  

- 마이그레이션할 데이터 크기
- 네트워크 대역폭
- 마이그레이션 기간

예를 들어 Azure Data Factory를 사용하여 2주(*마이그레이션 기간*) 내에 데이터 마이그레이션을 완료할 계획인 경우, 다음 표에서 분홍색/파란색을 확인하세요. 지정된 열에서 가장 아래에 있는 분홍색 셀은 마이그레이션 기간이 2주 미만인 데이터 크기/네트워크 대역폭 쌍을 보여줍니다. (파란색 셀의 모든 크기/대역폭 쌍은 온라인 마이그레이션 기간이 2주를 초과합니다.) 

:::image type="content" source="media/data-migration-guidance-overview/online-offline.png" alt-text="온라인 및 오프라인":::
이 표는 데이터 크기 및 사용 가능한 네트워크 대역폭에 따라 Azure Data Factory(온라인 마이그레이션)를 통해 의도한 마이그레이션 기간을 충족할 수 있는지 여부를 확인하는 데 도움이 됩니다. 온라인 마이그레이션 기간이 2주를 초과하면 오프라인 마이그레이션을 사용하는 것이 좋습니다.

> [!NOTE]
> 온라인 마이그레이션을 사용하면 단일 도구를 통해 기록 데이터 로드와 증분 피드 모두 엔드투엔드로 수행할 수 있습니다.  이 방법을 통해 전체 마이그레이션 기간 동안 기존 저장소와 새 저장소 간에 데이터를 동기화된 상태로 유지할 수 있습니다. 즉, 새로 고쳐진 데이터를 사용하여 새 저장소에서 ETL 논리를 다시 빌드할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [AWS S3에서 Azure로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
- [온-프레미스 Hadoop 클러스터에서 Azure로 데이터 마이그레이션](data-migration-guidance-hdfs-azure-storage.md)
- [온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션](data-migration-guidance-netezza-azure-sqldw.md)
