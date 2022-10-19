
# HWHash
## _Grab all HWInfo realtime sensor information via shared memory and updates them directly to a easily accessible Dictionary._
[![N|Solid](https://i.imgur.com/EyqeszJ.png)](https://divinelain.com)

[![GLWTPL](https://img.shields.io/badge/GLWT-Public_License-red.svg)](https://github.com/me-shaon/GLWTPL)



A tiny, singleton (static) class that reads HWInfo Shared Memory and packs it to a Dictionary.

- 🦄 Single file static class with no external dependencies.
- 😲 Tiny footprint, no memory leaks and 0.01% CPU Usage.
- ✨It simply works.

## Features

- Unique ID for each sensor avoid duplicate name collision
- Compatible with all HWInfo versions with Shared Memory Support
- Collects "Parent Sensor" information such as Name, ID and Instance
- Hashes both the Sensor's Original name and the User Defined name
- Exports an ordered list in the same order as HWInfo UI
- Exports to a JSON string
- Exports a minified List or JSON strong
*check the minified struct version below.*
---
Installation
---
Nuget build is available
```c#
NuGet\Install-Package HWHash -Version 1.1.0
```

Usage
---

It is as simple as:
```c#
HWHash.Launch();
```
---
Options
---
There are three startup options for HWHash.

| Option | Default |
| ------ | ------ |
| HighPrecision | ![](https://img.shields.io/static/v1?label=&message=false&color=ff7da8)  |
| HighPriority | ![](https://img.shields.io/static/v1?label=&message=false&color=ff7da8) |
| Delay | ![](https://img.shields.io/static/v1?label=&message=1000ms&color=b0a2f9) |

---
How to configure
---

High Precision:
```c#
HWHash.HighPrecision = true;
```
High Priority:
```c#
HWHash.HighPriority = true;
```

Delay:
```c#
//update the Dictionary every 500ms (twice per second)
HWHash.SetDelay(500);
```

Then -> Launch():
```c#
HWHash.HighPrecision = true;
HWHash.HighPriority = true;
HWHash.SetDelay(500);
HWHash.Launch();
```
---
Basic Functions
---
```c#
//Returns a List<HWINFO_HASH> in the same order as HWInfo UI
List<HWINFO_HASH> MyHWHashList = HWHash.GetOrderedList();
//Same as above but in a minified version
List<HWINFO_HASH_MINI> MyHWHashListMini = HWHash.GetOrderedListMini();
```
JSON Functions
---
```c#
//Returns a JSON string containing all sensors information (full/mini)
string _HWHashJson = HWHash.GetJsonString();
string _HWHashJsonMini = HWHash.GetJsonStringMini();
//If set to true, it will return a ordered list*
string _HWHashJsonOrdered = HWHash.GetJsonString(true);
//Same for the minified version
string _HWHashJsonMiniOrdered = HWHash.GetJsonStringMini(true);
```
Default Struct
---
This is the base struct, it contains all HWInfo sensor data, such as min, max and avg values.
```c#
 public struct HWINFO_HASH
    {
        public string ReadingType { get; set; }
        public uint SensorIndex { get; set; }
        public uint SensorID { get; set; }
        public ulong UniqueID { get; set; }
        public string NameDefault { get; set; }
        public string NameCustom { get; set; }
        public string Unit { get; set; }
        public double ValueNow { get; set; }
        public double ValueMin { get; set; }
        public double ValueMax { get; set; }
        public double ValueAvg { get; set; }
        public string ParentNameDefault { get; set; }
        public string ParentNameCustom { get; set; }
        public uint ParentID { get; set; }
        public uint ParentInstance { get; set; }
        public ulong ParentUniqueID { get; set; }
        public int IndexOrder { get; set; }
    }
```
Minified Struct
---
The minified version is more suitable for 'realtime' monitoring, since it is packed in a much smaller package.
```c#
public struct HWINFO_HASH_MINI
    {
        public ulong UniqueID { get; set; }
        public string NameCustom { get; set; }
        public string Unit { get; set; }
        public double ValueNow { get; set; }
        [JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingDefault)]
        public int IndexOrder { get; set; }
    }
```

### License
This project is licensed under [GLWTPL](./LICENSE)