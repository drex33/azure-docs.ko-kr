---
title: Azure Blob Storage (미리 보기)에 대 한 SFTP (Secure 파일 전송 프로토콜) 지원 | Microsoft Docs
description: 이제 Blob storage는 SFTP (Secure 파일 전송 프로토콜)를 지원 합니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 0cfd4a8dc9968ad9ef9e7538ac51414deb974fdb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556937"
---
# <a name="secure-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Azure Blob Storage (미리 보기)에 대 한 SFTP (Secure 파일 전송 프로토콜) 지원

이제 Blob storage는 SFTP (Secure 파일 전송 프로토콜)를 지원 합니다. 이 지원은 sftp 끝점을 통해 Blob Storage 계정에 안전 하 게 연결 하는 기능을 제공 하 여 파일 액세스, 파일 전송 및 파일 관리를 위해 sftp를 활용할 수 있도록 합니다.  

> [!IMPORTANT]
> SFTP 지원은 현재 미리 보기로 제공 되며 [이러한 지역](secure-file-transfer-protocol-support.md#regional-availability)에서 사용할 수 있습니다.
>
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/r/gZguN0j65Y)을 참조하세요.

azure를 사용 하면 azure Blob service REST API, azure sdk 및 AzCopy와 같은 도구를 사용 하 여 Blob Storage 계정으로 보안 데이터를 전송할 수 있습니다. 그러나 레거시 작업은 SFTP와 같은 기존의 파일 전송 프로토콜을 사용 하는 경우가 많습니다. REST API 및 Azure Sdk를 사용 하도록 사용자 지정 응용 프로그램을 업데이트 하 고 코드를 크게 변경할 수 있습니다.

이 기능을 출시 하기 전에 SFTP를 사용 하 여 Azure Blob Storage 데이터를 전송 하려는 경우 타사 제품을 구매 하거나 사용자 고유의 솔루션을 오케스트레이션 해야 합니다. Azure에서 SFTP 서버를 호스트 하는 VM (가상 머신)을 만든 다음 저장소 계정으로 데이터를 이동 하는 방법을 파악 해야 합니다. 

이제 Azure Blob Storage에 대 한 sftp 지원을 통해 단일 설정을 사용 하 여 Blob Storage 계정에 대해 sftp 끝점을 사용 하도록 설정할 수 있습니다. 그런 다음 추가 작업을 수행할 필요 없이 안전 하 게 데이터를 전송 하는 인증을 위해 로컬 사용자 id를 설정할 수 있습니다. 

이 문서에서는 Azure Blob Storage에 대 한 SFTP 지원에 대해 설명 합니다. 저장소 계정에 sftp를 사용 하도록 설정 하는 방법을 알아보려면 [sftp (Secure File Transfer) 프로토콜 (미리 보기)을 사용 하 여 Azure Blob Storage 커넥트을](secure-file-transfer-protocol-support-how-to.md)참조 하세요.

## <a name="sftp-and-the-hierarchical-namespace"></a>SFTP 및 계층적 네임 스페이스

SFTP 지원을 사용 하려면 blob을 계층적 네임 스페이스에 맞게 구성 해야 합니다. 계층 구조 네임스페이스를 사용하는 기능은 Azure Data Lake Storage Gen2에서 도입되었습니다. 이렇게 하면 개체(파일)가 컴퓨터의 파일 시스템이 구성되는 것과 동일한 방식으로 디렉터리 및 하위 디렉터리의 계층 구조로 구성됩니다. 계층 구조 네임스페이스는 선형으로 확장되며 데이터 용량 또는 성능을 저하시키지 않습니다. 

계층 구조 네임스페이스에서 서로 다른 프로토콜이 확장됩니다. SFTP는 이러한 사용 가능한 프로토콜 중 하나입니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스](./media/secure-file-transfer-protocol-support/hierarchical-namespace-and-sftp-support.png)

## <a name="sftp-permissions-model"></a>SFTP 권한 모델

Azure Storage는 SFTP 클라이언트를 연결 하는 데 사용 되는 SAS (공유 액세스 서명) 또는 azure AD (azure Active directory) 인증을 지원 하지 않습니다. 대신 SFTP 클라이언트는 암호 또는 SSH (보안 셸) 개인 키 자격 증명을 사용 해야 합니다.

연결 하는 클라이언트에 대 한 액세스 권한을 부여 하려면 저장소 계정에 해당 자격 증명과 연결 된 id가 있어야 합니다. 해당 id를 로컬 사용자 라고 합니다. 로컬 사용자는 SFTP 지원과 함께 제공 되는 새로운 형식의 id 관리입니다. 저장소 계정에 1000 로컬 사용자를 추가할 수 있습니다.

액세스 권한을 설정 하려면 로컬 사용자를 만들고 인증 방법을 선택 합니다. 그런 다음 계정의 각 컨테이너에 대해 해당 사용자에 게 부여할 액세스 수준을 지정할 수 있습니다.
 
> [!NOTE]
> 데이터가 Azure Storage 수집 되 면 Azure Storage 보안 설정의 전체 범위를 사용할 수 있습니다. RBAC (역할 기반 액세스 제어) 및 액세스 제어 목록과 같은 권한 부여 메커니즘은 연결 된 SFTP 클라이언트를 인증 하는 수단으로 지원 되지 않지만, azure 도구 (Azure Portal, Azure CLI, Azure PowerShell 명령 및 AzCopy) 뿐만 아니라 azure sdk 및 azure REST api를 통해 액세스 권한을 부여 하는 데 사용할 수 있습니다. 
> 
> 자세히 알아보려면 [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control-model.md) 을 참조 하세요.

## <a name="authentication-methods"></a>인증 방법

암호 또는 SSH (보안 셸) 공개/개인 키 쌍을 사용 하 여 SFTP 클라이언트 연결을 인증할 수 있습니다. 인증의 두 가지 인증을 모두 구성 하 고 클라이언트 연결에서 사용할 항목을 선택할 수 있습니다. 그러나 성공적인 인증을 위해 유효한 암호와 유효한 공개/개인 키 쌍이 모두 필요한 다단계 인증은 지원 되지 않습니다. 

#### <a name="passwords"></a>암호

암호가 생성 됩니다. 암호 인증을 선택 하는 경우 로컬 사용자 구성을 마친 후 암호가 제공 됩니다. 해당 암호를 복사 하 고 나중에 찾을 수 있는 위치에 저장 해야 합니다. Azure에서 해당 암호를 다시 검색할 수 없습니다. 암호를 분실 한 경우 새 암호를 생성 해야 합니다. 보안상의 이유로 암호를 직접 설정할 수 없습니다.   

#### <a name="ssh-key-pairs"></a>SSH 키 쌍

공개-개인 키 쌍은 SSH (Secure Shell)에 대 한 가장 일반적인 형태의 인증입니다. 개인 키는 비밀 이며 사용자 에게만 알려집니다. 공개 키는 Azure에 저장 됩니다. SSH 클라이언트는 저장소 계정에 연결할 때 개인 키와 서명이 포함 된 메시지를 보냅니다. Azure는 메시지의 유효성을 검사 하 고 저장소 계정에서 사용자 및 키가 인식 되는지 확인 합니다. 자세히 알아보려면 [SSH 및 키 개요](/azure/virtual-machines/linux/ssh-from-windows##overview-of-ssh-and-keys)를 참조 하세요.

개인-공개 키 쌍을 사용 하 여 인증 하도록 선택 하는 경우 하나를 생성 하거나, Azure에 이미 저장 된 항목을 사용 하거나, 기존 공개-개인 키 쌍의 공개 키를 Azure에 제공할 수 있습니다. 

## <a name="container-permissions"></a>컨테이너 권한

현재 릴리스에서는 컨테이너 수준 사용 권한만 지정할 수 있습니다. 디렉터리 수준 권한은 지원 되지 않습니다. 액세스 권한을 부여 하려는 컨테이너와 제공할 액세스 수준 (읽기, 쓰기, 나열, 삭제 및 만들기)을 선택할 수 있습니다. 이러한 권한은 컨테이너의 모든 디렉터리와 하위 디렉터리에 적용 됩니다. 각 로컬 사용자에 게 최대 100의 컨테이너에 대 한 액세스 권한을 부여할 수 있습니다. 로컬 사용자를 만든 후에도 컨테이너 권한을 업데이트할 수 있습니다. 다음 표에서는 각 사용 권한에 대해 자세히 설명 합니다.

| 사용 권한 | 권한 코드 | Description |
|---|---|---|
| 읽기 | r | <li>파일 콘텐츠 읽기</li> |
| 쓰기 | w | <li>파일 업로드</li><li>디렉터리 만들기</li><li>업로드 디렉터리</li> |
| 목록 | l | <li>컨테이너 내 내용 나열</li><li>디렉터리 내 내용 나열</li> |
| 삭제 | 일 | <li>파일/디렉터리 삭제</li> |
| 만들기 | c | <li>파일이 없는 경우 파일 업로드</li><li>디렉터리가 없는 경우 디렉터리를 만듭니다.</li><li>디렉터리 만들기</li>|

## <a name="home-directory"></a>홈 디렉터리

사용 권한을 구성할 때 로컬 사용자의 홈 디렉터리를 설정 하는 옵션이 있습니다. SFTP 연결 요청에 다른 컨테이너가 지정 되지 않은 경우 기본적으로 사용자가 연결 하는 디렉터리입니다. 예를 들어 [OPEN SSH](/windows-server/administration/openssh/openssh_overview)를 사용 하 여 다음 요청을 수행할 수 있습니다. 이 요청은 명령의 일부로 컨테이너 또는 디렉터리 이름을 지정 하지 않습니다 `sftp` .

```powershell
sftp myaccount.myusername@myaccount.blob.core.windows.net
put logfile.txt
```

사용자의 홈 디렉터리를로 설정 하면 `mycontainer/mydirectory` 클라이언트에서 해당 디렉터리에 연결 합니다. 그런 다음 `logfile.txt` 에 파일을 업로드 `mycontainer/mydirectory` 합니다. 홈 디렉터리를 설정 하지 않은 경우 연결 시도가 실패 합니다. 대신 연결 클라이언트는 요청과 함께 컨테이너를 지정 하 고 SFTP 명령을 사용 하 여 파일을 업로드 하기 전에 대상 디렉터리로 이동 해야 합니다. 다음 예제에서는 이러한 방법을 보여줍니다.

```powershell
sftp myaccount.mycontainer.myusername@myaccount.blob.core.windows.net
cd mydirectory
put logfile.txt  
```

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

현재 SFTP 지원 릴리스와 관련 된 문제 및 제한 사항에 대 한 전체 목록은 [알려진 문제](secure-file-transfer-protocol-known-issues.md) 문서를 참조 하세요.

## <a name="regional-availability"></a>국가별 가용성

SFTP 지원은 다음 지역에서 제공 됩니다. 

- 미국 중북부
- 미국 동부 2
- 미국 동부 2 EUAP
- 미국 중부 EUAP
- 캐나다 동부
- 캐나다 중부
- 서유럽
- 북유럽
- 오스트레일리아 동부
- 스위스 북부
- 독일 중서부
- 동아시아
- 프랑스 중부

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

> [!IMPORTANT]
> 공개 미리 보기 동안 SFTP를 사용 하는 경우 추가 요금이 발생 하지 않습니다. 그러나 기본 Azure Data Lake Store Gen2 계정에 대 한 표준 트랜잭션, 저장소 및 네트워킹 가격은 여전히 적용 됩니다. 기능이 일반 공급 되 면 SFTP는 추가 요금을 발생 시킬 수 있습니다.  

트랜잭션 및 저장소 비용은 저장소 계정 유형, 저장소 계정에 데이터를 전송 하는 데 사용 하는 끝점 등의 요소를 기반으로 합니다. 자세히 알아보려면 [Azure Blob Storage의 전체 청구 모델 이해](../common/storage-plan-manage-costs.md#understand-the-full-billing-model-for-azure-blob-storage)를 참조 하세요.

## <a name="see-also"></a>추가 정보

- [SFTP (Secure File Transfer) 프로토콜 (미리 보기)을 사용 하 여 Azure Blob Storage 커넥트](secure-file-transfer-protocol-support-how-to.md)
- [Azure Blob Storage의 SFTP (Secure File Transfer) 프로토콜 지원에 대 한 알려진 문제 (미리 보기)](secure-file-transfer-protocol-known-issues.md)