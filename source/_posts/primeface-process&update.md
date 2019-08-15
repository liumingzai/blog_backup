---
title: primefaces+jsf异步ajax-process&update
date: 2019-08-15 09:12:25
tags:
 - Primefaces
 - jsf
 - ajax
---

> 本博文介绍f:ajax jsf标签以及 p:remoteCommand primeface标签的原理

### 如何理解PrimeFaces process/update 和JSF的ajax execute/render属性

process属性：作用于服务端，其属性值只会影响EditableValueHolder(可编辑的标签，如input) 或者ActionSource(有事件操作的标签，如commandXXX)接口的UIComponent。
通过使用空格分隔IDS process告诉JSF在表单提交时，哪个组件必须在JSF生命周期中被处理。

    JSF将会应用请求值(通过组件自己的client ID查找HTTP请求的参数，如果组件是实现了EditableValueHolder接口的话，将会其设置其为提交的值，如果是实现了ActionSource接口的话，则会将一个新的ActionEvent加入事件队列)，转换、验证和更新模型的值(只针对实现了EditableValueHolder接口的组件)，最后触发队列中的ActionEvent(只针对实现了ActionSource接口的组件)。JSF会跳过处理所有其它没有被process属性包含的组件。并且，作为一种对付请求篡改的防护措施，<font color="#C7254E">rendered属性为false的组件在应用请求值阶段也会被跳过。</font>

    process属性值： @all(更新整个文件慎用) @parent(包含当前，同级，下级组件) @form(primeface默认)  @this(ajax默认) @none() IDS(空格分隔)  注意HTML不支持一次提交多个表单

    在标准JSF中，与PrimeFaces中process等同的属性为<f:ajax excute>的excute属性。它们效果完全相同，但是execute不支持以逗号分隔的字符串，而PrimeFaces的process支持(仅管我个人推荐遵守空格分隔的惯例)，execute也无法使用@parent关键字。并且，<p:commandXxx process> 缺省情况下为@form，<p:ajax process> 和<f:ajax execute> 缺省为@this。最后，process还支持所谓的PrimeFaces选择器，<font color="#C7254E">see also How do PrimeFaces Selectors as in update=”@(.myClass)” work?</font>  比如<p:message>的选择器.ui-message


update属性：作用于客户端，其属性值会影响HTML显示的所有UIComponents。通过使用空格分隔的IDS，update告诉JavaScript（负责ajax请求/响应)HTML DOM树的那些部分会被更新以响应表单提交

    然后JSF将为其准备正确的ajax响应，其中仅仅包含被要求更新的部分。 JSF会跳过处理所有其它在ajax响应中没有被update属性包含的组件，通过这种方式，来将响应的负荷保持在一个较低的水平。在渲染响应阶段中rendered属性为false的组件在应用请求值阶段也会被跳过。 注意，就算rendered将来会为true，但如果最初是false的话，JavaScript也不会在HTML DOM树中更新此组件。 你需要将其包装一下或者更新其父组件。<font color="#C7254E">See also Ajax update/render does not work on a component which has rendered attribute.</font>

    正常情况下，你会使用一个普通的链接( <a>或者<h:link>)，或者通过?faces-redirect=true 或 ExternalContext#redirect()进行重定向，来发送一个真实的GET请求。实际上，作为一个非ajax的提交，process="@form" update="@all"效果完全相同。在我使用JSF的整个职业生涯中，我唯一一次遇到@all的合理使用案例是显示一张错误页面，防止ajax请求中出现异常。<font color="#C7254E">See also What is the correct way to deal with JSF 2.0 exceptions for AJAXified components?</font>

    在标准JSF中，与PrimeFaces中update等同的属性为<f:ajax render>的render属性。它们效果完全相同，但是render不支持以逗号分隔的字符串，而PrimeFaces的update支持(仅管我个人推荐遵守空格分隔的惯例)，render也无法使用@parent关键字。update和render属性都缺省为@none




