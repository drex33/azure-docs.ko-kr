---
title: Azure Stack Edge Pro GPU용 Blob Storage 문제 해결 | Microsoft Docs
description: Azure Stack Edge 디바이스의 Blob Storage 문제를 해결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 2a77d4992d5ee2e505f9e3f29112d45c3776d8d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438765"
---
# <a name="troubleshoot-blob-storage-issues-for-an-azure-stack-edge-device"></a>Azure Stack Edge 장치의 Blob Storage 문제 해결 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge 디바이스의 Blob Storage 문제를 해결하는 방법을 설명합니다. 

## <a name="errors-for-blob-storage-on-device"></a>디바이스의 Blob Storage에 대한 오류 

다음은 Azure Stack Edge 디바이스의 Blob Storage와 관련된 오류입니다.

| **문제/오류** |  **해결 방법** | 
|--------------------|-----------------|
|자식 리소스를 검색할 수 없습니다. HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.| **편집** 메뉴에서 **대상 Azure Stack API** 를 선택합니다. 그런 다음 Azure Storage Explorer를 다시 시작합니다.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 Windows의 경우 `C:\Windows\System32\drivers\etc\hosts` 또는 Linux의 경우 `/etc/hosts` 경로의 호스트 파일에 추가되었는지 확인합니다.|
|자식 리소스를 검색할 수 없습니다.<br> 세부 정보: 자체 서명된 인증서 |디바이스에 대한 SSL 인증서를 Azure Storage Explorer로 가져옵니다. <ol><li>[인증서를 생성하고 다운로드](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)합니다.</li><li>**편집** 메뉴에서 SSL 인증서를 선택한 다음, **인증서 가져오기** 를 선택합니다.</li></ol>|
|다음 오류를 표시하기 전에 AzCopy 명령은 잠시 응답을 중지한 것처럼 표시됩니다.<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `C:\Windows\System32\drivers\etc\hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|다음 오류를 표시하기 전에 AzCopy 명령은 잠시 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |디바이스에 대한 SSL 인증서를 Azure Storage Explorer로 가져옵니다. <ol><li>[인증서를 생성하고 다운로드](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)합니다.</li><li>**편집** 메뉴에서 SSL 인증서를 선택한 다음, **인증서 가져오기** 를 선택합니다.</li></ol>|
|다음 오류를 표시하기 전에 AzCopy 명령은 20분 동안 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `/etc/hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|다음 오류를 표시하기 전에 AzCopy 명령은 20분 동안 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location… The SSL connection could not be established`. |디바이스에 대한 SSL 인증서를 Azure Storage Explorer로 가져옵니다. <ol><li>[인증서를 생성하고 다운로드](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)합니다.</li><li>**편집** 메뉴에서 SSL 인증서를 선택한 다음, **인증서 가져오기** 를 선택합니다.</li></ol>|
|다음 오류를 표시하기 전에 AzCopy 명령은 20분 동안 응답을 중지한 것처럼 표시됩니다.<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|엔드포인트 이름 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`이 `/etc/hosts`의 호스트 파일에 추가되었는지 확인합니다.|
|이 오류를 표시하기 전에 AzCopy 명령이 20분 동안 응답하지 않는 것으로 나타납니다. `Error parsing source location… The SSL connection could not be established`.|디바이스의 SSL 인증서를 시스템의 인증서 저장소로 가져옵니다. 자세한 내용은 [인증서 다운로드](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)를 참조하세요.|
|HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.|Azure Stack Edge에서 Python용 Microsoft Azure Storage 라이브러리의 설치된 버전이 지원되지 않습니다. 지원되는 라이브러리 버전은 [지원되는 Azure 클라이언트 라이브러리](azure-stack-edge-gpu-system-requirements-rest.md#supported-azure-client-libraries)를 참조하세요.|
|… [SSL: 인증서_검증_실패]...| Python을 실행하기 전에 REQUESTS_CA_BUNDLE 환경 변수를 Base64로 인코딩된 SSL 인증서 파일의 경로로 설정합니다([인증서 다운로드](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) 방법 참조). 예를 들어 다음을 실행합니다.<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>또는 인증서를 시스템의 인증서 저장소에 추가한 다음 이 환경 변수를 해당 저장소의 경로로 설정합니다. 예를 들어 Ubuntu에서 다음을 실행합니다.<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`|
|연결 시간 초과|디바이스에 로그인한 후 잠금 해제 여부를 확인합니다. 디바이스가 다시 시작될 때마다 사용자가 로그인할 때까지 잠긴 상태로 유지됩니다.|
|스토리지 계정을 만들거나 업데이트할 수 없습니다. 스토리지 계정의 액세스 키가 유효한지 확인합니다. 필요한 경우 디바이스의 키를 업데이트합니다.|스토리지 계정 키 동기화 [여기](azure-stack-edge-gpu-manage-storage-accounts.md#sync-storage-keys)에서 설명하는 단계를 수행합니다.|

## <a name="next-steps"></a>다음 단계

- [디바이스 업로드 및 새로 고침 오류 문제를 해결](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-device-upload-and-refresh-errors)합니다.
