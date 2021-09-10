---
title: Azure Storage 소개 - Azure의 클라우드 스토리지 | Microsoft Docs
description: 핵심 Azure Storage 플랫폼은 Microsoft의 클라우드 스토리지 솔루션입니다. Azure Storage는 고가용성, 보안, 지속형, 확장성이 뛰어난 중복된 데이터 개체에 대한 스토리지를 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 494afe20edc4ffdeea9e0eb1cd4b1368782c3cda
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729041"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>핵심 Azure Storage 서비스 소개

Azure Storage 플랫폼은 최신 데이터 스토리지 시나리오를 위한 Microsoft의 클라우드 스토리지 솔루션입니다. 핵심 스토리지 서비스는 데이터 개체를 저장하는 대규모 확장 가능한 개체 저장소, Azure VM(가상 머신)용 디스크 스토리지, 클라우드용 파일 시스템 서비스, 신뢰할 수 있는 메시징의 메시징 저장소 및 NoSQL 저장소를 제공합니다. 사용되는 서비스는 다음과 같습니다.

- **내구성 및 고가용성** 중복성은 일시적인 하드웨어 오류 발생 시 데이터를 안전하게 보호합니다. 또한 로컬 재해 또는 자연 재해로부터 추가 보호를 위해 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이 방식으로 복제된 데이터는 예기치 않은 중단 시에도 고가용성을 유지합니다.
- **보안** Azure 스토리지 계정에 기록된 모든 데이터는 서비스에 의해 암호화됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자를 자세히 제어할 수 있습니다.
- **확장성** Azure Storage는 오늘날의 애플리케이션에 대한 데이터 저장소 및 성능 요구 사항을 충족하기 위해 대규모로 확장할 수 있도록 설계되었습니다.
- **관리** 하드웨어 유지 관리, 업데이트 및 중요한 문제를 Azure에서 처리합니다.
- **액세스 가능** Azure Storage의 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. Microsoft는 완성도 높은 REST API뿐만 아니라 .NET, Java, Node.js, Python, PHP, Ruby, Go 등 기타 다양한 언어로 Azure Storage용 클라이언트 라이브러리를 제공합니다. Azure Storage는 Azure PowerShell 또는 Azure CLI에서 스크립트를 지원합니다. 또한 Azure Portal 및 Azure Storage Explorer는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공합니다.  

## <a name="core-storage-services"></a>핵심 스토리지 서비스

Azure Storage 플랫폼에는 다음과 같은 데이터 서비스가 포함됩니다.

- [Azure Blob](../blobs/storage-blobs-introduction.md): 텍스트 및 이진 데이터에 대한 확장성이 뛰어난 개체 저장소입니다. 또한 Data Lake Storage Gen2를 통한 빅 데이터 분석 지원도 포함합니다.
- [Azure Files](../files/storage-files-introduction.md): 클라우드 또는 온-프레미스 배포에 대한 관리되는 파일 공유입니다.
- [Azure 큐](../queues/storage-queues-introduction.md): 애플리케이션 구성 요소 간에 안정적인 메시징을 위한 메시징 저장소입니다.
- [Azure 테이블](../tables/table-storage-overview.md): 구조화된 데이터의 스키마 없는 스토리지를 위한 NoSQL 스토리지입니다.
- [Azure 디스크](../../virtual-machines/managed-disks-overview.md): Azure VM용 블록 수준 스토리지 볼륨입니다.

각 서비스는 스토리지 계정을 통해 액세스됩니다. 시작하려면 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

## <a name="example-scenarios"></a>예제 시나리오

다음 표에서는 파일, Blob , 디스크, 큐, 테이블을 비교하고 각각에 적절한 예제 시나리오를 보여줍니다.

| 기능 | Description | 사용 시기 |
|--------------|-------------|-------------|
| **Azure 파일** |업계 표준 SMB(서버 메시지 블록) 프로토콜을 통해 어디에서나 액세스할 수 있는 완전 관리형 클라우드 파일 공유를 제공합니다.<br><br>Azure 파일 공유를 Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포에 동시에 탑재할 수 있습니다. | 이미 기본 파일 시스템 API를 사용하는 클라우드로 응용 프로그램을 “전환”하여 Azure에서 실행 중인 다른 응용 프로그램과 데이터를 공유하려고 합니다.<br/><br/>온-프레미스 파일 서버 또는 NAS 장치를 바꾸거나 추가하려고 합니다.<br><br> 여러 가상 머신에서 액세스해야 하는 개발 및 디버깅 도구를 저장하려고 합니다. |
| **Azure Blob** | 구조화되지 않은 데이터를 블록 Blob에서 대규모로 저장 및 액세스할 수 있도록 합니다.<br/><br/>엔터프라이즈 빅 데이터 분석 솔루션을 위한 [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)도 지원합니다. | 애플리케이션에서 스트리밍 및 임의 액세스 시나리오를 지원하도록 하려고 합니다.<br/><br/>어디에서든 애플리케이션 데이터에 액세스할 수 있게 되기를 원합니다.<br/><br/>Azure에서 엔터프라이즈 Data Lake를 빌드하고 빅 데이터 분석을 수행하려고 합니다. |
| **Azure 디스크** | 연결된 가상 하드 디스크에서 데이터를 영구적으로 저장 및 액세스할 수 있습니다. | 기본 파일 시스템 API를 사용하는 응용 프로그램을 데이터를 영구 디스크에 읽고 쓰도록 전환하려고 합니다.<br/><br/>가상 머신 외부에서 액세스할 필요가 없는 데이터를 디스크가 연결된 컴퓨터에 저장하려고 합니다. |
| **Azure 큐** | 응용 프로그램 구성 요소 간의 비동기 메시지 큐를 허용합니다. | 응용 프로그램 구성 요소를 분리하고 비동기 메시징을 사용하여 서로 통신하려고 합니다.<br><br>Queue storage와 Service Bus 큐를 언제 사용하는지에 대한 지침은 [스토리지 큐 및 Service Bus 큐 - 비교 및 대조](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)를 참조하세요. |
| **Azure Tables** | 클라우드에 구조화된 NoSQL 데이터를 저장하는 서비스로, 스키마 없이 디자인된 키/특성 저장소를 제공합니다. | 웹 응용 프로그램의 사용자 데이터, 주소록, 장치 정보 및 서비스에 필요한 다른 유형의 메타데이터를 비롯한 유연한 데이터 집합을 저장하려고 합니다. <br/><br/>Table storage와 Azure Cosmos DB Table API를 비교하여 언제 사용할지에 대한 지침은 [Azure Cosmos DB Table API 및 Azure Table 스토리지를 이용한 개발](../../cosmos-db/table-support.md)을 참조하세요. |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. Blob Storage는 텍스트 또는 이진 데이터와 같이 구조화되지 않은 대량의 데이터를 저장하는 데 최적화되어 있습니다.

Blob 스토리지가 적합한 경우는 다음과 같습니다.

- 브라우저에 이미지 또는 문서 직접 제공
- 분산 액세스용 파일 저장.
- 비디오 및 오디오 스트리밍.
- 백업/복원, 재해 복구 및 보관용 데이터 저장
- 온-프레미스 또는 Azure 호스팅 서비스에서 분석하기 위한 데이터 저장.

Blob Storage의 개체는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있습니다. 사용자 또는 클라이언트 애플리케이션은 URL, [Azure Storage REST API](/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](/powershell/module/azure.storage), [Azure CLI](/cli/azure/storage) 또는 Azure Storage 클라이언트 라이브러리를 통해 Blob에 액세스할 수 있습니다. 스토리지 클라이언트 라이브러리는 [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) 및 [Ruby](https://azure.github.io/azure-storage-ruby)를 비롯한 여러 언어에서 사용할 수 있습니다.

Blob 스토리지에 대한 자세한 내용은 [Blob 스토리지 소개](../blobs/storage-blobs-introduction.md)를 참조하세요.

## <a name="azure-files"></a>Azure 파일

[Azure Files](../files/storage-files-introduction.md)를 사용하면 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 액세스할 수 있는 고가용성 네트워크 파일 공유를 설정할 수 있습니다. 즉, 여러 VM이 읽기 및 쓰기 권한을 모두 사용하여 동일한 파일을 공유할 수 있습니다. REST 인터페이스 또는 스토리지 클라이언트 라이브러리를 사용하여 파일을 읽을 수도 있습니다.

Azure Files가 회사 파일 공유의 파일과 다른 점 한 가지는 파일을 가리키고 SAS(공유 액세스 서명) 토큰을 포함하고 있는 URL을 사용하여 전 세계 어디서나 파일에 액세스할 수 있다는 것입니다. SAS 토큰은 생성 가능하며 특정 기간에 프라이빗 자산에 대한 특정 액세스를 허용합니다.

파일 공유를 사용할 수 있는 여러 가지 일반적인 시나리오는 다음과 같습니다.

- 여러 온-프레미스 애플리케이션에서 파일 공유를 사용합니다. 이 기능을 사용하면 데이터를 공유하는 애플리케이션을 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 파일 공유를 온-프레미스 애플리케이션에서 사용하는 것과 동일한 드라이브 문자에 탑재하면 파일 공유에 액세스하는 애플리케이션의 일부가 최소한의 변경 내용(있는 경우)으로 작동해야 합니다.

- 구성 파일을 파일 공유에 저장하고 여러 VM에서 액세스할 수 있습니다. 그룹의 여러 개발자가 사용하는 도구 및 유틸리티를 파일 공유에 저장할 수 있으며, 이렇게 하면 모든 사람이 찾아서 동일한 버전을 사용할 수 있습니다.

- 리소스 로그, 메트릭 및 크래시 덤프는 파일 공유에 기록하고 나중에 처리하거나 분석할 수 있는 데이터의 세 가지 예시입니다.

Azure Files에 대한 자세한 내용은 [Azure Files 소개](../files/storage-files-introduction.md)를 참조하세요.

일부 SMB 기능은 클라우드에 적용되지 않습니다. 자세한 내용은 [Azure File 서비스에서 지원되지 않는 기능](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)을 참조하세요.

## <a name="queue-storage"></a>Queue 스토리지

Azure 큐 서비스는 메시지를 저장하고 검색하는 데 사용됩니다. 큐 메시지의 크기는 최대 64KB일 수 있고 큐에는 수 많은 메시지가 포함될 수 있습니다. 큐는 일반적으로 비동기적으로 처리할 메시지의 목록을 저장하는 데 사용됩니다.

예를 들어 고객이 사진을 업로드하여 각 사진에 대한 썸네일을 만들려고 한다고 가정하겠습니다. 고객이 사진을 업로드하는 동안 썸네일을 만들 때까지 기다리게 할 수 있습니다. 대신 큐를 사용할 수 있습니다. 고객이 업로드를 완료하면 큐에 메시지를 작성합니다. 그런 다음 Azure Function에서는 큐의 메시지를 검색하고 썸네일을 만듭니다. 이 프로세스의 일부는 각각 별도로 확장될 수 있으며 사용하기 위해 조정하는 경우 더 세밀하게 조정할 수 있습니다.

Azure 큐에 대한 자세한 내용은 [큐 소개](../queues/storage-queues-introduction.md)를 참조하세요.

## <a name="table-storage"></a>Table Storage

Azure Table Storage는 이제 Azure Cosmos DB의 일부입니다. Azure Table Storage 설명서를 보려면 [Azure Table Storage 개요](../tables/table-storage-overview.md)를 참조하세요. 기존 Azure Table Storage 서비스 외에도 처리량 최적화 테이블, 전역 분산 및 자동 보조 인덱스를 제공하는 새로운 Azure Cosmos DB 테이블 API가 있습니다. 프리미엄 환경에 대해 자세히 알아보고 체험하려면 [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md)를 참조하세요.

Table Storage에 대한 자세한 내용은 [Azure Table Storage 개요](../tables/table-storage-overview.md)를 참조하세요.

## <a name="disk-storage"></a>디스크 스토리지

Azure Managed Disk는 VHD(가상 하드 디스크)입니다. 온-프레미스 서버의 물리적 디스크처럼 생각할 수 있지만 가상화된 디스크입니다. Azure 관리 디스크는 Azure의 임의 IO 스토리지 개체인 페이지 Blob으로 저장됩니다. 관리 디스크를 ‘관리’라고 부르는 이유는 이가 페이지 Blob, Blob 컨테이너 및 Azure 스토리지 계정의 추상화이기 때문입니다. 관리 디스크를 사용할 경우 디스크를 프로비저닝하기만 하면 Azure에서 나머지 작업을 처리합니다.

관리 디스크에 대한 자세한 내용은 [Azure 관리 디스크 개요](../../virtual-machines/managed-disks-overview.md)를 참조하세요.

## <a name="types-of-storage-accounts"></a>스토리지 계정 유형

Azure Storage는 여러 유형의 스토리지 계정을 제공합니다. 각각의 유형은 서로 다른 기능을 지원하며 고유의 가격 책정 모델이 있습니다. 애플리케이션 계정 유형에 대한 자세한 내용은 [Azure Storage 계정 개요](storage-account-overview.md)를 참조하세요.

## <a name="secure-access-to-storage-accounts"></a>스토리지 계정에 대한 액세스 보호

Azure Storage에 대한 모든 요청은 권한이 있어야 합니다. Azure Storage는 다음과 같은 권한 부여 메서드를 지원합니다.

- **Blob, 큐 및 테이블 데이터를 위한 Azure AD(Azure Active Directory) 통합** Azure Storage는 Azure RBAC(역할 기반 액세스 제어)를 통해 Blob 및 큐 서비스에 Azure AD를 사용하여 인증 및 권한 부여를 지원합니다. Azure AD를 통해 권한 부여는 미리 보기의 Table service에서도 지원됩니다. 뛰어난 보안과 사용 편의성을 위해 Azure AD를 통한 권한 부여 요청을 권장합니다. 자세한 내용은 [Azure Storage의 데이터에 대한 액세스 권한 부여](authorize-data-access.md)를 참조하세요.
- **Azure Files에 SMB를 통한 Azure AD 권한 부여.** Azure Files는 Azure AD DS(Azure Active Directory Domain Services) 또는 온-프레미스 Active Directory Domain Services(미리 보기)를 통해 SMB(서버 메시지 블록)를 통한 ID 기반 권한 부여를 지원합니다. 도메인 조인 Windows VM은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다. 자세한 내용은 [SMB 액세스를 위한 ID 기반 인증 지원 Azure Files 개요](../files/storage-files-active-directory-overview.md) 및 [Azure Files 배포에 대한 계획](../files/storage-files-planning.md#identity)을 참조하세요.
- **공유 키를 사용하는 권한 부여.** Azure Storage Blob, 파일, 큐 및 테이블 서비스는 공유 키를 사용한 권한 부여를 지원합니다. 공유 키를 사용하는 클라이언트는 스토리지 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.
- **SAS(공유 액세스 서명)를 사용한 권한 부여.** SAS(공유 액세스 서명)는 스토리지 리소스에 URI에 추가할 수 있는 보안 토큰을 포함한 문자열입니다. 보안 토큰은 권한 및 액세스 간격과 같은 제약 조건을 캡슐화 합니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)을 참조하세요.
- **컨테이너 및 Blob에 대한 익명 액세스.** 컨테이너와 컨테이너의 Blob은 공개적으로 사용할 수 있습니다. 컨테이너 또는 Blob을 공용으로 지정하면 누구나 이를 익명으로 읽을 수 있습니다. 인증은 불필요합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/anonymous-read-access-configure.md)를 참조하세요.

## <a name="encryption"></a>암호화

핵심 스토리지 서비스에는 두 가지 종류의 기본 암호화를 사용할 수 있습니다. 보안 및 암호화에 대한 자세한 내용은 [Azure Storage 보안 가이드](../blobs/security-recommendations.md)를 참조하세요.

### <a name="encryption-at-rest"></a>미사용 암호화

Azure Storage 암호화를 사용하여 사용자 조직의 보안 및 규정 준수 약정에 맞게 데이터를 안전하게 보호할 수 있습니다. Azure Storage는 스토리지 계정에 보존하기 전에 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 암호화, 암호 해독 및 키 관리는 사용자에게 투명하게 공개됩니다. 또한 고객은 Azure Key Vault를 사용하여 자신의 키를 관리할 수 있습니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)를 참조하세요.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

Azure Storage 클라이언트 라이브러리는 네트워크를 통해 전송하기 전에 클라이언트 라이브러리에서 데이터를 암호화하고 응답을 해독하는 메서드를 제공합니다. 클라이언트 쪽 암호화를 통해 암호화된 데이터는 Azure Storage를 통해 미사용 상태에서도 암호화됩니다. 클라이언트 쪽 암호화에 대한 자세한 내용은 [Azure Storage에서 .NET을 사용한 클라이언트 쪽 암호화](storage-client-side-encryption.md)를 참조하세요.

## <a name="redundancy"></a>중복

데이터가 지속되는지 확인하기 위해 Azure Storage는 여러 데이터 복사본을 저장합니다. 스토리지 계정을 설정할 때 중복 옵션을 선택합니다. 자세한 내용은 [Azure Storage 중복성](./storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

## <a name="transfer-data-to-and-from-azure-storage"></a>Azure Storage 간 데이터 전송

Azure Storage에서 데이터를 이동하는 몇 가지 옵션이 있습니다. 선택하는 옵션은 데이터 세트의 크기와 네트워크 대역폭에 따라 달라집니다. 자세한 내용은 [데이터 전송을 위한 Azure 솔루션 선택](storage-choose-data-transfer-solution.md)을 참조하세요.

## <a name="pricing"></a>가격 책정

데이터가 저장 및 액세스되는 방식을 결정할 때 관련 비용도 고려해야 합니다. 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

## <a name="storage-apis-libraries-and-tools"></a>Storage API, 라이브러리 및 도구

HTTP/HTTPS 요청을 수행할 수 있는 모든 언어로 스토리지 계정의 리소스에 액세스할 수 있습니다. 또한 핵심 Azure Storage 서비스는 널리 사용되는 여러 언어를 위한 프로그래밍 라이브러리를 제공합니다. 이 라이브러리는 동기/비동기 호출, 작업 일괄 처리, 예외 관리, 자동 재시도, 작업자 동작 등과 같은 세부 사항을 처리하여 Azure Storage 작업의 많은 측면을 간소화합니다. 현재 이 라이브러리는 파이프라인의 다른 라이브러리와 함께 다음 언어 및 플랫폼에 대해 사용할 수 있습니다.

### <a name="azure-storage-data-api-and-library-references"></a>Azure Storage 데이터 API 및 라이브러리 참조

- [Azure Storage REST API](/rest/api/storageservices/)
- [.NET용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)
- [Java/Android용 Azure Storage 클라이언트 라이브러리](/java/api/overview/azure/storage)
- [Node.js용 Azure Storage 클라이언트 라이브러리](/javascript/api/overview/azure/storage-overview)
- [Python용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-python)
- [PHP용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php)
- [Ruby용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-ruby)
- [C++용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure Storage 관리 API 및 라이브러리 참조

- [Storage 리소스 공급자 REST API](/rest/api/storagerp/)
- [.NET용 Storage 리소스 공급자 클라이언트 라이브러리](/dotnet/api/overview/azure/storage/management)
- [Storage 서비스 관리 REST API(클래식](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure Storage 데이터 이동 API 및 라이브러리 참조

- [스토리지 Import/Export 서비스 REST API](/rest/api/storageimportexport/)
- [.NET용 스토리지 데이터 이동 클라이언트 라이브러리](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>도구 및 유틸리티

- [Storage용 Azure PowerShell Cmdlet](/powershell/module/az.storage)
- [Storage용 Azure CLI Cmdlet](/cli/azure/storage)
- [AzCopy 명령줄 유틸리티](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
- [Azure Storage용 Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>다음 단계

핵심 Azure Storage 서비스를 시작하고 실행하려면 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.