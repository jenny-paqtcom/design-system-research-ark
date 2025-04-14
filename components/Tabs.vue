<template>
  <Root
    v-model="selectedTab"
    :orientation="isSmallScreen ? 'vertical' : 'horizontal'"
  >
    <List
      :class="[
        'grid sm:grid-cols-[repeat(auto-fit,_minmax(100px,_1fr))] h-fit sm:h-10 rounded-md relative',
        tabsContainerClass,
        color === 'primary'
          ? 'bg-[var(--ui-primary-bg)]'
          : 'bg-[var(--ui-secondary-bg)]',
      ]"
    >
      <Trigger
        :ref="(el) => (triggerRefs[index] = el)"
        v-for="(tab, index) in tabs"
        :key="`trigger-${tab.value}`"
        :value="tab.value"
        class="flex-1 z-1 cursor-pointer"
      >
        <div class="flex items-center justify-center gap-2 w-full h-10">
          <Icon v-if="tab.icon" :name="tab.icon" class="w-5 h-5" />
          <span>{{ tab.label }}</span>
        </div>
      </Trigger>

      <div
        ref="indicator"
        v-if="tabIndex !== undefined"
        :class="[
          'flex justify-center items-center w-full h-full absolute transition-left transition-top duration-150 ease-in-out p-1',
          indicatorContainerClass,
        ]"
        :style="indicatorStyle"
      >
        <div
          v-if="!hasSelectedIndicator"
          :class="[
            'h-full w-full rounded-md',
            indicatorClass,
            color === 'primary'
              ? 'bg-[var(--ui-primary)]'
              : 'bg-[var(--ui-secondary)]',
          ]"
        ></div>
        <slot v-else name="selectedIndicator"></slot>
      </div>
    </List>
    <Content
      v-for="tab in tabs"
      :key="`content-${tab.value}`"
      :value="tab.value"
      :class="['p-4', contentClass]"
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
import { ref, computed, useSlots } from "vue";

const slots = useSlots();
const hasSelectedIndicator = !!slots.selectedIndicator;

const { Root, List, Trigger, Content } = Tabs;

interface TabsItem {
  label: string;
  value: string;
  content?: string;
  icon?: string;
  to?:
    | string
    | {
        name: string;
        params?: Record<string, any>;
        query?: Record<string, any>;
      };
}

const props = withDefaults(
  defineProps<{
    tabs?: TabsItem[];
    tabsContainerClass?: string;
    indicatorClass?: string;
    indicatorContainerClass?: string;
    contentClass?: string;
    color?: "primary" | "secondary";
    modelValue?: string;
    tabIndex?: string | number;
  }>(),
  {
    color: "primary",
    tabIndex: 0,
  },
);

const emit = defineEmits(["update:modelValue"]);

const screenWidth = ref(window.innerWidth);

const isSmallScreen = computed(() => screenWidth.value <= 640);

const handleResize = () => {
  screenWidth.value = window.innerWidth;
  setTriggerSize();
};

const tabs = props.tabs || [
  { label: "Tab I", value: "tab-1", content: "Tab Content I" },
  { label: "Tab II", value: "tab-2", content: "Tab Content II" },
  { label: "Tab III", value: "tab-3", content: "Tab Content III" },
];

const selectedTab = ref<string>(tabs[Number(props.tabIndex)]?.value);

const triggerRefs = ref<(Element | ComponentPublicInstance | null)[]>([]);
const triggerHeight = ref<number>(0);
const triggerWidth = ref<number>(0);

const indicator = useTemplateRef("indicator");

const tabPercentage = 100 / tabs.length;

const indicatorStyle = computed(() => {
  const tabIndex = tabs.findIndex((tab) => tab.value === selectedTab.value);
  if (indicator.value) {
    return {
      top: `${isSmallScreen.value ? tabIndex * tabPercentage : 0}%`,
      left: `${isSmallScreen.value ? 0 : tabIndex * tabPercentage}%`,
      width: `${triggerWidth.value}px`,
      height: `${triggerHeight.value}px`,
    };
  }
  return null;
});

watch(selectedTab, (newTab, oldTab) => {
  if (newTab !== oldTab) {
    const matchingTab = tabs.find((tab) => tab.value === newTab);
    if (matchingTab?.to) {
      navigateTo(matchingTab.to);
    }
  }
});

watch(
  () => props.modelValue,
  (newVal) => {
    if (newVal && newVal !== selectedTab.value) {
      selectedTab.value = newVal;
    }
  },
);

onMounted(() => {
  window.addEventListener("resize", handleResize);

  setTriggerSize();
});

const setTriggerSize = (): void => {
  const firstTrigger = triggerRefs.value.find(
    (triggerRef) => triggerRef !== null,
  );

  let firstTriggerHtmlElement: HTMLElement | null = null;

  if (firstTrigger instanceof HTMLElement) {
    firstTriggerHtmlElement = firstTrigger;
  } else if (firstTrigger && "$el" in firstTrigger) {
    firstTriggerHtmlElement = (firstTrigger as ComponentPublicInstance)
      .$el as HTMLElement;
  }

  if (firstTriggerHtmlElement) {
    const height = parseFloat(
      window.getComputedStyle(firstTriggerHtmlElement).height,
    );
    const width = parseFloat(
      window.getComputedStyle(firstTriggerHtmlElement).width,
    );
    triggerHeight.value = height;
    triggerWidth.value = width;
  }
};

onBeforeUnmount(() => {
  window.removeEventListener("resize", handleResize);
});
</script>
