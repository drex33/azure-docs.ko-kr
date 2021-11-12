---
title: 자격 증명 모음을 다른 지역으로 이동 - Azure Key Vault | Microsoft Docs
description: 이 문서에서는 키 자격 증명 모음을 다른 지역으로 이동하는 방법에 대한 지침을 제공합니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 11/11/2021
ms.author: mbaldwin
ms.custom: subject-moving-resources
ms.openlocfilehash: b8364ae34c8fcef3b65b83d5f2e1851920634edd
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370142"
---
# <a name="move-an-azure-key-vault-across-regions"></a>지역 간에 Azure 키 자격 증명 모음 이동

Azure Key Vault 키 자격 증명 모음을 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 새 지역에서 키 자격 증명 모음을 만들고, 각 개별 키, 비밀 또는 인증서를 기존 키 자격 증명 모음에서 새 키 자격 증명 모음으로 수동으로 복사한 다음, 원래 키 자격 증명 모음을 제거할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

프로덕션 환경에 적용하기 전에 이 해결 방법이 미치는 영향을 이해하는 것이 중요합니다.

## <a name="prepare"></a>준비

먼저 이동하려는 지역에 새 키 자격 증명 모음을 만들어야 합니다. Azure Portal , [Azure CLI](quick-create-portal.md) [또는](quick-create-powershell.md) [Azure PowerShell](quick-create-cli.md)통해 수행할 수 있습니다.

다음과 같은 개념을 염두에 두어야 합니다.

* 키 자격 증명 모음 이름은 전역적으로 고유합니다. 자격 증명 모음 이름을 다시 사용할 수 없습니다.
* 새 키 자격 증명 모음에서 액세스 정책 및 네트워크 구성 설정을 다시 구성해야 합니다.
* 새 키 자격 증명 모음에서 일시 삭제 및 제거 방지를 다시 구성해야 합니다.
* 백업 및 복원 작업은 자동 회전 설정을 유지하지 않습니다. 설정을 다시 구성해야 할 수도 있습니다.

## <a name="move"></a>이동

이전 키 자격 증명 모음에서 키, 비밀 또는 인증서를 내보낸 다음 새 자격 증명 모음으로 가져옵니다. 

백업 명령을 사용하여 각 개별 비밀, 키 및 인증서를 자격 증명 모음에 백업할 수 있습니다. 비밀은 암호화된 Blob으로 다운로드됩니다.  단계별 지침은 [Azure Key Vault 백업 및 복원을](backup.md)참조하세요.

또는 특정 비밀 유형을 수동으로 다운로드할 수 있습니다. 예를 들어 인증서를 PFX 파일로 다운로드할 수 있습니다. 이 옵션은 인증서와 같은 일부 비밀 유형에 대한 지리적 제한을 제거합니다. 모든 지역의 모든 키 자격 증명 모음에 PFX 파일을 업로드할 수 있습니다. 비밀은 암호로 보호되지 않은 형식으로 다운로드됩니다. 이동하는 동안 비밀을 보호해야 합니다.

키, 비밀 또는 인증서를 다운로드한 후 새 키 자격 증명 모음으로 복원합니다. 

백업 및 복원 명령을 사용할 때는 두 가지 제한 사항이 있습니다.

* 키 자격 증명 모음을 한 지역에 백업하고 다른 지역에 복원할 수는 없습니다. 자세한 내용은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조하세요.

* 백업 명령은 각 비밀의 모든 버전을 백업합니다. 이전 버전이 많은 비밀(10개 초과)을 사용하는 경우 요청 크기가 허용되는 최댓값을 초과하여 작업이 실패할 수 있습니다.

## <a name="verify"></a>확인

이전 키 자격 증명 모음을 삭제하기 전에 새 자격 증명 모음에 필요한 키, 비밀 및 인증서가 모두 포함되어 있는지 확인합니다. 


## <a name="next-steps"></a>다음 단계

- [백업 및 복원 Azure Key Vault](backup.md)
- [리소스 그룹 간에 Azure Key Vault 이동](move-resourcegroup.md)
- [다른 구독으로 Azure Key Vault 이동](move-subscription.md)