<template>
  <div class="home" ref="imageTofile">
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js + TypeScript App" />
    <span class="button-dalod" @click="toImage()">生成证书图片</span>
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import HelloWorld from "@/components/HelloWorld.vue"; // @ is an alias to /src
import html2canvas from "html2canvas";

const imageTofile = ref();

function toImage() {
  // 手动创建一个 canvas 标签
  const canvas = document.createElement("canvas");
  // 获取父标签，意思是这个标签内的 DOM 元素生成图片
  // imageTofile是给截图范围内的父级元素自定义的ref名称
  let canvasBox = imageTofile.value;
  // 获取父级的宽高
  const width = parseInt(window.getComputedStyle(canvasBox).width);
  const height = parseInt(window.getComputedStyle(canvasBox).height);
  // 宽高 * 2 并放大 2 倍 是为了防止图片模糊
  canvas.width = width * 2;
  canvas.height = height * 2;
  canvas.style.width = width + "px";
  canvas.style.height = height + "px";
  const context = canvas.getContext("2d");
  context.scale(2, 2);
  const options = {
    backgroundColor: null,
    canvas: canvas,
    useCORS: true,
  };
  html2canvas(canvasBox, options).then((canvas: any) => {
    // toDataURL 图片格式转成 base64
    let dataURL = canvas.toDataURL("image/png");

    //向后台上传图片时，首先将base64格式的图片转换成blod格式，然后再转换成file格式，即可上传
    // downloadImage(dataURL);
    uploadImg(dataURL);
  });
}

// function downloadImage(url: string) {
//   // 如果是在网页中可以直接创建一个 a 标签直接下载
//   let a = document.createElement("a");
//   a.href = url;
//   a.download = "首页截图";
//   a.click();
// }

//上传图片
function uploadImg(url: string) {
  let formData = new FormData();
  let changeUrl = dataURLtoFile(url);
  // console.log(changeUrl);
  let fileOfBlob = new File([changeUrl], new Date() + ".jpg"); // 命名图片名
  formData.append("file", fileOfBlob);
  // uploadImageAPI(formData).then((res: any) => {
  //   this.$message.success("图片上传成功");
  //   downloadUrl.value = res.data;
  // });
}
//base64转化成blod形式
function dataURLtoFile(dataURI: string, type?: any) {
  let binary = atob(dataURI.split(",")[1]);
  let array = [];
  for (let i = 0; i < binary.length; i++) {
    array.push(binary.charCodeAt(i));
  }
  return new Blob([new Uint8Array(array)], { type: type });
}
</script>
