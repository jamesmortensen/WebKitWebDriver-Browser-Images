# Epiphany and WebKitWebDriver on Docker

Cross browser testing in Safari requires expensive Apple hardware and can be difficult to automate without a macOS operating system.  But what's most important is the core browser engine underneath the browser UI.  While we cannot run Safari on Linux, there are a few WebKit based alternatives which do run on Linux and which use the same underlying browser engines, WebKit.

The container image runs the following services when started and listen on the corresponding ports:

- xvfb and fluxbox
- VNC - 5900
- noVNC - 7900
- WebKitWebBrowser - 4444

NOTE: VNC/noVNC password is 'secret'


## Here's how to start the container:

```
docker run --rm -it --shm-size=3g -p 7900:7900 -p 4444:4444 -p 5900:5900 jamesmortensen/webkitwebdriver-epiphany:latest
```

To view the Debian desktop, visit http://localhost:7900 in your browser, or connect via VNCViewer to 127.0.0.1:5900

To run Selenium tests against the Epiphany browser, use the following configuration for your capabilities:

**Configuration example for WebdriverIO:**
```
    capabilities: [{
        maxInstances: 1,
        browserName: 'Epiphany',
        browserVersion: '3.38.2',
        'webkitgtk:browserOptions': {
            args: [
                '--automation-mode'
            ],
            binary: 'epiphany'
        }
    }],
};
```

The WebKitWebDriver package also comes bundled with MiniBrowser, a very lightweight build of WebKitGTK2. To run the tests against the WebKitGTK2 MiniBrowser, use the following capabilities:

```
capabilities: [{
        maxInstances: 1,
        browserName: 'MiniBrowser',
        browserVersion: '2.34.1',
        'webkitgtk:browserOptions': {
            args: [
                '--automation'
            ],
            binary: `/usr/lib/${ARCH}-linux-gnu/webkit2gtk-4.0/MiniBrowser`
        }
    }],
```

NOTE:  ${ARCH} must be replaced with either x86_64 or arm64, depending on your platform, as the location of MiniBrowser varies by architecture.  Type `arch` in the terminal to see which one you're running, if you're not sure.  arm64 or aarch64 means you're running on an arm64 platform.


## Viewing the tests 

To see the tests while they run in the browser, navigate to http://localhost:7900 in your browser. The password for noVNC is "secret".


## Manual Testing

**Launching Epiphany or MiniBrowser:**

- From within the container, via VNC/noVNC, right click and select "Application -> Shells -> Bash". This opens a terminal.
- Type `epiphany` and press enter to launch the Epiphany Browser.
- Type `/usr/lib/x86_64-linux-gnu/webkit2gtk-4.0/MiniBrowser` to launch the MiniBrowser.

NOTE: If you're running on an arm64 platform, such as the Mac M1, replace x86_64 with aarch64.


## Build the container image for both amd64 and arm64

```
docker buildx build --platform linux/amd64,linux/arm64 -f Dockerfile.epiphany -t jamesmortensen/webkitwebdriver-epiphany:latest .
```


## Pushing the image to DockerHub

```
docker buildx build --platform linux/amd64,linux/arm64 -f Dockerfile.epiphany -t jamesmortensen/webkitwebdriver-epiphany:latest --push .
```


## Find the container image on Docker Hub

[https://hub.docker.com/repository/docker/jamesmortensen/webkitwebdriver-epiphany](https://hub.docker.com/repository/docker/jamesmortensen/webkitwebdriver-epiphany)
