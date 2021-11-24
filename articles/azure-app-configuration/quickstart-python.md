---
title: 빠른 시작 - Python 앱에서 Azure App Configuration 사용 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration에서 Python 앱을 만들어 코드와 별도로 애플리케이션 설정의 저장 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python, mode-other
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 0adf8e460fe918c5ef652a6d6fedb8c0df335d3a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051768"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 Python 앱 만들기

이 빠른 시작에서는 Azure App Configuration에서 [Python용 Azure App Configuration 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)를 사용하여 애플리케이션 설정의 저장 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- Python 2.7 또는 3.6 이상 - Windows에서 Python을 설정하는 방법에 대한 자세한 내용은 [Windows 기반의 Python 설명서](/windows/python/)를 참조하세요.

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **구성 탐색기** > **만들기** > **키-값** 을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블** 과 **콘텐츠 형식** 을 비워 두세요.

8. **적용** 을 선택합니다.

## <a name="setting-up-the-python-app"></a>Python 앱 설정

1. 이 자습서에서는 *app-configuration-quickstart* 라는 프로젝트에 대한 새 디렉터리를 만듭니다.

    ```console
    mkdir app-configuration-quickstart
    ```

1. 새로 만든 *app-configuration-quickstart* 디렉터리로 전환합니다.

    ```console
    cd app-configuration-quickstart
    ```

1. `pip install` 명령을 사용하여 Azure App Configuration 클라이언트 라이브러리를 설치합니다.

    ```console
    pip install azure-appconfiguration
    ```

1. *app-configuration-quickstart.py* 라는 새 파일을 *app-configuration-quickstart* 디렉터리에 만들고 다음 코드를 추가합니다.

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> 이 빠른 시작의 코드 조각은 Python용 App Configuration 클라이언트 라이브러리를 시작하는 데 도움이 됩니다. 애플리케이션의 경우 필요에 따라 예외를 처리하는 것도 고려해야 합니다. 예외 처리에 대한 자세한 내용은 당사의 [Python SDK 설명서](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)를 참조하세요.

## <a name="configure-your-app-configuration-connection-string"></a>App Configuration 연결 문자열 구성

1. **AZURE_APP_CONFIG_CONNECTION_STRING** 이라는 환경 변수를 설정하고, 이를 App Configuration 저장소에 대한 액세스 키로 설정합니다. 명령줄에서 다음 명령을 실행합니다.

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다. 환경 변수의 값을 출력하여 올바르게 설정되었는지 확인합니다.

## <a name="code-samples"></a>코드 샘플

이 섹션의 샘플 코드 조각에서는 Python용 App Configuration 클라이언트 라이브러리를 사용하여 일반적인 작업을 수행하는 방법을 보여 줍니다. 이러한 코드 조각을 이전에 만든 *app-configuration-quickstart.py* 파일의 `try` 블록에 추가합니다.

> [!NOTE]
> App Configuration 클라이언트 라이브러리는 키-값 개체를 `ConfigurationSetting`으로 참조합니다. 따라서 이 문서에서는 App Configuration 저장소의 **키-값** 을 **구성 설정** 이라고 합니다.

* [App Configuration 저장소에 연결](#connect-to-an-app-configuration-store)
* [구성 설정 가져오기](#get-a-configuration-setting)
* [구성 설정 추가](#add-a-configuration-setting)
* [구성 설정 목록 가져오기](#get-a-list-of-configuration-settings)
* [구성 설정 잠금](#lock-a-configuration-setting)
* [구성 설정 잠금 해제](#unlock-a-configuration-setting)
* [구성 설정 업데이트](#update-a-configuration-setting)
* [구성 설정 삭제](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

다음 코드 조각에서는 환경 변수에 저장된 연결 문자열을 사용하여 **AzureAppConfigurationClient** 의 인스턴스를 만듭니다.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>구성 설정 가져오기

다음 코드 조각에서는 `key` 이름을 기준으로 구성 설정을 검색합니다.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>구성 설정 추가

다음 코드 조각에서는 `key` 및 `value` 필드가 있는 `ConfigurationSetting` 개체를 만들고, `add_configuration_setting` 메서드를 호출합니다. 이미 저장소에 있는 구성 설정을 추가하려고 하면 이 메서드에서 예외를 throw합니다. 이 예외를 방지하려면 [set_configuration_setting](#update-a-configuration-setting) 메서드를 대신 사용할 수 있습니다.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>구성 설정 목록 가져오기

다음 코드 조각에서는 구성 설정 목록을 검색합니다. `key_filter` 및 `label_filter` 인수를 제공하여 각각 `key` 및 `label`을 기준으로 키-값을 필터링할 수 있습니다. 필터링에 대한 자세한 내용은 [구성 설정을 쿼리](./concept-key-value.md#query-key-values)하는 방법을 참조하세요.

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>구성 설정 잠금

App Configuration에서 키-값의 잠금 상태는 `ConfigurationSetting` 개체의 `read_only` 특성으로 표시됩니다. `read_only`가 `True`이면 설정이 잠깁니다. `set_read_only` 메서드를 `read_only=True` 인수로 호출하여 구성 설정을 잠글 수 있습니다.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>구성 설정 잠금 해제

`ConfigurationSetting`의 `read_only` 특성이 `False`이면 설정이 잠금 해제됩니다. `set_read_only` 메서드를 `read_only=False` 인수로 호출하여 구성 설정의 잠금을 해제할 수 있습니다.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>구성 설정 업데이트

`set_configuration_setting` 메서드를 사용하여 기존 설정을 업데이트하거나 새 설정을 만들 수 있습니다. 다음 코드 조각에서는 기존 구성 설정의 값을 변경합니다.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>구성 설정 삭제

다음 코드 조각에서는 `key` 이름을 기준으로 구성 설정을 삭제합니다.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>앱 실행

이 빠른 시작에서 Azure App Configuration 클라이언트 라이브러리를 사용하여 Azure Portal을 통해 만든 구성 설정을 검색하고, 새 설정을 추가하고, 기존 설정 목록을 검색하고, 설정을 잠금 및 잠금 해제하고, 설정을 업데이트하고, 마지막으로 설정을 삭제하는 Python 앱을 만들었습니다.

이 시점에서 *app-configuration-quickstart.py* 파일에는 다음 코드가 있습니다.

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

콘솔 창에서 *app-configuration-quickstart.py* 파일이 포함된 디렉터리로 이동하고, 다음 python 명령을 실행하여 앱을 실행합니다.

```console
python app-configuration-quickstart.py
```

다음과 같은 출력이 표시됩니다.

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>리소스 정리


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고 Python 앱에서 키-값에 액세스하는 방법을 알아보았습니다.

추가 코드 샘플은 다음을 방문하세요.

> [!div class="nextstepaction"]
> [Azure App Configuration 클라이언트 라이브러리 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
