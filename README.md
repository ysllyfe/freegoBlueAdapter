## 小程序JSSDK

引用如下：

1,使用1.1的接口取得{授权token},{版本}, {设备id}

2,初始化BlueAdapter

3,监听事件,在蓝牙连接成功后,显示开锁按钮

4,开锁按钮对应事件为openDoor

5,注意,页面退出时,请一定调用this.BlueAdapter.release();方法

蓝牙连接大概要1~2秒，在初始化后未连接前，可以显示1.2对应的离线密码

### 代码示例如下

```javascript
import { BlueAdapter } from 'pathto/blue_adapter'
Page({

  data: {
    start_connect: false,
    is_connect: false
  },

  onLoad: function (options) {
    wx.request({
      url: '{后端处理接1.1接口}',
      data: {
        device_id: 'F34479F6F935'
      },
      method: 'POST',
      header: {
        'content-type': 'application/json' // 默认值
      },
      success: (res)=>{
        const data = res.data;
        this.BlueAdapter = new BlueAdapter(data.device_id, data.ver, data.token, this.callback)
      }
    })
  },

  startConnect(){
    this.BlueAdapter.connectBluetooth()
    this.setData({
      start_connect: true
    })
  },

  openDoor(){
    this.BlueAdapter.openDoor();
  },

  callback: function(res){
    console.log(res)
    switch(res) {
      case "connect_success":
        this.setData({
          is_connect: true
        })
      break;
      default:
        console.log('事件通知:', res)
    }
  },

  onUnload: function () {
    this.BlueAdapter.release();
  }
})
```

### SDK相关事件说明

| 事件       | 事件说明     |  加入版本     |
| :------------- | :----------: | -----------: |
| cannot_open_blue_tooth_adapter  | 蓝牙未打开   | 1.0   |
| start_bluetooth_devices_discovery  | 开始蓝牙设备扫描 | 1.0 |
| stop_bluetooth_devices_discovery | 关闭蓝牙设备扫描 | 1.0 |
| found_device | 蓝牙扫描中找到设备 | 1.0 |
| use_cached_device_id | 使用已缓存设备ID | 1.0 |
| no_write_uuids | 无写入端口(系统错误) | 1.0 |
| close_connection | 断开蓝牙连接 | 1.0 |
| connect_success | 蓝牙连接成功（重要） | 1.0 |
| open_success | 开门成功 | 1.1 |
| open_error | 开门失败 | 1.1 |
| open_verify_error | 开门校验失败 | 1.1 |
| forceopen_success | 反锁开门成功 | 1.1 |
| forceopen_error | 反锁开门失败 | 1.1 |
| forceopen_verify_error | 反锁开门校验失败 | 1.1 |