---
title: Microsoft Azure Recovery Services 자격 증명 모음 삭제
description: 이 문서에서는 종속성을 제거한 다음 Azure Backup Recovery Services 자격 증명 모음을 삭제하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/07/2021
ms.openlocfilehash: 0eb5e7c48bf15583e96a5bf30149ba01ee7c2e33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529225"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services 자격 증명 모음 삭제

이 문서에서는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 삭제하는 방법을 설명합니다. 종속성을 제거한 다음 자격 증명 모음을 삭제하는 방법에 대한 지침이 포함되어 있습니다.

## <a name="before-you-start"></a>시작하기 전에

다음과 같은 종속성이 있는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다.

- 보호된 데이터 원본(예: IaaS VM, SQL 데이터베이스, Azure 파일 공유)을 포함하는 자격 증명 모음은 삭제할 수 없습니다.
- 백업 데이터를 포함하는 자격 증명 모음은 삭제할 수 없습니다. 백업 데이터를 삭제하면 일시 삭제된 상태로 전환됩니다.
- 일시 삭제된 상태의 백업 데이터를 포함하는 자격 증명 모음은 삭제할 수 없습니다.
- 등록된 스토리지 계정이 있는 자격 증명 모음은 삭제할 수 없습니다.

종속성을 제거하지 않고 자격 증명 모음을 삭제하려고 하면 다음 오류 메시지 중 하나가 표시됩니다.

- 자격 증명 모음 내에 기존 리소스가 있으므로 자격 증명 모음을 삭제할 수 없습니다. 이 자격 증명 모음과 연결된 백업 항목, 보호된 서버 또는 백업 관리 서버가 없는지 확인하세요. 삭제를 진행하려면 이 자격 증명 모음과 연결된 다음 컨테이너의 등록을 취소합니다.

- 자격 증명 모음에 일시 삭제된 상태의 백업 항목이 있으므로 Recovery Services 자격 증명 모음을 삭제할 수 없습니다. 일시 삭제된 항목은 삭제 작업 14일 후에 영구적으로 삭제됩니다. 백업 항목이 영구적으로 삭제되고 자격 증명 모음에 일시 삭제된 상태의 항목이 없는 경우 자격 증명 모음 삭제를 시도해 보세요. 자세한 내용은 [Azure Backup에 대한 일시 삭제](./backup-azure-security-feature-cloud.md)를 참조하세요.

## <a name="proper-way-to-delete-a-vault"></a>자격 증명 모음을 삭제하는 적절한 방법

>[!WARNING]
>다음 작업은 파괴적이며 실행 취소할 수 없습니다. 보호된 서버와 연결된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제됩니다. 주의하여 진행하세요.

자격 증명 모음을 제대로 삭제하려면 다음 순서대로 단계를 수행해야 합니다.

- **1단계**: 일시 삭제 기능을 사용하지 않습니다. 일시 삭제를 사용하지 않는 단계는 [여기를 참조](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)하세요.

- **2단계**: 일시 삭제를 사용하지 않은 후 이전에 일시 삭제된 상태로 남아 있는 항목이 있는지 확인합니다. 일시 삭제된 상태의 항목이 있는 경우 ‘삭제 취소’하고 다시 ‘삭제’해야 합니다.  일시 삭제 항목을 찾아 영구적으로 삭제하려면 [다음 단계를 수행](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items)합니다.

- **3단계**: 보호된 항목이 있는지 확인하려면 다음 세 위치를 모두 선택해야 합니다.

  - **클라우드 보호된 항목**: 자격 증명 모음 대시보드 메뉴 > **백업 항목** 으로 이동합니다. **백업 중지** 또는 **백업 데이터 삭제** 를 사용하여 백업 데이터와 함께 여기에 나열된 모든 항목을 제거해야 합니다.  해당 항목을 제거하려면 [다음 단계를 수행](#delete-protected-items-in-the-cloud)합니다.
  - **SQL Server 인스턴스**: 자격 증명 모음 대시보드 메뉴 > **백업 인프라** > **보호된 서버** 로 이동합니다. 보호된 서버에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다. 보호된 서버를 마우스 오른쪽 단추로 클릭하고 **등록 취소** 를 선택합니다.
  - **MARS 보호된 서버**: 자격 증명 모음 대시보드 메뉴 > **백업 인프라** > **보호된 서버** 로 이동합니다. MARS 보호된 서버가 있는 경우 여기에 나열된 모든 항목은 해당 백업 데이터와 함께 삭제되어야 합니다. MARS 보호된 서버를 삭제하려면 [다음 단계를 수행](#delete-protected-items-on-premises)합니다.
  - **MABS 또는 DPM 관리 서버**: 자격 증명 모음 대시보드 메뉴 > **백업 인프라** > **백업 관리 서버** 로 이동합니다. DPM 또는 MABS(Azure Backup Server)를 사용하는 경우 여기에 나열된 모든 항목을 백업 데이터와 함께 삭제하거나 등록을 취소해야 합니다. 관리 서버를 삭제하려면 [다음 단계를 수행](#delete-protected-items-on-premises)합니다.

- **4단계**: 등록된 스토리지 계정이 모두 삭제되었는지 확인해야 합니다. 자격 증명 모음 대시보드 메뉴 > **백업 인프라** > **스토리지 계정** 으로 이동합니다. 여기에 나열된 스토리지 계정이 있는 경우 모두 등록을 취소해야 합니다. 계정 등록을 취소하는 방법을 알아보려면 [스토리지 계정 등록 취소](manage-afs-backup.md#unregister-a-storage-account)를 참조하세요.
- **5단계**: 자격 증명 모음에 대해 생성된 프라이빗 엔드포인트가 없는지 확인합니다. '설정'에서 자격 증명 모음 대시보드 메뉴 > **프라이빗 엔드포인트 연결** 로 이동한 다음, 자격 증명 모음에 프라이빗 엔드포인트 연결이 생성되거나 생성하려고 시도한 경우 자격 증명 모음 삭제를 진행하기 전에 제거되었는지 확인합니다. 

해당 단계를 완료하면 계속해서 [자격 증명 모음을 삭제](#delete-the-recovery-services-vault)할 수 있습니다.

종속성이 없는 **자격 증명 모음을 여전히 삭제할 수 없는 경우** [**ARM 클라이언트를 사용하여 자격 증명 모음 삭제**](#delete-the-recovery-services-vault-by-using-azure-resource-manager)에 나열된 단계를 수행합니다.

## <a name="delete-protected-items-in-the-cloud"></a>클라우드에서 보호된 항목 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해하려면 **[시작하기 전에](#before-you-start)** 섹션을 참조하세요.

보호를 중지하고 백업 데이터를 삭제하려면 다음 단계를 수행합니다.

1. 포털에서 **Recovery Services 자격 증명 모음** 으로 이동한 다음 **백업 항목** 으로 이동합니다. 그런 다음 **백업 관리 유형** 목록에서 클라우드의 보호된 항목을 선택합니다(예: Azure Virtual Machines, Azure Storage[Azure Files 서비스] 또는 Azure Virtual Machines의 SQL Server).

    ![백업 유형을 선택합니다.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 범주에 대한 모든 항목이 나열된 목록이 표시됩니다. 마우스 오른쪽 단추를 클릭하여 백업 항목을 선택합니다. 백업 항목이 보호되는지에 따라 메뉴에 **백업 중지** 창 또는 **백업 데이터 삭제** 창이 표시됩니다.

    - **백업 중지** 창이 표시되면 드롭다운 메뉴에서 **백업 데이터 삭제** 를 선택합니다. 백업 항목의 이름을 입력한 다음(이 필드는 대/소문자를 구분함) 드롭다운 메뉴에서 이유를 선택합니다. 설명(있는 경우)을 입력합니다. 그런 다음 **백업 중지** 를 선택합니다.

        ![백업 중지 창.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - **백업 데이터 삭제** 창이 나타나면 백업 항목의 이름을 입력한 다음(이 필드는 대/소문자를 구분함) 드롭다운 메뉴에서 이유를 선택합니다. 설명(있는 경우)을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

         ![백업 데이터 삭제 창.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   이 옵션은 예약된 백업을 삭제하고 주문형 백업도 삭제합니다.
3. **알림** 아이콘: ![알림 아이콘](./media/backup-azure-delete-vault/messages.png)을 선택합니다. 프로세스가 완료되면 서비스는 ‘“백업 항목”에 대한 백업 중지 및 백업 데이터 삭제’ 메시지를 표시합니다.  ‘작업이 성공적으로 완료되었습니다.’
4. **백업 항목** 메뉴에서 **새로 고침** 을 선택하여 백업 항목이 삭제되었는지 확인합니다.

      ![백업 항목 삭제 페이지.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>온-프레미스에서 보호된 항목 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해하려면 **[시작하기 전에](#before-you-start)** 섹션을 참조하세요.

1. 자격 증명 모음 대시보드 메뉴에서 **백업 인프라** 를 선택합니다.
2. 온-프레미스 시나리오에 따라 다음 옵션 중 하나를 선택합니다.

      - MARS의 경우 **보호된 서버** 를 선택한 다음 **Azure Backup Agent** 를 선택합니다. 그런 다음 삭제하려는 서버를 선택합니다.

        ![MARS의 경우 자격 증명 모음을 선택하여 해당 대시보드를 엽니다.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS 또는 DPM의 경우 **백업 관리 서버** 를 선택합니다. 그런 다음 삭제하려는 서버를 선택합니다.

          ![MABS 또는 DPM의 경우 자격 증명 모음을 선택하여 해당 대시보드를 엽니다.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **삭제** 창에 경고 메시지가 표시됩니다.

     ![삭제 창.](./media/backup-azure-delete-vault/delete-protected-server.png)

     동의 확인란에서 경고 메시지와 검토합니다.
    > [!NOTE]
    >
    >- 보호된 서버가 Azure 서비스와 동기화되고 백업 항목이 있는 경우 동의 확인란에는 종속 백업 항목 수와 백업 항목을 볼 수 있는 링크가 표시됩니다.
    >- 보호된 서버가 Azure 서비스와 동기화되지 않고 백업 항목이 있는 경우 동의 확인란에는 백업 항목 수만 표시됩니다.
    >- 백업 항목이 없는 경우 동의 확인란에서 삭제를 요청합니다.

4. 동의 확인란을 선택한 다음 **삭제** 를 선택합니다.

5. **알림** 아이콘 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png)를 선택합니다. 작업이 완료되면 서비스는 ‘“백업 항목”에 대한 백업 중지 및 백업 데이터 삭제’ 메시지를 표시됩니다. ‘작업이 성공적으로 완료되었습니다.’
6. **백업 항목** 메뉴에서 **새로 고침** 을 선택하여 백업 항목이 삭제되었는지 확인합니다.

>[!NOTE]
>종속성이 포함된 포털에서 온-프레미스 보호된 항목을 삭제하는 경우 “서버 등록 삭제는 파괴적 작업으로 실행 취소할 수 없습니다. 모든 백업 데이터(데이터를 복원하는 데 필요한 복구 지점)와 보호된 서버와 연결된 백업 항목은 영구적으로 삭제됩니다”라는 경고가 표시됩니다.

이 프로세스가 완료되면 관리 콘솔에서 백업 항목을 삭제할 수 있습니다.

- [MARS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mars-management-console)
- [MABS 또는 DPM 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>MARS 관리 콘솔에서 백업 항목 삭제

>[!NOTE]
>백업을 중지하지 않고 원본 머신을 삭제하거나 분실한 경우 예약된 다음 백업이 실패합니다. 이전 복구 지점은 정책에 따라 만료되지만 마지막 단일 복구 지점은 백업을 중지하고 데이터를 삭제할 때까지 항상 유지됩니다. 이는 [이 섹션](#delete-protected-items-on-premises)의 단계에 따라 실시할 수 있습니다.

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동한 다음 **백업 예약** 을 선택합니다.
2. **예약된 백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용을 중지하고 저장된 백업 모두 삭제** 를 선택합니다. 그다음에 **다음** 을 선택합니다.

    ![예약된 백업을 수정 또는 중지합니다.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **예약된 백업 중지** 페이지에서 **마침** 을 선택합니다.

    ![예약된 백업을 중지합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성해야 하는 보안 PIN(개인 식별 번호)을 입력하라는 메시지가 표시됩니다. 시작하려면 먼저 Azure Portal에 로그인합니다.
5. **Recovery Services 자격 증명 모음** > **설정** > **속성** 으로 이동합니다.
6. **보안 PIN** 에서 **생성** 을 선택합니다. 이 PIN을 복사합니다. PIN은 5 분 동안만 유효합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인** 을 선택합니다.

    ![보안 PIN을 생성합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. **백업 진행률 수정** 페이지에 *삭제된 백업 데이터는 14일 동안 보존됩니다. 이후 백업 데이터는 영구적으로 삭제됩니다.* 라는 메시지가 표시됩니다.  

    ![백업 인프라를 삭제합니다.](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제한 후 포털에서 다음 단계를 수행합니다.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>MABS 또는 DPM 관리 콘솔에서 백업 항목 삭제

>[!NOTE]
>백업을 중지하지 않고 원본 머신을 삭제하거나 분실한 경우 예약된 다음 백업이 실패합니다. 이전 복구 지점은 정책에 따라 만료되지만 마지막 단일 복구 지점은 백업을 중지하고 데이터를 삭제할 때까지 항상 유지됩니다. 이는 [이 섹션](#delete-protected-items-on-premises)의 단계에 따라 실시할 수 있습니다.

MABS 또는 DPM 관리 콘솔에서 백업 항목을 삭제하는 데 사용할 수 있는 방법에는 두 가지가 있습니다.

#### <a name="method-1"></a>방법 1

보호를 중지하고 백업 데이터를 삭제하려면 다음 단계를 수행합니다.

1. DPM 관리자 콘솔을 열고 탐색 모음에서 **보호** 를 선택합니다.
2. 디스플레이 창에서 제거할 보호 그룹 구성원을 선택합니다. **그룹 구성원의 보호 중지** 옵션을 마우스 오른쪽 단추로 클릭하여 선택합니다.
3. **보호 중지** 대화 상자에서 **보호된 데이터 삭제** 를 선택한 다음 **온라인 스토리지 삭제** 확인란을 선택합니다. 그런 다음 **보호 중지** 를 선택합니다.

    ![보호 중지 창에서 보호된 데이터 삭제를 선택합니다.](./media/backup-azure-delete-vault/delete-storage-online.png)

    다음 버전의 경우 수동으로 생성해야 하는 보안 PIN(개인 식별 번호)을 입력하라는 메시지가 표시됩니다.
    

    - DPM 2019 UR1 이상
    - DPM 2016 UR9 이상
    - MABS V3 UR1 이상
    
    PIN을 생성하려면 다음 단계를 수행합니다.
    
    1. Azure Portal에 로그인합니다.
    1. **Recovery Services 자격 증명 모음** > **설정** > **속성** 으로 이동합니다.
    1. **보안 PIN** 에서 **생성** 을 선택합니다.
    1. 이 PIN을 복사합니다. 
       >[!NOTE]
       >PIN은 5 분 동안만 유효합니다.
    1. 관리 콘솔에서 PIN을 붙여넣은 다음 **제출** 을 선택합니다.
       ![MABS 및 DPM 관리 콘솔에서 백업 항목을 삭제하려면 보안 PIN을 입력하세요.](./media/backup-azure-delete-vault/enter-security-pin.png)

4. 이전에 **보호 중지** 대화 상자에서 **온라인 스토리지 삭제** 를 선택한 경우 이 단계를 무시합니다. 비활성 보호 그룹을 마우스 오른쪽 단추로 클릭하고 **비활성 보호 제거** 를 선택합니다.

    ![비활성 보호를 제거합니다.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **비활성 보호 삭제** 창에서 **온라인 스토리지 삭제** 확인란을 선택한 다음 **확인** 을 선택합니다.

    ![온라인 스토리지를 삭제합니다.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

    다음 버전의 경우 수동으로 생성해야 하는 보안 PIN(개인 식별 번호)을 입력하라는 메시지가 표시됩니다.
    

    - DPM 2019 UR1 이상
    - DPM 2016 UR9 이상
    - MABS V3 UR1 이상
    
    PIN을 생성하려면 다음 단계를 수행합니다.
    
    1. Azure Portal에 로그인합니다.
    1. **Recovery Services 자격 증명 모음** > **설정** > **속성** 으로 이동합니다.
    1. **보안 PIN** 에서 **생성** 을 선택합니다.
    1. 이 PIN을 복사합니다. 
       >[!NOTE]
       >PIN은 5 분 동안만 유효합니다.
    1. 관리 콘솔에서 PIN을 붙여넣은 다음 **제출** 을 선택합니다.
       ![MABS 및 DPM 관리 콘솔에서 백업 항목을 삭제하려면 보안 PIN을 입력하세요.](./media/backup-azure-delete-vault/enter-security-pin.png)
 
     보호된 구성원 상태가 ‘비활성 복제본 사용 가능’으로 변경됩니다.

#### <a name="method-2"></a>방법 2

**MABS 관리** 또는 **DPM 관리** 콘솔을 엽니다. **데이터 보호 방법 선택** 에서 **온라인 보호 사용** 확인란의 선택을 취소합니다.

  ![데이터 보호 방법을 선택합니다.](./media/backup-azure-delete-vault/data-protection-method.png)

온-프레미스 백업 항목을 삭제한 후 포털에서 다음 단계를 수행합니다.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

1. 모든 종속성이 제거되면 자격 증명 모음 메뉴의 **Essentials** 창으로 스크롤합니다.
2. 백업 항목, 백업 관리 서버 또는 복제된 항목이 나열되어 있지 않은지 확인합니다. 항목이 자격 증명 모음에 계속 표시되는 경우 [시작하기 전에](#before-you-start) 섹션을 참조하세요.

3. 자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제** 를 선택합니다.

    ![자격 증명 모음 대시보드에서 삭제를 선택합니다.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 자격 증명 모음을 삭제할 것인지 확인하려면 **예** 를 선택합니다. 자격 증명 모음이 삭제됩니다. 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>PowerShell을 사용하여 Recovery Services 자격 증명 모음 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해하려면 **[시작하기 전에](#before-you-start)** 섹션을 참조하세요.

보호를 중지하고 백업 데이터를 삭제하려면:

- Azure VM 백업에서 SQL을 사용하고 있고 SQL 인스턴스에 대해 자동 보호를 사용한 경우 먼저 자동 보호를 사용하지 않습니다.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Azure Backup 보호된 항목에 대한 보호를 사용하지 않는 방법에 대해 [자세히 알아보세요](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection).

- 클라우드의 모든 백업 보호된 항목(예: IaaS VM, Azure 파일 공유 등)에 대한 보호를 중지하고 데이터를 삭제합니다.

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    백업 보호된 항목에 대한 보호를 사용하지 않는 방법에 대해 [자세히 알아보세요](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

- Azure에 백업하며 MARS(Azure Backup Agent)를 사용하여 보호되는 온-프레미스 파일 및 폴더의 경우 다음 PowerShell 명령을 사용하여 각 MARS PowerShell 모듈에서 백업 데이터를 삭제합니다.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    그러면 다음 프롬프트가 표시됩니다.

    ‘Microsoft Azure Backup 이 백업 정책을 제거하시겠습니까? 삭제된 백업 데이터는 14일 동안 보관됩니다. 이후 백업 데이터는 영구적으로 삭제됩니다. <br/> [Y] 예 [A] 모두 예 [N] 아니요 [L] 모두 아니요 [S] 일시 중단 [?] 도움말(기본값: "Y"):’

- MABS(Microsoft Azure Backup Server) 또는 DPM-Azure(System Center Data Protection Manager)를 사용하여 보호되는 온-프레미스 머신의 경우 다음 명령을 사용하여 Azure에서 백업 데이터를 삭제합니다.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    그러면 다음 프롬프트가 표시됩니다.

   *Microsoft Azure Backup* 이 백업 정책을 제거하시겠습니까? 삭제된 백업 데이터는 14일 동안 보관됩니다. 이 기간 후에는 백업 데이터가 영구적으로 삭제됩니다. <br/>
   [Y] 예 [A] 모두 예 [N] 아니요 [L] 모두 아니요 [S] 일시 중단 [?] 도움말(기본값: "Y"):*

백업 데이터를 삭제한 후 온-프레미스 컨테이너와 관리 서버의 등록을 취소합니다.

- Azure에 백업하며 MARS(Azure Backup Agent)를 사용하여 보호되는 온-프레미스 파일 및 폴더:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    자격 증명 모음에서 Windows Server 또는 기타 컨테이너를 등록 취소하는 방법에 대해 [자세히 알아보세요](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer).

- MABS(Microsoft Azure Backup Server) 또는 DPM-Azure(System Center Data Protection Manager)를 사용하여 보호되는 온-프레미스 머신:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    자격 증명 모음에서 백업 관리 컨테이너를 등록 취소하는 방법에 대해 [자세히 알아보세요](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer).

백업 데이터를 영구적으로 삭제하고 모든 컨테이너의 등록을 취소한 후 계속해서 자격 증명 모음을 삭제합니다.

Recovery Services 자격 증명 모음 삭제하려면

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

Recovery Services 자격 증명 모음을 삭제하는 방법에 대해 [자세히 알아보세요](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>CLI를 사용하여 Recovery Services 자격 증명 모음 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해하려면 **[시작하기 전에](#before-you-start)** 섹션을 참조하세요.

> [!NOTE]
> 현재 Azure Backup CLI는 Azure VM 백업만 관리하도록 지원하므로 자격 증명 모음을 삭제하는 다음 명령은 자격 증명 모음에 Azure VM 백업이 포함된 경우에만 작동합니다. 자격 증명 모음에 Azure VM 이외의 백업 항목이 포함된 경우 Azure Backup CLI를 사용하여 자격 증명 모음을 삭제할 수 없습니다.

기존 Recovery Services 자격 증명 모음을 삭제하려면 다음 단계를 수행합니다.

- 보호를 중지하고 백업 데이터를 삭제하려면

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    자세한 내용은 이  [문서](/cli/azure/backup/protection#az_backup_protection_disable)를 참조하세요.

- 기존 Recovery Services 자격 증명 모음을 삭제합니다.

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    자세한 내용은 이  [문서](/cli/azure/backup/vault)를 참조하세요.

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Recovery Services 자격 증명 모음 삭제

Recovery Services 자격 증명 모음을 삭제하는 이 옵션은 모든 종속성이 제거되고 ‘자격 증명 모음 삭제 오류’가 계속 발생하는 경우에만 권장됩니다. 다음 팁 중 하나 또는 모두를 시도합니다.

- 자격 증명 모음 메뉴의 **Essentials** 창에 백업 항목, 백업 관리 서버 또는 복제된 항목이 나열되어 있지 않은지 확인합니다. 백업 항목이 있는 경우 [시작하기 전에](#before-you-start) 섹션을 참조하세요.
- [포털에서 자격 증명 모음 삭제](#delete-the-recovery-services-vault)를 다시 시도해 보세요.
- 모든 종속성이 제거되고 ‘자격 증명 모음 삭제 오류’가 계속 발생하는 경우 ARMClient 도구를 사용하여 다음 단계를 수행합니다(참고 후).

1. [chocolatey.org](https://chocolatey.org/)로 이동하여 Chocolatey를 다운로드하고 설치합니다. 그런 다음, 다음 명령을 실행하여 ARMClient를 설치합니다.

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure 계정에 로그인하고 다음 명령을 실행합니다.

    `ARMClient.exe login [environment name]`

3. Azure Portal에서 삭제하려는 자격 증명 모음에 대한 구독 ID 및 리소스 그룹 이름을 수집합니다.

ARMClient 명령에 대한 자세한 내용은 [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md)를 참조하세요.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Azure Resource Manager 클라이언트를 사용하여 Recovery Services 자격 증명 모음 삭제

1. 구독 ID, 리소스 그룹 이름 및 자격 증명 모음 이름을 사용하여 다음 명령을 실행합니다. 종속성이 없는 경우 다음 명령을 실행하면 자격 증명 모음이 삭제됩니다.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. 자격 증명 모음이 비어 있지 않으면 ‘자격 증명 모음에 기존 리소스가 있으므로 이 자격 증명 모음을 삭제할 수 없습니다.’ 오류 메시지가 표시됩니다. 자격 증명 모음 내에서 보호된 항목 또는 컨테이너를 제거하려면 다음 명령을 실행합니다.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure Portal에서 자격 증명 모음이 삭제되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[Recovery Services 자격 증명 모음에 대한 자세한 정보](backup-azure-recovery-services-vault-overview.md)
[Recovery Services 자격 증명 모음 모니터링 및 관리에 대한 자세한 정보](backup-azure-manage-windows-server.md)
