# 移动端屏幕适配

移动端屏幕适配是一种使页面在各种移动端设备都能够正常显示的移动端开发方案。

> 移动端屏幕适配只需要考虑移动端，响应式布局还需要考虑PC端。

移动端屏幕适配本质上是要求页面的内容能够随着屏幕宽度等比例变化。

## 适配方案

### 简单适配方案

简单适配方案不考虑dpr，要求页面不能缩放。

**原理**

元素内容宽高使用rem作为单位，1 rem 等同于html 的font-size值，将该font-size值设置为视口宽度除以一个固定系数，在屏幕宽度改变时，通过js调整font-size大小，进而实现页面内容等比例变化。

**js实现**

```javascript
function getPageWidth(){
    const boundingRect = document.documentElement.getBoundingClientRect();
    return boundingRect.width||window.innerWidth;
}
function setRemUnit(ratio){
    const docEl = document.documentElement;
    return ()=>{
        docEl.style.fontSize = getPageWidth()/ratio + "px";
    }
}
window.addEventListener("resize",setRemUnit(18.75));
setRemUnit(18.75)();
```

### 通用适配方案

**原理**

通用适配方案在简单适配的基础上，通过控制initial-scale，使之与dpr的乘积为1，从而令页面的css像素与设备物理像素宽高相等。

**js实现**

```javascript
function getViewportDom(){
    let result = document.querySelector('meta[name="viewport"]');
    if(!result){
        result = document.createElement('meta');
        result.setAttribute('name','viewport');
        document.head.appendChild(result);
    }
    return result;
}
function getDpr(){
    let result = window.devicePixelRatio||1;
    return Math.floor(result);
}
function setInitialScale(){
    const scale = 1/getDpr();
    const CONTENT = `width=device-width,initial-scale=${scale},maximum-scale=${scale},minimum-scale=${scale},user-scalable=no`;
    getViewportDom().setAttribute('content',CONTENT);
}
setInitialScale();
document.documentElement.setAttribute('dpr-data',getDpr());
function setRemUnit(ratio,maxWidth,minWidth){
    const docEl = document.documentElement;
    function getWidth(){
        const viewWidth = getPageWidth();
        const Dpr = getDpr();
        if(maxWidth&&viewWidth/Dpr>maxWidth){
            return maxWidth*Dpr;
        }
        if(minWidth&&viewWidth/Dpr<minWidth){
            return minWidth*Dpr;
        }
        return viewWidth;
    }
    return ()=>{
        docEl.style.fontSize = getWidth()/ratio + "px";
    }
}
```

