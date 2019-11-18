# HLSCachingReverseProxyServer

![Swift](https://img.shields.io/badge/Swift-5.1-orange.svg)
[![CocoaPods](http://img.shields.io/cocoapods/v/HLSCachingReverseProxyServer.svg)](https://cocoapods.org/pods/HLSCachingReverseProxyServer)
[![Build Status](https://github.com/StyleShare/HLSCachingReverseProxyServer/workflows/CI/badge.svg)](https://github.com/StyleShare/HLSCachingReverseProxyServer/actions)
[![CodeCov](https://img.shields.io/codecov/c/github/StyleShare/HLSCachingReverseProxyServer.svg)](https://codecov.io/gh/StyleShare/HLSCachingReverseProxyServer)

A simple local reverse proxy server for HLS segment cache.

## How it works

![HLSCachingReverseProxyServer Concept](https://user-images.githubusercontent.com/931655/69081879-45206a80-0a82-11ea-8fca-3c09f3b1ebb1.png)

1. **User** sets a reverse proxy url to the `AVPlayer` instead of the origin url.
    ```diff
    - https://example.com/vod.m3u8
    + http://127.0.0.1:8080/vod.m3u8?__hls_origin_url=https://example.com/vod.m3u8
    ```
2. **AVPlayer** requests a playlist(`.m3u8`) to the local reverse proxy server.
3. **Reverse proxy server** fetches the origin playlist and replaces all URIs to point the localhost.
    ```diff
      #EXTM3U
      #EXTINF:12.000,
    - vod_00001.ts
    + http://127.0.0.1:8080/vod.m3u8?__hls_origin_url=https://example.com/vod_00001.ts
      #EXTINF:12.000,
    - vod_00002.ts
    + http://127.0.0.1:8080/vod.m3u8?__hls_origin_url=https://example.com/vod_00002.ts
      #EXTINF:12.000,
    - vod_00003.ts
    + http://127.0.0.1:8080/vod.m3u8?__hls_origin_url=https://example.com/vod_00003.ts
    ```
4. **AVPlayer** requests segments(`.ts`) to the local reverse proxy server.
5. **Reverse proxy server** fetches the origin segment and caches it. Next time the server will return the cached data for the same segment.

## Usage

```swift
let server = HLSCachingReverseProxyServer()
server.start(port: 8080)

let playlistURL = URL(string: "http://devstreaming.apple.com/videos/wwdc/2016/102w0bsn0ge83qfv7za/102/0640/0640.m3u8")!
let reverseProxyURL = server.reverseProxyURL(from: playlistURL)!
let playerItem = AVPlayerItem(url: reverseProxyURL)
self.player.replaceCurrentItem(with: playerItem)
```

## Dependencies

* [GCDWebServer](https://github.com/swisspol/GCDWebServer)
* [PINCache](https://github.com/pinterest/PINCache)

## Installation

Use CocoaPods with **Podfile**:

```ruby
pod 'HLSCachingReverseProxyServer'
```

## Development

```console
$ make project
$ open HLSCachingReverseProxyServer.xcworkspace
```

## License

HLSCachingReverseProxyServer is under MIT license. See the [LICENSE](LICENSE) for more info.
