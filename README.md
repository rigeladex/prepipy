# prepipy

PREtty PIctures using PYthon

## Overview

This package provides the ability to stretch and combine astronomical images from multiple bands into (RGB) colour images.

Images can be created in two main modes:
- JPEG image containing only the image in the pixel scale of the input, including coordinate information readable by e.g. [Aladin](https://aladin.u-strasbg.fr/).
- *DEVELOPMENT* Matplotlib image containing one ore more RGB conmibations from different bands in a grid layout. World coordinate axes are plotted on the images if available in the original input files. An additional sup-header containing the source name can be included. This mode also supportes multiple different options, such as plotting grid lines on top of the image, marking the center point in the image, of marking additional points of interest within the image, specified by world coordinates. By default, these images are saved in the pdf format.

## What does prepipy *not* do?

- Create an astrometric solution for input images. This must be provided in the FITS headers of the input images. Use a tool like e.g. [SCAMP](https://www.astromatic.net/software/scamp/) for this task.
- Resample the individual images to the same pixel scale. Input images must match exactly in terms of pixel scale, orientation and size (number of pixels along each axis). Prepipy assumes the input images can simply be added on a pixel-by-pixel basis. Use a tool like e.g. [SWarp](https://www.astromatic.net/software/swarp/) for this task.

## Example image

*Add text about example image, bands etc.*

![Example colour image of a star-forming region](https://nemesis.univie.ac.at/wp-content/uploads/2023/02/83.52054-5.39047.jpeg "Example image created using prepipy, centered around coordinates 83.52054, -5.39047.")


## Basic Usage

### JPEG mode

Current way to use from command line: run the `rgbcombo` script with arguments as described in the help message.

### Matplotlib mode

Development feature, not part of the current release.

### Input data

The input images are expected to fullfill the following criteria:
- FITS format images with the images data appearing in the primary HDU.
- Pixel scale and position matching across all input images. No additional resampling/reprojection is performed.
- WCS information is present in the FITS files.

### Setting options

The package currently uses two YAML configuration files to specify various options. These are referred to as the (general) `config` file and the `bands` file containing meta-information about the bands used in the original data.
If these files are not placed in the working directory, the path to them needs to be specified using the `-c` and `-b` command line options.

## Available classes

### Band

Data class used to store information about a passband.

The recommended way to construct instances is via a YAML config file.
Use the `Band.from_yaml_file(filename)` constructor to do so.
Alternatively, a minimalistic instance can be created by just providing the band name: `Band('foo')`. This will create a useable instance with all other parameters containing default or placeholder values.

### Frame

A `Frame` is an individual image taken in a given `Band`. Instances can be created manually or (recommended) either from a `astropy.io.fits.hdu.ImageHDU` object, `astropy.io.fits.hdu.HDUList` object plus an index or directly from a FITS file. Use the `from_hdu(hdu_object, band)`, `from_hdul(hdu_list_object, band, hdu_index)` or `from_fits(filename, band, hdu_index)` contructors respectively.

Operations like clipping, normalisation and stretching are performed as methods of the `Frame` class. Individual frames can be saved as single-HDU FITS files (`Frame.save_fits(filename)`).

### Picture

A `Picture` is a collection of one or more `Frame` objects. Frames can be added from a FITS file via `Picture.add_frame_from_file(filename, band)`, where `band` can be an instance of `Band` or, in the minimalistic case, a string containing the band name only. Frames can also be added directly from a 2D numpy array: `Picture.add_frame(image_array, band, header)`, where `header` can be an instance of `astropy.io.fits.Header` or `None`. A third option is to add `Frame` objects manually to the `Picture.frames` list. This has the downside that the frames's band is not checked against the other frames' bands already present in the picture. Normally, only one frame per band is allowed in a picture.

It is also possible to construct a `Picture` object from a 3D array containing 2D images, or to construct multiple instances from a 4D array. Warning: These features are currently highly experimental, not tested and not well documented.

The `Picture` class also provides a number of convenience properties, including `bands`, `primary_frame`, `image`, `coords`, `center`, `center_coords`, `center_coords_str`, `image_size`, `pixel_scale` and `image_scale`.

#### Subclasses of Picture

`RGBPicture`

`JPEGPicture`

# Acknowledgement

> This package was initially developed in the course of the [NEMESIS](https://nemesis.univie.ac.at) project at the University of Vienna. The NEMESIS project has received funding from the European Union's Horizon 2020 research and innovation programme under grant agreement No. 101004141.
