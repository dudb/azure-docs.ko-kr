---
title: IoT Hub 장치 관리 쌍 쿼리 | Microsoft Docs
description: 쿼리를 사용하여 장치 쌍을 찾는 방법을 설명하는 Azure IoT Hub 장치 관리 자습서.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# 자습서: node.js로 쿼리를 사용하여 쌍 장치를 찾는 방법(미리 보기)
[!INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

Azure IoT 장치 관리를 통해 쿼리를 사용하여 물리적 장치의 서비스 표시인 장치 쌍을 찾을 수 있습니다. 장치 속성, 서비스 속성 또는 장치 쌍의 태그를 기반으로 쿼리할 수 있습니다. 태그와 속성을 사용하여 쿼리할 수 있습니다.

* 태그를 사용하여 장치 쌍을 쿼리하려면 문자열 배열을 전달합니다. 그러면 쿼리가 해당하는 모든 문자열로 태그가 지정된 장치 집합을 반환합니다.
* 서비스 속성 또는 장치 속성을 사용하여 장치 쌍을 쿼리하려면 JSON 쿼리 식을 사용합니다.

장치 쌍 및 쿼리에 대한 자세한 내용은 [Azure IoT Hub 장치 관리 개요][lnk-dm-overview]를 참조하세요.

## 장치 쿼리 샘플 실행
다음 샘플은 [Azure IoT Hub 장치 관리 시작][lnk-get-started] 자습서 기능을 확장합니다. 먼저 여러 가지 시뮬레이션된 장치를 실행하고 쿼리를 사용하여 특정 장치를 찾습니다.

### 필수 조건
이 샘플을 실행하기 전에 [Azure IoT Hub 장치 관리 시작][lnk-get-started]의 단계를 완료했어야 합니다. 즉, 시뮬레이션된 장치가 실행 중이어야 합니다. 이전에 프로세스를 완료한 경우 시뮬레이션된 장치를 지금 다시 시작하세요.

### 샘플 시작
샘플을 시작하려면 `registry_queryDevices.js`을(를) 실행해야 합니다. 그러면 몇 가지 쿼리가 실행됩니다. 아래 단계에 따라 샘플을 시작하세요.

1. 시뮬레이션된 장치가 실행될 때까지 최소 1 분 이상 기다리세요. 이렇게 하면 쌍의 장치 속성이 물리적 장치와 확실히 동기화됩니다. 동기화에 대한 자세한 내용은 [자습서: 장치 쌍을 사용하는 방법][lnk-twin-tutorial]을 참조하세요.
2. **azure-iot-sdks** 리포지토리를 복제한 루트 폴더에서 **azure-iot-sdks/node/service/samples** 디렉터리로 이동합니다.
3. **registry\_queryDevices.js**를 열고 자리 표시자를 IoT Hub 연결 문자열로 바꿉니다.
4. `node registry_queryDevices.js`을 실행합니다.

태그, 서비스 속성 및 장치 속성을 사용하여 장치 개체에 대한 쿼리 결과를 보여 주는 명령줄 창의 출력이 표시됩니다.

## 쿼리 구조(JSON)
장치 속성 및 서비스 속성에 대한 쿼리는 쿼리 자체를 표현하는 JSON 문자열을 사용하여 실행됩니다. JSON 문자열은 4개 부분으로 구성됩니다. 다음은 각 부분 및 올바른 JSON 문자열로 직렬화할 수 있는 C# 개체에 대한 설명입니다.

* **Project**: 쿼리 결과 집합에 포함할 장치 개체의 필드를 지정하는 식(SQL의 SELECT와 같음):
  
    ```
    var projectQuery = {
        "project": {
            "all": false,
            "properties": [{
                "name": "CustomerId",
                "type": "service"
            }, {
                "name": "Weight",
                "type": "service"
            }]
        }
    }
    ```
* **Filter**: 쿼리 결과 집합에 포함되는 장치 개체를 제한하는 식(SQL의 WHERE와 같음):
  
    ```
    var filterQuery = {
      filter: {
        property: {
          name: "CustomerId",
          type: "service"
        },
        value: "123456",
        comparisonOperator: "eq",
        type: "comparison"
      },
      project: null,
      aggregate: null,
      sort: null
    };
    ```
* **Aggregate**: 쿼리 결과 집합을 그룹화하는 방법을 결정하는 식(SQL의 GROUPBY와 같음).
  
    ```
    var aggregateQuery = {
    filter: null,
    project: null,
    aggregate: {
    keys: [
      {
        name: "CustomerId",
        type: "service"
      }
    ],
    properties: [
      {
        operator: "sum",
        property: {
          name: "Weight",
          type: "service"
        },
        columnName: "TotalWeight"
      }
    ]
    },
    sort: null
    };
    ```
* **Sort**: 쿼리 결과 집합을 정렬하는 데 사용해야 하는 속성을 정의하는 식(SQL의 ORDER BY와 같음). Sort가 null인 경우 **deviceID**가 기본적으로 사용됩니다.

```
  var sortQuery = {
  filter: null,
  project: null,
  aggregate: null,
  sort: [
      {
          order: "asc",
          property: {
              name: "QoS",
              type: "service"
          }
      }
  ]
  };
```

### 제한 사항
쿼리에 대한 공용 미리 보기 구현에는 몇 가지 제한이 있습니다.

* 쿼리 식에 대한 유효성을 검사하지 않습니다.
* 쿼리는 대/소문자를 구분합니다.
* 쿼리 식을 사용하여 서비스 또는 장치 속성별로 쿼리하는 경우 장치 100개만 반환됩니다. 페이징을 구현하는 방법의 예제는 [쿼리 라이브러리][lnk-query-samples]에서 사용할 수 있습니다.

JSON의 구문 및 사용 가능한 필드에 대한 자세한 내용을 [사용할 수 있습니다][lnk-query-expression-guide]. [쿼리 식 라이브러리][lnk-query-samples]에서 샘플 쿼리도 볼 수 있습니다.

### 장치 및 서비스 속성별로 쿼리
JSON 쿼리 식이 있으면 장치 쌍을 쿼리할 수 있습니다. **queryDevices**를 호출하고 집계 쿼리에 대한 **결과** 개체를 확인하고 모든 쿼리에 대한 **장치** 개체를 확인합니다.

```
registry.queryDevices(query, done)
```

### 태그별로 쿼리
태그별로 쿼리를 통해 JSON 쿼리 식을 사용하지 않고 장치 개체를 찾을 수 있습니다. 둘 이상의 태그를 전달하는 경우 모든 태그가 있는 장치 개체만 반환됩니다. 두 번째 매개 변수는 반환해야 할 장치의 최대 수인 **maxCount**입니다. **maxCount**에 대한 최대값은 1000개입니다.

```
registry.queryDevicesByTags(['bacon'], 100, done)
```

### 장치 구현
쿼리는 Azure IoT Hub 장치 관리 클라이언트 라이브러리에서 사용하도록 설정됩니다. 장치 속성이 동기화되었다면([자습서: 장치 쌍을 사용하는 방법][lnk-twin-tutorial]에서 설명한 대로) 해당 속성에 대해 쿼리할 수 있습니다. 장치 속성은 물리적 장치가 IoT Hub에 연결하고 초기값을 제공한 후에만 사용할 수 있습니다.

## 다음 단계
Azure IoT Hub 장치 관리 기능에 대해 자세히 알아보려면 이 자습서를 차례로 실행해 볼 수 있습니다.

* [장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-tutorial-jobs]
* [IoT 게이트웨이 뒤에서 관리되는 장치 사용][lnk-dm-gateway]
* [Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개][lnk-library-c]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [솔루션 디자인][lnk-design]
* [개발자 가이드][lnk-devguide]
* [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
* [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->