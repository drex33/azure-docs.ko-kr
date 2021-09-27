---
title: BareMetal Infrastructure에서 Oracle용 SnapCenter 구성
description: BareMetal Infrastructure에서 Oracle용 SnapCenter을 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: edc75bd8d5fb2a64e22b2dd8f53fceda092161c3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676108"
---
# <a name="configure-snapcenter-for-oracle-on-baremetal-infrastructure"></a>BareMetal Infrastructure에서 Oracle용 SnapCenter 구성

이 문서에서는 BareMetal Infrastructure에서 Oracle을 실행하도록 NetApp SnapCenter를 구성하는 단계를 안내합니다.

## <a name="add-storage-hosts-to-snapcenter"></a>SnapCenter에 스토리지 호스트 추가

먼저 SnapCenter에 스토리지 호스트를 추가하겠습니다. 

SnapCenter 세션을 시작하고 보안 예외를 저장하면 애플리케이션이 시작됩니다. VM(가상 머신)에서 Active Directory 자격 증명을 사용하여 SnapCenter에 로그인합니다.

https://\<hostname\>:8146/

이제 프로덕션 스토리지 위치와 보조 스토리지 위치를 추가할 준비가 되었습니다.

### <a name="add-the-production-storage-location"></a>프로덕션 스토리지 위치 추가

프로덕션 SVM(스토리지 가상 머신)을 추가하려면 다음을 수행합니다.

1. 왼쪽 메뉴에서 **스토리지 시스템** 을 선택하고 **+ 새로 만들기** 를 선택합니다.

2. 다음과 같이 **스토리지 시스템 추가** 정보를 입력합니다.

      - 스토리지 시스템: Microsoft Operations에서 제공한 SVM IP 주소를 입력합니다.
      - 만든 사용자 이름을 입력합니다. 기본값은 **snap center** 입니다.
      - Microsoft Operations에서 [REST를 사용하여 고객 암호를 수정](set-up-snapcenter-to-route-traffic.md#steps-to-integrate-snapcenter)할 때 만든 암호를 입력합니다.
      - **실패한 작업에 대한 자동 지원 알림을 스토리지 시스템에 보내기** 를 선택하지 않은 상태로 둡니다.
      - **syslog에 SnapCenter 이벤트 기록** 을 선택합니다.

3. **제출** 을 선택합니다.

    스토리지가 확인된 후에는 추가된 스토리지 시스템의 IP 주소가 입력된 사용자 이름과 함께 표시됩니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/oracle-baremetal-snapcenter-add-production-storage-complete.png" alt-text="추가된 스토리지 시스템의 IP 주소를 보여주는 스크린샷":::

    기본값은 테넌트당 SVM 하나입니다. 고객에게 여러 테넌트가 있는 경우 SnapCenter에서 모든 SVM을 구성하는 것이 좋습니다.

### <a name="add-secondary-storage-location"></a>보조 스토리지 위치 추가

DR(재해 복구) 스토리지 위치를 추가하려면 기본 위치와 DR 위치 둘 다에서 고객 구독을 피어링해야 합니다. 지원이 필요하면 Microsoft Operations에 문의하세요.

보조 스토리지 위치를 추가하는 방법은 기본 스토리지 위치를 추가하는 것과 비슷합니다. 그러나 기본 위치와 DR 위치가 피어링되면 보조 위치에서 스토리지를 ping하여 보조 스토리지 위치에 대한 액세스를 확인해야 합니다. 

>[!NOTE]
>ping이 실패하는 경우 일반적으로 클라이언트 VLAN(가상 LAN)의 호스트에 기본 경로가 없기 때문입니다.

ping이 확인되면 기본 스토리지를 추가할 때 사용한 단계를 이번에는 DR 호스트의 DR 위치에 대해서만 반복합니다. 두 위치에서 동일한 인증서를 사용하는 것이 좋지만, 반드시 그래야 하는 것은 아닙니다.

## <a name="set-up-oracle-hosts"></a>Oracle 호스트 설정

>[!NOTE]
>이 프로세스는 프로덕션 또는 재해 복구와 같은 위치에 관계없이 모든 Oracle 호스트에 적용됩니다.

1. SnapCenter에 호스트를 추가하고 SnapCenter 플러그 인을 설치하려면 먼저 JAVA 1.8을 설치해야 합니다. 각 호스트에 Java 1.8이 설치되어 있는지 확인한 후 계속합니다.

2. 각 Oracle RAC(Real Application Clusters) 호스트에서 루트가 아닌 동일한 사용자를 만들고 /etc/sudoers에 추가합니다. 새 암호를 입력해야 합니다.

3. 사용자가 만들어지면 명시적 권한 세트를 사용하여 해당 사용자를 /etc/sudoers에 추가해야 합니다. 이러한 권한은 SnapCenter VM에서 C:\ProgramData\NetApp\SnapCenter\Package 리포지토리로 이동한 후 oracle\_checksum 파일을 열어서 찾을 수 있습니다.

4. sudo 패키지에 따라 적절한 명령 세트를 복사합니다. 이때 LINUXUSER를 새로 만든 사용자 이름으로 바꿉니다. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/netapp-snapcenter-oracle-checksum-details.png" alt-text="oracle_checksum 파일 세부 정보의 스크린샷":::

    sudo 패키지 1.8.7 이상에 대한 명령 세트가 /etc/sudoers에 복사됩니다.

5. 사용자가 sudoers에 추가되면 SnapCenter가 **설정** > **자격 증명** >  **새로 만들기** 를 선택합니다.

6. 다음과 같이 자격 증명 정보를 입력합니다.

    - **자격 증명 이름**: 사용자 이름 및 sudoers를 식별하는 이름을 입력합니다.
    - **인증**: Linux
    - **사용자 이름**: 새로 만든 사용자 이름을 입력합니다.
    - **암호**: \<enter password\>
    - **sudo 권한 사용** 확인란을 선택합니다.
    
7. **확인** 을 선택합니다.

8. 왼쪽 탐색 창에서 **호스트** 를 선택하고 **+ 추가** 를 선택합니다.

9. **호스트 추가** 에 다음 값을 입력합니다.

    - **호스트 유형**: Linux
    - **호스트 이름**: 기본 RAC 호스트의 FQDN 또는 기본 RAC 호스트의 IP 주소를 입력합니다.
    - **자격 증명**: 드롭다운을 선택하고 sudoers에 대해 새로 만든 자격 증명을 선택합니다.
    - **Oracle Database** 확인란을 선택합니다.

    >[!NOTE]
    >실제 Oracle 호스트 IP 주소 중 하나를 입력해야 합니다. 노드 VIP 또는 검색 IP를 입력하는 것은 지원되지 않습니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-details.png" alt-text="새 호스트의 세부 정보를 보여주는 스크린샷":::
    
10. **추가 옵션** 을 선택합니다. **Oracle RAC의 모든 호스트 추가** 를 선택합니다. **저장** 을 선택하고 **제출** 을 선택합니다.

11. 플러그 인 설치 프로그램은 제공된 IP 주소와 통신을 시도합니다. 통신이 성공하면 **확인 및 제출** 을 선택하여 제공된 Oracle RAC 호스트의 ID를 확인합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-confirm-fingerprint.png" alt-text="새 호스트 지문을 보여주는 스크린샷":::

12. 초기 Oracle RAC가 확인되면 SnapCenter는 클러스터의 일부로 다른 모든 Oracle RAC 서버와 통신하고 확인하려고 시도합니다. **다른 서버 확인 및 제출** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-fingerprint-authentication.png" alt-text="지문 인증을 보여주는 스크린샷":::

    SnapCenter가 선택한 Oracle 플러그 인을 설치하고 있다고 알려주는 관리 호스트에 대한 상태 화면이 나타납니다. 아무 Oracle RAC 호스트의 /opt/NetApp/snapcenter/scc/logs/DISCOVER-PLUGINS\_0.log에 있는 로그를 확인하여 설치 진행 상황을 확인할 수 있습니다. 

    플러그 인이 성공적으로 설치되면 다음 화면이 표시됩니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-installed-plugins.png" alt-text="설치된 모든 SnapCenter 플러그 인을 보여주는 스크린샷":::

## <a name="add-credentials-for-oracle-recovery-manager"></a>Oracle Recovery Manager에 대한 자격 증명 추가

Oracle RMAN(Recovery Manager) 카탈로그 인증 방법은 RMAN 카탈로그 데이터베이스에 대해 인증합니다. 외부 카탈로그 메커니즘을 구성하고 데이터베이스를 데이터베이스 카탈로그에 등록한 경우 RMAN 카탈로그 인증을 추가해야 합니다. RMAN 자격 증명은 언제든지 추가할 수 있습니다.

1. SnapCenter의 왼쪽 메뉴에서 **설정** 을 선택하고 **자격 증명** 을 선택합니다. 오른쪽 위 모서리에서 **새로 만들기** 를 선택합니다.

2. **자격 증명 이름** 을 입력하여 SnapCenter에 저장된 RMAN 자격 증명을 호출합니다. **인증** 드롭다운에서 **Oracle RMAN 인증용 카탈로그** 를 선택합니다. 사용자 이름 및 암호를 입력합니다. **확인** 을 선택합니다.

3. 자격 증명이 추가되면 SnapCenter 내에서 데이터베이스 설정을 수정해야 합니다. 리소스에서 데이터베이스를 선택합니다. 주 창의 오른쪽 위 모서리에서 **데이터베이스 설정** 을 선택합니다.

4. 데이터베이스 설정 화면에서 **데이터베이스 구성** 을 선택합니다.

5. **RMAN 카탈로그 설정 구성** 을 확장합니다. 이 데이터베이스에 대해 이전에 설정한 **기존 자격 증명 사용** 드롭다운을 선택하고 적절한 옵션을 선택합니다. 이 개별 데이터베이스의 TNS 이름을 추가합니다. **확인** 을 선택합니다.

    >[!NOTE]
    >고유한 사용자 이름/암호 조합마다 이전 단계에서 서로 다른 자격 증명을 만들어야 합니다. 원하는 경우 SnapCenter에서 모든 데이터베이스에 대해 단일 자격 증명 세트를 허용합니다.
    >
    >RMAN 자격 증명이 데이터베이스에 추가되었더라도 (백업 정책에 따라) 보호 정책 만들기에 대한 다음 섹션에서 설명하는 대로 각 보호 정책에서 Oracle RMAN(Recovery Manager)을 사용하여 카탈로그 백업을 선택하지 않으면 RMAN이 카탈로그화되지 않습니다.

## <a name="create-protection-policies"></a>보호 정책 만들기

호스트가 SnapCenter에 성공적으로 추가되었으면 보호 정책을 만들 준비가 된 것입니다. 

왼쪽 메뉴에서 **리소스** 를 선택합니다. RAC 호스트에 있는 것으로 확인된 모든 데이터베이스가 SnapCenter에 표시됩니다. bn6p1X 및 그리드의 단일 인스턴스 유형은 보호 스키마의 일부로 무시됩니다. 이 시점에서는 모든 항목의 상태가 "보호 안 됨"입니다.

[개요](netapp-snapcenter-integration-oracle-baremetal.md#oracle-disaster-recovery-architecture)에 설명된 대로 각 파일 형식에는 15분마다 보관 로그 백업을 수행하여 약 15분의 로컬 RPO를 사용하도록 설정하는 서로 다른 스냅샷 주기가 있습니다. 그 후 데이터 파일이 더 긴 간격(예: 1시간마다 또는 더 긴 시간마다)으로 백업됩니다. 따라서 두 가지 정책이 만들어집니다.

RAC 데이터베이스가 확인되면 데이터 파일 및 컨트롤 파일에 대한 정책과 보관 로그에 대한 정책을 포함하여 여러 가지 보호 정책이 만들어집니다.

### <a name="datafiles-protection-policy"></a>데이터 파일 보호 정책

다음 단계에 따라 데이터 파일 보호 정책을 만듭니다.

1. SnapCenter의 왼쪽 메뉴에서 **설정** 을 선택한 다음, 맨 위에 있는 메뉴에서 **정책** 을 선택합니다. **새로 만들기** 를 선택합니다.

2. **데이터 파일 및 컨트롤 파일** 의 라디오 단추를 선택하고 아래로 스크롤합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy.png" alt-text="새 데이터베이스 백업 정책에 대한 세부 정보를 보여주는 스크린샷":::

3. **매시간** 라디오 단추를 선택합니다. 카탈로그를 위해 RMAN과의 통합이 필요하고 RMAN 자격 증명이 이미 추가된 경우에는 **Oracle RMAN(Recovery Manager)을 사용하여 카탈로그 백업** 확인란을 선택합니다. **다음** 을 선택합니다. 카탈로그 백업을 선택하는 경우 [반드시 RMAN 자격 증명을 추가](#add-credentials-for-oracle-recovery-manager)해야만 카탈로그가 작성됩니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-backup-policy-frequency.png" alt-text="백업 정책에서 일정 빈도를 선택하는 옵션을 보여주는 스크린샷":::

    >[!IMPORTANT]
    >매시간 또는 매일 이외의 백업 빈도가 필요한 경우에도 일정 정책을 선택해야 합니다. 실제 백업 빈도는 프로세스에서 나중에 설정합니다. 이 정책의 모든 백업이 주문형인 경우를 제외하고 "없음"을 선택하지 마세요.

4. 왼쪽 메뉴에서 **보존** 을 선택합니다. 백업 정책에 대해 설정하는 두 가지 유형의 보존 설정이 있습니다. 첫 번째 보존 설정은 보존할 최대 주문형 스냅샷 수입니다. 주문형 스냅샷 48개를 14일 동안 보존하는 다음 예제처럼 백업 정책에 따라 설정된 수의 스냅샷이 보존됩니다. 백업 정책에 따라 적절한 주문형 보존 정책을 설정합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy-retention.png" alt-text="데이터베이스 백업 정책 보존 설정을 보여주는 스크린샷":::

5. 다음 보존 설정 세트는 매시간, 매일, 매주 등의 이전 항목을 기준으로 보존하도록 예약된 스냅샷 수입니다. **다음** 을 선택합니다.

6. **복제** 화면에서 **로컬 스냅샷 복사본을 만든 후 SnapMirror 업데이트** 확인란을 선택하고 **다음** 을 선택합니다. 다른 항목은 기본값으로 둡니다.

    >[!NOTE]
    >SnapVault는 현재 Oracle BareMetal Infrastructure 환경에서 지원되지 않습니다.

    나중에 보호 정책 화면으로 돌아간 후 왼쪽 메뉴에서 **보호 정책 수정** > **복제** 를 선택하여 복제를 추가할 수 있습니다.

7. **스크립트** 페이지에서는 Oracle 백업이 발생하기 전 또는 후에 실행하는 데 필요한 스크립트를 입력합니다. 현재 SnapCenter 프로세스에 포함된 스크립트는 지원되지 않습니다. **다음** 을 선택합니다.

8. 왼쪽 메뉴에서 **확인** 을 선택합니다. 스냅샷의 복구 가능성 무결성을 확인하는 기능을 원하는 경우 매시간 옆에 있는 확인란을 선택합니다. 확인 스크립트 명령은 현재 지원되지 않습니다. **다음** 을 선택합니다.

    >[!NOTE]
    >확인 프로세스의 실제 위치 및 일정은 뒤에 나오는 리소스에 보호 정책 할당 섹션에서 추가합니다.

9. **요약** 페이지에서 모든 설정이 예상대로 입력되었는지 확인하고 **마침** 을 선택합니다.

### <a name="archive-logs-protection-policy"></a>보관 로그 보호 정책

위의 단계를 다시 수행하여 보관 로그 보호 정책을 만듭니다. 2단계에서 "데이터 파일 및 컨트롤 파일" 대신 **보관 로그** 에 대한 라디오 단추를 선택합니다.

## <a name="assign-protection-policies-to-resources"></a>리소스에 보호 정책 할당

정책을 만든 후에는 SnapCenter가 해당 정책 내에서 실행될 수 있도록 정책을 리소스와 연결해야 합니다.

### <a name="datafiles-resource-group"></a>데이터 파일 리소스 그룹

1. 왼쪽 메뉴에서 **리소스** 를 선택한 다음, 주 창의 오른쪽 위 모서리에서 **새 리소스 그룹** 을 선택합니다.

2. 검색 가능성을 높일 수 있도록 이 리소스 그룹의 **이름** 과 태그를 추가합니다.

    >[!NOTE]
    >**스냅샷 복사에 사용자 지정 이름 형식 사용** 을 선택하고 **$ResourceGroup**, **$Policy** 및 **$ScheduleType** 항목을 추가하는 것이 좋습니다. 이렇게 하면 스냅샷 접두사가 SnapCenter 표준을 충족하고 스냅샷 이름을 보면 필요한 세부 정보를 한눈에 알 수 있습니다. **스냅샷 복사에 사용자 지정 이름 형식 사용** 을 선택하지 않은 경우 **이름** 에 배치되는 항목은 생성된 스냅샷의 접두사가 됩니다. 입력한 이름이 데이터베이스 및 보호되는 항목(예: 데이터 파일 및 컨트롤 파일)을 식별해야 합니다. 

3. 백업 설정에서 **/u95** 를 추가하여 보관 로그를 제외합니다.

4. 리소스 페이지에서 동일한 백업 보호 정책으로 보호되는 모든 데이터베이스를 "사용 가능한 리소스"에서 "선택한 리소스"로 이동합니다. 각 호스트에 대한 Oracle 데이터베이스 인스턴스를 추가하지 말고 데이터베이스만 추가합니다. **다음** 을 선택합니다.

5. 앞에서 만든 데이터 파일 및 컨트롤 파일에 대한 보호 정책을 선택합니다. 정책을 선택한 후에는 일정 구성에서 연필 아이콘을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-datafiles-resource-group-available-policies.png" alt-text="일정을 구성하기 위해 보호 정책을 선택하는 것을 보여주는 스크린샷":::

6. 선택한 정책을 실행할 일정을 추가합니다. 시작 날짜는 현재 날짜와 현재 시간 이후여야 합니다.

    >[!NOTE]
    >예약된 빈도는 정책을 만들 때 선택한 빈도와 일치하지 않아도 됩니다. 매시간의 경우 "1시간마다 반복"을 그대로 두는 것이 좋습니다. 시작 시간은 백업이 발생하는 주기의 시작 시간으로 작동합니다. 지정된 시간에 하나의 백업만 활성화할 수 있으므로 데이터 파일을 보호하는 일정이 보관 로그를 보호하는 일정과 겹치면 안 됩니다.

7. 생성된 스냅샷을 사용할 수 있는지 확인합니다. 확인 프로세스는 매우 광범위하며 모든 Oracle 데이터베이스 볼륨의 클론 만들기, 주 호스트에 데이터베이스 탑재 및 복구 가능성 확인이 포함됩니다. 이 프로세스에는 시간과 리소스가 많이 소요됩니다. 확인 일정을 구성하려면 **+** 기호를 선택합니다.

    >[!NOTE]
    >확인 프로세스는 상당히 긴 시간 동안 호스트의 리소스를 점유합니다. 사용 가능한 보조 위치가 있는 경우 보조 위치의 호스트에서 확인을 추가하는 것이 좋습니다.
    
    다음 스크린샷에서는 복제를 사용하도록 설정되지 않은 새 리소스 그룹의 확인과 생성될 때 복제된 스냅샷을 보여줍니다. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-verification-schedules.png" alt-text="리소스 그룹의 확인 일정을 구성하는 방법을 보여주는 스크린샷":::

    복제를 사용하고 재해 복구 위치의 호스트가 확인에 사용되는 경우 다음 하위 섹션의 2단계로 건너뜁니다. 이 단계를 통해 보조 로케이터를 로드하여 보조 위치에서 백업을 확인할 수 있습니다.

### <a name="add-verification-schedule-for-new-resource-group"></a>새 리소스 그룹의 확인 일정 추가

1. **백업 후 확인 실행** 또는 **예약된 확인 실행** 을 선택하고, 드롭다운에서 미리 예약된 빈도를 선택합니다. DR을 사용하도록 설정한 경우 **보조 스토리지 위치에서 확인** 을 선택하여 DR 위치에서 주기를 실행하여 프로덕션 환경의 리소스 부담을 줄일 수 있습니다. **확인** 을 선택합니다.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-add-verification-schedules.png" alt-text="확인 일정을 추가하는 방법을 보여주는 스크린샷":::

    확인이 필요하지 않거나 이미 로컬로 설정된 경우 아래의 SMTP 설정(5단계)으로 건너뜁니다.

2. 데이터 파일 보호 정책을 만들 때 SnapMirror 업데이트를 사용하도록 설정했고 보조 스토리지 위치를 추가한 경우 SnapCenter는 두 위치 간의 복제를 탐지합니다. 이 경우 보조 로케이터를 로드하여 보조 위치에서 백업을 확인할 수 있는 화면이 나타납니다. **로케이터 로드** 를 선택합니다.

3. 로케이터 로드를 선택하면 SnapCenter는 데이터 파일 및 컨트롤 파일을 포함하고 있는 것으로 확인된 SnapMirror 관계를 표시합니다. 이러한 관계는 Microsoft Operations에서 제공하는 재해 복구 프레임워크와 일치해야 합니다. 일정 구성 아래에서 연필 아이콘을 선택합니다.

4. **보조 스토리지 위치에서 확인** 확인란을 선택합니다. 

5. SMTP 서버를 사용할 수 있는 경우 SnapCenter는 SnapCenter 관리자에게 이메일을 보낼 수 있습니다. 이메일을 보내려면 다음 정보를 입력합니다.

    - **이메일 기본 설정**: 받는 이메일의 주기에 대한 기본 설정을 입력합니다.
    - **보낸 사람**: SnapCenter에서 이메일을 보내는 데 사용할 이메일 주소를 입력합니다.
    - **받는 사람**: SnapCenter에서 보내는 이메일을 받을 이메일 주소를 입력합니다.
    - **제목**: SnapCenter에서 이메일을 보낼 때 사용할 제목을 입력합니다.
    - **작업 보고서 첨부** 확인란을 선택합니다.
    - **다음** 을 선택합니다.

6. 요약 페이지에서 설정을 확인하고 **마침** 을 선택합니다.

리소스 그룹을 만든 후 리소스 그룹을 식별하려면 다음을 수행합니다.

1. 왼쪽 메뉴에서 **리소스** 를 선택합니다.
2. 보기 옆에 있는 주 창에서 드롭다운 메뉴를 선택하고 **리소스 그룹** 을 선택합니다.
 
    이 리소스 그룹이 첫 번째 리소스 그룹인 경우 데이터 파일 및 컨트롤 파일에 대해 새로 만든 리소스 그룹만 표시됩니다. 보관 로그 리소스 그룹을 만든 경우 이 리소스 그룹도 표시됩니다.

### <a name="archive-log-resource-group"></a>보관 로그 리소스 그룹

보관 로그 리소스 그룹에 보호 정책을 할당하려면 데이터 파일 리소스 그룹에 보호 정책을 할당할 때 수행한 것과 동일한 단계를 수행합니다. 차이점은 다음과 같습니다.

- 3단계에서 **/u95** 를 추가하여 보관 로그를 제외하지 말고, 이 필드를 비워 둡니다.
- 6단계에서 15분마다 보관 로그를 백업하는 것이 좋습니다.
- 보관 로그의 경우 확인 탭이 비어 있습니다. 

## <a name="next-steps"></a>다음 단계

SnapCenter에서 Oracle Database의 주문형 백업을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [SnapCenter에서 주문형 백업 만들기](create-on-demand-backup-oracle-baremetal.md)
