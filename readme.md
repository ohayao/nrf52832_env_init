## Mac + VScode 配置nrf52832环境
- ### 下载
    - [GNU Arm Embedded Toolchain 10.3.1](https://armkeil.blob.core.windows.net/developer/Files/downloads/gnu-rm/10.3-2021.10/gcc-arm-none-eabi-10.3-2021.10-mac.pkg)
    - [nRF_SDK 17.1.0](https://www.nordicsemi.com/-/media/Software-and-other-downloads/SDKs/nRF5/Binaries/nRF5_SDK_17.1.0_ddde560.zip)
- 安装gnu-arm
    - 安装后位置 `/Applications/ARM`
- 解压&重命名sdk
    - 解压后放在 `${HOME}/nRF5_SDK17`
- 配置
    - 环境变量
    ```zsh
    vim ~/.zshrc
    // 添加
    export nRF_SDK=${HOME}/nRF5_SDK17
    export GNU_GCC=/Applications/ARM
    // 保存退出 生效
    source ~/.zshrc
    ```
    - sdk 中配置gnu-gcc
    ```zsh
    # 编辑文件
    vim ${nRF_SDK}/components/toolchain/gcc/Makefile.posix

    # 设置内容
    GNU_INSTALL_ROOT ?= /Applications/ARM/bin/
    GNU_VERSION ?= 10.3.1
    GNU_PREFIX ?= arm-none-eabi
    ```
- ### vscode 配置 nrf52832
    - 打开示例工程文件夹 `blinky`
    ```
    cd ${nRF_SDK}/examples/ble_peripheral
    # 备份
    cp -r ble_app_blinky/ ble_app_blinky_bak/
    cd ble_app_blinky
    code .
    ```
    - 在当前位置添加工作区
    - 配置c/c++ `.vscode/c_cpp_properties.json`
    ```json
    {
        "configurations": [
            {
                "name": "nRF52832",
                "includePath": [
                    "${workspaceFolder}/**",
                    "${env:nRF_SDK}/components/libraries/util",
                    "${env:nRF_SDK}/modules/nrfx/mdk",
                    "${env:nRF_SDK}/components/softdevice/s132/headers",
                    "${env:nRF_SDK}/components/ble/common",
                    "${env:nRF_SDK}/components/softdevice/common",
                    "${env:nRF_SDK}/components/libraries/log",
                    "${env:nRF_SDK}/components/ble/ble_services/ble_libs",
                    "${env:nRF_SDK}/components/ble/nrf_ble_gatt",
                    "${env:nRF_SDK}/components/boards",
                    "${env:nRF_SDK}/components/libraries/button",
                    "${env:nRF_SDK}/components/**",
                    "${env:nRF_SDK}/**"
                ],
                "defines": [
                    "BOARD_PCA10040",
                    "CONFIG_GPIO_AS_PINRESET",
                    "INITIALIZE_USER_SECTIONS",
                    "FLOAT_ABI_HARD",
                    "NRF52",
                    "NRF52832_XXAA",
                    "NRF_SD_BLE_API_VERSION=6",
                    "S132",
                    "SOFTDEVICE_PRESENT",
                    "SWI_DISABLE0"
                ],
                "macFrameworkPath": [
                    "/System/Library/Frameworks",
                    "/Library/Frameworks"
                ],
                "compilerPath": "${env:GNU_GCC}/bin/arm-none-eabi-gcc",
                "cStandard": "c11",
                "cppStandard": "c++17",
                "intelliSenseMode": "clang-x64"
            }
        ],
        "version": 4
    }
    ```
    - 配置脚本任务 `.vscode/tasks.json`
    ```json
    {
    	"version": "2.0.0",
    	"tasks": [
    		{
    			"label": "build",
    			"type": "shell",
    			"command": "make",
    			"options": {
    				"cwd": "${workspaceFolder}/pca10040/s132/armgcc"
    			},
    			"problemMatcher": [],
    			"group": {
    				"kind": "build",
    				"isDefault": true
    			}
    		},
    		{
    			"label": "clean",
    			"type": "shell",
    			"command": "make clean",
    			"options": {
    				"cwd": "${workspaceFolder}/pca10040/s132/armgcc"
    			},
    			"problemMatcher": []
    		},
    		{
    			"label": "flash",
    			"type": "shell",
    			"command": "make flash",
    			"options": {
    				"cwd": "${workspaceFolder}/pca10040/s132/armgcc"
    			},
    			"group": "build",
    			"problemMatcher": []
    		},
    		{
    			"label": "flash_softdevice",
    			"type": "shell",
    			"command": "make flash_softdevice",
    			"options": {
    				"cwd": "${workspaceFolder}/pca10040/s132/armgcc"
    			},
    			"problemMatcher": []
    		},
    		{
    			"label": "sdk_config",
    			"type": "shell",
    			"command": "make sdk_config",
    			"options": {
    				"cwd": "${workspaceFolder}/pca10040/s132/armgcc"
    			},
    			"problemMatcher": []
    		},
    		{
    			"label": "erase",
    			"type": "shell",
    			"command": "make erase",
    			"options": {
    				"cwd": "${workspaceFolder}/pca10040/s132/armgcc"
    			},
    			"problemMatcher": []
    		}
    	]
    }
    ```