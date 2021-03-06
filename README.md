# Headless Selenium for Windows

[![Build status](https://ci.appveyor.com/api/projects/status/v5mtxvfvb2necfkb?svg=true)](https://ci.appveyor.com/project/kybu/headless-selenium-for-win)

It is quite inconvenient that a browser window pops up when running Selenium
tests. It might cause tests to fail because such window needs to hold user
input focus. That is the case with IE at least.

In Linux world, this is solved by running browsers in a virtual frame-buffer.
Similar approach can be taken on Windows platform as well.

Contrary to popular believe, Windows is fully multi-user OS. On top of
that, it supports virtual desktops even though it is not very often used compared
to Linux desktop environments.

This application uses virtual desktops to run web browsers in so that they
do not disturb the main user desktop.

It can be used by any language supported by Selenium Webdriver (Java, C#,
Ruby, ...).

# Download

Binaries can be downloaded from https://github.com/kybu/headless-selenium-for-win/releases 

# Firefox & Chrome

Selenium uses "drivers" to control web browsers. They are standalone
executables driving browsers. `headless_ie_selenium.exe` by default looks
for the IE driver in `PATH`, but it can be instructed to use other drivers
as well. All command line arguments are forwarded to the driver, so the
`HEADLESS_DRIVER` environment variable is used to specify the driver. Put
the driver in one of the `PATH` directories.

Set the `HEADLESS_DRIVER` environment variable to `geckodriver.exe` for
headless Firefox.

# Internet Explorer

When using IE, please pay full attention to configure the driver correctly. All
necessary details can be found at https://code.google.com/p/selenium/wiki/InternetExplorerDriver
in the 'Required Configuration' section and at http://heliumhq.com/docs/internet_explorer

## Setup

Selenium uses a standalone executable called `IEDriverServer.exe` to drive the IE browser window.
Selenium has to be instructed to use the 'headless_ie_selenium.exe' executable to run tests
headlessly.

`headless_ie_selenium.exe` creates a virtual desktop and runs `IEDriverServer.exe` inside of it.
Any command line parameters are passed on to the IE driver.

`IEDriverServer.exe` has to be stored in the searchable path.

New virtual desktop name is `HeadlessDesktop`. If you want to create a unique desktop name each time
the `headless_ie_selenium.exe` executable is run, variable `HEADLESS_UNIQUE` has to exist in the environment.
Its value does not matter, unique desktop name will be generated as long as this variable is present.

## Basic Ruby example

Following example uses www.google.com search to retrieve weather in London.

```ruby
require 'selenium-webdriver'

Dir.chdir(File.dirname $0)

# Generate unique desktop name each run
ENV['HEADLESS_UNIQUE'] = 1

# Find the Headless IE binary.
headlessBinary =
  if File.exists?('../Debug/headless_ie_selenium.exe')
    '../Debug/headless_ie_selenium.exe'
  elsif File.exists?('../Release/headless_ie_selenium.exe')
    '../Release/headless_ie_selenium.exe'
  else
    raise 'Cound not find headless_ie_selenium.exe!'
  end

puts "Headless IE binary found at: #{headlessBinary}"

# The most important part!
Selenium::WebDriver::IE.driver_path = headlessBinary

driver = Selenium::WebDriver.for :ie
driver.get 'https://www.google.com'

sleep 2

searchInput = driver.find_element :name => 'q'

searchInput.send_keys "london weather"
searchInput.submit

sleep 2

weather = driver.find_element :id => 'wob_wc'

puts weather.text

driver.quit
```

Output might be something like this. 'Sunny Weather' can be retrieved
only on rare occasions:

    Headless IE binary: ../Debug/headless_ie_selenium.exe
    London, UK
    Tuesday 11:00
    Partly Cloudy
    19
    °C | °F
    Precipitation: 3%
    Humidity: 69%
    Wind: 0 mph
    TemperaturePrecipitationWind
    1921211915141311
    12:0015:0018:0021:0000:0003:0006:0009:00
    Tue
    22°12°
    Wed
    21°11°
    Thu
    22°12°
    Fri
    22°13°
    Sat
    23°13°
    Sun
    23°16°
    Mon
    22°14°
    Tue
    22°13°

# Executables

'Headless Selenium for Windows' comes with these two binaries: `desktop_utils.exe` and `headless_ie_selenium.exe`

## headless_ie_selenium.exe

This executable runs IE Webdriver `IEDriverServer.exe` headlessly. It is
meant to be used by Selenium Webdriver library instead of IE Webdriver.
Other Selenium drivers can be used by specifying `HEADLESS_DRIVER`
environment variable.

## desktop_utils.exe

Generic Windows virtual desktop utility. It can run applications in virtual desktops, etc ...

Command line options:

    Desktop utils v1.4, Peter Vrabel (c) 2014-2017:
      -h [ --help ]               Feeling desperate?
      -r [ --run ] arg            Command to run headlessly.
      -n [ --desktop ] arg        Set the headless desktop name. Used with '--run'.
                                  Optional, default = HeadlessDesktop
      -l [ --list ]               List available desktops of current Window
                                  station.
      -s [ --switch-to ] arg      Switch to a desktop. Takes a desktop name from
                                  the list of desktops.
      -t [ --switch-to-default ]  Switch to the default desktop. Can be used if you
                                  are being stranded.
      -x [ --no-explorer ]        Don't run explorer in the created desktop


# Technical details

Tested on Win 7.

Supported browsers: IE, Firefox, Chrome.

Developed using VS 2013 Express, Boost 1.59 and a bit of system level
programming skills.

# License

GPLv3, Copyright 2014-2017 Peter Vrabel

