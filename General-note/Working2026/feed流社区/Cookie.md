---
title: Cookie
---
## Cookie 工具函数

Cookie 写入的核心工具函数在：

cookies.tsLines 1-15

```TypeScript
import { getCookie, setCookie } from 'cookies-next';
import type { OptionsType } from 'cookies-next/lib/types';
import { WEBID_COOKIE_KEY } from '@/config';
import { createRandomId } from '@/utils/random';
export { deleteCookie, getCookie } from 'cookies-next';
export const setCookieLongTerm = (key: string, data: any, options: OptionsType = {}) => {
  setCookie(key, data, { ...options, maxAge: 60 * 60 * 24 * 365 * 10 });
};
export const setCookieSession = (key: string, data: any, options?: OptionsType) => {
  setCookie(key, data, { ...options });
};
```

  

说明：

- 使用 cookies-next 库
    
- setCookieLongTerm：设置长期 cookie（10 年）
    
- setCookieSession：设置会话 cookie
    

## Cookie Key 定义

Cookie key 定义在配置文件中：

context.tsLines 1-14

```TypeScript
/**
 * @description 全局用户凭据标识
 */
export const USER_TOKEN_COOKIE_KEY = 'usertoken';
export const USER_TOKEN_COOKIE_NEW_KEY = 'liblibai_usertoken';
/**
 * @description 主观登录标识
 */
export const LOGIN_PROUDLY_COOKIE_KEY = '__login_proudly';
settings.tsLines 1-14
/**
 * @description 全局语言标识
 */
export const LOCALE_COOKIE_KEY = '__locale';
/**
 * @description 全局主题标识
 */
export const COLOR_MODE_COOKIE_KEY = '__color_mode';
/**
 * @description 全局设备唯一标识
 */
export const WEBID_COOKIE_KEY = 'webid';
```

  

## 主要写入位置

1. ### 登录表单组件
    

手机号登录 (packages/liblib/src/components/login/LoginForm/index.tsx)：

index.tsxLines 220-236

```Java
setCookieLongTerm(USER_TOKEN_COOKIE_KEY, token, {
  secure: true, // 仅通过 HTTPS 传输
  sameSite: 'none',
});
// 给pc.liblib.art用
// setCookieLongTerm(`${USER_TOKEN_COOKIE_KEY}Ext`, token, {
//   domain: 'liblib.art',
//   secure: true, // 仅通过 HTTPS 传输
//   sameSite: 'none',
// });
// 打通token
setCookieLongTerm(${config.platformTokenCookieKey}, token, {
  domain: 'liblib.art',
  secure: true, // 仅通过 HTTPS 传输
  sameSite: 'none',
});
```

微信登录 (packages/liblib/src/components/login/LoginForm/index.tsx)：

index.tsxLines 454-476

```TypeScript
setCookieLongTerm(USER_TOKEN_COOKIE_KEY, userToken, {
    secure: true, // 仅通过 HTTPS 传输
      sameSite: 'none',
    });
    window.location.href = getJumpUrl(params);
  }
  return;
}
// 以下代码处理lib相关登录逻辑 start
if (isNewUser !== '1') {
  setCookieLongTerm(USER_TOKEN_COOKIE_KEY, userToken, {
    secure: true, // 仅通过 HTTPS 传输
    sameSite: 'none',
  });
}
// 打通token
setCookieLongTerm(${config.platformTokenCookieKey}, userToken, {
  domain: 'liblib.art',
  secure: true, // 仅通过 HTTPS 传输
  sameSite: 'none',
});
```

2. ### 应用初始化 (_app.page.tsx)
    

从 URL 参数设置 cookie：

_app.page.tsxLines 283-289

```TypeScript
if (token && /^\w+$/.test(token) && ['/vip-model-page'].includes(url.pathname)) {
  appContext?.ctx?.res?.setHeader(
    'Set-Cookie',
    `usertoken=${token}; Max-Age=360000; SameSite=None; Secure; Path=/`,
     );
  outerToken = token;
}
```

  

从其他平台 token 同步：

_app.page.tsxLines 303-310

```TypeScript
// 如果还是没有找到token，尝试从platformTokenCookieKey获取
 if (!usertoken && config.platformTokenCookieKey) {
  usertoken = getCookie(config.platformTokenCookieKey, appContext.ctx);
  if (usertoken) {
    setCookieLongTerm(USER_TOKEN_COOKIE_KEY, usertoken, {
      secure: true, // 仅通过 HTTPS 传输
      sameSite: 'none',
    });
  }
}
```

3. ### WebID Cookie 自动创建
    

cookies.tsLines 17-38

```TypeScript
export const getCookieWebid = (options?: OptionsType): string => {
const webid = getCookie(WEBID_COOKIE_KEY, options);
  if (!webid || typeof webid !== 'string') {
    const randomId = createRandomId();
    setCookieLongTerm(WEBID_COOKIE_KEY, randomId, {
      ...options,
      secure: true, // 仅通过 HTTPS 传输
      sameSite: 'none',
    });
    setCookieLongTerm(${WEBID_COOKIE_KEY}Ext, randomId, {
      ...options,
      domain: 'liblib.art',
      secure: true, // 仅通过 HTTPS 传输
      sameSite: 'none',
    });
    return randomId;
  }
  return webid;
};
```

## Cookie 设置特点

1. 安全配置：
    

- secure: true（仅 HTTPS）
    
- sameSite: 'none'（跨站支持）
    

2. 跨域设置：
    

- 主 cookie：当前域名
    
- Ext cookie：domain: 'liblib.art'（跨子域共享）
    

3. 有效期：
    

- 长期 cookie：10 年（maxAge: 60 * 60 * 24 * 365 * 10）
    
- 会话 cookie：浏览器关闭后失效
    

## 其他写入位置

- packages/liblib/src/components/login/LoginFormMobile/index.tsx - 移动端登录
    
- packages/liblib/src/components/login/BindPhone/index.tsx - 绑定手机号
    
- packages/liblib/src/pages/index.page.tsx - 首页 token 同步
    
- 各种活动页面（zmxt, ljzgh, hngd 等）
    

总结：Cookie 写入主要通过 setCookieLongTerm 和 setCookieSession，主要在登录成功时设置用户 token，并在应用初始化时进行 token 同步和验证。

  

## WebID Cookie 的作用

webid cookie 是设备的唯一标识符，用于在未登录时追踪设备和用户行为。

1. ### 设备唯一标识
    

settings.tsLines 11-14

```TypeScript
/**
 * @description 全局设备唯一标识
 */
export const WEBID_COOKIE_KEY = 'webid';
```

  

2. ### 自动生成机制
    

首次访问时，如果不存在会自动生成并保存：

cookies.tsLines 17-38

```TypeScript
export const getCookieWebid = (options?: OptionsType): string => {
const webid = getCookie(WEBID_COOKIE_KEY, options);
  if (!webid || typeof webid !== 'string') {
    const randomId = createRandomId();
    setCookieLongTerm(WEBID_COOKIE_KEY, randomId, {
      ...options,
      secure: true, // 仅通过 HTTPS 传输
      sameSite: 'none',
    });
    setCookieLongTerm(${WEBID_COOKIE_KEY}Ext, randomId, {
      ...options,
      domain: 'liblib.art',
      secure: true, // 仅通过 HTTPS 传输
      sameSite: 'none',
    });
    return randomId;
  }
  return webid;
};
```

特点：

- 自动生成随机 ID
    
- 保存 10 年（长期 cookie）
    
- 跨域设置（domain: 'liblib.art'）
    

3. ### 主要使用场景
    

#### 在 HTTP 请求中作为 Header

所有 API 请求都会携带 webid：

request.tsLines 12-33

```TypeScript
getExtraHeaders: () => {
  const webid = getCookieWebid();
    const defaultValue: Record<string, string> = { webid };
    if (typeof window === 'undefined') {
  return defaultValue;
    }
    const usertoken = getCookie(USER_TOKEN_COOKIE_KEY) || getCookie(config.platformTokenCookieKey);
    if (!usertoken) {
      return defaultValue;
    }
    if (typeof usertoken !== 'string') {
      return defaultValue;
    }
    return {
      token: usertoken,
      webid,
    };
},
```

  

#### 作为 CID（客户ID）用于数据追踪

广告参数关联：

useCidHook.tsLines 7-24

```TypeScript
/**
* 负责将Url上的参数与cid做关联
*/
export const useCidHook = () => {
  const router = useRouter();
  useEffect(() => {
    const query = router.query;
    const { sourceId, keywordid } = query;
    relateCid({
      cid: getCookieWebid(),
      ...query,
      adSourceId: sourceId,
      adKeywordId: keywordid,
    });
  }, []);
};
```

数据上报：

logReporter.tsLines 129-138

```TypeScript
const baseInfo: ReportBaseInfo = {
    cid: getCookieWebid(),
    uuid: info.uuid || userUuid || '',
    ct: Date.now(),
    pageUrl: location.href,
    sys: 'COMMUNITY',
    abtest,
    referer: window?.NEXT_DATA?.props?.referer,
    env: window?.SERVER_ENV,
};
```

  

#### 在业务接口中作为参数

- 搜索接口：传递 cid 参数
    
- 模型列表：用于统计和推荐
    
- 图片/视频操作：收藏、点赞等行为追踪
    
- 登录相关：作为客户端标识
    

4. ### 在应用初始化时获取
    

_app.page.tsxLine 371

```TypeScript
webid: getCookieWebid(appContext.ctx),
```

## 总结

webid cookie 的主要用途：

1. 设备追踪：作为设备唯一标识，即使未登录也能识别设备
    
2. 数据统计：用于用户行为分析、访问统计、A/B 测试等
    
3. 广告追踪：关联广告来源、关键词等营销数据
    
4. 接口调用：在所有 API 请求中作为 header 或参数传递
    
5. 用户体验：支持个性化推荐和内容优化
    

与 usertoken 的区别：

- usertoken：用户登录后的身份标识（登录后才有）
    
- webid：设备标识（首次访问即自动生成，无需登录）
    

两者配合使用，可以同时追踪设备维度和用户维度的数据。