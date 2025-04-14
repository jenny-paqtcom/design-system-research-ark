<template>
  <Root v-model="selectedTab">
    <List>
      <Trigger
        v-for="tab in tabs"
        :key="`trigger-${tab.value}`"
        :value="tab.value"
      >
        <span> {{ tab.label }}</span>
      </Trigger>
    </List>
    <Content
      v-for="tab in tabs"
      :key="`content-${tab.value}`"
      :value="tab.value"
    >
      <template #default>
        <slot :name="tab.value">
          <div
            v-if="typeof tab.content === 'string'"
            v-html="tab.content"
          ></div>
          <div v-else>
            <component :is="tab.content" />
          </div>
        </slot>
      </template>
    </Content>
  </Root>
</template>

<script setup lang="ts">
import { Tabs } from "@ark-ui/vue/tabs";

const { Root, List, Trigger, Content } = Tabs;

const props = defineProps<{
  tabs?: { label: string; value: string; content: string }[];
}>();

const tabs = props.tabs || [
  { label: "Tab I", value: "tab-1", content: "Tab Content I" },
  { label: "Tab II", value: "tab-2", content: "Tab Content II" },
  { label: "Tab III", value: "tab-3", content: "Tab Content III" },
];

const selectedTab = ref<string>(tabs[0]?.value ?? "0");
</script>
