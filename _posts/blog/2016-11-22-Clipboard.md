---
layout: post
title: ZeroClipboard或许已经过去了 - by Mr.Zheng
description: Github本身就是不错的代码社区，他也提供了一些其他的服务，比如Github Pages，使用它可以很方便的建立自己的独立博客，并且免费。
category: blog
---


曾经，一个网页上要用Javascript实现网页内容的复制，其实是很麻烦的一件事情。虽然在这个问题上IE有其高大上的 `window.clipboardData` 方法支持直接复制指定内容，Firefox也早早的支持了 `document.execCommand` 命令，但是因为早期的Chrome不支持浏览器直接操作剪贴板，或者说不支持 `document.execCommand` 命令，让这一功能在兼容性上遇到了瓶颈。所以，聪明的开发者们开始走上“曲线救国”的道路：借助各大浏览器对Flash的支持，通过Javascript与Flash交互，将需要复制的内容传递到Flash中，再调用Flash操作剪切板的命令将内容复制到剪贴板，从而实现了兼容性极强的通过JS脚本复制网页文本的插件。这也就是[ZeroClipboard][]的使命。

The ZeroClipboard library provides an easy way to copy text to the clipboard using an invisible Adobe Flash movie and a JavaScript interface.

ZeroClipboard曾盛极一时，而如今它的未来，恐怕令人担忧。随着HTML5的风靡，Flash在网页应用中的地位受到了极大的冲击，甚至有很多人都认为Flash正在慢慢淡出网页开发者的世界，而事实也的确如此。不仅如此，自Chrome 43版本发布，增加 `document.execCommand` 命令支持，更让ZeroClipboard的地位严重受到威胁。为什么这么说呢？下面我们一步一步来说明。

##ZeroClipboard使用案例
通常情况下，ZeroClipord的应用场景大致是通过点击一个按钮复制一段指定的或用户输入的文本，HTML结构简单的做如下描述：

	<input type="text" name="" id="J_TextIn" value="Copy Test.">
	<input type="button" value="Copy" id="J_DoCopy">
	<script src="dist/ZeroClipboard.min.js"></script>

下面配上ZeroClipboard的方法绑定就大功告成了！


	(function(){
	    var btn = document.getElementById('J_DoCopy'),
	        text = document.getElementById('J_TextIn'),
	        zc = new ZeroClipboard(btn);
	    zc.on('beforecopy', function(e){
	        zc.setText(text.value);
	    });
	})();


我们预览页面，在文本框中输入任意字符，点击 `Copy` 按钮，文本框中输入的内容就被复制到剪贴板中了。我们可以在任意可写区域使用 `Ctrl+V` 或鼠标右键将内容进行粘贴。该案例可完美兼容各主流浏览器，包括IE6/7/8等骨灰级浏览器。

##现在不使用ZeroClipboard我们也能实现
首先，我们保证页面结构不变，但不在引入ZeroClipboard插件：

	<input type="text" name="" id="J_TextIn" value="Copy Test.">
	<input type="button" value="Copy" id="J_DoCopy">

然后，我们使用 `document.execCommamd` 来对内容进行复制：


	(function(){
	    var btn = document.getElementById('J_DoCopy'),
	        text = document.getElementById('J_TextIn');
	    btn.onclick = function(){
	        var transfer = document.getElementById('J_CopyTransfer');
	        if (!transfer) {
	            transfer = document.createElement('textarea');
	            transfer.id = 'J_CopyTransfer';
	            transfer.style.position = 'absolute';
	            transfer.style.left = '-9999px';
	            transfer.style.top = '-9999px';
	            document.body.appendChild(transfer);
	        }
	        transfer.value = text.value;
	        transfer.focus();
	        transfer.select();
	        document.execCommand('Copy', false, null);
	    };
	})();

接着，我们在浏览器中浏览，和使用ZeroClipboard时一样的去操作，效果是一样的。但是，这段代码的正确执行是有限制的，因为 `document.execCommand` 在Chrome中支持的比较晚，所以要求Chrome版本必须是43及以后。

最后，再补充说明一下，使用 `document.execCommand` 来实现复制内容时，过渡被复制内容的 `textarea` 标签（即：动态创建的那个textarea标签），在复制可输入区域（input:text,textarea）的内容时并不是必须的，可以直接简化为：


	(function(){
	    var btn = document.getElementById('J_DoCopy'),
	        text = document.getElementById('J_TextIn');
	    btn.onclick = function(){
	        text.focus();
	        text.select();
	        document.execCommand('Copy', false, null);
	        text.blur();
	    };
	})();

考虑到实际使用中，我们可能需要复制一些非编辑区域提供的内容（比如：一个a标签的链接地址等），所以增加了一个过渡的texearea，似乎更保险，更灵活一些。

此外，我再测试过程中，曾试图将过渡的那个textarea设置为不可见的 `visibility:hidden;` ，却发现复制功能失效了，所以这里需要注意一下…

[ZeroClipboard]:   http://http://zeroclipboard.org "ZeroClipboard"
