# @gustav0ar/ngx-highcharts
##### This is a fork based on https://github.com/kiwigrid/angular2-highcharts

> Highcharts chart components for Angular apps. 👉 [Live Demo](http://plnkr.co/edit/AJwozFWVR7TkQZnt05dN?p=preview)


[![build](https://travis-ci.org/kiwigrid/angular2-highcharts.svg?branch=master)](https://travis-ci.org/gevgeny/angular2-highcharts)
[![npm version](https://img.shields.io/npm/v/npm.svg)](https://github.com/kiwigrid/angular2-highcharts)
[![Maintenance](https://img.shields.io/maintenance/yes/2017.svg)](https://github.com/kiwigrid/angular2-highcharts)
[![David](https://img.shields.io/david/expressjs/express.svg)](https://github.com/kiwigrid/angular2-highcharts)


## Table of Contents
 - [Setting Up](#setting-up)
  - [Install angular2-highcharts](#install-angular2-highcharts)
  - [Setup App @NgModule](#setup-app-ngmodule)
 - [Usage](#usage)
  - [Basic Usage](#basic-usage)
    - [Setup App Module](#setup-app-module)
    - [Create First Chart Component](#create-first-chart-component)
  - [Handling Events](#handling-events)
    - [Chart Events](#chart-events)
    - [Series Events](#series-events)
    - [Point Events](#point-events)
    - [Axis Events](#axis-events)
  - [Dynamic Interaction with Chart Object](#dynamic-interaction-with-chart-object)
  - [Tooltip Formatter for Chart Object](#tooltip-formatter-for-chart-object)
  - [Highstock](#highstock)
  - [Highmaps](#highmaps)
  - [Add Highcharts Modules](#add-highcharts-modules)
  - [Access to the Highcharts Static API](#access-to-the-highcharts-static-api)
 -  [More Examples](#more-examples)
 - [FAQ](#faq)
 - [License](#license)

## Setting Up

### Install @gustav0ar/ngx-highcharts
```
npm install @gustav0ar/ngx-highcharts --save
```

### Setup App @NgModule
```TypeScript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { ChartModule } from '@gustav0ar/ngx-highcharts';
import { App } from './App';

@NgModule({
    imports: [
      BrowserModule,
      ChartModule.forRoot(require('highcharts'))
    ],
    declarations: [App],
    bootstrap: [App]
})
export class AppModule {}
```

### For angular-cli and other Webpack environments
No any additional setup needed

### For SystemJS environment
You should add appropriate mapping to your `systemjs.config.js`

```js
...
map: {
  ...
  '@gustav0ar/ngx-highcharts': 'node_modules/@gustav0ar/ngx-highcharts',
  'highcharts': 'node_modules/highcharts',
}
...
packages: {
  ...
  highcharts: {
    main: './highcharts.js',
    defaultExtension: 'js'
  },
  '@gustav0ar/ngx-highcharts': {
    main: './index.js',
    defaultExtension: 'js'
  }
}
```

## Usage

### Basic Usage

#### Create First Chart Component
Main charts functionality provided by the `chart` component and its `options` property.

```TypeScript
import { Component } from '@angular/core';

@Component({
    selector: 'simple-chart-example',
    template: `
        <chart [options]="options"></chart>
    `
})
export class App {
    constructor() {
        this.options = {
            title : { text : 'simple chart' },
            series: [{
                data: [29.9, 71.5, 106.4, 129.2],
            }]
        };
    }
    options: Object;
}
```
👉 [Live Demo](http://plnkr.co/edit/IuwjpPB1YQW1T7i4B8SZ?p=preview)

### Handling Events
Highcharts itself provides bunch of events, and you still can use them with @gustav0ar/ngx-highcharts via the `options` property of the `chart` component. But it is not an angular way to handle events like this. So that @gustav0ar/ngx-highcharts provides `EventEmitter<ChartEvent>` wrappers for highcharts events. `ChartEvent` is an @gustav0ar/ngx-highcharts class which simply wraps original Highcharts events (`chartEvent.originalEvent`) and adds event handler context (`chartEvent.context`) since it differs depending on events.

#### Chart Events

All the events from the [options.chart.events](http://api.highcharts.com/highcharts#chart.events) are available as output properties of the `chart` component.

```HTML
<chart [options]="options" (selection)="onChartSelection($event)"> </chart>
```
```TypeScript
onChartSelection (e) {
  this.from = e.originalEvent.xAxis[0].min.toFixed(2);
  this.to = e.originalEvent.xAxis[0].max.toFixed(2);
}
```
👉 [Live Demo](http://plnkr.co/edit/vdgKVJOymMYhiyqZrPma?p=preview)

#### Series Events

To use series events the same way you need to add the `series` component as a child of your chart. The only purpose of this auxiliary component is to provide access to [options.plotOptions.series.events](http://api.highcharts.com/highcharts#plotOptions.series.events) API

```HTML
<chart [options]="options">
    <series (mouseOver)="onSeriesMouseOver($event)">
    </series>
</chart>
<p><b>{{serieName}}</b> is hovered<p>
```
```TypeScript
onSeriesMouseOver (e) {
  this.serieName = e.context.name;
}
```
👉 [Live Demo](http://plnkr.co/edit/GkaJlk2UJjbTwsPyGXGC?p=preview)
#### Point Events

Similary you can use the `point` to access to [options.plotOptions.series.point.events](http://api.highcharts.com/highcharts#plotOptions.series.point.events) API.
```HTML
<chart [options]="options">
    <series>
        <point (select)="onPointSelect($event)"></point>
    </series>
</chart>
<p><b>{{point}}</b> is selected<p>
```
👉 [Live Demo](http://plnkr.co/edit/TpKoJ60n4vyIDWxHNUkg?p=preview)
#### Axis Events

Similary you can use the `xAxis` or `yAxes` to access to [options.xAxis.events](http://api.highcharts.com/highcharts#xAxis.events) or [options.yAxis.events](http://api.highcharts.com/highcharts#yAxis.events) API.
```HTML
<chart [options]="options">
     <xAxis (afterSetExtremes)="onAfterSetExtremesX($event)"></xAxis>
     <yAxis (afterSetExtremes)="onAfterSetExtremesY($event)"></yAxis>
</chart>
<p>{{minX}} - {{maxX}}<p>
<p>{{minY}} - {{maxY}}<p>
```
```TypeScript
onAfterSetExtremesX (e) {
  this.minX = e.context.min;
  this.maxX = e.context.max;
}
onAfterSetExtremesY (e) {
  this.minY = e.context.min;
  this.maxY = e.context.max;
}
```
👉 [Live Demo](http://plnkr.co/edit/c4ojcIRVOOwq7xmk9kfx?p=preview)
### Dynamic Interaction with Chart Object

@gustav0ar/ngx-highcharts provides possibility to interact with native `HighchartsChartObject` chart object.

```TypeScript
@Component({
    selector: 'my-app',
    directives: [CHART_DIRECTIVES],
    template: `
      <chart [options]="options"
             (load)="saveInstance($event.context)">
      </chart>
    `
})
class AppComponent {
    constructor() {
        this.options = {
          chart: { type: 'spline' },
          title: { text : 'dynamic data example'}
          series: [{ data: [2,3,5,8,13] }]
        };
        setInterval(() => this.chart.series[0].addPoint(Math.random() * 10), 1000);
    }
    chart : Object;
    options: Object;
    saveInstance(chartInstance) {
        this.chart = chartInstance;
    }
}
```
👉 [Live Demo](http://plnkr.co/edit/OQSFSKisIIWAH0megy4d?p=preview)

### Tooltip Formatter for Chart Object

angular2-higcharts provides possibility to override the native highcharts tooltip formatter (instead of using the highcharts callback)

```TypeScript
@Component({
    selector: 'my-app',
    directives: [CHART_DIRECTIVES],
    template: `
      <chart [options]="options"
             [tooltipFormatter]="formatter">
      </chart>
    `
})
class AppComponent {
    constructor() {
        this.options = {
          chart: { type: 'spline' },
          title: { text : 'dynamic data example'}
          series: [{ data: [2,3,5,8,13] }]
        };
    }
    chart : Object;
    options: Object;
    formatter: (point) {
        return `x: point.x y: point.y`
    }
}
```
👉 [Live Demo](http://plnkr.co/edit/OQSFSKisIIWAH0megy4d?p=preview)

### Highstock
```
<chart type="StockChart" [options]="options"></chart>
```
Also you need to change your `@NgModule` setup.

```diff
...
@NgModule({
    ...
    imports: [
      BrowserModule,
      ChartModule.forRoot(
-       require('highcharts'),
+       require('highcharts/highstock')
      )
    ]
})
```

👉 [Live Demo](http://plnkr.co/edit/2xSewTZ9b213vA0ALmFq?p=preview)

### Highmaps
```
<chart type="Map" [options]="options"></chart>
```
Also you need to change your `@NgModule` setup.

```diff
...
@NgModule({
    ...
    imports: [
      BrowserModule,
      ChartModule.forRoot(
-       require('highcharts'),
+       require('highcharts/highmaps')
      )
    ],
})
```

👉 [Live Demo](http://plnkr.co/edit/AmDfKwhRhshFn3CPprkk?p=preview)


### Add Highcharts Modules
Any other modules like highcharts-3d, highcharts-exporintg and etc. can be also added in `@NgModule` after main chart module

```diff
...
@NgModule({
    ...
    imports: [
      BrowserModule,
      ChartModule.forRoot(
        require('highcharts'),
+       require('highcharts/highcharts-3d'),
+       require('highcharts/modules/exporting')
      )
    ],
})
```

Check out structure of the `node-modules/highcharts` folder to find necessary module.

👉 [Live Demo](http://plnkr.co/edit/sz6OfccvAetQcBX8KFXy?p=preview)



### Access to the Highcharts Static API

```diff
...
const Highcharts = require('highcharts');

Highcharts.setOptions({
  colors: ['#50B432']
});

@NgModule({
    ...
    imports: [
      BrowserModule,
      ChartModule.forRoot(
-       require('highcharts'),
+       Highcharts
      )
    ],
})
```

👉 [Live Demo](http://plnkr.co/edit/uCtPFUExmZFG0diOvbXS?p=preview)

##More Examples

Here are some common charts examples with Webpack integration https://github.com/gevgeny/angular2-highcharts/tree/master/examples/webpack

##FAQ

#### Why don't my series, title, axes and etc redraw after I update initial options ?

Because `@gustav0ar/ngx-highcharts` is just a thin wrapper of the [Highcharts](http:/ /www.highcharts.com/) library and doesn't bind to initial options. I understand that you expect more angular-way behaviour like data binding with appropriate redrawing. But it is barely possible to implement it without redundant complications and performance decrease because almost all options can be dynamic. So my idea was to avoid any additional logic more than just a sugar (like events for series and options). In the other hand Highcharts has great [API](http://api.highcharts.com/highcharts#Chart) for dynamic manipulations with chart and `@gustav0ar/ngx-highcharts` [provides you access](#dynamic-interaction-with-chart-object) to the original chart object.

## License
MIT @ Eugene Gluhotorenko
MIT @ Kiwigrid
MIT @ Gustav0ar


