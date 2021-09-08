---
title: Azure Key Vault용 클라이언트 라이브러리 | Microsoft Docs
description: Azure Key Vault용 클라이언트 라이브러리
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 5b4057a15493581dfd6216022a280d742d9561e0
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480771"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault용 클라이언트 라이브러리

Azure Key Vault용 클라이언트 라이브러리를 사용하면 .NET, Python, Java 및 JavaScript를 비롯한 다양한 언어에서 Key Vault 기능에 프로그래밍 방식으로 액세스할 수 있습니다.

## <a name="client-libraries-per-language-and-object"></a>언어 및 개체당 클라이언트 라이브러리

각 SDK에는 아래 표에 따라 키 자격 증명 모음, 비밀, 키 및 인증서에 대한 별도의 클라이언트 라이브러리가 있습니다.

| 언어 | 비밀 | 구성 | 인증서 | Key Vault(관리 평면) |
|--|--|--|--|--|
| .NET | - [API 참조](/dotnet/api/azure.security.keyvault.secrets)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [빠른 시작](../secrets/quick-create-net.md) | - [API 참조](/dotnet/api/azure.security.keyvault.keys)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [빠른 시작](../keys/quick-create-net.md) | - [API 참조](/dotnet/api/azure.security.keyvault.certificates)<br>- [NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [빠른 시작](../certificates/quick-create-net.md) | - [API 참조](/dotnet/api/microsoft.azure.management.keyvault)<br>- [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API 참조](/python/api/overview/azure/keyvault-secrets-readme)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-secrets/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-python.md) |- [API 참조](/python/api/overview/azure/keyvault-keys-readme)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-keys/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [빠른 시작](../keys/quick-create-python.md) | - [API 참조](/python/api/overview/azure/keyvault-certificates-readme)<br>- [PyPi 패키지](https://pypi.org/project/azure-keyvault-certificates/)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [빠른 시작](../certificates/quick-create-python.md) | - [API 참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [PyPi 패키지](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-java.md) |- [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [빠른 시작](../keys/quick-create-java.md) | - [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [빠른 시작](../certificates/quick-create-java.md) |- [API 참조](/java/api/com.microsoft.azure.management.keyvault)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/keyvault/microsoft-azure-keyvault)|
| Node.js | - [API 참조](/javascript/api/@azure/keyvault-secrets/)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [빠른 시작](../secrets/quick-create-node.md) |- [API 참조](/javascript/api/@azure/keyvault-keys/)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [빠른 시작](../keys/quick-create-node.md)| - [API 참조](/javascript/api/@azure/keyvault-certificates/)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [빠른 시작](../certificates/quick-create-node.md) |  - [API 참조](/javascript/api/@azure/arm-keyvault/)<br>- [npm 패키지](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개발자 가이드](developers-guide.md) 참조
- [Key Vault에 인증](authentication.md)에 대해 자세히 알아보세요.
- [Key Vault에 대한 액세스 보안](security-features.md)에 대해 자세히 알아보세요.
