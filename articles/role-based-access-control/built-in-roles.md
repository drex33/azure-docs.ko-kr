---
title: Azure 기본 제공 역할 - Azure RBAC
description: 이 문서에서는 Azure RBAC(역할 기반 액세스 제어)의 Azure 기본 제공 역할에 대해 설명하고 Actions, NotActions, DataActions 및 NotDataActions를 나열합니다.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 10/23/2021
ms.custom: generated
ms.openlocfilehash: b8e3fe1f29650feebe5a666a60d37c4a09a4e363
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023750"
---
# <a name="azure-built-in-roles"></a>Azure 기본 제공 역할

[Azure RBAC(역할 기반 액세스 제어)](overview.md)는 사용자, 그룹, 서비스 주체 및 관리 ID에 할당할 수 있는 여러 가지 Azure 기본 제공 역할을 제공합니다. 역할 할당은 Azure 리소스에 대한 액세스를 제어하는 방법입니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [Azure 사용자 지정 역할](custom-roles.md)을 만들면 됩니다. 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할 할당 단계](role-assignments-steps.md)를 참조하세요.

이 문서에는 Azure 기본 제공 역할 목록이 포함되어 있습니다. Azure AD(Azure Active Directory)의 관리자 역할을 찾고 있는 경우 [Azure AD 기본 제공 역할](../active-directory/roles/permissions-reference.md)을 참조하세요.

다음 표에서는 각 기본 제공 역할에 대한 간략한 설명을 제공합니다. 각 역할의 `Actions`, `NotActions`, `DataActions` 및 `NotDataActions` 목록을 보려면 역할 이름을 클릭합니다. 이러한 작업의 의미와 컨트롤 및 데이터 평면에 적용되는 방법에 대한 자세한 내용은 [Azure 역할 정의 이해를 참조하세요.](role-definitions.md)

## <a name="all"></a>모두

> [!div class="mx-tableFixed"]
> | 기본 제공 역할 | 설명 | ID |
> | --- | --- | --- |
> | **일반** |  |  |
> | [기여자](#contributor) | 모든 리소스를 관리할 수 있는 모든 권한을 부여하지만, Azure RBAC에서 역할 할당, Azure Blueprints에서 할당 관리 또는 이미지 갤러리 공유를 허용하지 않습니다. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [소유자](#owner) | Azure RBAC에서 역할을 할당하는 기능을 포함하여 모든 리소스를 관리할 수 있는 모든 권한을 부여합니다. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [판독기](#reader) | 모든 리소스를 볼 수 있지만 변경할 수는 없습니다. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [사용자 액세스 관리자](#user-access-administrator) | Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **컴퓨팅** |  |  |
> | [Classic Virtual Machine 참가자](#classic-virtual-machine-contributor) | 클래식 가상 머신을 관리할 수 있지만 가상 머신이나 연결된 가상 네트워크 또는 스토리지 계정에 액세스할 수는 없습니다. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [가상 머신 관리자 로그인](#virtual-machine-administrator-login) | 포털에서 Virtual Machines를 보고 관리자 권한으로 로그인합니다. | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtual Machine 참가자](#virtual-machine-contributor) | 가상 머신을 만들고 관리하며, 디스크 및 디스크 스냅샷을 관리하고, 소프트웨어를 설치 및 실행하고, VM 확장을 사용하여 가상 머신의 루트 사용자에 대한 암호를 재설정하고, VM 확장을 사용하여 로컬 사용자 계정을 관리합니다. 이 역할은 가상 머신이 연결된 가상 네트워크 또는 스토리지 계정에 대한 관리 액세스 권한을 부여하지 않습니다. 이 역할은 Azure RBAC에서 역할을 할당하는 것을 허용하지 않습니다. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [가상 머신 사용자 로그인](#virtual-machine-user-login) | 포털에서 Virtual Machines를 보고 일반 사용자 권한으로 로그인합니다. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **네트워킹** |  |  |
> | [CDN 엔드포인트 기여자](#cdn-endpoint-contributor) | CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN 엔드포인트 독자](#cdn-endpoint-reader) | CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN 프로필 기여자](#cdn-profile-contributor) | CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN 프로필 독자](#cdn-profile-reader) | CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [클래식 네트워크 기여자](#classic-network-contributor) | 기본 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS 영역 참가자](#dns-zone-contributor) | Azure DNS의 DNS 영역과 레코드 집합을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [네트워크 기여자](#network-contributor) | 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [프라이빗 DNS 영역 기여자](#private-dns-zone-contributor) | 프라이빗 DNS 영역 리소스는 관리할 수 있으나, 연결되어 있는 가상 네트워크는 관리할 수 없습니다. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager 기여자](#traffic-manager-contributor) | Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **스토리지** |  |  |
> | [Avere 기여자](#avere-contributor) | Avere vFXT 클러스터를 만들고 관리할 수 있습니다. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere 운영자](#avere-operator) | Avere vFXT 클러스터에서 클러스터를 관리하는 데 사용됩니다. | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Backup 기여자](#backup-contributor) | 백업 서비스를 관리할 수 있지만, 자격 증명 모음을 만들고 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Backup 운영자](#backup-operator) | 백업 제거를 제외한 백업 서비스를 관리하고 자격 증명 모음 만들고 다른 사람에게 액세스 권한을 부여할 수 있습니다. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup 읽기 권한자](#backup-reader) | 백업 서비스를 볼 수 있지만 변경할 수는 없습니다. | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [클래식 Storage 계정 기여자](#classic-storage-account-contributor) | 클래식 Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [클래식 스토리지 계정 키 운영자 서비스 역할](#classic-storage-account-key-operator-service-role) | 클래식 스토리지 계정 키 운영자가 클래식 스토리지 계정에서 키를 나열하고 다시 생성할 수 있습니다. | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box 기여자](#data-box-contributor) | 다른 사람에게 액세스 권한을 부여하는 것을 제외한 모든 항목을 Data Box 서비스에서 관리할 수 있습니다. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box 읽기 권한자](#data-box-reader) | 주문하기나 주문 세부 정보 편집 및 다른 사용자에게 액세스 권한 부여 외에 Data Box 서비스를 관리할 수 있습니다. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics 개발자](#data-lake-analytics-developer) | 사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [읽기 권한자 및 데이터 액세스](#reader-and-data-access) | 모든 것을 볼 수 있지만, 스토리지 계정 또는 포함된 리소스를 삭제하거나 만들 수는 없습니다. 또한 스토리지 계정 키에 액세스하여 스토리지 계정에 포함된 모든 데이터를 읽고 쓸 수 있습니다. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Storage 계정 기여자](#storage-account-contributor) | 스토리지 계정을 관리할 수 있도록 허용합니다. 공유 키 권한 부여를 통해 데이터에 액세스하는 데 사용할 수 있는 계정 키에 대한 액세스 권한을 제공합니다. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [스토리지 계정 키 운영자 서비스 역할](#storage-account-key-operator-service-role) | 스토리지 계정 액세스 키를 나열하고 다시 생성할 수 있도록 허용합니다. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage Blob 데이터 기여자](#storage-blob-data-contributor) | Azure Storage 컨테이너 및 BLOB을 읽고, 쓰고, 삭제합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage Blob 데이터 소유자](#storage-blob-data-owner) | POSIX 액세스 제어 할당을 포함하여 Azure Storage BLOB 컨테이너 및 데이터에 대한 모든 액세스 권한을 제공합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage Blob 데이터 읽기 권한자](#storage-blob-data-reader) | Azure Storage 컨테이너 및 BLOB을 읽고 나열합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob 위임자](#storage-blob-delegator) | Azure AD 자격 증명으로 서명된 컨테이너 또는 BLOB의 공유 액세스 서명을 만드는 데 사용할 수 있는 사용자 위임 키를 가져옵니다. 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조하세요. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage 파일 데이터 SMB 공유 기여자](#storage-file-data-smb-share-contributor) | Azure 파일 공유의 파일/디렉터리에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. Windows 파일 서버에는 이 역할에 상응하는 기본 제공 역할이 없습니다. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage 파일 데이터 SMB 공유 높은 권한 기여자](#storage-file-data-smb-share-elevated-contributor) | Azure 파일 공유의 파일/디렉터리에 대한 ACL을 읽고, 쓰고, 삭제하고, 수정할 수 있습니다. 이 역할은 Windows 파일 서버의 변경 내용에 대한 파일 공유 ACL에 해당합니다. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage 파일 데이터 SMB 공유 읽기 권한자](#storage-file-data-smb-share-reader) | Azure 파일 공유의 파일/디렉터리에 대한 읽기 액세스를 허용합니다. 이 역할은 Windows 파일 서버에 대한 파일 공유 ACL 읽기에 해당합니다. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Storage 큐 데이터 기여자](#storage-queue-data-contributor) | Azure Storage 큐 및 큐 메시지를 읽고, 쓰고, 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Storage 큐 데이터 메시지 처리자](#storage-queue-data-message-processor) | Azure Storage 큐의 메시지를 선택, 검색 및 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Storage 큐 데이터 메시지 보내는 사람](#storage-queue-data-message-sender) | Azure Storage 큐에 메시지를 추가할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage 큐 데이터 읽기 권한자](#storage-queue-data-reader) | Azure Storage 큐 및 큐 메시지를 읽고 나열할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. | 19e7f393-937e-4f77-808e-94535e297925 |
> | [스토리지 테이블 데이터 기여자](#storage-table-data-contributor) | Azure Storage 테이블 및 엔터티에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. | 0a9a7e1f-b9d0-4cc4-a60d-0319b160aaa3 |
> | [스토리지 테이블 데이터 읽기 권한자](#storage-table-data-reader) | Azure Storage 테이블 및 엔터티에 대한 읽기 권한을 허용합니다. | 76199698-9eea-4c19-bc75-cec21354c6b6 |
> | **Web** |  |  |
> | [Azure Maps 데이터 기여자](#azure-maps-data-contributor) | Azure Maps 계정에서 지도 관련 데이터를 읽고, 쓰고, 삭제할 수 있는 액세스 권한을 부여합니다. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps 데이터 읽기 권한자](#azure-maps-data-reader) | Azure 맵 계정에서 맵 관련 데이터를 읽을 수 있는 액세스 권한을 부여합니다. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Spring Cloud Config Server 기여자](#azure-spring-cloud-config-server-contributor) | Azure 스프링 클라우드 구성 서버에 대 한 읽기, 쓰기 및 삭제 액세스 허용 | a06f5c24-21a7-4e1a-aa2b-f19eb6684f5b |
> | [Azure Spring Cloud 구성 서버 읽기 권한자](#azure-spring-cloud-config-server-reader) | Azure 스프링 클라우드 구성 서버에 대 한 읽기 액세스 허용 | d04c6db6-4947-4782-9e91-30a88feb7be7 |
> | [Azure Spring Cloud 데이터 판독기](#azure-spring-cloud-data-reader) | Azure Spring Cloud 데이터에 대한 읽기 권한 허용 | b5537268-8956-4941-a8f0-646150406f0c |
> | [Azure Spring Cloud Service Registry 기여자](#azure-spring-cloud-service-registry-contributor) | Azure 스프링 클라우드 서비스 레지스트리에 대 한 읽기, 쓰기 및 삭제 액세스 허용 | f5880b48-c26d-48be-b172-7927bfa1c8f1 |
> | [Azure Spring Cloud Service Registry 읽기 권한자](#azure-spring-cloud-service-registry-reader) | Azure 스프링 클라우드 서비스 레지스트리에 대 한 읽기 액세스 허용 | cff1b556-2399-4e7e-856d-a8f754be7b65 |
> | [Media Services 계정 관리자](#media-services-account-administrator) | Media Services 계정을 생성하고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다. | 054126f8-9a2b-4f1c-a9ad-eca461f08466 |
> | [Media Services 라이브 이벤트 관리자](#media-services-live-events-administrator) | 라이브 이벤트, 자산, 자산 필터, 스트리밍 로케이터를 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다. | 532bc159-b25e-42c0-969e-a1d439f60d77 |
> | [Media Services 미디어 운영자](#media-services-media-operator) | 자산, 자산 필터, 스트리밍 로케이터, 작업을 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다. | e4395492-1534-4db2-bedf-88c14621589c |
> | [Media Services 정책 관리자](#media-services-policy-administrator) | 계정 필터, 스트리밍 정책, 콘텐츠 키 정책 및 변환을 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다. 작업, 자산, 스트리밍 리소스를 만들 수 없습니다. | c4bba371-dacd-4a26-b320-7250bca963ae |
> | [Media Services 스트리밍 엔드포인트 관리자](#media-services-streaming-endpoints-administrator) | 스트리밍 엔드포인트를 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다. | 99dba123-b5fe-44d5-874c-ced7199a5804 |
> | [검색 인덱스 데이터 기여자](#search-index-data-contributor) | Azure Cognitive Search 인덱스 데이터에 대한 모든 액세스 권한을 부여합니다. | 8ebe5a00-799e-43f5-93ac-243d3dce84a7 |
> | [검색 인덱스 데이터 읽기 권한자](#search-index-data-reader) | Azure Cognitive Search 인덱스 데이터에 대한 읽기 권한을 부여합니다. | 1407120a-92aa-4202-b7e9-c0e197c71c8f |
> | [Search 서비스 기여자](#search-service-contributor) | Search 서비스를 관리할 수 있지만 액세스할 수는 없습니다. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey 읽기 권한자](#signalr-accesskey-reader) | SignalR 서비스 액세스 키 읽기 | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR 앱 서버(미리 보기)](#signalr-app-server-preview) | 앱 서버에서 AAD 인증 옵션을 사용하여 SignalR Service에 액세스할 수 있습니다. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR REST API 소유자](#signalr-rest-api-owner) | Azure SignalR Service REST API에 대한 모든 권한 | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR REST API 판독기](#signalr-rest-api-reader) | Azure SignalR Service REST API에 대한 읽기 전용 권한 | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [SignalR Service 소유자](#signalr-service-owner) | Azure SignalR Service REST API에 대한 모든 권한 | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR/웹 PubSub 기여자](#signalrweb-pubsub-contributor) | SignalR Service 리소스 만들기, 읽기, 업데이트, 삭제 | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [웹 계획 참가자](#web-plan-contributor) | 웹 사이트에 대 한 웹 요금제를 관리 합니다. Azure RBAC에서 역할을 할당 하는 것을 허용 하지 않습니다. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [웹 사이트 기여자](#website-contributor) | 웹 요금제는 관리 하지 않고 웹 사이트를 관리 합니다. Azure RBAC에서 역할을 할당 하는 것을 허용 하지 않습니다. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **컨테이너** |  |  |
> | [AcrDelete](#acrdelete) | 컨테이너 레지스트리에서 리포지토리, 태그, 매니페스트를 삭제합니다. | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | 콘텐츠 신뢰를 사용하도록 설정된 컨테이너 레지스트리에 신뢰할 수 있는 이미지를 푸시하거나 설정된 컨테이너 레지스트리에서 신뢰할 수 있는 이미지를 끌어옵니다. | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | 컨테이너 레지스트리에서 아티팩트를 끌어옵니다. | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | 컨테이너 레지스트리에 아티팩트를 푸시하거나 또는 컨테이너 레지스트리에서 아티팩트를 끌어옵니다. | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | 컨테이너 레지스트리에서 격리된 이미지를 끌어옵니다. | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | 컨테이너 레지스트리에 격리된 이미지를 푸시하거나 컨테이너 레지스트리에서 격리된 이미지를 끌어옵니다. | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service 클러스터 관리자 역할](#azure-kubernetes-service-cluster-admin-role) | 클러스터 관리자 자격 증명 작업을 나열합니다. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service 클러스터 사용자 역할](#azure-kubernetes-service-cluster-user-role) | 클러스터 사용자 자격 증명 작업을 나열합니다. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service 기여자 역할](#azure-kubernetes-service-contributor-role) | Azure Kubernetes Service 클러스터를 읽고 쓸 수 있는 액세스 권한을 부여합니다. | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC 관리자](#azure-kubernetes-service-rbac-admin) | 리소스 할당량 및 네임스페이스 업데이트 또는 삭제를 제외하고 클러스터/네임스페이스의 모든 리소스를 관리할 수 있습니다. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC 클러스터 관리자](#azure-kubernetes-service-rbac-cluster-admin) | 클러스터의 모든 리소스를 관리할 수 있습니다. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC 읽기 권한자](#azure-kubernetes-service-rbac-reader) | 네임스페이스에 있는 대부분의 개체를 볼 수 있는 읽기 전용 권한을 허용합니다. 역할 또는 역할 바인딩을 볼 수 없습니다. 이 역할은 비밀을 볼 수 없습니다. 비밀 내용을 읽으면 네임스페이스의 ServiceAccount 자격 증명에 액세스할 수 있으므로 네임스페이스의 ServiceAccount로 API 액세스가 허용될 수 있기 때문입니다(일종의 권한 상승). 클러스터 범위에 이 역할을 적용하면 모든 네임스페이스에 대한 액세스 권한이 부여됩니다. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC 쓰기 권한자](#azure-kubernetes-service-rbac-writer) | 네임스페이스의 대부분의 개체에 대한 읽기/쓰기 권한을 허용합니다. 이 역할은 역할이나 역할 바인딩을 보거나 수정할 수 없습니다. 그러나 이 역할을 사용하여 네임스페이스의 ServiceAccount로 Pod를 실행하고 비밀에 액세스할 수 있으므로 네임스페이스에 있는 모든 ServiceAccount의 API 액세스 수준을 얻을 수 있습니다. 클러스터 범위에 이 역할을 적용하면 모든 네임스페이스에 대한 액세스 권한이 부여됩니다. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **데이터베이스** |  |  |
> | [Azure Connected SQL Server 온보딩](#azure-connected-sql-server-onboarding) | Arc 지원 서버에서 SQL Server용 Azure 리소스에 대한 읽기 및 쓰기 권한을 허용합니다. | e8113dce-c529-4d33-91fa-e9b972617508 |
> | [Cosmos DB 계정 독자 역할](#cosmos-db-account-reader-role) | Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB 운영자](#cosmos-db-operator) | Azure Cosmos DB 계정을 관리할 수 있지만 계정의 데이터에 액세스할 수는 없습니다. 계정 키 및 연결 문자열에 대한 액세스를 차단합니다. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Cosmos DB 데이터베이스 또는 계정의 컨테이너에 대한 복원 요청을 제출할 수 있습니다. | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | 지속적인 백업 모드를 사용하여 Cosmos DB 데이터베이스 계정의 복원 작업을 수행할 수 있습니다. | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [DocumentDB 계정 기여자](#documentdb-account-contributor) | Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache 참가자](#redis-cache-contributor) | Redis Cache를 관리할 수 있지만 액세스할 수는 없습니다. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB 기여자](#sql-db-contributor) | SQL 데이터베이스를 관리할 수 있지만 액세스할 수는 없습니다. 또한 보안 관련 정책이나 부모 SQL 서버를 관리할 수 없습니다. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance 기여자](#sql-managed-instance-contributor) | SQL Managed Instances 및 필수 네트워크 구성을 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL 보안 관리자](#sql-security-manager) | SQL Server 및 데이터베이스의 보안과 관련된 정책을 관리할 수 있지만 여기에 액세스할 수는 없습니다. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server 기여자](#sql-server-contributor) | SQL Server 및 데이터베이스를 관리할 수 있지만 액세스할 수는 없으며, 해당하는 보안 관련 정책에도 액세스할 수 없습니다. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **분석** |  |  |
> | [Azure Event Hubs 데이터 소유자](#azure-event-hubs-data-owner) | Azure Event Hubs 리소스에 대한 전체 액세스를 허용합니다. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs 데이터 받는 사람](#azure-event-hubs-data-receiver) | Azure Event Hubs 리소스에 대한 받기 액세스 권한을 허용합니다. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs 데이터 보내는 사람](#azure-event-hubs-data-sender) | Azure Event Hubs 리소스에 대한 보내기 액세스 권한을 허용합니다. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory 참가자](#data-factory-contributor) | 데이터 팩터리를 만들고 관리하며 해당 하위 리소스도 만들고 관리합니다. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [데이터 제거자](#data-purger) | Log Analytics 작업 영역에서 프라이빗 데이터를 삭제합니다. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight 클러스터 운영자](#hdinsight-cluster-operator) | HDInsight 클러스터 구성을 읽고 수정할 수 있습니다. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight 도메인 서비스 기여자](#hdinsight-domain-services-contributor) | HDInsight Enterprise Security Package에 필요한 도메인 서비스 관련 작업을 읽고, 만들고, 수정하고, 삭제할 수 있음 | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics 기여자](#log-analytics-contributor) | Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics 독자](#log-analytics-reader) | Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [부서의 범위 Data 큐레이터 (레거시)](#purview-data-curator-legacy) | 부서의 범위 data 큐레이터는 카탈로그 데이터 개체를 만들고, 읽고, 수정 하 고, 삭제 하 고, 개체 간의 관계를 설정할 수 있는 레거시 역할입니다. 최근 Azure 역할 기반 액세스에서이 역할을 사용 하지 않고 Azure 부서의 범위 데이터 평면 내에 새 데이터 큐레이터를 도입 했습니다. [Azure 부서의 범위의 액세스 제어를](../purview/catalog-permissions.md#roles) 참조 하세요. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [부서의 범위 데이터 판독기 (레거시)](#purview-data-reader-legacy) | 부서의 범위 데이터 판독기는 카탈로그 데이터 개체를 읽을 수 있는 레거시 역할입니다. 최근 Azure 역할 기반 액세스에서이 역할을 사용 하지 않고 Azure 부서의 범위 데이터 평면 내에 새로운 데이터 판독기를 도입 했습니다. [Azure 부서의 범위의 액세스 제어를](../purview/catalog-permissions.md#roles) 참조 하세요. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [부서의 범위 데이터 원본 관리자 (레거시)](#purview-data-source-administrator-legacy) | 부서의 범위 데이터 원본 관리자는 데이터 원본 및 데이터 검색을 관리할 수 있는 레거시 역할입니다. 최근 Azure 역할 기반 액세스에서이 역할을 사용 하지 않고 Azure 부서의 범위 데이터 평면 내에 새로운 데이터 원본 관리자를 도입 했습니다. [Azure 부서의 범위의 액세스 제어를](../purview/catalog-permissions.md#roles) 참조 하세요. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [스키마 레지스트리 기여자(미리 보기)](#schema-registry-contributor-preview) | 스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [스키마 레지스트리 판독기(미리 보기)](#schema-registry-reader-preview) | 스키마 레지스트리 그룹 및 스키마를 읽고 나열합니다. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **블록체인** |  |  |
> | [블록체인 멤버 노드 액세스(미리 보기)](#blockchain-member-node-access-preview) | 블록체인 멤버 노드에 액세스할 수 있습니다. | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + 기계 학습** |  |  |
> | [AzureML 데이터 과학자](#azureml-data-scientist) | 컴퓨팅 리소스를 만들거나 삭제하고 작업 영역 자체를 수정하는 것을 제외하고 Azure Machine Learning 작업 영역에서 모든 작업을 수행할 수 있습니다. | f6c7c914-8db3-469d-8ca1-694a8f32e121 |
> | [Cognitive Services 기여자](#cognitive-services-contributor) | Cognitive Services의 키를 만들고, 읽고, 업데이트하고, 삭제 및 관리할 수 있습니다. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision 기여자](#cognitive-services-custom-vision-contributor) | 프로젝트를 보거나, 만들거나, 편집하거나, 삭제할 수 있는 기능을 포함하는 프로젝트에 대한 모든 권한입니다. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Cognitive Services Custom Vision 배포](#cognitive-services-custom-vision-deployment) | 모델을 게시하거나, 게시 취소하거나, 내보냅니다. 배포에서 프로젝트를 볼 수는 있지만 업데이트할 수는 없습니다. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision 레이블 지정자](#cognitive-services-custom-vision-labeler) | 학습 이미지를 보고 편집하고, 이미지 태그를 만들거나, 추가하거나, 제거하거나, 삭제합니다. 레이블 지정자는 프로젝트를 볼 수 있지만, 학습 이미지 및 태그 이외의 항목을 업데이트할 수 없습니다. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision 읽기 권한자](#cognitive-services-custom-vision-reader) | 프로젝트의 읽기 전용 작업입니다. 읽기 권한자는 프로젝트를 만들거나 업데이트할 수 없습니다. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision 강사](#cognitive-services-custom-vision-trainer) | 모델을 게시, 게시 취소, 내보내는 기능을 포함하여 프로젝트를 보고 편집하고 모델을 학습합니다. 강사는 프로젝트를 만들거나 삭제할 수 없습니다. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services 데이터 읽기 권한자(미리 보기)](#cognitive-services-data-reader-preview) | Cognitive Services 데이터를 읽을 수 있습니다. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services 얼굴 인식기](#cognitive-services-face-recognizer) | Face API에서 유사한 작업을 검색, 확인, 식별, 그룹화하고 찾을 수 있습니다. 이 역할은 만들기 또는 삭제 작업을 허용하지 않으므로, ‘최소 권한’ 모범 사례에 따라 추론 기능만 필요한 엔드포인트에 적합합니다. | 9894cab4-e18a-44aa-828b-cb588cd6f2d7 |
> | [Cognitive Services Metrics Advisor 관리자](#cognitive-services-metrics-advisor-administrator) | 시스템 수준 구성을 포함하는 프로젝트에 대한 모든 권한입니다. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker 편집기](#cognitive-services-qna-maker-editor) | KB를 만들고, 편집하고, 가져오고, 내보낼 수 있습니다. KB를 게시하거나 삭제할 수 없습니다. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker 읽기 권한자](#cognitive-services-qna-maker-reader) | KB만 읽고 테스트할 수 있습니다. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services 사용자](#cognitive-services-user) | Cognitive Services의 키를 읽고 나열할 수 있습니다. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **사물 인터넷** |  |  |
> | [디바이스 업데이트 관리자](#device-update-administrator) | 관리 및 콘텐츠 작업에 대한 모든 권한을 제공합니다. | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [디바이스 업데이트 콘텐츠 관리자](#device-update-content-administrator) | 콘텐츠 작업에 대한 모든 권한을 제공합니다. | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [디바이스 업데이트 콘텐츠 읽기 권한자](#device-update-content-reader) | 콘텐츠 작업에 대한 읽기 권한을 제공하지만, 변경은 허용하지 않습니다. | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [디바이스 업데이트 배포 관리자](#device-update-deployments-administrator) | 관리 작업에 대한 모든 권한을 제공합니다. | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [디바이스 업데이트 배포 읽기 권한자](#device-update-deployments-reader) | 관리 작업에 대한 읽기 권한을 제공하지만, 변경은 허용하지 않습니다. | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [디바이스 업데이트 읽기 권한자](#device-update-reader) | 관리 및 콘텐츠 작업에 대한 읽기 권한을 제공하지만, 변경은 허용하지 않습니다. | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | [IoT Hub 데이터 기여자](#iot-hub-data-contributor) | IoT Hub 데이터 평면 작업에 대한 모든 권한을 허용합니다. | 4fc6c259-987e-4a07-842e-c321cc9d413f |
> | [IoT Hub 데이터 판독기](#iot-hub-data-reader) | IoT Hub 데이터 평면 속성에 대한 전체 읽기 권한을 허용합니다 | b447c946-2db7-41ec-983d-d8bf3b1c77e3 |
> | [IoT Hub 레지스트리 기여자](#iot-hub-registry-contributor) | IoT Hub 디바이스 레지스트리에 대한 모든 권한을 허용합니다. | 4ea46cd5-c1b2-4a8e-910b-273211f9ce47 |
> | [IoT Hub 쌍 기여자](#iot-hub-twin-contributor) | 모든 IoT Hub 디바이스 및 모듈 쌍에 대한 읽기 및 쓰기 권한을 허용합니다. | 494bdba2-168f-4f31-a0a1-191d2f7c028c |
> | **혼합 현실** |  |  |
> | [Remote Rendering 관리자](#remote-rendering-administrator) | 사용자에게 Azure Remote Rendering에 대한 변환, 세션 관리, 렌더링, 진단 기능을 제공합니다. | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering 클라이언트](#remote-rendering-client) | 사용자에게 Azure Remote Rendering에 대한 세션 관리, 렌더링, 진단 기능을 제공합니다. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors 계정 기여자](#spatial-anchors-account-contributor) | 계정의 공간 앵커를 관리할 수 있지만 삭제할 수는 없습니다. | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors 계정 소유자](#spatial-anchors-account-owner) | 계정의 공간 앵커를 관리할 수 있고 삭제할 수도 있습니다. | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors 계정 읽기 권한자](#spatial-anchors-account-reader) | 계정의 공간 앵커 속성을 찾아서 읽을 수 있습니다. | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **통합** |  |  |
> | [API Management 서비스 참가자](#api-management-service-contributor) | 서비스 및 API를 관리할 수 있습니다. | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management 서비스 운영자 역할](#api-management-service-operator-role) | 서비스를 관리할 수 있지만 API는 관리할 수 없습니다. | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Azure API Management 읽기 권한자 역할](#api-management-service-reader-role) | 서비스 및 API에 대한 읽기 전용 액세스 | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration 데이터 소유자](#app-configuration-data-owner) | App Configuration 데이터에 대한 모든 액세스 권한을 허용합니다. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration 데이터 읽기 권한자](#app-configuration-data-reader) | App Configuration 데이터에 대한 읽기 액세스 권한을 허용합니다. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Relay 수신자](#azure-relay-listener) | Azure Relay 리소스에 대한 수신 대기 액세스 권한을 허용합니다. | 26e0b698-aa6d-4085-9386-aadae190014d |
> | [Azure Relay 소유자](#azure-relay-owner) | Azure Relay 리소스에 대한 전체 액세스 권한을 허용합니다. | 2787bf04-f1f5-4bfe-8383-c8a24483ee38 |
> | [Azure Relay 발신자](#azure-relay-sender) | Azure Relay 리소스에 대한 보내기 액세스 권한을 허용합니다. | 26baccc8-eea7-41f1-98f4-1762cc7f685d |
> | [Azure Service Bus 데이터 소유자](#azure-service-bus-data-owner) | Azure Service Bus 리소스에 대한 전체 액세스를 허용합니다. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus 데이터 받는 사람](#azure-service-bus-data-receiver) | Azure Service Bus 리소스에 대한 받기 액세스 권한을 허용합니다. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus 데이터 보내는 사람](#azure-service-bus-data-sender) | Azure Service Bus 리소스에 대한 보내기 액세스 권한을 허용합니다. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack 등록 소유자](#azure-stack-registration-owner) | Azure Stack 등록을 관리할 수 있습니다. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid 기여자](#eventgrid-contributor) | EventGrid 작업을 관리할 수 있습니다. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [EventGrid 데이터 발신자](#eventgrid-data-sender) | Event Grid 이벤트에 대한 보내기 액세스 권한을 허용합니다. | d5a91429-5739-47e2-a06b-3470a27159e7 |
> | [EventGrid EventSubscription 기여자](#eventgrid-eventsubscription-contributor) | EventGrid 이벤트 구독 작업을 관리할 수 있습니다. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription 읽기 권한자](#eventgrid-eventsubscription-reader) | EventGrid 이벤트 구독을 읽을 수 있습니다. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR 데이터 기여자](#fhir-data-contributor) | 역할을 통해 사용자 또는 보안 주체에게 FHIR 데이터에 대한 모든 권한을 제공할 수 있습니다. | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR 데이터 내보내기 도구](#fhir-data-exporter) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터 읽고 내보낼 수 있습니다. | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR 데이터 읽기 권한자](#fhir-data-reader) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터 읽을 수 있습니다. | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR 데이터 쓰기 권한자](#fhir-data-writer) | 역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 쓸 수 있습니다. | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [통합 서비스 환경 기여자](#integration-service-environment-contributor) | 통합 서비스 환경을 관리할 수 있지만 액세스할 수는 없습니다. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [통합 서비스 환경 개발자](#integration-service-environment-developer) | 개발자가 통합 서비스 환경에서 워크플로, 통합 계정, API 연결을 만들고 업데이트할 수 있습니다. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [지능형 시스템 계정 기여자](#intelligent-systems-account-contributor) | 인텔리전트 시스템 계정을 관리할 수 있지만 액세스할 수는 없습니다. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [논리 앱 기여자](#logic-app-contributor) | 논리 앱을 관리할 수 있지만 앱을 변경할 수는 없습니다. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [논리 앱 운영자](#logic-app-operator) | 논리 앱을 읽고 사용하도록 설정하고 사용하지 않도록 설정할 수 있지만 편집하거나 업데이트할 수는 없습니다. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **ID** |  |  |
> | [관리 ID 기여자](#managed-identity-contributor) | 사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다. | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [관리 ID 운영자](#managed-identity-operator) | 사용자 할당 ID를 읽고 할당합니다. | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **보안** |  |  |
> | [증명 기여자](#attestation-contributor) | 증명 공급자 인스턴스를 읽고 쓰거나 삭제할 수 있습니다. | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [증명 판독기](#attestation-reader) | 증명 공급자 속성을 읽을 수 있습니다. | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Automation 기여자](#azure-sentinel-automation-contributor) | Azure Sentinel Automation 기여자 | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Azure Sentinel 기여자](#azure-sentinel-contributor) | Azure Sentinel 기여자 | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel 읽기 권한자](#azure-sentinel-reader) | Azure Sentinel 읽기 권한자 | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel 응답자](#azure-sentinel-responder) | Azure Sentinel 응답자 | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault 관리자](#key-vault-administrator) | 인증서, 키, 비밀을 포함하여 Key Vault 및 해당 Key Vault에 있는 모든 개체에 대한 모든 데이터 평면 작업을 수행합니다. Key Vault 리소스를 관리하거나 역할 할당을 관리할 수 없습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault 인증서 책임자](#key-vault-certificates-officer) | 권한 관리를 제외한 Key Vault의 인증서에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [키 자격 증명 모음 기여자](#key-vault-contributor) | Key Vault를 관리하지만, Azure RBAC에서 역할을 할당하는 것을 허용하지 않으며 비밀, 키 또는 인증서에 액세스할 수 없습니다. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault 암호화 책임자](#key-vault-crypto-officer) | 권한 관리를 제외한 Key Vault 키에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault 암호화 서비스 암호화 사용자](#key-vault-crypto-service-encryption-user) | 키의 메타데이터를 읽고 래핑/래핑 해제 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault 암호화 사용자](#key-vault-crypto-user) | 키를 사용하여 암호화 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault 읽기 권한자](#key-vault-reader) | Key Vault 및 해당 인증서, 키, 비밀의 메타데이터를 읽습니다. 비밀 내용 또는 키 자료와 같은 중요한 값을 읽을 수 없습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault 비밀 책임자](#key-vault-secrets-officer) | 권한 관리를 제외한 Key Vault의 비밀에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault 비밀 사용자](#key-vault-secrets-user) | 비밀 내용을 읽습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [관리형 HSM 기여자](#managed-hsm-contributor) | 관리형 HSM 풀을 관리할 수 있지만 액세스할 수는 없습니다. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [보안 관리자](#security-admin) | Security Center에 대한 권한을 살펴보고 업데이트할 수 있습니다. 보안 읽기 권한자 역할과 동일한 권한이며, 보안 정책을 업데이트하고 경고 및 권장 사항을 해제할 수도 있습니다. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [보안 평가 기여자](#security-assessment-contributor) | Security Center로 평가를 푸시할 수 있습니다. | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [보안 관리자(레거시)](#security-manager-legacy) | 레거시 역할입니다. 그 대신 보안 관리자를 사용하세요. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [보안 판독기](#security-reader) | Security Center에 대한 권한을 살펴볼 수 있습니다. 권장 사항, 경고, 보안 정책 및 보안 상태를 볼 수 있지만 변경할 수는 없습니다. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Lab 사용자](#devtest-labs-user) | Azure DevTest Labs의 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있습니다. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [랩 작성자](#lab-creator) | Azure Lab Accounts에서 새 랩을 만들 수 있습니다. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **모니터** |  |  |
> | [Application Insights 구성 요소 기여자](#application-insights-component-contributor) | Application Insights 구성 요소를 관리할 수 있음 | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights 스냅샷 디버거](#application-insights-snapshot-debugger) | Application Insights 스냅샷 디버거를 사용하여 수집한 디버그 스냅샷을 보고 다운로드할 수 있는 사용자 권한을 제공합니다. 이러한 사용 권한은 [소유자](#owner) 또는 [기여자](#contributor) 역할에 포함되지 않습니다. 사용자에게 Application Insights 스냅샷 디버거 역할을 부여할 때 사용자에게 직접 역할을 부여해야 합니다. 이 역할은 사용자 지정 역할에 추가될 때 인식되지 않습니다. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Monitoring Contributor](#monitoring-contributor) | 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [모니터링 메트릭 게시자](#monitoring-metrics-publisher) | Azure 리소스에 대한 게시 메트릭 사용 | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) | 모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [통합 문서 기여자](#workbook-contributor) | 공유 통합 문서를 저장할 수 있습니다. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [통합 문서 읽기 권한자](#workbook-reader) | 통합 문서를 읽을 수 있습니다. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **관리 + 거버넌스** |  |  |
> | [Automation 기여자](#automation-contributor) | Azure Automation을 사용하여 Azure Automation 리소스 및 기타 리소스를 관리합니다. | f353d9bd-d4a6-484e-a77a-8050b599b867 |
> | [Automation 작업 연산자](#automation-job-operator) | Automation Runbook을 사용하여 작업을 만들고 관리합니다. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation 운영자](#automation-operator) | 자동화 연산자는 작업을 시작, 중지, 일시 중단 및 다시 시작할 수 있습니다. | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook 연산자](#automation-runbook-operator) | Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Arc 지원 Kubernetes 클러스터 사용자 역할](#azure-arc-enabled-kubernetes-cluster-user-role) | 클러스터 사용자 자격 증명 동작을 나열합니다. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc Kubernetes 관리자](#azure-arc-kubernetes-admin) | 리소스 할당량 및 네임스페이스 업데이트 또는 삭제를 제외하고 클러스터/네임스페이스의 모든 리소스를 관리할 수 있습니다. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc Kubernetes 클러스터 관리자](#azure-arc-kubernetes-cluster-admin) | 클러스터의 모든 리소스를 관리할 수 있습니다. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc Kubernetes 뷰어](#azure-arc-kubernetes-viewer) | 비밀을 제외한 클러스터/네임스페이스의 모든 리소스를 볼 수 있습니다. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes 작성자](#azure-arc-kubernetes-writer) | (클러스터)역할 및 (클러스터)역할 바인딩을 제외하고 클러스터/네임스페이스의 모든 항목을 업데이트할 수 있습니다. | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Azure Connected Machine 온보딩](#azure-connected-machine-onboarding) | Azure Connected Machines을 온보딩할 수 있습니다. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine 리소스 관리자](#azure-connected-machine-resource-administrator) | Azure Connected Machines을 읽고, 쓰고, 삭제하고, 다시 온보딩할 수 있습니다. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [청구 읽기 권한자](#billing-reader) | 결제 데이터에 대해 읽기 권한 허용 | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [청사진 기여자](#blueprint-contributor) | 청사진 정의를 관리할 수 있지만 할당할 수는 없습니다. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [청사진 연산자](#blueprint-operator) | 게시된 기존 청사진을 할당할 수 있지만 새 청사진을 만들 수는 없습니다. 이 역할은 사용자가 할당한 관리 ID를 사용하여 할당하는 경우에만 작동합니다. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management 기여자](#cost-management-contributor) | 비용을 확인하고 비용 구성(예: 예산, 내보내기)을 관리할 수 있음 | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management 읽기 권한자](#cost-management-reader) | 비용 데이터 및 구성(예: 예산, 내보내기)을 확인할 수 있음 | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [계층 구조 설정 관리자](#hierarchy-settings-administrator) | 사용자가 계층 구조 설정을 편집하고 삭제할 수 있습니다. | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes 클러스터 - Azure Arc 온보딩](#kubernetes-cluster---azure-arc-onboarding) | connectedClusters 리소스를 만들기 위해 모든 사용자/서비스에 권한을 부여하는 역할 정의 | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Kubernetes 확장 기여자](#kubernetes-extension-contributor) | Kubernetes 확장을 생성, 업데이트, 가져오기, 나열 및 삭제하고 확장 비동기 작업을 가져올 수 있습니다. | 85cb6faf-e071-4c9b-8136-154b5a04f717 |
> | [관리형 애플리케이션 기여자 역할](#managed-application-contributor-role) | 관리형 애플리케이션 리소스를 만들 수 있습니다. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [관리되는 애플리케이션 운영자 역할](#managed-application-operator-role) | 관리되는 애플리케이션 리소스에서 작업을 읽고 수행할 수 있습니다. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Managed Applications 읽기 권한자](#managed-applications-reader) | 관리 앱 및 요청 JIT 액세스에서 리소스를 읽을 수 있습니다. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [관리형 서비스 등록 할당 삭제 역할](#managed-services-registration-assignment-delete-role) | 관리형 서비스 등록 할당 삭제 역할은 관리하는 테넌트 사용자가 테넌트에 할당된 등록 할당을 삭제할 수 있도록 허용합니다. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [관리 그룹 참가자](#management-group-contributor) | 관리 그룹 참가자 역할 | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [관리 그룹 읽기 권한자](#management-group-reader) | 관리 그룹 읽기 권한자 역할 | ac63b705-f282-497d-ac71-919bf39d939d |
> | [NewRelic APM 계정 기여자](#new-relic-apm-account-contributor) | New Relic Application Performance Management 계정 및 애플리케이션을 관리할 수 있지만 액세스할 수는 없습니다. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights 데이터 쓰기 권한자(미리 보기)](#policy-insights-data-writer-preview) | 리소스 정책에 대한 읽기 액세스 권한과 리소스 구성 요소 정책 이벤트에 대한 쓰기 액세스 권한을 허용합니다. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [할당량 요청 운영자](#quota-request-operator) | 할당량 요청을 읽고 만들고, 할당량 요청 상태를 가져오고, 지원 티켓을 만듭니다. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [예약 구매자](#reservation-purchaser) | 예약을 구매할 수 있습니다. | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [리소스 정책 기여자](#resource-policy-contributor) | 리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 사용자입니다. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery 기여자](#site-recovery-contributor) | 자격 증명 모음 만들기 및 역할 할당을 제외한 Site Recovery 서비스를 관리할 수 있습니다. | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery 운영자](#site-recovery-operator) | 장애 조치(failover) 및 장애 복구(failback)를 수행할 수 있지만 다른 Site Recovery 관리 작업은 수행할 수 없습니다. | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery 구독자](#site-recovery-reader) | Site Recovery 상태를 볼 수 있지만 다른 관리 작업은 수행할 수 없습니다. | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [지원 요청 참가자](#support-request-contributor) | 지원 요청을 만들고 관리할 수 있습니다. | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [태그 기여자](#tag-contributor) | 엔터티의 태그를 관리할 수 있으며, 엔터티 자체에 대한 액세스 권한은 없습니다. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **기타** |  |  |
> | [Azure Digital Twins 데이터 소유자](#azure-digital-twins-data-owner) | Digital Twins 데이터 평면에 대한 모든 권한 역할 | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins 데이터 읽기 권한자](#azure-digital-twins-data-reader) | Digital Twins 데이터 평면 속성에 대한 읽기 전용 역할 | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk 참가자](#biztalk-contributor) | BizTalk Services를 관리할 수 있지만 액세스할 수는 없습니다. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [데스크톱 가상화 애플리케이션 그룹 기여자](#desktop-virtualization-application-group-contributor) | 데스크톱 가상화 애플리케이션 그룹의 기여자입니다. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [데스크톱 가상화 애플리케이션 그룹 읽기 권한자](#desktop-virtualization-application-group-reader) | 데스크톱 가상화 애플리케이션 그룹의 읽기 권한자입니다. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [데스크톱 가상화 기여자](#desktop-virtualization-contributor) | 데스크톱 가상화의 기여자입니다. | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [데스크톱 가상화 호스트 풀 기여자](#desktop-virtualization-host-pool-contributor) | 데스크톱 가상화 호스트 풀의 기여자입니다. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [데스크톱 가상화 호스트 풀 읽기 권한자](#desktop-virtualization-host-pool-reader) | 데스크톱 가상화 호스트 풀의 읽기 권한자입니다. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [데스크톱 가상화 읽기 권한자](#desktop-virtualization-reader) | 데스크톱 가상화의 읽기 권한자입니다. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [데스크톱 가상화 세션 호스트 운영자](#desktop-virtualization-session-host-operator) | 데스크톱 가상화 세션 호스트의 운영자입니다. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [데스크톱 가상화 사용자](#desktop-virtualization-user) | 사용자가 애플리케이션 그룹의 애플리케이션을 사용할 수 있도록 허용합니다. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [데스크톱 가상화 사용자 세션 운영자](#desktop-virtualization-user-session-operator) | 데스크톱 가상화 사용자 세션의 운영자입니다. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [데스크톱 가상화 작업 영역 기여자](#desktop-virtualization-workspace-contributor) | 데스크톱 가상화 작업 영역의 기여자입니다. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [데스크톱 가상화 작업 영역 읽기 권한자](#desktop-virtualization-workspace-reader) | 데스크톱 가상화 작업 영역의 읽기 권한자입니다. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [디스크 백업 읽기 권한자](#disk-backup-reader) | 디스크 백업을 수행하려면 백업 자격 증명 모음에 대한 권한을 제공합니다. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Disk Pool 연산자](#disk-pool-operator) | 디스크 풀에 추가 된 디스크를 관리할 수 있도록 StoragePool 리소스 공급자에 대 한 권한을 제공 합니다. | 60fc6e62-5479-42d4-8bf4-67625fcc2840 |
> | [디스크 복원 운영자](#disk-restore-operator) | 디스크 복원을 수행하려면 백업 자격 증명 모음에 대한 권한을 제공합니다. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [디스크 스냅샷 기여자](#disk-snapshot-contributor) | 디스크 스냅샷을 관리하려면 백업 자격 증명 모음에 대한 권한을 제공합니다. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Scheduler 작업 컬렉션 참가자](#scheduler-job-collections-contributor) | Scheduler 작업 컬렉션을 관리할 수 있지만 액세스할 수는 없습니다. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [서비스 허브 운영자](#services-hub-operator) | 서비스 허브 운영자를 사용하면 서비스 허브 커넥터와 관련된 모든 읽기, 쓰기, 삭제 작업을 수행할 수 있습니다. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>일반


### <a name="contributor"></a>참가자

모든 리소스를 관리할 수 있는 모든 권한을 부여하지만, Azure RBAC에서 역할 할당, Azure Blueprints에서 할당 관리 또는 이미지 갤러리 공유를 허용하지 않습니다. [자세히 알아보기](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | * | 모든 유형의 리소스 만들기 및 관리 |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | 역할, 정책 할당, 정책 정의 및 정책 집합 정의를 삭제합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | 역할, 역할 할당, 정책 할당, 정책 정의 및 정책 집합 정의를 기록합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | 테넌트 범위에서 호출자에게 사용자 액세스 관리자 액세스 권한 부여 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | 청사진 할당을 만들거나 업데이트합니다. |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | 청사진 할당을 삭제합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | 여러 범위에 갤러리를 공유합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>소유자

Azure RBAC에서 역할을 할당하는 기능을 포함하여 모든 리소스를 관리할 수 있는 모든 권한을 부여합니다. [자세히 알아보기](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | * | 모든 유형의 리소스 만들기 및 관리 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>판독기

모든 리소스를 볼 수 있지만 변경할 수는 없습니다. [자세히 알아보기](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>사용자 액세스 관리자

Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다. [자세히 알아보기](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | 권한 부여 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>컴퓨팅


### <a name="classic-virtual-machine-contributor"></a>클래식 Virtual Machine 참가자

클래식 가상 머신을 관리할 수 있지만 가상 머신이나 연결된 가상 네트워크 또는 스토리지 계정에 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | 클래식 컴퓨팅 도메인 이름 만들기 및 관리 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | 가상 머신 만들기 및 관리 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | 예약된 IP를 연결합니다. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | 예약된 IP를 가져옵니다. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | 가상 네트워크를 조인합니다. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | 가상 네트워크를 가져옵니다. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | 스토리지 계정 디스크를 반환합니다. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | 스토리지 계정 이미지를 반환합니다. (사용되지 않음, 대신 ‘Microsoft.ClassicStorage/storageAccounts/vmImages’ 사용) |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 지정된 계정의 스토리지 계정을 반환합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>가상 머신 관리자 로그인

포털에서 Virtual Machines를 보고 관리자 권한으로 로그인합니다. [자세한 정보](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 가상 머신에 일반 사용자로 로그인합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | 가상 머신에 Windows 관리자 또는 Linux 루트 사용자 권한으로 로그인합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/login/action | 일반 사용자로 Azure Arc 컴퓨터에 로그인합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/loginAsAdmin/action | Windows 관리자 또는 Linux 루트 사용자 권한으로 Azure Arc 컴퓨터에 로그인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.HybridCompute/machines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action",
        "Microsoft.HybridCompute/machines/login/action",
        "Microsoft.HybridCompute/machines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>가상 머신 참가자

가상 머신을 만들고 관리하며, 디스크 및 디스크 스냅샷을 관리하고, 소프트웨어를 설치 및 실행하고, VM 확장을 사용하여 가상 머신의 루트 사용자에 대한 암호를 재설정하고, VM 확장을 사용하여 로컬 사용자 계정을 관리합니다. 이 역할은 가상 머신이 연결된 가상 네트워크 또는 스토리지 계정에 대한 관리 액세스 권한을 부여하지 않습니다. 이 역할은 Azure RBAC에서 역할을 할당하는 것을 허용하지 않습니다. [자세히 알아보기](/azure/architecture/reference-architectures/n-tier/linux-vm)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | 컴퓨팅 가용성 집합 만들기 및 관리 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | 컴퓨팅 위치 만들기 및 관리 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | 가상 머신 만들기, 업데이트, 삭제, 시작, 다시 시작, 전원 끄기 등을 비롯한 모든 가상 머신 작업을 수행합니다. 가상 머신에서 스크립트를 실행합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | 가상 머신 확장 집합 만들기 및 관리 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/cloudServices/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | 새 디스크를 만들거나 기존 디스크를 업데이트합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | 디스크의 속성을 가져옵니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | 디스크를 삭제합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | 애플리케이션 게이트웨이 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | 부하 분산 장치 인바운드 NAT 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | 부하 분산 장치 프로브 사용을 허용합니다. 예를 들어 이 권한이 있으면 VM 확장 집합의 healthProbe 속성이 프로브를 참조할 수 있습니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | 네트워크 위치 만들기 및 관리 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | 네트워크 인터페이스 만들기 및 관리 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | 네트워크 보안 그룹 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 공용 IP 주소를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | 보호 정책을 만듭니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | Microsoft.SerialConsole/serialPorts/connect/action | 직렬 포트에 커넥트 |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/cloudServices/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SerialConsole/serialPorts/connect/action",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>가상 머신 사용자 로그인

포털에서 Virtual Machines를 보고 일반 사용자 권한으로 로그인합니다. [자세히 알아보기](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | 부하 분산 장치 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | 가상 머신에 일반 사용자로 로그인합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/login/action | 일반 사용자로 Azure Arc 컴퓨터에 로그인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.HybridCompute/machines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.HybridCompute/machines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>네트워킹


### <a name="cdn-endpoint-contributor"></a>CD 엔드포인트 참가자

CDN 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN 엔드포인트 독자

CDN 엔드포인트를 볼 수 있지만 변경할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN 프로필 참가자

CDN 프로필과 해당 엔드포인트를 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. [자세히 알아보기](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN 프로필 독자

CDN 프로필과 해당 엔드포인트를 볼 수 있지만 변경할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>클래식 네트워크 참가자

기본 네트워크를 관리할 수 있지만 액세스할 수는 없습니다. [자세히 알아보기](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | 클래식 네트워크 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS 영역 참가자

Azure DNS의 DNS 영역과 레코드 집합을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다. [자세히 알아보기](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS 영역 및 레코드 만들기 및 관리 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>네트워크 참가자

네트워크를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | 네트워크 만들기 및 관리 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>프라이빗 DNS 영역 기여자

프라이빗 DNS 영역 리소스는 관리할 수 있으나, 연결되어 있는 가상 네트워크는 관리할 수 없습니다. [자세히 알아보기](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager 참가자

Traffic Manager 프로필을 관리할 수 있지만 액세스할 수 있는 사람을 제어할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>스토리지


### <a name="avere-contributor"></a>Avere 기여자

Avere vFXT 클러스터를 만들고 관리할 수 있습니다. [자세히 알아보기](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 스토리지 계정 만들기 및 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | 리소스 그룹에 대한 리소스를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere 운영자

Avere vFXT 클러스터에서 클러스터를 관리하는 데 사용됩니다. [자세한 정보](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 가상 머신의 속성을 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | 가상 네트워크 서브넷 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | 네트워크 보안 그룹을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 컨테이너 삭제 결과를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 컨테이너 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Blob 컨테이너 넣기의 결과를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob 삭제 결과를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Blob 또는 Blob 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Blob 쓰기 결과 반환 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Backup 참가자

백업 서비스를 관리할 수 있지만, 자격 증명 모음을 만들고 다른 사용자에게 액세스 권한을 부여할 수는 없습니다. [자세한 정보](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | 백업 관리에 대한 작업의 결과 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Recovery Services 자격 증명 모음의 백업 패브릭 내에서 백업 컨테이너 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | 백업 정책 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | 백업한 항목 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | 백업 항목을 보유하는 컨테이너 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Recovery Services 자격 증명 모음의 백업과 관련된 인증서 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Recovery Services 자격 증명 모음 만들기 및 사용 관리 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | 자격 증명 모음 만들기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/getBackupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/write | 백업 인스턴스를 만듭니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/delete | 백업 인스턴스를 삭제합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | 모든 백업 인스턴스를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | 모든 백업 인스턴스를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/backup/action | 백업 인스턴스에서 백업을 수행합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/validateRestore/action | 백업 인스턴스의 복원에 대한 유효성을 검사합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/restore/action | 백업 인스턴스에서 복원을 트리거합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/write | 백업 정책을 만듭니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/delete | 백업 정책을 삭제합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | 모든 백업 정책을 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | 모든 백업 정책을 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | 복구 지점을 모두 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | 복구 지점을 모두 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/findRestorableTimeRanges/action | 복원 가능한 시간 범위를 찾습니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/write | BackupVault 작업에서는 '백업 자격 증명 모음' 형식의 Azure 리소스를 만듭니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/operationResults/read | 백업 자격 증명 모음에 대한 패치 작업의 작업 결과를 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/checkNameAvailability/action | 요청한 BackupVault 이름을 사용할 수 있는지 확인합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationStatus/read | 백업 자격 증명 모음의 백업 작업 상태를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationResults/read | 백업 자격 증명 모음의 백업 작업 결과를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/validateForBackup/action | 백업 인스턴스의 백업에 대한 유효성을 검사합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/providers/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*",
        "Microsoft.DataProtection/locations/getBackupStatus/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/write",
        "Microsoft.DataProtection/backupVaults/backupInstances/delete",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/backup/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/validateRestore/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/restore/action",
        "Microsoft.DataProtection/backupVaults/backupPolicies/write",
        "Microsoft.DataProtection/backupVaults/backupPolicies/delete",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action",
        "Microsoft.DataProtection/backupVaults/write",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/operationResults/read",
        "Microsoft.DataProtection/locations/checkNameAvailability/action",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/locations/operationStatus/read",
        "Microsoft.DataProtection/locations/operationResults/read",
        "Microsoft.DataProtection/backupVaults/validateForBackup/action",
        "Microsoft.DataProtection/providers/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Backup 운영자

백업 제거, 자격 증명 모음 만들기, 다른 사용자에게 액세스 권한 부여를 제외하고 백업 서비스를 관리할 수 있습니다. [자세한 정보](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 보호 항목 Backup을 수행합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 프로비전합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | 지역 간 복원의 AccessToken을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 보호 항목의 복구 지점을 복원합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 보호된 항목에 대한 빠른 항목 복구를 취소합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | 백업 보호 항목을 만듭니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | 컨테이너 목록을 새로 고칩니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | 백업 작업 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | 백업 관리 작업의 결과 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | 백업할 수 있는 항목 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | 서비스 컨테이너 등록 작업을 사용하여 복구 서비스와 함께 컨테이너를 등록할 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | 보호된 항목에 대한 작업의 유효성을 검사합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | 등록된 컨테이너를 만듭니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | 컨테이너 내의 워크로드를 조회합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | 백업 보호 의도 만들기 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | 보호 가능한 컨테이너를 모두 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | 지역 간 복원에 대한 세 번째 지역의 인증을 위해 AAD 속성을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Recovery Services 자격 증명 모음에 대한 보조 지역의 지역 간 복원 작업을 나열합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Recovery Services 자격 증명 모음에 대한 보조 지역의 지역 간 복원 작업 세부 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | 지역 간 복원을 트리거합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Recovery Services 자격 증명 모음의 CRR 작업 결과를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Recovery Services 자격 증명 모음의 CRR 작업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | 모든 백업 인스턴스를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | 모든 백업 인스턴스를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | 모든 백업 정책을 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | 모든 백업 정책을 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | 복구 지점을 모두 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | 복구 지점을 모두 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/findRestorableTimeRanges/action | 복원 가능한 시간 범위를 찾습니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/operationResults/read | 백업 자격 증명 모음에 대한 패치 작업의 작업 결과를 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationStatus/read | 백업 자격 증명 모음의 백업 작업 상태를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationResults/read | 백업 자격 증명 모음의 백업 작업 결과를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/providers/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/operationResults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/locations/operationStatus/read",
        "Microsoft.DataProtection/locations/operationResults/read",
        "Microsoft.DataProtection/providers/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Backup 읽기 권한자

백업 서비스를 볼 수 있지만 변경할 수는 없습니다. [자세한 정보](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | 작업의 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | 보호 컨테이너에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 보호 항목에 대해 수행된 작업의 결과를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 보호 항목에 대해 수행된 작업의 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | 보호된 항목의 개체 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 보호 항목의 복구 지점을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | 등록된 모든 컨테이너를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | 작업의 작업 결과를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | 모든 작업 개체를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | 작업을 내보냅니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Recovery Services 자격 증명 모음의 Backup 작업 결과를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | 정책 작업의 결과를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | 모든 보호 정책을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | 모든 보호 항목 목록을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | 구독에 속하는 컨테이너를 모두 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Recovery Services의 보호된 항목 및 보호된 서버에 대한 요약을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Recovery Services 자격 증명 모음에 대한 스토리지 구성을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Recovery Services 자격 증명 모음에 구성을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Recovery Services 자격 증명 모음의 Backup 작업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | 정책 작업의 상태를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | 자격 증명 모음에 등록된 모든 백업 관리 서버를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | 백업 보호 의도를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | 컨테이너의 모든 항목을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | 경고를 해결합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | 지정된 작업의 작업 상태를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | 모든 백업 보호 의도를 나열합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | 기능의 유효성을 검사합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Recovery Services 자격 증명 모음에 대한 보조 지역의 지역 간 복원 작업을 나열합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Recovery Services 자격 증명 모음에 대한 보조 지역의 지역 간 복원 작업 세부 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Recovery Services 자격 증명 모음의 CRR 작업 결과를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Recovery Services 자격 증명 모음의 CRR 작업 상태를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/getBackupStatus/action | Recovery Services 자격 증명 모음의 백업 상태를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/write | 백업 인스턴스를 만듭니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | 모든 백업 인스턴스를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | 모든 백업 인스턴스를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/backup/action | 백업 인스턴스에서 백업을 수행합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/validateRestore/action | 백업 인스턴스의 복원에 대한 유효성을 검사합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/restore/action | 백업 인스턴스에서 복원을 트리거합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | 모든 백업 정책을 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | 모든 백업 정책을 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | 복구 지점을 모두 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | 복구 지점을 모두 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/findRestorableTimeRanges/action | 복원 가능한 시간 범위를 찾습니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/operationResults/read | 백업 자격 증명 모음에 대한 패치 작업의 작업 결과를 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | 구독의 백업 자격 증명 모음 목록을 가져옵니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationStatus/read | 백업 자격 증명 모음의 백업 작업 상태를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationResults/read | 백업 자격 증명 모음의 백업 작업 결과를 반환합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/validateForBackup/action | 백업 인스턴스의 백업에 대한 유효성을 검사합니다. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/providers/operations/read | 작업에서 리소스 공급자에 대한 작업 목록을 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.DataProtection/locations/getBackupStatus/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/write",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/backup/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/validateRestore/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/restore/action",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/operationResults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/locations/operationStatus/read",
        "Microsoft.DataProtection/locations/operationResults/read",
        "Microsoft.DataProtection/backupVaults/validateForBackup/action",
        "Microsoft.DataProtection/providers/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>클래식 Storage 계정 참가자

클래식 Storage 계정을 관리할 수 있지만 여기에 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | 스토리지 계정 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>클래식 스토리지 계정 키 운영자 서비스 역할

클래식 스토리지 계정 키 운영자가 클래식 스토리지 계정에서 키를 나열하고 다시 생성할 수 있습니다. [자세한 정보](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | 스토리지 계정에 대한 기존 액세스 키를 다시 생성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box 기여자

다른 사람에게 액세스 권한을 부여하는 것을 제외한 모든 항목을 Data Box 서비스에서 관리할 수 있습니다. [자세히 알아보기](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box 읽기 권한자

주문하기나 주문 세부 정보 편집 및 다른 사용자에게 액세스 권한 부여 외에 Data Box 서비스를 관리할 수 있습니다. [자세히 알아보기](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | 주문과 관련된 암호화되지 않은 자격 증명을 나열합니다. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | 이 메서드는 사용할 수 있는 SKU 목록을 반환합니다. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | 이 메서드는 모든 유형의 유효성 검사를 수행합니다. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | 이 메서드는 영역에 대한 구성을 반환합니다. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | 배송 주소의 유효성을 검사하고, 있는 경우, 대체 주소를 제공합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics 개발자

사용자 자신의 작업을 제출, 모니터링 및 관리할 수 있지만 Data Lake Analytics 계정을 만들거나 삭제할 수는 없습니다. [자세히 알아보기](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | DataLakeAnalytics 계정을 삭제합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | 다른 사용자가 제출한 작업을 취소하는 권한을 부여합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | DataLakeAnalytics 계정을 만들거나 업데이트합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics 계정과 연결된 DataLakeStore 계정을 만들거나 업데이트합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | DataLakeAnalytics 계정에서 DataLakeStore 계정을 연결 해제합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | DataLakeAnalytics 계정과 연결된 Storage 계정을 만들거나 업데이트합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | DataLakeAnalytics 계정에서 Storage 계정을 연결 해제합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | 방화벽 규칙을 만들거나 업데이트합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | 방화벽 규칙을 삭제합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | 컴퓨팅 정책을 만들거나 업데이트합니다. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | 컴퓨팅 정책을 삭제합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>읽기 권한자 및 데이터 액세스

모든 것을 볼 수 있지만, 스토리지 계정 또는 포함된 리소스를 삭제하거나 만들 수는 없습니다. 또한 스토리지 계정 키에 액세스하여 스토리지 계정에 포함된 모든 데이터를 읽고 쓸 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | 지정된 스토리지 계정에 대한 계정 SAS 토큰을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Storage 계정 참가자

스토리지 계정을 관리할 수 있도록 허용합니다. 공유 키 권한 부여를 통해 데이터에 액세스하는 데 사용할 수 있는 계정 키에 대한 액세스 권한을 제공합니다. [자세히 알아보기](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | 스토리지 계정 만들기 및 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>스토리지 계정 키 운영자 서비스 역할

스토리지 계정 액세스 키를 나열하고 다시 생성할 수 있도록 허용합니다. [자세히 알아보기](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | 지정된 스토리지 계정에 대한 액세스 키를 다시 생성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Storage Blob 데이터 기여자

Azure Storage 컨테이너 및 BLOB을 읽고, 쓰고, 삭제합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | 컨테이너를 삭제합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 컨테이너 또는 컨테이너 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | 컨테이너의 메타데이터 또는 속성을 수정합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob을 삭제합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB 또는 BLOB 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | BLOB에 씁니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | BLOB을 한 경로에서 다른 경로로 이동합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Blob 콘텐츠 추가 결과를 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Storage Blob 데이터 소유자

POSIX 액세스 제어 할당을 포함하여 Azure Storage BLOB 컨테이너 및 데이터에 대한 모든 액세스 권한을 제공합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | 컨테이너에 대한 모든 권한이 있습니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | BLOB에 대한 모든 권한이 있습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Storage Blob 데이터 읽기 권한자

Azure Storage 컨테이너 및 BLOB을 읽고 나열합니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | 컨테이너 또는 컨테이너 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | BLOB 또는 BLOB 목록을 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage Blob 위임자

Azure AD 자격 증명으로 서명된 컨테이너 또는 BLOB의 공유 액세스 서명을 만드는 데 사용할 수 있는 사용자 위임 키를 가져옵니다. 자세한 내용은 [사용자 위임 SAS 만들기](/rest/api/storageservices/create-user-delegation-sas)를 참조하세요. [자세히 알아보기](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Blob service의 사용자 위임 키를 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Storage 파일 데이터 SMB 공유 기여자

Azure 파일 공유의 파일/디렉터리에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. Windows 파일 서버에는 이 역할에 상응하는 기본 제공 역할이 없습니다. [자세히 알아보기](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 파일을 쓰거나 폴더를 만든 결과를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 파일/폴더를 삭제한 결과를 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage 파일 데이터 SMB 공유 높은 권한 기여자

Azure 파일 공유의 파일/디렉터리에 대한 ACL을 읽고, 쓰고, 삭제하고, 수정할 수 있습니다. 이 역할은 Windows 파일 서버의 변경 내용에 대한 파일 공유 ACL에 해당합니다. [자세히 알아보기](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | 파일을 쓰거나 폴더를 만든 결과를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | 파일/폴더를 삭제한 결과를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | 파일/폴더에 대한 권한을 수정한 결과를 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Storage 파일 데이터 SMB 공유 읽기 권한자

Azure 파일 공유의 파일/디렉터리에 대한 읽기 액세스를 허용합니다. 이 역할은 Windows 파일 서버에 대한 파일 공유 ACL 읽기에 해당합니다. [자세히 알아보기](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | 파일/폴더 또는 파일/폴더 목록을 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Storage 큐 데이터 기여자

Azure Storage 큐 및 큐 메시지를 읽고, 쓰고, 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | 큐를 삭제합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | 큐 메타데이터 또는 속성을 수정합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | 큐에서 하나 이상의 메시지를 삭제합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 큐에서 하나 이상의 메시지를 선택 또는 검색합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | 큐에 메시지를 추가합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | 메시지 처리 결과를 반환합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Storage 큐 데이터 메시지 처리자

Azure Storage 큐의 메시지를 선택, 검색 및 삭제할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 메시지를 선택합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | 메시지를 검색하고 삭제합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Storage 큐 데이터 메시지 보내는 사람

Azure Storage 큐에 메시지를 추가할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | 큐에 메시지를 추가합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Storage 큐 데이터 읽기 권한자

Azure Storage 큐 및 큐 메시지를 읽고 나열할 수 있습니다. 특정 데이터 연산에 어떤 작업이 필요한지 알아보려면 [BLOB 및 큐 데이터 연산을 호출하기 위한 권한](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요. [자세히 알아보기](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | 큐 또는 큐 목록을 반환합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | 큐에서 하나 이상의 메시지를 선택 또는 검색합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-table-data-contributor"></a>스토리지 테이블 데이터 기여자

Azure Storage 테이블 및 엔터티에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/read | 쿼리 테이블 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/write | 테이블 만들기 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/delete | 테이블을 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/read | 테이블 엔터티를 쿼리합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/write | 테이블 엔터티 삽입, 병합하거나 바꿉니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/delete | 테이블 엔터티 삭제 |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/add/action | 테이블 엔터티를 삽입합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/update/action | 테이블 엔터티를 병합 또는 업데이트합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage tables and entities",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a9a7e1f-b9d0-4cc4-a60d-0319b160aaa3",
  "name": "0a9a7e1f-b9d0-4cc4-a60d-0319b160aaa3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/read",
        "Microsoft.Storage/storageAccounts/tableServices/tables/write",
        "Microsoft.Storage/storageAccounts/tableServices/tables/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/read",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/write",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/delete",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/add/action",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/update/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Table Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-table-data-reader"></a>스토리지 테이블 데이터 읽기 권한자

Azure Storage 테이블 및 엔터티에 대한 읽기 권한을 허용합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/read | 쿼리 테이블 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/read | 테이블 엔터티를 쿼리합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage tables and entities",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76199698-9eea-4c19-bc75-cec21354c6b6",
  "name": "76199698-9eea-4c19-bc75-cec21354c6b6",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Table Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>웹


### <a name="azure-maps-data-contributor"></a>Azure Maps 데이터 기여자

Azure Maps 계정에서 지도 관련 데이터를 읽고, 쓰고, 삭제할 수 있는 액세스 권한을 부여합니다. [자세히 알아보기](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | [Microsoft. 지도](resource-provider-operations.md#microsoftmaps) /accounts/*/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete",
        "Microsoft.Maps/accounts/*/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure Maps 데이터 읽기 권한자

Azure 맵 계정에서 맵 관련 데이터를 읽을 수 있는 액세스 권한을 부여합니다. [자세히 알아보기](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-config-server-contributor"></a>Azure Spring Cloud Config Server 기여자

읽기, 쓰기 및 삭제 액세스 허용 Azure Spring Cloud Config Server [자세히 알아보기](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/read | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 구성 콘텐츠(예: 애플리케이션)를 읽습니다. |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/write | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 구성 서버 콘텐츠 작성 |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/delete | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 구성 서버 콘텐츠 삭제 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read, write and delete access to Azure Spring Cloud Config Server",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a06f5c24-21a7-4e1a-aa2b-f19eb6684f5b",
  "name": "a06f5c24-21a7-4e1a-aa2b-f19eb6684f5b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/configService/read",
        "Microsoft.AppPlatform/Spring/configService/write",
        "Microsoft.AppPlatform/Spring/configService/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Config Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-config-server-reader"></a>Azure Spring Cloud 구성 서버 읽기 권한자

Azure Spring Cloud Config Server 읽기 액세스 허용 [자세히 알아보기](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/read | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 구성 콘텐츠(예: 애플리케이션)를 읽습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Config Server",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d04c6db6-4947-4782-9e91-30a88feb7be7",
  "name": "d04c6db6-4947-4782-9e91-30a88feb7be7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/configService/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Config Server Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-data-reader"></a>Azure Spring Cloud 데이터 판독기

Azure Spring Cloud 데이터에 대한 읽기 권한 허용

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-service-registry-contributor"></a>Azure Spring Cloud Service Registry 기여자

Azure Spring Cloud Service Registry에 대한 읽기, 쓰기 및 삭제 액세스 허용 [자세한 정보](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/read | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 사용자 앱 등록 정보를 읽습니다. |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/write | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 사용자 앱 등록 정보를 씁니다. |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/delete | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 사용자 앱 등록 정보를 삭제합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read, write and delete access to Azure Spring Cloud Service Registry",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f5880b48-c26d-48be-b172-7927bfa1c8f1",
  "name": "f5880b48-c26d-48be-b172-7927bfa1c8f1",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/eurekaService/read",
        "Microsoft.AppPlatform/Spring/eurekaService/write",
        "Microsoft.AppPlatform/Spring/eurekaService/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Service Registry Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-service-registry-reader"></a>Azure Spring Cloud Service Registry 읽기 권한자

Azure Spring Cloud Service Registry에 대한 읽기 액세스 허용 [자세한 정보](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/read | 특정 Azure Spring Cloud 서비스 인스턴스에 대한 사용자 앱 등록 정보를 읽습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Service Registry",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cff1b556-2399-4e7e-856d-a8f754be7b65",
  "name": "cff1b556-2399-4e7e-856d-a8f754be7b65",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/eurekaService/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Service Registry Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-account-administrator"></a>Media Services 계정 관리자

Media Services 계정을 생성하고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/listStreamingLocators/action | 자산에 대한 스트리밍 로케이터를 나열합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listPaths/action | 경로를 나열합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/write | Media Services 계정을 만들거나 업데이트합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/delete | Media Services 계정을 삭제합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/privateEndpointConnectionsApproval/action | 프라이빗 엔드포인트 연결을 승인합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/privateEndpointConnections/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Media Services accounts; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/054126f8-9a2b-4f1c-a9ad-eca461f08466",
  "name": "054126f8-9a2b-4f1c-a9ad-eca461f08466",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/listStreamingLocators/action",
        "Microsoft.Media/mediaservices/streamingLocators/listPaths/action",
        "Microsoft.Media/mediaservices/write",
        "Microsoft.Media/mediaservices/delete",
        "Microsoft.Media/mediaservices/privateEndpointConnectionsApproval/action",
        "Microsoft.Media/mediaservices/privateEndpointConnections/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Account Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-live-events-administrator"></a>Media Services 라이브 이벤트 관리자

라이브 이벤트, 자산, 자산 필터, 스트리밍 로케이터를 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/assetfilters/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/liveEvents/* |  |
> | **NotActions** |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/getEncryptionKey/action | 자산 암호화 키를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listContentKeys/action | 콘텐츠 키를 나열합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Live Events, Assets, Asset Filters, and Streaming Locators; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/532bc159-b25e-42c0-969e-a1d439f60d77",
  "name": "532bc159-b25e-42c0-969e-a1d439f60d77",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/*",
        "Microsoft.Media/mediaservices/assets/assetfilters/*",
        "Microsoft.Media/mediaservices/streamingLocators/*",
        "Microsoft.Media/mediaservices/liveEvents/*"
      ],
      "notActions": [
        "Microsoft.Media/mediaservices/assets/getEncryptionKey/action",
        "Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Live Events Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-media-operator"></a>Media Services 미디어 운영자

자산, 자산 필터, 스트리밍 로케이터, 작업을 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/assetfilters/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/transforms/jobs/* |  |
> | **NotActions** |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/getEncryptionKey/action | 자산 암호화 키를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listContentKeys/action | 콘텐츠 키를 나열합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Assets, Asset Filters, Streaming Locators, and Jobs; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4395492-1534-4db2-bedf-88c14621589c",
  "name": "e4395492-1534-4db2-bedf-88c14621589c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/*",
        "Microsoft.Media/mediaservices/assets/assetfilters/*",
        "Microsoft.Media/mediaservices/streamingLocators/*",
        "Microsoft.Media/mediaservices/transforms/jobs/*"
      ],
      "notActions": [
        "Microsoft.Media/mediaservices/assets/getEncryptionKey/action",
        "Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Media Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-policy-administrator"></a>Media Services 정책 관리자

계정 필터, 스트리밍 정책, 콘텐츠 키 정책 및 변환을 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다. 작업, 자산, 스트리밍 리소스를 만들 수 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/listStreamingLocators/action | 자산에 대한 스트리밍 로케이터를 나열합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listPaths/action | 경로를 나열합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/accountFilters/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingPolicies/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/contentKeyPolicies/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/transforms/* |  |
> | **NotActions** |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 비밀을 사용하여 정책 속성을 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Account Filters, Streaming Policies, Content Key Policies, and Transforms; read-only access to other Media Services resources. Cannot create Jobs, Assets or Streaming resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c4bba371-dacd-4a26-b320-7250bca963ae",
  "name": "c4bba371-dacd-4a26-b320-7250bca963ae",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/listStreamingLocators/action",
        "Microsoft.Media/mediaservices/streamingLocators/listPaths/action",
        "Microsoft.Media/mediaservices/accountFilters/*",
        "Microsoft.Media/mediaservices/streamingPolicies/*",
        "Microsoft.Media/mediaservices/contentKeyPolicies/*",
        "Microsoft.Media/mediaservices/transforms/*"
      ],
      "notActions": [
        "Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Policy Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-streaming-endpoints-administrator"></a>Media Services 스트리밍 엔드포인트 관리자

스트리밍 엔드포인트를 만들고, 읽고, 수정하고, 삭제합니다. 이는 다른 Media Services 리소스에 대한 읽기 전용 액세스입니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/listStreamingLocators/action | 자산에 대한 스트리밍 로케이터를 나열합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listPaths/action | 경로를 나열합니다. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingEndpoints/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Streaming Endpoints; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/99dba123-b5fe-44d5-874c-ced7199a5804",
  "name": "99dba123-b5fe-44d5-874c-ced7199a5804",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/listStreamingLocators/action",
        "Microsoft.Media/mediaservices/streamingLocators/listPaths/action",
        "Microsoft.Media/mediaservices/streamingEndpoints/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Streaming Endpoints Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-index-data-contributor"></a>검색 인덱스 데이터 기여자

Azure Cognitive Search 인덱스 데이터에 대한 모든 액세스 권한을 부여합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/indexes/documents/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to Azure Cognitive Search index data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8ebe5a00-799e-43f5-93ac-243d3dce84a7",
  "name": "8ebe5a00-799e-43f5-93ac-243d3dce84a7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Search/searchServices/indexes/documents/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Search Index Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-index-data-reader"></a>검색 인덱스 데이터 읽기 권한자

Azure Cognitive Search 인덱스 데이터에 대한 읽기 권한을 부여합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/indexes/documents/read | 인덱스에서 문서 또는 제안된 쿼리 용어를 읽습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants read access to Azure Cognitive Search index data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1407120a-92aa-4202-b7e9-c0e197c71c8f",
  "name": "1407120a-92aa-4202-b7e9-c0e197c71c8f",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Search/searchServices/indexes/documents/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Search Index Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search 서비스 참가자

Search 서비스를 관리할 수 있지만 액세스할 수는 없습니다. [자세히 알아보기](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | 검색 서비스 만들기 및 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey 읽기 권한자

SignalR 서비스 액세스 키 읽기

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | 관리 포털에서 또는 API를 통해 SignalR 액세스 키 값을 봅니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>SignalR 앱 서버(미리 보기)

앱 서버에서 AAD 인증 옵션을 사용하여 SignalR Service에 액세스할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | AccessToken에 서명하기 위한 AccessKey를 생성합니다. 이 키는 기본값으로 90분 후에 만료됩니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | 서버 연결을 시작합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-rest-api-owner"></a>SignalR REST API 소유자

Azure SignalR Service REST API에 대한 모든 권한

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | 클라이언트에서 ASRS에 연결하기 위한 AccessToken을 생성합니다. 이 토큰은 기본값으로 5분 후에 만료됩니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | 허브의 모든 클라이언트 연결에 메시지를 브로드캐스트합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | 그룹에 메시지를 브로드캐스트합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | 그룹의 존재 또는 그룹 내 사용자의 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | 그룹에 가입하거나 그룹을 탈퇴합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | 클라이언트 연결에 직접 메시지를 보냅니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | 클라이언트 연결 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | 클라이언트 연결을 닫습니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | 여러 클라이언트 연결로 구성될 수 있는 사용자에게 메시지를 보냅니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | 사용자 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | 사용자를 수정합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR REST API Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-rest-api-reader"></a>SignalR REST API 판독기

Azure SignalR Service REST API에 대한 읽기 전용 권한

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | 그룹의 존재 또는 그룹 내 사용자의 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | 클라이언트 연결 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | 사용자 존재를 확인합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR REST API Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner"></a>SignalR Service 소유자

Azure SignalR Service REST API에 대한 모든 권한

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | AccessToken에 서명하기 위한 AccessKey를 생성합니다. 이 키는 기본값으로 90분 후에 만료됩니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | 클라이언트에서 ASRS에 연결하기 위한 AccessToken을 생성합니다. 이 토큰은 기본값으로 5분 후에 만료됩니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | 허브의 모든 클라이언트 연결에 메시지를 브로드캐스트합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | 그룹에 메시지를 브로드캐스트합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | 그룹의 존재 또는 그룹 내 사용자의 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | 그룹에 가입하거나 그룹을 탈퇴합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | 클라이언트 연결에 직접 메시지를 보냅니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | 클라이언트 연결 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | 클라이언트 연결을 닫습니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | 서버 연결을 시작합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | 여러 클라이언트 연결로 구성될 수 있는 사용자에게 메시지를 보냅니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | 사용자 존재를 확인합니다. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | 사용자를 수정합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/serverConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalrweb-pubsub-contributor"></a>SignalR/웹 PubSub 기여자

SignalR Service 리소스 만들기, 읽기, 업데이트, 삭제

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR/Web PubSub Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>웹 계획 참가자

웹 사이트의 웹 계획을 관리합니다. Azure RBAC에서 역할을 할당할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | 서버 팜 만들기 및 관리 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | App Service Environment를 조인합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>웹 사이트 참가자

웹 사이트를 관리하지만 웹 계획은 관리하지 않습니다. Azure RBAC에서 역할을 할당할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights 구성 요소 만들기 및 관리 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | 웹 사이트 인증서 만들기 및 관리 |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | 호스트 이름에 할당된 사이트의 이름을 가져옵니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | App Service 요금제에 가입합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | 웹 사이트 만들기 및 관리(사이트 만들기도 관련 App Service 계획에 대한 쓰기 권한이 필요) |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>컨테이너


### <a name="acrdelete"></a>AcrDelete

컨테이너 레지스트리에서 리포지토리, 태그, 매니페스트를 삭제합니다. [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | 컨테이너 레지스트리의 아티팩트를 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

콘텐츠 신뢰를 사용하도록 설정된 컨테이너 레지스트리에 신뢰할 수 있는 이미지를 푸시하거나 설정된 컨테이너 레지스트리에서 신뢰할 수 있는 이미지를 끌어옵니다. [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | 컨테이너 레지스트리에 대한 콘텐츠 신뢰 메타데이터를 푸시/풀합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/trustedCollections/write | 컨테이너 레지스트리 콘텐츠의 신뢰할 수 있는 컬렉션을 밀어넣거나 게시할 수 있습니다. 이는 데이터 작업이라는 점을 제외하고 Microsoft.ContainerRegistry/registries/sign/write 작업과 유사합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerRegistry/registries/trustedCollections/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

컨테이너 레지스트리에서 아티팩트를 끌어옵니다. [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

컨테이너 레지스트리에 아티팩트를 푸시하거나 또는 컨테이너 레지스트리에서 아티팩트를 끌어옵니다. [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | 컨테이너 레지스트리에서 이미지를 끌어오거나 가져옵니다. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | 컨테이너 레지스트리에 이미지를 푸시하거나 작성합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

컨테이너 레지스트리에서 격리된 이미지를 끌어옵니다. [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantinedArtifacts/read | 컨테이너 레지스트리에서 격리된 아티팩트를 끌어오거나 가져올 수 있습니다. 이는 데이터 작업이라는 점을 제외하고 Microsoft.ContainerRegistry/registries/quarantine/read와 유사합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerRegistry/registries/quarantinedArtifacts/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

컨테이너 레지스트리에 격리된 이미지를 푸시하거나 컨테이너 레지스트리에서 격리된 이미지를 끌어옵니다. [자세히 알아보기](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | 컨테이너 레지스트리에서 격리된 이미지를 끌어오거나 가져옵니다. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | 격리된 이미지의 격리 상태를 작성/수정합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantinedArtifacts/read | 컨테이너 레지스트리에서 격리된 아티팩트를 끌어오거나 가져올 수 있습니다. 이는 데이터 작업이라는 점을 제외하고 Microsoft.ContainerRegistry/registries/quarantine/read와 유사합니다. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantinedArtifacts/write | 격리된 아티팩트의 격리 상태를 쓰거나 업데이트할 수 있습니다. 이는 데이터 작업이라는 점을 제외하고 Microsoft.ContainerRegistry/registries/quarantine/write 작업과 유사합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerRegistry/registries/quarantinedArtifacts/read",
        "Microsoft.ContainerRegistry/registries/quarantinedArtifacts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service 클러스터 관리자 역할

클러스터 관리자 자격 증명 작업을 나열합니다. [자세히 알아보기](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | 관리되는 클러스터의 clusterAdmin 자격 증명을 나열합니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | 자격 증명 나열을 사용하여 역할 이름별로 관리되는 클러스터 액세스 프로필을 가져옵니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service 클러스터 사용자 역할

클러스터 사용자 자격 증명 작업을 나열합니다. [자세히 알아보기](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service 기여자 역할

Azure Kubernetes Service 클러스터를 읽고 쓸 수 있는 액세스 권한을 부여합니다. [자세한 정보](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | 관리되는 클러스터를 가져옵니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | 새 관리되는 클러스터를 만들거나 기존 관리되는 클러스터를 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC 관리자

리소스 할당량 및 네임스페이스 업데이트 또는 삭제를 제외하고 클러스터/네임스페이스의 모든 리소스를 관리할 수 있습니다. [자세히 알아보기](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | resourcequotas를 씁니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | resourcequotas를 삭제합니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | namespaces를 씁니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | namespaces를 삭제합니다. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC 클러스터 관리자

클러스터의 모든 리소스를 관리할 수 있습니다. [자세히 알아보기](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | 관리되는 클러스터의 clusterUser 자격 증명을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC 읽기 권한자

네임스페이스에 있는 대부분의 개체를 볼 수 있는 읽기 전용 권한을 허용합니다. 역할 또는 역할 바인딩을 볼 수 없습니다. 이 역할은 비밀을 볼 수 없습니다. 비밀 내용을 읽으면 네임스페이스의 ServiceAccount 자격 증명에 액세스할 수 있으므로 네임스페이스의 ServiceAccount로 API 액세스가 허용될 수 있기 때문입니다(일종의 권한 상승). 클러스터 범위에 이 역할을 적용하면 모든 네임스페이스에 대한 액세스 권한이 부여됩니다. [자세히 알아보기](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | controllerrevisions를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | daemonsets를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | deployments를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | replicasets를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | statefulsets를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | horizontalpodautoscalers를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | cronjobs를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | jobs를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | configmaps를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | endpoints를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | events를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | events를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | daemonsets를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | deployments를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | ingresses를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | networkpolicies를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | replicasets를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | limitranges를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | namespaces를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | ingresses를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | networkpolicies를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | persistentvolumeclaims를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | pods를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | poddisruptionbudgets를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | replicationcontrollers를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | replicationcontrollers를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | resourcequotas를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | serviceaccounts를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | services를 읽습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC 쓰기 권한자

네임스페이스의 대부분의 개체에 대한 읽기/쓰기 권한을 허용합니다. 이 역할은 역할이나 역할 바인딩을 보거나 수정할 수 없습니다. 그러나 이 역할을 사용하여 네임스페이스의 ServiceAccount로 Pod를 실행하고 비밀에 액세스할 수 있으므로 네임스페이스에 있는 모든 ServiceAccount의 API 액세스 수준을 얻을 수 있습니다. 클러스터 범위에 이 역할을 적용하면 모든 네임스페이스에 대한 액세스 권한이 부여됩니다. [자세히 알아보기](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | controllerrevisions를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | events를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | events를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | limitranges를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | namespaces를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | resourcequotas를 읽습니다. |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>데이터베이스


### <a name="azure-connected-sql-server-onboarding"></a>Azure 연결 SQL Server 온 보 딩

Arc 지원 서버에서 SQL Server용 Azure 리소스에 대한 읽기 및 쓰기 권한을 허용합니다. [자세히 알아보기](/sql/sql-server/azure-arc/connect)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | Microsoft.AzureArcData/sqlServerInstances/read | SQL Server 인스턴스 리소스를 검색 합니다. |
> | Microsoft.AzureArcData/sqlServerInstances/write | SQL Server 인스턴스 리소스를 업데이트 합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Microsoft.AzureArcData service role to access the resources of Microsoft.AzureArcData stored with RPSAAS.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8113dce-c529-4d33-91fa-e9b972617508",
  "name": "e8113dce-c529-4d33-91fa-e9b972617508",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureArcData/sqlServerInstances/read",
        "Microsoft.AzureArcData/sqlServerInstances/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected SQL Server Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-account-reader-role"></a>Cosmos DB 계정 독자 역할

Azure Cosmos DB 계정 데이터를 읽을 수 있음. Azure Cosmos DB 계정 관리는 [DocumentDB 계정 참가자](#documentdb-account-contributor)를 참조하세요. [자세히 알아보기](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | 컬렉션 읽기 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | 데이터베이스 계정 읽기 전용 키를 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | 메트릭 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB 운영자

Azure Cosmos DB 계정을 관리할 수 있지만 계정의 데이터에 액세스할 수는 없습니다. 계정 키 및 연결 문자열에 대한 액세스를 차단합니다. [자세히 알아보기](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | SQL 역할 정의 만들기 또는 업데이트 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | SQL 역할 정의 삭제 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | SQL 역할 할당 만들기 또는 업데이트 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | SQL 역할 할당 삭제 |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Cosmos DB 데이터베이스 또는 계정의 컨테이너에 대한 복원 요청을 제출할 수 있습니다. [자세한 정보](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | 백업 구성하는 요청 제출 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | 복원 요청 제출 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

지속적인 백업 모드를 사용하여 Cosmos DB 데이터베이스 계정의 복원 작업을 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/restore/action | 복원 요청 제출 |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | 복원 가능한 데이터베이스 계정을 읽거나 모든 복원 가능한 데이터베이스 계정을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB 계정 참가자

Azure Cosmos DB 계정을 관리할 수 있습니다. Azure Cosmos DB는 이전의 DocumentDB입니다. [자세히 알아보기](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Azure Cosmos DB 계정 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache 참가자

Redis Cache를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | ‘Microsoft.Cache’ 리소스 공급자를 구독에 등록합니다. |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Redis 캐시 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB 참가자

SQL 데이터베이스를 관리할 수 있지만 액세스할 수는 없습니다. 또한 보안 관련 정책이나 부모 SQL 서버를 관리할 수 없습니다. [자세히 알아보기](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | SQL 데이터베이스 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/ledgerDigestUploads/write | 원장 다이제스트 업로드를 사용합니다.  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/ledgerDigestUploads/disable/action | 원장 다이제스트 업로드를 사용하지 않습니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | 감사 설정 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | 데이터 마스킹 정책 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | 보안 경고 정책 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | 보안 메트릭 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/servers/databases/ledgerDigestUploads/write",
        "Microsoft.Sql/servers/databases/ledgerDigestUploads/disable/action",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance 기여자

SQL Managed Instances 및 필수 네트워크 구성을 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | 특정 관리형 서버 Azure Active Directory 인증 개체만 삭제합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | 특정 관리형 서버 Azure Active Directory 인증 개체만 추가하거나 업데이트합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL 보안 관리자

SQL Server 및 데이터베이스의 보안과 관련된 정책을 관리할 수 있지만 여기에 액세스할 수는 없습니다. [자세히 알아보기](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | 스토리지 계정 또는 SQL 데이터베이스 같은 리소스를 서브넷에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | 관리형 인스턴스 Azure 비동기 관리자 작업 결과를 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL 서버 감사 설정 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | 지정된 서버에 구성된 확장 서버 Blob 감사 정책의 세부 정보를 검색합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | 지정된 데이터베이스에 구성된 확장 Blob 감사 정책의 세부 정보를 검색합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | 데이터베이스 목록을 가져오거나 지정된 데이터베이스에 대한 속성을 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | 데이터베이스 스키마를 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | 데이터베이스 열을 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | 데이터베이스 테이블을 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | 서버 목록을 가져오거나 지정된 서버에 대한 속성을 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 만들기 및 관리 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | 관리되는 인스턴스 목록을 반환하거나 지정된 관리되는 인스턴스에 대한 속성을 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | 관리되는 인스턴스 관리자 목록을 가져옵니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | 특정 Azure Active Directory 관리자 개체를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server 참가자

SQL Server 및 데이터베이스를 관리할 수 있지만 액세스할 수는 없으며, 해당하는 보안 관련 정책에도 액세스할 수 없습니다. [자세히 알아보기](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | SQL 서버 만들기 및 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | 메트릭 정의 읽기 |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL 서버 감사 설정 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL 서버 데이터베이스 감사 설정 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | 데이터베이스 blob 감사 레코드를 검색합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL 서버 데이터베이스 데이터 마스킹 정책 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL 서버 데이터베이스 보안 경고 정책 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL 서버 데이터베이스 보안 메트릭 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL 서버 보안 경고 정책 편집 |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | 특정 서버 Azure Active Directory 인증 개체만 삭제합니다. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | 특정 서버 Azure Active Directory 인증 개체만 추가하거나 업데이트합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>분석


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs 데이터 소유자

Azure Event Hubs 리소스에 대한 전체 액세스를 허용합니다. [자세히 알아보기](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs 데이터 받는 사람

Azure Event Hubs 리소스에 대한 받기 액세스 권한을 허용합니다. [자세히 알아보기](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs 데이터 보내는 사람

Azure Event Hubs 리소스에 대한 보내기 액세스 권한을 허용합니다. [자세히 알아보기](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory 참가자

데이터 팩터리를 만들고 관리하며 해당 하위 리소스도 만들고 관리합니다. [자세히 알아보기](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | 데이터 팩터리 및 그 안에 포함된 자식 리소스를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | eventSubscription을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>데이터 제거자

Log Analytics 작업 영역에서 프라이빗 데이터를 삭제합니다. [자세히 알아보기](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Application Insights에서 데이터 삭제 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 로그 분석 데이터를 봅니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | 작업 영역에서 지정된 데이터를 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight 클러스터 운영자

HDInsight 클러스터 구성을 읽고 수정할 수 있습니다. [자세히 알아보기](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | HDInsight 클러스터에 대한 게이트웨이 설정을 가져옵니다. |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | HDInsight 클러스터에 대한 게이트웨이 설정을 업데이트합니다. |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight 도메인 서비스 기여자

HDInsight Enterprise Security Package에 필요한 도메인 서비스 관련 작업을 읽고, 만들고, 수정하고, 삭제할 수 있습니다. [자세한 정보](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics 참가자

Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다. [자세히 알아보기](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Azure Arc 확장을 설치 또는 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics 독자

Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. [자세히 알아보기](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator-legacy"></a>Purview 데이터 보존자(레거시)

Microsoft.Purview 데이터 동료는 카탈로그 데이터 개체를 생성, 읽기, 수정 및 삭제하고 개체 간의 관계를 설정할 수 있는 레거시 역할입니다. 최근에 Azure 역할 기반 액세스에서 이 역할을 더 이상 사용되지 않으며 Azure Purview 데이터 평면 내에 새로운 데이터 경계선이 도입되었습니다. [Azure Purview의 액세스 제어 - 역할을](../purview/catalog-permissions.md#roles) 참조하세요.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Microsoft Purview 공급자에 대한 계정 리소스를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | 데이터 개체를 읽습니다. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | 데이터 개체를 만들고, 업데이트하고, 삭제합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator is a legacy role that can create, read, modify and delete catalog data objects and establish relationships between objects. We have recently deprecated this role from Azure role-based access and introduced a new data curator inside Azure Purview data plane. See https://docs.microsoft.com/azure/purview/catalog-permissions#roles",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader-legacy"></a>Purview 데이터 판독기(레거시)

Microsoft.Purview 데이터 판독기는 카탈로그 데이터 개체를 읽을 수 있는 레거시 역할입니다. 최근에 Azure 역할 기반 액세스에서 이 역할을 더 이상 사용되지 않으며 Azure Purview 데이터 평면 내에 새 데이터 판독기를 도입했습니다. [Azure Purview의 액세스 제어 - 역할을](../purview/catalog-permissions.md#roles) 참조하세요.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Microsoft Purview 공급자에 대한 계정 리소스를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | 데이터 개체를 읽습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader is a legacy role that can read catalog data objects. We have recently deprecated this role from Azure role-based access and introduced a new data reader inside Azure Purview data plane. See https://docs.microsoft.com/azure/purview/catalog-permissions#roles",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator-legacy"></a>Purview 데이터 원본 관리자(레거시)

Microsoft.Purview 데이터 원본 관리자는 데이터 원본 및 데이터 검색을 관리할 수 있는 레거시 역할입니다. 최근에 Azure 역할 기반 액세스에서 이 역할을 더 이상 사용되지 않으며 Azure Purview 데이터 평면 내에 새 데이터 원본 관리자를 도입했습니다. [Azure Purview의 액세스 제어 - 역할을](../purview/catalog-permissions.md#roles) 참조하세요.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Microsoft Purview 공급자에 대한 계정 리소스를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | 데이터 원본 및 검색을 읽습니다. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | 데이터 원본을 만들고, 업데이트하고, 삭제하며 검색을 관리합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator is a legacy role that can manage data sources and data scans. We have recently deprecated this role from Azure role-based access and introduced a new data source admin inside Azure Purview data plane. See https://docs.microsoft.com/azure/purview/catalog-permissions#roles",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>스키마 레지스트리 기여자(미리 보기)

스키마 레지스트리 그룹 및 스키마를 읽고, 쓰고, 삭제합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>스키마 레지스트리 판독기(미리 보기)

스키마 레지스트리 그룹 및 스키마를 읽고 나열합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | SchemaGroup 리소스 설명의 목록을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | 스키마 검색 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>블록체인


### <a name="blockchain-member-node-access-preview"></a>블록체인 멤버 노드 액세스(미리 보기)

블록체인 멤버 노드에 액세스할 수 있습니다. [자세한 정보](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | 기존 블록체인 멤버 트랜잭션 노드를 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | 블록체인 멤버 트랜잭션 노드에 연결합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + 기계 학습


### <a name="azureml-data-scientist"></a>AzureML 데이터 과학자

컴퓨팅 리소스를 만들거나 삭제하고 작업 영역 자체를 수정하는 것을 제외하고 Azure Machine Learning 작업 영역에서 모든 작업을 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/read |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/action |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/delete |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/write |  |
> | **NotActions** |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/delete | Machine Learning Services 작업 영역을 삭제합니다. |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/write | Machine Learning Services 작업 영역을 만들거나 업데이트합니다. |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/computes/*/write |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/computes/*/delete |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/computes/listKeys/action | Machine Learning Services 작업 영역에서 컴퓨팅 리소스에 대한 비밀을 나열합니다. |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/listKeys/action | Machine Learning Services 작업 영역에 대한 비밀을 나열합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform all actions within an Azure Machine Learning workspace, except for creating or deleting compute resources and modifying the workspace itself.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f6c7c914-8db3-469d-8ca1-694a8f32e121",
  "name": "f6c7c914-8db3-469d-8ca1-694a8f32e121",
  "permissions": [
    {
      "actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
      ],
      "notActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AzureML Data Scientist",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-contributor"></a>Cognitive Services 기여자

Cognitive Services의 키를 만들고, 읽고, 업데이트하고, 삭제 및 관리할 수 있습니다. [자세히 알아보기](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | 구독 기능을 가져옵니다. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | 지정된 리소스 공급자에서 구독의 기능을 가져옵니다. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/register/action | 지정된 리소스 공급자에서 구독의 기능을 등록합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 로그 정의 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Features/providers/features/register/action",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision 기여자

프로젝트를 보거나, 만들거나, 편집하거나, 삭제할 수 있는 기능을 포함하는 프로젝트에 대한 모든 권한입니다. [자세히 알아보기](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Cognitive Services Custom Vision 배포

모델을 게시하거나, 게시 취소하거나, 내보냅니다. 배포에서 프로젝트를 볼 수는 있지만 업데이트할 수는 없습니다. [자세히 알아보기](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | 프로젝트를 내보냅니다. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Cognitive Services Custom Vision 레이블 지정자

학습 이미지를 보고 편집하고, 이미지 태그를 만들거나, 추가하거나, 제거하거나, 삭제합니다. 레이블 지정자는 프로젝트를 볼 수 있지만, 학습 이미지 및 태그 이외의 항목을 업데이트할 수 없습니다. [자세히 알아보기](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | 예측 엔드포인트로 전송된 이미지를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | 이 API는 태그에 대한 신뢰도와 함께 태그가 지정되지 않은 이미지의 배열/일괄 처리에 대한 제안된 태그 및 영역을 가져옵니다. 태그를 찾을 수 없는 경우 빈 배열을 반환합니다. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | 프로젝트를 내보냅니다. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision 읽기 권한자

프로젝트의 읽기 전용 작업입니다. 읽기 권한자는 프로젝트를 만들거나 업데이트할 수 없습니다. [자세히 알아보기](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | 예측 엔드포인트로 전송된 이미지를 가져옵니다. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | 프로젝트를 내보냅니다. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision 강사

모델을 게시, 게시 취소, 내보내는 기능을 포함하여 프로젝트를 보고 편집하고 모델을 학습합니다. 강사는 프로젝트를 만들거나 삭제할 수 없습니다. [자세히 알아보기](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | 프로젝트를 생성합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | 특정 프로젝트를 삭제합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | 프로젝트를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | 프로젝트를 내보냅니다. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services 데이터 읽기 권한자(미리 보기)

Cognitive Services 데이터를 읽을 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-face-recognizer"></a>Cognitive Services 얼굴 인식기

Face API에서 유사한 작업을 검색, 확인, 식별, 그룹화하고 찾을 수 있습니다. 이 역할은 만들기 또는 삭제 작업을 허용하지 않으므로, ‘최소 권한’ 모범 사례에 따라 추론 기능만 필요한 엔드포인트에 적합합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/detect/action | 이미지에서 사람의 얼굴을 검색하고, 얼굴 사각형을 반환하고, 선택적으로 faceId, 랜드마크 및 특성을 함께 반환합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/verify/action | 두 얼굴이 동일한 사람인지 또는 하나의 얼굴이 한 사람에게 속하는지 여부를 확인합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/identify/action | 일대다 식별을 통해 사람 그룹 또는 대규모 사람 그룹에서 특정 쿼리 사람 얼굴과 가장 가까운 일치를 찾습니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/group/action | 얼굴 유사성을 기준으로 후보 얼굴을 그룹으로 나눕니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/findsimilars/action | 쿼리 얼굴의 faceId에 따라 faceId 배열, 얼굴 목록 또는 대규모 얼굴 목록에서 유사한 모습의 얼굴을 검색합니다. faceId |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you perform detect, verify, identify, group, and find similar operations on Face API. This role does not allow create or delete operations, which makes it well suited for endpoints that only need inferencing capabilities, following 'least privilege' best practices.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9894cab4-e18a-44aa-828b-cb588cd6f2d7",
  "name": "9894cab4-e18a-44aa-828b-cb588cd6f2d7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/Face/detect/action",
        "Microsoft.CognitiveServices/accounts/Face/verify/action",
        "Microsoft.CognitiveServices/accounts/Face/identify/action",
        "Microsoft.CognitiveServices/accounts/Face/group/action",
        "Microsoft.CognitiveServices/accounts/Face/findsimilars/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Face Recognizer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services Metrics Advisor 관리자

시스템 수준 구성을 포함하는 프로젝트에 대한 모든 권한입니다. [자세히 알아보기](../applied-ai-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker 편집기

KB를 만들고, 편집하고, 가져오고, 내보낼 수 있습니다. KB를 게시하거나 삭제할 수 없습니다. [자세히 알아보기](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | 역할 할당에 대한 정보를 가져옵니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | 역할 정의에 대한 정보를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | 기술 자료를 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | 새 기술 자료를 만드는 비동기 작업입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | 기술 자료를 수정하거나 기술 자료 콘텐츠를 교체하는 비동기 작업입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | 기술 자료를 쿼리하기 위한 GenerateAnswer 호출입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | 기술 자료에 제안 사항을 추가하기 위해 호출을 학습합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | 런타임에서 변경 사항을 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | 변경 데이터를 바꿉니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | 엔드포인트에 대한 엔드포인트 키를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | 엔드포인트 키를 다시 생성합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | 엔드포인트에 대한 엔드포인트 설정을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | 엔드포인트에 대한 엔드포인트 설정을 업데이트합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | 특정 장기 실행 작업에 대한 세부 정보를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | 기술 자료를 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | 새 기술 자료를 만드는 비동기 작업입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | 기술 자료를 수정하거나 기술 자료 콘텐츠를 교체하는 비동기 작업입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | 기술 자료를 쿼리하기 위한 GenerateAnswer 호출입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | 기술 자료에 제안 사항을 추가하기 위해 호출을 학습합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | 런타임에서 변경 사항을 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | 변경 데이터를 바꿉니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | 엔드포인트에 대한 엔드포인트 키를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | 엔드포인트 키를 다시 생성합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | 엔드포인트에 대한 엔드포인트 설정을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | 엔드포인트에 대한 엔드포인트 설정을 업데이트합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | 특정 장기 실행 작업에 대한 세부 정보를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | 기술 자료를 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | 새 기술 자료를 만드는 비동기 작업입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | 기술 자료를 수정하거나 기술 자료 콘텐츠를 교체하는 비동기 작업입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | 기술 자료를 쿼리하기 위한 GenerateAnswer 호출입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | 기술 자료에 제안 사항을 추가하기 위해 호출을 학습합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | 런타임에서 변경 사항을 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/write | 변경 데이터를 바꿉니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | 엔드포인트에 대한 엔드포인트 키를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | 엔드포인트 키를 다시 생성합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | 엔드포인트에 대한 엔드포인트 설정을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | 엔드포인트에 대한 엔드포인트 설정을 업데이트합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | 특정 장기 실행 작업에 대한 세부 정보를 가져옵니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Cognitive Services QnA Maker 읽기 권한자

KB만 읽고 테스트할 수 있습니다. [자세히 알아보기](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | 역할 할당에 대한 정보를 가져옵니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | 역할 정의에 대한 정보를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | 기술 자료를 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | 기술 자료를 쿼리하기 위한 GenerateAnswer 호출입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | 런타임에서 변경 사항을 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | 엔드포인트에 대한 엔드포인트 키를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | 엔드포인트에 대한 엔드포인트 설정을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | 기술 자료를 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | 기술 자료를 쿼리하기 위한 GenerateAnswer 호출입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | 런타임에서 변경 사항을 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | 엔드포인트에 대한 엔드포인트 키를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | 엔드포인트에 대한 엔드포인트 설정을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | 특정 knowledgebaser의 기술 자료 또는 세부 정보 목록을 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | 기술 자료를 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | 기술 자료를 쿼리하기 위한 GenerateAnswer 호출입니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | 런타임에서 변경 사항을 다운로드합니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | 엔드포인트에 대한 엔드포인트 키를 가져옵니다. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | 엔드포인트에 대한 엔드포인트 설정을 가져옵니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services 사용자

Cognitive Services의 키를 읽고 나열할 수 있습니다. [자세히 알아보기](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | 키 나열 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | 리소스 진단 설정을 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | 로그 정의 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | 메트릭 정의 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | 메트릭 읽기 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>사물 인터넷


### <a name="device-update-administrator"></a>디바이스 업데이트 관리자

관리 및 콘텐츠 작업에 대한 모든 권한을 제공합니다. [자세한 정보](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | 업데이트와 관련된 읽기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | 업데이트와 관련된 쓰기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | 업데이트와 관련된 삭제 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | 관리와 관련된 읽기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | 관리와 관련된 쓰기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | 관리와 관련된 삭제 작업을 수행합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>디바이스 업데이트 콘텐츠 관리자

콘텐츠 작업에 대한 모든 권한을 제공합니다. [자세한 정보](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | 업데이트와 관련된 읽기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | 업데이트와 관련된 쓰기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | 업데이트와 관련된 삭제 작업을 수행합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>디바이스 업데이트 콘텐츠 읽기 권한자

콘텐츠 작업에 대한 읽기 권한을 제공하지만, 변경은 허용하지 않습니다. [자세한 정보](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | 업데이트와 관련된 읽기 작업을 수행합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>디바이스 업데이트 배포 관리자

관리 작업에 대한 모든 권한을 제공합니다. [자세한 정보](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | 관리와 관련된 읽기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | 관리와 관련된 쓰기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | 관리와 관련된 삭제 작업을 수행합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>디바이스 업데이트 배포 읽기 권한자

관리 작업에 대한 읽기 권한을 제공하지만, 변경은 허용하지 않습니다. [자세한 정보](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | 관리와 관련된 읽기 작업을 수행합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>디바이스 업데이트 읽기 권한자

관리 및 콘텐츠 작업에 대한 읽기 권한을 제공하지만, 변경은 허용하지 않습니다. [자세한 정보](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | 업데이트와 관련된 읽기 작업을 수행합니다. |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | 관리와 관련된 읽기 작업을 수행합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-data-contributor"></a>IoT Hub 데이터 기여자

IoT Hub 데이터 평면 작업에 대한 모든 권한을 허용합니다. [자세히 알아보기](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to IoT Hub data plane operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fc6c259-987e-4a07-842e-c321cc9d413f",
  "name": "4fc6c259-987e-4a07-842e-c321cc9d413f",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-data-reader"></a>IoT Hub 데이터 판독기

IoT Hub 데이터 평면 속성에 대한 전체 읽기 권한을 허용합니다. [자세히 알아보기](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/*/read |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/fileUpload/notifications/action | 파일 업로드 알림을 수신, 완료하거나 중단합니다 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full read access to IoT Hub data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b447c946-2db7-41ec-983d-d8bf3b1c77e3",
  "name": "b447c946-2db7-41ec-983d-d8bf3b1c77e3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/*/read",
        "Microsoft.Devices/IotHubs/fileUpload/notifications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-registry-contributor"></a>IoT Hub 레지스트리 기여자

IoT Hub 디바이스 레지스트리에 대한 모든 권한을 허용합니다. [자세히 알아보기](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/devices/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to IoT Hub device registry.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4ea46cd5-c1b2-4a8e-910b-273211f9ce47",
  "name": "4ea46cd5-c1b2-4a8e-910b-273211f9ce47",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/devices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Registry Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-twin-contributor"></a>IoT Hub 쌍 기여자

모든 IoT Hub 디바이스 및 모듈 쌍에 대한 읽기 및 쓰기 권한을 허용합니다. [자세히 알아보기](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/twins/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read and write access to all IoT Hub device and module twins.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494bdba2-168f-4f31-a0a1-191d2f7c028c",
  "name": "494bdba2-168f-4f31-a0a1-191d2f7c028c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/twins/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Twin Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>혼합 현실


### <a name="remote-rendering-administrator"></a>Remote Rendering 관리자

사용자에게 Azure Remote Rendering에 대한 변환, 세션 관리, 렌더링, 진단 기능을 제공합니다. [자세한 정보](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | 자산 변환 시작 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | 자산 변환 속성 가져오기 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | 자산 변환 중지 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 세션 속성 설정 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 세션 시작 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 세션 중지 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 세션에 연결 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Remote Rendering 검사자에 연결 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Remote Rendering 클라이언트

사용자에게 Azure Remote Rendering에 대한 세션 관리, 렌더링, 진단 기능을 제공합니다. [자세히 알아보기](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | 세션 속성 설정 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | 세션 시작 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | 세션 중지 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | 세션에 연결 |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Remote Rendering 검사자에 연결 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors 계정 기여자

계정의 공간 앵커를 관리할 수 있지만 삭제할 수는 없습니다. [자세한 정보](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 공간 앵커를 만듭니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 주변의 공간 앵커를 검색합니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors 서비스 품질 개선에 도움이 되는 진단 데이터를 제출합니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 공간 앵커 속성을 업데이트합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors 계정 소유자

계정의 공간 앵커를 관리할 수 있고 삭제할 수도 있습니다. [자세한 정보](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | 공간 앵커를 만듭니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | 공간 앵커를 삭제합니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 주변의 공간 앵커를 검색합니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors 서비스 품질 개선에 도움이 되는 진단 데이터를 제출합니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | 공간 앵커 속성을 업데이트합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors 계정 읽기 권한자

계정의 공간 앵커 속성을 찾아서 읽을 수 있습니다. [자세한 정보](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | 주변의 공간 앵커를 검색합니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | 공간 앵커의 속성을 가져옵니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | 공간 앵커를 찾습니다. |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Azure Spatial Anchors 서비스 품질 개선에 도움이 되는 진단 데이터를 제출합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>통합


### <a name="api-management-service-contributor"></a>API Management 서비스 참가자

서비스 및 API를 관리할 수 있습니다. [자세한 정보](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | API Management 서비스 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management 서비스 운영자 역할

서비스를 관리할 수 있지만, API는 관리할 수 없습니다. [자세한 정보](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | API Management 서비스 인스턴스 읽기 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너로 API Management 서비스를 백업합니다. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | API Management 서비스 인스턴스를 삭제합니다. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | SKU/단위를 변경하고 API Management 서비스의 지역별 배포를 추가 또는 제거합니다. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | 사용자가 제공한 스토리지 계정의 지정된 컨테이너에서 API Management 서비스 복원 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | API Management 서비스에 대한 TLS/SSL 인증서를 업로드합니다. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | API Management 서비스에 대한 사용자 지정 도메인 이름 설정, 업데이트 또는 제거합니다. |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | API Management 서비스 인스턴스를 만들거나 업데이트합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 사용자와 연결된 키를 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Azure API Management 읽기 권한자 역할

서비스 및 API에 대한 읽기 전용 액세스 [자세한 정보](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | API Management 서비스 인스턴스 읽기 |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | API Management 서비스 인스턴스에 대한 메타데이터 읽기 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | 사용자와 연결된 키를 가져옵니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>App Configuration 데이터 소유자

App Configuration 데이터에 대한 모든 액세스 권한을 허용합니다. [자세히 알아보기](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>App Configuration 데이터 읽기 권한자

App Configuration 데이터에 대한 읽기 액세스 권한을 허용합니다. [자세히 알아보기](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-relay-listener"></a>Azure Relay 수신자

Azure Relay 리소스에 대한 수신 대기 액세스 권한을 허용합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/wcfRelays/read |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/hybridConnections/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/listen/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for listen access to Azure Relay resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/26e0b698-aa6d-4085-9386-aadae190014d",
  "name": "26e0b698-aa6d-4085-9386-aadae190014d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Relay/*/wcfRelays/read",
        "Microsoft.Relay/*/hybridConnections/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Relay/*/listen/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Relay Listener",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-relay-owner"></a>Azure Relay 소유자

Azure Relay 리소스에 대한 전체 액세스 권한을 허용합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Relay resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2787bf04-f1f5-4bfe-8383-c8a24483ee38",
  "name": "2787bf04-f1f5-4bfe-8383-c8a24483ee38",
  "permissions": [
    {
      "actions": [
        "Microsoft.Relay/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Relay/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Relay Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-relay-sender"></a>Azure Relay 발신자

Azure Relay 리소스에 대한 보내기 액세스 권한을 허용합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/wcfRelays/read |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/hybridConnections/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/send/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Relay resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/26baccc8-eea7-41f1-98f4-1762cc7f685d",
  "name": "26baccc8-eea7-41f1-98f4-1762cc7f685d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Relay/*/wcfRelays/read",
        "Microsoft.Relay/*/hybridConnections/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Relay/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Relay Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus 데이터 소유자

Azure Service Bus 리소스에 대한 전체 액세스를 허용합니다. [자세히 알아보기](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus 데이터 받는 사람

Azure Service Bus 리소스에 대한 받기 액세스 권한을 허용합니다. [자세히 알아보기](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus 데이터 보내는 사람

Azure Service Bus 리소스에 대한 보내기 액세스 권한을 허용합니다. [자세히 알아보기](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack 등록 소유자

Azure Stack 등록을 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Azure Stack Marketplace 제품의 속성 가져오기 |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Azure Stack 등록의 속성 가져오기 |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-contributor"></a>EventGrid 기여자

EventGrid 작업을 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Event Grid 리소스 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-data-sender"></a>EventGrid 데이터 발신자

Event Grid 이벤트에 대한 보내기 액세스 권한을 허용합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topics/read | 토픽을 읽습니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/domains/read | 도메인을 읽습니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/partnerNamespaces/read | 파트너 네임스페이스를 읽습니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/events/send/action | 토픽에 이벤트를 보냅니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to event grid events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d5a91429-5739-47e2-a06b-3470a27159e7",
  "name": "d5a91429-5739-47e2-a06b-3470a27159e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/topics/read",
        "Microsoft.EventGrid/domains/read",
        "Microsoft.EventGrid/partnerNamespaces/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventGrid/events/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription 기여자

EventGrid 이벤트 구독 작업을 관리할 수 있습니다. [자세히 알아보기](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | 지역 이벤트 구독 만들기 및 관리 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 항목 유형별로 글로벌 이벤트 구독을 나열합니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription 읽기 권한자

EventGrid 이벤트 구독을 읽을 수 있습니다. [자세히 알아보기](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | eventSubscription을 읽습니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | 항목 유형별로 글로벌 이벤트 구독을 나열합니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | 지역 이벤트 구독을 나열합니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | 항목 유형별로 지역 이벤트 구독을 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR 데이터 기여자

역할을 통해 사용자 또는 보안 주체에게 FHIR 데이터에 대한 모든 권한을 제공할 수 있습니다. [자세한 정보](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR 데이터 내보내기 도구

역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 내보낼 수 있습니다. [자세한 정보](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | 검색 및 버전 관리 기록이 포함된 FHIR 리소스를 읽습니다.  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | 작업을 내보냅니다($export). |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/read | 검색 및 버전 관리 기록이 포함된 FHIR 리소스를 읽습니다.  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/export/action | 작업을 내보냅니다($export). |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/read",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR 데이터 읽기 권한자

역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽을 수 있습니다. [자세한 정보](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | 검색 및 버전 관리 기록이 포함된 FHIR 리소스를 읽습니다.  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/read | 검색 및 버전 관리 기록이 포함된 FHIR 리소스를 읽습니다.  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR 데이터 쓰기 권한자

역할을 통해 사용자 또는 보안 주체가 FHIR 데이터를 읽고 쓸 수 있습니다. [자세한 정보](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | 하드 삭제(버전 기록 포함) |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/hardDelete/action | 하드 삭제(버전 기록 포함) |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>통합 서비스 환경 기여자

통합 서비스 환경을 관리할 수 있지만 액세스할 수는 없습니다. [자세히 알아보기](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>통합 서비스 환경 개발자

개발자가 통합 서비스 환경에서 워크플로, 통합 계정, API 연결을 만들고 업데이트할 수 있습니다. [자세히 알아보기](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | 통합 서비스 환경을 읽습니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>지능형 시스템 계정 참가자

인텔리전트 시스템 계정을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | Microsoft.IntelligentSystems/accounts/* | 지능형 시스템 계정 만들기 및 관리 |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>논리 앱 참가자

논리 앱을 관리할 수 있지만 앱을 변경할 수는 없습니다. [자세히 알아보기](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | 스토리지 계정의 액세스 키를 나열합니다. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | 지정된 계정의 스토리지 계정을 반환합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | 이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Logic Apps 리소스를 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | 연결 게이트웨이를 만들고 관리합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | 연결을 만들고 관리합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | 사용자 지정 API를 만들고 관리합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | App Service 요금제에 가입합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | 함수 비밀을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>논리 앱 운영자

논리 앱을 읽고 사용하도록 설정하고 사용하지 않도록 설정할 수 있지만 편집하거나 업데이트할 수는 없습니다. [자세히 알아보기](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Insights 경고 규칙을 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Logic Apps에 대한 진단 설정을 가져옵니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Logic Apps에 사용 가능한 메트릭을 가져옵니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Logic Apps 리소스를 읽습니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | 워크플로를 사용하지 않도록 설정합니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | 워크플로를 사용하도록 설정합니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | 워크플로의 유효성을 검사합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | 연결 게이트웨이를 읽습니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | 연결을 읽습니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | 사용자 지정 API를 읽습니다. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | App Service 계획의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>ID


### <a name="managed-identity-contributor"></a>관리 ID 참가자

사용자 할당 ID를 만들고, 읽고, 업데이트하고, 삭제합니다. [자세한 정보](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | 기존 사용자 할당 ID를 가져옵니다. |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | 새로운 사용자 할당 ID를 만들거나 기존 사용자 할당 ID와 연결된 태그를 업데이트합니다. |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | 기존 사용자 할당 ID를 삭제합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>관리 ID 운영자

사용자 할당 ID를 읽고 할당합니다. [자세한 정보](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>보안


### <a name="attestation-contributor"></a>증명 기여자

증명 공급자 인스턴스 읽기/쓰기 또는 삭제할 수 있습니다. [자세한 정보](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>증명 판독기

증명 공급자 속성을 읽을 수 있습니다. [자세한 정보](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel Automation 기여자

Azure Sentinel Automation 기여자 [자세히 알아보기](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | 트리거를 읽습니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | 워크플로의 콜백 URL을 가져옵니다. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | 워크플로 실행을 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Azure Sentinel 기여자

Azure Sentinel 기여자 [자세한 정보](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 로그 분석 데이터를 봅니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/querypacks/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | 프라이빗 통합 문서 읽기 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/querypacks/*/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel 읽기 권한자

Azure Sentinel 읽기 권한자 [자세한 정보](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 사용자 권한 부여 및 라이선스를 확인합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | 위협 인텔리전스 지표를 쿼리합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | 위협 인텔리전스 지표를 쿼리합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 로그 분석 데이터를 봅니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | 지정된 작업 영역에서 연결된 서비스를 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 저장된 검색 쿼리를 가져옵니다. |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/querypacks/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | 프라이빗 통합 문서 읽기 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/querypacks/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel 응답자

Azure Sentinel 응답자 [자세한 정보](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | 사용자 권한 부여 및 라이선스를 확인합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | 위협 인텔리전스 지표에 태그를 추가합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | 위협 인텔리전스 지표를 쿼리합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | 위협 인텔리전스에 대량 태그를 지정합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | 위협 인텔리전스 지표에 태그를 추가합니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | 위협 인텔리전스 지표의 태그를 바꿉니다. |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | 위협 인텔리전스 지표를 쿼리합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | 새 엔진을 사용하여 검색합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 로그 분석 데이터를 봅니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | 저장된 검색 쿼리를 가져옵니다. |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | 기존 OMS 솔루션을 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | 작업 영역의 데이터에서 쿼리를 실행 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | 작업 영역의 데이터 원본을 가져옵니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/querypacks/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | 프라이빗 통합 문서 읽기 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/querypacks/*/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault 관리자

인증서, 키, 비밀을 포함하여 Key Vault 및 해당 Key Vault에 있는 모든 개체에 대한 모든 데이터 평면 작업을 수행합니다. Key Vault 리소스를 관리하거나 역할 할당을 관리할 수 없습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault 인증서 책임자

권한 관리를 제외한 Key Vault의 인증서에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault 참가자

Key Vault를 관리하지만, Azure RBAC에서 역할을 할당하는 것을 허용하지 않으며 비밀, 키 또는 인증서에 액세스할 수 없습니다. [자세히 알아보기](../key-vault/general/security-features.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | 일시 삭제된 Key Vault를 제거합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Key Vault 암호화 책임자

권한 관리를 제외한 Key Vault 키에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault 암호화 서비스 암호화 사용자

키의 메타데이터를 읽고 래핑/래핑 해제 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | eventSubscription을 만들거나 업데이트합니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | eventSubscription을 읽습니다. |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | eventSubscription을 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 지정된 자격 증명 모음에 키를 나열하거나 키의 속성 및 퍼블릭 자료를 읽습니다. 비대칭 키의 경우 이 작업은 퍼블릭 키를 노출하고 서명 암호화 및 확인 같은 퍼블릭 키 알고리즘을 수행하는 기능을 포함합니다. 프라이빗 키 및 대칭 키는 노출되지 않습니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Key Vault 키를 사용하여 대칭 키를 래핑합니다. Key Vault 키가 비대칭이면 읽기 권한이 있는 보안 주체가 이 작업을 수행할 수 있습니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Key Vault 키를 사용하여 대칭 키의 래핑을 해제합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault 암호화 사용자

키를 사용하여 암호화 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | 지정된 자격 증명 모음에 키를 나열하거나 키의 속성 및 퍼블릭 자료를 읽습니다. 비대칭 키의 경우 이 작업은 퍼블릭 키를 노출하고 서명 암호화 및 확인 같은 퍼블릭 키 알고리즘을 수행하는 기능을 포함합니다. 프라이빗 키 및 대칭 키는 노출되지 않습니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | 주어진 키와 연결된 지정된 특성을 업데이트합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | 키의 백업 파일을 만듭니다. 이 파일은 동일한 구독의 Key Vault에서 키를 복원하는 데 사용할 수 있습니다. 제한이 적용될 수 있습니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | 키를 사용하여 일반 텍스트를 암호화합니다. 키가 비대칭이면 읽기 권한이 있는 보안 주체가 이 작업을 수행할 수 있습니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | 키를 사용하여 암호 텍스트를 해독합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Key Vault 키를 사용하여 대칭 키를 래핑합니다. Key Vault 키가 비대칭이면 읽기 권한이 있는 보안 주체가 이 작업을 수행할 수 있습니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Key Vault 키를 사용하여 대칭 키의 래핑을 해제합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | 키를 사용하여 메시지 다이제스트(해시)에 서명합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | 키를 사용하여 메시지 다이제스트(해시)의 서명을 확인합니다. 키가 비대칭이면 읽기 권한이 있는 보안 주체가 이 작업을 수행할 수 있습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault 읽기 권한자

Key Vault 및 해당 인증서, 키, 비밀의 메타데이터를 읽습니다. 비밀 내용 또는 키 자료와 같은 중요한 값을 읽을 수 없습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 비밀의 값이 아닌 비밀의 속성을 나열하거나 봅니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault 비밀 책임자

권한 관리를 제외한 Key Vault의 비밀에 대한 작업을 수행합니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Key Vault 이름이 유효하며 사용 중이 아닌지 확인합니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | 일시 삭제된 여러 Key Vault의 속성을 봅니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Microsoft.KeyVault 리소스 공급자에서 사용 가능한 작업을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault 비밀 사용자

비밀 내용을 읽습니다. 'Azure 역할 기반 액세스 제어' 권한 모델을 사용하는 Key Vault에만 적용됩니다. [자세히 알아보기](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | 비밀의 값을 가져옵니다. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | 비밀의 값이 아닌 비밀의 속성을 나열하거나 봅니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>관리형 HSM 기여자

관리형 HSM 풀을 관리할 수 있지만 액세스할 수는 없습니다. [자세히 알아보기](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>보안 관리자

Security Center에 대한 권한을 살펴보고 업데이트할 수 있습니다. 보안 읽기 권한자 역할과 동일한 권한이며, 보안 정책을 업데이트하고 경고 및 권장 사항을 해제할 수도 있습니다. [자세히 알아보기](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | 정책 할당 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | 정책 정의 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | 정책 예외 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | 정책 집합 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 로그 분석 데이터를 봅니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | 보안 구성 요소 및 정책 만들기 및 관리 |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/defenderSettings/write | IoT Defender 설정을 만들거나 업데이트합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.IoTSecurity/*",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.IoTSecurity/defenderSettings/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>보안 평가 기여자

Security Center로 평가를 푸시할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | 구독에 대한 보안 평가를 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>보안 관리자(레거시)

레거시 역할입니다. 그 대신 보안 관리자를 사용하세요.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | 클래식 가상 머신에 대한 구성 정보 읽기 |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | 클래식 가상 머신에 대한 구성 정보 쓰기 |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | 클래식 네트워크에 대한 구성 정보 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | 보안 구성 요소 및 정책 만들기 및 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>보안 판독기

Security Center에 대한 권한을 살펴볼 수 있습니다. 권장 사항, 경고, 보안 정책 및 보안 상태를 볼 수 있지만 변경할 수는 없습니다. [자세히 알아보기](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | 로그 분석 데이터를 봅니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | 보안 구성 요소 및 정책 읽기 |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | 다운로드 가능한 IoT Defender 패키지 정보를 가져옵니다. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | 구독 할당량 데이터가 포함된 관리자 활성화 파일을 다운로드합니다. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | IoT 센서에 대한 암호 재설정 파일을 다운로드합니다. |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/defenderSettings/packageDownloads/action | 다운로드 가능한 IoT Defender 패키지 정보를 가져옵니다. |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/defenderSettings/downloadManagerActivation/action | 관리자 활성화 파일을 다운로드합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.IoTSecurity/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.IoTSecurity/defenderSettings/packageDownloads/action",
        "Microsoft.IoTSecurity/defenderSettings/downloadManagerActivation/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Lab 사용자

Azure DevTest Labs의 가상 머신을 연결, 시작, 다시 시작 및 종료할 수 있습니다. [자세히 알아보기](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | 가용성 집합의 속성을 가져옵니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | 가상 머신(VM 크기, 런타임 상태, VM 확장 등)의 속성 읽기 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | 가상 머신을 끄고 Compute 리소스를 해제합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | 가상 머신의 속성을 가져옵니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | 가상 머신을 다시 시작합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | 가상 머신 시작 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | 랩의 속성 읽기 |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | 랩에서 임의 클레임 가능 가상 머신을 클레임합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | 랩에 가상 머신을 만듭니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | 현재 사용자가 랩에서 유효한 프로필을 갖도록 관리합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | 수식을 삭제합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | 수식을 읽습니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | 수식을 추가하거나 수정합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | 랩 정책을 평가합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | 기존 가상 머신의 소유권을 가져옵니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | 해당 시작/중지 일정이 있는 경우 이를 나열합니다. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | 가상 머신에 대한 RDP 파일의 콘텐츠를 나타내는 문자열을 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | 부하 분산 장치 백 엔드 주소 풀을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | 부하 분산 장치 인바운드 NAT 규칙을 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | 네트워크 인터페이스(예: 네트워크 인터페이스의 일부인 모든 부하 분산 장치)의 속성 읽기 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Virtual Machine을 네트워크 인터페이스에 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | 네트워크 인터페이스 정의를 가져옵니다.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | 네트워크 인터페이스를 만들거나 기존 네트워크 인터페이스를 업데이트합니다.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | 공용 IP 주소의 속성 읽기 |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | 공용 IP 주소를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | 공용 IP 주소 정의를 가져옵니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | 가상 네트워크를 조인합니다. 경고할 수 없습니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | 배포 작업을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | 배포를 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | 가상 머신이 업데이트될 수 있는 사용 가능한 크기를 나열합니다. |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>랩 작성자

Azure Lab Accounts에서 새 랩을 만들 수 있습니다. [자세히 알아보기](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | 랩 계정에서 랩을 만듭니다. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | 랩 계정에 대한 크기, 지역 및 운영 체제 조합의 가격 책정 및 가용성을 가져옵니다. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | 이 구독에 대한 주요 제한 및 사용 정보를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>모니터


### <a name="application-insights-component-contributor"></a>Application Insights 구성 요소 참가자

Application Insights 구성 요소를 관리할 수 있습니다. [자세한 정보](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 경고 규칙을 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | 라이브 메트릭 토큰 가져오기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | 새 경고 규칙을 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights 구성 요소 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/topology/read | 읽기 토폴로지 |
> | [Microsoft Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | 읽기 트랜잭션 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights 웹 테스트를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights 스냅샷 디버거

Application Insights 스냅샷 디버거를 사용하여 수집한 디버그 스냅샷을 보고 다운로드할 수 있는 사용자 권한을 제공합니다. 이러한 사용 권한은 [소유자](#owner) 또는 [기여자](#contributor) 역할에 포함되지 않습니다. 사용자에게 Application Insights 스냅샷 디버거 역할을 부여할 때 사용자에게 직접 역할을 부여해야 합니다. 이 역할은 사용자 지정 역할에 추가될 때 인식되지 않습니다. [자세히 알아보기](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Monitoring Contributor

모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있음 [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. [자세히 알아보기](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights 구성 요소 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | 구독에서 활동 로그 이벤트(관리 이벤트)를 나열합니다. 이 권한은 활동 로그에 대한 프로그래밍 방식 및 포털 액세스 모두에 적용 가능합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | 이 사용 권한은 포털을 통해 활동 로그에 액세스해야 하는 사용자에게 필요합니다. 활동 로그의 로그 범주를 나열합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | 메트릭 정의(리소스에 사용 가능한 메트릭 형식 목록)를 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | 리소스에 대한 메트릭을 읽습니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights 웹 테스트를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | 새 작업 영역을 만들거나 기존 작업 영역의 고객 ID를 제공하여 기존 작업 영역에 연결합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | 로그 분석 솔루션 팩을 읽고 쓰고 삭제합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | 로그 분석의 저장된 검색을 읽고 쓰고 삭제합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | 로그 분석 스토리지 인사이트 구성을 읽고 쓰고 삭제합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | 게스트 VM 상태 모니터에 대한 정보를 가져옵니다. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>모니터링 메트릭 게시자

Azure 리소스에 대한 게시 메트릭을 사용하도록 설정합니다. [자세한 정보](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Microsoft Insights 공급자를 등록합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | 메트릭을 작성합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Monitoring Reader

모든 모니터링 데이터를 읽을 수 있음(메트릭, 로그 등) [Azure Monitor에서의 역할, 권한 및 보안 시작](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)도 참조하세요. [자세히 알아보기](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | 검색 쿼리를 실행합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>통합 문서 기여자

공유 통합 문서를 저장할 수 있습니다. [자세히 알아보기](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | 통합 문서를 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | 통합 문서 삭제 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>통합 문서 읽기 권한자

통합 문서를 읽을 수 있습니다. [자세히 알아보기](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | 통합 문서를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>관리 + 거버넌스


### <a name="automation-contributor"></a>Automation 기여자

Azure Automation을 사용하여 Azure Automation 리소스 및 기타 리소스를 관리합니다. [자세히 알아보기](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights) /ActionGroups/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights) /ActivityLogAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights) /MetricAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights) /ScheduledQueryRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Analysis Server에 대한 진단 설정 생성, 업데이트 및 읽기 |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | 작업 영역에 대한 공유 키를 검색합니다. 이러한 키는 Microsoft Operational Insights 에이전트를 작업 영역에 연결하는 데 사용됩니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Manage azure automation resources and other resources using azure automation.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f353d9bd-d4a6-484e-a77a-8050b599b867",
  "name": "f353d9bd-d4a6-484e-a77a-8050b599b867",
  "permissions": [
    {
      "actions": [
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/ActionGroups/*",
        "Microsoft.Insights/ActivityLogAlerts/*",
        "Microsoft.Insights/MetricAlerts/*",
        "Microsoft.Insights/ScheduledQueryRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-job-operator"></a>Automation 작업 연산자

Automation Runbook을 사용하여 작업을 만들고 관리합니다. [자세히 알아보기](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | 작업의 출력을 가져옵니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation 운영자

Automation 운영자는 작업을 시작, 중지, 일시 중단, 다시 시작할 수 있습니다. [자세한 정보](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker 리소스를 읽습니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Azure Automation 작업을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Azure Automation 작업을 계속합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Azure Automation 작업을 중지합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Azure Automation 작업 스트림을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Azure Automation 작업을 일시 중단합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Azure Automation 작업을 만듭니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Azure Automation 작업 일정을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Azure Automation 작업 일정을 만듭니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | 자동화 계정에 연결된 작업 영역 가져오기 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Azure Automation 계정을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Azure Automation 일정 자산을 가져옵니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Azure Automation 일정 자산을 만들거나 업데이트합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | 작업의 출력을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook 연산자

Runbook 작업을 만들려면 Runbook 속성을 읽어보세요. [자세히 알아보기](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Azure Automation Runbook을 가져옵니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc 지원 Kubernetes 클러스터 사용자 역할

클러스터 사용자 자격 증명 동작을 나열합니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | clusterUser 자격 증명을 나열합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes 관리자

리소스 할당량 및 네임스페이스 업데이트 또는 삭제를 제외하고 클러스터/네임스페이스의 모든 리소스를 관리할 수 있습니다. [자세히 알아보기](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | controllerrevisions를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | localsubjectaccessreviews를 씁니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | events를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | events를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | limitranges를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | namespaces를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | resourcequotas를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc Kubernetes 클러스터 관리자

클러스터의 모든 리소스를 관리할 수 있습니다. [자세히 알아보기](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc Kubernetes 뷰어

비밀을 제외한 클러스터/네임스페이스의 모든 리소스를 볼 수 있습니다. [자세히 알아보기](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | controllerrevisions를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | daemonsets를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | deployments를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | replicasets를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | statefulsets를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | horizontalpodautoscalers를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | cronjobs를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | jobs를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | configmaps를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | endpoints를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | events를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | events를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | daemonsets를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | deployments를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | ingresses를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | networkpolicies를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | replicasets를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | limitranges를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | namespaces를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | ingresses를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | networkpolicies를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | persistentvolumeclaims를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | pods를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | poddisruptionbudgets를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | replicationcontrollers를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | replicationcontrollers를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | resourcequotas를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | serviceaccounts를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | services를 읽습니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes 작성자

(클러스터)역할 및 (클러스터)역할 바인딩을 제외하고 클러스터/네임스페이스의 모든 항목을 업데이트할 수 있습니다. [자세히 알아보기](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | controllerrevisions를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | events를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | events를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | limitranges를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | namespaces를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | resourcequotas를 읽습니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine 온보딩

Azure Connected Machines을 온보딩할 수 있습니다. [자세히 알아보기](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Azure Arc 머신을 읽습니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Azure Arc 머신을 씁니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | 모든 Azure Arc privateLinkScopes를 읽습니다 |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | 게스트 구성 할당을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/privateLinkScopes/read",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine 리소스 관리자

Azure Connected Machines을 읽고, 쓰고, 삭제하고, 다시 온보딩할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Azure Arc 머신을 읽습니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Azure Arc 머신을 씁니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Azure Arc 머신을 삭제합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/UpgradeExtensions/action | Azure Arc 컴퓨터에서 확장을 업그레이드합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/read | 모든 Azure Arc 확장을 읽습니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Azure Arc 확장을 설치 또는 업데이트합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/delete | Azure Arc 확장을 삭제합니다. |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/UpgradeExtensions/action",
        "Microsoft.HybridCompute/machines/extensions/read",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/machines/extensions/delete",
        "Microsoft.HybridCompute/privateLinkScopes/*",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>청구 읽기 권한자

청구 데이터에 대해 읽기 권한을 허용합니다. [자세한 정보](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | 대금 청구 정보 읽기 |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>청사진 기여자

청사진 정의를 관리할 수 있지만 할당할 수는 없습니다. [자세히 알아보기](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | 청사진 정의 또는 청사진 아티팩트를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>청사진 운영자

게시된 기존 청사진을 할당할 수 있지만 새 청사진을 만들 수는 없습니다. 이 역할은 사용자가 할당한 관리 ID를 사용하여 할당하는 경우에만 작동합니다. [자세히 알아보기](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | 청사진 할당을 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management 기여자

비용을 확인하고 비용 구성(예: 예산, 내보내기)을 관리할 수 있습니다. [자세한 정보](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 구성 가져오기 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 권장 사항을 읽습니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management 읽기 권한자

비용 데이터 및 구성(예: 예산, 내보내기)을 볼 수 있습니다. [자세한 정보](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | 구성 가져오기 |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | 권장 사항을 읽습니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>계층 구조 설정 관리자

사용자가 계층 구조 설정을 편집하고 삭제할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | 관리 그룹 계층 구조 설정을 만들거나 업데이트합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | 관리 그룹 계층 구조 설정을 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes 클러스터 - Azure Arc 온보딩

connectedClusters 리소스를 만들기 위해 모든 사용자/서비스에 권한을 부여하는 역할 정의입니다. [자세한 정보](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | 배포를 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | 구독 작업 결과를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | connectedClusters를 씁니다. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | connectedClusters를 읽습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-extension-contributor"></a>Kubernetes 확장 기여자

Kubernetes 확장을 생성, 업데이트, 가져오기, 나열 및 삭제하고 확장 비동기 작업을 가져올 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/write | 확장 리소스를 만들거나 업데이트합니다. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/read | 확장 인스턴스 리소스를 가져옵니다. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/delete | 확장 인스턴스 리소스를 삭제합니다. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/operations/read | 비동기 작업 상태를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create, update, get, list and delete Kubernetes Extensions, and get extension async operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/85cb6faf-e071-4c9b-8136-154b5a04f717",
  "name": "85cb6faf-e071-4c9b-8136-154b5a04f717",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.KubernetesConfiguration/extensions/write",
        "Microsoft.KubernetesConfiguration/extensions/read",
        "Microsoft.KubernetesConfiguration/extensions/delete",
        "Microsoft.KubernetesConfiguration/extensions/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Extension Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>관리형 애플리케이션 기여자 역할

관리형 애플리케이션 리소스를 만들 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | 솔루션에 등록합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>관리되는 애플리케이션 운영자 역할

관리되는 애플리케이션 리소스에서 작업을 읽고 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | 애플리케이션 목록을 검색합니다. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Managed Applications 읽기 권한자

관리 앱 및 요청 JIT 액세스에서 리소스를 읽을 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>관리형 서비스 등록 할당 삭제 역할

관리형 서비스 등록 할당 삭제 역할은 관리하는 테넌트 사용자가 테넌트에 할당된 등록 할당을 삭제할 수 있도록 허용합니다. [자세히 알아보기](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | 관리형 서비스 등록 할당 목록을 검색합니다. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | 관리형 서비스 등록 할당을 제거합니다. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | 리소스의 작업 상태를 읽습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>관리 그룹 참가자

관리 그룹 기여자 역할 [자세한 정보](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | 관리 그룹을 삭제합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | 관리 그룹에서 구독의 연결을 해제합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | 기존 구독을 관리 그룹과 연결합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | 관리 그룹을 만들거나 업데이트합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 지정된 관리 그룹에 구독을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>관리 그룹 읽기 권한자

관리 그룹 읽기 권한자 역할

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | 인증된 사용자의 관리 그룹을 나열합니다. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | 지정된 관리 그룹에 구독을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>NewRelic APM 계정 참가자

New Relic Application Performance Management 계정 및 애플리케이션을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights 데이터 쓰기 권한자(미리 보기)

리소스 정책에 대한 읽기 액세스 권한과 리소스 구성 요소 정책 이벤트에 대한 쓰기 액세스 권한을 허용합니다. [자세히 알아보기](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | 정책 할당에 대한 정보를 가져옵니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | 정책 정의에 대한 정보를 가져옵니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | 정책 예외에 대한 정보를 가져옵니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | 정책 집합 정의에 대한 정보를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | 특정 구성 요소의 데이터 정책 준수 상태를 확인합니다. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | 리소스 구성 요소 정책 이벤트를 기록합니다. |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="quota-request-operator"></a>할당량 요청 운영자

할당량 요청을 읽고 만들고, 할당량 요청 상태를 가져오고, 지원 티켓을 만듭니다. [자세히 알아보기](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | 지정된 리소스 및 위치의 현재 서비스 제한 또는 할당량을 가져옵니다. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | 지정된 리소스 및 위치의 서비스 제한 또는 할당량을 가져옵니다. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | 지정된 리소스 및 위치에 대한 서비스 제한 요청을 가져옵니다. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | 용량 리소스 공급자를 등록하고 용량 리소스 만들기를 사용하도록 설정합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>예약 구매자

예약을 구매할 수 있습니다. [자세한 정보](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | 구독 목록을 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | 용량 리소스 공급자를 등록하고 용량 리소스 만들기를 사용하도록 설정합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Microsoft.Compute 리소스 공급자에 구독을 등록합니다. |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Microsoft SQL Database 리소스 공급자에 대한 구독을 등록하고 Microsoft SQL Databases를 만들도록 설정합니다. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | 소비 RP에 등록합니다. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | 예약 카탈로그를 읽습니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | 역할 할당에 대한 정보를 가져옵니다. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | 구독의 예약 인스턴스에 대한 단일 또는 공유 권장 사항을 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | 지원 티켓을 만들고 업데이트할 수 있습니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>리소스 정책 참가자

리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 사용자입니다. [자세히 알아보기](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | */read | 암호를 제외한 모든 유형의 리소스를 읽습니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | 정책 할당 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | 정책 정의 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | 정책 예외 만들기 및 관리 |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | 정책 집합 만들기 및 관리 |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery 참가자

자격 증명 모음 만들기 및 역할 할당을 제외한 Site Recovery 서비스를 관리할 수 있습니다. [자세한 정보](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | 리소스 인증서 업데이트 작업은 리소스/저장소 자격 증명 인증서를 업데이트합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | 자격 증명 모음과 관련된 확장 정보 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | 등록된 ID 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | 복제 경고 설정 만들기 또는 업데이트 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | 복제 패브릭 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | 복제 정책 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | 복구 계획 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationVaultSettings/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Recovery Services 자격 증명 모음의 스토리지 구성 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | 자격 증명 모음 복제 작업 상태를 읽습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/vaults/replicationVaultSettings/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery 운영자

장애 조치(failover) 및 장애 복구(failback)를 수행할 수 있지만 다른 Site Recovery 관리 작업은 수행할 수 없습니다. [자세한 정보](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | 가상 네트워크 정의를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | 패브릭의 일관성을 검사합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | 게이트웨이를 다시 연결합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | 패브릭용 인증서 갱신 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 복구 지점을 적용합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 장애 조치(Failover) 커밋 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 계획된 장애 조치(Failover) |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 복제를 복구합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 보호된 항목을 다시 보호합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 보호 컨테이너를 전환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 테스트 장애 조치(Failover) |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 테스트 장애 조치(Failover) 정리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 장애 조치  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 모바일 서비스를 업데이트합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 공급자를 새로 고칩니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | 복제 작업 만들기 및 관리 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | 장애 조치(Failover) 커밋 복구 계획 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | 계획된 장애 조치(Failover) 복구 계획 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | 복구 계획을 다시 보호합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | 테스트 장애 조치(failover) 복구 계획 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 테스트 장애 조치(failover) 정리 복구 계획 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | 장애 조치(Failover) 복구 계획 |
> | [Microsoft RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationVaultSettings/read | 모든 항목을 읽습니다.  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Recovery Services 자격 증명 모음에 대한 경고 읽기 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationVaultSettings/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery 구독자

Site Recovery 상태를 볼 수 있지만 다른 관리 작업은 수행할 수 없습니다. [자세한 정보](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp는 서비스에서 사용하는 내부 작업입니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | 확장 정보 가져오기 작업에서는 ‘자격 증명 모음’ 형식의 Azure 리소스를 나타내는 개체의 확장 정보를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Recovery Services 자격 증명 모음에 대한 경고를 받습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | 자격 증명 모음 가져오기 작업에서는 '자격 증명 모음' 형식의 Azure 리소스를 나타내는 개체를 가져옵니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | 작업 결과 가져오기 작업을 사용하여 비동기적으로 제출된 작업에 대한 작업 상태와 결과를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | 컨테이너 가져오기 작업을 사용하여 리소스에 대해 등록된 컨테이너를 가져올 수 있습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | 경고 설정을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | 이벤트를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | 패브릭을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | 네트워크를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 네트워크 매핑을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | 보호 컨테이너를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 보호 가능한 항목을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 보호된 항목을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 복제 복구 지점을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 보호 컨테이너 매핑을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services 공급자를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | 스토리지 분류를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 스토리지 분류 매핑을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | vCenter를 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | 작업을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | 정책을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | 복구 계획을 읽습니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationVaultSettings/read | 모든 항목을 읽습니다.  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Recovery Services 자격 증명 모음에 대한 사용 세부 정보를 반환합니다. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | 자격 증명 모음 토큰 작업을 사용하여 자격 증명 모음 수준의 백 엔드 작업에 대한 자격 증명 모음 토큰을 가져올 수 있습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationVaultSettings/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>지원 요청 참가자

지원 요청을 만들고 관리할 수 있습니다. [자세한 정보](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>태그 기여자

엔터티의 태그를 관리할 수 있으며, 엔터티 자체에 대한 액세스 권한은 없습니다. [자세히 알아보기](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | 리소스 그룹에 대한 리소스를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | 구독 리소스를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>기타


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins 데이터 소유자

Digital Twins 데이터 평면에 대한 모든 권한 역할 [자세한 정보](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | 모든 이벤트 경로 읽기, 삭제, 만들기 또는 업데이트 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | 모든 디지털 트윈 읽기, 만들기, 업데이트 또는 삭제 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | 디지털 트윈에서 명령 호출 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | 디지털 트윈 관계 읽기, 만들기, 업데이트 또는 삭제 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | 모델 읽기, 만들기, 업데이트 또는 삭제 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | 모든 Digital Twins 그래프 쿼리 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins 데이터 읽기 권한자

Digital Twins 데이터 평면 속성에 대한 읽기 전용 역할 [자세한 정보](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | 디지털 트윈 읽기 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | 디지털 트윈 관계 읽기 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | 이벤트 경로 읽기 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | 모델 읽기 |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | 모든 Digital Twins 그래프 쿼리 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk 참가자

BizTalk Services를 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk 서비스 만들기 및 관리 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>데스크톱 가상화 애플리케이션 그룹 기여자

데스크톱 가상화 애플리케이션 그룹의 기여자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | hostpools 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | hostpools/sessionhosts 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>데스크톱 가상화 애플리케이션 그룹 읽기 권한자

데스크톱 가상화 애플리케이션 그룹의 읽기 권한자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | applicationgroups 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | hostpools 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | hostpools/sessionhosts 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | 배포를 가져오거나 나열합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>데스크톱 가상화 기여자

데스크톱 가상화의 기여자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>데스크톱 가상화 호스트 풀 기여자

데스크톱 가상화 호스트 풀의 기여자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>데스크톱 가상화 호스트 풀 읽기 권한자

데스크톱 가상화 호스트 풀의 읽기 권한자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | hostpools 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | 배포를 가져오거나 나열합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>데스크톱 가상화 읽기 권한자

데스크톱 가상화의 읽기 권한자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | 배포를 가져오거나 나열합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>데스크톱 가상화 세션 호스트 운영자

데스크톱 가상화 세션 호스트의 운영자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | hostpools 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>데스크톱 가상화 사용자

사용자가 애플리케이션 그룹의 애플리케이션을 사용할 수 있도록 허용합니다. [자세히 알아보기](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | *없음* |  |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | ApplicationGroup 사용 |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>데스크톱 가상화 사용자 세션 운영자

데스크톱 가상화 사용자 세션의 운영자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | hostpools 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | hostpools/sessionhosts 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>데스크톱 가상화 작업 영역 기여자

데스크톱 가상화 작업 영역의 기여자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | applicationgroups 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>데스크톱 가상화 작업 영역 읽기 권한자

데스크톱 가상화 작업 영역의 읽기 권한자입니다. [자세히 알아보기](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | 작업 영역 읽기 |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | applicationgroups 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | 배포를 가져오거나 나열합니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | 클래식 메트릭 경고를 읽습니다. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>디스크 백업 읽기 권한자

디스크 백업을 수행하려면 백업 자격 증명 모음에 대한 권한을 제공합니다. [자세히 알아보기](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | 디스크의 속성을 가져옵니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Blob 액세스에 대한 디스크의 SAS URI를 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-pool-operator"></a>Disk Pool 연산자

디스크 풀에 추가 된 디스크를 관리할 수 있도록 StoragePool 리소스 공급자에 대 한 권한을 제공 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | 새 디스크를 만들거나 기존 디스크를 업데이트합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | 디스크의 속성을 가져옵니다. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the StoragePool Resource Provider to manage Disks added to a Disk Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/60fc6e62-5479-42d4-8bf4-67625fcc2840",
  "name": "60fc6e62-5479-42d4-8bf4-67625fcc2840",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Pool Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>디스크 복원 운영자

디스크 복원을 수행하려면 백업 자격 증명 모음에 대한 권한을 제공합니다. [자세히 알아보기](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | 새 디스크를 만들거나 기존 디스크를 업데이트합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | 디스크의 속성을 가져옵니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>디스크 스냅샷 기여자

디스크 스냅샷을 관리하려면 백업 자격 증명 모음에 대한 권한을 제공합니다. [자세히 알아보기](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | 스냅샷을 삭제합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | 새 스냅샷을 만들거나 기존 스냅샷을 업데이트합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | 스냅샷의 속성을 가져옵니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | Blob 액세스에 대한 스냅샷의 SAS URI를 가져옵니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | 스냅샷의 SAS URI를 취소합니다. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Blob 액세스에 대한 디스크의 SAS URI를 가져옵니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | 지정된 스토리지 계정에 대한 액세스 키를 반환합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | 지정된 매개 변수를 사용하여 스토리지 계정을 만들거나, 속성 또는 태그를 업데이트하거나, 지정된 스토리지 계정의 사용자 지정 도메인을 추가합니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | 스토리지 계정의 목록을 반환하거나 지정된 스토리지 계정의 속성을 가져옵니다. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | 기존 스토리지 계정을 삭제합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Scheduler 작업 컬렉션 참가자

Scheduler 작업 컬렉션을 관리할 수 있지만 액세스할 수는 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | 클래식 메트릭 경고를 만들고 관리합니다. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | 지정된 범위의 모든 리소스에 대한 가용성 상태를 가져옵니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | 스케줄러 작업 컬렉션 만들기 및 관리 |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | 지원 티켓을 만들거나 업데이트합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>서비스 허브 운영자

서비스 허브 운영자를 사용하면 서비스 허브 커넥터와 관련된 모든 읽기, 쓰기, 삭제 작업을 수행할 수 있습니다. [자세히 알아보기](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | 동작 | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | 역할 및 역할 할당 읽기 |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | 리소스 그룹을 가져오거나 나열합니다. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | 배포를 만들고 관리합니다. |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | 서비스 허브 커넥터를 만들거나 업데이트합니다. |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | 서비스 허브 커넥터를 보거나 나열합니다. |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | 서비스 허브 커넥터를 삭제합니다. |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | 지정된 서비스 허브 작업 영역에 대한 평가 자격을 나열합니다. |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | 지정된 서비스 허브 작업 영역에 대한 지원 제공 자격을 확인합니다. |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | 지정된 사용자에 대한 서비스 허브 작업 영역을 나열합니다. |
> | **NotActions** |  |
> | *없음* |  |
> | **DataActions** |  |
> | *없음* |  |
> | **NotDataActions** |  |
> | *없음* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)
- [Azure 사용자 지정 역할](custom-roles.md)
- [Azure Security Center의 권한](../security-center/security-center-permissions.md)
