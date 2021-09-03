---
title: AD DS 스토리지 계정 암호 업데이트
description: 스토리지 계정을 나타내는 Active Directory Domain Services 계정의 암호를 업데이트하는 방법을 알아봅니다. 이렇게 하면 암호가 만료될 때 스토리지 계정이 정리되지 않아 인증 실패가 방지됩니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: f54a2e7471917adb604da29e3b7fcc260ac521e0
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729485"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>AD DS에서 스토리지 계정 ID의 암호 업데이트

암호 만료 시간을 적용하는 조직 구성 단위 또는 도메인에서 스토리지 계정을 나타내는 AD DS(Active Directory Domain Services) ID/계정을 등록한 경우 최대 암호 사용 기간 전에 암호를 변경해야 합니다. 조직에서 암호가 만료되면 계정을 삭제하는 자동화된 정리 스크립트를 실행할 수 있습니다. 이런 이유로, 암호가 만료되기 전에 암호를 변경하지 않으면 계정이 삭제되어 Azure 파일 공유에 액세스할 수 없게 됩니다.

암호 회전을 트리거하려면 [AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases) 모듈에서 `Update-AzStorageAccountADObjectPassword` 명령을 실행하면 됩니다. 스토리지 계정에 대한 소유자 권한 및 스토리지 계정을 나타내는 ID의 암호를 변경할 수 있는 AD DS 권한을 가진 하이브리드 사용자를 사용하여 온-프레미스 AD DS 조인 환경에서 이 명령을 실행해야 합니다. 이 명령은 스토리지 계정 키 회전과 유사한 작업을 수행합니다. 특히, 스토리지 계정의 두 번째 Kerberos 키를 가져오고 이를 사용하여 AD DS에서 등록된 계정의 암호를 업데이트합니다. 그런 다음, 스토리지 계정의 대상 Kerberos 키를 다시 생성하고 AD DS에서 등록된 계정의 암호를 업데이트합니다. 이 명령은 온-프레미스 AD DS 조인 환경에서 실행해야 합니다.

암호 회전을 방지하려면 도메인에서 Azure Storage 계정을 온보딩하는 동안 Azure Storage 계정을 AD DS의 별도 조직 구성 단위에 배치해야 합니다. 기본 도메인 정책이나 특정 암호 정책이 적용되지 않도록 하려면 이 조직 구성 단위에서 그룹 정책 상속을 사용하지 않도록 설정합니다.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |