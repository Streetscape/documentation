# Mapbox Connector V1.0

# Introduction

Streetscape Mapbox Connector is a tool for creative agencies to embed and configure 3d maps enriched with live Streetscape data. 

Attention: This product is currently in alpha, and we are not currently able to offer any of the features outlined in this documentation. The purpose of this page is to gauge potential interest in our upcoming product. If you have any questions about features or want to request a feature that isn't mentioned here, please contact your customer support manager and let us know! 

> Note: Everything in this document is subject to change.

# Quick Start

```jsx
<script type="text/javascript" src="https://www.streetscape.ai/js/smc-1.0.js"></script>
<script type="text/javascript">
let ssmap = new Map({
  container: "#mapContainer",
  apiKey: "my-public-api-key",
  mapboxLicenseKey: "mapbox-license-key"
});
</script>
```

### The Mapbox Object

```jsx
//it's easy to get the mapbox object and modify it

let map = ssmap.getMapbox();

map.addLayer("my-new-layer", {});

ssmap.setMapbox(map);
```

### Filters

```jsx
let filters = ssmap.getFilters("status");

/* returns the following
[
  {name: "For Sale", isSelected: false, isEnabled: true},
  {name: "Under Construction", isSelected: false, isEnabled: true},
  {name: "For Lease", isSelected: false, isEnabled: true},
]
*/
```

```jsx
//toggle the selection of a filter
let filters = ssmap.getFilters("status");

filters = filters.map(filter = () => {
  filter.selected = filter.name === "For Sale" ? !filter.selected : filter.selected;
  return filter;
});

ssmap.setFilters(filters);

```

### Levels

```jsx
let levels = ssmap.getLevels();

/* returns the following
[
  {id: 1, isSelected: false, isEnabled: true},
  {id: 2, isSelected: false, isEnabled: true},
  {id: 3, isSelected: false, isEnabled: true},
]
*/
```

```jsx
//toggle the 4th level
let levels = ssmap.getLevels();

levels = levels.map(level = () => {
  level.selected = level.id === 4 ? !level.selected : level.selected;
  return level;
});

ssmap.setLevels(levels);
```

### Menus

```jsx
//add a menu
ssmap.createMenu({
  name: "My Menu",
  className: "myMenu", //for custom styling
  elements: [
    {
      type: "label",
      defaultValue: "This is my new menu",
    }, 
    {
      type: "checkbox",
      defaultValue: "checked",
      label: "Toggle Sky Color",
      className: "toggleSkyColor",
      onChange: function (event, mapbox) {	
        let skyColor = event.target.value === "checked" ? "blue" : "red";
        mapbox.setPaintProperty("sky-layer", "sky-gradient", ["interpolate", ["linear"], ["sky-radial-progress"], 0.8, skyColor, 1, "white"]);
      },
    }
  ]
});
```

# Events

### onPropertyLoad()

```jsx
ssmap.onPropertyLoad((property, mapbox) => {

})
```

### onPropertyClick()

```jsx
ssmap.onPropertyClick((property, mapbox) => {

})
```

### onUnitClick()

```jsx
ssmap.onUnitClick((unit, mapbox) => {

})
```

### onFilterToggle()

```jsx
ssmap.onFilterToggle((filter, mapbox) => {

})
```

### onLevelToggle()

```jsx
ssmap.onLevelToggle((filter, mapbox) => {

})
```

### Style API

```css
.ss-menuOptionContainer { 
    
}

.ss-menuOption { 
  
}

.ss-filterContainer {
  
}

.ss-checkbox {

}

.ss-rangeSlider {

}
```