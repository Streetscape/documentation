# Google Maps Connector V1.0

# Quick Start

When you receive your integration kit, there a few things you'll need to take care in order to get the map working.

## Configure your google maps client

```jsx
<script src="https://maps.googleapis.com/maps/api/js?client=GOOGLE_MAPS_CLIENT"></script>
```

In order to load the map, you first need to set your own *google maps api key* where the google maps plugin is loaded.

[https://developers.google.com/maps/documentation/javascript/get-api-key](https://developers.google.com/maps/documentation/javascript/get-api-key) 

NOTE: DO NOT use the `defer` attribute when loading google maps script. 

## Configure Map Filters

Find the div with `id="filter-groups"`

In this section you will find two type of filters.

1. Legend Filters `id="map_legend"`

    In addition to hiding and showing matches of the selected filter(s), Legend Filters also change the colors of the units that are shown and show the color of each unit in the legend.

    ```jsx
    <ul id="map_legend" class="list-unstyled filter-group">
      <li
        class="map_legend_item lot_status_selector"
        filter_field_name="LotStatus.title"
        filter_value="For Sale"
      >
        <div class="map_legend_color" style="background-color: #3b7114"></div>
        For Sale
      </li>
      <li
        class="map_legend_item lot_status_selector"
        filter_field_name="LotStatus.title"
        filter_value="Sold"
      >
        <div class="map_legend_color" style="background-color: #0455a9"></div>
        Sold
      </li>
    </ul>
    ```

2. Custom Filters

    Custom filters are just filters that do not effect the color of the units available for selection.

    ```jsx
    <h3>Builder</h3>
    <ul class="list-unstyled filter-group">
      <li filter_field_name="Builder.id" filter_value="1243">
        <i class="fa fa-square-o"></i>Superior Builders
      </li>
      <li filter_field_name="Builder.id" filter_value="1517">
        <i class="fa fa-square-o"></i>Oreo Homebuilders
      </li>
    </ul>
    ```

All filters will filter out units from that map that do not match the filters enabled by the user. Eg: If "For Sale" is selected, only units with the status of "For Sale" will be shown on the map and all other units will vanish. If the user also selects the "Sold" status filter, all units with the status of "Sold" will also appear and be clickable.

## Defining Filter Parameters

New filters can be added to any `ul.filter-group` by adding a new `<li></li>`as a child. Follow the pattern as outlined above. The `filter_field_name` and `filter_value` attribute must be set to the field name and value you want to filter. If you would like to see a complete list of fields that can be filtered, open the dev tools in your browser and select the network tab before clicking on a unit on your map. You should see the response payload returned from the streetscape API which contains all field names and values for the unit clicked.  

## Configure Popup on Unit Click

Find the div with `id="lot_info_window_outer"`. Here you can define which fields will be shown and where to output the values for the unit. In order to display a field, create a `<span>` with a class named according to the fields you wish to display. 

Here's a example of an info window that displays the status, builder, and legal description.

```jsx
<div class="lot_data">
  Status: <span class="LotStatus-lot_statuses-title"></span>
  <div class="lot_status_color_block"></div>
  <div>Builder: <span class="Builder-name"></span></div>
  <div>
    Legal: Lot <span class="Lot-name"></span> Block <span class="Block-name"></span>
  </div>
</div>
```

If you would like to see a complete list of fields that can be displayed, open the dev tools in your browser and select the network tab before clicking on a unit on your map. You should see the response payload returned from the streetscape API which contains all field names and values for the unit clicked. Any of these values can be added to the info window.