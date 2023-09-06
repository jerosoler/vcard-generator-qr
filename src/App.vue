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
import { ref, watchEffect } from 'vue'

const fullname = ref('');
const phone = ref('');
const email = ref('');
const website = ref('');
const company = ref('');
const job = ref('');
const qrcode = ref('');

watchEffect(() => {
  const fields = [];
  if(fullname.value !== "") {
    const cardFullname = new FNProperty([], new TextType(fullname.value));
    fields.push(cardFullname);
  } 

  if(phone.value !== "") {
    const cardPhone = new TelProperty([], new URIType(`tel:${phone.value}`));
    fields.push(cardPhone);
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
    }
    const card = new VCARD(fields);
    qrcode.value = card.repr();
  }
  
  
}) 
</script>

<template>
  <h1>Vcard generator</h1>
  <!--{{  qrcode }}<br>-->
  <div class="form">
    <input v-model="fullname" placeholder="Fullname">
    <input v-model="phone" placeholder="Phone: ex: +34 600 600 600">
    <input v-model="email" type="email" placeholder="E-mail">
    <input v-model="website" placeholder="Websit: ex: https://google.es">
    <input v-model="company" placeholder="company Name">
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
  downloadButton="my-button"
  :downloadOptions="{ name: 'vcard-qr', extension: 'png' }"
  />
</template>

<style scoped>
.form {
  display: flex;
  flex-direction: column;
  gap: 18px;
  margin-bottom: 20px;
}
</style>
