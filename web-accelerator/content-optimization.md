## Content Optimization

FIT can automatically perform minification and other optimizations on your content, such as [resolving responsive images](https://developer.sevenval.com/docs/current/web-accelerator/Responsive_Image_Filtering.html), and [minification of HTML](https://developer.sevenval.com/docs/current/web-accelerator/HTMLMinifying.html), [SVG](https://developer.sevenval.com/docs/current/web-accelerator/SVGMinifying.html), and [JavaScript and CSS code](https://developer.sevenval.com/docs/current/web-accelerator/JsCssMinifying.html): This can reduce the number of transferred bytes significantly, even when gzip compression is used.


### CSS Minification

CSS minification is enabled in `config.xml` with the `style-minifying` element:

```xml
<config>
  <acceleration>
    <style-minifying strip-prefixes="true"/>
  </acceleration>
</config>
```

FIT has an advantage over simple static minification: it can remove CSS not applicable to the requesting client, such as vendor prefixes for other browsers, and unused media queries.

#### Vendor Prefix Removal

With the `strip-prefixes` option set to `true`, FIT will remove all CSS properties from the stylesheets that make use of vendor prefixes (e.g. `-webkit-` or `-ms-`) which do not match the `css/prefix` [property](https://developer.sevenval.com/docs/current/ress/DC_Props.html) of the requesting client.

FIT will remove unused prefixes from anywhere in the CSS, whether the prefix is part of a selector or a instruction. Additionally, if removal of a seclector or instuction results in no remaining instructions or selectors for a block, then the whole CSS block is removed. 

We can see the effect on our original prefixed CSS. The original looks like

```CSS
  body.is-loading *, body.is-loading *:before, body.is-loading *:after {
    -moz-animation: none !important;
    -webkit-animation: none !important;
    -ms-animation: none !important;
    animation: none !important;
    -moz-transition: none !important;
    -webkit-transition: none !important;
    -ms-transition: none !important;
    transition: none !important;
  }

  a {
    -moz-transition: color 0.2s ease-in-out, border-color 0.2s ease-in-out;
    -webkit-transition: color 0.2s ease-in-out, border-color 0.2s ease-in-out;
    -ms-transition: color 0.2s ease-in-out, border-color 0.2s ease-in-out;
    transition: color 0.2s ease-in-out, border-color 0.2s ease-in-out;
    border-bottom: dotted 1px;
    color: #49bf9d;
    text-decoration: none;
  }
```

Below we can see all prefixes not applicable to our requesting browser (Chrome) have been removed:

```CSS
CSS
```

### Filter Media Queries
To filter unused media queries, include `<filter-media-queries />` in your `config.xml`:

```xml
<config>
  <acceleration>
    <filter-media-queries />
  </acceleration>
</config>
```

Note that media queries that *might* match will not be filtered. Width values are only filtered if the client does not support [viewport resizing](/docs/current/ress/DC_Props.html#viewport_resizeable) and the necessary viewport dimensions are available. Orientation values are only filtered if the client cannot [change the viewport orientation](/docs/current/ress/DC_Props.html#orientation_switchable) and does not support viewport resizing.

In our example site, the media queries below have been removed:

```CSS

```

Leaving only the following remaining:

```CSS
```

### Script Minification

Script minification is enabled by including the `<script-minifying />` element in your `config.xml`:

```xml
<config>
  <acceleration>
    <script-minifying />
  </acceleration>
</config>
```

In our original page, we have a number of JavaScript files. The following excerpt comes from the beginning of `util.js`:

```JavaScript
(function($) {

  /**
   * Generate an indented list of links from a nav. Meant for use with panel().
   * @return {jQuery} jQuery object.
   */
  $.fn.navList = function() {

    var $this = $(this);
      $a = $this.find('a'),
      b = [];

    $a.each(function() {

      var $this = $(this),
        indent = Math.max(0, $this.parents('li').length - 1),
        href = $this.attr('href'),
        target = $this.attr('target');

      b.push(
        '<a ' +
          'class="link depth-' + indent + '"' +
          ( (typeof target !== 'undefined' && target != '') ? ' target="' + target + '"' : '') +
          ( (typeof href !== 'undefined' && href != '') ? ' href="' + href + '"' : '') +
        '>' +
          '<span class="indent-' + indent + '"></span>' +
          $this.text() +
        '</a>'
      );

    });

    return b.join('');

  };
```

After FIT optimization, we can see this excerpt has been minified. Note the lack of white space, and comments:

```JavaScript
(function($){$.fn.navList=function(){var  $this=$(this);$a=$this.find('a'),b=[];$a.each(function(){var  $this=$(this),indent=Math.max(0,$this.parents('li').length-1),href=$this.attr('href'),target=$this.attr('target');b.push('<a '+'class="link depth-'+indent+'"'+
((typeof target!=='undefined'&&target!='')?' target="'+target+'"':'')+
((typeof href!=='undefined'&&href!='')?' href="'+href+'"':'')+'>'+'<span class="indent-'+indent+'"></span>'+
$this.text()+'</a>');});return b.join('');};
```

### Markup Optimization

FIT can also perform various markup-based optimizations, such as HTML minification, SVG minification, and responsive image filtering.

#### HTML Minification

HTML minification is enabled by adding the `<html-minifying />` element to your `config.xml` file:

```xml
<config>
  <acceleration>
    <html-minifying />
  </acceleration>
</config>
```

An excerpt from the body of the original HTML is shown:

```html
  <body id="top">

    <!-- Header -->
      <header id="header">
        <a href="#" class="image avatar"><img src="images/avatar.svg" alt="" /></a>
        <h1><strong>Welcome to Cologne</strong><br /> a beautiful 2,000-year-old city<br /> in western Germany</h1>
      </header>

    <!-- Main -->
      <div id="main">

        <!-- One -->
          <section id="one">
            <header class="major">
              <h2>Ipsum lorem dolor aliquam ante commodo<br />
              magna sed accumsan arcu neque.</h2>
            </header>
            <p>Accumsan orci faucibus id eu lorem semper. Eu ac iaculis ac nunc nisi lorem vulputate lorem neque cubilia ac in adipiscing in curae lobortis tortor primis integer massa adipiscing id nisi accumsan pellentesque commodo blandit enim arcu non at amet id arcu magna. Accumsan orci faucibus id eu lorem semper nunc nisi lorem vulputate lorem neque cubilia.</p>
            <ul class="actions">
              <li><a href="#" class="button">Learn More</a></li>
            </ul>
          </section>
```

Compare this with the corresponding minified excerpt:

```xml
<body id=top>  <header id=header><a href=# class="image avatar"><img src="/;pass/images/avatar.svg" alt=""></a> <h1><strong>Welcome to Cologne</strong><br> a beautiful 2,000-year-old city<br> in western Germany</h1> </header><div id=main>  <section id=one><header class=major><h2>Ipsum lorem dolor aliquam ante commodo<br> magna sed accumsan arcu neque.</h2> </header><p>Accumsan orci faucibus id eu lorem semper. Eu ac iaculis ac nunc nisi lorem vulputate lorem neque cubilia ac in adipiscing in curae lobortis tortor primis integer massa adipiscing id nisi accumsan pellentesque commodo blandit enim arcu non at amet id arcu magna. Accumsan orci faucibus id eu lorem semper nunc nisi lorem vulputate lorem neque cubilia.</p> <ul class=actions><li><a href=# class=button>Learn More</a></li> </ul></section>
```

Quotes, comments and whitespace have all been removed where possible. This has resulted in a filesize reduction from 24KB to 7KB.

Some further configuration options are possible for HTML minification, such as retaining comments; see the [HTML minifying documentation](https://developer.sevenval.com/docs/current/web-accelerator/HTMLMinifying.html) for more information.

#### SVG minification

Likewise, since SVG images are markup based, they can also be minified. To enable SVG minification, `image-scaling` must also be enabled. So, you must add the `<svg-minifying/>` element **as well as** the `<image-scaling />` element to your `config.xml`:

```xml
<config>
  <acceleration>
    <svg-minifying />
  </acceleration>
  <ress>
    <image-scaling />
  </ress>
</config>
```

In our example site, the Cologne coat of arms image is an SVG, and its markup looks like this:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>

<!-- https://commons.wikimedia.org/wiki/File:Wappen_Koeln.svg -->

<svg id="Coat_of_arms_of_the_city_of_Cologne_(Germany)"
     xmlns="http://www.w3.org/2000/svg"
     xml:space="preserve"
     height="579.01"
     width="482"
     version="1.0"
     xmlns:xlink="http://www.w3.org/1999/xlink"
     viewBox="0 0 482 579.00635">

  <rect id="upper_red_area" height="198.76" width="472" y="5" x="5" fill="#f0000b"/>
  <path id="lower_white_area" fill="#fff" d="m477 338.01c0 130.34-105.66 236-236 236s-236-105.66-236-236v-134.34h472v134.34z"/>
  <g id="crown" fill="#ffe600">
    <path d="m270.92 107.79c0 0.81-0.66 1.46-1.46 1.46h-8.58c-0.81 0-1.46-0.65-1.46-1.46 0-0.8 0.65-1.46 1.46-1.46h8.58c0.8 0 1.46 0.66 1.46 1.46z"/>
    <circle cy="100.88" cx="265.67" r="3.417"/>
    <path d="m293.04 90.542l1.75-37.875s-9.54 5.5-9.54 10.5 1.63 4.75 1.63 8.125-1.13 3.875-2.5 3.875c-1.38 0-5-3.25-7.13-3.25s-5.63 2.875-5.63 6.375 2.5 8.25 2.5 8.25 3.76-4.375 6.26-4.375 5.87 2.625 4.12 8.375h8.54z"/>
    <path d="m293.94 94.292c0 0.806-0.66 1.459-1.46 1.459h-9.83c-0.81 0-1.46-0.653-1.46-1.459 0-0.805 0.65-1.458 1.46-1.458h9.83c0.8 0 1.46 0.653 1.46 1.458z"/>
    <path d="m241 90.501h-5c2-4.5 0.17-8.5-5.17-8.5-5.33 0-7 5.166-7 5.166s-5.16-5.666-5.16-10.333 3.66-7 7-7c3.33 0 6 2.667 7.66 3.667 1.21-3.25 3.63-4.084 3.63-4.084s-5.63-4.916-5.63-8.25c0-3.333 3.15-4.856 5.34-6.833 3.02-2.729 4.33-6.812 4.33-6.812s1.31 4.083 4.33 6.812c2.19 1.977 5.34 3.5 5.34 6.833 0 3.334-5.63 8.25-5.63 8.25s2.42 0.834 3.63 4.084c1.66-1 4.33-3.667 7.66-3.667 3.34 0 7 2.333 7 7s-5.16 10.333-5.16 10.333-1.67-5.166-7-5.166c-5.34 0-7.17 4-5.17 8.5h-5z"/>
    <path d="m234 96.417c-0.97 0-1.75-0.783-1.75-1.75 0-0.966 0.78-1.75 1.75-1.75h14c0.97 0 1.75 0.784 1.75 1.75 0 0.967-0.78 1.75-1.75 1.75h-14z"/>
    <path d="m193.67 131.08c0-6.75-3.84-32.746-3.84-32.746h9.34s3.91 2.836 3.91 7.666-4.16 6.42-4.16 12.08c0 5.67 4.91 8.09 8.16 8.09s8.25-1.75 8.25-7.25-1.5-6.75-1.5-6.75h6.34s-1.5 1.25-1.5 6.75 5 7.25 8.25 7.25 8.16-2.42 8.16-8.09c0-5.66-4.16-7.25-4.16-12.08s3.91-6.916 3.91-6.916h12.34s3.91 2.086 3.91 6.916-4.16 6.42-4.16 12.08c0 5.67 4.91 8.09 8.16 8.09s8.25-1.75 8.25-7.25-1.5-6.75-1.5-6.75h6.34s-1.5 1.25-1.5 6.75 5 7.25 8.25 7.25 8.16-2.42 8.16-8.09c0-5.66-4.16-7.25-4.16-12.08s3.91-7.666 3.91-7.666h9.34s-3.84 25.996-3.84 32.746h-94.66z"/>
    <path d="m211.08 107.79c0 0.81 0.66 1.46 1.46 1.46h8.58c0.81 0 1.46-0.65 1.46-1.46 0-0.8-0.65-1.46-1.46-1.46h-8.58c-0.8 0-1.46 0.66-1.46 1.46z"/>
    <circle cy="100.88" cx="216.33" r="3.417"/>
    <path d="m188.96 90.542l-1.75-37.875s9.54 5.5 9.54 10.5-1.63 4.75-1.63 8.125 1.13 3.875 2.5 3.875c1.38 0 5-3.25 7.13-3.25s5.63 2.875 5.63 6.375-2.5 8.25-2.5 8.25-3.76-4.375-6.26-4.375-5.87 2.625-4.12 8.375h-8.54z"/>
    <path d="m188.06 94.292c0 0.806 0.66 1.459 1.46 1.459h9.83c0.81 0 1.46-0.653 1.46-1.459 0-0.805-0.65-1.458-1.46-1.458h-9.83c-0.8 0-1.46 0.653-1.46 1.458z"/>
    <path d="m193.92 138.33c-1.25 0-2.25-1-2.25-2.25 0-1.24 1-2.25 2.25-2.25h94.16c1.25 0 2.25 1.01 2.25 2.25 0 1.25-1 2.25-2.25 2.25h-94.16z"/>
    <path d="m194.67 154.17c-0.92 0-1.67-0.75-1.67-1.67s0.75-1.67 1.67-1.67h92.66c0.92 0 1.67 0.75 1.67 1.67s-0.75 1.67-1.67 1.67h-92.66z"/>
    <path d="m195.17 169.5c-0.92 0-1.67-0.75-1.67-1.67s0.75-1.66 1.67-1.66h91.66c0.92 0 1.67 0.74 1.67 1.66s-0.75 1.67-1.67 1.67h-91.66z"/>
    <rect y="141" width="93" x="194.5" height="6.6665"/>
    <rect y="156.17" width="91" x="195.5" height="6.8335"/>
  </g>
  <use width="482" xlink:href="#crown" transform="translate(-142.4)" height="579.00635"/>
  <use width="482" xlink:href="#crown" transform="translate(142.4)" height="579.00635"/>
  <line stroke-width="6" y2="203.76" x2="5" stroke="#000" y1="203.76" x1="477" fill="none"/>
  <path id="border" d="m477 0h-477v338.01c0 132.88 108.11 241 241 241s241-108.12 241-241v-338.01h-5zm-5 10v328.01c0 127.37-103.63 231-231 231s-231-103.63-231-231v-328.01h462z"/>
  <path id="drip" d="m248.12 251.67s-5 7.75-5 13.25 3.76 11.5 7.26 16 9 12.75 9 22-4.76 22.25-18.5 22.25c-13.76 0-18.26-12.75-18.26-19.5 0-9 8.77-16.17 10.8-17.79 2.03-1.61-0.75-5.63-0.75-5.63s-1.96-3.54-1.96-7.04 1.91-15.29 17.41-23.54z"/>
  <g id="upper_drip_row">
    <use width="482" xlink:href="#drip" transform="translate(-168)" height="579.00635"/>
    <use width="482" xlink:href="#drip" transform="translate(-84)" height="579.00635"/>
    <use width="482" xlink:href="#drip" transform="translate(84)" height="579.00635"/>
    <use width="482" xlink:href="#drip" transform="translate(168)" height="579.00635"/>
  </g>
  <g id="middle_drip_row">
    <use width="482" xlink:href="#drip" transform="translate(-126,100)" height="579.00635"/>
    <use width="482" xlink:href="#drip" transform="translate(-42,100)" height="579.00635"/>
    <use id="use1416" xlink:href="#drip" width="482" height="579.00635" transform="translate(42,100)"/>
    <use width="482" xlink:href="#drip" transform="translate(126,100)" height="579.00635"/>
  </g>
  <g id="lower_drip_row">
    <use width="482" xlink:href="#drip" transform="translate(75,200)" height="579.00635"/>
    <use width="482" xlink:href="#drip" transform="translate(-75,200)" height="579.00635"/>
  </g>
</svg>
```


After FIT has performed its SVG minification, if we check its source (path is `/;m=is;f=svg;q/images/avatar.svg`) we can see that it has been minified:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" id="Coat_of_arms_of_the_city_of_Cologne_(Germany)" xml:space="preserve" height="579.01" width="482" version="1.0" viewBox="0 0 482 579.00635"> <rect id="upper_red_area" height="198.76" width="472" y="5" x="5" fill="#f0000b"/> <path id="lower_white_area" fill="#fff" d="m477 338.01c0 130.34-105.66 236-236 236s-236-105.66-236-236v-134.34h472v134.34z"/> <g id="crown" fill="#ffe600"> <path d="m270.92 107.79c0 0.81-0.66 1.46-1.46 1.46h-8.58c-0.81 0-1.46-0.65-1.46-1.46 0-0.8 0.65-1.46 1.46-1.46h8.58c0.8 0 1.46 0.66 1.46 1.46z"/> <circle cy="100.88" cx="265.67" r="3.417"/> <path d="m293.04 90.542l1.75-37.875s-9.54 5.5-9.54 10.5 1.63 4.75 1.63 8.125-1.13 3.875-2.5 3.875c-1.38 0-5-3.25-7.13-3.25s-5.63 2.875-5.63 6.375 2.5 8.25 2.5 8.25 3.76-4.375 6.26-4.375 5.87 2.625 4.12 8.375h8.54z"/> <path d="m293.94 94.292c0 0.806-0.66 1.459-1.46 1.459h-9.83c-0.81 0-1.46-0.653-1.46-1.459 0-0.805 0.65-1.458 1.46-1.458h9.83c0.8 0 1.46 0.653 1.46 1.458z"/> <path d="m241 90.501h-5c2-4.5 0.17-8.5-5.17-8.5-5.33 0-7 5.166-7 5.166s-5.16-5.666-5.16-10.333 3.66-7 7-7c3.33 0 6 2.667 7.66 3.667 1.21-3.25 3.63-4.084 3.63-4.084s-5.63-4.916-5.63-8.25c0-3.333 3.15-4.856 5.34-6.833 3.02-2.729 4.33-6.812 4.33-6.812s1.31 4.083 4.33 6.812c2.19 1.977 5.34 3.5 5.34 6.833 0 3.334-5.63 8.25-5.63 8.25s2.42 0.834 3.63 4.084c1.66-1 4.33-3.667 7.66-3.667 3.34 0 7 2.333 7 7s-5.16 10.333-5.16 10.333-1.67-5.166-7-5.166c-5.34 0-7.17 4-5.17 8.5h-5z"/> <path d="m234 96.417c-0.97 0-1.75-0.783-1.75-1.75 0-0.966 0.78-1.75 1.75-1.75h14c0.97 0 1.75 0.784 1.75 1.75 0 0.967-0.78 1.75-1.75 1.75h-14z"/> <path d="m193.67 131.08c0-6.75-3.84-32.746-3.84-32.746h9.34s3.91 2.836 3.91 7.666-4.16 6.42-4.16 12.08c0 5.67 4.91 8.09 8.16 8.09s8.25-1.75 8.25-7.25-1.5-6.75-1.5-6.75h6.34s-1.5 1.25-1.5 6.75 5 7.25 8.25 7.25 8.16-2.42 8.16-8.09c0-5.66-4.16-7.25-4.16-12.08s3.91-6.916 3.91-6.916h12.34s3.91 2.086 3.91 6.916-4.16 6.42-4.16 12.08c0 5.67 4.91 8.09 8.16 8.09s8.25-1.75 8.25-7.25-1.5-6.75-1.5-6.75h6.34s-1.5 1.25-1.5 6.75 5 7.25 8.25 7.25 8.16-2.42 8.16-8.09c0-5.66-4.16-7.25-4.16-12.08s3.91-7.666 3.91-7.666h9.34s-3.84 25.996-3.84 32.746h-94.66z"/> <path d="m211.08 107.79c0 0.81 0.66 1.46 1.46 1.46h8.58c0.81 0 1.46-0.65 1.46-1.46 0-0.8-0.65-1.46-1.46-1.46h-8.58c-0.8 0-1.46 0.66-1.46 1.46z"/> <circle cy="100.88" cx="216.33" r="3.417"/> <path d="m188.96 90.542l-1.75-37.875s9.54 5.5 9.54 10.5-1.63 4.75-1.63 8.125 1.13 3.875 2.5 3.875c1.38 0 5-3.25 7.13-3.25s5.63 2.875 5.63 6.375-2.5 8.25-2.5 8.25-3.76-4.375-6.26-4.375-5.87 2.625-4.12 8.375h-8.54z"/> <path d="m188.06 94.292c0 0.806 0.66 1.459 1.46 1.459h9.83c0.81 0 1.46-0.653 1.46-1.459 0-0.805-0.65-1.458-1.46-1.458h-9.83c-0.8 0-1.46 0.653-1.46 1.458z"/> <path d="m193.92 138.33c-1.25 0-2.25-1-2.25-2.25 0-1.24 1-2.25 2.25-2.25h94.16c1.25 0 2.25 1.01 2.25 2.25 0 1.25-1 2.25-2.25 2.25h-94.16z"/> <path d="m194.67 154.17c-0.92 0-1.67-0.75-1.67-1.67s0.75-1.67 1.67-1.67h92.66c0.92 0 1.67 0.75 1.67 1.67s-0.75 1.67-1.67 1.67h-92.66z"/> <path d="m195.17 169.5c-0.92 0-1.67-0.75-1.67-1.67s0.75-1.66 1.67-1.66h91.66c0.92 0 1.67 0.74 1.67 1.66s-0.75 1.67-1.67 1.67h-91.66z"/> <rect y="141" width="93" x="194.5" height="6.6665"/> <rect y="156.17" width="91" x="195.5" height="6.8335"/> </g> <use width="482" xlink:href="#crown" transform="translate(-142.4)" height="579.00635"/> <use width="482" xlink:href="#crown" transform="translate(142.4)" height="579.00635"/> <line stroke-width="6" y2="203.76" x2="5" stroke="#000" y1="203.76" x1="477" fill="none"/> <path id="border" d="m477 0h-477v338.01c0 132.88 108.11 241 241 241s241-108.12 241-241v-338.01h-5zm-5 10v328.01c0 127.37-103.63 231-231 231s-231-103.63-231-231v-328.01h462z"/> <path id="drip" d="m248.12 251.67s-5 7.75-5 13.25 3.76 11.5 7.26 16 9 12.75 9 22-4.76 22.25-18.5 22.25c-13.76 0-18.26-12.75-18.26-19.5 0-9 8.77-16.17 10.8-17.79 2.03-1.61-0.75-5.63-0.75-5.63s-1.96-3.54-1.96-7.04 1.91-15.29 17.41-23.54z"/> <g id="upper_drip_row"> <use width="482" xlink:href="#drip" transform="translate(-168)" height="579.00635"/> <use width="482" xlink:href="#drip" transform="translate(-84)" height="579.00635"/> <use width="482" xlink:href="#drip" transform="translate(84)" height="579.00635"/> <use width="482" xlink:href="#drip" transform="translate(168)" height="579.00635"/> </g> <g id="middle_drip_row"> <use width="482" xlink:href="#drip" transform="translate(-126,100)" height="579.00635"/> <use width="482" xlink:href="#drip" transform="translate(-42,100)" height="579.00635"/> <use id="use1416" xlink:href="#drip" width="482" height="579.00635" transform="translate(42,100)"/> <use width="482" xlink:href="#drip" transform="translate(126,100)" height="579.00635"/> </g> <g id="lower_drip_row"> <use width="482" xlink:href="#drip" transform="translate(75,200)" height="579.00635"/> <use width="482" xlink:href="#drip" transform="translate(-75,200)" height="579.00635"/> </g> </svg>
```


#### Responsive Image Filtering

FIT can improve responsive image markup by removing sources that don't apply.

If x-descriptors and other descriptors are mixed, then FIT will not make any changes.



#### Head Reordering
While parsing the HTML `head` element, browser rendering can be delayed until all CSS style sheets are processed. Any scripts encountered while parsing need to be executed and therefore also block the rendering process. Moreover, external style sheets and scripts must be downloaded before they can be processed, further delaying the rendering process.

FIT can [reorder the elements in the HTML head](https://docs/current/web-accelerator/Head_Reordering.html) to optimize how the `head` is parsed so that there is minimal interruption to rendering.

Head reordering is enabled with the `head-reordering` element in `config.xml`:

```xml
<config>
  <acceleration>
    <head-reordering />
  </acceleration>
</config>
```


#### IE Comment Resolving

Likewise, FIT can also [resolve Internet Explorer conditional comments](https://developer.sevenval.com/docs/current/web-accelerator/IE_Comment_Resolving.html) and resolve the conditional comment for applicable IE browsers, or remove the comment altogether when not applicable to the client:

```xml
<config>
  <acceleration>
    <ie-comment-resolving />
  </acceleration>
</config>
```

Our markup contains the following IE comment:

```html
<!--[if lt IE 9 ]><link rel="stylesheet" href="legacy-ie.css"><![endif]-->
```

For IE8, or any older IE clients, FIT will resolve this to:

```html
<link rel="stylesheet" href="PATH/legacy-ie.css">
```

For IE9 and non-IE browsers, the comment will be removed altogether.


### Content Optimization Total Savings

So, now let's check how our page has emproved with all the described content optimization options enabled. Our `config.xml` would look like this:

```xml
<config>
  <acceleration>
    <html-minifying />
    <script-minifying />
    <style-minifying strip-prefixes="true"/>
    <filter-media-queries />
    <svg-minifying />
    <responsive-image-filtering />
    <head-reordering />
    <ie-comment-resolving />
  </acceleration>
  <ress>
    <image-scaling />
  </ress>
</config>
```

After applying this configuation and reloading our optimized page at `http://local14.sevenval-fit.com` we can see the page weight has dropped from XXX to YYY.

Our PageSpeed Insights score has also improved: it now shows a score of XX, up from YY.