<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card class="logo py-4 d-flex justify-center">
        <NuxtLogo />
        <VuetifyLogo />
      </v-card>
      <v-card class="pa-6 my-4">
        <form name="contact" method="POST" data-netlify="true">
          <v-text-field v-model="name" label="お名前" required></v-text-field>
          <v-text-field v-model="mail" label="E-mail" required></v-text-field>
          <v-text-field v-model="tel" label="電話番号" required></v-text-field>
          <v-btn class="mr-4" type="submit"> 送信 </v-btn>
        </form>
      </v-card>
    </v-col>
  </v-row>
</template>
<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      name: "",
      mail: "",
      tel: "",
    };
  },
});
</script>
