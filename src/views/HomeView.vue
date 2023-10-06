<script setup>
import { ref, onMounted } from 'vue'
import TheWelcome from '../components/TheWelcome.vue'
import JSZip from "jszip";
import FileSaver from "file-saver";

const fileList = ref([
  { url: 'http://123.207.66.120:3000/image/1.jpg', name: '11.jpg' },
  { url: 'http://123.207.66.120:3000/image/2.jpg', name: '22.jpg' },
])

const downloadBtn = () => {
  download()
}
// 点击下载
const download = () => {
  const blogTitle = `下载文件名字`; // 下载后压缩包的命名
  const zip = new JSZip();
  const promises = [];
  const cache = {};
  const arrImg = [];
  fileList.value.forEach(item => {
    arrImg.push({ url: item.url, name: item.name })
  })
  for (let item of arrImg) {
    // item.url 为文件链接地址
    const promise = getImgArrayBuffer(item.url).then((data) => {
      // 下载文件, 并存成ArrayBuffer对象(blob)
      zip.file(item.name, data, { binary: true }); // 逐个添加文件
      cache[item.name] = data;
    });
    promises.push(promise);
  }
  Promise.all(promises).then(() => {
    zip.generateAsync({ type: "blob" }).then((content) => {
      console.log(44, content);
      // 生成二进制流
      FileSaver.saveAs(content, blogTitle); // 利用file-saver保存文件  自定义文件名
    });
  }).catch(() => {
    alert("文件压缩失败");
  });
}
const getImgArrayBuffer = (url) => {
  return new Promise((resolve, reject) => {
    //通过请求获取文件blob格式
    let xmlhttp = new XMLHttpRequest();
    xmlhttp.open("GET", url, true);
    xmlhttp.responseType = "blob";
    xmlhttp.onload = function () {
      if (this.status == 200) {
        resolve(this.response);
      } else {
        reject(this.status);
      }
    };
    xmlhttp.send();
  });
}
</script>

<template>
  <main>
    <TheWelcome />
    <button @click="downloadBtn">批量打包下载</button>
  </main>
</template>
