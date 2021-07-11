---
layout: post
title: "基本使用"
date:  2020-04-22 23:48:02 +0800
categories: notes uniapp base
tags: Uni-app 基础 使用
excerpt: "项目与页面结构"
---

## 项目基本结构

首先我们根据上一个文档中所写的文档格式，可以知道项目的基本结构是什么。我们开发不用管unpackage文件夹下面的编译出的文件。我们需要在pages文件夹中保存对应的页面文件就可以了。默认是一个页面被包含在一个同名文件夹中，当然你也可以不包含文件夹。index页面是默认进入的第一个页面。static文件夹就包含一些不用编译的静态文件，如一些JS文件，或者图片资源。App.vue是全局的Vue文件，包含每个页面的公有属性和公有的样式，类似微信小程序原生的全局文件。

```javascript
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

App.mpType = 'app'

const app = new Vue({
    ...App
})
app.$mount()
```

main.js是全局入口文件，其模式和Vue的入口文件main.js是一致的。

manifest.json是配置文件，如果你是使用HBuilderX它会为你提供可视化的配置界面。

pages.json是页面文件，提供程序路由。其中页面需要在pages数组中注册，具有path和style两个属性，前者表示路由，后者表示具体页面样式。而tabbar也在tabBar数组中定义，具体的API和微信原生类似。globalStyle数组中包含的是样式的全局值。

uni.scss代表的是全局的scss样式文件，你可以不使用import导入样式文件来使用这些全局样式，不过首先你应该去下载使用scss的插件。

&emsp;

## 安装scss插件

在HBuilderX中点击菜单栏中的“工具 – 插件安装 – scss/sass编译” 点击安装，如果成功了，就可以正常使用了。

![scss][scss]

![scssset][scssset]

你就可以在使用scss样式变量的页面中直接使用定义好的变量，不过你记得给style标签加上lang="scss"。

&emsp;



[scss]:data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAU0AAAFbCAIAAADeHUsVAAAgAElEQVR4Ae19TWxjx5VujfF6541jr0xRFuj8GJjFy8IxWxEsB1yMgYQvQWwCAtyYZ40X1qIxAkIHkDsAVwRiC7A5AINeyHhA6NeQAQH0C5LHBPAsCEMNjUyPF3mLB8T5IRSJ7JUTb7zrzZxz6v/+UJekdMlLnotGs27Vqb+v7lfnVN2rOv/w3e9+V/DFCDACC43AIwvdO+4cI8AIIALMc34OGIHFR4B5vvhjzD1kBJjn/AwwAouPAPN88ceYe8gIMM/5GWAEFh8B5vnijzH3kBFgnvMzwAgsPgLM88UfY+4hI8A852eAEVh8BJjniz/G3ENGgHnOzwAjsPgI/Le0u1iqdTZOyvWuqhduq0W3DcP27k6rL2MK2wfNSs5NjQj3Gra0iGSOyhIC+DisOE9Alto+121Nnefdw/ZWs1MTlpwOUYHYewG4VCpSPn9EmYJBNwPNDAPNfHcS0fMHP0kuXtcTdnEXzvBeT21cagIEUue56Ld2GvnO1nahC2q7tAHK/GRUO4vVTqeqBCKDPZu7sL1XEe1dMhY048vKcijVDrYLYCl067urB8297WNjNdj8HJoaAYI9B9zWuAsE/kyCjfwXehKeuiouYAwE0uc5NK5bp6cAnolqEfSstuFDzYYpodxyYvEhUkrdiTXB0q0KPGHS6MfwsL1vi+7Wd5Rgv3XUq1RvlVo0H5jcHJgegVKtiRNtWY6BLM8CP335XMKkCKTKc2PPoS0n5DPhqdX++SBXbXYqQgD9j/JNf+ku++goddVpZZKDbeBPGrn8mhBqqe/hQ2uHjZLo2mnAS+ebiRAobG/hCLgkN+VIPY+3NH5yyOQaqnG6XoVdGIoy8n4ABN1NHT+R7xIgkCrPSY+TUoaWKaXut9GPNLafEhqpz3EJMDjRtO6e9KpFsPndjQBbFcwnosJEt4BcRaiwuZ4Tg3M9An6RZJpF2e25SjVvrfw1PxffXRUCqfLcb7Sd4v14b+cmLOTpc6kXMH9hdUUMT88wSBfMGGe4XU/iob0gnAa2VgvR6l4Xwb9jIzC8OBs3T6/B66dxMRtffoY8VxtyF/Y9moBNG9ipOfHNacvlQPdAeMuLCigTubzHInEzbyv4uibWrPfK5JvrRWDEzODP8UW9HxuatK+3gYtR+gx5DgDSQtnsfcO40stTn+YiV6EVeyTew4vIaDfSLBYqzdq5fZvninD4KhCgxdD6ZqHVjzbdx67DbsPCXM3r87HxczPMjOdq6OglW7OTbzREtVqEqTq8i5Ncn6/EWuJyj93tOGz1jW9j+gXwnY8ALoaqFX6R4aMyH3fp83wtnxNgRxdhGV5HDLr1xkanWq16y3IXnGT6nLSJ3WBHm95ZEeBWMFRgLAVczdtNO7c2Dk+OgPw2odo5WLVrMTDS9sS+fasSPxePXS+uyPh1fDLYUuc57ZfJpTI8Ah36rBXUeLkLo0YrsNDyK6E+97fWwFoXWKBGAQpxTAXcGnY37bQU/06JAJrax7D9aVda7ujJKR3T3NgEVfqvYVSG4M5rgnKWV+QfZuOnASiOX66HOC134uAbud699so/X/ptuxo39dQknt+x9hHf2yzv05CpngdMtky1PfXGzojn19NP5O/6KdiMTavIgzWVy2V+QIKgZPGeR3GcUVsonsNbdLQTzN+xRAGBSp//IioKGY5bYAQWjOcLPFLcNUZgcgT4nInJseOcjEBWEGCeZ2WkuJ2MwOQIMM8nx45zMgJZQYB5npWR4nYyApMjwDyfHDvOyQhkBQHmeVZGitvJCEyOAPN8cuw4JyOQFQSY51kZKW4nIzA5AszzybHjnIxAVhBgnmdlpLidjMDkCDDPJ8eOczICWUEg6d+fP//881npEreTEVgeBO7fv5+ks/x3LElQYhlGINsIsN2e7fHj1jMCSRBgnidBiWUYgWwjwDzP9vhx6xmBJAgwz5OgxDKMQLYRYJ5ne/y49YxAEgSY50lQYhlGINsIMM+zPX7cekYgCQLM8yQosQwjkG0EmOfZHj9uPSOQBAHmeRKUWIYRyDYCzPNsjx+3nhFIggDzPAlKLMMIZBsB5nm2x49bzwgkQYB5ngQllmEEso3ATHkOXg87B9uFpAiieK2USBpFR8tGFBYRpSoD54uhdsZJx8Wjj8fRTUrUs8UWAqDDSM9blzPRyABoSc+ZCGS7ktvC5nqud9TqJy2sf3w6bFYPts92YvLAAGxdgFvkvui39tvrza3tQleJQlJVNMr1btLKouWwGPDObq5qB4pVl3TmLjtF1WjhYa8nisUcickMymG7zAj81z5eQ62ktByIH+Wb2XHzqjtO/Yvwca8Am/onHq64J8SpMq1GQpVuVd7QO8255mDqPJdPru2VyxQZa5Bw4bEZRKXZqTi3wj5IZxfD3PpmodWHqUMy/VapRdQurK6I3tHlJB9enLlFQ9g0ogjVQtPK5bqUwI7kj4IzR+lWBQiNncJuKF/rh/UWZNIZBMxGtpLC9l5FtHejJiAJle7e7upBc2/7OMETbAtPP2TaXNZgl2pmZkYsr2CyjemVqToKSy+PkUyhkVQXeOrWVSEchfQHMXWeI+D60fXAlzfwKDgkEIbzEaIYhSjmTRoq/IomOjD9qNfZKIluV6CSHZ6eaUGqpCdEsdo5WN3dF3tNJKe8zCSiqu7WYYQwgzQUoMKOFSZCUz4pXdjeKrqdK9XwuT6/1eloIwBmAJQvNg8EGR44L/QaLZiYglepBq1yCsPuVKp64gpKz8c9thkmrbLbn259J43GBeGKrzPVRuL4Dtv7etITIiU4gt2fCc+DjYi5J4rFpKnofmun7EgQF7aURkdM5SwqaX4cIhOw6HD1YE/sl8uYBhyGMM21genGVoEVtuiWphipz430Wl4MV5SF0ms0BLIchrhLzdAZUJ/LSQOshY0izBD2KdAVgWzVmzEwoXvY3mrKiUvLzdUvznLQG5fkpn3YeTk/EjxyWgTcYDHSOF2vQhJFGXk/AIIbJ0HTyRGJhMtJd4IzaGQuvwb2pdOGGQRnwnPQo3ZZG+r08IKi7JMRkghEyIdGRhIXAkovqDPRiB+cXqwUwRIA3o6jbvDJ1KoZK7QdQbMe5o2dHWw32fMgC8ty6Gmv3V6pKCMAMkA21UWkuRicBB8B6nmULd8/H4jK3BIdZ1MxOA/2Ro4LzY+IXtBuz1WqeWvVrknpsf6PhSuqlLQb2T3pVYvwvNfEiGkqqqFXHDcTnjvWaLA78Cgou91qTitjOGSjgiGy1qu1UreutKSawNUdiONQ946OxZa3yoeEtXxOyDkmWCjc53BbABpe1vaD0xZstNbQNitZEyR1TkaAzmDX5zjhOMsJmbVYbcJMEm3L41OztQovKKLJZOueVSi8v3FpS3oNPVKXikYJjIIrSh7iUm0kPAVnaMuQRhjx4Me09aqiZ8LzsRofTaIRRXTrjY2OZjpucwUWwDiBoJEu5xPUMY6GRrWsypYaGoxvSldGA5I1cn1OE4QpDEdVG6LDi1V4W6Tq8PU51BRSgPAswGIi/r3CPFiBI9AfN2kE6Xys0TKiwn2yXAbXuO2JlJ+ukVJj0bMBw78F+7Ot1OfpmfA8kd2uAAedNzw9dHAJZHaNdj1G3TpuTsM2L7yNitvMtrJyDa931v2nCKVgHw/nGpnBWhlaPVtDQW4I6HiYxWF7/CifG5y06rim1wluBllo8H+opJHvVPVeXTB5Tu9pVWF2Qa+gkRZrwH/E+nwcuGbWSLlZRFNXs3aeuhE/E56HuWSeCssoGUXrqVOTDIERma2YfKvWrKJ0sskTzfteY/diq+nY/LZAE4KR0pt1tGGvNxpC8w1s/osmteDEqnO9oLedWIk2w+VUVYl4JEboFtPI2QRwVVGtBDZH0mlKPFzB+mfYSGiKfGkSbFMK94+kUMfkVZDVDUvjveQfzUFlQMVOBxV5oz0E7R/6ki3cHppmB7BQxPkBNszjv7qDPT1xanbuga76Cthihe3tNShrKHonXVii4bWLdyqDXJKiciEzPNwi2iFswPc1XmNoBzFmpyuqjJTjgG0hxAFabwBoXruGdqFS9+ACjRE5juk2Elrhdh+1CT4S1wDA6CJnos8DpneghXozGqxl9TZ2rdZpdtZhXXMcEA3eAqq0mAY67RCWrW4LjW7c5A4pXJ2ZMhkFC8/Lrjhodjob4a/n4JnF4mHhDlvEhzp/xC9sDonG7vZeVX4zE1GUzHTJvhpMELBpV6W3/DiPoHET2reLqH52UWhqH8MWht3lcGGXOyeY5sYmaC1taV4q58F1HLXHKYtIs5GyTfKjCawdOp7QwLy0u2MJpO53CVVn+DMy02Ygndq6Rvo5L2Ewn7MBZjJggF5VIwMNW71keaMLUDKyHWinV+BrJalf3Vya/Sewp4d7aPLlmBLVZbkZ1EwioJ3rp7s78HGMbj4V5YuaaSfQy6CUdy+bHNFWT4pvCAEANuIdyNKCkzrPlxbpmI4jeXFeaIEJESOC0WD385M7Ap9gEoPlI8I89/GYwR0ZB5E2hdMY1PsrM3kj4zSCg5lFgHme2aHjhjMCiRGY7/32xN1gQUaAERiBAPN8BDicxAgsCALM8wUZSO4GIzACAeb5CHA4iRFYEASY5wsykNwNRmAEAszzEeBwEiOwIAgwzxdkILkbjMAIBJjnI8DhJEZgQRBgni/IQHI3GIERCDDPR4DDSYzAgiDAPF+QgeRuMAIjEGCejwCHkxiBBUGAeb4gA8ndYARGIMA8HwEOJzECs0fghRdeeO21127cuDFNU1I9Nwr/iBrPZjGX68HARFJAHiwEGdxzPkP5/eOHIs5biYhS9UBZofNG4qTj4pMc4YptTvqH41gPnk8bOGvOIBPbDiPhBEIdDEU4whycXwSef/75mzdvrqysvPXWWw8fPpysoany3D3mC59/cdKCA9xa1PLIp5C8q3i+LJyToeCp35us014ubIg7+cAR21O7QHUqkGeiRTtADFYtswX9REadhkUzgvEJ51QXJUzJ4OoNeukcT+/PkfLYaThCi86rd1CGzBHTD0VJ4aSTmNtIDo+BwN27d/P5/HPPPXfnzp2JqZ4qz6Fz8GiDhsZD1/D5sOdeoqMx75h2CQSe4pnvKP/G6KVInIyBEImGz0E29MKnHg/muzIXqPH0C5KXWAbtC7It2Dss0TqKNKl4lGEL7ijZOG3Vwl4HibOlGhgv5XpfFgDpWxf73nnXxm1rv7RRLXqHTdPR2kK4J7NTFPif7XbPMuHF1eCWxcCXX3755ptvvv3229NQPW2ek3pTvkroqTPPpKts3KdfGQHwDFejfQ7S2OGjK+bABar2skgzWmhhEHjM+srPI0YjRyNOpERCB3LF36LjKLwIMiIzLQGg6I2THYfkeEClx3LhuKCjA84dUiOnaX3leAPEiuTBs1nw4hoPWFZSpqd62jzHhxCdpTgPUtDUJMpq9ygwEqiQhDzi2Vu4wunnuSodIAzTwpEaMhCesQvU5M+OM8WpTDl30WALwv7lm2p1oUVITwPhfHvFv8MiyGGwEM5qBCJlIVQEybhuW8GLvCja8aEqjs9FxXpwRMNq2Fan2M+7F1fs3wJcU1I9TZ6TytKLSjJkHYIqi94esExKCbOg2aqUuj9eTiTIzYkLVN1EdKmQc5bDOt6x1HX7sZN05Ksyq0E0IgrcQxEaxko3JY4OQC3WE7On5M2CwHfbSl7kK1p7qwVVd816cPRoDg4l5tqL62hwMpXqUv3111+HdXvy5qfJc72oVA8bnHC+Ab5FIlafEc3HRzzy+HbSSWRbzoULVNN0NG2NvjSxylYx9xgobYID9qNb5EzCJEBWZ/fCRI8XQB+v6N7ifHQ2nJJc9w+S6NL/sp0CLoY5qeSluPFJA5PwvHtxHd39pUhNk+c+oKgoLsCpyYaMxqdnxKU25Dzvw2j3CvJhI1ex8+ICFbsR5I7sGnWSPKvSvZ28tI9WvUpBJyxy29/dqpCFOP9jeYOQ/3Q0jcjI13lLTpaYoOe2lYi+jo7f1sBC7zVwu1TGbRZaItLN+YJ5cY1BabbRjz32GOzG5XK5Tz/99L333hurMbPjubJanWcwvLb0ukILQfOCCjgS2LI3wtoe1mv8sFq9dheoaGAMrR820zQIOIzSBo5NRn2/Xdhx9udsmhcq1Wqifrjq+WEyrO9jfmU6aM7bJYQNwTL9YDXKm5X2KnosVgRsq1PNFAfWPBkqKs5rEt9cJwIuySd4uzY7nvug4IbvoGEXqIFU+b0MvWRD52b6Ja+/aeznwUXubFygwvwCvek1wq1DjpjLanMTRQGHhypB62V5S/nQrwME4iYTW6Kd9NSePjQs4LqpsCmE9yZNmuLV9Vu3hOPPjTbiN2pCa3hbB4QumaM9Wb4ZF4EpSQ7VzZzn+Bzik1scDodoq+JTqD2Sg90IxACvi/ho1REc+dVJlfwd2y27KNg0G7p9cdYGv4g1EXi6TSZygbqrp5gQC4yccoG63syDC1S4IAErCTmLg8gRbwAtIQLanMoa9IqgQcXKxb73asG0AV0xg4vGstyUw2/nnMnEm0WcLCqIG+8wPeyIGniGd4vXmlpoDCADkbrobKt7cWeqSPlj7Agvlm+uCIHpSQ4NeeSKGjNxMWBXgg9jfP52dtB3MHgltu5s8QkCZQbOxepn6OwYLnz3Wy6DQqe7GAfGUKjzMhoIFSjXtpZIKXDDKqYoLQr8yp+fbe/BZiC6e42TRraiLnd5pIsQtKkVcmuMCMAFXtXLdfwK6OJwZ1/sYZTrURcSaLcReg8THHVQtL1vXQisizNTmxOgKuhLGZidunUs3i0bSC0VupMF4+ByVhlC4Bs3UN2B9QitUaLrdcrj4KQI3L59W67JJzDXTZ2p+l2SJIC6pSGqORFUtPBUalej1E6VLULToiR8I6dSUC5/NEsXqP97UPmf/negpssScbcPqvG69SQAcc7HNSazm02Z32i4a5PCvIhQck7J0t2rnz8CVMzhOKeVjU32v0Q9OIbJ8rJUAgTg71iefvrpe/fuTfxxO1SSKs8TdIpFZoMAsjWB29ZA49iLawCQub2d+fp8bpFZrob1W/vt9Wazdg7UTd5zNAPAftGf1CbPyJIpI8D6PGXAuTpGYAYIzHwfbgZ95ioZgWVDgHm+bCPO/V1GBJjnyzjq3OdlQ4B5vmwjzv1dRgSY58s46tznZUOAeb5sI879XUYEmOfLOOrc52VDgHm+bCPO/V1GBJjnyzjq3OdlQ4B5vmwjzv1dRgSY58s46tznZUMg6d+xgPOXZYOG+8sIzD8C9+/fT9JI/juWJCixDCOQbQTYbs/2+HHrGYEkCDDPk6DEMoxAthFgnmd7/Lj1jEASBJjnSVBiGUYg2wgwz7M9ftx6RiAJAszzJCixDCOQbQSY59keP249I5AEAeZ5EpRYhhHINgLM82yPH7eeEUiCAPM8CUoswwhkGwHmebbHj1vPCCRBgHmeBCWWYQSyjQDzPNvjx61nBJIgwDxPghLLMAIzQwD8pb722ms3btyYpgVJ//58mjrCeY13zj6kaV/AgtwaGwe7ximwzj5stweVCrhB9i7pYllG6ZKw1LgYm7BxYurCNqCDZXu5xYZaYsVsKML1sE3kECMwMQJw9MPNmzdXVlam8X8+E31e2FzP9Y5alo4Sg+Oj9kq1c7BdoNt+awd8d5bLjR66S4fATqtVpxj7H6QNzoPFJAK0e4h11UpWGIiqr9320MbLkErFlOiglwFmho4snEIq7IhYAZhhTJcdAQ4yAgqBu3fvDofD55577s6dOxNr9VnwvHSrItqHXWcgc/k1Ifr9bmtnty0qe4rpJFDaKA5PjzWXgRV6HoDUwurK8OKMxAL/IXvwAiWdqzRl2OcTTCONXnFLVgWVBAoI3harVEizkhPRQSdDYXsPOrhbd3pYrLpTiiMruvVQl91kDi89Al9++eWbb745JdVnYLcTcw81c4U4uxiKvB5N1OItfQO/QWHhKHA0CwZHVJC1u4vNTkWqXCwF4rcudnfQdMCgUzAEu/UyUhF0a7UIJgOGI69gmyBDM39kjf5AJpjGcr2GY60Mez1RRKZ3XeqbXP3WUa9SvVVqRaYaMQ4sLwKS6m+//bbU6hMY8KnzvFQDHTu8CIzZytZBp5qzkWp5XNjeKvaO6kRlTPQV+Fo+1zuR7JSUdVhtiwqHzKSAa2pRa6LypalAi/bPB7kqzhe4YjjKN/2lu5QC/V7V8vJXtRlnpsCkcXG4f7HSrMQyHRYRW82NkujGTjV+TXy3fAhMSfWUeY7ElWNkyKaGbNAo7wQfc9SMQihGESnzOWGmCJ/0sSMfXsDTpEBKGTIppe5n9yNJ6zsCI/U5LgEGJ2ZmUtn6rf32ejzTYWIRFSa6gzEHwwi4VH/99ddh3R6WiYtJlee4cB00GoMqGNAOlYg3SjE77STNjxp1p0Vb8TIJl9ugaPWlrPQYkxemAi0Y+4u143QSunBi0TNPWMjT50qVQxFY4/D0LFSYGM307kmvurUKG5DBCSJcEMcwAuMjkCrP1/KDBlCnFjB4o7UZKMZer1f0iOpMDqqraBYEex0gpcNIXLy79MWcuCGXN6t4WRaVGph5LJcD9YGwv/APWxCUw2X6WaAMvJWbkREJHMUIIAKPPfYYLNFzudynn3763nvvjQVKqjzv1uvQOOddlmpqpDZDTocolKhvZt9MEj5I7HAZtD7e2z6Wi3TIVV0BK0LrciUfMCS8UkL7DV6qubFM3zWrD5PIAUZgBAIuybOwDxfZFSS6s+OM9Fa75I74KryUDhjYvUYD9souzqyUZKMy9un1ViPfjNvphmxQFX4vQzq92clDedVqEWaGVsiATq7PV+INcM30vZXQG3q/H7ZHHGIEpiQ5AJiqPo8fMNKo8DK7i/zCNW54K0ucB165YWmgewXuzAFdcRsfvqdRm/OkymFvD8vbXT3o1IRdbFMzYLMe34QX4R0cGhmwX9DY6FSrVXwnB2uL8JVMn9MahL4GCJdAMYrpOdgRGFiRmC5bAQ4tLQLTkxygmxOeq22q5oHY3Tmmr+U01Qy9elEDjWzF5bC3cA+a62DH7+IHahsOg2m/TL5OA3FpJwDDaalAb8xCdE+ozyPXIF7TNdOdSPwSYHh65sRwkBFQCNy+fVuuyScw1w2IM/C7FG2VY4skQSGgSaZFA5++K/VNnQjSjzS7zk8S+r8g/SFeVRghLg0E0bvXXvnnwGJBlxf6VU3BrMKZUkJyoQhsxogPb0LyHLFMCMDfsTz99NP37t17+PDhxP2eAc8nbmtWMiJt10/hdWCz07m0zfBNvZ7NYInBFyNwLQjMi91+LZ2bUaHSMG/WzoHDlzYBtT/s7ttv/i7NwQKMwNgIsD4fGzLOwAhkDoFZ/L1a5kDiBjMCGUeAeZ7xAeTmMwIJEGCeJwCJRRiBjCPAPM/4AHLzGYEECDDPE4DEIoxAxhFgnmd8ALn5jEACBJjnCUBiEUYg4wgwzzM+gNx8RiABAszzBCCxCCOQcQSY5xkfQG4+I5AAAeZ5ApBYhBHIOALM84wPIDefEUiAAPM8AUgswghkHAHmecYHkJu/6Ahk2F+qGZqRRyyo016MMBwzQ8c02ggZCh4pE0zne0Ygwwhcib/Umf/9+Qime8cpeTd44NOe2PecJdFI0tQwaOxebEX6XoAZAd0o4anN4QNdM/wocNMXGIHFOAcST3rd2yy0+tGnJqFzUuOIwRwF6XhQdAfYOCqFgyGVN0Z1XItLazj+tWkOa3ezc5gRmEMEXHdL4Bp5stMgUz43CmmnHKx5iHq+lOw5kE6I9Lk5uREz6xnAnOIYclTqVWFu2D+pgYID2UBgeqqnzHPv/GXCGJk/4nTUKH1uiA0FQPYNNVYRjkpjRpH9k8YAw9Fzi8CUVJ/n/Xb0n+Rdjv8E4PfBdsEdFXJUCke5J7nQm0IR/JPyxQhkBwFJ9eFwCF7QwV/qWA1PWZ+P0bZIE7/XbscUEeuoNFL+cm8Kkdk4khHIJgJp85w2xENeiPVSW2Io35NZEx8Yb9ytQX7tFZnckq65sMc4KnVFnDD7J3XA4OD8I+BuvM+1v1SA0vgy1bCi2o5bn0MaOjnUova3sL0Fu3kXnt1uUznECCwcAi7JJ9hyn+f1OZri6kIva3AdbAvwlUYvx3sNeBPeQUVvV+3kqFRnufTX87N6qTQLMAIzQ2BKkkO707bbx4FKujStldD3sfyyBRQ8uDKC/fYyuVnsdnEZAH5PgOqXOir1amb/pB4cfDPHCExPcujcHPNcvifD79fA4zH4PobLrtnpVi4DVHCcrTX2T6pA45/5RyB7/lJxLR71lUwE1vgp+9bqofqO7ZKM6oU6SsUt9b0q6Jubo2g3554g3zACM0eA/aUGhwD5e5mjUvZPGkSN75cAgTm228dHP4mjUlT67J90fGw5R6YRmPnfq2UaPW48I5ANBOb5vVo2EORWMgLzjwDzfP7HiFvICEyLAPN8WgQ5PyMw/wgwz+d/jLiFjMC0CDDPp0WQ8zMC848A83z+x4hbyAhMiwDzfFoEOT8jMP8IMM/nf4y4hYzAtAgwz6dFkPMzAvOPAPN8/seIW8gITIsA83xaBDk/IzD/CDDP53+MuIWMwLQIMM+nRZDzMwLzjwDzfP7HiFu41Ahk319q/DEx2gUqnhxRCR0DbYfddc1CzllCPhIjDpkJVBvjhRUqUaUHG4GNO970G6YbHCjbttQP2XY7Z9sE69FZrLCOwV+oyZx27cZTOFRUTCd1s0MFcMTcIHAl/lJTPWciQAJ4fstlCSemxB35FP2YS1dreWc0SjU6lApOhtUnvAvwXZHDWcI9Hx4fbazW4QnkVJXYSOKfLd00AiVUdFQcJFnuYK/CvlkDJds6Qmde26SIULfe2OiM8gdpmicr1Gfrwd0oV7NwmpZp9lpk+yPawlHXiMDdu3fz+Tz4YJnYiSI0LlWey7CJjL4AAB51SURBVEfNcsmAk9w1mskSDBDLwV3LSqUyaOij30q1g9X98vmt+EkkWEzcvevnTR1KKaLinPwR8w7NAcdKBumkTsujecjw0iljZPBaXM26NXbr7FvWxWM24Sk9q8lGp8rzEE7Ooy6KVg9bjRjKEROBnFI8aZ3XOrVSty6UMgKPa/S4YhydBh1ZhMNZ2xDjh9mY8NIMUCUYYuLMFShV9szpCCrVyuCo1ReFTSlrZr1YS8Yr08XKTbCwUaxplF51GMOHWiDXQNq+McJxrmbZt6wL9czC01N9tjwH4BwqSBhDpHEoGAJaM5E8oOqnF6SKcMw7Xq4RT8mh6kjM8Bgrv9jFU2alsUupaE8rMTpa+mz7oHPrqAwe1pF7aJjX5XnyWmht+wC5W0YLuFNrlA/B43pFtHfLUG7gQu+Pgnrh0DAgo1oXqISmnFFThIebAsoQG6qAxm2omkbYU+xbNjQaM4mYkuoz57l+0gzBQii6z6ab6DBRRscJmkzIYrixjCLVjVtUIy7ksjKvXSk7qZi5BOsnkTM9MeCZ8tVqB7P3GmGSQ0vQfxQtALZw26BF2ZUGnubg6ailfmFblo4o4Fym6oIfnGwGJ6E5SAqQAwzwLduNN4ZsSRy6RgRcqoO/VFi3J69sXnievMXRkqRzgcCdmN150uN6K6qPjNKPOzzeaPVb3gbsdp2J6iXSQ1n7Yg/eA0RMLCUQy9866BSlgQwSivuQE3YJ4HJcvoK7iAE4gK3kj9CX1K1S62RD7iScoSBp3FEKWwpF/E+tDMY79QaSLnE1O44DjEDJfDsvCGSA5w4FQ6gpc9SJj+AeUdoRiQqqbJb82lrwOWN5q6YKyV1ZIpaBoYvDnToFMC+w25tjSFnTC4HC9h4s2HfFFtzJpQHMN6D3QW8WZIFkDhhVas0QmSr/t/MT3stliZ2abH9svSA2tqtZ9i0r0Z7p/64Dpnn3lxoD1Ch/Z1HMxWI0E/0iY2YFvUfuC6s7ywubbBbl9TKsx/VLfCodyRT7/tyWYNxEAdlgu0BS0Eleyw8a9a7YxqUEXeFVMi2Ot7YLXbT5Q7Y4TiNxLyMhjV3NamAX4dcl+QT+Umejz9H9aQWe/PZADsEV+juLmxVGjDXyxVmBW7vdblnbEAhrYpqqnDhdjVW+yG/9mYBOpd8uKX2lunHa2ioOTw+B0M7VPz4dVtY3C62+H++IRAYDrmbBYsBm7OzSjAUbEuhqFmPAdpAXuZqNr4N9y2qgZvI7JcmhzanyXBPKEESIVouWoRXR660j9zHJ37weE9gYfY6lhDSqnG6gSsNDZKzcbx+z2qC4Ub5IeP3hTqgBTq6YN1v0YmtrbKJfpavZUbaW0wEOXhMC05McGpYqz8P2sVR79vkHnoFPc1A0zub0CObKN1Ievs4c4sU7OldNN1BrxIsuL5O9cRuh1wBRcZBDzh42qw3ZlOC+Ainz9q5RrzaPoG2wMYkuVwBX42r2Cm0tp1ccTIzAlfhLTZXnXtck21CZOk83zgTI/QNhqR7HXJRzv3uVpbvk8+oDfS6dK/veleVM40o6drs0ATDRNMKZL6LiQNTOWm6pNhzVbq3+1RREFWpYaHaEhKaztrCl+ftwGI/vCVfa+3Uw9QFazKjtCZvLhGQfaMMP9vthuwAubzpGmvfw8x6+ZoTA/fv3Hzx4cO/evYcPH07cBPavNjF0C5URJ5+Qq1n2LbswYzw7fb4wEC5ERyJdzaJ1wb5lF2J8WZ8vxDByJxiBkQg8MjKVExkBRmAREGCeL8Ioch8YgdEIMM9H48OpjMAiIMA8X4RR5D4wAqMRYJ6PxodTGYFFQIB5vgijyH1gBEYjwDwfjQ+nMgKLgADzfBFGkfvACIxGgHk+Gh9OZQQWAQHm+SKMIveBERiNAPN8ND6cyggsAgLM80UYRe4DIzAaAeb5aHw4lRFYBASY54swityHBUbgSvylzobn8IfNB9sFd2zCMSYVjkDoeOIUAedL6StQlMoopaLTTNk6EKpDJ8T9jmgvJKmrVrLZIzJERFl5DjECEgHwl/rSSy+BE8UbN25MjMlMzpmA48t6R/XLjiICGjjHsBqvJ8N249Se44RC4jxUFOWFM5H2weORewZVNE6lGrhF6vVExRydFC0HswH4SDNeGqWQ30yIg2qVbwanFDyxTR7mioXQyS2hRhtxWw+FpMsHlQqlozeHsBtWk5sDi4VA9vylKvzhmRdF8IBG7knUeWoRQwPHlIEvRDyFvCtTgVF0V9heV+JRxydKaiDZKNtO+Rz064F/tKRbG8grH4yY82D7zD2D0hUEB4jrOdE7gkaRJyXp+REr0t7XsID8kW6um9fSXMBRrKPPW0P/DeCMzRQTPm+uy25MXXAXPOy6W5rYNXL6+hy4CcpIn7QK3NXHoUcMF/kpUI5OKd++oryUDZyFLBlO+tQVU2dLdjoRfpIoi9bQcBjjrojX/3TwaXu/2+2DqzFsNx0ADQE9YclGeQczwiwAfKXpSB3NHpjk7KGTUhROub6FDp1a/Qg4TBS7MTVQLEVgeqqnznNgizjd10+xORt8DcZrfa+TDzgPxGPLDvbAIcnx5t766f6Ozoeji+wBchClgW3VIh7UXO4DdQPMA1mpdYmTmk/SfRlNOWvgSVmaDYrqnXzAOIcikH/D9rHbAmyFeziq1OfG/sBkvDCnGFJQtVnqajNXUJL6DzV/9AHPrpRgN6YeHIt/MyXV0+Y5PPODo7IhCxqxJ8RUOPd8MOjBqcwHq/JIZ6KuGj91MrH8GcJKGi/yRaCcEjls00ckq6zuj5WSqh8oTx5J+vXGBjgwFsRQzH+OW2n+MfKoh4M036x19qRXB2zsCm0cuPVRGKmtI6H3YJGr/upI7xeriXVd6kqyG1MXjaUIu1Sfb3+p4CgQFCi6BVdGL2zInUhfwni8+mG9Xoe0plSnsX5ZgKRFOLgdWYvSep8NuWYYpU5ehwWz60BVLnVR0Fk64DPSrcOSF+0GaS9j0TgVWL2O/LPWNHE3V8TtNHC01mnCTlmvsXsswFYRq9ZpK8wjh6u42G73KrilANZLyLOS/4SifTM8PfMirXcHiLbWCLsx9VDim5EIpKrPSxvkP6BQOGwMmlI59xqa5qqZQDH0W0iLcp+5JIBMPbY9In6iR2FpCUsek0VuVv2GGVCajCS1DndyN80WJoT1aIAluf7IQSeTJ1OQxnIu2j2xfrEPs0Jh05IPw+Ic7IGW8fK4lhft/db5LeI5mAo7bnXRYXJm6iTpTjlROshuTDUSy/DrOmCaa3+p3bp8zPv9vtTFWxeHEUYsWs563LyH3DBVp8Iv2q9hryyOQEyQyB6VBpXA/rvngURttKumqmmCmCsLUD5gpIdxV5+joYBdVu/RqWi/TrsPRxaIM4X5cny35Ai4JM+Mv1QYM2nCX/oS/dLRRUv3yi5atsMKWr8NkAXrjfbYarTJANkrAX3u5QnMLWQYWP9SKIoWwSWuS90S2Y2pi8bihqckOQCTqt1uBgIVG3julothEztWYMOsxoFl2ihwlrLamRoUatTosB1wO2xSkKnwXly9SPcbgtvlwR04X+Lq7mh3Lb8GZsqlZZpXFZdKskCmEZie5ND99HlO3NIa8NIBcJhLsoa+J/XwR2fGhSHUYdbn/qsvv0KlYGnLrYO5I6aeqI12vxTh+W503p8HvaIGskXdRuyuBSBQ0JGRcXoWVQbHLRQCGfSXSnYxfJZiFPClA3Lp+tyW4FjFOliwqaNCuCHQgvfxsMG+Hvxwzt9ox1KoE7jDfqKVbtSshVIT7Ruc9KpVs7EoWxbRdqS5v4kQIcRRi4AA+0u9/lHUE5Pczr/++rAGrDPkutStmt2YumhwOAkC7EcxCUopy1wyu+DKB97/x36Hn3JruboMIMA8z8AgcRMZgSkReGTK/JydEWAE5h8B5vn8jxG3kBGYFgHm+bQIcn5GYP4RYJ7P/xhxCxmBaRFgnk+LIOdnBOYfAeb5/I8Rt5ARmBYB5vm0CHJ+RmD+EWCez/8YcQsZgWkRYJ5PiyDnZwTmHwHm+fyPEbeQEZgWAeb5tAhyfkZg/hFgns//GHELGYFpEWCeT4sg52cE5h8B5vn8jxG3kBGYFoFUz42iv6vWXgGDJ8WE/6Qa/s5ae1ejw6bi+uqVZIWc7DYyOkTFR50Ko8Wh5XtiP/AX39idoAMZW2mCJoOIPMdeV6N+4+IDYnzLCCRFIFWe21OQ8Em2TYSzX4vD4bDSrJ07TosKZyftLeWfRR8EJfNEss4WN34IisdTIEc5UVSFxlWt48kfkvQpo/1MxLK2cCbPsY9w8hTbBZxbwGVrQ1Sjzqx0UvkgilgMlzEhVZ7TmUikFx2ogQZKlWPIoVq/3+3vnIkD64bByRUI0gOuLQUnzXpl1ZHD9v7p+p7rpEWnwK9xvqzjQqYCOHw7df20SEF0rAzH3uF5cTCXoTNGc8IbOmGJ8aQEx9j35QQDXinQ2XFRVwu/zsHu1tAwrlT7pY1qsbhREuDS0V76nPlu9wz8Qe9tHwcMECvJoSVDIGWeK3T1s0/0hPPS5fMIWhVdsXhnMaIJkGBIrKWga0C9B8QPERXTW/db+AO1h41xeQw0HDotj4QDGXKrhPLorQXLQyKTb0eMhAvMESOP96rNOG1p5jqHwBr3Ldh5NPuV3u/awzHjLAA8YVo5f8JzYYNE1zSnJhz1KvY4SWwVX8uMQMo8B9+H5CVwEzAHxyXV9VNwPRbQor1evtk5+Pd/F//0T6ihDVPjnv6I4QP9SiXnL0B3x6o1OCw9t7VZaPX1ua1UkjzgVatJNYEE6pb+k8jNEuQxiwqatrDNpIFltGazKpBuVYuPT3tN6zdSRY74gZYBGKogXCAUK65Gp5OewW+zLIEdqo5AcvmS0uW5dp4CdB9eHLfqrRYgTv9FId9ski+zqKRRcUAlcgIhapX8eWsfdW+MAUtcsBY2FeqRSVUjXZ6ukNvmoH1tNTWp+nLLZTLkdz3C+q3u91v18jEo9UALfCl7hxOQ40q1f3w6rDhEJ5qfWr/N7FDVQsehdHmOeKNTA/o1jk7lUcZq55poEvCCHjFMjvMCu3wFOcyOhyKDVitRNlDIR7XOwXbk8aj91lGvo1wrkzT5Jz8KeIMqbOYHvWERPJkWqwcX4GM94PoRc6LCpxL0f9gQvQsQ3CXAuUGZKdLGd4VVAc76XNo0x0FXqj7RgzQHMwNM+63VQhLfLrrR/LuoCKTLc2UFI5iSGNoeBt3UJBsUH9fekbI9R4BurHlXBkqjXeiAPyfpVNX6OHazkErXCl9tpwVqL91avzg5XVm5gPdqa7VOQu3rdNWtLxjW85Lrm5V21J0XDzIPrhJ8V6ou0cM0p1zsUDUI+JLep8tzAJm4CIvu9gARJ50DS+QWeD1dB90TQ/PC9vZa63zkEEmV6Ol2Kw+kw01w+b6rb+MhBEmNPDpRb5yu075/kOWwyXZUPltF/+W0FIcXcOAcLuIyLqFMWoSapjQ7SYXoaXbUTSEjApboZzA9Dh2jfUQmTlpGBNLkOT326HUJ7d5SjRwLn10Mc+tIdDQyN0siSptvwq7axW5LbMQPEGpi8nMqYH9cm8sgbixmpFb5vNZpRqh1qe+rFZAJWAKwDlgVbfTdvG3qNrtuJgYDaJh4EfomNPO4kkGaK5ZTKxCt4Fc4QVequO6oVDdKhdUYL0zsUFWPw5L/pslz15TVb9b6AnaT0EEo0gce7XWzYYwDg1K5Ijkh6msn4pEDZslHrtJIBgilP6dTefr60xWnDKlzgeLl81swCyDZ3bfOsFeG+h9WuWNeWPtFe3SmAM3VskHNNfCmvg0mSE0oCz7alSoZRBu3ViIWOxri0W3g1KVAIE2eu4DCNrS8RfKrBHg9LE53XbMa9+V93rllTBeWBKd3YOrNNc4CGAuvye3bPL8SncmPlXeO3Q57jfg92vHmQcXzpaqyKUmX5lQwfkmglg0wTciX7znz6VD0vhrGgmSvoTKalsnSz8w9B5YZgbR5bp5fZBIudfWXJGoQ1DazYrdS0zaTswtNdJS5xpwLZHGQqUxfsHnDr20OkMHXAkGrWyd7efAG5KXdjvTSuXDrTIdNDk/SrKnlvGe/JIBsalsf5NXWH1E6tA1oTRlTBQaoHUehdYgnwzdLgwD7V8vSUKPWH+lKVXaGHapmaVBTaWva+jyVTi1sJbhmX2/in/uU9WInqq9gAuCrg8BnAFGSHLckCLA+X5KB5m4uNQKPLHXvufOMwHIgwDxfjnHmXi43Aszz5R5/7v1yIMA8X45x5l4uNwLM8+Uef+79ciDAPF+OceZeLjcCzPPlHn/u/XIgwDxfjnHmXi43Aszz5R5/7v1yIMA8X45x5l4uNwLM8+Uef+79ciCQ9O9Ynn/++eUAhHvJCGQJgfv37ydpLv8dSxKUWIYRyDYCbLdne/y49YxAEgSY50lQYhlGINsIMM+zPX7cekYgCQLM8yQosQwjkG0EmOfZHj9uPSOQBAHmeRKUWIYRyDYCzPNsjx+3nhFIggDzPAlKLMMIZBsB5nm2x49bzwgkQYB5ngQllmEEso0A8zzb48etZwSSIMA8T4ISyzAC2UaAeZ7t8ePWMwJJEGCeJ0GJZRiBmSHwwgsvvPbaazdu3JimBUn//nyaOvy86Bf4YncnymMv+v+TfpJdb8IRGUJRcX7Je+32SqWiXK1jO1TBcfJeU91GeAl8wwikhwAc/XDz5s2VlZW33nrr4cOHk1WcPs9tOy2tVZx1+m2FRGmjODw97EOM8QqMN/6l/ZKXagerh/tib0/sm5mk1VKymD9v8zmUzx+V611ZgRu0sjKtMmiQnIz3m68nBYwFX6WmdrcMDjMC4yNw9+7dfD7/3HPP3blzZ2Kqp8pzw4xis1NB1Wq8+xIFj5BrwcvSXKzlc72jKDsgmCfZfbHa6VSVaGSwZ8spbO9VBHgaVg2U5gB2wLQY+lYTMAt067urB8297WNmuoWPQ1Mg8OWXX7755ptvv/32NFRP/zwZIIS02w3rIzGQ+tFV4bHyJBqZKgvR5cvJxNHIOsHT5DbShrBwYXQ53o1U2b64LYZDjMCkCDz22GNA9Vwu9+mnn06g1VPV534fu3WrDWMoWLoFa+shZStsbxUNbYFIEWt8SD7ZsHb7+a3Ohl+jvkMeyn0AHUO/jlJX8UNpgKNN0d41unwL70bZFd3D9lZzoyS6Rtt79fANIzA2AlNq9RnxfLPW2ZObcVI5Nk4jOo7U1tHAeLCbL+NNYXVFZ9C/AU6TLe7OMFIuZp6RiUBzMTjpK8nN9ZwYnKs7XUvgt38+EBUmegAVvp0OAZfqr7/+Oqzbk5eXNs+Ju7ni+unuzvHmQacJW+G9xu6x2FwXYnX7oKP2xkE1H67ikrjdq0CKAAKrzbhRXVsFDg6O+mLVF1Jamfbx7D4cUtvdiRfC0+c6E9UthqdnqkzYJHDuhHDnEWNviO5Jr7q1WhBi9Hzgt5PvGIHrQiBVngMnti7aPbF+sQ9mb2HTvOaisDhv7ZRbaq0sYNdNtPdb57eI57CfvnMpBMVKhczrfmmQqzY7IN87cTPhprx7LyyXvWgi75YXZRX42cVQuEaDsg1o1vCyiFx+jXnuQ8J30yDgLtHfe++9sYpKlefEiVKNmCtbqfa84TU33Lv6XIhuHZldknKu1pQxgvbsZRj5em4nDciKK3/IE7M+VyWIXAX3/aOv4UV0PJnk65uFVp9VdTRCHHsdCLgkz9Y+HKKhLV1Qh5WAPvfACqyoyTDw31HDhOC+KJO5fX0OcX7G5Pp8xdrgtMlWuVVq6ZdsXkOdm+HFmXPHQUZgYgSmJDnUm6o+n7ifyTLqSUNJx+hza4In1OekwR0bvN/ab683q52DVWemwWW7GJh24oag2bozsRxgBMZHYHqSQ50z5rmngp19MOcTleTAeIVRtqA+xxdkF4emxIT6PLSpRl/fwTTiWf3uLFOADUG7dWfq4wAjMD4Ct2/fnvi1uaktVZ7rLe6efkll7HbTHgiglN0Wd1MuCbtMA1Gpz8nMtoQctg+F3dYXNiFUNq3/5UyARK8GLfXAUsLNjzS/yi/33LI5vGwIgAe1Bw8e3Lt3b+KP2wGx9L+Hy+Qw4dyD7wJbzQ7u44+44FtefxNghCwnMQIpIZCqPk+pT9dQjVyTN2vn9pP8mFqA5PhRbJ1342MA4uhZIMD6fBaoc52MQLoIPJJudVwbI8AIzAAB5vkMQOcqGYGUEWCepww4V8cIzAAB5vkMQOcqGYGUEWCepww4V8cIzAABfK/2/556aQY1c5WMACOQCgL//a//h/V5KkhzJYzATBFgns8Ufq6cEUgFAeZ5KjBzJYzATBFgns8Ufq6cEUgFAeZ5KjBzJYzATBFgns8Ufq6cEUgFAeZ5KjBzJYzATBFYWJ4/u/UvX1WfmSm2XDkjMC8IpM/zZz784F9+/lRk/yHpja/o34fOSa2vVt/4/dYTXoaNH371zsazXlTw5rOj/9t88geXUj1Y+FMbv//gh68GC+N7RiDbCMzynAng2F2PrH+6/cq7LwfxfOaHz/79dx9+AdGgoj/+zh+/99OTz4IycA9zxA9eDMcD1T/4gRMNVfzmfRIWd9992Tve3ZGKCD7x83de3X3w20cbf8Bm/OhrVuTBJ7JJ2J0nVdimcogRmAMEUuW5IfaLb72xK5DVjyoIkEXf+hUwMHRtfPPFB398668Y/4+5r330q0iSy1x/b9755c9IMlSKjIC54JsU+sPLrwiwHT4U7/4G74nDT0oZ/P/uB28olzaaw89u/Y9d8cn3Gn9QQjpe5v34HQFUf7/x/rfeefV/bX3+7SOclfhiBOYHgVR5/n7jXdKl658jIdFK9zxE3X7jq9sGGal4n/j5j7/x5//8D1LgoNjFi88amW98/MFNKf1RWDOD+f3Wzb+E403xAqj+Bznv/Hn4xc9++u7PZBJmfPzfUOe71zN3fvS1j+5GTjFf/OwXn3z/rZt3Nk5ePvniZ7/60+7t7756FMjuFsVhRmAGCKTKc79/yDQdI/V5yJDe+O7uk+LPJPTs1vqLn6HZjHewPv/x33wD3i3tmQ+jSe7IQAm3vwETxG3xxk90I2J/0ab45HtxRv5f//YXk/PkP5o/fvWHG+L9OGEjyQFGIEUEZsTzmz/86l8lUXFd/fSvf/u7iD6jMtfRoFFF846ZF3S0/jUrAh0hhGcd2GgBjP3V4x/DNPHKb8BMeFV6ZCXaGyFjt0tL4dXiN8SDP5JNYUScwFOPPy3+/ruBjPni8wdit/iMOIltqpOTg4xASgikzXNUy+JrL+J22ucvvfPGx7Aq/uy33/tEvPQdIVZgr/vm16njQLC3VnBJ3Pzs5vchBrikV+mRwNCKgFKQsYI226wgzgJC2wIU/ajWtyojbO/bJbfNSKEnvgU2xX/GLbnRdvj6Z781+wLv9/5098ePw/Zi7LwQKJ5vGYHrRyBVngPffjL85CPxzc9/AWvdJ+Cv3vXSGsPgqOjbrwD/yIbHXTfR/MXJ5y8Tz/968u2fJgHjmQ/JGvdW109t/ORZ2KKzCja4Ya4Ktgt+WxOS/3O4/cvA5/mTN83uwJ9//f6jgY23Jx//R+a5BZFDs0cgVZ6rfbgf2W6/KK3rzz5pQpyrz4V4v/FLiFOvsn27mvI7tDSqGNTyZwLL/LF5v/XEz//V07eQ97OjXz56RGXI/2jv7S+fPf75h3q73tvGoznIEcegqTEQz7eMwFwikCrPwwgYff7zd24G9LknfPIbY2ljPNA+uA9H4id/eFkujIGoH7whlwDISfM+zCsUb9Ckh5feuFYHO+KNr+TbNcjyyruu4f30yhNC+Co9VJSNePC3/29vOMQIzB6B9L+HS6XP7h74k998KerzO2A4fHsHH+rod/L4du17v/47tE/HyKbi1trXc8DzRNezK4+LB39z54hE2ViIEbhOBGasz5XdLnvo7JB/NHGfyeQGTe4sm6Witl/RoA4Het9999GG3AtQ39JBlm+TSY9TgHyTT2/yxtlae+Kl73wtftNu4l5xRkZgKgRS5bneAPvTb/RXa9pud/ug9uHcqARhxVW00n2TG+xt+gwGP8v5PTL5C7s5L3Av/cXb3wx8b+sIUM0nf/zo9g/oSxi8DS7v3cY99a3vP/mnfwtsy7kCHGYEZoEA+lfj814vRd58Wv/xB2/ECT/6yrtgCPxkiFNJnAzHMwLpIwDnvaaqz9Pv4VXViH/99p1XP67+DcgcVyYuB2BLr8Ekj0OI42eGAOvzmUHPFTMC6SDA57engzPXwgjMGIEFfa82Y1S5ekZgvhBgns/XeHBrGIHrQIB5fh2ocpmMwHwhwDyfr/Hg1jAC14EAvleD7bjrKJrLZAQYgTlBgPX5nAwEN4MRuEYEmOfXCC4XzQjMCQLM8zkZCG4GI3CNCPwX7RnW6Lk/ulwAAAAASUVORK5CYII=

[scssset]:data:image/png;base64,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