---
title: Azure Files Virtual Desktop 문제 해결 - Azure
description: Azure Virtual Desktop용 Azure Files의 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 31fe9f55252c00b5475bbb96cef646d0906a05f7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568182"
---
# <a name="troubleshoot-azure-files-authorization"></a>Azure Files 권한 부여 문제 해결

이 문서에서는 Azure AD(Active Directory)를 사용하는 Azure Files 인증과 관련된 일반적인 문제와 이를 해결하는 방법에 대한 제안 사항을 설명합니다.

## <a name="my-group-membership-isnt-working"></a>내 그룹 구성원 자격이 작동하지 않음

VM(가상 머신)을 AD DS(Active Directory Domain Services) 그룹에 추가하는 경우 해당 VM을 다시 시작하여 서비스 내에서 해당 구성원 자격을 활성화해야 합니다.

## <a name="i-cant-add-my-storage-account-to-my-ad-ds"></a>내 AD DS에 스토리지 계정을 추가할 수 없음

먼저 [AD 자격 증명으로 Azure Files를 탑재할 수 없음](../storage/files/storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)을 확인하여 발생하는 문제가 나열되는지 알아봅니다.

다음은 사용자에게 문제가 발생할 수 있는 가장 일반적인 이유입니다.

- PowerShell에서 계정을 만들 때 표시되는 경고 메시지를 무시합니다. 경고를 무시하면 새 계정의 설정이 잘못 구성될 수 있습니다. 이 문제를 해결하려면 스토리지 계정을 나타내는 도메인 계정을 삭제하고 다시 시도해야 합니다.

- 계정이 잘못된 OU(조직 구성 단위)를 사용하고 있습니다. 이 문제를 해결하려면 다음 구문을 사용하여 OU 정보를 다시 입력합니다.
    
    ```powershell
    DC=ouname,DC=domainprefix,DC=topleveldomain
    ```

    예를 들면 다음과 같습니다.

    ```powershell
    DC=storageAccounts,DC=wvdcontoso,DC=com
    ```

- 스토리지 계정이 Azure AD에 즉시 표시되지 않더라도 걱정하지 마세요. 새 스토리지 계정이 Azure AD와 동기화되는 데 일반적으로 30분이 걸리므로 잠시 기다려 주세요. 30분 후에 동기화가 발생하지 않는 경우 [다음 섹션](#my-ad-ds-group-wont-sync-to-azure-ad)을 참조하세요.

## <a name="my-ad-ds-group-wont-sync-to-azure-ad"></a>내 AD DS 그룹이 Azure AD와 동기화되지 않음

스토리지 계정이 30분 후에 Azure AD와 자동으로 동기화되지 않는 경우 [이 스크립트](https://github.com/stgeorgi/msixappattach/blob/master/force%20AD%20DS%20to%20Azure%20AD%20sync/force%20sync.ps1)를 사용하여 강제로 동기화해야 합니다.

## <a name="my-storage-account-says-it-needs-additional-permissions"></a>내 스토리지 계정에 추가 권한이 필요하다고 표시됨

스토리지 계정에 추가 권한이 필요한 경우 MSIX 앱 연결 및 FSLogix에 액세스할 수 있는 권한이 없을 수 있습니다. 이 문제를 해결하려면 다음 권한 중 하나를 계정에 할당했는지 확인합니다.

- **스토리지 파일 데이터 SMB 공유 기여자** RBAC 사용 권한

- **읽기 및 실행** 과 **폴더 내용 나열** NTFS 권한

## <a name="next-steps"></a>다음 단계

Azure Files 설정 프로세스에 대한 메모리를 새로 고쳐야 하는 경우 [Azure Files 계정에 권한 부여](azure-files-authorization.md)를 참조하세요.