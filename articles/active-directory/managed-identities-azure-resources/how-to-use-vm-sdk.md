---
title: Azure SDK를 사용하여 Azure VM에서 관리 ID 사용 - Azure AD
description: Azure 리소스에 대한 관리 ID가 있는 Azure VM으로 Azure SDK를 사용하기 위한 코드 샘플입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85629f53cdd2753eca5574d627a01dd06ea49510
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749510"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Azure SDK를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하는 방법 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
이 문서에서는 Azure 리소스에 대한 관리 ID에 해당하는 Azure SDK 지원을 사용하는 방법을 보여주는 SDK 샘플 목록을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 이 문서의 모든 샘플 코드/스크립트는 Azure 리소스에 대한 관리 ID를 사용하는 VM에서 클라이언트가 실행되고 있다고 가정합니다. Azure Portal에서 VM "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 Azure 리소스에 대한 관리 ID 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 

## <a name="sdk-code-samples"></a>SDK 코드 샘플

| SDK)             | 코드 샘플 |
| --------------- | ----------- |
| .NET            | [Azure 리소스에 대한 관리 ID를 사용하여 Windows VM에서 Azure Resource Manager 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure 리소스에 대한 관리 ID를 사용하여 Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Go              | [Go용 Azure ID 클라이언트 모듈](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/azidentity#ManagedIdentityCredential)
| Node.js         | [Azure 리소스에 대한 관리 ID를 사용하는 리소스 관리](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Azure 리소스에 대한 관리 ID를 사용하여 VM 내부에서 간단하게 인증](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Azure 리소스에 대한 관리 ID를 사용하는 VM에서 리소스 관리](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>다음 단계

- 라이브러리 다운로드, 문서 등을 비롯한 Azure SDK 리소스의 전체 목록은 [Azure SDK](https://azure.microsoft.com/downloads/)를 참조하세요.
- Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하려면 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md)을 참조하세요.








