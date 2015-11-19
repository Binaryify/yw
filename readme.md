# AJAX
```js
function ajax(urll, Func, data) {
  $.ajax({
    url: urll + "&area=" + area,
    type: "POST",
    dataType: 'jsonp',
    jsonp: 'callback',
    data: data, //send data
    timeout: 3000,
    success: function(json) { //客户端jquery预先定义好的callback函数,成功获取跨域服务器上的json数据后,会动态执行这个callback函数
      Func(json) //callback
      netConnectStatus = true;
      $('.netErr').hide()
      $('.webContent').show()
    },
    error: function() {
      netConnectStatus = false;

      if (ajaxFailFlag <10) {
        console.log("AJAXerror")
        ajaxFailFlag++;
        setTimeout(function() {
          ajax(urll, Func, data)
        }, 300)
      } else {
        $('.netErr').show()
        $('.webContent').hide()
        console.log('server error')
      }
    }
  })
}
```
只要调用了ajax这个函数都会在url上带上area=当前区域
# 主页
```js
function loadtab1() {
  ajax("http://senguzh.com/ztf/yw100/server/indexnews.php?", function(data) {
    tab1.$data.newsListData = JSON.parse(data)
  }, "")
  ajax("http://senguzh.com/ztf/yw100/server/indexrent.php?", function(data) {
    tab1.$data.indexRentHouse = JSON.parse(data)
  }, "")
  ajax("http://senguzh.com/ztf/yw100/server/indexfirstHandHouse.php?", function(data) {
    tab1.$data.indexFirstHouse = JSON.parse(data)
  }, "")
  ajax("http://senguzh.com/ztf/yw100/server/indexsecondHandHouse.php?", function(data) {
    tab1.$data.indexSecondHouse = JSON.parse(data)
  }, "")
}
```


```js
ajax("http://senguzh.com/ztf/yw100/server/search.php?" + "search=" + searchFrameMain.$data.searchInput, loadSearch)
```
网页打开会触发5个AJAX,其中搜索只会发起1次(成功的时候)
主页会发起4个AJAX请求
分别对应:资讯头条,新房,二手房,租房

当点击资讯头条的里面的内容的时候,会发送那条新闻的id到newsdetail.php

```js
function loadnews(params) {
  if (params == undefined||"") {
    params = "null=null"
  }
  loadFunction = loadnews;
  loadParams = params;
  console.log(params)
  if (netConnectStatus == true) {
    setTimeout(function() {
      ajax("http://senguzh.com/ztf/yw100/server/news.php?" + params, function(json) {
        newsList.$data.newsListData = JSON.parse(json)
        loadDoSomething="newsList.$data.newsListData = dataArr"
        dataArr=JSON.parse(json);
        loadMoreURL="http://senguzh.com/ztf/yw100/server/news.php?" + params
      })
    }, 500)
  }
  hideSearch()
}
```

# 通用
当点击新房,二手房,租房里的区域,价格,面积,房龄,类型,特色的时候,会触发对应的
loadfirsthouse和loadsecondhouse以及loadrent方法
只是会传入对应的参数
## 例如:
```html
<li v-repeat="data in indexFirstHouse.area"><a href="#/tab/firstHandHouse" onclick="loadfirstHouse('city=(%data%)')">(%data%) </a></li>
```
```html
<li v-repeat="data in indexFirstHouse.money"><a href="#/tab/firstHandHouse" onclick="loadfirstHouse('money=(%data%)')">(%data%) </a></li>
```

```html
  <li v-repeat="data in indexFirstHouse.type"><a href="#/tab/firstHandHouse" onclick="loadfirstHouse('type=(%data%)')">(%data%) </a></li>
```

```html
<li v-repeat="data in indexFirstHouse.special"><a href="#/tab/firstHandHouse" onclick="loadfirstHouse('special=(%data%)')">(%data%) </a></li>
```

如果是详情列表,例如新闻,新房详情,二手房详情,租房详情,则传递具体的id(这个id是在服务器上定义的)
```html
<a href="#/tab/firstHandHouseDetail" onclick="loadfirstHouseDetail('(%id%)')">
```
#首页
1. 新房 :indexfirstHouse.php
2. 二手房 :indexsecondHandHouse.php
3. 租房 :indexrent.php
4. 新闻 :indexnew.php

# 新房
firstHandHouse.php
# 新房详情
firstHandHousedetail.php
# 二手房
secondHandHouse.php
# 二手房详情
secondHandHouseDetail.php
# 看房团
watchHouse.php
# 看房团详情
watchHouseDetail.php
# 小区
community.php
# 新闻
news.php
# 地图
map.php

# 搜索
search.php
当点击搜索的时候也是触发对应的load方法,参数为search=xxx
# 排序
当点击排序的时候会触发对应的load方法,参数为order="默认","价格","开盘","人气",up=false/true
# 刷新
重新加载对应的load方法,同时把全局保存的参数传入
# 滑动到底部加载
加载对应的load方法,同时传入page=1,2,3,4,5...
# 图片上传与加载
图片上传是把图片转化成base64码,每次添加图片都会在一个数组里push进base64码然后传到后台,后台需要解析这个数组,然后保存为图片
# 会员中心
登录需要token无法使用cookie

## 我的二手房
发送用户id
mySecondary.php
点击修改
changeReleaseSecondary.php
## 我的租房
发送用户id
myRentalHousing.php
点击修改
changeRentalHousing.php

## 我的收藏
collect.php
发送用户id
## 站内信
mail.php
发送用户id

# 头像上传
avatar.php
