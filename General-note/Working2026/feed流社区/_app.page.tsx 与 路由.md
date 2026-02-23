---
title: _app.page.tsx 与 路由
---
## _app.page.tsx - 全局应用配置组件

### 作用

Next.js 的 App 组件，用于：

- 全局配置和初始化
    
- 提供全局 Context
    
- 服务端数据预取
    
- 全局样式和主题设置
    

### 主要功能

1. #### 服务端初始化 (getInitialProps)
    

_app.page.tsxLines 273-373

```TypeScript
CustomApp.getInitialProps = async (appContext: AppContext): Promise<CustomAppInitialProps> => {
  // ... 初始化逻辑
  return {
    referer,
    currentUser,
    isDarkMode: getCookie(COLOR_MODE_COOKIE_KEY, appContext.ctx) === EnumColorMode.DARK,
    locale: getCookie(LOCALE_COOKIE_KEY, appContext.ctx) as EnumLocale,
    userAgent: appContext.ctx.req?.headers['user-agent'] || EMPTY_STRING,
    webid: getCookieWebid(appContext.ctx),
  };
};
```

  

功能：

- 用户认证：从多个 Cookie 源获取 token（USER_TOKEN_COOKIE_KEY、USER_TOKEN_COOKIE_NEW_KEY、platformTokenCookieKey），验证并获取用户信息
    
- URL token 处理：支持从 URL 参数获取 token（如 /vip-model-page?token=xxx）
    
- 用户标签：获取用户标签和收益中心状态
    
- 环境信息：读取暗色模式、语言、User-Agent、referer、webid
    

返回的 CustomAppInitialProps：

- currentUser: 当前用户信息（CustomAppCurrentUser | null）
    
- isDarkMode: 是否暗色模式
    
- locale: 语言设置
    
- userAgent: 用户代理字符串
    
- webid: Web ID
    
- referer: 来源页面
    

组件 Props (CustomAppProps)

_app.page.tsxLines 83-89

```TypeScript
const CustomApp = (props: CustomAppProps) => {
const isMobile = isMobileDevice();
  const [searchId, setSearchId] = useState<string>(UUIDv4());
  const updateSearchId = () => {
    setSearchId(UUIDv4());
  };
  const { Component, currentUser, isDarkMode, locale, pageProps, userAgent, router } = props;
```

包含：

- Component: 当前页面组件（CustomNextPage）
    
- currentUser: 用户信息
    
- isDarkMode: 暗色模式
    
- locale: 语言
    
- pageProps: 页面 props
    
- userAgent: 用户代理
    
- router: Next.js 路由对象
    

2. #### 全局 Context 提供
    

_app.page.tsxLines 232-268

```HTML
      <LocaleContext.Provider value={locale}>
      <UserAgentContext.Provider value={userAgent}>
          <ColorSchemeProvider colorScheme={colorScheme} toggleColorScheme={updateColorMode}>
            <ConfigProvider locale={i18n.AntdLocale} theme={antdTheme}>
              <MantineProvider theme={mantineTheme} withCSSVariables withGlobalStyles withNormalizeCSS>
                <CurrentUserContext.Provider value={currentUser}>
                  <SearchContext.Provider
                    value={{
                      searchId,
                      updateSearchId,
                    }}
                  >
                    <ModalsProvider>
                      <PageLayout {...pageProps}>
                        <Component {...pageProps} />
```

提供的 Context：

- LocaleContext: 语言
    
- UserAgentContext: 用户代理
    
- ColorSchemeProvider: 主题（Mantine）
    
- ConfigProvider: Ant Design 配置
    
- MantineProvider: Mantine UI 配置
    
- CurrentUserContext: 当前用户
    
- SearchContext: 搜索 ID
    
- ModalsProvider: 模态框管理
    

3. #### 其他功能
    

- 暗色模式切换：根据路由和 Cookie 设置主题
    
- 数据上报：页面展示、广告、关键词等
    
- 第三方集成：Howxm 客服、Zustand 状态监控（调试模式）
    
- 全局组件：图片详情、分享模态框、引导提示等
    
- 路由监听：处理图片详情模态框的关闭
    

---

## 文件二：index.page.tsx - 首页组件

### 作用

首页组件，负责：

- 首页内容展示
    
- 多标签页切换（模型、视频特效、灵感等）
    
- Banner 轮播
    
- 用户引导和兴趣标签
    

### 主要功能

1. #### 组件类型
    

index.page.tsxLine 97

```TypeScript
const HomePage: CustomNextPage<PropsWithCurrentUser<{}>> = () => {
```

- `CustomNextPage`: 扩展的 Next.js 页面类型
    
- `PropsWithCurrentUser<{}>`: 包含 currentUser 的 props
    

2. #### 路由与标签页逻辑
    

index.page.tsxLines 59-93

```TypeScript
// 方案3：动态路由处理函数
const getActiveFeedIndexFromPath = (pathname: string | null, isLogin: boolean, router?: any): string => {
  if (pathname === '/') {
    /*
      pc 用户
      已登录 进入第一个标签
      新用户 进入视频特效
      老用户未登录 进入图片模型
    */
    if (isLogin) {
      // 已登录 进入第一个标签
      return firstPageFeedList[0].name;
    } else {
      const isNotNewUser = localStorage.getItem('is_not_new_user_for_firstpage_default_tab');
      if (isNotNewUser) {
        // 老用户未登录，进入第一个标签
        return firstPageFeedList[0].name;
      } else {
        localStorage.setItem('is_not_new_user_for_firstpage_default_tab', '1');
        // 新用户 进入视频特效
        let aimPath = '/inspiration';
        // 判断当前url是否存在query，如果存在把query拼到aimPath上
        if (router && router.query && Object.keys(router.query).length > 0) {
          const queryString = new URLSearchParams(router.query as any).toString();
          aimPath = `${aimPath}?${queryString}`;
        }
        router.push(aimPath);
        return 'inspiration';
      }
    }
  }
  return firstPageFeedList.find((item) => item.route === pathname)?.name || '';
};
```

  

路由规则：

- 已登录用户 → 第一个标签页
    
- 新用户（未登录） → 跳转到 /inspiration（视频特效）
    
- 老用户（未登录） → 第一个标签页
    

支持的标签页：

- model: 模型列表
    
- image-template: 图片模板（已注释）
    
- video-effect: 视频特效
    
- inspiration: 灵感图片
    
- workflow: 工作流
    

3. #### 数据初始化
    

index.page.tsxLines 215-258

```TypeScript
useEffect(() => {
const initializeData = async () => {
      // 并行执行所有初始化API调用
      const [interestRes, bannersRes, videoModelsRes, imageModelsRes] = await Promise.allSettled([
        interestTagService.isSelectedInterestTag(),
        bannerService.getBanner(isMobile ? 29 : 30).catch(() => []),
        editorImageService.getVideoRecommendModels(),
        editorImageService.getModelCardConfig(),
      ]);
      // 处理兴趣标签结果
      if (interestRes.status === 'fulfilled' && interestRes.value.code === 0) {
        setSelectedInterestTag(interestRes.value.data);
      }
      // 处理banner结果
      if (bannersRes.status === 'fulfilled') {
        setBanners(bannersRes.value);
      }
      // 处理视频模型结果
      if (videoModelsRes.status === 'fulfilled' && videoModelsRes.value?.models?.length) {
        setVideoSupportModelList(videoModelsRes.value.models);
        setVideoModel(videoModelsRes.value.models);
      }
      // 处理图像模型结果
      if (imageModelsRes.status === 'fulfilled' && imageModelsRes.value?.modelCards?.length) {
        setImageSupportModelList(imageModelsRes.value.modelCards);
      }
    };
    initializeData();
    const handleWindowMessage = (event: MessageEvent) => {
      if (event.data === 'request-reload-models-feed') {
        setRerenderKey(Date.now());
      }
    };
    window.addEventListener('message', handleWindowMessage);
    return () => window.removeEventListener('message', handleWindowMessage);
  }, []);
```

#### 并行获取：

- 兴趣标签选择状态
    
- Banner（移动端 29，PC 30）
    
- 视频推荐模型
    
- 图片模型配置
    

4. #### 特殊 URL 参数处理
    

index.page.tsxLines 147-187

```TypeScript
  useEffect(() => {
      if (router.query?.forbidden) {
      if (!currentUser && router.query.forbidden === 'yes') {
        loginHandlers.open();
        setTimeout(() => {
          messageApi.open({
            type: 'error',
            content: '该账号尚未注册',
          });
        }, 100);
        window.history.replaceState({}, document.title, location.origin);
      }
      return;
    }
    if (router.query?.wxqq) {
      if (!currentUser && router.query.wxqq === 'yes') {
        window && window?._agl?.push(['track', ['success', { t: 3 }]]);
      }
      const oldToken = getCookie(USER_TOKEN_COOKIE_KEY);
      const newToken = getCookie(config.platformTokenCookieKey);
      if (oldToken && oldToken !== newToken) {
        setCookieLongTerm(`${config.platformTokenCookieKey}`, oldToken, {
          domain: 'liblib.art',
          secure: true, // 仅通过 HTTPS 传输
          sameSite: 'none',
        });
        reportLog({
          e: 'lib.login.success',
          var: {
            platform: 'liblib',
            login_type: 'qq',
          },
        });
      }
      window.history.replaceState({}, document.title, location.origin);
    }
  }, []);
```

  

- ?forbidden=yes: 未登录时打开登录弹窗并提示“该账号尚未注册”
    
- ?wxqq=yes: 处理 QQ/微信登录回调，同步 token 并上报登录成功
    

5. #### 关键词搜索处理
    

index.page.tsxLines 113-125

```TypeScript
  useEffect(() => {
  const keywordid = router.query.keywordid;
    if (keywordid) {
      request.get(`/api/canva/public/getKeyword?keywordId=${keywordid}`).then((res) => {
        if (res.code === 0 && res.data) {
          setSearchText(res.data as string);
          router.replace({
            query: omit(router.query, 'keywordid'),
          });
        }
      });
    }
  }, [router.query.keywordid]);
```

- 从 URL 的 keywordid 获取关键词并设置到搜索框，然后移除该参数
    

6. #### 主要内容渲染
    

index.page.tsxLines 330-367

```TypeScript
  // 渲染主要内容区域的函数，优化条件渲染逻辑
   const renderMainContent = useCallback(() => {
    if (!widthContainerRef.current) {
      return null;
    }
    const commonProps = {
      widthContainer: widthContainerRef.current,
    };
    if (isNative) {
      if (activeFeedIndex === 'model') {
        return <Models key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} isHome />;
      } else if (activeFeedIndex === 'video-effect') {
        return <VideoEffect key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} />;
      } else if (activeFeedIndex === 'inspiration') {
        return <Images key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} initialImages={[]} />;
      }
      return null;
    }
    // key 中加入 activeFeedIndex，确保 tab 切换时组件重新创建，从而重置筛选器状态
    switch (activeFeedIndex) {
      case 'model':
        return <Models key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} isHome />;
      // 2025-12-10: 暂时隐藏图片模板tab，对应的渲染逻辑也一并注释
      case 'image-template':
        return <Models key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} isHome isImageTemplate />;
      case 'video-effect':
        return <VideoEffect key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} />;
      case 'inspiration':
        return <Images key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} initialImages={[]} />;
      case 'workflow':
        return <Models key={`${rerenderKey}-${activeFeedIndex}`} {...commonProps} isWorkflows />;
      default:
        return null;
    }
  }, [activeFeedIndex, rerenderKey]);
```

根据 activeFeedIndex 渲染不同内容：

- model: 模型列表（Models，isHome）
    
- image-template: 图片模板（Models，isImageTemplate）
    
- video-effect: 视频特效（VideoEffect）
    
- inspiration: 灵感图片（Images）
    
- workflow: 工作流（Models，isWorkflows）
    

7. #### 兴趣标签弹窗
    

index.page.tsxLines 264-288

```TypeScript
  useEffect(() => {
  if (!currentUser || isSelectedInterestTag == null) {
      return;
    }
    if (isSelectedInterestTag === true) {
      return;
    }
    const userId = currentUser.id;
    const shouldOpen = shouldOpenInterestModal(userId);
    const openModal = async () => {
      const res = await interestTagService.getInterestTags();
      if (res.code === 0) {
        setInterestTagData(res.data);
        showInterestTagModal();
        updateOneOpen(userId);
      }
    };
    if (shouldOpen) {
      if (localStorage.getItem('cannotOpen')) {
        localStorage.removeItem('cannotOpen');
        return;
      }
      openModal();
    }
  }, [currentUser, isSelectedInterestTag]);
```

- 已登录且未选择兴趣标签时，根据规则弹出兴趣标签选择弹窗
    

### 主要状态和 Hooks

- rerenderKey: 强制重新渲染的 key
    
- banners: Banner 数据
    
- searchText: 搜索关键词
    
- activeFeedIndex: 当前激活的标签页
    
- isSelectedInterestTag: 是否已选择兴趣标签
    
- sidebarCollapsed: 侧边栏是否折叠
    

### 渲染结构

index.page.tsxLines 369-395

```TypeScript
  return (
  <BaseLayout topbarProps={topbarProps}>
      <div className={`w-full bg-background px-4 pt-0`} data-no-sidebar={sidebarCollapsed}>
        {!isNative && (
          <>
            {isMobile ? (
              <div className="pt-4">
                <H5Banner items={memoizedBanners as BannerItem[]} />
              </div>
            ) : (
              <CarouselBanner bannerInfo={memoizedBanners} onCardClick={handleCarouselClick} />
            )}
            <MemoizedLabelCardList router={router} />
          </>
        )}
        <FeedStrategyList activeFeedIndex={activeFeedIndex} onClick={handleFeedStrategyClick} />
        {/* 这个ref是给瀑布流计算宽度用的 */}
        <div ref={widthContainerRef} className="w-full" />
        {renderMainContent()}
      </div>
      <BackToTop />
      <SimpleGenerator />
      <ImageStyleModal />
      {loginContextHolder}
      {contextHolder}
    </BaseLayout>
  );
```

结构：

- BaseLayout: 基础布局（含 Topbar）
    
- Banner：移动端 H5Banner，PC 端 CarouselBanner
    
- LabelCardList: 标签卡片列表
    
- FeedStrategyList: 标签页切换列表
    
- renderMainContent(): 主要内容（根据标签页切换）
    
- 全局组件：BackToTop、SimpleGenerator、ImageStyleModal
    

---

## 总结

- _app.page.tsx：全局配置层，负责认证、主题、Context、全局组件
    
- index.page.tsx：首页业务层，负责内容展示、标签切换、用户引导
    

两者配合：_app 提供全局能力，index 使用这些能力实现首页功能。

这个项目的router在哪设置的

查找项目中路由的配置位置。

查看 pages 目录结构和路由相关配置：

路由配置位置如下：

  

## 路由配置位置

1. ### 文件系统路由（主要方式）
    

Next.js 使用文件系统路由，路由由 packages/liblib/src/pages/ 目录结构决定：

next.config.jsLine 27

pageExtensions: ['page.tsx'],

- 文件路径 → URL 路径
    
- pages/index.page.tsx → /
    
- pages/inspiration/index.page.tsx → /inspiration
    
- pages/message/[id].page.tsx → /message/:id（动态路由）
    
- pages/activities/[...slug].page.tsx → /activities/*（通配符路由）
    

### 2. Next.js 配置文件中的路由规则

在 packages/liblib/next.config.js 中配置了路由重写和重定向：

URL 重写（Rewrites）

next.config.jsLines 41-74

```TypeScript
  rewrites: async () => {
  return {
      beforeFiles: [
        {
          source: '/api/www/:path*',
          destination: `${nodeEnv === 'dev' ? config.apiHost : config.internalApiHost}/api/www/:path*`,
        },
        {
          source: '/gateway/:path*',
          destination: `${nodeEnv === 'dev' ? config.apiHost : config.internalApiHost}/gateway/:path*`,
        },
        {
          source: '/inspiration',
          destination: '/',
        },
        {
          source: '/workflows',
          destination: '/',
        },
        {
          source: '/image-model',
          destination: '/',
        },
        {
          source: '/video-effect',
          destination: '/',
        },
        {
          source: '/image-template',
          destination: '/',
        },
      ],
    };
  },
```

作用：

- API 代理：/api/www/* 和 /gateway/* 转发到后端
    
- 路径重写：/inspiration、/workflows 等重写到 /（首页通过 pathname 判断显示内容）
    

#### 重定向（Redirects）

next.config.jsLines 32-40

```TypeScript
  redirects: async () => {
  return [
      {
        source: '/activity/2',
        destination: 'https://www.liblib.art/activities/299e3e428b8f4a35be5a076dfc62521b/Original_Author_Revenue_Plan',
        permanent: true,
      },
    ];
  },
```

2. ### Router 对象的使用
    

Router 对象通过以下方式获取：

在页面组件中

```TypeScript
import { useRouter } from 'next/router';
const MyPage = () => {
  const router = useRouter();
  // router.pathname, router.query, router.push() 等
};
```

  

在 _app.page.tsx 中

_app.page.tsxLine 89

```TypeScript
const { Component, currentUser, isDarkMode, locale, pageProps, userAgent, router } = props;
```

Router 对象由 Next.js 自动注入到 _app 的 props 中。

3. ### 特殊路由文件
    

- _app.page.tsx：应用入口，所有页面都会经过它
    
- _document.page.tsx：自定义 HTML 文档结构
    
- 404.page.tsx：404 页面
    

## 总结

路由配置位置：

1. 文件系统路由：packages/liblib/src/pages/ 目录结构
    
2. 路由规则配置：packages/liblib/next.config.js 中的 rewrites 和 redirects
    
3. Router 对象：通过 useRouter() hook 或 _app 的 props 获取
    

不需要手动创建路由表，Next.js 会根据 pages 目录自动生成路由。