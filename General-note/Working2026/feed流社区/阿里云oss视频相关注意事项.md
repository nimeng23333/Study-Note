---
title: 阿里云oss视频相关注意事项
---
## oss链接格式

本质：oss的url前缀都是一样的

目前看来一个已上传的视频只支持mp4、png、webp格式，不支持jpeg、jpg，要获取jpg只能用getVideoCover

```TypeScript
const url = "https://images-wm.liblib.cloud/sd-gen-save-img/genius_playground/video/4e7115e6ee6947b78d73e0203a85fc56/8cc340ee94352df47ef12b0b7ded0a0db7888c171c7b28cdff369f47c4f50dcf"

const mp4Url = url + ".mp4" // 原视频
const webpUrl = url + ".webp" //动图
// getVideoCover函数 本质
const videoCover = url + ".mp4?x-oss-process=video/snapshot,t_0,f_jpg,m_fast" // 获取压缩的封面。t单位为ms
// getFasterImage函数 本质
const fastImg = url + ".png?x-oss-process=image/resize,w_300,m_lfit/format,png" //获取图片的压缩格式
```

https://help.aliyun.com/zh/oss/user-guide/convert-image-formats-2

https://help.aliyun.com/zh/oss/user-guide/video-snapshots?spm=a2c4g.11186623.help-menu-31815.d_0_11_3_4.689c7e29xuYdfF&scm=20140722.H_64555._.OR_help-T_cn~zh-V_1