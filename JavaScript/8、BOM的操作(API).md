# BOM的操作(API)

1. 浏览器可视窗口尺寸(包含滚动条)
   1. window.innerWidth
   2. window.innerHeight
2. 浏览器地址栏-location
   1. window.location.href 获取当前窗口URL
   2. window.location.reload() 重载当前页面
3. 版本信息-navigator
   1. window.navigator.userAgent 获取userAgent字段
   2. window.navigator.appName 返回使用浏览器的名称，火狐、谷歌、Safari等都返回Netscape
   3. window.navigator.appVersion 返回同userAgent
   4. window.navigator.platform 返回当前使用系统平台
4. 历史记录-history
   1. window.history.back() 退回
   2. window.history.forward() 前进
5. 滚轮滚的高度
   1. document.documentElement.scrollTop（谷歌等通用）
   2. document.body.scrollTop(老版本IE)