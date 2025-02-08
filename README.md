<!--[![Build Status](https://travis-ci.org/castlabs/dashas.png?branch=master)](https://travis-ci.org/castlabs/dashas)-->

dash.as
=======

Dash.as plays [MPEG-DASH](http://dashif.org/) video on any device supporting Adobe Flash. It is written in Adobe ActionScript and was designed from the ground-up to be lightweight with performance in mind. 

MPEG-DASH (Dynamic Adaptive Streaming over HTTP) became an [International Standard](http://standards.iso.org/ittf/PubliclyAvailableStandards/c057623_ISO_IEC_23009-1_2012.zip) in 2011 and is a technique designed specifically for streaming high-quality multimedia over an HTTP server. It works by fragmenting content into a series of small HTTP-based file segments where each contains block of playback time. Content is made available at a variety of different bitrates to account for variations in a connection's bandwidth quality. Upon each download of a segment, the highest bitrate the connection can handle in time for seamless playback is selected. This allows MPEG-DASH to seamlessly adapt to changing network conditions at the highest quality possible for a seamless user experience.

This project is an extension for [Open Source Media Framework](http://osmf.org/) and released under the [Mozilla Public License 2.0](http://www.mozilla.org/MPL/2.0/).

At the same time there is profersional edition of dash.as which integrates nicely with the [Adobe Access DRM](http://www.adobe.com/products/adobe-access.html). For more information please contact with sales department: "sales (at) castlabs (dot) com".

Please visit [CHANGELOG](https://github.com/castlabs/dashas/blob/master/CHANGELOG.md) for changes history.

For information on a digital rights management (DRM) enabled version, please visit the  [castLabs](http://castlabs.com/products/dashas/) website.

## Demo

For more information visit [examples page](http://dashas.castlabs.com/demo/try.html).

## Features

* An OSMF extension;
* Basic support for manifests with a list [1];
* Basic support for manifests with a template [1];
* Basic support for manifests with a base URL [1][2];
* Basic support for manifests with a time line;
* Audio-video playback [3];
* Adaptive bitrate streaming;
* Live streaming;
* User interface:
	* Play/Pause;
	* Seeking;
	* Fullscreen mode;
	* Duration;
	* Loaded bytes indicator;
	* Buffering indicator;

[1] A server has to have [crossdomain.xml](https://github.com/castlabs/dashas/blob/master/utils/crossdomain.xml) file in the root context.  
[2] A server has to have custom [.htaccess](https://github.com/castlabs/dashas/blob/master/utils/.htaccess) file in the root context.  
[3] Adobe Flash Player supports only [H.264 (MPEG-4 Part 10)  and HE-AAC (MPEG-4 Part 3) codecs](http://helpx.adobe.com/flash/kb/supported-codecs-flash-player.html).

For more information about which attributes and tags are required in a manifest please visit [unit tests](https://github.com/castlabs/dashas/blob/master/src/test/actionscript/com/castlabs/dash/ManifestHandlerTest.as).

## Quick start

### Input files

Create media input files in different bitrates.

#### Prerequisites

* ffmpeg is installed with libfdk_aac and libx264 library (tested with version 2.1.3);


#### Instructions

1. Download source file:

		$ wget http://mirrorblender.top-ix.org/movies/sintel-1024-surround.mp4

1. Create video file encoded at 250k 436p, H.264 codec:

		$ ffmpeg -i sintel-1024-surround.mp4 -an -b:v 250k -s 1024x436 -vcodec libx264 avc1-sintel-436p-250k.mp4

1. Create video file encoded at 500k 436p, H.264 codec:

		$ ffmpeg -i sintel-1024-surround.mp4 -an -b:v 500k -s 1024x436 -vcodec libx264 avc1-sintel-436p-500k.mp4
		
1. Create video file encoded at 1000k 436p, H.264 codec:

		$ ffmpeg -i sintel-1024-surround.mp4 -an -b:v 1000k -s 1024x436 -vcodec libx264 avc1-sintel-436p-1000k.mp4
		
1. Create audio file encoded at 69k stereo, HE-AAC codec:

		$ ffmpeg -i sintel-1024-surround.mp4 -vn -ac 2 -acodec libfdk_aac -profile:a aac_he -f mp4 mp4a-sintel-69k.mp4
		
		
### Stream
		
Convert media input files into DASH stream files.
		
#### Prerequisites

* MP4Box is installed (tested with version 0.5.1r5152);

#### Instructions

* Use following command to generate DASH stream:
		
		$ MP4Box -dash 3000 -bs-switching no -segment-name "%s_" -url-template -out sintel.mpd avc1-sintel-436p-250k.mp4 avc1-sintel-436p-500k.mp4 avc1-sintel-436p-1000k.mp4 mp4a-sintel-69k.mp4
		
### Page

Finally try dash.as locally.

#### Prerequisites

* apache2 is installed;

#### Instructions

1. Create a virtual host for localhost;

1. Copy DASH stream files into a document root;

1. Copy [crossdomain.xml](https://github.com/castlabs/dashas/blob/master/utils/crossdomain.xml) file into a document root (for more information visit [crossdomain page](https://github.com/castlabs/dashas/wiki/crossdomain));

1. Copy [.htaccess](https://github.com/castlabs/dashas/blob/master/utils/.htaccess) file into a document root (for more information visit [htaccess page](https://github.com/castlabs/dashas/wiki/htaccess));

1. Copy contents of the [demo](https://github.com/castlabs/dashas/tree/master/site/demo/) directory into a document root;

1. Create `index.html` file in a document root (type absolute URL to a `sintel.mpd` file):

		<html>
		<head>
		<script type="text/javascript" src="/swfobject/swfobject.js"></script>
		<script type="text/javascript">
		    var flashvars = {};
		
		    // absolut URL to sintel.mpd file
		    flashvars.src = encodeURIComponent("<absolute URL to sintel.mpd file>");
		
		    // absolut URL to dashas.swf file
		    flashvars.plugin_DashPlugin = encodeURIComponent(location.href + "/production/dashas.swf");
		
		    var params = {};
		    params.allowfullscreen = "true";
		    params.allownetworking = "true";
		    params.wmode = "direct";
		
		    swfobject.embedSWF("/StrobeMediaPlayback.swf", "placeholder", "640", "360", "10.1", "/swfobject/expressInstall.swf", flashvars, params, {});
		</script>
		</head>
		
		<body>
		<div id="placeholder">
		    <p><span>Please install <a href="http://get.adobe.com/flashplayer/">Adobe Flash Player</a></span></p>
		</div>
		</body>
		</html>
	
1. Go to `http://localhost/index.html`;

---

**TIP:** The flash is very aggressive in caching. If you notice that player doesn't respond to your server side changes, please clean browser's cache (i.e. cached images and files). 
	
## Development

At the moment project supports only Gradle 1.11. Please read [how to install Gradle 1.11 on Linux](https://github.com/castlabs/dashas/wiki/linux).

### Build

#### Prerequisites

* gradle is installed (tested with version 1.11);

#### Instructions

* Build debug SWF package:

		$ cd <project_workspace>
		$ gradle clean compile

* Build production SWF package:

		$ cd <project_workspace>
		$ gradle -Pprofile=production clean compile

... do you prefer developing in an IDE? Read how to [import project into the IntelliJ IDEA](https://github.com/castlabs/dashas/wiki/IntelliJ-IDEA).

### Test

#### Prerequisites

* gradle is installed (tested with version 1.11);

#### Instructions

1. Download and extract Flash Player:
    * [Windows](http://download.macromedia.com/pub/flashplayer/updaters/10/flashplayer_10_sa_debug.exe)
    * [Linux](http://download.macromedia.com/pub/flashplayer/updaters/10/flashplayer_10_sa_debug.tar.gz)
    * [OS X](http://download.macromedia.com/pub/flashplayer/updaters/10/flashplayer_10_sa_debug.app.zip)

1. Define global variable:
    
        $ export FLASH_PLAYER_EXE=Flash\ Player\ Debugger.app/Contents/MacOS/Flash\ Player\ Debugger
        
1. Run test suites:

        $ cd <project_workspace>
        $ gradle test
