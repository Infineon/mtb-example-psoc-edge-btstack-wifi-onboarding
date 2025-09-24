[Click here](../README.md) to view the README.

## Design and implementation

The design of this application is minimalistic to get started with code examples on PSOC&trade; Edge MCU devices. All PSOC&trade; Edge E84 MCU applications have a dual-CPU three-project structure to develop code for the CM33 and CM55 cores. The CM33 core has two separate projects for the secure processing environment (SPE) and non-secure processing environment (NSPE). A project folder consists of various subfolders, each denoting a specific aspect of the project. The three project folders are as follows:

**Table 1. Application projects**

Project | Description
--------|------------------------
*proj_cm33_s* | Project for CM33 secure processing environment (SPE)
*proj_cm33_ns* | Project for CM33 non-secure processing environment (NSPE)
*proj_cm55* | CM55 project

<br>

In this code example, at device reset, the secure boot process starts from the ROM boot with the secure enclave (SE) as the root of trust (RoT). From the secure enclave, the boot flow is passed on to the system CPU subsystem where the secure CM33 application starts. After all necessary secure configurations, the flow is passed on to the non-secure CM33 application. Resource initialization for this example is performed by this CM33 non-secure project. It configures the system clocks, pins, clock to peripheral connections, and other platform resources. It then enables the CM55 core using the `Cy_SysEnableCM55()` function and the CM55 core is subsequently put to DeepSleep mode.


### Introduction

In this example, Bluetooth&reg; LE provides enables the device to connect to a Wi-Fi AP by providing the Wi-Fi SSID and password securely. The Wi-Fi credentials are stored in non-volatile memory (NVM) so the device can use this data upon reset to connect to an AP without requiring Bluetooth&reg; LE intervention. Note that the data stored in the NVM is unencrypted.

The Wi-Fi SSID and password are exchanged using custom GATT service and characteristics. There are three characteristics to send SSID and password. The first two are used to send the SSID and password separately and the third is used to send the data together as described earlier. The fourth characteristic is to receive WI-Fi netowrk details when the device is asked to scan for networks. 

There is a fifth custom characteristic, which gives the command to connect, disconnect, and scan. Only the Wi-Fi SSID characteristic is readable; all others are either writable, notifiable, or both. The device needs to be paired before any characteristic can be read from or written to.

**Table 1. Application Source Files**

**File Name**            |**Comments**      |
 ------------------------|---------------------------------------|
 *main.c* | Has the application entry function. It initializes the UART for debugging and then initializes the controller stack. It handles Bluetooth LE initialization, configuration, advertisement, and responses to Bluetooth LE events.|
 *wifi_task.c* | It contains the wifi_task which helps to connect/disconnect to/from the Wi-Fi AP based on the Wi-Fi SSID and password provided.|
 *app_utils.c* | Has some utility functions which help to convert return and status values to meaningful text|

**Table 2. Functions in *main.c***

| **Function Name** | **Functionality** 
|-------------------|-------------------
| `main` | This is the main function for the CM33 CPU. It does the following:
|        | 1. Initializes the BSP  
|        | 2. Enables global interrupt
|        | 3. Initializes retarget IO
|        | 4. Initializes platform configuration 
|        | 5. Creates Wi-Fi connect and disconnect tasks 
|        | 6. Starts the scheduler 
| `application_init` | This function is called from the BTM enabled event, and does the following:  
|                    | 1. Creates a WICED heap
|                    | 2. Initializes button GPIO, enables interrupt and register callback
|                    | 3. Checks if Wi-Fi credential data is available in the NVM
|                    | 4. Initializes and registers the GATT DB 
|                    | 5. Sets pairable mode to 'TRUE' 
|                    | 6. Sets ADV data and starts advertising
| `app_management_cback`       | Handles BT stack events
| `app_get_attribute`          | Searches through the GATT DB to point to the attribute corresponding to the given handle
| `app_gatts_req_read_handler`    | Handles GATT read request events from the stack
| `app_gatt_read_by_type_handler` | Handles GATT read by type requests
| `app_gatts_req_write_handler`   | Handles GATT write request events from the stack 
| `app_gatt_connect_callback`     | Handles GATT connect request events from the stack
| `app_gatts_req_cb`              | Redirects GATT attribute requests to the appropriate functions
| `app_gatts_callback`            | Callback function for GATT events registered when the GATT database was initialized in the BTM-enabled event
| `gpio_interrupt_handler`        | GPIO interrupt service routine; detects button presses, deletes the Wi-Fi data from the NVM, and starts Bluetooth&reg; LE ADV

**Table 3. Functions in *wifi_task.c***

 **Function Name** | **Functionality**
-------------------|-------------------
 `wifi_task`  | Initializes the WCM module and connects disconnects to/from the AP and starts Wi-Fi scan
 `scan_callback`   | The callback function which accumulates the scan results


### Bluetooth&reg; LE GATT custom service

This example uses custom GATT service and characteristics to communicate with the Bluetooth&reg; LE GATT client. The GATT database was created using the Bluetooth&reg; Configurator. The configurator generates files called *cycfg_gatt_db.c* and *cycfg_gatt_db.h* which contain the GATT DB. See the *{ModusToolbox install directory}/tools_{version}/bt-configurator/docs/bt-configurator.pdf* to learn how to create a GATT database.

The custom characteristics used in this example is detailed in the specification called "Infineon Wi-Fi onboarding service (IWOS)" (file name: *002-33722_00_V.pdf*, included with this code example).


### Resources and settings

This section explains the ModusToolbox&trade; resources and their configurations as used in this code example. Note that all configurations explained in this section has already been used in the code example. The ModusToolbox&trade; IDE stores the configuration settings of the application in the *design.modus* file. This file is used by the graphical configurators, which generate the configuration firmware. This firmware is stored in the application’s *GeneratedSource* folder.

- **Device Configurator:** The Device Configurator is used to enable or configure the peripherals and the pins used in the application. See the
[Device Configurator Guide](https://www.infineon.com/dgdl/Infineon-ModusToolbox_Device_Configurator_Guide_4-UserManual-v01_00-EN.pdf?fileId=8ac78c8c7d718a49017d99ab297631cb&utm_source=cypress&utm_medium=referral&utm_campaign=202110_globe_en_all_integration-files) for details

- **Bluetooth Configurator:** The Bluetooth&reg; Configurator is used for generating or modifying the Bluetooth&reg; LE GATT database. See the
[Bluetooth Configurator Guide](https://www.infineon.com/dgdl/Infineon-ModusToolbox_Bluetooth_Configurator_Guide_3-UserManual-v01_00-EN.pdf?fileId=8ac78c8c7d718a49017d99aaf5b231be&utm_source=cypress&utm_medium=referral&utm_campaign=202110_globe_en_all_integration-files) for details

<br>
