# DIV+CSS布局

## 自动填满父级剩余宽度

**calc**

```css
   
    .father{
        width: 1000px;
        height: 100px;
        background-color: black;
    }

    .child-one{
        width: 250px;
        height: 100%;
        background-color: aqua;
        float: left;
    }

    .child-two{
        width: calc(100% - 250px);
        height: 100%;
        background-color: bisque;
        float: left;
    }

```

**flex-grow自动填充，flex-grow看剩下的部分被切割的比例，比如一个元素是1一个是2则两个宽度比为1:2**

```css
    .father{
        width: 1000px;
        height: 100px;
        background-color: black;
        display: flex;
    }

    .child-one{
        width: 250px;
        height: 100%;
        background-color: aqua;
    }

    .child-two{
        height: 100%;
        background-color: bisque;
        flex-grow: 1;
    }
```

