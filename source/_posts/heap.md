title: 二叉堆
date: 2016-07-14 09:49:07
tags:
- acm
- 数据结构
- 算法
- 可视化
categories:
- 算法可视化
---

二叉堆的应用场景很多，例如外排序多路归并，TopK问题，优先队列，排序

<!-- more -->

# 算法思想

堆是一个完全二叉树，也就是树的最后一层，节点都是靠左的,其上的层，都是满的。

另外一个性质，小(大)根堆除了根节点外的每一个节点的父节点都比该节点小(大)或相等。

## 插入

将待插入节点放置在树的最后，也就是如果最后一层不满，就靠左摆放，否则就增加新的一层，保证第一条性质。

插入之后，堆的第二条性质，有可能被打破了，（以下都以小根堆为例）该节点比其父节点小，交换其与父子节点，然后接着如此处理，直到该节点大于等于父节点，或者到达根节点。形象地看，就是向上冒泡。

## 删除

二叉堆只能删除掉根节点。

删除根节点后，将二叉堆最后一个节点放置到根处。

这时候第二条性质，可能被打破了，从根节点向下调整，若两个儿子都不小于当前节点，则结束，否则，找到儿子中小的那个与之交换，然后继续处理。

# 可视化

{% raw %}

<p>&#x4E8C;&#x53C9;&#x5806;&#x7684;&#x5E94;&#x7528;&#x573A;&#x666F;&#x5F88;&#x591A;&#xFF0C;&#x4F8B;&#x5982;&#x5916;&#x6392;&#x5E8F;&#x591A;&#x8DEF;&#x5F52;&#x5E76;&#xFF0C;TopK&#x95EE;&#x9898;&#xFF0C;&#x4F18;&#x5148;&#x961F;&#x5217;&#xFF0C;&#x6392;&#x5E8F;</p>
<a id="more"></a>
<h1 id="&#x7B97;&#x6CD5;&#x601D;&#x60F3;"><a href="#&#x7B97;&#x6CD5;&#x601D;&#x60F3;" class="headerlink" title="&#x7B97;&#x6CD5;&#x601D;&#x60F3;"></a>&#x7B97;&#x6CD5;&#x601D;&#x60F3;</h1><p>&#x5806;&#x662F;&#x4E00;&#x4E2A;&#x5B8C;&#x5168;&#x4E8C;&#x53C9;&#x6811;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x6811;&#x7684;&#x6700;&#x540E;&#x4E00;&#x5C42;&#xFF0C;&#x8282;&#x70B9;&#x90FD;&#x662F;&#x9760;&#x5DE6;&#x7684;,&#x5176;&#x4E0A;&#x7684;&#x5C42;&#xFF0C;&#x90FD;&#x662F;&#x6EE1;&#x7684;&#x3002;</p>
<p>&#x53E6;&#x5916;&#x4E00;&#x4E2A;&#x6027;&#x8D28;&#xFF0C;&#x5C0F;(&#x5927;)&#x6839;&#x5806;&#x9664;&#x4E86;&#x6839;&#x8282;&#x70B9;&#x5916;&#x7684;&#x6BCF;&#x4E00;&#x4E2A;&#x8282;&#x70B9;&#x7684;&#x7236;&#x8282;&#x70B9;&#x90FD;&#x6BD4;&#x8BE5;&#x8282;&#x70B9;&#x5C0F;(&#x5927;)&#x6216;&#x76F8;&#x7B49;&#x3002;</p>
<h3 id="&#x63D2;&#x5165;"><a href="#&#x63D2;&#x5165;" class="headerlink" title="&#x63D2;&#x5165;"></a>&#x63D2;&#x5165;</h3><p>&#x5C06;&#x5F85;&#x63D2;&#x5165;&#x8282;&#x70B9;&#x653E;&#x7F6E;&#x5728;&#x6811;&#x7684;&#x6700;&#x540E;&#xFF0C;&#x4E5F;&#x5C31;&#x662F;&#x5982;&#x679C;&#x6700;&#x540E;&#x4E00;&#x5C42;&#x4E0D;&#x6EE1;&#xFF0C;&#x5C31;&#x9760;&#x5DE6;&#x6446;&#x653E;&#xFF0C;&#x5426;&#x5219;&#x5C31;&#x589E;&#x52A0;&#x65B0;&#x7684;&#x4E00;&#x5C42;&#xFF0C;&#x4FDD;&#x8BC1;&#x7B2C;&#x4E00;&#x6761;&#x6027;&#x8D28;&#x3002;</p>
<p>&#x63D2;&#x5165;&#x4E4B;&#x540E;&#xFF0C;&#x5806;&#x7684;&#x7B2C;&#x4E8C;&#x6761;&#x6027;&#x8D28;&#xFF0C;&#x6709;&#x53EF;&#x80FD;&#x88AB;&#x6253;&#x7834;&#x4E86;&#xFF0C;&#xFF08;&#x4EE5;&#x4E0B;&#x90FD;&#x4EE5;&#x5C0F;&#x6839;&#x5806;&#x4E3A;&#x4F8B;&#xFF09;&#x8BE5;&#x8282;&#x70B9;&#x6BD4;&#x5176;&#x7236;&#x8282;&#x70B9;&#x5C0F;&#xFF0C;&#x4EA4;&#x6362;&#x5176;&#x4E0E;&#x7236;&#x5B50;&#x8282;&#x70B9;&#xFF0C;&#x7136;&#x540E;&#x63A5;&#x7740;&#x5982;&#x6B64;&#x5904;&#x7406;&#xFF0C;&#x76F4;&#x5230;&#x8BE5;&#x8282;&#x70B9;&#x5927;&#x4E8E;&#x7B49;&#x4E8E;&#x7236;&#x8282;&#x70B9;&#xFF0C;&#x6216;&#x8005;&#x5230;&#x8FBE;&#x6839;&#x8282;&#x70B9;&#x3002;&#x5F62;&#x8C61;&#x5730;&#x770B;&#xFF0C;&#x5C31;&#x662F;&#x5411;&#x4E0A;&#x5192;&#x6CE1;&#x3002;</p>
<h3 id="&#x5220;&#x9664;"><a href="#&#x5220;&#x9664;" class="headerlink" title="&#x5220;&#x9664;"></a>&#x5220;&#x9664;</h3><p>&#x4E8C;&#x53C9;&#x5806;&#x53EA;&#x80FD;&#x5220;&#x9664;&#x6389;&#x6839;&#x8282;&#x70B9;&#x3002;</p>
<p>&#x5220;&#x9664;&#x6839;&#x8282;&#x70B9;&#x540E;&#xFF0C;&#x5C06;&#x4E8C;&#x53C9;&#x5806;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x8282;&#x70B9;&#x653E;&#x7F6E;&#x5230;&#x6839;&#x5904;&#x3002;</p>
<p>&#x8FD9;&#x65F6;&#x5019;&#x7B2C;&#x4E8C;&#x6761;&#x6027;&#x8D28;&#xFF0C;&#x53EF;&#x80FD;&#x88AB;&#x6253;&#x7834;&#x4E86;&#xFF0C;&#x4ECE;&#x6839;&#x8282;&#x70B9;&#x5411;&#x4E0B;&#x8C03;&#x6574;&#xFF0C;&#x82E5;&#x4E24;&#x4E2A;&#x513F;&#x5B50;&#x90FD;&#x4E0D;&#x5C0F;&#x4E8E;&#x5F53;&#x524D;&#x8282;&#x70B9;&#xFF0C;&#x5219;&#x7ED3;&#x675F;&#xFF0C;&#x5426;&#x5219;&#xFF0C;&#x627E;&#x5230;&#x513F;&#x5B50;&#x4E2D;&#x5C0F;&#x7684;&#x90A3;&#x4E2A;&#x4E0E;&#x4E4B;&#x4EA4;&#x6362;&#xFF0C;&#x7136;&#x540E;&#x7EE7;&#x7EED;&#x5904;&#x7406;&#x3002;</p>
<h1 id="&#x6F14;&#x793A;&#x7A0B;&#x5E8F;"><a href="#&#x6F14;&#x793A;&#x7A0B;&#x5E8F;" class="headerlink" title="&#x6F14;&#x793A;&#x7A0B;&#x5E8F;"></a>&#x6F14;&#x793A;&#x7A0B;&#x5E8F;</h1><p><input id="text" type="text"> <button id="submit">insert</button> <button id="delete">delete</button></p>
<svg id="tree" width="1800" height="600" root_x="500" root_y="50" cheight="120" cwidth="50" creduis="20" cmax_ceng="4" cduration="1000"><br/></svg>

<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/4.1.1/d3.min.js"></script>
<script>

var svgContainer = d3.select("#tree");
var root_x = parseInt(svgContainer.attr("root_x"));
var root_y = parseInt(svgContainer.attr("root_y"));
var CHEIGHT = parseInt(svgContainer.attr("cheight"));
var CWIDTH = parseInt(svgContainer.attr("cwidth"));
var CREDUIS = parseInt(svgContainer.attr("creduis"));
var CMAX_CENG = parseInt(svgContainer.attr("cmax_ceng"));
var CDURATION = parseInt(svgContainer.attr("cduration"));

var nodes = [];
var data = [];

function cal_ceng(i) {
    var ii = i + 1;
    var ceng = 0;
    while(ii>>ceng > 0)ceng+=1;
    return ceng-1;
}

function cal_max_ceng(nodes) {
    var len = nodes.length;
    return cal_ceng(len);
}

function cal_x(i,max_ceng) {
    if(i==0)return root_x;

    var ceng = cal_ceng(i);
    var ceng_width = Math.pow(2,max_ceng-ceng) * CWIDTH;
    var ceng_num = i+1;
    var tmp = 1;
    while(ceng_num > tmp){
        ceng_num-=tmp;
        tmp=tmp<<1;
    }
    var ceng_ban = Math.pow(2,ceng-1)+0.5;
    return root_x+(ceng_num-ceng_ban)*ceng_width;
}

function cal_y(i) {
    return root_y + cal_ceng(i) * CHEIGHT;
}

function nodes(data) {

    var nodes = [];
    for(var i=0;i<data.length;i++){
        nodes.push({val:data[i],r:CREDUIS});
    }

    var max_ceng = cal_max_ceng(nodes);
    for(var i=0;i<nodes.length;i++){
        nodes[i].x = cal_x(i,max_ceng);
        nodes[i].y = cal_y(i);
    }
    return nodes;
}

function edges(nodes) {
    var edges = [];
    for(var i = 1;i < nodes.length; i++){
        var edge = {};
        edge.x1 = nodes[i].x;
        edge.y1 = nodes[i].y;
        edge.x2 = nodes[((i+1)>>1)-1].x;
        edge.y2 = nodes[((i+1)>>1)-1].y;
        edges.push(edge);
    }
    return edges;
}


//    var circles = svgContainer.selectAll("circle")
//            .data(nodes)
//            .enter()
//            .append("circle");
//
//    circles.transition()
//            .attr("cx",function(d){return d.x;})
//            .attr("cy",function(d){return d.y;})
//            .attr("r",function(d){return d.r});
//
//    var lines = svgContainer.selectAll("line")
//            .data(edges)
//            .enter()
//            .append("line");
//    lines.transition()
//            .attr("x1",function(d){return d.x1;})
//            .attr("y1",function(d){return d.y1;})
//            .attr("x2",function(d){return d.x2;})
//            .attr("y2",function(d){return d.y2;})
//            .attr("stroke","black")
//            .attr("stroke-width",5);
//
//    var texts = svgContainer.selectAll("text")
//            .data(nodes)
//            .enter()
//            .append("text");
//
//    texts.transition()
//            .text(function(d){return d.val;})
//            .attr("x",function(d){return d.x-CREDUIS/2;})
//            .attr("y",function(d){return d.y+CREDUIS/3;})
//            .attr("fill","white")
//            .attr("font-size",22);

function view_text(node) {
    var text = svgContainer.append("text")
        .text(node.val)
        .attr("x",node.x-CREDUIS/2)
        .attr("y",node.y+CREDUIS/3)
        .attr("fill","white")
        .attr("font-size",22);
    return text;
}



function view_insert(node) {
    var index = nodes.length;
    nodes.push(node);
    var x = cal_x(index,CMAX_CENG);
    var y = cal_y(index);
    node.x = x;
    node.y = y;
    node.circle.transition()
        .duration(CDURATION)
        .attr("cx",x)
        .attr("cy",y);
    if(index>0){
        var father = ((index+1)>>1)-1;
        var x1 = nodes[father].x, y1 = nodes[father].y, x2 = x, y2 = y;
        var c = Math.sqrt((x1-x2)*(x1-x2)+(y1-y2)*(y1-y2));
        var edge = svgContainer
            .append("line")
            .attr("x1",x1+CREDUIS*(x2-x1)/c)
            .attr("y1",y1+CREDUIS*(y2-y1)/c)
            .attr("x2",x2-CREDUIS*(x2-x1)/c)
            .attr("y2",y2-CREDUIS*(y2-y1)/c)
            .attr("stroke","black")
            .attr("stroke-width",5);

        node.edge = edge;
    }

    node.text.remove();
    node.text = view_text(node);
}


function view_node_init(val) {
    var x=0,y=0;
    var circle =svgContainer.append("circle")
        .attr("cx",x)
        .attr("cy",y)
        .attr("r",CREDUIS);
    var text = svgContainer.append("text")
        .text(val)
        .attr("x",x-CREDUIS/2)
        .attr("y",y+CREDUIS/3)
        .attr("fill","white")
        .attr("font-size",22);
    return {circle:circle,text:text,x:x,y:y,val:val}
}

function view_change(index,father) {

    var x1 = nodes[index].x;
    var y1 = nodes[index].y;
    var x2 = nodes[father].x;
    var y2 = nodes[father].y;
    nodes[index].circle.transition().attr("cx",x2).attr("cy",y2);
    nodes[father].circle.transition().attr("cx",x1).attr("cy",y1);
    nodes[index].text.transition().attr("x",x2-CREDUIS/2).attr("y",y2+CREDUIS/3);
    nodes[father].text.transition().attr("x",x1-CREDUIS/2).attr("y",y1+CREDUIS/3);

    var tmp = nodes[index];
    nodes[index] = nodes[father];
    nodes[father] = tmp;

    tmp = nodes[index].edge;
    nodes[index].edge = nodes[father].edge;
    nodes[father].edge = tmp;
    nodes[index].x = x1;
    nodes[index].y = y1;
    nodes[father].x = x2;
    nodes[father].y = y2;
}

function disable_buttons() {
    document.getElementById("submit").disabled=true;
    document.getElementById("delete").disabled=true;
}

function undisable_buttons() {
    document.getElementById("submit").disabled=false;
    document.getElementById("delete").disabled=false;
}

function view_ajust_from_leaf(index) {
    if(index==0){
        undisable_buttons();
        return ;
    }
    setTimeout(function () {

        var father = ((index+1)>>1)-1;
        if(nodes[index].val<nodes[father].val){
            view_change(index,father);
            view_ajust_from_leaf(father);
        }else{
            undisable_buttons();
        }
    },1000);
}

function view_ajust_from_head(index) {
    var lson = ((index+1)<<1)-1,rson = ((index+1)<<1|1)-1;
    var len = nodes.length;
    if(lson>=len){
        undisable_buttons();
        return ;
    }
    var son = -1;
    if(rson>=len){
        son = lson;
    }else if(nodes[lson].val <= nodes[rson].val){
        son = lson;
    }else{
        son = rson;
    }
    if(nodes[index].val <= nodes[son].val){
        undisable_buttons();
        return ;
    }

    setTimeout(function () {
        view_change(son,index);
        view_ajust_from_head(son);
    },1000);

}

function view_delete() {
    var x = nodes[0].x, y = nodes[0].y;
    nodes[0].circle.transition().attr("cx",0).attr("cy",0).remove();
    nodes[0].text.transition().attr("x",0).attr("y",0).remove();

    nodes[0] = nodes[nodes.length-1];
    nodes.pop();
    nodes[0].x = x;
    nodes[0].y = y;
    nodes[0].circle.transition().attr("cx",x).attr("cy",y);
    nodes[0].text.transition().attr("x",x-CREDUIS/2).attr("y",y+CREDUIS/3);
    nodes[0].edge.remove();
}

d3.select("#submit").on("click",function () {
    var val = document.getElementById("text").value;
    if(val.length > 2 || !/^[0-9]+$/.test(val)){
        alert("只能输入两为数字")
        return ;
    }
    val =  parseInt(val);
    disable_buttons();
    var node = view_node_init(val);
    view_insert(node);
    view_ajust_from_leaf(nodes.length-1);
});

d3.select("#delete").on("click",function () {
    disable_buttons();
    view_delete();
    if(nodes.length==0){
        undisable_buttons();
        return ;
    }
    view_ajust_from_head(0);
});

</script>

{% endraw %}

# 示例代码

虽然是树，但是由于完全二叉树的性质，可以很方便地使用数组模拟树结构，数组的头一个元素不用，下标１对应树根，对于节点i，i<<1为左儿子，i<<1|1为右儿子，i>>1为父节点。

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.Random;
import java.util.stream.Stream;

public class Heap<E> {

	public static void main(String... args) throws Exception {
		Heap<Double> heap = new Heap<Double>(DOUBLE_LITTLE_ROOT);
		Random r = new Random();
		Stream.generate(() -> r.nextDouble()).limit(10).forEach(val -> heap.push(val));
		System.out.println(heap.size);
		while (!heap.isEmpty()) {
			System.out.println(heap.pop());
		}
	}

	public static final Comparator<Integer> INTEGER_LITTLE_ROOT = (x, y) -> ((Integer) x).compareTo((Integer) y);
	public static final Comparator<Integer> INTEGER_BIG_ROOT = (x, y) -> ((Integer) y).compareTo((Integer) x);
	public static final Comparator<Double> DOUBLE_LITTLE_ROOT = (x, y) -> ((Double) x).compareTo((Double) y);
	public static final Comparator<Double> DOUBLE_BIT_ROOT = (x, y) -> ((Double) y).compareTo((Double) x);

	private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
	private static final Object[] EMPTY_ELEMENTDATA = {};
	private static final int DEFAULT_CAPACITY = 10;
	private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

	private Comparator<E> cmp;
	private Object[] elementData;
	private int size;

	public Heap(int initialCapacity, Comparator<E> cmp) {
		if (initialCapacity > 0) {
			this.elementData = new Object[initialCapacity];
		} else if (initialCapacity == 0) {
			this.elementData = EMPTY_ELEMENTDATA;
		} else {
			throw new IllegalArgumentException("Illegal Capacity: " + initialCapacity);
		}
		this.cmp = cmp;
		size = 1;
	}

	public Heap(Comparator<E> cmp) {
		this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
		this.cmp = cmp;
		size = 1;
	}

	private void ensureCapacityInternal(int minCapacity) {
		if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
			minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
		}
		ensureExplicitCapacity(minCapacity);
	}

	private void ensureExplicitCapacity(int minCapacity) {
		if (minCapacity - elementData.length > 0)
			grow(minCapacity);
	}

	private void grow(int minCapacity) {
		int oldCapacity = elementData.length;
		int newCapacity = oldCapacity + (oldCapacity >> 1);
		if (newCapacity - minCapacity < 0)
			newCapacity = minCapacity;
		if (newCapacity - MAX_ARRAY_SIZE > 0)
			newCapacity = hugeCapacity(minCapacity);
		elementData = Arrays.copyOf(elementData, newCapacity);
	}

	private static int hugeCapacity(int minCapacity) {
		if (minCapacity < 0)
			throw new OutOfMemoryError();
		return (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE : MAX_ARRAY_SIZE;
	}

	@SuppressWarnings("unchecked")
	public void push(E val) {
		ensureCapacityInternal(size + 1);

		elementData[size] = val;
		int pos = size++;
		while ((pos >> 1) != 0) {
			if (cmp.compare((E) elementData[pos], (E) elementData[pos >> 1]) < 0) {
				swap(elementData, pos, pos >> 1);
				pos = pos >> 1;
			} else
				break;
		}
	}

	public boolean isEmpty() {
		return size == 1;
	}

	@SuppressWarnings("unchecked")
	public E pop() throws Exception {
		if (size == 1)
			throw new Exception("heap is empty");
		E top = (E) elementData[1];
		elementData[1] = elementData[--size];
		int pos = 1;
		while (pos < size) {
			int sonPos = pos << 1;
			if (sonPos >= size)
				break;
			if (cmp.compare((E) elementData[sonPos | 1], (E) elementData[sonPos]) < 0 && (sonPos | 1) < size) {
				sonPos = sonPos | 1;
			}
			if (cmp.compare((E) elementData[pos], (E) elementData[sonPos]) <= 0) {
				break;
			}
			swap(elementData, pos, sonPos);
			pos = sonPos;
		}
		return top;
	}

	private void swap(Object[] elementData, int a, int b) {
		Object tmp = elementData[a];
		elementData[a] = elementData[b];
		elementData[b] = tmp;
	}

	public int size() {
		return size-1;
	}
}
```
