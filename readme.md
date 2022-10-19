

# HWHash
## _Grab all HWiNFO realtime sensor information via shared memory and updates them directly to a easily accessible Dictionary._
[![N|Solid](https://i.imgur.com/EyqeszJ.png)](https://divinelain.com)

[![GLWTPL](https://img.shields.io/badge/GLWT-Public_License-red.svg)](https://github.com/me-shaon/GLWTPL)



A tiny, singleton (static) class that reads HWiNFO Shared Memory and packs it to a Dictionary.

- 🦄 Single file static class with no external dependencies.
- 😲 Tiny footprint, no memory leaks and 0.01% CPU Usage.
- 💨Blazing fast, <1millisecond to iterate over 300 sensors.
- ✨It simply works.

## Features

- Unique ID for each sensor avoid duplicate name collision
- Compatible with all HWiNFO versions with Shared Memory Support
- Collects "Parent Sensor" information such as Name, ID and Instance
- Hashes both the Sensor's Original name and the User Defined name
- Exports sensor information in the same order HWiNFO UI
- Exports to a List or JSON string in both Full and Minified versions
**check the minified struct version below.*

Installation
---
Nuget package is available:
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
*Make sure you set the parameters **before** Lauching the HWHash thread.*

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

Then -> ```Launch()```
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
//Returns a List<HWINFO_HASH> in the same order as HWiNFO UI
List<HWiNFO_HASH> MyHWHashList = HWHash.GetOrderedList();
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
This is the base struct, it contains all HWiNFO sensor data, such as min, max and avg values.
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
        [JsonIgnore(Condition = JsonIgnoreCondition.Always)]
        public int IndexOrder { get; set; }
    }
```
---
Performance
---
You can access HWHash performance metrics by invoking the following method:
```c#
HWHashStats _Stats = HWHash.GetHWHashStats();
```
 HWHashStats *struct*
```c#
public struct HWHashStats
    {
        public long CollectionTime { get; set; }
        public uint TotalCategories { get; set; }
        public uint TotalEntries { get; set; }
    }
```
The most critical information we want to inspect is
```c#
...
long ProfilingTime = _Stats.CollectionTime;
...
```
On a decent modern system, even if there are over 300 sensors, profiling times should stay <1 millisecond. Which is not a concern since HWiNFO will flush new data with a minimum delay of 100ms between readings.

[![N|Solid](https://i.imgur.com/NHrArS2.png)]()

*CollectionTime returns the time in milliseconds between each full loop, in the screenshot above, there are 359 distinct sensor readings.*

We know that for Overclockers and Hardware enthusiasts, it is important to have fast, reliable and accurate readings, and a 1 millisecond overhead is well within what is considered a safe margin.

Notes on Sensor Poll Rate
---
This library relies on a third party application, which is HWiNFO, and HWiNFO relies on the exposed sensors from your hardware, such as motherboard sensors, CPU, GPU sensors, etc. 

Usually sensor access/read is deadly fast (nanoseconds) and it is never a bottleneck. There are few rare examples, for instance, on my personal system I have 8 Corsair Memory sticks, and they have a temperature sensor on each stick, out of 359 different readings on my system, the DIMMs are the only ones who take more than nanoseconds to be read, in my case, HWiNFO takes around 6MS to poll the Memory Temperature from all chips. 

Since HWiNFO fastest "poll rate" is 100MS, it is not a problem, but it is definitely something that we should keep an eye upon when reading from sensors exposed by our hardware.

### License
This project is licensed under [GLWTPL](./LICENSE)