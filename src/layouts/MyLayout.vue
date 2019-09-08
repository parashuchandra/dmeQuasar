<template>
  <q-layout view="hHh Lpr fFf">
    <q-header reveal class="bg-primary text-white " align = "center">
      <q-toolbar>
        <q-toolbar-title>DME2 Tester</q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from "quasar";

export default {
  name: "MyLayout",
  data() {
    return {
      leftDrawerOpen: false
    };
  },
  methods: {
    openURL
  }
};
</script>

<style></style>
