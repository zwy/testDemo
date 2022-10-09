<template>
  <div style="width: 80%; margin-left: 10%">
    <div id="poster-div">
      <div>
        <el-image
          style="width: 100%"
          src="https://cdn.mr-gut.cn//articles/220923/fafbf579ada6e40c755e6519364f9aaa.jpg"
        />
      </div>
      <div>
        <p>尊敬的张三老师</p>
        <p>
          2018和2019年，我们在北京连续成功举办了两届中国肠道大会，并让大会成为中国乃至世界最大的专门以肠道为主题的学术盛会，充分促进了相关领域产学研的交流与合作。
        </p>
        <p>
          2021年5月28-30日，我们将在玄武湖畔的南京国际展览中心再次举办中国肠道大会，并同期举办第三届中国肠道产业大会。
        </p>
        <p>
          本届大会是在会期改为两年一届后首次举办，我们将继续以“爱肠道、爱学习、爱整合”为主题，设立20个学术分会场，深度探讨肠道微生态、粪菌移植、消化道肿瘤、感染与免疫、营养学、肠脑轴、新技术等前沿科学问题。
        </p>
        <p>期待您能接受我们的邀请，让我们携手共创中国肠道研究的美好未来!</p>
        <p>参会提示:我们将免除您的参会注册费用，差旅费用需要您自行承担。</p>
        <p style="text-align: right">中国肠道大会组委会</p>
        <p style="text-align: right">2021年4月</p>
      </div>
    </div>
  </div>
  <div id="poster-operation">
    <el-button @click="onDownloadImage()">导出图片</el-button>
    <el-button type="primary" @click="onDownloadPDF()">导出PDF</el-button>
  </div>
</template>

<script lang="ts" setup>
import { jsPDF } from "jspdf";
import html2canvas from "html2canvas";

// URL 转 base64
function loadDataUrl(url, callback) {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.responseType = "blob";
  xhr.send();
  xhr.onload = function () {
    const reader = new FileReader();
    reader.readAsDataURL(xhr.response);
    reader.onloadend = function () {
      callback(reader.result);
    };
  };
}

// 导出图片
const onDownloadImage = () => {
  var elementList = document.querySelectorAll("#poster-div");
  var i;
  for (i = 0; i < elementList.length; i++) {
    const root = elementList[i];
    Promise.all(
      [root, ...Array.from(root.querySelectorAll("*"))].map(
        (el) =>
          new Promise((resolve) => {
            if (
              ~(el.src || "").indexOf(`${location.host}/ossFileHandle`) ||
              (el.src || "").indexOf("type=download")
            ) {
              loadDataUrl(el.src, (dataUrl) => {
                el.src = dataUrl;
                resolve();
              });
            } else {
              const bgImg = getComputedStyle(el).backgroundImage || "";
              if (
                ~bgImg.indexOf(`${location.host}/ossFileHandle`) ||
                (el.src || "").indexOf("type=download")
              ) {
                loadDataUrl(/url\(['"](.+)['"]\)/.exec(bgImg)[1], (dataUrl) => {
                  el.style.backgroundImage = `url(${dataUrl})`;
                  resolve();
                });
              } else {
                resolve();
              }
            }
          })
      )
    ).then(() => {
      html2canvas(root, {
        // width: root.clientWidth,
        // height: root.clientHeight,
        useCORS: true,
        allowTaint: false,
        scale: 4,
      }).then((canvas) => {
        const context = canvas.getContext("2d");
        // 【重要】关闭抗锯齿
        context.mozImageSmoothingEnabled = false;
        context.webkitImageSmoothingEnabled = false;
        context.msImageSmoothingEnabled = false;
        context.imageSmoothingEnabled = false;

        const a = document.createElement("a");
        a.download = "测试.jpg";
        a.href = canvas.toDataURL();
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
      });
    });
  }
};
// 导出PDF
const onDownloadPDF = () => {
  var elementList = document.querySelectorAll("#poster-div");
  var i;
  for (i = 0; i < elementList.length; i++) {
    const root = elementList[i];
    Promise.all(
      [root, ...Array.from(root.querySelectorAll("*"))].map(
        (el) =>
          new Promise((resolve) => {
            if (
              ~(el.src || "").indexOf(`${location.host}/ossFileHandle`) ||
              (el.src || "").indexOf("type=download")
            ) {
              loadDataUrl(el.src, (dataUrl) => {
                el.src = dataUrl;
                resolve();
              });
            } else {
              const bgImg = getComputedStyle(el).backgroundImage || "";
              if (
                ~bgImg.indexOf(`${location.host}/ossFileHandle`) ||
                (el.src || "").indexOf("type=download")
              ) {
                loadDataUrl(/url\(['"](.+)['"]\)/.exec(bgImg)[1], (dataUrl) => {
                  el.style.backgroundImage = `url(${dataUrl})`;
                  resolve();
                });
              } else {
                resolve();
              }
            }
          })
      )
    ).then(() => {
      html2canvas(root, {
        // width: root.clientWidth,
        // height: root.clientHeight,
        useCORS: true,
        allowTaint: false,
        scale: 4,
      }).then((canvas) => {
        const context = canvas.getContext("2d");
        // 【重要】关闭抗锯齿
        context.mozImageSmoothingEnabled = false;
        context.webkitImageSmoothingEnabled = false;
        context.msImageSmoothingEnabled = false;
        context.imageSmoothingEnabled = false;

        // Don't forget, that there are CORS-Restrictions. So if you want to run it without a Server in your Browser you need to transform the image to a dataURL
        // Use http://dataurl.net/#dataurlmaker
        var imgData = canvas.toDataURL();
        // A4 计算 210mm×297mm 单位mm http://raw.githack.com/MrRio/jsPDF/master/docs/jsPDF.html

        var h = 297; // 返回元素的总高度
        var w = (root.offsetWidth / root.offsetHeight) * h; // 返回元素的总宽度
        // var x = (210 - w) / 2;
        // var y = 0;
        var x = 0;
        var y = 0;
        console.info(x);
        console.info(y);
        console.info(w);
        console.info(h);

        const doc = new jsPDF({
          format: [w, h], // 设置PDF的长宽
        });
        doc.addImage(imgData, "JPEG", x, y, w, h);
        doc.save("测试.pdf");
      });
    });
  }
};
</script>
<style>
#poster-div {
  width: 480px;
  padding-bottom: 80px;
  background: aliceblue;
}
#poster-div p {
  text-align: left;
  margin-left: 20px;
  margin-right: 20px;
}

#poster-operation {
  margin-top: 40px;
  margin-bottom: 80px;
}
</style>
