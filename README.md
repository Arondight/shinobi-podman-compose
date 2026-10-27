# shinobi-podman-compose

## About

Shinobi is the Open Source CCTV Solution written in Node.JS. Designed with multiple account system, Streams by WebSocket, and Direct saving to MP4. Shinobi can record IP Cameras and Local Cameras.

## Usage

After deploy, visit [https://localhost:4000](https://localhost:4000) and [login to the Superuser panel](https://docs.shinobi.video/configure/first-login#link--your-first-login-to-the-superuser-panel-). Create an account, [login to the dashboard](https://docs.shinobi.video/configure/first-login#link--your-first-login-to-the-dashboard-), and [add cameras](https://docs.shinobi.video/configure/add-monitor-configuration).

## Stream Buffer

Update `shm_size` in [podman-compose.yaml](podman-compose.yaml) to set new stream buffer RAM size.

## Dashbord Language

The [only way](https://gitlab.com/Shinobi-Systems/Shinobi/-/work_items/555#note_2980844829) to set dashboard language is to change `conf.json`.

```diff
diff --color -Nur a/conf.json b/conf.json
--- a/conf.json 2026-04-07 01:55:12.654201086 +0800
+++ b/conf.json 2026-04-07 01:55:05.683030756 +0800
@@ -1,5 +1,6 @@
 {
    "port": 8080,
+   "language": "zh",
    "debugLog": false,
    "enableFaceManager": false,
    "videosDir": "__DIR__/videos",
```

> The Chinese translation is really terrible, and there are many `undefined` elements across the dashboard.

## Hardware Video Acceleration

I only tested it on my `Intel Core i9-12900H` with GPU `Intel Iris Xe Graphics 96EU Mobile`. In the `Monitor Settings` pages of dashboard, set `Accelerator` to `Yes`, set `HWAccel Device` to your DRM render node (`/dev/dri/renderD128` for the 1st, `/dev/dri/renderD122` for the 2nd, etc...), and set `HWAccel Format` to `vaapi`. Make sure the `ffmpeg` arguments includes something like `-hwaccel auto -hwaccel_device /dev/dri/renderD128 -hwaccel_output_format vaapi`.

## RTMP Server

To [push streams to Shinobi with RTMP](https://hub.shinobi.video/articles/view/nHglUO68J4NI2FE), run below command after shinobi startup. Then add a RTMP monitor in dashboard.

```bash
podman exec shinobi sh -c " \
    cd /home/Shinobi; \
    npm install discord.js; \
    node tools/modifyConfiguration.js rtmpServer='{\"port\":1935}'; \
    pm2 restart camera.js; \
"
```

You can use [OBS Studio](https://obsproject.com/) to streaming on port `4935`.

```text
rtmp://localhost:4935/[GROUP KEY]_[MONITOR ID]_[RTMP STREAM KEY]
```

> Where to [get your Group Key](https://hub.shinobi.video/articles/view/or1kfTcwpo7UivO).
