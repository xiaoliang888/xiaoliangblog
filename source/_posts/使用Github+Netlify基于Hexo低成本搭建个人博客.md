---
title:使用Github+Netlify基于Hexo低成本搭建一个个人博客
date:2025-06-30 09:37:00
tags:
---

# 1.前言

本篇将会记录从零开始搭建一个博客的过程，实现了低代码，零成本。

首先介绍一下本次需要用到的框架（使用框架能避免从头开始写大量的代码，同时能提供方便的功能）：

Hexo ，一个快速、简介且高效的博客框架，可以使用Markdown解析文章，并且有很多主题可供挑选。

>静态博客的优点：所有的内容都会需先生成并存储为静态文件（HTML，css，js等），服务器负责直接返回文件给用户，没有后端数据库和动态内容生成。
>
>- 优点：高性能、低成本、易于维护
>- 缺点：缺乏用户之间的互动、不够灵活

本套方案只需要几行命令就能生成一个博客框架，并且基于 github +Netlify部署即可。无需关心网站代码实现细节，只需要专心写文章！

**事不宜迟，我们开始吧!**



在开始之前，你还需要准备一下的工具：

- 一个Github账号，一个Netlify账号（可以使用Github账号登录）
- 对Github、Netlify操作有一个基本概念
- 一双手 一个脑子 一台能上网的设备
- git、node.js



# 2.初始化Hexo博客并推送到Github

登录[Nodejs](https://nodejs.org/zh-cn/download)官网

![image-20250630102034047](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630102034047.png)

选择windows安装程序(.msi)，下载完成后进行安装。



安装完成后使用`npm -v`和`node -v`验证是否安装成功，看到以下输出即安装成功

```shell
D:\blog>node -v
v22.17.0

D:\blog>npm -v
10.9.2
```

安装完成后需要先切换国内镜像源，运行一下命令将npm默认源切换至淘宝源：

```shell
npm config set registry https://registry.npmmirror.com
```

执行完这个命令后，所有通过npm下载的包都会从淘宝镜像源获取。你可以通过以下命令确认是否切换成功：

```shell
npm config get registry
```

如果返回`https://registry.npmmirror.com`，说明配置成功。



**接下来开始安装`hexo-cli`**

在这之前，你可以在除了C盘以外的盘新建一个文件夹，并在cmd进入这个文件夹

运行如下命令安装`hexo-cli`

```shell
npm install -g hexo-cli
```

安装完成后，使用`hexo-cli`初始化一个hexo博客模板：

```html
#初始化一个为myblog的博客
hexo init myblog
#进入项目文件夹
cd myblog
#这里进不去的话，可以试试my-blog,或者进入你上面创建的文件夹看看叫什么名字
#安装依赖
npm install
```

**到这里呢，你已经初始化完成一个Hexo博客了**

接下来需要在编辑器里面打开myblog文件夹，我这里以Trae来示例

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630125356651.png" alt="image-20250630125356651" style="zoom: 67%;" />

> Trae 国内首款 AI 原生 IDE，专为中国开发者打造，让 AI 深度融入编程，带来比插件更流畅、精准的开发体验。通过强大的上下文分析，Trae 可以实时预测和续写你的代码片段，快速无缝扩展你未完成的代码，数倍提升你的编码效率。

这里我们只用它的git和编辑器功能就可以了，其他功能可以自行探索~

接下来需要进入`_config.yml`更改的几项信息：

>`title: Hexo ` 这里是你网页的标题，你可以修改你喜欢的标题
>
>`subtitle: ''` 这个是网站的副标题，效果类似这样：
>
><img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630130750924.png" alt="image-20250630130750924" style="zoom:50%;" />
>
>`author: John Doe`这个是作者的名字，可以修改为你自己的名字
>
>`language: zh-cn` 这里是网站的语言，默认为en
>
>`timezone: ''` 这个是时区设置，不填默认设置
>
>其他的需要的话可以去[帮助文档](https://hexo.io/docs/configuration.html)自行探索

上面的配置结束后，效果如图所示：

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630132544299.png" alt="image-20250630132544299" style="zoom: 67%;" />





接下来推荐一款插件RSS插件，**hexo-generator-feed**，使用如下指令安装这个插件：

`npm install hexo-generator-feed --save`

在`_config.yml`中加入如下内容 可以按需修改：

```yaml
feed:
 type: 
  \- rss2
 path: 
  \- rss2.xml
 limit: 30
 hub:
 content:
 content_limit: 160
 content_limit_delim: ' '
 order_by: -date
```

接下来去 Github 新建一个仓库，点击`new`按钮

![image-20250630140128610](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630140128610.png)

`Repository name`是仓库名，可以随便写，这里推荐用`Hexo-Scoure`，这样看起来好看而且还适合快速寻找，`Description`是仓库简介，可以留空，按自己要求来写就好了，仓库标记为public仓库，以免出现其他意外。

然后新建一个`.gitignore`文件里面写：

```shell
node_modules/
db.json
public/
```

在新建一个`netlify.toml`文件，里面写入：

```toml
[build]
  command = "npm run build"
  publish = "public"

[build.environment]
  NODE_VERSION = "22"  
  NPM_VERSION = "10"    

[dev]
  command = "hexo server"  
  publish = "public"
```

保存，接下来依次执行以下命令

```
#初始化仓库，一条一条执行
git init #初始化项目
git add . #添加所有文件到缓存区
git commit -m "first commit" #首次提交
git branch -M main 切换分支
git remote add origin <仓库地址> #添加远程仓库
git push -u origin main #推送文件
```

推送完成后跳转到你的Github仓库会看到已经上传成功了

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630163701280.png" alt="image-20250630163701280" style="zoom:67%;" />

# 3.将博客部署到Netlify

登录Netlify控制台，选择`Import from git`

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630165407717.png" alt="image-20250630165407717" style="zoom:67%;" />

选择`github`

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630165443476.png" alt="image-20250630165443476" style="zoom:67%;" />

接下来会显示授权，授权完成后，选择你创建的仓库，之后进行配置

![image-20250630170434716](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630170434716.png)

这里填写你的项目名称，同时也会是你的网页地址`Project name.netlify.app`

其他的按需填写，填完后直接点击`Deploy Project name`

这样项目就开始部署了，部署完成后点击`view project deploy`就可以预览你的博客啦:smile:

![image-20250630170744592](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630170744592.png)

# 4.配置主题

接下来是主题的安装，这里以[Redefine](https://github.com/evannotfound/hexo-theme-redefine)主题为例：

**安装主题**

打开Trae自带的终端，输入下面命令：

`npm install hexo-theme-redefine@latest`

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630132902715.png" alt="image-20250630132902715" style="zoom: 67%;" />

安装完成后，需要在`_config.yml`中将`theme:landscape`改为`theme:redefine`

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630133208202.png" alt="image-20250630133208202" style="zoom:67%;" />

> :bulb:题外话：下面的操作这里作者踩了个坑，直接修改了`hexo-theme-redefine`文件夹中的`_config.yml`文件,部署后网页的改动没有生效，后面查了操作文档才能正常部署。

在根目录创建一个`_config.redefine.yml`文件

![image-20250630135110295](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250630135110295.png)

并将以下代码复制进去**（注：里面部分代码需修改，可参考[快速开始 | Hexo Theme Redefine Docs](https://redefine-docs.ohevan.com/zh/getting-started)文档，或者代码块中对应的网址）**

```yaml
# >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# THEME REDEFINE CONFIGURATION FILE V2
# BY EVANNOTFOUND
# GITHUB: https://github.com/EvanNotFound/hexo-theme-redefine
# DOCUMENTATION: https://redefine-docs.ohevan.com
# DEMO: https://redefine.ohevan.com
# <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# BASIC INFORMATION >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/basic/info
info:
  # Site title
  title: 小亮的博客 #此处需要修改
  # Site subtitle
  subtitle: 
  # Author name
  author: 小亮 #此处需要修改
  # Site URL
  url: https://redefine.ohevan.com #此处需要修改
# BASIC INFORMATION <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# IMAGE CONFIGURATION >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/basic/defaults
defaults:
  # Favicon
  favicon: /images/catblog.svg #此处需要修改
  # Site logo
  logo: /images/catblog.svg #此处需要修改
  # Site avatar
  avatar: /images/catblog.svg #此处需要修改
# IMAGE CONFIGURATION <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# COLORS >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/basic/colors
colors:
  #Primary color
  primary: "#A31F34"
  # Secondary color (TBD)
  secondary:
  # Default theme mode initial value (will be overwritten by prefer-color-scheme)
  default_mode: light # light, dark
# COLORS <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# SITE CUSTOMIZATION >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/basic/global
global:
  # Custom global fonts
  fonts:
    # Chinese fonts
    chinese: 
      enable: false # Whether to enable custom chinese fonts
      family:  # Font family
      url:  # Font URL to CSS file
    # English fonts
    english: 
      enable: false # Whether to enable custom english fonts
      family:  # Font family
      url:  # Font URL to CSS file
    # Custom title fonts (navbar, sidebar)
    title:
      enable: false # Whether to enable custom title fonts
      family:  # Font family
      url:  # Font URL to CSS file
  # Content max width
  content_max_width: 1000px
  # Sidebar width
  sidebar_width: 210px
  # Effects on mouse hover
  hover:
    shadow: true # shadow effect
    scale: false # scale effect
  # Scroll progress
  scroll_progress:
    bar: false # progress bar
    percentage: true # percentage
  # Website counter
  website_counter:
    url: https://cn.vercount.one/js # counter API URL (no need to change)
    enable: true # enable website counter or not
    site_pv: true # site page view
    site_uv: true # site unique visitor
    post_pv: true # post page view
  # Whether to enable single page experience (using swup). See https://swup.js.org/. similar to pjax
  single_page: true
  # Whether to enable Preloader.
  preloader:
    enable: false
    custom_message: # Custom message. If empty, the site title will be displayed
  # Whether to enable open graph
  open_graph:
    enable: true
    image: /images/redefine-og.webp  # default og:image
    description: Hexo Theme Redefine, Redefine Your Hexo Journey.
  # Google Analytics
  google_analytics:
    enable: false # Whether to enable Google Analytics
    id:  # Google Analytics Measurement ID
# SITE CUSTOMIZATION <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end
    

# FONTAWESOME >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/basic/fontawesome
fontawesome: # Pro v6.2.1
  # Thin version
  thin: false
  # Light version
  light: false
  # Duotone version
  duotone: false
  # Sharp Solid version
  sharp_solid: false
# FONTAWESOME <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# HOME BANNER >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/home/home_banner
home_banner:
  # Whether to enable home banner
  enable: true
  # style of home banner
  style: fixed # static or fixed
  # Home banner image
  image: 
    light: /images/wallhaven-wqery6-light.webp # light mode
    dark: /images/wallhaven-wqery6-dark.webp # dark mode
  # Home banner title
  title: Theme Redefine #此处需要更改
  # Home banner subtitle
  subtitle:
    text: [:D 获取中...] # subtitle text, array
    hitokoto:  # 一言配置
      enable: enable # Whether to enable hitokoto
      show_author: false # Whether to show author
      api: https://v1.hitokoto.cn # API URL, can add types, see https://developer.hitokoto.cn/sentence/#%E5%8F%A5%E5%AD%90%E7%B1%BB%E5%9E%8B-%E5%8F%82%E6%95%B0
    typing_speed: 100 # Typing speed (ms)
    backing_speed: 80 # Backing speed (ms)
    starting_delay: 500 # Start delay (ms)
    backing_delay: 1500 # Backing delay (ms)
    loop: true # Whether to loop
    smart_backspace: true # Whether to smart backspace
  # Color of home banner text
  text_color: 
    light: "#fff" # light mode
    dark: "#d1d1b6" # dark mode
  # Specific style of the text
  text_style: 
    # Title font size
    title_size: 2.8rem
    # Subtitle font size
    subtitle_size: 1.5rem
    # Line height between title and subtitle
    line_height: 1.2
  # Home banner custom font
  custom_font: 
    # Whether to enable custom font
    enable: false
    # Font family
    family: 
    # URL to font CSS file
    url:
  # Home banner social links
  social_links:
    # Whether to enable
    enable: false
    # Social links style
    style: default # default, reverse, center
    # Social links
    links:
      github:  # your GitHub URL
      instagram: # your Instagram URL
      zhihu:  # your ZhiHu URL
      twitter:  # your twitter URL
      email:  # your email
      # ...... # you can add more
    # Social links with QRcode drawers
    qrs:
      weixin:  # your Wechat QRcode image URL
      # ...... # you can add more
# HOME BANNER <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# NAVIGATION BAR >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/home/navbar
navbar:
  # Auto hide navbar
  auto_hide: false
  # Navbar background color
  color:
    left: "#f78736" #left side 
    right: "#367df7"  #right side
    transparency: 35 #percent (10-99)
  # Navbar width (usually no need to modify)
  width:
    home: 1200px #home page
    pages: 1000px #other pages
  # Navbar links
  links:
    Home: 
      path: / 
      icon: fa-regular fa-house # can be empty
    Archives: 
      path: /archives 
      icon: fa-regular fa-archive # can be empty
    # Status: 
    #   path: https://status.ohevan.com/
    #   icon: fa-regular fa-chart-bar
    # About: 
    #   icon: fa-regular fa-user
    #   submenus:
    #     Me: /about
    #     Github: https://github.com/EvanNotFound/hexo-theme-redefine
    #     Blog: https://ohevan.com
    #     Friends: /friends
    # Links: 
    #   icon: fa-regular fa-link
    #   submenus:
    #     Link1: /link1
    #     Link2: /link2
    #     Link3: /link3
    # ...... # you can add more
  # Navbar search (local search). Requires hexo-generator-searchdb (npm i hexo-generator-searchdb). See https://github.com/theme-next/hexo-generator-searchdb
  search:
    # Whether to enable
    enable: true
    # Preload search data when the page loads
    preload: true
# NAVIGATION BAR <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# HOME PAGE ARTICLE SETTINGS >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/home/home
home:
  # Sidebar settings
  sidebar:
    enable: true # Whether to enable sidebar
    position: right # Sidebar position. left, right
    first_item: menu # First item in sidebar. menu, info
    announcement: # Announcement text
    show_on_mobile: true # Whether to show sidebar navigation on mobile sheet menu
    links:
      Archives: 
        path: /archives 
        icon: fa-regular fa-archive # can be empty
      # Tags: 
      #   path: /tags 
      #   icon: fa-regular fa-tags # can be empty
      # Categories: 
      #   path: /categories 
      #   icon: fa-regular fa-folder # can be empty
      # ...... # you can add more
  # Article date format
  article_date_format: auto # auto, relative, YYYY-MM-DD, YYYY-MM-DD HH:mm:ss etc.
  # Article excerpt length
  excerpt_length: 200 # Max length of article excerpt
  # Article categories visibility
  categories:
    enable: true  # Whether to enable
    limit: 3 # Max number of categories to display
  # Article tags visibility
  tags:
    enable: true  # Whether to enable
    limit: 3  # Max number of tags to display
# HOME PAGE ARTICLE SETTINGS <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# ARTICLE >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/posts/articles
articles:
  # Set the styles of the article
  style:
    font_size: 16px # Font size
    line_height: 1.5 # Line height
    image_border_radius: 14px # image border radius
    image_alignment: center # image alignment. left, center
    image_caption: false # Whether to display image caption
    link_icon: true # Whether to display link icon
    delete_mask: false # Add mask effect to <del> tags, hiding content by default and revealing on hover
    title_alignment: left # Title alignment. left, center
    headings_top_spacing: # Top spacing for headings from h1-h6
      h1: 3.2rem
      h2: 2.4rem
      h3: 1.9rem
      h4: 1.6rem
      h5: 1.4rem
      h6: 1.3rem
  # Word count. Requires hexo-wordcount (npm install hexo-wordcount). See https://github.com/willin/hexo-wordcount
  word_count:
    enable: false # Whether to enable
    count: false # Whether to display word count
    min2read: false # Whether to display reading time
  # Author label
  author_label: 
    enable: true # Whether to enable
    auto: false # Whether to automatically add author label, e.g. Lv1, Lv2, Lv3...
    list: []
  # Code block settings
  code_block:
    copy: true # Whether to enable code block copy button
    style: mac # mac | simple
    highlight_theme: # Color scheme for highlightjs code highlighting. For preview, see https://highlightjs.org/examples
      light: github # light mode theme, support: github, atom-one-light, default
      dark: vs2015 # dark mode theme, support: github-dark, monokai-sublime, vs2015, night-owl, atom-one-dark, nord, tokyo-night-dark, a11y-dark, agate
    font: # Custom font
      enable: false # Whether to enable
      family: # Font family
      url: # Font URL to CSS file
  # Table of contents settings
  toc:
    enable: true # Whether to enable TOC
    max_depth: 3 # TOC depth
    number: false # Whether to add number to TOC automatically
    expand: true # Whether to expand TOC
    init_open: true # Open toc by default
  # Whether to enable copyright notice
  copyright:
    enable: true # Whether to enable
    default: cc_by_nc_sa # Default license, can be cc_by_nc_sa, cc_by_nd, cc_by_nc, cc_by_sa, cc_by, all_rights_reserved, public_domain
  # Whether to enable lazyload for images
  lazyload: true
  # Pangu.js (automatically add space between Chinese and English). See https://github.com/vinta/pangu.js
  pangu_js: false
  # Article recommendation. Requires nodejieba (npm install nodejieba). Transplanted from hexo-theme-volantis.
  recommendation:
    # Whether to enable article recommendation
    enable: false
    # Article recommendation title
    title: 推荐阅读
    # Max number of articles to display
    limit: 3
    # Max number of articles to display mobile
    mobile_limit: 2
    # Placeholder image
    placeholder: /images/wallhaven-wqery6-light.webp
    # Skip directory
    skip_dirs: []
# ARTICLE <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# COMMENT >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/posts/comment
comment:
  # Whether to enable comment
  enable: false
  # Comment system
  system: waline # waline, gitalk, twikoo, giscus
  # System configuration
  config:
    # Waline comment system. See https://waline.js.org/
    waline:
      serverUrl: https://example.example.com # Waline server URL. e.g. https://example.example.com
      lang: zh-CN # Waline language. e.g. zh-CN, en-US. See https://waline.js.org/guide/client/i18n.html
      emoji: [] # Waline emojis, see https://waline.js.org/guide/features/emoji.html
      recaptchaV3Key: # Google reCAPTCHA v3 key. See https://waline.js.org/reference/client/props.html#recaptchav3key
      turnstileKey: # Turnstile key. See https://waline.js.org/reference/client/props.html#turnstilekey
      reaction: false # Waline reaction. See https://waline.js.org/reference/client/props.html#reaction
    # Gitalk comment system. See https://github.com/gitalk/gitalk
    gitalk:
      clientID: # GitHub Application Client ID
      clientSecret: # GitHub Application Client Secret
      repo: # GitHub repository
      owner: # GitHub repository owner
      proxy: # GitHub repository proxy
    # Twikoo comment system. See https://twikoo.js.org/
    twikoo:
      version: 1.6.10 # Twikoo version, do not modify if you dont know what it is
      server_url: # Twikoo server URL. e.g. https://example.example.com
      region: # Twikoo region. can be empty
    # Giscus comment system. See https://giscus.app/
    giscus:
      repo: # Github repository name e.g. EvanNotFound/hexo-theme-redefine
      repo_id: # Github repository id
      category: # Github discussion category
      category_id: # Github discussion category id
      mapping: pathname # Which value to use as the unique identifier for the page. e.g. pathname, url, title, og:title. DO NOT USE og:title WITH PJAX ENABLED since pjax will not update og:title when the page changes
      strict: 0 # Whether to enable strict mode. e.g. 0, 1
      reactions_enabled: 1 # Whether to enable reactions. e.g. 0, 1
      emit_metadata: 0 # Whether to emit metadata. e.g. 0, 1
      lang: en # Giscus language. e.g. en, zh-CN, zh-TW
      input_position: bottom # Place the comment box above/below the comments. e.g. top, bottom
      loading: lazy # Load the comments lazily
# COMMENT <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# FOOTER >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/footer
footer:
  # Show website running time
  runtime: true # show website running time or not
  # Icon in footer, write fontawesome icon code here
  icon: '<i class="fa-solid fa-heart fa-beat" style="--fa-animation-duration: 0.5s; color: #f54545"></i>'
  # The start time of the website, format: YYYY/MM/DD HH:mm:ss
  start: 2025/6/29 11:45:14
  # Site statistics
  statistics: true # show site statistics or not (total articles, total words)
  # Footer message
  customize:
  # ICP record number. See https://beian.miit.gov.cn/
  icp:
    enable: false # Whether to enable
    number: # ICP record number
    url: # ICP record url
# FOOTER <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# INJECT >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/inject
inject:
  # Whether to enable inject
  enable: false
  # Inject custom head html code
  head: 
    -
    -
  # Inject custom footer html code
  footer:
    -
    -
# INJECT <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# PLUGINS >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/plugins
plugins:
  # RSS feed. Requires hexo-generator-feed (npm i hexo-generator-feed). See https://github.com/hexojs/hexo-generator-feed
  feed:
    enable: false # Whether to enable
  # Aplayer. See https://github.com/DIYgod/APlayer
  aplayer:
    enable: false # Whether to enable
    type: fixed # fixed, mini
    audios:
      - name: # audio name
        artist: # audio artist
        url: # audio url
        cover: # audio cover url
        lrc: # audio cover lrc
#      - name: # audio name
#        artist: # audio artist
#        url: # audio url
#        cover: # audio cover url
#        lrc: # audio cover lrc
      # .... you can add more audios here
  # Mermaid JS. Requires hexo-filter-mermaid-diagrams (npm i hexo-filter-mermaid-diagrams). See https://mermaid.js.org/
  mermaid:
    enable: false # enable mermaid or not
    version: "11.4.1" # default v11.4.1
# PLUGINS <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# PAGE TEMPLATES >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/page_templates
page_templates:
  # Friend Links page column number
  friends_column: 2
  # Tags page style
  tags_style: blur # blur, cloud
# PAGE TEMPLATES <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end


# CDN >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/cdn
cdn:
  # Whether to enable CDN
  enable: false
  # CDN Provider
  provider: npmmirror # npmmirror, zstatic, cdnjs, jsdelivr, unpkg, custom
  # Custom CDN URL
  # format example: https://cdn.custom.com/hexo-theme-redefine/${version}/source/${path}
  # The ${path} must leads to the root of the "source" folder of the theme
  custom_url: 
# CDN <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end

# DEVELOPER MODE >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> start
# Docs: https://redefine-docs.ohevan.com/developer
developer:
  # Whether to enable developer mode (only for developers who want to modify the theme source code, not for ordinary users)
  enable: false
# DEVELOPER MODE <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<< end

```

