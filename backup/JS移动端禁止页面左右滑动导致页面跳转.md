```javaScript
//禁止左右滑动导致的移动端页面跳转


document.addEventListener('touchmove',function(){
    function fun(e){
        var tar = e.target;
        let target = tar;
    }
    let list = document.getElementsByClassName("list");
    let YXplan = document.getElementsByClassName("YXplan");
    if(target!=list || target==YXplan){
  var startX,startY;
document.addEventListener("touchstart",function(e){
 
    startX = e.targetTouches[0].pageX;
    startY = e.targetTouches[0].pageY;
});
 
document.addEventListener("touchmove",function(e){
 
    var moveX = e.targetTouches[0].pageX;
    var moveY = e.targetTouches[0].pageY;
    
    if(Math.abs(moveX-startX)>Math.abs(moveY-startY)){
        e.preventDefault();
    }
},{passive:false});}
})
```