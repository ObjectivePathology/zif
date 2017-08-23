## ZIF : the Zoomable Image File Format
### Description
**ZIF**, the **Zoomable Image file format**, also known as the **Zoomify Image file format**, is an open-source, web-friendly, multi-resolution, pyramidal tiled file format, based on TIFF, designed to enable interactive panning and zooming of extremely large images, especially over the World Wide Web and other Internet services.

Essentially, ZIF is a subspecification of TIFF, with minor departures from the TIFF 6.0 specification, and support for web browser image codecs, to enable serverless delivery of images to modern web browsers and other network client applications.

The ZIF file structure is such that it does not require an image server to deliver image views, although an image server can provide increased performance, cacheing, security, annotations, static views, dynamic transcoding, quality/bandwidth control, image adjustments, and many other features. Basic functionality requires only a web server capable of supporting byte-range requests.

The format was developed in 2015 by [**Objective Pathology Services Ltd**.](http://www.objectivepathology.com) and maintained in conjunction with [**Zoomify Inc**.](http://zoomify.com)

ZIF comes in two flavors, Baseline and Advanced; Baseline generally follows the TIFF 6.0 specification and is intended for smaller, simpler images, while Advanced departs somewhat from TIFF 6.0 to allow SubIFDs, simultaneous zoomable, focusable, and time-series data, and more advanced codecs.

###  Common Specifications, Baseline and Advanced
- Only tiled, 8-bit, 3-channel, interleaved RGB images are supported; no strips/rasters, planar configuration, alpha channel, 1-,2-,4+ channels, higher bit depths, etc. ZIF is intended only for common, monitor-displayable images.
- Start bytes "II" - little-endian only, "MM" not permitted.
- Image Directory IFD 1 is the whole base image
- Tag 262 (Photometric Interpretation) must be 2 (RGB)

### Baseline Specification
- Baseline ZIF files must be tiled TIFF 6.0 files (no BigTIFF in Baseline ZIF), so maximum dimensions are 64K x 64K pixels (65,536 x 65,536), and 4 GB file size. Note that some software only supports 2 GB TIFFs.
- Image Directory IFD 2 + is:
* If IFDs ceiling(previous IFD / 2), multiresolution
* If all IFD's same size, time series
- Optional solid thumbnail in SubIFD 1 of IFD 1. Thumbnail must be JPEG or PNG.
- Tiles must be JPEG or PNG compressed; JPEG here is meant to be the legacy JPEG specification (ITU Recommendation T.81 | ISO/IEC 10918-1) as commonly used on the Internet and many software packages and operating systems, and as embodied by the popular Independent JPEG Group 6b specification of 1998. Note that a common TIFF codec, LZW, is specifically disallowed in ZIF, as are raw uncoded images.
- For JPEG tiles, the JPEG tables must be contained (duplicated) in every tile.
- Further, for JPEG-XR tiles, note that at this time only Microsoft Internet Explorer 9.0+ and Edge web browsers support this compression scheme.
- PNG tiles need a tile compression code in tag 259...34933
- JPEG-XR tiles need a tile compression code in tag 259...34934
- JPEG tiled ZIFs are very close to true TIFF 6.0 files, except for edge tile sizes; most readers are nevertheless fully able to read these files; start bytes as in TIFF.
- PNG and JPEG-XR tiled ZIFs are incompatible with TIFF readers, and start with custom bytes.
- JPEG tiled ZIFs may be renamed to Aperio/Leica .svs (ScanScope Virtual Slide) files for digital pathology and virtual microscopy applications, and are fully compatible with and equivalent to JPEG-compressed SVS slides.
- Tile size must be a multiple of 16 as per the TIFF 6.0 specification Section 15.
- Tag 51159: ZIF metadata
- Tag 51160: ZIF annotations

### Advanced Specification
- Advanced ZIF files must be BigTIFF only; note that if using LibTIFF, version 4.0 (December 2011) is required to support BigTIFF (*deviation*).
- In addition to JPEG or PNG, tiles may be JPEG XR compressed (targeting Microsoft Edge and IE 9+ browsers), or JPEG 2000 compressed (targeting Apple Safari and WebKit browsers). Note that with JPEG XR and JPEG 2000, server-based transcoding may be required for universal browser compatibility.
- For PNG and JPEG-XR tiles, note that these are not compatible with the TIFF 6.0 specification (*deviation*).
- For dedicated/embedded applications, tiles may be JPEG XT compressed (backwards-compatible with JPEG)

### Recommendations
- All tile IFDs should be, but need not be, located sequentially in a block at the beginning of the file.
- The size of the IDF block is readable by *?????*

### Image Viewers
In future, open-source browser-based image viewers will be available here for collaborative development:

* Full viewer in JavaScript/CoffeeScript, with S3.js
* Leaflet plugin
* Google Maps plugin
* OpenLayers 3 plugin
* Modest Maps plugin
* Polymaps plugin

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
