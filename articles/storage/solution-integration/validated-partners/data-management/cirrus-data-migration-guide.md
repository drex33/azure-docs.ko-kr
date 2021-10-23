---
title: Cirrus Data를 사용하여 블록 데이터를 Azure로 마이그레이션
titleSuffix: Azure Storage
description: Cirrus Migrate Cloud를 구현하고 데이터를 Azure로 마이그레이션하는 빠른 시작 가이드를 제공합니다.
author: dukicn
ms.author: nikoduki
ms.date: 09/06/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: b9e56df2b4643fabdbdcf0d00baa6fe63c0d9e95
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263233"
---
# <a name="migrate-your-block-data-to-azure-with-cirrus-migrate-cloud"></a>Cirrus Migrate Cloud를 사용하여 블록 데이터를 Azure로 마이그레이션

CMC(Cirrus Migrate Cloud)를 사용하면 기존 스토리지 시스템 또는 클라우드에서 Azure로 디스크를 마이그레이션할 수 있습니다. 마이그레이션은 원래 시스템이 아직 작동 중인 동안 수행됩니다. 이 문서에서는 마이그레이션을 성공적으로 구성하고 실행하는 방법을 설명합니다.

## <a name="overview"></a>개요

솔루션은 직접 호스트-호스트 연결을 허용하는 모든 호스트에서 실행되는 분산 마이그레이션 에이전트를 사용합니다. 각 호스트-호스트 마이그레이션은 독립적이며 데이터 흐름에 대한 중앙 병목 현상 없이 솔루션의 확장성을 무한히 확장할 수 있습니다. 마이그레이션은 프로덕션에 영향을 미치지 않도록 cMotion™ 기술을 사용하고 있습니다. 

## <a name="use-cases"></a>사용 사례

이 문서에서는 애플리케이션을 한 가상 머신(온-프레미스 또는 다른 클라우드 공급자에서 실행)에서 Azure의 가상 머신으로 이동하는 일반적인 마이그레이션 사례를 다룹니다. 다양한 사용 사례에 대한 심층적인 단계별 가이드는 다음 링크에서 자세히 알아볼 수 있습니다.

- [cMotion을 사용하여 워크로드를 Azure로 이동](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-on-premises-to-azure-1xo3nuf/)
- [Premium 디스크에서 Ultra Disks](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-between-azure-tiers-sxhppt/)
- [AWS에서 Azure로 이동](https://support.cirrusdata.cloud/en/article/howto-cirrus-migrate-cloud-migration-from-aws-to-azure-weegd9/.)

## <a name="components"></a>구성 요소

Cirrus Migrate Cloud는 다음과 같은 여러 구성 요소로 구성됩니다.

- **cmC의 cMotion™** 기능은 원본 호스트에 대한 가동 중지 시간 없이 원본에서 대상 클라우드로 스토리지 수준 잘라내기를 수행합니다. cMotion™ 원래 FC 또는 iSCSI 원본 디스크에서 새 대상 Azure Managed Disk로 워크로드를 회전하는 데 사용됩니다.
- **웹 기반 관리 포털** 서비스로서의 웹 기반 관리입니다. 이를 통해 사용자는 마이그레이션을 관리하고 블록 스토리지를 보호할 수 있습니다. 웹 기반 관리 포털 모든 CMC 애플리케이션 구성, 관리 및 관리 작업에 대한 인터페이스를 제공합니다.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-web-portal.jpg" alt-text="CMC 포털 스크린샷":::

## <a name="implementation-guide"></a>구현 가이드

사용자는 Azure 모범 사례를 따라 새 가상 머신을 구현해야 합니다. 프로세스에 익숙하지 않은 경우 [빠른 시작 가이드](../../../../virtual-machines/windows/quick-create-portal.md)에서 자세히 알아보세요.

마이그레이션을 시작하기 전에 다음 필수 조건이 충족되었는지 확인합니다.

- Azure의 OS에 적절한 라이선스가 있는지 확인합니다.
- Azure Virtual Machine에 대한 액세스 확인
- Azure에서 애플리케이션/데이터베이스 라이선스를 실행할 수 있는지 확인합니다.
- 대상 디스크 크기를 자동으로 할당할 수 있는 권한을 확인합니다.
- 관리 디스크의 크기가 원본 디스크보다 크거나 같은지 확인합니다. 
- 원본 또는 대상 가상 머신에 H2H 연결을 허용하는 포트가 열려 있는지 확인합니다.

1. **Azure 가상 머신을 준비합니다.** 문서에서는 가상 머신이 완전히 구현되었다고 가정합니다. 따라서 데이터 디스크가 마이그레이션되면 대상 호스트가 애플리케이션을 즉시 시작하고 온라인으로 가져올 수 있습니다. 데이터의 상태는 몇 초 전에 종료되었을 때 원본과 동일합니다. CMC는 원본에서 대상으로 OS 디스크를 마이그레이션하지 않습니다.

1. **Azure 가상 머신 에서 애플리케이션을 준비합니다.** 이 예제에서 원본은 Linux 호스트입니다. 해당 BSD 스토리지에 액세스하는 모든 사용자 애플리케이션을 실행할 수 있습니다. 원본 스토리지 디바이스로 1 GiB 디스크를 사용하여 원본에서 실행되는 데이터베이스 애플리케이션을 사용합니다. 그러나 모든 애플리케이션을 대신 사용할 수 있습니다. 대상 가상 머신으로 사용할 준비가 된 Azure에서 가상 머신을 설정합니다. 리소스 구성 및 운영 체제가 애플리케이션과 호환되고 CMC 포털을 사용하여 원본에서 마이그레이션을 받을 준비가 되었는지 확인합니다. 대상 블록 스토리지 디바이스/s는 마이그레이션 프로세스 중에 자동으로 할당되고 만들어집니다.

1. **CMC 계정 에 등록합니다.** CMC 계정을 얻으려면 지원 페이지에 따라 계정을 가져오는 방법에 대한 정확한 지침을 확인할 수 있습니다. 자세한 내용은 여기에서 확인할 수 [있습니다.](https://support.cirrusdata.cloud/en/article/licensing-m4lhll/)

1. 특정 마이그레이션 특성, 유형, 마이그레이션 소유자 및 작업을 정의하는 데 필요한 세부 정보를 반영하는 마이그레이션 **Project 만듭니다.** 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-create-project.jpg" alt-text="새 프로젝트를 만드는 스크린샷":::

1. **마이그레이션 프로젝트 매개 변수 를 정의합니다.** CMC 웹 기반 포털을 사용하여 원본, 대상 및 기타 매개 변수와 같은 매개 변수를 정의하여 마이그레이션을 구성합니다.

1. **원본 및 대상 호스트에 마이그레이션 CMC 에이전트를 설치합니다.** CMC 웹 기반 관리 포털을 사용하여 **Cirrus Migrate Cloud 배포를** 선택하여 새 설치에 대한 curl 명령을 **얻습니다.** 소스 및 대상 명령줄 인터페이스에서 명령을 실행합니다.

1. **원본 호스트와 대상 호스트 간에 양방향 연결을 만듭니다.** CMC 웹 기반 관리 포털에서 **H2H** 탭을 사용하고 **새 연결 만들기** 단추를 사용합니다. Linux 운영 체제에서 사용하는 디바이스가 아니라 애플리케이션에서 사용하는 디바이스를 선택합니다.

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-1.jpg" alt-text="배포된 호스트 목록을 보여 주는 스크린샷":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-2.jpg" alt-text="호스트-호스트 연결 목록을 보여 주는 스크린샷":::

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-3.jpg" alt-text="마이그레이션된 디바이스 목록을 보여 주는 스크린샷":::

1. CMC 웹 기반 관리 포털에서 호스트 볼륨 마이그레이션을 사용하여 **대상 가상 머신으로 마이그레이션을** **시작합니다.** 원격 위치에 대한 지침을 따릅니다. CMC 포털을 사용하여 화면 오른쪽에 **있는 대상 볼륨을 자동으로 할당합니다.** 
 
1. 다음으로 CMC 포털의 **통합** 탭을 사용하여 연결 및 디스크 프로비저닝을 허용하도록 Azure 자격 증명을 추가해야 합니다. Azure에 대한 프라이빗 회사의 값( **통합 이름,** **테넌트 ID,** **클라이언트/애플리케이션** ID 및 **비밀)을** 사용하여 필수 필드를 입력합니다. **저장** 을 누릅니다. 

    :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-migration-4.jpg" alt-text="Azure 자격 증명 입력을 보여 주는 스크린샷":::

    Azure AD 애플리케이션을 만드는 방법에 대한 자세한 내용은 [단계별 지침을 확인하세요.](https://support.cirrusdata.cloud/en/article/creating-an-azure-service-account-for-cirrus-data-cloud-tw2c9n/) CMC용 Azure AD 애플리케이션을 만들고 등록하면 대상 가상 머신에서 Azure Managed Disks 자동으로 생성할 수 있습니다.

    >[!NOTE]
    >이전 단계에서 **대상 볼륨 자동 할당을** 선택했기 때문에 새 할당을 위해 대상 볼륨을 다시 누르지 마세요. 이렇게 하면 출력 및 오류가 발생합니다. 대신 **계속을 누릅니다.**

## <a name="migration-guide"></a>마이그레이션 가이드

이전 단계에서 **저장을** 누르면 **새 마이그레이션 세션** 창이 나타납니다. 다음 필드를 입력합니다.
   - **세션 설명:** 의미 있는 설명 제공
   - **자동 다시동기 간격:** 마이그레이션 일정 사용 
   - iQoS를 사용하여 마이그레이션이 프로덕션에 미치는 영향을 선택합니다.
     - **최소** 제한 마이그레이션 속도는 사용 가능한 대역폭의 25%입니다.
     - **보통은** 사용 가능한 대역폭의 50%로 마이그레이션 속도를 조정합니다.
     - **사용** 가능한 대역폭의 75%에 대한 적극적인 제한 마이그레이션 속도
     - **스트림은** 마이그레이션을 제한하지 않습니다.

       :::image type="content" source="./media/cirrus-data-migration-guide/cirrus-iqos.jpg" alt-text="iQoS 설정에 대한 옵션을 보여 주는 스크린샷":::

**세션 만들기를** 눌러 마이그레이션을 시작합니다.

마이그레이션 초기 동기화의 시작부터 cMotion이 시작될 때까지 사용자가 CMC와 상호 작용할 필요가 없습니다.  진행률 모니터링은 예외입니다. 대시보드를 사용하여 현재 상태, 세션 볼륨을 모니터링하고 변경 내용을 추적할 수 있습니다. 

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-1.jpg" alt-text="모니터링 진행 상황을 보여 주는 스크린샷":::

마이그레이션하는 동안 변경된 데이터 맵 눌러 원본 디바이스에서 블록이 변경된 것을 확인할 수 있습니다.  

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-2.jpg" alt-text="변경된 데이터 맵을 보여 주는 스크린샷":::

iQoS에 대한 세부 정보는 동기화된 블록 및 마이그레이션 상태를 표시합니다. 또한 프로덕션 IO에는 영향을 미치지 않습니다.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-3.jpg" alt-text="iQoS 세부 정보를 보여 주는 스크린샷":::

## <a name="moving-the-workload-to-azure-with-cmotion"></a>cMotion을 사용하여 워크로드를 Azure로 이동

초기 동기화가 완료되면 cMotion™ 사용하여 워크로드를 원본 디스크에서 대상 Azure Managed Disk로 이동할 준비를 합니다.

### <a name="start-cmotion"></a>cMotion을 시작합니다™

이 시점에서 시스템은 cMotion™ 마이그레이션을 중단할 준비가 완료되었습니다. 

1. CMS 포털에서 세션을 사용 하 여 **cMotion™ 트리거** 를 선택 하 여 원본에서 대상 디스크로 작업을 전환 합니다. 프로세스가 완료 되었는지 확인 하려면 iostat 또는 이와 동등한 명령을 사용할 수 있습니다. Azure 가상 컴퓨터의 터미널로 이동 하 고 *용 iostat/vv/<device_name>* (예:/stv/sc)을 실행 하 고 Azure 클라우드의 대상 디스크에서 응용 프로그램이 IOs를 작성 하는지 확인 합니다.

:::image type="content" source="./media/cirrus-data-migration-guide/cirrus-monitor-4.jpg" alt-text="현재 모니터링 상태를 보여 주는 스크린샷":::

이 상태에서는 작업을 swung 하거나 언제 든 지 원본 디스크로 다시 이동할 수 있습니다. 프로덕션 가상 머신을 되돌리려면 **세션 작업** 단추를 사용 하 고 **cmotion™ 되돌리기** 옵션을 선택 합니다. 원본 호스트/v m에서 응용 프로그램이 실행 되는 동안 원하는 횟수 만큼 전환할 수 있습니다.

대상 가상 머신으로 마지막으로 이동 해야 하는 경우 다음 단계를 수행 합니다.
1. **세션 작업** 선택
2. **종료 종료** 옵션을 클릭 하 여 새 Azure 가상 머신으로의 "잠금"을 클릭 하 고 원본 디스크를 제거 하는 옵션을 사용 하지 않도록 설정 합니다. 최종 호스트를 이동 하기 위해 원본 호스트에서 실행 중인 다른 응용 프로그램을 중지 합니다. 

### <a name="move-the-application-to-the-destination-virtual-machine"></a>대상 가상 머신으로 응용 프로그램 이동

이동이 완료 되 면 응용 프로그램을 새 가상 머신으로 전환 해야 합니다. 이렇게 하려면 다음 단계를 수행 합니다.

1. 응용 프로그램 중지
2. 마이그레이션된 장치를 분리 합니다.
3. Azure 가상 머신에서 마이그레이션된 새 장치를 탑재 합니다. 
4. 새 마이그레이션된 디스크의 Azure 가상 머신에서 동일한 응용 프로그램을 시작 합니다. 
 
원본 호스트에서 용 iostat 명령을 실행 하 여 원본 호스트 장치로 사용할 IOs가 없는지 확인 합니다. Azure 가상 컴퓨터에서 용 iostat를 실행 하면 IO가 Azure virtual machine 터미널에서 실행 중임을 볼 수 있습니다.

### <a name="complete-the-migration-session-in-cmc-gui"></a>CMC GUI의 마이그레이션 세션 완료 

CMotion™ 트리거한 후 모든 IOs가 대상 장치로 리디렉션되는 경우 마이그레이션 단계가 완료 됩니다. 이제 **세션 작업** 을 사용 하 여 세션을 닫을 수 있습니다. **세션 삭제** 를 클릭 하 여 마이그레이션 세션을 닫습니다. 마지막 단계로 원본 호스트와 Azure 가상 컴퓨터 모두에서 **Cirrus 마이그레이션 클라우드 에이전트가** 제거 됩니다. 제거를 수행 하려면 **Cirrus 마이그레이션 클라우드 배포** 단추에서 **제거 말아 넘기기 명령을** 가져옵니다. 옵션은 포털의 **호스트** 섹션에 있습니다. 

에이전트가 제거 되 면 마이그레이션이 완전히 완료 된 것입니다. 이제 원본 응용 프로그램은 로컬에 탑재 된 디스크를 사용 하 여 대상 Azure 가상 머신에서 프로덕션 환경에서 실행 됩니다.    

## <a name="support"></a>고객 지원팀

### <a name="how-to-open-a-case-with-azure"></a>Azure와 관련된 사례를 여는 방법

[Azure Portal](https://portal.azure.com)의 맨 위에 있는 검색 표시줄에서 지원을 검색합니다. **도움말 + 지원** -> **새 지원 요청** 을 선택합니다.

### <a name="engaging-cirrus-support"></a>Cirrus 지원

CMC 포털의 CMC 포털에서 **도움말 센터** 탭을 선택 하 여 Cirrus Data Solutions 지원에 문의 하거나 [cdsi 웹 사이트로](https://support.cirrusdata.cloud/en/)이동 하 여 지원 요청을 파일 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure virtual machines](../../../../virtual-machines/windows/overview.md) 에 대 한 자세한 정보
- [Azure Managed Disks](../../../../virtual-machines/managed-disks-overview.md) 에 대 한 자세한 정보
- [저장소 마이그레이션](../../../common/storage-migration-overview.md) 에 대 한 자세한 정보
- [Cirrus 데이터 웹 사이트](https://www.cirrusdata.com/)
- [Cmotion](https://support.cirrusdata.cloud/en/category/howtos-1un623w/) 에 대 한 단계별 가이드