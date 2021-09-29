---
title: Azure Data Box 및 Azure Data Box 가져오기 주문에 대한 이벤트 추적 및 로깅| Microsoft Docs
description: Azure Data Box 및 Azure Data Box Heavy 가져오기 주문의 다양한 단계에서 이벤트를 추적하고 로깅하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/24/2021
ms.author: alkohli
ms.openlocfilehash: 842a6572ca7daf3c0039b088f25d538f709a1df7
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "129220409"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>Azure Data Box에 대한 추적 및 이벤트 로깅 및 Azure Data Box Heavy 가져오기 주문

Data Box 또는 Data Box Heavy 가져오기 주문은 주문, 설정, 데이터 복사, 반환, Azure에 업로드 후 확인, 데이터 지우기 단계를 거칩니다. 주문의 각 단계에서 주문에 대한 액세스 제어, 이벤트 감사, 주문 추적, 생성되는 다양한 로그 해석과 같은 여러 작업을 수행할 수 있습니다.

다음 표에는 가져오기 주문을 처리하는 각 단계와 단계 중에 주문을 추적하고 감사하는 데 사용할 수 있는 도구에 대한 설명이 요약되어 있습니다.

| Data Box 가져오기 주문 단계| 추적 및 감사 도구|
|----------------------------|------------------------|
| 주문 만들기               | [Azure RBAC를 통해 주문에 대한 액세스 제어 설정](#set-up-access-control-on-the-order)                                                    |
| 주문 처리            | 다음을 통해 [주문 추적](#track-the-order) <ul><li> Azure portal </li><li> 운송업체 웹 사이트 </li><li>이메일 알림</ul> |
| 디바이스 설정              | [활동 로그](#query-activity-logs-during-setup)에 기록된 디바이스 자격 증명 액세스                                              |
| 디바이스에 데이터 복사        | 데이터 복사에 사용할 [*error.xml* 파일 보기](#view-error-log-during-data-copy)                                                             |
| 배송 준비            | 디바이스에서 [BOM 파일](#inspect-bom-during-prepare-to-ship) 또는 매니페스트 파일 검사                                      |
| Azure에 데이터 업로드       | [복사 로그를 검토](#review-copy-log-during-upload-to-azure)하여 Azure 데이터 센터에서 데이터를 업로드하는 동안 오류가 발생하지 않았는지 확인                         |
| 디바이스에서 데이터 삭제   | 감사 로그 및 주문 기록을 포함한 [연계 보관 로그 확인](#get-chain-of-custody-logs-after-data-erasure)                |

이 문서에서는 Data Box 또는 Data Box Heavy 가져오기 주문을 추적하고 감사하는 데 사용할 수 있는 다양한 메커니즘 또는 도구에 대해 자세히 설명합니다. 이 문서의 정보는 Data Box 및 Data Box Heavy 가져오기 주문에 모두 적용됩니다. 이후 섹션에서는 Data Box에 대한 모든 참조가 Data Box Heavy에도 적용됩니다.

> [!NOTE]
> [!INCLUDE [data-box-copy-logs-behind-firewall](../../includes/data-box-copy-logs-behind-firewall.md)]

## <a name="set-up-access-control-on-the-order"></a>주문에 대한 액세스 제어 설정

주문이 처음 생성될 때 주문에 액세스할 수 있는 사용자를 컨트롤할 수 있습니다. 다양한 범위에서 Azure 역할을 설정하여 Data Box 순서에 대한 액세스를 제어합니다. Azure 역할은 작업의 하위 집합에 대한 읽기-쓰기, 읽기 전용, 읽기-쓰기 액세스 유형을 결정합니다.

Azure Data Box 서비스에 대해 정의할 수 있는 두 가지 역할은 다음과 같습니다.

- **Data Box 읽기** - 범위에 따라 정의된 주문에 대한 읽기 전용 액세스 권한이 있습니다. 주문 세부 정보만 볼 수 있습니다. 스토리지 계정과 관련된 다른 세부 정보에 액세스하거나 주소 등과 같은 주문 세부 정보를 편집할 수 없습니다.
- **Data Box 기여자** - *스토리지 계정에 대한 쓰기 권한이 이미 있는 경우* 지정된 스토리지 계정으로 데이터를 전송하는 주문만 만들 수 있습니다. 스토리지 계정에 액세스할 수 없는 경우 계정에 데이터를 복사하는 Data Box 순서를 만들 수도 없습니다. 이 역할은 스토리지 계정 관련 사용 권한을 정의하지 않으며 스토리지 계정에 대한 액세스 권한을 부여 하지도 않습니다.  

주문에 대한 액세스를 제한하려면 다음을 수행할 수 있습니다.

- 순서 수준에서 역할을 할당합니다. 사용자에게는 해당 특정 Data Box 순서와 상호작용하기 위해 역할에 따라 정의된 권한만 있고 다른 작업은 없습니다.
- 리소스 그룹 수준에서 역할을 할당하면 사용자는 리소스 그룹 내의 모든 Data Box 주문에 액세스할 수 있습니다.

권장하는 Azure RBAC 사용에 대한 자세한 내용은 [Azure RBAC에 대한 모범 사례](../role-based-access-control/best-practices.md)를 참조하세요.

## <a name="track-the-order"></a>주문 추적

Azure Portal 및 이동 통신 사업자 웹 사이트를 통해 주문을 추적할 수 있습니다. 다음 메커니즘은 언제든지 Data Box 순서를 추적하는 데 사용할 수 있습니다.

- 디바이스가 Azure 데이터 센터 또는 온-프레미스에 있는 경우 순서를 추적하려면 Azure Portal에서 **Data Box 순서 > 개요** 로 이동합니다.

    ![주문 상태 및 추적 번호 보기](media/data-box-logs/overview-view-status-1.png)

- 디바이스가 전송되는 동안 주문을 추적하려면 지역 이동 통신 사업자 웹사이트(예: 미국의 UPS 웹 사이트)로 이동합니다. 주문과 관련된 추적 번호를 제공 합니다.
- 또한 Data Box는 주문 생성 시 제공된 이메일에 따라 주문 상태가 변경될 때마다 이메일 알림을 보냅니다. 모든 Data Box 주문 상태 목록은 [주문 상태 보기](data-box-portal-admin.md#view-order-status)를 참조하세요. 주문과 관련된 알림 설정을 변경하려면 [알림 세부 정보 편집](data-box-portal-admin.md#edit-notification-details)을 참조하세요.

## <a name="query-activity-logs-during-setup"></a>설정 중 활동 로그 쿼리

- Data Box는 잠긴 상태로 온-프레미스에 도착합니다. 주문에 Azure Portal에서 사용 가능한 디바이스 자격 증명을 사용할 수 있습니다.  

    Data Box가 설정되면 디바이스 자격 증명에 액세스한 모든 사람을 알아야 할 수 있습니다. **디바이스 자격 증명** 블레이드에 액세스한 사람을 파악하기 위해 활동 로그를 쿼리할 수 있습니다.  **디바이스 세부 정보 > 자격 증명** 블레이드 액세스와 관련된 모든 작업은 `ListCredentials` 활동 로그에 작업으로 기록됩니다.

    ![활동 로그 쿼리](media/data-box-logs/query-activity-log-1.png)

- 모든 Data Box 로그인은 실시간으로 기록됩니다. 그러나 이 정보는 주문이 성공적으로 완료된 후 [감사 로그](#audit-logs)에서만 사용할 수 있습니다.

## <a name="view-error-log-during-data-copy"></a>데이터를 복사하는 동안 오류 로그 보기

Data Box 또는 Data Box Heavy에 데이터를 복사하는 동안 복사되는 데이터에 문제가 있으면 오류 파일이 생성됩니다.

### <a name="errorxml-file"></a>Error.xml 파일

오류 없이 복사 작업이 완료되었는지 확인합니다. 복사하는 동안 오류가 있는 경우 **연결 및 복사** 페이지에서 로그를 다운로드합니다.

- 512바이트로 정렬되지 않은 파일을 Data Box의 관리 디스크 폴더에 복사한 경우 해당 파일은 준비 스토리지 계정에 페이지 Blob으로 업로드되지 않습니다. 로그에 오류가 표시됩니다. 파일을 제거하고, 512바이트로 정렬된 파일을 복사합니다.
- VHDX 또는 동적 VHD 또는 차이점 보관용 VHD를 복사한 경우 로그에 오류가 표시 됩니다. 이러한 파일 형식은 지원 되지 않습니다.

다음은 관리 디스크에 복사할 때 발생하는 여러 가지 오류에 대한 *error.xml* 파일 샘플입니다.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

다음은 페이지 Blob에 복사할 때 발생하는 여러 가지 오류에 대한 *error.xml* 파일 샘플입니다.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


다음은 블록 Blob에 복사할 때 발생하는 여러 가지 오류에 대한 *error.xml* 파일 샘플입니다.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

다음은 Azure Files에 복사할 때 발생하는 여러 가지 오류에 대한 *error.xml* 파일 샘플입니다.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

위의 각 사례에서 오류를 해결한 후 다음 단계를 진행합니다. SMB 또는 NFS 프로토콜을 통해 Data Box에 데이터를 복사하는 동안 수신된 오류에 대한 자세한 내용은 [Data Box 및 Data Box Heavy 문제 해결](data-box-troubleshoot.md)을 참조하세요. REST를 통해 Data Box에 데이터를 복사하는 동안 수신된 오류에 대한 자세한 내용은 [Data Box Blob 스토리지 문제 해결](data-box-troubleshoot-rest.md)을 참조하세요.

## <a name="inspect-bom-during-prepare-to-ship"></a>배송 준비 중 BOM 검사

배송을 준비하는 동안 BOM(제품 구성 정보)이라고 하는 파일 또는 매니페스트 파일 목록이 만들어집니다.

- 이 파일을 사용하여 Data Box에 복사된 파일의 실제 이름 및 파일 수를 확인합니다.
- 이 파일을 사용하여 파일의 실제 크기를 확인합니다.
- *crc64* 가 0이 아닌 문자열에 해당하는지 확인합니다. <!--A null value for crc64 indicates that there was a reparse point error)-->

배송 준비 중에 수신한 오류에 대한 자세한 내용은 [Data Box 및 Data Box Heavy 문제 해결](data-box-troubleshoot.md)을 참조하세요.

### <a name="bom-or-manifest-file"></a>BOM 또는 매니페스트 파일

BOM 또는 매니페스트 파일에는 Data Box 디바이스에 복사된 모든 파일 목록이 포함됩니다. BOM 파일에는 파일 이름, 파일 크기 및 체크섬이 들어 있습니다. REST API를 통해 Data Box의 관리 디스크에 복사하는 경우에는 블록 Blob, 페이지 Blob, Azure Files에 대한 별도의 BOM 파일이 만들어집니다. 배송 준비 중에 디바이스의 로컬 웹 UI에서 BOM 파일을 다운로드할 수 있습니다.

이러한 파일은 Data Box 디바이스에도 있으며 Azure 데이터 센터의 연결된 스토리지 계정에 업로드됩니다.

### <a name="bom-file-format"></a>BOM 파일 형식

BOM 또는 매니페스트 파일의 일반적인 형식은 다음과 같습니다.

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

다음은 Data Box의 블록 Blob 공유에 데이터가 복사될 때 생성되는 매니페스트 샘플입니다.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM 또는 매니페스트 파일도 Azure 스토리지 계정에 복사됩니다. BOM 또는 매니페스트 파일을 사용하여 Azure에 업로드된 파일이 Data Box에 복사된 데이터와 일치하는지 확인할 수 있습니다.

## <a name="review-copy-log-during-upload-to-azure"></a>Azure에 업로드하는 동안 복사 로그 검토

Azure에 데이터를 업로드하는 동안 복사 로그가 생성됩니다.

### <a name="copy-log"></a>복사 로그

처리되는 각 주문과 관련하여, Data Box 서비스는 연결된 스토리지 계정에 복사 로그를 만듭니다. 복사 로그에는 업로드된 총 파일 수와 Data Box에서 Azure 스토리지 계정으로 데이터를 복사하는 동안 오류가 발생한 파일 수가 포함되어 있습니다.

Azure에 업로드하는 동안 CRC(순환 중복 검사) 계산이 수행됩니다. 데이터 복사의 CRC와 데이터 업로드 후의 CRC가 비교됩니다. CRC가 일치하지 않으면 해당 파일을 업로드하지 못했다는 뜻입니다.

기본적으로 로그는 `copylog`라는 컨테이너에 기록됩니다. 로그는 다음 명명 규칙을 사용하여 저장됩니다.

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

복사 로그 경로는 포털의 **개요** 블레이드에도 표시됩니다.

![완료되면 표시되는 개요 블레이드의 복사 로그 경로](media/data-box-logs/copy-log-path-1.png)

> [!NOTE]
> [!INCLUDE [data-box-copy-logs-behind-firewall](../../includes/data-box-copy-logs-behind-firewall.md)]

### <a name="upload-completed-successfully"></a>성공적으로 업로드 완료

다음 샘플에서는 성공적으로 완료된 Data Box 업로드의 일반적인 복사 로그 형식을 설명합니다.

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-warnings"></a>경고와 함께 업로드 완료

데이터에 Azure 명명 규칙을 준수하지 않는 컨테이너, Blob 또는 파일 이름이 있어서 Azure에 데이터를 업로드하기 위해 이름을 수정한 경우 경고와 함께 업로드가 완료됩니다.

![경고와 함께 완료될 때 표시되는 개요 블레이드의 복사 로그 경로](media/data-box-logs/copy-log-path-3.png)

다음은 Azure에 데이터를 업로드하는 동안 Azure 명명 규칙을 준수하지 않는 컨테이너의 이름이 변경된 복사 로그의 예입니다.

새 컨테이너의 고유한 이름은 `DataBox-GUID` 형식입니다. 원래 컨테이너의 데이터는 이름이 바뀐 새 컨테이너에 저장됩니다. 복사 로그는 이전 및 새 컨테이너 이름을 지정합니다.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

다음은 Azure에 데이터를 업로드하는 동안 Azure 명명 규칙을 준수하지 않는 Blob 또는 파일의 이름이 변경된 복사 로그의 예입니다. 새 Blob 또는 파일 이름은 컨테이너에 대한 상대 경로의 SHA256 다이제스트로 변환되고 대상 유형에 따라 경로에 업로드됩니다. 대상은 블록 Blob, 페이지 Blob 또는 Azure Files입니다.

`copylog`는 Azure의 이전 Blob 및 새 Blob 또는 파일 이름과 경로를 지정합니다.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```


### <a name="upload-completed-with-errors"></a>오류와 함께 업로드 완료

Azure에 데이터가 업로드되었지만 오류가 발생하는 경우가 있습니다.

![오류와 함께 완료될 때 표시되는 개요 블레이드의 복사 로그 경로](media/data-box-logs/copy-log-path-2.png)

파일이 업로드되지 않고 다시 시도할 수 없는 오류가 발생할 때도 있습니다. 이 경우 알림을 받게 됩니다. 알림에 대한 후속 조치를 취하는 방법에 대한 자세한 내용은 [Azure Data Box 및 Azure Data Box Heavy 디바이스에서 데이터 업로드의 복사 오류 검토](data-box-troubleshoot-data-upload.md)를 참조하세요.

다음은 오류와 함께 업로드가 완료된 복사 로그의 예입니다.

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>데이터 삭제 후 관리 연속성 로그 가져오기

NIST SP 800-88 개정 1 지침에 따라 Data Box 디스크에서 데이터가 지워진 후 관리 연속성 로그를 사용할 수 있습니다. 이러한 로그에는 감사 로그 및 주문 기록이 포함됩니다. BOM 또는 매니페스트 파일도 감사 로그와 함께 복사됩니다.

### <a name="audit-logs"></a>감사 로그

감사 로그에는 Data Box 또는 Data Box Heavy가 Azure 데이터 센터 외부에 있는 경우 전원을 켜고 공유에 액세스하는 방법에 대한 정보가 포함되어 있습니다. 이러한 로그는 `storage-account/azuredatabox-chainofcustodylogs`에 있습니다.

다음은 Data Box의 감사 로그 샘플입니다.

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>주문 기록 다운로드

주문 기록은 Azure Portal에서 사용할 수 있습니다. 주문이 완료되고 디바이스 정리(디스크에서 데이터 지우기)가 완료되면, 디바이스 주문으로 이동하여 **주문 세부 정보** 로 이동합니다. **주문 기록 다운로드** 옵션을 사용할 수 있습니다. 자세한 정보는 [다운로드 주문 기록](data-box-portal-admin.md#download-order-history)을 참조하세요.

주문 기록을 스크롤하면 다음을 확인할 수 있습니다.

- 디바이스에 대한 이동 통신 사업자 추적 정보.
- *SecureErase* 작업을 사용하는 이벤트. 이러한 이벤트는 디스크의 데이터 지우기에 해당합니다.
- Data Box 로그 링크 *감사 로그*, *복사 로그* 및 *BOM* 파일의 경로가 표시됩니다.

다음은 Azure Portal의 주문 기록 로그 샘플입니다.

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>다음 단계

- [Data Box 및 Data Box Heavy 문제 해결](data-box-troubleshoot.md) 방법에 대해 알아보세요.
