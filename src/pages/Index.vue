<template>
  <q-page padding style="background:#d5e1df">
    <div class="row q-mb-lg">
      <q-select filled v-model="requesttype" :options="options" />
      <q-input square filled v-model="url" class="col" />
      <q-btn
        :loading="loadComplete"
        color="primary"
        @click="simulateProgress()"
        style="width: 150px"
        @click.native="callAPI()"
      >
        Send
        <template v-slot:loading>
          <q-spinner-hourglass class="on-left" />Loading...
        </template>
      </q-btn>
    </div>
    <q-card flat bordered class="my-card bg-grey-1 q-gutter-y-md col">
      <q-tabs
        v-model="tab"
        dense
        class="text-grey"
        active-color="primary"
        indicator-color="primary"
        align="justify"
        narrow-indicator
      >
        <q-tab name="auth" label="Authorization" />
        <q-tab name="headers" label="Headers" />
        <q-tab name="body" label="Body" />
      </q-tabs>
      <q-separator />
      <q-tab-panels v-model="tab" style="background:#d5e1df" class="text-black text-center">
        <q-tab-panel name="auth">
          Basic Authentication
          <q-input v-model="username" filled type="text" label="username" />
          <q-input v-model="password" filled type="password" label="password" />
        </q-tab-panel>

        <q-tab-panel name="headers">
          <q-input v-model="headers" filled type="textarea" label="Headers" />
        </q-tab-panel>
        <q-tab-panel name="body">
          <q-input v-model="body" filled type="textarea" label="Body" />
        </q-tab-panel>
      </q-tab-panels>
    </q-card>
    <div class="row">
      <q-input
        v-model="responseBody"
        filled
        autogrow
        type="text"
        label="Response"
        class="col"
        :disable="!enableResponse"
        readonly
      />
    </div>
  </q-page>
</template>

<style></style>

<script>
export default {
  name: "PageIndex",

  data() {
    return {
      options: ["GET", "POST", "DELETE"],
      tab: "auth",
      hideResponse: true,
      loadComplete: false,
      enableResponse: false,
      url: "",
      requesttype: "GET",
      headers: "",
      body: "",
      responseBody: "",
      username: "",
      password: ""
    };
  },
  computed: {
    isDisabled: function() {
      return !this.enableResponse;
    },
    contentStyle() {
      return {
        backgroundColor: "rgba(0,0,0,0.02)",
        color: "#555"
      };
    },

    contentActiveStyle() {
      return {
        backgroundColor: "#eee",
        color: "black"
      };
    },

    thumbStyle() {
      return {
        right: "2px",
        borderRadius: "5px",
        backgroundColor: "#027be3",
        width: "5px",
        opacity: 0.75
      };
    }
  },
  methods: {
    onItemClick() {
      console.log("Clicked on an Item");
    },
    async callAPI() {
      try {
        this.loadComplete = true;
        console.log(
          ` type: ${this.requesttype.toLowerCase()} \n url: ${
            this.url
          }\n username: ${this.username} \n password${
            this.password
          } \n headers: ${this.headers} \n body: ${this.body}`
        );
        let axiosOptions = {
          method: this.requesttype.toLowerCase() || "get",
          url: this.url,
          data: this.requesttype.toLowerCase() === "post" ? this.body : "",
          headers: this.headers,
          auth: {
            username: this.username,
            password: this.password
          },
          responseEncoding: "utf8",
          xsrfCookieName: "XSRF-TOKEN",
          xsrfHeaderName: "X-XSRF-TOKEN",
          transformRequest: [
            function(data, headers) {
              console.log(`headers: ${JSON.stringify(headers)}`);
              return data;
            }
          ],
          transformResponse: [
            function(data) {
              return data;
            }
          ]
        };
        let apiResponse = await this.$axios(axiosOptions);
        console.log("apiResponse.status" + apiResponse.status);
        console.log("apiResponse.statusText" + apiResponse.statusText);
        console.log("apiResponse.headers" + apiResponse.headers);
        console.log("apiResponse.config" + apiResponse.config);
        this.loadComplete = false;
        this.enableResponse = true;
        this.responseBody = apiResponse.data;
      } catch (e) {
        this.loadComplete = false;
        this.enableResponse = true;
        this.responseBody = e;
      }
    }
  }
};
</script>
