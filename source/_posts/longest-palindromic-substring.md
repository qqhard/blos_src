title: 最长回文子串
date: 2016-07-11 09:49:07
tags:
- acm
- 数据结构
- 算法
- 可视化
categories:
- 算法可视化
---

给一个字符串，求最长的回文子串。女朋友说Manacher看不懂，我专门写了图形演示的程序。她看了之后，一会儿就ac了。


<!-- more -->

# 暴力解法

遍历字符串，每次以一个位置为中心(字符或两个字符)，向两边扩展，这样的解法时间复杂度是O(n*n)。

## Manacher算法

单纯暴力的方法，没有利用一个有效的信息，就是在当前扩展中心位置ｉ之前的位置，我们已经求出了它们为中心的最长回文串，并且在某个长回文串中，我们有可能找到关于这个回文串中心轴对称的点。

首先，算法的大致框架和暴力解法相同，枚举字符串的每一个字符，从左到右，对于每一个字符，向两边扩展，求出以该点为中心的最大的回文子串。

在这个过程中，我们要维护以下的数据结构：

ext[]，这个数组的大小和母串长度相同，ext[i]代表从ｉ位置向两方扩展的长度，举例，abc的话，ext[1]=1;aba的话,ext[1]=2

mid,向右扩展能够达到最右的回文子串的中心下标。

max_mid,整个算法过程中，最长回文子串的中心下标。

在开始算法前，要完成一个预处理。因为回文子串有两种，偶数长和奇数长，分情况处理起来比较麻烦。我们把原串的中间和两侧都插入一个不会在串中存在的字符，开头再加另外一个不会出现的字符，结尾加\0。例如，aba变成了^#a#b#a#,abba变成了^#a#b#b#a#。这样处理的好处，无论原串的奇偶，都处理成了奇数，只有一种情况，而且还不影响原来的结果，开头加一个唯一的字符，是为了少判断个数组越界。

好了，想好了数据结构，做好了预处理，就可以开始算法了，即在算法的大致框架中维护这些数据结构。

对于当前的遍历到的位置i，我们准备向两边扩展得到以它为中心的最长回文子串了，在这个干之前，我们看看有啥可以利用的。我们已经知道mid是已知向右最远回文子串的中心下标，那么这个回文串最远覆盖的位置就是mid+ext[mid]-1,如果mid+ext[mid]>i，这样的情况出现，先窃喜一下，因为我们可以知道i关于mid对称的位置mid2-i的情况ext[mid2-i],想象一下，以mid2-i为中心的最长回文子串被以mid为中心的回文子串完全覆盖，是不是ext[i]=ext[mid2-i]了啊。但是如果超出里mid回文的范围，ext[i]至少可以扩展到mid回文的边界，也就是ext[i]=ext[mid]+mid-i（好好算下下标）。

好了，ext[i]根据上面的有效信息，已经得到一个不错的开始，倘若i就在mid回文之外呢，我靠，那就ext[i]=1开始呗。无论如何，这一步都要接着像暴力那样扩展。

扩展结束了，维护一波mid,维护一波max_mid。

# 演示程序

这有一个演示程序，蓝色代表当前的回文扩展，绿色代表最右覆盖的回文，红色代表最长的回文。

{% raw %}

<p>&#x7ED9;&#x4E00;&#x4E2A;&#x5B57;&#x7B26;&#x4E32;&#xFF0C;&#x6C42;&#x6700;&#x957F;&#x7684;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x3002;&#x5973;&#x670B;&#x53CB;&#x8BF4;Manacher&#x770B;&#x4E0D;&#x61C2;&#xFF0C;&#x6211;&#x4E13;&#x95E8;&#x5199;&#x4E86;&#x56FE;&#x5F62;&#x6F14;&#x793A;&#x7684;&#x7A0B;&#x5E8F;&#x3002;&#x5979;&#x770B;&#x4E86;&#x4E4B;&#x540E;&#xFF0C;&#x4E00;&#x4F1A;&#x513F;&#x5C31;ac&#x4E86;&#x3002;<br><a id="more"></a></p>
<h1 id="&#x66B4;&#x529B;&#x89E3;&#x6CD5;"><a href="#&#x66B4;&#x529B;&#x89E3;&#x6CD5;" class="headerlink" title="&#x66B4;&#x529B;&#x89E3;&#x6CD5;"></a>&#x66B4;&#x529B;&#x89E3;&#x6CD5;</h1><p>&#x904D;&#x5386;&#x5B57;&#x7B26;&#x4E32;&#xFF0C;&#x6BCF;&#x6B21;&#x4EE5;&#x4E00;&#x4E2A;&#x4F4D;&#x7F6E;&#x4E3A;&#x4E2D;&#x5FC3;(&#x5B57;&#x7B26;&#x6216;&#x4E24;&#x4E2A;&#x5B57;&#x7B26;)&#xFF0C;&#x5411;&#x4E24;&#x8FB9;&#x6269;&#x5C55;&#xFF0C;&#x8FD9;&#x6837;&#x7684;&#x89E3;&#x6CD5;&#x65F6;&#x95F4;&#x590D;&#x6742;&#x5EA6;&#x662F;O(n*n)&#x3002;</p>
<h1 id="Manacher&#x7B97;&#x6CD5;"><a href="#Manacher&#x7B97;&#x6CD5;" class="headerlink" title="Manacher&#x7B97;&#x6CD5;"></a>Manacher&#x7B97;&#x6CD5;</h1><p>&#x5355;&#x7EAF;&#x66B4;&#x529B;&#x7684;&#x65B9;&#x6CD5;&#xFF0C;&#x6CA1;&#x6709;&#x5229;&#x7528;&#x4E00;&#x4E2A;&#x6709;&#x6548;&#x7684;&#x4FE1;&#x606F;&#xFF0C;&#x5C31;&#x662F;&#x5728;&#x5F53;&#x524D;&#x6269;&#x5C55;&#x4E2D;&#x5FC3;&#x4F4D;&#x7F6E;&#xFF49;&#x4E4B;&#x524D;&#x7684;&#x4F4D;&#x7F6E;&#xFF0C;&#x6211;&#x4EEC;&#x5DF2;&#x7ECF;&#x6C42;&#x51FA;&#x4E86;&#x5B83;&#x4EEC;&#x4E3A;&#x4E2D;&#x5FC3;&#x7684;&#x6700;&#x957F;&#x56DE;&#x6587;&#x4E32;&#xFF0C;&#x5E76;&#x4E14;&#x5728;&#x67D0;&#x4E2A;&#x957F;&#x56DE;&#x6587;&#x4E32;&#x4E2D;&#xFF0C;&#x6211;&#x4EEC;&#x6709;&#x53EF;&#x80FD;&#x627E;&#x5230;&#x5173;&#x4E8E;&#x8FD9;&#x4E2A;&#x56DE;&#x6587;&#x4E32;&#x4E2D;&#x5FC3;&#x8F74;&#x5BF9;&#x79F0;&#x7684;&#x70B9;&#x3002;</p>
<p>&#x9996;&#x5148;&#xFF0C;&#x7B97;&#x6CD5;&#x7684;&#x5927;&#x81F4;&#x6846;&#x67B6;&#x548C;&#x66B4;&#x529B;&#x89E3;&#x6CD5;&#x76F8;&#x540C;&#xFF0C;&#x679A;&#x4E3E;&#x5B57;&#x7B26;&#x4E32;&#x7684;&#x6BCF;&#x4E00;&#x4E2A;&#x5B57;&#x7B26;&#xFF0C;&#x4ECE;&#x5DE6;&#x5230;&#x53F3;&#xFF0C;&#x5BF9;&#x4E8E;&#x6BCF;&#x4E00;&#x4E2A;&#x5B57;&#x7B26;&#xFF0C;&#x5411;&#x4E24;&#x8FB9;&#x6269;&#x5C55;&#xFF0C;&#x6C42;&#x51FA;&#x4EE5;&#x8BE5;&#x70B9;&#x4E3A;&#x4E2D;&#x5FC3;&#x7684;&#x6700;&#x5927;&#x7684;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x3002;</p>
<p>&#x5728;&#x8FD9;&#x4E2A;&#x8FC7;&#x7A0B;&#x4E2D;&#xFF0C;&#x6211;&#x4EEC;&#x8981;&#x7EF4;&#x62A4;&#x4EE5;&#x4E0B;&#x7684;&#x6570;&#x636E;&#x7ED3;&#x6784;&#xFF1A;</p>
<p>ext[]&#xFF0C;&#x8FD9;&#x4E2A;&#x6570;&#x7EC4;&#x7684;&#x5927;&#x5C0F;&#x548C;&#x6BCD;&#x4E32;&#x957F;&#x5EA6;&#x76F8;&#x540C;&#xFF0C;ext[i]&#x4EE3;&#x8868;&#x4ECE;&#xFF49;&#x4F4D;&#x7F6E;&#x5411;&#x4E24;&#x65B9;&#x6269;&#x5C55;&#x7684;&#x957F;&#x5EA6;&#xFF0C;&#x4E3E;&#x4F8B;&#xFF0C;abc&#x7684;&#x8BDD;&#xFF0C;ext[1]=1;aba&#x7684;&#x8BDD;,ext[1]=2</p>
<p>mid,&#x5411;&#x53F3;&#x6269;&#x5C55;&#x80FD;&#x591F;&#x8FBE;&#x5230;&#x6700;&#x53F3;&#x7684;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x7684;&#x4E2D;&#x5FC3;&#x4E0B;&#x6807;&#x3002;</p>
<p>max_mid,&#x6574;&#x4E2A;&#x7B97;&#x6CD5;&#x8FC7;&#x7A0B;&#x4E2D;&#xFF0C;&#x6700;&#x957F;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x7684;&#x4E2D;&#x5FC3;&#x4E0B;&#x6807;&#x3002;</p>
<p>&#x5728;&#x5F00;&#x59CB;&#x7B97;&#x6CD5;&#x524D;&#xFF0C;&#x8981;&#x5B8C;&#x6210;&#x4E00;&#x4E2A;&#x9884;&#x5904;&#x7406;&#x3002;&#x56E0;&#x4E3A;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x6709;&#x4E24;&#x79CD;&#xFF0C;&#x5076;&#x6570;&#x957F;&#x548C;&#x5947;&#x6570;&#x957F;&#xFF0C;&#x5206;&#x60C5;&#x51B5;&#x5904;&#x7406;&#x8D77;&#x6765;&#x6BD4;&#x8F83;&#x9EBB;&#x70E6;&#x3002;&#x6211;&#x4EEC;&#x628A;&#x539F;&#x4E32;&#x7684;&#x4E2D;&#x95F4;&#x548C;&#x4E24;&#x4FA7;&#x90FD;&#x63D2;&#x5165;&#x4E00;&#x4E2A;&#x4E0D;&#x4F1A;&#x5728;&#x4E32;&#x4E2D;&#x5B58;&#x5728;&#x7684;&#x5B57;&#x7B26;&#xFF0C;&#x5F00;&#x5934;&#x518D;&#x52A0;&#x53E6;&#x5916;&#x4E00;&#x4E2A;&#x4E0D;&#x4F1A;&#x51FA;&#x73B0;&#x7684;&#x5B57;&#x7B26;&#xFF0C;&#x7ED3;&#x5C3E;&#x52A0;\0&#x3002;&#x4F8B;&#x5982;&#xFF0C;aba&#x53D8;&#x6210;&#x4E86;^#a#b#a#,abba&#x53D8;&#x6210;&#x4E86;^#a#b#b#a#&#x3002;&#x8FD9;&#x6837;&#x5904;&#x7406;&#x7684;&#x597D;&#x5904;&#xFF0C;&#x65E0;&#x8BBA;&#x539F;&#x4E32;&#x7684;&#x5947;&#x5076;&#xFF0C;&#x90FD;&#x5904;&#x7406;&#x6210;&#x4E86;&#x5947;&#x6570;&#xFF0C;&#x53EA;&#x6709;&#x4E00;&#x79CD;&#x60C5;&#x51B5;&#xFF0C;&#x800C;&#x4E14;&#x8FD8;&#x4E0D;&#x5F71;&#x54CD;&#x539F;&#x6765;&#x7684;&#x7ED3;&#x679C;&#xFF0C;&#x5F00;&#x5934;&#x52A0;&#x4E00;&#x4E2A;&#x552F;&#x4E00;&#x7684;&#x5B57;&#x7B26;&#xFF0C;&#x662F;&#x4E3A;&#x4E86;&#x5C11;&#x5224;&#x65AD;&#x4E2A;&#x6570;&#x7EC4;&#x8D8A;&#x754C;&#x3002;</p>
<p>&#x597D;&#x4E86;&#xFF0C;&#x60F3;&#x597D;&#x4E86;&#x6570;&#x636E;&#x7ED3;&#x6784;&#xFF0C;&#x505A;&#x597D;&#x4E86;&#x9884;&#x5904;&#x7406;&#xFF0C;&#x5C31;&#x53EF;&#x4EE5;&#x5F00;&#x59CB;&#x7B97;&#x6CD5;&#x4E86;&#xFF0C;&#x5373;&#x5728;&#x7B97;&#x6CD5;&#x7684;&#x5927;&#x81F4;&#x6846;&#x67B6;&#x4E2D;&#x7EF4;&#x62A4;&#x8FD9;&#x4E9B;&#x6570;&#x636E;&#x7ED3;&#x6784;&#x3002;</p>
<p>&#x5BF9;&#x4E8E;&#x5F53;&#x524D;&#x7684;&#x904D;&#x5386;&#x5230;&#x7684;&#x4F4D;&#x7F6E;i&#xFF0C;&#x6211;&#x4EEC;&#x51C6;&#x5907;&#x5411;&#x4E24;&#x8FB9;&#x6269;&#x5C55;&#x5F97;&#x5230;&#x4EE5;&#x5B83;&#x4E3A;&#x4E2D;&#x5FC3;&#x7684;&#x6700;&#x957F;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x4E86;&#xFF0C;&#x5728;&#x8FD9;&#x4E2A;&#x5E72;&#x4E4B;&#x524D;&#xFF0C;&#x6211;&#x4EEC;&#x770B;&#x770B;&#x6709;&#x5565;&#x53EF;&#x4EE5;&#x5229;&#x7528;&#x7684;&#x3002;&#x6211;&#x4EEC;&#x5DF2;&#x7ECF;&#x77E5;&#x9053;mid&#x662F;&#x5DF2;&#x77E5;&#x5411;&#x53F3;&#x6700;&#x8FDC;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x7684;&#x4E2D;&#x5FC3;&#x4E0B;&#x6807;&#xFF0C;&#x90A3;&#x4E48;&#x8FD9;&#x4E2A;&#x56DE;&#x6587;&#x4E32;&#x6700;&#x8FDC;&#x8986;&#x76D6;&#x7684;&#x4F4D;&#x7F6E;&#x5C31;&#x662F;mid+ext[mid]-1,&#x5982;&#x679C;mid+ext[mid]&gt;i&#xFF0C;&#x8FD9;&#x6837;&#x7684;&#x60C5;&#x51B5;&#x51FA;&#x73B0;&#xFF0C;&#x5148;&#x7A83;&#x559C;&#x4E00;&#x4E0B;&#xFF0C;&#x56E0;&#x4E3A;&#x6211;&#x4EEC;&#x53EF;&#x4EE5;&#x77E5;&#x9053;i&#x5173;&#x4E8E;mid&#x5BF9;&#x79F0;&#x7684;&#x4F4D;&#x7F6E;mid2-i&#x7684;&#x60C5;&#x51B5;ext[mid2-i],&#x60F3;&#x8C61;&#x4E00;&#x4E0B;&#xFF0C;&#x4EE5;mid2-i&#x4E3A;&#x4E2D;&#x5FC3;&#x7684;&#x6700;&#x957F;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x88AB;&#x4EE5;mid&#x4E3A;&#x4E2D;&#x5FC3;&#x7684;&#x56DE;&#x6587;&#x5B50;&#x4E32;&#x5B8C;&#x5168;&#x8986;&#x76D6;&#xFF0C;&#x662F;&#x4E0D;&#x662F;ext[i]=ext[mid2-i]&#x4E86;&#x554A;&#x3002;&#x4F46;&#x662F;&#x5982;&#x679C;&#x8D85;&#x51FA;&#x91CC;mid&#x56DE;&#x6587;&#x7684;&#x8303;&#x56F4;&#xFF0C;ext[i]&#x81F3;&#x5C11;&#x53EF;&#x4EE5;&#x6269;&#x5C55;&#x5230;mid&#x56DE;&#x6587;&#x7684;&#x8FB9;&#x754C;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;ext[i]=ext[mid]+mid-i&#xFF08;&#x597D;&#x597D;&#x7B97;&#x4E0B;&#x4E0B;&#x6807;&#xFF09;&#x3002;</p>
<p>&#x597D;&#x4E86;&#xFF0C;ext[i]&#x6839;&#x636E;&#x4E0A;&#x9762;&#x7684;&#x6709;&#x6548;&#x4FE1;&#x606F;&#xFF0C;&#x5DF2;&#x7ECF;&#x5F97;&#x5230;&#x4E00;&#x4E2A;&#x4E0D;&#x9519;&#x7684;&#x5F00;&#x59CB;&#xFF0C;&#x5018;&#x82E5;i&#x5C31;&#x5728;mid&#x56DE;&#x6587;&#x4E4B;&#x5916;&#x5462;&#xFF0C;&#x6211;&#x9760;&#xFF0C;&#x90A3;&#x5C31;ext[i]=1&#x5F00;&#x59CB;&#x5457;&#x3002;&#x65E0;&#x8BBA;&#x5982;&#x4F55;&#xFF0C;&#x8FD9;&#x4E00;&#x6B65;&#x90FD;&#x8981;&#x63A5;&#x7740;&#x50CF;&#x66B4;&#x529B;&#x90A3;&#x6837;&#x6269;&#x5C55;&#x3002;</p>
<p>&#x6269;&#x5C55;&#x7ED3;&#x675F;&#x4E86;&#xFF0C;&#x7EF4;&#x62A4;&#x4E00;&#x6CE2;mid,&#x7EF4;&#x62A4;&#x4E00;&#x6CE2;max_mid&#x3002;</p>
<h1 id="&#x6F14;&#x793A;&#x7A0B;&#x5E8F;"><a href="#&#x6F14;&#x793A;&#x7A0B;&#x5E8F;" class="headerlink" title="&#x6F14;&#x793A;&#x7A0B;&#x5E8F;"></a>&#x6F14;&#x793A;&#x7A0B;&#x5E8F;</h1><p>&#x8FD9;&#x6709;&#x4E00;&#x4E2A;&#x6F14;&#x793A;&#x7A0B;&#x5E8F;&#xFF0C;&#x84DD;&#x8272;&#x4EE3;&#x8868;&#x5F53;&#x524D;&#x7684;&#x56DE;&#x6587;&#x6269;&#x5C55;&#xFF0C;&#x7EFF;&#x8272;&#x4EE3;&#x8868;&#x6700;&#x53F3;&#x8986;&#x76D6;&#x7684;&#x56DE;&#x6587;&#xFF0C;&#x7EA2;&#x8272;&#x4EE3;&#x8868;&#x6700;&#x957F;&#x7684;&#x56DE;&#x6587;&#x3002;</p>
<p><div><br>    <input id="str" type="text"> <button id="submit">submit</button> <button id="next" disabled>next</button> <button id="display" disabled>display</button> <button id="stop" disabled>stop</button> <button id="back" disabled>back</button><br></div></p>
<canvas id="myCanvas" width="1800" height="200"><br>    Your browser does not support the HTML5 canvas tag.<br></canvas>


<script>
var c=document.getElementById("myCanvas");
var ctx=c.getContext("2d");
var string;
var states = [];
var state_index = 0;
var intervalevent=-1;

function initStr(ctx,s) {

   var ss="^#";
   for(var i=0;i<s.length;i++){
       ss+=s[i]+"#";
   }
   ss+="$";
   ctx.fillStyle="black";
   for(var i=0;i<ss.length;i++){
       ctx.strokeRect(i*30+50,50,30,30);
       ctx.font="20px Georgia";
       ctx.fillText(ss[i],i*30+60,70);
   }
   return ss;
}
function valid(s) {
   if(s.length==0){
       alert("empty string");
       return false;
   }
   if(s.length>20){
       alert("too long");
       return false;
   }
   if(!/^[a-z]+$/.test(s)){
       alert("only lowercase");
       return false;
   }
   return true;
}

function draw_line(start,end,color,level) {
   ctx.fillStyle=color;
   ctx.fillRect(start*30+50,90+level*10,(end-start+1)*30,5);
}

function draw_cycle(index,color,level) {
   ctx.beginPath();
   ctx.fillStyle=color;
   ctx.arc(index*30+65,92+level*10,5,0,2*Math.PI);
   ctx.closePath();
   ctx.fill();
}

function draw(ctx,s,state) {
   ctx.clearRect(0,0,1800,300);
   initStr(ctx,s);
   draw_cycle(state.index,"blue",0);
   if(state.ext_start<=state.ext_end)draw_line(state.ext_start,state.ext_end,"blue",0);
   draw_cycle(state.long_index,"green",1);
   if(state.long_ext_start<=state.long_ext_end)draw_line(state.long_ext_start,state.long_ext_end,"green",1);
   draw_cycle(state.max_index,"red",2);
   if(state.max_ext_start<=state.max_ext_end)draw_line(state.max_ext_start,state.max_ext_end,"red",2);
}

function copy( obj ){
   return JSON.parse( JSON.stringify( obj ) );
}

function algorithm(states) {
   var ss="^#";
   for(var i=0;i<string.length;i++){
       ss+=string[i]+"#";
   }
   ss+="$";
   var ext = [];
   for(var i=0;i<ss.length;i++)ext.push(0);
   var mid,max_mid;
   mid=max_mid=0;
   ext[0]=0;
   var state = {};
   for(i=2;i<ss.length;i++){
       state.index = i;
       state.ext_start=0;
       state.ext_end=-1;
       states.push(copy(state));
       if(ext[mid]+mid>i){
           ext[i]=Math.min(ext[mid]+mid-i,ext[mid*2-i]);
       }
       else ext[i]=1;
       state.ext_start=i-ext[i]+1;
       state.ext_end=i+ext[i]-1;
       states.push(copy(state));

       while(ss[i-ext[i]]==ss[i+ext[i]]){
           ext[i]++;
           state.ext_start=i-ext[i]+1;
           state.ext_end=i+ext[i]-1;
           states.push(copy(state));
       }
       if(ext[i]+i>ext[mid]+mid){
           mid=i;
           state.long_index=i;
           state.long_ext_start=i-ext[i]+1;
           state.long_ext_end=i+ext[i]-1;
           states.push(copy(state));
       }
       if(ext[i]>ext[max_mid]){
           max_mid=i;
           state.max_index=i;
           state.max_ext_start=i-ext[i]+1;
           state.max_ext_end=i+ext[i]-1;
           states.push(copy(state));
       }
   }
}

document.getElementById('submit').addEventListener('click', function(event) {
   var s = document.getElementById("str").value;
   document.getElementById("next").disabled=true;
   document.getElementById("back").disabled=true;
   string = s;
   if(!valid(s)){
       ctx.clearRect(0,0,1800,300);
       return;
   }
   states = [];
   algorithm(states);
   document.getElementById("next").disabled=false;
   document.getElementById("display").disabled=false;
   state_index=0;
   draw(ctx,string,states[state_index]);
}, false);

function beginInterval()
{
if(intervalevent != -1)
{
 clearInterval(intervalevent);
}
donext();
intervalevent=setInterval(donext,500);
}

function stopInterval()
{
if(intervalevent != -1)
{
 clearInterval(intervalevent);
}
intervalevent = -1 ;        
}

function donext()
{        
   state_index+=1;
   if(state_index<states.length){
       draw(ctx,string,states[state_index]);
       document.getElementById("back").disabled=false;
   }
   if(state_index==states.length-1){
       document.getElementById("next").disabled=true;
       document.getElementById("display").disabled=true;            
       document.getElementById("stop").disabled=true;
 stopInterval();            
   }
}

document.getElementById('next').addEventListener('click', function(event) {
donext();
}, false);

document.getElementById('back').addEventListener('click', function(event) {
   state_index-=1;
   if(state_index>=0){
       draw(ctx,string,states[state_index]);
       document.getElementById("next").disabled=false;
       document.getElementById("display").disabled=false;            
       document.getElementById("stop").disabled=false;
   }
   if(state_index==0){
       document.getElementById("back").disabled=true;
   }
}, false);

document.getElementById('display').addEventListener('click', function(event) {
   stopInterval();
beginInterval();        
   document.getElementById("display").disabled=true;        
   document.getElementById("stop").disabled=false;
}, false);

document.getElementById('stop').addEventListener('click', function(event) {
   stopInterval();
   document.getElementById("display").disabled=false;        
   document.getElementById("stop").disabled=true;
}, false);
</script>

{% endraw %}

# 代码

https://leetcode.com/problems/longest-palindromic-substring/

```c++
#include<stdio.h>
#include<string.h>
#include<stdlib.h>

int min(int a,int b){
    return a<b?a:b;
}

char* longestPalindrome(char* s) {
    char* ans = (char*)malloc(1005*sizeof(char));
    char* ss = (char*)malloc(2010*sizeof(char));
    int* ext = (int*)malloc(2010*sizeof(int));
    int len = strlen(s);
    int i,j,mid,max_mid;
    for(i=0;i<len;i++){
        ss[i+i+1] = 1;
        ss[i+i+2] = s[i];
    }
    ss[0]=2;ss[(len<<1)+1]=1;ss[(len<<2)+2]=0;

    mid=max_mid=0;
    ext[0]=0;
    for(i=2;i<(len<<1|1);i++){
        if(ext[mid]+mid>i)ext[i]=min(ext[mid]+mid-i,ext[(mid<<1)-i]);
        else ext[i]=1;
        while(ss[i-ext[i]]==ss[i+ext[i]])ext[i]++;
        if(ext[i]+i>ext[mid]+mid)mid=i;
        if(ext[i]>ext[max_mid])max_mid=i;
    }
    int cnt=0;
    for(i=max_mid-ext[max_mid]+1;i<max_mid+ext[max_mid];i++){
        if(ss[i]!=1&&ss[i]!=2)ans[cnt++]=ss[i];
    }
    ans[cnt]=0;
    return ans;
}


int main(){
    char s[10000];
    scanf("%s",s);
    printf("%s\n",longestPalindrome(s));
    return 0;
}
```
