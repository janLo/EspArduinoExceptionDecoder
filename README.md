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
epc1=0x4022ca68 epc2=0x00000000 epc3=0x00000000 excvaddr=0x4026b579 depc=0x00000000

ctx: cont
sp: 3fff2250 end: 3fff2590 offset: 01a0

>>>stack>>>
3fff23f0:  00000000 00000000 3fff5a50 4020d548
3fff2400:  00000003 3fff13c4 3fff5a50 4020ea3c
3fff2410:  3fff4bb4 00000066 00000000 40229598
3fff2420:  3fff46dc 00000010 00000000 40229598
3fff2430:  3fff0000 00000001 00000000 4020560d
3fff2440:  3fff4c44 00000280 4020d04c 40229524
3fff2450:  3fff4bb4 3fff24ec 4020d014 40229598
3fff2460:  3fff5314 3fff24c0 00000000 40202b20
3fff2470:  3fff5a50 00000001 00000000 40203b67
3fff2480:  00010101 3f010101 3fff5a2c 40228d4c
3fff2490:  3fff2432 00000001 3fff24c0 402013d2
3fff24a0:  3fff59ec 3fff5a2c 00000000 3fff13c4
3fff24b0:  3fff5a50 3fff24ec 00000000 4020ebfd
3fff24c0:  3ffe98b0 00000000 000003e8 00008c1c
3fff24d0:  00000000 00000000 3fff6174 0000000f
3fff24e0:  00000000 3fff24ec 0000000a 3ffe9838
3fff24f0:  3fff59d0 3fff5d64 00000800 40201864
3fff2500:  3fff13c4 3fff2de0 3fff2de0 4022b498
3fff2510:  3fff13c4 3fff2de0 00000000 4022b1de
3fff2520:  3fff4bb4 feefeffe 4022a150 4022a4ec
3fff2530:  feefeffe feefeffe feefeffe feefeffe
3fff2540:  feefeffe feefeffe feefeffe feefeffe
3fff2550:  feefeffe feefeffe feefeffe feefeffe
3fff2560:  feefeffe feefeffe feefeffe 3fff1564
3fff2570:  3fffdad0 00000000 3fff155c 40202cfc
3fff2580:  feefeffe feefeffe 3fff1570 40204eb0
<<<stack<<<
```

You can dump it in a file called `myStackTrace.txt` and run the `decode.py` script:

```
$ <...>/decoder.py -e .pioenvs/d1_mini/firmware.elf myStackTrace.txt1
```

This prints the processed stacktrace:

```
Exception: 3 (LoadStoreError: Processor internal physical address or data error during load or store)

epc1:     0x4022ca68: strcmp at /Users/igrokhotkov/e/newlib-xtensa/xtensa-lx106-elf/newlib/libc/machine/xtensa/../../../../.././newlib/libc/machine/xtensa/strcmp.S:470
epc2:     0x00000000
epc3:     0x00000000
excvaddr: 0x4026b579: chip_v6_unset_chanfreq at ??:?
depc:     0x00000000

ctx: cont

sp:       0x3fff2250
end:      0x3fff2590
offset:   0x000001a0

stack:
0x4020d548: bool ArduinoJson::JsonObject::containsKey<__FlashStringHelper>(__FlashStringHelper const*) const at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x4020ea3c: Settings::applyJson(ArduinoJson::JsonObject&) at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x40229598: String::length() const at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/WString.h:79
  \-> inlined by: operator() at /home/jan/devel/MQTT433gateway/lib/Settings/Settings.cpp:67
  \-> inlined by: _M_invoke at /home/jan/.platformio/packages/toolchain-xtensa/xtensa-lx106-elf/include/c++/4.8.2/functional:2057
0x40229598: String::length() const at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/WString.h:79
  \-> inlined by: operator() at /home/jan/devel/MQTT433gateway/lib/Settings/Settings.cpp:67
  \-> inlined by: _M_invoke at /home/jan/.platformio/packages/toolchain-xtensa/xtensa-lx106-elf/include/c++/4.8.2/functional:2057
0x4020560d: spiffs_hydro_read at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/spiffs/spiffs_hydrogen.c:1122
  \-> inlined by: SPIFFS_read at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/spiffs/spiffs_hydrogen.c:427
0x4020d04c: std::_Function_base::_Base_manager<std::function<bool (unsigned short const&)> notZero<unsigned short>()::{lambda(unsigned short const&)#1}>::_M_manager(std::_Any_data&, std::_Function_base::_Base_manager<std::function<bool (unsigned short const&)> notZero<unsigned short>()::{lambda(unsigned short const&)#1}> const&, std::_Manager_operation) at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x40229524: std::function<bool (unsigned short const&)> notZero<unsigned short>()::{lambda(unsigned short const&)#1}::operator()(unsigned short const&) const at /home/jan/devel/MQTT433gateway/lib/Settings/Settings.cpp:72
  \-> inlined by: std::_Function_handler<bool (unsigned short const&), std::function<bool (unsigned short const&)> notZero<unsigned short>()::{lambda(unsigned short const&)#1}>::_M_invoke(std::_Any_data const&, unsigned short const&) at /home/jan/.platformio/packages/toolchain-xtensa/xtensa-lx106-elf/include/c++/4.8.2/functional:2057
0x4020d014: _M_manager at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x40229598: String::length() const at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/WString.h:79
  \-> inlined by: operator() at /home/jan/devel/MQTT433gateway/lib/Settings/Settings.cpp:67
  \-> inlined by: _M_invoke at /home/jan/.platformio/packages/toolchain-xtensa/xtensa-lx106-elf/include/c++/4.8.2/functional:2057
0x40202b20: operator delete(void*) at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/abi.cpp:57
0x40203b67: std::_Sp_counted_deleter<SPIFFSFileImpl*, std::__shared_ptr<SPIFFSFileImpl, (__gnu_cxx::_Lock_policy)0>::_Deleter<std::allocator<SPIFFSFileImpl> >, std::allocator<SPIFFSFileImpl>, (__gnu_cxx::_Lock_policy)0>::_M_destroy() at /home/jan/.platformio/packages/toolchain-xtensa/xtensa-lx106-elf/include/c++/4.8.2/bits/shared_ptr_base.h:357
0x40228d4c: std::__shared_count<(__gnu_cxx::_Lock_policy)0>::~__shared_count() at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/libraries/ESP8266mDNS/ESP8266mDNS.cpp:396
0x402013d2: fs::File::close() at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/FS.cpp:251
0x4020ebfd: Settings::load() at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x40201864: Print::println() at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/Print.cpp:187
0x4022b498: std::_Function_handler<void (Settings const&), void (*)(Settings const&)>::_M_invoke(std::_Any_data const&, Settings const&) at /home/jan/.platformio/packages/toolchain-xtensa/xtensa-lx106-elf/include/c++/4.8.2/functional:2069
0x4022b1de: setup at /home/jan/devel/MQTT433gateway/src/MQTT433gateway.cpp:207
0x4022a150: _M_manager at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x4022a4ec: _M_invoke at /home/jan/devel/MQTT433gateway/.piolibdeps/ArduinoJson_ID64/src/ArduinoJson/Serialization/JsonSerializerImpl.hpp:56
0x40202cfc: loop_wrapper at /home/jan/.platformio/packages/framework-arduinoespressif8266@src-31d658a59f41540201fc3726a1394910/cores/esp8266/core_esp8266_main.cpp:57
0x40204eb0: cont_norm at cont.o:?
```


## Acknowledgement
This is heavily inspired by [EspExceptionDecoder](https://github.com/me-no-dev/EspExceptionDecoder).
