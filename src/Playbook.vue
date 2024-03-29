<script setup lang="ts">
import { ref } from "vue";
import Toggle from "./components/UI/Toggle/index.vue";
import DelayedToggle from "./components/UI/DelayedToggle/index.vue";
import Group from "./components/UI/Group/index.vue";
import Hover from "./components/UI/Hover/index.vue";
import Focus from "./components/UI/Focus/index.vue";
import LongPress from "./components/UI/LongPress/index.vue";
import Click from "./components/UI/Click/index.vue";
import Collapsible from "./components/UI/Collapsible/index.vue";
import Intersection from "./components/UI/Intersection/index.vue";
import Img from "./components/UI/Img/index.vue";
import Switch from "./components/UI/Switch/index.vue";
import Overlay from "./components/UI/Overlay/index.vue";
import Sheet from "./components/UI/Sheet/index.vue";
import Toast from "./components/UI/Toast/index.vue";
import autofocus from "./framework/directives/autofocus";
import Countdown from "./components/UI/Countdown/index.vue";
import Radio from "./components/UI/Radio/index.vue";
import Checkbox from "./components/UI/Checkbox/index.vue";
import Id from "./components/UI/Id/index.vue";
import TrapFocus from "ui-trap-focus";
import eventKey from "./utils/eventkey";
import RadioGroup from "./components/UI/RadioGroup/index.vue";
import CheckboxGroup from "./components/UI/CheckboxGroup/index.vue";
// import Countdown_ from "./utils/countdown"

// window.Countdown = Countdown_

const toggled = ref(false);
const delayedToggle = ref(false);
const group = ref([]);
const focus = ref(false);
const hover = ref(false);

const keydown = (evt: KeyboardEvent) => {
  new TrapFocus({
    forward: (evt) => /arrow_down|arrow_right/.test(eventKey(evt)),
    backward: (evt) => /arrow_up|arrow_left/.test(eventKey(evt)),
    loop: true,
  })
    .init(evt)
    .then((el) => {
      if (el) {
        el.click();
      }
    });
};
</script>

<script lang="ts">
import { defineComponent } from "vue";
import { RouterLink } from "vue-router";

export default defineComponent({
  name: "app",
  directives: {
    autofocus,
  },
});
</script>

<template>
  <RouterLink to="/">Home</RouterLink>

  <div>
    <Toggle tag="section" #default="{ toggle }" v-model="toggled">
      <div @click="toggle">
        {{ toggled || "false" }}
      </div>
    </Toggle>

    <DelayedToggle
      v-model="delayedToggle"
      #default="{ on, off, toggle, waiting }"
      delay="2000"
    >
      <div class="box" @mouseenter="on" @mouseleave="off" @click="toggle">
        {{ delayedToggle }}
      </div>

      <div>wait: {{ waiting }}</div>

      <div>
        {{ delayedToggle }}
      </div>
    </DelayedToggle>

    <Group
      v-model="group"
      #default="{ add, isActive, remove }"
      allow-repeated
      multiple
    >
      <div>
        {{ group }}
      </div>

      <button
        v-for="i in 5"
        :key="i"
        @click="add({ foo: i })"
        style="background-color: blue"
      >
        {{ isActive({ foo: i }) }}
      </button>

      <button
        v-for="i in 5"
        :key="i"
        @click="remove({ foo: i })"
        style="background-color: red"
      >
        {{ isActive({ foo: i }) }}
      </button>
    </Group>

    <Hover v-model="hover" #default="{ events }">
      <div class="box" v-on="events">Hover me</div>

      <div>
        {{ hover }}
      </div>
    </Hover>

    <Focus v-model="focus">
      <div tabindex="0">
        {{ focus }}
      </div>
    </Focus>

    <details>
      <summary>Epcot Center</summary>
      <p>
        Epcot is a theme park at Walt Disney World Resort featuring exciting
        attractions, international pavilions, award-winning fireworks and
        seasonal special events.
      </p>
    </details>

    <LongPress #default="{ active, willChange, events }">
      <div class="box" v-on="events">Longpress</div>

      <div>active: {{ active }} will-change: {{ willChange }}</div>
    </LongPress>

    <Click #default="{ active }" :range="200">
      <div class="box">
        Click:
        {{ active }}
      </div>
    </Click>

    <Group #default="{ isActive, toggle }">
      <template v-for="i in 3" :key="i">
        <button @click="toggle(i)">Collapse {{ isActive(i) }}</button>

        <Collapsible :open="isActive(i)">
          <div class="box" @click="toggle(i)">
            {{ i }}

            <div v-for="ii in 2 * i">
              Lorem ipsum dolor sit amet consectetur, adipisicing elit. Itaque
              quae placeat quos tenetur voluptatibus obcaecati consequatur qui
              eaque, veritatis reiciendis, impedit consectetur laboriosam id,
              quis molestias nesciunt ipsa deleniti rerum!
            </div>
          </div>
        </Collapsible>
      </template>
    </Group>
  </div>

  <Intersection #default="{ isIntersecting, ratio }" :threshold-length="300">
    <div
      style="height: 500px; margin-top: 500px; margin-bottom: 500px"
      class="box"
    >
      {{ ratio }}

      <div class="box" :style="{ opacity: ratio }">HELLO</div>
    </div>
  </Intersection>

  <Img
    load-effect="fade"
    height="200"
    src="https://picsum.photos/500/500"
    alt="Hello"
  >
    <template #prepend="{ loading, loaded, error, refresh }">
      <div style="margin-top: 100px" @click="refresh">
        loading: {{ loading }} loaded: {{ loaded }} error: {{ error }}
      </div>
    </template>
  </Img>

  <div>
    <Switch track-class="tracks" thumb-class="thumb">
      <template #prepend="{ id }">
        <label :for="id"> Toggle </label>
      </template>
    </Switch>
  </div>

  <Overlay
    closeOnClickOutside
    z-index-offset="10"
    always-render
    custom-transition
  >
    <template #activator="{ active, toggle, attrs }">
      <button v-bind="attrs" @click.stop="toggle">Overlay: {{ active }}</button>
    </template>

    <template #default="{ close, transitionEvents, active }">
      <div style="position: fixed; top: 0; left: 0; width: 100%; height: 100%">
        <UiTransition appear>
          <div
            v-if="active"
            @click="close"
            style="
              position: absolute;
              top: 0;
              left: 0;
              width: 100%;
              height: 100%;
              background: rgb(0, 0, 0, 0.5);
            "
          ></div>
        </UiTransition>

        <UiTransition
          appear
          v-on="transitionEvents"
          :config="['slideY', 'fade']"
        >
          <div
            v-if="active"
            tabindex="0"
            style="position: relative; background-color: aliceblue"
          >
            <div>Hello world</div>

            <div>Hii</div>

            <Overlay closeOnClickOutside>
              <template #activator="inner">
                <button v-autofocus @click.stop="inner.toggle">foo</button>
              </template>

              <div>Hello world</div>
            </Overlay>
          </div>
        </UiTransition>
      </div>
    </template>
  </Overlay>

  <Sheet from="bottom" snap-mandatory>
    <template #activator="{ active, toggle, attrs }">
      <button v-bind="attrs" @click="toggle">Sheet {{ active }}</button>
    </template>

    <template #prepend="{ ratio, active }">
      <UiTransition :config="{ enter: 'fade', leave: `fade(0, ${ratio})` }">
        <div
          v-if="active"
          style="
            background-color: rgb(0, 0, 0, 0.6);
            inset: 0;
            position: fixed;
            pointer-events: none;
          "
          :style="{ opacity: ratio }"
        ></div>
      </UiTransition>
    </template>

    <template #default="{ transitionEvents, active }">
      <UiTransition v-on="transitionEvents" :config="['slideY(100)', 'fade']">
        <div v-if="active" style="background: white; height: 90vh">
          Hello world
        </div>
      </UiTransition>
    </template>
  </Sheet>

  <Toast duration="15s">
    <template #activator="{ active, toggle }">
      <button @click="toggle">Toast: {{ active }}</button>
    </template>

    <template #default="{ hover, countdown }">
      <div>
        TOAST! {{ hover }} {{ countdown }}

        <div
          style="
            background: red;
            width: 100vw;
            height: 2px;
            margin: 1rem 0;
            transform-origin: left;
          "
          :style="{
            transform: `scale3d(${Math.abs(countdown.value - 1)},1,1)`,
          }"
        ></div>
      </div>
    </template>
  </Toast>

  <Countdown
    duration="1s"
    #default="{ value, start, pause, stop, restart, touched, step }"
  >
    <div>
      {{ touched ? step(0, 100).toFixed(0) : 0 }}
    </div>

    <button @click="start">start</button>

    <button @click="pause">pause</button>

    <button @click="stop">stop</button>

    <button @click="restart">restart</button>
  </Countdown>

  <div style="display: flex">
    <Radio>
      <template #prepend="{ id, active }">
        <label :for="id"> Radio label {{ active }} </label>
      </template>

      <template #default="{ active }">
        <div
          style="
            height: 16px;
            width: 16px;
            border-radius: 16px;
            background: red;
            transition: 0.4s;
          "
          :style="{
            transform: `scale3d(${active ? 1 : 0}, ${active ? 1 : 0}, 1)`,
          }"
        ></div>
      </template>
    </Radio>

    <Checkbox>
      <template #append="{ id, active }">
        <label :for="id"> Checkbox label {{ active }} </label>
      </template>

      <template #default="{ active }">
        <div
          style="
            height: 16px;
            width: 16px;
            border-radius: 16px;
            background: red;
            transition: 0.4s;
          "
          :style="{
            transform: `scale3d(${active ? 1 : 0}, ${active ? 1 : 0}, 1)`,
          }"
        ></div>
      </template>
    </Checkbox>

    <RadioGroup :option="[1, 2, 3]" #default="{ items }" :initial="1">
      <Radio v-for="{ attrs, item } in items" :key="item" v-bind="attrs">
        <template #prepend="{ id, active }">
          <label :for="id"> Radio label {{ active }} {{ item }} </label>
        </template>

        <template #default="{ active }">
          <div
            style="
              height: 16px;
              width: 16px;
              border-radius: 16px;
              background: red;
              transition: 0.4s;
            "
            :style="{
              transform: `scale3d(${active ? 1 : 0}, ${active ? 1 : 0}, 1)`,
            }"
          ></div>
        </template>
      </Radio>
    </RadioGroup>

    <CheckboxGroup
      :option="[1, 2, 3]"
      #default="{ items, intermediate, selectAll, reset, allChecked }"
      :initial="1"
    >
      <button @click="allChecked ? reset() : selectAll()">
        {{ allChecked ? "Clear" : "Select all" }}
      </button>

      <Checkbox v-for="{ attrs, item } in items" :key="item" v-bind="attrs">
        <template #prepend="{ id, active }">
          <label :for="id"> Checkbox label {{ active }} {{ item }} </label>
        </template>

        <template #default="{ active }">
          <div
            style="
              height: 16px;
              width: 16px;
              border-radius: 16px;
              background: red;
              transition: 0.4s;
            "
            :style="{
              transform: `scale3d(${active ? 1 : 0}, ${active ? 1 : 0}, 1)`,
            }"
          ></div>
        </template>
      </Checkbox>
    </CheckboxGroup>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.box {
  background-color: red;
  min-height: 100px;
  color: white;
}

.tracks {
  background-color: black !important;
}
</style>
