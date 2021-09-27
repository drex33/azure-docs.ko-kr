---
title: AuthN/AuthZ의 파일 기반 구성
description: 특정 미리 보기 기능을 사용하려면 구성 파일을 사용하여 App Service에서 인증 및 권한 부여를 구성합니다.
ms.topic: article
ms.date: 07/15/2021
ms.openlocfilehash: 2875c529e644b46d08251ae57ee9f424492a2433
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657218"
---
# <a name="file-based-configuration-in-azure-app-service-authentication"></a>Azure App Service 인증의 파일 기반 구성

[App Service 인증](overview-authentication-authorization.md)을 사용하면 파일로 인증 설정을 구성할 수 있습니다. [Azure Resource Manager](../azure-resource-manager/management/overview.md) API를 통해 노출되기 전에 App Service 인증/승인의 특정 미리 보기 기능을 사용하려면 파일 기반 구성을 사용해야 할 수 있습니다.

> [!IMPORTANT]
> 애플리케이션 페이로드와 해당 파일은 [슬롯](./deploy-staging-slots.md)과 같이 환경 간에 이동할 수 있습니다. 각 슬롯에 다른 앱 등록을 고정하는 것이 좋습니다. 이러한 경우에는 구성 파일을 사용하는 대신 표준 구성 방법을 계속 사용해야 합니다.

## <a name="enabling-file-based-configuration"></a>파일 기반 구성 사용

1. 프로젝트의 루트에서 구성에 관한 새 JSON 파일을 만듭니다(웹/함수 앱의 D:\home\site\wwwroot에 배포됨). [파일 기반 구성 참조](#configuration-file-reference)에 따라 원하는 구성을 입력합니다. 기존 Azure Resource Manager 구성을 수정하는 경우 `authsettings` 컬렉션에서 캡처된 속성을 구성 파일로 변환해야 합니다.

2. `Microsoft.Web/sites/<siteName>/config/authsettingsV2` 아래의 [Azure Resource Manager](../azure-resource-manager/management/overview.md) API에서 캡쳐되는 기존 구성을 수정합니다. 이를 수정하기 위해 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md) 또는 [Azure Resource Explorer](https://resources.azure.com/) 같은 도구를 사용할 수 있습니다. authsettingsV2 컬렉션 내에서 두 개의 속성을 설정해야 합니다(다른 속성은 제거할 수 있음).

    1. `platform.enabled`을 “true”로 설정합니다.
    2. `platform.configFilePath`를 파일 이름으로 설정합니다(예: "auth.json").

> [!NOTE]
> `platform.configFilePath`의 형식은 플랫폼마다 다릅니다. Windows에서는 상대 경로와 절대 경로를 모두 지원합니다. 상대 경로를 권장합니다. Linux의 경우 현재 절대 경로만 지원되므로 설정값은 "/home/site/wwwroot/auth.json" 또는 이와 유사해야 합니다.

구성을 업데이트한 후에는 파일의 콘텐츠가 해당 사이트에 관한 App Service 인증/권한 부여의 동작을 정의하는 데 사용됩니다. Azure Resource Manager 구성으로 돌아가려면 `platform.configFilePath` 설정을 null로 변경하여 제거하면 됩니다.

## <a name="configuration-file-reference"></a>구성 파일 참조

구성 파일에서 참조되는 모든 비밀은 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 저장해야 합니다. 설정의 이름을 원하는 대로 바꿀 수 있습니다. 구성 파일의 참조가 동일한 키를 사용하는지 확인합니다.

파일 내에서 가능한 구성 옵션은 다음과 같습니다.

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="more-resources"></a>추가 리소스

- [자습서: 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
- [인증을 위한 환경 변수 및 앱 설정](reference-app-settings.md#authentication--authorization)
