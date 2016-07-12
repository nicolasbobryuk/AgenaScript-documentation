
#Keywords

Add()
-----

### Description

The add method allows you to add plots or line objects to the chart. When a new plot object is added using Add(), this automatically creates a data series of the type DataSeries, which is attached to this object. The value collection allows you to reference and access this data series.
Add() can be used with the Initialize() and the OnBarUpdate() methods.

### Parameter

plot – a [*Plot*] object
line – a [*Line*] object

### Usage

**Add**(Plot plot)

**Add**(Line line)

### Example

\#**region** Usings

**using** System;

**using** System.Collections.Generic;

**using** System.ComponentModel;

**using** System.Drawing;

**using** System.Linq;

**using** System.Xml;

**using** System.Xml.Serialization;

**using** AgenaTrader.API;

**using** AgenaTrader.Custom;

**using** AgenaTrader.Plugins;

**using** AgenaTrader.Helper;

\#**endregion**

**namespace** AgenaTrader.UserCode

{

\[**Description**("Enter the description for the new custom indicator here")\]

**public** class MyIndicator : UserIndicator

{

**protected** override void **Initialize**()

{

// Two blue lines will be placed into the chart, one at 70 and the other at 30

**Add**(**new Line**(Color.Blue, 70, "UpperLine"));

**Add**(**new Line**(Color.Blue, 30, "LowerLine"));

// Add 2 plots

**Add**(**new Plot**(Color.Red, "myFastSMA"));

**Add**(**new Plot**(Color.Blue, "mySlowSMA"));

}

**protected** override void **OnBarUpdate**()

{

//The set method is assigned to the value of the current bar

FastSMA.**Set**( **SMA**(8)\[0\] ); // is identical with Values\[0\].Set( SMA(8)\[0\] );

SlowSMA.**Set**( **SMA**(50)\[0\] ); // is identical with Values\[1\].Set( SMA(50)\[0\] );

}

// Two data series are made available here

// These are not necessary for the display of the indicator // With the help of these series, one indicator can access the other

// For example: double d = MyIndicator.FastSMA\[0\] - MyIndicator.SlowSMA\[0\];

\[**Browsable**(**false**)\]

\[**XmlIgnore**()\]

**public** DataSeries FastSMA

{

get { return Values\[0\]; }

}

\[**Browsable**(**false**)\]

\[**XmlIgnore**()\]

**public** DataSeries SlowSMA

{

get { return Values\[1\]; }

}

}

}

Alert()
-------

### Description

The alert method creates an acoustic and/or visual alarm.

### Usage

**Alert**(string message, **bool** showMessageBox, string soundLocation);

Due to compatability reasons, an old signature is still used here. When using this method, the color settings and the “re-arm seconds” parameter are ignored.

**Alert**(string id, AlertPriority priority, string message, string soundLocation, **int** rearmSeconds, Color backColor, Color forColor);

### 

### Return Value

None

### Parameter

|                |                                                                                                                       |
|----------------|-----------------------------------------------------------------------------------------------------------------------|
| message        | Alert text displayed within the messages tab                                                                          |
| soundLocation  | Name of a sound file in the \*.wav format. If no path is specified, then “My Documents\\AgenaTrader\\Sounds\\ is used |
| showMessageBox | If set to “true”, a message box will be displayed in addition to the sound                                            |

### Example

// Message will be outputted if the SMA(20) crosses below the SMA(50)

**if** (**CrossBelow**(**SMA**(20), **SMA**(50), 1))

**Alert**("Check short signal!", **true**, "Alert4.wav");

To use music files in a different path, you need to specify the path:

string pathOfSoundfile = Environment.**GetFolderPath**(Environment.SpecialFolder.MyDocuments)+@"\\MyAlertSounds\\";

string nameOfSoundFile = "MyAlertSoundFile.wav";

**Alert**("Message text", **true**, pathOfSoundfile + nameOfSoundFile);

AllowRemovalOfDrawObjects
-------------------------

### Description

“AllowRemovalOfDrawObjects” is a property of indicators that can be set under [*Initialize()*].

**AllowRemovalOfDrawObjects = true**

Drawing objects that are drawn by an indicator or a strategy can be manually removed from the chart.

**AllowRemovalOfDrawObjects = false (default)**

Drawing objects that have been created by a strategy or indicator CANNOT be manually removed from the chart. They are removed once the indicator or strategy is removed.

This property can be queried and will return “true” or “false”.

### Usage

AllowRemovalOfDrawObjects

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//Drawing objects can be manually removed from the chart

AllowRemovalOfDrawObjects = **true**;

}

Attribute
---------

Attribute is a component of the C\# language. Within AgenaScript, indicators, and strategies, you can use these attributes in the same manner as you would in C\#.
Information regarding the usage of attributes can be found here:

[*http://msdn.microsoft.com/de-de/library/z0w1kczw%28v=vs.80%29.aspx*]

The most commonly used attributes in AgenaScript are:

-   [*Browsable*]

-   [*Category*]

-   [*ConditionalValue*]

-   [*Description*]

-   [*DisplayName*]

-   [*TimeFrameRequirements*]

-   [*XmlIgnore*]

Browsable
---------

Browsable is an *[Attribut]e* within AgenaScript.

AgenaScript uses public variables for entering parameters for indicators (such as periods for the SMA) and for outputting events and calculations within indicators (for example, data series).
Variables used for entering parameters must be displayed in the properties dialog. Data series are exempt from this.
Public variables with the browsable attribute set to false are not displayed within the properties dialog.

By default, browsable is set to true. Therefore, within a variable containing an entry parameter, the attribute does not need to be specified.

**Example for a parameter:**

The parameter should be displayed and queried in the properties window. Therefore browsable should be set to true.

\[Description("Numbers of bars used for calculations")\]

\[Category("Parameters")\]

public int Period

{

get { return period; }

set { period = Math.Max(1, value); }

}

**Example for a data series:**

\[Browsable(false)\]

\[DisplayName("Lower band")\]

\[XmlIgnore\]

public DataSeries Lower

{

get { return Values\[0\]; }

}

Category
--------

Category is an *[Attribut]e* in AgenaScript.

The category attribute defines under which category in the properties dialog the parameter is shown.
If this attribute is missing, the parameters category is accepted as the standard.

The following example shows how to create the new category “My Parameters” in the properties dialog:

\[Category("My Parameters")\]

\[DisplayName("Period number")\]

public double \_period

{

get { return \_period; }

set { \_period = value; }

}

<img src=".//media/image11.png" width="520" height="565" />

ConditionalValue
----------------

Conditional value is an *[Attribut]e* in AgenaScript.

Normally, when making comparisons within the ConditionEscort, the data series generated by indicators are used. One such example would be checking whether a moving average lies above or below a specific price value.
An indicator can also yield values that are not contained within data series, such as values of the type int, double, char, Boolean, string, etc.
To use these values within the scanner or ConditionEscort, they have to be labeled with the conditional value attribute.

\[**Browsable**(**false**)\]

\[XmlIgnore\]

\[ConditionalValue\]

**public int** PublicVariable

{

get

{

**Update**();

return \_internVariable;

}

}

Description
-----------

Description is an attribute in AgenaScript.

The description attribute is used in AgenaScript for classes and public variables.
As an attribute of the class, the text is a description of the function of the entire indicator.

\[Description("Displays the tick count of a bar.")\]

public class TickCounter : UserIndicator

{

...

As an attribute of a public variable, the text is a description of the function of the parameter.

\[Description("Number of standard deviations")\]

\[DisplayName("\# of std. dev.")\]

public double NumStdDev

{

get { return numStdDev; }

set { numStdDev = Math.Max(0, value); }

}

The descriptions are displayed in the relevant properties dialog.

DisplayName
-----------

Display name is an attribute in AgenaScript.

The display name attribute defines the text shown in the properties dialog for the parameter.

If this attribute is not specified, the name of the public variable is used.

\[Description("Number of standard deviations")\]

\[DisplayName("\# of std. dev.")\]

public double NumStdDev

{

get { return numStdDev; }

set { numStdDev = Math.Max(0, value); }

}

TimeFrameRequirements
---------------------

Timeframe requirements is an attribute in AgenaScripts.

If you want a script to use data from various timeframes, the class requires the attribute „TimeFrameRequirements“. You can specify multiple timeframes here:

\[**TimeFrameRequirements**("1 day")\]

\[**TimeFrameRequirements**("15 minutes", "1 day", "1 week")\]

The amount of data provided for the other timeframes will always be the same as the number of actual candles loaded into the chart. If there are 500 candles for a 5-minute chart, then 500 candles of another timeframe will also be loaded. In the first example above, 500 daily candles will be loaded. In the second example, 500 15-minute candles, 500 daily candles and 500 weekly candles will be loaded.
The amount of data can become rather large very quickly, thus you should take precautions when using this attribute.

See [*MultiBars*].

**Important:**

If a class uses a different indicator that requires one or more secondary timeframes, then the “TimeFrameRequirements” attribute must be set for the class retrieving the data. An example for this can be seen here: [*GetDayBar*].

XMLIgnore
---------

XML ignore is an attribute in AgenaScript.

AgenaTrader saves all parameter settings for the indicators in a template. The template files are saved in an XML format. In order to avoid a parameter being saved as part of the template, the attribute XML ignore can be set.

To save parameters in an XML file, the values must be serialized. Under most circumstances, AgenaTrader performs this automatically. Self-defined data types cannot be serialized automatically, so in this case the programmer is responsible for the correct serialization.
In the following example, the color and font are used as parameters of an indicator. AgenaTrader has two methods for serializing color and font information (TextColorSerialize and TextFontSerialize). Both parameters – TextColor and TextFont – thus need to be marked with the XML ignore parameter.

**private** Color \_textColor = Color.Blue;

**private** Font \_textFont = **new Font**("Arial", 12, FontStyle.Bold);

\[XmlIgnore\]

\[Description("Textcolor")\]

public Color TextColor

{

get { return \_textColor; }

set { \_textColor = value; }

}

\[Browsable(false)\]

public string TextColorSerialize

{

get { return SerializableColor.ToString(\_textColor); }

set { \_textColor = SerializableColor.FromString(value); }

}

\[XmlIgnore()\]

\[Description("TextFont")\]

public Font TextFont

{

get { return \_textFont; }

set { \_textFont = value; }

}

\[Browsable(false)\]

public string TextFontSerialize

{

get { return SerializableFont.ToString(\_textFont); }

set { \_textFont = SerializableFont.FromString(value); }

}

AutoScale
---------

### Description

Auto scale is a property of indicators that can be set within the Initialize() method.

**AutoScale = true (default)**

The price axis (y-axis) of the chart is set so that all plots and lines of an indicator are visible.

**AutoScale = false**

Plots and lines of an indicator or strategy are not accounted for in the scaling of the y-axis. Therefore they may lie outside of the visible chart area.

This property can be queried and will return either “true” or “false”.

### Usage

AutoScale

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//Scale the chart so that all drawing objects are visible

AutoScale = **true**;

}

BarsRequired
------------

### Description

The property “BarsRequired” determines how many historical bars are required for an indicator or a strategy to call up the OnBarUpdate() method for the first time and thus begin the calculations. Bars required should be set within the Initialize() method.
The setting should be chosen carefully. If you require 100 days for the calculation of a moving average, then you should ensure that at least 100 days of historical data are loaded.
The property can be queried in the script and will return an int value.

When OnBarUpdate is called up for the first time, the CurrentBar property is 0 regardless of the value of BarsRequired.

### Usage

BarsRequired

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//The indicator requires a minimum of 50 bars loaded into the history

BarsRequired = 50;

}

CalculateOnBarClose
-------------------

### Description

The property “CalculateOnBarClose” determines the events for which AgenaTrader can call up the OnBarUpdate() method.

**CalculateOnBarClose = true
**OnBarUpdate() is called up when a bar is closed and the next incoming tick creates a new bar.

**CalculateOnBarClose = false
**OnBarUpdate() is called up for each new incoming tick.
If you are running AgenaTrader on older hardware, this may cause performance issues with instruments that are highly liquid.
The property can be queried in the script and will return a value of the type Boolean (true or false).
CalculateOnBarClose can be used within Initialize() and also within OnBarUpdate().
OnBarUpdate is only called up for the closing price of each bar with historical data, even if CalculateOnBarClose is set to false.
When an indicator is called up by another indicator, the CalculateOnBarClose property of the retrieved indicator overwrites the indicator performing the retrieving.

### Usage

CalculateOnBarClose

### More Information

See [*Bars*].

### Example

**protected** override void **Initialize**()

{

//Indicator calculation should only occur when a bar has closed/finished

CalculateOnBarClose = **true**;

}

ChartControl
------------

Chart control is an object that provides reading access of various properties for the chart.

The important properties are:

-   ChartFontColor, BackColor

-   UpColor, DownColor

-   Font

-   BarMarginLeft, BarMarginRight

-   BarSpace, BarWidth

-   BarsPainted

-   FirstBarPainted, LastBarPainted

-   BarsVisible

-   FirstBarVisible, LastBarVisible

-   GetXByBarIdx, GetYByValue

An example can be seen here: [*PlotSample*].

**BarsPainted und BarsVisible:**

BarsPainted contains the number of bars that a chart *could* display from the left to right border with the current width and distance of the candles.
BarsVisible contains the number of bars actually visible.

**FirstBarPainted und FirstBarVisible:**

FirstBarPainted contains the number of the bar that *would* be displayed on the left border of the chart.

FirstBarVisible contains the number of the bar that is actually shown as the first bar on the left side of the chart area.

Example: the chart has been moved so that the first bar of the chart is now in the middle of the chart.

FirstBarPainted would be negative.

FirstBarVisible would be 0.

**LastBarPainted und LastBarVisible:**

LastBarPainted contains the number of the bar that *would* be displayed on the right border of the chart.

LastBarVisible contains the number of the bar that is actually displayed on the right side of the chart.

Example: the chart has been moved so that the last bar of the chart is displayed in the middle section.

LastBarPainted would be larger than Bars.Count.

LastBarVisible would be Bars.Count -1.

ClearOutputWindow()
-------------------

### Description

The ClearOutputWindow() method empties the output window. The method can be used within Initialize() as well as within OnBarUpdate().
The output window contains all outputs that have been created with the [*Print()*] command.
Using the output window is a great method for code debugging.

### Usage

ClearOutputWindow()

### Parameter

none

### Return Value

none

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

// Delete the content of the output window

ClearOutputWindow();

}

CrossAbove()
------------

### Description

The CrossAbove() method allows you to check whether a crossing of two values has occurred (from bottom to top) within a predefined number of periods. The values can be a market price, an indicator, a data series or a constant value.

See [*CrossBelow()*], [*Rising()*], [*Falling()*].

### Usage

**CrossAbove**(IDataSeries series1, **double value**, **int** lookBackPeriod)

**CrossAbove**(IDataSeries series1, IDataSeries series2, **int** lookBackPeriod)

### Return Value

**true** a cross has occurred
**false** a cross has not occurred

### Parameter

|                     |                                                          |
|---------------------|----------------------------------------------------------|
| lookBackPeriod      | Number of bars within which a cross will be searched for |
| series1 und series2 | A data series such as an indicator, close, high, etc.    |
| value               | A constant value of the type double                      |

### Example

// Puts out a notice if the SMA(20) crosses above the SMA(50)

**if** (**CrossAbove**(**SMA**(20), **SMA**(50), 1))

**Print**("SMA(20) has risen above SMA(50)!");

// Puts out a notice if the SMA(20) crosses above the value of 40

**if** (**CrossAbove**(**SMA**(20), 40, 1))

**Print**("SMA(20) has risen above 40!");

// Put out a notice for a long entry if the SMA(20) has crossed above the SMA(50) within the last 5 bars.

**if** (**CrossAbove**(**SMA**(20), **SMA**(50), 1) && Close\[0\] &gt; Close\[1\])

**Print**("Long entry !!!");

CrossBelow()
------------

### Description

Using the CrossBelow() method, you can test whether or not a cross below has occurred within a predefined number of periods. The values can be the market price, an indicator, any data series, or a constant value.

See [*CrossAbove()*], [*Rising()*], [*Falling()*].

### Usage

**CrossBelow**(IDataSeries series1, **double value**, **int** lookBackPeriod)

**CrossBelow**(IDataSeries series1, IDataSeries series2, **int** lookBackPeriod)

### Return Value

**true** a cross has occurred
**false** a cross has not occurred

### Parameter

|                     |                                                          |
|---------------------|----------------------------------------------------------|
| lookBackPeriod      | Number of Bars within which a cross will be searched for |
| series1 und series2 | A data series such as an indicator, close, high etc.     |
| value               | A constant value of the type double                      |

### Example

// Puts out a notice if the SMA(20) crosses below the SMA(50)

**if** (**CrossBelow**(**SMA**(20), **SMA**(50), 1))

**Print**("SMA(20) has fallen below SMA(50)!");

// Puts out a notice if the SMA(20) falls below the value of 40

**if** (**CrossBelow**(**SMA**(20), 40, 1))

**Print**("SMA(20) has fallen below 40!");

// Puts out a notice for a short entry if a crossing of the SMA(20) below the SMA(50) has occurred within the last 5 bars.

.

**if** (**CrossBelow**(**SMA**(20), **SMA**(50), 1) && Close\[1\] &gt; Close\[0\])

**Print**("Short entry !!!");

CurrentBar
----------

### Description

Current bar is a method of indexing bars used in the OnBarUpdate() method. If a chart contains 500 bars and an indicator is to be calculated on the basis of these, then AgenaTrader will begin calculating from the oldest bar. The oldest bar receives the number 0. Once the calculation for this bar has been completed, the OnBarUpdate() method is called up for the next bar, which in turn receives the number 1. This continues until the last bar, which receives a value of 500.

### Parameter

none

### Return Value

Current bar is a variable of the type int, which always contains the number of the bar currently being used.

### Usage

CurrentBar

### More Information

The OnBarUpdate() method uses numbering different from that of CurrentBar in terms of the [*Barindex*][*Bars*]. Understanding this difference is of great importance, which is why we ask you to please read the following paragraph carefully:

CurrentBar numbers continuously from the oldest to youngest bar starting with 0. The BarIndex for the youngest bar is always 0. In the example referenced below this paragraph, Time\[0\] stands for the timestamp of the current bar. The index of the oldest bar always has 1 added to it. Thus a logical numbering of barsAgo is possible. The timestamp for the bar of 5 periods ago is Time\[5\].
For using multiple timeframes (multi-bars) in an indicator, see CurrentBars.

### Example

**protected** override void **OnBarUpdate**()

{

**Print**("Call of OnBarUpdate for bar nr. " + CurrentBar + " of " + Time\[0\]);

}

DatafeedHistoryPeriodicity
--------------------------

### Description

Datafeed history periodicity is a data type.

### Definition

public enum DatafeedHistoryPeriodicity

-   DatafeedHistoryPeriodicity.Tick

-   DatafeedHistoryPeriodicity.Second

-   DatafeedHistoryPeriodicity.Minute

-   DatafeedHistoryPeriodicity.Hour

-   DatafeedHistoryPeriodicity.Day

-   DatafeedHistoryPeriodicity.Week

-   DatafeedHistoryPeriodicity.Month

-   DatafeedHistoryPeriodicity.Volume

-   DatafeedHistoryPeriodicity.Range

-   DatafeedHistoryPeriodicity.Quarter

-   DatafeedHistoryPeriodicity.Year

-   DatafeedHistoryPeriodicity.HeikinAshi

-   DatafeedHistoryPeriodicity.Renko

-   DatafeedHistoryPeriodicity.LineBreak

-   DatafeedHistoryPeriodicity.Kagi

-   DatafeedHistoryPeriodicity.PointAndFigure

-   DatafeedHistoryPeriodicity.Custom

See *TimeFrame*, *TimeFrames*.

DataSeries
----------

### Description

Data series (data rows) are an easy yet powerful method of saving additional values for individual bars. For example, when calculating the smoothing average, each bar is assigned the value calculated for this bar.
A data series is an array that contains as many elements as there are bars displayed in a chart. AgenaTrader ensures that data series are correctly synchronized with the bars.
Data series are used in exactly the same way as the close or time series. They can therefore also be used for the input data for various indicators.
In the table below you will find 4 newly created data series (highlighted). Each data series has exactly one value of a special data type (int, bool, string) attached to it per bar. The indexing with barsAgo is thus identical to the data series provided by the system.

<img src=".//media/image12.png" width="318" height="248" />

### Usable Data Series in AgenaTrader

[*BoolSeries*]

[*DataSeries*]

[*DateTimeSeries*]

[*FloatSeries*]

[*IntSeries*]

[*LongSeries*]

[*StringSeries*]

In addition, there are also data series such as ColorSeries, although these are only used for internal purposes and should not be used directly.
To change the color of plots, please use [*PlotColors*].

### Set(), Reset() und ContainsValue()

Each data series contains a **Set()**, **Reset()** and **ContainsValue()** method.
With Set(value) or Set(int barsAgo, value) you can place values into the data series for the current position, or in this case into the barsAgo position.
With Reset() or Reset(int barsAgo) you can delete a value from the data series for the current position or for the barsAgo position. This has the result that no valid value exists at this position any more.
Programming with the help of the reset method can simplify otherwise complex logic. This is especially true for Boolean series, where only “true” or “false” values can be included.
The ContainsValue() checks whether a data series has a value for a specific position.

### Information about Data Types

[*http://msdn.microsoft.com/de-de/library/s1ax56ch%28v=vs.80%29.aspx*]

BoolSeries
----------

### Description

Bool series is a data series that contains a Boolean value for each bar. The number of elements in this series correlates with the exact number of bars within the chart.

### Create New Bool Series

In the area for the declaration of variables, simply declare a new variable:

//Variable declaration

**private** BoolSeries myBoolSeries;

With the Initialize() method, this variable assigns a new instance of the Bool series:

**protected** override void **Initialize**()

{

myBoolSeries = **new BoolSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the data series for the current position:

myBoolSeries.**Set**(**true**);

Writing a value in the past into the data series:

myBoolSeries.**Set**(**int** barsAgo, **bool** Value);

### Delete Values

Removing the current value for the data series:

myBoolSeries.**Reset**();

Removing a value in the past from the data series:

myBoolSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myBoolSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** ("For the bar of " + Time\[0\] + " ago the value of the data series is: " + myBoolSeries\[0\]);

### Example

protected override void OnBarUpdate()

{

**if** (Close\[0\] &gt; Open\[0\])

myBoolSeries.**Set**(**true**);

**else**

myBoolSeries.**Set**(**false**);

}

DataSeries
----------

### Description

Data series is a [*DataSeries*][*Data series*] that can contain a double value for each bar. The number of elements in this series corresponds to the exact number of bars within the charts.

Data series for double values are the data series most commonly used for indicators.

### Create a New Data Series

In the declaration area for variables:

//Variable declaration

**private** DataSeries myDataSeries;

With the Initialize() method, this variable is assigned a new instance:

**protected** override void **Initialize**()

{

myDataSeries = **new DataSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the data series for the current position:

myDataSeries.Set(Bars\[0\].Close);

Writing a value in the past into the data series:

myDataSeries.**Set**(**int** barsAgo, **duble** Value);

### Delete Values

Removing the current value from the data series:

myDataSeries.**Reset**();

Removing a value in the past from the data series:

myDataSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myDataSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** ("For the bar from " + Time\[0\] + " ago the value for the data series is: " + myDataSeries\[0\]);

### Example

//Saves the span between the high and low of a bar

myDataSeries.**Set**(Math.**Abs**(High\[0\]-Low\[0\]));

DateTimeSeries
--------------

### Description

Date time series is a [*DataSeries*][*Data series*] that can record a date time value for each bar. The number of elements in this series corresponds to the number of bars in the chart.

### Create a New Data Series

Create a new variable in the declaration area:

//Variable declaration

**private** DateTimeSeries myDataSeries;

Assign a new instance of DateTimeSeries for the variable with the Initialize() method:

**protected** override void **Initialize**()

{

myDataSeries = **new DateTimeSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the current position of the data series:

myDataSeries.**Set**(DateTime Value);

Writing a value from the past into the data series:

myDataSeries.**Set**(**int** barsAgo, DateTime Value);

### Delete Values

Removing the current value from the data series:

myDataSeries.**Reset**();

Remove a past value from the data series:

myDataSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myDataSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** ("For the bar from " + Time\[0\] + " ago the value of the data series is: " + myDataSeries\[0\]);

### Example

//Saves the difference of -6 hours (eastern time, New York) for a time zone conversion

myDataSeries.**Set**(Time\[0\].**AddHours**(-6);

FloatSeries
-----------

### Description

Float series is a DataSeries that contains a float value for each bar in the chart. The number of elements in this series corresponds to the number of bars within the chart.

### Create a New Data Series

Create a new variable in the declaration area:

//Variable declaration

**private** FloatSeries myDataSeries;

Assign a new instance of the FloatSeries to the variable with the Initialize() method:

**protected** override void **Initialize**()

{

myDatatSeries = **new FloatSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the current position of the data series

myDataSeries.**Set**(**float** Value);

Writing a value from the past into the data series:

myDataSeries.**Set**(**int** barsAgo, **float** Value);

### Delete Values

Removing the current value from the data series:

myDataSeries.**Reset**();

Removing a value located in the past from the data series:

myDataSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myDataSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** ("For the bar from " + Time\[0\] + " ago the value for the data series is: " + myDataSeries\[0\]);

### Example

//Saves the span between the high and the low of a bar

myDataSeries.**Set**(Math.**Abs**((**float**) High\[0\] - (**float**) Low\[0\]));

IntSeries
---------

### Description

Int series is a data series that can assign an integer value for each bar. The number of elements in this series corresponds to the number of bars within the chart.

### Create a New Data Series

Create a new variable in the declaration area:

//Variable declaration

**private** IntSeries myDataSeries;

Assign an instance of the int series to the variable with the Initialize() method:

**protected** override void **Initialize**()

{

myDataSeries = **new IntSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the current position of the data series

myDataSeries.**Set**(**int** Value);

Writing a value from the past into the data series:

myDataSeries.**Set**(**int** barsAgo, **int** Value);

### Delete Values

Removing the current value from the data series:

myDataSeries.**Reset**();

Removing a value located in the past from the data series:

myDataSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myDataSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** (For the bar from + Time\[0\] + the value of the data series is:+ myDataSeries\[0\]);

### Example

//Saves the span in ticks between high and low for each bar

myDataSeries.**Set**((**int**) ((High\[0\] - Low\[0\]) / TickSize));

LongSeries
----------

### Description

Long series is a data series that can include an integer value for each bar. The number of elements in this series corresponds to the number of bars within the chart.

### Create a New Data Series

Create a new variable in the declaration area:

//Variable declaration

**private** LongSeries myDataSeries;

Assign a new instance of the long series to the variable with the Initialize() method:

**protected** override void **Initialize**()

{

myDataSeries = **new LongSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the current position of the data series:

myDataSeries.**Set**(**long** Value);

Writing a value from the past into the data deries:

myDataSeries.**Set**(**int** barsAgo, **long** Value);

### Delete Values

Removing the current value from the data series:

myDataSeries.**Reset**();

Removing a value located in the past from the data series:

myDataSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myDataSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** (For the bar from + Time\[0\] + the value of the data series is:+ myDataSeries\[0\]);

### Example

//Saves the span of ticks between high and low for each bar

myDataSeries.**Set**((**long**) ((High\[0\] - Low\[0\]) / TickSize));

StringSeries
------------

### Description

String series is a data series for string values that are saved for each bar. The number of elements in this series corresponds to the number of bars within the chart.

### Create a New Data Series

Create a new variable in the declaration area:

//Variable declaration

**private** StringSeries myDataSeries;

Assign an instance of string series to the variable with the Initialize() method:

**protected** override void **Initialize**()

{

myDataSeries = **new StringSeries**(**this**);

CalculateOnBarClose = **true**;

}

### Assign Values

Assigning a value to the current position of the data series:

myDataSeries.**Set**(string Value);

Writing a value from the past into the data series:

myDataSeries.**Set**(**int** barsAgo, string Value);

### Delete Values

Remove the current value from the data series:

myDataSeries.**Reset**();

Remove a value located in the past from the data series:

myDataSeries.**Reset**(**int** barsAgo);

### Check Values for their Validity

myDataSeries.**ContainsValue**(**int** barsAgo);

### Read Value

**Print** (For the bar from + Time\[0\] + the value of the data series is:+ myDataSeries\[0\]);

### Example

//Save the current calendar day for each bar (Monday… Tuesday etc.)

myDataSeries.**Set**(string.**Format**("{0:dddd}", Time\[0\]));

DayOfWeek
---------

### Description

“DayOfWeek” outputs the date-time value (such as a timestamp) for each bar.

Of course, all other methods defined within the C\# language for usage of date-time objects are also available, such as day, month, year, hour, minute, second, day of week etc.

See [*http://msdn.microsoft.com/de-de/library/03ybds8y.aspx*]

### Definition

Property DayOfWeek

public enum DayOfWeek

-   DayOfWeek.Monday

-   DayOfWeek.Tuesday

-   DayOfWeek.Wednesday

-   DayOfWeek.Thursday

-   DayOfWeek.Friday

-   DayOfWeek.Saturday

-   DayOfWeek.Sunday

### Example

//Outputs the weekday for each bar

**Print**(Time\[0\].DayOfWeek);

//Do not execute trades on a Friday

**if** (Time\[0\].DayOfWeek == DayOfWeek.Friday)

return;

Displacement
------------

### Description

By implementing “Displacement”, you can shift a drawn indicator line right or left along the x-axis.
This property can be queried within the script and will return an int value.

Blue line: Displacement = 0 (Original)
Red line: Displacement = -5
Green line: Displacement = +5

<img src=".//media/image13.png" width="454" height="384" />

### Usage

Displacement

### Parameter

int Offset Number of bars by which the indicator is to be moved.

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//Displacement of the plot by one bar to the right

Displacement = 1;

}

DisplayInDataBox
----------------

### Description

The property “DisplayInDataBox” states whether the value of an indicator is contained in the data box of the chart or not.

The property can be queried in the script and returns a value of the type Boolean (true or false).

**DisplayInDataBox = true (default)**

The indicator values are displayed in the data box.

**DisplayInDataBox = false**

The indicator values are not displayed in the data box.

The following image displays the values of 3 smoothed averages in the data box.

<img src=".//media/image14.png" width="452" height="387" />

### Usage

DisplayInDataBox

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//Values will not be shown in the data box

DisplayInDataBox = **false**;

}

DrawOnPricePanel
----------------

### Description

The property “DrawOnPricePanel” determines the panel in which the drawing objects are drawn.

**DrawOnPricePanel = true (default)**

Drawing objects are shown in the price chart

**DrawOnPricePanel = false**

Drawing objects are drawn in the panel (subchart) assigned to the indicator

If the indicator is already assigned to the price chart (overlay = true) then this property has no effect, meaning that no additional subchart is opened.
The property can be queried within the script and returns a Boolean value.

### Usage

DrawOnPricePanel

### Example

**protected** override void **Initialize**()

{

// Indicator is drawn in a new subchart

Overlay = **false**;

**Add**(**new Plot**(Color.Red, "MyPlot1"));

// Drawing object is drawn in the price chart

DrawOnPricePanel = **true**;

}

**protected** override void **OnBarUpdate**()

{

// Draws a vertical line in the price chart for the bar from 5 minutes ago

**DrawVerticalLine**("MyVerticalLine", 5, Color.Black);

}

Falling()
---------

### Description

The Falling() method allows you to test whether an “is falling” condition exists, i.e. whether the current value is smaller than the value of the previous bar.

See [*CrossAbove()*], [*CrossBelow()*], [*Rising()*].

### Usage

**Falling**(IDataSeries series)

### Return Value

**true** If the data series is falling
**false** If the data series is not falling

### Parameter

series a data series such as an indicator, close, high etc.

### Example

// Check whether SMA(20) is falling

**if** (**Falling**(**SMA**(20)))

**Print**("The SMA(20) is currently falling.");

Colors
------

AgenaScript provides you with the following commands for defining colors and making color changes to the chart:

[*BarColor*] Color of a bar

[*BackColor*] Background color of the chart

[*BackColorAll*] Background color of the chart and all panels

ChartControl.UpColor Color of up ticks (up bars)
ChartControl.DownColor Color of down ticks (down bars)

For each bar, its colors are saved in the following data series. If these data series are written in, the color of the referenced bar will change.

[*BarColorSeries*]

[*CandleOutlineColorSeries*]

[*BackColorSeries*]

[*BackColorAllSeries*]

BarColor
--------

### Description

Bar color changes the color of a bar.

See [*Colors*], [*BarColorSeries*], [*BackColor*], [*BackColorAll*], [*CandleOutlineColor*].

### Parameter

a color object of the type “public struct color”

### Usage

BarColor

### Example

// If the closing price is above the SMA(14), color the bar orange

**if** (Close\[0\] &gt; **SMA**(14)\[0\]) BarColor = Color.Orange;

<img src=".//media/image15.png" width="427" height="386" />

BackColor
---------

### Description

Back color changes the background color of a bar or gives the current background color of a bar when queried.

See [*Colors*], [*BarColor*], [*BackColorAll*], [*CandleOutlineColor*].

### Parameter

a color object of the type “public struct color”

### Usage

BackColor

### Example

// Every Monday, change the bar background color to blue

**if** (Time\[0\].DayOfWeek == DayOfWeek.Monday)

BackColor = Color.Blue;

<img src=".//media/image16.png" width="432" height="386" />

// Changing the bar background color depending on a smoothing average

// Market price above the SMA(14) to green

// Market price below the SMA(14) to maroon

BackColor = **SMA**(14)\[0\] &gt;= Close\[0\] ? Color.Maroon : Color.LimeGreen;

<img src=".//media/image17.png" width="430" height="384" />

BackColorAll
------------

### Description

Back color all changes the background color of a bar within the chart window and in all subcharts.

See [*Colors*], [*BarColor*], [*BackColor*], [*CandleOutlineColor*].

### Parameter

A color object of the type “public struct color”

### Usage

BackColorAll

### Example

// Every Monday, change the bar background color to blue

**if** (Time\[0\].DayOfWeek == DayOfWeek.Monday)

BackColorAll = Color.Blue;

<img src=".//media/image18.png" width="431" height="386" />

BarColorSeries
--------------

### Description

Bar color series is a data series containing the color for each bar.

See [*Colors*], [*BackColorSeries*], [*BackColorAllSeries*], [*CandleOutlineColorSeries*].

### Parameter

a color object of the type “public struct color”

int barsAgo

### Usage

BarColorSeries

BarColorSeries\[**int** barsAgo\]

When using the method with an index \[**int** barsAgo\] the color for the referenced bar will be changed or returned.

**Caution: Only the color of a bar whose color has been explicitly changed beforehand will be returned. In all other cases, the “Color.Empty” value will be returned.**

### Example

**protected** override void **OnBarUpdate**()

{

**if** (CurrentBar == Bars.Count-1-(CalculateOnBarClose?1:0))

{

> // Color the current bar blue
>
> // This is identical to BarColor = color.Blue

BarColorSeries\[0\] = Color.Blue;

// Color the previous bars green

BarColorSeries\[1\] = Color.Orange;

// Color the third bar yellow

BarColorSeries\[2\] = Color.Yellow;

}

}

<img src=".//media/image19.png" width="423" height="372" />

BackColorSeries
---------------

### Description

Back color series is a data series containing the background color for each bar. If the background color for the subcharts is to be included, please use “BackColorAllSeries” instead.

See [*Colors*], [*BarColorSeries*][*BarColor*], [*BackColorAllSeries*][] [*CandleOutlineColorSeries*].

### Parameter

a color object of the type “public struct color”

int barsAgo

### Usage

BackColorSeries

BackColorSeries\[**int** barsAgo\]

When using this method with an index \[**int** barsAgo\] the background color for the referenced bar will be outputted.

### Example

// Which background color does the current bar have?

**Print** (BackColorSeries\[0\]);

// Set the current bar’s background color to blue

// This is identical to BackColor = Color.Blue

BackColorSeries\[3\] = Color.Blue;

// Set background color for the previous bar to green

BackColorSeries\[1\] = Color.Green;

BackColorAllSeries
------------------

### Description

Back color all series is a data series containing the background color for each bar. The difference to BackColorSeries is that the background color of the subchart is included.

See [*Colors*], [*BarColorSeries*], [*BackColorSeries*], [*CandleOutlineColorSeries*].

### Parameter

a color object of the type “public struct color”

int barsAgo

### Usage

BackColorAllSeries

BackColorAllSeries\[**int** barsAgo\]

When using the method with an index \[**int** barsAgo\] the background color for the referenced bar will be changed or returned.

### Example

See [*BackColorSeries*].

CandleOutlineColor
------------------

### Description

Candle outline color changes the border/outline color (including the wick) of a bar.

If the color of the bar is changed using BarColor and the outline is not changed using CandleOutlineColor, the outline color is adjusted to match the color of the bar.

See [*Colors*], [*BarColor*], [*BackColor*], [*BackColorAll*].

### Parameter

a color object of the type “public struct color”

### Usage

CandleOutlineColor

### Example

**if** (**SMA**(14)\[0\] &gt; **SMA**(200)\[0\])

CandleOutlineColor = Color.LimeGreen;

**else**

CandleOutlineColor = Color.Red;

<img src=".//media/image20.png" width="491" height="449" />

CandleOutlineColorSeries
------------------------

### Description

Candle outline color series is a data series that saves the outline color for each bar.

See [*Colors*], [*BarColorSeries*], [*BackColorSeries*], [*BackColorAllSeries*].

### Parameter

a color object of the type “public struct color”

int barsAgo

### Usage

CandleOutlineColorSeries
CandleOutlineColorSeries\[**int** barsAgo\]

When using this method with an index \[**int** barsAgo\] the border color for the referenced bar will be outputted.

**Caution: Color.Empty will be outputted for a bar unless it has been previously changed.**

### Example

// Set the outline color of the current bar to blue

CandleOutlineColorSeries\[0\] = Color.Blue;

// Change the outline color to the chart default value

CandleOutlineColorSeries\[0\] = Color.Empty;

FirstTickOfBar
--------------

### Description

FirstTickOfBar is a property of the type “bool” that returns “true” if the currently incoming tick is associated with a new bar. This means that this tick is the first tick of a new bar.
This property can only be meaningfully applied when the indicator or strategy is running in the tick-by-tick mode, meaning that CalculateOnBarClose = false and the data feed is able to output real-time values.
When using end-of-day data in a daily chart, the “FirstTickOfBar” is always true for the last bar.
FirstTickOfBar should not be used outside of the OnBarUpdate() method.
See [*Bars.TickCount*].

### Usage

FirstTickOfBar

### Example

// Within a tick-by-tick strategy, execute one part bar-by-bar only

**if** (FirstTickOfBar)

{

**if** (**CCI**(20)\[1\] &lt; -250)

**EnterLong**();

return;

}

FirstTickOfBarMtf
-----------------

### Description

FirstTickOfBarMtf is the **m**ulti-**t**ime **f**rame variant of the [*FirstTickOfBar*] property.

The setting of CalculateOnBarClose only affects the primary timeframe (chart timeframe). When working with multi-bars, the ticks of the secondary timeframes are provided on a tick-by-tick basis independently of the CalculateOnBarClose setting.
With the help of FirstTickOfBarMtf, it is possible to determine when a new bar has begun in a secondary timeframe.

### Usage

FirstTickOfBarMtf(BarsInProgress)

### Parameter

FirstTickOfBarMtf(BarsInProgress).

See [*BarsInProgress*].

### Example

**if** (**FirstTickOfBarMtf**(BarsInProgress))

**Print**("A new bar has begun.");

GetCurrentAsk()
---------------

### Description

The GetCurrentAsk() method returns the current value of the ask side of the order book. If no level 1 data is available to AgenaTrader, then this function simply outputs the last trade value.

See [*GetCurrentBid()*] and [*OnMarketData()*].

### Usage

GetCurrentAsk()

### Return Value

double value

### Parameter

none

### Example

If an entry condition is fulfilled, then 1 contract should be sold at the current ask price:

**private** IOrder entryOrder = **null**;

**protected** override void **OnBarUpdate**()

{

// Entry condition

**if** (Close\[0\] &lt; **SMA**(20)\[0\] && entryOrder == **null**)

// Sell 1 contract at the current ask price

entryOrder = **SubmitOrder**(0, OrderAction.SellShort, OrderType.Limit, 1, **GetCurrentAsk**(), 0, "", "Enter short");

}

GetCurrentBid()
---------------

### Description

The GetCurrentBid() method returns the current value of the bid side of the order book. If no level 1 data is available to AgenaTrader, then the function outputs the last traded price.

See [*GetCurrentAsk()*] and [*OnMarketData()*].

### Usage

GetCurrentBid()

### Return Value

double value

### Parameter

none

### Example

If an entry condition is fulfilled, then 1 contract should be sold at the current bid price:

**private** IOrder entryOrder = **null**;

**protected** override void **OnBarUpdate**()

{

// Entry condition

**if** (Close\[0\] &gt; **SMA**(20)\[0\] && entryOrder == **null**)

// Sell 1 contract at the current bid price

entryOrder = **SubmitOrder**(0, OrderAction.Buy, OrderType.Limit, 1, **GetCurrentBid**(), 0, "", "Enter long");

}

HighestBar
----------

### Description

The HighestBar() method searches within a predetermined number of periods for the highest bar and outputs how many bars ago it can be found.

See [*LowestBar()*].

### Parameter

period Number of bars within which the bar is searched for

series Every data series, such as close, high, low, etc.

### Return Value

**int** barsAgo How many bars ago the high occurred

### Usage

**HighestBar**(IDataSeries series, **int** period)

### Example

// How many bars ago was the highest high for the current session?

**Print**(**HighestBar**(High, Bars.BarsSinceSession - 1));

// What value did the market price have at the highest high of the session?

**Print**("The highest price for the session was: " + Open\[**HighestBar**(High, Bars.BarsSinceSession - 1)\]);

Historical
----------

### Description

Historical allows you to check whether AgenaScript is working with historical or real-time data.
As long as OnBarUpdate() is called up for historical data, then historical = true. As soon as live data is being used, then historical = false.
During a backtest, historical is always true.

### Usage

Historical

### Return Value

**true** when using historical data
**false** when using real-time data

### Example

**protected** override void **OnBarUpdate**()

{

// only execute for real-time data

**if** (Historical) return;

// Trading technique

}

Initialize()
------------

### Description

The Initialize() method is called up once at the beginning of an indicator or strategy calculation. This method can be used to set indicator properties, initialize your own variables, or add plots.

### Parameter

none

### Return Value

none

### Usage

**protected** override void **Initialize**()

### Important Keywords

-   [*Add()*]

-   [*AllowRemovalOfDrawObjects*]

-   [*AutoScale*]

-   [*BarsRequired*]

-   [*CalculateOnBarClose*]

-   [*ClearOutputWindow()*]

-   [*Displacement*]

-   [*DisplayInDataBox*]

-   [*DrawOnPricePanel*]

-   [*InputPriceType*]

-   [*Overlay*]

-   [*PaintPriceMarkers*]

-   [*SessionBreakLines*]

-   [*VerticalGridLines*]

> **Additional Keywords for Strategies**

-   [*DefaultQuantity*]

-   [*EntriesPerDirection*]

-   [*EntryHandling*]

-   [*SetStopLoss()*]

-   [*SetProfitTarget()*]

-   [*SetTrailStop()*]

-   [*TimeInForce*]

-   [*TraceOrders*]

### More Information

**Caution:**

The Initialize() method is not only called up at the beginning of an indicator or strategy calculation, but also if the chart is reloaded unexpectedly or if the properties dialog of indicators is opened and so on.
Developers of custom AgenaScripts should NOT use this method for running their own routines, opening forms, performing license checks, etc. The OnStartUp() method should be used for these kind of tasks.

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Blue, "myPlot"));

**ClearOutputWindow**();

AutoScale = **false**;

Overlay = **true**;

PaintPriceMarkers = **false**;

DisplayInDataBox = **false**;

CalculateOnBarClose = **true**;

}

InitRequirements()
------------------

### Description

The InitRequirements() method is called up once at the beginning of an indicator and/or strategy calculation. This method is only necessary when using multi-bars.
Within InitRequirements, no other programming commands are executed. For initializing, the Initialize() or OnStartUp() method should be used.

### Parameter

none

### Return Value

none

### Example

**protected** override void **InitRequirements**()

{

**Add**(DatafeedHistoryPeriodicity.Day, 1);

**Add**(DatafeedHistoryPeriodicity.Week, 1);

}

InputPriceType
--------------

### Description

The input price type property determines which price series is used by default when calculating an indicator, if no other data series is explicitly stated.
InputPriceType can be set with the Initialize() method; this specification is then valid for all further calculations.
If InputPriceType is in OnBarUpdate(), these changes are only valid starting with the next instruction.
Every further appearance of InputPriceType will be ignored!

See [*PriceType*]

### Usage

InputPriceType

### Example1

**protected** override void **Initialize**()

{

**ClearOutputWindow**();

InputPriceType = PriceType.Low;

}

**protected** override void **OnBarUpdate**()

{

// The input data series for the indicator (input) is low

**Print**(Low\[0\] + " " + Input\[0\] + " " + InputPriceType);

}

### Example2

**protected** override void **OnBarUpdate**()

{

// These values are identical

// since close is used as the input data series by default

**Print**(**SMA**(20)\[0\] + " " + **SMA**(Close, 20)\[0\]);

InputPriceType = PriceType.Low;

// From here on out, low is used instead of close

// Both values are identical

**Print**(**SMA**(20)\[0\] + " " + **SMA**(Low, 20)\[0\]);

InputPriceType = PriceType.High;

// The instructions will be ignored

// Input = low is still in effect

}

Instrument
----------

### Description

With “instrument”, information concerning the trading instrument (stock, future etc.) is made available.

Detailed information can be found here: *Instruments*.

Line()
------

### Description

A line object is used for drawing a horizontal line in the chart. Usually, these are upper and lower trigger lines for indicators such as the RSI (70 and 30).
The lines described here are not to be confused with lines from the drawing objects (see “DrawHorizontalLine”).
Line objects can be added to an indicator with the help of the Add() method, and with this, added to the lines collection.

See [*Plot*].

### Parameter

|       |                                                              |
|-------|--------------------------------------------------------------|
| Color | Line color                                                   |
| Name  | Description                                                  |
| Pen   | A pen object                                                 |
| Value | Defines which value on the y-axis the line will be drawn for |

### Usage

**Line**(Color color, **double value**, string name)

**Line**(Pen pen, **double value**, string name)

### More Information

Information on the pen class: [*http://msdn.microsoft.com/de-de/library/system.drawing.pen.aspx*]

### Example

// Example 1

// A new line with standard values drawn at the value of 70

**Add**(**new Line**(Color.Black, 70, "Upper"));

// Example 2

// A new line with self-defined values

**private** Line line;

**private** Pen pen;

**protected** override void **Initialize**()

{

// Define a red pen with the line strength 1

pen = **new Pen**(Color.Red, 1);

// Define a horizontal line at 10

line = **new Line**(pen, 10, "MyLine");

// add the defined line to the indicator

**Add**(line);

}

// Example 3

// Short form for the line in example 2

**Add**(**new Line**(**new Pen**(Color.Red, 1), 10, "MyLine"));

Log()
-----

### Description

Log() allows you to write outputs in the AgenaTrader log file (log tab). 5 different log levels are supported.

Note:

If the log tab is not viewable, it can be displayed using the tools log.

### Usage

**Log**(string message, LogLevel logLevel)

### Parameter

|          |                      |
|----------|----------------------|
| message  | Text (message)       |
| logLevel | Possible values are  
                                  
            InfoLogLevel.Info     
                                  
            InfoLogLevel.Message  
                                  
            InfoLogLevel.Warning  
                                  
            InfoLogLevel.Alert    
                                  
            InfoLogLevel.Error    |

### Example

######### //Tab protocol

######### Log("This is information.", InfoLogLevel.Info); //white

######### Log("This is a message.", InfoLogLevel.Message); // blue

######### Log("This is a warning.", InfoLogLevel.Warning); // yellow

######### Log("This is an alarm.", InfoLogLevel. AlertLog); //green

######### Log("This is a mistake.", InfoLogLevel.Error); // red

######### 

######### //Tab messags

######### Log("This is a message (messages).", InfoLogLevel.Message); //gray

######### 

######### //PopUp& protocoll

######### Log("This is an alert popup window.", InfoLogLevel.Alert); //green

LowestBar
---------

### Description

The LowestBar() method attempts to find the lowest bar within a predefined number of periods.

See [*HighestBar()*].

### Parameter

period Number of bars that will be searched for the lowest bar

series Every data series, such as close, high, low etc.

### Return Value

**int** barsAgo How many bars ago the low occurred

### Usage

**LowestBar**(IDataSeries series, **int** period)

### Example

// How many bars ago was the lowest low of the session?

**Print**(**LowestBar**(Low, Bars.BarsSinceSession - 1));

// Which price did the lowest open of the current session have?

**Print**("The lowest open price of the current session was: " + Open\[**LowestBar**(Low, Bars.BarsSinceSession - 1)\]);

MarketDataEventArgs
-------------------

### Description

The data type MarketDataEventArgs represents a change in the level 1 data and is used as a parameter of the OnMarketData() function.

|                |                                                                                                                                  |
|----------------|----------------------------------------------------------------------------------------------------------------------------------|
| AskSize        | Current order volume on the ask side                                                                                             |
| AskPrice       | Current ask price                                                                                                                |
| BidSize        | Current order volume on the bid side                                                                                             |
| BidPrice       | Current bid price.                                                                                                               |
| Instrument     | An object of the type instrument that contains the trading instrument for which the level 1 data is outputted. See *Instruments* |
| LastPrice      | Last traded price                                                                                                                |
| MarketDataType | Potential values are:                                                                                                            
                                                                                                                                                    
                  MarketDataType.Ask                                                                                                                
                                                                                                                                                    
                  MarketDataType.AskSize                                                                                                            
                                                                                                                                                    
                  MarketDataType.Bid                                                                                                                
                                                                                                                                                    
                  MarketDataType.BidSize                                                                                                            
                                                                                                                                                    
                  MarketDataType.Last                                                                                                               
                                                                                                                                                    
                  MarketDataType.Volume                                                                                                             |
| Price          | This is equal to last price. This field only exists for compatability reasons                                                    |
| Time           | A date-time value containing the timestamp of the change                                                                         |
| Volume         | A long value that shows the volume                                                                                               |

### Example

See [*OnMarketData()*].

MarketDepthEventArgs
--------------------

### Description

The data type MarketDepthEventArgs represents a change in the level 2 data (market depth) and is used as a parameter within OnMarketDepth().

|                |                                                                |
|----------------|----------------------------------------------------------------|
| MarketDataType | Potential values are:                                          
                                                                                  
                  MarketDataType.Ask                                              
                  MarketDataType.Bid                                              |
| MarketMaker    | A string value containing the market maker ID                  |
| Position       | An int value that defines the position within the market depth |
| Operation      | Represents the action caused by a change in the order book.    
                                                                                  
                  Values can be:                                                  
                                                                                  
                  Operation.Insert                                                
                  Operation.Remove                                                
                  Operation.Update                                                |
| Price          | A double value that displays the bid/ask price                 |
| Time           | A date-time value containing the timestamp of the change       |
| Volume         | A long value that shows the volume                             |

### Example

See [*OnMarketDepth()*].

Overlay
-------

### Description

The overlay property defines whether the indicator outputs are displayed in the price chart above the bars or whether a separate chart window is opened below the charting area.

**Overlay = true**

The indicator is drawn above the price (for example an [*SMA*])

**Overlay = false (default)**

A separate chart window is opened (RSI)

This property can be queried within the script and outputs a value of the type Boolean (true or false).

### Usage

Overlay

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//The indicator should be displayed within a separate window

Overlay = **false**;

}

PaintPriceMarkers
-----------------

### Description

The paint price markers property defines whether the so-called price markers for the indicator outputs are displayed on the right-hand chart border (in the price axis) or not. In some cases it makes sense to switch these off for a better overview in the chart.
**PaintPriceMarkers = true (default)**

Price markers are shown in the price axis

**PaintPriceMarkers = false**

Price markers are not shown in the price axis

This property can be queried within the script and returns a value of the type Boolean (true or false).

### Usage

PaintPriceMarkers

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//Do not show price markers in the price axis

PaintPriceMarkers = **false**;

}

PlaySound()
-----------

### Description

This method allows you to play a wav file.

### Usage

**PlaySound**(wavFile)

### Return Value

none

### Parameter

wavFile File name of the wav file to be played

### Example

**using** System.IO;

string path = Environment.**GetFolderPath**(Environment.SpecialFolder.MyDocuments);

string file = "\\\\AgenaTrader\\\\Sounds\\\\Alert1.wav";

**PlaySound**(path + file);

Plot()
------

### Description

A plot (drawing) is used to visually display indicators in a chart. Plot objects are assigned to an indicator with the help of the Add() method and attached to the plots collection.
See [*Line*].

### Parameter

|           |                        |
|-----------|------------------------|
| Color     | Drawing color          |
| Pen       | Pen object             |
| PlotStyle | Line type              
                                     
             PlotStyle.Bar           
                                     
             PlotStyle.Block         
                                     
             PlotStyle.Cross         
                                     
             PlotStyle.Dot           
                                     
             PlotStyle.Hash          
                                     
             PlotStyle.Line          
                                     
             PlotStyle.Square        
                                     
             PlotStyle.TriangleDown  
                                     
             PlotStyle.TriangleUp    |
| Name      | Description            |

### Usage

**Plot**(Color color, string name)

**Plot**(Pen pen, string name)

**Plot**(Color color, PlotStyle plotStyle, string name)

**Plot**(Pen pen, PlotStyle plotStyle, string name)

### More Information

Information on the pen class: [*http://msdn.microsoft.com/de-de/library/system.drawing.pen.aspx*]

### Example

// Example 1

// Plot with standard values (line with line strength 1)

**Add**(**new Plot**(Color.Green, "MyPlot"));

// Example 2

// user-defined values for pen and plot style

**private** Plot plot;

**private** Pen pen;

**protected** override void **Initialize**()

{

// a red pen with the line strength of 6 is defined

pen = **new Pen**(Color.Blue, 6);

// a point line with a thick red pen from above is defined

plot = **new Plot**(pen, PlotStyle.Dot, "MyPlot");

// The defined plot is to be used as a representation for an indicator

**Add**(plot);

}

// Example 3

// Abbreviation of example 2

**protected** override void **Initialize**()

{

**Add**(**new Plot**(**new Pen**(Color.Blue, 6), PlotStyle.Dot, "MyPlot"));

}

PlotMethod
----------

### Description

In each indicator, the plot method can be overridden in order to add your own graphics (GDI+) to the price chart with the help of the graphics class (System.Drawing).

See [*http://msdn.microsoft.com/de-de/library/system.drawing.graphics.aspx*].

The [*ChartControl*] object offers several parameters.

More examples: [*Bar Numbering*][*PlotSample*], *Chart Background Image*.

### Parameter

graphics The graphics object of the price chart (context)

rectangle The size of the drawing area (type “public struct rectangle”)

double min The smallest price in the y-axis

double max The biggest price in the y-axis

### Return Value

none

### Usage

**public** override void **Plot**(Graphics graphics, Rectangle r, **double** min, **double** max)

### Example

**using** System;

**using** System.Collections.Generic;

**using** System.ComponentModel;

**using** System.Drawing;

**using** System.Drawing.Drawing2D;

**using** AgenaTrader.API;

**using** AgenaTrader.Custom;

**using** AgenaTrader.Plugins;

**namespace** AgenaTrader.UserCode

{

\[**Description**("Example for the usage of the plot method.")\]

**public** class PlotSample : UserIndicator

{

**private** StringFormat stringFormat = **new StringFormat**();

**private** SolidBrush brush = **new SolidBrush**(Color.Black);

**private** Font font = **new Font**("Arial", 10);

**protected** override void **Initialize**()

{

ChartOnly = **true**;

Overlay = **true**;

}

**protected** override void **OnBarUpdate**()

{}

**protected** override void **OnTermination**()

{

brush.**Dispose**();

stringFormat.**Dispose**();

}

**public** override void **Plot**(Graphics graphics, Rectangle r, **double** min, **double** max)

{

// Fill a rectangle

SolidBrush tmpBrush = **new SolidBrush**(Color.LightGray);

graphics.**FillRectangle**(tmpBrush, **new Rectangle** (0, 0, 300, 300));

tmpBrush.**Dispose**();

// Draw a red line from top left to bottom right

Pen pen = **new Pen**(Color.Red);

graphics.**DrawLine**(pen, r.X, r.Y, r.X + r.Width, r.Y + r.Height);

// Draw a red line from bottom left to top right

// Use anti-alias (the line appears smoother)

// The current settings for the smoothing are saved

// Restore after drawing

SmoothingMode oldSmoothingMode = graphics.SmoothingMode; //Save settings

graphics.SmoothingMode = SmoothingMode.AntiAlias; // Use higher smoothing settings

graphics.**DrawLine**(pen, r.X, r.Y + r.Height, r.X + r.Width, r.Y);

graphics.SmoothingMode = oldSmoothingMode; // Settings restored

pen.**Dispose**();

// Text in the upper left corner (position 10,35)

stringFormat.Alignment = StringAlignment.Near; // Align text to the left

brush.Color = Color.Blue;

graphics.**DrawString**("Hello world!", font, brush, r.X + 10, r.Y + 35, stringFormat);

// Text in the left lower corner and draw a line around it

brush.Color = Color.Aquamarine;

graphics.**FillRectangle**(brush, r.X + 10, r.Y + r.Height - 20, 140, 19);

// Draw outside line

pen = **new Pen**(Color.Black);

graphics.**DrawRectangle**(pen, r.X + 10, r.Y + r.Height - 20, 140, 19);

pen.**Dispose**();

// Write text

brush.Color = Color.Red;

graphics.**DrawString**("Here is bottom left!", font, brush, r.X + 10, r.Y + r.Height - 20, stringFormat);

}

}

}

PriceType
---------

### Description

Price type describes a form of price data.

See [*InputPriceType*]

Following variables are available:

-   PriceType.Close

-   PriceType.High

-   PriceType.Low

-   PriceType.Median

-   PriceType.Open

-   PriceType.Typical

-   PriceType.Volume

-   PriceType.Weighted

### Usage

PriceType

### Example

See [*InputPriceType*]

Print()
-------

### Description

The Print() method writes outputs in the AgenaTrader output window.
See [*ClearOutputWindow()*].

### Usage

**Print**(string message)

**Print**(**bool value**)

**Print**(**double value**)

**Print**(**int value**)

**Print**(DateTime **value**)

**Print**(string format, string message)

### Parameter

string Text an individual message text

### Return Value

none

### More Information

Information regarding output formatting: *Formatting numbers*.

Hints about the String.Format() method: [*http://msdn.microsoft.com/de-de/library/fht0f5be%28v=vs.80%29.aspx*]

### Example

// "Quick&Dirty" formatting of a number with 2 decimal points

**Print**(Close\[0\].**ToString**("0.00"));

// Output day of the week from the timestamp for the bar

**Print**(string.**Format**("{0:dddd}", Time\[0\]));

// An additional empty row with an escape sequence

**Print**("One empty row afterwards \\n");

RemoveDrawObject()
------------------

### Description

The RemoveDrawObject() method removes a specific drawing object from the chart based on a unique identifier (tag).
See [*RemoveDrawObjects()*].

### Usage

RemoveDrawObject(string tag)

### Return Value

none

### Parameter

string tag The clearly identifiable name for the drawing object

### Example

**RemoveDrawObjects**("My line");

RemoveDrawObjects()
-------------------

### Description

This method removes all drawings from the chart
See [*RemoveDrawObject()*].

### Usage

RemoveDrawObjects()

### Return Value

none

### Example

//Delete all drawings from the chart

**RemoveDrawObjects**();

Rising()
--------

### Description

With this method you can check if an uptrend exists, i.e. if the current value is bigger than the previous bar’s value.

See [*CrossAbove()*], [*CrossBelow()*], [*Falling()*].

### Usage

**Rising**(IDataSeries series)

### Return Value

**true** If the data series is rising
**false** If the data series is not rising

### Parameter

series A data series such as an indicator, close, high etc.

### Example

// Check if SMA(20) is rising

**if** (**Rising**(**SMA**(20)))

**Print**("The SMA(20) is currently rising.");

SessionBreakLines
-----------------

### Description

The property SessionBreakLines defines whether the vertical lines that represent a trading session stop are displayed within the chart.
This may be useful for stocks and other instruments if you wish to display session breaks/trading stops.
**SessionBreakLines = true (default)**

Session break lines are shown

**SessionBreakLines = false**

Session break lines are not shown

This property can be queried within the script and outputs a value of the type Boolean (true or false).

### Usage

SessionBreakLines

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

//Session break lines should not be shown

SessionBreakLines = **false**;

}

TickSize
--------

A tick is the smallest possible price change of a financial instrument within an exchange. If, for example, the trading prices are specified to 2 decimal places, then a tick equals 0.01. You can expect Forex instruments to be specified to within 4 or 5 decimal places. A tick is called a pip in Forex trading and usually equals 0.0001 or 0.00001.
The tick value is usually predefined by the exchange and does not (usually) change.
See [*Instrument.TickSize*].

Usually, a tick is displayed as a decimal number. Historically speaking (especially in American exchanges) stocks have been noted with tick sizes of 1/16 of a dollar.
This notation is still widespread within commodities. Corn futures (ZC) are noted in ¼ US cents/bushel (usually equals 12.50 US$ per contract).
US treasury bonds are noted in a tick size of 1/32 points, which equals 31.25$.
Notations are usually made with apostrophes, for example:

149'00 equals exactly 149,
149'01 equals 149 1/32 (meaning 149.03125),
149'31 equals 149 31/32 (149.96875),
and the next value after this is 150’00

In the so-called T-Bond intermonth spreads, notations are specified in quarters of 1/32, resulting in point values of 7.8125 per contract.

Notations have a dash:

17-24 equals 17 24/32 points,
17-242 equals 17 24.25/32 points,
17-245 equals 17 24.5/32 points and
17-247 equals 17 24.75/32 points.
The next notation after 17-247 is 17-25 and then 17-252, 17-255 etc.
After 17-317 comes 18.

The individual contract specifications can be found on the websites of the respective exchanges.

CME: [*www.cmegroup.com*] under Products & Trading
Eurex (FDAX): *http://www.eurexchange.com/exchange-en/products/idx/dax/17206/*

See [*Instrument.TickSize*].

TimeFrame
---------

See [*Bars.TimeFrame*].

When using multiple timeframes ([*Multibars*][*MultiBars*]) in an indicator, please see [*TimeFrames*].

ToDay()
-------

### Description

To day is a method specifically suited for inexperienced programmers who have problems with the potentially complex .net date-time structure of C\#.
Experienced programmers can continue using the date-time function directly.

To day outputs an int representation in the format of yyyymmdd.
(yyyy = year, mm = month, dd = day)

13.08.2012 would thus be 20120813.

See [*ToTime*].

Help with date-time: [*http://msdn.microsoft.com/de-de/library/system.datetime.aspx*]

### Usage

ToDay(DateTime time)

### Example

// Do not trade on the 11<sup>th</sup> of September

**if** (**ToDay**(Time\[0\]) = 20130911)

return;

ToTime()
--------

### Description

To time is a method specifically suited for inexperienced programmers who have problems with the potentially complex .net date-time structure of C\#.

To time outputs an int representation in the format hhmmss.
(hh = hour, mm = minute, ss = seconds)

The time 07:30 will be displayed as 73000 and 14:15:12 will become 141512.

See [*ToDay*].

Help with date-time: [*http://msdn.microsoft.com/de-de/library/system.datetime.aspx*]

### Usage

ToTime(DateTime time)

### Example

// Only enter trades between 08:15 and 16:35

**if** (**ToTime**(Time\[0\]) &gt;= 81500 && **ToTime**(Time\[0\]) &lt;= 163500)

{

// Any trading technique

}

Update()
--------

### Description

The Update() method calls up the OnBarUpdate method in order to recalculate the indicator values.

Update() is to be used with caution and is intended for use by experienced programmers.

### Usage

Update()

### Return Value

none

### Parameter

none

### Example

The effect of update can be illustrated with the help of 2 indicators.
The first indicator, Ind1, uses a public variable from the indicator Ind2.

**Code from Ind1:**

**public** class Ind1 : UserIndicator

{

**protected** override void **OnBarUpdate**()

{

> **Print**( **Ind2**().MyPublicVariable );

}

}

**Code from Ind2:**

**private double** myPublicVariable = 0;

**protected** override void **OnBarUpdate**()

{

myPublicVariable = 1;

}

**public double** MyPublicVariable

{

get

{

**Update**();

return myPublicVariable;

}

}

**Without Update() - Wrong
**If Ind2 is called up by Ind1, the get-method of MyPublicVariable is called up in Ind2. Without Update(), the value of MyPublicVariable would be returned. In this case it would be 0.

**With Update() - Correct
**By calling up Update(), OnBarUpdate() is initially executed by Ind2. This sets MyPublicVariable to 1. Lastly, the value 1 is passed on to the requesting indicator.

Value
-----

### Description

Value is a data series object containing the first data series of an indicator.

When the Add() method is called up, a value object is automatically created and added to the values collection.

Value is identical to Values\[0\].

### Usage

Value

Value\[**int** barsAgo\]

### More Information

The methods known for a collection, Set(), Reset(), and Count(), can be used for values.

### Example

See [*Values*].

VerticalGridLines
-----------------

### Description

The property VerticalGridLines defines whether or not the regularly spaced vertical lines (the so-called grid) are shown within the charting area.

**VerticalGridLines = true (default)**

Vertical grid lines are shown

**VerticalGridLines = false**

Vertical grid lines are not shown

This property can be queried within the script and returns a value of the type Boolean (true or false).

### Usage

VerticalGridLines

### Example

**protected** override void **Initialize**()

{

**Add**(**new Plot**(Color.Red, "MyPlot1"));

// Vertical grid lines shall not be shown within the chart

VerticalGridLines = **false**;

}