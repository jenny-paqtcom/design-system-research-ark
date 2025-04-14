<template>
  <div class="flex flex-col p-4">
    <Card>
      <span class="font-semibold">{{
        "ArkUI's tabs component without any styling or additional functionalities:"
      }}</span>
      <BasicArkUITabs :tabs="filteredexpressionsTabsWithContent" />
    </Card>
    <br />
    <Card>
      <span class="font-semibold mb-4">{{
        "Tabs with static (string) content and no custom styling:"
      }}</span>
      <span class="font-semibold text-sm mb-2">{{ "Primary" }}</span>
      <Tabs :tabs="filteredexpressionsTabsWithContent" />
      <br />
      <span class="font-semibold text-sm mb-2">{{ "Secondary" }}</span>
      <Tabs :tabs="filteredexpressionsTabsWithContent" color="secondary" />
    </Card>
    <br />
    <Card>
      <span class="font-semibold mb-4">{{ "Tabs with custom content:" }}</span>
      <span class="font-semibold text-sm mb-2">{{ "Default:" }}</span>
      <Tabs :tabs="filteredExpressionsTabs3">
        <template
          v-for="(tab, index) in filteredexpressionsTabs"
          :key="tab.value"
          #[tab.value]
        >
          <span>{{ expressions[index].description }}</span>
          <img
            class="w-40 h-auto"
            :src="expressions[index].imageUrl"
            :alt="expressions[index].value"
          />
        </template>
      </Tabs>
      <br />
      <span class="font-semibold text-sm mb-2">{{
        "With custom styling"
      }}</span>
      <Tabs
        :tabs="filteredexpressionsTabs2"
        tab-index="1"
        tabs-container-class="bg-rose-300 rounded-none"
        content-class="bg-rose-200"
        indicatorContainerClass="!p-0 rounded-none"
      >
        <template
          v-for="(tab, index) in filteredexpressionsTabs"
          :key="tab.value"
          #[tab.value]
        >
          <span>{{ expressions[index].description }}</span>
          <img
            class="w-40 h-auto"
            :src="expressions[index].imageUrl"
            :alt="expressions[index].value"
          />
        </template>

        <template #selectedIndicator>
          <div class="bg-rose-400 h-full w-full"></div>
        </template>
      </Tabs>
      <br />
      <span class="font-semibold text-sm mb-2">{{
        "With custom indicator I:"
      }}</span>
      <Tabs
        :tabs="filteredexpressionsTabs2"
        tab-index="1"
        tabs-container-class="bg-sky-300 rounded-none"
        content-class="bg-sky-200"
      >
        <template
          v-for="(tab, index) in filteredexpressionsTabs"
          :key="tab.value"
          #[tab.value]
        >
          <span>{{ expressions[index].description }}</span>
          <img
            class="w-40 h-auto"
            :src="expressions[index].imageUrl"
            :alt="expressions[index].value"
          />
        </template>

        <template #selectedIndicator>
          <div class="bg-sky-500 h-1 bottom-0 absolute w-full"></div>
        </template>
      </Tabs>
      <br />
      <span class="font-semibold text-sm mb-2">{{
        "With custom indicator II:"
      }}</span>
      <Tabs
        :tabs="filteredexpressionsTabs2"
        tab-index="1"
        tabs-container-class="bg-indigo-300 rounded-none"
        content-class="bg-indigo-200"
      >
        <template
          v-for="(tab, index) in filteredexpressionsTabs"
          :key="tab.value"
          #[tab.value]
        >
          <span>{{ expressions[index].description }}</span>
          <img
            class="w-40 h-auto"
            :src="expressions[index].imageUrl"
            :alt="expressions[index].value"
          />
        </template>

        <template #selectedIndicator>
          <div class="flex justify-between items-center w-52 h-full relative">
            <Icon name="arrow-right" class="w-5 h-5 text-indigo-600" />
            <Icon name="arrow-left" class="w-5 h-5 text-indigo-600" />
          </div>
        </template>
      </Tabs>
    </Card>
    <br />
    <Card>
      <span class="font-semibold mb-4">{{
        "Programaticlaly select tabs:"
      }}</span>
      <Tabs v-model="selectedTab" :tabs="filteredexpressionsTabsWithContent" />
      <div class="flex gap-1">
        <button
          class="bg-indigo-300 flex-1 rounded-md p-2 cursor-pointer"
          @click="() => changeTab(0)"
        >
          Click to set to tab 1
        </button>
        <button
          class="bg-indigo-300 flex-1 rounded-md p-2 cursor-pointer"
          @click="() => changeTab(1)"
        >
          Click to set to tab 2
        </button>
        <button
          class="bg-indigo-300 flex-1 rounded-md p-2 cursor-pointer"
          @click="() => changeTab(2)"
        >
          Click to set to tab 3
        </button>
        <button
          class="bg-indigo-300 flex-1 rounded-md p-2 cursor-pointer"
          @click="() => changeTab(3)"
        >
          Click to set to tab 4
        </button>
      </div>
    </Card>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  name: "playground",
});

const expressions = [
  {
    label: "Expression 1",
    value: "Expression-1",
    icon: "rabbit",
    imageUrl: "https://picsum.photos/100",
    description: "It's raining pipesteels.",
  },
  {
    label: "Expression 2",
    value: "Expression-2",
    icon: "rat",
    imageUrl: "https://picsum.photos/200",
    description: "Now the monkey comes out of the sleeves.",
  },
  {
    label: "Expression 3",
    value: "Expression-3",
    icon: "shrimp",
    imageUrl: "https://picsum.photos/300",
    description: "That's a flute of a cent.",
  },
  {
    label: "Expression 4",
    value: "Expression-4",
    icon: "snail",
    imageUrl: "https://picsum.photos/400",
    description: "Go with the chickens on a stick.",
  },
];

const filteredexpressionsTabs = expressions.map((item) => ({
  label: item.label,
  value: item.value,
  icon: item.icon,
}));

const filteredexpressionsTabs2 = expressions.slice(0, 2).map((item) => ({
  label: item.label,
  value: item.value,
  icon: item.icon,
}));

const filteredExpressionsTabs3 = expressions.map((item) => ({
  label: item.label,
  value: item.value,
}));

const filteredexpressionsTabsWithContent = expressions.map((item) => ({
  label: item.label,
  value: item.value,
  content: item.description,
}));

const selectedTab = ref(filteredexpressionsTabsWithContent[1].value);

const changeTab = (index: number) => {
  const tabs = filteredexpressionsTabsWithContent;
  if (tabs[index]) {
    selectedTab.value = tabs[index].value;
  }
};
</script>
