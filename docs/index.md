---
layout: docs
title: Documentation
permalink: /docs/index.html
---

Vega-Lite provides a higher-level grammar for visual analysis, akin to ggplot or Tableau, that generates complete [Vega](https://vega.github.io/) specifications.

Vega-Lite specifications consist of simple mappings of variables in a data set to visual encoding channels such as position (`x`,`y`), `size`, `color` and `shape`. These mappings are then translated into detailed visualization specifications in the form of Vega specification language.  Vega-Lite produces default values for visualization components (e.g., scales, axes, and legends) in the output Vega specification using a rule-based approach, but users can explicit specify these properties to override default values.  
This documentation outlines the syntax of Vega-Lite specification, and how to embed Vega-Lite visualizations in your applications.

## Vega-Lite Specification

A Vega-Lite specification is a JSON object that describes data source (`data`),
a mark type (`marktype`), key-value visual encodings of data variables (`encoding`),
and data transformations.

Vega-Lite assumes a tabular data model: each data source is a set of records,
where each record has values for the same set of variables.

In the current version, Vega-Lite specification is a JSON object
that contains the following top-level properties:

| Property             | Type          | Description    |
| :------------        |:-------------:| :------------- |
| [data](data.html)    | Object        | An object describing data source |
| [marktype](marktype.html)| String        | The mark type.  Currently Vega-Lite supports `bar`, `line`, `area`, `point`, and `text` (text table). |
| [encoding](encoding.html)| Object        | key-value mapping between encoding channels and encoding object |
| [config](config.html)   | Object        | Configuration object. |

## Using Vega-Lite

### Inline Data

Here is the bare minimum html file to get Vega-Lite with inline values working in a webpage.
Basically, Vega-Lite compiles a Vega-Lite specification into a Vega
specification and use Vega Runtime to display visualizations.

```html
<!DOCTYPE html>
<meta charset="utf-8">

<script src="../lib/d3.min.js"></script>
<script src="../lib/vega.js"></script>
<script src="../lib/vega-lite.js"></script>

<div id="vis"></div>

<script>

function parse(spec) {
  vg.parse.spec(spec, function(chart) {
    chart({el:"#vis"}).update(); });
}

var vlspec = {
      "data": {
        "values": [
          {"a":"A", "b":28}, {"a":"B", "b":55}, {"a":"C", "b":43},
          {"a":"D", "b":91}, {"a":"E", "b":81}, {"a":"F", "b":53},
          {"a":"G", "b":19}, {"a":"H", "b":87}, {"a":"I", "b":52}
        ]
      },
      "marktype": "bar",
      "encoding": {
        "x": {"type": "ordinal","field": "a"},
        "y": {"type": "quantitative","field": "b"}
      }
    };

var vgspec = vl.compile(vlspec).spec;
parse(vgspec);

</script>
```


### Data from URL

Here is the bare minimum html file to get Vega-Lite with data from url working in a webpage.
Currently, Vega-Lite relies on pre-calculating statistics of the underlying data,
so `vl.data.stats()` must be called beforehand.
After that, Vega-Lite compiles a Vega-Lite specification into a Vega specification
(using some knowledge from the stats object) and use Vega Runtime to display visualizations.

Note that the need to call `vl.data.stats()` will be eliminated very soon (before we release 1.0).

```html
<!DOCTYPE html>
<meta charset="utf-8">

<script src="../lib/d3.min.js"></script>
<script src="../lib/vega.js"></script>
<script src="../lib/vega-lite.js"></script>

<div id="vis"></div>

<script>

function render(vlSpec) {
  var callback = function(stats) {
    var vgSpec = vl.compile(vlSpec, stats).spec;

    vg.parse.spec(vgSpec, function(chart) {
      var view = chart({el: '#vis', renderer: 'svg'});
      view.update();
    });
  };

  if (!vlSpec.data.values) {
    d3.json(vlSpec.data.url, function(err, data) {
      if (err) return alert('Error loading data ' + err.statusText);
      var stats = vl.data.stats(data);
      callback(stats);
    });
  } else {
    callback();
  }
}

var vlspec = {
      "data": {"url": "data/cars.json"},
      "marktype": "point",
      "encoding": {
        "x": {"type": "ordinal","field": "Origin"},
        "y": {"type": "quantitative","field": "Acceleration"}
      }
    };


render(vlSpec);

</script>
```


__Pending Revision__:
Vega-Lite Compile API is under revision.  A better tutorial is coming soon.