---
title: Azure AD B2C 사용자 흐름을 수정하기 위한 API 샘플
description: API 커넥터로 사용자 흐름을 수정하기 위한 코드 샘플
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 07/16/2021
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d4a807d725caa224eba67ee7419b91753dcc439c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038045"
---
# <a name="api-connector-rest-api-samples"></a>API 커넥터 REST API 샘플

다음 표에서는 [API 커넥터](api-connectors-overview.md)를 사용하여 사용자 흐름에서 웹 API를 사용하기 위한 코드 샘플에 대한 링크를 제공합니다. 이러한 샘플은 기본적으로 기본 제공 사용자 흐름과 함께 사용하도록 설계되었습니다.

## <a name="azure-function-quickstarts"></a>Azure 함수 빠른 시작
| 샘플                                                                                                                          | Description                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | 이 .NET Core Azure 함수 샘플은 특정 이메일 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다. |
| [Node.JS](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | 이 Node.js Azure 함수 샘플은 특정 이메일 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | 이 Python Azure 함수 샘플은 특정 이메일 도메인으로 등록을 제한하고 사용자가 제공한 정보의 유효성을 검사하는 방법을 보여줍니다.    |


## <a name="automated-fraud-protection-services--captcha"></a>자동화된 사기 방지 서비스 및 CAPTCHA
| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs 사기 행위 및 남용 방지](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | 이 샘플에서는 Arkose Labs 사기 행위 및 남용 방지 서비스를 사용하여 사용자 등록을 보호하는 방법을 보여줍니다. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | 이 샘플에서는 자동화된 남용을 방지하기 위해 reCAPTCHA 챌린지를 사용하여 사용자 등록을 보호하는 방법을 보여줍니다. |


## <a name="identity-verification"></a>ID 확인

| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | 이 샘플은 IDology 서비스를 사용하는 등록 흐름의 일부로 사용자 ID를 확인하는 방법을 보여 줍니다. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | 이 샘플은 Experian 서비스를 통한 등록 흐름의 일부로 사용자 ID를 확인하는 방법을 보여 줍니다. |


## <a name="other"></a>기타

| 샘플                                                                                                            | Description                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [초대 코드](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | 이 샘플에서는 초대 코드를 사용하여 특정 대상으로 등록을 제한하는 방법을 보여줍니다.|
| [API 커넥터 커뮤니티 샘플](https://github.com/azure-ad-b2c/api-connector-samples) | 이 리포지토리에는 API 커넥터에 의해 활성화된 시나리오의 커뮤니티에서 유지 관리되는 샘플이 있습니다.|
