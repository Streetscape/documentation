# Google Maps Connector - V2 Bravo Edition

## Quick Start

- We will provide you with a starter map page, from which you can generate a starter map export using the "Map Code" button.
- The map code this generates is a bare bones starter version of your map, which is intended to be integrated into your website template.
- This starter map should work out-of-box, with any filters that were initially requested (with only the addition of your Google Maps API key, mentioned below).
- You have the ability to customize styles and behaviours, however, we've made the export fully functional, so that you don't necessarily need to modify anything.

The map export will include the following top level directory structure:

```
index.html
- css
- fonts
- images
- js
```

### Configure your Map with your Google Maps API Key

Because our maps use the Google Maps JavaScript API, you will need to obtain a Google Maps API key from here:

https://developers.google.com/maps/documentation/javascript/get-api-key

Once you have your key, you will replace the sample line of code in the index.html file:

```html
<script src="https://maps.googleapis.com/maps/api/js?v=3" type="text/javascript"></script>
````

with

```html
<script src="https://maps.googleapis.com/maps/api/js?v=3&key=YOUR_API_KEY"></script>
```

## Important Settings

### Streetscape API Script Inclusion
Our API script is called through the following include:

```html
<script src="https://app.streetscape.ai/js/api/mapsplus.jquery.min.js" type="text/javascript"></script>
```

Our API script currently relies on jQuery and jQuery UI, so it's also imperative that you must retain the include to the jQuery earlier in the file as well.

```html
<script type="text/javascript" src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script type="text/javascript" src="https://code.jquery.com/ui/1.13.2/jquery-ui.min.js"></script>
```

## The mapConfig object

The mapConfig object is where the majority of the map customization is defined. Many of the settings in there are standard and should be left as is, however, the key properties of this object that make your map really unique are the following set:

```javascript
var mapConfig = {
  overlayType: 'tile', //
  mapTargetId: 'mapsplus', // This needs to correspond to the id of the element of where the map will display.
  mapKey: 'a1b2c3', // This is your Streetscape Phase API key. This currently corresponds the "primary" phase in the community. It is provided in your map export.
  additionalMapKeys: 'b1c2d3, c1d2e3, d1e2f3, ...', // OPTIONAL: Additional map keys - each key corresponds to an individual phase.
  customLayout: 'lot_info_window_outer', // OPTIONAL: Set to the id of a custom info window content container. The content of this container will be cloned into the dialog window. Example: everything inside <div id="lot_info_window_outer"> will be copied into the dialog window.
  map_options: {
		
    // We configure the starting position and zoom level for what we feel looks "good", but you have full control to adjust as necessary.
    zoom: 17, // Starting zoom level

    // Starting lat and lon positioning
    starting: { 
      'lat': 51.12762, 
      'lon': -112.4319 
    },

    // Positioning of standard Google Maps tools
    mapTypeControlOptions: {
      position: google.maps.ControlPosition.RIGHT_BOTTOM
    },       
    panControlOptions: {
      position: google.maps.ControlPosition.RIGHT_TOP
    },                
    zoomControlOptions: {
      position: google.maps.ControlPosition.RIGHT_TOP
    },
    streetViewControlOptions: {
      position: google.maps.ControlPosition.RIGHT_TOP
    },
    rotateControl: false
  },

  dialogOptions: { // This uses the jQuery UI dialog() options and events.
    width: '300', // OPTIONAL      
    modal: false,
    position: { 
      my: 'center top', 
      at: 'center top+87'
    },
    title: '' // The default is to dynamically populate with the civic address of the lot, or the legal lot description if there is no civic.
    
    open:function(event,ui) {}, // More detail in next section.
    close: function(event,ui) {}
  }
};
```

For more detail on jQuery UI's dialog widget, including more options availabe for its open and close methods, visit their documentation at https://api.jqueryui.com/dialog/.

## The Returned lotData Object

When a lot is clicked on the map, the data associated with that lot is returned via the lotData object. Accessing this object inside the open method of the jQuery dialog gives you a lot of control over what and how data is displayed in the info window.

```javascript
dialogOptions: {

  open:function(event,ui){
    
    var lotData = $(this).dialog('option','lotData'); // This is the lot data that is returned when a lot is clicked.
      
    // This is a great place to see the structure and contents of what's returned in the lotData object via the console in your browser's developer tools.
    console.log(lotData);
      
    // Fill the status indicator with the colour returned from the lotData
    $(this).find('.lot_status_color_block').css({'background-color':'#'+lotData.LotStatus.lot_statuses.map_color});
      
    // Configure dialog buttons
    $(this).dialog('option', 'buttons',[{
      text: 'More Information', // Whatever call to action you like.
      click: function(e) {
        // Example: A mailto configuration with lot details inserted into the subject line
        window.open('mailto:info@mycompany.com?subject=Inquiry Regarding: '+lotData.Community.name+' Phase '+lotData.Phase.name+' Block '+lotData.Block.name+' Lot '+lotData.Lot.name);
            
        // Example: A URL to a landing page on your website that accepts custom parameters
        window.open('https://www.mycompany.com?community='+lotData.Community.name+'&phase='+lotData.Phase.name+'&=lot '+lotData.Lot.name);
      }
    }]);
                              
    // Further customization of the title - if you want to customize the dynamic creation of the dialog title.
    $(this).dialog({title : 'Details about Lot ' + lotData.Lot.name + ' in Phase ' + lotData.Phase.name});              
  }
}
```
## Configuring Dialog Layout and Content

### Basic

The dialog info window template is configured to correspond to properties of the lotData object. For a quick implementation without using extra JavaScript, using the following naming convention will configure your dialog windown with the data you'd like to see.

```html
<!-- Info window template -->
<div id="lot_info_window_outer" style="display: none;">
  <div class="lot_information_inner">
    <div class="lot_data">
      
      <!-- LotStatus-lot_statuses-title maps to LotStatus.lot_statuses.title in the lotData object -->
      Status: <span class="LotStatus-lot_statuses-title"></span>
      
      <!-- This div's background colour is manipulated in the dialog's open method - see previous section -->
      <div class="lot_status_color_block" style="float: right; margin-left: 8px;"></div>
      
      <br />
    	
      <!-- CustomRecord-product_type maps to CustomRecord.product_type in the lotData object -->
      <div>Product Type: <span class="CustomRecord-product_type"></span></div>
    	
      <!-- CustomRecord-building_pocket maps to CustomRecord.building_pocket in the lotData object -->
      <div>Building Pocket: <span class="CustomRecord-building_pocket"></span></div>
      
      <!-- And so on... -->
      <div class="lot_legal_description">Legal: Lot <span class="Lot-name"></span> Block <span class="Block-name"></span></div>

    </div>
  </div>
</div>
```

### Advanced

If you want more control over the dialog window, you'll need to manipulate the DOM of the info window with JavaScript inside the open method of the jQuery UI dialog.

Let's say you used the above "Basic" implementation, but the lot didn't have any data for the Building Pocket. The basic implementation would present a blank value, which may look incomplete. You might want to catch empty or null values and display a replacement value instead.

```javascript
if (!lotData.CustomRecord.building_pocket){
  // Find that particular span in the info window and replace it with "N/A".
  $(this).find('.CustomRecord-building_pocket').text('N/A');
}	
```

Alternatiely, if the value were empty, maybe you don't want to display that field at all. In which case you can hide the parent div, effectively hiding the label.

```javascript
if (!lotData.CustomRecord.building_pocket) {
  $(this).find('.CustomRecord-building_pocket').parent('div').hide();
} else { // Remember that you'll want to make sure it is visible when there is data.
  $(this).find('.CustomRecord-building_pocket').parent('div').show();
}
```

## Configuring Map Filters

### Lot Statuses

When we provide the starter map code, we configure the set of lot statuses with what has been configured in the community on Streetscape, in combination with any customized requests by the community's developer. Each list item is structured with the following attributes:

- filter_field_name
- filter_value

The out-of-box behaviour of these items is to filter the lot polygons on the map by matching the value in filter_value with the name of the field, filter_field_name. Note that while the filter_value needs to match exactly, the label you display publicly can be however you'd like to label it.

```html
<ul id="map_legend" class="list-unstyled filter-group">
  <li class="map_legend_item lot_status_selector" filter_field_name="LotStatus.title" filter_value="For Sale">
    <div class="map_legend_color" style="background-color: #7ebd51;"></div>
    For Sale / Available <!-- This publicly visible label can be different from the filter_value. -->
  </li>
  
  <li class="map_legend_item lot_status_selector" filter_field_name="LotStatus.title" filter_value="Show Home">
    <div class="map_legend_color" style="background-color: #e5982a;"></div>
    Show Home
  </li>
  
  <li class="map_legend_item lot_status_selector" filter_field_name="LotStatus.title" filter_value="Sold">
    <div class="map_legend_color" style="background-color: #880707;"></div>
    Sold
  </li>
</ul>
```

### Data Filters

Filter sets can be established for any field found in the lotData object. A typical filter used is the Builder filter. It is ideal to use the ID of the builder. This can be accessed a number of ways:

- If there is a lot already assinged to the particular builder, you can determine the builder's ID by examining the lotObject in the console after the lot is clicked.
- If the builder is not yet assigned to any lots, the builder ID can be obtained from Streetscape, by the community developer, or by submitting a support ticket to us at support@streetscape.ai.
- In the interum, you could match against the Builder's name (see example below). Note that this is less ideal as builder names can potentially change slightly from time to time.

```html
<h3>Assigned Builders</h3>
<ul class="list-unstyled filter-group">
  <li class="builder_selector" filter_field_name="Builder.id" filter_value="1234">
    <i class="fa fa-square-o"></i>Frank's Custom Homes
  </li>

  <li class="builder_selector" filter_field_name="Builder.id" filter_value="5678">
    <i class="fa fa-square-o"></i>Jack and Jill Homes
  </li>
  
  <!-- If you're stuck for a Builder ID, you could theoretically match against the builder name, so long as it exactly matches their name in Streetscape. -->
  <li class="builder_selector" filter_field_name="Builder.name" filter_value="Sky High Builders">
    <i class="fa fa-square-o"></i>Sky High Builders
  </li>
</ul>
```

Another example filtering on Product Type:

```html
<h3>Product Type</h3>
<ul class="list-unstyled filter-group">
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Front Drive Single Family">
    <i class="fa fa-square-o"></i>Front Drive Single Family
  </li>
  
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Front Drive Duplex">
    <i class="fa fa-square-o"></i>Front Drive Duplex
  </li>
  
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Rear Lane Single Family">
    <i class="fa fa-square-o"></i>Rear Lane Single Family
  </li>
  
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Rear Lane Single Family Zero Lot Line">
    <i class="fa fa-square-o"></i>Rear Lane Single Family Zero Lot Line
  </li>

  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Rear Lane Single Family Double Garage">
    <i class="fa fa-square-o"></i>Rear Lane Single Family Double Garage
  </li>
</ul>
```

#### Changing a Filter's Label

Our starter code will contain filter data based on what had been provided to us, or from actual data that had already been imported into Streetscape. Let's say you've got the following filter set up:

```html
<h3>Product Type</h3>
<ul class="list-unstyled filter-group">
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Front Drive Single Family">
    <i class="fa fa-square-o"></i>Front Drive Single Family
  </li>
</ul>
````

If you wanted to change the label only, without having to change the underlying data on Streetscape, as long as the "value" of the list item matches the data on Streetscape, the filter will work. So, for example, you could adjust the label as such:

```html
<h3>Product Type</h3>
<ul class="list-unstyled filter-group">
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Front Drive Single Family">
    <i class="fa fa-square-o"></i>SF - Front Drive
  </li>
</ul>
````


### Additional Values Attribute

Let's say in the above example, you wanted to group together multiple values into one filter selection. For example, assume you wanted to group the last three Rear Lane options into one filter item labeled "Rear Lane Single Family". You can add an "additional_values" attribute to the primary element with the value being a JSON array of the extra values.

```html
<h3>Product Type</h3>
<ul class="list-unstyled filter-group">
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Front Drive Single Family">
    <i class="fa fa-square-o"></i>Front Drive Single Family
  </li>
  
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Front Drive Duplex">
    <i class="fa fa-square-o"></i>Front Drive Duplex
  </li>
  
  <li class="product_type_selector" filter_field_name="CustomRecord.product_type" filter_value="Rear Lane Single Family" additional_values='["Rear Lane Single Family ZLL", "Rear Lane Single Family Zero Lot Line", "Rear Lane Single Family Double Garage"]'>
    <i class="fa fa-square-o"></i>Rear Lane Single Family
  </li>
</ul>
```

### Adding a New Set of Filters

If at some point you need to add a new filter set, you can do that by using exactly the same format as the previous examples. One extra piece of code needs to also be included so that the filter set works as expected.

In the "Event Listeners" portion of your starter code, you want to reference the new filter set by the class name you give each list item. For example, if you added a Building Pocket filer set, following the existing convention, your code would now looks like this:

```javascript
// START page event listeners.

$('.lot_status_selector').on('click',function(event){
  selector($(this));
});

$('.product_type_selector').on('click',function(event){
  selector($(this));
});

// If you had just added the Building Pocket filter group, you would need to add the following.
$('.building_pocket_selector').on('click',function(event){
  selector($(this));
});
```

## Conclusion
This is an ever changing document. If you have further questions, or would like more or different examples, please send us an email at support@streetscape.ai and we'll do our best to offer our guidance as quickly as possible.