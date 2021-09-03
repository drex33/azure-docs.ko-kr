---
title: Data Dynamics StorageX를 사용하여 파일 데이터를 분석하고 Azure로 마이그레이션
titleSuffix: Azure Storage
description: Data Dynamics StorageX를 구현하기 위한 시작 가이드입니다. 이 가이드에서는 데이터를 Azure Files, Azure NetApp Files, Azure Blob Storage 또는 사용 가능한 모든 ISV NAS 솔루션으로 마이그레이션하는 방법을 보여 줍니다.
author: dukicn
ms.author: nikoduki
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 8613d641f305f93598ac070c5eaaeee5fed81b8d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290237"
---
# <a name="migrate-data-to-azure-with-data-dynamics-storagex"></a>Data Dynamics StorageX를 사용하여 Azure로 데이터 마이그레이션

이 문서는 Microsoft Azure에서 Data Dynamics StorageX를 배포하는 데 도움이 됩니다. StorageX의 작동 방식과 관련된 주요 개념, 배포 필수 조건, 설치 프로세스, 운영 지침을 위한 방법을 소개합니다. 자세한 내용은 [Data Dynamics 고객 포털](https://www.datdynsupport.com/) 참조하세요.

Data Dynamics StorageX는 이기종 스토리지 환경에서 데이터를 분석, 관리, 이동할 수 있는 통합된 비정형 데이터 관리 플랫폼입니다. 기본 기능은 다음과 같습니다.
- 데이터 이동 기능
    - NAS 간(분석 유무에 관계없음)
    - NAS-개체 복제
    - 개체-NAS 보관
- 중복 파일 검사 보고서
- 공개 공유 보고서
- 사용자 지정 태그를 사용한 파일 메타데이터 분석
  
StorageX에서 더 많은 기능을 찾고 [비교 테이블](./migration-tools-comparison.md)에서 유사한 도구와 비교할 수 있습니다.

이 문서에서는 세 가지 주요 항목을 다룹니다.
- Azure 환경에서 StorageX 제품을 배포하는 프로세스,
- StorageX에 파일 및 클라우드 스토리지 리소스를 추가하는 기본 단계,
- 데이터 이동 정책 만들기.

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 StorageX의 온-프레미스에서 Azure로 배포와 Azure 내 배포를 위한 참조 아키텍처를 제공합니다.

:::image type="content" source="./media/storagex-quick-start-guide/storagex-architecture.png" alt-text="StorageX 구현을 위한 참조 아키텍처를 보여 주는 다이어그램":::

## <a name="storagex-interoperability-matrix"></a>StorageX 상호 운용성 매트릭스

| 기능 | SMB | NFS | 참고 |
| ------- |--- | --- | --- |
| **마이그레이션** | SMB 간 | NFSv3 간 | 멀티 프로토콜에 대해 결합할 수 있음 |
| **파일-개체 보관** | SMB-Azure Blob Storage | NFSv3-Azure Blob Storage | 데이터를 보관하여 비용 최적화 |
| **파일-개체 복제** | SMB-Azure Blob Storage | NFSv3-Azure Blob Storage | 데이터를 복제하여 보호 강화 |

## <a name="before-you-begin"></a>시작하기 전에

사전 계획을 통해 마이그레이션을 간소화하고 위험을 줄입니다. 마이그레이션을 간소화하는 몇 가지 팁은 다음과 같습니다.

- 원본 및 대상 서비스에서 데이터를 수집합니다.
- 연결 지점과 필요한 모든 네트워크 포트의 목록이 있는지 확인합니다.
- Azure의 StorageX 서버는 Active Directory 인프라에 포함되어야 합니다. Active Directory에 서버를 추가할 수 있는 Active Directory 권한이 있는 관리자에게 문의해야 합니다.
- 더 쉽게 구현하려면 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview)를 사용합니다.

### <a name="get-started-with-azure"></a>Azure 시작

Microsoft는 Azure를 시작하기 위해 수행할 수 있는 프레임워크를 제공합니다. CAF([클라우드 채택 프레임워크](/azure/architecture/cloud-adoption/))는 프로덕션 등급 클라우드 도입을 계획하기 위한 엔터프라이즈 디지털 변환 및 포괄적인 가이드에 대한 자세한 접근 방식입니다. CAF에는 빠르고 안전하게 시작 및 실행하는 데 도움이 되는 단계별 [Azure 설치 가이드](/azure/cloud-adoption-framework/ready/azure-setup-guide/)가 포함되어 있습니다. [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)에서 대화형 버전을 찾을 수 있습니다. 샘플 아키텍처, 애플리케이션 배포에 대한 특정 모범 사례 및 Azure 전문 지식을 얻을 수 있는 무료 교육 리소스를 찾을 수 있습니다.

### <a name="considerations-for-migrations"></a>마이그레이션 고려 사항

Azure로 파일 데이터의 마이그레이션을 고려할 때 몇 가지 중요한 측면이 있습니다. 계속하기 전에 자세히 알아보세요.

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [마이그레이션 도구 비교 매트릭스](./migration-tools-comparison.md)에서 Data Dynamics StorageX가 지원하는 최신 기능

프로덕션 애플리케이션에 영향을 주지 않고 마이그레이션을 지원하기에 충분한 네트워크 용량이 필요합니다. 이 섹션에서는 네트워크 요구 사항을 평가하는 데 사용할 수 있는 도구와 기술에 대해 간략하게 설명합니다.

#### <a name="determine-unutilized-internet-bandwidth"></a>사용되지 않은 인터넷 대역폭 확인

일별로 사용할 수 있는, 일반적으로 사용되지 않은 대역폭(또는 *여유 대역폭*)의 양을 파악하는 것이 중요합니다. 이를 통해 다음에 대한 목표를 충족할 수 있는지 여부를 평가할 수 있습니다.

- 오프라인 방법에 Azure Data Box를 사용하지 않는 경우 마이그레이션의 초기 시간
- 대상 파일 서비스로 최종 전환하기 전에 증분 다시 동기화를 수행하는 데 필요한 시간

Azure가 사용할 수 있는 여유 대역폭을 확인하려면 다음 방법을 사용합니다.

- 기존 Azure ExpressRoute 고객인 경우 Azure Portal에서 [회로 사용량](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)을 확인합니다.
- ISP에 연락하여 기존 일별 및 월별 사용률을 보여 주는 보고서를 요청합니다.
- 라우터/스위치 수준에서 네트워크 트래픽을 모니터링하여 사용률을 측정할 수 있는 몇 가지 도구가 있습니다.
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

#### <a name="creating-a-storagex-service-account"></a>StorageX 서비스 계정 만들기

SMB 마이그레이션의 경우 StorageX 서비스를 실행하는 서비스 계정을 사용하는 것이 좋습니다. 서비스 계정을 사용하면 지원을 위해 액세스가 추적될 수 있지만 모든 권한을 부여하기 위한 적절한 권한이 제공됩니다.

StorageX Universal Data Engine에서 데이터 이동 정책을 실행하는 프로세스는 StorageX 서비스 계정을 사용합니다. 해당 계정에는 Active Directory 도메인의 모든 필요한 권한이 할당되어야 합니다.

환경에서 허용되는 경우 도메인 관리자와 같은 계정을 만들 수 있습니다. NAS 서버에서 직접 권한을 부여하는 것이 좋습니다. 두 번째 작업을 수행하려면 Administrators 및 Backup Operators 그룹에 서비스 계정을 배치합니다.

StorageX 서비스 계정에는 다음 권한이 필요합니다.

- SeServiceLogonRight
- SeSecurityPrivilege 
- SeBackupPrivilege 
- SeRestorePrivilege 
- SeAuditPrivilege
- SeInteractiveLogonRight

NFS 액세스를 사용하려면 StorageX 서버의 IP에 대한 내보내기 규칙이 필요하며 RW 및 루트에 대한 액세스 권한을 부여합니다. 예를 들면 “root = 10.2.3.12, rw = 10.2.3.12”입니다. 여기서 10.2.3.12는 StorageX 서버 IP 주소입니다.

## <a name="deployment-guide"></a>배포 가이드

이 섹션에서는 Azure 환경에 StorageX 서버와 기타 StorageX 구성 요소를 배포하는 방법에 관한 지침을 제공합니다.

구현이 시작되기 전에 여러 가지 필수 조건이 중요합니다.

- 대상 스토리지 서비스를 식별하고 만들어야 합니다.
- 스토리지 서비스에 액세스하기 위한 적절한 포트가 열려 있는지 확인합니다. Microsoft의 [portqryui](https://www.microsoft.com/download/details.aspx?id=24009) 도구를 사용하여 필요한 포트가 열려 있는지 확인할 수 있습니다.

여기에서 자세히 알아볼 수 있습니다.

- [Azure 파일 공유](../../../files/storage-how-to-create-file-share.md)를 만드는 방법
- Azure NetApp Files에서 [SMB 볼륨](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) 또는 [NFS 내보내기](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md)를 만드는 방법

SMB 마이그레이션에서는 리소스를 추가하기 전에 Active Directory를 올바르게 설정해야 합니다. 마이그레이션 대상용 새 볼륨과 함께 기존 Azure NetApp Files 구현을 사용할 예정입니다. 새 Azure NetApp Files 볼륨을 만들기 전에 다음을 수행해야 합니다.

- [Azure NetApp Files 계정 만들기](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-netapp-account)
- [용량 풀 만들기](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#set-up-a-capacity-pool)
- [Active Directory 도메인에 Azure NetApp Files 계정 조인](../../../../azure-netapp-files/create-active-directory-connections.md)

Azure NetApp Files의 구성이 완료되면 마이그레이션에 사용할 공유를 만들 수 있습니다. 

1. 기존 Azure NetApp Files 계정으로 이동하고 **볼륨** 을 선택합니다. **볼륨 추가** 를 선택합니다. 이름을 지정하고 용량 풀과 네트워킹 설정을 선택합니다.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-1.png" alt-text="새 Azure NetApp Files 볼륨을 추가하는 스크린샷":::

1. **SMB** 프로토콜을 선택하고, 기존 Active Directory 연결을 선택하고, **공유 이름** 을 제공합니다.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-2.png" alt-text="Azure NetApp Files 볼륨의 SMB 속성 스크린샷":::

1. 태그를 사용하는 경우 **태그** 를 추가하고 공유를 **만듭니다**.

    볼륨이 만들어지면 StorageX 배포를 계속 진행할 수 있습니다.

1.  Azure에서 Data Dynamics 데이터 이동 및 모바일 제품을 배포합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview)를 사용하는 것이 좋습니다. 배포된 서버를 Active Directory 도메인에 추가합니다.
1.  배포된 서버에서 필요한 모든 네트워크 포트가 열려 있는지 확인합니다.
1.  StorageX 설치 스크립트를 실행합니다. 설치 스크립트는 Azure에서 배포된 서버에 로그인할 때 바탕 화면에서 사용할 수 있습니다. 
    모든 설치 파일과 출력 로그는 **C:\ProgramData\data Dynamics\StorageX** 폴더에 있습니다. 해당 폴더 내에서 **InstallationFiles** 하위 폴더에는 압축된 원본 설치 파일이 포함되지만 **SilentInstaller-DD** 폴더에는 구성 XML 파일과 설치 로그가 포함됩니다.

    기본적으로 설치 스크립트는 다음 서비스를 배포합니다.

    - StorageX
    - StorageX API
    - StorageX Processing Engine
    - StorageX Universal Data Engine
    - StorageX File Recovery
    - Elasticsearch
    - Microsoft SQL Express
    - Microsoft SQL Management Studio
    - Kibana
    - 모든 지원 소프트웨어(예: Java)

    설치 스크립트에는 이러한 다양한 서비스를 사용자 지정할 수 있는 XML 구성 파일이 있지만 구성은 수동으로 수정하지 않는 것이 좋습니다.

    설치하려면 바탕 화면에서 **StorageXPOCSilentInstaller - 바로 가기** 를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행** 을 선택합니다. 설치가 시작된 후 StorageX 서비스 계정과 해당 계정의 암호를 묻는 메시지가 표시됩니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-1.jpg" alt-text="설치 CLI의 스크린샷":::

    설치 스크립트는 모든 서비스를 계속 설치합니다. 설치 프로세스가 완료되면 명령줄 인터페이스에 메시지가 표시됩니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-2.jpg" alt-text="완료된 설치를 보여 주는 스크린샷":::

    StorageX가 자동으로 시작됩니다.

## <a name="migration-guide"></a>마이그레이션 가이드

설치가 완료되고 모든 서비스가 시작되면 마이그레이션 프로세스를 시작할 수 있습니다.

1.  StorageX 콘솔을 엽니다.
1.  **스토리지 리소스** 탭을 클릭합니다.
1.  **내 리소스** 보기에서 StorageX의 **스토리지 리소스** 라는 스토리지 서비스 또는 NAS 디바이스를 추가합니다. 다른 유형을 추가할 수 있습니다.
    - **파일 스토리지 리소스 추가** - 온-프레미스 NAS 디바이스, Windows 파일 서버, Azure NetApp Files 또는 일반 파일 서버
    - **개체 스토리지 리소스 추가** - S3 또는 Azure Blob Storage(Azure Files 서비스 포함)
    - **DFS 네임스페이스 추가** - DFS 파일 서버

    **파일 스토리지 리소스 추가** 를 선택하여 Azure NetApp Files 마이그레이션 대상을 추가하는 마법사를 시작합니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-1.png" alt-text="StorageX 배포에 새 리소스를 추가하는 스크린샷":::

1. CIFS 및 SMB 마이그레이션에 대해 **일반 CIFS** 를 선택합니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-2.png" alt-text="StorageX 배포에 일반 CIFS 리소스를 추가하는 스크린샷":::

1. Azure NetApp Files 컴퓨터 계정을 찾아봅니다. Azure NetApp Files 컴퓨터 계정은 Active Directory 도메인 추가될 때 생성되었습니다.
   
    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-3.png" alt-text="Azure NetApp Files 컴퓨터 계정을 추가하는 스크린샷":::

    마법사를 **완료** 하고 계정이 추가되었고 만든 공유가 포함되어 있는지 확인합니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-4.png" alt-text="추가된 리소스를 검색하는 스크린샷":::

    모든 원본과 대상을 별도의 리소스로 추가해야 합니다. 예제에서는 Azure NetApp Files 공유를 추가하는 작업을 보여 주며 이 작업은 다른 서비스에서는 약간 다를 수 있습니다.

1.  **데이터 이동** 탭을 클릭합니다.

1.  트리 창에서 새 정책을 만들 폴더를 마우스 오른쪽 단추로 클릭하고 **단계별 마이그레이션 정책** 을 선택합니다.

1.  마법사의 단계에 따라 단계별 마이그레이션 정책을 만듭니다. 적절한 **원본** 및 **대상** 의 대상을 선택합니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-1.png" alt-text="적절한 원본 및 대상의 대상을 선택하는 스크린샷":::

1. 정책의 이름과 설명을 제공하고 마법사를 **완료** 합니다.

1.  새 정책을 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택하여 테스트 마이그레이션을 실행합니다. 원본 리소스의 여러 파일에 대한 보안 구성을 확인하여 필요한 권한이 제공되었는지 확인하는 것이 좋습니다.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-2.png" alt-text="정의된 정책을 실행하는 스크린샷":::

1. 정책의 상태를 확인하여 초기 마이그레이션이 성공적으로 실행되었는지 확인합니다. 

## <a name="support"></a>지원

문제가 발생하는 경우 Microsoft와 Data Dynamics에서 일반 지원 채널을 사용하여 도움을 제공할 수 있습니다. 인프라 문제나 Azure 서비스에 관해서는 Microsoft에 문의하세요. Data Dynamics StorageX 설치와 관련된 문제는 Data Dynamics에 문의하세요.

### <a name="how-to-open-a-case-with-azure"></a>Azure와 관련된 사례를 여는 방법

[Azure Portal](https://portal.azure.com)의 맨 위에 있는 검색 표시줄에서 지원을 검색합니다. **도움말 + 지원** -> **새 지원 요청** 을 선택합니다.

### <a name="how-to-open-a-case-with-data-dynamics"></a>Data Dynamics와 관련된 사례를 여는 방법

[Data Dynamics 지원 포털](https://www.datdynsupport.com/)로 이동합니다. 등록하지 않은 경우 메일 주소를 제공하면 지원 팀이 계정을 만듭니다. 로그인한 후 사용자 요청을 엽니다. Azure 지원 사례를 이미 연 경우 요청을 만들 때 지원 요청 번호를 기록해 둡니다.

## <a name="next-steps"></a>다음 단계

자세히 알아볼 수 있는 다양한 리소스가 제공됩니다.

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [마이그레이션 도구 비교 매트릭스](./migration-tools-comparison.md)에서 Data Dynamics StorageX가 지원하는 기능
- [Data Dynamics](https://www.datadynamicsinc.com/)
- [Data Dynamics 고객 포털](https://www.datdynsupport.com/)에 있는 StorageX에 관한 전체 설명서