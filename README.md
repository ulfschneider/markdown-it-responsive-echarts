# markdown-it-responsive-echarts

A [markdown-it](https://github.com/markdown-it/markdown-it) plugin to render responsive [ECharts](https://echarts.apache.org/) diagram definitions that react to light mode and dark mode.

In your Markdown, describe the chart within a fenced codeblock, introduced with the `echarts` keyword.

> [!NOTE]
> It is mandatory to create an object named `config` that holds your chart definition.
> You cannot choose a different name!

~~~markdown
```echarts
const config = {
  renderOptions: {
    renderer: "svg"
  },
  figcaption: 'This is the most simple line chart rendered by echarts',
  xAxis: {
    type: 'category',
    data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
  },
  yAxis: {
    type: 'value'
  },
  series: [
    {
      data: [150, 230, 224, 218, 135, 147, 260],
      type: 'line'
    }
  ]
};
```
~~~

As a result you get the chart code, wrapped into a `figure` tag:

```html
<figure id="echarts-tifftwsxeckl" class="echarts">
  <div id="echarts-rfisxrzggkaz" class="echarts-container line">
    <!-- the chart will be here -->
  </div>
  <script src="https://cdn.jsdelivr.net/npm/echarts/dist/echarts.min.js"></script>
  <script type="module">
  //... the plugin will put the chart definition code here
  </script>
  <figcaption>This is the most simple line chart rendered by echarts</figcaption>
</figure>
```

Notice how the `figcaption` is created from the `config.figcaption` property.

The diagram types of all data series in the diagram are added as CSS class names to the ECharts container. In the example above you see `echarts-container line`, because we are preparing a line chart. You can use that information for the CSS styling of your diagrams.

The plugin will make your charts responsive by default, but it is important to start with proper `width` and `height` settings for the ECharts container, othwerwise you end up with a container that does have zero width or zero height and you won´t be able to see any diagram. You can for example do something like:

```css
.echarts-container {
    height: 60vh;
    width: 100%;
    &.pie {
        height: unset;
        aspect-ratio: 1 / 1;
        max-width: 65ch;
        max-height: 60vh;
    }
}
```

The settings to be applied to all `series` of all charts can be defined in the `defaults.series` settings object.

Your charts will react to light mode and dark mode when you have `defaults.darkModeConfig` settings defined. In that case `darkModeConfig` will be applied automatically to each chart by the plugin when dark mode is active.

When creating the config object, you have access to three generated variables you might want to use to adapt your chart to its context:

- `containerWidth`: Will give you the current width of the chart container as a number of pixels.
- `containerHeight`: Will give you the current height of the chart container as a number of pixels.
- `isDarkMode`: Will be true if the page is viewed in dark mode, otherwise false.

## Install

`npm install markdown-it-responsive-echarts`

## Use

```js
import markdownItECharts from 'markdown-it-responsive-echarts'
import markdownIt from 'markdown-it'

const md = markdownIt()

//default settings
const markdownItEChartsOptions = {
  echarts: "https://cdn.jsdelivr.net/npm/echarts/dist/echarts.min.js",
  throwOnError: false,
  verbose: false,
  defaults: {},
};

md.use(markdownItECharts, markdownItEChartsOptions)
```

## Options

- `echarts`: The location of the ECharts library code. You might host the code on your own server and would have to adapt this setting in that case to point to that location.
- `throwOnError`: A value of `true` will throw errors that occurred during processing. A value of `false` will only log errors. Default value is `false`.
- `verbose`: A value of `true` will activate detailed logging. Default is `false`.
- `defaults` : Optional. The default settings to be applied to all echart diagrams prepared by the plugin. Use it exactly how you would set options for an echart. There are some specialities that expand the defaults:
  - The optional `series` property, which you can use to make settings that belong to all series used in your chart.
  - The optional `darkModeConfig` property, which you can use for dark mode settings. Dark mode is automatically detected during runtime and the settings you define here are applied to the chart. `darkModeConfig` can also contain a `series` property which is used for settings that should be applied to all series of a chart when dark mode is active.
  - The optional `renderOptions` property, wich controls how all charts are rendered. You can define a `renderOptions` object as well inside of the chart config, which will apply the `renderOptions` to that single chart only. In either case, `renderOptions` can contain any of the following properties:
    - `renderer`: Optional. Default is `"canvas"`. It can be either `"canvas"` or `"svg"`.
    - `debounceMillis`: Optional. Default is `16`. A debounce timeout in milliseconds for repeated rendering of charts, which for instance is done continuously when resizing the browser window.
    - `notMerge`:  Optional. Please refer to https://echarts.apache.org/en/api.html#echartsInstance.setOption.
    - `replaceMerge`: Optional. Please refer to https://echarts.apache.org/en/api.html#echartsInstance.setOption.
    - `lazyUpdate`: Optional. Please refer to https://echarts.apache.org/en/api.html#echartsInstance.setOption.
    - `silent`: Optional. Please refer to https://echarts.apache.org/en/api.html#echartsInstance.setOption.


Here is an example for the initialization of the plugin with settings that will be applied to all echart diagrams. These settings ensure the charts respond to changes from light mode to dark mode and the charts are rendered in SVG format:

```js
mdLib.use(markdownItEcharts, {
  verbose: true,
  defaults: {
    renderOptions: {
      renderer: "svg"
    },
    aria: {
      show: true,
    },
    toolbox: {
      feature: {
        restore: {},
        saveAsImage: {},
      },
    },
    title: {
      textStyle: {
        color: "#000",
      },
    },
    legend: {
      textStyle: {
        color: "#000",
      },
    },
    textStyle: {
      color: "#000",
    },
    series: {
      label: {
        show: true,
        textStyle: {
          color: "#000",
        },
      },
      markPoint: {
        label: {
          show: true,
          textStyle: {
            color: "#000",
          },
        },
      },
    },
    darkModeConfig: {
      title: {
        textStyle: {
          color: "#fff",
        },
      },
      textStyle: {
        color: "#fff",
      },
      legend: {
        textStyle: {
          color: "#fff",
        },
      },
      series: {
        label: {
          textStyle: {
            color: "#fff",
          },
        },
        markPoint: {
          label: {
            textStyle: {
              color: "#fff",
            },
          },
        },
      },
    },
  },
});
```
