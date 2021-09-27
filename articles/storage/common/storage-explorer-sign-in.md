---
title: Azure Storage Explorer에 로그인 | Microsoft Docs
description: Azure Storage Explorer에 로그인하는 방법에 관한 설명서
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: bf7fe9dc4d8c60b7e8779100bd959ee7e85415e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583859"
---
# <a name="sign-in-to-storage-explorer"></a>Storage Explorer에 로그인

로그인은 Storage Explorer를 사용하여 Azure Storage 리소스에 액세스하기 위한 권장 방법입니다. 로그인하면 RBAC 및 Gen2 POSIX ACL과 같은 Azure AD 지원 권한을 활용할 수 있습니다.

## <a name="how-to-sign-in"></a>로그인 방법

Storage Explorer에 로그인하려면 **연결 대화 상자** 를 엽니다. 왼쪽 세로 도구 모음에서 또는 **계정 패널** 에서 **계정 추가...** 를 클릭하여 **연결 대화 상자** 를 열 수 있습니다.

대화 상자가 열리면 연결하려는 리소스 종류로 **구독** 을 선택하고 **다음** 을 클릭합니다.

이제 로그인하려는 Azure 환경을 선택해야 합니다. Azure 또는 Azure 중국과 같은 알려진 환경 중에서 선택하거나 고유한 환경을 추가할 수 있습니다. 환경이 선택되면 **다음** 을 클릭합니다.

이 시점에서 OS의 **기본 웹 브라우저** 가 시작되고 로그인 페이지가 열립니다. 최상의 결과를 얻으려면 Storage Explorer를 사용하는 동안 또는 최소한 필요한 모든 MFA를 수행할 때까지 이 브라우저 창을 열어 둡니다. 로그인이 완료되면 Storage Explorer로 돌아갈 수 있습니다.

## <a name="managing-accounts"></a>계정 관리

**계정 패널** 에서 로그인한 Azure 계정을 관리 및 제거할 수 있습니다. 왼쪽 세로 도구 모음에서 **계정 관리** 단추를 클릭하여 **계정 패널** 을 열 수 있습니다.

**계정 패널** 에 로그인한 계정이 표시됩니다. 각 계정 아래에 다음 항목이 있습니다.
- 계정이 속한 테넌트
- 각 테넌트에 대해 액세스 권한이 있는 구독

기본적으로 Storage Explorer에서는 홈 테넌트에만 로그인됩니다. 다른 테넌트의 구독과 리소스를 보려면 해당 테넌트를 활성화해야 합니다. 테넌트를 활성화하려면 해당 테넌트 옆의 확인란을 선택합니다. 테넌트 작업을 완료한 후 해당 확인란의 선택을 취소하여 비활성화할 수 있습니다. 홈 테넌트를 비활성화할 수는 없습니다.

테넌트를 활성화한 후 Storage Explorer가 구독을 로드하거나 테넌트의 리소스에 액세스하려면 먼저 사용자가 자격 증명을 다시 입력해야 할 수 있습니다. 일반적으로 MFA(다단계 인증)와 같은 CA(조건부 액세스) 정책 때문에 자격 증명을 다시 입력해야 합니다. 또한 다른 테넌트에 대해 이미 MFA를 수행한 경우라도 해당 작업을 다시 수행해야 합니다. 자격 증명을 다시 입력하려면 **자격 증명 다시 입력...** 을 클릭하면 됩니다. 또한 **오류 세부 정보...** 를 클릭하여 구독을 로드하지 못한 이유를 정확하게 확인할 수 있습니다.

구독이 로드된 후 해당 확인란을 선택하거나 선택 취소하여 필터링하려는 항목을 선택할 수 있습니다.

전체 Azure 계정을 제거하려면 계정 옆의 **제거** 를 클릭합니다.

## <a name="changing-where-sign-in-happens"></a>로그인이 수행되는 위치 변경

기본적으로 로그인은 OS의 **기본 웹 브라우저** 에서 수행됩니다. 기본 웹 브라우저를 사용하여 로그인하면 MFA와 같은 CA 정책을 통해 보호된 리소스에 액세스하는 방법이 간소화됩니다. 어떤 이유로 OS의 **기본 웹 브라우저** 를 사용한 로그인이 작동하지 않는 경우 Storage Explorer가 로그인을 수행하는 위치 또는 방법을 변경할 수 있습니다.

**설정(왼쪽의 기어 아이콘)**  > **애플리케이션** > **로그인** 에서 **다음으로 로그인** 설정을 찾습니다. 세 개의 옵션이 있습니다.
- **기본 웹 브라우저**: 로그인은 OS의 **기본 웹 브라우저** 에서 수행됩니다. 이 옵션을 사용하는 것이 좋습니다.
- **통합 로그인**: 로그인이 Storage Explorer 창에서 수행됩니다. 이 옵션은 **기본 웹 브라우저** 를 사용하여 로그인하는 데 문제가 있는 경우 유용할 수 있습니다.
- **디바이스 코드 흐름**: Storage Explorer가 브라우저 창에 입력할 코드를 제공합니다. 이 옵션은 권장되지 않습니다. 디바이스 코드 흐름은 많은 CA 정책과 호환되지 않습니다.

## <a name="troubleshooting-sign-in-issues"></a>로그인 문제 해결

로그인하는 데 문제가 있거나 로그인한 후에 Azure 계정에 문제가 있는 경우 [Storage Explorer 문제 해결 가이드의 로그인 섹션](./storage-explorer-troubleshooting.md#sign-in-issues)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md)
- [로그인 문제 해결](./storage-explorer-troubleshooting.md#sign-in-issues)
