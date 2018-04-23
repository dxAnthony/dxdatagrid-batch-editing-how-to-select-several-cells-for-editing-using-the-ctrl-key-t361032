# dxDataGrid - Batch Editing - How to select several cells for editing using the CTRL key


<p>This example illustrates how to allow end used to select more than one cell for editing by holding the CTRL key. Then, when the user changes the editor text, the same text will be entered to all selected cells. </p>
<p>It is possible to implement this scenario by performing the following steps:</p>
<p>Create an array to store selected cells:</p>


```js
var editCells = [];

```


<p> Then, handle the <a href="http://js.devexpress.com/Documentation/ApiReference/UI_Widgets/dxDataGrid/Configuration/?version=15_2#onCellClick">onCellClick</a> event to add the selected cell to the array when the Ctrl key is held or clear the array if it is not:</p>


```js
onCellClick: function (e) {
    if (e.jQueryEvent.ctrlKey) {
        editCells.push(e.rowIndex + ":" + e.columnIndex);
    }
    else if (editCells.length) {
        editCells = [];
        e.component.repaint();
    }
},

```


<p> The <a href="http://js.devexpress.com/Documentation/ApiReference/UI_Widgets/dxDataGrid/Configuration/?version=15_2#onCellPrepared">onCellPrepared</a> event is handled to change the style of the selected row: </p>


```js
onCellPrepared: function (e) {
    if (e.rowType === "data" && $.inArray(e.rowIndex + ":" + e.columnIndex, editCells) >= 0) {
        e.cellElement.css("background-color", "lightblue");
    }
},

```


<p> After that, handle the <a href="http://js.devexpress.com/Documentation/ApiReference/UI_Widgets/dxDataGrid/Configuration/?version=15_2#onEditorPreparing">onEditorPreparing</a> event to set the text value for all selected cells when a user edits the last cell:</p>


```js
onEditorPreparing: function (e) {
    var grid = e.component;
    if (e.parentType === "dataRow") {
        var oldOnValueChanged = e.editorOptions.onValueChanged;
        e.editorOptions.onValueChanged = function (e) {
            oldOnValueChanged.apply(this, arguments);
            for (var i = 0; i < editCells.length; i++) {
                var rowIndex = Number(editCells[i].split(":")[0]);
                var columnIndex = Number(editCells[i].split(":")[1]);
                grid.cellValue(rowIndex, columnIndex, e.value);
            }
        }
    }
},

```


<p> To reset the selection when the Save or Cancel button is clicked, use the <a href="http://js.devexpress.com/Documentation/ApiReference/UI_Widgets/dxDataGrid/Configuration/?version=15_2#onContentReady">onContentReady</a> event:</p>


```js
onContentReady: function (e) {
    e.element.find(".dx-datagrid-save-button").click(function (e) {
        if (editCells.length)
            editCells = [];
    });
    e.element.find(".dx-datagrid-cancel-button ").click(function (e) {
        if (editCells.length)
            editCells = [];
    });
}
```



<br/>


