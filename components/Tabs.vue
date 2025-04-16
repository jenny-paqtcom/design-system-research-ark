<template>
  <Root
    v-model="selectedTab"
    :orientation="isSmallScreen ? 'vertical' : 'horizontal'"
  >
    <List
      :class="[
        'grid sm:grid-cols-[repeat(auto-fit,_minmax(100px,_1fr))] h-fit sm:h-10 rounded-md relative bg-[var(--ui-tabs-bg)]',
        tabsContainerClass,
      ]"
    >
      <Trigger
        :ref="(el) => (tabRefs[index] = el)"
        v-for="(tab, index) in tabs"
        :key="`trigger-${tab.value}`"
        :value="tab.value"
        class="flex-1 z-1 cursor-pointer"
      >
        <div
          class="flex items-center justify-center gap-2 w-full h-10 group transition-colors duration-75 ease-in-out"
        >
          <Icon
            v-if="tab.icon"
            :name="tab.icon"
            :class="[
              'w-5 h-5 ',
              selectedTab === tab.value
                ? 'text-black'
                : 'text-gray-400 group-hover:text-gray-700',
            ]"
          />
          <span
            :class="[
              selectedTab === tab.value
                ? 'text-black'
                : 'text-gray-400 group-hover:text-gray-700',
            ]"
            >{{ tab.label }}</span
          >
        </div>
      </Trigger>

      <div
        ref="indicator"
        v-if="tabIndex !== undefined"
        :class="[
          'flex justify-center items-center w-full h-full absolute p-1',
          isTransitionEnabled
            ? 'transition-left transition-top duration-150 ease-in-out'
            : '',
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

const slots = useSlots();
const hasSelectedIndicator = !!slots.selectedIndicator;
const ANIMATION_DURATION = 150; // MS

const { Root, List, Trigger, Content } = Tabs;

type RouteObject = {
  name: string;
  params?: Record<string, any>;
  query?: Record<string, any>;
};

interface TabsItem {
  label: string;
  value: string;
  content?: string;
  icon?: string;
  to?: string | RouteObject;
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
let resizeTimeout: ReturnType<typeof setTimeout>;

const screenWidth = ref(window.innerWidth);
const isSmallScreen = computed(() => screenWidth.value <= 640);

const isTransitionEnabled = ref(false);

const handleResize = () => {
  screenWidth.value = window.innerWidth;
  setTriggerSize();

  clearTimeout(resizeTimeout);
  resizeTimeout = setTimeout(() => {
    isTransitionEnabled.value = true;
  }, ANIMATION_DURATION);
};

const tabs = props.tabs || [
  { label: "Tab I", value: "tab-1", content: "Tab Content I" },
  { label: "Tab II", value: "tab-2", content: "Tab Content II" },
  { label: "Tab III", value: "tab-3", content: "Tab Content III" },
];

const selectedTab = ref<string>(tabs[Number(props.tabIndex)]?.value);
const tabRefs = ref<(Element | ComponentPublicInstance | null)[]>([]);
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

  setTimeout(() => {
    isTransitionEnabled.value = true;
  }, ANIMATION_DURATION);
});

const setTriggerSize = (): void => {
  const firstTrigger = tabRefs.value.find((triggerRef) => triggerRef !== null);

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
