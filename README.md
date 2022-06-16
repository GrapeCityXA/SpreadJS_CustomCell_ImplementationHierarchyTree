# SpreadJS_CustomCell_ImplementationHierarchyTree
在纯前端在线表格中实现自定义单元格实现层级树功能
### SpreadJS 示例，基于 JavaScript组件实现基于自定义单元格的zTree树

该示例包括使用 SpreadJS API 的演示脚本，可用于实现包含合并单元格的数据绑定。
有关 SpreadJS API 的更多信息，请参阅[SpreadJS API指南]( https://demo.grapecity.com.cn/spreadjs/help/api/) 和[帮助手册]( https://help.grapecity.com.cn/pages/viewpage.action?pageId=5963808)。
树。
有关 SpreadJS API 的更多信息，请参阅SpreadJS API 指南和帮助手册。
 

目录：
-	运行步骤
-	控件初始化
-	示例代码
-	关于 SpreadJS
外部文件：
-	临时授权申请



### 运行步骤
1、在开始之前，请确保您已满足以下先决条件：

要运行 SpreadJS，浏览器必须支持 HTML5，客户端导入和导出 Excel 需要 IE10及以上。请先了解 [SpreadJS 的产品使用环境]( https://www.grapecity.com.cn/developer/spreadjs/selection-guide/product-use-environment)，并申请临时部署授权激活
安装并更新NodeJS和NPM
2、克隆或下载此代码库
3、初始化控件，并运行示例脚本

#### 控件初始化
1、	首先，创建一个新页面，并在页面上输入以下代码：
```
<!DOCTYPE html>
    <html>
    <head>
        <title>Spread HTML test page</title>
```
2、在页面中添加对 Spread.JS 的引用。代码如下。需要注意的是，Spread 提供压缩过
```
（minified）的 JavaScript 文件和和用于调试的文件：
<script src="[Your_Scripts_Path]/gc.spread.sheets.all.xxxx.min.js" type="text/javascript"></script>
```

3、添加 CSS 文件以改变Spread.JS 的外观。默认的CSS文件名为： 
gc.spread.sheets.xxxx.css，里面包含了所有的默认样式。该 CSS 文件将会影响滚动条，筛选框及其子元素，单元格和下方标签栏的样式。引入 CSS 的代码如下：

```
//<link href="[Your_CSS_Path]/gc.spread.sheets.xxxx.css" rel="stylesheet" type="text/css"/>
//OR
<link href="[Your_CSS_Path]/bootstrap/bootstrap.min.css" rel="stylesheet" type="text/css"/>
<link href="[Your_CSS_Path]/bootstrap/bootstrap-theme.min.css" rel="stylesheet" type="text/css"/>
```
4、添加产品授权，代码为：
```
GC.Spread.Sheets.LicenseKey = "xxx";
```
5. 添加控件初始化代码。本例会在一个 id 为“ss”的 DOM 元素上初始化 Spread.Sheets：
```
<script type="text/javascript">
// Add your license
 GC.Spread.Sheets.LicenseKey = "xxx";
// Add your code
 window.onload = function(){
var spread = new GC.Spread.Sheets.Workbook(document.getElementById("ss"),{sheetCount:3});
var sheet = spread.getActiveSheet();
 }
</script>
</head>
<body>
```
6、 创建一个 id 为 “ss”的元素，Spread.Sheets 将在该 DOM 中初始化：
```
<div id="ss" style="height: 500px; width: 800px"></div>
</body>
</html>
```
#### 示例代码
```
HTML：
    <p>自定义单元格实现zTree树</p>
    <div id="ss"></div>
CSS：
    p{
        color: #90156b;
        text-align: center;
    }
    
    #ss{
        width: 100%;
        height: 480px;
    }
JavaScript：
    //需要引入ztree css js
    //css: https://lib.baomitu.com/zTree.v3/3.5.42/css/zTreeStyle/zTreeStyle.min.css
    //js:  https://lib.baomitu.com/zTree.v3/3.5.42/js/jquery.ztree.all.min.js
    var keyword_null = null, keyword_undefined = undefined;
        var DefaultDropDownButtonWidth = 17;
    	var ComboTreeCellType = function () { ComboTreeCellType.treeID = 0; };
    	ComboTreeCellType.prototype = new GC.Spread.Sheets.CellTypes.ComboBox();
    	
    	ComboTreeCellType.prototype.createEditorElement = function (context) {
    		var self = this, sheet = context.sheet;
    		var zTree = $('<ul class="ztree"></ul>');
    		self.treeID = "tree" + ComboTreeCellType.treeID++;
    		var setting = {
    			treeId: self.treeID,
    			callback: {
    				onClick: function (data, treeId, treeNode) {
    					self.selectedNode = treeNode;
    					sheet.endEdit();
    				}
    			}
    		};
    		$.fn.zTree.init(zTree, setting, this.items());
    		var editor = $('<div gcUIElement="ComboTree" style="background-color:white;max-height:400px;border-style:solid;border-width:thin;border-color:black;overflow:scroll"></div>');
    		editor.append(zTree);
    		editor[0].comboBox = zTree[0];
    		return editor[0];
        }
    	ComboTreeCellType.prototype.onItemSelect = function (data, treeId, treeNode) {
    		console.log("data:"+data);
    		console.log("treeId:"+treeId);
    		console.log("treeNode:"+treeNode);
    	}
    	ComboTreeCellType.prototype.getEditorValue = function (editorContext, context) {
    		var zTree = editorContext;
    		if (this.selectedNode) {
    			var row = context.row;
    			var col = context.col;
    			var cell = context.sheet.getCell(row,col);
    			return this.selectedNode;
    		}
    		return "";
    	}
    	
    	var oldpaint = ComboTreeCellType.prototype.paint;
    	ComboTreeCellType.prototype.paint = function (ctx, value, x, y, w, h, style, options) {
    	
    		if(value){
    			oldpaint.call(this, ctx, value.name , x, y, w, h, style, options);
    		}else{
    			oldpaint.call(this, ctx, value, x, y, w, h, style, options);
    		}
    
    	};
    	ComboTreeCellType.prototype.setEditorValue = function (editorContext, value, context) {
    		var treeObj = $.fn.zTree.getZTreeObj(self.treeID);
    		var nodes = treeObj.getNodes();
    		if (value) {
    			var node = this.findNode(nodes, value);
    			if (node) {
    				treeObj.selectNode(node);
    				this.selectedNode = node;
    			} else {
    				treeObj.selectNode(null);
    			}
    		} else {
    			treeObj.selectNode(null);
    		}
    	}
    
    	ComboTreeCellType.prototype.findNode = function (nodes, name) {
    		for (var i = 0; i < nodes.length; i++) {
    			if (nodes[i].name === name) {
    				return nodes[i];
    			}
    			if (nodes[i].children && nodes[i].children.length > 0) {
    				var node = this.findNode(nodes[i].children, name);
    				if (node) {
    					return node;
    				}
    			}
    		}
    		return null;
    	}
    
    	
    	ComboTreeCellType.prototype.updateEditor = function (editorContext, cellStyle, cellRect, context) {
    		var sheet = editorContext && context && context.sheet;
    		if (!sheet) {
    			return;
    		}
    		var comboBox = editorContext.comboBox;
    		if (cellStyle && comboBox) {
    			var render = sheet._render;
    			// 将 Cell 样式设置给Combo
    		}
    		if (cellRect && comboBox) {
    			var offset = $(editorContext.parentNode).position();
    
    			$(editorContext).width(140);
                $(editorContext).height(200);
                return {height:200,width:140};
    		}
    	};
    	
    	
    	ComboTreeCellType.prototype.processMouseDown = function (hitInfo) {
    		var sheet = hitInfo.sheet, sheetArea = hitInfo.sheetArea;
    		if ((sheetArea === keyword_null || sheetArea === keyword_undefined || sheetArea === 3 /* viewport */) && hitInfo.isReservedLocation && sheet) {
    			sheet.startEdit();
    		}
    	};
    
    	$(document).ready(function () {
    		var spread = new GC.Spread.Sheets.Workbook(document.getElementById("ss"), {sheetCount: 2});
    		var sheet = spread.getActiveSheet();
    		
    		var items = [
    			{
    				id:1,
    				name: "北京", open: true,
    				children: [
    					{
    						id:11,
    						name: "海淀区",
    					},
    					{
    						id:12,
    						name: "朝阳区",
    					}
    				]
    			},
    			{
    				id:2,
    				name: "上海", open: true,
    				children: [
    					{
    						id:21,
    						name: "浦东区",
    					}
    				]
    			},
    			{
    				id:3,
    				name: "天津",
    			},
    			{
    				id:4,
    				name: "重庆",
    			},
    			{
    				id:5,
    				name: "陕西省", open: true,
    				children: [
    					{
    						id:51,
    						name: "西安市",
    						children: [
    							{
    								id:511,
    								name: "雁塔区",
    							},
    							{
    								id:512,
    								name: "莲湖区",
    							}
    						]
    					},
    					{
    						id:52,
    						name: "宝鸡市",
    					}
    				]
    			},
    		];
    
    
    
    		var cellType = new ComboTreeCellType();
    		cellType.items(items);
    
    		sheet.setCellType(0, 0, cellType);
    		sheet.setCellType(0, 2, cellType);
    
    	});

```
#### 关于 SpreadJS
[SpreadJS]( https://www.grapecity.com.cn/developer/spreadjs) 是一款基于 HTML5 的纯前端表格控件，兼容 450 多种 Excel 公式，具备“高性能、跨平台、与 Excel 高度兼容”的产品特性。使用 SpreadJS，可直接在 Angular、 React、 Vue 等前端框架中实现高效的模板设计、在线编辑和数据绑定等功能，为最终用户提供高度类似 Excel 的使用体验。
 

