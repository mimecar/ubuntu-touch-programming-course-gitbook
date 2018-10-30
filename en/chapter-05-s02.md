# chapter-05-s02

## QML and Charts

## Introduction

The purpose of this chapter is show how to draw charts from QML + Javascript applications for Ubuntu Touch \(UBPorts\).

## Requirement

To follow this chapter is required an Ubuntu SDK IDE configured with a "Desktop Kit", a knowledge of QML and Javascript. For more informations about environment configuration you can look at the beginning of this course.

Is also necessary a base knowledge of SQL language and how to access at a SQLite database from QML. If the last requirements are missing you can get them from the dedicated chapter of this course.

## Premise

This chapter uses as sample application the one created in the previous chapter about QML and database access. Just for quick summary, that sample application was titled "WeatherRecorder" and was designed to save and manage the daily temperature values for a favourite city.

Now, in this chapter, we want improve that application adding an analytic feature: display the monthly temperature values with a chart.

## Charts with QML

The presented solution uses the [open source Javascript library Chart.js](http://www.chartjs.org/) as drawing library which uses the new HTML5 elements. To use it form QML is necessary a "binding" library placed in the middle between Charts.js and the QML code. That binding is provided by another open source library named [qchart.js](https://github.com/jwintz/qchart.js) that define a custom QML Component representing a chart that wrap Chart.js object.

Thanks to Jwintz, the author of **qchart.js**, for his great job! But don’t worry, we don’t need to manage any low level details about that.

## Chart support configuration

If you import our sample application into Ubuntu SDK IDE, the following configuration steps can be skipped. They must be executed if you are creating a new QML application from scratch and you need chart support.

To draw charts from our QML application is necessary include two files \(you can find all the necessary files from [https://github.com/jwintz/qchart.js](https://github.com/jwintz/qchart.js)\):

### Qchart.js

Is the Javascript drawing library containing the core functions to draw the chart, calculates the axis scales and so on. It is a version of Chart.js library already bundled with the "qchart.js" one \(with maybe with some little patches\). To use it is necessary this import statement:

```javascript
import "QChart.js" as Charts
```

Note: the above import syntax is the same necessary to use any other Javascript file from QML.

### QChart.qml

Define the new QML component \(named "Qchart"\) to be used in our QML code to draw a chart. The use is made with a block like this:

```javascript
QChart {                  
  //chart configuration options
}
```

As last step, is necessary include that files in the .qrc file \(a special project file that list all the files that composing the project\). Now the QML application is ready to draw charts.

## Introduction at the sample application

As noted above, we will use the application presented in the chapter dedicated at QML and database access and we will improve it. To keep separated the source code of the two applications, and prevent confusion, for this chapter there is a separated application named "WeatherRecorderChart" created using the previous one "WeatherRecorder" as base.

If you import the "WeatherRecorderChart" project into Ubuntu SDK IDE in the project source tree you should get a view like this:

![Application source tree](../.gitbook/assets/01_application_source%20%281%29.png)

If you run the application for the first time \(pressing the green "Play" icon\) you get that application page:

![Weather Recorder Configuration](../.gitbook/assets/02_application%20%281%29.png)

When you have accepted \(or updated\) the proposed configuration you get the new Application home page:

![Weather Recorder new design](../.gitbook/assets/03_application_new_design%20%281%29.png)

Note: next application start-up the configuration windows will not shown \(we have described what happen at application start-up in the previous chapter\).

**Important:** the new application \(like the old one\) uses a SQLite database to save the temperature values. But, the location of the database files is different, due to the fact that "applicationName" field in Main.qml have a different value \(please, see chapter about database access for info about database location\).

In comparison at the other sample application "WeatherRecorder" we note a new section at the end of the page named "Analytic section". If you press the green button "Chart" you get a new page that allow you to choose a target month and then display his temperature values with a bar chart like this:

![Analytic Page](../.gitbook/assets/04_chart%20%281%29.png)

On the right there is a simple chart legend that show the values used to draw the chart. If no values are found, the chart will be empty \(this is the case when all the temperature values are missing for the chosen month\). Insert some values and retry to redraw the chart.

## New application features in details

The previous section have already shown the new chart page added at our application. Here, we want describe in more details the changes added.

In the user interface there is new section, whose source code can be found in Main.qml file. This section is composed of two new QML Row components with id "chartSectionHeader" and "chartRow" \(try to search them with CTRL + F\) appended at the ones already existing.

To have a new separated page for the chart, and his legend, we have introduced a new QML Component: [PageStack](https://docs.ubuntu.com/phone/en/apps/api-qml-current/Ubuntu.Components.PageStack). Very quickly, it is a component used to manage the navigation between pages, that works like a stack: the page on the top is the one showed. To add/remove a page from the stack there are the methods "push" and "pop".

As first thing, when the full application starts, is necessary push on the PageStack the first page to show, this is done using the "onComplete" event of the PageStack component raised when it is created \(see the previous chapter dedicated a database access with QML for more information about the use of "onComplete" event\).

That initialization is made with this statements:

```javascript
// Listing 01
PageStack {
  id: pageStack

  /* set the firts page of the application */
  Component.onCompleted: {
    pageStack.push(mainPage);
  }

    // a set of Page components
 }
```

With the above code is pushed on the top of the stack the QML page with id "mainPage". That identifiers is the one of the home page shown on application start. Try to search that identifiers in the code.

Now, we can look at the entry point of our chart page: the "Chart" button, his source code \(form Main.qml\) is the following:

```javascript
// Listing 02
Button {
  id:showChartButton
  text: i18n.tr("Chart")
  width: units.gu(14)
  color: UbuntuColors.green
  onClicked: {
    pageStack.push(chartPage)
  }
}
```

When the user press the button \(i.e.: the "onClick" event is raised\) a new page is pushed on the top of our PageStack component and will be shown replacing the previous one. Which is the page shown? Is the one with "chartPage" identifiers. Try a "CTRL + click" on that identifiers passed at the "push" function \(or try or search with CTRL + F\).

Doing this you will be redirect to the following code:

```javascript
// Listing 03
Component {
    id: chartPage
    ChartPage{}
}
```

That statement, define and import a custom component whose source code is contained in the ChartPage.qml file \(try a CTRL+Click on ChartPage{} to open that file\).

\(Note: listing 03 shows how a QML Component, defined in an external file, can be imported into another QML file\). In our case the imported QML Component is the Page whit the id "chartPage".

In ChartPage.qml we find the page implementation:

```javascript
// Listing 04
Page {
     id: chartPage

     //... other code omitted for shortness
 }
```

## The Chart page in details

Now, that the navigation flow is explained, we can focus on the page that create the chart, whose source code is contained in ChartPage.qml file.

Note: we omit explanations of the header part of that page because contains components already presented in the previous chapters of the course \(DatePicker, Buttons. etc.\).

Our description starts when the user press the "Show Chart" button. Here the code executed when the button "onClick" event is raised:

```javascript
// Listing 05
onClicked: {
  /* extract the year, month, day from the variable ‘targetDate’ */
  var dateParts = chartPage.targetDate.split("-");
  /* build a JS Date object using string tokens (month is 0-based) */
  var date = new Date(dateParts[0], dateParts[1] - 1, dateParts[2]);
  /* calculates first and last day of the month */
  var firstDayMonth = new Date( date.getFullYear(),date.getMonth(), 1);
  var lastDayMonth = new Date( date.getFullYear(), date.getMonth() + 1, 0);
  /* set the data-set at the chart and make visible the chart and legend */
  temperatureChart.chartData = ChartUtils.getChartData(firstDayMonth,lastDayMonth);

  /* make visible the chart and the legend */
  temperatureChartContainer.visible = true;     
 }
```

When the user choose a month with the DatePicker, that value is assigned at the Javascript variable named "chartPage" as a formatted value yyyy-mm-dd \(see: the Component with id "popoverTargetMonthPicker"\).

After a splitting of that valu \(see "listing 05", is created a Javascript Date object to use his native methods to calculate the first and last days of the chosen month \(this calculation must be done at runtime because we don’t know the user choice\).

The calculated first and last days are passed as input at the Javascript function "getChartData" that create the XY data-set for the chart:

```javascript
// Listing 06
temperatureChart.chartData= ChartUtils.getChartData(firstDayMonth,lastDayMonth);
```

That returned data-set is assigned at the field named "chartData" of the QML Component with id "temperatureChart", which is our chart declared as follow:

```javascript
// Listing 07
/* The monthly temperature chart */
QChart{
  id: temperatureChart;
  width: parent.width
  height: parent.height;
  chartAnimated: false;

  /* for all the options see: QChart.js */
  chartOptions: {"barStrokeWidth": 0};

  /* chartData: set when the user press 'Show Chart' button*/
  chartType: Charts.ChartType.BAR;
}
```

Listing 07 show the declaration of the chart QML Component defined by the Qchart.js library described at the beginning of the chapter. Note ho we specify that we want a Bar chart.

## Chart creation in details

What happen at QML components level was described in the previous sections. Now, we want study the Javascript code that build the chart data-set.

All the functions used to create our temperature chart, are contained in ChartUtils.js file \(imported in our QML file\). Note: ChartUtils.js file was created for this application, is NOT owned by any library.

Let’s look at that file.

The entry point is the function: `function getChartData(fromDate, toDate)` invoked when the "Show Chart" button is pressed and the first/last days of the month are calculated.

That function perform the following steps:

### Step 1

Calculate the amount of days contained in the chosen month \(i.e. 28 – 31\) using the function "getDifferenceInDays" contained in DateUtils.js file:

```javascript
// Listing 08

/* the amount of days for the target month (e.g.: 30) */
var monthDays = DateUtils.getDifferenceInDays(fromDate,toDate);
```

### Step 2

Prepare a key-value associative Javascript Array whose size is the amount of days \(this will be the base for our chart data-set\). The key part of the Array is a date in the target month, the associated value is the temperature. All the entry values are initialized to 0 \(zero\). This is necessary to manage the cases when a temperature value is missing \(e.g. the user forgot to insert it\). The missing temperature values are the ones shown with "N/A" in the user interface.

Note: set them to "zero" is only a choice, was also possible set a different default value. This is the code that perform the operations described in ‘step 2’:

```javascript
// Listing 09
function prepareEmptyDataset(fromDate, monthDays){

  /* Init an empty associative array */
  var xyDataSet = {};
  for(var i=0;i < monthDays+1; i++) {
      /* initialize to zero the temperature value for the date */
      xyDataSet[DateUtils.addDaysAndFormat(fromDate, i)] = 0;
  }
  return xyDataSet;
}
```

### Step 3

Extract from the SQLite database the temperature values for the chosen months, then update the key-value Javascript Array prepared in the previous step. \(for details about SQLite database access from QML we invite you to read the dedicated chapter previously published\).

This activity is done in the following Javascript function: `function updateXYdataset(fromDate, toDate, xyDataSet)` \(we omit the full code for shortness\)

When the update of the Array is finished, our chart data-set will contains a not zero temperature value only for the days updated by the user. For the others ones remain the default value set during the initialization. We have now the XY data-set for our chart.

### Step 4

The last step is extract the single X and Y values to have two distinct sets. This is done with to dedicated Javascript functions:

* `function getXaxisValue(xyDataSet)`:  extract the keys of the Javascript array, they are our X values
* `function getYaxisValue(xyDataSet)`: extract the values form the array, they are our Y values.

This split is necessary because the Javascript Object accepted by the chart library require to separated sets \(see step 5\).

### Step 5

Create this Javascript object to be assigned at our QChart QML component \(declared in listing 7\)

```javascript
// Listing 10
var ChartBarData = {
  labels: < our X axis values extracted at step 4 above>,
  datasets: [{
    fillColor: "rgba(220,220,220,0.5)",
    strokeColor: "rgba(220,220,220,1)",
    data: < our Y axis values extracted at step 4 above>
    }
  ]
}
```

The assignment to QChart component is done with the following statement `temperatureChart.chartData = ChartUtils.getChartData(firstDayMonth,lastDayMonth);` where "temperatureChart" is the identifier of our QChart Component \(listing 7\) and "chartData" is his field to be initialized with an object like the one in listing 10.

Note: In other cases, when the chart data-set is already known or doesn’t depends on runtime choices \(i.e. the target month in our case\), the setting of the "chartData" field \(of QChart component\) can be done when the QChart component is declared \(i.e. in listing 7\) instead of after a processing.

## The chart legend

On the right of the chart is drawn a simple legend filled with the X-Y values used to draw the chart. It is not mandatory, we insert it to completeness and to give at the reader new ideas about possible future customizations.

That legend is created in the **ChartPage.qml** using a ListModel QML Component that act as a container for the values to display. That model is filled whit the following function \(see ChartUtils.js\):

```javascript
// Listing 11

/* fill the Listmodel used to create the Chart legend */
function getChartLegendData(xyDataSet){
  customRangeChartListModel.clear();
  for(var key in xyDataSet) {
    customRangeChartListModel.append({"date": key,"temp":xyDataSet[key]});
  }
}
```

It perform an iteration on the full XY data-set and extract the values to place \(as Javascript Object key-value\) in the ListModel. We invite to read the official documentation for more information about ListModel. We choose to not give more details because that arguments are out of the Chapter purpose.

To show the values contained in the ListModel is used an [UbuntuListView Component](https://docs.ubuntu.com/phone/en/apps/api-qml-current/Ubuntu.Components.UbuntuListView). The implementation and the configuration of our UbuntuListView can be found in ChartPage.qml file. Here a code snapshot with some omission for shortness:

```javascript
// Listing 12
/* ListView that display the values in the legend */
UbuntuListView {
  id:chartLegendListView
  anchors.fill: parent

  /* disable the dragging of the model list elements */
  boundsBehavior: Flickable.StopAtBounds
  model: /* id of the ListModel containing the data */

  delegate:
    /* ‘delegate’ is the component that define the layout
    used to display the value from the ListModel */
    Component{
      id: customReportChartLegend
      Rectangle {
        id: wrapper
        height: legendEntry.height
        border.color: UbuntuColors.graphite
        border.width:units.gu(0.5)

        Label {
          id: legendEntry
          /* ‘key’ and ‘temp’ are the key used to
          store date and temperature values in the
          ListModel */
          text: date+" :  "+temp
          fontSize: Label.XSmall
        }
      }
    }
}
```

Only a couple of tips about how UbuntuListView works: it receive in input \(with the field named "model"\) the ListModel containing the values to display and a QML component that define the layout used to show them in the page \(field "delegate"\). The "delegate" component access at the values in the ListModel using the key values used to store them \(i.e. "date" and "temp" looking at listing 11\).

## Conclusions

We have shown ho to draw charts from an QML + Javascript application using values from a SQLite database. To reach that we have made some choices, like to use a Bar Chart or use "Zero" as default value in case of missing value. Maybe, our choices and some details can be disputable but our focus was provide at the reader a knowledge about drawing charts with QML and suggest new ideas for future applications.

As exercise, the reader can try the use of different chart chart type \(e.g. a line bar\) or add another chart about pressure values for the city...

We invite the reader to look at the application source code \(and his comments\) for more details omitted for shortness in this chapter.

## References

Here some useful links about the arguments explained in the chapter:

* [Ubuntu touch QML API](https://docs.ubuntu.com/phone/en/apps/api-qml-current)
* [The repository of Qchart.js library](https://github.com/jwintz/qchart.js)

## People who have collaborated

* Fulvio Russo: author
* Miguel Menéndez: revision of the chapter in English. Translation into Spanish.

