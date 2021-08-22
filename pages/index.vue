<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card class="logo py-4 d-flex justify-center">
        <NuxtLogo />
        <VuetifyLogo />
      </v-card>
      <v-card class="pa-6 my-4">
        <v-card-title> テストフォーム </v-card-title>
        <validation-observer
          ref="observer"
          tag="form"
          name="contact"
          method="POST"
          data-netlify="true"
          data-netlify-honeypot="bot-field"
          @submit.prevent="onSubmit"
        >
          <input type="hidden" name="form-name" value="contact" />
          <validation-provider
            v-slot="{ errors }"
            rules="required|max:100"
            name="お名前"
          >
            <v-text-field
              v-model="name"
              label="お名前"
              required
              :error-messages="errors"
            ></v-text-field>
          </validation-provider>
          <validation-provider
            v-slot="{ errors }"
            rules="required|email|max:256"
            name="メールアドレス"
          >
            <v-text-field
              v-model="mail"
              label="E-mail"
              required
              :error-messages="errors"
            ></v-text-field>
          </validation-provider>

          <v-text-field v-model="tel" label="電話番号" required></v-text-field>

          <v-btn
            class="mr-4"
            type="submit"
            :loading="sendStatus === 'is-sending'"
          >
            送信
          </v-btn>
        </validation-observer>
        <div>{{ text }}</div>
      </v-card>
    </v-col>
  </v-row>
</template>
<script>
export default {
  data() {
    return {
      text: "",
      name: "",
      mail: "",
      tel: "",
      botField: "",
      sendStatus: "",
    };
  },
  methods: {
    onSubmit() {
      if (this.sendStatus === "is-sending") return;
      this.sendStatus = "is-sending";
      this.text = "送信中...";
      const params = new URLSearchParams();
      params.append("form-name", "contact");
      params.append("username", this.name);
      params.append("email", this.mail);
      params.append("tel", this.tel);
      if (this.botField) {
        params.append("bot-field", this.botField);
      }
      console.log("送信しました");
      this.$axios
        .$post("/", params)
        .then(() => {
          this.text = "お問い合わせを送信しました！";
          this.resetForm();
          this.sendStatus = "is-complete";
        })
        .catch(() => {
          this.text = "お問い合わせの送信が失敗しました";
          this.sendStatus = "is-error";
        });
    },
    resetForm() {
      this.sendStatus = "";
      this.name = "";
      this.mail = "";
      this.tel = "";
      this.$refs.observer.reset();
    },
  },
};
</script>
