# Matter Kit CSS
**A Sass based CSS framework for faster project scaffolding.**

The matter-kit-css framework is a silent framework composed of functions, mixins and variable declarations. This framework aims to be flexible and extendable, with sensible defaults for projects. There are several functions for altering our main global configuration variables within a projects local scope, rather than having to change the core matter-kit-css files. This allows both the configuration files and the core css files to be copied freely between projects.

## How to Use the Framework
The framework is designed to be imported before any project-specific configuration settings. Ideally, these configuration settings would be separated into their own partials to keep the import files tidy. The order of dependencies is quite specific, so we would recommend using the setup below. 

```SCSS
// Import the Matter Kit CSS
@import '../../../../../bower_components/matter-kit-css/matter-kit';


// General Config files
@import '../config/config.colors';
@import '../config/config.typography';
@import '../config/config.grid';
@import '../config/config.misc';


// Reset
@import '../../../../../bower_components/matter-kit-css/matter-kit-css-reset';


// Object Config
@import '../config/config.objects';


// Objects
@import '../../../../../bower_components/matter-kit-css/matter-kit-css-objects';


// Import project elements/components here


// Utility Config
@import '../config/config.utility';


// Utilities
@import '../../../../../bower_components/matter-kit-css/matter-kit-css-utility';


// Output grid classes
@include mk-generate-grid-widths();

```
## Project Configuration
Create your configuration partials, or add sections above the @import for the matter-kit-css framework. Our recommended configuration partials are:
- config/_config.colors.scss
- config/_config.grid.scss
- config/_config.typography.scss
- config/_config.objects.scss
- config/_config.utility.scss
- config/_config.misc.scss

### Defining Project Colours
Colours are designed to be used project wide. Colours for specific use cases, such as elements have their own definitions. Colour configurations for the project should be defined in the /config/_config.grid.scss partial.

#### Adding a colour
```
// Add a colour
@include mk-add-color( primary, base, #ae6f35 );
```

This adds the colour to a global colours array and we have functions to retrieve colours easily. 

#### Adding variations of a colour
```
// Setting our primary 'base' colour
@include mk-add-color( primary, base, #ae6f35 );

// Setting our variations of that colour
@include mk-add-color( primary, dark, darken( mk-color( primary, base ), 20 ) );
@include mk-add-color( primary, light, darken( mk-color( primary, base ), 20 ) );
```

#### Retrieving a colour
To retrieve a colour, we use the mk-color() function. This will return the value of the colour we requested, or null if the colour doesn’t exist. By default, this returns the 'base' shade of the colour.
```
// Retrieve a colour
color:  mk-color( primary );
```

#### Retrieving variations of a colour
We use the same mk-color() function, but this time pass a second argument to retrieve a different shade. This will return null if the requested colour and shade doesn’t exist.
```
// Retrieve a specific shade of a colour
color:  mk-color( primary, dark );
```

### Defining Grid Settings
We have a global array used to store our grid settings. This array has several defaults to ensure that the framework will still function correctly without changes. Grid settings for the project should be defined in the /config/_config.grid.scss partial.

#### Default Settings
```
// Our default grid settings
$mk-global-grid: (
	base: (
		columns:                     12,
		gutter:                      20px,
		page-gutter:                 20px,
		wrap-width:                  1170px,
	)
);
```

Our core grid declarations are made within the 'base' part of the global grid map. By default, we have used a standard 12 column grid, with 20px gutters between. These are settings we’ve found to be common in how we work.

#### Changing default settings
```
// Set the number of columns to 16
@include mk-add-grid-config( base, columns, 16 );
```

This will override the base setting of 12 columns and change it to work on 16 columns.

#### Adding Major Breakpoints
Breakpoints are also added to our global grid variable. Breakpoints should be added in a mobile first approach and must be given aliases.
```
// Adding a major breakpoint
@include mk-add-grid-config( breakpoints, palm, null ); // Mobile first
@include mk-add-grid-config( breakpoints, lap, 768px ); // Our first breakpoint
@include mk-add-grid-config( breakpoints, desk, 990px ); // Another breakpoint
```

Our first breakpoint definition is set to null because this will be all sizes from 1px up until our next breakpoint size. You can declare as many breakpoints as you wish for your project. We try to keep this limited to ensure consistency. 

#### Setting Grid Widths
We have an in-built mixin for outputting helper classes for grid widths. Once the grid-widths have been set up, the classes will be generated using the syntax .g-[width]@[breakpoint].

Our initial, or 'mobile' breakpoint should be called 'base'. 
```
// Setting grid width
@include mk-add-grid-width( lap, one-half, 50% ); // .g-one-half@lap
@include mk-add-grid-width( lap, one-third, 33.333% ); // .g-one-third@lap
@include mk-add-grid-width( lap, two-thirds, 66.666% ); // .g-two-thirds@lap
```

At the end of your project import file, include the mixin `mk-generate-grid-widths()`;

### Defining Typography Settings
We have a global array used to store our typography settings. This array has several defaults to ensure that the framework will still function correctly without changes. Typography settings for the project should be defined in the /config/_config.typography.scss partial.

```
Default Typography Settings
// Setting grid width
$mk-global-typography:                (
	base: (
		type-scale:                   1.25
		line-height:                  26px,
		line-length:                  55rem,	
	),
	size: (
		base:                         16px,
	),
	face: (
		primary:                      "'Open Sans', Verdana, sans-serif",
	)
) !global;
```

#### Setting the default font size
A default font size of 16px is defined in our global settings. To change this, we use the mk-add-typography mixin. Sizes should always be set in pixels here, because we can use this size to calculate em or rem sizing with other functions.

```
// Set the base font size to 18px
@include mk-add-typography( size, base, 18px );
```

#### Setting the base line-height
Similar to setting the base font size, we use the same function to add our base line-height.
```
// Set the base line-height to 32px
@include mk-add-typography( base, line-height, 32px );
```

Whenever the base line height is changed, a ‘base spacing unit’ global map is updated to reference the new sizing.

#### Adding Typefaces
By default, we have a primary typeface defined. To update this, we call the mk-add-typography mixin again and define our font stack. To add new typefaces, we simply change the second argument to add secondary or tertiary typefaces. These can be named to whatever makes sense for your project.

```
// Set the primary typeface to ‘Proxima Nova’
@include mk-add-typography( face, primary, “‘Proxima Nova’, Arial, sans-serif” );
```

## Functions & Mixins

### General
##### mk-add-key-to-map( $map, $key, $value )
Used for merging maps together, this is mostly a wrapper for map-merge. This is a core function and is used throughout several other functions. 
- `$map`
The original map you want to add the key/value to
- `$key`
The key of the new map you are merging into $map
- `$value`
The value of the new map you are merging into $map


##### mk-add-data( $map, $outer_key, $inner_key, $value )
Add data to an existing map - this expects a map with 2 levels of nested maps. This is used by many of our configuration setter functions to add information to global maps. 
- `$map`
The map to add data to
- `$outer_key`
The outer key for the map - this is defined by the user, but some global maps have a default structure set in the global variables file.
- `$inner_key`
The inner key for the map. This becomes a ‘child’ of the outer key.
- `$value`
The value of our inner key.

The function checks to see if the outer key already exists and updates it if it finds anything. If the outer key doesn’t exist, a new one will be added.

##### pow( $number, $exp )
This function is used for our typography scale and was written by Daniel Perez Alvarez who posted it in his blog: https://unindented.org/articles/trigonometry-in-sass/

##### strip-unit( $number )
Sometimes we’ve found we need to remove the unit from a value for calculation purposes. Most of what we calculate is based on pixel values, which are then converted to rem, or em. This function will simply return the value supplied without it’s unit.
```
// Strip a unit of it’s number
$var: strip-unit( 30px );
```

### Typography

##### calc-rem( $px-size, $rem )
Turn a pixel value to a rem size. This pulls through the configured base font size by default.
```
// Calculate a rem size based on a px value
.selector {
    padding-bottom: calc-rem( 20px );
}
```

##### calc-em( $px-size, $context-px-size )
Turn a pixel value to a em size. This pulls through the configured base font size by default for context. The contextual size should be defined every time the function is called so the calculation will be accurate.
```
// Calculate a em size based on a px value
.selector {
    padding-bottom: calc-em( 20px, 16px );
}
```

### Grid & Media Queries
##### col( $span, $columns )
Output a width for spanning columns within a grid. By default, this uses the global column amount, however it can be set individually to make a grid within a grid.

```
// Span columns using our defined global grid column count
.selector {
    width: col( 5 );
}

// Span columns using a local grid column count
.selector {
    $local-grid-columns: 16;
    width: col( 2, $local-grid-columns );
}
```

##### mk-media-query( $from, $until )
Include a media query that we’ve defined in our configuration for our project. We encourage media query bubbling within your sass components to keep everything organised and easy to find.

By default this function works mobile first, but we can use it to work desktop down and sizes between breakpoints.

##### Mobile first usage
This will output a min-width media query.

```
// Include a media query using our configured breakpoint sizes
.selector {
    @include mk-media-query( desk ) {
        // Styles
    }
}

// Include a media query using a specific size
.selector {
    @include mk-media-query( 500px ) {
        // Styles
    }
}
```

##### 'Desktop down' usage
This will output a max-width media query.

```
// Include a media query using our configured breakpoint sizes
.selector {
    @include mk-media-query( null, desk ) {
        // Styles
    }
}

// Include a media query using a specific size
.selector {
    @include mk-media-query( null, 500px ) {
        // Styles
    }
}
```

##### Min and max width
This will output a media query with both min-width and max-width values.

```
// Include a media query using our configured breakpoint sizes
.selector {
    @include mk-media-query( lap, desk ) {
        // Styles
    }
}

// Include a media query using a specific size
.selector {
    @include mk-media-query( 300px, 500px ) {
        // Styles
    }
}
```

##### mk-generate-grid-widths()
After the grid-widths have been set up, we can use this mixin to generate a list of width helper classes. It’s recommended to call this function at the bottom of your main project import file.

The classes will be generated using the syntax .g-[width]@[breakpoint].

This mixin does have optional values, pulling global defaults, so you are able to use this function to generate other grids from a separately defined map. You can also change the prefix of .g- and change it to a placeholder.

### Base Spacing Unit

**What is a base spacing unit?**

It’s close to what designers call 'vertical rhythm'. A consistent size is used to calculate padding and margins between elements. This unit is tied directly to the line height, so the entire interface will scale based on type size changes.

The base spacing unit sizes are defined automatically based on your line height. When you update your line height using the mk-add-typography mixin, a function is called to update the base spacing unit global settings to ensure it always remains up-to-date.

#### Using a base spacing unit
There are two ways we can call a base spacing unit. The first way is with our named sizes.

##### Named Sizes
```
// Set the padding to be the ‘large’ base spacing unit size
.selector {
    padding: mk-base-spacing-unit( large );
}
```

There are eight named sizes available for us to use, which are multiples or divisions of our base spacing unit. These sizes should be our main preference for use within a project.

- nano - ⅛ a base spacing unit
- tiny - ¼ a base spacing unit
- small - ½ a base spacing unit
- base - 1 base spacing unit
- large - 2 base spacing units
- huge - 4 base spacing units
- giga - 8 base spacing units

##### Calculated sizes
For some specific use cases, we may need 3 base spacing units, rather than 2 or 4. For these cases, you can pass a number to the function and it will return the base spacing unit multiplied by the number supplied.
```
// Set the padding to be 3 x the base spacing unit size
.selector {
    padding: mk-base-spacing-unit( 3 );
}
```
##### Returning Pixel Sizes
By default, the mk-base-spacing-unit function returns rem values. If you want to return pixel values for calculation purposes, set a second argument to 'false'.
```
// Set the padding to be 3 x the base spacing unit size, returned in px 
$var: mk-base-spacing-unit( 3, false );
```

### Objects
We have a few css objects we use on every project and have bundled these into an installable module.

##### Installing objects
Install the matter-kit-objects file to your bower components and import the main sass file.

```
// Import the core objects
@import '../../../../../bower_components/matter-kit-css-objects-core/matter-kit-css-objects-core';
```

##### Adding objects to a project
To enable a layout object option in your project, you can configure them in the `_config.objects.scss` partial. 
```
// Include the layout object and it’s 'base' form 
@include mk-add-object( 'lyt', 'base' );
```

##### Layout
The layout module is used for laying items out. This is designed to work with the grid system we have configured in our base framework and our grid widths. The layout object works using display: inline-block; so be wary of whitespace between items. Using inline-block rather than floats allows us to use basic vertical alignment for grid items. 

**Default name:** lyt

The default name can be changed by setting the `$mk-obj-layout-name` variable before importing the main object import file.

The 'o-lyt' layout class is designed to be used for layout purposes and is tied to built-in grid system. By default, layout items fill the space. Using our grid-width classes, we can set these to change size across our major responsive breakpoints.

```
<ul class="o-lyt">
    <li class="o-lyt__item  g-one-third@desk">First column</li>
    <li class="o-lyt__item  g-one-third@desk">Second column</li>
    <li class="o-lyt__item  g-one-third@desk">Third column</li>
</ul>
```

There are also several modifiers to adjust the default layout. To enable these, we need to invoke the base layout object:

```
// Include the base layout object
@include mk-add-object( 'lyt', 'base' );
```

**.o-lyt--bordered** - sets the gutter between layout items to the width of our global borders
```
@include mk-add-object( 'lyt', 'bordered' );
```
**.o-lyt--nano** - sets the gutter between layout items to 1/8 of the standard gutter
```
@include mk-add-object( 'lyt', 'nano' );
```
**.o-lyt--tiny** - sets the gutter between layout items to 1/4 of the standard gutter
```
@include mk-add-object( 'lyt', 'tiny' );
```
**.o-lyt--small** - sets the gutter between layout items to 1/2 of the standard gutter
```
@include mk-add-object( 'lyt', 'small' );
```
**.o-lyt--large** - sets the gutter between layout items to 2 * the standard gutter
```
@include mk-add-object( 'lyt', 'large' );
```
**.o-lyt--huge** - sets the gutter between layout items to 4 * the standard gutter
```
@include mk-add-object( 'lyt', 'huge' );
```
**.o-lyt--giga** - sets the gutter between layout items to 8 * the standard gutter
```
@include mk-add-object( 'lyt', 'giga' );
```
**.o-lyt--flush** - removes the gutter between layout items
```
@include mk-add-object( 'lyt', 'flush' );
```
**.o-lyt--auto** - removes the 100% width on the items - layout items will be as wide as the content inside them
```
@include mk-add-object( 'lyt', 'auto' );
```
**.o-lyt--middle** - vertically align the layout items to the middle
```
@include mk-add-object( 'lyt', 'middle' );
```
**.o-lyt--bottom** - vertically align the layout items to the bottom
```
@include mk-add-object( 'lyt', 'bottom' );
```
**.o-lyt--baseline** - vertically align the layout items to the baseline
```
@include mk-add-object( 'lyt', 'baseline' );
```
**.o-lyt--left** - horizontally align the layout items to the left
```
@include mk-add-object( 'lyt', 'left' );
```
**.o-lyt--right** - horizontally align the layout items to the right
```
@include mk-add-object( 'lyt', 'right' );
```
**.o-lyt--center** - horizontally align the layout items to the center
```
@include mk-add-object( 'lyt', 'center' );
```
**.o-lyt--split** - horizontally center the items, until a defined breakpoint, then left align the first column and right align the last column
```
@include mk-add-object( 'lyt', 'split' );
```
**.o-lyt--reverse** - swap the order of the items using direction: rtl;
```
@include mk-add-object( 'lyt', 'reverse' );
```
**.o-lyt--alternate** - swap the direction of nested lyt objects to alternate left/right.
```
@include mk-add-object( 'lyt', 'alternate' );
```
**.o-lyt--media** - add extra padding to the image columns to create further separation between the text and image.
```
@include mk-add-object( 'lyt', 'media' );
```






Mixins for the layout object

```
mk-create-layout( $size: null, $vertical-align: null, $horizontal-align: null )
```

This is designed as an internal mixin, but it can be used outside to create further layout modifiers. The size adds padding and margins to create 'gutters' between the layout item elements. Vertical and horizontal alignment are both mostly set in the base already. Most layout object options also use this mixin.

## Major Contributors
- Hannah Malcolm
- Nathan Crowe
- Matt Sinagra
- Cameron Jones
- Svanhild Egge
- Rowan Lines
- Ben Maden

Copyright 2016 Matter Solutions.