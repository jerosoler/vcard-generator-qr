<script setup>
import QRCodeVue3 from "qrcode-vue3";
import {
  TelProperty,
  EmailProperty,
  TitleProperty,
  OrgProperty,
  URLProperty,
  PrefParameter,
  IntegerType,
  URIType,
  TypeParameter,
  TextType,
  Group,
  FNProperty,
  AdrProperty,
  SpecialValueType,
  NProperty,
  VCARD,
} from "vcard4";
import { renderSVG } from 'uqr'
import { ref, watchEffect } from 'vue'

const fullname = ref('');
const phone = ref('');
const phoneWork = ref('');
const email = ref('');
const website = ref('');
const company = ref('');
const job = ref('');
const qrcode = ref('');
const url = ref('');
const qrcodeUrl = ref('');

function downloadSVG() {
  const svg = renderSVG(qrcode.value);
  const svgBlob = new Blob([svg], {type:"image/svg+xml;charset=utf-8"});
        const svgUrl = URL.createObjectURL(svgBlob);
        const downloadLink = document.createElement("a");
        downloadLink.href = svgUrl;
        downloadLink.download = 'vcard-qr';
        document.body.appendChild(downloadLink);
        downloadLink.click();
        document.body.removeChild(downloadLink);
}

function downloadSVGUrl() {
  const svg = renderSVG(qrcodeUrl.value);
  const svgBlob = new Blob([svg], {type:"image/svg+xml;charset=utf-8"});
        const svgUrl = URL.createObjectURL(svgBlob);
        const downloadLink = document.createElement("a");
        downloadLink.href = svgUrl;
        downloadLink.download = 'url-qr';
        document.body.appendChild(downloadLink);
        downloadLink.click();
        document.body.removeChild(downloadLink);
}

watchEffect(() => {
  const fields = [];
  if(fullname.value !== "") {
    const cardFullname = new FNProperty([], new TextType(fullname.value));
    const nArr =  new Array(5);
    const names = fullname.value.split(" ").reverse();
    names.forEach((name, index) => {
      nArr[index] = new TextType(name);
    });
    const cardName =  new NProperty([], new SpecialValueType(nArr, "NProperty"));
    fields.push(cardFullname);
    fields.push(cardName);
  } 

  if(phone.value !== "") {
    //const cardPhone = new TelProperty([], new URIType(`tel:${phone.value}`));
    const cardPhone = new TelProperty([new TypeParameter(new TextType("home"), "TelProperty")], new TextType(`${phone.value}`));
    fields.push(cardPhone);
  }

  if(phoneWork.value !== "") {
    //const cardPhone = new TelProperty([], new URIType(`tel:${phone.value}`));
    const cardPhoneWork = new TelProperty([new TypeParameter(new TextType("work"), "TelProperty")], new TextType(`${phoneWork.value}`));
    fields.push(cardPhoneWork);
  }


  if(email.value !== "") {
    const cardEmail = new EmailProperty([], new TextType(email.value));
    fields.push(cardEmail);
  }

  if(website.value !== "") {
    const url  = (website.value.startsWith('http') ? website.value : `https://${website.value}`);
    const cardWebsite = new URLProperty([], new URIType(url));
    
    fields.push(cardWebsite);
  }
  
  if(company.value !== "") {
    const cardCompany = new OrgProperty([], new SpecialValueType([ new TextType(company.value) ], "orgproperty" ));
    fields.push(cardCompany);
  }
  
  if(job.value !== "") {
    const cardJob = new TitleProperty([], new TextType(job.value));
    fields.push(cardJob);
  }
  
  if(fields.length !== 0) {
    if(fullname.value === '') {
      const cardFullname = new FNProperty([], new TextType(" "));
      fields.push(cardFullname);
      const nArr = new Array(5);
      nArr[0] = new TextType(" ");
      const cardName =  new NProperty([], new SpecialValueType(nArr, "NProperty"));
      fields.push(cardName);
    }
    const card = new VCARD(fields);
    qrcode.value = card.repr();
  } else {
    qrcode.value = '';
  }
  
  if(url.value !== "") {
    qrcodeUrl.value = url.value;
  } else {
    qrcodeUrl.value = '';
  }
}) 
</script>

<template>
  <h1>Vcard generator</h1>
  <!--<pre>{{  qrcode }}</pre><br>-->
  <div class="form">
    <input v-model="fullname" placeholder="Fullname">
    <input v-model="phone" placeholder="Home Phone: ex: +34 600 600 600">
    <input v-model="phoneWork" placeholder="Work Phone: ex: +34 600 600 600">
    <input v-model="email" type="email" placeholder="E-mail">
    <input v-model="website" placeholder="Websit: ex: https://google.es">
    <input v-model="company" placeholder="Company Name">
    <input v-model="job" placeholder="Job Title">
  </div>
  <QRCodeVue3 :value="qrcode" 
  :width="200"
  :height="200"
  :margin="0"
  :key="qrcode"
  :qr-options="{
          errorCorrectionLevel: 'H'
        }"
  :dotsOptions="{
            type: 'square',
            color: '#000000',
          }"
  :cornersSquareOptions="{ type: 'square', color: '#000000' }"
  :cornersDotOptions="{ type: undefined, color: '#000000' }"
  fileExt="png"
  :download="true"
  downloadButton="download"
  :downloadOptions="{ name: 'vcard-qr', extension: 'png' }"
  />
  <button class="download" @click="downloadSVG" v-if="qrcode !== ''">Download SVG</button>

  <br>
  <hr>
  <br>

  <h1>URL Generator</h1>
  <input v-model="url" placeholder="https://google.es">
  <QRCodeVue3 :value="qrcodeUrl" 
  :width="200"
  :height="200"
  :margin="0"
  :key="qrcodeUrl"
  :qr-options="{
          errorCorrectionLevel: 'H'
        }"
  :dotsOptions="{
            type: 'square',
            color: '#000000',
          }"
  :cornersSquareOptions="{ type: 'square', color: '#000000' }"
  :cornersDotOptions="{ type: undefined, color: '#000000' }"
  fileExt="png"
  :download="true"
  downloadButton="download"
  :downloadOptions="{ name: 'url-qr', extension: 'png' }"
  />
  <button class="download" @click="downloadSVGUrl" v-if="qrcodeUrl !== ''">Download SVG</button>
  <br>
  <br>
</template>

<style scoped>
.form {
  display: flex;
  flex-direction: column;
  gap: 18px;
  margin-bottom: 20px;
}
.download {
  margin-top: 20px;
}
</style>
