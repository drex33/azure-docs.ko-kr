---
title: Azure Storage 마이그레이션 도구 비교 - 비정형 데이터
description: 비정형 데이터의 마이그레이션에 사용되는 도구 간의 기본 기능 및 비교
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 08/04/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: d266f059869bb0f25df10dcc4fad317d3d3da7c3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426709"
---
# <a name="comparison-matrix"></a>비교 행렬

다음 비교 행렬에서는 비정형 데이터의 마이그레이션에 사용할 수 있는 다양한 도구의 기본 기능을 보여 줍니다. 

## <a name="supported-azure-services"></a>지원되는 Azure 서비스

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **솔루션 이름**  | [Azure 파일 동기화](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [데이터 이동성 및 마이그레이션](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [인텔리전트 데이터 관리](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files 지원(모든 계층)** | 예                          | 예                      | 예            | 예                            |
| **Azure NetApp Files 지원**      | 아니요                           | 예                      | 예            | 예                            |
| **Azure Blob 핫/쿨 지원**   | No                           | 예(NFS 미리 보기를 통해)    | 예            | 예                            |
| **Azure Blob 보관 계층 지원** | 아니요                           | 예                       | 아니요             | 예(마이그레이션 대상으로) |
| **Azure Data Lake Storage 지원** | 아니요                           | 예                       | 예             | 아니요                             |
| **지원되는 원본**      | Windows Server 2012 R2 이상 | NAS 및 클라우드 파일 시스템 | 모든 NAS 및 S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>지원되는 프로토콜(원본/대상)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **솔루션 이름**   | [Azure 파일 동기화](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [데이터 이동성 및 마이그레이션](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [인텔리전트 데이터 관리](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | 예 | 예 | 예 | 예 |
| **SMB 3.0**       | 예 | 예 | 예 | 예 |
| **SMB 3.1**       | 예 | 예 | 예 | 예 |
| **NFS v3**        | 아니요  | 예 | 예 | 예 |
| **NFS v4.1**      | 아니요  | 예 | 예  | 예 |
| **Blob REST API** | 아니요  | 예  | 예 | 예 |
| **S3**            | 아니요  | 예 | 예 | 예 |

## <a name="extended-features"></a>확장된 기능

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **솔루션 이름**  | [Azure 파일 동기화](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [데이터 이동성 및 마이그레이션](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [인텔리전트 데이터 관리](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID/SID 다시 매핑**                   | 아니요  | 예                        | 예 | 아니요                             |
| **프로토콜 ACL 다시 매핑**                | 아니요  | 예                         | 예  | 아니요                             |
| **DFS 지원**                           | 예 | 예                        | 예 | 예                            |
| **제한 지원**                    | 예 | 예                        | 예 | 예                            |
| **파일 패턴 제외**               | 아니요  | 예                        | 예 | 예(복사 기능 사용) |
| **선택적 파일 특성에 대한 지원** | 예 | 예                        | 예 | 예(확장된 특성의 경우)  |
| **전파 삭제**                   | 예 | 예                        | 예 | 예                            |
| **NTFS 분기 동기화 팔로우**                 | 아니요  | 예                        | 예  | 예                            |
| **SMB 소유자 및 그룹 소유자 재정의**    | 예 | 예                        | 예 | 아니요                             |
| **관리 연속성 보고**            | 아니요  | 예                        | 예  | 예                            |
| **대체 데이터 스트림 지원**    | 아니요  | 예                        | 예 | 아니요                             |
| **마이그레이션 예약**              | 아니요  | 예                        | 예 | 예                            |
| **ACL 유지**                        | 예  | 예                        | 예 | 예                            |
| **DACL 지원**                          | 예 | 예                        | 예 | 예                            |
| **SACL 지원**                          | 예 | 예                        | 예 | 아니요                             |
| **액세스 시간 유지**                | 예 | 예                        | 예 | 예                            |
| **수정된 시간 유지**              | 예 | 예                        | 예 | 예                            |
| **만든 시간 유지**              | 예  | 예                        | 예 | 예                            |
| **Azure Data Box 지원**       | 예 | 예                        | 예  | 아니요                             |
| **스냅샷의 마이그레이션**                | 예  | 설명서                     | 예 | 아니요                             |
| **바로 가기 링크 지원**                 | 아니요  | 예                        | 예  | 예                            |
| **하드 링크 지원**                     | 예  | 별도 파일로 마이그레이션 | 예 | 예                            |
| **열기/잠금 파일 지원**       | 예 | 예                        | 예 | 예                            |
| **증분 마이그레이션**                 | 예 | 예                        | 예 | 예                            |
| **전환 지원**                    | 아니요  | 예                        | 예 | 아니요(수동 전용)               |
| **[기타 기능](#other-features)**         | [링크](#azure-file-sync)| [링크](#datadobi-dobimigrate) | [링크](#data-dynamics-data-mobility-and-migration) | [링크](#komprise-intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>평가 및 보고

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **솔루션 이름**   | [Azure 파일 동기화](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [데이터 이동성 및 마이그레이션](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [인텔리전트 데이터 관리](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **용량**                        | 아니요      | 예 | 예 | 예            |
| **파일/폴더의 #**            | 아니요      | 예 | 예 | 예            |
| **시간에 따른 연령 분포**      | 아니요      | 예 | 예 | 예            |
| **액세스 시간**                     | 아니요      | 예 | 예 | 예            |
| **수정된 시간**                   | 아니요      | 예 | 예 | 예            |
| **만든 시간**                   | 아니요      | 예 | 예 | 예            |
| **파일/개체 보고서 상태별** | 부분 | 예 | 예 | 예            |

## <a name="licensing"></a>라이선싱

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **솔루션 이름**   | [Azure 파일 동기화](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [데이터 이동성 및 마이그레이션](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [인텔리전트 데이터 관리](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | 해당 없음 | 예 | 예 | 예 |
| **Azure 약정** | 예   | 예 | 예 | 예 |

## <a name="other-features"></a>기타 기능

### <a name="azure-file-sync"></a>Azure 파일 동기화

- 내부 해시 유효성 검사

> [!TIP]
> Azure 파일 동기화은 다양 한 Azure 파일 공유의 온-프레미스 캐싱/동기화를 위한 영구적인 하이브리드 솔루션입니다. 이 함수는 가동 중지 시간이 없는 클라우드 마이그레이션을 제공 합니다. Azure 파일 공유를 온-프레미스로 캐시 하지 않으려는 경우에는 Azure 파일 동기화 권장 되지 않는 마이그레이션 도구가 아닙니다. [Azure 파일 공유 마이그레이션 개요](../../../files/storage-files-migration-overview.md) 또는 이 문서에 설명된 다른 파트너 도구를 참조하세요.

### <a name="datadobi-dobimigrate"></a>Datadobi DobiMigrate

- 마이그레이션 사전 검사
- 마이그레이션 계획
- 이동 테스트를 위한 시험 실행
- 이동하기 전에 대상 측 사용자 활동을 검색하고 경고합니다.
- 정책 기반 마이그레이션
- 예약된 복사 반복
- 루트 디렉터리 보안을 처리하기 위한 구성 가능한 옵션
- 주문형 확인 실행
- 원본 및 대상에 대한 데이터 다시 읽기 확인
- 그래픽, 대화형 오류 처리 워크플로
- 삭제 및 업데이트와 같이 특정 작업의 전파를 제한하는 기능
- 원본에 대한 액세스 시간을 유지하는 기능(대상 외에)
- 마이그레이션 전환 중에 원본에 대한 롤백을 실행하는 기능
- 선택한 SMB 파일 특성을 마이그레이션하는 기능
- NTFS 보안 설명자를 정리하는 기능
- NFSv3 권한을 재정의하고 대상에 새 모드 비트를 쓰는 기능
- NFSv3 POSIX 초안 ACL을 NFSv4 ACL로 변환하는 기능
- SMB 1(CIFS)
- 지원 24 x 7 x 365

### <a name="data-dynamics-data-mobility-and-migration"></a>데이터 Dynamics Data 이동성 및 마이그레이션

- 해시 유효성 검사

### <a name="komprise-intelligent-data-management"></a>Komprise 인텔리전트 데이터 관리

- 프로젝트/디렉터리 기반 마이그레이션
- 실패 시 자동 다시 시도
- 평가/보고: 파일 형식, 파일 크기, 프로젝트 기반
- 평가/보고: 사용자 지정 메타데이터 기반 검색
- 보관, 복제, 분석을 위한 전체 데이터 수명 주기 관리 솔루션
- Blob, S3 데이터에 대한 시간 기반 분석 액세스
- 태그 지정
- 지원 24 x 7 x 365
- 해시 유효성 검사

*목록은 2021년 3월 31일에 마지막으로 확인되었습니다.*

## <a name="see-also"></a>추가 정보

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [데이터 전송에 사용할 Azure 솔루션 선택](../../../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Azure 파일 공유로 마이그레이션](../../../files/storage-files-migration-overview.md)
- [Azure용 WANdisco LiveData 플랫폼을 사용하여 Data Lake Storage로 마이그레이션](../../../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [AzCopy를 사용하여 Azure Storage로 데이터 복사 또는 이동](../../../common/storage-use-azcopy-v10.md)
- [AzReplicate를 사용하여 Azure Blob Storage로 대량 데이터 세트 마이그레이션(샘플 애플리케이션)](/samples/azure/azreplicate/azreplicate/)