<img src="ZIF.svg" width="216" height="126">

## ZIF : the Zoomable Image File Format

### Description
**ZIF**, the **Zoomable Image File format**, alternatively also known as the **Zoomify Image File format**, is an open-source, web-friendly, multi-resolution, pyramidal tiled file format, based on TIFF, designed to enable interactive panning and zooming of extremely large images, especially over the World Wide Web and other Internet services.

Essentially, ZIF is a subspecification of BigTIFF, departing substantially from the TIFF 6.0 Baseline and TIFF 6.0 Part 2: TIFF Extensions specifications, adding support for very large images and web browser image codecs, to enable serverless delivery of images to modern web browsers and other network client applications, while still being interoperable with the popular [**libTIFF**](http://www.simplesystems.org/libtiff) imaging library.

The ZIF file structure is such that it does not require an image server to deliver image views, although an image server can provide increased performance, cacheing, security, annotations, static views, dynamic transcoding, quality/bandwidth control, image adjustments, and many other features. Basic functionality requires only a web server capable of supporting byte serving via  [**IETF RFC 7233**](https://tools.ietf.org/html/rfc7233) Range Requests.

The format was conceived and designed in 2012 by [**Objective Pathology Services Ltd**](http://www.objectivepathology.com)., and is maintained and supported in conjunction with [**Zoomify Inc**](http://zoomify.com)., who provided initial development and real-world testing.

ZIF comes in two flavors: Baseline and Advanced. Baseline is intended for easy implementation and wide compatibility, while Advanced takes inspiration from the more comprehensive TIFF Extensions subset to allow SubIFDs, simultaneous zoomable, focusable, and time-series data, and more advanced codecs. All forms of ZIF can be created and used with [**libTIFF**](http:/libtiff.maptools.org)&nbsp;v.4.0 and later, and other libraries supporting BigTIFF.

### Why no "standard" TIFF 6.0 support?
Since all zoomable images require tiles, many zoomable images are larger than the 64K&nbsp;&#10005;&nbsp;64K, 4&nbsp;GB TIFF&nbsp;6.0 limits, and since for browser interoperability either JPEG or PNG codecs unsupported by baseline TIFF are required, even with a 32-bit TIFF&nbsp;6.0 Part&nbsp;2 zooming implementation almost no existing software could read or write most images anyway; so to keep ZIF implementations simple and future-focused, only BigTIFF containers are permitted.

###  Common Specifications, Baseline and Advanced
- no strips/rasters, planar configuration, alpha channel, higher bit depths, etc. ZIF Baseline is intended only for common, monitor-displayable images, 8-bit, monochrome or RGB.
- Start bytes "II" - little-endian only, "MM" not permitted.
- Version 002B<sub>16</sub> for BigTIFF only, never TIFF&nbsp;6.0 002A<sub>16</sub>
- Image Directory IFD&nbsp;1 is the whole base image, always interleaved and tiled
- Tile size must be a multiple of 16 as per the TIFF&nbsp;6.0 specification Section&nbsp;15.

| Zoomable | Z-stack<br>/ Focal | Time<br>Series | Baseline | Advanced |
| :---: | :---: | :---: | :---: | :---: |
| &#10003; | - | &ndash; | &#10003; | &#10003; |
| &ndash; | &#10003; | &ndash; | &#10003; | &#10003; |
| &ndash; | &ndash; | &#10003; | &#10003; | &#10003; |
| &ndash; | &ndash; | &#10003; | &#10003; | &#10003; |

### Baseline Specification
* Only tiled, 8-bit, 1- or 3-channel, interleaved monochrome or RGB images are supported; 
* Image Directory IFD&nbsp;2 + is:
  * If IFDs are halving in size (pixels rounded up, contents precisely half, left/top aligned), multiresolution
  * If all IFD's are the same size, time series
  * else, a collection of distinct images
* Images/tiles may have a Z-dimension, which without any metadata description represents focal plane slices or true z-dimension (never time-series, exposure series, or other representations).
* Optional solid thumbnail in SubIFD 1 of IFD 1 (for multiresolution images) or each IFD (for other content). Thumbnail must be JPEG or PNG, strip or raster (no tiles), and no larger than 4096&nbsp;&#10005;&nbsp;4096; recommended size is 1024&nbsp;pixels on largest side; progressively encoded YCbCr&nbsp;4:2:0 is recommended but not mandatory for JPEG thumbnails.
* Tiles must be JPEG or PNG compressed; JPEG here is meant to be the legacy JFIF specification (ITU Recommendation T.81&nbsp;: ISO/IEC 10918-1) as commonly used on the Internet and many software packages and operating systems, and as embodied by the popular Independent JPEG Group's libjpeg&nbsp;v6b specification of 1998. JPEG tiles must be packaged as standalone JFIF streams (see below). Note that two very common TIFF codecs, LZW and Deflate, are specifically disallowed in ZIF, as are raw uncoded images.
* For JPEG tiles, the JPEG tables must be contained (duplicated) in every tile, such that each extracted tile is independently viewable.
* For JPEG tiles, the JFIF APPn colorspace must be contained (duplicated) in every tile, such that each extracted tile is independently viewable. Note that implementations should ideally also support the Adobe APPn colorspace tag also for increased compatibility with JPEG codecs.
* For JPEG tiles, TIFF tag 259<sub>10</sub> (0103<sub>16</sub>): Compression must be 7 (JPEG), and TIFF tag 262<sub>10</sub> (0106<sub>16</sub>): PhotometricInterpretation must be RGB or YCbCr for 3-channel images, or grayscale for 1-channel monochrome images. Channel subsampling of either 4:4:4 or 4:2:0 is permitted for YCbCr colorspace; RGB colorspace must be 4:4:4 subsampled.
* PNG tiles need a tile compression code in tag 259<sub>10</sub> (0103<sub>16</sub>): Compression of 34933<sub>10</sub>. 24-bit true-color PNGs are recommended; although palettized PNGs are permitted they are highly discouraged.

### Advanced Specification
- In addition to JPEG or PNG, tiles may be JPEG XR compressed (targeting Microsoft Edge and IE 9+ browsers), or JPEG 2000 compressed (targeting Apple Safari and WebKit browsers). Note that with JPEG XR and JPEG 2000, server-based transcoding may be required for universal browser compatibility, and are intended for LAN-based applications rather than the public Internet.
- For JPEG tiles, TIFF tag PhotometricInterpretation may additionally be RGB, in which case no channel subsampling is permitted.
- For PNG, JPEG XR and JPEG 2000 tiles, note that these are not compatible with the TIFF 6.0 specification (*deviation*).
- JPEG-XR tiles need a tile compression code in tag 259<sub>10</sub> (0103<sub>16</sub>): Compression of 34934<sub>10</sub>.
- Further, for JPEG-XR tiles, note that at this time only Microsoft Internet Explorer 9.0+ and Edge web browsers support this compression scheme.
- For dedicated/embedded applications, tiles may optionally be JPEG XT compressed (backwards-compatible with JPEG), still using compression code 7 (JPEG), but with the knowledge that not all implementations will have access to the additional detail that JPEG XT provides over JPEG.

### Typical Codings
#### 3-channel 8-bit lossy "normal" single zoomable image
Code as Baseline, JPEG tiles (3-channel interleaved), using chain of top-level IFDs as decreasing resolution levels (pixels must be halving, rounded up)
#### 3-channel 8-bit lossless "normal" single zoomable image
Code as Baseline, 24-bit PNG tiles (3-channel interleaved), using chain of top-level IFDs as decreasing resolution levels (pixels must be halving, rounded up)
#### 3-channel 8-bit JPEG 2000 tiled single image
Note that this is *not* a true JPEG 2000 file
Recommended: code as Advanced, JPEG 2000 tiles (3-channel interleaved), first top-level IFD contains base image, Sub-IFDs contain decreasing resolution levels
Not recommended: code as Advanced, JPEG 2000 tiles (3-channel interleaved), using chain of top-level IFDs as decreasing resolution levels (pixels must be halving, rounded up)

### Recommendations
- All tile IFDs should be, but need not be, located sequentially in a block at the beginning of the file.
- note that standards-compliant JPEG 2000 Compression is value 34712<sub>10</sub> (8798<sub>16</sub>) with PhotometricInterpretation of either (YCbCr) or (RCT). Aperio.svs files also use 33003<sub>10</sub> (80EB<sub>16</sub>) for YCbCr from Matrox libraries, or 33005<sub>10</sub> (80ED<sub>16</sub>) for RGB/RCT from Kakadu libraries. Both of these are not standards-compliant, and may not be used in in any ZIF files. Note that PhotometricInterpretation of JPEG 2000 data is not defined in the TIFF specifications.

### Metadata
Any TIFF-compatible metadata tags are fine with ZIF. Some examples:
- TIFF_Tag 34665<sub>10</sub> (8769<sub>16</sub>): EXIF Metadata
- TIFF_Tag 51159<sub>10</sub> (C7D7<sub>16</sub>): Objective/WSI/ZIF metadata
- TIFF_Tag 51160<sub>10</sub> (C7D8<sub>16</sub>): Objective/WSI/ZIF annotations

#### TIFF Tag Make Registry
TIFF_Tag Make, 271<sub>10</sub> (010F<sub>16</sub>), if present, contains the make/manufacturer of scanner, software, or device used. Registered values are:

| Value | Make |
| :---: | :--- |
| 0 | (unknown) |
| 1 | [Objective Pathology Services](http://www.objectivepathology.com) |
| 2 | [Zoomify](http://www.zoomify.com) |
| 3 | [Huron Digital Pathology](http://hurondigitalpathology.com) |

#### TIFF Tag Model Registry
TIFF_Tag Model, 272<sub>10</sub> (0110<sub>16</sub>), if present, contains the model/type of scanner or device used. Registered values are:

| Value | Make | Model |
| :---: | :--- | :--- |
| 0 | (unknown or software-generated) | (none) |
| 1 - 15 | [Objective Pathology Services](http://www.objectivepathology.com) | (reserved) |
| 16 - 31 | [Huron Digital Pathology](http://hurondigitalpathology.com) | (reserved) |

#### TIFF Tag Software Registry
TIFF_Tag Software, 305<sub>10</sub> (0131<sub>16</sub>), if present, contains the output software used. Registered values are:

| Value | Make | Model |
| :---: | :--- | :--- |
| 0 | (unknown or hardware-generated) | (none) |
| 1 - 15 | [Objective Pathology Services](http://www.objectivepathology.com) | (reserved) |
| 16 - 23 | [Zoomify](http://www.zoomify.com) | (reserved) |
| 24 - 39 | [Huron Digital Pathology](http://hurondigitalpathology.com) | (reserved) |

### ZIF Generators
* Adobe Photoshop plugin (possibly native)
* ImageJ / Fiji
* GIMP
* ImageMagick
* VIPS
* Objective Converter
* Zoomify Converter

See http://iipimage.sourceforge.net/documentation/images.

See http://libvips.blogspot.ca/2013/03/making-deepzoom-zoomify-and-google-maps.html.

### Image Viewers
In future, open-source browser-based image viewers will be available here for collaborative development:
* Full viewer in JavaScript/CoffeeScript, with S3.js
* Leaflet/Mapbox plugin
* Google Maps plugin
* OpenLayers 3 plugin
* Modest Maps plugin
* Polymaps plugin

### BigTIFF Resources

#### C/C++

* [**libTIFF**](ftp://download.osgeo.org/libtiff) (web pages at [**Simple Systems**](http://www.simplesystems.org/libtiff) or [**MapTools**](http:/libtiff.maptools.org)); do NOT use obsolete libtiff.org.
* [**AWare Systems](https://www.awaresystems.be/imaging/tiff/bigtiff.html)
* [**LeadTools**](https://www.leadtools.com/help/leadtools/v19/dh/to/l-topics-fileformatstiffandbigtifftaggedimagefileformat.html)
* [**VintaSoft**](https://www.vintasoft.com/vsimaging-dotnet-index.html)
* BigTIFF.org (original Aperio implementation, obsolete

### APIs

In addition to raw ZIF byte-served access, server APIs will include:
* REST
* JSON
* Protocol Buffers
* XML-RPC
* MessagePack-RPC
* SOAP
* Thrift
* [IIIF](http://iiif.io)

Serialization formats:
* JSON
* MessagePack
* Gobs (in Go)
* BSON
* Google Protocol Buffers
* Thrift
* HDF5
* XML
* Avro
* Base64/JSON – strings and ArrayBuffers
* don’t forget data can be delivered gzipped…
* HmlHttpRequest2 supports ArrayBuffers, Blobs, and FormData


### Client Libraries
Client libraries will eventually be provided here for:
* JavaScript
* CoffeeScript
* Python
* Ruby
* PHP
* C
* C++
* C# .NET
* D
* Go
* Rust
* Julia
* Erlang
