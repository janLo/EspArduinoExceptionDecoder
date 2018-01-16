# EspArduinoExceptionDecoder

Python Script to decode ESP8266 Exceptions.


## License

GPL 3.0


## Usage:

```
usage: decoder.py [-h] [-p {ESP8266,ESP32}] [-t TOOL] -e ELF [-f] file

decode ESP Stacktraces.

positional arguments:
  file                  The file to read the exception data from ('-' for
                        STDIN)

optional arguments:
  -h, --help            show this help message and exit
  -p {ESP8266,ESP32}, --platform {ESP8266,ESP32}
                        The platform to decode from
  -t TOOL, --tool TOOL  Path to the xtensa toolchain
  -e ELF, --elf ELF     path to elf file
  -f, --full            Print full stack dump
```

The tool is the path to your xtensa toolchain. If you use [PlatformIO](http://platformio.org/) it should be `~/.platformio/packages/toolchain-xtensa` for the ESP8266 and `~/.platformio/packages/toolchain-xtensa32` for the ESP32.

The elf path is the path to your built elf binary. On PlatformIO it is located at `<project-dir>/.pioenvs/<environment-name>/firmware.elf`.


## Dependencies:

* python 3
* The xtensa toolchain for your ESP


## Example:

Given you have the following stacktrace from the ESP:

```
Exception (3):
epc1=0x4022caf4 epc2=0x00000000 epc3=0x00000000 excvaddr=0x4026b70f depc=0x00000000

ctx: cont
sp: 3fff2200 end: 3fff2590 offset: 01a0

>>>stack>>>
3fff23a0:  40202d30 3fff1570 00000000 40204e8d
3fff23b0:  00000000 3fff13c4 3fff2410 4020d494
3fff23c0:  ffffffff 3fffc6fc 00000001 4020d4a4
3fff23d0:  3fff24c0 00000000 3fff13c4 4020ea3c
3fff23e0:  3fff5a50 3fff13c4 3fff5a50 401004d8
3fff23f0:  0000000d 00000000 3fff24dc 00000000
3fff2400:  3fff5a50 3fff13c4 3fff5a50 4020eba6
3fff2410:  3fff4bb4 00000066 4020d060 402295ec
3fff2420:  3fff5314 00000010 00000000 4010053d
3fff2430:  3fff5314 00000001 3fff4c80 40205631
3fff2440:  3fff27ac 00000280 00000280 4010020c
3fff2450:  3fff5a50 3fff24ec 3fff5a2c 40100690
3fff2460:  3fff5314 3fff24c0 3fff1570 40202b20
3fff2470:  3fff5a50 00000001 3fff59ec 40203b67
3fff2480:  00000000 3fff24ec 3fff5a2c 40228da0
3fff2490:  3ffe97e8 3fff5d70 3fff24c0 402013d2
3fff24a0:  3fff59ec 3fff5a2c 00000000 3fff13c4
3fff24b0:  3fff5a50 3fff24ec 00000000 4020ef05
3fff24c0:  3ffe98b0 00000000 000003e8 00008b7a
3fff24d0:  00000000 00000000 3fff6174 0000000f
3fff24e0:  00000000 3fff24ec 0000000a 3ffe9838
3fff24f0:  3fff59d0 3fff5d64 00000800 40201864
3fff2500:  3fff13c4 3fff2de0 3fff2de0 4022b524
3fff2510:  3fff13c4 3fff2de0 00000000 4022b26a
3fff2520:  3fff4bb4 feefeffe 4022a1a4 4022a574
3fff2530:  feefeffe feefeffe feefeffe feefeffe
3fff2540:  feefeffe feefeffe feefeffe feefeffe
3fff2550:  feefeffe feefeffe feefeffe feefeffe
3fff2560:  feefeffe feefeffe feefeffe 3fff1564
3fff2570:  3fffdad0 00000000 3fff155c 40202cfc
3fff2580:  feefeffe feefeffe 3fff1570 40204ed4
<<<stack<<<
```

You can dump it in a file called `myStackTrace.txt` and run the `decode.py` script:

```
$ <...>/decoder.py -e .pioenvs/d1_mini/firmware.elf myStackTrace.txt1
```

This prints the processed stacktrace:

```
Exception: 3 (LoadStoreError: Processor internal physical address or data error during load or store)

epc1:     0x4022caf4: strcmp at /Users/igrokhotkov/e/newlib-xtensa/xtensa-lx106-elf/newlib/libc/machine/xtensa/../../../../.././newlib/libc/machine/xtensa/strcmp.S:470
epc2:     0x00000000
epc3:     0x00000000
excvaddr: 0x4026b70f: chip_v6_unset_chanfreq at ??:?
depc:     0x00000000

ctx: cont

sp:       0x3fff2200
end:      0x3fff2590
offset:   0x000001a0

stack:
0x40202d30: yield at ??:?
0x40204e8d: cont_continue at cont.o:?
0x4020d494: ArduinoJson::Internals::ListConstIterator<ArduinoJson::JsonPair> ArduinoJson::JsonObject::findKey<char const*>(char const*) const at ??:?
0x4020d4a4: bool ArduinoJson::JsonObject::containsKey<char>(char const*) const at ??:?
0x4020ea3c: bool setIfPresent<String, char const*>(ArduinoJson::JsonObject&, char const*, String&, std::function<bool (String const&)> const&) at ??:?
0x401004d8: malloc at ??:?
0x4020eba6: Settings::applyJson(ArduinoJson::JsonObject&) at ??:?
0x4020d060: std::_Function_base::_Base_manager<notEmpty()::{lambda(String const&)#1}>::_M_manager(std::_Any_data&, std::_Function_base::_Base_manager<notEmpty()::{lambda(String const&)#1}> const&, std::_Manager_operation) at Settings.cpp:?
0x402295ec: std::_Function_handler<bool (String const&), notEmpty()::{lambda(String const&)#1}>::_M_invoke(std::_Any_data const&, String const&) at Settings.cpp:?
0x4010053d: realloc at ??:?
0x40205631: SPIFFS_read at ??:?
0x4010020c: _umm_free at umm_malloc.c:?
0x40100690: free at ??:?
0x40202b20: operator delete(void*) at ??:?
0x40203b67: std::_Sp_counted_deleter<SPIFFSFileImpl*, std::__shared_ptr<SPIFFSFileImpl, (__gnu_cxx::_Lock_policy)0>::_Deleter<std::allocator<SPIFFSFileImpl> >, std::allocator<SPIFFSFileImpl>, (__gnu_cxx::_Lock_policy)0>::_M_destroy() at ??:?
0x40228da0: std::__shared_count<(__gnu_cxx::_Lock_policy)0>::~__shared_count() at ??:?
0x402013d2: fs::File::close() at ??:?
0x4020ef05: Settings::load() at ??:?
0x40201864: Print::println() at ??:?
0x4022b524: std::_Function_handler<void (Settings const&), void (*)(Settings const&)>::_M_invoke(std::_Any_data const&, Settings const&) at ??:?
0x4022b26a: setup at ??:?
0x4022a1a4: std::_Function_base::_Base_manager<setup::{lambda(Settings const&)#5}>::_M_manager(std::_Any_data&, std::_Function_base::_Base_manager<setup::{lambda(Settings const&)#5}> const&, std::_Manager_operation) at MQTT433gateway.cpp:?
0x4022a574: std::_Function_handler<void (Settings const&), setup::{lambda(Settings const&)#5}>::_M_invoke(std::_Any_data const&, Settings const&) at MQTT433gateway.cpp:?
0x40202cfc: loop_wrapper() at core_esp8266_main.cpp:?
0x40204ed4: cont_norm at cont.o:?
```


## Acknowledgement
This is heavily inspired by [EspExceptionDecoder](https://github.com/me-no-dev/EspExceptionDecoder).