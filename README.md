# Clash-SpeedTest

基于 Clash 核心的测速工具，快速测试你的 Clash 节点速度。

Features:
1. 无需额外的配置，直接将 Clash 配置文件地址作为参数传入即可
2. 支持 Proxy 和 Proxy Provider 中定义的全部类型代理节点，兼容性跟 Clash 一致
3. 不依赖额外的 Clash 实例，单一进程即可完成测试
4. 代码简单而且开源，不发布构建好的二进制文件，保证你的节点安全

<img width="907" alt="image" src="https://user-images.githubusercontent.com/3659110/235856244-1a55b1c7-31c5-4663-a123-bb64c5b63503.png">

## 使用方法

```bash
# 从源码安装
> go install github.com/faceair/clash-speedtest@latest

# 查看帮助
> clash-speedtest -h
Usage of clash-speedtest:
  -c string
        specify configuration file
  -f string
        filter proxies by name, use regexp (default ".*")
  -s int
        download size for testing proxies (default 104857600)
  -t duration
        timeout for testing proxies (default 5s)

# 测试全部节点
> clash-speedtest -c ~/.config/clash/config.yaml
节点                     带宽         延迟
[GroupName] US 01      	26.13MB/s 	1173.00ms
[GroupName] US 02     	51.30MB/s 	3051.00ms
[GroupName] US 03      	26.49MB/s 	1226.00ms

# 测试香港节点，使用正则表达式过滤即可
> clash-speedtest -c ~/.config/clash/config.yaml -f 'HK|港'
```

## 速度测试原理

通过 HTTP GET 请求下载指定大小的文件，默认使用 https://speed.cloudflare.com/__down?bytes=104857600 (100MB) 进行测试，计算下载时间得到下载速度。

测试结果：
1. 带宽 是指下载指定大小文件的速度，即一般理解中的下载速度。当这个数值越高时表明节点的出口带宽越大。
2. 延迟 是指 HTTP GET 请求拿到第一个字节的的响应时间，即一般理解中的 TTFB。当这个数值越低时表明你本地到达节点的延迟越低，可能意味着中转节点有 BGP 部署、出海线路是 IEPL、IPLC 等。

请注意带宽跟延迟是两个独立的指标，两者并不关联：
1. 可能带宽很高但是延迟也很高，这种情况下你下载速度很快但是打开网页的时候却很慢，可能是是中转节点没有 BGP 加速，但出海线路带宽很充足。
2. 可能带宽很低但是延迟也很低，这种情况下你打开网页的时候很快但是下载速度很慢，可能是中转节点有 BGP 加速，但出海线路的 IEPL、IPLC 带宽很小。

## License

[MIT](LICENSE)