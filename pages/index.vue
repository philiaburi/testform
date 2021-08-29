<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card class="logo py-4 d-flex justify-center">
        <NuxtLogo />
        <VuetifyLogo />
      </v-card>
      <v-card class="pa-6 my-4">
        <v-card-title>
          <div v-for="(item, index) in posts" :key="index">
            {{ item.fields.test || "" }}
          </div>
        </v-card-title>
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
              name="username"
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
              name="email"
              :error-messages="errors"
            ></v-text-field>
          </validation-provider>

          <v-text-field
            v-model="tel"
            label="電話番号"
            name="tel"
            required
          ></v-text-field>

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
import { createClient } from "~/plugins/contentful.js";
const client = createClient();
export default {
  data() {
    return {
      text: "",
      name: "",
      mail: "",
      tel: "",
      botField: "",
      sendStatus: "",
      posts: [],
    };
  },
  async fetch() {
    // 記事一覧を取得
    try {
      const entries = await client.getEntries({
        content_type: process.env.CTFL_CONTENT_TYPE_POST,
      });
      this.posts = entries.items;
    } catch (error) {
      console.log("エラーが出ました", error);
    }
  },
  methods: {
    encode(data) {
      return Object.keys(data)
        .map(
          (key) => `${encodeURIComponent(key)}=${encodeURIComponent(data[key])}`
        )
        .join("&");
    },
    onSubmit() {
      if (this.sendStatus === "is-sending") return;
      const axiosConfig = {
        header: { "Content-Type": "application/x-www-form-urlencoded" },
      };
      this.sendStatus = "is-sending";
      this.text = "送信中...";
      const params = {
        "form-name": "contact",
        username: this.name,
        email: this.mail,
        tel: this.tel,
        "bot-field": this.botField,
      };

      if (this.botField) {
        params.append("bot-field", this.botField);
      }

      this.$axios
        .$post("/", this.encode(params), axiosConfig)
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
