# Simulated Temperature Sensor for Azure IoT Edge

This module is an example of a temperature sensor simulation Azure IoT Edge module. You can see its usage in the [Azure IoT Edge documentation](https://docs.microsoft.com/en-us/azure/iot-edge/). It continuously creates simulated temperature data and sends the message to the `temperatureOutput` endpoint.

## Available Endpoints

You can interact with the Temperature Simulator in several ways

## Output Queue Endpoints

The temperature simulator is producing a simulation on machine temperature / pressure and environmental parameters like temperature and humidity. The endpoint is `temperatureOutput` with the following payload

```json
{
    "machine": {
        "temperature": 55.651076260675254,
        "pressure": 4.9475909664060413
    },
    "ambient": {
        "temperature": 21.195752660602217,
        "humidity": 26
    },
    "timeCreated": "2018-02-09T10:53:32.2731850+00:00"
}
```

## Direct Method Invocation

The module provides a direct method handler which will **reset** the data to its initial values. To invoke this method you just create from another module or service a `CloudToDeviceMethod`

Here is sample code to showcase how such a method invocation would look like through the `ServiceClient`

```c#
...
var resetMethod = new CloudToDeviceMethod("reset");
var response = await serviceClient.InvokeDeviceMethodAsync(deviceId, moduleId, resetMethod);
...
```

## Input Queue Endpoints

You can also **reset** the data to its initial values via a message going through the EdgeHub routing system to the temperature simulation module. You have to send the payload to the input endpoint `control`.

```json
{
    "command": "reset"
}
```

## Desired Properties Support

The sending behavior can be configured using desired properties for the module via the module twin.

```json
{
    "properties": {
        "desired": {
            "SendData": true,
            "SendInterval": 5
        }
    }
}
```

Properties can be set during the set module process in the Azure Portal or via the Azure CLI with the [Azure IoT Extension for Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension).

**SendData** = true | false
- Start or stops pushing messages to the `temperatureOutput` endpoint.

**SendInterval** = int value
- The interval in seconds between messages being pushed into the endpoint.

## Building Multi-Architecture Docker Container

This sample includes the file `multi-arch-manifest.yml` to showcase how to create a multi-architecture docker image.

1. Create for each architecture you want support a docker container using the Dockerfiles provided by the sample
2. Push the container to the registry of your choice
3. Replace the parameters `<registry>` and `<version>` with your values.
4. Get the [Manifest-Tool](https://github.com/estesp/manifest-tool)
5. Push `multi-arch-manifest.yml` to your registry using the manifest tool


# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
