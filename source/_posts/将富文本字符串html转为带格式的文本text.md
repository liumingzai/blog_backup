---
title: 将富文本字符串html转为带格式的文本text
date: 2020-12-16 09:03:40
tags:
  - java
  - 后端
---

> 给移动端提供的api返回的富文本数据包含html标签<p></p><span></span>等。app开发同事反馈他们的webview只能整页渲染做不到对html片段做渲染。虽保持怀疑但能不撕逼就奋力解决

### 方案一、用正则过滤标签

``` bash

  String text = htmlStr.replace(/<[^>]+>|&[^>]+;/g, "") : '' );

```

富文本编辑器中有制表位、换行、空行、如果有上下角标，栅格线可能还需要额外处理，图片资源等等吧。造轮子的过程很漫长


### 方案二、通过JDK9自带的Html解析器HTMLEditorKit.ParserCallback,下面只对handleText做解读。还有handleComment、handleStartTag、handleError等暂不介绍

``` bash

package com.liumingzai.test;

import javax.swing.text.html.HTMLEditorKit;
import javax.swing.text.html.parser.ParserDelegator;
import java.io.*;

public class HtmlToText extends  HTMLEditorKit.ParserCallback {

    private static HtmlToText html2Text = new HtmlToText();

    StringBuffer stringBuffer;

    private HtmlToText() {}

    public void parse(String str) throws IOException {

        InputStream iin = new ByteArrayInputStream(str.getBytes());
        Reader in = new InputStreamReader(iin);
        stringBuffer = new StringBuffer();
        ParserDelegator delegator = new ParserDelegator();
        delegator.parse(in, this, Boolean.TRUE);         // the third parameter is TRUE to ignore charset directive
        iin.close();
        in.close();
    }

    public void handleText(char[] text, int pos) {
        stringBuffer.append(text).append( "\n" );
    }


    public String getText() {
        return stringBuffer.toString();
    }

    public static String getContent(String str) {
        try {
            html2Text.parse(str);
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return html2Text.getText();
    }

    public static void main(String[] args) {
        String hm = "<p>　　2019年6月6日是第24个全国&ldquo;爱眼日&rdquo;，今年的爱眼日主题是号召全社会&ldquo;共同呵护好孩子的眼睛，让他们拥有一个光明的未来&rdquo;。</p>\n" +
                "\n" +
                "<p><img alt=\"杨书记.jpg\" src=\"https://www.pkufh.com/Sites/Uploaded/Image/2019/06/066369544014514513905026911.jpg\" title=\"杨书记.jpg\" /></p>\n" +
                "\n" +
                "<p>　　北京大学第一医院眼科主任杨柳教授表示：&ldquo;近年来我国儿童青少年近视呈&lsquo;发病早、进展快、高度近视比例增加&rsquo;的趋势，因高度近视导致眼底病变的情况愈发多见。青少年近视防控已经成为国家层面重视的重大公共卫生问题，刻不容缓。作为有悠久历史和&lsquo;厚德尚道&rsquo;院训的北大医院和新扩大的眼视光中心，贯彻落实习近平总书记关于儿童青少年近视防控的重要指示精神，做好青少年的近视防控工作，是我们的职责所在。&rdquo;</p>\n" +
                "\n" +
                "<p><strong>北大-黄小手牵手 近视防控光明行</strong></p>\n" +
                "\n" +
                "<p><img alt=\"11.jpg\" src=\"https://www.pkufh.com/Sites/Uploaded/Image/2019/06/066369544017687951193520799.jpg\" title=\"11.jpg\" /></p>\n" +
                "\n" +
                "<p>　　6月6日，北大医院眼视光中心副主任荣蓓副主任医师以及青年医师王珂欣、汪晓莎组成的医疗团队走进黄城根小学，小学教师、学生及家长共计50余人参加了本次活动。</p>\n" +
                "\n" +
                "<p><img alt=\"12.jpg\" src=\"https://www.pkufh.com/Sites/Uploaded/Image/2019/06/066369544018737951358328608.jpg\" title=\"12.jpg\" /></p>\n" +
                "\n" +
                "<p>　　荣蓓副主任医师围绕青少年近视的病因、预防、筛查、矫正等方面给学生及家长进行了全方位的科普宣讲，指出青少年近视防控是学校、家长、学生、医生共同努力的系统工程，各方都需要掌握科学的防控知识，缺一不可。</p>\n";
        String text = HtmlToText.getContent(hm);
        System.out.println(text);
    }
}

上文解析结果如下:

　　2019年6月6日是第24个全国“爱眼日”，今年的爱眼日主题是号召全社会“共同呵护好孩子的眼睛，让他们拥有一个光明的未来”。
　　北京大学第一医院眼科主任杨柳教授表示：“近年来我国儿童青少年近视呈‘发病早、进展快、高度近视比例增加’的趋势，因高度近视导致眼底病变的情况愈发多见。青少年近视防控已经成为国家层面重视的重大公共卫生问题，刻不容缓。作为有悠久历史和‘厚德尚道’院训的北大医院和新扩大的眼视光中心，贯彻落实习近平总书记关于儿童青少年近视防控的重要指示精神，做好青少年的近视防控工作，是我们的职责所在。”
北大-黄小手牵手 近视防控光明行
　　6月6日，北大医院眼视光中心副主任荣蓓副主任医师以及青年医师王珂欣、汪晓莎组成的医疗团队走进黄城根小学，小学教师、学生及家长共计50余人参加了本次活动。
　　荣蓓副主任医师围绕青少年近视的病因、预防、筛查、矫正等方面给学生及家长进行了全方位的科普宣讲，指出青少年近视防控是学校、家长、学生、医生共同努力的系统工程，各方都需要掌握科学的防控知识，缺一不可。


```


