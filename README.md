# tzdata #

This repository mirrors the data fetched from <https://data.iana.org/time-zones/releases/> .
It started based on <https://data.iana.org/time-zones/releases/tzdata2021a.tar.gz>.

Additional for windows
<https://raw.githubusercontent.com/unicode-org/cldr/master/common/supplemental/windowsZones.xml>
was added to tzdata folder like described in
<https://howardhinnant.github.io/date/tz.html#Installation> (section "Windows specific").

To keep the database up to date just download the latest version of tzdata and untar it into
the tzdata folder (same for windowsZones.xml).

The CMakeLists.txt file created by Barco provides an easy mechanism to integrate this lib into
BasicToolBox. It is actually an indirect dependency introduced by Howard Hinnant's libdate.
The original CMakeList.txt file libdate was adapted to explicitely express a dependency to tzdata.

For more details have a look into this CMakeLists.txt and the one
in libdate (https://github.com/barcoopensource/date/blob/master/CMakeLists.txt#L39).