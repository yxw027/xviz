# Components

## Common Fields

Component is the base type for all visual elements.

| **Name** | **Type**                 | **Description**                     |
| -------- | ------------------------ | ----------------------------------- |
| `type`   | `enum{ component_type }` | The explicit type of this component |

The valid values of `component_type`:

- [`TABLE`](#Table)
- [`METRIC`](#Metric)
- [`PLOT`](#Plot)
- [`TREETABLE`](#treetable)
- [`VIDEO`](#video)
- [`SELECT`](#select-warning-unstable-feature-) (WARNING: Unstable feature)

## Table

The Table element renders data similar to how data is presented in a traditional HTML table.

_TODO: screentshot: panel with table from streetscape.gl demo app_

| **Name**          | **Type**    | **Description**                                                                                                                                       |
| ----------------- | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `stream`          | `stream_id` | The stream name to populate the table data from. Must be a stream of [`treetable`](/docs/protocol-schema/ui-primitives.md#treetable-type) primitives. |
| `title`           | `string`    | A title to display above the component.                                                                                                               |
| `description`     | `string`    | A description of this element, displayed when hovering over the title.                                                                                |
| `displayObjectId` | `boolean`   | Controls whether or not the object ID column, which is automatically added to TreeTables, is displayed.                                               |

### Supported Interactions

| **Interaction**    | **Description**                                                                      |
| ------------------ | ------------------------------------------------------------------------------------ |
| Highlight on Hover | Highlight an object in the world view when hovering over a row or a row is selected. |
| Copy               | Copy and paste data out of the table                                                 |
| Sort               | Sort the table based on the contents of the columns                                  |
| Filter             | Exclude rows based on their values in certain columns                                |

### JSON Example

```js
{
  "components": [
    {
      "type": "TABLE",
      "title": "A table showing something"
      "description": "These are the details of this table",
      "displayObjectId": true,
      "stream": "/prediction/some_table",
    }
  ]
}
```

### YAML Example

```
components:
  - description: These are the details of this table
    display_object_id: true
    stream: /prediction/some_table
    title: A table showing something
    type: TABLE
```

## Metric

The Metric element renders time series data. A single metric element can render multiple streams of
time series data on the same chart so that data can be easily compared.

_TODO: screenshot: streetscape.gl demo app showing drag-able sub-panels_

| **Name**      | **Type**                 | **Description**                                                                                                              |
| ------------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| `streams`     | `map<string, stream_id>` | The streams to display in the chart, expects a [`time_series`](/docs/protocol-schema/core-types.md#time-series-state) stream |
| `title`       | `string`                 |                                                                                                                              |
| `description` | `string`                 | Displayed when hovering over the title                                                                                       |

### Supported Interactions

| **Interaction**  | **Description**                                                                                |
| ---------------- | ---------------------------------------------------------------------------------------------- |
| Details on Hover | When hovering over a metrics element, the current value of the metric at the location is shown |

### JSON Example

```js
{
  "components": [
    {
      "title": "Some metric",
      "type": "METRIC",
      "description": "The actual vs commanded value for some variable",
      "streams": ["/some_value/actual", "/some_value/commanded"]
    }
  ]
}
```

### YAML Example

```
components:
  - type: METRIC
    title: Some metric
    description: The actual vs commanded value for some variable
    streams:
      - /some_value/actual
      - /some_value/commanded
```

## Plot

The Plot component is used for showing one or more
[`variables` streams](/docs/protocol-schema/core-types.md#variables) at once on screen. It has a
standard mode where it shows a set of dependent variables as a function of an independent variable.
In region mode 2D regions defined by an x variable and min and max y variables.

_TODO: screenshot: streetscape.gl demo app showing normal variable plot_

| **Name**      | **Type** | **Description**                        |
| ------------- | -------- | -------------------------------------- |
| `title`       | `string` | Shown at the top of the plot           |
| `description` | `string` | Displayed when hovering over the title |

#### Standard Mode Fields

| **Name**              | **Type**          | **Description**                                                                                                                                     |
| --------------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `independentVariable` | `stream_id`       | The [`variable`](/docs/protocol-schema/core-types.md#variables) stream to use as the X axis.                                                        |
| `dependentVariables`  | `list<stream_id>` | The [`variable`](/docs/protocol-schema/core-types.md#variables) streams to plot on the Y axis as a function of the stream that makes up the X axis. |

#### Region Mode Fields (WARNING: Unstable feature)

Theses define areas between an upper and lower bound along a set of x coordinates. These streams can
contain multiple objects so you can plot a full set of regions with a single entry region entry. To
style these regions apply style information to the `x` stream.

| **Name**  | **Type**       | **Description**    |
| --------- | -------------- | ------------------ |
| `regions` | `list<region>` | The set of regions |

| **Name** | **Type**    | **Description**                                                                                                                                                                 |
| -------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `x`      | `stream_id` | The X axis values of the regions, expects a floating point [`variable`](/docs/protocol-schema/core-types.md#variables) stream. Style's on this stream apply to the full region. |
| `yMin`   | `stream_id` | The visible lower bound of the region on the Y axis, expects a floating point [`variable`](/docs/protocol-schema/core-types.md#variables) stream.                               |
| `yMax`   | `stream_id` | The visible upper bound of the region on the Y axis, expects a floating point [`variable`](/docs/protocol-schema/core-types.md#variables) stream.                               |

### Supported Interactions

| **Interaction**       | **Description**                                                                                                 |
| --------------------- | --------------------------------------------------------------------------------------------------------------- |
| Details on Hover      | When hovering over the plot, the exact value of every stream at the point being hovered over will be displayed. |
| Toggle Streams On/Off | Below the plot a list of streams is shown, clicking on the streams will toggle displaying them on the plot      |

### JSON Example

```js
{
  "components": [
    {
      "type": "PLOT",
      "title": "Some Other Streams vs Some Stream",
      "description": "The change in some streams as a function of the other one",
      "independentVariable": "/some/stream",
      "dependentVariables": ["/some/other_stream", "/some/second_other_stream"]
    }
  ]
}
```

### YAML Example

```
components:
  - type: PLOT
    title: Some Other Streams vs Some Stream
    description: The change in some streams as a function of the other one
    independentVariable: /some/stream
    dependentVariables:
    - /some/other_stream
    - /some/second_other_stream
```

## Video

The Video element is used to display streams of video data. A single video element can support
multiple streams of video, however not all streams may be viewed at the same time. To view multiple
streams concurrently, multiple video elements need to be created.

_TODO: screenshot: streetscape.gl demo app showing the video element_

| **Name**  | **Type**       | **Description**                                                                                                                                                                 |
| --------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cameras` | `list<string>` | A list of the streams of video that can be rendered by this element. The available streams are in the [`metadata`](/docs/protocol-schema/session-protocol.md#metadata) message. |

### Supported Interactions

| **Interaction**           | **Description**                                                                                                                               |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Select source             | Using a dropdown overlayed on the video, a user can select which video to show.                                                               |
| Video settings adjustment | A user can adjust video settings such as saturation, brightness, and contrast from the settings menu that is overlaid on the video component. |

### JSON Example

```js
{
  "components": [
    {
      "type": "VIDEO",
      "cameras": [
        "front-center-roof-camera",
        "rear-starboard-roof-camera",
        "rear-port-roof-camera"
      ]
    }
  ]
}
```

### YAML Example

```
components:
  - type: VIDEO
    cameras:
      - front-center-roof-camera
      - rear-starboard-roof-camera
      - rear-port-roof-camera
```

## TreeTable

The TreeTable represents data in a way similar to that of a file system explorer window. The data is
hierarchical with common fields at each node, some fields being empty depending on where the node
sits in the tree. Each node is typically rendered as one row in TreeTable with higher level nodes
being collapsible. . It's similar in concept to Qt's
([tree model concept](http://doc.qt.io/qt-5/qtwidgets-itemviews-simpletreemodel-example.html#design-and-concepts)).
See the docs on the [`treetable`](/docs/protocol-schema/ui-primitives.md#treetable-type) UI
primitive to learn more about the XVIZ version.

_TODO: screenshot: streetscape.gl demo app showing treetable data_

| **Name**            | **Type**    | **Description**                                                                                                                                    |
| ------------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `stream`            | `stream_id` | The stream of of [`treetable`](/docs/protocol-schema/ui-primitives.md#treetable-type) primitives to populate with which to populate this component |
| `title`             | `string`    | A title to display at the top of the TreeTable                                                                                                     |
| `description`       | `string`    | A description of this component, displayed when hovering over the title.                                                                           |
| `display_object_id` | `boolean`   | Whether or not to display the object ID column                                                                                                     |

### Supported Interactions

| **Interaction**    | **Description**                                                                      |
| ------------------ | ------------------------------------------------------------------------------------ |
| Highlight on Hover | Highlight an object in the world view when hovering over a row or a row is selected. |
| Copy               | Copy and paste data out of the table                                                 |
| Sort               | Sort the table based on the contents of the columns                                  |
| Filter             | Exclude rows based on their values in certain columns                                |

### JSON Example

```js
{
  "components": [
    {
      "display_object_id": false,
      "type": "TREETABLE",
      "description": "These are the details of the TreeTable",
      "stream": "/some/stream/of/treetable/primmatives",
      "title": "A TreeTable!"
    }
  ]
}
```

### YAML Example

```
components:
  - description: These are the details of the TreeTable
    display_object_id: false
    stream: /some/stream/of/treetable/primmatives
    title: A TreeTable!
    type: TREETABLE
```

## Select (WARNING: Unstable feature)

The Select components allows dynamically configuring the XVIZ transformation done on data sent to
client. The component displays a list of options populated by an XVIZ variable stream, and allows
the user to select one of them. This also known as a "combobox" or "dropdown".

When a new option is selected the client sends backend a message reconfiguration with the updated
value then:

- The backend responds with an updated view of the world for the current time
- Any future requests will use the updated configuration

| **Name**      | **Type**    | **Description**                                                                                       |
| ------------- | ----------- | ----------------------------------------------------------------------------------------------------- |
| `title`       | `string`    | Displayed on screen besides the selection box                                                         |
| `description` | `string`    | Displayed when hovering over the title                                                                |
| `stream`      | `stream_id` | A [`variable`](/docs/protocol-schema/core-types.md#variables) stream containing the options to select |
| `onchange`    | `onchange`  | Describes what to happen when a new select option is chosen.                                          |

**onchange** fields

| **Name** | **Type** | **Description**                                            |
| -------- | -------- | ---------------------------------------------------------- |
| `target` | `string` | A JSON pointer (RFC 6901) that describes the key to update |

### Supported Interactions

| **Interaction** | **Description**                                 |
| --------------- | ----------------------------------------------- |
| `onchange`      | Reconfigure the backend when the select changes |

### Resulting messages

The reconfiguration message sent preforms an update to the configuration of the backend. Allow to
for example to toggle on and off an expensive to compute and transmit feature.

So for the examples below we would get a `reconfigure` message of the form:

```
{
    "update_type": "DELTA",
    "config_update": {
        "system": {
            "info": {
                "type": "newvalue"
            }
        }
    }
}
```

### JSON Example

```js
{
  "components": [
    {
      "title": "Additional Info Type",
      "description": "Which type of additional information you want sent",
      "type": "SELECT",
      "stream": "/system/additional_info/types",
      "onchange": {
        "target": "/system/info/type"
      }
    }
  ]
}
```

### YAML Example

```
components:
  - type: SELECT
    title: Additional Info Type
    description: Which type of additional information you want sent
    streams: /system/additional_info/types
    onchange:
      target: /system/info/type
```

## Complete Example

Below is a complete example of what a panel definition would look like

## JSON Example

```js
{
  "type": "PANEL",
  "layout": "VERTICAL",
  "name": "Example Panel",
  "interactions": ["REORDERABLE"],
  "children": [
    {
      "type": "CONTAINER",
      "layout": "HORIZONTAL",
      "name": "Example Container #1",
      "interactions": ["DRAG_OUT"],
      "children": [
        {
          "type": "TABLE",
          "title": "A nested table showing something",
          "description": "These are the details of this table",
          "stream": "/some/table_stream",
          "displayObjectId": false
        },
        {
          "type": "PLOT",
          "title": "A nested plot!",
          "description": "The change in some streams as a function of the other one",
          "independentVariable": "/some/stream",
          "dependentVariable": [
            "/some/other_stream",
            "/some/second_other_stream"
          ]
        }
      ]
    },
    {
      "type": "TABLE",
      "title": "A table showing something",
      "description": "These are the details of this table",
      "displayObjectId": true,
      "stream": "/prediction/some_table"
    },
    {
      "type": "METRIC",
      "title": "Some metric",
      "description": "The actual vs commanded value for some variable",
      "streams": [
        "/some_value/actual",
        "/some_value/commanded"
      ]
    },
    {
      "type": "PLOT",
      "title": "Some Other Streams vs Some Stream",
      "description": "The change in some streams as a function of the other one",
      "independentVariable": "/some/stream",
      "dependentVariable": [
        "/some/other_stream",
        "/some/second_other_stream"
      ]
    },
    {
      "type": "VIDEO",
      "cameras": [
        "front-center-roof-camera",
        "rear-starboard-roof-camera",
        "rear-port-roof-camera"
      ]
    },
    {
      "type": "TREETABLE",
      "title": "A TreeTable!",
      "description": "These are the details of the TreeTable",
      "displayObjectId": false,
      "stream": "/some/stream/of/treetable/primmatives"
    }
  ]
}
```

## YAML Example

```
components:
  - description: These are the details of this table
    display_object_id: true
    stream: /prediction/some_table
    title: A table showing something
    type: TABLE
  - description: The actual vs commanded value for some variable
    streams:
      - /some_value/actual
      - /some_value/commanded
    title: Some metric
    type: METRIC
  - dependent_variables:
      - /some/other_stream
      - /some/second_other_stream
    description: The change in some streams as a function of the other one
    independent_variable: /some/stream
    title: Some Other Streams vs Some Stream
    type: PLOT
  - cameras:
      - front-center-roof-camera
      - rear-starboard-roof-camera
      - rear-port-roof-camera
    type: VIDEO
  - description: These are the details of the TreeTable
    display_object_id: false
    stream: /some/stream/of/treetable/primmatives
    title: A TreeTable!
    type: TREETABLE
containers:
  - components:
      - description: These are the details of this table
        display_object_id: false
        stream: /some/table_stream
        title: A nested table showing something
        type: TABLE
      - dependent_variables:
          - /some/other_stream
          - /some/second_other_stream
        description: The change in some streams as a function of the other one
        independent_variable: /some/stream
        title: A nested plot!
        type: PLOT
    containers: []
    interactions:
      - DRAG_OUT
    layout: HORIZONTAL
    name: 'Example Container #1'
interactions:
  - REORDERABLE
layout: VERTICAL
name: Example Panel
```
