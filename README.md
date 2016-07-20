# Hymir IIIF Server

[![Build Status](https://travis-ci.org/dbmdz/iiif-server-hymir.svg?branch=master)](https://travis-ci.org/dbmdz/iiif-server-hymir)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub release](https://img.shields.io/github/release/dbmdz/iiif-server-hymir.svg?maxAge=2592000)](https://github.com/dbmdz/iiif-server-hymir/releases)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/54890c0e2bce4489ad0793658b2a4d0c)](https://www.codacy.com/app/ralf-eichinger/iiif-server-hymir?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=dbmdz/iiif-server-hymir&amp;utm_campaign=Badge_Grade)
[![codecov](https://codecov.io/gh/dbmdz/iiif-server-hymir/branch/master/graph/badge.svg)](https://codecov.io/gh/dbmdz/iiif-server-hymir)
[![Maven Central](https://img.shields.io/maven-central/v/de.digitalcollections/iiif-server-hymir.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iiif-server-hymir%22)

Hymir is a Java based IIIF Server. It is based on [IIIF Image API Java Libraries](https://github.com/dbmdz/iiif-image-api "IIIF Image API Java Libraries") and [IIIF Presentation API Java Libraries](https://github.com/dbmdz/iiif-presentation-api "IIIF Presentation API Java Libraries") (Java implementations of the [IIIF specifications](http://iiif.io/technical-details/ "IIIF specifications")).

## Features

- IIIF Image API 2.0.0 compliant (see <a href="http://iiif.io/api/image/2.0/">http://iiif.io/api/image/2.0/</a>).
- IIIF Presentation API 2.0.0 compliant (see <a href="http://iiif.io/api/presentation/2.0/">http://iiif.io/api/presentation/2.0/</a>). (implementation is work in progress)
- Based on IIIF Image API Java Library and IIIF Presentation API Java Library.
- On the fly image processing. No additional pregenerated (pyramid zoom) images are needed. No additional storage consumption.
- Ready to deploy standard java web application (WAR) for running as a standalone IIIF server.
- Spring based modular, extendable, easy to maintain enterprise architecture.
- Highly customizable image storage and identifier resolving: Access to images over project specific Resolver-plugin mechanism.
- Support for Filesystem- and HTTP-Image-Repositories (own protocols can be added by providing specific resolver)
- Highly customizable manifest generation: implement your own mapping from project specific structure metadata to standard Manifest object.
- Configurable, pluggable Image processing engines: Choose one of
    - Java Image I/O API (javax.imageio) (see <a href="http://docs.oracle.com/javase/8/docs/api/javax/imageio/package-summary.html#package.description">Image IO package description</a>)
    - Independent JPEG Group library "libjpeg8" (see <a href="http://ijg.org/">http://ijg.org/</a>)
    - ... more to come (or implement your own as pluggable library)
- Embedded IIIF Image Viewer (for out of the box viewing of served images): OpenSeadragon 2.0.0 (see "Usage" below)
- Embedded IIIF Presentation Viewer: Mirador 2.0.0 (see "Usage" below)
- Direct Manifest access (see "Usage" below)

### Supported image formats

<table border="1">
  <tr>
    <th>Format</th>
    <th>supported</th>
    <th>Image processing engine</th>
    <th>tested</th>
  </tr>
  <tr>
    <td><a href="http://www.jpeg.org/">JPEG</a></td>
    <td>yes</td>
    <td>Java Image I/O</a>
    <td>yes</td>
  </tr>
  <tr>
    <td></td>
    <td>yes</td>
    <td>libjpeg8</a>
    <td>yes</td>
  </tr>
  <tr>
    <td><a href="http://www.libpng.org/pub/png/spec/">PNG</a></td>
    <td>yes</td>
    <td>Java Image I/O</a>
    <td>no</td>
  </tr>
  <tr>
    <td><a href="http://www.jpeg.org/">BMP</a></td>
    <td>yes</td>
    <td>Java Image I/O</a>
    <td>no</td>
  </tr>
  <tr>
    <td><a href="http://www.wapforum.org/what/technical/SPEC-WAESpec-19990524.pdf">WBMP</a></td>
    <td>yes</td>
    <td>Java Image I/O</a>
    <td>no</td>
  </tr>
  <tr>
    <td><a href="http://www.w3.org/Graphics/GIF/spec-gif89a.txt">GIF</a></td>
    <td>yes</td>
    <td>Java Image I/O</a>
    <td>no</td>
  </tr>
</table>

More formats (including TIFF) will be supported soon by using Java Image I/O format plugins from <a href="http://haraldk.github.io/TwelveMonkeys/">TwelveMonkeys ImageIO</a>.

## Prerequisites

- Server with minimum 4GB RAM.
- Java 8 and Java Webapplication Server (e.g. Tomcat 8)

## Installation

Download (from GitHub-project page under "releases") and deploy WAR file into (Apache Tomcat) application server.

## Configuration

Based on unique resource identifiers the server tries to resolve identifier to a file path.
The resolving rules are configurable with regular expressions.

### Image resolving

After application server unpacked Hymir-WAR, you can configure image resolving by defining your resolving-rules in file:

```
$ cd $TOMCAT_HOME/webapps/<hymir-directory>
$ mkdir -p WEB-INF/classes/de/digitalcollections/iiif/image/config
$ cd WEB-INF/classes/de/digitalcollections/iiif/image/config
$ vi image-resolvingpatterns-PROD.properties
# Example: bsb00046285_00001 -> file:/bsbzend/bsb0004/bsb00046285/images/150/bsb00046285_00001.jpg
#^(\\w{3})(\\d{4})(\\d{4})_(\\d{5})$=file:/var/local/$1$2/$1$2$3/images/150/$1$2$3_$4.jpg
```

### Presentation manifest resolving

After application server unpacked Hymir-WAR, you can configure manifest resolving by defining your resolving-rules in file:

```
$ cd $TOMCAT_HOME/webapps/<hymir-directory>
$ mkdir -p WEB-INF/classes/de/digitalcollections/iiif/presentation/config
$ cd WEB-INF/classes/de/digitalcollections/iiif/presentation/config
$ vi presentation-resolvingpatterns-PROD.properties
# Example: bsb00046585 -> file:/var/local/bsb0004/bsb00046585/bsb00046585_manifest.json
#^(\\w{3})(\\d{4})(\\d{4})$=file:/var/local/$1$2/$1$2$3/$1$2$3_manifest.json
```

## Usage

Start application server.
Access Hymir GUI (e.g. http://localhost:8080/).

## Users

- Bavarian State Library, Project Bavarikon: <a href="http://www.bavarikon.de/">http://www.bavarikon.de/</a>
- Bavarian State Library, Project digiPress: <a href="https://digipress2.digitale-sammlungen.de/">https://digipress2.digitale-sammlungen.de/</a>