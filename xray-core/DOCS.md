# Home Assistant Add-on: v2fly-core


## 准备xray配置文件

电脑上打开v2rayN，点击菜单栏中的“参数设置”，点击“路由设置”，“域名策略”选“AsIs”
“预定义规则”选“绕过局域网及大陆地址”，之后点击确定。

在你想要使用的节点上单击右键，选择“导出所选服务器为客户端配置” 
将导出文件放在容器的/data/config.json里

```yaml
// Config file of V2Ray. This file follows standard JSON format, with comments support.
// Uncomment entries below to satisfy your needs. Also read our manual for more detail at
// https://www.v2fly.org/
{
  "log": {
    // By default, V2Ray writes access log to stdout.
    // "access": "/path/to/access/log/file",

    // By default, V2Ray write error log to stdout.
    // "error": "/path/to/error/log/file",

    // Log level, one of "debug", "info", "warning", "error", "none"
    "loglevel": "warning"
  },
  // List of inbound proxy configurations.
  "inbounds": [{
    // Port to listen on. You may need root access if the value is less than 1024.
    "port": 1080,

    // IP address to listen on. Change to "0.0.0.0" to listen on all network interfaces.
    "listen": "127.0.0.1",

    // Tag of the inbound proxy. May be used for routing.
    "tag": "socks-inbound",

    // Protocol name of inbound proxy.
    "protocol": "socks",

    // Settings of the protocol. Varies based on protocol.
    "settings": {
      "auth": "noauth",
      "udp": false,
      "ip": "127.0.0.1"
    },

    // Enable sniffing on TCP connection.
    "sniffing": {
      "enabled": true,
      // Target domain will be overriden to the one carried by the connection, if the connection is HTTP or HTTPS.
      "destOverride": ["http", "tls"]
    }
  }],
  // List of outbound proxy configurations.
  "outbounds": [{
    // Protocol name of the outbound proxy.
    "protocol": "freedom",

    // Settings of the protocol. Varies based on protocol.
    "settings": {},

    // Tag of the outbound. May be used for routing.
    "tag": "direct"
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],

  // Transport is for global transport settings. If you have multiple transports with same settings
  // (say mKCP), you may put it here, instead of in each individual inbound/outbounds.
  //"transport": {},

  // Routing controls how traffic from inbounds are sent to outbounds.
  "routing": {
    "domainStrategy": "IPOnDemand",
    "rules":[
      {
        // Blocks access to private IPs. Remove this if you want to access your router.
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      },
      {
        // Blocks major ads.
        "type": "field",
        "domain": ["geosite:category-ads"],
        "outboundTag": "blocked"
      }
    ]
  },

  // Dns settings for domain resolution.
  "dns": {
    // Static hosts, similar to hosts file.
    "hosts": {
      // Match v2fly.org to another domain on CloudFlare. This domain will be used when querying IPs for v2fly.org.
      "domain:v2fly.org": "www.vicemc.net",

      // The following settings help to eliminate DNS poisoning in mainland China.
      // It is safe to comment these out if this is not the case for you.
      "domain:github.io": "pages.github.com",
      "domain:wikipedia.org": "www.wikimedia.org",
      "domain:shadowsocks.org": "electronicsrealm.com"
    },
    "servers": [
      "1.1.1.1",
      {
        "address": "114.114.114.114",
        "port": 53,
        // List of domains that use this DNS first.
        "domains": [
          "geosite:cn"
        ]
      },
      "8.8.8.8",
      "localhost"
    ]
  },

  // Policy controls some internal behavior of how V2Ray handles connections.
  // It may be on connection level by user levels in 'levels', or global settings in 'system.'
  "policy": {
    // Connection policys by user levels
    "levels": {
      "0": {
        "uplinkOnly": 0,
        "downlinkOnly": 0
      }
    },
    "system": {
      "statsInboundUplink": false,
      "statsInboundDownlink": false,
      "statsOutboundUplink": false,
      "statsOutboundDownlink": false
    }
  },

  // Stats enables internal stats counter.
  // This setting can be used together with Policy and Api. 
  //"stats":{},

  // Api enables gRPC APIs for external programs to communicate with V2Ray instance.
  //"api": {
    //"tag": "api",
    //"services": [
    //  "HandlerService",
    //  "LoggerService",
    //  "StatsService"
    //]
  //},

  // You may add other entries to the configuration, but they will not be recognized by V2Ray.
  "other": {}
}

```

重启容器生效

容器日志出现

```yaml
[Warning] V2Ray 4.44.0 started
```
启动成功