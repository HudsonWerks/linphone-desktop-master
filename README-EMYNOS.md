Please first read README.md before reading this document.

The extension is developed by the EMYNOS consortium[1], based on EENA NG112 LTD [7].

## Extra instructions for installing Linphone in Linux and Windows

### Windows

Please read **Software to install** and **General rules for compilation** in `submodules/linphone/README.mingw` for extra components required for compiling in Windows.

It is possible to have errors related to SAFESEH, you may set the linker properties to disable this function. If you are more comfort to **x86 Native Tools Command Prompt**, you may follow these instructions to compile the project:

    SET LINK=/SAFESEH:NO
    SET _LINK_=/SAFESEH:NO
    msbuild WORK\desktop\cmake\Project.sln

### Linux

Here is the command line to get all extra dependencies required in Ubuntu and Debian, this is tested in Ubuntu 16.04 and should work for Ubuntu 16.04+.

    $ sudo apt install -y git cmake build-essential pkg-config intltool yasm libtool-bin libx11-dev libasound2-dev openjdk-8-jdk libgtk2.0-dev libnotify-dev libxv-dev nasm libspeexdsp-dev libavcodec-dev libswscale-dev libgl1-mesa-dev libglew1.6-dev libv4l-dev libxml2-dev
    + for optional library
    $ sudo apt install -y libreadline-dev libgsm1-dev libtheora-dev libsqlite3-dev libupnp4-dev libsrtp-dev

### Google Maps services

Our extension uses Google Static Maps and Geocoding APIs to provide certain functions. Please create an API key from [here](https://developers.google.com/maps/documentation/static-maps/get-api-key), and add the key and the length of the key to `submodules/linphone/includes/google.h`.

## Running Linphone

After compilation, binaries and other required files are inside `OUTPUT/desktop`. You may run Linphone by 

 - running `bin/linphone` (Linux)
 - use the shortcut inside the folder (Windows)

### Emergency call

Before testing emergency call, please set the **Location** preferences in the **Settings** window. In **Location** tab, choose

 - Retrieval methods that your network can support (DHCP[2, 3, 4], HELD[5], LLDP-MED[6])
 - Provide **by value** or **by reference** location when using HELD
 - Format of the location retrieved (**geodetic**, **civic address**)

If your network does NOT support any retrieval methods and you are dialing emergency call in a **testing network**, please

 - DO NOT tick any retrieval methods
 - Choose at least one format

You may make an emergency call in Linphone by calling **urn:service:sos**.

### PSAP interface

In addition to the normal Linphone interface, there is an extra interface for emergency call taker that can visualize the location information of the caller. The interface file is in `submodules/linphone/gtk/main_psap.ui`, if you want to test this interface, please copy the interface file to `OUTPUT/desktop/share/linphone` and change the name to `main.ui`. Please do backup the original `main.ui` if you need it.

### Simple scenarios

#### Two clients (without SIP proxy)

In this scenario, we are having two SIP clients that are **connected to the same LAN**, or both having **public IPs** (not behind **NAT**).

![alt text][diagram1]

[diagram1]: https://git.emynos.pasiphae.eu/emynos/linphone-desktop/raw/master/images/no_sip_proxy.png "Network diagram"

##### Client configuration (10.0.0.2)

 1. Change **My current identity** to the default one: **sip:[username]@10.0.0.2:5060 (Default)**, this should be the only identity if you open Linphone for the first time, i.e. no existing accounts
 2. Call the other side by using **sip:10.0.0.3**

#### Emergency call 

In this scenario, we are having a caller and a call taker connected to an "Emergency Services Routing Proxy" (ESRP) (defined in EENA NG112 LTD [7]):

![alt text][diagram2]

[diagram2]: https://git.emynos.pasiphae.eu/emynos/linphone-desktop/raw/master/images/simple_scenario.png "Network diagram"

##### Call taker side

 1. Change to **PSAP interface** so that the caller's location can be visualized
 2. Set the account up:
	 3. If you are using the **Account setup assistant**, please choose **I have already a sip account and I just want to use it** and fill in these information in the next page: 
		 4. Username: psap
		 5. Password: [password]
		 6. Domain: esrp.emynos.eu
	 7. If you are using the **Configure a SIP account** window, please fill in these blanks and tick **Register**:
		 8. Your SIP identity: sip:psap@esrp.emynos.eu
		 9. SIP Proxy address: sip: esrp.emynos.eu
	 3. In the main window, you should see a tick next to the account you configured if it is connected

##### Caller side

 1. Set the account up:
	 3. If you are using the **Account setup assistant**, please choose **I have already a sip account and I just want to use it** and fill in these information in the next page: 
		 4. Username: caller
		 5. Password: [password]
		 6. Domain: esrp.emynos.eu
	 7. If you are using the **Configure a SIP account** window, please fill in these blanks and tick **Register**:
		 8. Your SIP identity: sip:caller@esrp.emynos.eu
		 9. SIP Proxy address: sip: esrp.emynos.eu
	 3. In the main window, you should see a tick next to the account you configured if it is connected
 2. Follow the **Emergency call** part to set how should Linphone gets the location. In this simple scenario, you may set like this:
	 3. Do not tick any **Retrieval method**
	 4. Choose "By value"
	 5. Choose "Geodetic coordinates" in **Format**
 3. Make an emergency call by calling **urn:service:sos**


----------
- [1] EMYNOS: https://www.emynos.eu/
- [2] RFC 4776: https://tools.ietf.org/html/rfc4776
- [3] RFC 5986: https://tools.ietf.org/html/rfc5986
- [4] RFC 6225: https://tools.ietf.org/html/rfc6225
- [5] RFC 5985: https://tools.ietf.org/html/rfc5985
- [6] ANSI/TIA-1057
- [7] Next Generation 112 Long Term Definition standard for emergency services document: http://www.eena.org/ressource/static/files/2013-03-15-eena_ltd_v1-1_final.pdf
