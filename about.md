---
layout: page
title: "关于"
permalink: about.html
image: /public/images/hedin.jpg
color: '#f44336'
sequence: 9
---


{% comment %}
  This inserts the "about" photo and text from `_config.yml`.
  You can edit it there (jekyll needs restart!) or remove it and provide your own photo/text.
  Don't forget to add the `me` class to the photo, like this: `![alt](src){:.me}`.
{% endcomment %}

{% if site.author.photo %}
  ![{{site.author.name}}]({{site.author.photo}}){:.me}
{% endif %}


我是唐英淇，目前在中国杭州居住和工作，从事 IT 行业。


## 更加了解我

我在[豆瓣读书](https://book.douban.com/people/83846059/)上分享我正在看的书以及想看的书，在 [GitHub](https://github.com/Tangyingqi) 上维护我的代码以及关注开源项目。


## 版权说明

我坚信着开放、自由和乐于分享是推动计算机技术发展的动力之一。所以本站所有内容均采用[署名 4.0 国际（CC BY 4.0）](http://creativecommons.org/licenses/by/4.0/deed.zh)创作共享协议。通俗地讲，只要在使用时署名，那么使用者可以对本站所有内容进行转载、节选、二次创作，并且允许商业性使用。

## 联系我

欢迎关注我的微博 [@反正我们是](https://weibo.com/{{site.author.weibo}})，或发邮件到 [{{site.author.email}}](mailto:{{site.author.email}}) 与我联系。你也可以通过页面左下角的链接给我留言。

