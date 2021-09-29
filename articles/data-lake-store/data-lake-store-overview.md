---
title: Azure Data Lake Storage Gen1이란? | Microsoft Docs
description: Data Lake Storage Gen1(이전에는 Azure Data Lake Store라고 함) 개요 및 다른 데이터 저장소에 대해 제공하는 값에 대해 설명합니다.
author: normesta
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: normesta
ms.openlocfilehash: f105815e6c20777060c1350b9f12fbb160dbc710
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655584"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1이란?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1은 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Azure 데이터 레이크를 사용하면 작동 및 예비 분석에 대해 한 곳에서 모든 크기, 형식 및 수집 속도의 데이터를 캡처할 수 있습니다.

Data Lake Storage Gen1은 WebHDFS 호환 REST API를 사용하여 Hadoop(HDInsight 클러스터에서 사용 가능)에서 액세스할 수 있습니다. 저장된 데이터에 대한 분석을 지원하도록 설계되었으며, 데이터 분석 시나리오에 필요한 성능에 맞게 튜닝되었습니다. Data Lake Storage Gen1에는 보안, 관리 효율성, 확장성, 안정성 및 가용성과 같은 모든 엔터프라이즈급 기능이 포함됩니다.

![Azure 데이터 레이크](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>주요 기능

Data Lake Storage Gen1의 주요 기능 중 일부는 다음과 같습니다.

### <a name="built-for-hadoop"></a>Hadoop용으로 작성

Data Lake Storage Gen1은 HDFS(Hadoop 분산 파일 시스템)와 호환되고 Hadoop 에코시스템에서 작동하는 Apache Hadoop 파일 시스템입니다. WebHDFS API를 사용하는 기존 HDInsight 애플리케이션 또는 서비스는 Data Lake Storage Gen1과 쉽게 통합할 수 있습니다. Data Lake Storage Gen1은 또한 애플리케이션에 대한 WebHDFS 호환 REST 인터페이스를 노출합니다.

MapReduce 또는 Hive와 같은 Hadoop 분석 프레임워크를 사용하여 Data Lake Storage Gen1에 저장된 데이터를 쉽게 분석할 수 있습니다. Azure HDInsight 클러스터를 프로비저닝하고 Data Lake Storage Gen1에 저장된 데이터에 직접 액세스하도록 구성할 수 있습니다.

### <a name="unlimited-storage-petabyte-files"></a>무제한 스토리지, 페타바이트 파일

Data Lake Storage Gen1은 무제한 스토리지를 제공하며, 다양한 분석 데이터를 저장하는 데 적합합니다. 데이터 레이크에 저장할 수 있는 계정 크기, 파일 크기 또는 데이터 양을 제한하지 않습니다. 개별 파일의 크기는 킬로바이트에서 페타바이트까지 다양합니다. 데이터는 여러 복사본을 만들어 영구적으로 저장됩니다. 데이터를 데이터 레이크에 저장할 수 있는 기간에는 제한이 없습니다.

### <a name="performance-tuned-for-big-data-analytics"></a>빅 데이터 분석에 대한 성능 조정

Data Lake Storage Gen1은 대량의 데이터를 쿼리하고 분석하기 위해 대규모 처리량이 필요한 대규모 분석 시스템을 실행할 수 있도록 빌드되었습니다. 데이터 레이크는 개별 스토리지 서버의 수 이상으로 파일의 일부분을 배포합니다. 데이터 분석을 수행하기 위해 병렬로 파일을 읽을 때 읽기 처리량이 향상됩니다.

### <a name="enterprise-ready-highly-available-and-secure"></a>엔터프라이즈 지원: 고가용성 및 보안

Data Lake Storage Gen1은 업계 표준 가용성과 안정성을 제공합니다. 데이터 자산은 모든 예기치 않은 오류로부터 보호하도록 중복 복사본을 만들어 영구적으로 저장됩니다.

또한 Data Lake Storage Gen1은 저장된 데이터에 대한 엔터프라이즈급 보안을 제공합니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 데이터 보안](#DataLakeStoreSecurity)을 참조하세요.

### <a name="all-data"></a>모든 데이터

Data Lake Storage Gen1은 사전에 변환하지 않고도 모든 데이터를 네이티브 형식으로 저장할 수 있습니다. Data Lake Storage Gen1은 데이터가 로드되기 전에 정의되어야 하는 스키마가 필요하지 않으므로 개별 분석 프레임워크가 데이터를 해석하고 분석 시 스키마를 정의할 때까지 그대로 둡니다. 임의 크기 및 형식의 파일을 저장할 수 있는 기능을 통해 Data Lake Storage Gen1에서 정형, 반정형 및 비정형 데이터를 처리할 수 있습니다.

데이터에 대한 Data Lake Storage Gen1 컨테이너는 기본적으로 폴더 및 파일입니다. SDK, Azure Portal 및 Azure Powershell을 사용하여 저장된 데이터에 대한 작업을 수행합니다. 이러한 인터페이스와 적절한 컨테이너를 사용하여 데이터를 저장소에 저장하면 모든 형식의 데이터를 저장할 수 있습니다. Data Lake Storage Gen1은 저장하는 데이터의 형식에 따라 데이터의 특수한 처리를 수행하지 않습니다.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>데이터 보안

Data Lake Storage Gen1은 Azure AD(Azure Active Directory)를 인증 및 ACL(액세스 제어 목록)에 사용하여 데이터에 대한 액세스를 관리합니다.

| 기능 | 설명 |
| --- | --- |
| 인증 |Data Lake Storage Gen1은 Data Lake Storage Gen1에 저장된 모든 데이터에 대한 ID 및 액세스를 관리할 수 있도록 Azure AD와 통합됩니다. 통합에 따라 Data Lake Storage Gen1에서 다단계 인증, 조건부 액세스, Azure 역할 기반 액세스 제어, 애플리케이션 사용 현황 모니터링, 보안 모니터링 및 경고 등과 같은 모든 Azure AD 기능을 활용할 수 있습니다. Data Lake Storage Gen1은 REST 인터페이스에서 인증을 위한 OAuth 2.0 프로토콜을 지원합니다. [Data Lake Storage Gen1 인증](data-lakes-store-authentication-using-azure-active-directory.md)을 참조하세요.|
| Access Control |Data Lake Storage Gen1은 WebHDFS 프로토콜에 의해 노출되는 POSIX 스타일 권한을 지원하여 액세스 제어를 제공합니다. 루트 폴더, 하위 폴더 및 개별 파일에서 ACL을 사용하도록 설정할 수 있습니다. Data Lake Storage Gen1의 컨텍스트에서 ACL이 작동하는 방법에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어](data-lake-store-access-control.md)를 참조하세요. |
| 암호화 |Data Lake Storage Gen1은 계정에 저장된 데이터에 대한 암호화도 제공합니다. Data Lake Storage Gen1 계정을 만드는 동안 암호화 설정을 지정합니다. 암호화된 데이터 또는 암호화 없음을 선택할 수 있습니다. 자세한 내용은 [Data Lake Storage Gen1의 암호화](data-lake-store-encryption.md)를 참조하세요. 암호화 관련 구성을 제공하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요. |

Data Lake Storage Gen1의 데이터 보안 방법에 대한 지침은 [Azure Data Lake Storage Gen1의 데이터 보안](data-lake-store-secure-data.md)을 참조하세요.

## <a name="application-compatibility"></a>애플리케이션 호환성

Data Lake Storage Gen1은 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 다른 Azure 서비스와도 원활하게 통합됩니다. 오픈 소스 구성 요소 및 기타 Azure 서비스에서 Data Lake Storage Gen1을 사용하는 방법에 대한 자세한 내용은 다음 링크를 사용하세요.

- Data Lake Storage Gen1과 상호 운용 가능한 오픈 소스 애플리케이션 목록은 [Azure Data Lake Storage Gen1과 호환되는 애플리케이션 및 서비스](data-lake-store-compatible-oss-other-applications.md)를 참조하세요.
- 다른 Azure 서비스에서 Data Lake Storage Gen1을 사용하여 더 광범위한 시나리오를 사용하도록 설정하는 방법을 이해하려면 [다른 Azure 서비스와 통합](data-lake-store-integrate-with-other-services.md)을 참조하세요.
- [Data Lake Storage Gen1을 사용하는 시나리오](data-lake-store-data-scenarios.md)를 참조하여 데이터 수집, 데이터 처리, 데이터 다운로드 및 데이터 시각화와 같은 시나리오에서 Data Lake Storage Gen1을 사용하는 방법을 알아봅니다.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 파일 시스템

Data Lake Storage Gen1은 Hadoop 환경(HDInsight 클러스터에서 사용 가능)에서 AzureDataLakeFilesystem(adl://) 파일 시스템을 통해 액세스할 수 있습니다. adl://을 사용하는 애플리케이션 및 서비스는 현재 WebHDFS에서 사용할 수 없는 추가 성능 최적화를 활용할 수 있습니다. 이에 따라 Data Lake Storage Gen1은 권장되는 adl://을 사용하는 데 추천되는 옵션을 사용하여 최상의 성능을 활용하거나 WebHDFS API를 직접 계속 사용하여 기존 코드를 유지할 수 있는 유연성을 제공합니다. Azure HDInsight는 Data Lake Storage Gen1에서 최상의 성능을 제공하도록 AzureDataLakeFilesystem을 완벽하게 활용합니다.

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`을 사용하여 Data Lake Storage Gen1의 데이터에 액세스할 수 있습니다. Data Lake Storage Gen1의 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장된 데이터의 속성 보기](data-lake-store-get-started-portal.md#properties)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)
- [.NET SDK를 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-net-sdk.md)
- [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)