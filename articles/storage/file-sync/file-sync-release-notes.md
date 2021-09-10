---
title: Azure 파일 동기화 에이전트에 대한 릴리스 정보 | Microsoft Docs
description: Azure Files에서 조직의 파일 공유를 중앙 집중화하는 데 사용할 수 있는 Azure 파일 동기화 에이전트의 릴리스 정보를 참조하세요.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 8/10/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: d159f8ad1840f3351fa31ec78ceead9cdfd7d9b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567739"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트에 대한 릴리스 정보
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Windows Server 설치는 Azure 파일 공유의 빠른 캐시로 변환됩니다. 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다(SMB, NFS 및 FTPS 포함). 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 파일 동기화 에이전트의 지원되는 버전에 대한 릴리스 정보를 제공합니다.

## <a name="supported-versions"></a>지원되는 버전
지원되는 Azure 파일 동기화 에이전트 버전은 다음과 같습니다.

| Milestone | 에이전트 버전 번호 | 릴리스 날짜 | 상태 |
|----|----------------------|--------------|------------------|
| V13 릴리스 - [KB4588753](https://support.microsoft.com/topic/632fb833-42ed-4e4d-8abd-746bd01c1064)| 13.0.0.0 | 2021년 7월 12일 | 지원 여부 |
| V12.1 릴리스-[KB4588751](https://support.microsoft.com/topic/497dc33c-d38b-42ca-8015-01c906b96132)| 12.1.0.0 | 2021년 5월 20일 | 지원 여부 |
| V12 릴리스 - [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 2021년 3월 26일 | 지원 여부 |
| V11.3 릴리스 - [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 2021년 4월 7일 | 지원 여부 |
| V11.2 릴리스 - [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2021년 2월 2일 | 지원 여부 |
| V11.1 릴리스 - [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 2020년 11월 4일 | 지원 여부 |

## <a name="unsupported-versions"></a>지원되지 않는 버전
다음 Azure 파일 동기화 에이전트 버전은 만료되었으며 더 이상 지원되지 않습니다.

| Milestone | 에이전트 버전 번호 | 릴리스 날짜 | 상태 |
|----|----------------------|--------------|------------------|
| V10 릴리스 | 10.0.0.0 - 10.1.0.0 | 해당 없음 | 미지원 - 2021년 6월 28일에 에이전트 버전 만료 |
| V9 릴리스 | 9.0.0.0 - 9.1.0.0 | 해당 없음 | 미지원 - 2021년 2월 16일에 에이전트 버전 만료 |
| V8 릴리스 | 8.0.0.0 | 해당 없음 | 미지원 - 2021년 1월 12일에 에이전트 버전 만료 |
| V7 릴리스 | 7.0.0.0 - 7.2.0.0 | 해당 없음 | 미지원 - 2020년 9월 1일에 에이전트 버전 만료 |
| V6 릴리스 | 6.0.0.0 - 6.3.0.0 | 해당 없음 | 미지원 - 2020년 4월 21일에 에이전트 버전 만료 |
| V5 릴리스 | 5.0.2.0 - 5.2.0.0 | 해당 없음 | 미지원 - 2020년 3월 18일에 에이전트 버전 만료 |
| V4 릴리스 | 4.0.1.0 - 4.3.0.0 | 해당 없음 | 미지원 - 2019년 11월 6일에 에이전트 버전 만료 |
| V3 릴리스 | 3.1.0.0 - 3.4.0.0 | 해당 없음 | 미지원 - 2019년 8월 19일에 에이전트 버전 만료 |
| GA 이전 에이전트 | 1.1.0.0 - 3.0.13.0 | 해당 없음 | 미지원 - 2018년 10월 1일에 에이전트 버전 만료 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-13000"></a>에이전트 버전 13.0.0.0
다음 릴리스 정보는 2021년 7월 12일 릴리스된 Azure 파일 동기화 에이전트의 버전 13.0.0.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제
- 신뢰할 수 있는 업로드  
    - 신뢰할 수 있는 업로드는 동기화 그룹에서 첫 번째 서버 엔드포인트를 만들 때 사용할 수 있는 새로운 모드입니다. 클라우드(Azure 파일 공유)에 일부/대부분의 데이터가 있지만. 오래되어 새 서버 엔드포인트에서 최신 데이터를 파악해야 하는 시나리오에 유용합니다. 예를 들어 DataBox와 같은 오프라인 마이그레이션 시나리오가 이 경우에 해당합니다. DataBox를 채워 Azure로 전송하면 로컬 서버의 사용자는 로컬 서버의 파일을 계속 변경/추가/삭제합니다. 그러면 DataBox의 데이터와 Azure 파일 공유가 약간 오래된 것이 될 수 있습니다. 신뢰할 수 있는 업로드를 사용하면 이제 서버와 클라우드에 이 사례를 해결하고 서버의 최신 변경 사항으로 클라우드를 원활하게 업데이트하는 방법을 알릴 수 있습니다. 

        데이터가 클라우드에 어떻게 전달되었는지와 관계없이 이 모드에서는 데이터가 서버의 일치하는 위치에서 비롯된 경우 Azure 파일 공유를 업데이트할 수 있습니다. 클라우드의 초기 사본을 받은 후 신뢰할 수 있는 업로드를 계속 받는 사이에는 대규모 디렉터리 재구성을 피해야 합니다. 그러면 업데이트만 전송됩니다. 디렉터리 이름을 변경하면 이름이 바뀐 디렉터리에 있는 모든 파일이 다시 업로드됩니다. 이 기능은 원본에 더 이상 존재하지 않는 대상의 파일 제거를 포함하여 RoboCopy /MIR = 원본에서 대상으로 미러링의 의미 체계와 비슷합니다. 
        
        신뢰할 수 있는 업로드는 스테이징 공유를 통해 Azure 파일 동기화와 DataBox 통합을 위한 “오프라인 데이터 전송” 기능을 대체합니다. 준비 공유에서는 더 이상 DataBox를 사용할 필요가 없습니다. AFS V13 에이전트를 통해 새 오프라인 데이터 전송 작업을 더 이상 시작할 수 없습니다. 에이전트 버전 13으로 업그레이드해도 서버의 기존 작업은 계속됩니다.

- 클라우드 변경 내용 열거 및 동기화 진행률을 볼 수 있는 포털 개선 사항  
    - 새 동기화 그룹을 만들 때 클라우드 변경 열거가 완료되면 연결된 서버 엔드포인트만 동기화를 시작할 수 있습니다.  이 동기화 그룹의 클라우드 엔드포인트(Azure 파일 공유)에 파일이 이미 있으면 클라우드에서 콘텐츠 열거를 변경하는 데 시간이 걸릴 수 있습니다. 네임스페이스에 더 많은 항목(파일 및 폴더)이 있으므로 이 프로세스가 더 오래 걸릴 수 있습니다. 이제 관리자는 Azure Portal에서 클라우드 변경 열거 진행률을 가져와서 서버에서 완료/동기화를 시작할 시간을 예상할 수 있습니다.

- 서버 이름 바꾸기 지원  
    - 등록된 서버의 이름이 바뀌면 이제 Azure 파일 동기화가 포털에 새 서버 이름을 표시합니다. v13 릴리스 이전에 서버 이름이 변경된 경우 이제 포털의 서버 이름이 올바른 서버 이름을 표시하도록 업데이트됩니다.

- Windows Server 2022 Preview에 대한 지원  
    - Azure 파일 동기화 에이전트는 이제 Windows Server 2022 Preview 빌드 20348 이상에서 지원됩니다.

    > [!Note]  
    > Windows Server 2022는 현재 Azure 파일 동기화에서 지원되지 않는 TLS 1.3에 대한 지원을 추가합니다. [TLS 설정](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)이 그룹 정책을 통해 관리되는 경우 TLS 1.2를 지원하도록 서버를 구성해야 합니다. 

- 기타 개선 사항
    - 동기화, 클라우드 계층화 및 클라우드 변경 열거를 위한 안정성 향상.
    - 서버에서 다수의 파일이 변경된 경우 이제 VSS 스냅샷에서 동기화 업로드가 수행되므로 항목별 오류 및 동기화 세션 실패를 줄입니다. 
    - Invoke-StorageSyncFileRecall cmdlet은 이제 파일이 서버 엔드포인트 위치 외부로 이동한 경우에도 서버 엔드포인트와 연결된 계층화된 모든 파일을 회수합니다. 
    - 이제 클라우드 계층화 마지막 액세스 시간 추적에서 Explorer.exe가 제외됩니다.
    - 클라우드 계층화가 사용으로 설정된 서버 엔드포인트를 제거한 후 분리된 계층화 파일 정리 진행률을 모니터링하는 새로운 원격 분석(이벤트 ID 6664).


### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](file-sync-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](file-sync-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](file-sync-deployment-guide.md)을 참조하세요.

- 에이전트 버전이 버전 12.0 미만인 경우 기존 Azure 파일 동기화 에이전트가 설치된 서버를 다시 시작해야 합니다.
- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 및 Windows Server 2022 Preview에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다. 자세한 내용은 [추천 시스템 리소스](file-sync-planning.md#recommended-system-resources)를 참조하세요.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](file-sync-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](file-sync-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정을 다른 리소스 그룹이나 구독 또는 Azure AD 테넌트로 이동할 수 있습니다. 스토리지 동기화 서비스 또는 스토리지 계정을 이동한 후에는 Microsoft.StorageSync 애플리케이션 액세스 권한을 스토리지 계정에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > 클라우드 엔드포인트를 만들 때 스토리지 동기화 서비스 및 스토리지 계정이 동일한 Azure AD 테넌트에 있어야 합니다. 클라우드 엔드포인트를 만든 후에는 스토리지 동기화 서비스 및 스토리지 계정을 다른 Azure AD 테넌트로 이동할 수 있습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-12100"></a>에이전트 버전 12.1.0.0
다음 릴리스 정보는 2021년 5월 20일 릴리스된 Azure 파일 동기화 에이전트의 버전 12.1.0.0에 대한 것입니다. 이러한 정보는 버전 12.0.0.0에 대해 나열된 릴리스 정보에 대한 추가 사항입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제 
V12.0 에이전트 릴리스에는 이 릴리스에서 수정된 두 가지 버그가 있습니다.
- 에이전트 자동 업데이트에서 에이전트를 최신 버전으로 업데이트하지 못했습니다.
- FileSyncErrorsReport.ps1 스크립트는 항목당 오류 목록을 제공하지 않습니다.

## <a name="agent-version-12000"></a>에이전트 버전 12.0.0.0
다음 릴리스 정보는 Azure 파일 동기화 에이전트(2021년 3월 26일 릴리스) 버전 12.0.0.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제
- 네트워크 액세스 정책 및 프라이빗 엔드포인트 연결을 구성하는 새로운 포털 환경
    - 이제 포털을 사용하여 스토리지 동기화 서비스 퍼블릭 엔드포인트에 대한 액세스를 사용하지 않도록 설정하고 프라이빗 엔드포인트 연결을 승인, 거부 및 제거할 수 있습니다. 네트워크 액세스 정책 및 프라이빗 엔드포인트 연결을 구성하려면 스토리지 동기화 서비스 포털을 열고 [설정] 섹션으로 이동한 다음, [네트워크]를 클릭합니다.
 
- 64KiB 보다 큰 볼륨 클러스터 크기에 대한 클라우드 계층화 지원
    - 클라우드 계층화는 Server 2019에서 최대 2MiB의 볼륨 클러스터 크기를 지원합니다. 자세히 알아보려면 [계층화할 파일의 최소 파일 크기는 얼마인가요?](./file-sync-choose-cloud-tiering-policies.md#minimum-file-size-for-a-file-to-tier)를 참조하세요.
 
- Azure 파일 동기화 서비스 및 스토리지 계정에 대한 대역폭 및 대기 시간 측정
    - Test-StorageSyncNetworkConnectivity cmdlet을 사용하여 Azure 파일 동기화 서비스 및 스토리지 계정에 대한 대기 시간 및 대역폭을 측정할 수 있습니다. Azure 파일 동기화 서비스 및 스토리지 계정에 대한 대기 시간은 cmdlet을 실행할 때 기본적으로 측정됩니다.  스토리지 계정에 대한 업로드 및 다운로드 대역폭은 "-MeasureBandwidth" 매개 변수를 사용할 때 측정됩니다.
 
        예를 들어 Azure 파일 동기화 서비스 및 스토리지 계정에 대한 대역폭 및 대기 시간을 측정하려면 다음 PowerShell 명령을 실행합니다.
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- 서버 엔드포인트 생성 실패에 대한 포털의 오류 메시지가 개선됨
    - 여러분의 피드백에 따라 서버 엔드포인트 생성이 실패하는 경우 오류 메시지와 지침이 개선되었습니다.
 
- 기타 성능 및 안정성 개선
    - Azure 파일 공유에서 변경된 파일을 검색하는 변경 내용 검색 성능이 향상되었습니다.
    - 조정 동기화 세션의 성능이 향상되었습니다. 
    - 동기화 기능이 향상되어 ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED 및 ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED 오류가 줄어듭니다.
    - 클라우드 계층화를 사용하도록 설정하고, Robocopy를 /B 매개 변수와 함께 사용하여 계층화된 파일이 복사되는 경우 데이터 손상을 유발하는 버그가 수정되었습니다.
    - 볼륨에서 데이터 중복 제거를 사용하는 경우 Server 2019에서 파일 계층화 실패를 유발할 수 있는 버그가 수정되었습니다.
    - 파일이 2GiB보다 큰 경우 AFSDiag가 파일 압축에 실패하도록 유발할 수 있는 버그가 수정되었습니다.

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](file-sync-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](file-sync-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](file-sync-deployment-guide.md)을 참조하세요.

- 기존 Azure 파일 동기화 에이전트 설치가 있는 서버는 다시 시작해야 합니다.
- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다. 자세한 내용은 [추천 시스템 리소스](file-sync-planning.md#recommended-system-resources)를 참조하세요.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](file-sync-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](file-sync-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정을 다른 리소스 그룹이나 구독 또는 Azure AD 테넌트로 이동할 수 있습니다. 스토리지 동기화 서비스 또는 스토리지 계정을 이동한 후에는 Microsoft.StorageSync 애플리케이션 액세스 권한을 스토리지 계정에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > 클라우드 엔드포인트를 만들 때 스토리지 동기화 서비스 및 스토리지 계정이 동일한 Azure AD 테넌트에 있어야 합니다. 클라우드 엔드포인트를 만든 후에는 스토리지 동기화 서비스 및 스토리지 계정을 다른 Azure AD 테넌트로 이동할 수 있습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-11300"></a>에이전트 버전 11.3.0.0
다음 릴리스 정보는 2021년 4월 7일에 릴리스된 Azure 파일 동기화 에이전트의 버전 11.3.0.0에 대한 것입니다. 이러한 정보는 버전 11.1.0.0에 대해 나열된 릴리스 정보에 대한 추가 사항입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제 
클라우드 계층화를 사용하도록 설정하고, Robocopy를 /B 매개 변수와 함께 사용하여 계층화된 파일이 복사되는 경우 데이터 손상을 유발하는 버그가 수정되었습니다.

## <a name="agent-version-11200"></a>에이전트 버전 11.2.0.0
다음 릴리스 정보는 2021년 2월 2일에 릴리스된 Azure 파일 동기화 에이전트의 버전 11.2.0.0에 대한 것입니다. 이러한 정보는 버전 11.1.0.0에 대해 나열된 릴리스 정보에 대한 추가 사항입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제 
- 항목별 오류 수가 많아서 동기화 세션이 취소된 경우 Azure 파일 동기화 서비스에서 항목별 오류를 수정하는 데 사용자 지정 동기화 세션이 필요하다고 확인되면 새 세션이 시작될 때 동기화가 조정될 수 있습니다.
- Register-AzStorageSyncServer cmdlet을 사용하여 서버를 등록하면 "처리되지 않은 예외" 오류로 인해 실패할 수 있습니다.
- 새로운 PowerShell cmdlet(Add-StorageSyncAllowedServerEndpointPath)은 서버에서 허용되는 서버 엔드포인트 경로를 구성합니다. 이 cmdlet은 Azure 파일 동기화 배포는 CSP(클라우드 솔루션 공급자) 또는 서비스 공급자가 관리하고, 고객은 서버에서 허용되는 서버 엔드포인트 경로를 구성하려고 하는 경우 유용합니다. 서버 엔드포인트를 만들 때 지정된 경로가 허용 목록에 없으면 서버 엔드포인트 만들기가 실패합니다. 이것은 선택적 기능이며 지원되는 모든 경로는 서버 엔드포인트를 만들 때 기본적으로 허용됩니다.  

    
    - 허용되는 서버 엔드포인트 경로를 추가하려면 서버에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - 지원되는 경로 목록을 가져오려면 다음 PowerShell 명령을 실행합니다.
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - 경로를 제거하려면 다음 PowerShell 명령을 실행합니다.
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>에이전트 버전 11.1.0.0
다음 릴리스 정보는 2020년 11월 4일에 릴리스된 Azure 파일 동기화 에이전트의 버전 11.1.0.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제
- 초기 다운로드 및 사전 회수를 제어하는 새로운 클라우드 계층화 모드
    - 초기 다운로드 모드: 파일을 처음에 새 서버 엔드포인트로 다운로드하는 방식을 선택할 수 있습니다. 마지막으로 수정된 타임스탬프를 기준으로 계층화된 모든 파일 또는 가능한 많은 파일을 서버에 다운로드할까요? 가능합니다! 클라우드 계층화를 사용할 수 없나요? 이제 시스템에서 계층화된 파일을 피하도록 선택할 수 있습니다. 자세한 내용은 Azure 파일 동기화 배포 설명서에서 [서버 엔드포인트 만들기](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) 섹션을 참조하세요.
    - 사전 회수 모드: 파일이 만들어지거나 수정될 때마다 동일한 동기화 그룹 내에서 지정한 서버로 사전 회수가 가능합니다. 이렇게 하면 지정한 각 서버에서 파일을 쉽게 사용할 수 있습니다. 전 세계에 있는 팀이 동일한 데이터로 작업하고 있나요? 사전 회수를 사용하도록 설정하면, 한 팀이 아침에 출근하면 다른 표준 시간대의 팀에서 업데이트한 모든 파일이 다운로드되어 있어서 바로 사용이 가능합니다! 자세한 내용은 Azure 파일 동기화 배포 설명서에서 [Azure 파일 공유에서 새 파일 및 변경된 파일 사전 회수](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) 섹션을 참조하세요.

- 클라우드 계층화 마지막 액세스 시간 추적에서 애플리케이션 제외. 이제 마지막 액세스 시간 추적에서 애플리케이션을 제외할 수 있습니다. 애플리케이션이 파일에 액세스하면 이 파일에 대한 마지막 액세스 시간이 클라우드 계층화 데이터베이스에서 업데이트됩니다. 파일 시스템을 검사하는 애플리케이션(예: 바이러스 백신) 때문에 모든 파일의 마지막 액세스 시간이 동일해져서 파일을 계층화할 때 영향을 줄 수 있습니다.

    마지막 액세스 시간 추적에서 애플리케이션을 제외하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync에 있는 HeatTrackingProcessNameExclusionList 레지스트리 설정에 해당 프로세스 이름을 추가합니다.

    예: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > 데이터 중복 제거 및 FSRM(파일 서버 리소스 관리자) 프로세스는 기본적으로 제외되며(하드 코딩됨) 프로세스 제외 목록은 5분마다 새로 고쳐집니다.

- 기타 성능 및 안정성 개선
    - Azure 파일 공유에서 변경된 파일을 검색하는 변경 내용 검색 성능이 향상되었습니다.
    - 동기화 업로드 성능이 향상되었습니다.
    - 이제 초기 업로드가 VSS 스냅샷에서 수행되기 때문에 항목별 오류 및 동기화 세션 오류가 줄어듭니다.
    - 특정 I/O 패턴에 대한 동기화 안정성이 향상되었습니다.
    - 장애 조치(failover)가 발생할 때 동기화 데이터베이스가 장애 조치(failover) 클러스터에서 시간을 되돌리지 못하도록 버그가 수정되었습니다.
    - 계층화된 파일에 액세스할 때 회수 성능이 향상되었습니다.

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](../file-sync/file-sync-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](../file-sync/file-sync-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](../file-sync/file-sync-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다. 자세한 내용은 [추천 시스템 리소스](../file-sync/file-sync-planning.md#recommended-system-resources)를 참조하세요.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정을 다른 리소스 그룹이나 구독 또는 Azure AD 테넌트로 이동할 수 있습니다. 스토리지 동기화 서비스 또는 스토리지 계정을 이동한 후에는 Microsoft.StorageSync 애플리케이션 액세스 권한을 스토리지 계정에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > 클라우드 엔드포인트를 만들 때 스토리지 동기화 서비스 및 스토리지 계정이 동일한 Azure AD 테넌트에 있어야 합니다. 클라우드 엔드포인트를 만든 후에는 스토리지 동기화 서비스 및 스토리지 계정을 다른 Azure AD 테넌트로 이동할 수 있습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
    > [!Warning]  
    > Robocopy /B 스위치가 Azure 파일 동기화에서 지원되지 않습니다. Azure 파일 동기화 서버 엔드포인트와 함께 Robocopy /B 스위치를 원본으로 사용하면 파일이 손상될 수 있습니다.