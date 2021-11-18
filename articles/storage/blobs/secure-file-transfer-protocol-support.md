---
title: Azure Blob Storage(미리 보기) | 대한 SFTP(Secure 파일 전송 프로토콜) 지원 Microsoft Docs
description: Blob Storage는 이제 SFTP(Secure 파일 전송 프로토콜)를 지원합니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 847001d7a01e29ae40db73080efcae19517544e3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751216"
---
# <a name="ssh-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Azure Blob Storage(미리 보기)에 대한 SFTP(SSH 파일 전송 프로토콜) 지원

Blob Storage는 이제 SFTP(SSH 파일 전송 프로토콜)를 지원합니다. 이 지원은 SFTP 엔드포인트를 통해 Blob Storage 계정에 안전하게 연결하는 기능을 제공하며, 이를 통해 파일 액세스, 파일 전송 및 파일 관리에 SFTP를 활용할 수 있습니다.  

> [!IMPORTANT]
> SFTP 지원은 현재 미리 보기로 제공되며 이러한 지역에서 사용할 수 [있습니다.](secure-file-transfer-protocol-support.md#regional-availability)
>
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/r/gZguN0j65Y)을 참조하세요.

Azure를 사용하면 Azure Blob service REST API, Azure SDK 및 AzCopy와 같은 도구를 사용하여 Blob Storage 계정으로 안전하게 데이터를 전송할 수 있습니다. 그러나 레거시 워크로드는 종종 SFTP와 같은 기존 파일 전송 프로토콜을 사용합니다. REST API 및 Azure SDK를 사용하도록 사용자 지정 애플리케이션을 업데이트할 수 있지만 중요한 코드를 변경해야 합니다.

이 기능이 릴리스되기 전에 SFTP를 사용하여 Azure Blob Storage 데이터를 전송하려는 경우 타사 제품을 구입하거나 고유한 솔루션을 오케스트레이션해야 합니다. SFTP 서버를 호스트하기 위해 Azure에서 VM(가상 머신)을 만든 다음, 스토리지 계정으로 데이터를 이동하는 방법을 파악해야 합니다. 

이제 Azure Blob Storage 대한 SFTP 지원을 통해 단일 설정으로 Blob Storage 계정에 대한 SFTP 엔드포인트를 사용하도록 설정할 수 있습니다. 그런 다음 추가 작업을 수행할 필요 없이 데이터를 안전하게 전송하도록 인증을 위한 로컬 사용자 ID를 설정할 수 있습니다. 

이 문서에서는 Azure Blob Storage 대한 SFTP 지원에 대해 설명합니다. 스토리지 계정에 SFTP를 사용하도록 설정하는 방법을 알아보려면 [SFTP(보안 파일 전송) 프로토콜(미리 보기)을 사용하여 Azure Blob Storage](secure-file-transfer-protocol-support-how-to.md)커넥트 참조하세요.

## <a name="sftp-and-the-hierarchical-namespace"></a>SFTP 및 계층 구조 네임스페이스

SFTP 지원을 사용하려면 계층 구조 네임스페이스에서 Blob을 로 구성해야 합니다. 계층 구조 네임스페이스를 사용하는 기능은 Azure Data Lake Storage Gen2에서 도입되었습니다. 이렇게 하면 개체(파일)가 컴퓨터의 파일 시스템이 구성되는 것과 동일한 방식으로 디렉터리 및 하위 디렉터리의 계층 구조로 구성됩니다. 계층 구조 네임스페이스는 선형으로 확장되며 데이터 용량 또는 성능을 저하시키지 않습니다. 

계층 구조 네임스페이스에서 서로 다른 프로토콜이 확장됩니다. SFTP는 이러한 사용 가능한 프로토콜 중 하나입니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스](./media/secure-file-transfer-protocol-support/hierarchical-namespace-and-sftp-support.png)

## <a name="sftp-permissions-model"></a>SFTP 사용 권한 모델

Azure Storage SFTP 클라이언트 연결에 대한 SAS(공유 액세스 서명) 또는 Azure AD(Azure Active Directory) 인증을 지원하지 않습니다. 대신 SFTP 클라이언트는 암호 또는 SSH(Secure Shell) 프라이빗 키 자격 증명을 사용해야 합니다.

연결하는 클라이언트에 대한 액세스 권한을 부여하려면 스토리지 계정에 해당 자격 증명과 연결된 ID가 있어야 합니다. 이 ID를 로컬 사용자라고 부립니다. 로컬 사용자는 SFTP 지원과 함께 제공되는 새로운 형태의 ID 관리입니다. 스토리지 계정에 1,000명의 로컬 사용자를 추가할 수 있습니다.

액세스 권한을 설정하려면 로컬 사용자를 만들고 인증 방법을 선택합니다. 그런 다음 계정의 각 컨테이너에 대해 해당 사용자에게 제공하려는 액세스 수준을 지정할 수 있습니다.
 
> [!NOTE]
> 데이터가 Azure Storage 수집되면 전체 Azure Storage 보안 설정을 사용할 수 있습니다. RBAC(역할 기반 액세스 제어) 및 액세스 제어 목록과 같은 권한 부여 메커니즘은 연결하는 SFTP 클라이언트에 권한을 부여하는 수단으로 지원되지 않지만 Azure SDKS 및 Azure REST API뿐만 아니라 Azure 도구(예: Azure Portal, Azure CLI, Azure PowerShell 명령 및 AzCopy)를 통해 액세스 권한을 부여하는 데 사용할 수 있습니다. 
> 
> 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어 모델을 참조하세요.](data-lake-storage-access-control-model.md)

## <a name="authentication-methods"></a>인증 방법

암호 또는 SSH(Secure Shell) 퍼블릭-프라이빗 키페어를 사용하여 연결하는 SFTP 클라이언트를 인증할 수 있습니다. 두 가지 형태의 인증을 모두 구성하고 연결 클라이언트에서 사용할 인증을 선택하도록 할 수 있습니다. 그러나 성공적인 인증을 위해 유효한 암호와 유효한 퍼블릭-프라이빗 키 쌍이 모두 필요한 다단계 인증은 지원되지 않습니다. 

#### <a name="passwords"></a>암호

암호가 생성됩니다. 암호 인증을 선택하면 로컬 사용자 구성을 완료한 후 암호가 제공됩니다. 해당 암호를 복사하고 나중에 찾을 수 있는 위치에 저장해야 합니다. Azure에서 해당 암호를 다시 검색할 수 없습니다. 암호를 분실한 경우 새 암호를 생성해야 합니다. 보안상의 이유로 암호를 직접 설정할 수 없습니다.   

#### <a name="ssh-key-pairs"></a>SSH 키 쌍

퍼블릭-프라이빗 키 쌍은 SSH(Secure Shell)에 대한 가장 일반적인 인증 형식입니다. 프라이빗 키는 비밀이며 귀하에게만 알려져 있습니다. 공개 키는 Azure에 저장됩니다. SSH 클라이언트는 스토리지 계정에 연결하면 프라이빗 키와 서명이 있는 메시지를 보냅니다. Azure는 메시지의 유효성을 검사하고 사용자와 키가 스토리지 계정에서 인식되고 있는지 확인합니다. 자세한 내용은 [SSH 및 키 개요를 참조하세요.](../../virtual-machines/linux/ssh-from-windows.md#)

프라이빗-퍼블릭 키 쌍을 사용하여 인증하도록 선택하는 경우 하나를 생성하거나, Azure에 이미 저장된 키를 사용하거나, Azure에 기존 퍼블릭-프라이빗 키 쌍의 공개 키를 제공할 수 있습니다. 

## <a name="container-permissions"></a>컨테이너 권한

현재 릴리스에서는 컨테이너 수준 권한만 지정할 수 있습니다. 디렉터리 수준 권한은 지원되지 않습니다. 액세스 권한을 부여할 컨테이너와 제공하려는 액세스 수준(읽기, 쓰기, 나열, 삭제 및 만들기)을 선택할 수 있습니다. 이러한 권한은 컨테이너의 모든 디렉터리 및 하위 디렉터리에 적용됩니다. 각 로컬 사용자에게 최대 100개의 컨테이너에 대한 액세스 권한을 부여할 수 있습니다. 로컬 사용자를 만든 후 컨테이너 권한을 업데이트할 수도 있습니다. 다음 표에서는 각 사용 권한에 대해 자세히 설명합니다.

| 사용 권한 | 권한 코드 | Description |
|---|---|---|
| Read | r | <li>파일 내용 읽기</li> |
| 쓰기 | w | <li>파일 업로드</li><li>디렉터리 만들기</li><li>업로드 디렉터리</li> |
| 목록 | l | <li>컨테이너 내의 콘텐츠 나열</li><li>디렉터리 내의 콘텐츠 나열</li> |
| DELETE | 일 | <li>파일/디렉터리 삭제</li> |
| 생성 | c | <li>파일이 없는 경우 파일 업로드</li><li>디렉터리(없는 경우) 만들기</li><li>디렉터리 만들기</li>|

## <a name="home-directory"></a>홈 디렉터리

권한을 구성할 때 로컬 사용자에 대한 홈 디렉터리를 설정할 수 있습니다. SFTP 연결 요청에 다른 컨테이너가 지정되지 않은 경우 기본적으로 사용자가 연결하는 디렉터리입니다. 예를 들어 [Open SSH](/windows-server/administration/openssh/openssh_overview)를 사용하여 다음 요청을 수행합니다. 이 요청은 명령의 일부로 컨테이너 또는 디렉터리 이름을 지정하지 `sftp` 않습니다.

```powershell
sftp myaccount.myusername@myaccount.blob.core.windows.net
put logfile.txt
```

사용자의 홈 디렉터리를 로 설정하면 `mycontainer/mydirectory` 클라이언트가 해당 디렉터리에 연결합니다. 그런 다음 `logfile.txt` 파일에 `mycontainer/mydirectory` 업로드됩니다. 홈 디렉터리를 설정하지 않은 경우 연결 시도가 실패합니다. 대신 연결 클라이언트는 요청과 함께 컨테이너를 지정한 다음 SFTP 명령을 사용하여 파일을 업로드하기 전에 대상 디렉터리로 이동해야 합니다. 다음 예제에서는 이러한 방법을 보여줍니다.

```powershell
sftp myaccount.mycontainer.myusername@myaccount.blob.core.windows.net
cd mydirectory
put logfile.txt  
```

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

SFTP 지원의 현재 릴리스와 관련된 문제 및 제한 사항의 전체 목록은 [알려진 문제](secure-file-transfer-protocol-known-issues.md) 문서를 참조하세요.

## <a name="regional-availability"></a>국가별 가용성

SFTP 지원은 다음 지역에서 제공 됩니다. 

- 미국 중북부
- 미국 동부 2
- 미국 동부 2 EUAP
- 미국 중부 EUAP
- 캐나다 동부
- 캐나다 중부
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
