---
title: 스토리지 방화벽 설정 해결
description: Azure HPC Cache에서 Azure Blob 스토리지 대상을 만들 때, 스토리지 계정 네트워크 방화벽 설정이 실패할 수 있습니다. 이 문서는 소프트웨어 픽스가 적용될 때까지 제한 사항에 대한 해결 방법을 설명합니다.
author: femila
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: femila
ms.openlocfilehash: 11b5a28f35250a5d600cf849a3bd7b1a5bbe262e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068439"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Blob 스토리지 계정 방화벽 설정 해결

스토리지 계정 방화벽에 사용되는 특정 설정으로 인해 Blob 스토리지 대상을 생성하지 못할 수 있습니다. Azure HPC Cache 팀은 해당 문제에 대해 소프트웨어 픽스 작업을 진행 중이지만, 이 문서의 지침에 따라 문제를 해결할 수 있습니다.

“선택한 네트워크”에서만 액세스할 수 있도록 방화벽을 설정하면 캐시가 Blob 스토리지 대상을 생성하거나 수정하는 것을 방지할 수 있습니다. 해당 구성은 스토리지 계정의 **방화벽 및 가상 네트워크** 설정 페이지에 있습니다. (이 문제는 ADLS-NFS 스토리지 대상에 적용되지 않습니다.)

문제는 캐시 서비스가 고객 환경과 분리된 숨겨진 서비스 가상 네트워크를 사용한다는 것입니다. 해당 네트워크가 내 스토리지 계정에 액세스하도록 명시적으로 권한을 부여할 수 없습니다.

Blob 스토리지 대상을 생성하면 캐시 서비스는 이 네트워크를 사용하여 컨테이너가 비어 있는지를 확인합니다. 방화벽이 숨겨진 네트워크에서 액세스하는 것을 허용하지 않는다면, 확인을 할 수 없고 스토리지 대상을 생성할 수 없습니다.

또한 방화벽은 Blob 스토리지 대상 네임스페이스 경로에 대한 변경을 차단할 수 있습니다.

문제를 해결하려면 스토리지 대상을 생성하는 동안 방화벽 설정을 일시적으로 변경하세요.

1. 스토리지 계정 **방화벽 및 가상 네트워크** 페이지로 이동하여 “액세스 허용” 설정을 **모든 네트워크** 로 변경합니다.
1. Azure HPC Cache에서 Blob 스토리지 대상을 만듭니다.
1. 스토리지 대상의 네임스페이스 경로를 만듭니다.
1. 스토리지 대상과 경로를 성공적으로 만든 후에는 계정의 방화벽 설정을 **선택한 네트워크** 로 다시 변경합니다.

Azure HPC Cache는 완성된 스토리지 대상에 액세스하기 위해 서비스 가상 네트워크를 사용하지 않습니다.

해당 해결 방법에 대한 자세한 내용은 [Microsoft 서비스 및 지원](hpc-cache-support-ticket.md)에 문의하세요.
