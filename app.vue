<template>
  <NuxtLayout>
    <ClientOnly>
      <Card class="m-4">
        <span class="font-semibold mb-4">{{
          "Use tabs as navigator (menu):"
        }}</span>
        <Tabs :tabs="navigationTabs" />
      </Card>
      <NuxtPage />
    </ClientOnly>
  </NuxtLayout>
</template>

<script setup lang="tsx">
const navigationTabs = [
  {
    label: "Playground",
    value: "playground",
    icon: "rabbit",
    to: { name: "playground" },
  },
  { label: "Home", value: "home", icon: "home", to: { name: "home" } },
  {
    label: "Settings",
    value: "settings",
    icon: "settings",
    to: { name: "settings" },
  },
];
</script>
