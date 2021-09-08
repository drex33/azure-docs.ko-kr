---
title: 로컬 웹 UI를 사용하여 Azure Data Box/Azure Data Box Heavy 관리
description: 로컬 웹 UI를 사용하여 Data Box 및 Data Box Heavy 디바이스를 관리하는 방법 설명
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/25/2021
ms.author: alkohli
ms.openlocfilehash: fa37d5dc9957e39d7fe4f47404bc9aaf249f192f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226393"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>로컬 웹 UI를 사용하여 Data Box 및 Data Box Heavy 디바이스 관리

이 문서는 Data Box 및 Data Box Heavy 디바이스에서 수행되는 몇 가지 구성 및 관리 작업을 설명합니다. 디바이스의 로컬 웹 UI와 Azure Portal UI를 통해 Data Box 및 Data Box Heavy 디바이스를 관리할 수 있습니다. 이 문서에서는 로컬 웹 UI를 사용하여 수행되는 작업을 집중적으로 설명합니다.

Data Box 및 Data Box Heavy용 로컬 웹 UI는 디바이스의 초기 구성에 사용됩니다. 로컬 웹 UI를 사용하여 디바이스를 종료하거나 다시 시작하고, 진단 테스트를 실행하고, 소프트웨어를 업데이트하고, 복사 로그를 확인하고, 디바이스에서 로컬 데이터를 삭제하며, Microsoft 지원에 보낼 지원 패키지를 생성할 수도 있습니다. 독립적인 노드가 두 개인 Data Box Heavy 디바이스에서는 디바이스의 각 노드에 해당하는 두 개의 개별 로컬 웹 UI에 액세스할 수 있습니다.

## <a name="generate-support-package"></a>지원 패키지 생성

디바이스 문제가 발생하는 경우 시스템 로그에서 지원 패키지를 만들 수 있습니다. Microsoft 지원에서는 이 패키지를 사용하여 문제를 해결합니다.

지원 패키지를 생성하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **고객 지원팀에 문의** 로 이동합니다. 필요에 따라 **메모리 덤프 포함** 을 선택합니다. 그런 다음 **지원 패키지 만들기** 를 선택합니다.

    메모리 덤프는 시스템 실패가 발생한 후 저장되는 디바이스 메모리의 콘텐츠입니다.

    고객 지원팀이 요청하지 않는 한 **메모리 덤프 포함** 옵션을 선택해서는 안 됩니다. 메모리 덤프가 포함된 지원 패키지를 수집하는 데는 시간이 오래 걸리고 중요한 데이터가 포함되어 있습니다.

    ![지원 패키지 만들기 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    지원 패키지가 수집됩니다. 시스템 로그만 포함하는 경우 이 작업은 몇 분 정도 걸립니다. 메모리 덤프를 포함하는 경우 시간이 더 오래 걸립니다.

    ![지원 패키지 만들기 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. 지원 패키지 만들기가 완료되면 **지원 패키지 다운로드** 를 선택합니다.

    ![지원 패키지 만들기 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. 다운로드 위치를 찾아서 선택합니다. 폴더를 열어 내용을 확인합니다.

    ![지원 패키지 만들기 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>디바이스에서 로컬 데이터 지우기

로컬 웹 UI를 사용하여 Azure 데이터 센터에 반환하기 전에 디바이스에서 로컬 데이터를 지울 수 있습니다.

> [!IMPORTANT]
> 데이터를 지우면 되돌릴 수 없습니다. 디바이스에서 로컬 데이터를 지우기 전에 파일을 백업해야 합니다.

디바이스에서 로컬 데이터를 지우려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **데이터 지우기** 로 이동합니다.
2. 디바이스 암호를 입력하고 **데이터 지우기** 를 선택합니다.

    ![디바이스에 대한 데이터 지우기 옵션](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. 확인 메시지가 나타나면 **예** 를 선택하여 계속 진행합니다. 데이터 지우기는 50분까지 걸릴 수 있습니다.

   디바이스에서 삭제하기 전에 로컬 데이터를 백업해야 합니다. 데이터를 지우면 되돌릴 수 없습니다.

    ![데이터 지우기 확인 프롬프트](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>디바이스 종료 또는 다시 시작

로컬 웹 UI를 사용하여 디바이스를 종료하거나 다시 시작할 수 있습니다. 다시 시작하기 전에 호스트에서 공유를 오프라인으로 전환한 후 디바이스를 다시 시작하는 것이 좋습니다. 이렇게 하면 데이터 손상 가능성이 최소화됩니다. 디바이스를 종료할 때 데이터 복사가 진행 중이지 않은지 확인합니다.

디바이스를 종료하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **종료 또는 다시 시작** 으로 이동합니다.

2. **종료** 를 선택합니다.

    ![Data Box 종료 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 확인을 요청하는 메시지가 표시되면 **확인** 을 선택하여 계속 진행합니다.

    ![Data Box 종료 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

디바이스가 종료되면 전면 패널의 전원 단추를 사용하여 디바이스를 켭니다.

Data Box를 다시 시작하려면 다음 단계를 수행합니다.

1. 로컬 웹 UI에서 **종료 또는 다시 시작** 으로 이동합니다.
2. **다시 시작** 을 선택합니다.

    ![Data Box 다시 시작 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 확인을 요청하는 메시지가 표시되면 **확인** 을 선택하여 계속 진행합니다.

   디바이스가 종료되었다가 다시 시작됩니다.

## <a name="get-share-credentials"></a>공유 자격 증명 가져오기 

디바이스의 공유에 연결하는 데 사용할 사용자 이름과 암호를 찾아야 하는 경우 로컬 웹 UI의 **연결 및 복사** 에서 공유 자격 증명을 찾을 수 있습니다.

디바이스를 주문할 때 디바이스에서 공유하기 위한 기본 시스템 생성 암호를 사용하거나 자신의 암호를 사용하도록 선택할 수 있습니다. 어느 쪽이든 공유 암호는 공장에서 설정되며 변경할 수 없습니다. 

공유에 대한 자격 증명을 얻으려면:

1. 로컬 웹 UI에서 **연결 및 복사** 로 이동합니다. **SMB** 를 선택하여 스토리지 계정과 연결된 공유의 액세스 자격 증명을 가져옵니다.

   ![Data Box에 대한 로컬 웹 UI의 연결 및 복사 페이지를 보여 주는 스크린샷. 연결 및 복사 메뉴 항목과 SMB 옵션이 강조 표시됩니다.](media/data-box-local-web-ui-admin/get-share-credentials-01.png)

1. **액세스 공유 및 데이터 복사** 대화 상자에서 복사 아이콘을 사용하여 공유에 해당하는 **사용자 이름** 및 **암호** 를 복사합니다. 대화 상자를 닫으려면 **확인** 을 선택합니다.

   ![데이터 상자의 SMB 공유에 대한 로컬 웹 UI의 액세스 공유 및 데이터 복사 대화 상자를 보여 주는 스크린샷. 스토리지 계정 및 암호 옵션에 대한 복사 아이콘과 확인 단추가 강조 표시됩니다.](media/data-box-local-web-ui-admin/get-share-credentials-02.png)

> [!NOTE]
> 잘못된 암호를 사용하여 여러 번 공유 연결 시도에 실패한 후에는 사용자 계정이 공유에서 잠기게 됩니다. 몇 분 후에 계정 잠금이 해제되고 공유에 다시 연결할 수 있습니다.  
> - Data Box 4.1 이상: 로그인 시도가 5회 실패하면 계정이 15분 동안 잠깁니다. 
> - Data Box 4.0 이하: 로그인 시도가 3회 실패하면 30분 동안 계정이 잠깁니다.

## <a name="download-bom-or-manifest-files"></a>BOM 또는 매니페스트 파일 다운로드

BOM 또는 매니페스트 파일은 Data Box 또는 Data Box Heavy에 복사되는 파일 목록을 포함하고 있습니다. 이러한 파일은 디바이스를 배송할 준비를 할 때 가져오기 순서에 따라 생성됩니다.

시작하기 전에 다음 단계를 수행하여 가져오기 순서에 따라 BOM 또는 매니페스트 파일을 다운로드합니다.

1. 디바이스의 로컬 웹 UI로 이동합니다. 디바이스가 **배송 준비** 단계를 완료했는지 확인합니다. 디바이스 준비가 완료되면 디바이스 상태가 **배송 준비 완료** 로 표시됩니다.

    ![디바이스 배송 준비 완료](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. **파일 목록 다운로드** 를 선택하면 Data Box에 복사된 파일 목록을 다운로드할 수 있습니다.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. 파일 탐색기에서, 디바이스에 연결하는 데 사용된 프로토콜 및 사용된 Azure Storage 유형에 따라 별도의 파일 목록이 생성되는 것을 볼 수 있습니다.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![스토리지 유형 및 연결 프로토콜에 대한 파일](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   다음 표는 파일 이름을 Azure Storage 유형 및 사용된 연결 프로토콜로 매핑합니다.

    |파일 이름  |Azure Storage 유형  |사용된 연결 프로토콜 |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |블록 Blob         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |페이지 Blob         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure 파일         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |페이지 Blob         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |블록 Blob         |REST         |

이 목록을 사용하여 Data Box가 Azure 데이터 센터로 반환된 후 Azure Storage 계정에 업로드된 파일을 확인합니다. 샘플 매니페스트 파일은 아래에 표시되어 있습니다.

> [!NOTE]
> Data Box Heavy에는 디바이스의 두 노드에 해당하는 두 개의 파일 목록 집합(BOM 파일)이 표시됩니다.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

이 파일에는 Data Box 또는 Data Box Heavy에 복사된 모든 파일 목록이 포함되어 있습니다. 이 파일의 *crc64* 값은 해당 파일에 대해 생성된 체크섬과 관련이 있습니다.

## <a name="view-available-capacity-of-the-device"></a>디바이스의 사용 가능한 용량 확인

디바이스 대시보드를 통해 디바이스에서 사용 가능한 용량과 사용한 용량을 확인할 수 있습니다.

1. 로컬 웹 UI에서 **대시보드 보기** 로 이동합니다.
2. **연결 및 복사** 아래에 디바이스의 사용 가능한 공간과 사용한 공간이 표시됩니다.

    ![사용 가능한 용량 확인](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>체크섬 유효성 검사 건너뛰기

기본적으로는 배송을 준비할 때 데이터의 체크섬이 생성됩니다. 드물지만 파일 형식(작은 파일 크기)에 따라 성능이 저하될 수 있습니다. 이러한 경우에는 체크섬을 건너뛸 수 있습니다.

배송 준비 중 체크섬 계산은 가져오기 주문에 대해서만 수행되며 내보내기 주문에 대해서는 수행되지 않습니다.

성능이 심하게 영향을 받은 경우가 아니면 체크섬은 사용하는 것이 좋습니다.

1. 디바이스 로컬 웹 UI의 오른쪽 위에 있는 **설정** 으로 이동합니다.

    ![체크섬 사용 안 함](media/data-box-local-web-ui-admin/disable-checksum.png)

2. 체크섬 유효성 검사를 **사용 안 함** 으로 설정합니다.
3. **적용** 을 선택합니다.

> [!NOTE]
> 체크섬 계산 건너뛰기 옵션은 Azure Data Box가 잠금 해제된 경우에만 사용할 수 있습니다. 디바이스가 잠겨 있으면 이 옵션이 표시되지 않습니다.

## <a name="enable-smb-signing"></a>SMB 서명 사용

SMB(서버 메시지 블록) 서명은 SMB를 사용하는 통신을 패킷 수준에서 디지털 서명할 수 있는 기능입니다. 이 서명은 전송 중인 SMB 패킷을 수정하는 공격을 방지합니다.

SMB 서명과 관련된 자세한 내용은 [서버 메시지 블록 서명 개요](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing)를 참조하세요.

Azure 디바이스에서 SMB 서명을 사용하도록 설정하려면 다음을 수행합니다.

1. 디바이스 로컬 웹 UI의 오른쪽 위에 있는 **설정** 을 선택합니다.

    ![설정 열기](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. SMB 서명을 **사용** 합니다.

    ![SMB 서명 사용](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. **적용** 을 선택합니다.
4. 로컬 웹 UI에서 **종료 또는 다시 시작** 으로 이동합니다.
5. **다시 시작** 을 선택합니다.

## <a name="enable-backup-operator-privileges"></a>백업 운영자 권한 사용

웹 UI 사용자에게는 기본값으로 SMB 공유에 대한 백업 운영자 권한이 있습니다. 이를 원하지 않는 경우에는 **백업 운영자 권한 사용** 을 사용하여 권한을 사용하거나 사용하지 않도록 설정합니다.

자세한 내용은 [Active Directory 보안 그룹](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators)의 백업 운영자를 참조하세요.

Azure 디바이스에서 백업 운영자 권한을 사용하도록 설정하려면 다음을 수행합니다.

1. 디바이스 로컬 웹 UI의 오른쪽 위에 있는 **설정** 을 선택합니다.

   ![Data Box 설정 열기-1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. 백업 운영자 권한을 **사용** 합니다.

   ![백업 운영자 권한 사용](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **적용을 선택합니다**.
4. 로컬 웹 UI에서 **종료 또는 다시 시작** 으로 이동합니다.
5. **다시 시작** 을 선택합니다.

## <a name="enable-acls-for-azure-files"></a>Azure Files용 ACL 사용

사용자가 SMB를 통해 Data Box에 데이터를 업로드하면 기본값으로 파일의 메타데이터가 전송됩니다. 메타데이터에는 ACL(액세스 제어 목록), 파일 특성 및 타임스탬프가 포함됩니다. 이 기능을 사용하지 않으려면 **Azure 파일용 ACL** 을 사용하여 이 기능을 사용하지 않도록 설정하거나 사용하도록 설정합니다.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> 파일을 사용하여 메타데이터를 전송하려면 백업 운영자여야 합니다. 이 기능을 사용하는 경우 웹 UI의 로컬 사용자가 백업 운영자인지 확인합니다. [백업 운영자 권한 사용](#enable-backup-operator-privileges)을 참조하세요.

Azure 파일에 대한 ACL 전송을 사용하도록 설정하려면 다음을 수행합니다.

1. 디바이스 로컬 웹 UI의 오른쪽 위에 있는 **설정** 을 선택합니다.

    ![Data Box 설정 열기-2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. Azure Files용 ACL을 **사용** 합니다.

     ![Azure Files용 ACL 사용](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. **적용** 을 선택합니다.
4. 로컬 웹 UI에서 **종료 또는 다시 시작** 으로 이동합니다.
5. **다시 시작** 을 선택합니다.

## <a name="enable-tls-11"></a>TLS 1.1 사용

기본값으로 Azure Data Box는 암호화에 TSL(전송 계층 보안) 1.1보다 더 안전한 TLS 1.2를 사용합니다. 그러나 사용자 또는 클라이언트에서 브라우저를 사용하여 TLS 1.2을 지원하지 않는 데이터에 액세스하는 경우 TLS 1.1을 사용하도록 설정할 수 있습니다.

TLS와 관련된 자세한 내용은 [Azure Data Box Gateway 보안](../databox-gateway/data-box-gateway-security.md)을 참조하세요.

Azure 디바이스에서 TLS 1.1을 사용하도록 설정하려면 다음을 수행합니다.

1. 디바이스 로컬 웹 UI의 오른쪽 위에 있는 **설정** 을 선택합니다.

    ![Data Box 설정 열기-3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. TLS 1.1을 **사용** 합니다.

    ![TLS 1.1 사용](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. **적용** 을 선택합니다.
4. 로컬 웹 UI에서 **종료 또는 다시 시작** 으로 이동합니다.
5. **다시 시작** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 Data Box 및 Data Box Heavy를 관리](data-box-portal-admin.md)하는 방법을 알아봅니다.
