---
title: Azure Data Lake Storage에 대한 다중 프로토콜 액세스
description: Azure Data Lake Storage Gen2에서 Blob API를 사용하는 애플리케이션 및 Blob API를 사용합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 01e8cec9a77db207e5320386cbc39c8bbcfddd07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621151"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 다중 프로토콜 액세스

Blob API는 이제 계층 구조 네임스페이스가 있는 계정에서 작동합니다. 이는 도구, 애플리케이션, 서비스의 에코시스템뿐 아니라 계층 구조 네임스페이스가 있는 계정에 대한 여러 가지 Blob Storage 기능의 잠금을 해제합니다.

최근까지는 개체 스토리지와 분석 스토리지에 대한 개별 스토리지 솔루션을 유지 관리해야 했을 수 있습니다. Azure Data Lake Storage Gen2에서 에코시스템 지원을 제한했기 때문입니다. 진단 로깅과 같은 Blob service 기능에 대한 액세스도 제한했습니다. 조각화된 스토리지 솔루션은 다양한 시나리오를 수행하기 위해 계정 간에 데이터를 이동해야 하기 때문에 유지 관리하기 어렵습니다. 더 이상이 해당 작업을 수행할 필요가 없습니다.

Data Lake Storage에 대한 다중 프로토콜 액세스를 통해 도구, 애플리케이션, 서비스의 에코시스템을 사용하여 데이터 작업을 수행할 수 있습니다. 여기에는 타사 도구와 애플리케이션도 포함됩니다. 계층 구조 네임스페이스를 수정하지 않고도 해당 네임스페이스가 포함된 계정을 가리킬 수 있습니다. Blob API가 이제 계층 구조 네임스페이스가 있는 계정의 데이터에서 작동할 수 있으므로 이러한 애플리케이션은 Blob API를 호출하는 경우에도 ‘있는 그대로’ 작동합니다.

[진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md) 및 [Blob Storage 수명 주기 관리 정책](./lifecycle-management-overview.md)과 같은 Blob Storage 기능은 이제 계층 구조 네임스페이스가 있는 계정으로 작동합니다. 따라서 이 중요한 기능에 대한 액세스를 잃지 않고 Blob Storage 계정에서 계층 구조 네임스페이스를 사용하도록 설정할 수 있습니다.

> [!NOTE]
> Data Lake Storage에 대한 다중 프로토콜 액세스는 일반 공급되며 모든 지역에서 사용할 수 있습니다. 다중 프로토콜 액세스에 의해 사용하도록 설정된 일부 Azure 서비스 또는 Blob Storage 기능은 미리 보기 상태로 유지됩니다. 이 문서에서는 Blob Storage 기능과 Azure 서비스 통합에 대한 현재 지원을 요약합니다.
>
> [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md)
>
> [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data Lake Storage에 대한 다중 프로토콜 액세스의 작동 방식

Blob API와 Data Lake Storage Gen2 API는 계층 구조 네임스페이스가 있는 스토리지 계정의 동일한 데이터에서 작동할 수 있습니다. Data Lake Storage Gen2는 계층 구조 네임스페이스를 통해 Blob API를 라우팅하므로 첫 번째 클래스 디렉터리 작업 및 POSIX 규격 ACL(액세스 제어 목록)의 이점을 얻을 수 있습니다.

![Data Lake Storage에 대한 다중 프로토콜 액세스 개념](./media/data-lake-storage-interop/interop-concept.png)

Blob API를 사용하는 기존 도구와 애플리케이션은 이 이점을 자동으로 얻습니다. 개발자가 이를 수정할 필요가 없습니다. Data Lake Storage Gen2는 도구와 애플리케이션이 데이터에 액세스하는 데 사용하는 프로토콜에 관계없이 디렉터리 및 파일 수준 ACL을 일관되게 적용합니다.

## <a name="see-also"></a>참고 항목

- [Azure Storage 계정의 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md)
- [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2에서 알려진 문제](data-lake-storage-known-issues.md)
