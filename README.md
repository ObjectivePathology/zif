## ZIF : the Zoomable Image File Format

### Description
**ZIF**, the **Zoomable Image File format**, alternatively also known as the **Zoomify Image File format**, is an open-source, web-friendly, multi-resolution, pyramidal tiled file format, based on TIFF, designed to enable interactive panning and zooming of extremely large images, especially over the World Wide Web and other Internet services.

Essentially, ZIF is a subspecification of TIFF, with minor departures from the TIFF 6.0 specification, and support for web browser image codecs, to enable serverless delivery of images to modern web browsers and other network client applications.

The ZIF file structure is such that it does not require an image server to deliver image views, although an image server can provide increased performance, cacheing, security, annotations, static views, dynamic transcoding, quality/bandwidth control, image adjustments, and many other features. Basic functionality requires only a web server capable of supporting byte-range requests per [RFC 7233](https://tools.ietf.org/html/rfc7233).

The format was conceived and developed in 2015 by [**Objective Pathology Services Ltd**](http://www.objectivepathology.com). and maintained and supported in conjunction with [**Zoomify Inc**](http://zoomify.com).

ZIF comes in two flavors, Baseline and Advanced; Baseline generally follows a subset the TIFF 6.0 Part 2: TIFF Extensions specification and is intended for smaller, simpler images, while Advanced follows more comprehensive TIFF Extensions subset to allow SubIFDs, simultaneous zoomable, focusable, and time-series data, and more advanced codecs, and moves to BigTIFF to support very large image dimensions. Both flavours of ZIF can be manipulated with [libTIFF](http://simplesystems.org/libtiff) and other libraries.

###  Common Specifications, Baseline and Advanced
- Only tiled, 8-bit, 3-channel, interleaved RGB images are supported; no strips/rasters, planar configuration, alpha channel, 1-,2-,4+ channels, higher bit depths, etc. ZIF is intended only for common, monitor-displayable images.
- Start bytes "II" - little-endian only, "MM" not permitted.
- Image Directory IFD 1 is the whole base image
- Tag 262 (Photometric Interpretation) must be 2 (RGB)
- Tile size must be a multiple of 16 as per the TIFF 6.0 specification Section 15.

### Baseline Specification
* Baseline ZIF files must be tiled TIFF 6.0 files (no BigTIFF is permitted in Baseline ZIF); maximum dimensions are 64K x 64K pixels (65,536 x 65,536), and not more than 4 GB file size. Note that some software only supports 2 GB TIFFs.
* Image Directory IFD 2 + is:
  * If IFDs are halving in size (pixels rounded up, contents precisely half, left/top aligned), multiresolution
  * If all IFD's same size, time series
  * else, a collection of distinct images
* Images/tiles may have a Z-dimension, which without any metadata description represents focal plane slices or true z-dimension (not time-series, exposure series, or other representations).
* Optional solid thumbnail in SubIFD 1 of IFD 1 (for multiresolution images) or each IFD (for other content). Thumbnail must be JPEG or PNG, and no larger than 4096 x 4096; recommended size is 1024 pixels on largest side, and a progressive encoding is recommended for JPEG thumbnails.
* Tiles must be JPEG or PNG compressed; JPEG here is meant to be the legacy JPEG specification (ITU Recommendation T.81 : ISO/IEC 10918-1) as commonly used on the Internet and many software packages and operating systems, and as embodied by the popular Independent JPEG Group 6b specification of 1998. Note that two common TIFF codecs, LZW and Deflate, are specifically disallowed in ZIF, as are raw uncoded images.
* For JPEG tiles, the JPEG tables must be contained (duplicated) in every tile, such that each tile is independently viewable.
* For JPEG tiles, TIFF tag 259(0x0103): Compression must be 7 (JPEG), and TIFF tag 262(0x0106): PhotometricInterpretation must be YCbCr. Channel subsampling is permitted.
* PNG tiles need a tile compression code in tag 259(0x0103): Compression of 34933.
* JPEG tiled ZIFs are true TIFF 6.0 Part 2 files; some common readers are fully able to read these files.
* PNG tiled ZIFs are incompatible with common TIFF readers.

### Advanced Specification
- Advanced ZIF files must be BigTIFF only; note that if using LibTIFF, version 4.0 (December 2011) is required to support BigTIFF (*deviation*). Note that Baseline versus Advanced ZIF may be recognized simply by testing for TIFF 6.0 vs BigTIFF.
- In addition to JPEG or PNG, tiles may be JPEG XR compressed (targeting Microsoft Edge and IE 9+ browsers), or JPEG 2000 compressed (targeting Apple Safari and WebKit browsers). Note that with JPEG XR and JPEG 2000, server-based transcoding may be required for universal browser compatibility, and are intended for LAN-based applications rather than the public Internet.
- For JPEG tiles, TIFF tag PhotometricInterpretation may additionally be RGB, in which case no channel subsampling is permitted.
- For PNG, JPEG XR and JPEG 2000 tiles, note that these are not compatible with the TIFF 6.0 specification (*deviation*).
- JPEG-XR tiles need a tile compression code in tag 259...34934
- Further, for JPEG-XR tiles, note that at this time only Microsoft Internet Explorer 9.0+ and Edge web browsers support this compression scheme.
- PNG and JPEG-XR tiled ZIFs are incompatible with TIFF readers.
- For dedicated/embedded applications, tiles may be JPEG XT compressed (backwards-compatible with JPEG)

### Recommendations
- All tile IFDs should be, but need not be, located sequentially in a block at the beginning of the file.
- The size of the IFD block is readable by **?????**
- note that standards-compliant JPEG 2000 Compression is value 34712 (0x8798) with PhotometricInterpretation of either (YCbCr) or (RCT). Aperio.svs files also use 33003 (0x80EB) for YCbCr from Matrox libraries, or 33005 (0x80ED) for RGB/RCT from Kakadu libraries. Both of these are not standards-compliant, and may not be used in in any ZIF files. Note that PhotometricInterpretation of JPEG 2000 data is not defined in the TIFF specifications.

### Metadata
Any TIFF-compatible metadata tags are fine with ZIF. Some examples:
- Tag 0x8769: EXIF metadata
- Tag 51159: Objective/WSI/ZIF metadata
- Tag 51160: Objective/WSI/ZIF annotations

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
