<template>
        <a @click="screenShot" href="javascript:;" title="截屏">点击截屏</a>
</template>

<script>
import html2canvas from 'html2canvas';
import canvasToImage from '@/components/js/canvas2image';
 
export default {
    name: 'ScreenShots',
    data() {
        return {    
            cutFlag: false,
        };
    },
    created() {
        
    },
    mounted() {},
    methods: {
        // 截屏
        // 思路：
        // 拖拽状态 = 鼠标在元素上按下的时候{      
        //     拖拽状态 = 1      
        //     记录下鼠标的x和y坐标      
        //     记录下元素的x和y坐标      
        //    }   
        //  鼠标在元素上移动的时候{      
        //     如果拖拽状态是0就什么也不做。      
        //     如果拖拽状态是1，那么      
        //     元素y = 现在鼠标y - 原来鼠标y + 原来元素y      
        //     元素x = 现在鼠标x - 原来鼠标x + 原来元素x      
        //     }       
        //  鼠标在任何时候放开的时候{      
        //     拖拽状态 = 0      
        // } 

        screenShot(){
            let _this = this;
            this.cutFlag = true;
            var img,canvasWidth,canvasHeight;
            var test = document.querySelector('body'); //将jQuery对象转换为dom对象

            html2canvas(test,{
                useCORS: true,
            }).then(function(canvas) {
                // canvas宽度
                canvasWidth = canvas.width;
                // canvas高度
                canvasHeight = canvas.height;               
                canvas.id = 'myCanvas';   
                test.appendChild(canvas);
                img = canvasToImage.convertToImage(canvas,canvasWidth, canvasHeight);   
                           
                var wId = "w";
                var index = 0;
                var startX = 0,
                    startY = 0;
                var flag = false;
                var retcLeft = "0px",
                    retcTop = "0px",
                    retcHeight = "0px",
                    retcWidth = "0px";
                var canvasLeft = 0,
                    canvasTop = 0,
                    canvasHeight = 0,
                    canvasWidth = 0;
                document.onmousedown = function(e) {
                    if(!flag && _this.cutFlag){
                        flag = true;
                        try {
                            var evt = window.event || e;
                            var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
                            var scrollLeft = document.body.scrollLeft || document.documentElement.scrollLeft;
                            startX = evt.clientX + scrollLeft;
                            startY = evt.clientY + scrollTop;
                            index++;
                            var div = document.createElement("div");
                            div.id = wId + index;
                            div.className = "div";
                            div.style.marginLeft = startX + "px";
                            div.style.marginTop = startY + "px";
                            document.body.appendChild(div);
                        } catch (e) {
                           
                        }                               
                    } else{                        
                        flag = false;
                    }
                }
                document.onmouseup = function(e) {
                    if (flag&& _this.cutFlag) {
                        try {
                            var evt = window.event || e;
                           
                            if(startX !== evt.clientX || startY!== evt.clientY){
                                _this.cutFlag = false;
                                var arr = document.getElementsByClassName("div");
                                for(let i=0; i<arr.length; i++){
                                    if (arr[i] != null){
                                        arr[i].parentNode.removeChild(arr[i]);
                                    }
                                }               
                                var retCanvas = document.createElement('canvas');
                                var retCtx = retCanvas.getContext('2d');
                                retCanvas.width = canvasWidth;
                                retCanvas.height = canvasHeight;
                                retCanvas.style.background="#ffffff";
                                retCtx.drawImage(img, canvasLeft,canvasTop,canvasWidth,canvasHeight,0,0,canvasWidth,canvasHeight);         
                                var img2 = canvasToImage.convertToImage(retCanvas, canvasWidth, canvasHeight);                              
                                _this.blobToFile(_this.convertBase64UrlToBlob(img2.src));
                                var canvas2 = document.querySelector('#myCanvas');                       
                                canvas2.parentNode.removeChild(canvas2);
                            } else {
                                _this.cutFlag = false;
                                var arr = document.getElementsByClassName("div");
                                for(let i=0; i<arr.length; i++){
                                    if (arr[i] != null){
                                        arr[i].parentNode.removeChild(arr[i]);
                                    }
                                }
                                if(document.querySelector('#myCanvas')){
                                    var canvas2 = document.querySelector('#myCanvas');
                                    canvas2.parentNode.removeChild(canvas2);
                                }  
                            }                                                                                                     
                        } catch (e) {
                                                  
                        }
                    }else{
                        var arr = document.getElementsByClassName("div");
                        for(let i=0; i<arr.length; i++){
                            if (arr[i] != null)
                                arr[i].parentNode.removeChild(arr[i]);
                        }
                        
                        if(document.querySelector('#myCanvas')){
                            var canvas2 = document.querySelector('#myCanvas');
                            canvas2.parentNode.removeChild(canvas2);
                        }                                            
                    }
                }
                document.onmousemove = function(e) {
                    if (flag&& _this.cutFlag) {
                        try {
                            var evt = window.event || e;
                            var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
                            var scrollLeft = document.body.scrollLeft || document.documentElement.scrollLeft;
                            retcLeft = (startX - evt.clientX - scrollLeft > 0 ? evt.clientX + scrollLeft : startX) + "px";
                            canvasLeft = startX - evt.clientX - scrollLeft > 0 ? evt.clientX + scrollLeft : startX;
                            retcTop = (startY - evt.clientY - scrollTop > 0 ? evt.clientY + scrollTop : startY) + "px";
                            canvasTop = startY - evt.clientY - scrollTop > 0 ? evt.clientY + scrollTop : startY;
                            retcHeight = Math.abs(startY - evt.clientY - scrollTop) + "px";
                            canvasHeight = Math.abs(startY - evt.clientY - scrollTop);
                            retcWidth = Math.abs(startX - evt.clientX - scrollLeft) + "px";
                            canvasWidth = Math.abs(startX - evt.clientX - scrollLeft);
                            $(wId + index).style.marginLeft = retcLeft;
                            $(wId + index).style.marginTop = retcTop;                           
                            $(wId + index).style.width = retcWidth;                          
                            $(wId + index).style.height = retcHeight;                            
                                                    
                        } catch (e) {
                            //alert(e);
                        }
                    }
                }
            
                var $ = function(id) {
                    return document.getElementById(id);
                }

            }).catch({});
            
            
        },
        // 将base64的图片url数据转换为Blob
        convertBase64UrlToBlob(dataurl) { 
            try{
                var arr = dataurl.split(','),
                bstr = atob(arr[1]),
                n = bstr.length,
                u8arr = new Uint8Array(n);
                while (n--) {
                    u8arr[n] = bstr.charCodeAt(n);
                }
                return new Blob([u8arr], {
                    type: 'image/png'
                });
            } catch(err) {
                console.log(err.message);
            }
        },
        // 将读取到的文件编码成Data URL
        blobToFile(blob){
            let _this = this;
            let reader = new FileReader();
            reader.readAsDataURL(blob);
            //读取文件过程方法
            reader.onload = function () {
                _this.$emit('modalConfirm', reader.result, blob);
            }
        },
    },
    computed: {},
    components:{},
};

</script>