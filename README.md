## ZIF : the Zoomable Image File Format
### Description
**ZIF**, the **Zoomable Image file format**, also known as the **Zoomify Image file format**, is a open-source, multiresolution, pyramidal tiled file format, based on TIFF, designed to enable interactive panning and zooming of extremely large images, especially over the World Wide Web and other Internet services.

The ZIF file structure is such that it does not require an image server to deliver image views, although an image server can provide increased performance, cacheing, security, annotations, static views, dynamic transcoding, quality/bandwidth control, image adjustments, and many other features. Basic functionality requires only a web server capable of supporting byte-range requests.

The format was developed in 2015 by [**Objective Pathology Services Ltd**.](http://www.objectivepathology.com) and maintained in conjunction with [**Zoomify Inc**.](http://zoomify.com)

### Specification
Essentially, ZIF is a subspecification of TIFF, with minor departures from the TIFF 6.0 specification, and support for web browser image codecs, to enable serverless delivery of images to modern web browsers and other client applications.

- ZIF files must be tiled TIFFs
- Can be TIFF or BigTIFF, with TIFF preferred for files under 4 GB; note that if using LibTIFF, version 4.0 (December 2011) is required to support BigTIFF.
- Start bytes "II" - little-endian only, "MM" not permitted.it
- Image 1 is the whole image
- Image 2 is a thumbnail overview... ???
- All tile IFDs are located sequentially in a block at the beginning of the file.
- Subsampled image resolutions are in SubIFD tags
- Only 8-bit 3-channel RGB images
- Tag 262 (Photometric Interpretation) must be 2 (RGB)
- Tiles must be JPEG, PNG, or JPEG-XR compressed.
- For JPEG tiles, the JPEG tables must be contained (repeated) in every tile.
- For PNG and JPEG-XR tiles, note that these are not compatible with the TIFF 6.0 specification (*deviation*).
- Further, for JPEG-XR tiles, note that at this time only Microsoft Internet Explorer 9.0+ and Edge web browsers support this compression scheme.
- PNG tiles need a tile compression code in tag 259...34933
- JPEG-XR tiles need a tile compression code in tag 259...34934
- JPEG tiled ZIFs are very close to true TIFF 6.0 files, except for edge tile sizes; most readers are nevertheless fully able to read these files; start bytes as in TIFF.
- PNG and JPEG-XR tiled ZIFs are incompatible with TIFF readers, and start with custom bytes.
- JPEG tiled ZIFs may be renamed to Aperio/Leica .svs (ScanScope Virtual Slide) files for digital pathology and virtual microscopy applications, and are fully compatible with and equivalent to JPEG-compressed SVS slides.
- Edge (right and lower) tiles are clipped, not padded to multiples of 16 as per the TIFF 6.0 specification (*deviation*)

- Tag 51159: ZIF metadata
- Tag 51160: ZIF annotations


### Image Viewer
TBD

### Client Libraries
Client libraries will eventually be provided here for

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

